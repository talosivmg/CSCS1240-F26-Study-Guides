# What can this object do?

**CSCS1240F26 · Session 16 · Worked teaching companion**  
*Linked fruit nodes, built-in collections, and the difference between changing a name and changing an object*

`price[1] = 3.98` changes the price of eggs. `budget[1] = None` fails. Both statements ask Python to write to slot one. The difference is **which object owns that slot**. The route to this example starts with something humbler: a student trying to use command-line arguments for a real task, and a class trying to say exactly what “append” means. Follow that question through the session: *what object do I have, what operation am I asking it to perform, and what evidence would show that it worked?*

**Reading route:** §1 applies previous work on arguments; §2 follows fruit nodes in both directions; §3 predicts three different failures; §§4–5 compare lists, tuples and empty containers; §§6–7 explain the shared grocery budget; §8 tests what makes a set different. The syntax reference, knowledge check and definitions follow. Historical listings are separate from runnable teaching versions.

**Source and coverage.** [Recording: CSCS1240F26 16](https://www.youtube.com/watch?v=zx13yjJnsTs&t=5s), ID `zx13yjJnsTs`. The saved timestamped transcript has 1,243 cues, starting at `0:00:00` and ending with a cue starting at `1:04:04`. The previously probed video runs `1:04:06.617`; the helper reports `1:04:08` and does not expose individual cue ends. Recording metadata lists an English automatic track and no manual track; no speaker identities are supplied by the caption export. The complete local track, rather than the short attachment, is the speech source. All 65 whole-minute captures (`0:00:00`–`1:04:00`) plus 32 original event/closing captures decoded at 1920 × 1080. Two additional event frames (`0:08:25`, `1:03:09`) resolve the revised source and the duplicate-set readback. All 99 pictures matched fresh same-recording FFmpeg decodes pixel for pixel at their requested targets. This verifies the pictures, not what happened between captures. In examples, *observed* means shown on screen, *posted* means seen in an earlier chat message, and *independently run* refers to a separate guide fixture—not a second classroom run.

## 1 · From “I understand arguments” to “what could I do with them?”

*0:00:00–0:05:29 · class Discord discussion*

A student's markup shows `py .\src\example.py yes`. Python sees **two** entries in `sys.argv`: at index `0`, the script name `.\src\example.py`; at index `1`, the string `yes`. The printed `len(sys.argv)` is the integer `2`, not the string `"2"`. Count the script name when interpreting that length, but don't count the `py` launcher as another script argument. The instructor acknowledged the markup and invited the student to explain it in words. Instead of asking for another definition, the instructor asked what the student could **build** with it. Consider a calculator given two pieces of text at launch. To add numbers rather than join strings, convert the arguments to numbers, add them and send the result to standard output. A third argument could specify the operation. Now the same program needs a decision: which calculation did the caller request? Asking for another value *while the program is running* would use a different input channel, standard input.

The posted exercise develops the calculator in testable stages: accept two arguments and add their floating-point values; then accept three with the operator in the middle; check the number of arguments; validate the numeric and operator strings; raise an exception for illegal arguments. The post calls the program `calc<your initials>` and links the [`F26Notes` repository](https://github.com/joes-ta/F26Notes); square brackets in the post mark optional steps, including add/commit/push checkpoints. The bracket notation is documentation, not text for the command line. These are suggested extensions visible in the discussion, not evidence that a student implemented them. A separate dated post above the markup says an exceptions phrase mistakenly added to challenge `2058` had been removed; do not treat the calculator suggestions as a new grading requirement for that challenge. A student also proposed a dictionary-style word lookup that prints a matching definition. Multiple operations would pose another design question: **infix** places an operator between operands (`2 + 3`); **prefix** places it before them (`+ 2 3`); **postfix** places it after them (`2 3 +`). Those last three expressions illustrate the terms; the class did not implement an expression parser.

A second idea came from everyday camera/network support: supply an address at launch, record diagnostics and later read authorized devices' addresses from a file instead of retyping them. The screen shows a *posted* single-host example that runs a diagnostic, waits for it, and examines its ordinary output, diagnostic output and completion status. No live diagnostic run in this lesson is needed to learn that pattern. The instructor valued a student finding their own use for the material and mentioned possible extra credit **at the time**; that remark is not current grading policy.

**Annotated teaching version — guide-added comments; local-only transfer.** This substitutes a harmless child Python process for the posted network command. It preserves the operation the post was teaching: collect and inspect the three result fields. The child prints a word locally and performs no network I/O.

```python
import subprocess                       # Interface for starting a child process.
import sys                              # Use this interpreter, not a guessed command path.
result = subprocess.run(
    [sys.executable, '-c', "print('ready')"],  # One executable and its arguments.
    capture_output=True, text=True,     # Keep both output streams as strings.
)
print('stdout:', result.stdout.strip()) # Display the child's word without its final newline.
print('stderr:', repr(result.stderr))   # Empty string: nothing was written to stderr.
print('exit:', result.returncode)       # This child completed normally: zero.
```

**Independent local output:**

```text
stdout: ready
stderr: ''
exit: 0
```

In the **posted** result, `py .\subproc.py` displayed four replies, zero packet loss, an empty `STDERR:` field, and `Exit Code: 0`. Don't confuse the host program's `print` labels with the child's output. `capture_output=True` keeps the streams for inspection; `text=True` requests text instead of bytes.[4] A zero exit code describes that run, not a guarantee about all future runs. The posted example has a fixed hostname, not an implemented inventory tool. The student identified `subprocess` as the unfamiliar piece to look up; the instructor also invited them to explain the diagnostic to classmates.

**Key items to scan**

- **`sys.argv`** — launch arguments discussed in chat. Converting a numeric argument from text is different from reading a later line of standard input.
- Operator in the middle — the proposed three-argument calculator needs to validate the operator and dispatch to the right calculation.
- **`subprocess.run(...)`** — wait for one child and get a result; **`stdout`**, **`stderr`**, and **`returncode`** answer different questions.
- **`F26Notes`** — suggested repository for incremental commits; the chat is a record of a proposal, not a commit log for the student's work.

## 2 · Build three fruit nodes, then walk back without rebuilding them

*0:05:29–0:10:59 · constructor revision, followed by the append revision*

The instructor returns to the doubly linked list as a way to understand classes and objects. At the machine level the stored values are bit patterns; at the Python level it matters how we interpret them. Here three different **`Item`** instances count as three nodes, each with somewhere to keep a fruit value and references to neighboring nodes. A reference tells Python *which object* to reach; the string **`"banana"`** itself is not the link.

**Annotated teaching version — guide-added comments.** In the classroom the class definition lives in `item.py`, and the driver imports it with `from item import Item`. The two parts are together below so this example can run as one file.

```python
class Item:
    object = None                 # Default payload before any per-node value is set.
    next = None                   # No forward neighbor by default.
    prev = None                   # No backward neighbor by default.

    def __init__(self, obj=None): # self is the NEW node; obj is its optional fruit.
        self.object = obj         # Store the value on this particular node.

fruit = Item()                    # New node; the initializer stores None.
fruit.object = "apple"            # Replace the payload on that same node.
fruit.next = Item("banana")       # Make a second node and link apple forward to it.
fruit.next.prev = fruit           # Make banana point BACK to apple.
fruit.next.next = Item(obj="peach")  # Make peach; link banana forward to it.
fruit.next.next.prev = fruit.next   # Make peach point back to banana.

iter = fruit                      # A second name for the apple node, not a copy.
print(iter.object)                # apple
iter = iter.next                  # Move to banana; fruit still refers to apple.
print(iter.object)                # banana
iter = iter.next                  # Move to peach.
print(iter.object)                # peach
iter = iter.prev                  # Follow peach's backward link.
print(iter.object)                # banana
iter = iter.prev                  # Follow banana's backward link.
print(iter.object)                # apple
```

**Observed classroom output and independently reproduced:** `py .\test\fruit.py` at the constructor revision printed:

```text
apple
banana
peach
banana
apple
```

| Starting position | Instruction | Value displayed next | Why this node is reachable |
|---|---|---|---|
| **`fruit`** | **`iter = fruit`** | **`apple`** | The starting name already points there. |
| Apple | **`iter = iter.next`** | **`banana`** | Apple acquired a forward reference during setup. |
| Banana | **`iter = iter.next`** | **`peach`** | Banana acquired a forward reference to peach. |
| Peach | **`iter = iter.prev`** | **`banana`** | The driver explicitly installed the reverse reference. |
| Banana | **`iter = iter.prev`** | **`apple`** | Banana's **`prev`** points to the original starting node. |

The terminal shows **`git checkout master~18`** at the constructor checkpoint, then a run. Later **`git checkout master~17`** selects the revision whose message announces the addition of **`append`**. A checkout changes the code being examined; it does not rerun the driver or erase earlier output. The modified-file indicator for **`script/path.ps1`** is not the program's output. Git's detached-HEAD notice means a particular commit is checked out instead of an active branch; the screen's suggestions for creating a branch aren't commands the class necessarily ran.

Why introduce **`append`**? To ask for one action—“put this at the end”—instead of manually writing every link in the driver. The class works from ordinary English before committing to an implementation: adding something somewhere is not enough; *where* it is added matters. In **`def append(self, obj):`**, **`self`** is the node receiving the call and **`obj`** is the item to add. **`fruit.append("banana")`** supplies the explicit item; the bound method already knows which **`fruit`** received it. The changed driver assigns the result back to **`fruit`**, unlike the manual driver where **`fruit`** stays on apple. The recovered **`0:08:25`** frame shows why that assignment works. Do not attribute the five lines printed above to this later checkout.

**Annotated teaching version — guide-added comments.** This standalone extract repeats just the necessary setup and prints the changed starting position. Appendix A4 gives the full, separately replayed driver.

```python
class Item:
    object = None              # Default stored value.
    next = None                # No next neighbor yet.
    prev = None                # No previous neighbor yet.
    def __init__(self, obj=None):
        self.object = obj      # Store this node’s value, e.g. "apple".
    def append(self, obj):     # Called on the current end node with the new value.
        newItem = Item(obj)    # Create a NEW node, e.g. the banana node.
        newItem.prev = self    # Banana can now reach apple.
        self.next = newItem    # Apple can now reach banana.
        return newItem         # Return banana, NOT apple and NOT None.

fruit = Item("apple")          # Start a separate example at apple.
fruit = fruit.append("banana") # Call on apple; rebind fruit to the returned banana.
fruit = fruit.append("peach")  # Call on banana; rebind fruit to the returned peach.
iter = fruit.prev.prev         # From peach, follow banana back to apple.
print(fruit.object, iter.object)  # Two names, different positions: peach and apple.
```

**Independent script output:**

```text
peach apple
```

There is **no search for the end** inside this `append`. The driver makes the current node the end by retaining the return from each call. Calling this implementation on a node that already has a successor would overwrite its forward link; the demonstrated driver does not do that. Its walk then starts at apple again and follows the same five positions as the first trace. Compare the built-in list method in §3: there, assigning the return back to the list name would lose that name's link to the list.

**Key items to scan**

- **`Item()`** — creates a node initialized with **`None`**; **`fruit.object = "apple"`** changes that node, not its identity.
- **`Item("banana")`** / **`Item(obj="peach")`** — positional and named ways to supply the constructor's fruit value.
- **`next`** / **`prev`** — two independent directions. Installing the forward link does not automatically install its reverse.
- **`iter = iter.next`** — rebinds the local walking name. **`iter`** is just the instructor's name here, not a call to Python's iterator machinery.
- **`return newItem`** — gives the caller the new tail. After two appends **`fruit`** refers to peach, and **`fruit.prev.prev`** reaches apple.
- **`master~18`** / **`master~17`** — different Git revisions. Old output below a prompt does not prove the later file ran.

## 3 · Predict the error before pressing Enter

*0:10:00–0:22:05 · bare name, integer receiver, list receiver*

A student asks whether `append` is already built in. The name sounds familiar, but ask the more precise question: **append to what?** The instructor compares scope to a telescope or microscope: there is a region you can see and a region outside it. Python's bare-name lookup and its member lookup after a dot are distinct. Calling one class's method `append` doesn't install a global function, replace Python's list method, or give the method to integers. The instructor uses “overloading” informally here; the example is the same spelling on different types, not one global function selecting a signature.

Another student asks, “Can you append to an append?” The instructor treats it as an open design question, not a new operation the class has demonstrated. Grammatically *append* is a verb; to turn that verb into a method call, specify an object that can do it and the thing it should add. The suggestion that appending to **`7`** might mean addition or sticking digits on the end shows the problem: there is no single meaning Python had to give integers.

**Annotated teaching version — guide-added comments.** The class tried the expressions one by one at the prompt. These separate handlers reproduce the successive predictions without stopping the script on its first error.

```python
try:
    append                     # Look up this NAME before making any call.
except NameError as err:
    print(type(err).__name__)  # No bare append in this session.
try:
    append()                   # Parentheses still require the name to resolve first.
except NameError as err:
    print(type(err).__name__)
a = 7                          # Integer receiver.
try:
    a.append(3)                # No such attribute on 7; supplying 3 cannot fix it.
except AttributeError as err:
    print(err)
b = [7]                        # New list: a single integer at index zero.
try:
    b.append()                 # Method exists, but WHICH element should it add?
except TypeError as err:
    print(type(err).__name__)
result = b.append(3)           # Change b itself; the routine returns None.
print(result, b)               # No new list is returned. The old list now holds 7 and 3.
```

**Independent script output:**

```text
NameError
NameError
'int' object has no attribute 'append'
TypeError
None [7, 3]
```

At the prompt the successful method call produced no displayed result. Entering **`b`** on the *next* line displayed **`[7, 3]`**. That second display is the interpreter showing the list, not the method printing it. The **`0:22:00`** scrollback confirms the successful **`b.append(3)`** and separate **`b`** inspection. The class first tried the integer without a new item and then with **`3`**: both fail before argument counting because the integer cannot supply the method. For the list, lookup succeeds and argument counting becomes meaningful. Python does not require a custom **`Item.append`** to return what **`list.append`** returns.

**Key items to scan**

- **`append`** / **`append()`** — same **`NameError`** here: there is no bare name to inspect or call.
- **`a = 7`** — the integer has no **`append`**; changing the number of arguments cannot fix an **`AttributeError`**.
- **`b.append()`** — the method was found; a missing element now causes **`TypeError`**.
- **`b.append(3)`** — appends in place, returns **`None`**; inspecting **`b`** afterward reveals **`[7, 3]`**.

## 4 · An empty container is not a container of nothing

*0:22:00–0:26:15 · Python prompt*

Having seen a list accept `append`, the class asks a smaller question: what does a list contain **before** anything is appended? Square brackets can describe the contents literally. Calling the class by name can construct a list instead. Both routes can produce the same contents without being the same expression or creating the same object.

**Annotated teaching version — guide-added comments.** `print` makes the classroom's interactive displays visible in a script. The handler lets the example continue after the deliberate bad lookup.

```python
c = list()                  # Call the class to make a new empty list.
d = []                      # Describe another new empty list with a literal.
print(type(c))              # Display the type of the container itself.
try:
    print(type(c[0]))       # Must retrieve element zero BEFORE type can inspect it.
except IndexError as err:
    print(type(err).__name__)  # No first element: display IndexError and continue.
e = [None]                  # One slot, occupied by the object None.
print(c, d, e)              # Show the contents of the three separate containers.
print(len(c), len(e))       # Count elements, not the number of useful values.
```

**Independent script output:**

```text
<class 'list'>
IndexError
[] [] [None]
0 1
```

The important prediction was not whether Python would report the type of **`None`**. It never gets that far: indexing the empty list fails first. In the one-element list, **`e[0]`** really does refer to **`None`**. No element and an element used to stand for “no value” are different states.

**Key items to scan**

- **`[]`** and **`list()`** — two ways to make an empty list; neither supplies a slot at index zero.
- **`type(c)`** — asks about the list. **`type(c[0])`** first asks for an item that does not exist.
- **`[None]`** — one actual element. **`len(e)`** is **`1`**, whereas **`len(c)`** is **`0`**.

## 5 · When does `(2)` stop being just two?

*0:26:15–0:35:59 · predictions before checking the prompt*

The instructor works up to a new type rather than naming it first. Assign `2` to `f`, then put parentheses around `2` on the right of an assignment to `g`. If you evaluated the right-hand side on paper, you would still get two. Python agrees. Now try `2,3` inside those parentheses. It looks like a coordinate, and it is no longer one number: Python groups the two objects into a **tuple**. The class's “couple, triple, quadruple, quintuple” exchange makes the point that two is not a size limit; available memory is a practical limit.

**Annotated teaching version — guide-added comments.** Each new assignment to `h` replaces what the *name* refers to; it does not extend the previous tuple.

```python
f = 2                       # An integer.
g = (2)                     # Group the expression; the result is still the integer 2.
h = (2,3)                   # A pair: two slots in a particular order.
print(f, g, type(g), h, type(h))
h = (2,3,4,5)               # Rebind h to a new four-element tuple.
h = (2,3,4,5,54)            # Rebind it again. It now names the five-element tuple.
i = [2,3,4,5,54]            # Same starting values, in a separate list.
print(h[1], i[1])           # Index 1 is the SECOND slot of each sequence.
i[1] = 73                   # Replace the 3 in the existing list.
print(i, h[1])              # The tuple's second slot still contains 3.
try:
    h[1] = 73               # Attempt the same replacement in the tuple.
except TypeError as err:
    print(err)              # Report the failure; the tuple is unchanged.
```

**Independent script output:**

```text
2 2 <class 'int'> (2, 3) <class 'tuple'>
3 3
[2, 73, 4, 5, 54] 3
'tuple' object does not support item assignment
```

The list and tuple can both answer “what is in slot one?” The difference appears when the question becomes an instruction: “put `73` in slot one.” The list accepts that instruction; the tuple rejects it. This is **mutability** (the existing object can change) versus **immutability** (these slots cannot be changed). A failed item assignment does not prevent you from assigning an entirely different tuple to the *name* `h` later.

**Supplemental punctuation check.** `(2,)` is a tuple with one element. It is the comma, not a decorative pair of parentheses, that distinguishes this from `(2)`; `()` is the special empty-tuple form.[2]

**Key items to scan**

- **`g = (2)`** — grouping gives the integer **`2`**. **`h = (2,3)`** — the comma makes a pair.
- **`h[1]`** and **`i[1]`** — both retrieve **`3`** before any replacement; indexes start at zero.
- **`i[1] = 73`** — edits the existing list. **`h[1] = 73`** — raises **`TypeError`**, not an out-of-range error.
- **`h = ...`** — changes the binding of a name; it does not require the old object to be mutable.

## 6 · Why group things that already have names?

*0:36:00–0:44:59 · coordinates, then the grocery example*

A student asks why a tuple is preferable to a couple of variables. For just one point, **`x = 2`** and **`y = 3`** can work. Add another point and now someone must remember which of **`x1`**, **`x2`**, **`y1`**, and **`y2`** belong together. The pair **`(2, 3)`** carries that relationship as one value. For the instructor's example of a home's latitude and longitude, protecting the pair against accidental item replacement is useful. It does not mean a program can never record a different address.

Then comes the better test: if a tuple holds **lists**, can the contents of a list change? One student reasons that the tuple cannot acquire another list, but one of its existing lists could change. The instructor proposes two ways to settle the question: check the documentation or try the operation. The class chooses a grocery list and a price list, with an item and its price at corresponding positions: milk costs `2.99`, eggs `4.99`, bread `1.52`. It groups the *lists*, not individual item–price pairs, into `budget`.

**Annotated teaching version — guide-added comments.**

```python
grocery = ['milk', 'eggs', 'bread']  # Three strings in item order.
price = [2.99, 4.99, 1.52]           # Same order; eggs correspond to index 1.
budget = (grocery, price)           # New tuple referring to the two EXISTING lists.
print(budget[0])                    # Retrieve and display the entire grocery list.
print(budget[1])                    # Retrieve and display the entire price list.
```

**Independent script output:**

```text
['milk', 'eggs', 'bread']
[2.99, 4.99, 1.52]
```

**Key items to scan**

- **`(2, 3)`** — keeps a point's coordinates together; separate variable names alone do not encode that pairing.
- **`budget[0]`** — the grocery list, not just **`'milk'`**. **`budget[1]`** — the whole price list, not the price of eggs yet.
- Corresponding positions — **`'eggs'`** and **`4.99`** are both at index **`1`** in their respective lists. The programmer maintains that correspondence; the tuple does not check it.

## 7 · Three assignments, two objects, one egg price

*0:45:00–0:55:59 · follow the object receiving the final write*

The instructor tries to make everything free by replacing `budget[1]` with `None`. Python rejects the operation **before any price changes**. Replacing the second slot of the tuple is not the same instruction as changing a number in the price list. The class pauses here to ask what kind of data store it has made.

**Is this a database?** The class briefly debates the word while trying to change the egg price. A tuple of lists organizes data, but the demonstration uses no database management system (DBMS). Flat files can store the values too. A DBMS supplies facilities for managing data, such as querying, searching and sorting, that a flat-file program would otherwise have to supply or arrange itself. The instructor's advice for conversation or an interview was to distinguish the data from the software that manages it. No database was built in this demo.

Returning to the egg-price question, a student supplies the shorter route to the price list: go through its original name, **`price`**.

**Annotated teaching version — guide-added comments.** Continue with the two lists and `budget` from §6. The original session was interactive: the failed assignment returned control to the prompt. The handler below reproduces that ability to continue in a script.

```python
try:
    budget[1] = None        # Would replace the tuple's reference to its price list.
except TypeError as err:
    print(err)              # The tuple refuses; it still refers to the SAME list.
price[1] = 3.98             # Change the price for eggs through the list's own name.
print(budget)               # The sale price appears through the tuple too.
budget[1][1] = 0            # Follow the tuple to the list, then replace its second price.
print(budget)               # Eggs are free; milk and bread keep their prices.
```

**Independent script output:**

```text
'tuple' object does not support item assignment
(['milk', 'eggs', 'bread'], [2.99, 3.98, 1.52])
(['milk', 'eggs', 'bread'], [2.99, 0, 1.52])
```

| Attempt | Object asked to change | Effect on the second price |
|---|---|---|
| **`budget[1] = None`** | The outer tuple | Refused; still **`4.99`**. |
| **`price[1] = 3.98`** | The existing price list | **`4.99`** becomes **`3.98`**, visible inside **`budget`**. |
| **`budget[1][1] = 0`** | That same price list, reached through the tuple | **`3.98`** becomes **`0`**, visible through **`price`** too. |

Read the last target from left to right: **`budget[1]`** gets the second object in the tuple; the next **`[1]`** selects the second slot in that object. More precisely, assignment evaluates the right-hand value first, then resolves the target and performs the write.[3] The square brackets work in matched pairs, with an operand (the index) inside each pair. A student’s question about changing the name is a different issue: assigning a different object to **`budget`** would rebind the name, not edit the existing tuple. The instructor contrasts chained indexing with **`3 + 5 * 2`**, where multiplication happens first and the result is **`13`**, not **`16`**. The spoken multiplication example is not a displayed run: at **`0:54:45`** the pending prompt instead reads **`y = 3 + 5 + 2`**. No printed **`13`** is evidenced there. Chained indexing is not multiplication; each step supplies the object for the next step. A third index only makes sense if the value you have reached supports it.

**Key items to scan**

- **`budget[1] = None`** — invalid tuple-slot replacement; **`None`** is not a command to zero out the prices.
- **`price[1]`** — a direct path to the egg slot. **`budget[1][1]`** — an indirect path to exactly the same slot.
- **`3 + 5 * 2`** — first **`5 * 2`**, then add **`3`**; use the actual operator rules, not a universal “always left to right.”
- DBMS — management software. Grouping values in **`budget`** was a Python data-structure exercise, not a DBMS operation.

## 8 · A set answers “is it there?”, not “what's at position zero?”

*0:56:00–1:03:30 · Python prompt, then closing discussion*

The class has been using square brackets to retrieve positions. A natural experiment is to start with an empty set, `j = set()`, and try `j[0]`. With an empty **list** this would be an `IndexError`: the operation exists but that slot does not. With a **set**, Python raises `TypeError`: positional indexing isn't an operation on this object at all. Adding elements will not create a “first” slot you can retrieve by index.

The instructor next tries to give the constructor three integers as three arguments. It rejects the call. Inspecting **`j`** afterward still shows the earlier empty set: Python must finish evaluating the right-hand side before it can rebind the name. Next, **`dir(j)`** lets the class look for the operation it actually needs. **`append`** isn't there. That makes sense: adding to the *end* presupposes a sequence. **`add`** is there, and adding the same member twice does not create two copies. At **`1:03:09`**, the two calls and both separate inspections of **`j`** are visible: each inspection displays **`{2}`**.

**Annotated teaching version — guide-added comments.** The handlers stand in for the interactive prompt, which lets you try the next expression after an exception. The membership tests against `dir(j)` condense its long observed display.

```python
j = set()                     # New empty set, not an empty dictionary.
try:
    print(j[0])               # No positional indexing, even if j had members.
except TypeError as err:
    print(err)
try:
    j = set(2,5,7)            # Three arguments, not one collection to read from.
except TypeError as err:
    print(err)                # No new value was assigned to j.
print(j, type(j))             # Still the original empty set.
print('append' in dir(j), 'add' in dir(j))  # Inspect the available operation names.
try:
    j.append(2)               # The set has no such method.
except AttributeError as err:
    print(err)
j.add(2)                      # Make 2 a member; does not return the set.
print(j)                      # One distinct member.
j.add(2)                      # Already present. No error, no duplicate.
print(j)                      # The contents have not changed.
```

**Independent script output:**

```text
'set' object is not subscriptable
set expected at most 1 argument, got 3
set() <class 'set'>
False True
'set' object has no attribute 'append'
{2}
{2}
```

**A correction worth keeping.** The discussion suggests that Python lacks a set literal. What the failed call establishes is narrower: the constructor doesn't take *three separate positional arguments*. Python has a nonempty set display, `{2, 5, 7}`. `set([2, 5, 7])` works too because the list is **one iterable argument**. Use `set()` for an empty set: `{}` is an empty dictionary. A set can contain many kinds of object, not just numbers, but a member must be **hashable**. The set itself is mutable; that does not make a mutable list eligible to be one of its members. Uniqueness follows hashing and equality, not simply “a different object at a different address.”[1][2]

**Supplemental check — independently run, not a classroom command:**

```python
print({2, 5, 7} == set([2, 5, 7]))  # Same members; don't rely on their display order.
print(type({}).__name__)            # Empty braces construct a dict.
print(len({2, 2.0}))                # Equal numeric values count as one set member.
try:
    set().add([2])                  # The list is not hashable.
except TypeError as err:
    print(err)
```

```text
True
dict
1
cannot use 'list' as a set element (unhashable type: 'list')
```

**Key items to scan**

- **`j[0]`** — fails because a set is not positionally indexable; a larger set would fail too.
- **`set(2,5,7)`** — three arguments, rejected. **`set([2, 5, 7])`** — one iterable, accepted.
- **`dir(j)`** — inspect member names; use it to find **`add`**, not to infer a method's full contract.
- **`j.add(2)`** — puts **`2`** in the set if absent. Its return is **`None`**; inspect **`j`** to see **`{2}`**.
- **`{}`** versus **`set()`** — an empty dictionary versus an empty set. **`{2}`** is a set with one member.

## Syntax to remember

| Pattern | What to ask in this lesson |
|---|---|
| **`name(...)`** | Does the bare name exist before Python can call it? |
| **`receiver.method(arg)`** | Which object is to the left of the dot? Does its type supply the method? Does the call supply what it needs? |
| **`Item(obj = "peach")`** | The keyword names the parameter; the string becomes the new node's stored value. |
| **`iter = iter.prev`** | Follow the existing backward link. Change the name's current node, not the node's stored value. |
| **`()`** / **`(2)`** / **`(2,)`** | Empty tuple / grouped integer / one-element tuple. |
| **`[7]`** / **`b[0]`** | Construct a list containing seven / index an existing object. Position in the expression changes the brackets' job. |
| **`budget[1][1] = 0`** | Retrieve the price list, then change its second slot. The outer tuple stays intact. |
| **`set()`** / **`j.add(2)`** | Make an empty set / include one value, without promising a positional order. |

## Knowledge check

1. You are at the peach node. Which two links take you back to apple? Does following them change any fruit names?
2. Why do **`append()`**, **`a.append(3)`** for **`a = 7`**, and **`b.append()`** for **`b = [7]`** fail at three different stages?
3. If **`c = list()`** and **`e = [None]`**, which expression can produce **`type(None)`** as its result: **`type(c[0])`** or **`type(e[0])`**? Why?
4. A teammate writes **`point = (2)`** and wants a fixed sequence of one coordinate. What did they construct? What is the smallest change that gives them the intended type?
5. Starting with the original grocery and price lists, predict the effects of **`budget[1] = None`** and **`budget[1][1] = 0`**. Which name besides **`budget`** can show the changed price?
6. You want the unique values 2, 5 and 7. Why does **`set(2,5,7)`** fail? Give two valid constructions. If you add 2 again, what changes?

### Answer key

1. Peach's **`prev`** is banana; banana's **`prev`** is apple. The moving name follows references. Neither the payloads nor the links need to change.
2. No bare **`append`** name is defined in this session (**`NameError`**). An integer exists but has no **`append`** attribute (**`AttributeError`**). The list does have the method, so Python reaches argument checking and rejects the missing new element (**`TypeError`**).
3. Only **`type(e[0])`**. Element zero exists and contains **`None`**, whose type is **`NoneType`**. In the other expression, indexing fails with **`IndexError`** before **`type`** can be called.
4. **`(2)`** is an integer expression with grouping. Add the comma: **`(2,)`** is a one-element tuple. Rebinding **`point`** later is still possible.
5. The first statement tries to replace a slot of an immutable tuple and raises **`TypeError`**. The second writes to the existing mutable price list, so **`price[1]`** also shows **`0`**. The tuple's two slots still refer to the original lists.
6. The constructor expects at most one argument. **`{2, 5, 7}`** and **`set([2, 5, 7])`** both work. Adding an equal member leaves the same three distinct values; don't use display order as evidence of insertion position.

## Library of definitions

| Term | Meaning in Session 16 |
|---|---|
| Standard input / standard output / standard error | The running program's input stream, its ordinary result stream, and its diagnostic stream. Launch arguments are supplied separately. |
| Class / instance | **`Item`** describes the kind of object; the banana node is one particular object of that class. |
| Constructor / initializer | Calling **`Item(...)`** produces a new instance; its **`__init__`** initializes that instance's stored value. |
| Attribute / method | A named part of an object, such as **`prev`**; a method is callable behavior found on an object, such as the list's **`append`**. |
| Scope / attribute lookup | Scope concerns where a name can be resolved. Looking for **`append`** in the surrounding code is different from looking for it on **`b`** after a dot. |
| Receiver / argument / parameter | In **`b.append(3)`**, **`b`** is the receiver and **`3`** is the explicit argument. A parameter is a name in the method definition. |
| Node / link / traversal | An **`Item`** holds a fruit and references to neighbors. Traversal follows those references without needing to recreate the nodes. |
| Literal or display / constructor call | **`[7]`** directly specifies list contents; **`list()`** calls the class to construct a list. |
| Sequence / index | An ordered collection and a position within it; in the five-element tuple **`h`**, position **`1`** holds **`3`**. |
| Binding / mutation | Binding associates a name with an object; mutation changes the object itself. Rebinding **`h`** and editing **`i[1]`** are different operations. |
| Immutable / mutable | A tuple cannot replace its slots; a list can. Immutability does not recursively freeze every object referenced from a tuple. |
| Aliasing | Multiple routes reach the same object. **`price`** and **`budget[1]`** reach the very same list. |
| Iterable / hashable | An iterable can supply successive values to a constructor. A hashable object has a stable hash compatible with equality and can be a set member.[1] |
| Uniqueness / positional order | A set stores one member per equal value; that does not give its members user-addressable numbered positions. |
| Flat file / DBMS | A file can hold the data; a database management system supplies operations to organize, query and manage it. |

## Sources

Primary lesson: [CSCS1240F26 16 — JoesTA, full recording](https://www.youtube.com/watch?v=zx13yjJnsTs&t=5s). Elapsed-time anchors in the guide refer to the full recording; the five-second link parameter is not the lesson's starting boundary. The historical appendix identifies supporting capture times. Supplemental Python references, not additional classroom events:

[1] [Built-in Types](https://docs.python.org/3/library/stdtypes.html) · sequences, lists, sets and membership.

[2] [Expressions](https://docs.python.org/3/reference/expressions.html) · grouping, container displays, subscriptions and calls.

[3] [Simple statements](https://docs.python.org/3/reference/simple_stmts.html) · name binding and assignment to subscription targets.

[4] [Subprocess management](https://docs.python.org/3/library/subprocess.html) · running a child process and examining its result.

## Appendix A · Historical screen code and run record

**How to read this appendix:** numbers to the left of `│` are gutters, **not Python to type**. Discord listings use guide-added line numbers; Sublime Text listings use the visible editor gutters. Blank numbered rows are visible blank lines. Prompts below are interactive evidence, not lines to put in a `.py` file. Added comments and independent fixtures belong in the teaching sections, not in these transcriptions.

### A1 · The posted subprocess example

**Discord `#general`, `0:03:00`; output continues at `0:04:00`.** This is a chat message on screen, not a command typed live during this recording. The list on line 3 supplies the program name and its argument separately. Lines 5–7 print three fields from the result after the child exits.

```text
 1 │ import subprocess
 2 │
 3 │ result = subprocess.run(["ping", "google.com"], capture_output=True, text=True)
 4 │
 5 │ print(f"STDOUT: {result.stdout}")
 6 │ print(f"STDERR: {result.stderr}")
 7 │ print(f"Exit Code: {result.returncode}")
```

The posted command was **`py .\subproc.py`**. Four packets were sent and four received, with zero loss; the posted summary gives minimum **`35ms`**, maximum **`43ms`**, average **`37ms`**. No error text follows **`STDERR:`**; **`Exit Code: 0`** follows it. §1 uses a *different*, local-only child for independent verification.

### A2 · Constructor checkpoint: `src\item.py`

**Sublime Text, `0:06:05`; constructor commit `2499ca0`.** The three indented assignments near the top are class attributes. They supply defaults when an instance has no corresponding attribute of its own. The initializer then writes `object` on the newly created instance, not on all nodes.

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

### A3 · Same checkpoint: `test\fruit.py`

**Sublime Text, `0:06:05`; PowerShell, `0:06:35`.** Lines 3–10 set up three nodes. Lines 7 and 10 start with `#`: they are not second writes to the banana and peach payloads. Lines 12–22 move the local name `iter` first forward, then backward, and print after each position is reached.

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

**Observed run:** `PS C:\CSCS1240F26\Item> py .\test\fruit.py` printed `apple`, `banana`, `peach`, `banana`, `apple`, each on its own line. An independent replay of these two transcribed files, with only the displayed gutters removed, produced the same five lines. At `0:08:10` the terminal still displays those lines **above** the checkout of `master~17` (`fc6c333`, `Item: addition of append( ).`). They belong to the constructor checkpoint, not to the changed driver.

### A4 · Append checkpoint: `src\item.py` and `test\fruit.py`

**Unobstructed event frame, `0:08:25` · commit `fc6c333`.** Both revised files are visible at once; no assembly across a covered screen is necessary. In the class, lines 9–13 create and return the next node. In the driver, lines 3–5 therefore leave `fruit` on peach. Lines 7–17 first step back to apple, then traverse in both directions.

**`src\item.py`**


```text
 1 │ class Item:
 2 │     object = None
 3 │     next = None
 4 │     prev = None
 5 │
 6 │     def __init__(self, obj = None):
 7 │         self.object = obj
 8 │
 9 │     def append(self, obj):
10 │         newItem = Item(obj)
11 │         newItem.prev = self
12 │         self.next = newItem
13 │         return newItem
14 │
```


**`test\fruit.py`**


```text
 1 │ from item import Item
 2 │
 3 │ fruit = Item("apple")
 4 │ fruit = fruit.append("banana")
 5 │ fruit = fruit.append("peach")
 6 │
 7 │ iter = fruit.prev.prev
 8 │ print( iter.object )
 9 │ iter = iter.next
10 │ print( iter.object )
11 │ iter = iter.next
12 │ print( iter.object )
13 │
14 │ iter = iter.prev
15 │ print( iter.object )
16 │ iter = iter.prev
17 │ print( iter.object )
18 │
```

**Independent exact-file replay:** with the two files transcribed as shown, the five lines are `apple`, `banana`, `peach`, `banana`, `apple`. There is no corresponding post-checkout classroom execution established by this frame.

### A5 · Python prompt: evidence for the error sequence

After the checkout, bare `py` launched an interactive Python 3.14.7 prompt, **not another run of `fruit.py`**. The excerpts below assemble submitted entries from overlapping frames; intervening traceback scaffolding is omitted where only the last line is reproduced. The instructor asked for a prediction before Enter. Text to the right of the live caret is not a completed call.

**Lookup before argument checking** (`0:13:50`, `0:20:15`, `0:22:00`). The second bare-name error is the same category even though the second expression adds parentheses.

```text
>>> append
NameError: name 'append' is not defined
>>> append()
NameError: name 'append' is not defined
>>> a = 7
>>> a.append()
AttributeError: 'int' object has no attribute 'append'
>>> a.append(3)
AttributeError: 'int' object has no attribute 'append'
>>> b = [7]
>>> b.append()
TypeError: list.append() takes exactly one argument (0 given)
>>> b.append(3)
>>> b
[7, 3]
```

**Retrieving before inspecting** (`0:23:00`, `0:24:30`, `0:26:15`). The traceback points to the inner lookup; the later lengths settle the question about `None`.

```text
>>> c = list()
>>> c
[]
>>> d = []
>>> d
[]
>>> type(c)
<class 'list'>
>>> type(c[0])
Traceback (most recent call last):
  File "<python-input-14>", line 1, in <module>
    type(c[0])
         ~^^^
IndexError: list index out of range
>>> e = [None]
>>> e
[None]
>>> len(c)
0
>>> len(e)
1
```

**Reading versus writing** (`0:30:15`–`0:34:25`). The completed assignments to `h` used four and five items; an earlier three-item line was still being edited. The spaces around the assignment sign do not change the language rule. Here is the list/tuple comparison as entered.

```text
>>> h = (2,3,4,5)
>>> h = (2,3,4,5,54)
>>> h
(2, 3, 4, 5, 54)
>>> type(h)
<class 'tuple'>
>>> h[1]
3
>>> i = [2,3,4,5,54]
>>> i[1]
3
>>> i[1]=73
>>> i
[2, 73, 4, 5, 54]
>>> h[1]
3
>>> h[1]=73
Traceback (most recent call last):
  File "<python-input-38>", line 1, in <module>
    h[1]=73
    ~^^^
TypeError: 'tuple' object does not support item assignment
```

**The two routes to egg prices** (`0:45:45`, `0:50:00`, `0:53:00`). Chronological excerpts: the original prices below the failed assignment are a readback, not a reset. At `0:55:00` the old prices are selected in earlier scrollback, not newly printed after the eggs become free.

```text
>>> budget = (grocery, price)
>>> budget
(['milk', 'eggs', 'bread'], [2.99, 4.99, 1.52])
>>> budget[0]
['milk', 'eggs', 'bread']
>>> budget[1]
[2.99, 4.99, 1.52]
>>> budget[1] = None
Traceback (most recent call last):
  File "<python-input-45>", line 1, in <module>
    budget[1] = None
    ~~~~~~^^^
TypeError: 'tuple' object does not support item assignment
>>> budget[1]
[2.99, 4.99, 1.52]
>>> price[1] = 3.98
>>> budget
(['milk', 'eggs', 'bread'], [2.99, 3.98, 1.52])
>>> budget[1][1] = 0
>>> budget
(['milk', 'eggs', 'bread'], [2.99, 0, 1.52])
```

**Two incompatible requests of a set** (`0:57:00`–`1:01:00`). The first error concerns indexing; the second concerns how many arguments the constructor accepts.

```text
>>> j = set()
>>> j
set()
>>> j[0]
Traceback (most recent call last):
  File "<python-input-53>", line 1, in <module>
    j[0]
    ~^^^
TypeError: 'set' object is not subscriptable
>>> j = set(2,5,7)
Traceback (most recent call last):
  File "<python-input-54>", line 1, in <module>
    j = set(2,5,7)
TypeError: set expected at most 1 argument, got 3
>>> j
set()
```

**Which operation does this object offer?** The actual call was `dir(j)`; visible names include `add`, `discard`, `intersection`, `union` and `update`, but not `append`. `type(j)` displayed `<class 'set'>`. At `1:03:00` the scrollback shows:

```text
>>> j.append(2)
Traceback (most recent call last):
  File "<python-input-58>", line 1, in <module>
    j.append(2)
    ^^^^^^^^
AttributeError: 'set' object has no attribute 'append'
>>> j.add(2)
>>> j
{2}
>>> j.add(2)
>>>
```

The first `{2}` comes from evaluating `j`, not from `add`. At `1:03:05` a second `j` is typed at the prompt; the targeted `1:03:09` capture shows its submitted result, `{2}`, after the second `j.add(2)`. The class therefore **displayed the unchanged set twice**, independently confirmed by the fixture in §8. At closing, Discord shows the **same posted example** as §1, not a second live network test. The final caption says “and I'm going to save”; the sampled screen does not establish that a save completed.

## Appendix B · Micro-Learning Insights

*This last appendix can be removed without losing the worked lesson, definitions, knowledge check, or historical evidence.*

- **Count what the program actually receives.** In the posted `py .\src\example.py yes` example, the script name occupies `sys.argv[0]` and `yes` occupies `sys.argv[1]`. Two entries do not mean two user-supplied operands. *(0:01:00)*
- **Grow a practice program by one decision at a time.** Add two numeric arguments first; then put an operator between them; only then tackle invalid counts and illegal values. Each stage gives you something small to test and commit. *(0:02:00)*
- **Ask what remains unfamiliar.** The student singled out `subprocess`. Separating the unfamiliar module from the familiar `print` calls gives a sharper next question than “I don't understand this program.” *(0:04:00)*
- **A name can walk while the structure stays still.** Draw apple ↔ banana ↔ peach, put a marker on apple, and move only the marker for each `iter = iter.next` or `iter = iter.prev`. That produces five displays from three nodes. *(0:06:05–0:06:35)*
- **Keep a run with its revision.** Output above `git checkout master~17` cannot show what happened after that checkout. Find the invocation before you connect code and output. *(0:08:10)*
- **Read an error as a progress report.** `NameError` for `append()` means there is no bare name; “no attribute” for `a.append(3)` means the name `a` resolved; the missing-argument error for `b.append()` means even the member lookup succeeded. *(0:13:50–0:22:00)*
- **Ask what an expression has to do first.** `type(c[0])` cannot answer the type question until `c[0]` succeeds. An empty container has no first element, not a hidden `None`. *(0:24:30–0:26:15)*
- **Change one feature of a test.** Keep `2,3,4,5,54`, index `1` and replacement `73` fixed; change only the kind of container. The different outcome isolates list versus tuple behavior. *(0:32:25–0:34:25)*
- **Point to the destination of a write.** In `budget[1] = None`, point to a tuple slot. In `budget[1][1] = 0`, point to a price-list slot. Being *inside* a tuple does not turn the list into a tuple. *(0:45:45–0:53:00)*
- **Read back the data, not the method's return.** `b.append(3)` changes the list; `b` displays it. `j.add(2)` changes membership if necessary; `j` displays the set. Neither method prints the collection. *(0:22:00; 1:03:00)*
- **Test order and duplication separately.** `j[0]` asks for a position; `j.add(2)` twice tests whether the collection permits another equal member. The two experiments answer different questions. *(0:57:00–1:03:05)*
- **Limit the conclusion to the experiment.** `set(2,5,7)` disproves a three-argument constructor call, not the existence of `{2, 5, 7}`. Confirm the broader syntax rule in the language reference.[1][2] *(0:59:55)*
