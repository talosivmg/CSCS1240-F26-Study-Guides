# Session 15 · Following references in Python

**CSCS1240F26 / Worked teaching companion**

When you write **`fruit.next.prev = fruit`**, does Python make another apple? That student question ties the whole class together. If the right side is a name, Python finds the object the name already refers to. If it is a call, Python must perform the call to find out what object comes back. The difference explains both how a linked list holds together and how the same name can first stand for the number seven and later for a function.

**Recording and evidence.** [JoesTA · CSCS1240F26 15 · 56:47](https://www.youtube.com/watch?v=abrPTt833-c&t=2s). Times below are elapsed from the beginning, not from the link’s two-second playback position. The saved English auto-captions run from the OBS opening to the closing exchange (1,175 cues). All 57 whole-minute PNGs from 00:00–56:00, eight event frames, and the 56:45 closing frame were verified against the recording’s capture manifests. Times are seek targets, not exact frame presentation times. Screens establish what was visible; a separately tested program is not evidence of a classroom run.

## 1 · What makes an Item a link? 00:00–09:00

The opening is a course check-in: the instructor describes **2059** as the last available challenge *at the time* and says only one person had completed it at his last check. This is historical context, not a current deadline or a statement of the task’s requirements. After opening draw.io by mistake, he returns to the two files that matter: a definition on the left and a program that uses it on the right. He asks what an **`Item`** is *before* explaining any new syntax.

**On the course Discord screen (01:00), not a new in-class solution:** a reply to a question about 2059 says the exception should be caught and sent to standard output for that challenge, while noting this was not the instructor’s preferred practice for the factory example. A separate 2058 post reports a mismatch between **`None`** and an expected number, and a rejected exception. These are different challenges: a student’s report is not a verified grader rule. The screenshot does not establish a fix for 2058.

Think of an apple written on a card. The card has room for two directions: where to find the next card and where to find the previous one. The word “apple” is neither direction. Here each card is an **`Item`** instance (one particular node). Its **`object`** attribute holds the useful data, or *payload*. Its **`next`** and **`prev`** attributes can refer to other nodes. A *doubly linked list* is the structure those connections make; it is not a built-in Python list, nor does it depend on the nodes sitting next to each other in memory.

The terminal provides a map of the exercise’s history. PowerShell scrollback from **`git log --oneline`** lets the class look back through changes. Earlier messages name direct assignments, a second reference to fruit, use of the backward link, and addition of a constructor. The visible **`git checkout master~18`** selects a past revision; the terminal later identifies **`2499ca0`** as “Item: addition of constructor.” A detached HEAD means the checkout points directly at a commit rather than following a branch. Entries about later features are history on the screen, not methods taught or executed here.

Why pick out line 12? In the driver, **`iter = fruit`** gives the first node a second name. It does not make a second node or even copy the apple string. Keeping one name at the front makes it possible to move the other from card to card without losing the starting point. This line is discussed early but **executes after** the three nodes have been connected.

**Key items to scan**

- **`Item`** — one node; several linked instances form the list.
- **`object`** — the payload attribute; the first node will refer to the string **`"apple"`** here.
- **`next`** — the forward link. **`prev`** — the backward link; “previous” in speech is spelled **`prev`** in the code.
- **`git log --oneline`** — short commit IDs and subjects. **`git checkout master~18`** — selects the old constructor revision, not the most recent implementation.
- **`iter = fruit`** — two names, initially one node; useful when one name needs to move.

## 2 · Name, value, type: what does the call make? 09:00–21:00

A student calls a variable “something that represents something else.” The class refines that: the **name** distinguishes what we want to look up; the **value** is what we get; the **type** tells us what kind of thing we have. In algebra we often assume the value is a number. Here it might be the string **`"apple"`**, an **`Item`**, or even a function. The name **`fruit`** is not permanently typed as any of these. The object it currently denotes has the type.[1][3]

The instructor offers a category test: a dog is a kind of animal; an individual dog is not the category itself. Likewise **`Item`** is a class (and itself a Python object), whereas **`Item()`** normally produces one new instance of that class. The class tells us which names the programmer intends to use for a node. It does not decide which other node is this particular apple’s successor.

**Annotated teaching version — guide-added comments. Save as **`item.py`**.**

```python
class Item:                     # Define the kind of node; no fruit nodes exist yet.
    object = None               # Class default: no payload supplied here.
    next = None                 # No forward neighbor until a link is assigned.
    prev = None                 # No backward neighbor until a link is assigned.

    def __init__(self, obj=None):  # obj defaults to None if the caller omits it.
        self.object = obj       # Set this instance's payload, not the class default.
```

Type **`Item()`** and ordinary construction has two jobs. **`__new__`** creates the instance; if it returns an instance of the requested class, the construction machinery calls **`__init__`** on it. **`self`** is the instance being initialized, not a number that the programmer must turn back into an object. The caller does not supply **`self`**. With **`Item("banana")`**, **`obj`** refers to the string **`"banana"`**; the initializer stores that reference on just this node. It need not return the node: it finishes with an implicit return of **`None`**. The *class call* yields the instance.[1]

There is a useful detail on the left of the screen: the three defaults are indented under the **class**, not inside the method. At the start, reading a new node’s **`next`** finds the class value **`None`**. Assigning that node’s **`next`** creates an attribute on the instance that takes precedence over the default. It does not change every node’s **`next`**. The initializer already assigns **`self.object`**, so even the no-argument instance starts with its own **`object`** attribute.

**Supplemental precision — the memory sketch.** The instructor asks two questions about a type: how much storage is needed, and how is it organized? This helps distinguish a node from a string. Do not turn the sketch into three literal, consecutive memory fields in source order. Python specifies observable attributes and operations, not those offsets. Class methods are not copied into each instance. The ordinary object model gives each object an identity; **`id()`** returns an integer representing it. That integer is an address in CPython, not a portable address for reading bytes. Allocation can fail, but CPython manages a private heap: a new node does not require a fresh operating-system request every time.[1][2]

**Key items to scan**

- **Name / value / type** — **`fruit`** is the name; its bound object is a particular node; that node’s type is **`Item`**.
- **`Item()`** — creates a new node whose payload is initially **`None`**. **`Item("banana")`** — creates a different node holding banana.
- **`__new__`** — creation. **`__init__`** — initialization of the instance already created. **`self`** — that instance.
- **`obj=None`** — omission is allowed; the default is not the text **`"None"`**.
- **Class default / instance attribute** — **`Item.next`** can still be **`None`** after the apple node has acquired its own forward link.
- **`id()`** — identifies a live object; you follow a link with attribute access, not arithmetic on its ID.

## 3 · Read the assignment at the right moment 21:00–30:00

The instructor pauses the program between two lines. On line 3, Python evaluates the call on the right of the assignment and binds **`fruit`** to the resulting node. On line 4, therefore, **`fruit`** is already known, but its payload is **not yet** **`"apple"`**. A student answer that would be right on line 5 is wrong a moment earlier. The instructor insists on saying “is assigned” rather than “equals” to make that direction of change audible.

**Annotated teaching version — guide-added comments. Begin **`fruit.py`** beside **`item.py`**.**

```python
from item import Item           # Bring the class from item.py into this module.
fruit = Item()                  # A new node; its own object attribute is None.
fruit.object = "apple"          # Evaluate the string, then set that node's payload.
```

These lines print nothing. The last one changes an existing node; it neither rebinds **`fruit`** nor constructs another **`Item`**. Read the dot as “the attribute named … on this particular object.” The instructor points out a naming trap: “the object” might mean the **whole node**, or it might mean the **attribute literally named `object`**. Ask which one before answering what a line evaluates to.

**Supplemental precision — reading versus executing.** Seeing an identifier on the left does not mean Python evaluates that target before the right-hand expression. Similarly, deciding that **`fruit`** is not a reserved keyword is a syntax question, not a runtime search through all keywords. For this module’s ordinary name lookup, a binding in the module takes precedence over a built-in. This is why the later assignment to **`iter`** works even though Python already has a built-in function named **`iter`**. The assigned name hides that built-in in this scope; it does not modify Python’s original function.[3]

**Key items to scan**

- **`from item import Item`** — **`item`** names a module; **`Item`** names the class brought into this program.
- **`fruit = Item()`** — binds a name after construction. **`fruit.object = "apple"`** — changes a field on the already-bound node.
- **Before / after line 4** — payload **`None`** / payload **`"apple"`**; the identity of the node is unchanged.
- **`=`** — assignment. The classroom’s “read left to right” is not an instruction to evaluate an assignment target first.

## 4 · How do five letters become one value? 30:00–43:00

Before moving to banana, the instructor stops on the literal **`"apple"`**. A student calls a string a group of characters. Could it be a bag? Shake a bag and the contents are still the same; shuffle the letters of *apple* and it may no longer be the same word. **Sequence** is the important improvement: there is a first **`a`**, then a **`p`**, then another **`p`**, followed by **`l`** and **`e`**. The program can work with this entire ordered value through one reference.

Suppose you were designing a format for putting characters into storage or sending them over a network. Knowing where to begin is not enough: how does the reader know when to stop? The class considers three designs. These are alternative representations, **not three steps Python performs**.

| Design | What the reader does | What the fruit example reveals |
|---|---|---|
| Fixed-width field | Read exactly the agreed width. | Five positions fit `apple`, not `banana`. Abbreviating everything loses information; reserving thousands of positions wastes space on short names. |
| Terminator | Read until a special end marker, as in a C-style NUL-terminated string. | The marker cannot also serve as ordinary data under that convention without a different encoding or a separate length. This is not how to find the end of a Python `str`. |
| Stored length | Carry the extent along with a reference to the data. | The reader knows how much belongs to this value. A format must specify whether the length counts bytes or characters. |

The instructor’s “address of the first character” sketch explains why one handle can designate a whole sequence. In Python, evaluating **`"apple"`** yields a **string object**, not a programmer-visible pointer to its first **`a`**. Its identity is not a character-buffer address promised by the language; evaluating the same literal need not allocate a fresh string. Assigning the payload stores a reference to the string, not a duplicate of its five characters inside the node.[1]

### Count the choices, not just the slots 35:00–39:00

Would a five-character limit give us only 640 labels? That suggestion counts **five lots of 128**, not the number of *ordered combinations of choices*. The instructor shrinks the problem: use only A, B, C and fill two positions. With A fixed first, there are **`AA`**, **`AB`**, **`AC`**. With B first, there are **`BA`**, **`BB`**, **`BC`**. Those **two groups** account for the six discussed in class. There is still a third group: **`CA`**, **`CB`**, **`CC`**. All nine are possible if a letter can repeat. Asking “are duplicates allowed?” changes the counting rule, not just the final rounding.

| Problem | Choices, position by position | Independently calculated total |
|---|---|---:|
| Two letters from A, B, C; repeats allowed | 3 × 3 | 9 |
| Exactly five symbols from 128; repeats allowed | 128 × 128 × 128 × 128 × 128 | 34,359,738,368 |
| Exactly five symbols from 128; no repeats | 128 × 127 × 126 × 125 × 124 | 31,747,968,000 |

The classroom estimates of roughly 3.4 and 3.17 times ten to the tenth belong to the last two rows respectively. The figures count **symbol sequences** under the stated rules, not legally valid Python identifiers: the hypothetical alphabet includes nonprinting ASCII values. Enormous is still finite; even billions of possibilities do not let a six-character word fit unchanged in five positions.

**Key items to scan**

- **Sequence** — **`apple`** has five ordered characters; a bag metaphor loses their order.
- **Fixed width / terminator / stored length** — three distinct answers to “where does the string end?”
- **`128 ** 5`** — five independent choices if repeats are allowed, not **`128 * 5`**.
- **No repeats** — after one symbol is used only 127 remain for the second slot; order still matters.
- **Node versus payload** — a node can refer to one complete string, another type of object, or later even a function; its links have a different purpose.

## 5 · Connect the neighbors, then walk both ways 43:00–48:00

Return to the driver with the first node holding apple. There is no reason banana’s node must sit immediately after apple’s in physical memory. To make banana the *logical* successor, create its node and store a reference to it in the apple node. To make the reverse trip possible, store a reference to the apple node on banana. These are separate writes on **different objects**. Nothing about writing one automatically fills the other.

**Annotated teaching version — guide-added comments. Continue **`fruit.py`** without resetting **`fruit`**.**

```python
fruit.next = Item("banana")          # Make banana, then put it in apple's next link.
fruit.next.prev = fruit              # On banana, set prev to the EXISTING apple node.
fruit.next.next = Item(obj="peach")  # Make peach; banana's next now leads to peach.
fruit.next.next.prev = fruit.next    # On peach, set prev to the existing banana node.
```

On the second line the right-hand side is just **`fruit`**: no call, no initialization. To find the destination of the assignment, start at apple, follow **`next`** to banana, and set **banana’s** **`prev`**. At this point there are two routes to the *same* apple: **`fruit`** and **`fruit.next.prev`**. The last line repeats the reasoning one node farther along. Naming the **`obj`** argument for peach and passing banana by position both initialize a payload; the old separate payload assignments in the editor are **comments**, not extra writes.

The instructor calls this the “banana object” when discussing its backward link. The recipient of **`.prev`** is the **node holding banana**, not the string **`"banana"`**. That distinction also explains how the nodes can be scattered in storage while retaining a definite order. To find the next item, follow the reference rather than adding a fixed number of bytes to an address.

**Annotated teaching version — guide-added comments. Finish the same **`fruit.py`**; **`iter`** is the classroom variable name.**

```python
iter = fruit             # Start at apple; fruit itself stays there.
print(iter.object)       # Display apple.
iter = iter.next         # Move the name iter to banana, not the banana string.
print(iter.object)       # Display banana.
iter = iter.next         # Move to peach.
print(iter.object)       # Display peach.
iter = iter.prev         # Follow peach's backward link to banana.
print(iter.object)       # Display banana again.
iter = iter.prev         # Follow banana's backward link back to apple.
print(iter.object)       # Display apple again.
```

**Independent reproduction.** Run **`python fruit.py`** with the two files together (on Windows, **`py fruit.py`** also works if the launcher is installed). The class reviewed the source; the five-line block below is from a separate execution, not a claimed classroom terminal run.

```text
apple
banana
peach
banana
apple
```

| Starting position | Operation before display | Payload printed | What did not move |
|---|---|---|---|
| No traversal name yet | Give `iter` the same node as `fruit` | apple | `fruit` still names apple |
| Apple | Follow apple’s `next` | banana | Both nodes and their links |
| Banana | Follow banana’s `next` | peach | The head is still apple |
| Peach | Follow peach’s `prev` | banana | The forward link to peach |
| Banana | Follow banana’s `prev` | apple | The backward link from peach |

This is manual traversal. There is no loop, call to **`next()`**, or iterator-protocol exhaustion in the pictured driver. It stops because the last **`print`** is the last executable line. If the backward link had been omitted, moving to it could produce **`None`**; the subsequent attempt to read **`.object`** would then fail. That last case is a prediction, not an error observed in class.

**Key items to scan**

- **`fruit.next = Item("banana")`** — one allocation and one forward-link assignment; banana’s backward link is still missing.
- **`fruit.next.prev = fruit`** — no allocation; completes the return path to the existing apple.
- **`Item(obj="peach")`** — supplies the payload by keyword instead of position.
- **`iter = iter.prev`** — changes the walking name, not the list. **`print(iter.object)`** — displays the payload, not the node’s ID.

## 6 · Can another name call the same function? 48:00–56:47

The question “does that reinitialize?” prompts a different experiment. Suppose the right side were a function call rather than the name **`fruit`**. Would the result still be a reference to something already there? **It depends on what the call returns.** The instructor sketches alternatives in the editor, then switches to Python’s interactive prompt to settle the question. The screen spells the variable **`num`**, not the auto-caption’s occasional “numbum.”

**Annotated teaching version — guide-added comments. In a fresh Python prompt, enter each statement separately; enter a blank line after the function body.**

```python
def f(x):           # Define a function with one required parameter named x.
    return 7        # Every successful call returns 7; x is not used in the body.

num = f(0)          # Call f with 0; bind num to the integer result.
num                 # Interactive display: 7. No print occurs inside f.
num = f()           # Intentional TypeError: no x. This assignment does not finish.
num = f             # Do not call anything. Rebind num to the function itself.
num(0)              # Call the same function via its other name; display 7.
num                 # Display the function object, still named f, not the number 7.
```

**Observed in the classroom REPL** (50:45 through 55:28): the first lookup displayed **`7`**. The failed call reported **`TypeError: f() missing 1 required positional argument: 'x'`**. The later call through **`num`** displayed **`7`**; looking up **`num`** alone then displayed a function representation beginning **`<function f at `**, with a hexadecimal address. See the source record for the session-specific display. An address from one interpreter session is not an expected output for another.

| Input | Is the body run? | Result or error | What `num` names afterward |
|---|---|---|---|
| `num = f(0)` | Yes | `7` returned; assignment itself displays nothing | Integer `7` |
| `num = f()` | No; the required argument is missing | `TypeError`; no result is assigned | Still integer `7` |
| `num = f` | No | No display; no error | The same function as `f` |
| `num(0)` | Yes | REPL displays `7` | Still the function; the call did not rebind the name |
| `num` | No | REPL displays the function representation | Still the function |

A student’s “can you call it with **`num`** now?” is a precise test. If **`num`** were the string **`"f"`**, the call would not invoke **`f`**. If it were the number **`7`**, it would not be callable either. It works because assignment preserved the actual function object. Think back to algebra’s **`f(x)`** and **`g(x)`**: distinguish the **name**, what goes **in**, what operation occurs and what comes **out**. Here **`0`** is an argument even though the operation ignores it. An unused parameter is still required unless its definition supplies a default.

The class extends the idea to a node’s payload: if the payload can hold a string or a number, why not a function? Store the function **object**, not the text of its name. The instructor proposes the possibility; the screenshots establish the REPL alias experiment, **not** a run of a whole function-valued list. A student also proposes returning an **`Item`** and asks about repeated construction. Distinguish the proposals: a function returning **`Item()`** would create a new node each time that expression runs; returning **`fruit`** would reuse the existing node. The class does not demonstrate a factory implementation.

**Transfer example — independently tested, not classroom code.** This short test uses the **`Item`** and **`f`** defined above. It answers the question without changing the original fruit list.

```python
holder = Item(f)                # A NEW node; its payload is the existing function.
print(holder.object is f)       # True: identity test, not a string-name comparison.
print(holder.object(0))         # Retrieve f, call it with 0, and display its return: 7.
```

```text
True
7
```

At the end a student connects this to C pointers. The common intuition is indirection: one value lets you reach something stored elsewhere. The boundary matters: a Python reference is not a C pointer you increment, and **`id()`** is not the operation that follows a list link. At the Python level, writing **`fruit.next`** is enough.[1]

**Key items to scan**

- **`def f(x):`** — defines the function; **`x`** is required. **`return 7`** — gives a value to the caller without printing it.
- **`f(0)` / `f()`** — a successful call versus a missing-argument error; the unused parameter still counts.
- **`num = f`** — an alias to the function, not its return value or its name as text.
- **`num(0)`** — displays **`7`** in the REPL and leaves **`num`** callable. **`num`** — inspects the function without calling it.
- **`holder.object(0)`** — follows a payload reference and then calls the retrieved function; transfer, not a recorded list run.

## Syntax to remember

| Pattern | What to ask in this lesson |
|---|---|
| `Item()` versus `fruit` | Is the program constructing a new node or looking up the one it already has? |
| `fruit.next.prev` | Which node does each dot reach? The last name belongs to banana here. |
| `x = y` versus `x.attr = y` | Are we rebinding a name or setting an attribute on an object? |
| `f` versus `f(0)` | Are we retrieving the function or retrieving the result of calling it? |
| `return` versus `print` versus a bare REPL expression | Give a value to the caller; write text; or let the interactive prompt display a result. |
| `a is b` | Do both expressions designate the very same object? Use this for identity, not to compare fruit spellings. |

## Knowledge check

1. Immediately after **`fruit = Item()`**, what would **`fruit.object`** and **`fruit.next`** each give you? Do they get that value in exactly the same way?
2. Stop after **`fruit.next = Item("banana")`** but before **`fruit.next.prev = fruit`**. Is it possible to get from apple to banana? From banana back to apple using **`prev`**? Explain the difference.
3. A classmate replaces **`iter = iter.next`** with **`fruit = fruit.next`** and expects both names to move together. Starting with both names at apple, where does each point afterward?
4. Why does five times 128 give the wrong count of five-symbol strings? How would prohibiting repeats change your computation?
5. If **`num`** currently holds **`7`**, predict what happens after each separate prompt: **`num = f()`**, **`num = f`**, **`num(0)`**, and finally **`num`**. Which statement actually changes the binding?
6. Someone proposes storing **`f()`** in a node to “save the function for later.” How would you store it instead? What would storing **`f(0)`** save?

### Answer key

1. Both reads yield **`None`**. The initializer assigned **`self.object`** on this instance, but the fresh node has no instance-level **`next`**; the read finds the class default. Same result, different lookup path.
2. Apple’s **`next`** already reaches banana. Banana’s **`prev`** still resolves to **`None`**. A forward assignment changes only the named field; a separate backward assignment is required.
3. **`fruit`** now names banana, **`iter`** still names apple. They shared a node, not a rule that keeps their names synchronized. Neither node changed.
4. Every choice of first symbol has a whole set of independent possibilities for the remaining positions. Multiply once **per position**: **`128 ** 5`**. Without repeats use **`128 * 127 * 126 * 125 * 124`** because each chosen symbol is unavailable later.
5. First comes a **`TypeError`** before any assignment, leaving **`7`** intact. Only **`num = f`** rebinds **`num`**, to the function. **`num(0)`** returns **`7`** and the prompt displays it. The last **`num`** displays the function object; the previous call did not replace it.
6. Supply **`f`** with no call parentheses, as in **`Item(f)`**. **`f()`** would fail here because **`x`** is required. **`Item(f(0))`** would call the function successfully and construct a node holding the **integer** **`7`**.

## Library of definitions

| Term | Working definition in this session |
|---|---|
| Node / payload | One `Item` is a node. Its `object` attribute refers to the data the node carries. |
| Doubly linked list | Nodes connected by both forward and backward references. The links describe order, not physical adjacency. |
| Reference / alias / identity | A reference lets you reach an object; two names for that object are aliases. Identity distinguishes that particular object from all other objects alive at that time. |
| Class / instance / type | `Item` defines a kind of object; the node carrying apple is an instance; inspecting its type gives the class `Item`. |
| Attribute / default | A named value obtained through an object, such as `.prev`; a class default can satisfy a lookup when no instance-specific value has been set. |
| Binding / mutation | `iter = fruit` binds a name. `fruit.next = ...` changes the state of the node already reached through `fruit`. |
| Initializer / method | `__init__` is the method that sets up a new instance. A method such as this receives the instance as `self`. |
| Identifier / literal | `fruit` is a name to resolve. `"apple"` is source notation for a particular string value; Python does not look up a variable named apple. |
| Parameter / argument | `x` is the parameter written in `def f(x):`. The `0` supplied to `f(0)` is an argument. |
| Callable / return value | A function object can be called. Its return value is what the call produces; here `f` is callable and `7` is its return value. |
| REPL / traceback | The interactive read–evaluate–print loop displays expression results. A traceback identifies a failed operation and ends with its exception type and message. |
| Fixed width / terminator / length | Reserve the same space every time, mark the end with a sentinel, or store the extent. The three trade-offs are illustrated in §4. |
| Detached HEAD | Git has checked out a commit directly. The matching historical revision helps interpret what the editor is showing. |

## Sources

**Primary lesson evidence:** [CSCS1240F26 15, JoesTA](https://www.youtube.com/watch?v=abrPTt833-c&t=2s), saved timestamped captions and original desktop captures. Time ranges identify where to review the spoken reasoning. The appendix identifies the editor and terminal capture targets. No earlier study guide is a lesson source.

**Supplemental references:** These clarify Python behavior; they do not establish what the class ran.

[1] https://docs.python.org/3/reference/datamodel.html

[2] https://docs.python.org/3/c-api/memory.html

[3] https://docs.python.org/3/reference/executionmodel.html

## Appendix A · Historical screen code and run record

These are classroom artifacts, not the guide’s annotated code. In the numbered listings, the gutter and **`│`** separator are **not Python to type**. Blank numbered lines were visible in the editor. Indentation is represented with spaces; a still image does not distinguish a tab from equivalent spaces. The PowerShell and Python prompts in the terminal record are not file contents.

### A1 · **`src/item.py`** — the constructor revision

**Observed, 35:00**, left pane of Sublime Text. Lines 2–4 set class attributes. Line 6 adds an initializer with an optional payload; line 7 assigns it on the instance receiving this call. No append, automatic reciprocal link, or iterator method appears in this version.

```text
 1 │ class Item:
 2 │     object = None
 3 │     next = None
 4 │     prev = None
 5 │
 6 │     def __init__(self, obj = None):
 7 │         self.object = obj
 8 │
```

### A2 · **`test/fruit.py`** — the matching driver

**Observed, 35:00**, right pane. Lines 3–10 assemble the list; the **`#`** at the start of lines 7 and 10 matters. The constructor now supplies banana and peach, so those older payload assignments do not run. Lines 12–22 use an ordinary name to walk forward and back. The five-line independent output in §5 belongs to this logic, not to the modified buffer in A3.

```text
 1 │ from item import Item
 2 │
 3 │ fruit = Item( )
 4 │ fruit.object = "apple"
 5 │ fruit.next = Item( "banana" )
 6 │ fruit.next.prev = fruit
 7 │ #fruit.next.object = "banana"
 8 │ fruit.next.next = Item( obj = "peach" )
 9 │ fruit.next.next.prev = fruit.next
10 │ #fruit.next.next.object = "peach"
11 │
12 │ iter = fruit
13 │ print( iter.object )
14 │ iter = iter.next
15 │ print( iter.object )
16 │ iter = iter.next
17 │ print( iter.object )
18 │
19 │ iter = iter.prev
20 │ print( iter.object )
21 │ iter = iter.prev
22 │ print( iter.object )
23 │
```

### A3 · **`test/fruit.py`** becomes a scratchpad

The modified editor buffer is not the REPL. At 49:00, line 3 shows **`def f(x):`** and line 4 shows an indented **`return`**, with the caret and completion menu visible. At 49:20 the return has the value **`7`**, but line 6 shows just **`num =`** awaiting a right-hand side. A suggestion in a completion menu is not inserted code. The next two views support the following distinction:

```text
49:45 · edited prefix
 1 │ from item import Item
 2 │
 3 │ def f(x):
 4 │     return 7
 5 │
 6 │ num = f()
 7 │
 8 │ fruit = Item( )

49:55 · alternative on the same editor line
 6 │ num = f
```

In the 49:55 view, lines 8–24 reproduce the readable run of A2 lines 3–19, shifted down five rows: the apple assignment is now on line 9, the traversal begins on line 17, and the partly visible line 24 moves backward. The remaining tail is off-screen; A2 supplies the earlier version, not proof of those unseen lines. No editor capture establishes **`num = f(0)`** here: that spelling is confirmed in the separate REPL. With line 6 as **`num = f()`**, running the file from the start would fail **before line 8**. With **`num = f`**, execution could proceed. These are predictions about those editor states. Neither a save nor a run of either modified file is established by these stills.

### A4 · PowerShell into the interactive interpreter

**Observed terminal state**, 50:15–56:00 (overlapping REPL views). The shell still shows the constructor checkout, including **`M       script/path.ps1`**; that status denotes a modified path, not a command to run. At 50:15 the gray **` .\test\fruit.py`** after the caret is a shell suggestion, not an executed filename. The following prompt confirms a bare **`py`** launch. Inside the interpreter the instructor defines the function again. This text identifies which expression caused the traceback and which later call succeeded.

```text
PS C:\CSCS1240F26\Item> py
>>> def f(x):
...     return 7
...
>>> num = f(0)
>>> num
7
>>> num = f()
Traceback (most recent call last):
  File "<python-input-3>", line 1, in <module>
    num = f()
TypeError: f() missing 1 required positional argument: 'x'
>>> num = f
>>> num(0)
7
```

The banner reads Python 3.14.7 on 64-bit Windows. The error did not end the session. By 55:28, bare **`num`** displays the function named **`f`**. The pointer partly overlaps the last address digit; it appears to be **`0`**. This is a reading of one run, not an expected address.

```text
>>> num
<function f at 0x000001DC74268250>
```

## Micro-Learning Insights · detachable recall sheet

- **Ask “an address of what?”** At 46:00, “an address” alone is not enough to predict the next line. **`fruit.next`** reaches an **`Item`** carrying banana; **`fruit.next.object`** reaches the string. Only the former has this node’s **`prev`** link.
- **Freeze the program one line earlier.** Around 29:00, the learner’s answer depends on whether line 4 has run. Before **`fruit.object = "apple"`**, the payload is **`None`**; after the assignment it is **`"apple"`**. The node is the same in both snapshots.
- **Read the arrows in both directions.** Say “apple points to banana” and then “banana points back to apple.” The second sentence requires its own assignment. It is not a restatement of the first.
- **Keep a landing place.** The reason for **`iter = fruit`** at line 12 is practical: you can walk away from apple without losing the name that still takes you straight back there. A second reference buys another starting point, not another copy.
- **Look at the indentation before describing the storage.** **`next = None`** is in the class body; **`self.object = obj`** is in the initializer. The first is a shared default for lookup; the second establishes an attribute on this node.
- **Test a counting rule on a small alphabet.** Write the A row, B row and C row before scaling up. Six pairs account for only two rows; nine account for all three. Decide separately whether **`AA`** is legal.
- **Ask the decoder’s question.** If all you know is where the first character starts, what tells you to stop? “Read five,” “stop at a marker,” and “read the stored length” describe different representations with different costs.
- **Read the parentheses last.** **`num = f`** keeps the callable. **`num = f(0)`** keeps the result. The same distinction tells you whether **`Item(f)`** can hold behavior for later use or **`Item(f(0))`** already holds an integer.
- **Identify the source of a displayed value.** In the driver, **`print`** writes each fruit. In the prompt, a bare expression displays **`7`** even though **`f`** never prints anything. A visible number is not proof the function contained a print statement.
- **Separate an editor prediction from a run.** The changed line in **`fruit.py`** poses the question; the later **`>>>`** prompt is where the class actually tests it. Match a traceback with its prompt, not with whichever buffer is visible behind the terminal.
