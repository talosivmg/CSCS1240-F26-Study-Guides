# Python collections, call stacks, and the missing banana

**CSCS1240F26 · Session 17 · Worked teaching companion**

When the class asks a set to remove `2`, the number disappears, but the variable meant to receive it does not contain `2`. Later, a call that creates a banana succeeds, yet the next walk through the fruit list skips the banana. These are two different surprises with the same useful starting question: *what did the call change, and what did the caller do with the value it returned?*

**Reading route.** Start in the interactive Python shell with lists, tuples, and sets. Follow the question about `pop()` into the call stack and tracebacks. Then use dictionaries and JSON to separate a Python object from the text that describes it. Finally, trace the same apple, banana, and peach through both directions of a hand-built list. The knowledge check and definitions precede the historical screen listings; the final insights appendix is detachable.

**Recording and evidence.** [JoesTA · CSCS1240F26 17](https://www.youtube.com/watch?v=scn2bNF9RRA&t=1s).[1] Times are elapsed recording times, not clock times. The saved English auto-captions have 2,716 cues, beginning at 0:00:00.560 and ending at 1:49:50.520. Their words match the saved timestamped transcript. Turns are not reliably named. The capture manifests report a video duration of 1:50:01.550. The final 11.03 seconds have no caption; that does not establish silence.

**Visual coverage.** The same-recording PNGs cover all 111 whole minutes, 0:00:00 through 1:50:00, with nine additional event frames and a closing frame at 1:50:00.800. All 121 images decoded at 1920 × 1080. These are requested seek times, not probed timestamps of individual decoded frames. No source movie was located for a fresh pixel comparison. A still can establish what was visible, not that the instructor saved or ran the editor buffer next to a terminal. The annotated examples explain behavior; the historical appendix preserves what was legible on screen.

## 0:02:36 — Choose a collection by what must stay true

The class has been building a doubly linked list: neighboring items can be connected in both directions. Before extending it, the instructor returns to the previous session’s question: why would Python offer several ways to collect values? If two apples occur in a sequence, you might need both occurrences and their positions. If all you need is which fruits occur, counting apple twice is precisely what you do *not* want. Start with the problem, not the familiar list.

A terminal visible at 0:03:00 supplies a reminder of the custom project. Its existing scrollback shows **`git checkout master~17`**, a checkout at **`fc6c333`**, and **`py .\test\fruit.py`** displaying **`apple`**, **`banana`**, **`peach`**, **`banana`**, **`apple`**, one per line. The output is consistent with visiting fruit in both directions; the driver's instructions aren't visible here, so it does not establish a particular traversal implementation. This is not a run of the collection expressions below.

| Ask of the collection | List | Tuple | Set |
|---|---|---|---|
| Can I change its membership or replace entries in place? | Yes | No: its sequence of entries is fixed | Yes: add or remove members |
| Is position part of its meaning? | Yes | Yes | No: membership has no first or last position |
| Can equal values occur more than once? | Yes | Yes | No: adding an existing value does not increase membership |

A tuple's immutability means you cannot replace one of its entries, not that a name can never be assigned another tuple. Similarly, an unordered set may display its members in a particular sequence without promising that sequence to your program.

**Key items to scan**

- **`list`** — keep an ordered, editable sequence; repeating a fruit is allowed.
- **`tuple`** — preserve a sequence of entries without allowing replacement, insertion or removal in that tuple.
- **`set`** — track distinct members, not their positions or counts of repetitions.
- **`git checkout master~17`** and **`fc6c333`** — the checkout shown in the custom-list shell; the message identifies addition of the append operation.
- **`py .\test\fruit.py`** — earlier visible driver run; **`apple`**, **`banana`**, **`peach`**, **`banana`**, **`apple`** are its five output lines, not a set display.

## 0:04:24 — Ask Python what each notation actually made

In the interactive Python shell, a bare expression can display its value without an explicit print. The instructor uses **`type()`** to test an assumption: does calling a class and writing a literal produce the same *kind* of object? Both list expressions produce empty lists, but they create two distinct lists, not one object under two names.

**Annotated teaching version — guide-added comments.** Read these as inputs at the interactive prompt. Results in comments are the classroom displays (completed sequence visible at 0:08:00).

```python
a = list()       # Call the list constructor; a refers to a new empty list.
b = []           # Create another empty list using square brackets.
type(a)          # <class 'list'>
type(b)          # <class 'list'>; same type does not mean same object.
c = tuple()      # No items supplied: c refers to the empty tuple.
type(c)          # <class 'tuple'>
d = (3)          # Group the expression 3; d refers to an integer.
type(d)          # <class 'int'>, not a tuple.
e = (2,3)        # The comma separates two entries; keep their order.
type(e)          # <class 'tuple'>
f = set()        # Create an empty set; keep it for the next experiments.
type(f)          # <class 'set'>
```

Why was **`(3)`** not a tuple? Parentheses can just group an arithmetic expression. Three in parentheses is still three. In **`(2,3)`**, the comma does different work. A useful refinement to the class's “multiple values” explanation: **`(3,)`** really is a one-item tuple. It needs a comma, not a second value. The empty tuple **`()`** is a special case.

The instructor says sets have no literal and later calls the braces in the display just a representation. The empty-set example is right; the wider claim is not. **`set()`** makes an empty set because **`{}`** already means empty dictionary. Braces with comma-separated values, such as **`{2,4,6}`**, *do* construct a nonempty set. There is no need to invent new punctuation. The following checks were run independently, not typed in the recording:

```python
type((3,))     # <class 'tuple'>: one value and a trailing comma.
type({})       # <class 'dict'>: empty braces alone are not a set.
{2,2,4}        # {2, 4}: equal members collapse even in the literal.
```

**Key items to scan**

- **`type()`** — inspect the object rather than identifying it from its punctuation alone.
- **`list()`** and **`[]`** — each makes an empty list; **`a`** and **`b`** refer to separate objects.
- **`tuple()`** and **`()`** — two ways to express the empty tuple.
- **`(3)`**, **`(3,)`**, **`(2,3)`** — respectively an integer, a one-item tuple, and a two-item tuple.
- **`set()`** — the constructor used for the empty set **`f`**.
- **`{}`** versus **`{2,4,6}`** — empty dictionary versus nonempty set. **`{2,2,4}`** contains just two distinct integers.

## 0:09:27 — Add one member, not a list of members

How do you put something into the empty set? Rather than guessing a method name, the instructor enters **`dir(f)`**. It lists names available on this particular object; among them are **`add`**, **`pop`**, and **`remove`**. Why not call the operation **`append`** or **`insert`**? Appending to a list puts a value *at the end*. Inserting at a position also assumes an order. Adding to a set promises only that the member will be present. The instructor initially overlooks **`remove`** in the long list, then finds it: inspect the actual names rather than relying on the first glance.

The test at 0:13:00 is more specific than “sets can't hold lists of values.” The call passes *one* argument: a list containing three integers. Python tries to store the list itself as one member; it does not unpack the three integers. A set needs elements with a usable stable hash for membership lookup. Ordinary lists are mutable and unhashable. The attempt therefore raises an exception before anything is added.

**Annotated teaching version — guide-added comments.** Each statement was entered at a separate prompt; the exception does not prevent entering the next command.

```python
f.add([2,4,6])   # One argument, a list. Raises TypeError; f stays empty.
f.add(2)         # Store the integer 2, not a list. f now contains 2.
f.add(4)         # Add a distinct member to the same set: 2 and 4.
f.add(6)         # Add a third member. The existing set changed in place.
f                # REPL displays {2, 4, 6}; the add calls printed nothing.
```

Classroom error, exactly as displayed:

```text
TypeError: cannot use 'list' as a set element (unhashable type: 'list')
```

The instructor acknowledges that a loop could avoid retyping the three calls. The instructor also recalls that adding a duplicate did not add another occurrence. A small independently run version makes both points visible:

```python
f = set()                 # Start over with a NEW empty set for this example.
for v in [2,4,6,6]:       # Visit four integers; the last repeats 6.
    f.add(v)              # Add this integer, not the list used by the loop.
```

| Pass | Integer supplied | Members after call | Return from the call |
|---|---:|---|---|
| 1 | 2 | `{2}` | `None` |
| 2 | 4 | `{2, 4}` | `None` |
| 3 | 6 | `{2, 4, 6}` | `None` |
| 4 | 6 | `{2, 4, 6}` — unchanged | `None` |

**Key items to scan**

- **`dir(f)`** — asks for available names, including **`add`**, **`pop`**, and **`remove`**; it does not call them.
- **`append`** / **`insert`** — imply an end or a position; neither describes set membership.
- **`f.add([2,4,6])`** — tries to add an unhashable list as one member and raises **`TypeError`**.
- **`f.add(2)`**, **`f.add(4)`**, **`f.add(6)`** — three calls with individually hashable integers. A repeated 6 adds nothing new.
- **`for v in [2,4,6,6]:`** — it is fine to *loop over* a list to add its members; the error concerns *storing* the list itself.

## 0:13:13 — Separate what changed from what came back

A student chooses 2 to remove from the three-member set. When **`g = f.remove(2)`** finishes, there are two different questions to ask: *What remains in the set?* and *What did the call return to the assignment?* Examining just one name cannot answer both. Continue from the classroom set with members 2, 4 and 6.

**Annotated teaching version — guide-added comments; classroom inputs and results.**

```python
g = f.remove(2)   # Change f by deleting 2; bind g to the return value, None.
f                 # Displays {4, 6}; the original set itself has changed.
g                 # No REPL echo for None. This is not an undefined name.
g                 # Still no echo; looking at g does not remove another item.
type(g)           # Displays <class 'NoneType'>, positive evidence about g.
h = f.pop()       # Remove an arbitrary remaining member AND return it to h.
h                 # Displays 4 in this run.
f                 # Displays {6}; this is a one-member set, not an integer.
```

A student wonders whether inspecting **`g`** is still removing things. It is not: the call ran once, on the right side of the assignment. The name now holds **`None`**, a real value representing the absence of a useful result. The interactive shell suppresses its display for a bare expression. **`f.pop()`**, by contrast, both mutates the set and hands back the removed member. It took 4 *in this run*; it did not promise “the smallest,” “the first,” or “the last.” That distinction will matter when the class borrows the word *pop* from a structure that really does have a particular end.

**Key items to scan**

- **`g = f.remove(2)`** — remove the named member 2; **`f`** retains 4 and 6, and **`g`** gets **`None`**.
- **`type(g)`** — displays **`<class 'NoneType'>`**; no REPL echo from **`g`** is not the same as no binding.
- **`f.pop()`** — chooses an existing member with no position guarantee and returns it as it removes it.
- **`h`** and **`{6}`** — the saved member is 4; the surviving set contains 6.

## 0:15:30 — From a traceback to a call inside a string

Where does *pop* come from? The instructor points back to the traceback for the rejected list. An error report like that is often called a *stack trace*: it identifies active calls leading to a failure. To see what a call actually involves, the instructor opens **`example.py`** in the demo project's source folder. The editor is in front of the terminal; the set results behind it aren't outputs from this file.

First distinguish a function from a call to it. When Python reaches the definition below, **`doNothing`** becomes a name for a function object. Its body does not execute at definition time. The three inspection expressions pass the *object* to another function. Only the fourth asks Python to execute its body.

**Annotated teaching version — guide-added comments; editor lines 1–7.**

```python
def doNothing():     # Define a function taking no arguments.
    pass             # Do nothing when called; reaching the end returns None.

print(f"dir(doNothing): {dir(doNothing)}\n")    # Display names on the function object.
print(f"type(doNothing): {type(doNothing)}\n")  # Display <class 'function'>.
print(f"id(doNothing): {id(doNothing)}\n")      # Display this object's identity number.
print(f"call of doNothing(): {doNothing()}\n")  # Call it; format None and print the line.
```

Independently running these displayed lines produced a list of attribute names (including **`__call__`** and **`__name__`**), **`<class 'function'>`**, a run-specific integer, and **`call of doNothing(): None`**, each followed by a blank line. The instructor describes the identity as “some place in memory”; the portable observation is that **`id(doNothing)`** identifies this object during its lifetime. An exact number from another run would tell you nothing about this one.

How many arguments go to the final **`print`**? Look for commas *at the level of the call*, not a comma inside quoted text, not one in a nested call, and not merely how many operations are visible. Here there is **one** argument: a formatted string. The early **`doNothing()`** before the colon is literal label text; only the one inside the replacement field is executed. A formatted string has an **`f`** prefix. Its unescaped braces delimit an expression whose result is formatted into the string; doubled braces would stand for literal braces. The **`\n`** adds a newline *inside* the string; the print function normally adds another at the end.

| Order in this line | What happens | Value passed on |
|---|---|---|
| 1 | Evaluate the expression in `{doNothing()}`; enter the no-argument function. | No arguments go into the inner call. |
| 2 | Execute `pass`, then reach the end of the body. | The inner call returns `None`; it prints nothing. |
| 3 | Format that value after the literal label and include the newline. | One completed string, not a string and a separate return value. |
| 4 | Call `print` with that string. | Visible line `call of doNothing(): None`, then a blank line. |

**Key items to scan**

- **`example.py`** — the older demo file opened to reason about calls; the set REPL is a separate window.
- **`doNothing`** — the function object. **`doNothing()`** — call it with zero arguments.
- **`pass`** — valid empty action; reaching the end returns **`None`** without printing.
- **`dir(doNothing)`** — available names include **`__call__`** and **`__name__`**; **`type(doNothing)`** returns **`<class 'function'>`**.
- **`id(doNothing)`** — an identity integer for this run, not a class-wide or permanent ID.
- **`f`**, **`{doNothing()}`**, **`\n`** — mark the formatted string, evaluate the actual call, and put a newline in the string.
- **`print`** — receives one completed string here; its visible line is **`call of doNothing(): None`**.

### 0:17:00 — Read the other definitions in the same editor window

The same file supplies useful contrasts. Read downward as Python would: each new **`def doNothing`** binds that name to a *new function*. Earlier calls already happened under the earlier definition; there is no choice among several versions based on how many arguments you supply. These are visible editor examples, not additional classroom shell runs.

**Annotated teaching version — guide-added comments.** The following excerpts retain the editor's unusual extra closing parentheses *inside printed labels*. Results are independently verified with Python 3.14; they are not terminal output visible behind the editor.

```python
def doNothing():        # Replace the earlier no-argument function.
    return None         # Return the same value as falling off the end.
print(f"call of doNothing()): {doNothing()}\n")  # Prints the label followed by None.

def douglasAdams():     # Define a separate no-argument function.
    return 42           # Hand the integer 42 back to the caller.
print(f"call of douglasAdams()): {douglasAdams()}\n")  # Prints the label followed by 42.
```

```python
def doNothing(milk):                      # Replace the zero-argument version.
    if milk == "fresh": return "spoiled"  # With "fresh", return the other string.
    else: return milk                     # Otherwise hand back the supplied value.
print(f"doNothing(\"fresh\")): {doNothing("fresh")}\n")  # Give one argument; result: spoiled.
```

```python
def doNothing(milk, flour):                  # Replace it again; now both arguments are required.
    if milk == "fresh":                      # Only fresh milk leads to the flour test.
        if flour != None: return "matzah"    # "white" differs from None: choose matzah.
        else: return milk                    # Fresh with None returns the original "fresh".
    else: return "spoiled"                   # Non-fresh bypasses the flour test.
print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", "white")}\n")  # Result: matzah.
```

These expressions contain two kinds of quotation marks: escaped quotes in the printed label, and quotes around actual string arguments inside the replacement field. Likewise, the last line passes two arguments to the *inner* function but still only one completed string to the *outer* print. Independently verified output from these four print calls, with the extra blank lines omitted here:

```text
call of doNothing()): None
call of douglasAdams()): 42
doNothing("fresh")): spoiled
doNothing("fresh", "white"): matzah
```

The illustrated two-argument call satisfies both tests. To separate the other branches, a guide-created check of **`doNothing("fresh", None)`** returned **`"fresh"`**; **`doNothing("old", "white")`** returned **`"spoiled"`** without testing the flour. The inner test compares specifically against **`None`**, not against every value that might count as false: fresh milk and an empty string for flour still returned **`"matzah"`** in the independent check.

**Key items to scan**

- **`def doNothing`** — a later definition replaces the name for subsequent calls; it does not change a return value from an earlier call.
- **`return None`** — explicitly returns the same value that the first function returned implicitly.
- **`douglasAdams()`** — takes no arguments and returns **`42`**; the outer print displays it.
- **`milk == "fresh"`** — one-argument version returns **`"spoiled"`** for fresh milk, otherwise the original input.
- **`flour != None`** — two-argument version returns **`"matzah"`** if milk is fresh and flour is not **`None`**.
- **`doNothing("fresh", None)`** — returns **`"fresh"`**; **`doNothing("old", "white")`** returns **`"spoiled"`** under the outer else.

## 0:19:54 — Push and pop from the same end

A student pictures a stack of papers: you put a sheet on top and take a sheet from the top. Pulling from the middle would be more like Jenga. Whether a stack has zero, one, or a million entries, its permitted access end stays the same. *Push* puts an item on top; *pop* takes that top item away. This is *last in, first out* (LIFO). A set's pop shares the idea of removing and returning an item, **not** the stack's guarantee of which item comes next.

| Paper action (illustrative) | Stack, bottom → top | Item returned |
|---|---|---|
| Push sheet A, then push sheet B | A, B | Neither is removed |
| Pop once | A | B, the most recently pushed sheet |
| Pop again | Empty | A |

Now change what the stack holds in your mental picture: instead of sheets, think of calls that started but have not finished. The *call stack* keeps the current function's work associated with the work that must resume afterward. The traceback from the rejected set insertion gives this an immediate use: it points back to the call at which execution failed. The next question the class poses is how a supplied argument reaches a function and how its result gets back to the waiting caller.

**Key items to scan**

- **Stack** — a collection accessed at one end; the top is the only removal point in the paper analogy.
- **Push** — put the new sheet on top; **pop** — remove and return that top sheet.
- **LIFO** — with A then B pushed, B comes off before A. A set makes no corresponding order promise.
- **Call stack** — the current chain of unfinished calls; a **stack trace** or Python **traceback** helps locate an error in that chain.

## 0:22:00 — How does `"fresh"` get into `milk`, and where does the answer go?

In the editor, the instructor selects **`doNothing("fresh")`** inside a longer print statement. There are two different pieces of text on that line: a label for a reader, and a call that Python must actually evaluate. The outer print cannot display the answer until the inner call finishes. In the one-parameter version, **`milk`** becomes a local name for the string passed by the caller. Compare the word to **`"fresh"`**: it matches, so the function hands **`"spoiled"`** back to the waiting expression. Returning that string is not the same as printing it.

Annotated teaching version — guide-added comments; editor lines 17–20, frames 0:23:00–0:25:00:

```python
def doNothing(milk):                  # On a call, milk names the supplied argument.
    if milk == "fresh": return "spoiled"  # The actual call takes this branch and stops here.
    else: return milk                 # Otherwise hand back the original argument.
print(f"doNothing(\"fresh\")): {doNothing("fresh")}\n")  # Insert the result, then print.
```

Independent execution of these screen lines (Python 3.14; not a classroom terminal run):

```text
doNothing("fresh")): spoiled
```

The extra closing parenthesis before the colon is part of the printed label, not part of the call; the trailing newline in the string adds a blank line. The backslashes in the label put quotation marks in the text. The braces, by contrast, enclose a Python expression that runs.

| Moment | What is active? | What is waiting or returned? |
|---|---|---|
| Reach the print expression | Module evaluates the replacement field in the formatted string. | Formatting and printing must wait. |
| Enter the call | The argument is `"fresh"`; local parameter `milk` refers to it. | The caller's place is retained. |
| Take the matching branch | `return "spoiled"` finishes this call immediately. | `"spoiled"` becomes the value of the call expression. |
| Resume at the caller | Formatting inserts the returned string; the outer print displays the line. | The module can proceed to the next statement. |

The instructor's **call stack** model is a pile of papers: push new work on top; pop the most recent work when it finishes. The spoken walkthrough describes pushing the argument and the address to return to, transferring control to the function, picking up its parameter, then returning a value to the suspended expression. Distinguish *where to start the called function* from *where to resume its caller*. Python keeps track of active calls for you; this model explains that control flow without requiring a particular physical push/pop sequence. The function object's identity is not the caller's return location.

**Key items to scan**

- **`doNothing("fresh")`** — calls the current one-parameter function; the label alone does not call it.
- **`milk`** — local parameter that refers to the argument **`"fresh"`** during this call.
- **`return "spoiled"`** — delivers **`"spoiled"`** to the caller; the surrounding print displays it.
- **`{...}`** in the formatted string — evaluate this expression before displaying its value.
- **Call stack** — records the caller waiting underneath the active call; a return resumes the waiting caller.

### A later screen reveals one more version: omit the flour

The earlier two-argument function required both inputs. Scrolling reveals two separate changes: the second argument becomes optional, and the inner alternative returns a different string. The following annotated teaching version includes guide-added comments.

```python
def doNothing(milk, flour=None):         # New definition: omit flour to use None.
    if milk == "fresh":                  # Only fresh milk enters the inner test.
        if flour != None: return "matzah"  # Supplying "white" still returns "matzah".
        else: return "spoiled"           # Changed: fresh milk with no flour now returns "spoiled".
    else: return "spoiled"               # Non-fresh milk also returns "spoiled".
```

With guide-selected inputs, the required-flour version returns `"fresh"` for `("fresh", None)`, while the defaulted version returns `"spoiled"` for `("fresh",)` and `"matzah"` for `("fresh", "white")`. Those are returned values, not messages the definitions themselves print. The exact driver below this last definition is vertically clipped in the 0:28:00–0:29:00 frames.

**Key items to scan**

- **`flour=None`** — the one-argument call is now legal; no flour is supplied.
- **`else: return "spoiled"`** — with fresh milk and no flour, the new version returns `"spoiled"`, not the earlier `"fresh"`.

## 0:25:00 — Read a traceback from the failure back to its caller

Suppose the module calls a function, which calls another function. The module remains paused while the first function waits on the second. A normal return resumes the most recent caller first. If a call raises an exception that no one handles, the pending calls unwind instead. A **traceback** records the path of calls to the failure; it can show one Python location, as here, or many. It does not require a hand-written stack in the program.

At 0:26:00–0:27:00 the instructor brings earlier interactive output to the front, not a failure of the milk example. The input was **`f.add([2,4,6])`**: the brackets created *one list*, not three separate set elements. That type of object is unhashable and cannot be an element of a set. Selected observed lines:

```text
>>> f.add([2,4,6])
Traceback (most recent call last):
  File "<python-input-13>", line 1, in <module>
    f.add([2,4,6])
TypeError: cannot use 'list' as a set element (unhashable type: 'list')
```

Read the bottom line for the exception and the last listed source location for the operation that failed. Then move up through earlier calls to find who supplied the bad argument. The point of failure need not be where the mistake originated. The instructor contrasted this order with stack displays one might encounter in other language tools; read the actual header rather than assuming all tools display frames alike. A possible future stack challenge was mentioned, not assigned here with a specification.

**Key items to scan**

- **Traceback** — shows the path to the error; **`most recent call last`** tells you where to start reading.
- **`f.add([2,4,6])`** — tries to insert a single list, not `2`, `4`, and `6` individually.
- **`TypeError`** — the list is an unsuitable set element; look at both the failing operation and where the argument came from.

## 0:28:00 — The set is not a stack: keep the return value separate from the mutation

Why was the class discussing stacks? The name **`pop`** prompted the detour. A stack removes its most recently pushed item; a set contains unique elements but offers no corresponding promise about which one will come out. In the earlier console history, adding three integers individually produced `{2, 4, 6}`. Here is the contrast the instructor returns to, with the REPL inputs annotated (0:26:00 and 0:30:00). Bare expressions display their values at this prompt except for `None`.

```python
g = f.remove(2)     # Remove the named member 2; bind g to None, not to 2.
f                   # Displays {4, 6} in the captured run.
g                   # No displayed line: the result is None.
type(g)             # Displays <class 'NoneType'>.
h = f.pop()         # Remove one arbitrary member and save it in h; here it was 4.
h                   # Displays 4 in this run.
f                   # Displays {6}; the set itself has changed.
```

The spoken return-address analogy adds that a call hands back *one object*; the caller may store it, use it immediately, or discard it. That object can itself contain multiple values. No explicit argument appears inside **`f.pop()`**, but the method operates on **`f`**. The screen resolves two verbal slips: **`h`**, not `f`, holds the returned integer; in this particular call it was *saved*, not thrown away. Neither the pop operation nor its assignment prints the integer. It appears when `h` is evaluated on the next prompt.

**Key items to scan**

- **`f`** — the same set is changed; after the remove it contains `4` and `6`.
- **`g = f.remove(2)`** — selects a particular member to remove; `g` is `None`, not the removed member.
- **`pop`** / **`f.pop()`** — chooses an arbitrary remaining member and returns it; do not predict a last-in/first-out order.
- **`h`** — saved the returned `4` in the captured run; only `6` remained in the set.

## 0:31:00 — Make an empty dictionary; distinguish a key from a position

The fourth collection introduced after lists, tuples and sets is a **dictionary**. A list answers “what is at position 2?” A dictionary answers “what value did we associate with this key?” A learner suggests the keys on a keyboard as an analogy: an input leads to an output. The useful part is the association. The dictionary need not compute an answer; it can simply look up a value that someone stored earlier.

Annotated teaching version — guide-added comments; observed interactive setup, 0:32:00–0:35:00:

```python
i = dict()          # Make a new empty dictionary; i names that object.
type(i)             # The prompt displays <class 'dict'>.
dir(i)              # Ask which attribute names this particular object offers.
i["one"] = 1        # Store integer 1 under the string key "one". No output.
```

The method inventory includes double-underscore names and the ordinary names `clear`, `copy`, `fromkeys`, `get`, `items`, `keys`, `pop`, `popitem`, `setdefault`, `update`, `values`. Seeing a name in **`dir(i)`** is evidence that it exists, not that the class tried it. One question was whether **`fromkeys`** takes keys out of the dictionary. The instructor left that question open and demonstrated **`keys()`** later. To settle the distinction, here is a *separate, independently checked example* using the two keys the class would shortly store:

```python
i = {"one": 1, 2: 2}       # Guide fixture: recreate the later two-entry mapping.
print(i.keys())             # Show existing keys: dict_keys(['one', 2]).
print(dict.fromkeys(i, 0))  # Make a NEW dictionary: {'one': 0, 2: 0}; i is unchanged.
```

**Key items to scan**

- **Dictionary** / **`dict()`** — a new empty mapping; entries are stored by key, not by numbered position.
- **`type(i)`** — displays **`<class 'dict'>`**; `i` is an instance, not the class name.
- **`dir(i)`** — lists available attribute names; it neither calls the methods nor lists the keys of the stored data.
- **`i["one"] = 1`** — associates the integer `1` with the string `"one"`.
- **`keys()`** — view of the keys already present. **`fromkeys`** — constructs a new dictionary from supplied keys, with a common initial value.

### 0:34:00 — Why `j["2"]` fails but `i["one"]` works

Same brackets, different object. **`j`** names a list of three integers. Its positions count from zero. The third value is at integer position two; putting quotes around the digit changes its type, not just its appearance. The instructor notes that slices are also allowed, but postpones teaching them. Compare the observed prompt operations below (0:35:00–0:39:00). Enter the failing lookup on its *own* prompt so the investigation can continue afterward.

```python
j=[5,7,9]          # Make a new list: position 0 holds 5, position 1 holds 7, position 2 holds 9.
j[2]               # Display 9: integer position 2 selects the third element.
j["2"]             # Raise TypeError: the string "2" is not a list position.
```

Observed final error line: `TypeError: list indices must be integers or slices, not str`.

```python
i[2] = 2           # Same dictionary as the earlier i["one"] = 1; add integer key 2.
i                  # Displays {'one': 1, 2: 2}; two entries, not three positions.
i["one"]           # Look up the string key; displays 1.
```

Nothing requires two intervening items before a dictionary can use integer key `2`. The number is a label, not an offset. The instructor says lists often suit integer positions, with exceptions; choose a dictionary if the integers name things rather than their places in a sequence.

The instructor describes using a **hash** to help find the entry. One qualification matters: Python doesn't discard the original key or guarantee a unique hash for every possible key. It uses hashing to narrow the search and key equality to distinguish entries. You can recover the original keys from this very dictionary. An independent check also found that `hash(-1)` and `hash(-2)` both returned `-2` on the test interpreter, yet `{-1: 'first', -2: 'second'}` kept both entries. Keys need a stable hash while stored. The earlier refusal to accept a list as a set element illustrates the same hashability constraint.

**Key items to scan**

- **`j`** — refers to `[5, 7, 9]`; its integer positions are `0`, `1`, `2`.
- **`j[2]`** — retrieves `9`. **`j["2"]`** — raises a **`TypeError`** because a string is neither an integer position nor a slice.
- **`i[2] = 2`** — stores the value `2` under the integer key `2`; it does not refer to a third entry.
- **`i["one"]`** — retrieves the original integer `1` by its string key.
- **Hash** — lookup aid, not a substitute for the actual key and not guaranteed unique; unequal keys can coexist despite a collision.

## 0:38:00 — Ask one dictionary three different questions

To get a single value, supply a key. To inspect *which keys*, *which values*, or *which pairings* are present, ask the dictionary for the corresponding view. A **view** supplies a way to look at the contents of the dictionary itself; it is not a separately built list. The shell displays wrappers named `dict_keys`, `dict_values`, and `dict_items`. Read those outer names as carefully as the brackets inside them.

Annotated teaching version — guide-added comments; actual REPL results, 0:39:00–0:41:00:

```python
i.keys()           # dict_keys(['one', 2]): keys to use for lookups.
i.values()         # dict_values([1, 2]): associated values, without the keys.
dict.items()       # Failed attempt on the CLASS: no dictionary instance supplied.
dict.item()        # Another attempt; no singular item method on dict.
i.items()          # Correct: dict_items([('one', 1), (2, 2)]).
```

The class initially tries changing the method name. Two distinct questions diagnose the failures:

| Input at its own prompt | Observed result | How to interpret it |
|---|---|---|
| `dict.items()` | `TypeError: unbound method dict.items() needs an argument` | The plural method exists, but *whose* entries should it show? No instance was supplied. |
| `dict.item()` | `AttributeError: type object 'dict' has no attribute 'item'. Did you mean: 'items'?` | The singular name isn't on the class. Changing the spelling hasn't provided a receiver either. |
| `i.items()` | `dict_items([('one', 1), (2, 2)])` | Calling the plural method on the actual dictionary supplies the object whose entries are wanted. |

Within the last result, **`('one', 1)`** is a two-element **tuple**: first the key, then its value. The next pair is **`(2, 2)`**. The discussion calls this a “list of tuples,” which describes the visual grouping but not the outer type: it is a live items view *yielding* tuples. Each pair is immutable: you cannot replace its first or second slot to edit the dictionary. You *can* change a value by assigning through its key, as the instructor does next. Don't conclude that a value must be immutable just because its pair is a tuple.

**Key items to scan**

- **`i.keys()`** — view of the two keys, `'one'` and `2`; **`i.values()`** — view of the associated values, `1` and `2`.
- **`dict.items()`** — missing a dictionary instance; an existing method called without the object it needs raises **`TypeError`**.
- **`dict.item()`** — singular name absent, so attribute lookup raises **`AttributeError`**.
- **`i.items()`** — on the proper instance, returns a **view**, not a copied list.
- **Tuple** — **`('one', 1)`** and **`(2, 2)`** each retain the key–value order; the slots in the returned pair cannot be reassigned.

## 0:42:00 — Change `i`, inspect `k`: what did the saved view remember?

The first `i.items()` call displayed a result without assigning it to a name. Now the instructor saves the view as **`k`**. Ask what the *same* `k` will show after changing `i`, without calling the method again or assigning anything else to `k`.

Annotated teaching version — guide-added comments; REPL state at 0:43:00–0:44:00:

```python
k = i.items()      # Keep a view onto the dictionary, not a copy of the two pairs.
k                  # Before: dict_items([('one', 1), (2, 2)]).
i[2] = 37          # Replace only the value belonging to integer key 2; no output.
k                  # After:  dict_items([('one', 1), (2, 37)]).
```

| Step | Entry for key `2` in `i` | What's visible through `k`? |
|---|---|---|
| Save and display the view | `2` | The pair is `(2, 2)`. |
| Assign through key `2` | `37` | No new view was assigned and nothing was displayed by the assignment. |
| Display `k` again | Still `37` | The pair is now `(2, 37)`; `('one', 1)` is unchanged. |

A student correctly points out that changing the dictionary changes what the view will show. This is not a retroactive change to the text already printed in the terminal; the two display lines remain on screen for comparison. The spoken recap abbreviates the number, but the command and the later display both show `37`.

The contrast with a “list” is clearest if we explicitly *copy* the pairs. Two names for the same ordinary list can see one another's mutations, too; the difference here is a live view versus a snapshot, not that lists never reflect changes. Independently verified comparison (guide-created, not a classroom run):

```python
i = {"one": 1, 2: 2}        # Reset: make a NEW two-entry dictionary for this example.
k = i.items()               # A new live view onto this new dictionary.
snapshot = list(k)          # Collect its current pair tuples in a separate list.
i[2] = 37                   # Change i; do not assign to k or to snapshot.
print(k)                    # Shows the replacement, (2, 37).
print(snapshot)             # Still has (2, 2), the pair collected earlier.
```

```text
dict_items([('one', 1), (2, 37)])
[('one', 1), (2, 2)]
```

**Key items to scan**

- **`k`** / **`k = i.items()`** — keep a live view of this specific dictionary; no repeat call is necessary.
- **`i[2] = 37`** — reassign the value for an existing key; `i` still has two entries.
- **`list(k)`** — collect the pairs now. In this example the second pair in the separate list stays `(2, 2)`.

### 0:44:00 — Syntax to remember from these examples

| Pattern | Meaning here |
|---|---|
| `name` versus `name(...)` | The first can refer to the function object; the second evaluates arguments and calls it. |
| `return value` | End the current call and make its value available at the waiting call site; this does not itself print. |
| `f"label: {expression}\n"` | The label is text, the braces request evaluation, and the escape adds a newline. |
| `j[2]` versus `i[2]` | On this list, select the third element; on this dictionary, look up the integer key `2`. |
| `i[key] = value` | Add an association or replace its value; use the existing key `2` to replace `2` with `37`. |
| `i.items()` versus `list(i.items())` | Keep a view of current pairs, or collect the pairs currently present into a separate list. |

At 0:44:00 the instructor begins a different construction, choosing the name `m` and proposing color words as keys. The next demonstration asks what curly braces, quotes and colons contribute when the pairs are written out directly rather than added one at a time.

## 0:45:00 — Write the pairs directly: why quoting `red` matters

The earlier items view still shows the change from 2 to 37. Now the instructor makes a *different* dictionary, with color names for keys. Instead of starting empty and assigning one key at a time, write both pairs between braces. The colon joins each key to its value; the comma separates one pair from the next. The prompt returns without printing anything: assigning a dictionary and asking the shell to display it are different actions.

Annotated teaching version — guide-added comments; successful assignment at 0:46:00 and display at 0:50:00:

```python
m = {"red": "apple", "blue":"sky"}  # Create a new dictionary and name it m.
m                                  # At the Python prompt, display its representation.
```

Observed display:

```text
{'red': 'apple', 'blue': 'sky'}
```

A student tried removing all the quotes. The instructor changes just one thing instead: remove the quotes around the first key and leave the other strings alone. In **`"red"`**, the characters themselves are the key. In **`red`**, Python must first find an object already named red. No such name exists in this session. The braces and colon are fine; the lookup fails *before* the new dictionary can be assigned. The successful mapping in **`m`** survives the failed experiment with **`n`**.

```python
n = {red: "apple", "blue":"sky"}    # Try to evaluate the name red, not the string "red".
```

Observed at 0:49:00–0:50:00, including a repeat attempt:

```text
NameError: name 'red' is not defined
```

**Key items to scan**

- **`m`** — the name assigned to the new two-pair dictionary.
- **`{...}`**, **`:`**, **`,`** — enclose the dictionary, connect key to value, and separate pairs, respectively.
- **`"red"`** — literal text that maps to **`"apple"`**; **`"blue"`** maps to **`"sky"`**.
- **`red`** — an identifier; Python looks it up rather than using the letters as data.
- **`n`** — the proposed destination; this failed assignment never places a new dictionary there.
- **`NameError`** — the expression is valid Python syntax, but a required name is missing.

## 0:46:30 — From a dictionary to JSON text and back

Why spend time on those braces? The instructor recognizes a notation used to send data from one place to another: **JSON** (JavaScript Object Notation). Its two *container* forms are objects, which associate names with values, and arrays, which keep values in order. Objects can contain other objects or arrays. A JSON object written as text may look like the Python dictionary above, but a piece of text and a dictionary already in memory are different things. The detour through the missing name sets up the next question: is a name available just because Python ships a library with that name?

At the Python prompt, **`json`** initially raises **`NameError`** with the hint `Did you forget to import 'json'?`. After **`import json`**, the same name displays a module and its installation path. A module groups related definitions behind a name. The class checks the data and then inspects the tool that will read it.

Annotated teaching version — guide-added comments; REPL displays at 0:50:00–0:51:00 and 0:55:00:

```python
import json     # Make the standard-library module available by this name.
json            # Displays a module and the location Python loaded it from.
type(m)         # Displays <class 'dict'>: m is already Python data.
dir(json)       # Lists names available on the module; look for dumps and loads.
```

The **`s`** is a useful direction cue: **`json.dumps`** takes Python data and produces JSON text; **`json.loads`** takes JSON text and produces Python data. The instructor points out both names but demonstrates loading. This short round trip separately checks the other direction with the same two pairs. It is not classroom output.

```python
text = json.dumps(m)             # New string; m is still the original dictionary.
print(text)                      # Print JSON text, including the double quotes.
print(json.loads(text) == m)     # Read it back; compare the keys and values.
```

Independently verified output:

```text
{"red": "apple", "blue": "sky"}
True
```

**Key items to scan**

- **JSON** — transferable text notation; objects use named members, arrays hold ordered elements.
- **`json`** — the module, not a particular JSON document or decoded dictionary.
- **`NameError`** — before import, this interactive session has no binding for the name **`json`**.
- **`import json`** — brings the module name into this session; evaluating the name now identifies its origin.
- **`type(m)`** — confirms that the original color mapping is a Python dictionary.
- **`dir(json)`** — discovers module names, including the two string conversion functions.
- **`s`**, **`json.dumps`**, **`json.loads`** — dump *to* a string or load *from* a string; the two operations run in opposite directions.

### 0:51:00 — Which parser sees the quotation marks first?

The instructor copies the valid dictionary-shaped text into a call to **`json.loads`**. The caller now needs to pass *one string*, not a dictionary literal. First attempt, as entered (deliberately invalid Python; note is guide-added):

```python
p = json.loads("{"red": "apple", "blue":"sky"}")  # Inner double quote ends the Python string early.
```

Observed at 0:53:00:

```text
SyntaxError: invalid syntax. Is this intended to be part of the string?
```

Read from left to right as Python must. The first double quote opens a string. The next character, the opening brace, is ordinary text inside it. The double quote immediately before **`red`** now *closes* that string. What follows does not form a valid Python expression; the JSON function never receives an argument. Deleting all the inner quotes, as a student suggests, would lose part of the JSON notation. The repair is to change only the *outer* delimiters.

Annotated teaching version — guide-added comments; corrected call at 0:54:00, displays at 0:55:00:

```python
p = json.loads('{"red": "apple", "blue":"sky"}')  # One Python string; JSON receives the inner double quotes.
p                                                # Display the new Python dictionary.
type(p)                                          # Ask what the decoded data is.
type(json)                                       # Student question: what is the library itself?
```

Observed displays, in order:

```text
{'red': 'apple', 'blue': 'sky'}
<class 'dict'>
<class 'module'>
```

| Stage | What is being read? | What results? |
|---|---|---|
| Python reads the corrected argument | Outer single quotes delimit one string. | The string contains braces, colons, commas, and double-quoted names and values. The outer quotes are not part of its contents. |
| The JSON decoder reads that string | It finds an object with the two color members. | It returns a Python dictionary; the assignment names it **`p`** without printing. |
| The prompt evaluates the new name | It asks Python to display the dictionary. | Python shows single quotes around the strings. The quote style of that display does not change either pair or prescribe JSON syntax. |

The instructor's suggestion to mix quote styles is about protecting the *Python* string boundary. The inner JSON names and string values still need double quotes. Compare the two failures: missing **`red`** in a valid Python expression previously gave a name lookup error; mismatched string delimiters here give a **`SyntaxError`** before any lookup inside the JSON decoder. This is why it helps to ask which language layer is reading a given character.

**Key items to scan**

- **`json.loads`** — consumes correctly formed JSON text; it is not called at all in the first attempt.
- **`red`** — in the faulty call these letters fall *outside* the prematurely closed Python string; this is not the earlier missing-key-name experiment.
- **`SyntaxError`** — Python cannot parse the input line, so no result is assigned.
- **`p`** — the newly decoded mapping, separate from the earlier literal dictionary.
- **`type(p)`** — the data is a **`dict`**; **`type(json)`** — the library is a **`module`**.

## 0:55:00 — A module, a class, and an apple are three different things

The REPL experiment ends: the terminal shows **`exit`** followed by the PowerShell prompt. The instructor notes that the bare form worked in this interpreter; **`exit()`** is the call form. Back in the project, **`item.py`** supplies a module, while **`Item`** names a class defined inside it. Its instances are the individual items that will be linked. Unlike the interactive prompt, a script does not automatically display the value of an expression. The driver therefore uses explicit prints to compare identities.

Annotated teaching version — guide-added comments; successful driver prefix at 0:59:00:

```python
from item import Item       # Bind the class named Item in this driver.
thing = Item                # A second name for the class; no item was created.
print(id(Item))             # Print the identity of the class itself.
print(id(thing))            # Print that very same identity through its second name.
fruit = Item("apple")        # Call the class; create a distinct instance for apple.
print(id(fruit))            # Print the identity of the apple instance, not of the class.
```

Observed command and first three output lines (fruit traversal continues below):

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
1499181725904
1499181725904
1499185972624
```

Predict the *relationship*, not the digits. The first two names refer to one live object, the class; the third refers to a different live object, the apple item. Assigning **`thing = Item`** does not copy the class or call it. The parentheses in **`Item("apple")`** are the change that matters. The identity numbers belong to this one process; another run need not reuse them.

**Key items to scan**

- **`exit`**, **`exit()`** — leave the interactive Python session; the subsequent `py` command belongs to PowerShell.
- **`item.py`** — the module file; **`Item`** — the class imported from it.
- **`from item import Item`** — makes the uppercase class name available directly in the driver.
- **`thing = Item`** — creates another reference to the class, not another fruit.
- **`Item("apple")`** — creates an instance initialized with the apple string.
- **`id`**, **`print`** — obtain an identity, then display it; only the equality pattern is predictable here.

### 0:58:00 — Read the rest of the successful driver: walk the links in both directions

The identity check sits in an existing fruit driver. Its five remaining output lines come from following links, not from the constructor or the identity function. After starting with apple, each assignment replaces the name **`fruit`** with the item returned by this project's **`append`**. By the time peach is added, that name is at the last item. The snippets below follow the actual driver; this trace uses the visible calls and the successful output, without assuming how the hidden method body was implemented.

Annotated teaching version — guide-added comments; follows the apple construction above:

```python
fruit = fruit.append("banana")  # Add banana after apple; keep the returned banana item.
fruit = fruit.append("peach")   # Add peach after banana; fruit now names the peach end.
iter = fruit.prev.prev          # From peach, go back through banana to apple.
print( iter.object )            # Print apple, the value stored at this item.
iter = iter.next                # Move the separate position name forward to banana.
print( iter.object )            # Print banana.
iter = iter.next                # Move forward to peach.
print( iter.object )            # Print peach.
iter = iter.prev                # Reverse direction; return to banana.
print( iter.object )            # Print banana again.
iter = iter.prev                # Go back once more, to apple.
print( iter.object )            # Print apple again.
```

| Driver action | Position used for printing | Observed line (0:59:00) |
|---|---|---|
| Go back twice from the item named by `fruit`. | apple | `apple` |
| Follow the next link. | banana | `banana` |
| Follow the next link again. | peach | `peach` |
| Follow the previous link. | banana | `banana` |
| Follow the previous link again. | apple | `apple` |

Moving the position name does not move an item within the chain or change its stored fruit. Notice what the driver does *not* do: it never asks for the item before the first apple or after the last peach. Also, **`iter`** here is a variable chosen by the instructor, not a call to Python's built-in iterator function. Reusing that name hides the built-in under that spelling in this driver.

**Key items to scan**

- **`fruit`** — starts at apple, is rebound to banana, then to peach by the two assignments.
- **`append`** — this custom method gives the driver the added item to use in the next assignment; do not generalize its return value to every similarly named method.
- **`fruit.prev.prev`** — follows two backward references before the first print, so output starts at apple rather than peach.
- **`iter`** — a separate current-position name; changing it leaves the chain in place.
- **`iter.next`**, **`iter.prev`** — the forward and backward links used for the five-stop walk.
- **`iter.object`** — the stored value passed to each print, not the entire linked item.

## 1:00:00 — Why neither `id(item)` nor `type(item)` can work here

A student predicts the next failure: the driver imported the uppercase class, not a lowercase variable. To test that distinction the instructor adds a print on line 2, before any fruit is made. Then the class tries a different inspection function. These are two separate versions, not two consecutive lines of the same script.

Annotated teaching versions — guide-added comments:

```python
from item import Item      # Bind Item, not item, as a driver name.
print(id(item))            # First revision: must find item before id can run.
```

```python
from item import Item      # The same import in the next run.
print(type(item))          # Second revision: changing the function cannot supply its missing argument.
```

Observed at 1:01:00: PowerShell runs **`py .\test\fruit.py`** for each revision. Each traceback points to its own line 2 and ends:

```text
NameError: name 'item' is not defined. Did you mean: 'Item'?
```

Python must resolve **`item`** before it calls either inspection function, and before **`print`** receives anything. Neither failed run emits identities or fruit. The numbers and fruit above the tracebacks belong to the earlier successful run. The traceback's **`<module>`** identifies top-level execution in the driver; it does not mean that an object named **`item`** is available to the expression. Contrast this with the earlier whole-module import of **`json`**, after which checking the module's type succeeded.

**Key items to scan**

- **`item`** — module name in the import statement, but not a lowercase local name bound by `from item import Item`.
- **`id(item)`** — first attempt; lookup fails before an identity can be obtained.
- **`type(item)`** — second attempt; lookup fails before a type can be obtained.
- **`print`** — never runs at line 2, because evaluating its argument failed.
- **`py .\test\fruit.py`** — the PowerShell command used for the separate runs.
- **`<module>`** — traceback context, not an automatic binding of the module filename.
- **`json`** — unlike lowercase `item` in this driver, that module name was bound by an explicit whole-module import.

### 1:01:00 — Naming conveys intent; spelling controls lookup

The failure gives the instructor a reason to distinguish a style convention from a language rule. Python *does* distinguish uppercase and lowercase names. The instructor *recommends* lowercase module filenames such as **`fruit.py`** and **`item.py`**, alluding to package tooling; the demonstration does not establish a universal packaging restriction. For classes the instructor uses **Pascal case**: begin each word, including the first, with a capital letter. For identifiers inside a class, the instructor favors **camel case**: begin lowercase and capitalize later words. The capital in the middle looks like a camel's hump. The instructor connects the name Pascal to the language and its mathematician namesake.

In **snake case**, underscores mark word breaks where a space cannot go. The class briefly distinguishes that from the doubled underscores students remembered. During the illustration, the import line actually shows **`Item_one`** at 1:03:00 and **`itemOne`** at 1:04:00. The former is a transitional mixed spelling, *not* the all-lowercase snake-case example described aloud. These were naming illustrations, not successful imports of new classes.

**Key items to scan**

- **`fruit.py`**, **`item.py`** — lowercase module filenames in this project; a naming choice, not the reason the driver's lowercase lookup failed.
- **Pascal case** — initial capital plus word capitals; `ItemOne` illustrates the spoken class-naming pattern.
- **camel case** — lowercase start, later capital; **`itemOne`** is the edited example with the “hump.”
- **snake case** — underscores between words, as in the illustrative `item_one`; unlike a special name such as `__init__`, it needs no paired double underscores.
- **`Item_one`** — actual mixed-case intermediate editor text; do not mistake it for a newly defined class.

## 1:04:45 — Constructing an item does not link it to a neighbor

Back in **`item.py`**, the instructor distinguishes making an item from connecting items. Calling the class could already make an instance before this class supplied its own initializer. What the new initializer adds is a convenient way to put a different value into each new item. The instructor refers back to **`__new__`** and **`__init__`**: creation and initialization are related steps, not two separate items. The code actually visible here customizes initialization, not allocation.

Annotated teaching version — guide-added comments; class excerpt at 1:05:00–1:07:00; two calls below are an independently tested illustration of this initializer alone:

```python
class Item:                          # Define the class, not an individual fruit.
    object = None                    # Class default: no stored value yet.
    next = None                      # Class default: no forward neighbor yet.
    prev = None                      # Class default: no backward neighbor yet.

    def __init__(self, obj = None):    # Python supplies the new item; caller may omit obj.
        self.object = obj             # Store this call's value on this particular item.

fruit = Item("apple")                 # Create an apple item; its object is "apple".
other = Item()                        # Create another item; no argument means its object is None.
print(fruit.object, other.object)     # Read each stored value independently.
print(other.next, other.prev)         # No links were set during initialization.
```

Independently verified values (initializer isolated from the longer class):

```text
apple None
None None
```

The name **`obj`** belongs to one initializer call; **`self.object`** belongs to the item being initialized and remains readable afterward. The similar names prompted the instructor to remark that the naming could be clearer. Neither link is assigned inside this initializer. Making a dozen such items would not, by itself, tell any one item which of the other eleven is its neighbor.

To motivate the next method, the instructor asks where you would add another entry on a grocery list: usually at the bottom. The visible header is **`def append(self, obj):`**. Unlike the initializer, it offers no default for the new value. In the ordinary instance call **`fruit.append("banana")`**, the existing item supplies **`self`**; the caller supplies banana for **`obj`**. That is the answer to the instructor's question at 1:07:00: in this calling pattern there must already be an item on which to call the method. It is not enough to have several disconnected items; the method will have to establish how they lead to one another.

**Key items to scan**

- **`item.py`** — defines the class with a value and two neighbor references.
- **`__new__`**, **`__init__`** — creation versus initialization; only the custom initializer is shown here.
- **`obj`** — input value, optional in the initializer because it defaults to `None`.
- **`self.object`** — this instance's stored value; initializing another object does not overwrite the first apple.
- **`next`**, **`prev`** — initially `None` through class defaults, so construction alone establishes no neighbor.
- **`def append(self, obj):`** — instance-method header requiring a new value in this version; the visible excerpt does not show its body.
- **`fruit.append("banana")`** — call on the already existing fruit item with one explicit argument; **`self`** receives the existing item.

## 1:08:00 — What must exist before you can append?

To attach banana to apple, you need something to attach it **to**. In this design, that something is an **`Item`** instance representing apple. The class itself is also an object in Python, but the class describes the nodes; it isn't the particular apple node whose link needs changing. Calling **`fruit.append("banana")`** supplies both pieces: the object found under **`fruit`** before the dot, and the string in parentheses. This method attaches a node directly after its receiver; it does not search for the end of a list. The driver deliberately calls it on the current last node.

**Annotated teaching version — guide-added comments.** The class as displayed at 1:10:00–1:13:00, before the parameter renaming:

```python
class Item:                         # Define the class, not an apple or banana yet.
    object = None                   # Class default: no stored value yet.
    next = None                     # No forward neighbor until a link is assigned.
    prev = None                     # No backward neighbor until a link is assigned.

    def __init__(self, obj = None):  # Initialize this new node; the value is optional here.
        self.object = obj           # Item("apple") stores "apple" on this node.

    def append(self, obj):          # When apple receives the call, obj is "banana".
        newItem = Item(obj)         # Create a different node, initialized with "banana".
        newItem.prev = self         # The new banana node now points back to apple.
        self.next = newItem         # The existing apple node now points forward to banana.
        return newItem              # Give back the banana node, not the string "banana".
```

The two assignments make the links. The return does a different job: it lets the caller keep the new node as its starting point for the next operation. The local name **`newItem`** goes out of scope when this call finishes; the banana object does not disappear with its name. Apple's forward link already refers to it, and the caller can also retain the returned reference. This is the useful distinction in the discussion of lifetime and garbage collection: losing a local name does not reclaim an object that is still reachable.

**Key items to scan**

- **`Item`** — the class shared by apple and banana; each node is a separate instance.
- **`fruit`** — the caller's name for one node, initially apple in this example.
- **`fruit.append("banana")`** — uses apple as the receiver and supplies banana as the new stored value.
- **`self`** — the particular instance a method is working on, not the entire class.
- **`object`**, **`next`**, **`prev`** — the stored value, forward link, and backward link. The class provides `None` defaults; assignments through an instance set attributes on that instance.
- **`newItem`** — a temporary local name for a node that remains reachable after the method exits.
- **`return newItem`** — returns the new node without printing it or rebinding the caller's name by itself.

### 1:11:15 — Why can a method call its own class, and which names must match?

The expression **`Item(obj)`** is inside the definition of **`Item`**. Why isn't that too early to use the name? Defining the method records what to do later; it does not construct a node at definition time. When the driver finally calls **`append`**, the class already exists. Creating the new node initializes it; that initialization stores the value and does **not** call append again. Nor is the argument necessarily **`None`**: that is the initializer's default if the caller omits the value, not a replacement for an explicit banana.

To test whether the two methods need identically named parameters, the professor changes the append parameter from **`obj`** to **`nobj`**. A student's NAND-gate comparison prompts a useful correction: the letter n is just part of an identifier, not logical negation. Later **`xobj`** serves the same purpose. The change must be consistent *within the method*. At the call boundary, it is the value that crosses, not its local name.

**Annotated teaching version — guide-added comments.** The revised beginning of the method (1:14:00); its two link assignments and return remain as above.

```python
    def append(self, nobj):         # The caller's "banana" is named nobj here.
        newItem = Item(nobj)        # Pass that value; __init__ still calls it obj.
```

At 1:13:27 the class reports the forward-and-back sequence, and at 1:14:00 the terminal shows this successful run below the older lowercase-name errors. Independent runs of the three consistently named versions also yielded these same five lines; the most recent run shown beside the later **`xobj`** editor is a different, failing experiment.

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
apple
banana
peach
banana
apple
```

**Key items to scan**

- **`Item(obj)`** — looks up the already-defined **`Item`** class when **`append`** runs; creates one new node.
- **`obj`** — the initializer's own name for its incoming value; it need not match the caller's name.
- **`None`** — the default in the initializer, used only when its optional value is omitted.
- **`nobj`** — renamed parameter; **`Item(nobj)`** passes its value to the initializer.
- **`xobj`** — later spelling of the append parameter, also used in its constructor call.
- **`py .\test\fruit.py`** — the PowerShell command runs the driver; the five words come from the driver's print statements, not from the constructor or append.

### 1:15:42 — Remove the initializer: make the error move

How can you tell whether **`Item("apple")`** really uses the initializer? Disable exactly the initializer and try the same driver. Now change just the first construction to **`Item()`**. If the two runs fail at different lines, that tells you which operation Python got past. The instructor saves the commented-out version before the first test (1:15:42–1:15:58).

**Annotated teaching version — guide-added comments.** These are excerpts of the disabled state, not a second initializer. The leading `#` characters on the first two lines are the instructor's edit; the explanatory notes are added.

```python
    #def __init__(self, obj = None):  # Disabled: no custom initializer accepts a fruit value.
        #self.object = obj           # Disabled: no instance value is stored here.

    def append(self, xobj):          # Can still be called on an existing Item.
        newItem = Item(xobj)         # Would need to construct a node with one supplied value.
        newItem.prev = self          # Not reached if that construction raises.
        self.next = newItem          # No new forward link in that failed attempt.
        return newItem               # No node is returned on that path.
```

**Experiment 1 — 1:15:58.** The class still exists, but without this initializer its inherited construction behavior doesn't accept the extra fruit value.

```python
fruit = Item("apple")               # Driver line 2: raises before fruit is assigned.
```

**Observed, final lines of traceback:**

```text
    fruit = Item("apple")
TypeError: Item() takes no arguments
```

**Experiment 2 — 1:17:00.** The driver has changed, not the meaning of the error. This time the first call takes no argument and succeeds. In particular, it has **not** stored apple.

```python
fruit = Item()                      # Driver line 2: create a node with no supplied value.
fruit = fruit.append("banana")      # Line 3 enters append; its Item(xobj) now raises.
```

**Observed, second run:**

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 3, in <module>
    fruit = fruit.append("banana")
  File "C:\CSCS1240F26\Item\src\item.py", line 10, in append
    newItem = Item(xobj)
TypeError: Item() takes no arguments
```

Read from the driver into the method: the outer line explains *why* Python entered append; the innermost line shows the operation it could not finish. There are no fruit prints in either failing run. Both failure locations and messages were separately reproduced from the transcribed code. By 1:20:00 the editor shows the initializer active again; the error still on the screen is terminal history, not evidence that the restored version fails.

**Key items to scan**

- **`Item("apple")`** — with the initializer disabled, the first test stops at driver line 2.
- **`Item()`** — succeeds without the custom initializer; this does not mean `Item("banana")` will succeed.
- **`fruit = fruit.append("banana")`** — in the second test, enters the method before the right-hand side fails; the assignment does not complete.
- **`newItem = Item(xobj)`** — the inner failure, `item.py` line 10; the explicit argument is banana.
- **`TypeError`** — here the supplied argument doesn't fit this version of the class; the traceback distinguishes two occurrences of the same message.

### 1:17:42 — One typed argument, two parameters

With the initializer restored, **`Item("apple")`** yields an initialized node, not the string and not the return value of **`__init__`**. The professor describes a blueprint, allocation and an address: each apple, banana or peach needs its *own* storage and the class's shared method needs to know which instance it is changing. In Python terms, construction uses **`__new__`** to obtain the instance; Python then invokes the initializer on that instance. The first parameter **`self`** receives the object itself, not an integer address that you must calculate or pass. The explicit string supplies the second parameter, **`obj`**. The initializer sets the node's attribute and finishes without an explicit return; the overall *class call* yields the node.

The same distinction applies again inside a bound method call. At 1:20:24–1:21:54, the instructor follows the banana value from the caller through two local names. The identities of the two instances matter more than the spellings of those names.

| Call reached | Which instance is `self` here? | Explicit value | What changes or comes back? |
|---|---|---|---|
| `Item("apple")` → `__init__` | Newly created apple node | `obj` is `"apple"` | Its `object` becomes `"apple"`; the class call yields the initialized node. |
| `fruit.append("banana")` → `append` | Existing apple node | `xobj` is `"banana"` | This call will attach the new node to apple. |
| `Item(xobj)` inside `append` → `__init__` | Newly created banana node, **not apple** | `obj` is `"banana"` | Sets banana's `object`; the class call yields that new node. |
| Resume `append` | Still the apple node | `newItem` refers to banana | Links both directions and explicitly returns banana. |

**Key items to scan**

- **`Item("apple")`** — the expression that produces the new initialized node for the caller.
- **`__new__`** — the creation step, described by the professor in terms of allocating memory.
- **`__init__`** — sets up that already-created instance; it doesn't return the new node to the driver.
- **`self`** — apple in the outer append, banana in the initializer for the new node.
- **`obj`** — the initializer receives the explicit value here. It is independent of the outer method's local name.
- **`xobj`** — the banana value in the outer append, passed as the explicit argument of the inner construction.

### 1:21:33 — After line four, does fruit still mean apple?

A student hesitates between apple and peach: nothing in the driver says “move to the next node” during construction. The decisive operation is **assignment**, not traversal. On each line Python evaluates the right-hand side *using the old binding*, waits for the call to return, and only then changes what the name on the left refers to. The existing nodes do not move. The professor later asks which *file and line* the call reaches (1:25:20–1:29:16), describing a call stack that keeps track of where execution resumes. Driver line 2 reaches the initializer in **`item.py`** at line 6; driver line 3 reaches **`append`** at line 9, which constructs its new node at line 10 before setting the links. The editor confirms where to look: its “Definition of append” pop-up shows `item.py:9` and identifies `fruit.py:4` as another reference (1:29:00). That pop-up locates source; it does not report which line ran.

**Annotated teaching version — guide-added comments.** The restored driver and class are side by side at 1:22:00.

```python
from item import Item               # Make the class available by its capitalized name.
fruit = Item("apple")               # Create apple; fruit now refers to this first node.
fruit = fruit.append("banana")      # Apple receives the call; rebind fruit to returned banana.
fruit = fruit.append("peach")       # Banana receives this call; rebind fruit to returned peach.
```

| Driver line completed | Receiver used on the right | Link changes during the call | What `fruit` now names |
|---|---|---|---|
| 2 | New instance is initialized | Both neighbor lookups still give `None`. | Apple node |
| 3 | Apple | Banana's `prev` becomes apple; apple's `next` becomes banana. | Banana node |
| 4 | Banana, **not apple** | Peach's `prev` becomes banana; banana's `next` becomes peach. The apple–banana links remain. | Peach node |

To isolate the question “what does this name refer to *here*?”, the editor briefly shows just **`fruit`** on line 5 (1:25:00–1:30:00). As a bare expression in a script it would look up the peach node and discard the result, not print it and not follow a link. The line is blank again at 1:31:00. An independent run with the expression inserted produced the same five fruit lines, not a sixth line.

**Key items to scan**

- **Assignment** — finish the right-hand call before rebinding the name on the left.
- **`fruit = Item("apple")`** — creates the first node; the initializer stores its value, but the *class call* supplies the node.
- **`fruit = fruit.append("banana")`** — changes two links, then changes the caller's binding from apple to banana.
- **`fruit = fruit.append("peach")`** — same method, different receiver; `fruit` ends at the third node.
- **`fruit`** — on line 5 the name denotes peach; writing a name in a script is not a print statement.
- **`item.py`** — defines **`append`** on line 9; the editor distinguishes its definition from callers such as `fruit.py:4`.
- **Call stack** — return from constructing banana to the unfinished append, then return from append to the assignment in the driver.

### 1:31:00 — Walk the links without losing the end

At the end of construction, **`fruit`** still refers to peach. To start printing at apple, the driver follows **`fruit.prev.prev`**: peach's previous node is banana, and banana's previous is apple. It assigns that reference to **`iter`**, a separate name for the current node. Here “iterator” means a manually moved position; this code uses neither an iteration protocol nor a loop. No nodes are copied, and walking the links does not reassign fruit.

**Annotated teaching version — guide-added comments.** Continued driver from the 1:31:00 frame; the discussion at that point follows the same walk.

```python
iter = fruit.prev.prev              # Start at peach; follow two back links to apple.
print( iter.object )                # Read the current node's value; print apple.
iter = iter.next                    # Move only the walking name, from apple to banana.
print( iter.object )                # Print banana.
iter = iter.next                    # Move from banana to peach.
print( iter.object )                # Print peach (the same node fruit still names).

iter = iter.prev                    # Turn around: peach back to banana.
print( iter.object )                # Print banana a second time.
iter = iter.prev                    # Banana back to apple.
print( iter.object )                # Print apple; fruit is still at peach.
```

The print order is **apple → banana → peach → banana → apple**, the five-line sequence observed in the earlier successful run and independently verified with the restored code. Each print reads the stored string from the current node; reading a value does not advance the walker. Only the assignments to the walking name do that. The left terminal in the 1:31:00 frame still ends with the earlier failed experiment, not a fresh run of this buffer.

**Key items to scan**

- **`fruit`** — remains bound to peach for the whole walk.
- **`fruit.prev.prev`** — follows two links, peach → banana → apple, to find the first node from the last.
- **`iter`** — the driver's movable name; ends at apple independently of `fruit`.
- **`iter = iter.next`** — follows one forward link; the two moves reach banana and peach.
- **`iter = iter.prev`** — follows one backward link; the two return moves reach banana and apple.
- **`print( iter.object )`** — displays the value without moving. The output sequence is **apple → banana → peach → banana → apple**.

## 1:32:00 — One more step back: distinguish the move from the failed print

The working walk finishes on apple. The class asks what will happen if it takes one more step backward. To predict it, first fix the picture in mind: this version **keeps the return value** of each append. Apple’s successor is banana, banana’s successor is peach, and the backward links point the other way. The name **`fruit`** ends on peach, so two backward links are needed just to start the printout at apple. The driver calls its moving reference **`iter`**; it is an ordinary variable here, not a call to Python’s iterator machinery.

**Annotated teaching version — guide-added comments** (extended driver visible at 1:33:14):

```python
from item import Item              # Use the class whose nodes hold the fruit and two links.
fruit = Item("apple")              # Create apple; it has no predecessor or successor yet.
fruit = fruit.append("banana")     # Link banana after apple; rebind fruit to banana.
fruit = fruit.append("peach")      # Link peach after banana; rebind fruit to peach.

iter = fruit.prev.prev             # Peach → banana → apple; fruit itself stays on peach.
print( iter.object )               # Display apple, not the Item instance's representation.
iter = iter.next                   # Apple → banana.
print( iter.object )               # Display banana.
iter = iter.next                   # Banana → peach.
print( iter.object )               # Display peach.

iter = iter.prev                   # Peach → banana.
print( iter.object )               # Display banana again.
iter = iter.prev                   # Banana → apple.
print( iter.object )               # Display apple again. The earlier walk stopped here.
iter = iter.prev                   # Apple has no predecessor: iter now holds None.
print( iter.object )               # Fails while evaluating the argument; no sixth print.
```

The first attempt did **not** test the new boundary. PowerShell reported **`TypeError: Item() takes no arguments`** when the driver tried to create the first apple. The instructor recalled commenting out the constructor, saving that state, then uncommenting it without saving the class file. The editor could show a perfectly usable constructor while a fresh **`py .\test\fruit.py`** still loaded the saved version. There are two files to check, not just the driver. An earlier traceback in the same terminal even stops inside the banana call at **`newItem = Item(xobj)`**; it is a different run, not another step of this failed apple construction.

After correcting the saved class, the extended driver actually reached the boundary. Observed output at 1:33:34 (traceback abbreviated):

```text
apple
banana
peach
banana
apple
  File "C:\CSCS1240F26\Item\test\fruit.py", line 18, in <module>
    print( iter.object )
AttributeError: 'NoneType' object has no attribute 'object'
```

| Step in the extended driver | Reference before the move | Value obtained | Result at the following print |
|---|---|---|---|
| Line 6: choose the start | peach | banana’s predecessor, apple | apple |
| Line 8: move forward | apple | banana | banana |
| Line 10: move forward | banana | peach | peach |
| Line 13: move backward | peach | banana | banana |
| Line 15: move backward | banana | apple | apple |
| Line 17: move backward | apple | no predecessor: **`None`** | Line 18 raises; nothing printed |

The traceback names line 18, not 17. The first apple really does have a **`prev`** field, so reading it succeeds. Its value is **`None`**. The failure comes only when line 18 requests **`object`** from that value. This is not an undefined variable, a “none statement,” or an inability to print the value itself. **`NoneType`** is the type of **`None`**; more precisely than the classroom shorthand, it is an object, but not a fruit node with these fields. At the other boundary peach’s **`next`** is also **`None`**. The instructor’s design caution was to think carefully before giving callers a possible non-node where they expect a node—not a Python rule forbidding **`None`**, nor a change to the implementation in this example.

**Key items to scan**

- **`fruit`** — ends on peach because each call’s result is assigned back to the name.
- **`iter`** — the separate moving reference; walking it back to apple does not move **`fruit`**.
- **`fruit.prev.prev`** — evaluated left to right: peach → banana → apple.
- **`py .\test\fruit.py`** — launches the on-disk driver; check the class file’s saved state too.
- **`TypeError: Item() takes no arguments`** — construction failed before this run could test a link. **`newItem = Item(xobj)`** is a different constructor call in older scrollback.
- **`prev`** / **`next`** — predecessor and successor fields. At the ends apple has no predecessor and peach has no successor.
- **`None`** / **`NoneType`** — a value marking no neighboring node, and that value’s type.
- **`object`** — the fruit field on an **`Item`**; asking for it after the move onto **`None`** raises **`AttributeError`**.

### 1:37:12 — The reverse operation: put a new item before apple

The oral assignment was to create a **`prepend`** method using the linked items just discussed. **`append`** is supposed to add after the last item; prepending adds before the first. With the three classroom items, the new item goes before apple, not between banana and peach and not merely before whichever node the variable happens to name. The editor briefly shows **`def prepend`** at 1:38:00. That is the beginning of a definition, not a working method, test result, or complete assignment specification.

**Key items to scan**

- **`prepend`** — the assigned operation: add before the first node; in this example that is apple.
- **`append`** — the contrasting promise: add at the end. The closing experiment tests whether the implementation actually keeps that promise.
- **`def prepend`** — unfinished editor text; no body or completed implementation is shown.

### 1:38:37 — Ignore the two return values: which node does the second call change?

The instructor removes the extra failing pair of lines at the bottom of the driver and makes a different change at the top: call **`append`** twice without assigning either result. A student compares it with earlier list operations and wonders whether it changes the type or replaces each fruit with the next fruit. Neither explains this code. The class still makes a banana and a peach; the change is **which node receives the second call**. An ignored return value does not undo links already written.

**Annotated teaching version — guide-added comments** (method and changed caller; the following excerpts run in the class and driver respectively):

```python
    def append(self, xobj):       # self is the node to which this call is addressed.
        newItem = Item(xobj)      # Create a NEW banana or peach node, not just a string link.
        newItem.prev = self       # The new node points back to the receiver.
        self.next = newItem       # Replace the receiver's forward link, even if it was occupied.
        return newItem            # Give the caller the actual node; do not print it.
```

```python
from item import Item             # Start the revised driver in a fresh run.
fruit = Item("apple")             # New apple; fruit stays bound to it for the rest of this run.
fruit.append("banana")            # Apple.next becomes banana. Returned banana is not assigned.
fruit.append("peach")             # Same apple: replace its banana link with a new peach link.

iter = fruit.prev.prev            # First prev is None; the second lookup fails here (line 6).
print( iter.object )              # Not reached. This version prints no fruit at all.
```

| After driver line | Node referred to by **`fruit`** | Forward path starting at that node | Backward link just set | Returned node |
|---|---|---|---|---|
| 2 | apple | apple → no neighbor | none set | constructor supplies the apple instance |
| 3 | apple | apple → banana | banana → apple | banana, discarded by the caller |
| 4 | apple | apple → peach | peach → apple | peach, discarded by the caller |

In class, “returns banana’s ID” means that the caller gets a reference to the very banana instance just created. **`return newItem`** does not call **`id()`** or return a number. The two bare calls each receive the *same apple* as **`self`**. Only the first one writes a link to banana. The second does not find banana and continue from it; it writes peach into the same place, **`self.next`**. No operation in either call gives apple a predecessor. In this state, **`fruit.prev.prev`** fails before even the first print. The terminal identifies line 6 and the failing lookup of **`prev`** (1:42:00–1:43:00); a separate run of the transcribed example confirmed the full message:

```text
AttributeError: 'NoneType' object has no attribute 'prev'
```

**Key items to scan**

- **`append`** — this revision attaches after its receiver, not necessarily at the list’s end.
- **`fruit`** — remains apple on both bare calls because the driver never assigns another node to it.
- **`self`** — apple on both calls in this version; it was apple then banana in the assigning version.
- **`return newItem`** — supplies the new instance to the caller; **`id()`** is not invoked.
- **`self.next`** — the one field overwritten: first with banana, then with peach.
- **`fruit.prev.prev`** — lookup of the second **`prev`** fails because apple’s first **`prev`** yielded **`None`**.

### 1:41:54 — Start from apple instead: read the output before predicting the error

Now change only the starting position. The actual line is **`iter = fruit`**, with neither backward hop. This gets past the failed initialization. A student predicts failure at the second print, reasoning there is nothing after apple. The instructor asks what *did* run, as well as what did not. The two printed lines settle the question: apple was not the last reachable node, but banana wasn’t its successor either.

**Annotated teaching version — guide-added comments** (same two bare append calls; replacement traversal, 1:42:00–1:43:00):

```python
iter = fruit                      # Start on apple; no predecessor lookup.
print( iter.object )              # Line 7: apple.
iter = iter.next                  # Line 8: apple's current successor is peach.
print( iter.object )              # Line 9: peach, not banana.
iter = iter.next                  # Line 10: peach has no successor; assigning None succeeds.
print( iter.object )              # Line 11: None has no object field; no third fruit printed.
```

Observed at 1:43:00: **`apple`**, then **`peach`**, then a traceback at line 11 on **`print( iter.object )`**. The error’s right edge is covered by the editor; the independent run confirmed **`AttributeError: 'NoneType' object has no attribute 'object'`**. The two later backward moves in the file never execute in this run.

A student asks whether the banana line might as well have a **`#`** in front of it. The instructor corrects the distinction: it was *apple’s forward link*, not the variable naming apple, that was overwritten. With a comment, no banana would be constructed and no link to it would ever be established. With the line active, both things happened; only the final path from apple is the same. The driver keeps no separate banana reference, so walking forward or backward from apple or peach cannot recover it. That is a claim about *reachability*, not a measurement of whether the object remains allocated in memory.

**Key items to scan**

- **`iter = fruit`** — starts on apple, avoiding the failed two-hop lookup.
- **`apple`**, **`peach`** — the first and second printed values; the second value proves apple had a successor.
- **`print( iter.object )`** — at line 11, it needs a node after peach; there isn’t one.
- **`AttributeError: 'NoneType' object has no attribute 'object'`** — the independently confirmed exception after those two prints.
- **`#`** — commenting out banana would prevent its creation; overwriting a link afterward is not the same event.

### 1:46:38 — Can going backward bring banana back?

A student proposes changing line 10 to a backward move, like the one on line 13. This is testable: **`iter.prev`** from peach follows the link actually written when peach was created. That link leads to apple, because the receiver of the peach call was apple. Banana’s old link *to* apple does not give apple a way back to banana.

**Annotated teaching version — guide-added comments** (driver at 1:48:00; same bare calls and starting apple):

```python
iter = fruit                      # Apple.
print( iter.object )              # Line 7: apple.
iter = iter.next                  # Follow apple's current link to peach.
print( iter.object )              # Line 9: peach.
iter = iter.prev                  # Changed line 10: peach points back to apple.
print( iter.object )              # Line 11: apple, not banana.

iter = iter.prev                  # Line 13: apple has no predecessor; iter becomes None.
print( iter.object )              # Line 14 fails. The final move and print are not reached.
```

Observed terminal result (1:48:57; traceback abbreviated):

```text
apple
peach
apple
  File "C:\CSCS1240F26\Item\test\fruit.py", line 14, in <module>
    print( iter.object )
AttributeError: 'NoneType' object has no attribute 'object'
```

This reveals a design flaw, not a limitation of backward traversal. If a caller remembers to keep the return value, the next call happens to be made on the tail. If the caller keeps apple instead, exactly the same method overwrites apple’s existing successor. The promised operation is to attach after the **last reachable node**, regardless of which return values the caller saves. The instructor proposes finding the last node first and then doing the attachment. The class does not implement that repair here.

**Key items to scan**

- **`iter.prev`** — changing line 10 walks peach → apple; line 13 then walks apple → **`None`**.
- **`last`** — proposed helper to reach the list’s end before changing its links; not an implementation demonstrated here.
- **`append`** — “put it at the end” is the intended behavior; “write to this node’s next field” is what the demonstrated body actually does.

### 1:48:38 — Separate saved files, uncommitted edits, and the history Git can show

The instructor tries to change revisions. This time saving an editor buffer is not enough: the working files differ from the checked-out version, and the requested checkout would overwrite them. Git refuses rather than discarding them. After discussing restoration and a switch to **`master`**, the instructor looks at the history to locate the point reached in class and preview the next design step.

| Action in order | Evidence | Meaning in this example |
|---|---|---|
| **`git checkout master~16`** | Command and refusal, 1:48:57 | Requested an ancestor of master. Git lists **`src/item.py`** and **`test/fruit.py`**; checkout aborts. No switch occurs. |
| **`git checkout master`** | Second command and second refusal, same frame | Naming the branch does not remove the conflicting changes. Git suggests committing or stashing before switching. |
| Restore the two files; try master again | Spoken, 1:48:57–1:49:24 | The instructor names restoring the class and test driver. The complete restore command lines and their individual responses are not captured; restoring a working file is not the same as preserving an edit in a commit. |
| View the short history | Spoken request for **`git log --oneline`**; resulting history visible at 1:49:38 | The log now shows **`843e915 (HEAD -> master) generator`**. That verifies the eventual branch, not a successful result for either earlier aborted attempt. |

The selected history entry **`fc6c333`** is the addition of append. In chronological order the next two entries above it are **`4847342`**, addition of last, and **`6cdb33d`**, calling last from append. That explains the preview: first find the last node, *then* attach the new one. These are later revisions in the repository, not new methods typed or run in the closing minutes. The final captures at 1:50:00 show recording controls rather than another classroom run.

**Key items to scan**

- **`git checkout master~16`** — failed request for the older revision; **`~16`** means follow the first parent sixteen times from **`master`**.
- **`git checkout master`** — initially aborts too; the later history, not the failed command, establishes the final branch.
- **`src/item.py`** / **`test/fruit.py`** — the working files Git warns would be overwritten; saving to disk does not commit a file.
- **`git restore`** — the restoration action named in speech; do not use it to preserve edits you mean to keep.
- **`git log --oneline`** — compact history discussed at the end; **`843e915 (HEAD -> master) generator`** marks the later checked-out branch tip.
- **`fc6c333`** → **`4847342`** → **`6cdb33d`** — addition of append, addition of last, then calling last from append.

## Knowledge check

1. You need an empty collection of unique items. Why is `{}` the wrong starting point? Why is `add` a more accurate verb than `append` for what happens next?
2. A set contains `2`, `4`, and `6`. After `g = f.remove(2)`, what can you predict about `f` and `g`? What changes if you next call `h = f.pop()`?
3. Suppose a function call is inside a replacement field of an f-string passed to `print`. Which call must finish first? If the inner call raises, where would you start reading the traceback?
4. Given `k = i.items()`, why can looking at `k` a second time show `(2, 37)` even though nobody reassigned `k`? How would you keep the earlier pairs instead?
5. Explain the two sets of quotation marks in `json.loads('{"red": "apple"}')`. What type do you have before decoding, and what lookup works afterward?
6. Why can `thing = Item` and `fruit = Item("apple")` both succeed yet refer to different kinds of objects? Does the initializer have to return the apple node explicitly?
7. With the class's append-to-current-node method, compare `fruit = fruit.append("banana")` with `fruit.append("banana")`. If the next call is made on `fruit`, which node receives it in each case? Why can a successful peach append make a subsequent traversal fail?
8. Your editor shows an initializer that accepts a value, but the terminal reports that `Item` takes no arguments. What should you verify before changing the signature? Once a run reaches an end link, is assigning `None` to a traversal variable itself the failing operation?

### Answer key

1. **`{}` builds a dictionary.** Use **`set()`** for an empty set; a nonempty set can be written **`{2, 4, 6}`**. A set tracks membership, not a last position where the caller can request insertion.
2. **`f`** now contains only `4` and `6`; **`g`** is `None`. Removal changed the existing set; it did not return the deleted number. **`pop()`** both removes and returns a remaining member. Class saw `4`; code should not depend on which member is chosen.
3. The inner call must supply a value before the string can be finished and passed to **`print`**. Read the exception type and message, inspect the last frame for the failing operation, then follow the callers upward to see who supplied its inputs. The last frame is not necessarily where a bad value originated.
4. **`k` refers to a view of `i`.** Changing the value for the existing key `2` changes what that view shows. A separate **`list(i.items())`** made *before* the change would keep the old pair in this integer-valued example.
5. The outer single quotes delimit a Python **`str`**. Its content includes double quotes around the JSON names and strings; they belong to the encoded text. **`json.loads`** decodes this JSON object to a Python **`dict`**, so looking up **`["red"]`** on the result yields `apple`.[4]
6. **`Item`** without parentheses refers to the class. Calling it constructs an instance; the initializer sets that instance's payload. **`__init__`** must not return the new object. The class call produces the instance for **`fruit`**.[3]
7. Both forms first link a new banana after apple. Only the assignment makes **`fruit`** refer to banana. The next call therefore writes **`banana.next`** in the first version, but **`apple.next`** in the second. In the second version peach replaces banana as apple's successor. Walking from apple now reaches peach and then `None`; asking that `None` for an attribute raises an exception.
8. Check the filename, import, save status and **newly executed** command before treating scrollback as evidence about the editor. A link may legally hold `None`, and assigning that value to a name succeeds. Trying to read **`.object`**, **`.next`**, or **`.prev`** *from* `None` is the failure.

## Library of definitions

| Term | Meaning in this session |
|---|---|
| Literal or display | Source notation that constructs or denotes a value directly: `[]`, `(3,)`, and `{"red": "apple"}` do not require the reader to identify a separately named constructor. |
| Mutable / immutable | A list's entries can change in place. A tuple's sequence of references cannot be replaced in place; reassigning a variable to another tuple is a different operation. |
| Hashable | A value has a hash stable during its lifetime and agrees in hash with values it compares equal to. Set members and dictionary keys must be hashable. A list is not. |
| Index / key | A sequence index selects a position. A mapping key selects its associated value; dictionary key `2` does not mean “third entry.” |
| View / snapshot | A dictionary view reflects later mapping changes. Converting its items to a list captures the pairs at that time; it is not a deep copy of their contents. |
| Serialization / deserialization | Turn supported Python data into interchange text, or read that text back into Python values. A displayed Python dictionary is not necessarily valid JSON text. |
| Argument / parameter | An argument is a value supplied by the caller. A parameter is the name that receives it for this call, as with the fruit label passed to the initializer. |
| Call stack / traceback | The stack represents active, unfinished calls. A traceback reports the chain of frames involved in an exception; it is not an instruction log of every line that ran. |
| Identity / reference | Identity distinguishes one live object from another. A variable refers to an object; assigning another reference need not copy or change that object. |
| Class / instance | `Item` is a class and itself an object. A particular `Item("apple")` is an instance with its own identity. |
| Method / receiver | A method is behavior defined on a class. In `fruit.append("peach")`, the current object referred to by `fruit` is the receiver supplied to the bound method. |
| Local name / reachability | A method's local name can go out of scope while its object remains reachable through a returned reference or a link held by another object. Losing one path does not prove immediate destruction. |
| Node / payload / link | One `Item` is a node; `object` holds its fruit label, and `next` and `prev` link to neighboring nodes. The label is not itself a node. |
| Endpoint / sentinel | At an end of the chain there is no neighboring node in one direction. `None`, a real Python object, represents that absence; it has no fruit attributes. |
| Rebinding / mutation | Assigning the call result to `fruit` changes which node the name refers to. Writing `self.next` changes a link on an existing node. One call may do both, or only the latter if the caller discards the result. |

**Reference notes.** The Python references below support technical clarifications, not claims about what happened on screen. Built-in collection behavior is documented in *Built-in Types*.[2] Python's data model distinguishes identity from an implementation-specific memory address and objects from the names that refer to them.[3] The JSON module describes the distinction between decoding text and encoding Python values.[4]

## Sources

[1] https://www.youtube.com/watch?v=scn2bNF9RRA&t=1s — CSCS1240F26 17 — JoesTA

[2] https://docs.python.org/3/builtins/stdtypes.html — Python: Built-in Types

[3] https://docs.python.org/3/reference/datamodel.html — Python: Data model

[4] https://docs.python.org/3/library/json.html — Python: JSON encoder and decoder

## Historical screen-code appendix

Editor line numbers are evidence, not code to type. Console line numbers, where added, are editorial. An ellipsis or visibility marker is not an instruction. Follow the timestamps to distinguish an editor change from a saved file, and both from a completed run.

### 0:03:00 — Earlier Item checkout and fruit-driver scrollback

The terminal visible at 0:03:00–0:04:00 already contains the following commands and results. The older history entries describe construction and navigation before the addition of append. The checkout reports a modified path script, so the checkout message alone does not establish a pristine working tree. No linked-list implementation is printed here.

```text
PS C:\com\joesta> cd C:\CSCS1240F26\Item\
PS C:\CSCS1240F26\Item> .\script\path.ps1
PS C:\CSCS1240F26\Item> git log --oneline
fc6c333 (HEAD) Item: addition of append( ).
2499ca0 Item: addition of constructor.
0203781 Fruit: use of prev attribute.
f70c1ad Fruit: use of second reference to fruit.
47413b4 Item: with object, next, and prev attributes. Fruit: direct assignments to object, and next attributes.
PS C:\CSCS1240F26\Item> git checkout master~17
HEAD is now at fc6c333 Item: addition of append( ).
M       script/path.ps1
PS C:\CSCS1240F26\Item> py .\test\fruit.py
apple
banana
peach
banana
apple
```

A separate image posted in Discord behind the shell shows an older **`py .\subproc.py`** run: ping output under **`STDOUT:`**, no text under **`STDERR:`**, and **`Exit Code: 0`**. It is not the live fruit test or an exercise assigned in this segment.

### 0:05:00–0:16:00 — Python shell record: types, a failed add, and two ways to remove

The PowerShell command **`py`** starts the Python 3.14.7 interactive interpreter. These are completed inputs and their results, not lines from an editor. The **`>>>`** symbols are prompts, not part of the input. The 0:05:10 event shows **`type`** still being typed; later frames show the complete call. At 0:07:00, **`e = (2`** is also in progress. Both the 0:08:00 and 0:08:47 frames show the completed six type checks.

```pycon
>>> a = list()
>>> b = []
>>> type(a)
<class 'list'>
>>> type(b)
<class 'list'>
>>> c = tuple()
>>> type(c)
<class 'tuple'>
>>> d = (3)
>>> type(d)
<class 'int'>
>>> e = (2,3)
>>> type(e)
<class 'tuple'>
>>> f = set()
>>> type(f)
<class 'set'>
```

At 0:10:00, the next result is a single list. Its wrapping below is editorial; the names and sequence follow the screen. At 0:11:00 the instructor has highlighted **`'add'`**.

```pycon
>>> dir(f)
['__and__', '__class__', '__class_getitem__', '__contains__', '__delattr__',
'__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__',
'__getstate__', '__gt__', '__hash__', '__iand__', '__init__',
'__init_subclass__', '__ior__', '__isub__', '__iter__', '__ixor__', '__le__',
'__len__', '__lt__', '__ne__', '__new__', '__or__', '__rand__', '__reduce__',
'__reduce_ex__', '__repr__', '__ror__', '__rsub__', '__rxor__',
'__setattr__', '__sizeof__', '__str__', '__sub__', '__subclasshook__',
'__xor__', 'add', 'clear', 'copy', 'difference', 'difference_update',
'discard', 'intersection', 'intersection_update', 'isdisjoint', 'issubset',
'issuperset', 'pop', 'remove', 'symmetric_difference',
'symmetric_difference_update', 'union', 'update']
```

At 0:12:00 the unfinished prompt reads **`f.add([2,3`**. It is *not* the submitted call. At 0:13:00 the failing call shows **`[2,4,6]`**, followed by the three successful integer adds. The later frames at 0:14:00, 0:15:00 and 0:16:00 add the removal, checks of the saved result, and pop respectively. The consecutive prompts after evaluating **`g`** are deliberate: the REPL produced no output for either evaluation.

```pycon
>>> f.add([2,4,6])
Traceback (most recent call last):
  File "<python-input-13>", line 1, in <module>
    f.add([2,4,6])
    ~~~~~^^^^^^^^^
TypeError: cannot use 'list' as a set element (unhashable type: 'list')
>>> f.add(2)
>>> f.add(4)
>>> f.add(6)
>>> f
{2, 4, 6}
>>> g = f.remove(2)
>>> f
{4, 6}
>>> g
>>> g
>>> type(g)
<class 'NoneType'>
>>> h = f.pop()
>>> h
4
>>> f
{6}
```

At 0:17:00 the editor shows lines 1–27 of `F26Demo\src\example.py`. The selections move from lines 6–7 to the formatted string, then to its inner call. Selecting text is not executing it. The following longer listing includes this prefix; the right end of line 27 is also legible at 0:20:00.


### 0:22:00–0:29:00 — `example.py`: calls, return values and successive definitions

Assembled visual reconstruction, not a claim about a saved file or a completed classroom run. The gutter numbers below are editor line numbers, **not Python to type**. `00-22-00.png` through `00-25-00.png` show lines 1–27; `00-28-00.png` and `00-29-00.png` show the overlapping region from line 7 through line 33. Line 27's full right end is readable in `00-24-00.png` and `00-29-00.png`. The overlapping text and numbering agree; intervening off-screen changes cannot be ruled out. Indentation is reproduced as spaces at the visible levels.

The top of the file distinguishes inspecting a function from calling it. The next definitions reuse a name but change the accepted arguments and return branches. Read the actual expressions in braces separately from the labels that precede them.

```text
 1 | def doNothing():
 2 |     pass
 3 |
 4 | print(f"dir(doNothing): {dir(doNothing)}\n")
 5 | print(f"type(doNothing): {type(doNothing)}\n")
 6 | print(f"id(doNothing): {id(doNothing)}\n")
 7 | print(f"call of doNothing(): {doNothing()}\n")
 8 |
 9 | def doNothing():
10 |     return None
11 | print(f"call of doNothing()): {doNothing()}\n")
12 |
13 | def douglasAdams():
14 |     return 42
15 | print(f"call of douglasAdams()): {douglasAdams()}\n")
16 |
17 | def doNothing(milk):
18 |     if milk == "fresh": return "spoiled"
19 |     else: return milk
20 | print(f"doNothing(\"fresh\")): {doNothing("fresh")}\n")
21 |
22 | def doNothing(milk, flour):
23 |     if milk == "fresh":
24 |         if flour != None: return "matzah"
25 |         else: return milk
26 |     else: return "spoiled"
27 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", "white")}\n")
28 |
29 | def doNothing(milk, flour=None):
30 |     if milk == "fresh":
31 |         if flour != None: return "matzah"
32 |         else: return "spoiled"
33 |     else: return "spoiled"
34 | [remainder not visible]
```

- Lines 1–7: inspect attributes, type and identity; finally call the no-op function and interpolate its `None` return. The identity depends on the run.
- Lines 9–15: explicit `return None` and integer `42` contrast with the earlier fall-through return. Lines 11 and 15 have an extra `)` in the printed label.
- Lines 17–20: `"fresh"` chooses `"spoiled"`; the extra `)` belongs to the label on line 20, too. Other input takes the `else` branch and is returned unchanged.
- Lines 22–27: `"fresh"` and `"white"` choose `"matzah"`. In this version fresh milk with `None` flour would return the milk itself.
- Lines 29–33: omitting flour is now legal. The inner `else` also changed, so omitting it with fresh milk returns `"spoiled"`, not `"fresh"`. Only the upper portions of the characters on line 34 are visible in the two later captures; no full driver is transcribed or treated as a historical run. The lesson's separate set traceback came from the REPL, not from this file.

At 0:26:00–0:31:00 the original set error, subsequent calls, and return values reappear in the foreground. These are the same REPL lines transcribed above, not a failure produced by the function listing.

### 0:32:00–0:44:00 — The dictionary experiment in the interactive shell

Selected console history, assembled from compatible overlapping scrollback, not a script or one uninterrupted screenshot. `00-32-00.png`–`00-33-00.png` show the constructor, type and attribute list; `00-34-00.png` shows an unfinished assignment, completed by `00-35-00.png`. `00-36-00.png`–`00-40-00.png` supply the subsequent lookups and view results. The placeholder on line 5 below is editorial; the ordinary method names from that display are given in the lesson.

```text
 1 | >>> i = dict()
 2 | >>> type(i)
 3 | <class 'dict'>
 4 | >>> dir(i)
 5 | [attribute-list display omitted here]
 6 | >>> i["one"] = 1
 7 | >>> j=[5,7,9]
 8 | >>> j[2]
 9 | 9
10 | >>> j["2"]
11 | Traceback (most recent call last):
12 |   File "<python-input-32>", line 1, in <module>
13 |     j["2"]
14 |     ~^^^^^
15 | TypeError: list indices must be integers or slices, not str
16 | >>> i[2] = 2
17 | >>> i
18 | {'one': 1, 2: 2}
19 | >>> i["one"]
20 | 1
21 | >>> i.keys()
22 | dict_keys(['one', 2])
23 | >>> i.values()
24 | dict_values([1, 2])
```

Lines 6–15 compare a named dictionary key to a list position: the list lookup fails only after the argument changes to a string. Lines 16–24 use both the string and integer keys of `i` and request separate views rather than a single value.

The following continuation preserves the error order. `00-40-00.png` contains the two failed calls; `00-41-00.png`–`00-42-00.png` show the corrected one. `00-43-00.png` already shows the reassignment through `i` but not the second display of `k`, which is visible in `00-44-00.png`.

```text
25 | >>> dict.items()
26 | Traceback (most recent call last):
27 |   File "<python-input-38>", line 1, in <module>
28 |     dict.items()
29 |     ~~~~~~~~~~^^
30 | TypeError: unbound method dict.items() needs an argument
31 | >>> dict.item()
32 | Traceback (most recent call last):
33 |   File "<python-input-39>", line 1, in <module>
34 |     dict.item()
35 |     ^^^^^^^^^
36 | AttributeError: type object 'dict' has no attribute 'item'. Did you mean: 'items'?
37 | >>> i.items()
38 | dict_items([('one', 1), (2, 2)])
39 | >>> k = i.items()
40 | >>> k
41 | dict_items([('one', 1), (2, 2)])
42 | >>> i[2] = 37
43 | >>> k
44 | dict_items([('one', 1), (2, 37)])
```

Lines 25–36 show two different problems: a missing instance, then a missing attribute. Lines 37–44 use the correct instance and preserve the first view result alongside the changed one. The only assignment between the two displays of `k` targets `i[2]`.

### 0:45:00–0:55:00 — Interactive source record: dictionary, module, and JSON text

These are selected Python prompt entries, not a `.py` file: the numbers before `|` are editorial, **not** part of commands to type. The source frames are `00-46-00.png`, `00-49-00.png`, `00-50-00.png`, `00-51-00.png`, `00-53-00.png`, `00-54-00.png`, and `00-55-00.png`. Terminal scrollback overlaps across frames. Repeated errors and intervening prompts are omitted; the bad quoting is intentionally preserved.

```text
 1 | m = {"red": "apple", "blue":"sky"}
 2 | n = {red: "apple", "blue":"sky"}
 3 | json
 4 | import json
 5 | json
 6 | m
 7 | type(m)
 8 | dir(json)
 9 | p = json.loads("{"red": "apple", "blue":"sky"}")
10 | p = json.loads('{"red": "apple", "blue":"sky"}')
11 | p
12 | type(p)
13 | type(json)
```

- Entries 1–3: the literal succeeds. Bare `red` raises `NameError: name 'red' is not defined`; bare `json`, before import, raises `NameError: name 'json' is not defined. Did you forget to import 'json'?`.
- Entries 4–8: the module name becomes available; evaluating it displays its origin, reproduced below as represented by Python. The data in `m` displays as `{'red': 'apple', 'blue': 'sky'}`, its type is `<class 'dict'>`, and the directory listing includes `dump`, `dumps`, `load`, and `loads`.

```text
<module 'json' from 'C:\\Users\\joe\\AppData\\Local\\Programs\\Python\\Python314\\Lib\\json\\__init__.py'>
```

- Entry 9: the completed, invalid line generates `SyntaxError: invalid syntax. Is this intended to be part of the string?` at `red`. The 0:52:00 image instead shows an unfinished input; it is not the executed line.
- Entries 10–13: the corrected expression is accepted without output. The next three expressions display `{'red': 'apple', 'blue': 'sky'}`, `<class 'dict'>`, and `<class 'module'>` respectively. Nothing in these captures shows an execution of `json.dumps`.

### 0:56:00 / 1:05:00–1:07:00 — `src/item.py`: the visible class prefix

Observed editor lines from `00-56-00.png`, confirmed in `00-57-00.png` and `01-05-00.png` through `01-07-00.png`. The terminal covers the rest of this file in these views. Gutter numbers are not Python.

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
 5 |
 6 |     def __init__(self, obj = None):
 7 |         self.object = obj
 8 |
 9 |     def append(self, obj):
```

Lines 2–4 establish defaults, not three new linked items. Lines 6–7 put the supplied value on the newly created instance; a missing argument uses `None`. Line 9 adds a separate operation with a required value. Its body cannot be recovered from these particular frames; the console's lower-case-name errors come from the driver, not from this method.

### 0:58:00 — `test/fruit.py`: the early, one-ID editor state

Observed, `00-58-00.png`. This whole visible driver already contains a bidirectional walk; it has only one identity print at line 4. Do not attach the separate *three*-ID run to this exact version.

```text
 1 | from item import Item
 2 |
 3 | thing = Item
 4 | print(id(Item))
 5 | fruit = Item("apple")
 6 | fruit = fruit.append("banana")
 7 | fruit = fruit.append("peach")
 8 |
 9 | iter = fruit.prev.prev
10 | print( iter.object )
11 | iter = iter.next
12 | print( iter.object )
13 | iter = iter.next
14 | print( iter.object )
15 |
16 | iter = iter.prev
17 | print( iter.object )
18 | iter = iter.prev
19 | print( iter.object )
20 |
```

Lines 3–7 name the class, identify it, and build the chain. Lines 9–14 start at apple and follow forward links to peach; lines 16–19 retrace the last two links. The name `iter` holds an item here; these lines select attributes, not a Python iterator protocol.

### 0:59:00 — `test/fruit.py`: successful three-ID version

Observed prefix and terminal from `00-59-00.png`; the terminal covers the editor after line 9. The preceding and following frames show a matching traversal, but the successful frame alone does not show the entire saved file.

```text
 1 | from item import Item
 2 |
 3 | thing = Item
 4 | print(id(Item))
 5 | print(id(thing))
 6 | fruit = Item("apple")
 7 | print(id(fruit))
 8 | fruit = fruit.append("banana")
 9 | fruit = fruit.append("peach")
```

The new prints at lines 5 and 7 compare the second name for the class against the first actual instance. Output and command in the same frame:

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
1499181725904
1499181725904
1499185972624
apple
banana
peach
banana
apple
```

### 1:00:00–1:04:00 — `test/fruit.py`: two failing probes and two naming edits

First probe, `01-00-00.png`, full visible editor state. This frame still has the earlier success in its terminal; `01-01-00.png` separately shows this first probe failing and the revised probe failing afterward.

```text
 1 | from item import Item
 2 | print(id(item))
 3 | thing = Item
 4 | print(id(Item))
 5 | print(id(thing))
 6 | fruit = Item("apple")
 7 | print(id(fruit))
 8 | fruit = fruit.append("banana")
 9 | fruit = fruit.append("peach")
10 |
11 | iter = fruit.prev.prev
12 | print( iter.object )
13 | iter = iter.next
14 | print( iter.object )
15 | iter = iter.next
16 | print( iter.object )
17 |
18 | iter = iter.prev
19 | print( iter.object )
20 | iter = iter.prev
21 | print( iter.object )
22 |
```

Second probe: `01-01-00.png` and the unobstructed driver in `01-02-00.png`. Only line 2 differs from the preceding 22 displayed lines:

```text
 2 | print(type(item))
```

Each run stops before line 3. Each ends `NameError: name 'item' is not defined. Did you mean: 'Item'?`, once for an attempt to obtain an identity, then for an attempt to obtain a type. Subsequent line-1 edits illustrate capitalization; no run of either naming illustration is shown. Separate editor states:

`01-03-00.png`:

```text
 1 | from item import Item_one
```

`01-04-00.png`:

```text
 1 | from item import itemOne
```

Line 2 still reads `print(type(item))` in both images. Neither changed import is evidence that the module acquired a newly named class.

### 1:10:00 — `src/item.py`: the class behind each linked node

The gutter numbers below identify lines in the editor; do not type them as Python. In `01-10-00.png` (also `01-13-00.png`), lines 2–4 provide class defaults, and lines 6–7 initialize one node's stored value. Lines 9–13 create the neighbor, install reciprocal links, and return the neighbor. The lowercase-name tracebacks beside this listing are from earlier tests, not failures in this method.

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
 5 |
 6 |     def __init__(self, obj = None):
 7 |         self.object = obj
 8 |
 9 |     def append(self, obj):
10 |         newItem = Item(obj)
11 |         newItem.prev = self
12 |         self.next = newItem
13 |         return newItem
```

### 1:14:00 — `src/item.py`: rename both uses inside append

`01-14-00.png` and `01-15-00.png` show the following method with its first explicit argument renamed. The initializer above it still says `obj`; it isn't necessary to change names in two different method scopes. The latest run visible here prints apple, banana, peach, banana, apple on separate lines, matching the result the instructor reports.

```text
 9 |     def append(self, nobj):
10 |         newItem = Item(nobj)
11 |         newItem.prev = self
12 |         self.next = newItem
13 |         return newItem
```

### 1:15:58–1:19:00 — `src/item.py` and `test/fruit.py`: the two failures

In `event_01-15-58.png` the terminal covers the lower part of the class, but the two comment markers and the `xobj` parameter are visible. `01-18-00.png` independently shows the full disabled class below. The first failed invocation stops in `fruit.py` line 2 at `fruit = Item("apple")`; no call to append is necessary to expose this failure.

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
 5 |
 6 |     #def __init__(self, obj = None):
 7 |         #self.object = obj
 8 |
 9 |     def append(self, xobj):
10 |         newItem = Item(xobj)
11 |         newItem.prev = self
12 |         self.next = newItem
13 |         return newItem
```

At `01-17-00.png`, the driver's line 2 instead says `Item()`; here are the visible lines, not a reconstruction of what was behind the terminal. This second invocation gets past line 2, reaches driver line 3, and fails at `item.py` line 10 before a banana node or either link is assigned. The terminal's two distinct tracebacks are reproduced in the worked section above.

```text
 1 | from item import Item
 2 | fruit = Item()
 3 | fruit = fruit.append("banana")
 4 | fruit = fruit.append("peach")
 5 |
 6 | iter = fruit.prev.prev
 7 | print( iter.object )
 8 | iter = iter.next
 9 | print( iter.object )
```

### 1:20:00 — `src/item.py`: initializer active again

`01-20-00.png` and `01-21-00.png` show this complete version in the editor. The assignment in line 7 is executable again and the append parameter remains `xobj`. The displayed buffer is evidence of the restoration; beside it are earlier terminal errors, not a successful rerun of the restored file.

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
 5 |
 6 |     def __init__(self, obj = None):
 7 |         self.object = obj
 8 |
 9 |     def append(self, xobj):
10 |         newItem = Item(xobj)
11 |         newItem.prev = self
12 |         self.next = newItem
13 |         return newItem
```

### 1:22:00–1:31:00 — `test/fruit.py`: a movable reference and a manual walk

`01-22-00.png` shows the full driver below with line 5 blank. `01-31-00.png` independently shows the same blank-line state. Lines 2–4 bind the driver name to apple, then banana, then peach. Lines 6–16 walk from peach back to the beginning, then across and back while printing the stored strings. These frames show the editor, not a fresh successful invocation.

```text
 1 | from item import Item
 2 | fruit = Item("apple")
 3 | fruit = fruit.append("banana")
 4 | fruit = fruit.append("peach")
 5 |
 6 | iter = fruit.prev.prev
 7 | print( iter.object )
 8 | iter = iter.next
 9 | print( iter.object )
10 | iter = iter.next
11 | print( iter.object )
12 |
13 | iter = iter.prev
14 | print( iter.object )
15 | iter = iter.prev
16 | print( iter.object )
```

**Intermediate editor state, not an inserted output line.** `01-25-00.png` (also `01-27-00.png` and `01-30-00.png`) shows line 5 occupied while the class discusses what `fruit` means at that point. Lines 4–6 in *that* state read:

```text
 4 | fruit = fruit.append("peach")
 5 | fruit
 6 | iter = fruit.prev.prev
```

Two editor pop-ups make the source locations concrete. `01-26-00.png` shows “References to print” with `fruit.py:9`, `fruit.py:11`, `fruit.py:14` and `fruit.py:16` while the first print is visible at line 7. `01-29-00.png` shows “Definition of append” at `item.py:9` and another reference at `fruit.py:4`. These are navigation aids over the source, not Python output or evidence that a line executed.

### 1:32:00–1:38:00 — `src/item.py`: the class and the unfinished method

Historical editor transcription from `event_01-33-14.png`, corroborated at `01-37-00.png`. Numbers at left reproduce the editor gutter; do not type them. The split pane cuts off the suffix of line 6. The independently executed fixture supplied a definition-ending colon; this transcription does not silently add it.

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
 5 |
 6 |     def __init__(self, obj = None)[remainder not visible]
 7 |         self.object = obj
 8 |
 9 |     def append(self, xobj):
10 |         newItem = Item(xobj)
11 |         newItem.prev = self
12 |         self.next = newItem
13 |         return newItem
```

Lines 2–4 supply initial field values; line 7 stores this node’s fruit. Lines 10–13 give the new neighbor a backward link, give the receiver a forward link, and return the new *node*. A return does not move the caller’s variable unless the caller makes an assignment. At 1:38:00, `01-38-00.png` shows the following additional, incomplete editor text after the unchanged line 13:

```text
14 |
15 |     def prepend
```

No parameters or body are present. The stub is absent at `01-39-00.png`; no sampled run shows it executing.

### 1:32:00–1:33:34 — `test/fruit.py`: keep the tail, then try a third backward move

`01-32-00.png` shows lines 1–16 below, with line 17 blank: the driver stores both returns, starts two predecessors back, and prints a five-step walk through three nodes. The entire listing below, **including lines 17–18**, is the later `event_01-33-14.png` state. At line 17 apple’s predecessor is a valid field containing `None`; line 18 is the first attempt to read a fruit from that value.

```text
 1 | from item import Item
 2 | fruit = Item("apple")
 3 | fruit = fruit.append("banana")
 4 | fruit = fruit.append("peach")
 5 |
 6 | iter = fruit.prev.prev
 7 | print( iter.object )
 8 | iter = iter.next
 9 | print( iter.object )
10 | iter = iter.next
11 | print( iter.object )
12 |
13 | iter = iter.prev
14 | print( iter.object )
15 | iter = iter.prev
16 | print( iter.object )
17 | iter = iter.prev
18 | print( iter.object )
```

`01-33-00.png` preserves several independent invocations of `py .\test\fruit.py`. Reading down: an earlier five-value run has no traceback before the next prompt; one run stops at driver line 2 (`fruit = Item("apple")`); another stops at driver line 3 (`fruit = fruit.append("banana")`) inside class line 10 (`newItem = Item(xobj)`); then another stops at driver line 2. All three construction failures report `TypeError: Item() takes no arguments`. The command at the bottom of that frame is input, not a completed result in the frame. The instructor explains why an uncommented constructor in the editor and a failing launch can coexist: the uncommented class file had not yet been saved. At 1:33:00 the class tab still shows the modified dot, while the driver tab shows a close mark; at 1:33:34 both show close marks.

The lower run in `event_01-33-34.png` prints `apple`, `banana`, `peach`, `banana`, `apple` on separate lines, then stops at driver line 18 with `AttributeError: 'NoneType' object has no attribute 'object'`. That is the run for the extended version; the preceding runs are not evidence of the same saved files.

### 1:39:00 — `test/fruit.py`: two bare calls and the original two-hop start

Observed in `01-39-00.png`. Compared with the extended driver, lines 3 and 4 no longer store the returned nodes, and lines 17–18 have been removed. Lines 6–16 have not been adapted to the changed starting node. That mismatch stops the execution at line 6, before any print.

```text
 1 | from item import Item
 2 | fruit = Item("apple")
 3 | fruit.append("banana")
 4 | fruit.append("peach")
 5 |
 6 | iter = fruit.prev.prev
 7 | print( iter.object )
 8 | iter = iter.next
 9 | print( iter.object )
10 | iter = iter.next
11 | print( iter.object )
12 |
13 | iter = iter.prev
14 | print( iter.object )
15 | iter = iter.prev
16 | print( iter.object )
```

The shell behind the editor at 1:39:00 still contains **older** output. The matching failure is in later scrollback (`01-42-00.png`, `01-43-00.png`): line 6, `iter = fruit.prev.prev`, `AttributeError` on the second `prev` lookup. No fruit printed in that invocation.

### 1:42:00–1:43:00 — `test/fruit.py`: start from the retained apple

Historical driver at `01-43-00.png`. The 1:42:00 status bar explicitly says the driver is saved, even though the adjacent terminal still shows the earlier two-`prev` failure. This revision changes line 6; both forward moves remain. At 1:43:00 the matching later invocation prints `apple` and `peach`, then fails at line 11 on `print( iter.object )`.

```text
 1 | from item import Item
 2 | fruit = Item("apple")
 3 | fruit.append("banana")
 4 | fruit.append("peach")
 5 |
 6 | iter = fruit
 7 | print( iter.object )
 8 | iter = iter.next
 9 | print( iter.object )
10 | iter = iter.next
11 | print( iter.object )
12 |
13 | iter = iter.prev
14 | print( iter.object )
15 | iter = iter.prev
16 | print( iter.object )
```

### 1:48:00 — `test/fruit.py`: reverse the second move

Historical driver at `01-48-00.png`. Only line 10 changes relative to the previous listing; `01-47-00.png` still reads `iter = iter.next` there. The student’s proposed change was not yet code at that earlier instant. With `prev` in place, line 11 prints apple; line 14 tries to get `object` from apple’s absent predecessor. Lines 15–16 remain in the file but never run.

```text
 1 | from item import Item
 2 | fruit = Item("apple")
 3 | fruit.append("banana")
 4 | fruit.append("peach")
 5 |
 6 | iter = fruit
 7 | print( iter.object )
 8 | iter = iter.next
 9 | print( iter.object )
10 | iter = iter.prev
11 | print( iter.object )
12 |
13 | iter = iter.prev
14 | print( iter.object )
15 | iter = iter.prev
16 | print( iter.object )
```

Observed lower run at `event_01-48-57.png`:

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
apple
peach
apple
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 14, in <module>
    print( iter.object )
           ^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'object'
```

### 1:48:57–1:50:00 — Checkout evidence and the `last` preview

`event_01-48-57.png` shows two **completed, failed** commands. Git issues the same warning for each (terminal wrapping normalized):

```text
PS C:\CSCS1240F26\Item> git checkout master~16
error: Your local changes to the following files would be overwritten by checkout:
        src/item.py
        test/fruit.py
Please commit your changes or stash them before you switch branches.
Aborting
PS C:\CSCS1240F26\Item> git checkout master
error: Your local changes to the following files would be overwritten by checkout:
        src/item.py
        test/fruit.py
Please commit your changes or stash them before you switch branches.
Aborting
```

At `01-49-00.png` only `g` precedes the caret; `it checkout master` is a gray suggestion. The spoken restore attempts and subsequent checkout fall between the available frames; the exact restore spellings and individual terminal responses are not visible. At `event_01-49-38.png` the history pager identifies `HEAD -> master`, and the editor status bar says `master`. An older-looking driver remains visible beside the pager; that is not proof of a fresh run or of the saved master source. Selected history rows, in their on-screen newest-first order (other entries omitted):

```text
843e915 (HEAD -> master) generator
ab909a4 Item: first( ), prepend( ), insertBefore( )
e01cc6d Item: avoid returning None.
7653923 Item: private attributes with accessor methods.
6cdb33d Item: call last( ) from append( ).
4847342 Item: addition of last( ).
fc6c333 Item: addition of append( ).
2499ca0 Item: addition of constructor.
0203781 Fruit: use of prev attribute.
f70c1ad Fruit: use of second reference to fruit.
```

Read bottom to top to see the sequence of design revisions. The instructor locates the append addition and previews reaching the last item before appending. The entries above it show what exists in repository history, not what was implemented during this closing discussion. The two closing PNGs (`01-50-00.png`, `01-50-00.800.png`) show OBS with **Stop Recording** still visible, not a completed stop.

## Micro-Learning Insights · removable appendix

- **Choose a collection by the question you need to ask.** “What is third?” needs an ordered sequence. “Is this already present?” fits a set. “What value goes with this key?” fits a dictionary.
- **In `(3,)`, the comma does the work.** `(3)` still evaluates to the integer `3`; grouping a number is not constructing a one-item tuple.
- **No REPL echo does not mean no effect.** After `g = f.remove(2)`, examine the set for the change and use `print(g)` to see `None`. Those observations answer different questions.
- **A word shared by two APIs need not promise the same selection rule.** A stack pops its top. A set's `pop()` gives you a member without promising *which* member.
- **Read a nested expression from the inside out.** A call in an f-string finishes before the outer `print` gets its string. In a traceback, find the failing line, then walk back to the caller that supplied its value.
- **Distinguish a view from a photograph.** The saved `items()` view shows the change to `(2, 37)` without being assigned again. Make a list first if the earlier pairs are what you need to keep.
- **Ask which parser sees a quotation mark.** Python first builds the string. Only then does `json.loads` see its contents. An invalid Python string literal and invalid JSON inside a valid Python string are different failures.
- **Compare identities, not the printed digits.** Whether `thing is Item` is a repeatable question. The particular integer returned by `id(Item)` in one process is not a repeatable answer.
- **Track arrows on paper.** For each append, write down the receiver, that receiver's new `next`, and the returned node. Add a fourth column for `fruit` only after the call returns. That is enough to locate the missing banana.
- **Returning is not assigning.** `fruit.append("banana")` still creates and links banana. Discarding the result leaves `fruit` at apple; it does not undo the operation.
- **The end marker is a value, not a node.** `iter = iter.prev` can successfully put `None` in `iter`. It is the *next attribute lookup* that needs a node and fails.
- **Test the promise in a method's name.** “Attach after this node” and “append at the end” agree only when this node is last. The instructor proposed finding the last node first; the proposal is not a completed Session 17 repair.
