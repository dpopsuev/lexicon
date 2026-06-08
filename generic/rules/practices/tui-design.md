---
id: tui-design
title: "TUI Design"
description: "Four-layer architecture, threading model, output discipline, anti-flickering, and protocol boundaries for terminal user interfaces."
labels: [tui, architecture, threading, prompt_toolkit, textual, ink, window-manager]
---

# TUI Design

**Never block the event loop. Never write to the terminal directly while the TUI owns it.**

These two rules are the origin of every other constraint. Everything else is a consequence.

## The Four-Layer Model

Every mature TUI framework — curses, urwid, Blessed, prompt_toolkit, Textual, Ink — and every mature Window Manager — X11, Wayland, dwm, i3, sway — converges on the same four-layer split:

```
Input   — reads keystrokes, mouse, resize signals
          runs in a dedicated reader thread or selector
          emits events upward; never mutates state directly

State   — the application model
          mutated ONLY from the event loop thread
          reactive or observable: state changes trigger re-render

Render  — computes what to draw from current state
          diff-based: writes only changed cells, not a full repaint
          never blocks; never does I/O

Output  — flushes escape sequences to the terminal fd atomically
          one write() + flush() per frame
          single writer: no concurrent writes from multiple threads
```

**State flows down, events flow up. No layer skips another.**

## The Two Rules

### Rule 1 — Never block the event loop

The event loop owns the render thread. Any blocking call — network, disk, LLM streaming, subprocess — must run in a worker thread or async task. Blocking freezes the UI: keystrokes queue up, the spinner stops, the user sees a dead terminal.

Responsiveness budget: an event handler must return in under ~8ms before the user perceives lag. Everything slower belongs in a worker.

### Rule 2 — Never write to the terminal directly while the TUI owns it

Raw `print()` or `sys.stdout.write()` from a worker thread interleave with the renderer's escape sequences and corrupt terminal state. All output must go through the framework's pipeline:

- **prompt_toolkit**: `run_in_terminal()` — pauses the app, writes, resumes
- **Textual**: `call_from_thread(widget.update, ...)` or `post_message()`
- **Ink / Node**: event queue → component re-render

Every write to `run_in_terminal` must end with `\n`. The prompt_toolkit cursor tracking assumes a newline terminates each "above-prompt" write. Writes without `\n` cause cursor drift on the next `run_in_terminal` call.

## Lessons from Window Manager Architecture

WMs solved the same problems a decade earlier in pixel space. The lessons transfer directly to cell space.

### Damage regions → shadow buffer diff

In X11/Wayland, clients send damage rectangles to the compositor: "this region changed, recomposite it." The compositor skips unchanged regions entirely.

In a TUI: maintain two cell grids (`prev_buf`, `curr_buf`). After computing the new frame, diff cell-by-cell. Emit cursor-position + SGR + character only for cells that changed. prompt_toolkit does this internally via `_last_rendered_screen`. Your application-level output (scrollback, streaming) should follow the same discipline: write content once, never revisit it.

### Double buffering → in-memory frame + atomic emit

In WMs: render to a back buffer, page-flip to front at vsync. The hardware never sees a partial frame.

In a TUI: accumulate all output for a frame into a buffer. Issue one `write()` + `flush()` per frame. Synchronized Output (DEC mode 2026) is the terminal equivalent of a page flip.

### `x_push_changes()` centralisation → single output gate

i3 centralised all X11 output into `src/x.c: x_push_changes()`. Before: scattered X11 calls throughout the source. After: one place, one function.

In a TUI: one function, one path, for all terminal writes. No scattered `sys.stdout.write()` or `print()` calls. If you can't name the single output gate, you don't have one.

### dwm KISS → minimise dynamic layout axes

dwm achieves ~2000 lines of C by eliminating runtime-configurable layout. Complexity correlates with the number of layout axes that can change at runtime — every dynamic axis requires a layout-update, re-render, and event-dispatch path.

In a TUI: prefer a static layout tree with dynamic content. A streaming widget that grows with LLM output is a dynamic layout axis that inflates the footer on every frame. Route streaming content to scrollback instead.

### Event batching → drain all events, render once

WMs collect all pending events from the fd before rendering: `events = poll(fd_set)` → `for each: dispatch()` → `if dirty: render()`. One render per loop iteration regardless of event burst size.

In a TUI: drain the entire event queue before invalidating the layout. If 50 `TurnDelta` events arrive in a burst, process all 50, then call `app.invalidate()` once — not 50 times.

## Anti-Flickering Techniques

### Technique 1: Cursor hide during redraw (universal)

```
Hide: \033[?25l   (DECTCEM reset)
Show: \033[?25h   (DECTCEM set)
```

Prevents the cursor from visibly teleporting across the screen during a redraw. prompt_toolkit and Textual do this automatically.

### Technique 2: Shadow buffer diff (universal)

Maintain a `prev_screen[row][col]` and `next_screen[row][col]`. Emit sequences only for cells where `next != prev`. This is the most impactful anti-flicker technique — reduces TTY write volume by 90-99% for interactive TUIs where most of the screen is static.

### Technique 3: Write coalescing (universal)

Accumulate all escape sequences for a frame into one buffer. Issue a single `write()` + `flush()` syscall at frame end. Multiple writes cause the terminal to render intermediate partial states between syscalls.

### Technique 4: Synchronized Output — BSU/ESU (modern terminals)

```python
_BSU = "\033[?2026h"   # Begin Synchronized Update
_ESU = "\033[?2026l"   # End Synchronized Update
```

The terminal processes incoming bytes but defers rendering to the screen until ESU. This is the terminal equivalent of a WM page flip: the user never sees a partial frame regardless of how fast you write.

**Detection** (DECRQM query):
```
Query:    \033[?2026$p
Response: \033[?2026;Ns$y   (N=1 = supported+active, N=2 = supported+inactive)
```

**Support (2026):** kitty, Alacritty, WezTerm, iTerm2, foot, Windows Terminal, tmux, Zellij. Not VTE/GNOME Terminal.

**Usage pattern:**
```python
sys.stdout.write("\033[?2026h")   # BSU
sys.stdout.write("\033[?25l")     # hide cursor
# ... all drawing operations ...
sys.stdout.write("\033[?25h")     # show cursor
sys.stdout.write("\033[?2026l")   # ESU
sys.stdout.flush()
```

Unsupported terminals silently ignore the sequences — zero cost on old terminals.

### Technique 5: Alternate screen buffer (universal)

```
Enter: \033[?1049h   # save cursor + switch to alternate screen + clear
Exit:  \033[?1049l   # restore cursor + switch back to primary screen
```

Ensures the primary screen (scrollback) is untouched when the TUI exits. All full-screen TUIs should use this.

### Technique 6: Line scroll operations (universal)

```
Scroll region:  \033[{top};{bottom}r   (DECSTBM)
Scroll up N:    \033[{n}S
Insert line:    \033[{n}L
Delete line:    \033[{n}M
```

When a scrolling pane shifts by N lines, instruct the terminal to move its cell grid rather than rewriting every cell. Reduces emission from O(rows × cols) to O(cols) for the new content.

## Threading Model for Background Work (LLM Streaming)

```
Event loop (main thread)              LLM worker thread (daemon)
  — input handling                      — calls LLM API, iterates chunks
  — state mutation (only here)          — produces typed events ONLY
  — render                              — NEVER touches UI state directly
  — output flush               ←───────────────────────────────────────
                                 typed event queue
                                 (the only thread-safe crossing)
                                 In prompt_toolkit: app.loop.call_soon_threadsafe()
                                 In Textual: post_message()
```

**The worker produces data. The event loop consumes it and mutates state.**

## Typewriter Effect — Streaming Rate Control Middleware

**Canonical name:** "Typewriter Effect" — established in game development (RPGs/visual novels, 1980s), codified for web by CSS-Tricks (2016), used by all major AI chat UIs (ChatGPT, Claude, Gemini, Perplexity). No Wikipedia article exists. npm: `typewriter-effect` (514k/week), `flowtoken` (LLM-specific).

**Important distinction** — two different things share this name:

| Variant | Input | What it does |
|---|---|---|
| Classic typewriter effect | Static, pre-known text | Animates character-by-character with CSS `steps()` or a JS timer |
| **LLM streaming typewriter** | Live async SSE stream (bursty) | Jitter-compensation middleware: buffers tokens, drains at controlled rate |

The LLM-specific variant (what this section describes) has **no established name** in any community. It is an unnamed implementation pattern that every major AI chat UI uses. Practitioners call it "making the streaming look smooth."

A Typewriter sits between the LLM chunk source and the event queue. It smooths bursty network delivery into a controlled reveal rate (~60fps) with adaptive backpressure.

```
LLM agent.stream() → Typewriter.receive(chunk) → (tick at 16ms) → downstream(delta) → event_queue
```

Key properties:
- **Adaptive rate:** 1 char/tick (calm, low backpressure) → 8 chars/tick (fast drain when model outpaces display)
- **Flush discipline:** call `flush()` before any structural event (turn end, abort, error) to drain before the spinner stops or status bar updates
- **Sentinel passthrough:** boundary tokens (`<think>`, `</think>`) bypass the Typewriter and go directly to the event queue — they must arrive whole, not split across ticks
- **Rate control:** prevents rapid `run_in_terminal` calls that cause cursor drift in PTY environments

Prior art: the `flowtoken` npm package and several internal agent implementations.

## Output Discipline

### Flush-on-newline, not flush-per-chunk

Buffer chunks until `\n`. One `run_in_terminal` call per complete line. Eager flushing of partial lines is prohibited: `_emit_line()` must append `\n` (cursor tracking requirement), which would create artificial line breaks mid-word.

Streaming feel for multi-line responses: as `\n` characters arrive through the Typewriter, each completed line appears progressively. Single-line responses appear at turn end when `flush()` is called.

### Scrollback vs in-app widgets

LLM output goes to scrollback via `run_in_terminal`. Never accumulate streaming content in a layout widget — this creates a dynamic-height element that inflates the footer on every frame and collapses at turn end.

**Footer height invariant:** footer height = f(input field content) only. Never a function of LLM output.

### Input field dynamic height

A multiline TextArea should use a callable height, not a static `Dimension`:

```python
input_field.window.height = lambda: max(1, min(input_field.buffer.text.count("\n") + 1, 8))
```

Static `Dimension(min=1, max=8)` without `preferred` causes the layout to fill to max when space is available. The callable reads actual buffer content.

## Protocol Boundary (Hermes Proto Pattern)

When the renderer and agent are in different languages or need independent deployment, use a typed protocol boundary over a pipe:

```
Renderer (TypeScript/Ink)  ←── newline-delimited JSON-RPC over stdio ──→  Agent (Python)
owns the screen                                                             owns domain logic
```

Hermes Agent (NousResearch, `github.com/NousResearch/hermes-agent`) implements this with TypeScript/Ink as the renderer and Python as the gateway, communicating over newline-delimited JSON-RPC. Events: `gateway.ready`, `message.delta`, `message.complete`, `tool.start/progress/complete`. Transport is mirrored to a WebSocket sidecar for dashboard embedding.

Same-language, single-process equivalent: typed event queue + `call_soon_threadsafe`:

```python
# Protocol events
@dataclass(frozen=True)
class TurnDelta:
    text: str

# Worker — enqueues only
event_queue.put(TurnDelta(text=chunk))
app.loop.call_soon_threadsafe(_drain_events)

# Event loop — sole state writer
def _apply_event(event):
    if isinstance(event, TurnDelta):
        stream_buf.feed(event.text)
```

## Known Failure Modes

| Failure | Cause | Fix |
|---|---|---|
| Cursor drift | Multiple `run_in_terminal` calls without `\n` termination | Always end writes with `\n`; use Typewriter for rate control |
| Sentinel splitting | Typewriter splits `<think>` character-by-character | Detect sentinels in the LLM thread; bypass Typewriter for sentinels |
| Screen corruption | Concurrent writes from render + worker threads | Single-writer model; all writes through framework pipeline |
| Flickering | No synchronized output; partial frame visible | BSU/ESU (`\033[?2026h/l`); shadow buffer diff |
| Footer inflation | LLM tokens in a layout widget | Route to scrollback via `run_in_terminal` |
| Input box too large | `Dimension(min=1, max=8)` without `preferred` fills to max | Callable height: `lambda: max(1, min(lines, 8))` |
| ESC mangling | `patch_stdout(raw=False)` converts `\x1b` → `?` | Use `patch_stdout(raw=True)` |
| State from wrong thread | Worker writes `phase`, counters directly | All state mutation via `call_soon_threadsafe` on event loop thread |
| `_current_app = None` | `asyncio.run(app.run_async())` isolates context in Task | Use `app.run()` (sync); threads access `app.loop` directly |

## Framework Selection

| Framework | Model | When to use |
|---|---|---|
| **prompt_toolkit** | Stateless renderer + explicit Buffer/Layout | Custom TUI; SSH/telnet embedding; fine-grained control |
| **Textual** | Reactive DOM + CSS layout + actor model | Full-featured app; multiple screens; built-in scrolling |
| **Ink** (TypeScript) | React component tree + hooks | TypeScript stack; React ecosystem |
| **Rich** | Rendering library (not a TUI framework) | CLI output; use as rendering backend inside a TUI |
| **urwid** | Signal-based widgets + pluggable mainloop | Legacy Python; GLib/Twisted integration |

## Complements

- `practices/tui-testing` — PTY-based integration tests; state assertions over render assertions
- `paradigms/concurrent` — threading models, event loops, actor model
- `paradigms/reactive` — reactive state and propagation
- `architecture/event-driven-architecture` — typed event channels; protocol boundary
- `architecture/ports-and-adapters` — renderer as adapter; agent as domain core

## References

- prompt_toolkit — https://python-prompt-toolkit.readthedocs.io/en/stable/
- Textual — https://textual.textualize.io/guide/
- Hermes Agent — https://github.com/NousResearch/hermes-agent
- i3 hacking howto — https://i3wm.org/docs/hacking-howto.html
- dwm — https://dwm.suckless.org
- The TTY demystified — https://www.linusakesson.net/programming/tty/
