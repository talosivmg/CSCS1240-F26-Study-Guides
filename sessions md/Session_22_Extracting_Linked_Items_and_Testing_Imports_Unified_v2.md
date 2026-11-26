# CSCS1240F26 · Session 22 | Taking an Item out without losing the list

**Worked teaching companion · Unified v2**  
[Class recording: CSCS1240F26 22, JoesTA](https://www.youtube.com/watch?v=qOvl06SSbjI&t=1s) · October 21, 2026

If you take item 5 out of a chain of ten, what should the caller hold afterward? In this class the caller can keep **both** the isolated fifth item and a reference into the surviving chain. Which one comes back from the method is the key to reading the code. The second half asks a related question about testing: how can a file demonstrate its own class without printing that demonstration whenever another program imports it?

**Reading route.** Start with the assignment boundary and the meaning of an empty **`Item`**. Trace the four extraction cases, follow a fruit driver that saves two different references, then diagnose an import failure before interpreting the test output. The knowledge check, definitions, numbered screen record and detachable recall notes are at the end.

**Evidence.** The saved English auto-captions contain 1,445 cues from the opening to the sign-off. Their last cue begins at 59:12.160 and nominally ends at 59:15.200; the previously probed video ends at 59:14.217. The extra fraction in the caption timing is not extra footage. All 60 whole-minute pictures (00:00–59:00), five event pictures and the 59:12 closing picture match the capture manifest and decode at 1920×1080. Times below identify requested image times or approximate discussion windows, not exact decoded video timestamps. A screen shows a buffer or terminal at an instant, not every edit or save. The separate Python practice checks below are not classroom runs.

## 1 · What the challenge asks, and what today adds | 00:00–05:51

A student had reached challenge 2060 and asked whether another had been released. The instructor said **2070** was available that morning. Its starting point was the doubly linked list from about two classes earlier: a class named **`Item`**, navigation to the ends, and adding an item at either end or on either side of a chosen item. The instructor also named forward and backward accessors; the code spells them **`next()`** and **`prev()`**. The grader would import the submission and exercise the class itself. An unexpected line on standard output could therefore matter even if the links were correct.

Allowing an empty list was a more recent design change. Extracting an item is **today’s extension**, not a stated 2070 requirement. The instructor speculated it might come later. The captions also render the later recap as “Challenge 27”; the opening release discussion uses 2070. The oral overview is not a substitute for the assignment’s written contract.

The instructor said the starting point was ten revisions back. The terminal later preserves the completed **`git checkout master~9`**: **`HEAD`** moved from **`8887c71`** (“empty list edge case”) to **`a5c546d`** (“extract( ). Cannot extract last value, but can check return for empty”). **`master~9`** means nine first-parent steps back from **`master`**, not nine more steps back from whatever happens to be on screen. It was a way to inspect a particular historical implementation, not an instruction to overwrite your working copy. The commit note makes sense once you distinguish the original one-item receiver from the *new empty remainder*.

With this revision available, the instructor posed a design question: if objects are constructed for us, what would it mean to *delete* one from this structure? In Python, **`__new__`** creates the instance and **`__init__`** initializes it; **`del`** can remove a name without destroying an object that another reference still reaches. The proposed operation is about membership, not forcing destruction. Many designs distinguish a list container from its nodes. Here an **`Item`** is itself a point of access into a chain, so removing that very item raises a practical question: how will the caller still reach the rest?

A student suggested that *extract* means the item does not go away. The instructor initially described returning the removed element. Keep that design intention separate from the implementation: **the object on which you call the method becomes isolated; `return rest` gives you an entry into what remains.** No automatic move to a new storage location is needed.

**Key items to scan**

- **`git checkout master~9`** — moved to the recorded extraction revision. The editor and old terminal lines do not automatically describe the same execution.
- **`Item`** — the class submitted for import; its instances also serve as nodes. There is no separate list-wrapper object in this example.
- **`first()`**, **`last()`** — find an end starting from the item you hold; they do not remove anything.
- **`append`**, **`prepend`**, **`insertAfter`**, **`insertBefore`** — add at an end or relative to a selected item. These belong to the earlier design; extraction is a separate operation.
- **`__new__` / `__init__`** — creation followed by initialization. Neither means “remove this item from a chain.”
- **`del`** — dropping a name and repairing neighboring links solve different problems.

## 2 · Read the three fields before reading the branches | 05:51–21:54

Suppose the current item’s previous link and next link are both **`None`**. Students first described this as “nothing there.” The instructor asked: *where* is “there”? It is true that there is no **neighbor** on either side. The instructor’s next question—whether to allow an empty list at all—matters because it changes what that answer tells you. It does not follow that the item you are holding contains no **value**. One item storing fruit and the object used to represent an empty list have identical links. The stored object distinguishes them.

| Current item | `_prev` | `_object` | `_next` | What this establishes |
|---|---|---|---|---|
| Empty `Item()` | `None` | `None` | `None` | A real `Item` instance represents no stored fruit. |
| Only apple | `None` | `"apple"` | `None` | One stored value; both first and last. |
| First of several | `None` | A value | Another item | Its successor can become first. |
| Last of several | Another item | A value | `None` | Its predecessor can become last. |
| Interior item | Another item | A value | Another item | At least three items, not necessarily exactly three. |

A singly linked list would follow one direction. Here two links let you walk either way. These rules assume the class has maintained a valid chain; assigning an arbitrary number to a neighbor field would not turn that number into an **`Item`**. When a student asked whether “previous” could be 17, the important missing setup was that some caller had already constructed the instances and joined them with the class methods. A method definition in an editor does not itself build ten items or run an extraction.

Supporting emptiness was a **design choice**, not a Python requirement. Once made, it affects insertions as well as extraction: adding the first value to the empty representative must not leave an extra empty node in front of it. Under this class’s convention **`None`** in the value field denotes no stored item; it cannot also distinguish an ordinary stored **`None`** value.

**Key items to scan**

- **`_prev`** — **`None`** means no predecessor. In a valid chain, the current item is first.
- **`_next`** — **`None`** means no successor. The current item is last, not second-to-last.
- **`_object`** — holds the stored value. Inspect it, not just the links, to distinguish empty from one real item.
- **`self`** — the specific instance used for this call. Its identity and the references stored in its fields are different things.

### Read the methods behind the three fields

The editor supplies a detail the captions alone do not: the raw **`_next`** link can be **`None`** while the public **`next()`** method still returns an **`Item`**. That choice explains the fruit driver’s stopping test later. The class-body assignments provide fallback values of **`None`**; **`__init__`** sets the new instance’s payload. Later assignments such as **`banana._prev = apple`** give that instance its own link. The leading underscore is a naming convention, not protection against all access.

**Annotated teaching version — guide-added comments** (39:00, class opening):

```python
class Item:  # Define the type; this does not construct an apple.
    _object = None  # Default: no stored value.
    _next = None  # Default: no following node.
    _prev = None  # Default: no preceding node.

    def __init__(self, obj = None):  # Item() uses None; Item("apple") supplies apple.
        self._object = obj  # Store that value on this instance.

    def object(self):  # Read the payload, not a link.
        return self._object  # Return apple if this node stores apple.

    def next(self):  # Ask for the following item.
        if self._next != None:  # A raw successor exists.
            return self._next  # Return that existing node.
        return self  # At the end, return the current item instead.

    def prev(self):  # Ask for the preceding item.
        if self._prev != None:  # A raw predecessor exists.
            return self._prev  # Return that existing node.
        return self  # At the beginning, stay on this item.
```

**Key items to scan**

- **`object()`** — returns the payload; the driver’s **`print`** is what displays it.
- **`next()`** — returns a neighbor or, at the end, the current object. It is an ordinary course-defined method, not **`__next__`**.
- **`prev()`** — the backward counterpart; being at the front does not make this method return **`None`**.

## 3 · Extract the item you already hold | 11:11–43:22

The instructor read the method as a series of questions. First, *is this item alone?* If so, make a new empty **`Item`** for the remainder and skip the whole **`else`**. Otherwise at least one neighbor exists. Test the front first, the back second; the final **`else`** is the interior case. The common ending clears the current item’s two links and returns the saved remainder. It does **not** clear its fruit.

**Annotated teaching version — guide-added comments.** This is the method shown at 48:40, formatted compactly for study. It belongs inside **`Item`**; it is not a complete standalone class.

```python
    def extract(self):                       # Isolate the item on which this is called.
        rest = None                          # Placeholder, not yet a surviving Item.
        if self._prev == None and self._next == None:
            rest = Item( )                   # Alone: return a NEW empty representative.
        else:                                # At least one neighbor exists.
            if self._prev == None:            # First of at least two: next exists.
                self._next._prev = None       # The old second item now has no predecessor.
                rest = self._next             # Save the new first item, not a copy.
            else:                            # A predecessor definitely exists.
                if self._next == None:        # Last of at least two.
                    self._prev._next = None   # The old penultimate item becomes last.
                    rest = self._prev         # Return access at that surviving end.
                else:                        # Both neighbors exist: e.g., 4 and 6.
                    self._prev._next = self._next  # 4 now points forward to 6.
                    self._next._prev = self._prev  # 6 now points backward to 4.
                    rest = self._prev         # Save 4 before clearing 5’s links.
        self._prev = None                     # The selected item has no predecessor.
        self._next = None                     # It has no successor either.
        return rest                           # Give the caller the remainder; do not print.
```

### Alone: why assign `None` twice when nothing changes?

For an isolated apple, the two-part test is true. After **`rest = Item( )`**, execution jumps past every neighbor-repair branch. The next executed statement clears the previous link, not the first line of the **`else`**. Both links were already **`None`**. The common cleanup is redundant **on this path**, but necessary on the others. Apple stays apple; the returned item is a *different object* with no fruit. Starting with an empty **`Item()`** takes the same path: you still have the original empty instance and receive a distinct empty instance, not the special value **`None`**.

### Front and back: whose attribute changes?

At the front, follow **`self._next`** **once**: you reach the old second item. Its **`_prev`** currently refers back to **`self`**. Assigning **`self._next._prev = None`** changes a field **on that second item**. It does not set **`self`** to **`None`** or erase its **`_next`**. That is why the next line can still use **`self._next`** to save the survivor. Only then does common cleanup sever the old first item’s remaining link. For two items the survivor is now both first and last.

At the back the process is reversed. If a predecessor exists and the current item’s **`_next`** is **`None`**, **the current item is last**. Its predecessor is second-to-last *before* the edit and last *after* it. Set that predecessor’s **`_next`** to **`None`**, save it as **`rest`**, then detach the original last item. Some spoken ordinal labels switched between the two items. The field being assigned settles which one changes position.

A student wondered whether the chained assignment was a way to change **`self`** indirectly. The useful distinction is ownership: the second item’s field can refer to the same object as the local name **`self`**, but they are not the same place to store a reference. Python allows rebinding the **local name** **`self`**; that would not rebind the caller’s variable or repair either neighbor. Mutating an attribute on a shared object is what other holders of that object can observe. “Pointer” and “address” in the discussion describe following references; this method needs no address arithmetic.

**Key items to scan**

- **`and`** — only the simultaneous absence of both neighbors enters the first branch. Its **`else`** does not mean “both neighbors exist.”
- **`rest = Item( )`** — allocate an empty representative; unlike **`rest = self._next`**, this creates a new instance.
- **`self._next._prev = None`** — modify the old second item. The current item’s own forward link survives long enough to save the remainder.
- **`self._prev._next = None`** — modify the old penultimate item. You can return a valid remainder from its *last* item.

### Middle: take the fifth of ten out, in both directions

Treat 1–10 as **positions before extraction**, not as required stored values. The fifth item has a predecessor and a successor. A student pictured the operation as combining two lists. That works if you picture the portions before and after 5: the program joins them by changing links on their existing boundary items. It does not concatenate copies. Watch which old links stay usable while the new ones are being installed.

| Step in the displayed method | State change with `self` at 5 | Why the next step still works |
|---|---|---|
| Enter the final `else` | 4 points forward to 5; 6 points back to 5. | 5 still points to both 4 and 6. |
| Set the predecessor’s forward link | 4 now points forward to 6. | Walking forward skips 5; walking backward from 6 still reaches 5. The repair is only half done. |
| Set the successor’s back link | 6 now points back to 4. | Both directions bypass 5, but 5 still knows its old neighbors. |
| Save `rest` | The local name refers to the existing item 4. | Nothing was copied. Clearing 5’s links will not clear this saved reference. |
| Clear both links on 5 | 5 is an isolated one-item chain. | Any caller reference to 5 still identifies that same object. |
| Return | The call yields item 4; no output is printed by `extract`. | Walk back from 4 to 3, 2, 1 or forward to 6, 7, 8, 9, 10. |

Subtle but important: the returned item is **not necessarily first**. If you want the entire remaining chain in order, start at **`rest.first()`**, not at **`rest`** and certainly not at the now-isolated item. The method only repairs nearby links; locating the fifth item in the first place is a separate operation.

**Key items to scan**

- **`self._prev._next = self._next`** — with 5 selected, install the forward route 4 → 6.
- **`self._next._prev = self._prev`** — install the return route 6 → 4. A forward-only test would miss a forgotten backward repair.
- **`rest = self._prev`** — remember 4 while 5 still points to it. Moving this assignment below the cleanup would lose that reference.
- **`return rest`** — gives the caller access to survivors. The selected item and its value are still accessible through the caller’s original reference.

**Independent execution check.** In a separate fixture, the guide ran this method on empty and single-apple representatives, on each end of a pair, and on old item 5 of ten. For 5, the returned payload was **`4`**; checking every survivor’s forward **and** backward link gave **`1, 2, 3, 4, 6, 7, 8, 9, 10`**. The detached item still contained **`5`**. The empty and single-apple calls each returned a distinct empty **`Item`**; the test checked instance identity, not just matching payloads. These are new checks of the displayed logic, not runs from the class terminal.

## 4 · Two names in the fruit driver: one for each result | 43:29–45:49; screen detail at 56:00

Returning to challenge 2070, the instructor explained why the grader could supply its own driver. The fruit file is a concrete example of that arrangement. It imports the class, builds a chain, and examines what remains after removing selected items. The full opening of this driver is legible later, at 56:00. It gives actual values to the earlier discussion of references.

**Annotated teaching version — guide-added comments**, fruit setup. The **`display`** function used here is shown below.

```python
from item import Item  # Bring the Item class into this driver.
```

```python
fruit = Item("apple")  # Start a new one-item chain; fruit refers to apple.
fruit.append("banana")  # Append banana; fruit still refers to apple.
fruit.append("peach")  # Append peach after banana; fruit still refers to apple.
fruit = fruit.last()  # Rebind fruit to peach. No fruit moved.
fruit.insertBefore("mango")  # Put mango just before peach, not at the beginning.
fruit.prepend("acai")  # Put acai before the first item, apple; fruit is still peach.
print("\nFruit:")  # Print a blank line and the Fruit: heading.
display(fruit)  # Walk from the first item, regardless of where fruit points.
```

### What the helper methods do to the chain

Compare the actions while **`fruit`** refers to peach. **`insertBefore("mango")`** works **at peach**; **`prepend("acai")`** first travels **all the way to apple**. Both add a value, but only one deliberately finds an endpoint. The source also implements the forward counterparts **`append`** and **`insertAfter`**. These are visible class methods, not four new live terminal runs.

**Annotated teaching version — guide-added comments**, representative endpoint search and end insertion:

```python
    def last(self):  # Find the last item, from wherever the caller is.
        iter = self  # Use a local reference; do not move the caller’s variable.
        while True:  # Keep walking until the explicit stopping test.
            if iter._next == None:  # At peach the raw next link is None.
                break  # Stop before moving past the end.
            iter = iter._next  # Otherwise advance one existing node.
        return iter  # Return the endpoint itself, not its fruit string.
```

```python
    def append(self, obj):  # Place obj at the far end, e.g., add peach.
        if self._object == None:  # Is the receiver the empty representative?
            self._object = obj  # Fill this instance instead of linking an extra empty node.
            newItem = self  # In this case the added item is self.
        else:  # For a nonempty list, make a separate node.
            last = self.last()  # When adding peach, the current last item is banana.

            newItem = Item(obj)  # Create peach, a new Item.
            newItem._prev = last  # Peach points back to banana.
            last._next = newItem  # Banana now points forward to peach.
        return newItem  # Return the filled or newly linked item.
```

**`first()`** follows the raw **`_prev`** links with the same stop-before-advancing rule. In **`prepend`**, the nonempty branch finds that first item, creates acai, sets acai’s forward link to apple, and sets apple’s backward link to acai. Calling these methods without assigning their return values still changes the chain. It does not change which instance the caller’s variable names.

**Annotated teaching version — guide-added comments**, interior insertion before peach, from the nonempty branch of **`insertBefore`**. Here the old predecessor is banana.

```python
            newItem = Item(obj)  # Create mango; self is still peach.
            if self._prev != None:  # Is there an old predecessor to reconnect?
                newItem._next = self  # Mango’s forward link points to peach.
                newItem._prev = self._prev  # Mango’s backward link points to banana.
                self._prev._next = newItem  # Banana now points forward to mango.
                self._prev = newItem  # Peach now points back to mango.
            else:  # If inserting before the first item, no old predecessor exists.
                newItem._next = self  # New first item points forward to the old first.
                self._prev = newItem  # Old first points back to the new first.
```

**`insertAfter`** reverses those roles: with a successor, save that successor in the new item, change the successor’s back link, then change the receiver’s forward link. Without a successor, only the new item’s backward link and the old endpoint’s forward link need writing. Both relative-insertion methods also have the empty-representative branch: store the first payload on **`self`** and return it. The numbered listing preserves all four implementations.

**Annotated teaching version — guide-added comments**, the other direction. These excerpts show what changes when the target is an interior successor, or when the operation is explicitly at the front. The setup and the empty-receiver rule are the same as above.

```python
    def insertAfter(self, obj):  # Insert immediately after this chosen item.
        if self._object == None:  # An empty receiver needs a payload, not a neighbor.
            self._object = obj  # Store the first value on self.
            newItem = self  # Return this same, now-filled item.
        else:  # Otherwise link a new node.
            newItem = Item(obj)  # Create it before reconnecting the old neighbors.
            if self._next != None:  # Does the selected item already have a successor?
                newItem._prev = self  # New item points back to the selected one.
                newItem._next = self._next  # Keep the old successor reachable through the new item.
                self._next._prev = newItem  # That old successor now points back to the new item.
                self._next = newItem  # Make the selected item point forward to the new item.
            else:  # No successor: the selected item was the tail.
                newItem._prev = self  # Connect the new tail backward.
                self._next = newItem  # Connect the old tail forward.
        return newItem  # Return the inserted item, not the old tail.
```

```python
    def prepend(self, obj):  # Add a value at the beginning, even when called on peach.
        if self._object == None:  # Does this representative still have no payload?
            self._object = obj  # Fill it with obj.
            newItem = self  # The added item is the existing receiver.
        else:  # Otherwise use the old first item.
            first = self.first()  # Follow backward links to the old first item, apple.
            newItem = Item(obj)  # Create a new item for acai.
            newItem._next = first  # Acai points forward to apple.
            first._prev = newItem  # Apple now points back to acai.
        return newItem  # Return acai; the driver ignores this return value.
```


**Key items to scan**

- **`fruit = fruit.last()`** — peach already exists. This changes a name, not the list’s order.
- **`append("peach")`** — banana and peach acquire reciprocal links; the new node is returned, even though this call ignores it.
- **`insertBefore("mango")`** — joins banana → mango → peach and repairs the reverse direction.
- **`prepend("acai")`** — finds apple even when called on peach; acai becomes first.
- **`insertAfter`** — works at a selected item instead of always finding the tail. At an interior position it must repair the old successor too.

### Display once per node, including the last one

**Annotated teaching version — guide-added comments**, the driver’s **`display`** function:

```python
def display(list):  # The parameter is an Item reference, not a built-in Python list.
    iter = list.first()  # Start at acai even if the caller passed peach.
    print("-----")  # Print the five-dash divider.
    while True:  # A break below, not the loop header, ends traversal.
        print(iter.object())  # Read and print the current payload, including the last.
        if iter.next() == iter:  # At peach, next() returns this very peach.
            break  # Stop here so peach does not print forever.
        iter = iter.next()  # Otherwise advance the local position.
```

With the five-fruit input, display prints acai, apple, banana, mango and peach on separate lines. It does **not** make a sixth pass. The test follows the print so peach appears once. The parameter **`list`** and the traversal variable **`iter`** are names chosen by the instructor; they shadow Python built-ins but do not turn these course objects into a Python list or a protocol iterator. As a consequence of this exact loop, calling it on the empty representative would print its **`None`** payload once; that is a prediction about the helper, not a claim about the cropped end of the classroom run.

**Annotated teaching version — guide-added comments**, beginning of the extraction driver:

```python
iter = fruit.first()  # Save the actual first item, acai.
fruit = iter.extract()  # Isolate acai; fruit now refers to the surviving apple.
print("\nIter:")  # Label the isolated group.
display(iter)  # Print only acai.
print("\nFruit:")  # Label the remaining group.
display(fruit)  # Print apple, banana, mango, peach.

iter = fruit.last()  # Now select peach, the last remaining item.
fruit = iter.extract()  # Isolate peach; fruit now refers to mango, not apple.
print("\nIter:")  # Start the next isolated-item group.
```

| Stage | Selected reference (`iter`) | Reference returned into `fruit` | Values displayed from each group |
|---|---|---|---|
| Before extraction | Not yet assigned at driver level | `fruit` already refers to peach | `Fruit:` acai, apple, banana, mango, peach |
| Extract first | Acai, now isolated | Apple, new first | `Iter:` acai; `Fruit:` apple, banana, mango, peach |
| Extract last | Peach, now isolated | Mango, new last | `Iter:` peach; `Fruit:` apple, banana, mango. Display starts at `first()`, not at mango. |

The group values above are visible across 55:00 and 55:28; returned references are traced from the displayed code. Later scrollback also shows isolated banana, survivors apple and mango, and then isolated apple. It does not show the complete ending or establish the exact driver statements for those later groups. The independent run of the recovered driver prefix (lines 1–30) reproduced the initial five-fruit group, isolated acai and its four survivors; the prefix stops immediately after printing the next **`Iter:`** heading. It does not claim to reproduce the rest of the historical driver.

**Key items to scan**

- **`display(fruit)`** — normalizes the starting position with **`first()`**; a reference to mango can still display earlier survivors.
- **`iter.next() == iter`** — endpoint test for this particular class. Testing the public return for **`None`** instead would not stop at peach.
- **`iter = fruit.first()`** — keep a name for the object about to be detached. After extraction it still names acai.
- **`fruit = iter.extract()`** — give the surviving chain a name as well. Without that assignment, the returned handle is discarded.

## 5 · A class file that can test itself | 44:44–50:35

What happens if you hand Python the class file rather than the fruit driver? In the first demonstration, the instructor reported that it ran without showing anything. At 50:49 the terminal history shows two such **`py .\src\item.py`** invocations with no intervening output. That is different from doing nothing. Python executes the module, builds the class and makes its methods available. Defining **`extract`** does not call it, and defining **`Item`** does not by itself create a fruit instance. Entering a **`.py`** pathname in PowerShell also need not mean “run this with Python”; the instructor explicitly used the Windows launcher **`py .\src\item.py`** for the test.

Adding two lines changed what was visible. **Annotated teaching version — guide-added comments**, at module level after the class:

```python
a = Item()       # Create an empty Item instance, not the class itself.
print(a)         # Ask Python to display that instance; this writes to stdout.
```

The observed direct-run scrollback includes **`<__main__.Item object at 0x0000017AD9C986E0>`**. This is the default representation of **one instance**, not a dump of the list or a fixed address to expect. The text before the dot is the module name under which the class was defined. An unguarded **`print(a)`** is useful for a quick test, but it also runs when the grader first imports the file.

The class wanted to keep the test while letting callers import quietly. **Annotated teaching version — guide-added comments**, completed editor state at 48:40:

```python
if __name__ == "__main__":    # Is this module the program’s entry point?
    a = Item()               # Direct run only: construct the sample instance.
    print(a)                 # Direct run only: display its representation.
```

The left operand **`__name__`** is a variable supplied by Python; putting it in quotes would compare the wrong thing. The right operand **`"__main__"`** is a string literal. When **`item.py`** is launched as the program, those values match. When **`fruit.py`** is launched, *fruit* is the entry point. Its **`from item import Item`** first loads the module under the name **`item`**, so the condition in **`item.py`** is false. The later direct launch still produced **`<__main__.Item object at 0x000002243E7386E0>`**, as the 50:49 history shows. An ordinary import can define several classes in one file; only the indented test is skipped here. This is a guard for the test, not a way to prevent importing the class or to stop all module code from executing.

**Key items to scan**

- **`py .\src\item.py`** — explicitly run the class file using the Windows Python launcher, from the classroom project directory.
- **`a = Item()`** — this statement constructs the sample; the **`class`** definition alone does not.
- **`print(a)`** — the source of the extra standard-output line. **`extract`** does not print it.
- **`__name__ == "__main__"`** — true for the directly executed module, false for **`item`** when imported by the driver.

## 6 · The failed import is a different problem | 50:35–55:31

A student asked whether the two files were in the same directory. They were not: **`src\item.py`** provided the class and **`test\fruit.py`** used it. The instructor preferred keeping tests apart from source. That layout is legitimate, but it does not automatically put the sibling **`src`** directory on the driver’s import search path. Until Python can locate **`item`**, the guard and extraction code are irrelevant: execution stops at line 1 of the driver.

| Observed PowerShell action, in order | Result and reasoning |
|---|---|
| `py .\test\fruit.py` | `from item import Item` fails with `ModuleNotFoundError: No module named 'item'`. The driver has not reached fruit construction. |
| `.\script\path.ps1`, then `py .\test\fruit.py` | The same error returns. Running a configuration helper does not prove it configured the right directory. |
| `cat .\script\path.ps1` | Read the helper rather than guessing. Its setting names `C:\CSCS1240F25\Item\src`; the prompt is under `C:\CSCS1240F26\Item`. The stale **year**, not the name `Item`, distinguishes the paths. |
| Set the corrected value in PowerShell, then `py .\test\fruit.py` | The driver now reaches `Fruit:` and prints the five fruit names. This establishes that this launch could import the module; it does not establish that the helper file was permanently edited. |

**Observed setting at the prompt** (55:00; PowerShell, not Python):

```powershell
$env:PYTHONPATH="$env:PYTHONPATH;C:\CSCS1240F26\Item\src"
py .\test\fruit.py
```

The quoted right side keeps the current **`PYTHONPATH`** value and appends a directory. The semicolon separates **search directories** on Windows; it is not the backslash that separates folders inside one path. Unix-like systems, including macOS and GNU/Linux, generally separate search-path entries with a colon. An environment is a collection of name/value settings available to a process; Python started from this PowerShell session can inherit the change. That does not alter the class, move the file, or configure every other terminal and IDE. The instructor noted that an IDE’s project settings may supply the path behind the scenes and deferred formal module organization. In particular, the directory of a launched script and the shell’s working directory are not interchangeable guarantees about where an import will search.

**Key items to scan**

- **`src\item.py`** — contains the class definition; the search path needs its *directory* for **`from item import Item`**.
- **`test\fruit.py`** — the directly launched consumer in this test. Its sibling is not automatically searched merely because both folders belong to the project.
- **`ModuleNotFoundError`** — here it names **`item`** at the import line. Do not debug list links for a run that never got that far.
- **`cat .\script\path.ps1`** — revealed **`F25`**, explaining why the helper did not fix an **`F26`** run.
- **`$env:PYTHONPATH`** — a PowerShell environment setting; its corrected **`src`** entry lets a subsequently launched Python process find the class.

## 7 · Change one thing and watch the first output line | 55:31–58:23

Once the path was correct, the first meaningful output from the guarded fruit run was **`Fruit:`**, not a class-and-address line. That absence has a cause: Python successfully loaded the class and skipped the test. It is not evidence that imports “don’t run files.” To distinguish those explanations, the instructor temporarily took the construction and print out of the conditional block, then ran the **same driver** again.

**Observed start of the unguarded driver run** (57:18; excerpt):

```text
<item.Item object at 0x0000021A514786E0>

Fruit:
-----
acai
apple
banana
mango
peach
```

The first line belongs to **`print(a)` in the imported `item` module**. The **`Fruit:`** label and names belong to the driver. The prefix **`item.Item`** rather than **`__main__.Item`** is a second clue to where that first instance was created. The numerical address is specific to the run. The instructor then restored the guard in stages. At 58:00 the header was back but its body was not yet indented; the closing frame shows the two test lines indented and the header hidden behind the terminal. Its scrollback still shows the earlier unguarded launch, not a fresh test of the restoration.

**Key items to scan**

- **`__main__.Item`** — the instance representation seen when the class’s module ran directly.
- **`item.Item`** — the module-qualified class seen in the extra line during the unguarded import experiment.
- **`Fruit:`** — the driver’s heading. Its position relative to the object line helps identify who printed what.
- **Indentation beneath the guard** — controls whether both test statements are conditional; commenting out a heading alone is not a complete working edit if the body remains indented.

## Syntax to remember

| Pattern from the lesson | Read it as |
|---|---|
| `rest = self._prev` | Keep another reference to the predecessor; do not clone a sublist. |
| `self._prev._next = self._next` | Look up the successor, then write that reference into the predecessor’s forward-link field. |
| `iter = iter.next()` | Move a local name to the neighboring instance. At the endpoint this class returns the same item. |
| `if __name__ == "__main__":` | Compare a module variable to a literal string; the indented suite is a direct-run test. |
| `from item import Item` | Find the module named `item`, execute its top-level code on first load in this interpreter, then make its `Item` class available here. |

## Check your understanding

1. An item has no previous or next neighbor. Which field must you inspect before saying the list is empty? What does **`extract()`** return if that field contains apple?
2. Before a front extraction, the second item’s **`_prev`** points to the current first item. Why doesn’t assigning **`None`** to that field also erase **`self._next`**?
3. In the fifth-of-ten example, a forward walk looks correct after only one write. Give a backward test that would still fail. Which item is returned after the whole method finishes?
4. In a fruit driver, what goes wrong if you detach the first fruit, ignore the return value and try to visit the remaining fruit by following links from the isolated one?
5. A direct run prints an **`Item`** representation, but a driver fails at **`from item import Item`**. Should you remove the print first? After import succeeds, how would you let a direct run print without making a successful import noisy?

### Answer key

1. Inspect **`_object`**. With **`"apple"`** stored, the receiver is a one-item list, not empty. Extraction leaves that same apple in the caller’s hands and returns a **new empty `Item`**, not **`None`** and not apple.
2. **`self._next`** belongs to the old first item; **`self._next._prev`** belongs to the old second item. Clearing the second item’s reference to the first does not change the first item’s reference to the second. The method can still save the second item before clearing the old first item’s links.
3. Ask where the old sixth item points **back**: until the second repair it still points to 5, not 4. After both repairs and cleanup, the caller still holds 5 and receives the old fourth item as a way into the remainder.
4. Its links now end in both directions. Unless you kept another reference into the chain, you cannot reach the survivors from that item. Save the returned reference, then call **`first()`** on it to start a complete forward display.
5. The import failure comes first: make the directory containing **`item.py`** discoverable. A guard cannot run in a module Python cannot find. Once it can load the file, put the sample construction **and** print inside **`if __name__ == "__main__":`**. The import still defines **`Item`**; only the direct-run test is skipped.

## Library of definitions

| Term | Meaning in this recording |
|---|---|
| Instance; class | An instance is one actual `Item` (such as the node holding apple). `Item` is the class that defines the operations its instances support. |
| Node; payload | A node is one linked item. Its payload is the stored value in `_object`, as distinct from the links used to reach neighbors. |
| Doubly linked; predecessor; successor | Each item can refer to one item before it and one after it. The predecessor of old 5 is 4; the successor is 6. |
| Reference; dereference | A reference lets code reach an object. Following `self._next` reaches a neighbor; a further `._prev` accesses an attribute of **that neighbor**. |
| Binding; mutation | `rest = self._prev` makes a name refer to an existing item. `self._prev._next = self._next` changes an existing item’s stored link. |
| Receiver; method | In `iter.extract()`, the receiver is the instance named `iter`; the method is `extract`. That instance is supplied as `self`. |
| Endpoint; invariant | An endpoint lacks a neighbor in one direction. An invariant is a relationship the implementation maintains: if 4’s successor is 6, 6’s predecessor should be 4 in the repaired chain. |
| Empty representative | A real `Item()` with no payload and no links. It is not the special Python object `None`. |
| Module; entry point | A Python file can be loaded as a module. The file started as the program gets the module name `__main__`; the ordinary import here uses `item`. |
| Driver; standard output | The driver creates inputs and calls the class. Standard output is the stream written by `print`; an importer can see unwanted test output even if every returned value is right. |
| Environment variable; search path | A named process setting and, here, an ordered set of directories to look in. `PYTHONPATH` contributes directories, not individual class names. |

## Appendix A · Numbered screen-code record

**Do not type the line numbers or `|` separators.** They reproduce the editor gutter for reference. These listings preserve the visible code without guide-added inline comments. This is an **assembled visual reconstruction**, not a repository export: blocks match overlapping views of **`src/item.py`** at the extraction revision, but the frames cannot establish what was saved between them. The module-level test below is a later edit, not part of the initial checkout. For the separate practice run only, the class and completed guarded test were assembled as **`src/item.py`** and the visible driver prefix as **`test/fruit.py`**.

### A1 · `src/item.py`: what one item knows

Lines 1–20 (39:00, also visible in the earlier revision at 02:00). The three class defaults make unassigned links read as **`None`**; initialization writes the instance’s payload. The accessors return either a value or an **`Item`**; endpoint navigation returns the receiver rather than a missing neighbor.

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
```

### A2 · Insert at the ends; walk to an endpoint

Lines 21–60 (39:00, 44:00, 45:00, with the next header at 61). Lines 22–43 distinguish filling an empty representative from attaching a new one. Lines 45–59 stop a local traversal on the endpoint itself; **`last`** uses raw forward links, **`first`** backward links. They return the endpoint without changing which node the caller holds.

```text
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
```

```text
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

### A3 · `insertAfter` and `insertBefore`: insert beside the receiver

Lines 61–92 (03:00–05:00; **`insertBefore`** also complete at 40:00). In a nonempty chain, lines 66–74 wire the new node after the selected one; lines 82–90 wire it before. Each checks whether there is an existing outer neighbor whose reciprocal link must be repaired. If the receiver is empty, both methods reuse it instead.

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
```

```text
 77 |     def insertBefore(self, obj):
 78 |         if self._object == None:
 79 |             self._object = obj
 80 |             newItem = self
 81 |         else:
 82 |             newItem = Item(obj)
 83 |             if self._prev != None:
 84 |                 newItem._next = self
 85 |                 newItem._prev = self._prev
 86 |                 self._prev._next = newItem
 87 |                 self._prev = newItem
 88 |             else:
 89 |                 newItem._next = self
 90 |                 self._prev = newItem
 91 |         return newItem
 92 |
```

### A4 · `extract` and the completed direct-run test

Lines 93–111 (e.g. 13:00 and 48:40). Lines 95–108 choose an empty representative, successor or predecessor to return. The last three lines do not delete the selected instance: they isolate it and return the stored remainder. Lines 112–115 below are from the later **48:40** frame; the guard’s suite is indented in this state.

```text
 93 |     def extract(self):
 94 |         rest = None
 95 |         if self._prev == None and self._next == None:
 96 |             rest = Item( )
 97 |         else:
 98 |             if self._prev == None:
 99 |                 self._next._prev = None
100 |                 rest = self._next
101 |             else:
102 |                 if self._next == None:
103 |                     self._prev._next = None
104 |                     rest = self._prev
105 |                 else:
106 |                     self._prev._next = self._next
107 |                     self._next._prev = self._prev
108 |                     rest = self._prev
109 |         self._prev = None
110 |         self._next = None
111 |         return rest
112 |
113 | if __name__ == "__main__":
114 |     a = Item()
115 |     print(a)
```

**The changing test, not four successful runs.** These are distinct editor observations, not alternative working programs. At **47:00** line 113 contained the unsaved draft **`print()`**; the instructor then chose to construct and print an instance, not just print a blank line. At **48:00** the conditional header was still being typed; both following lines were at the left margin. At **57:00** the header was commented and the selection was mid-indent. The completed unguarded execution is evidenced by the 57:18 **terminal**, not by treating the 57:00 buffer as valid. At **58:00** the uncommented header is visible again but its suite is not yet indented. The 59:12 closing view exposes indented lines 114–115, not the hidden header or a new execution.

**48:00 · draft**

```text
113 | if __name__ =
114 | a = Item()
115 | print(a)
```

**57:00 · draft**

```text
113 | #if __name__ == "__main__":
114 |         a = Item()
115 | print(a)
```

**58:00 · restoration in progress**

```text
113 | if __name__ == "__main__":
114 | a = Item()
115 | print(a)
```

### A5 · `test/fruit.py`: the recoverable driver

Lines 1–30 are fully readable at 56:00; the traceback at 50:49 independently shows the import at line 1. Lines 3–10 display a whole chain from whichever item the caller has. Lines 12–19 build five fruits; 21–26 compare isolated acai with the survivors; 28–30 select and extract peach and begin its output group. Later complete source lines are not established here merely because some later output is visible.

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
 12 | fruit = Item("apple")
 13 | fruit.append("banana")
 14 | fruit.append("peach")
 15 | fruit = fruit.last()
 16 | fruit.insertBefore("mango")
 17 | fruit.prepend("acai")
 18 | print("\nFruit:")
 19 | display(fruit)
 20 |
 21 | iter = fruit.first()
 22 | fruit = iter.extract()
 23 | print("\nIter:")
 24 | display(iter)
 25 | print("\nFruit:")
 26 | display(fruit)
 27 |
 28 | iter = fruit.last()
 29 | fruit = iter.extract()
 30 | print("\nIter:")
```

**Use it as a practice pair.** In a fresh folder, save the gutter-free class and its completed 48:40 guard under **`src/item.py`**, and lines 1–30 above under **`test/fruit.py`**. Make the **`src`** directory discoverable before launching the driver. On Windows PowerShell, from that practice folder, the following uses *your* **`src`** path rather than the instructor’s:

```powershell
$env:PYTHONPATH = (Resolve-Path .\src).Path
py .\test\fruit.py
```

This practice setting replaces **`PYTHONPATH`** for the current shell; use a fresh practice terminal. Independent check: the exact recovered Python prefix ran in a fresh interpreter with **`src`** on the search path. It ended at the second **`Iter:`** heading, as it must with no later display call supplied. The historical classroom driver ran farther.


### A6 · What the terminal establishes

At 50:49, four explicit launches of **`py .\src\item.py`** are visible in order. Two returned to the prompt without output; the next two printed an instance each. In the first case the class alone had been loaded. The discussion and intervening editor frames connect the first print to the added test and the second to the guarded direct launch. The command history itself does not identify the saved version.

```text
<__main__.Item object at 0x0000017AD9C986E0>
<__main__.Item object at 0x000002243E7386E0>
```

These are separate launches, not two outputs from one invocation. The subsequent *driver* launch failed before either the guard or the fruit code could print. This traceback appears at 50:49 and again after the unsuccessful helper at 51:00:

```text
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 1, in <module>
    from item import Item
ModuleNotFoundError: No module named 'item'
```

**Three checkpoints for reading the later terminal:** At 55:00 the corrected search path and guarded launch are together: **`Fruit:`** comes first. At 55:28 later scrollback reaches isolated apple; the frame does not show the ending. At 57:18 a new driver launch begins with **`item.Item`** before **`Fruit:`**. This last result was reproduced independently by changing only the test guard in a practice copy: the fruit prefix was unchanged and the import added exactly one representation line.

## Appendix B · Micro-Learning Insights

- **Draw ownership, not just arrows.** For “the second item points back to the first,” put **`_prev`** inside the second box. Erasing that arrow does not erase the first box or its forward link. This resolves the class question at 25:33–29:16.
- **Read the branch you actually reached.** Inside the outer **`else`**, knowing **`_prev`** is **`None`** is enough to dereference **`_next`**: the both-missing case has already been excluded. Outside that context the same dereference need not be safe.
- **Distinguish no neighbors from no value.** An isolated apple and the empty representative share the link pattern **`(None, None)`**. **`_object`** answers the other question. Two empty representatives can also be distinct instances.
- **Test in reverse.** In the fifth-of-ten example, 4 → 6 can be correct while 6 still points back to 5. Verify both directions before declaring the repair complete.
- **Keep two names when you want two results.** After **`fruit = iter.extract()`**, inspect **`iter`** for the detached fruit and **`fruit.first()`** for the remaining chain. Rebinding **`fruit`** does not turn **`iter`** into a different object.
- **Ask what the public method promises.** At peach, **`_next`** is **`None`** but **`next()`** returns peach. A loop that waits for the public method to return **`None`** misses this class’s stop signal.
- **Read the failing line before editing the algorithm.** The traceback stopped at **`from item import Item`**. Reading **`path.ps1`** exposed **`F25`** instead of **`F26`**; no change to the linked-list method could fix that failure.
- **Use output order as a witness.** **`item.Item`** **before** **`Fruit:`** identifies an import-time side effect. Guard the test and repeat the same driver, rather than comparing different programs with different search paths.
- **Practice one changed link at a time.** At 29:50 the instructor encouraged retracing the example after a break. Cover the final graph, predict the next assignment, then check both neighbors; that gives “try it, break it, fix it” a concrete test.
- **Treat a checkout, a buffer and a run as different evidence.** A visible guard with an unindented body is a work in progress; old terminal output beside it does not show that draft running. The same discipline is useful when debugging your own code.
