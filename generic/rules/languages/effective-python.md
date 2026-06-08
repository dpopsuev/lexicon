---
id: effective-python
title: Effective Python
description: Python-specific conventions for imports, type safety, error handling, and code hygiene. Derived from PEP 8, PEP 484, the official typing best practices, and the Python Anti-Patterns catalogue.
labels: [python, code-hygiene, best-practices, type-safety]
---

# Effective Python

**Every Python file must follow these conventions.**

## 1. All imports at the top of the module

PEP 8 is unambiguous: imports go at the top of the file, after module docstring and before module globals.

```python
# Good — all imports visible at a glance
from __future__ import annotations
import asyncio
import json
from pathlib import Path
from typing import Any

from myapp.events import Event
```

```python
# Bad — import hidden inside function
def process(data: str) -> None:
    import json          # repeated on every call; hides dependency
    return json.loads(data)
```

**The two legitimate exceptions:**

1. **Circular import** — move the import inside the function as a last resort. Add a comment naming the cycle. Fix the cycle if possible.
2. **Optional/heavy dependency** — import only when the path actually runs, e.g. `from langchain_openai import ChatOpenAI` inside a factory function that may not be called if a different provider is used.

```python
# Good — optional dependency with explicit comment
def _build_openai_model(spec: str) -> object:
    # Inline: this dependency is optional and not installed in all environments.
    from langchain_openai import ChatOpenAI
    return ChatOpenAI(model=spec)
```

Enable `PLC0415` in ruff to catch new violations at lint time:

```toml
# pyproject.toml
[tool.ruff.lint]
select = ["PLC0415"]   # import-outside-toplevel
```

---

## 2. `object` over `Any` when the type truly is unknown

`Any` disables all type checking for that value. Use it only when the type system genuinely cannot express the constraint. Use `object` when a value can be anything but you only call universally-available methods (`str()`, `repr()`, `isinstance()`).

```python
# Good — object: accepts everything, checking preserved
def print_formatted(o: object) -> None:
    if isinstance(o, int):
        print(f"{o:02d}")
    else:
        print(o)

# Good — Any: type cannot be expressed (heterogeneous payload dict)
payload: dict[str, Any]

# Bad — Any disables checking; object is correct here
def log(value: Any) -> None:
    print(str(value))
```

For function arguments that accept protocol-compatible objects, prefer abstract types from `collections.abc`:

```python
# Good — accepts any iterable, not just list
from collections.abc import Iterable, Mapping, Sequence

def process(items: Iterable[str]) -> list[int]: ...
def lookup(data: Mapping[str, int], key: str) -> int: ...
```

---

## 3. `Protocol` over ABC for structural typing

Use `typing.Protocol` (Python 3.8+) to define interfaces without requiring inheritance. Callers are not forced to import or subclass — duck typing is preserved at the type-checker level.

```python
# Good — structural protocol; no import required by callers
from typing import Protocol, runtime_checkable

@runtime_checkable
class Renderer(Protocol):
    def print_completion(self, text: str) -> None: ...
    def print_error(self, message: str) -> None: ...

def attach(graph: object, renderer: Renderer) -> None: ...
```

```python
# Bad — ABC forces inheritance and import coupling
from abc import ABC, abstractmethod

class RendererBase(ABC):
    @abstractmethod
    def print_completion(self, text: str) -> None: ...
```

Rules:
- Prefer narrow protocols (1–3 methods) over wide ones. Split a wide Protocol by responsibility.
- Use `@runtime_checkable` when `isinstance()` checks are needed.
- `ClassVar` for class-level attributes on Protocol subclasses to avoid "instance variable conflicts".

---

## 4. Modern type annotation syntax (Python 3.10+)

Use shorthand syntax from PEP 604 / PEP 585. Do not import from `typing` when built-ins work.

```python
# Good — modern syntax
def foo(x: str | int | None) -> list[str]: ...
items: dict[str, list[int]] = {}

# Bad — legacy aliases
from typing import Dict, List, Optional, Union
def foo(x: Union[str, int, Optional[str]]) -> List[str]: ...
```

Use `TypeAlias` for genuine aliases, not for variable assignments:

```python
# Good
from typing import TypeAlias
EventType: TypeAlias = str

# Bad — not a type alias, just a variable
EventType = str
```

Use `from __future__ import annotations` at the top of every file to enable PEP 563 postponed evaluation — allows forward references without quotes.

---

## 5. `dataclass` over manual `__init__`

Use `@dataclass` for classes whose primary purpose is holding structured data. Use `frozen=True` for value objects. Use `field(default_factory=...)` for mutable defaults.

```python
# Good
from dataclasses import dataclass, field

@dataclass(frozen=True)
class Event:
    type: str
    payload: object = None
    correlation_id: str = field(default_factory=lambda: str(uuid4()))

# Bad — manual __init__ for a data holder
class Event:
    def __init__(self, type: str, payload: object = None) -> None:
        self.type = type
        self.payload = payload
```

For performance-sensitive code with many instances, add `slots=True` (Python 3.10+):

```python
@dataclass(slots=True, frozen=True)
class Point:
    x: float
    y: float
```

---

## 6. Mutable default arguments — never

Python evaluates default argument values once at function definition, not at call time. Mutable defaults are shared across all calls.

```python
# Bad — list is shared across all calls; mutations accumulate
def add_item(item: str, items: list[str] = []) -> list[str]:
    items.append(item)
    return items

# Good — None sentinel, create fresh each call
def add_item(item: str, items: list[str] | None = None) -> list[str]:
    if items is None:
        items = []
    items.append(item)
    return items
```

The same applies to `dict`, `set`, and any other mutable type.

---

## 7. Never use bare `except`

Bare `except:` catches `SystemExit`, `KeyboardInterrupt`, and `GeneratorExit` — signals that should propagate. Always name the exception type.

```python
# Bad — swallows Ctrl+C, process.kill(), and generator cleanup
try:
    do_something()
except:
    pass

# Bad — catches too much; hides programming errors
try:
    do_something()
except Exception:
    pass

# Good — catch what you can handle, let the rest propagate
try:
    result = json.loads(data)
except json.JSONDecodeError as exc:
    raise ValueError(f"invalid JSON: {exc}") from exc
```

Use `contextlib.suppress` for explicit intentional swallowing:

```python
import contextlib

with contextlib.suppress(FileNotFoundError):
    path.unlink()
```

---

## 8. Constants at module level, not embedded literals

Name any value that appears more than once or whose meaning is not obvious from context. Use `typing.Final` to signal immutability.

```python
# Good
from typing import Final

MAX_RESULT_LINES: Final = 8
_REFRESH_HZ: Final = 20
_TOOL_PENDING_BG: Final = "#282832"

# Bad — magic number, no name, no context
if len(lines) > 8:
    display = lines[:8]
```

---

## 9. Context managers for resource cleanup

Never manually close files, sockets, or locks. Always use `with`.

```python
# Good
with path.open("a", encoding="utf-8") as f:
    f.write(line + "\n")

# Bad — file leaked on exception
f = path.open("a", encoding="utf-8")
f.write(line + "\n")
f.close()
```

Implement `__enter__` / `__exit__` or use `@contextlib.contextmanager` for custom resource managers.

---

## 10. `ClassVar` for class-level attributes

Attributes defined at class level that are not instance attributes must be annotated with `ClassVar`. Without it, mypy and type checkers treat them as instance attributes, causing false positives.

```python
# Good
from typing import ClassVar

class SessionNode:
    name: ClassVar[str] = "session"
    subscribes: ClassVar[list[str]] = ["user_input", "messages"]

# Bad — treated as instance attribute by type checkers
class SessionNode:
    name = "session"
    subscribes = ["user_input", "messages"]
```

---

## 11. Explicit `re-raise` to preserve traceback

When catching an exception to add context and re-raising, always use `raise ... from exc` to preserve the original traceback. Bare `raise` is correct when re-raising without modification.

```python
# Good — chain preserved; original traceback visible
try:
    result = store.load(session_id)
except json.JSONDecodeError as exc:
    raise ValueError(f"corrupt session {session_id}") from exc

# Good — bare re-raise: adds logging, preserves everything
try:
    node.process(event)
except asyncio.CancelledError:
    log.warning("turn cancelled")
    raise

# Bad — original traceback lost
try:
    result = store.load(session_id)
except json.JSONDecodeError:
    raise ValueError("corrupt session")  # no `from exc`
```

---

## 12. f-strings over `.format()` and `%`

f-strings (PEP 498, Python 3.6+) are faster, more readable, and catch name errors at parse time.

```python
# Good
name = "worker"
msg = f"agent {name!r} started in {cwd}"

# Bad
msg = "agent %r started in %s" % (name, cwd)
msg = "agent {} started in {}".format(name, cwd)
```

Do not embed complex expressions in f-strings — extract to a variable first.

---

## Anti-pattern Summary

| Anti-pattern | Rule | Fix |
|---|---|---|
| `import x` inside a function | §1 | Hoist to module top |
| `except:` bare | §7 | Name the exception type |
| Mutable default arg (`def f(x=[])`) | §6 | Use `None` sentinel |
| `Any` when `object` suffices | §2 | Use `object` |
| `List[str]` from typing | §4 | Use `list[str]` |
| `Optional[X]` | §4 | Use `X \| None` |
| `Union[X, Y]` | §4 | Use `X \| Y` |
| Manual `__init__` for data holder | §5 | Use `@dataclass` |
| Missing `ClassVar` on class attr | §10 | Add `ClassVar[T]` |
| Wide ABC instead of Protocol | §3 | Use narrow `Protocol` |
| Magic number in logic | §8 | Name as `Final` constant |
| `f.close()` manually | §9 | Use `with` |
| `raise ValueError(...)` without `from` | §11 | Add `from exc` |
| `%` or `.format()` string | §12 | Use f-string |

## Linter Configuration

Ruff rule groups covering these conventions:

```toml
[tool.ruff.lint]
select = [
    "E", "W",       # PEP 8 style
    "F",            # undefined names, unused imports
    "I",            # isort — import ordering
    "UP",           # pyupgrade — modern syntax (§4)
    "B",            # flake8-bugbear — mutable defaults (§6), bare except (§7)
    "SIM",          # simplify — contextlib.suppress (§9)
    "RUF",          # ruff-specific
    "PLC0415",      # import-outside-toplevel (§1)
    "TRY",          # exception handling (§7, §11)
]
```

## References

- [PEP 8 — Style Guide for Python Code](https://peps.python.org/pep-0008/)
- [PEP 484 — Type Hints](https://peps.python.org/pep-0484/)
- [PEP 544 — Protocols](https://peps.python.org/pep-0544/)
- [PEP 557 — Data Classes](https://peps.python.org/pep-0557/)
- [Typing Best Practices](https://typing.python.org/en/latest/reference/best_practices.html)
- [The Hitchhiker's Guide to Python](https://docs.python-guide.org/writing/style/)
- [Python Anti-Patterns](http://docs.quantifiedcode.com/python-anti-patterns/)
