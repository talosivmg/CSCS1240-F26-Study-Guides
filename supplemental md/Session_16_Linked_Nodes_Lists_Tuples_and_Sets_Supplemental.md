# Linked nodes, lists, tuples, and sets

**CSCS1240F26 · Session 16 | Compressed technical study companion · v1.2**

Why can both `price[1] = 3.98` and `budget[1][1] = 0` change the price of eggs, while `budget[1] = None` raises an exception? Trace each target to the object that owns its final slot. The same question explains why a fruit node and a list can both offer `append` yet return different values.

**Using the examples.** Classroom actions and posted results below are reported by the supplied session Markdown. Python listings are annotated for study. Each listing is standalone unless a shared block or filename is specified. Output boxes show independent practice runs. A bare expression at the interactive prompt can display a value; use `print` to see that value in a script.

## 1. Turn launch arguments into a calculation

In the student's posted command `py .\src\example.py yes`, `sys.argv[0]` identifies the script and `sys.argv[1]` is the string `'yes'`. The launcher contributes no entry. The length is the integer `2`, with just one argument available to the script beyond its name. Standard input is a separate stream for reading data while the program runs.

The proposed `calc<your initials>` exercise starts with two numeric strings, adds an operator between them, then checks count, numbers and operator. Small working revisions make natural add/commit/push checkpoints. Square brackets around optional steps in the post were instructions to the reader. Another student proposed printing the definition matching a word. Neither proposal establishes a completed assignment or grading requirement.

**Supplemental runnable calculator.** Save as `calc.py`. Teaching inputs are `2` and `3`. Two operands imply addition; the infix form accepts `+` and `-` in this practice implementation. Infix `2 + 3`, prefix `+ 2 3` and postfix `2 3 +` express different arrangements. Only the infix layout is implemented for explicit operators.

```python
import sys                         # Obtain the arguments of this script.

def calculate(args):               # args includes the script name at slot 0.
    if len(args) == 3:             # Script plus two operands: use addition.
        left, right = args[1:]     # Unpack the two strings after the name.
        op = "+"                   # Supply the implicit operation.
    elif len(args) == 4:           # Script plus left, operator, right.
        left, op, right = args[1:] # Slots 1, 2 and 3 retain launch order.
    else:                          # Neither supported argument count fits.
        raise ValueError("invalid argument count")  # Reject before unpacking.
    a = float(left)                # '2' becomes 2.0; bad text raises ValueError.
    b = float(right)               # Convert before adding: '2' + '3' is '23'.
    if op == "+":                  # Compare the supplied symbol with '+'.
        return a + b               # Give 5.0 to the caller for 2 and 3.
    if op == "-":                  # First return was skipped; test subtraction.
        return a - b               # Operand order matters: 2 minus 3 is -1.0.
    raise ValueError("unsupported operator")  # Both operator tests failed.

print(calculate(sys.argv))         # Calculate first; print only on success.
```

`raise` interrupts the current call with an exception. The count-error line above constructs that exception with an explanatory message. Conversion errors arise inside `float`. With an uncaught error, the outer `print` never receives a result.

| Run | Count including script | Result |
|---|---|---|
| `py calc.py 2 3` | 3 | Prints `5.0` |
| `py calc.py 2 + 3` | 4 | Prints `5.0` |
| `py calc.py 2 - 3` | 4 | Prints `-1.0` |
| `py calc.py 2` | 2 | Count check raises `ValueError` |
| `py calc.py two + 3` | 4 | First numeric conversion raises `ValueError` |
| `py calc.py 2 x 3` | 4 | Both conversions succeed; unsupported operator raises `ValueError` |

**Key Items to Scan**

- `args[1:]` skips the script; unpacking assigns one remaining element to each name.
- `return` supplies a numeric result to `print`. A validation failure takes a different path.
- Test one new decision at a time: input count, numeric conversion, then operation.

## 2. Read the result of a child process

A posted single-host diagnostic illustrated a camera-support idea: accept an address at launch and eventually read authorized device addresses from a file. The posted script used a fixed host. Its `py .\subproc.py` output reported four replies, zero loss, 35 ms minimum, 43 ms maximum, 37 ms average, empty stderr and exit code 0. These figures belong to the posted run. The instructor encouraged the student to identify the unfamiliar call and explain it to peers.

**Supplemental local equivalent.** This child only prints `ready`. As in the posted example, the first list element identifies the program and the remaining elements are its arguments. The parent waits before inspecting the result.

```python
import subprocess                       # Start a child and collect its result.
import sys                              # Locate the current Python executable.
result = subprocess.run(                # Wait, then bind the completed result.
    [sys.executable, "-c", "print('ready')"],  # -c runs this Python text.
    capture_output=True,                # Keep the two output streams separately.
    text=True,                          # Decode the captured bytes to strings.
)
print("stdout:", result.stdout.strip()) # Strip the child's trailing newline.
print("stderr:", repr(result.stderr))   # repr makes the empty string visible.
print("exit:", result.returncode)       # Zero is this child's exit status.
```

```text
stdout: ready
stderr: ''
exit: 0
```

**Key Items to Scan**

- `result.stdout` and `result.stderr` contain the child's text. Labels such as `stdout:` come from the parent.
- `returncode` reports completion status for that invocation. Inspect it separately from either stream.
- A posted f-string such as `f"Exit Code: {result.returncode}"` inserts the field value into the label.

## 3. Build the links, then move a name through them

At constructor checkpoint `2499ca0` (`master~18`), `src\item.py` defined the node and `test\fruit.py` imported it with `from item import Item`. At the Python level, a link identifies another object. The payload `"banana"` identifies what a node stores. The bit patterns underlying both acquire meaning through the operations Python performs on them.

**Runnable constructor revision.** Save these three blocks in order as `fruit_manual.py` and run `py fruit_manual.py`. Combining module and driver removes the classroom import-path dependency. The third block is also reused for the next revision.

```python
class Item:                        # Define the node type used for all three fruits.
    object = None                  # Class default for the payload.
    next = None                    # Default seen until this node gets a successor.
    prev = None                    # Default seen until this node gets a predecessor.

    def __init__(self, obj=None):  # self is the new instance; obj may be omitted.
        self.object = obj          # Write a per-instance payload, even for None.
```

```python
fruit = Item()                     # Create the first node with payload None.
fruit.object = "apple"             # Change its payload; keep the same node.
fruit.next = Item("banana")        # Construct banana; write apple's forward link.
fruit.next.prev = fruit            # Resolve apple.next; write banana's back link.
fruit.next.next = Item(obj="peach")  # Keyword obj supplies the new node's payload.
                                     # Store peach in banana.next.
fruit.next.next.prev = fruit.next  # RHS reaches banana; target reaches peach.
                                   # Write peach.prev. Apple.prev stays None.
iter = fruit                       # Start the walk on apple. No node is copied.
```

**Shared walk.** Append this block after either setup when `iter` refers to apple. The spelling `iter` is the classroom cursor variable. Binding it hides Python's built-in of the same name in this scope.

```python
print(iter.object)                 # Read apple's payload and display apple.
iter = iter.next                   # Move the cursor from apple to banana.
print(iter.object)                 # Display banana.
iter = iter.next                   # Follow banana.next to peach.
print(iter.object)                 # Display peach.
iter = iter.prev                   # Follow peach.prev back to banana.
print(iter.object)                 # Display banana again.
iter = iter.prev                   # Return to the original apple node.
print(iter.object)                 # Display apple again.
```

| Cursor before | Operation | Cursor after | Next print |
|---|---|---|---|
| Unbound | `iter = fruit` in manual setup | Apple | apple |
| Apple | `iter = iter.next` | Banana | banana |
| Banana | `iter = iter.next` | Peach | peach |
| Peach | `iter = iter.prev` | Banana | banana |
| Banana | `iter = iter.prev` | Apple | apple |

The five entries in the last column are five separate output lines, reported for the classroom command `py .\test\fruit.py` and reproduced here. The driver stops explicitly after the final print. At the ends, apple's `prev` and peach's `next` are `None`. Another step outward would bind the cursor to `None`; reading its `object` would then fail.

**Key Items to Scan**

- `Item("banana")` and `Item(obj="peach")` show positional and keyword arguments to the initializer.
- `self.object` belongs to one instance. Unassigned links read the class default; writing an instance link leaves the other nodes' defaults alone.
- Draw the three nodes once. Move a marker for `iter` on each assignment. Five displays require only three nodes.

## 4. Make the new tail the return value

At `fc6c333` (`master~17`), the revised node class adds `append`. Start with the English contract: put this value **at the end**. The implementation assumes the receiver is already the tail. There is no loop to find it. In `fruit.append("banana")`, the bound method receives the current apple node as `self` and the explicit string as `obj`.

**Runnable append revision.** In a separate `fruit_append.py`, copy the `Item` definition from §3. Place this method **inside the class**, directly below `__init__` at the same indentation. Then add the driver below at the left margin and finish with the shared walk from §3. Run `py fruit_append.py`.

```python
    def append(self, obj):         # self is this call's tail; obj is the payload.
        newItem = Item(obj)        # Allocate a different node and set its payload.
        newItem.prev = self        # First write: new tail can reach the old tail.
        self.next = newItem        # Second write: old tail can reach the new one.
        return newItem             # Pass the new endpoint back to the caller.
```

```python
fruit = Item("apple")              # Begin a fresh chain with one node.
fruit = fruit.append("banana")     # Call on apple; only then rebind fruit to banana.
fruit = fruit.append("peach")      # Call on banana; rebind fruit to returned peach.
iter = fruit.prev.prev             # Peach -> banana -> apple. fruit stays on peach.
```

| Operation | Link or name changed | Other relevant state |
|---|---|---|
| Create banana | `newItem` refers to banana | `fruit` still names apple; both banana links are `None` |
| `newItem.prev = self` | banana.prev → apple | apple.next still `None` |
| `self.next = newItem` | apple.next → banana | banana.prev already points to apple |
| Return and assign | `fruit` moves from apple to banana | Both links survive the call |
| Create peach | New local `newItem` refers to peach | `self` is banana; banana.prev is apple |
| `newItem.prev = self` | peach.prev → banana | banana.next still `None` |
| `self.next = newItem` | banana.next → peach | apple.next still points to banana |
| Return and assign | `fruit` moves from banana to peach | peach.next still `None` |
| `iter = fruit.prev.prev` | `iter` reaches apple | The shared walk can now print the same five lines |

The source shows the later files but associates the visible terminal output with the earlier revision. Both practice drivers independently printed apple, banana, peach, banana, apple. Historical `git checkout master~17` changed the checked-out revision; a detached HEAD means HEAD refers directly to a commit. A modified `script/path.ps1` indicator is file status. Launching bare `py` starts a prompt. To identify a run, match the saved driver, checkout and actual script invocation before reading the lines below it.

**Supplemental boundary test.** Run after the append-revision class in a fresh driver. Retain references so you can see what a second call on the same receiver does.

```python
head = Item("apple")               # Keep apple as a stable reference.
old = head.append("banana")        # Save banana so it remains available to inspect.
head.append("peach")               # Return discarded; apple.next still changes.
print(head.next.object)            # Apple now leads directly to peach.
print(old.prev is head)            # Banana's old back link still reaches apple.
print(head.next is old)            # Apple no longer points forward to banana.
```

```text
peach
True
False
```

**Key Items to Scan**

- `return newItem` lets the caller keep the current tail. Ignoring the return leaves both link writes in effect.
- Appending again on a node with a successor overwrites its forward link. The old successor's backward link survives.
- Chaining `head.append("banana").append("peach")` can work because the intermediate result is an `Item`. Whether a second call works depends on the first call's return type.

## 5. Predict the first operation that can fail

Before pressing Enter, identify the bare name, then the receiver, then the required arguments. Scope determines where Python can resolve a bare name. A name after a dot is sought on the receiver. Defining `Item.append` does not install a global `append` or change `list.append`. What the instructor called “overloading” here is the same method spelling on different types. The proposed integer examples exposed an ambiguity: appending to 7 could suggest arithmetic or joining digits.

**Classroom progression, reported for Python 3.14.7.** The entries were tried separately at the prompt.

| Expression | First decisive operation | Classroom result or next state |
|---|---|---|
| `append` | Look up bare name | `NameError: name 'append' is not defined` |
| `append()` | Look up bare name before call | Same `NameError`; no call starts |
| `a = 7`, then `a.append()` | Resolve member on integer | `AttributeError: 'int' object has no attribute 'append'` |
| `a.append(3)` | Resolve same missing member | Same `AttributeError`; an argument cannot supply a method |
| `b = [7]`, then `b.append()` | Member found; check arguments | `TypeError: list.append() takes exactly one argument (0 given)` |
| `b.append(3)` | Append integer to existing list | Returns `None`; prompt displays nothing |
| `b` | Inspect list after mutation | Displays `[7, 3]` |

**Runnable failure and repair.** A script would stop on its first unhandled exception. Each `try` below narrows the experiment to one operation. In `except ... as err`, the handler binds the actual exception object to `err`. `type(err)` obtains its class; `.__name__` reads the class name string before `print` displays it.

```python
try:                              # Predict whether a bare name is available.
    append()                      # Name lookup fails before the call begins.
except NameError as err:          # Catch the failed bare-name lookup.
    print(type(err).__name__)     # Display NameError.

a = 7                             # The name resolves to an integer.
try:                              # Next test concerns the integer's members.
    a.append(3)                   # Fails at .append, before argument checking.
except AttributeError as err:     # Report the missing attribute on this receiver.
    print(err)                    # Display the integer-specific error message.

b = [7]                           # A list supplies the append method.
try:                              # Is the argument required?
    b.append()                    # Found the method; supplied zero elements.
except TypeError as err:          # The call signature rejects the argument count.
    print(type(err).__name__)     # Display TypeError; b still holds only 7.
result = b.append(3)              # Mutate b; save the method's None return.
print(result, b)                  # Show the return and the mutated object.
```

```text
NameError
'int' object has no attribute 'append'
TypeError
None [7, 3]
```

**Key Items to Scan**

- A different exception reveals how far execution got. Changing arguments helps only after lookup succeeds.
- `b = b.append(3)` would append first, then bind `b` to `None`. The fruit-node driver relies on a different return contract.
- Read back the receiver to see a mutation. The prompt suppresses the display of `None`.

## 6. Count elements, then test one slot

`list()` and `[]` each construct a fresh empty list. `[None]` constructs a list with a first element. A student predicting the type of `c[0]` must first decide whether element zero exists.

```python
c = list()                        # An empty list constructed by calling its class.
d = []                            # A separate empty list made with a display.
e = [None]                        # One occupied slot whose value is None.
print(c, d, e)                    # Show [] [] [None].
print(type(c), len(c), len(e))    # Inspect the container type and the two counts.
try:                              # Check the empty-list prediction.
    print(type(c[0]))             # c[0] fails; neither type nor print is called.
except IndexError as err:         # Lists support indexing, but slot 0 is absent.
    print(err)                    # Display list index out of range.
print(type(e[0]).__name__)        # e[0] exists; its object's type is NoneType.
```

```text
[] [] [None]
<class 'list'> 0 1
list index out of range
NoneType
```

A coordinate groups related values that could otherwise be mixed up across separate names. With `(2, 3)` one value carries both coordinates. A tuple's fixed slots suit a latitude/longitude pair. A new address can still be associated with the same variable.

```python
f = 2                             # A single integer.
g = (2)                           # Parentheses group the same integer expression.
h = (2, 3)                        # A comma separates two coordinate slots.
print(f, g, type(g), h, type(h))  # Compare the integer and the tuple directly.
h = (2, 3, 4, 5)                  # Rebind h to a four-element tuple.
h = (2, 3, 4, 5, 54)              # Rebind again; previous tuples did not grow.
i = [2, 3, 4, 5, 54]              # Keep values fixed; change the container type.
print(h[1], i[1])                 # Position 1 is the second element in each.
i[1] = 73                         # Replace 3 in the existing list.
print(i, h[1])                    # The independent tuple still contains 3.
try:                              # Attempt the identical write on a tuple.
    h[1] = 73                     # Valid position, unsupported assignment.
except TypeError as err:          # The failed write leaves all five slots intact.
    print(err)                    # Explain why the replacement failed.
print((2,), ())                   # Supplemental: singleton comma and empty tuple.
```

```text
2 2 <class 'int'> (2, 3) <class 'tuple'>
3 3
[2, 73, 4, 5, 54] 3
'tuple' object does not support item assignment
(2,) ()
```

**Key Items to Scan**

- `len` counts elements. `None` counts when it occupies a slot. A missing slot raises `IndexError`.
- `(2)` groups an expression, `(2,)` packs one element, and `()` constructs the empty tuple. Tuples can have more than two elements, subject to memory.
- Keep contents, index and replacement fixed. Changing the receiver from list to tuple isolates mutability as the reason the write fails.

## 7. Find the owner of the egg-price slot

`budget` groups two lists by reference. Index 1 of each list associates eggs with their price. The programmer maintains that correspondence. A tuple fixes its own slots and imposes no such constraint on the lists inside it.

```python
grocery = ["milk", "eggs", "bread"]  # Product order determines price alignment.
price = [2.99, 4.99, 1.52]           # Eggs start at 4.99 in the second slot.
budget = (grocery, price)            # Store references to both existing lists.
print(budget[0], budget[1])          # Retrieve the whole lists independently.
try:                                # Attempt to replace the entire price list.
    budget[1] = None                # The target belongs to the outer tuple.
except TypeError as err:            # Tuple refuses; prices are still intact.
    print(err)                      # Display the reason execution enters here.
print(budget[1])                    # Read back 4.99 in its original slot.
price[1] = 3.98                     # Direct write: change the egg price to 3.98.
print(budget)                       # The tuple sees the change via its list.
budget[1][1] = 0                    # Evaluate 0, then obtain price via budget[1].
                                    # Final [1] belongs to price; overwrite 3.98.
print(budget)                       # The other prices, 2.99 and 1.52, survive.
print(budget[1] is price)           # Supplemental identity test: the very same list.
```

```text
['milk', 'eggs', 'bread'] [2.99, 4.99, 1.52]
'tuple' object does not support item assignment
[2.99, 4.99, 1.52]
(['milk', 'eggs', 'bread'], [2.99, 3.98, 1.52])
(['milk', 'eggs', 'bread'], [2.99, 0, 1.52])
True
```

| Statement | Final destination | Eggs before → after | Tuple slot 1 |
|---|---|---|---|
| `budget = (grocery, price)` | Name `budget` | 4.99 → 4.99 | References `price` |
| `budget[1] = None` | Tuple slot 1 | 4.99 → 4.99 | Refused; same list |
| `price[1] = 3.98` | Price list slot 1 | 4.99 → 3.98 | Same list |
| `budget[1][1] = 0` | Price list slot 1 | 3.98 → 0 | Same list |

**Micro-learning.** Read a chain one bracket pair at a time. After `budget[1][1]` reaches the integer `0`, another subscript would ask that integer to support indexing. For assignment, evaluate the right-hand value before resolving the target. For arithmetic, use operator precedence: in the spoken example `3 + 5 * 2`, multiplication yields `10` before addition yields `13`. The pending screen expression was `y = 3 + 5 + 2`; the source documents no classroom print of `13`.

**Data versus management software.** The two lists organize data in memory. A flat file could persist the values. A database management system (DBMS) supplies facilities to query, search and manage stored data; a flat-file program would have to supply or arrange those operations. The class discussion concerned that distinction.

**Key Items to Scan**

- `budget[1]` retrieves a list. The final `[1]` in `budget[1][1]` selects the egg-price slot **on that list**.
- `is` checks identity. Independent paths to one object explain why reading through either name shows the new price.
- A readback immediately after a failed write checks preservation. Earlier values selected in terminal scrollback do not establish a later reset.

## 8. Test set membership independently of position

An empty list understands an index and can reject it as out of range. A set supplies no positional lookup, even when populated. A separate experiment asks whether Python can construct a set from three positional arguments. That failure leaves the earlier binding intact.

```python
j = set()                         # Start with zero members.
try:                              # Test whether a set has a slot zero.
    print(j[0])                   # Subscription fails before print runs.
except TypeError as err:          # Unsupported operation for this container type.
    print(err)                    # Display the subscriptability error.
try:                              # Test constructor arity independently.
    j = set(2, 5, 7)              # RHS fails before assignment can replace j.
except TypeError as err:          # Three separate arguments exceed the limit.
    print(err)                    # Display the count mismatch.
print(j, type(j))                 # j still names the original empty set.
names = dir(j)                    # Discover attribute names available on j.
print("append" in names, "add" in names)  # Search those names: False, True.
try:                              # Check the missing member directly.
    j.append(2)                   # There is no sequence end to append to.
except AttributeError as err:     # Method lookup fails before a call.
    print(err)                    # Display the missing attribute.
print(j.add(2), j)                # First argument adds 2, returns None; show j.
print(j.add(2), j)                # Equal member already exists; show j again.
```

```text
'set' object is not subscriptable
set expected at most 1 argument, got 3
set() <class 'set'>
False True
'set' object has no attribute 'append'
None {2}
None {2}
```

| Operation in the classroom | Members before | Result of operation | Members after |
|---|---|---|---|
| `j[0]` | Empty | `TypeError` | Empty |
| `j = set(2,5,7)` | Empty | `TypeError`; no rebinding | Empty |
| `j.append(2)` | Empty | `AttributeError` | Empty |
| `j.add(2)` | Empty | `None` | 2 |
| Inspect `j` | 2 | Prompt displays `{2}` | 2 |
| `j.add(2)` again | 2 | `None` | 2 |
| Inspect `j` again | 2 | Prompt again displays `{2}` | 2 |

**Supplemental syntax correction.** A failed three-argument call says nothing about the availability of a set display. Both constructions below work. The constructor consumes one iterable; the display lists elements directly. A hashable object has a hash that stays stable over its lifetime, and equal hashable values must have equal hashes. Hash collisions alone do not make two values equal.

```python
print({2, 5, 7} == set([2, 5, 7]))  # Compare members, independent of display order.
print(type({}).__name__)            # Empty braces create a dictionary: dict.
print(len({2, 2.0}))                # Equal integer and float make one member.
try:                               # A set is mutable; each member must be hashable.
    set().add([2])                  # New empty set; a mutable list cannot join it.
except TypeError as err:           # Version-specific wording; the type is stable.
    print(type(err).__name__)       # Report TypeError.
```

```text
True
dict
1
TypeError
```

**Key Items to Scan**

- `set()` creates an empty set. `{}` creates an empty dictionary. `{2}` is a nonempty set display.
- `dir(j)` also showed `discard`, `intersection`, `union` and `update`. Names suggest operations; documentation or a small run establishes a call's contract.
- Test uniqueness by repeating `add`. Test positional access separately with `j[0]`. Each experiment supports a different conclusion.

## 9. Consolidated syntax reference and library of definitions

### Starting programs and reading results

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `import` | Bind a module name for later attribute lookup. | `import sys` | `sys.argv` then accesses that module's data. |
| `from … import` | Bind a member directly in the importing module. | `from item import Item` | Historical driver needs `item.py` on the import path. |
| `sys.argv` | List of launch arguments. | `sys.argv[1]` | Holds `'yes'` in the posted example; slot 0 identifies the script. |
| `len(x)` | Count elements. | `len(sys.argv)` | Two entries include the script itself. |
| `args[1:]` | Slice from position 1 through the end. | `left, right = args[1:]` | Unpacking requires exactly two elements here. |
| `float(x)` | Convert numeric text to a floating-point value. | `float("2")` | Returns `2.0`; invalid text raises `ValueError`. |
| `if / elif / else` | Choose a branch based on successive tests. | `if len(args) == 3` | At most one branch in a chain runs. |
| `==` | Test equality of values. | `op == "+"` | For sets, equality compares membership. |
| `+ / - / *` | Add, subtract or multiply numbers. | `3 + 5 * 2` | Multiply first. On two strings, `+` concatenates. |
| `raise` | Signal an exception, stopping the current normal path. | `raise ValueError(...)` | Caller gets an error instead of a result. |
| `subprocess.run` | Execute a child and wait for its result. | `subprocess.run([...])` | Pass executable and arguments as separate list elements. |
| `sys.executable` | Path of the running Python interpreter. | `[sys.executable, "-c", …]` | `-c` passes code to that interpreter. |
| `capture_output=True` | Collect both output streams. | `result.stderr` | Inspect each stream after completion. |
| `text=True` | Request decoded text streams. | `result.stdout` | The child's line includes a final newline. |
| stdin / stdout / stderr | Input, ordinary output and diagnostic streams. | `result.stdout` | Launch arguments arrive separately from stdin. |
| `returncode` | Child process's numeric completion status. | `result.returncode` | Zero in the posted and local runs. |
| `.strip()` | Remove surrounding whitespace from text. | `result.stdout.strip()` | Produces `'ready'` for the local child. |
| `repr(x)` | Produce a representation of a value. | `repr(result.stderr)` | An empty string becomes visible as `''`. |
| f-string | Evaluate a field inside formatted text. | `f"{result.returncode}"` | Inserts the number into a string. |
| `print(...)` | Display arguments, normally to stdout. | `print(result, b)` | A script requires an explicit display to show these values. |

### Classes, links, calls and debugging

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `class` / instance | Define a type; create an individual object by calling it. | `Item("apple")` | Each class call here creates a distinct node. |
| `def` / parameter | Define callable behavior and name its inputs. | `def append(self, obj):` | At the call, an argument supplies a parameter value. |
| `__init__` | Initialize a newly created instance. | `self.object = obj` | The class call yields the instance. The initializer writes its payload. |
| Default argument | Supply a value when the caller omits an argument. | `obj=None` | `Item()` starts with a `None` payload. |
| Keyword argument | Associate an argument with a named parameter. | `Item(obj="peach")` | Positional `Item("peach")` supplies the same payload. |
| Class attribute | Value looked up on the class when an instance has no override here. | `next = None` | One node's `self.next` write creates an instance-specific link. |
| `self` / receiver | Instance bound to a method call. | `fruit.append("banana")` | Apple is the first receiver; banana is the first new node. |
| Attribute / method | A named object member; a bound method can be called. | `fruit.next` | Reading a link follows it. Calling `append` executes behavior. |
| Node / traversal | A payload and links; walking follows a link per step. | `iter = iter.prev` | Rebinding the cursor preserves the node structure. |
| `return` | End the call and send its value to the caller. | `return newItem` | Assignment can keep the returned tail; mutation survives a discarded return. |
| `=` / mutation | Bind a name, set an attribute or write a slot according to the target. | `fruit = fruit.next` | Rebinding the name does not edit a link. |
| Scope / lookup | Resolve a bare name in visible namespaces. | `append()` | A dot lookup uses a receiver; a bare name has none. |
| `try / except` | Run a guarded block and handle a matching exception. | `except TypeError` | Continue after the handler; the failed statement is not retried. |
| `as` | Bind the caught exception inside the handler. | `except TypeError as err:` | `print(err)` displays the error message. |
| `type(x)` | Retrieve the object's class. | `type(err)` | For a missing bare name this yields the `NameError` class. |
| `.__name__` | Read a class name as a string. | `type(err).__name__` | The outer print shows the name without the class wrapper. |
| `NameError` | A name cannot be resolved. | `append` | Parentheses do not fix the failed name lookup. |
| `AttributeError` | A requested member is unavailable. | `a.append(3)` | On integer 7, argument checking is never reached. |
| `TypeError` | An operation or call is incompatible with its target or arguments. | `b.append()` | Found the list method; missing its required item. |
| `IndexError` | An indexed sequence has no such position. | `c[0]` | An empty list has no first element. |
| `ValueError` | A value cannot be accepted for the requested operation. | `float("two")` | Supplemental calculator also raises it for invalid input. |
| `#` comment | Annotate code without executing the annotation. | `# Display banana.` | Commented payload writes in the source driver never ran. |
| Detached HEAD | Git HEAD identifies a commit directly. | `git checkout master~17` | Checkout selects code. A subsequent invocation produces new output. |

### Containers, identity and membership

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| List display / constructor | Build a mutable ordered sequence. | `[]` or `list()` | Each creates a fresh empty list; `[7]` starts with one slot. |
| `None` | Singleton used here for absence of a neighbor or result. | `[None]` | As a stored element it counts toward length. |
| Subscription | Retrieve an item using an index. | `h[1]` | Zero-based index 1 selects the second item. |
| Item assignment | Replace the object at an existing mutable slot. | `i[1] = 73` | The slot must exist and the receiver must support writing. |
| Tuple / grouping | Fixed ordered slots; a comma packs a singleton. | `(2,)` | `(2)` is an integer expression; `()` is an empty tuple. |
| Mutability | Ability to change an existing object. | `price[1] = 3.98` | Tuple immutability constrains its own slots only. |
| Chained indexing | Use one lookup's result as the next receiver. | `budget[1][1]` | First lookup yields the list; the second identifies an egg price. |
| Aliasing / `is` | More than one path identifies the same object; test identity. | `budget[1] is price` | A write through either path is visible through both. |
| `list.append` | Add one element at the end of a list. | `b.append(3)` | Mutates in place and returns `None`. |
| `set()` / set display | Construct an unordered collection of distinct hashable members. | `{2, 5, 7}` | `set()` is empty; `{}` constructs an empty dictionary. |
| Iterable | Supplies successive elements to a consumer. | `set([2, 5, 7])` | One iterable supplies three members in one argument. |
| Hashable | Stable hash, consistent with equality; required for set members. | `{2, 2.0}` | These equal numbers collapse to one member; a list cannot join. |
| `.add(x)` | Include a set member if absent. | `j.add(2)` | Repeating it returns `None` and preserves `{2}`. |
| `dir(x)` | Inspect available attribute names. | `dir(j)` | Use the names to choose what to investigate. |
| `in` | Test membership. | `"add" in names` | Tests a name in the listing; does not call the method. |
| Flat file / DBMS | A file can persist data; a DBMS manages stored data. | Grocery records | Grouping two Python lists creates an in-memory structure. |

## 10. Knowledge check

1. A script is launched as `py calc.py 2 - 3`. What are the operator's index, the argument-list length and the numeric result?
2. From peach, which expression reaches apple? Which name still points to peach after the shared walk in the append driver?
3. Why can `fruit = fruit.append("banana")` advance a node driver while `b = b.append(3)` loses the list binding?
4. Predict the first error for `append()`, `(7).append(3)`, `[7].append()` and `type([][0])`.
5. How would you construct a tuple holding only `2`? What happens when you try to assign `73` to slot 1 of the five-element classroom tuple?
6. Starting with eggs at 4.99, predict the effect of `budget[1] = None`, then `price[1] = 3.98`, then `budget[1][1] = 0`. What stays fixed?
7. After `j = set()` and a failed `j = set(2, 5, 7)`, what does `j` contain? What do two calls to `j.add(2)` return and what do two inspections display?

### Answer key

1. Slot `2` holds `'-'`. There are four entries including the script. Conversion and subtraction return `-1.0`.
2. `fruit.prev.prev` follows peach → banana → apple. `fruit` stays at peach; `iter` finishes on apple.
3. The custom method returns the new node. The built-in list method appends in place and returns `None`, which assignment binds to `b`.
4. `NameError` at bare lookup; `AttributeError` at integer member lookup; `TypeError` at argument checking; `IndexError` at the inner subscription before `type` runs.
5. `(2,)`. Slot 1 exists, but assigning to the tuple raises `TypeError`. Its second value remains `3`.
6. The first write raises `TypeError`; eggs still cost 4.99. The list writes change the price to 3.98 and then 0. The tuple keeps the same two list references; milk and bread keep their prices.
7. The failed constructor leaves the empty set bound to `j`. Each `add` returns `None`. Each subsequent inspection displays `{2}`.

## Technical references

These official references clarify language and tool behavior. Classroom coverage comes from the supplied Session 16 Markdown, including its historical listings and micro-learning appendix.

- [Python · Script arguments](https://docs.python.org/3/library/sys.html#sys.argv)
- [Python · Running a subprocess](https://docs.python.org/3/library/subprocess.html#subprocess.run)
- [Python · Class and instance variables](https://docs.python.org/3/tutorial/classes.html#class-and-instance-variables)
- [Python · Grouped expressions and tuple displays](https://docs.python.org/3/reference/expressions.html#parenthesized-forms)
- [Python · Assignment statements and targets](https://docs.python.org/3/reference/simple_stmts.html#assignment-statements)
- [Python · Set types](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset)
- [Git · Checkout and detached HEAD](https://git-scm.com/docs/git-checkout)
