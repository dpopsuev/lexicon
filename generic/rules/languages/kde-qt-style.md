---
id: kde-qt-style
title: KDE / Qt Coding Style & Contribution Guide
description: C++/Qt coding conventions for KDE-native applications, Qt contribution workflow via Gerrit, and KDE contribution workflow via invent.kde.org. Derived from wiki.qt.io/Qt_Coding_Style, wiki.qt.io/Qt_Contribution_Guidelines, and develop.kde.org.
labels: [kde, qt, cpp, code-hygiene, best-practices, conventions, contribution]
priority: 20
---

# KDE / Qt Coding Style & Contribution Guide

## Contribution Workflows

### Qt — https://wiki.qt.io/Qt_Contribution_Guidelines

- Code review: Gerrit at https://codereview.qt-project.org
- Bug tracker: https://bugreports.qt.io
- One Qt account (https://login.qt.io) covers both
- Workflow per patch:
  1. Branch: `git checkout -b my-fix origin/dev`
  2. Commit (one logical change per commit, amend with `--amend`)
  3. Push: `git push gerrit HEAD:refs/for/dev`
  4. Add reviewers manually — patch goes unnoticed without them
  5. Fix CI failures and re-push
  6. Stage for integration once approved — you are responsible
- Small fixes: attach a patch to a bugreports.qt.io ticket (lower friction, slower integration)
- Commit policy: https://wiki.qt.io/Commit_Policy

### KDE — https://develop.kde.org/docs/getting-started/

- GitLab at https://invent.kde.org
- Build tool: `kde-builder` (builds entire KDE stack from source)
- Language entry points: C++/KXmlGui, C++/Kirigami, Python/Kirigami, Rust/Kirigami
- KDE officially supports Rust bindings for Kirigami (develop.kde.org/docs/getting-started/rust/)
- New project path: propose → community review → KDE Review → release
- IRC: #kde-devel on irc.libera.chat
- Mailing lists: kde-devel@kde.org

---

## Qt Coding Style (https://wiki.qt.io/Qt_Coding_Style)

KDE style is derived from Qt style with a few stricter rules noted below.

### Formatting

- **Indentation**: 4 spaces, no tabs
- **Line length**: < 100 characters; comments and docs < 80
- **Blank lines**: at most one blank line between statements
- **One statement per line** — never `if (foo) bar();` on one line

### Braces

```cpp
// Control flow — attached brace (same line)
if (condition) {
} else {
}

// Function and class bodies — brace on new line
void foo(int x)
{
    // body
}

class Moo
{
};

// KDE rule (stricter than Qt): always use braces, even for single-line bodies
// Qt allows:   if (foo) return;
// KDE requires:
if (foo) {
    return;
}
```

### Pointer and reference alignment

```cpp
// Space before * or &, no space after — type owns the modifier
char *name;
const QString &value;
const char * const literal = "hello";

// No C-style casts — always use C++ named casts
char *buf = reinterpret_cast<char *>(malloc(size));  // correct
char *buf = (char *)malloc(size);                    // wrong
```

### Variables

```cpp
// Declare one variable per line
int height;
int width;

// Wait until first use to declare
// No abbreviations — full names
short counter;       // correct
short Cntr;          // wrong

// Naming
int myCounter;       // camelCase, starts lowercase
class MyWidget {};   // PascalCase, starts uppercase
```

### Switch statements

```cpp
switch (myEnum) {
case Value1:
    doSomething();
    break;
case Value2:
case Value3:
    doSomethingElse();
    Q_FALLTHROUGH();
default:
    defaultHandling();
    break;
}
```

### Jump statements

```cpp
// No else after return/break/continue
if (done)
    return result;
doMoreWork();  // no else needed
```

### Spacing within expressions

```cpp
// Space after flow-control keywords, before brace
if (foo) {   // correct
if(foo){     // wrong

// Surround binary operators with spaces
x = a + b;

// No space after cast
int n = static_cast<int>(myFloat);
```

---

## Qt / KDE Naming Conventions

```cpp
// Member variables: m_ prefix
class Bridge {
    QLocalServer *m_server;
    QString       m_path;
};

// Static variables: s_ prefix
static QString s_instance;

// Constants: k prefix (KDE convention)
static constexpr QLatin1StringView kProtocol{"kathode/1"};

// Public Qt classes: Q prefix
class QWidget {};

// Acronyms: camel-cased
QXmlStreamReader   // correct
QXMLStreamReader   // wrong
```

---

## Qt String Literals (C++/Qt6 pitfall)

```cpp
// WRONG — const char* where QString is needed
app.setApplicationName("kathodectl");

// WRONG — QStringLiteral is not constexpr
static constexpr auto kName = QStringLiteral("foo");

// CORRECT — compile-time ASCII, zero cost, constexpr-safe
using namespace Qt::Literals::StringLiterals;
static constexpr auto kName = "foo"_L1;   // QLatin1StringView

// CORRECT — runtime QString from literal
app.setApplicationName(u"kathodectl"_s);  // _s = QStringLiteral equivalent
app.setApplicationName(QStringLiteral("kathodectl"));

// CORRECT — static (not constexpr) QString constant
static const QString kFoo = QStringLiteral("foo");
```

---

## Qt Object Model Rules

```cpp
// Every QObject subclass must have Q_OBJECT — required for signals/slots and qobject_cast
class Bridge : public QObject {
    Q_OBJECT
public:
    explicit Bridge(QObject *parent = nullptr);
};

// Use qobject_cast, never dynamic_cast
auto *w = qobject_cast<KWin::Window *>(obj);

// override on every reimplemented virtual — never repeat 'virtual'
void keyPressEvent(QKeyEvent *e) override;   // correct
virtual void keyPressEvent(QKeyEvent *e);    // wrong

// Range-for on const containers
for (const auto &item : std::as_const(list)) { }
```

---

## Include Order (KDE convention)

```cpp
#include "own_header.h"         // 1. own header
#include "other_local.h"        // 2. other local headers
#include <kwin/workspace.h>     // 3. KDE/KF6 headers
#include <KNotification>        // 4. other KF6
#include <QString>              // 5. Qt headers
#include <unistd.h>             // 6. system headers
```

---

## QJsonObject initializer (Qt6 pitfall)

```cpp
// WRONG — initializer_list constructor unreliable in Qt6
QJsonObject obj = {{"key", "value"}};

// CORRECT
QJsonObject obj;
obj.insert(u"key"_s, u"value"_s);
```

---

## Key References

| Resource | URL |
|---|---|
| Qt Contribution Guidelines | https://wiki.qt.io/Qt_Contribution_Guidelines |
| Qt Coding Style | https://wiki.qt.io/Qt_Coding_Style |
| Qt Coding Conventions | https://wiki.qt.io/Coding_Conventions |
| Qt API Design Principles | https://wiki.qt.io/API_Design_Principles |
| KDE Getting Started | https://develop.kde.org/docs/getting-started/ |
| KDE Library Code Policy | https://community.kde.org/Policies/Library_Code_Policy |
| KDE Invent (GitLab) | https://invent.kde.org |
| Qt Gerrit | https://codereview.qt-project.org |
| Qt Bug Tracker | https://bugreports.qt.io |
