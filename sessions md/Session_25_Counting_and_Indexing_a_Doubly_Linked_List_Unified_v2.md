# Counting and indexing a doubly linked list

**CSCS1240F26 · Session 25 · Worked teaching companion**  
[Class recording · JoesTA · October 28, 2026](https://www.youtube.com/watch?v=eU49LGG63rM)

How many items are there, and which item is at position two? Both questions use the same links, but they count different things. With apple, banana, and grape, there are **three stored items** and it takes **two forward moves** to reach grape from apple. That distinction explains why the two methods start their counters differently, where each loop stops, and what happens when someone asks for an item beyond grape.

**Reading route.** Start with the empty-list test and the counter’s capacity. Follow the detour through brackets, parentheses, and types: it explains what the instructor wants `fruit[2]` to *mean*, before the class supports that spelling. Then trace the actual `at` method, including the failed lookup. The numbered appendix preserves the editor states separately from the teaching examples.

**Evidence.** The English auto-caption track has 1,138 cues from 00:01.040 through 54:22.280, including the sign-off. The downloaded video lasts 54:27.350. All 55 whole-minute targets (00:00–54:00), 21 additional event targets, and the 54:25 closing target yielded 1920 × 1080 frames. Times identify requested seeks, not exact decoded-frame timestamps. The final 5.070 seconds are uncaptioned; the closing sample shows the course Discord, not a further code run. Still images do not establish every keystroke, save, or execution. “Observed” below means readable class evidence; a separate Python test is not a replay of the instructor’s project.

## Count the stored items, not the links

*Class discussion, 01:04–04:26; screen: `item.py` at 02:00, 03:00 and 04:00.* After confirming that class can meet while the instructor has no court reporting date, the group picks up a question from the previous meeting. They had set out to count a doubly linked list, then paused to ask how Python knows what to do with a loop over an object. Now return to the count: if you start at apple and follow a link to banana, you have followed one link, but visited two items. Where should the counter start?

There is a wrinkle even before the first link. The class allows an **`Item`** object to exist without storing a value. In that empty state its **`_object`**, **`_prev`** and **`_next`** attributes are all **`None`**. A single apple also has no next item. So looking only for a next link cannot tell empty and one-apple lists apart. The method first checks what the first node stores, and only then counts the links it can follow. A *node* here is one object with room for a stored value and links to its neighbors.

*Annotated teaching version — guide-added comments; complete visible method in `t03m00s.png`, lines 135–146.*

```python
def count(self):                       # Count the entire list containing this node.
    iter = self.first( )               # Start at its first node, not necessarily self.
    if iter._object == None:           # Is the first node present but empty?
        count = 0                      # No stored items yet.
    else:                              # The first node already holds a value.
        count = 1                      # Count that first value, e.g. apple.
    while True:                        # Both starting cases reach this loop.
        if iter._next == None:         # No neighbor after the current node?
            break                      # Stop before following a missing link.
        iter = iter._next              # Move the local position, e.g. apple → banana.
        count += 1                     # One more stored item reached: 1 becomes 2.
    return count                       # Give the number to the caller; do not print it.
```

The instructor first describes starting at zero and incrementing on moves. The visible code makes the necessary distinction: on a nonempty list it credits the first value *before* making a move. And although the empty-case explanation says to assign zero and return it, the visible **`return count`** is shared: the empty case also tests for a next link and immediately breaks. Reassigning **`iter`** changes which node this call is looking at; it does not move apple or change either link.

*Guide-derived trace of the visible code on the driver's three inputs:* 

| Driver case | First node examined | Next-link decisions in order | Value returned |
|---|---|---|---:|
| Empty | No stored value → start at 0 | No next node; break on the first check | 0 |
| Apple | Apple → start at 1 | No next node; break on the first check | 1 |
| Apple, banana | Apple → start at 1 | Apple has a next node: move to banana, add 1. Banana has none: break on the second check. | 2 |

**Key items to scan**

- **`Item`** — the class can represent an empty list with an actual object; the object alone is not an item to count.
- **`_object`** — holds the stored value; **`None`** here distinguishes the empty starting state from one apple.
- **`_prev`** and **`_next`** — links to the previous and next nodes; this loop follows only the next link after finding the first node.
- **`iter = self.first( )`** — gives the call its own local starting position at the first node; **`iter`** is a variable here, not a call to Python's built-in function.
- **`while True`** and **`break`** — keep checking until the current node has no successor; the last node has already been counted.
- **`count += 1`** — add one only after a successful move. **`return count`** gives the total back without displaying it.

## Test empty, one, and more than one

*Class discussion, 04:29–05:11; screen: `fruit.py` and PowerShell, 04:50–05:06.* The instructor chooses three small cases that force different decisions. Empty and one apple both stop immediately, but they must give different answers. Two items force a successful move as well as a stop. The test file, or *driver*, creates the inputs and prints the answers; the counting method itself does neither.

*Annotated teaching version — guide-added comments; `fruit.py`, lines 1–11.*

```python
from item import Item           # Make the class from the item module available here.

fruit = Item( )                 # Create a new empty list; fruit refers to that object.
print( fruit.count( ) )         # Get 0 from count, then print 0.

fruit = Item( "apple" )         # Rebind fruit to a NEW list holding one apple.
print( fruit.count( ) )         # Get 1, then print 1.

fruit = Item( "apple" )         # Start over again: a different one-apple list.
fruit.append("banana")          # Change THIS list by adding banana after apple.
print( fruit.count( ) )         # Get 2 from the changed list, then print 2.
```

*Observed PowerShell scrollback, `t05m00s.png`; command and output:*

```text
PS C:\com\joesta\Item> py .\test\fruit.py
0
1
2
```

The second apple constructor matters: the third case doesn't depend on retaining the object used in the second test. Only the append in that third case changes an existing list. Seeing two gives the instructor confidence that the same step can count three, four, and more. It does not establish how many objects fit in memory, or record a million-item test.

**Key items to scan**

- **`from item import Item`** — imports the class the driver uses; it is the three explicit prints that display the totals.
- **`Item( )`** — creates the empty test case, which returns 0.
- **`Item( "apple" )`** — each call creates a fresh one-apple list; the second such call resets the third test to one item.
- **`fruit.append("banana")`** — adds banana to the current apple list; the count becomes 2.
- **`print( fruit.count( ) )`** — asks for the number first, then displays it. **`py .\test\fruit.py`** is the observed command that produces three output lines.

### The neighboring methods: what moves, and where is the position kept?

*Screen evidence, not a new narrated run: `item.py`, `t02m00s.png`, lines 106–133.* Comparing these methods with the count explains the earlier discussion of iteration. In **`forEach`**, the method delivers a value to another object's method itself. In **`__iter__`** and **`__next__`**, Python's iteration machinery can ask for values one at a time. The latter approach needs somewhere to remember the position after a call returns.

*Annotated teaching version — guide-added comments; separate methods from the same screen. Apple and banana illustrate expected behavior, not an observed callback or loop run.*

```python
def forEach(self, caller):               # caller supplies the action to perform.
    iter = self.first( )                 # Begin at the first node, e.g. apple.
    while True:                          # Process the current node before testing its link.
        caller.withItem(iter._object)    # Give caller apple, then banana on the next pass.
        if iter._next == None:           # Is this the last node (banana)?
            break                        # Leave after delivering its value.
        iter = iter._next                # Otherwise advance to the next node.

def __iter__(self):                      # Start (or restart) iteration over this list.
    head = self.first( )                 # Find the first node, e.g. apple.
    head._iter = head                    # Store apple as the next node to deliver.
    return head._iter                    # Return that node as the iterator, not the fruit value.

def __next__(self):                      # Supply one stored value per successful request.
    head = self.first( )                 # Return to where the position is kept.
    if head._iter == None:               # Is there no node left to deliver?
        raise StopIteration              # Signal exhaustion; do not return another value.
    target = head._iter._object          # Save the current value before advancing.
    head._iter = head._iter._next        # After apple, point to banana; after banana, to None.
    return target                        # Give back the saved value, not the new position.
```

For apple followed by banana, the first request returns apple and leaves the saved position at banana. The second returns banana and leaves it at **`None`**. A *third* request raises **`StopIteration`**; it neither returns nor prints a third fruit. In contrast, **`count`** uses a local position and returns just once, after the traversal is over. A separate guide fixture with two linked nodes verified the two deliveries and the third-request exception; it was not a run of the instructor's full project.

One more visible fragment shows a different use of node references: changing the list itself rather than walking through it. The enclosing method name and earlier cases are above the screen. In the visible branch, suppose the current node has a neighbor on each side. The assignments make those two neighbors point to one another, and the trailing lines disconnect the current node. This is a reading of the fragment, not an observed deletion or a reconstruction of the missing branches.

*Annotated teaching version — guide-added comments; indentation is relative to the cropped fragment, which is not standalone Python.*

```python
            else:                           # Inner alternate case; earlier tests aren't visible.
                self._prev._next = self._next  # Previous node now leads to the following node.
                self._next._prev = self._prev  # Following node now links back to the previous one.
                rest = self._prev              # Save the previous node before clearing the link.
    self._prev = None                       # Disconnect this node from its former predecessor.
    self._next = None                       # Disconnect it from its former successor.
    return rest                             # In this branch, give the caller the saved previous node.
```

**Key items to scan**

- **`forEach`** — calls **`caller.withItem(iter._object)`** once per visited node; the check for the final link comes *after* that call.
- **`__iter__`** — stores the first node in **`head._iter`**; starting iteration again resets that saved position.
- **`__next__`** — saves one fruit in **`target`**, advances the position, then returns the saved fruit.
- **`None`** — as a saved iterator position, means no next node is available; **`StopIteration`** is the exception raised on the next request.
- **`count`** — follows links with a local variable instead of updating the position saved on the head.
- **`rest`** — in the visible two-neighbor branch, preserves the previous node even after the current node's two links are cleared.

## How large could the count become?

*Class discussion, 05:11–12:38. The screen remains on the counting code; the quantities below are not terminal output.* A student asks about a million items. If a million works, why not a billion or a trillion? Trying successively larger lists never answers the general question. The instructor returns to three things to identify about a variable: its **name**, its **value**, and the **type** of that value. In the math-class example “x equals 3,” x is the name and 3 the value. In this program, the same distinction applies to the name `count` and its current integer value. Other names can refer to strings, lists, or objects of a class the programmer defines.

A student suggests an **address**. That answers a different question: *where is this particular object?* For the integer-range question, the instructor wants to know *how much storage is available and how is it organized?* One bit has two possible settings. Eight independent bits therefore provide two choices eight times over, not eight times two. The class's first suggestion of 16 gives way to 256.

| Fixed width discussed | Number of distinct bit patterns (guide calculation) | How to read it |
|---:|---:|---|
| 8 bits | 2⁸ = 256 | If the numbering starts at 0, the last value is 255. |
| 16 bits | 2¹⁶ = 65,536 | Eight more bits multiply the number of possibilities by 256. |
| 32 bits | 2³² = 4,294,967,296 | Whether that is “large” depends on what one must count. |
| 64 bits | 2⁶⁴ = 18,446,744,073,709,551,616 | Still a finite set if the width is fixed. |

The number of patterns is not the largest represented number. The instructor says to remember zero, but also says “0 to 256”: **256 patterns** numbered consecutively from zero cover **0 through 255**, not 256. You could instead assign some patterns to negative numbers. The mapping changes what the patterns mean, not how many there are. The bank-account versus Amazon-transactions example makes the practical point: the same capacity can be generous for one application and inadequate for another.

The instructor contrasts common fixed-width integer types in C, C++, and Java with Python integers: Python can use more bits when the value grows. That flexibility has overhead; the lesson shows no timing benchmark or largest feasible list. Even if the integer can represent the count, the separate list nodes still have to fit somewhere and the method still has to visit them. Arrays and stacks come up as possible *later* settings for thinking about how to represent numbers of arbitrary size, not as implementations shown here.

**Key items to scan**

- **Name, value, type** — `count` names a value such as 2; its integer type matters when asking what larger values can be represented.
- **Address** — identifies where a particular object is stored, not how many patterns a fixed number of bits can encode.
- **256 patterns** — eight bits; **0 through 255** if the patterns encode consecutive unsigned integers.
- **Fixed-width integer** — adding one more distinct value beyond the allotted patterns requires a different representation; doubling the number of bits does not just double the number of patterns.
- **Python integer** — can use more bits as needed; this is not evidence that a billion-node list was built or traversed in class.

## Moving from counting to a position

*Screen: `t05m00s.png` and `t12m50s.png`; speech: 12:50–12:58.* The instructor uses Git revisions to move between examples in the same files. The word **`HEAD`** in PowerShell means the checked-out Git revision, not the first node of the fruit list. Two earlier checkouts in the scrollback explain why both method names appear even though the visible driver prints only counts:

| Screen action in order | Effect reported in PowerShell |
|---|---|
| `git checkout master~10` | `HEAD is now at 91be848 at(index)` |
| `git checkout master~11` | `HEAD is now at 3907fbe count` |
| `py .\test\fruit.py` | Prints `0`, `1`, `2` on successive lines in the count revision. |

Here **`master~10`** names the ancestor ten first-parent steps behind `master`; it is not a new branch named “version 10.” At 12:50 the class is about to return to the other revision. The last prompt visible in that frame is still below `0`, `1`, `2`, with no new command entered. In the following captions the instructor says the checkout is to “version 10” and identifies the commit message. This is the transition from *how many items are there?* to *what might a method named `at` do with an index?* By the 13:00 frame a *new* `git checkout master~10` appears below the count output and reports `HEAD is now at 91be848 at(index)`. The right editor still displays the count driver; the checkout did not turn its old results into an index test.

The terminal also offers a way to retain any new work done while looking at an older commit: **`git switch -c <new-branch-name>`** would create a branch there. It also offers **`git switch -`** to switch back. These are Git's suggestions, not two more completed classroom commands.

**Key items to scan**

- **`HEAD`** — Git's currently checked-out revision; read the status line after a command to see which version is active.
- **`git checkout master~11`** — the earlier return to **`3907fbe count`** precedes the observed count run.
- **`master~10`** — the revision whose status identifies **`91be848 at(index)`**; the instructor announces a return to it at the end of the discussion.
- **`git switch -c <new-branch-name>`** — suggested way to create a branch at the checked-out commit; **`git switch -`** is the separate suggestion for switching back.

## What would it mean to ask for the fruit *at* a position? (13:00–20:50)

The instructor starts with a name rather than an implementation. The commit message **`at(index)`** invites a prediction: if someone asked you to implement this method, what should a caller get? A student suggests that you are “calling something.” True, but that explains the act of calling, not what the call means. In an email address, *at* relates a user to a domain. A book’s index or a library location number points you toward something you want to find. For this class, the more specific clue is the earlier use of *index*: a position in a Python list.

Consider the three colors in the editor. The brackets on the right side of the first assignment enclose the items of a **list literal**: the program gives Python the values directly. In **`color[1]`**, by contrast, the list already exists; the brackets ask for one of its items. Counting starts at zero, so asking for position one selects the **second** color. The bare expression in the earlier editor state becomes an assignment to **`fav`** in the later one.

*Annotated teaching version — guide-added comments; color literal and selection from `t18m00s.png`, assignment from `t20m45s.png`.*

```python
color = ['red', 'white', "blue"]  # Create a new three-item list in this order.
color[1]                         # Earlier scratch expression: evaluates to 'white'.
```

```python
fav = color[1]                   # Later version: bind fav to 'white'; color is unchanged.
```

*Trace of the positions, independently checked with the displayed literal:* index 0 → `'red'`; index 1 → `'white'`; index 2 → `'blue'`. Neither a bare expression in a script nor an assignment prints those strings. No color output is shown in the classroom frames.

**Key items to scan**

- **`at(index)`** — the commit message suggests a lookup by numbered position; it is not the literal Python signature.
- **List literal** — `['red', 'white', "blue"]` creates the three-item example. The different quote styles still enclose strings.
- **`color[1]`** — reads the second value, `'white'`; it makes no change to the list.
- **`fav`** — the name the editor uses to keep the selected color for later use.

The custom doubly linked list poses a different question. It already lets you start at the first or last item and move to a neighboring item in either direction. A student asks whether the list can therefore *already* do the job. Yes, if the caller performs the navigation; no, if the caller wants to ask for a number and let the list do the work. The instructor prefers thinking in links rather than positions, but proposes **`at`** as a *convenience method*: start at the first item and count forward moves until the count equals the requested index. Adding a numeric interface need not change how the items are linked.

The left editor shows why the counter does not start at one. An apple at the front already counts as **one item**, but finding it takes **zero moves**. The method’s actual parameter is **`n`**, not the word *index* used in the commit message.

*Annotated teaching version — beginning of the method visible in `item.py` at `t16m35s.png`, guide-added comments. The rest is below this frame.*

```python
def at(self, n):                  # n is the requested position in this custom list.
    iter = self.first( )          # Begin at the first node, regardless of the starting node.
    if iter._object != None:      # A populated first node has a stored object, such as apple.
        count = 0                # Already at position 0; no link has been followed yet.
```

*Guide-derived trace of that proposal, using the populated apple/banana/grape example in `t16m35s.png`; this is a reasoning trace, not a captured run.*

| Position requested | Start | Moves from first | Item reached |
|---|---|---|---|
| 0 | apple | Zero moves | apple (first) |
| 1 | apple | apple → banana | banana (second) |
| 2 | apple | apple → banana → grape | grape (third) |

The distinction between *third item* and *index three* matters. Two moves from the first item reach the third; index three would ask for a fourth. The edited line **`lunch = fruit[2]`** expresses the interface the instructor *wants*, analogous to the color lookup. It does not demonstrate that this custom type already supports brackets. Nor is it a run showing that lunch becomes grape: the editor has a fresh empty `Item( )` assignment above the scratch line. The trace describes the populated example, not an execution of that whole buffer.

*Annotated teaching version — observed scratch statement, guide-added comment (`t20m45s.png`).*

```python
lunch = fruit[2]                 # Proposed spelling: ask for the item at index 2.
```

**Key items to scan**

- **`at`** — packages “start at first and follow links” into one request; the left editor shows `def at(self, n):`, with `n` as the position parameter.
- **`n`**, **`count = 0`** — the destination and the starting position. A populated first node represents one item but zero moves from the front.
- **Index 2** — the third item, two forward links from apple; not three forward moves.
- **`lunch = fruit[2]`** — desired custom-list syntax, not evidence that bracket lookup was implemented or executed.

## Read punctuation by what it does (20:53–24:42)

When a student suggests that the brackets are an argument, the instructor separates the marks from the data. In **`color[1]`**, **`[]`** signals a subscription—select something from the object on the left—and the **operand** inside is the index, `1`. In a written method call, the value supplied inside the call parentheses is an **argument**. These are two ways of asking for work to be done, not interchangeable marks. The question here is how to read the notation; it is not a claim that an object’s methods can never implement an operator.

Addition makes the distinction visible. In **`n = 2 + 3`**, Python recognizes **`+`** as the instruction to add and the two numbers as what to add. It is a **binary operator** because it uses two operands; **infix notation** puts the operator between them. Thus `n+m` reads as two names with an operation between them, not one identifier containing a plus sign. The instructor mentions prefix and postfix as possible future topics, not further demonstrations.

*Annotated teaching version — completed editor line in `t23m00s.png`, guide-added comment.*

```python
n = 2 + 3                        # Evaluate the right side to 5, then bind n to 5.
```

*Independently checked:* the sum is `5`. The assignment does not print. An earlier frame catches an unfinished `n ==` (`t22m15s.png`); the completed addition uses one equals sign, not two. **`=`** assigns, while **`==`** asks whether two values compare equal. The instructor also recalls subtraction and multiplication, then points out what is unusual about subscription: its opening and closing brackets are two separate characters marking one operation. Parentheses also come in a matching pair, but they do not mean the same thing as brackets.

**Key items to scan**

- **`color[1]`**, **`[]`** — subscription of the existing color list by position; the two brackets act together.
- **Operand** — a value an operation works on, such as the index `1` or the two numbers in `2 + 3`.
- **Argument** — a value supplied in a call, as in the fruit driver’s `fruit.append("banana")`.
- **`n = 2 + 3`**, **`+`** — add first, then store the result. A plus sign cannot be part of a single Python identifier.
- **Binary operator**, **infix notation** — two operands; the addition sign sits between them.
- **`=`**, **`==`** — storing a value and comparing values are different operations.

## Same two numbers, different punctuation (24:43–31:30)

A question about swapping brackets and parentheses leads to the class’s “four-letter word” hint: **type**. If `2` and `3` appear inside parentheses, are they arguments to a function? They could be if those parentheses followed something being called. In the driver, **`fruit.append("banana")`** calls a method with one argument; its parentheses belong to the call. After an equals sign, **`(2 + 3)`** has no function in front of it. Here the parentheses group an expression. To see why grouping matters, the instructor adds a multiplication by four and asks whether removing the parentheses will make Python fail. It changes the calculation, not its validity.

*Annotated teaching version — separate alternatives for the same line, not three lines executed by the instructor. Grouped states are visible at `t27m20s.png` and `t28m00s.png`; the ungrouped form is the complete alternative discussed aloud.*

```python
r = (2 + 3)                      # One addition produces the integer 5.
```

```python
r = (2 + 3) * 4                  # Group first: 5 times 4 produces 20.
```

```python
r = 2 + 3 * 4                    # Multiply first: 2 plus 12 produces 14.
```

*Execution order, independently checked:* grouped → add → multiply → `20`; ungrouped → multiply → add → `14`. This ordering is **precedence**. The class supplied `14` and `20` orally; neither is printed by these statements. The 28:18 frame is mid-edit, not a completed execution of the ungrouped form.

**Key items to scan**

- **Type** — ask what kind of value an expression creates, not just which marks surround it.
- **`fruit.append("banana")`** — parentheses supply an argument to a named method.
- **`(2 + 3)`** — parentheses group the addition; its result is a single integer, `5`.
- **Precedence** — in `2 + 3 * 4`, multiplication goes first; in `(2 + 3) * 4`, the grouped addition goes first.

The instructor next asks for the most literal difference between the neighboring editor lines. One has a plus sign; one has a comma. Addition combines the two numbers into one number. A comma keeps the values separate: in **`m = (2, 3)`** they become one **tuple** with two entries. In **`p = [2, 3]`**, the brackets create one **list** with the same two entries in the same order. The instructor calls the comma a “separation” operation as a reading aid. The comma in this tuple expression, not parentheses by themselves, distinguishes the pair from the grouped sum.

*Annotated teaching version — the three neighboring expressions in `t30m00s.png`, guide-added comments.*

```python
r = (2 + 3)                      # Add: r refers to one integer, 5.
m = (2, 3)                       # Keep both values: m refers to the tuple (2, 3).
p = [2, 3]                       # Keep both values in a new list, [2, 3].
```

*Expected state:* one scalar, one tuple, one list; no printed output. The choice between the two collections becomes meaningful when a caller tries to replace an item.

**Key items to scan**

- **`m = (2, 3)`**, **tuple** — stores two ordered entries; the comma is not addition.
- **`p = [2, 3]`**, **list** — stores two ordered entries in a collection whose items can be replaced.

## Which collection permits the operation? (31:31–35:59)

Start with the same second item, `3`, in the list and the tuple. If you ask Python to replace it with `6`, does it change, stay as it was, or signal that the request is not allowed? **Mutability** answers whether the existing collection can be changed. It is distinct from whether a name can later be made to refer to a different collection. The editor uses an indexed assignment in both cases, so the difference cannot be explained by the index or the replacement value.

*Annotated teaching version — list and tuple setup and assignments visible at `t33m30s.png`, guide-added comments. Standalone excerpt, not execution of the whole fruit driver.*

```python
m = (2, 3)                       # Create a tuple; its second item is 3.
p = [2, 3]                       # Create a separate list; its second item is also 3.
p[1] = 6                         # Replace the list's 3 with 6 in place.
m[1] = 6                         # Attempt the same replacement; Python raises TypeError.
```

| Operation | Before | Result, independently checked | State afterward |
|---|---|---|---|
| **`p[1] = 6`** | `[2, 3]` | Replacement succeeds; nothing printed | The same list is `[2, 6]` |
| **`m[1] = 6`** | `(2, 3)` | Raises **`TypeError`**; not a silent no-op | The tuple remains `(2, 3)` |

*Independent Python exception, not classroom terminal output:*

```text
TypeError: 'tuple' object does not support item assignment
```

The frame still shows the original list literal `[2, 3]`: executing an assignment would change an object in memory, not rewrite the source text. The instructor explicitly predicts an exception rather than a silent refusal. No classroom traceback for this line is shown.

**Key items to scan**

- **Mutability** — the list can change in place; the tuple’s item slots cannot be reassigned.
- **`p[1] = 6`** — index 1 is the second slot; only the list’s `3` is replaced.
- **`m[1] = 6`**, **`TypeError`** — the corresponding tuple request raises an exception and leaves its contents unchanged.

The class separates three questions about a group of values: Does order matter? Must the entries be unique? Can they be changed? A list and a tuple both preserve position and both permit duplicates. It is the third question, replacement, that separates them in this example. A student suggests dictionaries when the instructor asks about uniqueness. The relevant distinction for a **dictionary** is that its *keys* are unique; values assigned to different keys may be equal. The instructor eventually gives away the other type by explaining why he carefully said “group of values” rather than “set of values”: a **set** has distinct members, not numbered positions. No set or dictionary literal is typed in the sampled editor.

*Separately tested illustration — not classroom code; guide-added comments.*

```python
members = {2, 2, 3}              # Only two distinct members: 2 and 3.
labels = {'first': 2, 'second': 2}  # Two distinct keys may both map to 2.
```

*Independent checks:* the set has two members and compares equal to `{3, 2}`; the dictionary has two entries. Neither assignment prints. This brings the review back to its starting point: choosing an ordinal position makes sense for an ordered list of fruit. The next step is to see what the custom method returns for such a request.

**Key items to scan**

- **Order** — list and tuple positions matter; the second element does not become the first just because the values are unchanged.
- **Uniqueness** — repeated items are allowed in lists and tuples; a **set** keeps only distinct members.
- **Dictionary** — uniqueness applies to keys, not necessarily to the values stored under different keys.

## Looking up a fruit by position: what comes back?

Apple, banana, grape: which of those should position zero give us? The class wants the **first item**, but the driver asks for two things in succession. **`fruit.at(0)`** should find the *item* at that position. **`.object()`** should then read the value stored in that item. Only the outer **`print()`** displays the value. If the lookup returned the string `apple` itself, the next call would be asking that string for the course class’s accessor. The way the caller is written tells us something about what the method must return. (35:43–38:10; driver visible at 36:00 and 36:28.)

*Annotated teaching version — guide-added comments; setup and first lookup from the driver.*

```python
from item import Item                  # Bring the linked-item class into this file.
fruit = Item( "apple" )                # Create a new item containing apple; name it fruit.
fruit.append("banana")                 # Add banana after apple in this same list.
fruit.append("grape")                  # Add grape last; the positions are now 0, 1, 2.
# Inside the driver's try block:
print( fruit.at( 0 ).object( ) )       # Get the apple item, read its value, expect to print apple.
```

Position 11 is a different question. A long enough list could have that position; this three-item list does not. The instructor compares it with a built-in list of colors: change the requested index from 1 to 11 and ask what Python *would* do. The lesson borrows the idea of signaling an invalid request, not Python’s exact built-in exception class. For this built-in list, requesting index 11 would raise `IndexError`; the custom method instead explicitly raises the base `Exception` class. No classroom traceback for the color example is shown. (38:10–39:24; 36:00 vs. 39:00/39:10.)

*Annotated teaching version — guide-added comments; successive scratch states, not successive executions.*

```python
color = ['red', 'white', "blue"]      # Three values: there is no position 11.
fav = color[1]                        # Earlier buffer: second color, white; assignment does not print.
```

```python
fav = color[11]                       # Replacement line: if reached, lookup fails before assignment.
```

**Key items to scan**

- **First item** — zero-based position 0 is apple; the third position in everyday speech is index 2, grape.
- **`fruit.at(0)`** — asks the custom list for the first linked item, not just its contents.
- **`.object()`** — reads the stored value from that returned item.
- **`print()`** — displays a value only after its argument has been evaluated successfully.
- **`color[1]`** — selects white; **`color[11]`** is outside this three-color list.
- **`Exception`** — the custom class uses a general exception for a missing forward position; the built-in comparison motivates the choice to raise.

## One handler for four requests

Before looking inside the method, read the driver as a prediction. The four lookups share **one** **`try`** block, not four independent tests. The first lookup can display apple, but the second cannot supply an item. Control transfers to **`except Exception as e`**; it does not come back and try the remaining two lines. Execution can continue *after the handler*. This resolves the class question about whether the program can go on: yes, but not by resuming at the third print inside the interrupted block. (39:21–40:56; 40:00, 41:00.)

*Annotated teaching version — guide-added comments; same three-item setup as above.*

```python
try:                                  # A failure in any of these four statements leaves the block.
    print( fruit.at( 0 ).object( ) )  # Expect apple to print before any failure.
    print( fruit.at( 11 ).object( ) ) # at raises; neither object() nor this print runs.
    print( fruit.at( 2 ).object( ) )  # Skipped here, though position 2 exists.
    print( fruit.at( 3 ).object( ) )  # Also skipped here; not a second observed exception.
except Exception as e:                # Catch the first matching exception and name it e.
    print( e )                        # Expect just its message: Index beyond end.

fruit = Item( )                       # Start over: fruit now names a NEW empty item, not the apple list.
print( fruit.at( 0 ).object( ) )      # Expect None: the empty item is returned, then its value is read.
```

**Expected displayed lines through the empty-item print** (driver lines 1–15). A separate teaching check using the transcribed `at` method and minimal stand-ins for list construction and access reproduced these lines; this is not classroom terminal output or a test of the full project:

```text
apple
Index beyond end.
None
```

**Key items to scan**

- **`try`** — covers the four nonempty-list requests in the order 0, 11, 2, 3.
- **`except Exception as e`** — takes control on the failure at 11; positions 2 and 3 are not evaluated in this sequence.
- **`print( e )`** — displays the message from the caught exception, not an entire uncaught traceback.
- **`fruit = Item( )`** — rebinds the name after the handler; does not empty the earlier three-item chain.
- **`None`** — the empty item's stored value, not a missing item returned by this successful lookup.

## Inside `at`: stop at the requested item, not necessarily the tail

A caller might hold the banana item rather than the apple item. The method therefore starts by following the list back to its first item. The instructor describes **`first()`** as following previous links until there is no predecessor. The local name **`iter`** is the instructor’s choice for the item currently being examined; this code moves it explicitly rather than calling the class’s iterator protocol. No fruit is added, removed, or overwritten. (41:15–43:25; complete method visible at 42:00.)

*Annotated teaching version — guide-added comments; method of `Item`.*

```python
def at(self, n):                           # n is the requested position; self can be any item in the chain.
    iter = self.first( )                   # Start at apple, even if the caller held banana.
    if iter._object != None:               # Enter only if the first item stores a value.
        count = 0                          # Apple is already position 0; no links traversed yet.
        while count < n:                   # Continue only while the requested position is ahead.
            if iter._next == None:         # Need another link, but the current item is the last one.
                iter = None                # Clear the local name; does not delete the last item.
                raise Exception( "Index beyond end." )  # Leave the method; do not return None.
            iter = iter._next              # Only on success: move to the next item.
            count += 1                     # Its position is one higher; check the loop again.
    return iter                            # Give back the reached item (also the empty item if guard failed).
```

Two checks involving **`None`** answer different questions. **`iter._object != None`** asks whether the first item contains anything. The instructor briefly reverses the reading, then corrects it: `apple` is not `None`, so enter the search; an empty item skips the entire search and reaches the return. The single leading underscore marks an internal attribute in this class; it is not what tests for emptiness. **`iter._next == None`** asks about a *link*, not a stored value. A grape with no successor is still a perfectly valid result at index 2. Its missing link matters only if the loop asks to move farther. (41:40–43:18, 47:02–48:26.)

Why not scan the whole list and remember the matching item? Once the count equals the requested index, the local name already points to the answer. **`while count < n`** avoids unnecessary moves and does not even enter the loop for zero. To reach the **`raise`**, the method must have been called, the first item must contain a value, another move must be required, and the current item must have no successor. Setting the local name to `None` immediately beforehand is not a return. The exception leaves this call, bypassing the move, increment, and return. The instructor compares this with **`break`** in the count loop: both interrupt ordinary progression, but `break` exits its loop and continues within the method; a raise searches for a matching handler, potentially all the way back to the command line. (43:27–46:55.)

**Key items to scan**

- **`first()`** — returns the first item so positions are measured from the list’s start, not from the caller’s location.
- **`iter`** — the current item; assigning a new value to this local name does not change the links.
- **`None`** — can mark an empty item’s contents or an absent link; inspect which attribute is being compared.
- **`iter._object != None`** — an empty first item skips the search and is returned as an item.
- **`while count < n`** — keep moving only while the desired position is farther ahead.
- **`iter._next == None`** — if another move is required here, the request is out of range.
- **`raise`** — sends `Exception("Index beyond end.")` to the caller; the normal return is not reached.
- **`break`** — by contrast, the earlier count loop can stop and still execute its following return.
- **`count += 1`** — record one successful move, not one attempted move.

## Trace the positions 0, 2, 3, and 11

Keep the current item and the number of links already followed beside each other: apple/0, banana/1, grape/2. The table traces **separate calls on the same nonempty chain**, starting from the first item each time. It follows the displayed code and the class’s walkthrough; it is not a transcript of four terminal runs. In particular, the instructor walks through positions 2 and 3 that the displayed driver's shared handler prevents it from reaching. (48:24–51:31.)

| Requested position | Each loop check and the move it permits | Result of the call; display only if a caller prints |
|---|---|---|
| 0 | Apple/0: `0 < 0` is false. No link is inspected. | Return the apple item. Reading and printing its value displays `apple`. |
| 2 | Apple/0: `0 < 2`, move to banana/1. Banana/1: `1 < 2`, move to grape/2. Grape/2: `2 < 2` is false. | Return the grape item. Its value would display as `grape` in an independent print; skipped in the driver. |
| 3 | Apple/0: `0 < 3`, move to banana/1. Banana/1: `1 < 3`, move to grape/2. Grape/2: `2 < 3`, but there is no next item. | Set the local item to `None` and raise. Count never becomes 3. No item or fruit value is returned. |
| 11 | Apple/0: `0 < 11`, move to banana/1. Banana/1: `1 < 11`, move to grape/2. Grape/2: `2 < 11`, but there is no next item. | Same exception, **at grape**, not after eleven iterations. The driver’s handler would print `Index beyond end.` |

**Key items to scan**

- **Index 0** — zero moves, one existing item returned.
- **Index 2** — two moves bring the search to grape; the loop stops before checking for a successor.
- **Index 3** — a third move is needed but impossible; being *at* the tail differs from trying to move *past* it.
- **Index 11** — fails at the same missing link as 3; the method does not need to know the list’s total length first.

A useful limit of the analogy: in this revision an empty `Item` skips the index test altogether, and a negative `n` on a populated list makes `0 < n` false immediately. Separate fixture checks confirmed that `empty.at(11)` returns that same empty node and `fruit.at(-1)` returns apple, **not** the last fruit. Those are consequences of the code, not promised features or classroom runs.

## The next revision is visible; the next lesson is not

The closing choice was between inspecting a membership search and giving this class bracket syntax. In PowerShell, **`git checkout master~9`** actually switches from **`91be848 at(index)`** to **`0ec6bc1 has(object)`** (52:38). The old `0`, `1`, `2` above that command belong to an earlier run of the **count** revision, not to `at`. At 53:00 and 53:15 the next method is visible below `at`; the class does not walk through or run it in this interval. Read its visible start as a change of question: instead of asking for *position 2*, it compares each stored value to a requested object.

*Annotated teaching version — guide-added comments; visible `has` prefix only.*

```python
def has(self, obj):                 # obj is the value being sought, not an index.
    if self._object != None:        # Test the receiver’s stored value before starting a search.
        iter = self.first( )        # For a nonempty receiver, begin at the first item.
        while iter != None:         # Examine each item while there is one to examine.
            if iter._object == obj: # Compare this item's contents with the requested value.
                break               # Stop scanning on a match; not itself a return statement.
            iter = iter._next       # Otherwise advance toward the tail.
```

*Result boundary:* the remainder is below the visible editor pane. Neither a return statement nor a displayed search result is established by this fragment.

The alternate direction was **operator overloading**: define what an existing operator means for this class. The right-hand buffer already contained a line illustrating the desired spelling. Writing that line is not the same as implementing subscription. The instructor says the current class does not support that operation; no bracket traceback or working custom bracket implementation is visible. In the displayed driver this line is *after* the empty-item reset and *outside* the handler. If it fails as predicted, the later color and tuple scratch lines are never reached in that run. (51:46–53:23.)

*Annotated teaching version — guide-added comment; proposed use visible in the driver.*

```python
lunch = fruit[2]                    # Ask by bracket notation; instructor predicts an error, not an assignment.
```

**Key items to scan**

- **`git checkout master~9`** — the observed command that exposes the subsequent revision.
- **`91be848 at(index)`** — the checkpoint just left; its position lookup was the lesson’s worked method.
- **`0ec6bc1 has(object)`** — the new checkpoint; the visible search compares stored objects rather than following a requested number of links.
- **`has(self, obj)`** — only the search prefix is visible here; its final return is off-screen.
- **Operator overloading** — the proposed way to give the custom class a meaning for an operator; not implemented in the demonstrated bracket example.
- **`fruit[2]`** — desired subscription syntax; unlike `fruit.at(2)`, it cannot rely merely on the existence of an `at` method.

## Syntax to remember

| Pattern | Read it this way in this lesson |
|---|---|
| `iter = self.first( )` | Start this search at the first node, even when the method was called on another node. This local name holds one node at a time. |
| `iter = iter._next` | Look up the next node using the **old** local reference; then make the local name refer to that successor. This does not change a link. |
| `count += 1` | In `count()`, credit another stored item; in `at()`, record one forward move. Same arithmetic, different quantity. |
| `fruit.at(2).object( )` | First get the node at position two; then ask that node for its stored value. This expression alone is not a print statement. |
| `except Exception as e:` | After a matching failure, bind the exception object to `e`. `print(e)` displays its message; it does not resume the failed loop. |
| `color[1]` versus `[2, 3]` | Following a collection, brackets ask for an item. Standing alone here, brackets construct a list. |
| `(2 + 3)` versus `(2, 3)` | The former groups one arithmetic expression. The comma in the latter separates the elements of a tuple. |

## Knowledge check

1. An empty `Item()` and a one-apple `Item` each have no next node. Why don’t they both count as one? Does the empty case return immediately in the displayed code?
2. A student argues that an eight-bit counter can store all the integers from 0 through 256. What is the fencepost error? Does a Python `int` have that cutoff?
3. With apple → banana → grape, begin **at banana** and call `at(2)`. Which node does the search start from, how many forward moves does it make, and what must the caller do to *display* `grape`?
4. One `try` block prints the results for positions 0, 11, 2, and 3, in that order. Predict which of those calls actually run. What changes if 2 is tested separately?
5. A student says that removing the parentheses from `(2 + 3) * 4` is a syntax error, and that `m[1] = 6` always works if `m` contains two numbers. Correct both predictions.
6. Why can the course class have a working `at(2)` method while `fruit[2]` still fails? Which part was left for another class?

### Answer key

1. The method inspects the first node’s `_object`. A vacant node starts the counter at **0**; a node already holding apple starts it at **1**. Both still enter `while True`. With no successor both break on the first check, then return the counter. The difference was established *before* the loop.
2. Eight bits yield **256 distinct patterns**. Starting at zero uses one of them, so the largest ordinary unsigned value is **255**. Python integers can grow as needed; that does not give the process unlimited time or memory.
3. `self.first( )` follows backward links to **apple**. Two forward steps reach banana and then grape. The result of `at(2)` is the **grape node**, not a string or printed line. `print(fruit.at(2).object())` retrieves the string and displays it.
4. Position **0** succeeds. At **11**, grape has no successor; `raise` transfers control to the handler. Neither `.object()` nor `print()` finishes for that expression, and the remaining calls **in the same try** are skipped. The handler prints `Index beyond end.` A separate call for **2** can return the grape node normally; an independent call for **3** reaches the same end condition as 11.
5. Both arithmetic expressions are valid: grouped addition gives **20**; multiplication first gives **14**. With `[2, 3]`, replacing index 1 yields `[2, 6]`. With `(2, 3)`, the same assignment raises `TypeError`; the tuple’s entries cannot be replaced.
6. A method name does not automatically define what Python’s subscription syntax means. `at` is the explicit method in this revision. Giving the class bracket behavior was a proposed next step, not an implementation demonstrated here.

## Library of definitions

| Term | Meaning in the classroom example |
|---|---|
| Doubly linked list; node | Each `Item` can store a value and refer to a previous and next item. Apple, banana, and grape reside in nodes, not in three numbered slots of the `Item` class. |
| Head; empty representation | The head is the first node. Even an empty course list has an `Item` object; its stored value and links are `None`. A missing successor alone only identifies the end. |
| Reference; rebinding; mutation | A reference identifies an object. Assigning `iter = iter._next` changes what the name denotes; appending banana changes the linked structure. Starting a fresh `fruit = Item(...)` reuses the name, not the previous structure. |
| Traversal; iterator | Traversal means moving along the links. The earlier iterator protocol supports asking for successive values. The search here uses its own local position; naming it `iter` is not a call to Python’s built-in `iter()`. |
| Count; zero-based index | Count is the number of elements. The index is the number of forward steps from the first element: grape is the third item but has index two. |
| Parameter; argument; return value | `n` is the parameter in the method definition; `2` is an argument at a call. The node supplied back to the caller is the return value of `at(2)`. |
| Operator; operand; infix; precedence | In `2 + 3`, `+` is the operator, 2 and 3 are operands, and infix means *between* them. Precedence explains why multiplication groups first in `2 + 3 * 4`. |
| Subscription; operator overloading | `color[1]` asks a collection for the item associated with the key or index 1. Overloading lets a type supply its own behavior for supported syntax. It does not give a linked list random access for free. |
| Type; bit width | A type determines supported operations and representation. A bit width counts binary positions available in a fixed-width representation, not the largest value directly. |
| Ordered; mutable; unique | These answer three different questions: do positions matter, can entries change, and are repeated elements permitted? A list and a tuple both keep order and permit duplicates; only the list permits replacing a slot. |
| Exception; handler; propagation | `raise` signals failure rather than returning a result. Control leaves the failing code and goes to a matching handler; if none handles it, the failure reaches the calling environment. |
| Revision; checkout; `HEAD` | A revision is a recorded project state. A checkout can switch which revision is being examined; `HEAD` identifies the checked-out position. Old terminal lines do not change when the file on screen changes. |

**Supplemental Python references.** The class supplies the examples; these language references clarify terminology and behavior rather than reconstructing the recording: [built-in types: integers, lists, tuples, sets and mappings](https://docs.python.org/3/builtins/stdtypes.html) [1]; [expressions: parentheses, calls and subscriptions](https://docs.python.org/3/reference/expressions.html) [2]; [handling exceptions and skipping the rest of a `try` clause](https://docs.python.org/3/tutorial/errors.html) [3].

## Historical appendix · numbered screen code

### The count revision: `item.py` and `fruit.py`

**Historical screen code.** The numbers before `│` are the editor gutter, not Python to type. These are listings of visible buffers, not a copy of the whole repository. Indentation is represented with spaces at the displayed depth. No instructional comments have been inserted.

**`item.py`, lines 106–147 — assembled visual reconstruction.** `t02m00s.png` shows lines 106–140; `t03m00s.png` supplies the continuation through 147. The overlapping `__iter__`, `__next__` and start of `count` agree; `t04m00s.png` independently shows the end of the method. The header and preceding branches of the method ending at 112 are above the screen, not recoverable here. No intervening save or change to unseen lines can be established from the stills.

```text
106 │                else:
107 │                    self._prev._next = self._next
108 │                    self._next._prev = self._prev
109 │                    rest = self._prev
110 │        self._prev = None
111 │        self._next = None
112 │        return rest
113 │
114 │    def forEach(self, caller):
115 │        iter = self.first( )
116 │        while True:
117 │            caller.withItem(iter._object)
118 │            if iter._next == None:
119 │                break
120 │            iter = iter._next
121 │
122 │    def __iter__(self):
123 │        head = self.first( )
124 │        head._iter = head
125 │        return head._iter
126 │
127 │    def __next__(self):
128 │        head = self.first( )
129 │        if head._iter == None:
130 │            raise StopIteration
131 │        target = head._iter._object
132 │        head._iter = head._iter._next
133 │        return target
134 │
135 │    def count(self):
136 │        iter = self.first( )
137 │        if iter._object == None:
138 │            count = 0
139 │        else:
140 │            count = 1
141 │        while True:
142 │            if iter._next == None:
143 │                break
144 │            iter = iter._next
145 │            count += 1
146 │        return count
147 │
```

- **106–112:** In the legible alternate branch, the previous and next nodes are joined in both directions. The branch saves the previous node in `rest`; the shared tail clears this node's links and returns `rest`. The name of the enclosing method and other possible `rest` assignments are not shown.
- **114–120:** `forEach` calls `caller.withItem` with the current node's stored value, including on the last visit. It tests for a successor *after* the call; no separate empty-value guard is visible in this method.
- **122–133:** `__iter__` puts the first node into its own `_iter` field. `__next__` looks up that shared position each time, saves the current value, advances the field and returns the saved value. An exhausted request raises rather than returning. No output for these helpers is shown in this run.
- **135–146:** `count` starts at zero for the empty placeholder, otherwise one for the first stored item. Each successful move earns one increment. Both branches reach the same `while` and the same `return`.

**`fruit.py`, lines 1–12 — directly observed, `t04m50s.png`; compare `t02m00s.png`.** The first two constructors set up the empty and singleton tests. The third constructor starts a different singleton list before appending banana, so the three printed counts are independent checks.

```text
 1 │from item import Item
 2 │
 3 │fruit = Item( )
 4 │print( fruit.count( ) )
 5 │
 6 │fruit = Item( "apple" )
 7 │print( fruit.count( ) )
 8 │
 9 │fruit = Item( "apple" )
10 │fruit.append("banana")
11 │print( fruit.count( ) )
12 │
```

**Run evidence.** At 05:00 the recorded `py .\test\fruit.py` invocation prints `0`, `1`, `2` at `3907fbe count`. The longer shell transcript at the end of this appendix preserves that run alongside the later checkouts; the initial `HEAD` status line is already on screen, not an independently witnessed command.

### From count driver to positional-lookup scratchpad (`fruit.py`, 13:00–35:00)

These are editor states, not a program assembled for execution. Left-hand numbers reproduce the visible gutter; do not type them. A blank numbered row is a blank editor line. The excerpts below come from individual frames unless explicitly compared. Selection, caret and completion popups are not source characters.

**Earlier count buffer — `t13m00s.png`; also visible through `t16m00s.png`.** The three calls test an empty list, an apple, then a *new* apple list to which banana is appended. The nearby PowerShell scrollback shows `py .\test\fruit.py` printing `0`, `1`, `2`, in that order, at `3907fbe count`. **After** that run, `git checkout master~10` reports `HEAD is now at 91be848 at(index)`. The still-visible count buffer and old output do not establish a run of the new positional-lookup code.

The unchanged count driver is transcribed in the preceding listing (lines 1–12).

**Early color scratch — `t18m00s.png`, right editor.** After the driver changes, the color list is initially written at line 17; line 18 holds an unassigned expression. The earlier samples show just `colo` on line 17 at 16:35, the completed literal at 17:00, and `color` alone on line 18 at 17:15. The 19:00 sample still shows the version below.

```text
17  color = ['red', 'white', "blue"]
18  color[1]
```

At 19:55 the two lines occupy 19–20; at 20:00, line 17 contains just `fruit`. By 20:45, line 17 is a complete *proposal* for bracket access and the second color line binds the result to `fav`. Neither spelling of the color expression includes `print`.

```text
17  lunch = fruit[2]
18
19
20  color = ['red', 'white', "blue"]
21  fav = color[1]
```

**Addition before the collection comparison — two distinct line 22 states.** At `t22m15s.png`, the line reads `22  n ==` with no right operand. At `t23m00s.png`, it reads `22  n = 2 + 3`. The earlier text is a typing state, not a demonstrated comparison or a captured syntax error.

**Building the comparison — `t26m00s.png`.** At 25:00, the tuple expression is complete on line 24 but line 25 is only `p` with editor suggestions. A minute later the contrasting list expression is present. At 27:00, the same expressions are visible; higher in the driver a popup for `append` shows “Definition of append” at `item.py:23` and “References” at `fruit.py:4`. The popup is editor assistance, not a program result.

```text
24  m = (2, 3)
25  p = [2, 3]
```

**Precedence scratch — `t27m20s.png` and later revisions.** The grouped sum is now line 24 and the pair of collection displays moves down to lines 25–26. The alternatives in the following table replace line 24; they are not successive instructions in one file.

```text
24  r = (2 + 3)
25  m = (2, 3)
26  p = [2, 3]
```

| Target frame | Gutter | Editor text | Why this state matters |
|---|---:|---|---|
| `t28m00s.png` | 24 | `r = (2 + 3) * 4` | Grouped addition now feeds multiplication. |
| `t28m18s.png` | 24 | `r = (2 + 3 * 4` | Mid-edit; opening parenthesis still visible, no closing parenthesis on the line. Not the completed ungrouped teaching example. |
| `t29m00s.png` | 24 | `r = (2 + 3) * 4` | Balanced grouped product again. |
| `t30m00s.png` | 24 | `r = (2 + 3)` | Product removed for the comparison of `+` with the comma below it. |

**Fullest visible driver and scratchpad — `t35m00s.png`, single-frame `fruit.py` state.** Lines 1–15 belong to the driver in the upper editor region; lines 17–28 are active-looking scratch statements, not printed notes or observed output. At 33:00 only the list write is present; both writes are visible by 33:30. The frame does not establish that the file was saved or this entire state ran.

```text
 1  from item import Item
 2
 3  fruit = Item( "apple" )
 4  fruit.append("banana")
 5  fruit.append("grape")
 6  try:
 7      print( fruit.at( 0 ).object( ) )
 8      print( fruit.at( 11 ).object( ) )
 9      print( fruit.at( 2 ).object( ) )
10      print( fruit.at( 3 ).object( ) )
11  except Exception as e:
12      print( e )
13
14  fruit = Item( )
15  print( fruit.at( 0 ).object( ) )
16
17  lunch = fruit[2]
18
19
20  color = ['red', 'white', "blue"]
21  fav = color[1]
22  n = 2 + 3
23
24  r = (2 + 3)
25  m = (2, 3)
26  p = [2, 3][remainder not visible]
27  p[1] = 6[remainder not visible]
28  m[1] = 6[remainder not visible]
29  [remainder not visible]
```

Lines 3–5 set up apple, banana and grape. Lines 7–10 request **0, 11, 2, 3**, in that order: line 8 is not `1`. Each line requests an item, then its stored object, then prints the result if both calls succeed. If a call in the `try` raises, the remaining lines in that block are skipped and the handler prints the exception. Lines 14–15 rebind `fruit` to a fresh empty `Item` and try index 0 **outside** the handler. Thus the later scratch lines cannot be treated as independent proof of a successful fruit lookup or a historical tuple exception. They serve here as evidence for what the instructor wrote while reasoning.

### Historical screen code: the index checkpoint and its driver

Numbers at the left reproduce the editor gutter; **do not type them**. A marker in square brackets is an editorial visibility note, not source. The filenames give requested capture times, not exact decoded timestamps.

#### `item.py` at the `at(index)` checkpoint — assembled visual reconstruction

At `t36m28s.png` the top of the pane is already inside a method. Lines 115–120 pass each current value to a caller; the containing signature is above this view. Lines 122–133 show a different use of traversal state: the first item holds `_iter` across requests, and exhaustion raises `StopIteration`. Neither is how `at` counts positions: its `iter` is local to one call.

The 36:28 frame supplies 115–148, `t36m00s.png` independently shows 121–154, and `t42m00s.png` supplies 133–159, including all of `at` on a single screen. Shared line numbers and text agree. This is an assembly of the visible editor regions, not a recovered disk file or proof that no intervening keystrokes occurred.

At this checkpoint, lines 115–147 agree with the numbered count-revision listing above; line 114 is above the 36:28 view. Here is the newly visible continuation. In the middle of the lesson (16:35 and 35:00), the viewport ends at line 151; the later 42:00 view independently supplies the complete implementation.

```text
148     def at(self, n):
149         iter = self.first( )
150         if iter._object != None:
151             count = 0
152             while count < n:
153                 if iter._next == None:
154                     iter = None
155                     raise Exception( "Index beyond end." )
156                 iter = iter._next
157                 count += 1
158         return iter
159
```

Lines 135–146 count *all* items, starting with zero for an empty first item or one for a populated first item. Lines 148–158 instead start position counting at zero on a populated list. Reaching line 155 means another move was required and impossible; lines 156–158 are not a continuation of that failure path. The message is present **as code** in this revision. The terminal run reproduced below was a count run, not a run of this listing’s index method.

#### `test\fruit.py` — from color index 1 to 11

At 36:00 the right editor still shows the three-item driver and scratchpad transcribed in the 35:00 listing. Its video overlay hides the far right of the last rows. No intervening run is established.

`t39m00s.png` and `t39m10s.png` show the altered assignment. The latter frame shows the same visible surrounding driver, including the earlier custom call at index 11 on line 8. Those two occurrences of 11 are different operations: the custom method call is inside the handler; the built-in color lookup is later, outside it. The line below persists in the right-hand buffer even after the later library checkout (`t53m15s.png`); that does not certify a saved driver at either checkpoint.

```text
20 color = ['red', 'white', "blue"]
21 fav = color[11]
22 n = 2 + 3
```

#### PowerShell scrollback — count output versus the final checkout

`t52m38s.png` foregrounds the shell. The following contiguous excerpt preserves the executed commands and output in order. The `0`, `1`, `2` follow the script invocation at `3907fbe count`; **they are not the results of index lookups**. After the last checkout the prompt returns, with no intervening invocation of the index or membership driver in this visible sequence. Git’s `git switch` lines higher in the window are advice, not evidence of executed commands.

```text
HEAD is now at 3907fbe count
PS C:\com\joesta\Item> git checkout master~10
Previous HEAD position was 3907fbe count
HEAD is now at 91be848 at(index)
PS C:\com\joesta\Item> git checkout master~11
Previous HEAD position was 91be848 at(index)
HEAD is now at 3907fbe count
PS C:\com\joesta\Item> py .\test\fruit.py
0
1
2
PS C:\com\joesta\Item> git checkout master~10
Previous HEAD position was 3907fbe count
HEAD is now at 91be848 at(index)
PS C:\com\joesta\Item> git checkout master~9
Previous HEAD position was 91be848 at(index)
HEAD is now at 0ec6bc1 has(object)
PS C:\com\joesta\Item>
```

#### `item.py` — visible additional method after the `has(object)` checkout

At `t53m00s.png` and `t53m15s.png`, lines 133–158 still show the same count tail and index method transcribed above; here the file visibly continues below line 159. This later state is not evidence that `has` existed in the earlier checkout. The visible code searches by value, breaking at the first match and otherwise advancing one link. Line 166 is the last readable line; the method’s return value is not recoverable from these frames.

```text
158         return iter
159
160     def has(self, obj):
161         if self._object != None:
162             iter = self.first( )
163             while iter != None:
164                 if iter._object == obj:
165                     break
166                 iter = iter._next
```

There is no shown invocation or result for this fragment, and no observed bracket error text. The final captures at 54:00 and 54:25 show the course chat rather than more source code; the last caption starts at 54:16.640. Neither the closing still nor the absence of later cues establishes what was said or not said between sampled moments.

## Removable appendix · Micro-Learning Insights

- **Ask what the counter measures before choosing its first value.** In `count()`, apple has already contributed one item. In `at(0)`, reaching apple has cost zero forward moves. The different starting values are both right. *02:00–04:00; 43:00–49:00.*
- **Test the boundary, then the transition.** Empty, apple alone, and a new apple followed by banana test three distinct paths. The last verifies that one successful advance contributes exactly one; it is not proof that a billion-node list is practical. *04:00–06:00.*
- **Count patterns before naming their values.** Eight binary places allow 256 patterns. Whether a pattern denotes zero, a negative number, or something else depends on the representation. *08:00–11:00.*
- **Read punctuation with its neighbors.** In `[2, 3]` the brackets build a list; in `color[1]` they request a value. In `(2, 3)` the comma matters; in `(2 + 3) * 4` the grouping changes what is multiplied. *16:00–31:00.*
- **Separate order, repetition, and changeability.** Position one means something in both `[2, 3]` and `(2, 3)`. Only the list allows that position to be reassigned. A set answers a different question: which distinct elements are present? *31:00–35:00.*
- **Read a chain from the object outward.** For `fruit.at(0).object()`, establish what `fruit` names, what `at` returns, and only then what `.object()` can access. If `at` raises, there is no returned node on which to make the next call. *36:00–41:00.*
- **Find the stop condition before tracing the body.** With `n = 0`, `0 < 0` is already false: zero iterations is a successful lookup. With `n = 3`, the third forward move is needed but unavailable: that is failure, not normal termination. *48:00–51:00.*
- **A handled error does not mean every test ran.** To see all four outcomes for 0, 11, 2 and 3, test each request independently. One handler around the whole group skips the later requests after 11 fails. *38:00–41:00; 45:00–47:00.*
- **Keep the interface distinct from the work.** Spelling a future lookup `fruit[2]` might make the caller’s code look like a built-in list operation. It would not remove the need to walk two links in this design. *17:00–21:00; 52:00–53:30.*
