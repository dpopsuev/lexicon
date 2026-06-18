---
id: svelte5-runes
title: Svelte 5 Runes Best Practices
description: Patterns for Svelte 5 reactivity — $state, $derived, $effect, untrack, performance pitfalls, canvas/WebGL integration.
labels: [typescript, svelte, reactivity, best-practices, performance]
sources:
  - https://svelte.dev/docs/svelte/$effect
  - https://github.com/sveltejs/svelte/issues/14697
  - https://github.com/sveltejs/svelte/issues/9944
  - https://webjose.hashnode.dev/svelte-reactivity-lets-talk-about-effects
  - https://mainmatter.com/blog/2025/03/11/global-state-in-svelte-5/
  - https://umaranis.com/2025/05/28/svelte-reactivity-beyond-the-basics/
  - https://www.dualmedia.fr/en/svelte-5-runes-react/
---

# Svelte 5 Runes Best Practices

## 1. $derived for values, $effect for side effects

If you're computing a value → `$derived`. If you're syncing with the outside world → `$effect`.

```svelte
// Good — computed value
let fullName = $derived(first + ' ' + last);

// Good — sync with external system
$effect(() => {
    document.title = fullName;
});

// Bad — using $effect to compute
$effect(() => {
    fullName = first + ' ' + last;  // use $derived instead
});
```

## 2. Never write $state inside rAF or high-frequency handlers

Each `$state` write triggers a Svelte update cycle. At 60fps, that's 60 DOM reconciliations per second.

```svelte
// Bad — triggers Svelte update 60x/sec
let fps = $state(60);
function render(timestamp) {
    fps = 1000 / delta;  // $state write in rAF → DOM thrash
    requestAnimationFrame(render);
}

// Good — plain variable, no reactivity overhead
let fps = 60;  // not $state
function render(timestamp) {
    fps = 1000 / delta;  // no Svelte update triggered
    requestAnimationFrame(render);
}
```

Same applies to: wheel handlers, mousemove, scroll, touchmove, resize.

## 3. Use untrack() to prevent unwanted dependency tracking

`$effect` tracks ALL `$state` reads in its synchronous execution path, including nested function calls.

```svelte
// Bug: $effect tracks lock via fitCamera() → systemCanMove(lock)
$effect(() => {
    if (nodes) {
        startSimulation();  // reads lock inside → $effect tracks lock
    }                       // → lock change re-runs simulation = 3s freeze
});

// Fix: untrack prevents tracking reads inside startSimulation
$effect(() => {
    const _n = nodes;  // explicitly track nodes
    if (_n) {
        untrack(() => startSimulation());  // reads inside are invisible
    }
});
```

## 4. Camera/transform state should NOT be $state

For WebGL/canvas rendering, camera position (x, y, zoom) changes on every interaction. These should be plain `let` variables read by the rAF render loop, not `$state`.

```svelte
// Bad — each wheel event triggers Svelte
let camX = $state(0);
let camZoom = $state(1);

// Good — plain variables, render loop reads directly
let camX = 0;
let camZoom = 1;
```

## 5. Use $state.raw for large objects you replace, not mutate

`$state` creates deep proxies. `$state.raw` only reacts to reassignment.

```svelte
// Good for large arrays/objects you replace entirely
let items = $state.raw(fetchedData);
items = newData;  // reactive
items.push(x);    // NOT reactive (no proxy)

// Default $state — for objects you mutate
let form = $state({ name: '', email: '' });
form.name = 'Daniel';  // reactive (proxy catches it)
```

## 6. One purpose per $effect

```svelte
// Bad — mixed concerns
$effect(() => {
    fetchData(query);
    updateTitle(query);
    logAnalytics(query);
});

// Good — separate effects
$effect(() => { fetchData(query); });
$effect(() => { document.title = query; });
```

## 7. Always clean up in $effect and onMount

```svelte
$effect(() => {
    const interval = setInterval(poll, 1000);
    return () => clearInterval(interval);  // cleanup
});

onMount(() => {
    canvas.addEventListener('wheel', handler, { passive: false });
    return () => canvas.removeEventListener('wheel', handler);
});
```

## 8. Canvas/WebGL integration pattern

Use `onMount` for setup, rAF for rendering, `$effect` only for data-change response.

```svelte
onMount(() => {
    gl = canvas.getContext('webgl2');
    setupShaders();
    startSimulation();
    animFrame = requestAnimationFrame(render);
    return () => {
        cancelAnimationFrame(animFrame);
        simulation.stop();
    };
});

// Only re-initialize when DATA changes, not camera/zoom
$effect(() => {
    const _n = nodes;  // track data prop
    if (_n && gl) {
        untrack(() => startSimulation());  // don't track internal reads
    }
});

// render() is pure rAF — reads plain variables, no $state
function render(timestamp) {
    buildViewMatrix(camX, camY, camZoom);
    drawNodes();
    drawEdges();
    animFrame = requestAnimationFrame(render);
}
```

## 9. Avoid $state in template expressions that update per-frame

DOM elements with dynamic styles bound to per-frame values cause layout thrash.

```svelte
<!-- Bad: 60 divs with dynamic height, re-rendered per frame -->
{#each frameHistory as f}
    <div style="height:{f}px"></div>
{/each}

<!-- Good: static elements, or update via Canvas 2D / WebGL -->
```

## 10. Async reads are NOT tracked

Values read after `await` or inside `setTimeout` are invisible to `$effect`.

```svelte
$effect(() => {
    const val = count;  // tracked ✓
    setTimeout(() => {
        console.log(count);  // NOT tracked ✗
    }, 100);
});
```
