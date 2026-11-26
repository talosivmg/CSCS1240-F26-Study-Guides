# Where did banana go?

**CSCS1240F26 · Session 18 · Worked teaching companion**

The class starts with a program that prints `apple`, `banana`, `peach`, `banana`, `apple`. Remove the two occurrences of `fruit =` before the append calls and the program fails. Both calls still run. So what changed? To answer, keep two records on paper: **which node each name refers to**, and **which node each link reaches**. A call can change a link without moving a name. That distinction explains the first traceback, the missing banana, and the unfinished repair at the end of class.

**Reading the evidence.** This is [JoesTA’s Session 18 recording](https://www.youtube.com/watch?v=aFHuko-ptPA&t=1s).[1] The local English auto-captions cover the opening through the sign-off. The saved media probe gives `1:02:07.283`; the last cue starts at `1:02:04.799` and its nominal end extends slightly beyond the video. The original 1080p images cover every whole minute from `0:00:00` to `1:02:00`, plus six event targets and a closing frame at `1:02:06.283`. These are requested capture times, not measurements of exact decoded timestamps. Intervals without a cue and edits between frames are not reconstructed as speech or actions. Read *observed* as something visible in class, *expected* as a prediction from the stated setup, and *independently verified* as a separate run, not the instructor’s terminal.

**Study route:** follow the working driver; diagnose the two failed lookups; distinguish syntax from execution; trace the attempted endpoint repair. The workbench then tests a separate solution. Use the numbered editor listings near the end to compare revisions without typing the gutter numbers.

## One apple, two appends: which item does `fruit` name?

### Practice with the list, not just with fruit

**Instructor/class account · 00:00:34–00:02:17.** The instructor connects three things: the general idea of classes and objects, Python’s way of making them, and the course’s own doubly linked list, built from **`Item`** objects. A student had said that more practice would help. The Discord post offers ways to practice the *same structure* with different data, inputs and directions of travel. Its extra-credit wording records what the class saw then, not a current grading promise.

**Observed on screen · Discord `#general`, 00:02:00.** The post reads:

> Practice ideas (good for **extra** credit too):
>
> 1. Create a list of a few Items other than fruit.
> 2. Create a list of a few Items from CLI arguments.
> 3. Create a list of a few Items from stdin.
> 4. Create a list of a few Items from data stored in a file.
> 5. Create a list of a few Items, then iterate through the Items writing them to a file
> 6. Create a prepend method.

The second and third prompts are different experiments: values supplied when launching a program are command-line arguments; standard input is a stream the running program reads. The fourth and fifth reverse the file’s role: first a file supplies values, then a file receives them from a traversal. The instructor also recommends practicing Git while doing these exercises: share work in Discord if useful, but use the collaborative notes repository or a repository of your own to practice adding, committing and pushing. This is a recommendation, not a sequence of commands run on screen here.

**Key items to scan**

- **`Item`** — the course’s building block can hold something other than a fruit name; changing the data need not change the links.
- **CLI arguments** — build items from values passed at program launch; **`stdin`** — build them from the input stream instead.
- **Input file** — read values to construct items. **Output file** — visit items in order and write their values.
- **Prepend** — proposed practice: add at the front rather than after the current item; no implementation is shown in the post.
- **Git add, commit, push** — stage changes, record a revision, then share commits with the remote repository. Discord sharing alone does not exercise those steps.

### What is stored on apple, and what comes from the class?

**Observed on screen · `item.py`, 00:01:00 and 00:03:00; instructor/class account · 00:02:23–00:03:19.** One item holds a value and has places to look for a neighbor on either side. In **`Item("apple")`**, **`Item`** is the class; the new node holding the string **`"apple"`** is one *instance* of that class. The string and its node are not the same thing: you follow links between nodes and read a node’s **`object`** attribute to get the string.

**Annotated teaching version — guide-added comments · `item.py`, opening methods.**

```python
class Item:                         # Define the kind of node used for all three fruits.
    object = None                   # Defaults on the class, not three new nodes.
    next = None                     # No forward neighbor unless this node gets one.
    prev = None                     # No backward neighbor unless this node gets one.

    def __init__(self, obj = None): # Initialize the new node; obj can be omitted.
        self.object = obj           # Store this node's own value, e.g. "apple".

    def append(self, obj):          # Add after the particular node making the call.
        newItem = Item(obj)         # For obj = "banana", create a NEW banana node.
        newItem.prev = self         # Banana can now go back to apple on the first call.
        self.next = newItem         # Apple can go forward to banana; replaces any old link.
        return newItem              # Give the caller banana itself, not its string; print nothing.
```

Notice where the assignments occur. **`next = None`** in the class body supplies a default; **`self.next = newItem`** writes an attribute on the particular instance making the call. After appending banana to apple, apple’s own forward link leads to banana. Banana’s forward link still reads as **`None`** from the class until banana itself gets a successor. The class default has not been changed for everyone.[2] Likewise, the initializer sets each node’s own value without changing the class’s **`object`** default. The parameter **`self`** names the particular node in play; it does not mean “the whole list.”

The class also has a way to *find* the last node. It is already visible here; the opening driver does **not** call it, and the displayed append does **not** use it automatically.

The class returns to this helper after diagnosing the missing banana. For now, notice that merely defining a method does not make a call to it happen.

**Key items to scan**

- **`Item("apple")`** — makes an instance whose own **`object`** attribute contains **`"apple"`**; the class **`Item`** is not that particular node.
- **`__init__(self, obj = None)`** — initializes a new node; **`self`** is the new instance, **`obj`** the supplied value or the default **`None`**.
- **`next = None`** / **`prev = None`** — class-level defaults read when an instance has no overriding link of that name.
- **`newItem.prev = self`** — the new banana node gets its own backward link to apple.
- **`self.next = newItem`** — apple gets its own forward link to banana; this implementation does not search for the end first.
- **`return newItem`** — hands back the newly linked node; it does not move the caller’s variable or print anything by itself.
- **`last()`** — follows **`next`** until the neighbor would be **`None`**; **`break`** preserves the last real node for **`return iter`**.

### Baseline: save each return, then walk in both directions

**Observed editor · `fruit.py`, 00:01:00 and 00:04:00; class account · 00:03:56–00:04:17.** The name **`fruit`** starts at apple. The first append changes the links *and* returns banana. Saving that return is what makes banana, not apple, the receiver of the second append. The second assignment then leaves **`fruit`** at peach. The separate name **`iter`** can move through the nodes without changing where **`fruit`** points. Here **`iter`** is just a variable in a hand-written traversal, not a call to Python’s built-in iterator function.

**Annotated teaching version — guide-added comments · baseline driver.**

```python
from item import Item             # Use the course's custom node class.

fruit = Item("apple")             # Make the first node; fruit names apple.
fruit = fruit.append("banana")    # Link apple to a NEW banana; rebind fruit to banana.
fruit = fruit.append("peach")     # Link banana to a NEW peach; rebind fruit to peach.

iter = fruit.prev.prev            # From peach go back to banana, then apple.
#iter = fruit                     # Inactive alternative; does not reset iter to peach.
print( iter.object )              # Display apple.
iter = iter.next                  # Move only iter to banana.
print( iter.object )              # Display banana.
iter = iter.next                  # Move iter to peach.
print( iter.object )              # Display peach once, at the turn.

iter = iter.prev                  # Go back to banana.
print( iter.object )              # Display banana.
iter = iter.prev                  # Go back to apple.
print( iter.object )              # Display apple.
```

**Try the baseline on your own.** In a new folder, save the opening class from Appendix A as `item.py` and its baseline driver as `fruit.py`, without the gutter numbers or bars. With the two files side by side, run `python fruit.py` from that folder. This avoids depending on the course project’s import configuration. Start a new process for each driver edit.

**Observed PowerShell output · scrollback at 00:07:00, `py .\test\fruit.py`.**

```text
apple
banana
peach
banana
apple
```

**Source-based execution trace · building versus walking.** The method alters links; the assignments in the caller decide which name moves. The only lines that display anything are the five prints.

| Driver line(s) | Receiver or starting point | What changes | `fruit` after | Printed |
|---|---|---|---|---|
| 3 | New node | Create apple | apple | — |
| 4 | apple | Join apple ↔ banana; save returned banana | banana | — |
| 5 | banana | Join banana ↔ peach; save returned peach | peach | — |
| 7–9 | peach | Back twice, so `iter` names apple | peach | `apple` |
| 10–11 | apple | `iter` follows `next` to banana | peach | `banana` |
| 12–13 | banana | `iter` follows `next` to peach | peach | `peach` |
| 15–16 | peach | `iter` follows `prev` to banana | peach | `banana` |
| 17–18 | banana | `iter` follows `prev` to apple | peach | `apple` |

**Key items to scan**

- **`from item import Item`** — brings the class into the driver; it does not make apple.
- **`fruit = fruit.append("banana")`** — call on apple first, then save the returned banana node under **`fruit`**.
- **`fruit = fruit.append("peach")`** — now the call is on banana, so peach’s predecessor is banana.
- **`iter = fruit.prev.prev`** — with **`fruit`** at peach, two backward links put **`iter`** at apple.
- **`#iter = fruit`** — a comment, not an executed assignment.
- **`print( iter.object )`** — display the string at the current node; the intervening **`iter.next`** and **`iter.prev`** lookups supply the moves.

### Two failed runs: a legal `None` versus an illegal lookup on `None`

**Instructor/class account · 00:03:21–00:03:55 and 00:04:20–00:05:59; observed changed driver · 00:05:00.** What happens if the two **`fruit =`** prefixes disappear? Both calls still run. The first leaves apple pointing forward to banana. But because **`fruit`** still names apple, the second call writes *apple’s same forward link* again, this time to peach. Peach’s backward link points directly to apple. Banana was created; it is no longer reachable by walking forward from apple. In neither call did anything give apple a predecessor.

**Annotated teaching version — guide-added comments · failed run A, excerpt through first possible print.**

```python
from item import Item             # Same class; this run starts over.
fruit = Item("apple")             # A NEW apple, not the peach from the prior run.
fruit.append("banana")            # Link apple to banana; do not save the return.
fruit.append("peach")             # Link apple to peach instead; fruit is STILL apple.
iter = fruit.prev.prev            # Apple.prev is None; looking up None.prev raises here.
#iter = fruit                     # Comment only; cannot rescue the previous line.
print( iter.object )              # Never reached; no fruit is printed in this run.
```

**Observed traceback excerpt · 00:07:00, second invocation in the visible group.**

```text
  File "C:\CSCS1240F26\Item\test\fruit.py", line 7, in <module>
    iter = fruit.prev.prev
AttributeError: 'NoneType' object has no attribute 'prev'
```

**Instructor/class account · 00:06:00–00:08:43; observed changed driver · 00:08:00.** The instructor tries shortening the expression. Does making line 7 legal fix the program? This time Python *does* finish the assignment. The important question is what value it assigned.

**Annotated teaching version — guide-added comments · failed run B, changed section.** The run starts afresh with the same new apple and the same two unsaved append returns as run A.

```python
iter = fruit.prev                 # Legal: apple has a prev attribute. Its value is None.
#iter = fruit                     # Still disabled; iter therefore stays None.
print( iter.object )              # First evaluate None.object. It raises before print runs.
```

**Observed traceback excerpt · 00:07:00, third invocation in the visible group.**

```text
  File "C:\CSCS1240F26\Item\test\fruit.py", line 9, in <module>
    print( iter.object )
AttributeError: 'NoneType' object has no attribute 'object'
```

| Question | Run A: two backward lookups | Run B: one backward lookup |
|---|---|---|
| What is `fruit` just before line 7? | Apple; `apple.next` is peach | Apple; `apple.next` is peach |
| What does the first `.prev` retrieve? | `None` from apple | `None` from apple |
| Does the assignment to `iter` finish? | No; the next lookup asks `None` for `prev` | Yes; `iter` now names `None` |
| What is the first illegal request? | `.prev` on `None`, line 7 | `.object` on `None`, line 9 |
| Is any fruit printed on this run? | No; line 9 is never reached | No; computing the argument fails before `print` can display it |

**Independently verified, not a classroom execution.** Running the transcribed class and the three driver states separately reproduced the five-line baseline output, then empty standard output with the line-7 exception, then empty standard output with the line-9 exception. The fruit above the tracebacks in PowerShell belongs to the earlier successful invocation.

**Instructor/class account · 00:08:50–00:11:58.** The class asks whether the thing being looked up is defined and which dot matters. The instructor restores the two-lookups expression and asks the class to “become Python.” Split the line into the seven meaningful tokens the class counts: **`iter`**, **`=`**, **`fruit`**, **`.`**, **`prev`**, **`.`**, **`prev`**. Reading the text from the left is not the same as performing the assignment target first. Determine what the right side can produce before saying what the left side names. 

**Key items to scan**

- **`fruit.append("banana")`** — ignoring the return does not cancel the link change; it leaves **`fruit`** on apple.
- **`fruit.append("peach")`** — on that same apple, replaces the forward link to banana with one to peach.
- **`fruit.prev`** — an existing attribute can legally contain **`None`**; it does not raise just because there is no previous node.
- **`fruit.prev.prev`** — the second **`.`** lookup asks **`None`**, not apple, for **`prev`**; line 7 raises **`AttributeError`**.
- **`iter = fruit.prev`** — **`=`** binds **`iter`** to **`None`** because the right side completed.
- **`iter.object`** — in the shorter version line 9 asks **`None`** for **`object`**; the attribute name in the error changes along with the failing line.

## Which part of the line goes first? (0:12:00–0:14:22)

Look at **`iter = fruit.prev.prev`** before deciding which link is broken. The class asks whether the equal sign counts as a token. It does. A **token** is a meaningful unit of source text; it need not have spaces on either side. Count the names and punctuation in reading order, then ask a *different* question: in what order do they produce a value?

```text
iter   =   fruit   .   prev   .   prev
 1     2     3     4     5    6     7
```

Recognizing **`=`** tells you this is an assignment; it doesn't mean that Python acts on the destination first. Set aside the name on the left. There are five tokens on the right, beginning with **`fruit`**, the third token of the whole statement. Python needs the object this name currently refers to. The first dot looks up **`prev`** on *that* object; the second dot looks up the same name on the *result of the first lookup*. Only if both succeed does the result become the new value of **`iter`**. The two spellings of the attribute name are identical; their owners need not be.

A student wonders if having two operators together causes the error. No: the expression has an unambiguous grouping. **Precedence** tells us which operations bind more tightly; the two member accesses are at the same level and chain from left to right. The professor compares **`2 + 3 + 4 + 5`**: start with 2 plus 3 to get 5, add 4 to get 9, then add 5 to get 14. This is an analogy for grouping these particular operations, not a rule that *every* Python operator groups left to right. The useful debugging question is: *What value did the first operation hand to the second?*

**Key items to scan**

- **Token** — one meaningful piece of the line; each dot counts even without spaces.
- **`iter = fruit.prev.prev`** — seven tokens in all; five belong to the right-hand expression.
- **`=`** — finish evaluating the right before assigning to the left.
- **`fruit`** — first name whose current value is needed, not the name being assigned.
- **`prev`** — first read a link on the starting node, then read a link on its predecessor.
- **`iter`** — receives the last lookup's result only if that lookup succeeds.
- **Precedence** and **`2 + 3 + 4 + 5`** — the repeated additions group from the left (5, 9, 14), as do the two attribute accesses in the fruit example.

## One sign, two jobs (0:14:22–0:18:54)

The professor writes two assignments at the bottom of the driver and asks: *Are both valid? Do they say the same thing?* Leaving them after a line known to fail would not establish that they execute. Moving them **before** that line makes a useful test: the instructor reports that the driver reached line 10, and the later terminal frame at 0:20:00 shows that line's failure. The arithmetic assignments didn't print anything, but they didn't stop execution either.

**Annotated teaching version — guide-added comments.** The first pair is visible at 0:15:00 and 0:18:00; the second is the replacement at 0:19:00. The values below were independently checked, not printed in class.

```python
a = +5   # Positive five: a becomes 5.
b = 3+5  # Add two numbers: b becomes 8.
```

```python
a = -5   # Negate five: a becomes -5.
b = 3-5  # Subtract five from three: b becomes -2.
```

An **operand** is what an operation acts on. Read the first line aloud: not “just five,” but “a is assigned **positive five**.” There's one operand for the sign; there is no missing addend. The instructor suggests *uni* as in *unicycle*: a **unary** operator takes one operand. Between 3 and 5 the same glyph instead performs addition, a **binary** operation with two operands. Negative five versus three minus five makes the distinction easier to hear. The signs are not part of the variable name and the equals sign is not another operand of the addition.

**Key items to scan**

- **Operand** — the 5 in **`+5`**; both the 3 and the 5 in **`3+5`**.
- **Unary** — **`+5`** means *positive five* here; **`-5`** gives negative five. One operand is enough.
- **Binary** — **`3+5`** adds to get 8; **`3-5`** subtracts to get −2. Each needs two operands.
- Moving the test above a known failure — reaching the later line shows the earlier assignments completed; it does not mean their values were printed.

## Read the dot as “member of”: break the notation deliberately (0:18:54–0:24:46)

Can the dot in **`fruit.append("banana")`** stand alone on one side the way a sign can? The professor takes away one side at a time. These are different versions of line 4, not consecutive instructions. The first is visible in the 0:20:05 terminal frame; the second is visible in the 0:20:45 editor and the class reports the second rejection around 0:20:52. Each invalid line below was also checked separately with Python.

**Annotated teaching version — guide-added comments.**

```python
fruit.              # Invalid: there is an owner, but no attribute name after the dot.
```

```python
.append("banana")   # Invalid as a statement: which object's append is this?
```

Observed for the trailing-dot run, and independently verified for both isolated statements:

```text
SyntaxError: invalid syntax
```

This fails *before* any of the driver's statements can run: Python cannot parse the file. Restoring **`fruit.append("banana")`** fixes that particular defect. In the professor's terminology the “member-of operator” is **binary**: you need the thing on the left and the member name on the right. More precisely, Python evaluates the expression on the left and looks up the *spelled name* on the right, not the value of a second variable called **`append`**. The parentheses then call the resulting method with the string.[4] A complete expression can still fail at runtime: **`fruit.prev.prev`** *parses*, but the second lookup fails if the first gave **`None`**. That is an **`AttributeError`**, not a syntax error.

Why insist on saying “member of” rather than “dot”? A student gives the example of a *member of a union*. Volunteering alongside a union is not the same as belonging to it; saying someone is a member presupposes something they're a member **of**. In code, identifying the owner is the difference between selecting apple's link and banana's. The class definition tells us which named parts an Item supplies, including callable ones. Another student asks what *append* means: think of the appendix at the end of a document. The intended action is *add to the end*; in **this** implementation that only works as intended when the call is made on the current end node. The method itself does not search for the end.

**Key items to scan**

- **`fruit.`** — missing the named member; the 0:20:05 terminal names line 4 and reports **`SyntaxError`**.
- **`.append("banana")`** — missing the owner; a leading member dot is not like a unary sign.
- **`fruit.append("banana")`** — look up **`append`** on the current node, then call it with the banana string.
- **Binary** — the professor's two-sided reading of member access: owner on the left, attribute *name* on the right.
- **`fruit.prev.prev`**, **`None`**, **`AttributeError`** — valid syntax; looking for the second link on a value without that attribute fails during execution.
- Union member / document appendix — one analogy identifies *whose* member; the other explains *where* an append is intended to add.

## What gets the banana, and what stays apple? (0:24:47–0:28:20)

The class returns to **`fruit`**. It is a reference to an Item in memory, not the word “apple” and not a container that automatically follows every new item. With **`Item("apple")`**, the new node's **`self.object`** is set to the string **`"apple"`**. Its two links initially read as **`None`**: the class declares those defaults, and this initializer changes only the data attribute. The professor notes that **`object`** was a choice of field name: **`value`** or **`thing`** would have served if both the writer and the readers consistently used the new spelling.

Now follow the explicit string into **`fruit.append("banana")`**. The node currently named by **`fruit`** is the **receiver**, supplied to the method as **`self`**. The string goes into the method's parameter **`obj`**. Inside the method, **`Item(obj)`** creates a *different* node and passes the string into that new node's initializer. The name **`obj`** occurs in both definitions, but each call has its own local parameter; the initializer's **`self`** is now the new banana, not the older apple. After initialization, the method joins the nodes and returns the banana node. Neither returning a node nor changing a link reassigns the caller's name.

**Annotated teaching version — guide-added comments.** Focus on which node each call uses, not just on the two fruit names in the arguments (editor at 0:27:00).

```python
fruit = Item("apple")    # Make the apple node; fruit names it.
fruit.append("banana")   # Apple.next becomes banana; ignore the returned banana node.
fruit.append("peach")    # Still calling on apple: replace apple.next with peach.
```

Thus fruit still refers to apple and its backward link is still empty. Starting the two-step backward lookup there tries to read a second link from **`None`**. The goal, as the instructor answers a student's “what does it look like?” question, is to walk apple → banana → peach and back to apple. Merely constructing all three nodes doesn't guarantee that route. The earlier successful output in scrollback belonged to an earlier setup, not to these two unassigned calls.

**Key items to scan**

- **`fruit`** / **receiver** — the caller's name still points to apple for *both* unassigned calls.
- **`Item("apple")`**, **`self.object`**, **`"apple"`** — the call makes a node; the field holds the string, not the entire node.
- **`None`** — the initial link value, not a node with another backward link.
- **`object`**, **`value`**, **`thing`** — the first is the actual field; the last two are spoken naming alternatives, not edits.
- **`fruit.append("banana")`** — its link changes survive even when its return value is ignored.
- **`self`** / **`obj`** — which node is acting versus which data was supplied; for the append these are apple and the banana string.
- **`Item(obj)`** — creates the banana node inside the append; its initializer receives that new node and the same string.

## Restore the assignments: the receiver changes between calls (0:28:20–0:35:43)

“I don't know how you did that” is a good objection: adding **`fruit =`** changes no code inside the method. What changes is **which node receives the next call**. Read the right occurrence of **`fruit`** as “the node I have now” and the left one as “the name I will give the returned node.” The 0:29:00 editor has both assignments restored. The visible terminal output beside it is apple, banana, peach, banana, apple on separate lines; the command itself is behind the editor in that frame.

**Annotated teaching version — guide-added comments.** Assume the lesson's Item class has been imported.

```python
fruit = Item("apple")            # New node, no neighbors; fruit refers to apple.
fruit = fruit.append("banana")   # Call on apple; keep the returned banana node in fruit.
fruit = fruit.append("peach")    # Call on banana; keep the returned peach node in fruit.
```

| Right-hand call | What was changed inside the call? | After the assignment |
|---|---|---|
| `Item("apple")` | The apple node stores the string; both links still read as `None`. | `fruit` → apple |
| `fruit.append("banana")` | Apple’s next is banana; banana’s previous is apple. | `fruit` → banana |
| `fruit.append("peach")` | Banana’s next is peach; peach’s previous is banana. Apple’s next is **still banana**. | `fruit` → peach |

The professor compares reusing this one name with assigning **`a = -5`**, then **`a = +5`**. The second assignment leaves the same name holding a different value; it does not turn negative five into a different integer. Likewise the apple node has not *become* banana. Rebinding changes which object the name identifies; the two link assignments within the method are what changed existing state.

A further question asks whether using **`Item`** is what made the example work. Separate two fixes: calling the class is what made the *first instance* available at all; keeping the append results is what made *this traversal* work. Importing a class makes the class available, not an apple node. Calling the class starts **instantiation**: in the professor's account Python uses **`__new__`** to obtain the new instance and **`__init__`** to initialize it. It is the *class call*, not a return of the node from the initializer, that gives the caller a reference. The instructor cautions against invoking the initializer directly a second time. For this particular class that would write the data field of the *same* node again, not make a fresh node or reset its links. That distinction matters more than whether one casually calls the whole process “the constructor.”[3]

**Key items to scan**

- **`fruit =`** / **`fruit`** — only rebind the name once the right-hand call has supplied a node.
- **`fruit = fruit.append("banana")`** — receiver apple, return value banana, final value of the name banana.
- **`fruit = fruit.append("peach")`** — receiver banana this time; peach can now lead backward through banana to apple.
- **`a = -5`** then **`a = +5`** — same name, different value; these are assignments, not two outputs.
- **`Item`** / **instantiation** — a class definition and an instance made by calling the class are different things.
- **`__new__`** — obtains the new instance during ordinary construction here.
- **`__init__`** — initializes that instance; directly running it again does not create a second node.

## Is it a decimal point or a link? Walk back from peach (0:35:43–0:41:00)

Before solving the two backward lookups, the professor contrasts the dot in **`a = +5.3`** (editor at 0:37:00–0:38:00) with the first dot in **`fruit.prev.prev`**. In **`5.3`**, the point separates the whole part from the fractional part *inside one numeric literal*. In the second expression, **`fruit`** and **`prev`** are names separated by attribute-access punctuation. Python does not first fetch two values, discover that they are numbers, and then build a decimal from them. It is the spelling of the source that makes **`5.3`** one number token. That is why a name holding a number would not turn a following attribute name into a fractional part.

**Annotated teaching version — guide-added comments.** The arithmetic scratch and the driver are independent; the decimal assignment doesn't move any links. This is the portion of the restored walkthrough that explains the *first* printed fruit.

```python
a = +5.3                 # Store positive 5.3 in a; nothing is printed.
iter = fruit.prev.prev   # From peach, follow prev to banana, then prev to apple.
print( iter.object )     # Read the apple node's stored string and display apple.
```

Ask *which item's previous?* twice. For the first dot the owner is peach because that is where the last append left **`fruit`**. For the second it is banana, not peach again: the first lookup delivered the node that owns the next lookup. Only then does **`iter`** receive apple. Apple here is initially an **Item**, not a string; **`iter.object`** is the string the outer print displays. Neither lookup moves **`fruit`**, which still identifies peach. The subsequent forward steps can now start from apple without reconstructing the list.

**Key items to scan**

- **`a = +5.3`**, **`5.3`** — a unary plus and one decimal number; no named member is involved.
- **`fruit.prev.prev`** — two distinct lookups: peach → banana, then banana → apple.
- **`fruit`** / **`prev`** — starting object versus the name of the backward link sought on each successive owner.
- **`iter`** / **Item** — the result assigned to the traversal name is the apple node; no new node is created.
- **`iter.object`** — the apple string read from that node. The surrounding print displays `apple`.

## Where did banana go? Follow the reference before changing the code

At 00:41:00 the working walkthrough has two different names doing two different jobs. After the two assignments from the append calls, **`fruit`** refers to peach. Walking back twice from peach reaches apple. The separate name **`iter`** now starts at apple: it can move forward and back without changing where **`fruit`** points. On **`iter = iter.next`**, Python looks up the right-hand side on the *current* node before changing what the name denotes. It neither copies banana into apple nor removes apple from the list.

**What changed at 41:33?** The instructor returns to the failing version. The two bare calls are the same experiment as before; the new evidence comes from changing where the walk starts and recording exactly how far each run gets.

### Remove two assignments: the second append overwrites a link

The class asks why removing the assignments breaks the walk (00:41:33–00:49:38). Starting each run with a new apple, neither bare append changes what **`fruit`** names. The code *inside* **`append`** still changes a link and returns a new node. The problem is not that the second call failed to create peach; it's that the second call attached peach to apple **again**.

**Annotated teaching version — guide-added comments.** This is one setup, followed by three alternative starts that were tried on separate runs.

```python
fruit = Item("apple")       # Fresh apple; both links initially read as None.
fruit.append("banana")      # Apple.next becomes banana; the returned banana isn't assigned.
fruit.append("peach")       # Apple.next becomes peach instead; fruit still denotes apple.
```

| After this call | Node receiving the call | Forward path from apple | Backward link just set | Return used by caller? |
|---|---|---|---|---|
| Construct apple | New apple | apple → None | None | Yes: fruit names apple |
| Append banana | apple | apple → banana → None | banana.prev → apple | No |
| Append peach | apple, **not banana** | apple → peach → None | peach.prev → apple | No |

There is no longer a forward path from apple to banana. That is all the walk proves; it does not prove when a detached node is destroyed. Peach is a valid node with an **`object`** value of `"peach"`. It is **`peach.next`** (in words: the next link of the peach node), not peach itself, that is **`None`**. The instructor notes that the name **`object`** makes the error harder to read: here it means an attribute holding the stored value. **`None`** has no attribute named **`prev`** or **`object`**; no broader assertion about all Python attributes is needed.

**Annotated teaching version — guide-added comments.** Read each group as a *different* version of line 11, not three instructions executed in order. The ordinary prints and link steps beneath line 11 were left in the driver.

```python
# Run A: keep the old two-link rewind, even though fruit no longer names peach.
iter = fruit.prev.prev      # 11: apple.prev is None; asking that result for .prev raises.

# Run B: remove only the second .prev.
iter = fruit.prev           # 11: reading apple.prev succeeds and assigns None.
print( iter.object )        # 13: reading None.object raises before print can display anything.

# Run C: start right at apple instead of rewinding.
iter = fruit                # 11: apple, not None.
print( iter.object )        # 13: apple.
iter = iter.next            # 14: apple.next now leads to peach, not banana.
print( iter.object )        # 15: peach.
iter = iter.next            # 16: peach.next is None; the assignment itself succeeds.
print( iter.object )        # 17: None.object raises; no third fruit and no backward walk.
```

**Observed from three separate invocations** (00:42:00, 00:44:00, 00:47:00; corresponding editor starts at 00:43:00, 00:45:00, 00:46:00). The exception text is literal; the dash means no fruit line was printed, not that the program printed a dash.

| Start on line 11 | Fruit lines before traceback | Failure point and actual message |
|---|---|---|
| `iter = fruit.prev.prev` | — | 11: `AttributeError: 'NoneType' object has no attribute 'prev'` |
| `iter = fruit.prev` | — | 13: `AttributeError: 'NoneType' object has no attribute 'object'` |
| `iter = fruit` | `apple`, then `peach` | 17: `AttributeError: 'NoneType' object has no attribute 'object'` |

One debugging method works in either direction. Forward: identify the receiver of each call, then draw the link it writes. Backward: start at the first failed lookup, identify which name held **`None`**, then ask which earlier expression assigned it. Moving a failure from line 11 to 17 is progress in diagnosis, not evidence that appending is fixed.

**Key items to scan**

- **`fruit`** — with no reassignment it remains the new apple for both calls.
- **`append`** — currently means “attach after the receiver”; that is only “add at the end” if the receiver is already last.
- **`fruit.append("banana")`** — produces banana and writes apple's next link; ignoring the return does not undo the write.
- **`fruit.append("peach")`** — overwrites that same forward link; apple now leads straight to peach.
- **`prev`** — the first lookup on apple returns `None`; the *second* lookup is the one that raises at line 11.
- **`peach.next`** — descriptive shorthand for peach's missing forward link; the driver reads it with `iter.next`.
- **`object`** — the node's stored string; looking for this attribute on `None` explains the line-13 and line-17 failures.
- **`None`** — safe to assign to `iter`, not safe to treat as another fruit node.

## Find the last *reachable* node, then decide what to do with it

At 00:50:18–00:56:58 the question changes: could the list find its own end rather than require the caller to keep hold of the last node? Calling **`fruit.last()`** passes no argument between the parentheses. Python supplies the node before the dot as **`self`**. Here that's apple. Inside **`last`**, **`iter`** is a local name with its own scope; it is not the driver's variable of the same spelling. The two names can refer to the same node without becoming the same variable.

**Annotated teaching version — guide-added comments.** The method is already visible before its walkthrough. This is the pre-insertion version, lines 15–21 of `item.py`.

```python
def last(self):                 # Called on apple; self refers to that node throughout.
    iter = self                 # Start a local reference at apple; do not change any link.
    while True:                 # Enter at least once; True will not end this loop by itself.
        if iter.next == None:   # Is there no successor to the node we're visiting?
            break               # Stop the loop while iter still names the last node.
        iter = iter.next        # Otherwise follow one link, then test again.
    return iter                 # Give the node to the caller; don't print its string.
```

Unlike a **`for`** loop over a supplied sequence, this **`while`** loop repeats according to a condition. Here the outer condition is always **`True`**; the real exit test sits *inside* the loop. Indentation matters: **`break`** exits the loop, not the method. **`return iter`** is outside the loop; it ends the method and sends a reference back. Testing the *next link* before moving is why the result is peach rather than **`None`**. If the very first node has no successor, the first test stops immediately and the method returns its starting node.

**Instructor's hand trace, independently checked against the pictured code.** The chain is the damaged **apple → peach** chain, not the earlier intact three-node chain.

| Step / line | Local position | Test or action | Consequence |
|---|---|---|---|
| Entry, 16 | apple | Set local `iter` from `self` | Driver hasn't received a return yet |
| Pass 1, 17–20 | apple | `apple.next` denotes peach, not `None` | No break; advance local `iter` to peach |
| Pass 2, 17–19 | peach | `peach.next` is `None` | Break; line 20 does not execute this time |
| After loop, 21 | peach | Return the peach **node** | No output yet; apple and its links are unchanged |

More intervening nodes would mean more advances. A search along existing links does not reconstruct the missing banana. The name **`iter`** here is just a variable: the walk is explicitly coded one link at a time.

**Key items to scan**

- **`fruit.last()`** — no explicit argument; start at the apple named by `fruit` and return the reachable endpoint.
- **`last`** — follows links already present; returns peach in the damaged chain, not an inventory of fruit ever created.
- **`self`** — the implicit receiver, still apple even after local `iter` has moved to peach.
- **`iter`** — method-local walking position, distinct from the caller's name of the same spelling.
- **`for`** — the earlier kind of iteration contrasted with testing a condition here.
- **`while`**, **`True`** — repeat an indented body; the literal constant alone never turns false.
- **`if iter.next == None:`** — compare the successor with **`None`**, not the current node; keep hold of the endpoint.
- **`break`** — leave the nearest loop immediately; **`return iter`** then sends that last node to the caller.

### Change the caller's start: predict first, then read the actual run

At 00:57:17–00:58:07 the instructor asks what will print if line 11 now uses **`iter = fruit.last()`**. The method has correctly found peach. But the driver still tries to walk **forward** from whichever node it gets. Starting at the correct endpoint for an append is the wrong starting point for a forward display of the whole list.

**Annotated teaching version — guide-added comments.** The two bare appends have already left apple pointing to peach. The line numbers belong to the driver, not the method.

```python
iter = fruit.last()         # 11: first run last() on apple; bind the caller's iter to returned peach.
#iter = fruit               # 12: commented out; it does not reset the caller to apple.
print( iter.object )        # 13: extract and print peach's string.
iter = iter.next            # 14: move past peach; iter now holds None.
print( iter.object )        # 15: the lookup fails before a second print can happen.
```

**Expected in class, then observed at 00:58:00:** exactly one fruit line, `peach`, followed by line 15 at `print( iter.object )` and `AttributeError: 'NoneType' object has no attribute 'object'`. This is not the earlier line-17 failure after *two* fruit lines. No instructions below line 15 ran in this invocation.

**Key items to scan**

- **`iter = fruit.last()`** — consumes the return and changes the caller's start from apple to peach; it does not rebind `fruit`.
- **`#iter = fruit`** — the leading `#` makes it inactive; only line 11 selects the start.
- **`print( iter.object )`** — first succeeds on peach (13), then fails on `None` (15).
- **`iter = iter.next`** — line 14 finishes normally: reading a missing successor is not itself the exception.

## Calling the right method is not the same as using its return

At 00:58:40 the driver is back to **`iter = fruit`** in the editor. The instructor wants end-finding inside **`append`**, so callers can start anywhere on the chain and still add to the end. By 00:59:20 the method begins with **`self.last()`**. A method may call another method on the same object. The new first line really does search, but it has no assignment, and the search changes no links. Its return is discarded. The two link assignments below still use the original **`self`**.

**Annotated teaching version — guide-added comments.** This is the *attempted* change, not a finished fix.

```python
def append(self, obj):          # Both bare driver calls make apple the receiver.
    self.last()                 # Finds apple, then banana on the second call; result unused.
    newItem = Item(obj)         # Create a new banana or peach node from the supplied string.
    newItem.prev = self         # Its backward link still points to apple, not the found tail.
    self.next = newItem         # Write apple.next both times: peach replaces banana there.
    return newItem              # Return the new node; the bare driver call discards this too.
```

| Call position | What runs, and where execution resumes | Node/result |
|---|---|---|
| Banana call on apple | Enter `append` on apple; pause it at line 10 to call `last` on apple | `last` finds apple immediately; returns apple, which line 10 does not keep |
| Resume banana append | Lines 11–14 create banana, set its `prev`, set apple's `next`, return | Caller ignores returned banana; `fruit` is still apple |
| Peach call on apple | Enter `append` on apple again; `last` now advances once | `last` returns banana, the node the append *needed* to use; line 10 discards it |
| Resume peach append | Lines 11–14 write links using the unchanged apple receiver | `peach.prev` is apple; `apple.next` is peach; caller ignores returned peach |

The instructor follows the banana call into **`last`** and back, then defers further work (01:01:00–01:01:58). The rest of the table spells out the consequence of the visible statements; it is **not** a completed classroom repair. To preserve banana, the link writes would need to use the *returned endpoint* as their attachment point. Moving a local name during a search does not move the receiver in the method that called it.

**Key items to scan**

- **`iter = fruit`** — the displayed driver now starts at apple, irrespective of the endpoint a search found internally.
- **`append`** — the intended operation is “attach at the end,” even if the caller still holds apple.
- **`self.last()`** — returns apple on the banana append and banana on the peach append; neither result is captured.
- **`self`** — still the node before the dot in the outer call, apple in both cases.
- **`last`** — its local walk ends and control returns to the next statement of `append`; it does not silently retarget that statement.
- **`newItem.prev = self`** — peach's backward link is set to apple, not banana.
- **`self.next = newItem`** — the write that replaces apple's banana link with peach.
- **`return newItem`** — distinct from the inner return of the tail; the driver ignores the new-node return too.

### The closing discrepancy: which version actually ran?

At 01:00:00 PowerShell shows **two completed invocations** of **`py .\test\fruit.py`**. Each prints only `peach`, then fails in `fruit.py` on line 15. In the editor behind it **`fruit.py`** still has a modification dot and line 11 reads **`iter = fruit`**. At 01:01:00 the instructor asks how an apple start could print peach first. It couldn't with the displayed code: distinguish the edited text from the program the terminal read. The compatible earlier driver began with **`iter = fruit.last()`**. The two runs are observed; the precise saved contents at launch were not read from disk.

| Evidence | Start and class state | Fruit output and exact stopping point |
|---|---|---|
| **Observed**, 00:58:00 | Driver visibly starts at `fruit.last()`; before the new inner call | `peach`; line 15: `AttributeError: 'NoneType' object has no attribute 'object'` |
| **Observed**, 01:00:00 | Two completed command/result groups, the earlier one and a repeat; changed driver still marked unsaved | Each: `peach`; same line 15 and same `AttributeError`; prompt returns after the second |
| **Independently verified**, not classroom output | Fresh in-memory transcription of both pictured closing panes: bare `self.last()` in `append`; driver starts at `fruit` | `apple`, then `peach`; line **17**: `AttributeError: 'NoneType' object has no attribute 'object'` |

Changing only the start back to the tail in that independent check reproduced the single `peach` and line 15; that shows consistency, not proof of the exact saved source. No later capture shows a successful full list or a third closing invocation. The class stops at the design goal: retain all fruit whether or not the caller reassigns its variable.

**Key items to scan**

- **`py .\test\fruit.py`** — two completed groups are visible at 01:00:00; a repeated traceback is not a new kind of failure.
- **`fruit.py`** — its modification dot distinguishes the displayed buffer from the saved version used by a command.
- **`iter = fruit`** — expected and independently verified first print is `apple`; the next is `peach`, then line 17 fails.
- **`iter = fruit.last()`** — the earlier saved-compatible start explains the terminal's one `peach` and line-15 failure.
- **`AttributeError`** — compare the failing line *and* fruit lines printed before it; the message alone cannot identify the version.

At `1:02:00`, the screen returns to the same practice menu seen at the opening. The closing OBS capture shows recording controls, not a new run or a demonstration of `prepend`.


## Workbench: finish the repair, then try to disprove it

**Transfer example — guide-added, not a completed classroom fix.** Keep the class’s `last()` method. Replace only `append` with the following method inside `Item`. If the caller keeps referring to apple, the second call must still find banana and attach peach **to banana**, not to apple. The returned tail matters twice: use it for the new node’s backward link and for the existing node’s forward link.

```python
def append(self, obj):           # self may still be apple on BOTH calls.
    tail = self.last()           # First call: apple. Second call from apple: banana.
    newItem = Item(obj)          # Make a fresh banana or peach node.
    newItem.prev = tail          # Peach must lead back to banana, not to apple.
    tail.next = newItem          # Banana now leads forward to peach.
    return newItem               # The caller may keep this node or ignore it.
```

**Independent test driver** (with that replacement, not the historical method):

```python
fruit = Item("apple")                 # Keep a name for the original first node.
banana = fruit.append("banana")       # Keep the returned node for comparison.
peach = fruit.append("peach")         # Deliberately call on apple a second time.
print(fruit.object, fruit.next.object, fruit.last().object)  # Forward order.
print(peach.prev is banana, banana.prev is fruit)           # Check both back links.
```

**Independently verified output:**

```text
apple banana peach
True True
```

Checking only that `fruit.last().object` is `peach` would be a weak test: it also succeeds on the broken two-node path. The middle value and the backward links distinguish a real repair from a list that merely ends on the expected fruit. The test uses a new three-node setup, not objects left over from the earlier failures.

**Key items to scan**

- `tail = self.last()` — keeps the returned node; the search need not change `self`.
- `tail.next` and `newItem.prev` — write the two sides of the **same** new neighboring pair.
- `peach.prev is banana` — checks that the link reaches the particular banana node, not just some node with the same payload.

## Syntax to remember

| Pattern from the lesson | Read it as |
|---|---|
| `fruit = Item("apple")` | Make a node initialized with apple; bind `fruit` to the node, not to the string. |
| `fruit = fruit.append("banana")` | Look up the old `fruit`, run its method, then bind the name to the returned banana node. |
| `fruit.append("banana")` | Run the method, including its link writes, without keeping its returned node in `fruit`. |
| `fruit.prev.prev` | Read one named attribute at a time: `(fruit.prev).prev`. |
| `print( iter.object )` | Look up the current payload first. If lookup fails, this call prints no payload. |
| `a = +5.3` | The period is inside the number; unary `+` applies to that number. |
| `if iter.next == None:` | As written in class: test the next link; equality is a test, not an assignment. |
| `break` / `return iter` | Exit the nearest loop / leave the method with the current node. These are separate control transfers. |

## Knowledge check

1. You start with apple and write `fruit = fruit.append("banana")`. Which node receives the call? Which node does `fruit` name after it? What would change if you dropped the assignment?
2. With the original method, call both appends on apple. Predict the payloads reachable by moving forward from apple. Which exact link write removed banana from that route?
3. Still holding apple, compare `iter = fruit.prev.prev` with `iter = fruit.prev` followed by `print(iter.object)`. Which lookup fails in each case? Does either print a payload?
4. A learner says: “The first dot in `fruit.prev.prev` can use the same rule as the dot in `+5.3`.” How would you explain the difference? What part of `a = +5.3` is an operator acting on one operand?
5. Run `last()` in your head on the shortened apple → peach route. At which node does the loop break, what is returned, and what remains unchanged in the caller?
6. A terminal shows only `peach` before an error, while the visible editor starts at apple. What evidence would distinguish a different saved caller from a defect in `print`? What could you conclude without that evidence?

### Answer key

1. Apple receives the call. The method builds banana and links it to apple; then its return value is assigned to `fruit`, so the name moves to banana. Without assignment the link writes still happen, but the name stays on apple. Another call on `fruit` would therefore have a different receiver.
2. Apple, then peach, then no next node. The second `self.next = newItem` writes the apple node’s one forward field again. It replaces the reference to banana; it does not rewrite banana’s stored string to say peach.
3. In the first case `apple.prev` gives `None`; looking up **`prev`** on that value fails *before* assignment completes. In the second case binding `iter` to `None` succeeds, but looking up **`object`** on it fails while evaluating the argument to `print`. Neither failing path displays a payload.
4. In `fruit.prev`, `prev` names an attribute on the object to the left. In `5.3`, the dot helps spell the numeric literal; Python is not looking for a member named `3`. The leading `+` is the unary operator. Class discussion calls the first use “member of”; the language reference calls the construct an attribute reference.[4]
5. `last()` first sees another node after apple, so its local `iter` moves to peach. Peach has `next` equal to `None`, so the loop breaks *before* moving off peach. `return iter` returns peach itself. The caller’s `fruit` is still apple, and neither link was rewritten by the search.
6. Inspect the saved driver’s starting expression, save indicator, command and new output together, or execute each candidate separately with fresh nodes and label those runs. The tail-start caller predicts peach first; the head-start caller predicts apple first. Scrollback alone does not establish which editor revision produced it, and it is not evidence that Python printed the wrong payload.

## Library of definitions

| Term | Meaning in this session |
|---|---|
| Class / instance | `Item` defines the node type; the node initialized with `"apple"` is one instance. Calling the class instantiates it. |
| Initialization | `__init__` receives the already-created instance as `self` and stores `obj`. The class call supplies the resulting instance to the caller; `__init__` does not return it.[3] |
| Attribute / payload | An attribute is a named part accessed on an object. The field literally named `object` holds the payload (here, a fruit string); `next` and `prev` hold links. |
| Doubly linked list | A chain whose neighboring nodes have forward and backward references. In this demonstration the program works directly with nodes rather than a separate list container. |
| Head / tail | First / last node on the intended path. At its ends the missing neighbor is represented by `None`, not by a new empty fruit node. |
| Reference / binding | A reference gives access to an object. Binding a name such as `fruit` to a different node does not move the contents of either node.[2] |
| Mutation / rebinding | `self.next = newItem` changes a field on an existing node. `fruit = ...` changes which value that name denotes. These can occur in the same statement’s execution. |
| Method / receiver | A method supplies behavior for an object. In `fruit.append(...)`, the node named by `fruit` is the receiver; it supplies `self`. |
| Argument / parameter | `"banana"` is an argument (a supplied value). `obj` is a parameter (a local name for a supplied value). |
| Scope / traversal cursor | `iter` inside `last()` is that call’s local walking position. It is not the caller’s variable of the same spelling. Here `iter` is an ordinary name, not the Python iteration protocol. |
| Token / operand | A token is a lexical unit such as a name, `.` or `=`. An operand is a value or expression an operator acts on; counting tokens is not the same as ordering the work. |
| Unary / binary | Unary `-` negates one operand as in `-5`; binary `-` takes two as in `3-5`. Read punctuation in context. |
| `None` / `AttributeError` / `SyntaxError` | `None` marks the absent neighbor. A lookup unsupported by an object can raise `AttributeError` at run time. An incomplete expression can instead raise `SyntaxError` before the file runs. |

## Sources


[1] https://www.youtube.com/watch?v=aFHuko-ptPA&t=1s — CSCS1240F26 18 — JoesTA

[2] https://docs.python.org/3/tutorial/classes.html — Python tutorial: Classes

[3] https://docs.python.org/3/reference/datamodel.html — Python data model: object.__init__

[4] https://docs.python.org/3/reference/expressions.html — Python expressions: Attribute references

## Appendix A · Historical screen-code record

### Opening code record: the class and three distinct driver states

**Observed on screen.** The numbers to the left of `|` are Sublime Text gutter numbers, not part of the Python source. A number without code indicates a visible blank row. Each listing below is supported in a single original frame; adjacent frames corroborate it. Times are requested frame targets, not a continuous record of edits.

#### `src/item.py` — class, append-after-current, and last-node search

**00:01:00**, left pane; the class is independently legible again at **00:06:00**. Lines 2–4 provide class defaults; lines 6–7 initialize one instance’s stored value. Lines 9–13 create and join a new neighbor, then return that neighbor. Lines 15–21 instead look ahead one link at a time, stop before moving beyond the end, and return the node on which the search stopped. There is no invocation of `last()` in the three drivers below.

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
14 |
15 |     def last(self):
16 |         iter = self
17 |         while True:
18 |             if iter.next == None:
19 |                 break
20 |             iter = iter.next
21 |         return iter
22 |
```

#### `test/fruit.py` — baseline, returned nodes assigned back to `fruit`

**00:01:00**, right pane; repeated at **00:03:00** and **00:04:00**. The 00:04:00 status bar says `Saved C:\CSCS1240F26\Item\test\fruit.py (UTF-8)`. Lines 3–5 leave `fruit` on peach, so line 7 can reach apple via banana. Line 8 is a comment; the five active prints walk out and back, printing peach only once.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
 6 |
 7 | iter = fruit.prev.prev
 8 | #iter = fruit
 9 | print( iter.object )
10 | iter = iter.next
11 | print( iter.object )
12 | iter = iter.next
13 | print( iter.object )
14 |
15 | iter = iter.prev
16 | print( iter.object )
17 | iter = iter.prev
18 | print( iter.object )
19 |
```

**Observed PowerShell · 00:07:00, first of three visible runs.**

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
apple
banana
peach
banana
apple
```

#### `test/fruit.py` — returns discarded; two `.prev` lookups

**00:05:00**, right pane; the two-lookups expression is selected in part at **00:06:00**. Lines 4–5 no longer reassign `fruit`; the rest of the numbered driver is still present. The second append is called on apple, and line 7 fails before any print. Its matching traceback is visible in full in the **00:07:00** terminal scrollback, not in the partially covered 00:05:00 terminal alone.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | iter = fruit.prev.prev
 8 | #iter = fruit
 9 | print( iter.object )
10 | iter = iter.next
11 | print( iter.object )
12 | iter = iter.next
13 | print( iter.object )
14 |
15 | iter = iter.prev
16 | print( iter.object )
17 | iter = iter.prev
18 | print( iter.object )
19 |
```

**Observed PowerShell · 00:07:00, second visible run.**

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 7, in <module>
    iter = fruit.prev.prev
           ^^^^^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'prev'
```

#### `test/fruit.py` — returns still discarded; one `.prev` lookup

**00:08:00**, right pane. The shorter line 7 successfully retrieves `None`; line 8 is still disabled. Consequently it is line 9, not line 7, that asks for an attribute on `None`. The traceback text is already visible in the **00:07:00** terminal; the later editor view makes the compatible driver state legible.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | iter = fruit.prev
 8 | #iter = fruit
 9 | print( iter.object )
10 | iter = iter.next
11 | print( iter.object )
12 | iter = iter.next
13 | print( iter.object )
14 |
15 | iter = iter.prev
16 | print( iter.object )
17 | iter = iter.prev
18 | print( iter.object )
19 |
```

**Observed PowerShell · 00:07:00, third visible run.**

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 9, in <module>
    print( iter.object )
           ^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'object'
```

**Revision and rerun evidence.** Above these runs, PowerShell says `HEAD is now at 4847342 Item: addition of last( ).` and marks `script/path.ps1` with `M`. The message locates a revision; it is not a command entered by the instructor at this moment or proof that every later editor buffer was committed. At **00:09:00** the right editor again shows `iter = fruit.prev.prev` with a modified-buffer dot, but the terminal behind it retains earlier output. At **00:10:00** another `py .\test\fruit.py` invocation is visible below the two older tracebacks without its result yet in view. The instructor reports the line-7 failure again around 00:10:02–00:10:13; **00:11:00** shows another partly covered traceback. Do not read the old line-9 message as the result of restoring the second `.prev`.

### Historical screen code: operator experiments and the restored driver

The numbers before `|` are the Sublime Text gutter, **not part of the file**. Times identify original PNG targets; e.g., 0:20:05 means `target-00-20-05.000.png`. These are separate snapshots of `test\fruit.py` unless identified as terminal text. The class beside the driver is `src\item.py`; the 0:24:00 selection shows its three class defaults, initializer, append and last-node helper. The arithmetic experiments change the driver, not those method bodies.

#### M1. Where the arithmetic first appeared (0:12:00–0:18:00)

At 0:12:00–0:14:00, neither append call assigns its return. The traversal begins at line 7 with `iter = fruit.prev.prev`; line 8 is `#iter = fruit`. At 0:15:00 the original traversal still ends at line 18. The following is the visible addition **after** the failing lookup, not two numbers printed by it:

```text
18 | print( iter.object )
19 |
20 | a = +5
21 | b = 3+5
```

The statements subsequently appear **above** the traversal and the former line 7 becomes line 10. These successive fragments retain the distinct stages of the instructor's comments; the unfinished words were not Python errors. At 0:16:00 the completion menu obscures much of the lower driver.

**0:16:00**

```text
 7 | a = +5  #unary op
 8 | b = 3+5
```

**0:17:00**

```text
 7 | a = +5  #unary operator, one operand
 8 | b = 3+5 #binary
```

**0:18:00**

```text
 7 | a = +5  #unary operator, one operand
 8 | b = 3+5 #binary operator, two operands
 9 |
10 | iter = fruit.prev.prev
11 | #iter = fruit
```

#### M2. Minus replaces plus; a complete, syntactically valid but failing driver (0:19:00; 0:22:00)

This is the full nonblank extent of the 0:22:00 editor; the same expressions are visible at 0:19:00, before the two malformed line-4 trials below. The two sign examples at lines 7–8 neither inspect nor change a node. Line 10 fails because `fruit` still refers to apple, whose `prev` is `None`; the second lookup cannot succeed. The print at line 12 is not reached in that run.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | a = -5  #unary operator, one operand
 8 | b = 3-5 #binary operator, two operands
 9 |
10 | iter = fruit.prev.prev
11 | #iter = fruit
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
```

At 0:20:00 the foreground PowerShell scrollback contains more than one execution of `py .\test\fruit.py`. The last complete traceback in that frame is:

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 10, in <module>
    iter = fruit.prev.prev
           ^^^^^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'prev'
```

Higher up are older errors on line 7 (`prev`) and line 9 (`object`) and the tail of older fruit output. These are **not** additional lines printed by the line-10 run. The line-10 traceback confirms the later failure site, not which sign glyphs had been saved when it ran.

#### M3. Remove each side of member access separately (0:20:05, 0:20:45, 0:21:00; repair at 0:22:00)

At 0:20:05 the terminal, not the editor, supplies the exact first experiment. It is a parse error at line 4, not another failure on the backward links:

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
  File "C:\CSCS1240F26\Item\test\fruit.py", line 4
    fruit.
          ^
SyntaxError: invalid syntax
```

At 0:20:45 the editor shows the complementary experiment. The terminal partly exposed behind it still shows the *trailing*-dot failure. By 0:21:00 a second `SyntaxError` is visible in the terminal strip, but its full wording and command are hidden; the instructor reports invalid syntax again. The full leading-dot source below is independently visible in the **editor**, not reconstructed from the clipped terminal.

```text
 3 | fruit = Item("apple")
 4 | .append("banana")
 5 | fruit.append("peach")
```

At 0:22:00 line 4 again reads `fruit.append("banana")` as in M2. The dot is selected. Both operands are back, but the return value still is **not** assigned. The same unassigned calls remain visible at 0:28:00; neither syntax repair is the later traversal repair.

#### M4. Restore the callers’ assignments (0:29:00)

Compared with the full M2 driver, these are the new lines at 0:29:00. The sign scratch is still the two-line minus version; traversal still starts on line 10. The assignments put the result of each append into the name used as the next receiver.

```text
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
```

Visible output below an earlier error in the terminal at 0:29:00, consistent with the instructor's account of the restored version. The invocation for these five lines is covered by the editor:

```text
apple
banana
peach
banana
apple
```

#### M5. Insert a rebinding example, then contrast a decimal (0:30:00–0:40:00)

At 0:30:00 a blank line 8 separates `a = -5` from `b = 3-5`. At 0:31:00 it contains a second assignment. The two distinct sampled states are:

**0:30:00**

```text
 7 | a = -5  #unary operator, one operand
 8 |
 9 | b = 3-5 #binary operator, two operands
```

**0:31:00; also visible at 0:36:00**

```text
 7 | a = -5  #unary operator, one operand
 8 | a = +5
 9 | b = 3-5 #binary operator, two operands
```

By 0:37:00 the second value is `+5.3`. The following complete text is visible at 0:38:00; 0:40:00 highlights `fruit.prev`, the first meaningful intermediate result at line 11. Lines 3–5 construct and link the nodes; lines 7–9 illustrate two roles of punctuation, not additional output. Lines 11–22 use the stored links. No new run of the decimal version is established by the earlier fruit output still visible behind it.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
 6 |
 7 | a = -5  #unary operator, one operand
 8 | a = +5.3
 9 | b = 3-5 #binary operator, two operands
10 |
11 | iter = fruit.prev.prev
12 | #iter = fruit
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
16 | iter = iter.next
17 | print( iter.object )
18 |
19 | iter = iter.prev
20 | print( iter.object )
21 | iter = iter.prev
22 | print( iter.object )
```

### Historical screen code: the closing revisions

Numbers to the left of `|` are editor gutter numbers, **not Python to type**. Empty numbered lines have no visible code. Indentation and spaces within statements reproduce the visual columns, without claiming invisible trailing whitespace or a particular tab encoding. Times below are requested PNG targets. Each block names a single observed state; a later variant quoted by line number is a replacement in that state, not additional code to execute in sequence.

#### `fruit.py`: reassigned returns and a full round trip (00:41:00)

**Observed editor**, `target-00-41-00.000.png`, right pane. The two assignments at 4–5 leave `fruit` at peach. Lines 7–9 are active arithmetic examples; their original comments and spacing are retained. They do not select the traversal start or print anything. Lines 11–22 rewind twice, read three values forward, then two backward. The tab has a modification dot: the five values visible from an earlier terminal run support the behavior, not a claim that this precise buffer had just run. Lines 1–22 match the 38:00 listing in M5; the 41:00 view also shows blank lines 23–24.

#### `fruit.py`: remove the assignments; change the start in stages

**Observed editor**, `target-00-43-00.000.png`, right pane. This is the complete double-`prev` state. Compare lines 4 and 5 to the preceding listing: the only caller-side record of the new end is gone. The comment on line 12 remains a comment; it cannot rescue the failing line above it.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | a = -5  #unary operator, one operand
 8 | a = +5.3
 9 | b = 3-5 #binary operator, two operands
10 |
11 | iter = fruit.prev.prev
12 | #iter = fruit
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
16 | iter = iter.next
17 | print( iter.object )
18 |
19 | iter = iter.prev
20 | print( iter.object )
21 | iter = iter.prev
22 | print( iter.object )
23 |
24 |
```

**Observed separate replacements** in the same numbered driver. The surrounding driver is readable in each named original; the excerpts below give only the changed or context lines. They are alternatives, not a stitched program.

`target-00-45-00.000.png`: one `prev`; the assignment succeeds with `None`. The first print fails at line 13.

```text
11 | iter = fruit.prev
12 | #iter = fruit
13 | print( iter.object )
```

`target-00-46-00.000.png`: no rewind. This time the code prints apple and peach. Line 17 fails before any backward move.

```text
11 | iter = fruit
12 | #iter = fruit
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
16 | iter = iter.next
17 | print( iter.object )
```

`target-00-51-00.000.png`: start at the returned tail; the modification dot is visible. At `target-00-58-00.000.png`, line 11 still shows the call, the tab instead shows its close mark, and the terminal shows one peach and a line-15 failure. The driver visible at 00:51:00 continues through line 24 exactly as in the double-`prev` listing apart from line 11; the terminal hides the lower lines at 00:58:00.

```text
11 | iter = fruit.last()
12 | #iter = fruit
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
```

#### `item.py`: an endpoint finder alongside the original append

**Observed editor**, `target-00-48-00.000.png`, `target-00-56-00.000.png`, and `target-00-59-00.000.png`, left pane. The entire listing is independently visible in each, not assembled across an insertion. Lines 2–4 provide class-level defaults; line 7 sets the new instance's `object`. The append at 9–13 attaches directly to `self` and returns its new node, whereas 15–21 follows `next` and returns an *existing* node. Neither method prints. See the full opening `src/item.py` listing (lines 1–22); these later views corroborate the same method bodies before the insertion.

#### `item.py`: one inserted line shifts the method below it

**Observed editor**, `target-00-59-20.000.png` and `target-00-59-50.000.png`; same contents and a close mark rather than the modification dot in `target-01-00-10.000.png`. The new line 10 is precisely `self.last()`, not an assignment. The loop now begins at 18 and returns at 22. Compare lines 12 and 13 with the earlier 11 and 12: neither assignment uses the returned tail.

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
10 |         self.last()
11 |         newItem = Item(obj)
12 |         newItem.prev = self
13 |         self.next = newItem
14 |         return newItem
15 |
16 |     def last(self):
17 |         iter = self
18 |         while True:
19 |             if iter.next == None:
20 |                 break
21 |             iter = iter.next
22 |         return iter
23 |
```

#### `fruit.py`: final buffer versus terminal, not a final successful program

**Observed editor**, `target-00-58-40.000.png` (changed driver start, still old class), then `target-01-00-10.000.png` and `target-01-01-00.000.png` (changed driver and changed class). Below is the right pane at **01:01:00**, where the driver's tab still shows a modification dot. The left-hand class has the inserted call, but the driver now takes its starting node directly from `fruit`.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | a = -5  #unary operator, one operand
 8 | a = +5.3
 9 | b = 3-5 #binary operator, two operands
10 |
11 | iter = fruit
12 | #iter = fruit
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
16 | iter = iter.next
17 | print( iter.object )
18 |
19 | iter = iter.prev
20 | print( iter.object )
21 | iter = iter.prev
22 | print( iter.object )
23 |
24 |
```

**Observed partial terminal at 00:58:40**, when the editor covers the right side. One source-line fragment there reads `print( iter.[remainder not visible]`. Do not fill it from the surrounding editor buffer: that buffer was being changed. The complete *traceback* statement `print( iter.object )` is independently readable in the uncovered `target-00-58-00.000.png` terminal. No class or driver line in the complete editor listings above requires a guessed suffix.

**Run boundaries.** At 00:42:00 the terminal also retains earlier, separate line-4 syntax errors showing `fruit.` and `.append("banana")`, and a line-10 `iter = fruit.prev.prev` traceback. Those are older commands, not the line-11 version or consequences of the successful five-line walk. The completed groups for the later drivers are distinguishable at 00:42:00 (line 11), 00:44:00 (line 13), 00:47:00 (apple, peach, then line 17), and 00:58:00 (peach, then line 15).

**Observed terminal, `target-01-00-00.000.png`.** Both command/result groups and the following prompt fit in the window. Neither run begins by printing apple. The shell prompt identifies `C:\CSCS1240F26\Item` as the working directory; the traceback names the test driver, not the class definition.

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
peach
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 15, in <module>
    print( iter.object )
           ^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'object'
PS C:\CSCS1240F26\Item> py .\test\fruit.py
peach
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 15, in <module>
    print( iter.object )
           ^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'object'
PS C:\CSCS1240F26\Item>
```

The pictured, unsaved direct-start driver predicts **apple, peach, then line 17**, a result also obtained by separately executing in-memory transcriptions. It is not the observed final run. At 01:01:22–01:01:33 the spoken trace enters `last` from the banana append and returns to continue the append; the instructor explicitly postpones the rest. No visible completed repair follows.

## Micro-Learning Insights

*Detachable recall sheet · CSCS1240F26, Session 18*

- **Draw names outside the nodes.** Put `fruit` on a sticky note next to apple. `fruit = fruit.append("banana")` moves that note only after the call returns. Draw the two link writes *inside* the node boxes. Moving the note and rewriting an arrow are different actions.
- **Read assignments from the right, attribute chains from the left.** In `iter = fruit.prev.prev`, first solve `(fruit.prev).prev`. If the first lookup gives `None`, you have located the object that cannot satisfy the second lookup; the assignment never finishes.
- **Ask “whose `next`?” before asking “where is banana?”** The decisive line has the same spelling on both calls: `self.next = newItem`. Changing which node supplies `self` changes which forward arrow gets replaced.
- **Name the operand, not just the punctuation.** `-5` has one operand; `3-5` has two. `+5.3` is not a lookup on `5`. The instructor’s request to avoid “just” is a useful way to catch an explanation that has skipped the operation.
- **A later traceback can mean an earlier operation succeeded.** Shorten `fruit.prev.prev` to `fruit.prev` and the assignment now succeeds. An error naming `object` instead of `prev` identifies the *next* bad lookup; it does not mean the list is fixed.
- **Stop on the node, not past it.** In `last()`, the condition inspects `iter.next` but `return iter` returns the *current* node. On apple → peach the answer is peach, not `None`.
- **A search result is not a side effect.** `self.last()` can correctly return banana yet leave `self` naming apple. If the result determines where to write, store it and use that same node for both links.
- **Test the middle and the return trip.** A chain that has lost banana can still begin at apple and end at peach. Inspect apple’s next node and peach’s previous node, not only the first and last fruits.
- **Keep three records when debugging live code:** the visible editor, the saved file that could have been run, and the terminal history. The final peach-only output does not certify the head-start code visible beside it.
- **Practise the structure independently of the fruit names.** The opening Discord post suggests other values and other sources of input; the spoken Git suggestion adds a way to save checkpoints. A commit that works and a commit with only the assignment removed make a precise experiment, not two unrelated programs.
