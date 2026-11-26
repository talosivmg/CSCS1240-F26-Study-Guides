# When an empty list still has a node

**CSCS1240F26 · Session 21 | Worked teaching companion**

An apple has not been added yet, but there is already something to call a method on. That is the puzzle behind this class: **`fruit = Item()`** creates an object, while the field that would hold its first fruit contains **`None`**. Follow which one the instructor is talking about—the node, its contents, a link to another node, or a name for the node—and the behavior of the whole demonstration becomes predictable.

**Find your place in the recording**

| Time | Question to take into the example |
|---|---|
| [01:00](https://www.youtube.com/watch?v=9IFcP9rgUFw&t=60s) | Which Git revision is the class actually inspecting? |
| [06:00](https://www.youtube.com/watch?v=9IFcP9rgUFw&t=360s) | If nobody supplied a first fruit, what does the constructor make? |
| [15:00](https://www.youtube.com/watch?v=9IFcP9rgUFw&t=900s) | When does adding fruit need a new node, and when can it fill the existing one? |
| [21:00](https://www.youtube.com/watch?v=9IFcP9rgUFw&t=1260s) | Why does the display loop print the last item once and then stop? |
| [28:00](https://www.youtube.com/watch?v=9IFcP9rgUFw&t=1680s) | Why start over with a fresh empty object for each test? |
| [36:00](https://www.youtube.com/watch?v=9IFcP9rgUFw&t=2160s) | Which references come and go during a function call? |
| [50:00](https://www.youtube.com/watch?v=9IFcP9rgUFw&t=3000s) | Why can a line headed `Prepend:` be evidence about `insertAfter`? |

**Evidence.** The saved recording manifest names *CSCS1240F26 21*, by JoesTA.[1] All 1,276 English auto-caption cues agree with the readable transcript. The first begins at 00:00.720; the last begins at 52:49.400 and includes the sign-off. Its nominal end, 52:53.800, slightly exceeds the recorded video duration of 52:53.150. Intervals without captions do not establish silence. The original 1920 × 1080 images cover all 53 whole minutes, with 21 event captures and a closing image at 52:52.150. Every PNG decoded and matched its archived hash. Times identify requested capture targets, not exact decoded frame times. The guide uses screen samples for what was visible, captions for the discussion, and labeled teaching examples for what can be tested separately. It does not treat a later editor buffer as proof of what ran earlier.

## Picking up the linked list: what does one item know? · 00:00–03:00

After the break, the instructor uses the Git history to recover the class's stopping point: inserting a value *after* a particular item. The code is split across two files. In **`fruit.py`**, the caller says what to do with apple, banana and peach. In **`item.py`**, the class defines how to do it. There is no separate container in this example: you can hold a reference to any item and follow its links to the others. This is a **doubly linked list** because each item can refer to a neighbor on either side.

An **`Item("apple")`** holds the string **`"apple"`**, not another list of fruits inside it. That string is its stored value, or payload. An **`Item()`** also creates a real object, but its stored value is **`None`**. The two links start out missing, so a single apple has neither an actual next nor an actual previous neighbor. In the following excerpts from the class, **`self`** is the particular item on which the caller invoked the method.

To try the earliest example, copy the first complete **`item.py`** and **`fruit.py`** listings from the historical appendix into two files with those names in the same practice folder. Omit the gutters. From that folder run **`py fruit.py`** on Windows. That layout is a guide convenience; the classroom repository kept source and tests apart. Later checkpoints are separate versions, not methods to paste over this run.

Annotated teaching version — guide-added comments (02:00, **`item.py`**):

```python
class Item:                          # One node: a stored object and two directions.
    _object = None                   # Class-level default: no stored value.
    _next = None                     # No following node unless a link is set.
    _prev = None                     # No preceding node unless a link is set.

    def __init__(self, obj = None):  # Item() supplies None; Item("apple") supplies apple.
        self._object = obj           # Store that value on this particular new node.

    def object(self):                # Ask the node what it holds.
        return self._object          # Return apple or None; this does not print.
```

A leading underscore is the designer's request that callers leave the field alone. Python does not enforce that request simply because of the spelling. An **accessor method** such as **`object()`** gives callers a way to ask for information without depending on that internal field name. That separation will matter when the designer decides what a caller should receive at the end of the list.

**Key items to scan**

- **`fruit.py`** — caller/test driver; chooses inputs and prints results.
- **`item.py`** — implementation file containing the **`Item`** class and its methods.
- **Doubly linked list** — apple can lead forward to banana; banana can lead back to apple.
- **`Item("apple")`** — creates a new node containing apple. **`Item()`** — creates a new node representing an empty list.
- **`None`** — this design's marker for an empty value or missing internal link; those are different questions.
- **`self`** — the node receiving the call. **`obj`** — the value supplied by the caller or by the default.
- **`_object`** — stored value; **`_next`** and **`_prev`** — links to neighboring nodes, not the neighbors' fruit strings.
- **Accessor method · `object()`** — returns the node's value rather than exposing how the class stores it.

### Append to an end; insert beside a particular node

Start with apple. Appending banana and then peach is meant to leave apple in place and put each addition at the far end. In the earlier version, **`append()`** first calls **`last()`**: if asked to append from apple when banana is already there, it needs banana, not apple, as the old tail. The local name **`iter`** walks the links without changing where the caller's name points. These are ordinary explicit method calls, not use of Python's iterator protocol.

Annotated teaching version — guide-added comments (02:00–03:00, earlier **`Item`** methods):

```python
def last(self):                   # Called on apple, banana, or any other item.
    iter = self                   # Begin at the item we actually have.
    while True:                   # Follow links until the test below succeeds.
        if iter._next == None:    # Peach has no following node.
            break                 # Keep peach as the answer.
        iter = iter._next         # Move the local reference, not any node.
    return iter                   # Return the endpoint node, not its fruit string.

def append(self, obj):            # Earlier version: always adds a new node.
    last = self.last()            # For the second append, this finds banana.
    newItem = Item(obj)           # Make the new peach node.
    newItem._prev = last          # Peach points back to banana.
    last._next = newItem          # Banana now points forward to peach.
    return newItem                # Caller could retain peach; need not do so.
```

Now insert avocado after apple. This isn't an overwrite: banana must still be reachable. Unlike an append, inserting in the middle changes an existing relationship. Save the old forward neighbor and repair its backward link **before** redirecting apple's forward link. Otherwise, reading apple's next link can give you avocado when you still need banana.

Annotated teaching version — guide-added comments (03:00, **`item.py`**):

```python
def insertAfter(self, obj):               # Here self is apple; obj is avocado.
    newItem = Item(obj)                   # Create a separate avocado node.
    if self._next != None:                # Apple already has a right neighbor, banana.
        newItem._prev = self              # Avocado points back to apple.
        newItem._next = self._next        # Avocado points on to the original banana.
        self._next._prev = newItem        # Banana now points back to avocado.
        self._next = newItem              # Apple finally points forward to avocado.
    else:                                 # If called on peach instead, there is no right neighbor.
        newItem._prev = self              # Point back to the former last node.
        self._next = newItem              # Link the former tail to the new tail.
    return newItem                        # Both paths return the inserted node.
```

**Key items to scan**

- **`append()`** — finds the far end even if called on apple; the original version always allocates a new node.
- **`last()`** — returns the endpoint *node*; **`last().object()`** asks that node for the stored value.
- **`iter`** — temporary reference used to find that endpoint. **`while True`** needs a reachable exit; **`break`** leaves the loop on the endpoint.
- **`insertAfter()`** — inserts beside the receiving node, not necessarily at the end. Apple → banana becomes apple → avocado → banana.
- **`self._next._prev`** — reaches the old right neighbor, banana, then changes its backward link.
- **`return newItem`** — hands the added node back without rebinding the caller's **`fruit`** variable or printing anything.

### Read the driver from the receiver outward · 02:00–03:45

The driver shown beside the class tests two different lists. **`veg`** is empty; making **`fruit`** does not populate **`veg`**. The leading **`#`** in front of the line that would move **`fruit`** to the tail changes the insertion site: **`fruit`** still denotes apple. For the final loop, the public navigation methods return the same node at an endpoint. Printing the fruit *before* checking that condition ensures peach appears once rather than being skipped.

Annotated teaching version — guide-added comments (**`fruit.py`**, 02:00–03:00):

```python
from item import Item             # Import the class from the module named item.
veg = Item()                      # Create a separate empty list.
print(veg.last().object())        # Find its only node, fetch None, then print None.
fruit = Item("apple")              # Create a NEW list with one apple.
fruit.append("banana")            # Add banana; fruit still denotes apple.
fruit.append("peach")             # Add peach after banana; fruit still denotes apple.
#fruit = fruit.last()             # Inactive: would have moved fruit to peach.
fruit.insertAfter("avocado")      # Insert immediately after apple, not after peach.

iter = fruit                      # Start at apple, using a separate name to move.
while True:                       # Process the current node, including the final one.
    print( iter.object() )        # Print apple, avocado, banana, then peach.
    if iter.next() == iter:       # At peach, asking for next gives peach itself.
        break                     # Stop here; iter still refers to peach.
    iter = iter.next()            # Otherwise, advance one real node.
print(iter.last())                # Print the node representation, not "peach".
print(iter)                       # Same endpoint object, so the representations agree.
```

Observed PowerShell scrollback (02:00; top of 03:45). An earlier traceback ends **`ModuleNotFoundError: No module named 'item'`** at the import on line 1 of the test driver. The first failing invocation is not visible. The next visible commands are **`.\script\path.ps1`** and **`py .\test\fruit.py`**. The second command successfully printed:

```text
None
apple
avocado
banana
peach
<item.Item object at 0x000001855521E060>
<item.Item object at 0x000001855521E060>
```

The first line comes from printing **`veg`**'s value, not from importing the class. The final two lines show one object twice: finding the last node *from* the last node returns that node. The address is the one visible in this run, not a value to reproduce. The run **precedes** the checkout in the terminal. Its output is consistent with this editor example, and a separate fixture reproduces the values and the identity match; neither fact proves the editor buffer was the exact file executed earlier. The path script's contents are not shown.

**Key items to scan**

- **`veg`** — the separate empty object whose stored value prints as **`None`**.
- **`fruit`** — remains at apple while append and insert change the links around it.
- **`#`** — disables **`fruit = fruit.last()`** in this driver. Uncommenting it would change which item receives the insertion.
- **`iter.next() == iter`** — compares nodes; it does not compare the displayed strings.
- **`print(iter)`** — displays the node representation; **`print(iter.object())`** displays its stored fruit instead.
- **`ModuleNotFoundError`** — the earlier interpreter could not import **`item`** from the available search locations.
- **`.\script\path.ps1`** — the path script visibly invoked before the successful retry. **`py .\test\fruit.py`** — launches the test driver.

## Git as a sequence of worked versions · 03:00–06:00

The instructor is not adding every method live. Moving through saved commits exposes successive design choices. The PowerShell prompt is in **`C:\CSCS1240F26\Item`**. **`master~12`**, **`master~11`** and **`master~10`** are positions measured *backward* from the same named branch: the smaller number here selects a newer stage. **`HEAD`** identifies the currently checked-out commit. A detached HEAD in this demonstration means Git has selected a particular commit rather than following a branch tip.

| Observed input in order | What happened and why it matters |
|---|---|
| `git checkout master~12` | Selected `7630f8b`, the insert-after version. The terminal also marked `script/path.ps1` and `test/fruit.py` as modified: checking out a commit had not made those local edits disappear. |
| `git log --oneline` | Showed the short IDs and messages: first object/next/prev and references, then constructor, append, last, using last inside append, private attributes/accessors, avoiding `None`, and `insertAfter`. This is how the instructor finds the stopping point. |
| `git checkout master~11` | **Aborted**: `test/fruit.py` would be overwritten. The message recommended committing or stashing changes. No version switch occurred on this attempt. |
| `git status` | Still `HEAD detached at 7630f8b`. Both tracked files had unstaged changes. `app/`, `pylintrc`, `src/pyproject.toml` and `test/clearTest.py` were separately listed as untracked. They were not the blocker in this checkout. |
| `git restore .\test\fruit.py` then `git restore .\script\path.ps1` | Intentionally discarded the unstaged edits to those two named tracked files. Restore is not a way to preserve changes you still want. |
| `git checkout master~11` | Now succeeded: previous `HEAD` was `7630f8b`; new `HEAD` was `ab909a4`. First, prepend and insert-before are the next design additions. |
| `git checkout master~10` | Selected `8887c71`, identified in the terminal as `Item: empty list edge case`. |

A useful debugging habit is to separate the three questions the terminal answers: **Which file blocked the operation? Which files have local changes? Where is HEAD now?** They need not have the same answer. In particular, the presence of untracked files in status was not a reason to remove them, and an attempted checkout was not evidence of a new code version. (Terminal: 03:45, 04:00, 05:00 and 06:00.)

**Key items to scan**

- **`C:\CSCS1240F26\Item`** — classroom repository directory, not a path required on a learner's machine.
- **`HEAD`** — selected commit; compare the reported ID before and after checking out.
- **`git log --oneline`** — short history used to place **`insertAfter`** after accessors and **`last`**.
- **`git status`** — distinguishes two modified tracked files from four untracked entries.
- **`git restore`** — intentionally discards these unstaged changes; commit or stash instead if they should be kept.
- **`master~12` → `master~11` → `master~10`** — **`7630f8b`** → **`ab909a4`** → **`8887c71`**; the first attempt to move to **`master~11`** failed.

### A new handle to peach changes where mango goes · 05:00–05:59

At **`ab909a4`**, the reverse-direction operations complete the earlier pairs. **`first()`** walks actual backward links to the item with no preceding node. **`prepend()`** uses that result even if called on peach. **`insertBefore()`** uses the particular item it was called on: before peach is not necessarily the front. Insert means *keep* peach and fit mango beside it, not overwrite peach with mango.

Annotated teaching version — guide-added comments (**`item.py`**, 05:00 and 05:03):

```python
def first(self):                   # From peach, look left for the beginning.
    iter = self                    # A local name starts on peach.
    while True:                    # Follow predecessors until none remain.
        if iter._prev == None:     # The current node is already first.
            break                  # Stop without stepping off that node.
        iter = iter._prev          # Move one node left; links do not change.
    return iter                    # Return the first node itself.

def prepend(self, obj):            # Before the empty-list revision.
    first = self.first()           # Called from peach, this still finds apple.
    newItem = Item(obj)            # Create a separate acai node.
    newItem._next = first          # Acai points forward to apple.
    first._prev = newItem          # Apple points back to acai.
    return newItem                 # Return acai; caller may ignore it.
```

Annotated teaching version — guide-added comments (**`fruit.py`**, 05:00):

```python
from item import Item             # Use the class for this checkpoint.
fruit = Item("apple")              # A new list; fruit denotes apple.
fruit.append("banana")            # Apple → banana.
fruit.append("peach")             # Apple → banana → peach.
fruit = fruit.last()              # ACTIVE now: rebind fruit to existing peach.
fruit.insertBefore("mango")       # Put mango between banana and peach.
fruit.prepend("acai")             # Add acai before apple, even though fruit is peach.

iter = fruit.first()              # Start a different name at acai.
while True:                       # Visit each node, including peach.
    print( iter.object() )        # Display the fruit held at this position.
    if iter.next() == iter:       # Same-node response at peach marks the end.
        break                     # Do not print peach again.
    iter = iter.next()            # Otherwise follow the next link.
```

A reconstruction independently run with a mirrored insert-before helper printed the following lines. That helper supplies the behavior described in class; its implementation at this checkpoint is not visible in these frames. This output is **not** historical terminal output from the five-fruit editor state.

```text
acai
apple
banana
mango
peach
```

**Key items to scan**

- **`ab909a4`** — checkout with the five-fruit driver, distinct from the four-fruit scrollback.
- **`first()`** — walks back to the first node without moving the caller's **`fruit`** reference.
- **`fruit = fruit.last()`** — rebinds the name to peach; no node is created or removed.
- **`insertBefore()`** — here places mango immediately before peach.
- **`prepend()`** — places acai at the absolute front; the call does not rebind **`fruit`**.
- **`iter = fruit.first()`** — gets acai from the peach handle so that the output includes the entire chain.

## What should an empty list mean? · 05:59–13:00

At the next checkpoint the instructor asks a design question, not a syntax question. Should a caller be allowed to create a list before knowing its first value? Requiring the first value would mean treating the first input specially, then looping over the *rest*. Allowing **`Item()`** means the same loop can accept every input from another list, a CSV file, or the output of another process. If that source yields nothing, the empty list already exists. The class chooses this second contract.

Annotated teaching version — guide-added comments (separately tested adaptation of the spoken population scenario with the empty-aware revision):

```python
fruit = Item()                            # New empty list before reading any fruit.
for name in ["apple", "banana", "peach"]:  # Same action for all three input values.
    fruit.append(name)                    # First fills the empty node; later calls add nodes.
print(fruit.object(), fruit.next().object(), fruit.last().object())  # Display the three values.
```

Independently verified output: **`apple banana peach`**. This works with the revised append below, **not** the earlier unconditional append. In the earlier fixture, appending apple to **`Item()`** left the first node holding **`None`** and linked a second node holding apple. Allowing **`Item()`** in the constructor and correctly supporting it throughout the class are different accomplishments.

**Key items to scan**

- **`Item()`** — creates a real object before any input arrives; the first stored value is **`None`**.
- **`for name in …`** — passes every incoming value through the same operation; no separate first-record path in the caller.
- **`fruit.append(name)`** — in the revised implementation, the method itself treats the first incoming value specially.
- **Empty-list edge case** — zero logical fruits but one physical node for operations to act on.

### Getting a value is different from getting a neighbor

The instructor switches seats mentally: what happens to the class implementer, and what happens to the user of the class? The accessor **`object()`** simply returns the stored value. It can return **`None`** without any failure inside the method. But a caller who assumes they received a fruit may discover the problem only when they use the result. The instructor's preference is to be *careful* about returning **`None`**, not to forbid it. Raising an empty-list exception was discussed as an alternative: a caller could catch and handle it, or let it propagate. No such exception was implemented in the shown accessor (09:00–11:00).

Navigation makes a different promise. If the caller asks peach what comes next, **`next()`** returns peach itself, not peach's stored value and not **`None`**. Likewise **`prev()`** on the first node returns that same first node. An existing node returned to signal “you cannot go farther” is a design decision of this class, not a universal linked-list rule.

Annotated teaching version — guide-added comments (**`item.py`**, 12:00–13:00):

```python
def next(self):                      # Ask a node for the following node.
    if self._next != None:           # Apple has a real successor in a longer list.
        return self._next            # Give the caller that successor node.
    return self                      # Peach (or the empty placeholder) gives itself back.

def prev(self):                      # Ask a node for the preceding node.
    if self._prev != None:           # Banana has a predecessor when apple precedes it.
        return self._prev            # Give the caller the actual preceding node.
    return self                      # No predecessor: stay on this node.
```

For **`veg = Item()`**, the independently checked results are **`veg.object() is None`**, **`veg.next() is veg`**, and **`veg.prev() is veg`**: all true. The *internal* missing links stay **`None`**. Returning the same node is not the same as setting its links to itself. This is why the earlier driver tested whether the returned node was the one it already had instead of testing whether the returned node was **`None`**.

**Key items to scan**

- **`object()`** — returns the payload; for an empty node this is genuinely **`None`**.
- **`None`** — a legitimate return value here, not an exception being raised.
- **Empty-list exception** — proposed way to notify a caller immediately; not present in the classroom accessor.
- **`next()`** — returns the right neighbor if present, otherwise the same node.
- **`prev()`** — returns the left neighbor if present, otherwise the same node.
- **`return self`** — hands back the receiver without creating a node or changing a link.

## Filling the first node versus linking another one · 13:00–17:59

“Is a list ever really empty?” has two answers in this design. There are zero fruit values in **`Item()`**, but there is already an object whose value field is **`None`**. If the first apple were appended as a new node behind it, a traversal would still encounter the empty node. Overwriting a *populated* first node would have the opposite problem: it would lose the old fruit. The revised **`append()`** tests which case it has before touching links.

Annotated teaching version — guide-added comments (15:43–16:00, **`item.py`**, commit **`8887c71`**):

```python
def append(self, obj):              # Add obj at the end; return its node.
    if self._object == None:        # The receiving node represents the empty list.
        self._object = obj          # Put the first apple in the EXISTING node.
        newItem = self              # A second local name for it; no allocation or link change.
    else:                           # There is already a fruit to preserve.
        last = self.last()          # Find the current end (apple in a one-fruit list).
        newItem = Item(obj)         # For banana, allocate a distinct banana node.
        newItem._prev = last        # Banana points back to apple.
        last._next = newItem        # Apple points forward to banana.
    return newItem                  # Same placeholder now filled, OR the newly linked node.
```

Guide trace, independently checked with the revised method:

| Operation | Node that receives the call | What changes | Returned value |
|---|---|---|---|
| `fruit = Item()` | New placeholder | Value and both links are `None`; `fruit` refers to this object. | Construction gives the caller the placeholder. |
| `fruit.append("apple")` | That placeholder | Its value becomes `"apple"`; both links remain `None`. | The very same node, now holding apple. |
| `fruit.append("banana")` | Existing apple node | Apple keeps its value; a new banana is linked on its right and points back to it. | The new banana node. `fruit` still refers to apple. |

The companion **`prepend()`** method, also visible at 16:00, uses the same fill-the-placeholder rule. Its nonempty branch performs the opposite endpoint operation. The third name, **`newItem`**, serves one purpose in the empty branch: both branches can finish with the same return statement. An assignment between names is not by itself a request to create another item.

Annotated teaching version — guide-added comments (16:00, **`item.py`**):

```python
def prepend(self, obj):             # Add at the front, not necessarily beside self.
    if self._object == None:        # No fruit yet: no links to rearrange.
        self._object = obj          # A fresh empty node can hold the first banana.
        newItem = self              # Return the SAME now-populated node.
    else:                           # Keep the existing first fruit.
        first = self.first()        # Find banana if it is currently first.
        newItem = Item(obj)         # Create a new acai node in front.
        newItem._next = first       # Acai leads forward to banana.
        first._prev = newItem       # Banana leads back to acai.
    return newItem                  # Return whichever node now holds the new value.
```

Independent check on a **fresh** empty list: prepending banana returned the original node; prepending acai next produced **`acai → banana`**, with the backward link from banana returning to acai. This was a separate test, not a continuation of apple and banana in the table.

**Key items to scan**

- **`Item()`** — zero stored fruits, but one node is available to fill.
- **`append()`** — checks for an empty receiving node before allocating another one.
- **`self._object == None`** — distinguishes “fill the placeholder” from “preserve an existing fruit and link another.”
- **`newItem = self`** — two names for one node; neither **`_next`** nor **`_prev`** changes.
- **`newItem = Item(obj)`** — makes a genuinely new node in the populated branch.
- **`prepend()`** — fills an empty node, or links a new node before the first existing one.

### Which methods need to change? · 16:53–18:38

A student suggests testing whether the next link is missing. That answers “Am I at the end?” without answering “Do I hold a fruit?” At 17:00–18:00, the earlier **`last()`** and **`first()`** loops therefore work unchanged: on the empty placeholder each breaks on the first check and returns the same node. On a node in the middle they follow links until the corresponding end. The internal field check is deliberate: the public accessors hide the missing neighbor by returning the current node instead.

**Key items to scan**

- **`iter._next == None`** — stop the forward search here, whether the current node holds peach or no fruit at all.
- **`iter._prev == None`** — stop the reverse search; the algorithm returns a node rather than the payload **`None`**.

### Inserting next to a node: fill, splice, or extend (18:38–20:35)

For **`insertAfter`**, the same empty representation *does* require a special case. Inserting after a placeholder should not preserve an extra blank node in front of the fruit. Make that very node the real item. If it already holds a fruit, create a different node and decide whether there is an existing right neighbor. At 19:51 the instructor corrects “none” to “not none”: it is the *presence* of that neighbor that requires reconnecting both sides. At 19:37, 20:00 and 20:16 the screen picks out the three branches.

Annotated teaching version — guide-added comments. To read the nonempty branch, imagine inserting grape after apple while banana is already next:

```python
    def insertAfter(self, obj):         # Insert beside this particular Item.
        if self._object == None:        # Is this still the empty placeholder?
            self._object = obj          # Store the fruit right here; links stay empty.
            newItem = self              # Return this filled node, not a new one.
        else:                           # The current fruit must be preserved.
            newItem = Item(obj)         # Allocate a distinct node for grape.
            if self._next != None:      # Banana already follows apple.
                newItem._prev = self    # Grape points back to apple.
                newItem._next = self._next  # Grape points forward to banana.
                self._next._prev = newItem  # Banana now points back to grape.
                self._next = newItem    # Apple now points forward to grape.
            else:                       # No old successor: inserting at the tail.
                newItem._prev = self    # New tail points back to its neighbor.
                self._next = newItem    # Old tail points forward to the new tail.
        return newItem                  # The Item containing the inserted fruit.
```

Order matters in the middle branch. While apple’s next link still reaches banana, the code can find banana and fix its backward link. Replacing apple’s next link first would change which node that same expression reaches. At the tail there is no banana link to preserve.

For **`insertBefore`**, read left and right in reverse. The screen confirms the empty guard and the start of the nonempty branch, not its entire body (19:37 and 20:00). The instructor describes the symmetry; the following is deliberately a fragment, not a substitute implementation.

Annotated teaching version — guide-added comments (visible portion only):

```python
    def insertBefore(self, obj):        # Place a fruit before the current item.
        if self._object == None:        # A placeholder has no fruit to displace.
            self._object = obj          # For the driver's grape, store grape here.
            newItem = self              # Name the existing, now-filled node.
        else:                           # A real fruit is already present.
            newItem = Item(obj)         # Create a separate node for the new fruit.
            if self._prev != None:      # Is there an existing left neighbor?
                newItem._next = self    # The new node's right neighbor is this node.
                # Remaining nonempty-branch lines are not established by these frames.
```

Independent transfer test, not classroom output: starting with apple, insert banana after the tail, then grape after apple. The tested forward order is **`apple → grape → banana`**; walking backward from banana reaches grape, then apple. This checks both directions, not merely whether the printed list looks plausible.

**Key items to scan**

- **`insertAfter`** — fills an empty node, splices between two fruits, or adds at the tail, depending on the starting state.
- **`self._object == None`** — asks whether the current node needs its first fruit; unlike the end test, it does not inspect a link.
- **`newItem = self`** — keeps the existing node in the empty branch; **`newItem = Item(obj)`** creates a distinct one in the nonempty branch.
- **`self._next != None`** — an old right neighbor exists; it needs an updated backward link.
- **`self._next._prev = newItem`** — redirects the old neighbor back to the inserted node before replacing the current node’s forward link.
- **`insertBefore`** — fills the placeholder on an empty list; in the visible middle-branch fragment the new node points forward to the current one.
- **`apple → grape → banana`** — independently tested example of inserting in the middle; no classroom run of this sequence is claimed.

### Displaying the list: why the last fruit prints exactly once (20:45–27:52)

The instructor finds **`display`** already defined in the driver and explains why it is there: to avoid copying and pasting print statements for every test. It is a free function, called as **`display(fruit)`**; the methods it calls belong to an item. Its parameter happens to be named **`list`**, but a parameter name does not validate its argument or turn it into a linked list. The helper assumes a course-specific object that can find its first node. Type checking and validation are suggestions, not present in this version.

Annotated teaching version — guide-added comments (21:00, right-hand driver pane; 24:22, selected end test):

```python
def display(list):                  # Receive an Item anywhere in this chain.
    iter = list.first()             # Begin at the left end, even if passed grape in the middle.
    print("-----")                  # Print one five-hyphen divider per call.
    while True:                     # A constant true condition never stops by itself.
        print(iter.object())        # Fetch the current payload and print it, even if None.
        if iter.next() == iter:     # At the right end next() returns this same Item.
            break                   # The last payload has already printed; leave now.
        iter = iter.next()          # Otherwise move to the next node and repeat.
```

The instructor asks what to look for on seeing **`while True`**. A normal **`while`** tests its condition each time; a constantly false condition would skip the body, but this condition will *never* become false. Find the exit, then check whether normal input will ever reach it. Here the exit depends on a design choice: **`next()`** returns the neighboring node if one exists, and **returns the current node** at the boundary rather than exposing the missing link as **`None`**. The final payload therefore prints *before* **`break`**. An empty placeholder prints **`None`** once for precisely the same reason. This logic is not universal to doubly linked lists.

Independent trace for the apple–grape–banana example, calling the helper on grape. The initial call to **`first()`** returns apple; the divider prints once before the loop.

| Pass | Node printed by the body | Node returned by its next accessor | Decision |
|---|---|---|---|
| 1 | apple | grape | Different node; move to grape. |
| 2 | grape | banana | Different node; move to banana. |
| 3 | banana | banana itself | Equal; break immediately. There is no fourth print. |

At 26:39–27:45 the instructor asks about a possible failure involving the “same value” or “same object” twice, and suggests that insertion methods could validate or raise an exception. No duplicate failure is shown. The comparison in the driver is between two **nodes**, not between their fruit strings. In the independent fixture, distinct nodes holding the same string print **`apple`** twice. A link that leads back to the very node being checked would instead be indistinguishable to this test from the end-return convention. Passing an ordinary Python list is a separate failure: an independent call with **`display([])`** raised **`AttributeError: 'list' object has no attribute 'first'`** before even printing the divider.

**Key items to scan**

- **`display`**, **`display(fruit)`** — the reusable driver function and the call that prints the contents of the passed chain.
- **`list`** — the local parameter; the helper assumes a compatible **`Item`**, not Python’s built-in list.
- **`first()`** — positions the traversal at apple before the first print in the sample.
- **`while`**, **`while True`** — the loop tests a condition; this literal never turns false.
- **`print(iter.object())`** — prints the value held in the current node, not the node itself.
- **`next()`** — returns the neighbor, or **returns the current node** if its next link is **`None`**.
- **`iter.next() == iter`** — compares **nodes**, not their fruit values; two separate apple nodes need not stop the loop early.
- **`break`** — exits after the last node’s payload has printed.
- **`iter = iter.next()`** — advances the local name only when a different next node is available.
- **`display([])`** — independently raised **`AttributeError: 'list' object has no attribute 'first'`**; the suggested validation is not in the class demonstration.

### The four empty starts in the driver (27:54–31:30)

The driver asks four separate questions, not “which fruits have accumulated so far?” Each call to **`Item()`** starts a *new* empty list. Only the ensuing insertion changes the existing object. The first run visible here follows the checkout labeled **`8887c71 Item: empty list edge case`**; at 28:48 the command is present at the prompt without output below it, and by 29:00 the output is visible. The 30:00 terminal shows the actual invocation **`py .\test\fruit.py`**. There are no extra diagnostic prints of the objects returned by the insertion calls.

Annotated teaching version — guide-added comments (driver statements from the captures, through line 42):

```python
from item import Item           # In the classroom driver, import the course class.
# display() is the function defined above in this same driver.
fruit = Item()                  # A new empty placeholder, not the value None itself.
print("\nEmpty:")               # Begin a new output block with a blank line and label.
display( fruit )                # Print the divider and the empty payload: None.
fruit.append("apple")           # Fill the existing empty node with apple.
print("\nAppend:")              # Print the driver's label, not the method's return.
display(fruit)                  # The same node now displays apple.

fruit = Item()                  # Start over: fruit no longer refers to the apple node.
print("\nEmpty:")               # Mark the second empty start.
display( fruit )                # Divider, then None again, not apple.
fruit.prepend("banana")         # Fill this new placeholder with banana.
print("\nPrepend:")             # Label the result.
display(fruit)                  # The only fruit here is banana.

fruit = Item()                  # A third new node; do not reuse the banana node.
print("\nEmpty:")               # Mark the third empty start.
display( fruit )                # Divider, then None.
fruit.insertBefore("grape")     # No earlier fruit to shift: fill this node with grape.
print("\nPrepend:")             # Literal label is Prepend:, not InsertBefore:.
display(fruit)                  # Divider, then grape; independent fixture verified.

fruit = Item()                  # A fourth new node, initially empty.
print("\nEmpty:")               # Mark the fourth empty start.
display( fruit )                # Divider, then None.
fruit.insertAfter("strawberry") # No later fruit to shift: fill this node with strawberry.
print("\nPrepend:")             # Still says Prepend:, despite calling insertAfter.
display(fruit)                  # Divider, then strawberry; independent fixture verified.
```

Observed terminal excerpt (29:00, 29:05 and 30:00; only the viewport through the third empty start):

```text
Empty:
-----
None

Append:
-----
apple

Empty:
-----
None

Prepend:
-----
banana

Empty:
-----
None
```

The visible **`None`** is the placeholder’s payload; the function’s own return is not printed. The **`\n`** at the beginning of each heading adds a blank line; the divider comes from **`display`**. The later terminal view at 51:25 exposes the remaining grape and strawberry results, both labeled **`Prepend:`**. Those lines are consistent with the independent empty-case fixture. The closing section places them alongside the actual calls.

The instructor asks why not keep the apple. A student identifies the second test: to prove that prepending to an *empty* list works, it must begin empty. Rebinding **`fruit`** is different from clearing the object: any other name for that original object would still see apple. A separate name for the next test would have worked too. The class proposes a **clear operation**, not yet implemented here: start at the front and remove successive items, or start at the back and remove predecessors. That would change the existing chain instead of merely pointing one name at another chain.

**Key items to scan**

- **`py .\test\fruit.py`** — PowerShell invocation whose visible output starts with the empty case at revision **`8887c71`**.
- **`Item()`** — constructs a new placeholder each time; it does not erase another node.
- **`fruit`** — reused name; after rebinding, it refers to the new node.
- **`fruit.append("apple")`** — fills the first placeholder; the terminal shows apple.
- **`fruit.prepend("banana")`** — fills the second placeholder; the terminal shows banana.
- **`fruit.insertBefore("grape")`** — tests an empty third list; the printed heading in the source is still **`Prepend:`**.
- **`fruit.insertAfter("strawberry")`** — tests an empty fourth list; again the label does not name the actual **method call**.
- **`\n`** — inserts a newline before each heading. **`None`** is the empty payload printed below the divider.
- **Clear operation** — proposed in-place removal; not demonstrated as a working method here.

### What survives a reset? Follow the names (31:39–35:59)

After **`fruit = Item()`** is executed a second time, there is no route *through that name* to the first apple. What if someone else still holds it? This motivates **reference counting**: reasoning about how many references keep the same object accessible. The instructor connects construction to **`__new__`** (creates the instance) and **`__init__`** (in this class, sets the stored value), and sketches one lasting reference from the name after construction. The sketch is not a measurement of the interpreter’s total references during a call.[2][3] Nor does the high-level description of **garbage collection** as a periodic parallel process establish a separate process, a collection time, or removal of the object while someone still refers to it. No collection or count is measured on screen in this passage.

At 34:51 the editor adds **`veg = fruit`** immediately after the first construction. Does that make another item? No: two names now reach the same object; that is an **alias**. The instructor mentions using a similar name for a traversal. This was an editor illustration, not a rerun of the terminal experiment.

Annotated teaching version — guide-added comments. First two assignments reproduce the draft; the prints and later operations form an independently tested transfer example:

```python
fruit = Item()                # One new placeholder; its payload, not the Item, is None.
veg = fruit                   # A second name for this very same Item; no constructor call.
print(veg is fruit)           # True: both names reach one object.
fruit.append("apple")         # Change the shared object through fruit.
print(veg.object())           # apple: veg sees that same change.
fruit = Item()                # Bind only fruit to a different new, empty Item.
print(veg is fruit)           # False: veg still reaches the earlier apple.
print(fruit.object())         # None: fruit now reaches the new placeholder.
```

Independently verified output, not class scrollback:

```text
True
apple
False
None
```

When asked what **`fruit`** represents after the first assignment, a student answers “None.” Test that prediction against the earlier successful append. If **`fruit`** really were **`None`**, Python could not find the append operation on it in the first place. The instructor predicts this error verbally; an independent check of **`fruit = None`** followed by **`fruit.append("apple")`** raised **`AttributeError: 'NoneType' object has no attribute 'append'`**. The distinction to carry forward is between a reference to an existing object and the empty value *inside* that object.

**Key items to scan**

- **`fruit = Item()`** — creates an object and binds a name; it does not bind **`fruit`** to **`None`**.
- **`__new__`** — the construction step that creates the instance.
- **`__init__`** — initializes that instance’s stored value; without a fruit argument it stores **`None`**.
- **`veg = fruit`** — creates an **alias**, not a new **`Item`**; a change through one name is visible through the other.
- **Reference counting** — another reference can keep the same apple node reachable even after **`fruit`** changes.
- **Garbage collection** — the class’s reason for discussing unreachable objects, not an event observed in the terminal.
- **`AttributeError: 'NoneType' object has no attribute 'append'`** — independently verified counterexample; not the result of calling append on the real empty **`Item`**.

By 38:00 the alias illustration has been undone. The instructor calls what passes between names an “address” or “ID.” In the call that follows, Python passes a reference to the object, not an integer on which the caller does address arithmetic. **`id()`** is a different operation.[2]

## 37:12–42:39 · Follow one item through two calls

The class follows **`display( fruit )`** into **`def display(list):`**. The caller supplies an object as the *argument*; **`list`** is the *parameter*, a new local name for that same object. The question “Where did **`self`** come from?” is about the next call: in **`list.first()`**, the object before the dot supplies the method's first parameter. No second list is built, and the caller does not type the object a second time between the parentheses. Here **`list`** is the instructor's parameter name, not a conversion to a built-in Python list.

The question “What does **`first()`** return?” has two answers depending on the starting point. On the empty node it returns that very node. From a later node it returns a different object, the one at the front. Independently verified with an explicit two-node fixture: starting at banana with apple immediately before it, the first node holds apple; starting at apple, the last node holds banana. To see why, compare those two links with the empty-case trace:

| Call or step | Name and object in the empty test | What happens next |
|---|---|---|
| Caller invokes display | `fruit` refers to a real, empty node | The parameter `list` refers to that same node. |
| Display asks for the first node | `self` in `first` is the object before the dot | The method's own `iter` initially refers to `self`. |
| Method tests its predecessor | `iter._prev` is `None` | It breaks and returns that same node. |
| Display receives the result | Display's **different local** `iter` now refers to the node returned | It can read the payload and obtain the next node. |

**Key items to scan**

- **`display( fruit )`** — sends the existing object into a function; the spaces inside the parentheses do not copy it.
- **`def display(list):`** — gives that function call its own parameter named **`list`**.
- **`list.first()`** — a method call on that parameter's object; **`self`** receives the object before the dot.
- **`first()`** — follows **`_prev`** toward the front; on the empty node it returns the starting node, not **`None`**.
- **`last()`** — follows **`_next`** toward the back; on a one-node list the first and last are the same node.
- **`iter`** — each function call has its own local name; returning one node lets the caller bind it to a different local name with the same spelling.

### What is printed, and what is returned?

In **`display`**, the hard work is the printing. What, then, comes back to the caller? The answer is **`None`**, because execution reaches the end of the function. That is *not* why the screen shows the word **`None`** for an empty item. That line comes from printing the node's *stored value*, before the function exits.

Annotated teaching version — guide-added checks. With the same **`display`** function defined above, capture its return separately:

```python
result = display(Item())              # Guide check: prints the divider and empty payload.
print(result is None)                 # Guide check: separately displays True.
```

Independently verified values, in order:

```text
-----
None
True
```

**Key items to scan**

- **`display`** — prints values as its effect; falling off the end returns **`None`**.
- **`print(iter.object())`** — the source of the visible **`None`** in the empty case; it prints a payload, not a return from display.
- **`iter.next() == iter`** — the end test works here because this class's accessor returns the current node at the boundary.
- **`break`** — leaves this loop; control then reaches the end of the function.
- **`result is None`** — the independent test distinguishes the function's return from what appeared on the screen during the call.

## 42:39–50:12 · Losing a name is not the same as losing the object

What changes when **`iter = iter.next()`** runs? Not the old node, and not a numeric address plus one. Python first calls the method on the current node, then makes the local name refer to the object it got back. The instructor contrasts this with a contiguous array: linked nodes need not sit next to each other in memory because an explicit link identifies the successor. No code in this traversal does memory-address arithmetic.

What happens to **`iter`** when display returns? Its local binding no longer gives the caller a way to reach that node. In the initial empty case, the caller still has **`fruit`**. A reference count concerns *references to the object*, not the spelling of one variable. Taking away one reference need not destroy the object; adding an alias need not copy it. No reference counts were measured here; the earlier terminal’s object representations are not a measurement of these later bindings.

At 47:00 the instructor's editor goes a step further: **`del fruit`** appears between the empty-list display and the apple append. This deletion is materially different from assigning a fresh item to the same name. Deletion leaves that name unbound; reassignment leaves it usable but pointing somewhere else.

Annotated teaching version — guide-added comments. Editor state at 47:00–50:00; the following result is **independent**, not a classroom traceback.

```python
fruit = Item()                 # Bind the name to a new empty node.
print("\nEmpty:")              # Print a blank line and the Empty: heading.
display( fruit )               # Print five dashes, then the empty payload None.
del fruit                      # Remove the caller's binding; this does not call a list method.
fruit.append("apple")          # Cannot look up fruit; no append method is called.
```

Independent module-level test, with the blank leading line omitted:

```text
Empty:
-----
None
NameError: name 'fruit' is not defined
```

An unbound name fails at lookup (**`NameError`**). A bound name whose value is **`None`** would instead fail when looking for its missing append method (**`AttributeError`**). An alias, if one had been kept under another name, could still reach the old node; it would not restore the deleted name.[4] The successful apple and later fruit values already in the classroom terminal do not show this deletion edit running.

**Key items to scan**

- **`iter = iter.next()`** — replaces one local reference with the returned successor; no one-byte increment or change to the old node.
- **`iter`** — a local name in display, unavailable to the caller once the call completes.
- **`fruit`** — the caller's independent reference survives the end of display.
- **`del fruit`** — removes this name. It neither clears every node nor promises immediate memory reclamation.
- **`NameError`** — in the deletion draft, lookup of the now-unbound name fails before an apple can be appended.
- **`AttributeError`** — different failure: a lookup *found* **`None`**, but that object has no append method.

### Why clearing a doubly linked list is a different question

The proposed **clear method** returns to an earlier design question: if a program owns a long chain, is dropping its one outside name the same as deliberately disconnecting its links? Suppose apple points forward to banana and banana points back to apple. Even after the outside names go away, each node still has a reference coming from the other. One node with no neighbors doesn't have that complication. A reference that some *other part of the program* keeps is yet another case: that node is still accessible there.

The instructor suggests walking from one end, removing or unlinking nodes, and asks students to consider a long-running server where thousands of users repeatedly create and discard lists. Memory held between uses may matter there even when waiting for a short classroom program to end seems harmless. This is a design discussion, not a new implemented method, measured memory leak, or requirement that Python programmers manually delete every object.

To separate that concern from a claim about what Python must do, a small independent CPython test made exactly the two-way link above. With automatic cyclic collection temporarily disabled, a non-owning reference still saw the apple node after both outside names were deleted. After explicit cyclic collection it no longer did. Thus simple reference counts alone do not explain the whole outcome. An unreachable cycle can be reclaimed; the class's high-level “collector comes along” picture doesn't guarantee a particular cleanup time or a separate always-running process.[2][3] No such collector experiment was shown in class.

**Key items to scan**

- **Clear method** — a proposed operation on the structure itself; not the same as replacing one caller's name with a fresh empty object.
- **Reference count** — counts live references, including neighbor links, not just variables a programmer can name.
- **Cycle** — apple's forward link and banana's backward link keep each other referenced even without an external name.
- **Cyclic collection** — the independent test reclaimed that unreachable pair; it did not reclaim anything still reachable by an alias.
- **Long-running server** — the instructor's scale example explains why delayed reclamation can matter; no performance result was measured.

## 50:18–closing · Four empty starts, not one four-fruit list

The instructor returns to the earlier successful driver. Its question is narrow: can each way of adding a value work when there is **no existing fruit to go before or after**? Each test starts from a new **`Item()`**. The earlier apple, banana, and grape are not transferred into the next test. Calling a method *on* the current node changes it; assigning **`fruit = Item()`** later switches the name to a new object.

Classroom terminal, read across the visible scrollback at 35:25 and 51:00–51:25. Each row represents one fresh-start test. A blank line precedes each heading, and the divider is five hyphens. Rows preserve the labels the students actually saw.

| Test | First heading → divider → payload | Second heading → divider → payload |
|---|---|---|
| Append | `Empty:` → `-----` → `None` | `Append:` → `-----` → `apple` |
| Prepend | `Empty:` → `-----` → `None` | `Prepend:` → `-----` → `banana` |
| Insert before | `Empty:` → `-----` → `None` | `Prepend:` → `-----` → `grape` |
| Insert after | `Empty:` → `-----` → `None` | `Prepend:` → `-----` → `strawberry` |

With no existing fruit to order, the four operations have the same observable effect: the placeholder now holds one value. That test cannot distinguish a correct middle insertion from one that forgets to repair a backward link; the separate apple–grape–banana trace tests that difference.

The class catches a different problem at 51:01: copying a print line can leave the *explanation* wrong even when the data is right. The strawberry call is visibly **`fruit.insertAfter("strawberry")`**, yet its heading is **`Prepend:`**. The instructor proposes changing that heading. It is still unchanged at 52:00; no corrected run is shown. The grape heading has the same mismatch. To review a test like this, check the call, starting state and stored result before trusting the label. The instructor stops at this revision, invites questions, and encourages the class to try variations and break things rather than assume one passing test covers every case.

**Key items to scan**

- **`Item()`** and **`fruit = Item()`** — each fresh start creates a new node; the previous test's fruit is not in the next list.
- **`fruit.append("apple")`** — first case; the only value printed after the append is apple.
- **`fruit.prepend("banana")`** — second case; banana is the only value, not a new neighbor of apple.
- **`fruit.insertBefore("grape")`** — third case; grape appears under the copied **`Prepend:`** heading.
- **`fruit.insertAfter("strawberry")`** — fourth case; strawberry also appears under **`Prepend:`**, not a heading naming insert-after.

- **`Prepend:`** — literal text, not a report generated from the method name. A copied label can survive a change to the call.

## Syntax to remember

| Pattern | What to ask in this driver |
|---|---|
| `fruit = fruit.last()` | Which node comes back? The assignment moves the name to peach; it does not move the fruit values. |
| `iter.next() == iter` | Does the next accessor hand back the node already being displayed? If so, break *after* printing. |
| `self._next._prev = newItem` | Follow the old next link to the neighbor; set that neighbor’s backward reference before changing the original next link. |
| `newItem = self` versus `newItem = Item(obj)` | The first names an existing node. Only the second constructs another node. |
| `print(iter.object())` versus `return iter` | One sends a payload to the screen. The other sends a node back to the caller. |
| `del fruit` versus `fruit = Item()` | The first removes the name; the second keeps the name usable but binds it to a new object. |

## Knowledge check

1. Immediately after **`fruit = Item()`**, predict the results of **`fruit is None`**, **`fruit.object()`**, and **`fruit.next() is fruit`**. Why can the three answers differ?
2. If the empty-list branch of **`append`** simply made a new node after the existing one, what would the classroom display function print for an initial append of apple? Contrast this with the actual design.
3. Imagine two distinct adjacent nodes both holding **`"apple"`**. What should **`display`** do at the first? Would comparing their payloads instead of their nodes preserve the stopping rule?
4. Suppose **`veg = fruit`** occurs just before **`fruit = Item()`**. Can the old apple still be reached? What changes if the only remaining reference to the old apple is removed?
5. The terminal shows **`Prepend:`** followed by **`strawberry`**. Which evidence tells you what operation the instructor tested? Why is the same reasoning important just after a Git checkout?

### Answer key

1. **`False`; `None`; `True`.** The name refers to a real node; its payload is empty; its public navigation method returns that same node when there is no successor. Neither returning a value nor checking identity prints anything by itself.
2. With the placeholder left intact, a traversal starting there would print **`None`** and then **`apple`** after the divider. Instead the empty branch writes apple into the existing node, so the actual append test displays only **`apple`** below the divider.
3. It should print the first apple and continue to the second. The two *nodes* differ even if the two *values* compare equal. A test comparing payloads could mistake an ordinary pair of neighbors for the end.
4. Yes: **`veg`** still names the old node. Rebinding **`fruit`** does not clear the old object. If nothing reachable points to it, reclamation becomes possible. Losing one name neither forces immediate cleanup in every Python implementation nor deletes a node that is still reachable.[2][3]
5. Read the driver call: **`fruit.insertAfter("strawberry")`**. The next line prints an independent string copied from the prepend test. A heading is not an execution trace; likewise, a successful checkout changes the working files but does not rewrite earlier terminal scrollback.

## Library of definitions

| Term | Meaning in this lesson |
|---|---|
| Doubly linked list | Nodes connected in both directions. From a middle fruit one can walk to the head or the tail without rearranging the fruit values. |
| Node / payload | An `Item` is the node. Its `_object` field holds the payload, such as `"apple"`; the placeholder node has `None` there. |
| Constructor / initialization | `Item()` produces a fresh instance. `__new__` creates the instance; `__init__` initializes its state. Assignment to `fruit` gives that result a name.[2] |
| Accessor / internal field | `next()` is the operation offered to callers. `_next` is an internal field; the method can return `self` even when the field contains `None`. |
| Head / tail | The first node has no previous neighbor; the last has no next neighbor. A single node is both. |
| Edge case / sentinel | A boundary case that needs checking. Here `None` in the payload marks an empty list; `None` in a link means a neighbor is absent. These tests answer different questions. |
| Insertion / overwrite | Inserting into a populated stretch adds a node and preserves the neighboring values. Filling the empty placeholder assigns a payload without allocating a second node. |
| Reference / alias | A way to refer to an existing object. A second name for it is an alias, not a copy. |
| Argument / parameter / receiver | In `display(fruit)`, `fruit` supplies the argument; the parameter `list` names that object inside the function. In `list.first()`, that object is the receiver supplied to the method as `self`. |
| Traversal / cursor | Following links from node to node. `iter` is the local name tracking the current position; its spelling does not make this an implementation of Python’s iterator protocol. |
| Identity / equality | Identity asks “the same object?” Equality asks what the objects’ comparison says. With the classroom class as shown, comparing nodes does not compare fruit strings. |
| Local binding / rebinding | A name inside one function call, or assigning an existing name to a different object. Moving the traversal variable does not move a node in memory. |
| Reference count / cycle | CPython tracks references to an object. Two nodes that point back at each other can keep positive counts even after the program loses its last external path.[2][3] |
| Working tree / detached HEAD | The files available to edit, and a Git state in which the checked-out commit is selected directly. The classroom terminal can still show output from a previous revision. |

**Supplemental documentation.** The Python data model distinguishes a reference from the integer returned by **`id()`** and explains implementation-dependent reclamation.[2] The **`gc`** documentation describes cyclic collection as a supplement to reference counting, not a guaranteed separate process.[3] The **`del`** statement removes a binding when its target is a name; it is not a command to destroy all aliases or linked neighbors.[4]

## Sources

- [1] <https://www.youtube.com/watch?v=9IFcP9rgUFw&t=2s>
- [2] <https://docs.python.org/3/reference/datamodel.html>
- [3] <https://docs.python.org/3/library/gc.html>
- [4] <https://docs.python.org/3/reference/simple_stmts.html>

## Historical screen-code appendix · three Git checkpoints and two working edits

### The insert-after checkpoint: `item.py` and `fruit.py` · 02:00–03:00

The numbers before the vertical bars reproduce the editor gutter; **do not type the numbers or bars**. Numbered blocks here preserve classroom code, not the guide-added comments used in the teaching sections. A frame time identifies a sampled screen, not a save or an execution.

**`item.py` — assembled visual reconstruction.** **`minute-00h02m00s000.png`** shows lines 1–35; **`minute-00h03m00s000.png`** shows 25–48. Lines 25–35 agree across the two views. This is the class visible while the terminal identifies **`7630f8b`** as HEAD; the two samples cannot establish every intervening editor action.

```text
 1 | class Item:
 2 |     _object = None
 3 |     _next = None
 4 |     _prev = None
 5 |
 6 |     def __init__(self, obj = None):
 7 |         self._object = obj
 8 |
 9 |     def object(self):
10 |         return self._object
11 |
12 |     def next(self):
13 |         if self._next != None:
14 |             return self._next
15 |         return self
16 |
17 |     def prev(self):
18 |         if self._prev != None:
19 |             return self._prev
20 |         return self
21 |
22 |     def append(self, obj):
23 |         last = self.last()
24 |         newItem = Item(obj)
25 |         newItem._prev = last
26 |         last._next = newItem
27 |         return newItem
28 |
29 |     def last(self):
30 |         iter = self
31 |         while True:
32 |             if iter._next == None:
33 |                 break
34 |             iter = iter._next
35 |         return iter
36 |
37 |     def insertAfter(self, obj):
38 |         newItem = Item(obj)
39 |         if self._next != None:
40 |             newItem._prev = self
41 |             newItem._next = self._next
42 |             self._next._prev = newItem
43 |             self._next = newItem
44 |         else:
45 |             newItem._prev = self
46 |             self._next = newItem
47 |         return newItem
```

Lines 1–20 establish the stored value and the two navigation rules. Lines 22–35 add a newly created node beyond the current last node, whereas 37–47 distinguish inserting in the middle (repair the link from the old right neighbor too) from inserting at the end. There is no empty-value guard in this version of append or insert-after.

**`fruit.py` — observed working-file buffer**, right pane in both frames above. The terminal lists this file as locally modified. The first two executable statements after the import test a separate empty list; the longer test keeps **`fruit`** on apple because line 9 is commented out.

```text
 1 | from item import Item
 2 |
 3 | veg = Item()
 4 | print(veg.last().object())
 5 |
 6 | fruit = Item("apple")
 7 | fruit.append("banana")
 8 | fruit.append("peach")
 9 | #fruit = fruit.last()
10 | fruit.insertAfter("avocado")
11 |
12 | iter = fruit
13 | while True:
14 |     print( iter.object() )
15 |     if iter.next() == iter:
16 |         break
17 |     iter = iter.next()
18 | print(iter.last())
19 | print(iter)
```

Lines 12–17 print each stored fruit. Lines 18–19 print the endpoint object, not its value. The earlier terminal run and its exact output are transcribed in the teaching section; that run appears above the checkout command in scrollback.

### After restoring the tracked files: `ab909a4` · 05:00–05:03

**`item.py` — partial, assembled visual reconstruction.** **`event-00h05m03s000.png`** shows lines 16–50; **`minute-00h05m00s000.png`** shows 25–59. The overlapping methods and gutter align; lines before 16 and the code below 59 are not reconstructed for this checkpoint. In particular the call to **`insertBefore`** is visible in the driver, but its definition is not in either editor view.

```text
16 |
17 |     def prev(self):
18 |         if self._prev != None:
19 |             return self._prev
20 |         return self
21 |
22 |     def append(self, obj):
23 |         last = self.last()
24 |         newItem = Item(obj)
25 |         newItem._prev = last
26 |         last._next = newItem
27 |         return newItem
28 |
29 |     def prepend(self, obj):
30 |         first = self.first()
31 |         newItem = Item(obj)
32 |         newItem._next = first
33 |         first._prev = newItem
34 |         return newItem
35 |
36 |     def last(self):
37 |         iter = self
38 |         while True:
39 |             if iter._next == None:
40 |                 break
41 |             iter = iter._next
42 |         return iter
43 |
44 |     def first(self):
45 |         iter = self
46 |         while True:
47 |             if iter._prev == None:
48 |                 break
49 |             iter = iter._prev
50 |         return iter
51 |
52 |     def insertAfter(self, obj):
53 |         newItem = Item(obj)
54 |         if self._next != None:
55 |             newItem._prev = self
56 |             newItem._next = self._next
57 |             self._next._prev = newItem
58 |             self._next = newItem
59 |         else:
```

Lines 22–34 show the paired endpoint additions: each finds an end, constructs a node and links it in both directions. Lines 36–50 find the ends without changing any links. The partial block at 52–59 shows the middle-insertion path and the start of an alternative; the earlier checkpoint is not a source for the unseen lines of this one.

**`fruit.py` — observed**, same two frames. Line 6 now actively rebinds **`fruit`** to peach; insert-before uses that position, but prepend first finds the other end. The driver resets its traversal name to the beginning at line 11. No five-fruit run is shown in this pair of frames.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 | fruit = fruit.last()
 7 | fruit.insertBefore("mango")
 8 | fruit.prepend("acai")
 9 |
10 |
11 | iter = fruit.first()
12 | while True:
13 |     print( iter.object() )
14 |     if iter.next() == iter:
15 |         break
16 |     iter = iter.next()
```

### `src/item.py` — empty-list edge-case revision

Assembled visual reconstruction of compatible editor views, not a checkout performed for this guide. The numbers before the bar reproduce the editor gutter; they are **not code to type**. The 30:00 terminal shows **`8887c71 Item: empty list edge case`**. No single still shows all the lines, so the joins rely on compatible line numbering and overlap, not proof that nothing off screen changed between captures.

At 18:00, lines 1–30 show the class defaults and constructor: each instance gets its stored value from the constructor; untouched links resolve to the class defaults. The three accessors return the stored fruit or a neighboring **`Item`**; at a boundary the navigation methods return the current item. At 19:00, overlapping lines 7–36 complete **`append`** and begin **`prepend`**.

```text
 1 | class Item:
 2 |     _object = None
 3 |     _next = None
 4 |     _prev = None
 5 |
 6 |     def __init__(self, obj = None):
 7 |         self._object = obj
 8 |
 9 |     def object(self):
10 |         return self._object
11 |
12 |     def next(self):
13 |         if self._next != None:
14 |             return self._next
15 |         return self
16 |
17 |     def prev(self):
18 |         if self._prev != None:
19 |             return self._prev
20 |         return self
21 |
22 |     def append(self, obj):
23 |         if self._object == None:
24 |             self._object = obj
25 |             newItem = self
26 |         else:
27 |             last = self.last()
28 |
29 |             newItem = Item(obj)
30 |             newItem._prev = last
31 |             last._next = newItem
32 |         return newItem
```

Lines 34–43 use the same empty guard for prepending. Otherwise locate the old first node, make a distinct new first node, and connect both directions. Lines 45–59 walk the underlying links, stopping at the missing link and returning the current node. Sources: 19:00 for lines 33–36; 17:00 for lines 37–59 and the overlap into **`insertAfter`**.

```text
33 |
34 |     def prepend(self, obj):
35 |         if self._object == None:
36 |             self._object = obj
37 |             newItem = self
38 |         else:
39 |             first = self.first()
40 |             newItem = Item(obj)
41 |             newItem._next = first
42 |             first._prev = newItem
43 |         return newItem
44 |
45 |     def last(self):
46 |         iter = self
47 |         while True:
48 |             if iter._next == None:
49 |                 break
50 |             iter = iter._next
51 |         return iter
52 |
53 |     def first(self):
54 |         iter = self
55 |         while True:
56 |             if iter._prev == None:
57 |                 break
58 |             iter = iter._prev
59 |         return iter
60 |
```

Sources: 19:37, 20:00, and 20:16. In **`insertAfter`**, the selection moves from the empty case to the four assignments between neighbors and then to the two assignments at the tail; the selections are not new implementations. Lines 77–84 show only the start of **`insertBefore`**. Line 85 is cut vertically at the bottom of the most informative view; a dependable continuation or return line cannot be transcribed from these frames.

```text
61 |     def insertAfter(self, obj):
62 |         if self._object == None:
63 |             self._object = obj
64 |             newItem = self
65 |         else:
66 |             newItem = Item(obj)
67 |             if self._next != None:
68 |                 newItem._prev = self
69 |                 newItem._next = self._next
70 |                 self._next._prev = newItem
71 |                 self._next = newItem
72 |             else:
73 |                 newItem._prev = self
74 |                 self._next = newItem
75 |         return newItem
76 |
77 |     def insertBefore(self, obj):
78 |         if self._object == None:
79 |             self._object = obj
80 |             newItem = self
81 |         else:
82 |             newItem = Item(obj)
83 |             if self._prev != None:
84 |                 newItem._next = self
```

### `test/fruit.py` — helper and four isolated empty starts

Assembled visual reconstruction before the alias edit. Use 20:35 and 21:00 for lines 1–30; 22:08 for the full line 32; 31:00 for the lower driver through line 42. In the 31:00 pane, line 32 reads **`fruit.insertBefore("grape"[remainder not visible]`** under the camera overlay. The closing parenthesis in the listing is recovered from **22:08**, where the entire line is legible, not supplied from Python syntax. The full line 40 also comes from **31:00**; it is bottom-clipped at 30:00. Indentation is represented with spaces.

```text
 1 | from item import Item
 2 |
 3 | def display(list):
 4 |     iter = list.first()
 5 |     print("-----")
 6 |     while True:
 7 |         print(iter.object())
 8 |         if iter.next() == iter:
 9 |             break
10 |         iter = iter.next()
11 |
```

The driver imports the class; it does not get the helper from that import. Lines 3–10 define a function that first prints the separator and then each payload from left to right, using the class-specific end check. The definition itself prints nothing. Each of the four pairs below calls it before and after a different operation on a freshly constructed list.

```text
12 | fruit = Item()
13 | print("\nEmpty:")
14 | display( fruit )
15 |
16 | fruit.append("apple")
17 | print("\nAppend:")
18 | display(fruit)
19 |
20 | fruit = Item()
21 | print("\nEmpty:")
22 | display( fruit )
23 |
24 | fruit.prepend("banana")
25 | print("\nPrepend:")
26 | display(fruit)
27 |
```

```text
28 | fruit = Item()
29 | print("\nEmpty:")
30 | display( fruit )
31 |
32 | fruit.insertBefore("grape")
33 | print("\nPrepend:")
34 | display(fruit)
35 |
36 | fruit = Item()
37 | print("\nEmpty:")
38 | display( fruit )
39 |
40 | fruit.insertAfter("strawberry")
41 | print("\nPrepend:")
42 | display(fruit)
```

The literal labels on lines 33 and 41 both say **`Prepend:`** even though the actual calls are **`insertBefore`** and **`insertAfter`**. The terminal at 29:00 and 30:00 visibly shows **`None`**, **`apple`**, **`None`**, **`banana`**, **`None`**, each with its own driver label and divider. No output from a reference-count check or a later alias edit is visible there.

**Additional overlap checks.** The 06:00, 15:43, 16:00 and 17:00 views corroborate the class prefix through line 66. The 14:00 view also exposes the banana call and its label (driver lines 24–25), partly behind the camera at 06:00.

### `fruit.py` · Alias experiment, undo and the local cursor

Observed variant, 35:25 and 36:00. The recently added alias changes the line numbers below it. The selection on **`fruit.append("apple")`** illustrates a hypothetical failure on a **`None`** receiver; no such exception appears in the observed output. Lines 12–31 below are visible across 35:25–37:00; subsequent driver lines are omitted here because this excerpt isolates the changed region.

```text
12 | fruit = Item()
13 | veg = fruit
14 | print("\nEmpty:")
15 | display( fruit )
16 |
17 | fruit.append("apple")
18 | print("\nAppend:")
19 | display(fruit)
20 |
21 | fruit = Item()
22 | print("\nEmpty:")
23 | display( fruit )
24 |
25 | fruit.prepend("banana")
26 | print("\nPrepend:")
27 | display(fruit)
28 |
29 | fruit = Item()
30 | print("\nEmpty:")
31 | display( fruit )
```

The alias first appears at 34:51. By 38:00–42:40 the unmodified helper and driver prefix are visible again; the constructor is back on line 12 and the label, not an alias, follows it.

At 44:00–46:17, lines 11–13 are blank, the constructor moves from 12 to 14, and the apple call moves from 16 to 18. These spacing changes alone do not alter what executes. The next meaningful edit does.

### `fruit.py` · `del` draft and final copied headings

Assembled visual reconstruction, not a single screenshot or a verified saved file. Lines 1–30 are readable across 49:00 and 50:00 (line endings of the display function independently legible in the 50:00 upper crop). Lines 31–47 are visible at 51:00 and 51:25, with 28–47 also visible at 52:00: 28–30 overlap the earlier part. Continuity of lines outside the currently visible pane is an assumption, not evidence against an intervening edit. The title at 51:00 identifies **`test\fruit.py`**.

```text
 1 | from item import Item
 2 |
 3 | def display(list):
 4 |     iter = list.first()
 5 |     print("-----")
 6 |     while True:
 7 |         print(iter.object())
 8 |         if iter.next() == iter:
 9 |             break
10 |         iter = iter.next()
11 |
12 |
13 |
14 | fruit = Item()
15 | print("\nEmpty:")
16 | display( fruit )
17 | del fruit
18 |
19 | fruit.append("apple")
20 | print("\nAppend:")
21 | display(fruit)
22 |
23 | fruit = Item()
24 | print("\nEmpty:")
25 | display( fruit )
26 |
27 | fruit.prepend("banana")
28 | print("\nPrepend:")
29 | display(fruit)
30 |
31 | fruit = Item()
32 | print("\nEmpty:")
33 | display( fruit )
34 |
35 | fruit.insertBefore("grape")
36 | print("\nPrepend:")
37 | display(fruit)
38 |
39 | fruit = Item()
40 | print("\nEmpty:")
41 | display( fruit )
42 |
43 | fruit.insertAfter("strawberry")
44 | print("\nPrepend:")
45 | display(fruit)
46 |
47 |
```

- Lines 14–19 contain a consequential change: **`del fruit`** is now between the display and the apple call. If executed as shown at module level, the next lookup of **`fruit`** fails before reaching the later tests. A separate probe reproduced **`NameError: name 'fruit' is not defined`**; that exception is not present in the class terminal.
- Lines 23, 31 and 39 start new independent tests. Lines 35 and 43 call different methods; neither calls **`prepend`**, despite the two **`Prepend:`** print literals. No edited heading is visible at 52:00.
- The left-hand terminal at 51:00 shows earlier successful scrollback ending in a prompt, not evidence that this entire **`del`** draft succeeded. The strawberry portion below is selected at 51:25. It is direct terminal text, not an expected corrected heading:

```text
Prepend:
-----
strawberry
PS C:\CSCS1240F26\Item>
```

## Micro-Learning Insights · removable appendix

- **Name the layer before answering “is it empty?”** At 08:00–13:00, the data, links and node give three different answers: no fruit, no neighbor, but a real object on which to call a method.
- **Trace the first addition separately.** At 15:00–16:00, the first append fills the placeholder. **`newItem = self`** gives the common return statement a name to return; it does not create or connect a node.
- **Keep both directions on paper.** For a middle insertion, check “old node → new node → old successor” and then read the arrows backwards. Forward traversal alone cannot expose every broken previous link (19:00–20:00).
- **Start a `while True` review at the exit.** In **`display`**, the last node must be printed *before* the equality check breaks the loop. Moving the print after the check would lose the last fruit (22:00–26:00).
- **Test repeated data, not just different fruit.** Two distinct nodes can carry the same string. Ask whether a proposed duplicate check compares fruit values, node identity, or links before calling the result a bug (26:00–28:00).
- **Reset the precondition, not just the heading.** Repeating **`fruit = Item()`** lets the append, prepend, insert-before and insert-after cases each start with no payload. Reusing the name is not reusing the first object (28:00–31:00).
- **Follow a call by asking who receives it.** In **`display(fruit)`**, **`list.first()`** calls **`first`** on the same object the caller named **`fruit`**. The local **`self`** is not an extra node (37:00–41:00).
- **Do not add one to an address to find the next fruit.** **`iter = iter.next()`** follows the link stored by this node. The linked nodes need not be beside each other in memory (43:00–45:00).
- **Losing a name is not losing all paths.** An alias or a neighbor can still refer to the node after a local disappears. For a long-lived program, reason about *reachability*, not merely which names you deleted (45:00–50:00).[2][3][4]
- **Read the call before trusting the label.** Both grape and strawberry appear under **`Prepend:`** in the captured tests. Check the arguments and method names before diagnosing a broken insertion (50:00–51:25).
