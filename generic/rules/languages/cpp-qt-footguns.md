---
id: cpp-qt-footguns
title: C++ / Qt Footguns — Top 10 Common Mistakes
description: The most dangerous and common C++ pitfalls in Qt/KDE codebases. Derived from the C++ Core Guidelines (github.com/isocpp/CppCoreGuidelines) and Qt documentation.
labels: [cpp, qt, kde, safety, code-hygiene, footguns, undefined-behavior]
priority: 30
---

# C++ / Qt Footguns — Top 10 Common Mistakes

**Rule: every item on this list is either UB, a crash, or a silent logic error. Check for all of them on every code review.**

Reference: https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md

---

## 1. Uninitialized `enum class` and POD members in structs

`enum class` values and primitive types (`int`, `double`, `bool`, raw pointers) are **NOT** zero-initialized by default in struct members. Reading them before assignment is Undefined Behaviour.

```cpp
// WRONG — type has indeterminate value until assigned
struct Incident {
    IncidentType type;   // UB if read before write
    qint64 timestamp;    // UB
};

// CORRECT — always provide in-class default initialisers
struct Incident {
    IncidentType type      = IncidentType::ServiceFailure;
    qint64       timestamp = 0;
    Severity     severity  = Severity::Error;
};
```

**Rule:** Every scalar member of a struct/class must have a default initialiser at declaration. No exceptions.

---

## 2. QNetworkReply double-free — Qt parent + deleteLater

`QNetworkAccessManager::post()` returns a `QNetworkReply*` parented to the `QNetworkAccessManager`. When the manager is destroyed (e.g. goes out of scope), it **destroys all its children**. Calling `deleteLater()` on the reply schedules a second delete → use-after-free crash.

```cpp
// WRONG — double-free when nam is destroyed at end of scope
QNetworkAccessManager nam;                              // stack
QNetworkReply *reply = nam.post(request, body);         // reply parented to nam
...
reply->deleteLater();                                   // schedules delete #1
// end of scope: nam destroyed → deletes reply (delete #2) → crash

// CORRECT — let the parent own it, never call deleteLater
QNetworkAccessManager nam;
QNetworkReply *reply = nam.post(request, body);
QEventLoop loop;
QObject::connect(reply, &QNetworkReply::finished, &loop, &QEventLoop::quit);
loop.exec();
const QByteArray data = reply->readAll();
// Do NOT call reply->deleteLater() — nam will delete it on scope exit
```

**Rule:** Never call `deleteLater()` on a `QObject` that has a parent. Either own it (no parent, `QScopedPointer`) or let the parent own it (no manual delete).

---

## 3. `processEvents()` reentrancy inside slot handlers

`QCoreApplication::processEvents()` or `QEventLoop::exec()` inside a slot pumps the **entire** event loop. Any pending signal — user click, timer, network event — fires **re-entrantly**. If those signals mutate shared state your handler is currently reading, you get corruption or double-execution.

```cpp
// WRONG — user can click "Analyse" again while analysing
void MainWindow::onAnalyseClicked()
{
    statusLabel->setText("Analysing...");
    QApplication::processEvents();          // re-entrant: second click fires here
    const auto result = provider->analyse(bundle);   // bundle may be stale
}

// CORRECT — disable the trigger before any event pump
void MainWindow::onAnalyseClicked()
{
    m_analyseButton->setEnabled(false);
    statusLabel->setText("Analysing...");
    const auto result = provider->analyse(bundle);
    m_analyseButton->setEnabled(true);
}
```

**Rule:** Before any blocking call that pumps the event loop (network, QEventLoop, processEvents), disable all UI triggers that could mutate the state you are operating on.

---

## 4. Dangling `const &` into growing containers

Returning a `const T&` into a `QVector`, `std::vector`, or `QList` is safe **until the container reallocates**. Any `append()`, `insert()`, or `resize()` can invalidate all existing references and iterators.

```cpp
// WRONG — reference may dangle after any append to m_events
const Event &e = model->eventAt(row);
model->appendEvent(newEvent);   // reallocation possible → e is dangling
doSomethingWith(e);             // UB

// CORRECT — copy the value if the container may mutate
const Event e = model->eventAt(row);   // value, not reference
```

**Rule:** Never store or return a `const T&` into a growable container across any call that may append to it. Return by value or document the narrow validity window explicitly.

---

## 5. Static local with non-trivial destructor

A `static` local variable with a non-trivial destructor (e.g. `static const QStringList`, `static const QString`) is initialised on first call (thread-safe in C++11) and destroyed at program exit. In library code, destruction order relative to other statics is undefined. Also: it creates a hidden mutex per static (C++11 magic static), which adds overhead.

```cpp
// WRONG — non-trivial destructor, hidden mutex, undefined destruction order
static const QStringList kKnown = {
    QStringLiteral("__REALTIME_TIMESTAMP"), ...
};

// CORRECT — constexpr array, zero overhead, no destructor
static constexpr QLatin1StringView kKnown[] = {
    "__REALTIME_TIMESTAMP"_L1,
    "PRIORITY"_L1,
    ...
};
```

**Rule:** No `static` local variables with non-trivial constructors or destructors in library code. Use `constexpr` arrays or function-local statics of trivial types only.

---

## 6. Narrowing `qsizetype` → `int`

Qt6 uses `qsizetype` (= `ptrdiff_t`, 64-bit on 64-bit platforms) for container sizes. Assigning to `int` silently truncates if the container has > 2 billion elements (unlikely but legally UB). More importantly, **mixing `int` and `qsizetype` in comparisons generates signed/signed promotions that the compiler may warn about**.

```cpp
// WRONG — narrowing, potential truncation
const int row = m_events.size();    // qsizetype → int

// CORRECT
const qsizetype row = m_events.size();
// or:
const auto row = m_events.size();
```

**Rule:** Use `auto` or `qsizetype` when working with container sizes. Only cast to `int` at a Qt model boundary where the API requires it (`rowCount()` returns `int`).

---

## 7. Missing `explicit` on single-argument constructors

A constructor taking one argument is an implicit conversion constructor unless marked `explicit`. This allows accidental implicit conversions that silently construct objects.

```cpp
// WRONG — QString implicitly constructs MyPath
class MyPath {
public:
    MyPath(const QString &s);   // implicit — danger
};
void process(MyPath path);
process("some/string");   // silently constructs MyPath — intended?

// CORRECT
class MyPath {
public:
    explicit MyPath(const QString &s);
};
process("some/string");   // compile error — good, forces intentionality
```

**Rule:** Every constructor taking a single argument must be `explicit` unless implicit conversion is the intended design.

---

## 8. Using a moved-from object

After `std::move(x)`, `x` is in a **valid but unspecified state**. Using it for anything other than re-assignment or destruction is UB or silent logic error.

```cpp
// WRONG — m_pending is moved-from, then used in the next iteration
QVector<int> m_pending;
auto copy = std::move(m_pending);
process(copy);
m_pending.append(42);   // OK — but easy to accidentally read m_pending before re-assigning

// CORRECT — move then immediately invalidate
auto copy = std::move(m_pending);
m_pending = {};          // explicit reset makes state clear
process(copy);
```

**Rule:** After `std::move()`, treat the source as write-only until explicitly re-assigned or destroyed.

---

## 9. Virtual destructor missing from interface base classes

If a base class has any `virtual` method and objects are deleted through a base pointer, the destructor **must** be `virtual`. Without it, only the base destructor runs → derived class resources leak.

```cpp
// WRONG — deleting via IAiProvider* only calls ~IAiProvider()
class IAiProvider {
public:
    virtual AnalysisResult analyse(...) = 0;
    ~IAiProvider();   // non-virtual → leak when deleting derived
};

// CORRECT
class IAiProvider {
public:
    virtual AnalysisResult analyse(...) = 0;
    virtual ~IAiProvider() = default;   // or = 0
};
```

**Rule:** Every class with any `virtual` method must have a `virtual` destructor. No exceptions.

---

## 10. QObject ownership — parent-less heap allocation

A `QObject` created on the heap without a parent is not tracked by Qt's object tree. If an exception is thrown or an early return is taken before explicit deletion, it leaks. If both the parent and the explicit delete fire, it double-frees.

```cpp
// WRONG — orphan QObject, manual ownership, leak/double-free risk
QNetworkReply *reply = new QNetworkReply(...);  // no parent
// ... exception thrown here → leak
delete reply;

// CORRECT — use parent ownership
QNetworkReply *reply = nam.post(...);   // parented to nam, deleted by nam
// OR: use QScopedPointer for explicit lifetime
QScopedPointer<QNetworkReply, QScopedPointerDeleteLater> reply(nam.post(...));

// CORRECT — for non-QObject, use smart pointers
auto provider = std::make_unique<OllamaProvider>();
```

**Rule:** Every heap-allocated `QObject` must either have a `QObject*` parent or be wrapped in `QScopedPointer`. Bare `new QObject` with manual `delete` is forbidden. For non-`QObject` classes, use `std::unique_ptr` or `std::shared_ptr`.

---

## Qt/KDE-specific additions

### Never `static const QString` at global scope in library code

Qt's global `QString` statics have undefined initialisation order relative to the Qt runtime. Use `Q_GLOBAL_STATIC` or `constexpr QLatin1StringView`.

### `QVariant::fromValue<T>()` requires `Q_DECLARE_METATYPE(T)`

Without the macro, `fromValue` silently returns an invalid `QVariant` that crashes on `value<T>()`. Always declare custom types.

### `QPointer` for observers, not owners

When a slot holds a pointer to another `QObject` that might be deleted externally, use `QPointer<T>` which auto-nulls on deletion.

---

## Enforcement

Enable these in CMakeLists.txt to catch issues at compile time:
```cmake
add_compile_options(
    -Wall -Wextra
    -Wshadow
    -Wzero-as-null-pointer-constant   # nullptr not 0
    -Wold-style-cast                   # no C casts
    -Wsuggest-override                 # missing override keyword
    -Wnon-virtual-dtor                 # missing virtual destructor
)
```

Run regularly with sanitizers:
```bash
cmake -DENABLE_ASAN=ON -DENABLE_UBSAN=ON ...
```
