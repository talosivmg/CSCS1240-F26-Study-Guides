# Following references in Python

**CSCS1240F26 · Session 15 · Compressed technical companion**

Does `fruit.next.prev = fruit` make another apple? Read the right side first. It retrieves the node already named by `fruit`. On the left, `fruit.next` reaches the banana node. Python stores the apple reference in **banana’s** `prev` field. Two expressions can now reach the same apple. The lesson uses that same distinction to explain how a second name can call a function.

## 1 · Build one node and locate its data

A doubly linked list gets its order from **links**. Each `Item` is one node. Its `object` attribute holds the payload. Its `next` and `prev` attributes can lead to neighboring nodes. The class describes a kind of object. Each call to this ordinary class makes a distinct instance, even when two calls receive the same payload.

**Classroom implementation · item.py.** All Python comments in this companion are teaching annotations. For practice, save this file beside `fruit.py`. The historical project kept the class in `src/item.py` and its driver in `test/fruit.py`.

```python
class Item:                        # Define the node type; no fruit exists yet.
    object = None                  # Class-level fallback for the payload.
    next = None                    # A fresh node has no forward neighbor.
    prev = None                    # It has no backward neighbor either.

    def __init__(self, obj=None):  # self receives the new node automatically.
                                   # Omit obj and its parameter value is None.
        self.object = obj          # Write an instance field, even for None.
```

During `Item("banana")`, the inherited `__new__` creates the node. Python passes that instance and the argument to `__init__`. The initializer stores the string reference and implicitly returns `None`. The **class call** gives its caller the initialized node. The method is defined once on the class and shared by its instances.

**Classroom driver · fruit.py, part 1 of 3.** Combine the three driver blocks in order. Historical lines 3 and 4 distinguish two moments in the life of the same node.

```python
from item import Item     # Bind the class from item.py in this driver.
fruit = Item()            # Call the class, then bind fruit to its new node.
                          # fruit.object exists on that node and holds None.
fruit.object = "apple"    # Set its payload to the string value "apple".
                          # The name fruit still reaches the original node.
```

Freeze execution just before line 4. Both `fruit.object` and `fruit.next` yield `None`. The first read finds the instance field written by the initializer. The second finds the class default. Later, assigning `fruit.next` creates an instance field that takes precedence on this node. Other nodes can still read the class default.

**Key Items to Scan**

- **Name → value → type.** Looking up `fruit` yields a specific node whose type is `Item`. The name can later be rebound.
- **Indentation identifies the owner.** `next = None` belongs to the class body. `self.object = obj` writes on the instance during initialization.
- **Say “is assigned.”** At line 3 the payload is `None`. Line 4 writes `"apple"`. Neither assignment prints anything.

## 2 · Follow the owner of each link

**Classroom driver · fruit.py, part 2 of 3.** Apple already exists. The constructor accepts banana by position and peach by keyword. In the classroom revision the old separate writes to their payloads were commented out.

```python
fruit.next = Item("banana")          # Make banana; write apple.next.
fruit.next.prev = fruit              # First read fruit (apple). On the left,
                                     # follow apple.next; write banana.prev.
fruit.next.next = Item(obj="peach")  # Make peach with obj bound by name.
                                     # Follow apple.next; write banana.next.
fruit.next.next.prev = fruit.next    # Read apple.next (banana) on the right.
                                     # Follow two next links from apple;
                                     # write peach.prev with that same banana.
```

**Trace the writes.** A, B and P denote the actual apple, banana and peach nodes. Each pair is **(prev, next)**. A dash means the node has yet to be created. `None` is a real Python value at an open end.

| Operation completed | Apple A | Banana B | Peach P | New route |
|---|---|---|---|---|
| Apple payload set | (None, None) | — | — | Head reaches A |
| Assign A.next | (None, B) | (None, None) | — | A → B |
| Assign B.prev | (None, B) | (A, None) | — | B → A |
| Assign B.next | (None, B) | (A, P) | (None, None) | B → P |
| Assign P.prev | (None, B) | (A, P) | (B, None) | P → B |

The type of an object tells you what storage and operations its implementation needs. A memory sketch can explain indirection, but Python specifies no fixed offsets for these three fields. CPython manages a private heap and may reuse allocated space. `id(fruit)` identifies the live apple node. In CPython its value represents an address. Follow `.next` to reach the logical neighbor even when the nodes occupy distant locations.

**Key Items to Scan**

- **“An address of what?”** `fruit.next` reaches the banana node. `fruit.next.object` reaches its string payload. The node owns `prev`.
- **One write, one direction.** Stop after the first write and A can reach B while B’s reverse link still reads `None`.
- **Identity check.** After the second write, `fruit.next.prev is fruit` evaluates to `True`. This checks that both routes reach one node.

## 3 · Walk both ways while keeping the head

**Classroom driver · fruit.py, part 3 of 3.** The historical driver uses `iter` as an ordinary module-level name. It hides the built-in `iter` in this scope. Each reassignment moves that name to a neighbor. The nodes keep their links.

```python
iter = fruit          # Both names reach apple; keep fruit as the head.
print(iter.object)    # Read apple's payload and write apple to stdout.
iter = iter.next      # Read apple.next, then rebind iter to banana.
print(iter.object)    # banana
iter = iter.next      # Follow banana.next to peach.
print(iter.object)    # peach
iter = iter.prev      # Follow peach.prev back to banana.
print(iter.object)    # banana
iter = iter.prev      # Follow banana.prev back to apple.
print(iter.object)    # apple
```

Run `python fruit.py` from the directory containing the two practice files. On Windows, `py fruit.py` works with the Python launcher installed. The source reports that the class reviewed this driver. The table records the output independently reproduced from it, in order.

| Before | Step before print | After | Stdout line |
|---|---|---|---|
| Name unbound | `iter = fruit` | Apple | apple |
| Apple | `iter = iter.next` | Banana | banana |
| Banana | `iter = iter.next` | Peach | peach |
| Peach | `iter = iter.prev` | Banana | banana |
| Banana | `iter = iter.prev` | Apple | apple |

**Supplemental fault experiment · fresh file.** Save as `broken.py` beside `item.py` and run `python broken.py`. Build only the forward link to isolate the missing write.

```python
from item import Item          # Use the same constructor revision.
head = Item("apple")           # Keep this reference for the repair.
head.next = Item("banana")     # Only apple points to its neighbor.
cursor = head.next.prev        # Banana inherits prev = None; read succeeds.
print(cursor.object)           # AttributeError while looking up .object.
                               # print itself never receives an argument.
```

**Repair.** Insert the first line below immediately before the failing assignment to `cursor`. The three lines show the complete replacement for the last two lines of `broken.py`.

```python
head.next.prev = head          # On banana, install the route back to apple.
cursor = head.next.prev        # Read the repaired field; cursor reaches apple.
print(cursor.object)           # apple
```

**Key Items to Scan**

- **Keep a landing place.** With both names initially at apple, `fruit = fruit.next` would move only `fruit` to banana and leave `iter` at apple.
- **Find the first failed lookup.** Reading the missing reverse link yields `None`. Reading `.object` on that value raises `AttributeError: 'NoneType' object has no attribute 'object'`.
- **Termination.** The working driver stops after its fifth print. It uses explicit moves and never requests another node through the iterator protocol.

## 4 · Count the contents of a string

The payload `"apple"` is one string object containing five ordered Unicode code points. Reordering its letters can change the value. Storing it in a node stores a reference to the whole string. Python can reuse a literal. Its identity gives no portable address for its first character. When designing a file or network format, ask **how will the reader find the end?**

| Format considered in class | Reader’s rule | Trade-off |
|---|---|---|
| Fixed width | Consume the agreed number of positions. | Five fit apple. Banana needs six. Truncation loses data and oversized fields waste space. |
| Terminator | Stop at the designated marker, e.g. C’s NUL. | An embedded marker needs another encoding or a separate length. Python strings can contain NUL. |
| Stored length | Read the declared extent. | Sender and reader must agree whether the length counts bytes or characters. |

**Counting rule.** Use A, B, C for two positions. Fix the first letter, then list every second letter. A gives AA, AB, AC. B gives BA, BB, BC. C gives CA, CB, CC. The six pairs from the first two groups omit the last three. Allowing repeats gives nine. Forbidding repeats eliminates AA, BB and CC, leaving six different pairs.

| Exactly five positions, 128 choices initially | Calculation | Sequences |
|---|---|---:|
| Repeats allowed | `128 ** 5` | 34,359,738,368 |
| No repeated symbols | `128 * 127 * 126 * 125 * 124` | 31,747,968,000 |

**Key Items to Scan**

- **Add or multiply?** `5 * 128` gives 640 by adding five groups. Each first-symbol choice allows an entire set of four-symbol endings, so multiply the choices for successive positions.
- **Check the assumptions.** These counts include nonprinting symbols and count ordered sequences. Python identifiers have additional spelling rules.
- **Capacity versus width.** Billions of five-symbol combinations still leave no sixth position for the final letter of banana.

## 5 · Bind a function or bind its return

**Classroom REPL · fresh Python prompt.** Enter each statement separately. End the indented definition with a blank line. The missing-argument error is intentional. At the interactive prompt you can continue on the following line after the error.

```python
def f(x):         # Create a function with one required parameter, x.
    return 7      # A successful call gives 7 back without printing.

num = f(0)        # Bind x to 0, run f, then bind num to the returned 7.
num               # The prompt displays 7.
num = f()         # Missing x: argument binding fails before the body.
                  # Assignment never completes; num still refers to 7.
num = f           # Look up the function itself and bind num to it.
num(0)            # Call f through num; the prompt displays its result, 7.
num               # Display the function representation; num is still f.
```

| Prompt action | Body runs? | Display | Binding after |
|---|---|---|---|
| Define `f` | No | — | `f` names a function |
| `num = f(0)` | Yes | — | Integer 7 |
| `num` | No | `7` | Integer 7 |
| `num = f()` | No | `TypeError` | Integer 7 |
| `num = f` | No | — | Same function as `f` |
| `num(0)` | Yes | `7` | Same function |
| `num` | No | `<function f at 0x…>` | Same function |

A dash indicates an empty display. The class saw `TypeError: f() missing 1 required positional argument: 'x'` at interactive input 3, line 1. A later bare `num` displayed the function named `f` with a run-specific hexadecimal address. A bare expression in a script would discard the result. A REPL displays it.

**Key Items to Scan**

- **Ask what can be called.** The student’s test `num(0)` succeeds when `num` holds the function. Neither the integer `7` nor the string `"f"` can perform that call.
- **Arguments are checked before the body.** The definition requires `x` even though `return 7` never reads it.
- **Read the punctuation.** `num = f` saves behavior. `num = f(0)` runs that behavior immediately and saves its result.

## 6 · Practice with callable payloads and node identity

**Supplemental · payloads.py.** Place beside `item.py` and run `python payloads.py`. The class proposed storing behavior in a node and asked about returning nodes. This runnable comparison develops both ideas on fresh objects. Each print comment gives the independently tested output.

```python
from item import Item           # Use the constructor from section 1.

def f(x):                       # Keep the classroom's required parameter.
    return 7                    # Supply the integer to the caller.

holder = Item(f)                # Create a node; store the existing function.
print(holder.object is f)       # True: both expressions reach one function.
print(holder.object(0))         # Read f from holder, call with 0, then print 7.
value = Item(f(0))              # Call f first. Item receives the integer 7.
print(value.object)             # 7; this payload now holds the result.
fruit = Item("apple")           # Keep one particular apple for comparison.

def existing():                 # Callers can ask for our saved apple.
    return fruit                # Look up the existing node on every call.

def fresh():                    # This version performs construction in its body.
    return Item("apple")        # Each call makes its own node holding apple.

print(existing() is fruit)      # True: the returned node is the saved apple.
print(fresh() is fresh())       # False: compare two distinct, live nodes.
```

**Key Items to Scan**

- **Evaluate inner calls first.** In `Item(f())`, the missing argument raises before `Item` receives a value. In `Item(f(0))`, the inner call yields 7 and construction proceeds.
- **One spelling, different kinds of objects.** `holder.object(0)` calls the payload retrieved from the instance. Storing `"f"` would give it a string payload instead.
- **Inspect the return expression.** A function can return an existing object or a newly constructed one. Identical fruit spellings do not establish node identity.

## 7 · Match a debugging claim to a run

| Classroom evidence in the supplied Markdown | What it establishes |
|---|---|
| `git log --oneline` and historical `git checkout master~18` | Short IDs and subjects locate revisions. The checkout selected an ancestor 18 first-parent steps back. `2499ca0` identifies the constructor revision. Detached HEAD points directly at a commit. Checkout changes the working tree; the practice files need no checkout. |
| Modified editor line 6 | `num =` is an unfinished assignment. A saved file with that syntax would fail to parse. With `num = f()` it would parse and fail at runtime before line 8 creates fruit. With `num = f` it could proceed. The source establishes neither save nor run. |
| PowerShell `py`, then Python’s `>>>` prompt | The class launched a bare interpreter and defined `f` again. The suggested driver path was never part of the command. `M script/path.ps1` marked a modified file. Only the interactive run establishes the reported traceback. |
| Challenge discussion | The 2059 reply described catching an exception and sending it to standard output for that challenge. The instructor preferred different behavior for the factory example. A separate 2058 report mentioned `None`, an expected number and a rejected exception. Its grading rule and repair are unknown. |

**Key Items to Scan**

- **Locate the first operation that fails.** For `num = f()`, argument binding fails before the return statement, before reassignment and before any later script statement.
- **Match environment and revision.** An editor buffer, a shell suggestion and an executed prompt line each support different conclusions. Use the saved module and its matching driver for a reproduction.

## Syntax reference and library of definitions

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `class` / instance | Define a type; a class call can construct one member. | `class Item:` | Each ordinary `Item()` creates a distinct node. |
| `__new__` | Construct the instance the class call will initialize. | `Item()` | Creation precedes initialization here. |
| `__init__` / `self` | Initialize the receiving instance. Python supplies that instance as the first argument. | `self.object = obj` | The initializer implicitly returns `None`. The class call yields the node. |
| `None` | Singleton used for an absent value or neighbor. | `prev = None` | Reading this default succeeds. Following it as a node fails. |
| Default parameter | Supply a value when the caller omits an argument. | `obj=None` | `Item()` creates a node with an instance payload of `None`. |
| Positional argument | Bind a supplied value by its position. | `Item("banana")` | The string binds to `obj` after the implicit `self`. |
| Keyword argument | Match an argument to its parameter by name. | `Item(obj="peach")` | The initializer receives peach through the same parameter. |
| `from … import` | Bind a name exported by another module. | `from item import Item` | The adjacent practice file supplies the class. |
| `=` / binding | Evaluate the right side, then assign its result to the target. | `iter = fruit` | The two names can subsequently be rebound independently. |
| Dot / mutation | Read or write an attribute on its owner. | `fruit.next.prev = fruit` | The final write changes the banana node. |
| Class default | Class attribute used when this instance has no overriding field. | `Item.next` | Setting apple’s link leaves the class default available to peach. |
| Node / payload | A node carries data and links to neighbors. | `fruit.object` | The payload can be a string, an integer or a callable. |
| Reference / alias | A reference reaches an object. Two names bound to it are aliases. | `num = f` | Either name can call the same function. |
| `is` / identity | Compare whether two expressions reach one object. | `holder.object is f` | Equal-looking payloads can belong to distinct nodes. |
| `id()` | Obtain a live object’s integer identity. | `id(fruit)` | A portable program uses attributes to follow links. |
| `str` / literal | An ordered text sequence; quotes express its value in source. | `"apple"` | The quotes distinguish a literal from a name lookup. |
| `*` / `**` | Multiply factors / raise a base to a power. | `128 ** 5` | Exponentiation fits five equally sized choice sets. |
| `def` / parameter | Bind a function with a declared input name. | `def f(x):` | Required argument binding occurs even if the body ignores `x`. |
| Call parentheses | Invoke the retrieved callable with the given arguments. | `holder.object(0)` | Retrieve the payload first, then call it with 0. |
| `return` | End the function call with a value for the caller. | `return fruit` | Returning a reference can preserve node identity. |
| `print()` / stdout | Write the displayed form of a value to standard output. | `print(iter.object)` | Prints the payload on its own line and returns `None`. |
| REPL | Read, evaluate and display an interactive expression result. | `num(0)` | The displayed 7 came from the prompt, not an inner print. |
| Traceback / `TypeError` | Report the failing operation and exception; the call signature can cause this type of error. | `f()` | A failed right side leaves the earlier binding intact. |
| `AttributeError` | An attribute lookup cannot supply the requested name. | `cursor.object` | With `cursor` bound to `None`, the failure precedes printing. |

## Knowledge check

1. Immediately after `fruit = Item()`, where does Python find `fruit.object`? Where does it find `fruit.prev`?
2. Stop just after creating peach, before the final reverse write. Which node can reach peach? What happens if you move backward from peach and then read the payload?
3. With `iter` and `fruit` both at apple, predict both bindings after `fruit = fruit.next`.
4. How many two-letter sequences can A, B and C make when repeats are allowed? Which three disappear if repeats are forbidden?
5. If `num` currently holds 7, which of `num = f()`, `num = f` and `num(0)` changes its binding in the classroom REPL?
6. Which expression stores a callable for later use, `Item(f)` or `Item(f(0))`? Why can two calls to `fresh()` produce matching payloads yet compare false with `is`?

### Answer key

1. Both reads give `None`. The initializer installed `object` on the instance. The fresh node inherits the class value for `prev`.
2. Banana reaches peach directly and apple reaches it through banana. Peach’s `prev` still yields `None`. Reading `.object` from that value raises `AttributeError`.
3. `fruit` reaches banana. `iter` still reaches apple. Rebinding one name leaves the other binding and both nodes intact.
4. Three choices for each of two positions give nine. Removing AA, BB and CC leaves six.
5. Only `num = f` changes the binding. The first statement raises before reassignment. The final call displays 7 and leaves the function bound to `num`.
6. `Item(f)` stores the callable. `Item(f(0))` stores the returned integer. Each call to `fresh()` constructs a distinct node, and `is` compares those nodes.

### Technical references

- Python language reference — [object construction](https://docs.python.org/3/reference/datamodel.html#object.__new__), [assignment](https://docs.python.org/3/reference/simple_stmts.html#assignment-statements), [calls](https://docs.python.org/3/reference/expressions.html#calls).
- Python reference — [text sequences](https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str) and [CPython memory management](https://docs.python.org/3/c-api/memory.html#overview).
- Git reference — [checkout and detached HEAD](https://git-scm.com/docs/git-checkout#_detached_head).

**Classroom source.** Session_15_Following_References_in_Python_Unified_v2.md, including its historical-code appendix and recall sheet. Descriptions of class activity follow that Markdown. Practice failures, repairs and transfer examples were tested separately.

