# Session 23 · Taking a linked list apart and walking it again

**CSCS1240F26 · Worked teaching companion · Unified v2**

**If `fruit` refers to peach, how can `for x in fruit` start with `acai`?** The name `fruit` holds a reference to one `Item`, not a container wrapped around all the fruit. From peach, the links take you to either end. That distinction drives the whole class: first separate one node without losing the others, then let another object work on each value, and finally let Python drive the walk itself.

### Route through the recording

| Jump to | Question to follow |
|---|---|
| [0:02:00 · The fruit driver](https://www.youtube.com/watch?v=jE-Y3-6ds7I&t=120s) | Which object does `fruit` actually name? What does a quoted label print? |
| [0:22:00 · Extract, don't delete](https://www.youtube.com/watch?v=jE-Y3-6ds7I&t=1320s) | Which links change, and who keeps the two results? |
| [0:43:00 · Tail, middle, singleton](https://www.youtube.com/watch?v=jE-Y3-6ds7I&t=2580s) | Why does the method sometimes return a predecessor and sometimes a new object? |
| [1:07:00 · The `None` experiment](https://www.youtube.com/watch?v=jE-Y3-6ds7I&t=4020s) | Why does a real empty `Item` make repeated extraction possible? |
| [1:14:00 · Where did that print come from?](https://www.youtube.com/watch?v=jE-Y3-6ds7I&t=4440s) | Which file is executing, which is imported, and which version ran? |
| [1:23:00 · A callback](https://www.youtube.com/watch?v=jE-Y3-6ds7I&t=4980s) | Who controls the walk, and who decides what to do with a fruit? |
| [1:33:00 · A Python iterator](https://www.youtube.com/watch?v=jE-Y3-6ds7I&t=5580s) | What does a `for` loop request, and how does it know to stop? |

**Sources and limits.** [JoesTA’s CSCS1240F26 23 recording](https://www.youtube.com/watch?v=jE-Y3-6ds7I&t=2s); times below are elapsed from the start, not from the link’s two-second playback position. The complete saved English auto-caption track contains 2,712 cues. Text and start times match the readable transcript from the greeting at `0:00:00.800` to the final cue at `2:07:32.560`. Caption intervals without text are not evidence of silence. The closing cue’s nominal end (`2:07:36.320`) extends slightly beyond the previously probed movie duration (`2:07:35.217`). The attached excerpt is not the basis for end-of-class coverage.

The saved visual evidence comprises 128 whole-minute targets, 15 additional event captures and one closing capture. All 144 originals passed decoding, size and archived hash checks. Times identify requested capture targets, not exact decoded frame timestamps. We have stills, not a record of every keystroke; the original movie was not located for a fresh seek check. An editor and an overlapping terminal can show **different runs**. Classroom output, speech, source-based traces and independent practice runs are identified separately below. Code comments in teaching versions are guide-added; numbered historical listings preserve what the screen supports.

**Opening note (0:00:00–0:01:20).** The instructor warned that a federal grand-jury summons might affect class meetings and promised updates. No cancellation or replacement schedule was announced.

## One name, five linked items (0:01:38–0:03:42)

The earlier class had designed several ways to insert an item and then a way to extract one. This time the question was what happens to the *two sides of the operation*: the item you take out and the chain you leave behind. Although the class calls it a list, there is no separate container in this example. **`fruit`** names one **`Item`**. Each item holds its payload and links to its neighbors. A handle to peach can therefore be used to display the whole chain, or to find and extract a different item. The class previously decided to allow an empty list; this example starts with a real apple, not an empty item.

The helper below is from `fruit.py:3–10` (0:04:00). All right-side comments in these worked examples are guide-added. To run the example without the course checkout, use the practice setup in the practice section before the syntax reference; it reproduces the needed interface. The historical listing separately preserves the narrower original views.

```python
def display(list):  # Accept a handle to ANY item, not necessarily the head.
    iter = list.first()  # Start at the head, even when the caller supplies peach.
    print("-----")  # Print five hyphens once for this display.
    while True:  # There is at least an Item to inspect (possibly an empty one).
        print(iter.object())  # Print the current payload before deciding to stop.
        if iter.next() == iter:  # At the end, next() returns this very item.
            break  # The final payload has already been printed.
        iter = iter.next()  # Otherwise move the local cursor; do not change the caller.
```

Why start by moving backward? The caller need not hold the head. Once **`first()`** finds it, the **`display`** helper can make one forward pass. The public **`next()`** in this class returns the item itself when the raw next link is missing. If the look-ahead equals the cursor, **`break`** ends the loop *after* printing the tail. An isolated item therefore prints once, not zero times. The local parameter **`list`** and cursor **`iter`** are names chosen here; this is not Python's built-in list or iterator protocol.

**Key items to scan**

- **`fruit`**, **`Item`** — a name bound to one linked object, not a container variable that automatically follows the most recent insertion.
- **`display`**, **`first()`** — start at the head regardless of which item the caller supplies.
- **`list`**, **`iter`** — receiver supplied to the helper and its independent local cursor.
- **`next()`**, **`break`** — look ahead; a step that returns the same node means the last payload has already printed.

The split editor also shows how those accessors work (0:16:00 and 0:19:00). The visible class body starts with **`_object`**, **`_next`** and **`_prev`** set to `None`; the initializer assigns the supplied payload to `self._object`. Reading `object()` returns that payload. Reading `next()` or `prev()` returns the neighbor if present, otherwise the receiver. In contrast, the traversal helpers shown at 0:03:00/0:20:00 use a local cursor initialized to the receiver, follow raw `_next` or `_prev` fields in a loop, and return the cursor at the boundary. Do not repeatedly call the public `next()` until it returns `None`: it will not do so at the tail.

The insertion fragments distinguish *filling* an empty receiver from *allocating* another node. In the first arm, the payload changes and **`newItem = self`** keeps the same object as the result. In the nonempty arm the class creates another item and adjusts links. For example, the start-of-chain branch of `insertBefore` ends with `newItem._next = self` followed by `self._prev = newItem`; the new node leads into the old head, and the old head points back. The symmetric **`insertAfter`** helper is visible but not called by this driver. The narrower source views do not expose every right-hand side; the practice adapter later in the guide is a way to try the behavior, not a verbatim replacement.

**Key items to scan**

- **`_object`**, **`_next`**, **`_prev`** — payload and two raw neighbor links; a missing raw neighbor is `None`.
- **`newItem = self`** — reuse the empty receiver rather than allocating a different node.
- **`insertAfter`** — companion operation for an insertion on the other side of a receiver; not one of the fruit calls.

Now follow the setup, first as it appeared with the plain heading at 0:04:00. **`append`**, **`insertBefore`** and **`prepend`** change the chain. Only the line that explicitly assigns the result of **`last()`** changes which node the name refers to.

```python
fruit = Item("apple")  # Create an apple Item; fruit refers to it.
fruit.append("banana")  # Link a new banana at the end; fruit still refers to apple.
fruit.append("peach")  # Link a new peach after banana; still apple.
fruit = fruit.last()  # Walk from apple; now bind fruit to the peach Item.
fruit.insertBefore("mango")  # Link mango immediately before peach; fruit stays peach.
fruit.prepend("acai")  # Link acai before the head; fruit still stays peach.
print("\nFruit:")  # Blank line, then the literal title Fruit:.
display(fruit)  # Go to the first Item and print all five payloads.
```

**Independent run of this code with the practice setup** (includes a blank line before the heading):

```text

Fruit:
-----
acai
apple
banana
mango
peach
```

| After driver line | Chain from first to last | Item named `fruit` |
|---|---|---|
| 12 | apple | apple — new item |
| 13 | apple → banana | apple — append return unused |
| 14 | apple → banana → peach | apple — still no assignment |
| 15 | apple → banana → peach | peach — result of the forward walk |
| 16 | apple → banana → mango → peach | peach — mango linked before the receiver |
| 17 | acai → apple → banana → mango → peach | peach — acai linked before the head |

On the initial display the cursor visits acai → apple → banana → mango → peach. At each of the first four nodes the next item differs, so the loop advances; at peach, `next()` returns peach and the loop stops. The helper's traversal didn't reassign the caller's variable.

**Key items to scan**

- **`append`** — links banana, then peach, at the end without rebinding the caller's name.
- **`last()`** — returns the end item; assignment makes the caller's name refer to it.
- **`insertBefore`** — inserts relative to *this* item, peach; **`prepend`** — inserts relative to the *head* of the chain, apple.

## Why one printed backslash takes two in the source (0:03:49–0:13:32)

A student asked whether the backslash in **`print("\nFruit:")`** removes a space or a return. Read the call from outside in: it has one argument, the entire quoted string. In this literal **`\n`** denotes one newline; it does not print an `n` or remove anything. With `print`'s own line ending, the result is a blank line before the heading and a line ending after it. That suggested a useful test: what if the heading itself should read `Fruit\Veg:` on one line?

Here are the changes the class tried to line 18. **Each row is a separate replacement**, not a sequence of prints in the classroom. The left column is from the editor; the right column describes independent Python 3.14.9 checks. Every successfully printed heading below has the initial blank line; it is omitted from the description only.

| Frame | Source line (original punctuation) | Independently checked effect |
|---|---|---|
| 0:07:00 | `print("\nFruit\Veg:")` | Uppercase `\V` is unrecognized: this interpreter warns and retains the slash. The editor had highlighted it; no classroom traceback is shown. Do not rely on it. |
| 0:08:00 | `print("\nFruit\nVeg:")` | `Fruit` on one line, `Veg:` on the next. Correct newline, wrong layout. |
| 0:08:27 / 0:09:00 | `print("\nFruit"\"Veg:")` | The first inner `"` ends the first string. Backslash is now outside it; this yields `SyntaxError: unexpected character after line continuation character`. Merely adding quotation marks did not join the strings. |
| 0:09:23 | `print("\nFruit'\'Veg:")` | The first apostrophe is data inside the outer double quotes. The slash escapes the second apostrophe. Prints `Fruit''Veg:` with **two apostrophes, no backslash**. |
| 0:10:00 | `print("\nFruit\\Veg:")` | `Fruit\Veg:`. The first backslash introduces the escape, the second specifies a literal backslash. |

A student then asked whether **`Veg`** needed to be defined, and expected the fruit variable to supply peach. Nothing in the ordinary quoted label asks Python to look up either word. The word **`Fruit`** is spelled with a capital F, unlike the variable **`fruit`**. In a formatted string the **`f`** prefix enables interpolation, but only a field such as **`{fruit}`** requests a lookup. Watch what happens when *only* the prefix is removed.

```python
fruit = "peach"  # Practice fixture: a string, NOT the classroom Item.
print(f"\nFruit\\Veg:")  # 0:13:00: plain words even with f.
print(f"\n{fruit}\\Veg:")  # 0:13:14: substitute the lowercase variable.
print("\n{fruit}\\Veg:")  # 0:13:27: no f, so even the braces print.
```

**Independent output with that string fixture:**

```text

Fruit\Veg:

peach\Veg:

{fruit}\Veg:
```

In the actual driver the variable names a node, not the string `"peach"`: substituting the node would format the object, not automatically call its payload accessor. No classroom output of that f-string is shown. By 0:14:00 line 18 is back to `print("\nFruit\\Veg:")`; subsequent `Iter:` and `Fruit:` labels are separate ordinary strings and were not renamed.

**Key items to scan**

- **`print("\nFruit:")`**, **`\n`** — one string argument; create a newline before the title.
- **`\\`** — source spelling for one printed backslash. Quotes of the same kind instead close the string unless escaped.
- **`Fruit`**, **`Veg`** — quoted words in the headings; no corresponding variable is required.
- **`f`**, **`{fruit}`**, **`fruit`** — formatted-string prefix, replacement field, and case-sensitive name to look up. Without the prefix the braces are text.

## Predicting the receiver before predicting the return (0:13:41–0:21:49)

The guesses included banana, peach and mango. Each guess was premature: before asking what extraction returns, identify the object **receiving** the call. On the right side of `fruit = fruit.last()`, `fruit` still names **apple**. Walk forward from there and **then** rebind `fruit` to peach. The next two method calls return things, but there is no assignment on either line. At `iter = fruit.first()`, the receiver is therefore peach, even though acai was added most recently. The class caught the instructor calling apple the first item: after the prepend, the reverse walk is peach → mango → banana → apple → **acai**.

| Expression, in evaluation order | Specific result |
|---|---|
| Look up `fruit` in `fruit.first()` | The peach node, not its payload and not a list container |
| Call `first()` there | Repeatedly follow `_prev` until the item with no predecessor: acai |
| Bind `iter` to that result | `iter` → acai; `fruit` → peach, unchanged |
| Evaluate `iter.extract()` on the next right-hand side | Receive acai as `self` inside the method; change links there |
| Only when the call returns, assign its result to `fruit` | `fruit` → apple, the returned remainder handle; `iter` → the still-existing acai |

The instructor used ID, address, pointer and reference to distinguish *which item* from *what it contains*. You don't need a numeric address. Ask two questions instead: “Which node does this name reach?” and “What does that node's `object()` return?”

**Key items to scan**

- **`fruit.last()`** — evaluated on apple in the setup; returns the node holding peach.
- **`fruit.first()`** — evaluated on peach after the prepend; returns the node holding acai.
- **`iter.extract()`** — evaluated on acai; the receiving object is not the returned remainder.

## Extracting without deleting (0:21:50–0:31:59)

Why call it **`extract`**, not “delete”? No payload has to move elsewhere in memory. The operation disconnects the receiver from the chain, while **`rest`** identifies a way into the remaining chain. If there is no other node, the course implementation allocates a new empty item instead of returning a missing handle. Compare the stable method at 0:23:00 and 0:27:00 (`item.py:93–111`). Its four branches depend on the *raw* links; unlike `next()` and `prev()`, the fields really can contain `None`.

```python
    def extract(self):  # The receiver in iter.extract() becomes this local self.
        rest = None  # Local name to hold a reference into the remaining list.
        if self._prev == None and self._next == None:  # Is the receiver isolated on BOTH sides?
            rest = Item( )  # Allocate a NEW empty Item; do not empty the old node.
        else:  # At least one neighbor exists.
            if self._prev == None:  # At the head of a multi-node list?
                self._next._prev = None  # Apple (next) loses its old backlink to acai.
                rest = self._next  # Keep apple available to return as the new head.
            else:  # There is a predecessor.
                if self._next == None:  # No successor: the receiver is the tail.
                    self._prev._next = None  # The predecessor receives the end marker.
                    rest = self._prev  # Return a handle to that predecessor.
                else:  # Both neighbors exist: a middle item.
                    self._prev._next = self._next  # The left neighbor now points ahead past self.
                    self._next._prev = self._prev  # The right neighbor now points back past self.
                    rest = self._prev  # Use the predecessor as the remaining-list handle.
        self._prev = None  # Detach the receiver on its previous side.
        self._next = None  # Detach it on its next side; keep its payload.
        return rest  # Hand the caller a reference into the remainder.
```

| Receiver before extraction | What changes in the remaining chain | Returned handle |
|---|---|---|
| No predecessor and no successor | Allocate a different empty `Item( )`; the original node keeps its payload | New empty node (not Python `None`) |
| Head with a successor (acai) | Successor apple loses its predecessor | Apple |
| Tail with a predecessor (peach after acai is gone) | Predecessor mango loses its successor | Mango |
| Interior (banana after both ends above have been removed) | Apple points forward to mango; mango points backward to apple | Apple, the predecessor |

In every case, the final two field assignments clear the receiver's own links. The return is **not** always the head: after a tail extraction it can be the new *tail*. That's why the display helper always asks for the first item before printing. The four-branch table describes the visible implementation; only the acai/head branch is being stepped through at this point in class.

**Key items to scan**

- **`extract`**, **`rest`** — detach this receiver; return something that reaches the remainder.
- **`Item( )`** — allocate a *different* empty node when the receiver alone made up the chain.
- **`_prev`**, **`_next`** — raw fields; their `None` values identify which boundary or interior case applies.

### Can assigning `None` to `self` remove the caller's item?

Inside the call, **`self`** is a local parameter referring to the same acai object as caller variable **`iter`**. At 0:25:00–0:26:00, the instructor temporarily typed **`self = None`** after initializing `rest`. Python *allows* this local assignment. It does not rebind `iter`, `fruit`, or any other caller name. It does make the next attempted attribute lookup fail. The instructor removed the statement before stepping through the real branch; the error below was not shown in the classroom terminal.

Independent demonstration of the exact failing access, without breaking the working class:

```python
def probe(self):
    rest = None                # Normal local initialization.
    self = None                # Rebind JUST this local name.
    return self._prev          # Same first attribute read as the class test.

node = Item("acai")            # A fresh practice item, not the five-node chain.
try:
    probe(node)                # Caller still has node.
except AttributeError as e:
    print(type(e).__name__ + ":", e)  # Report the failed attribute lookup.
print(node.object())           # Still prints acai.
```

**Independent output:**

```text
AttributeError: 'NoneType' object has no attribute '_prev'
acai
```

The parameter and `rest` are local names. Returning from a function ends those bindings; it does not imply that every object they named vanishes. A caller binding, a returned reference or links from other objects may still keep an item reachable. Here the caller already has the extracted item's handle before the call.

**Key items to scan**

- **`self`**, **`iter`** — different bindings can refer to the same object.
- **`self = None`** — local rebinding; does not delete the acai item or overwrite a caller name.
- **`AttributeError`** — a consequence of asking `None` for `_prev`; the assignment itself succeeded.

### Which item loses its backlink?

For the first call, the outer test uses **`and`**. Acai has no predecessor (true) but *does* have apple as successor (second test false). Therefore take the outer `else`, not the singleton branch. Inside it, the predecessor test alone is true. At line 99, compare the *value* of **`self._next._prev`** with the *place* where a value will be written. Before the change, that value refers to acai, just like `self`. But the place being changed is a field of **apple**, not the local name `self` and not a field of acai.

| Step on the head branch | Acai's raw links (`prev`, `next`) | Apple's `prev` | Effect |
|---|---|---|---|
| Enter with `self` → acai | `None`, apple | acai | Both caller `iter` and local `self` can reach acai |
| `self._next._prev = None` | `None`, apple | `None` | Apple becomes first. Acai's forward link still exists *at this step*. |
| `rest = self._next` | `None`, apple | `None` | Save the apple handle before clearing acai's forward link |
| `self._prev = None`; `self._next = None` | `None`, `None` | `None` | Acai is isolated, with payload unchanged |
| `return rest`; assign to `fruit` | `None`, `None` | `None` | Caller `fruit` now enters remainder at apple; caller `iter` still reaches acai |

In that first assignment, evaluate the right-hand `None`; on the target side follow the first dot from acai to apple, then set **apple's** `_prev`. Asked at 0:31:10 whether acai has been lost, don't answer “we printed it.” Right here inside the method **`self`** still refers to it. Externally the caller's **`iter`** still does too. A print is not what preserves the reference.

The driver checks both handles, not just the survivor:

```python
iter = fruit.first()  # Walk BACK from peach; iter now refers to acai.
fruit = iter.extract()  # Detach acai; fruit receives apple, not acai.
print("\nIter:")  # Literal heading for the original receiver.
display(iter)  # One isolated item prints acai.
print("\nFruit:")  # Literal heading for the returned remainder.
display(fruit)  # From apple print apple, banana, mango, peach.
```

**Independent output immediately after the setup shown above:**

```text

Iter:
-----
acai

Fruit:
-----
apple
banana
mango
peach
```

**Key items to scan**

- **`and`** — a true left operand alone does not select the singleton case.
- **`self._next._prev`** — resolve the successor (apple); the field to write belongs to that object.
- **`self`**, **`iter`** — still reach the extracted node even once the survivor no longer links back to it.
- **`rest = self._next`** — save the survivor before removing the receiver's route to it.

### What does the display actually show? (0:32:00–0:40:51)

Why save **`rest`** before the cleanup? If the method first cleared acai’s forward link and then read that same link, it would get `None`, not apple. On return, `iter` still names only acai; `fruit` names only apple, not “the array of remaining fruits.” Starting at that one apple node, however, the program can still reach apple, banana, mango, and peach. That distinction resolves the class’s two answers to “what is the value of fruit?” (0:37:39–0:39:42).

A second question was whether line 23 prints a word or an object. The literal **`print("\nIter:")`** emits a blank line and the heading `Iter:`; the colon and uppercase `I` are part of the string. Line 24 is **`display(iter)`**, not `print(iter)`. The helper calls **`print(iter.object())`** on each visited node. The discussion about printing the raw object’s class/address (0:40:08–0:40:51) describes a *different operation*; it is not this helper’s output. The helper prints `-----` and then `acai` for this isolated node. No raw address is visible in this segment’s PowerShell tail.

**Key items to scan**

- **`acai`**, **`iter = fruit.first()`**: choose and retain the head before extraction; the spelling in the source is `acai`.
- **`fruit = iter.extract()`**, **`iter`**, **`fruit`**, **`rest`**: the receiver remains available through `iter`; `fruit` is rebound to the returned neighbor, saved locally as `rest`.
- **`print("\nIter:")`**, **`display(iter)`**, **`print(iter.object())`**: print the heading, traverse, then print each payload. These are three different operations.

### Taking out the last node: follow the receiver, not its name (0:41:06–0:47:36)

Does line 28 mean apple or peach? **`fruit.last()`** starts *at* apple. Inside `last`, a local cursor starts at the receiver and follows forward links until its current node has no successor; it returns that node. So the cursor visits apple → banana → mango → peach and returns peach. The original mango insertion was *before* peach; peach is still the tail. Line 28 assigns the result to **`iter`**, not to `fruit`. This matters because a spoken shorthand around 0:42:45 calls `fruit` peach. The editor makes the distinction unambiguous: apple is the starting receiver of `last`; peach becomes the receiver of `extract`.

```python
iter = fruit.last()      # Starts at apple; only iter becomes peach.
fruit = iter.extract()   # Evaluates the call on peach first; its returned neighbor is mango.
```

Why does the method take the tail branch? At line 95 the test is **`self._prev == None and self._next == None`**. Peach has a predecessor, mango, so the first comparison is false. With **`and`**, that settles the conjunction: the second comparison need not run. Peach’s forward link *is* `None`, but Python need not read it *here*. The later, distinct `if self._next == None:` at line 102 really does check it. The class called this **short-circuit evaluation** and contrasted `or`: a true first operand can settle an `or`, not an `and`. A student suggested ordering conditions to get a decisive answer early; the instructor agreed that it can help efficiency but said not to optimize first (0:44:20–0:44:51).

```python
self._prev._next = None  # Reach mango via peach’s backlink; mango now has no successor.
rest = self._prev        # Peach still points to mango, so save mango as the remainder handle.
self._prev = None        # Disconnect peach from mango in the reverse direction too.
self._next = None        # Peach already had no successor.
return rest              # Mango is a tail, not a head, but the earlier nodes are reachable.
```

The student’s question “mango or banana?” has two checks: which node directly precedes peach, and what does `display` do with it? The immediate returned node is mango, not banana. **`display(fruit)`** starts from `fruit.first()` rather than printing only mango. The trace therefore gives `Iter:` → peach and `Fruit:` → apple, banana, mango, each fruit on its own line after `-----`. The terminal foreground did not show a fresh run for this question; these rows follow from the editor and were separately checked.

**Key items to scan**

- **`fruit.last()`**, **`iter`**: walking from apple returns peach; assigning the return to `iter` does not rebind `fruit`.
- **`self._prev == None and self._next == None`**, **`and`**, **short-circuit evaluation**: a false first comparison skips the second comparison *in this expression*, not every later test of `_next`.
- **`display(fruit)`**: the handle is now a tail; the helper finds the head before printing through to that tail.

**Reading the neighboring insertion code (screen-only, 0:42:00–0:43:00).** Extraction takes a node *out*; the adjacent **`insertAfter`** method shows the reverse bookkeeping for putting one *in*. This is not the driver’s mango call—that call is `insertBefore`. On an empty receiver the visible branch sets `self._object = obj` and `newItem = self`: it fills the existing node. On an occupied receiver that already has a successor, the method instead constructs a new node. The four writes below are what it takes to put that new node between two existing ones:

```python
newItem = Item(obj)             # A different node will carry the new payload (line 66).
if self._next != None:          # There is already a neighbor on the far side.
    newItem._prev = self        # New node points back to the receiver.
    newItem._next = self._next  # Save the OLD successor on the new node before overwriting self._next.
    self._next._prev = newItem  # Old successor now points back to the inserted node.
    self._next = newItem        # Receiver now points forward to the inserted node.
```

Starting with `left ↔ right`, this branch produces `left ↔ new ↔ right` (a teaching diagram, not console output). Overwriting `left._next` before saving the old successor would lose the path these assignments use to reach `right`. The endpoint `else:` is visible at line 72 but its body is below this pane; no execution of this method is implied by the frame.

**Key items to scan**

- **`insertAfter`**: fill an existing empty receiver, or allocate and link a node if occupied; distinguish it from the `insertBefore` used in the actual fruit setup.
- **`newItem._next = self._next`**: keep the old successor available until both directions have been repaired.

### Taking out a middle node: repair both directions (0:47:39–0:54:45)

To find the starting value of `fruit`, the instructor asks where it was *last assigned*. It was assigned mango by the tail extraction. On driver line 35, **`fruit.first().next( )`** first walks back from mango to apple; *then* apple’s `next()` returns banana. `iter` therefore names banana. Apple is at the head, but that means **apple's** `_prev` is `None`, not that **banana's** `_prev` is `None`. Likewise, the next node after banana is mango, not the peach already taken out. Neither endpoint branch applies.

**Middle-node path (source-based trace)** (`item.py` 106–111; read each right-hand side before the assignment target):

```python
self._prev._next = self._next  # Read banana’s next (mango); write apple’s forward link.
self._next._prev = self._prev  # Read banana’s prev (apple); write mango’s backward link.
rest = self._prev              # Banana’s own backlink is still apple. Save apple, not banana.
self._prev = None              # Cut banana’s remaining link back to apple.
self._next = None              # Cut banana’s remaining link forward to mango.
return rest                    # Driver line 36 rebinds fruit to apple.
```

| Checkpoint | What a forward walk from apple sees | What a backward walk from mango sees | Banana’s own links |
|---|---|---|---|
| Before line 106 | apple → banana → mango | mango → banana → apple | apple / mango |
| After line 106 only | apple → mango | mango → banana → apple | still apple / mango |
| After line 107 | apple → mango | mango → apple | still apple / mango |
| After lines 109–110 | apple → mango | mango → apple | `None` / `None` |

The first write alone would make a forward display look right while leaving the backward walk wrong. The two repair writes bypass banana *from its neighbors*. The cleanup writes then isolate banana *from its own side*. `iter` still names banana; `fruit` names apple in the two-node apple ↔ mango remainder. Neither variable refers to both nodes at once. In the earlier terminal tail, the `Fruit:` block with two payload lines `apple` and `mango` is visible (0:57:00); that matches this state but does not establish a new invocation at 0:57:00.

**Key items to scan**

- **`fruit.first().next( )`**: finish the backward traversal before taking one forward step; the selected middle node is banana.
- **`self._prev._next`** and **`self._next._prev`**: write two *neighbors’* fields, not banana’s own fields.
- **`rest = self._prev`**: preserve a handle to the remainder before clearing banana’s links. Here that handle is apple because the code explicitly chose the predecessor.

### Why repeat the same extract block? The input changes (0:55:01–1:03:59)

A student notices that lines 42–47 recur. Repetition here tests different states with the same operations. First inventory what is *still linked*: not peach (the tail extraction took it out), not banana (the middle extraction took it out), and not acai. The instructor initially includes peach; the class walks back to the earlier extraction and corrects the inventory to apple ↔ mango. With two nodes left, “apple has no previous” does not mean “apple has no remainder.”

```python
iter = fruit             # Line 42: both names point to apple; the other linked node is mango.
fruit = iter.extract()   # Line 43: isolate apple and rebind fruit to the one remaining mango.
# Lines 44–47 display the two sides. Next time through the same source pattern:
iter = fruit             # Line 49: both names now point to mango, with no neighbor on either side.
fruit = iter.extract()   # Line 50: isolate mango; rebind fruit to a NEW, empty Item, not None.
```

For mango, both comparisons in **`self._prev == None and self._next == None`** are true. Unlike peach’s earlier test, the first comparison cannot settle the result, so the second must be evaluated. The body at line 96 is **`rest = Item( )`**. It does not use the module’s unrelated `if __name__ == "__main__":` test at line 113; it calls the class. The class body gives `_next` and `_prev` defaults of `None`. With no payload argument, the initializer sets the *new instance’s* `_object` to `None`. The common cleanup still acts on the old mango, which retains its `"mango"` payload.

Inside `extract`, the entire creation expression is one line:

```python
rest = Item( )                     # Make a fresh empty Item; mango itself is unchanged here.
```

Elsewhere in `class Item`, a different method initializes that instance:

```python
def __init__(self, obj = None):    # This self is the NEW instance; obj defaults to None.
    self._object = obj             # Store None on the new instance, not on mango.
```

The instructor distinguishes allocation from initialization: calling the class uses **`__new__`** to create the instance, then **`__init__`** to initialize it. In the class example the caller does not explicitly call either one. No earlier fruit is reused as the new empty `Item`. This is the key to the question at 1:03:42–1:03:59: taking out the only item must leave *an empty list*, but “empty” here means an `Item` whose payload is `None`, **not the Python value `None` returned in place of an `Item`**. The helper always prints its current payload once before it tests whether to break, so that empty representation displays `None` on one line. No call on a `None` receiver is involved.

| Driver selection and assignment | `display(iter)` payload(s) | `display(fruit)` payload(s) | Why the second display differs |
|---|---|---|---|
| Lines 42–43: take apple from apple ↔ mango | `apple` | `mango` | The head’s successor becomes the only remaining node. |
| Lines 49–50: take lone mango | `mango` | `None` | There is no neighbor to return; a new empty `Item` represents the remainder. |
| Lines 56–57: use that empty `Item` as receiver | `None` | `None` | Screen-visible third test: its two links are also absent. The return is *another* empty `Item`; equal-looking payloads are not proof of identical objects. |

The instructor defers the question about the final repetition at 1:02:48; the last row is readable in the **driver** and in already-visible terminal scrollback, not a claim it was explained before 1:04:00. All three rows were independently reproduced using the observed extraction/display logic and a five-node starting chain wired as a separate fixture. In the shell, each side has its own heading and five-hyphen separator; the cells above omit those repeated lines.

**Key items to scan**

- **`self._prev == None and self._next == None`**: selects either a lone fruit node or the empty representation; the test checks links, not the payload.
- **`rest = Item( )`**: create a new `Item` rather than returning a bare `None` or overwriting the old payload.
- **`__new__`**, **`__init__`**: creation followed by initialization; the new initializer’s `self` is not mango.
- **Empty `Item` versus `None`**: the first supports the list methods; the second is the stored payload which the display prints.

## 1:04:00–1:13:00 | When the last mango is removed, what should `fruit` be?

After removing the last mango, the caller still wants to work with the remainder: display it, append to it, or extract from it again. Returning the mango would give back the wrong part of the list. Returning **`None`** would give back something that has no list methods. **`Item( )`** instead makes a new empty node. The mango and the remainder are now two different objects. Displaying the remainder prints its *payload*, which happens to be `None`; that output does not mean the returned reference was `None`.

The class asked whether clearing both links would damage an empty list. Look at the receiver of those assignments: it is still the *old mango*. Skipping the `else` skips all neighbour repairs, not the common cleanup after them. The new empty item is not the target of either cleanup statement.

**Trace of the extraction logic, not a transcript of a single terminal screen.** An independent fixture used the classroom extraction and display methods on the linked values below. The classroom driver and output fragments support the progression; the first selection is legible in the earlier driver frames. “Returned handle” names the payload at the node returned by `extract`, whereas display starts at `first()` even when that handle is elsewhere.

| Selected node | Why that node? | Returned handle | Values displayed for the remainder |
|---|---|---|---|
| acai | First node | apple | apple, banana, mango, peach |
| peach | Last in the remainder | mango | apple, banana, mango |
| banana | From the first node, go next | apple | apple, mango |
| apple | Current handle | mango | mango |
| mango | Only populated node | new empty item E1 | `None` |
| E1 | Extract again, this time from empty | **another** empty item E2 | `None` |

**A deliberately worse return value, event 1:07:45–1:08:06.** Line 96 alone became `rest = None`; the instructor asked what happens at the *next* call to **`extract()`**. The following two questions have different answers:

| Try this on the returned `None` | What happens in a separate check of the shown logic? |
|---|---|
| `remainder.extract()` | `AttributeError: 'NoneType' object has no attribute 'extract'` — the instructor’s prediction. |
| `display(remainder)` | `AttributeError: 'NoneType' object has no attribute 'first'` — the full driver attempts display *before* its next extraction. |

Neither error was displayed in these classroom frames. By 1:09:00 the editor again used `Item( )`. This small change is a contract decision, not a formatting preference.

**Annotated teaching version — only the deliberately changed singleton branch.**

```python
if self._prev == None and self._next == None:  # Both links absent on the last mango.
    rest = None  # Experimental change: the common return now sends back bare None.
```

**Key items to scan**

- **`Item( )` vs `None`** — a new empty Item supports Item operations; the absence of an object of that class does not.
- **`extract()`** — repairs any neighbor links, detaches its receiver, and returns a handle into the remaining chain.
- **`display`**, **`-----`**, **`next()`** — print the separator and each payload until the accessor points back at the current endpoint.

### Does a new object mean a permanent memory leak?

Look at the *names* between the last two calls. When the new empty Item is assigned to **`fruit`**, Python changes what that name refers to, not what the old object is. A second name can keep an old object accessible. Merely printing the same payload twice cannot settle whether there is one object or two.

```python
iter = fruit              # At first, both names designate the lone mango.
fruit = iter.extract()    # fruit → new empty E1; iter still designates mango.
# The driver displays iter and fruit separately here.
iter = fruit              # Now iter and fruit both designate E1.
fruit = iter.extract()    # fruit → new empty E2; iter still designates E1.
```

At the end of *these exact lines*, E1 is **not** unreachable: `display(iter)` still prints its payload. The instructor’s wider point was about losing the last usable reference. One student proposed a second name, another asked if the computer still held the data, and another asked why one would need its old address. Keep a reference beforehand if the object is needed; a remembered address is not a substitute. The discussion of reference counts and garbage collection was not a measurement. Neither an immediate collection nor eventual memory exhaustion was demonstrated.

At 1:11:00 the instructor temporarily assigned the first remainder to **`fruit1`**, used `iter = fruit1`, and assigned the next remainder to **`fruit2`**. That would preserve both. The two result displays still read `display(fruit)`, however; the screen is a naming experiment, not evidence of a run printing `fruit1` and `fruit2`. The earlier names are back by 1:12:00.

**Key items to scan**

- **`fruit`**, **`iter`** — an assignment rebinds one name without retargeting another name that still designates the old object.
- **`fruit1`**, **`fruit2`** — alternate retained references, not new names for the outputs printed by the unedited display lines.
- **Reference count / reclamation** — ask which references still reach the object; do not rely on recovering its old address or on a particular collection schedule.

## 1:13:00–1:22:00 | Find who printed a line before changing the program

A student noticed the edited label might not have been saved. The class discussed saving and rerunning; the later terminal shows the edited heading, then looked for an object representation above the fruit heading. The earlier exposed terminal strip does show the beginning `<item.Ite…`; the beginning of a representation does not identify the statement that printed it. A student proposed driver line 3. But **`def display(list):`** binds a function; it does not execute the indented body. Its later call does. The driver’s own `print("\nFruit\\Veg:")` explains the blank line followed by `Fruit\Veg:`, not the extra representation.

The `Item` class explains two more distinctions. Its class body assigns defaults for the stored payload and links; its constructor writes the supplied value onto a particular instance. Reading the definitions during import makes the methods available; it does not call their bodies for every new item.

```python
class Item:
    _object = None                       # Class-level starting defaults on the screen.
    _next = None                         # No forward link until one is set.
    _prev = None                         # No backward link until one is set.

    def __init__(self, obj = None):      # Called for each new instance.
        self._object = obj               # Item("apple") stores apple; Item() stores None.

    def object(self):
        return self._object              # display prints whatever this returns.

    def next(self):
        if self._next != None:
            return self._next            # Actual successor if it exists.
        return self                      # Otherwise, endpoint test in display succeeds.

    def prev(self):
        if self._prev != None:
            return self._prev            # Actual predecessor if it exists.
        return self                      # Same convention in reverse.
```

The same 1:15:00 editor shows **`append`** and **`prepend`** testing the payload, not whether the receiver variable contains `None`. If its payload is empty, both assign the incoming value to the *existing* empty node and return that node. Otherwise `append` finds the last item, constructs a new one, links `newItem._prev` to the old tail and `last._next` to the new node. `prepend` does the mirror image at the first item. That is why a remainder made with `Item()` can be put back to work. For a concrete separate check, appending `"fresh"` to the final empty remainder returned the same instance; it was not replaced with another list.

**Key items to scan**

- **`def display(list):`** — body runs on call, not when Python encounters the definition.
- **`__init__`**, **`_object`** — each instance receives its own payload; that is what `object()` returns.
- **`append`**, **`prepend`** — fill an empty receiver in place; otherwise link a newly created node at the appropriate end.

### A diagnostic marker separates the entry program from an import

If `item.py` is run directly, its **`__name__`** is **`"__main__"`**. If the driver is run directly and imports `item`, the *driver* has that special name. The imported module still executes its top-level code, including the class definition. It simply fails this guard and skips the test. Instead of guessing what an unlabeled representation means, the instructor tagged the suspected print with a recognizable prefix.

```python
# In src/item.py; the guard is at module level, outside class Item.
if __name__ == "__main__":        # True for this module only when it is the entry program here.
    a = Item()                    # Construct a new empty Item for this direct test.
    print(f"a: {a}")              # Mark the instance representation with a distinctive prefix.
```

**Three separate classroom launches, in order (1:19:00 PowerShell).** The first direct run preceded the marker edit; its line was `print(a)`. The second tested the marker. The third ran the fruit driver and did not print the marker. The object addresses are from separate runs, not persistent identifiers. The driver output below stops after its initial displayed group.

```text
PS C:\CSCS1240F26\Item> py .\src\item.py
<__main__.Item object at 0x0000014A5B1486E0>
PS C:\CSCS1240F26\Item> py .\src\item.py
a: <__main__.Item object at 0x00000195547F86E0>
PS C:\CSCS1240F26\Item> py .\test\fruit.py

Fruit\Veg:
-----
acai
apple
banana
mango
peach
```

This check accounts for what the *current* two files do. It does not establish what earlier statement produced the clipped `<item.Ite…` line; the instructor explicitly left that unresolved. His course application was concrete: the next challenge would import a submitted Item and exercise `first`, `last`, `append`, `prepend`, `insertAfter`, and `insertBefore`. He said the challenge should see no standard output, and that `extract` and edge cases were not required at that stage. This is the in-class overview, not a substitute for the assignment instructions. Keeping ad hoc prints behind the guard allows direct tests without that import-time noise.

**Key items to scan**

- **`__name__`**, **`"__main__"`** — the guard distinguishes direct execution of this module from importing it.
- **`from item import Item`** — executes the imported module as needed and makes the class available to the driver; it does not call the guarded instance test.
- **`a:`** — a diagnostic marker whose presence or absence tests a specific explanation, without guessing from an address.

## 1:22:00–1:32:00 | One object walks the list; another decides what to do with each value

Before the next demonstration the instructor inspected **`git status`**: the working versions of `src/item.py` and `test/fruit.py` had unstaged edits. He discarded those two tracked edits with `git restore .\src\item.py` and `git restore .\test\fruit.py`, then selected **`git checkout master~8`**. The new checkpoint was `ed154df`, labeled `Item: forEach( ), withItem( ) callback`. This matters when reading the demonstration: a callback now starts where the edited file previously had its main guard. Here the restore commands discarded unstaged changes in the named tracked files. Do not use them on edits you want to keep.

Suppose a method visits five nodes, but the list class should not decide what to print about each one. Rather than return one accumulated result, **`forEach`** can call a supplied object once per value. The class put the intent into ordinary English: *for each of these objects, do this*. In this Python class the name is a convention chosen by the programmer, not a keyword.

**Matching classroom code, 1:24:00–1:32:00; comments added.** The driver supplies the action and builds the example:

```python
from item import Item                     # Use the linked Item type from this checkpoint.

class Stand:
    def withItem(self, value):            # Here self is the Stand; value is one fruit string.
        print("with: " + value )          # Report this payload now; no collection of results needed.

fruit = Item("apple")                     # New one-node chain; fruit points at apple.
fruit.append("banana")                    # Link banana after apple; fruit still points at apple.
fruit.append("peach")                     # Append peach at the tail.
fruit = fruit.last()                      # Rebind fruit to peach in the same chain.
fruit.insertBefore("mango")               # Put mango between banana and peach.
fruit.prepend("acai")                     # Put acai before the first node, not just before peach.
stand = Stand()                           # New callback receiver, separate from the linked nodes.
fruit.forEach(stand)                      # Give Item both a receiver (peach) and a recipient (stand).
```

The implementation in `Item` does not know how Stand prints. It only assumes that the supplied object has a compatible **`withItem`** method. When `fruit.forEach(stand)` starts, `self` is the peach Item, not the Stand. Inside `stand.withItem(value)`, the *other* method’s `self` is the Stand. This is what gives the extra **`caller`** parameter a job. The instructor used employer/employee to distinguish the initiator from the recipient of a call. Despite the parameter’s name, it is the driver that executes `fruit.forEach(stand)`; `stand` is the object to call back.

```python
def forEach(self, caller):                 # self: Item; caller: the Stand passed by the driver.
    iter = self.first( )                   # Start at acai even though fruit designates peach.
    while True:
        caller.withItem(iter._object)      # Pass the payload string, not its containing Item.
        if iter._next == None:             # Inspect the underlying link after sending this value.
            break                          # The final peach has already been delivered.
        iter = iter._next                  # Move to the next node and repeat.
```

**Classroom run, event 1:31:30:** `py .\test\fruit.py`. Each printed line comes from `Stand.withItem`, never from a `print` in `Item.forEach`.

| Current Item | Actual output from callback | What happens after the callback? |
|---|---|---|
| acai | `with: acai` | Follow its link to apple. |
| apple | `with: apple` | Advance to banana. |
| banana | `with: banana` | Advance to mango. |
| mango | `with: mango` | Advance to peach. |
| peach | `with: peach` | `_next == None`; break, then return to the driver. |

A callback here is synchronous: the loop invokes the handler during its traversal, not in a background job. The list controls *when* each handler call happens; a different compatible receiver could supply a different action without rewriting the walk. The instructor called this a design pattern and encouraged trying another receiver to see where the assumptions break.

**Key items to scan**

- **`git status`**, **`git checkout master~8`** — distinguish local modifications from the recorded callback checkpoint being demonstrated.
- **`forEach`**, **`caller`** — Item owns traversal; the driver explicitly supplies another object to receive callbacks.
- **`withItem`** — Stand’s per-value method; it receives the payload, not an Item node.
- **`_next`** — raw link is `None` at the endpoint; checking after delivery preserves the final value.

## 1:32:00–1:35:59 | From a callback to `for x in fruit`

The next checkout, **`git checkout master~7`**, reports `ec4fdd0 Item: python Iterator`. The previous five `with:` lines are still visible in its terminal history; they do **not** show that the iterator driver ran. In the updated fruit driver (event 1:33:32), the construction of the same five-node chain remains, but the entire Stand class and callback call are gone. The driver itself now states what to do on each pass:

```python
for x in fruit:               # Ask this iterable for successive values; bind each one to x.
    print( x )                # The driver, not Stand.withItem, now owns the printing.
```

A student asked whether this numbers the list. No: **`for … in …`** binds the target to each supplied value; it does not automatically supply an index. An iterable is something this form can ask for successive items. The new checkout aims to make the custom Item usable in that position. The displayed driver establishes the interface being requested; its implementation and a run of this driver are not shown at this transition.

The instructor then opened a separate scratch buffer to compare a built-in list with the linked structure. At 1:35:00 the exact data was **`[2,4,5,7,]`**, with a legal trailing comma. He contrasted printing the entire list once with binding a variable on each pass and printing that value. His suggested **`_`** is a usable variable like `x` here: `print(_)` reads its current value. The in-progress second line visible then is only `pr`; the finished program and its terminal output belong to the following portion, not the callback run.

**Key items to scan**

- **`git checkout master~7`** — selects the iterator checkpoint; old scrollback survives the switch.
- **`for … in …`** — successive values, not automatic numbering; the loop body chooses what happens to each value.
- **`[2,4,5,7,]`**, **`_`** — ordinary-list control example; trailing comma adds no element, and the target still holds the current item.

### Values, positions, and the first position that does not exist · 1:36:00–1:39:36

What does the loop variable stand for? The instructor starts with four numbers and prints the container before printing its contents. The verb he corrects is important: the loop **assigns** each element to **`_`**. It does not return a list of answers. In this program `_` is an ordinary readable variable; choosing that short name doesn't discard the value.

Classroom `forloop.py`; comments to the right are guide-added:

```python
a = [2,4,5,7,]     # Make a list of four integers; the last comma adds no element.
print(a)           # Print the whole container: [2, 4, 5, 7].
for _ in a:        # Bind _ to 2, then 4, then 5, then 7.
    print(_)       # Print the current integer, not its index.
```

Observed output of **`py .\test\forloop.py`** (1:36:00):

```text
[2, 4, 5, 7]
2
4
5
7
```

Next, leave the first two lines alone and change the expression after **`in`**. **`range(4)`** supplies integers starting at zero and stops *before* four; it does not look inside the list. The instructor mentions that a range can also specify a start and increment. Although he calls it an “iterable list” in passing, the expression produces a range object, not a Python list. Here it happens to supply exactly the four valid positions in `a`.

```python
for _ in range(4):    # Take 0, 1, 2, 3 from the range.
    print(_)          # Print those positions, not 2, 4, 5, 7.
```

The first rerun still printed the old values; the instructor realized he had not saved. In the 1:38:00 terminal the *newest* run prints `[2, 4, 5, 7]` and then `0`, `1`, `2`, `3`, each on its own line. Asked how to turn those numbers back into the original values, a learner suggested arithmetic. The list already contains the answers: use each number as a subscript.

```python
for _ in range(4):    # Visit the four positions.
    print(a[_])       # Read a[0], a[1], a[2], a[3] before printing each result.
```

This is the edit at **1:38:52**. Its successful result is visible at **1:39:00**: `[2, 4, 5, 7]` followed by `2`, `4`, `5`, `7` on separate lines. At that point the editor has *already moved on* to the following experiment:

```python
for _ in range(5):    # Request one more position: 4.
    print(a[_])       # The lookup fails at a[4]; print gets no fifth value.
```

| `_` supplied by the range | Lookup before printing | Result |
|---|---|---|
| `0` | `a[0]` | prints `2` |
| `1` | `a[1]` | prints `4` |
| `2` | `a[2]` | prints `5` |
| `3` | `a[3]` | prints `7` |
| `4` | `a[4]` | raises `IndexError: list index out of range` |

At **1:39:07** the new invocation prints the list and all four valid values *before* the line-4 exception. The earlier successful run didn't fail retroactively. Nor is this how normal `for` loops end: here the range supplied a valid next number, and a separate list lookup in the body failed.

**Key items to scan**

- **`_`** — successive list elements in the first loop; successive range integers in the revisions.
- **`py .\test\forloop.py`** — launches the saved script. An unsaved edit does not change the next execution.
- **`in`**, **`range(4)`** — specify where the values come from; the upper bound is excluded.
- **`a[_]`**, **`IndexError`** — read the numbered position; no position 4 in a four-element list.

### From a callback to Python’s `for` loop · 1:39:51–1:51:45

A student suggests that Python knows when to stop because it “counts.” The instructor asks the class to consider a different collection: its nodes link to one another, and the loop in the driver supplies no count at all. Previously, **`forEach`** explicitly walked the nodes and called **`caller.withItem`** on each stored object. Line 120, `iter = iter._next`, belongs to *that* method. The name `forEach` was the instructor’s choice. **`for`** is a Python keyword; `for x in fruit:` does not call the method merely because its name resembles the keyword.

First establish which node the driver's name actually identifies. The proposals “apple,” “peach,” and “acai” all name real nodes, but answer different questions. Use assignments, not the order in which the fruit names were mentioned. The right-hand editor at **1:41:00** and **1:48:00** shows:

```python
from item import Item          # Use the classroom node class.
fruit = Item("apple")          # Create the apple node; fruit refers to it.
fruit.append("banana")         # Add banana after apple; fruit still refers to apple.
fruit.append("peach")          # Add peach at the end; still no rebinding.
fruit = fruit.last()           # Rebind fruit to the existing peach node.
fruit.insertBefore("mango")    # Link mango immediately before peach.
fruit.prepend("acai")          # Add a new first node; fruit still refers to peach.

for x in fruit:                # Obtain an iterator starting from fruit.
    print( x )                 # Print the stored string that each next call returns.
```

What is first in the chain? **`acai`**. What is the last node assigned to **`fruit`**? **`peach`**. Changing the links did not reassign the caller's variable. The earlier terminal lines `with: acai` through `with: peach` belong to the callback driver at the preceding checkout. They are *not* observed output from this bare `print( x )` driver.

**Key items to scan**

- **`forEach`**, **`caller.withItem`** — classroom traversal and callback; receive one stored payload per visit.
- **`for`** — asks the iterable for an iterator rather than calling the classroom `forEach` method.
- **`fruit`**, **`peach`** — line 6 rebinds the name to peach; later insertion does not change that binding.
- **`acai`** — first node after prepend, distinct from the node named by `fruit`.

### Which object advances? · 1:41:21–1:58:29

The two new methods let `Item` participate in **`for x in fruit`**. Read the returns literally. **`__iter__`** returns the *iterator object*. It is **`__next__`** on that returned object that supplies the first value of `x` **as well as every later value**. The spoken walkthrough briefly describes `x` as coming straight from `__iter__` and, at one point, calls peach the receiver for every next call. The displayed return lines resolve both points.

`src/item.py` lines 122–133, method bodies as displayed; comments guide-added:

```python
def __iter__(self):                # Called once on peach for this loop.
    head = self.first( )           # Follow the chain to the acai node.
    head._iter = head              # On acai, create/reset a cursor referring to acai.
    return head._iter              # Return that node as the iterator, not the string "acai".

def __next__(self):                # Called repeatedly on the returned node (acai).
    head = self.first( )           # A fresh local named head refers to the first node.
    if head._iter == None:         # Has the cursor passed the last node?
        raise StopIteration        # Tell the for loop there is no next element.
    target = head._iter._object    # Save this node’s stored string before moving.
    head._iter = head._iter._next  # Move only the cursor; leave the chain and fruit intact.
    return target                  # Bind x to the saved string for this pass.
```

“What's the value of `head` before this assignment?” was a trap worth keeping: on entering the second method its local **`head`** has no binding yet. It is not a shared variable left behind by the first method, and it has not been assigned `None`. Both locals can refer to the *same node* once assigned. The state that survives between calls is **`head._iter`**, an attribute of that node. Python lets the instructor create it by assignment; the leading underscore signals intended internal use rather than enforcing privacy. Repeating `head = self.first( )` therefore does *not* restart traversal. Repeating `head._iter = head` would reset it; that statement is in `__iter__`, not `__next__`.

**Pass-by-pass check.** Starting with `fruit` bound to peach, `iter(fruit)` returns the acai **node**. In each successful row below, first read **`_object`** from the cursor's node, next read its **`_next`** link and update the cursor, *then* return the preserved string. These transitions were independently checked using the displayed iterator methods and a separately linked five-node fixture. They explain the driver; they are not a transcript of this version being run in class.

| `next` call | Cursor before | `target` (then `x`) | Cursor after | What the driver would do |
|---|---|---|---|---|
| 1 | acai node | `"acai"` | apple node | print `acai` |
| 2 | apple node | `"apple"` | banana node | print `apple` |
| 3 | banana node | `"banana"` | mango node | print `banana` |
| 4 | mango node | `"mango"` | peach node | print `mango` |
| 5 | peach node | `"peach"` | `None` | print `peach` |
| 6 | `None` | no `target` assigned | `None` | leave loop without printing |

The class asks exactly where the cursor becomes `None`. It happens **on line 132 during the successful peach pass**: peach has no successor. Only the *following* call reaches **`raise StopIteration`**. Python's `for` treats this signal from its iterator as normal exhaustion, unlike the `IndexError` raised in the earlier loop body. The chain is walked **forward**, not backwards from peach: `first()` chooses the starting end, and `_next` chooses the direction. Acai's immediate successor is apple, not banana; the instructor corrects that point before continuing.

**Key items to scan**

- **`for x in fruit`**, **`__iter__`** — request an iterator from peach; the method returns the acai node.
- **`__next__`**, **`_object`** — each successful call on the iterator yields a stored string, not a node.
- **`head`**, **`head._iter`** — one is a local binding; the other is a persistent cursor attribute on the head node.
- **`_next`**, **`raise StopIteration`** — advance through links; the next request after the final payload ends the loop.

### One integer, or a list containing one integer? · 1:58:29–2:01:03

A learner predicts that a new `b = 7` example will print another seven after the earlier list output. The instructor removes the `a` block so the new case stands alone. At 1:59:00 both blocks are visible in an edited buffer; the captured error at 1:59:49 is from the *three-line* script below, not that combined draft.

```python
b = 7              # The object is an integer; this assignment prints nothing.
for _ in b:        # Python cannot get an iterator from the integer.
    print(_)       # Never reached: even one iteration would need an iterable.
```

Observed, line 2: **`TypeError: 'int' object is not iterable`**. No integer was printed by this run. The class contrasts an int or float with a string, list, or set: the question is whether the object can supply elements, not whether it has a value. A student notices the missing brackets. Keep the integer; change the object the loop is handed:

```python
b = [7]            # New list with one member, the integer 7.
for _ in b:        # Iterate over the list; _ refers to its one element.
    print(_)       # Print 7 once, then the list iterator is exhausted.
```

Observed at **2:00:56**, after a fresh script invocation: `7`. The square brackets do not make the integer itself iterable. They give Python a different object to iterate over.

**Key items to scan**

- **`b = 7`**, **`TypeError: 'int' object is not iterable`** — the loop fails before assigning its variable or running its body.
- **`b = [7]`** — the container supplies one element. Unlike the four-element exercise, there is no `print(b)` before this loop.

### Ask the container and its element separately · 2:01:03–2:07:34

The instructor now enters bare **`py`** in PowerShell and starts a fresh interactive Python 3.14.7 session. The names no longer have to match the script’s variables. He sets **`a = 7`** and **`b = [7]`**. Rather than guessing from the brackets, use **`dir`** to inspect which attribute names each object presents.

| Interactive input, in teaching order | Observed result and why |
|---|---|
| `dir(a)` | Integer names, including `__index__`, `bit_length` and `to_bytes`; no `__iter__`. |
| `dir(b)` | List names, including `__getitem__`, `__iter__`, `__len__`, `append`, `pop` and `sort`. A learner asks whether the display only *looks* shorter; this displayed list really has fewer names than the integer's. |
| `dir(a[0])` | `TypeError: 'int' object is not subscriptable`. Python must evaluate `a[0]` *before* it can call `dir`; an integer does not provide an element at position zero. |
| `dir(b[0])` | First look up the integer `7` in the list. Its attribute names agree with those in `dir(a)`, not `dir(b)`. |
| `dir(list)` | The list type itself gives the same displayed name sequence here as `dir(b)`. The instructor also points out names such as `__new__` and `__init__`; listing a name does not call it. |

An iterable need not be its own iterator: the list display contains **`__iter__`** but not **`__next__`**. The list can provide a separate iterator; the classroom `Item` instead returns a node that implements `__next__` itself. Counting all the names would not reveal that distinction. Look for the operation you need and identify which object supplies it.

The final experiment asks what the loop variable receives from a one-element list. At 2:06:00 the body is still being typed; at **2:06:26**, the *submitted* REPL input is:

```pycon
>>> for x in b:
...     dir(x)
...
```

The REPL displays the integer attribute-name list again, from `'__abs__'` to `'to_bytes'`, because **`x`** is the contained `7`. It does **not** print a bare `7` here: that was the earlier script's `print(_)` result. The interactive interpreter displays the value of the expression `dir(x)` without an explicit `print`; in a script that bare expression would not display the list.

**Key items to scan**

- **`py`** — no script argument on this launch; `>>>` marks the interactive prompt.
- **`a = 7`**, **`b = [7]`**, **`dir`** — same integer, different objects inspected: scalar, list, and list element.
- **`__iter__`**, **`__next__`** — the ability to *supply an iterator* is distinct from the ability to *supply the next element*.
- **`x`** — integer payload in the final loop; `dir(x)` displays its attribute names.

The instructor stops the technical discussion at about 2:06:44, then signs off to the people present and anyone watching later: “enjoy the rest of your day” (final caption starts 2:07:32.560). The closing capture shows the recording controls, not another example.

## Transfer lab · Can two walks keep their place?

**Supplemental, independently tested.** Use a fresh interpreter for this iterator experiment so the earlier driver variable named `iter` does not shadow the built-in `iter()` function. Start with the same five fruit nodes in their final order and `fruit` pointing to peach. The class walk works, but where the cursor is stored matters if two consumers try to walk at once. Predict the third printed line before reading the result.

**Annotated teaching version — guide-added experiment and comments.** Requires the iterator revision of `Item` and the fruit setup above, not the earlier callback-only checkout.

```python
walk = iter(fruit)       # Peach supplies the acai head as the iterator.
print(next(walk))        # Prints acai; the cursor now points to apple.
again = iter(fruit)      # Starts at the same head and resets its cursor.
print(walk is again)     # True: two names for one iterator object.
print(next(walk))        # Prints acai again, not the apple we expected before the reset.
print(next(again))       # Prints apple: both names advance the same saved position.
```

**Independent fixture output, not classroom scrollback:**

```text
acai
True
acai
apple
```

The surprise is the **second `acai`**. A name can be local to a function call, but an attribute belongs to an object. Both traversals write the same head node’s `_iter`. An iterator that needs an independent position for each consumer would put the cursor on a separate iterator object per walk. Python distinguishes the [iterable that supplies an iterator from the iterator that supplies successive values](https://docs.python.org/3/library/stdtypes.html#iterator-types).[1]

**Key items to scan**

- `walk is again` — tests object identity, not whether two traversals happen to produce equal strings.
- `head._iter` — one shared storage location; starting the second walk resets the first.
- `next(walk)` — advances once; it does not call `iter(fruit)` again before each step.

**Two language references for the lesson.** A [`range`](https://docs.python.org/3/library/stdtypes.html#ranges) is an immutable sequence, not a materialized Python list; `range(4)` supplies the indexes `0, 1, 2, 3`.[1] The [`__main__` documentation](https://docs.python.org/3/library/__main__.html) distinguishes running a module as the entry point from importing it; it explains the guard, not the source of an unidentified classroom print.[2]

## Practice setup · Run the examples without the checkout

If the original class is not in your Python path, the following small support class makes the worked examples runnable in **one file**. It is a guide-created adapter for the visible interface—not a recovery of the clipped class source. For instance, it initializes instance links explicitly and uses shorter navigation loops. Paste it first in `practice.py`. Add the indented `extract` method from **Extracting without deleting** to the class. Then add the top-level `display` helper and eight-line apple setup from **One name, five linked items**, followed by the six-line driver block from **Which item loses its backlink?**. Run `python practice.py`. No `from item import Item` is needed in this one-file arrangement. The historical driver uses that import instead. The printed setup and first-extraction groups above were checked by running exactly that block sequence; the escape variations and temporary failing method are separate experiments.

```python
class Item:  # Practice implementation, not a verbatim class listing.
    def __init__(self, obj=None):  # Allow an empty item by default.
        self._object = obj  # Store the payload, even if it is None.
        self._prev = self._next = None  # Practice version sets instance links explicitly.

    def object(self):  # Read the payload rather than printing the Item itself.
        return self._object  # Return the stored value.

    def next(self):  # Navigate one step forward.
        return self if self._next is None else self._next  # A missing link makes next() return this same Item.

    def prev(self):  # Navigate one step backward.
        return self if self._prev is None else self._prev  # Mirror the forward endpoint rule.

    def first(self):  # Find the head from any node.
        node = self  # Use a local cursor, not a new list.
        while node._prev is not None:  # Can we still move backward?
            node = node._prev  # Follow one backlink.
        return node  # Return the actual first node.

    def last(self):  # Find the tail from any node.
        node = self  # Start at the supplied node.
        while node._next is not None:  # Can we still move forward?
            node = node._next  # Follow one forward link.
        return node  # Return the actual last node.

    def append(self, obj):  # Add at the end.
        if self._object is None:  # Reuse an empty receiver rather than making a second node.
            self._object = obj  # Give it the first payload.
            return self  # It is the item that was filled.
        tail = self.last()  # Find the old tail.
        node = Item(obj)  # Allocate the new tail.
        tail._next, node._prev = node, tail  # Link the two items in both directions.
        return node  # Return the inserted item (driver ignores it).

    def insertBefore(self, obj):  # Add immediately before this receiver.
        if self._object is None:  # An empty receiver can hold the value directly.
            self._object = obj  # Fill it.
            return self  # No distinct node was needed.
        node = Item(obj)  # Create a new item for a nonempty list.
        node._prev, node._next = self._prev, self  # It lies between the old predecessor and this item.
        if self._prev is not None:  # Only a non-head receiver has a previous neighbor.
            self._prev._next = node  # Redirect that neighbor forward to the insertion.
        self._prev = node  # Receiver now points back to the insertion.
        return node  # Return the inserted node.

    def prepend(self, obj):  # Add at the beginning from any receiver.
        return self.first().insertBefore(obj)  # Reuse head insertion; it returns the new head.
```

**Verification of the practice arrangement.** In fresh practice directories, the three recovered `fruit.py` drivers were launched against an `item.py` made from the practice support and the methods for that revision. The extraction driver completed all six trials. The callback driver printed the five `with:` lines; the iterator driver printed the same fruit without prefixes. These are independent runs with a practice module, not claims about an unseen classroom checkout.

## Syntax to remember

| Pattern from the session | Read it this way |
|---|---|
| `print("\nFruit:")` | One string argument. Start a new line, then write the literal label; the ordinary `print` ending also advances the line. |
| `fruit = fruit.last()` | Resolve the old `fruit`, call its method, then bind the same name to the returned node. This is not a move in memory. |
| `self._next._prev = None` | Follow **one link** to the neighbor; clear **that neighbor’s** backward link. Don’t clear `self._next` prematurely. |
| `fruit = iter.extract()` | Keep the isolated node under `iter`; bind `fruit` to a node in the remainder (or an empty `Item`). |
| `if __name__ == "__main__":` | A gate for demo code that should run on direct launch but not on ordinary import. |
| `caller.withItem(iter._object)` | Call the supplied object’s method with the current node’s stored value. |
| `for x in fruit:` | Obtain an iterator, repeatedly request values, bind each successful value to `x`, then run the loop body. |
| `raise StopIteration` | Signal that there is no next value. This is different from `return None`, which would yield a value. |
| `dir(b)` vs. `dir(b[0])` | Inspect the list itself versus its first element. With `b = [7]`, those are different types. |

## Knowledge check

1. After appending banana and peach, why does the driver need `fruit = fruit.last()` to make `fruit` refer to peach? Does prepending `acai` change that binding?
2. You stop right after `self._next._prev = None` while extracting `acai`. Which link is already clear? Which link still leads to apple? Why save `rest` before clearing it?
3. With `apple ↔ banana ↔ mango`, predict both names after `iter` is set to banana and `fruit = iter.extract()` completes. Can the remaining list be walked in both directions?
4. A proposed shortcut returns `None` after removing the last fruit. At which point does that break a caller: at assignment, or at a subsequent method call? What does the real implementation return instead?
5. A test script imports `Item`. Why can it use the class without printing the module’s guarded demo? Would moving the demo outside the guard change that?
6. In the callback version and the iterator version, what crosses the boundary into the consuming code on the first step? Which object controls the next step in each version?
7. `fruit` still refers to peach. In the displayed iterator revision, what is the result of `iter(fruit)`, what is the first value returned by `next`, and what happens one request after peach?
8. Why can `for x in [7]` work when `for x in 7` fails? Is the failure the same as indexing past the end of `[2, 4, 5, 7]`?

### Answer key

1. Appending changes the links; it does not assign a new value to the caller’s name. `fruit` starts on apple. The explicit assignment makes it refer to peach. Inserting mango and prepending `acai` change the chain, not that binding.
2. Apple’s `_prev` is now `None`; `acai._next` still leads to apple. Save the apple reference while that forward link exists. When both links on `acai` are cleared, `self` and the caller’s `iter` still refer to the isolated `acai`, and `rest` still reaches the remainder.
3. `iter` still names banana, now isolated; `fruit` names apple because the middle branch returns banana’s former predecessor. Apple’s forward link now points to mango **and** mango’s backward link points to apple. Fixing only one link would leave the reverse walk wrong.
4. Assignment accepts `None`. Failure comes when the caller asks that value for an `Item` method such as `extract`. The normal singleton branch returns a *new* `Item()` with no neighbors and a `None` payload. It has the methods the caller needs.
5. On ordinary import in this example, the module’s `__name__` is not `"__main__"`. Python processes the class definition but skips the guarded test. An unguarded top-level print would run on initial import and could pollute the challenge grader’s expected stdout.
6. Initially both consumers receive the string `"acai"`, not the entire chain. `forEach` walks the nodes and calls `stand.withItem` at each one. With `for`, Python requests each value from the returned iterator; the driver’s loop body decides what to do with it.
7. `__iter__` on peach finds `acai`, stores that node in its own `_iter` attribute, and returns that **node**. The first `__next__` on that iterator returns the **string** `"acai"`; it leaves the cursor on apple. After returning `"peach"`, the cursor is `None`. One further request raises `StopIteration`; the body does not run again.
8. `[7]` is an iterable list containing one integer; asking the integer itself for an iterator gives a `TypeError`. Accessing index `4` on a four-element list is a different operation: the list exists, but that index does not, so Python raises `IndexError`.

## Library of definitions

| Term | Meaning in this lesson |
|---|---|
| Node / `Item` | One object holding a payload and links. A reference to a single node can give access to the entire connected chain. |
| Payload | The content, such as the string `"mango"`. It is not the `Item` object that holds the string. |
| Doubly linked list | A chain in which neighbors refer to one another through forward and backward links. |
| Head / tail | First node (no predecessor) and last node (no successor). The caller’s `fruit` need not point to the head. |
| Reference / alias | A way to reach an object; two names may reach the same node. Changing the node is visible through either name; rebinding one name does not rebind the other. |
| Receiver / `self` | The object on which a method was invoked, available through a local parameter in the method. It need not be the object returned by the method. |
| Empty `Item` / singleton | The empty representation has a `None` payload and no neighbors. A one-fruit chain also has no neighbors but has a fruit payload. The no-neighbors test covers both. |
| Short-circuit evaluation | With `and`, a false left operand is enough to choose the false path. Python need not evaluate the right operand. |
| Constructor / initializer | Calling `Item()` produces an instance; object creation and `__init__` initialization are distinct steps. The default argument supplies `None` when no payload is passed. |
| Module / entry point | Importing a module makes definitions available; the module launched as the program has the special name `__main__`. |
| Callback | Code invoked back on an object supplied by another caller. `forEach` knows how to walk the chain; `Stand.withItem` knows what to print. |
| Iterable / iterator | An iterable provides an iterator. An iterator supplies successive values and signals exhaustion. In this implementation, the head node doubles as the iterator. |
| Cursor | The saved position, here `head._iter`. The node holding the attribute stays put while the attribute changes from node to node. |
| Exception | A control signal, not a returned payload. Here exhaustion ends a `for` loop normally; `TypeError`, `IndexError` and `AttributeError` identify different failed operations. |
| Introspection | Examining an object’s attributes, here with `dir`. It helps distinguish the capabilities of a list, its integer element and an iterator. |

## Sources

[1] https://docs.python.org/3/library/stdtypes.html — Python documentation — Iterator types
[2] https://docs.python.org/3/library/__main__.html — Python documentation — __main__

## Historical screen-code appendix · What was visible

Numbers before the vertical bar are Sublime Text gutter numbers, not Python to type. These are **assembled screen transcriptions**, not exported repository files. Each checkout has its own listings. A timestamp means a requested frame target; an editor state alone is not evidence of a save or a run.

### 1 · Extraction driver: `test/fruit.py`

**Editor, not a single photographed run.** Sublime Text shows `C:\CSCS1240F26\Item\test\fruit.py` (right) and `C:\CSCS1240F26\Item\src\item.py` (left). Numbers below reproduce the editor gutter; `|` is editorial. The right-pane transcription assembles overlapping text: 1:03:00 supplies lines 1–19; 0:32:00 and 0:40:00 supply 20–27; 0:43:00 and 0:48:00 supply 28–36; 0:52:00, 0:55:00, 0:57:00 and 0:58:00 supply 37–62. In particular, line 57 is fully readable below the camera at 0:52:00 and above it at 0:58:00; 58/60 clear the camera at 0:55:00. Matching text across the overlaps supports this assembly, not a claim about intervening saves. The extra space in `next( )` and the two source backslashes in the first heading are visible. The first selected node is confirmed at line 21; all six trials use the same construction.

**`fruit.py` — display and all six extraction trials, with one shared setup.**

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
 18 | print("\nFruit\\Veg:")
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
 31 | display(iter)
 32 | print("\nFruit:")
 33 | display(fruit)
 34 |
 35 | iter = fruit.first().next( )
 36 | fruit = iter.extract()
 37 | print("\nIter:")
 38 | display(iter)
 39 | print("\nFruit:")
 40 | display(fruit)
 41 |
 42 | iter = fruit
 43 | fruit = iter.extract()
 44 | print("\nIter:")
 45 | display(iter)
 46 | print("\nFruit:")
 47 | display(fruit)
 48 |
 49 | iter = fruit
 50 | fruit = iter.extract()
 51 | print("\nIter:")
 52 | display(iter)
 53 | print("\nFruit:")
 54 | display(fruit)
 55 |
 56 | iter = fruit
 57 | fruit = iter.extract()
 58 | print("\nIter:")
 59 | display(iter)
 60 | print("\nFruit:")
 61 | display(fruit)
 62 |
```

### 2 · `fruit.py:18`: the actual revisions, not alternative runs

Never apply a changed line 18 to all the other literal `Fruit:` labels in the driver; they stay as shown. The double quotes before and after the stray backslash in the 0:08:27 row are two different string delimiters; do not insert commas or `+` to repair the example. A double backslash in the source is two characters in this Markdown code block.

```text
0:04:00; also 0:06:00                   18 | print("\nFruit:")
0:07:00                                 18 | print("\nFruit\Veg:")
0:08:00                                 18 | print("\nFruit\nVeg:")
0:08:27 event; also 0:09:00             18 | print("\nFruit"\"Veg:")
0:09:23 event                           18 | print("\nFruit'\'Veg:")
0:10:00–0:12:00; restored by 0:14:00    18 | print("\nFruit\\Veg:")
0:13:00                                 18 | print(f"\nFruit\\Veg:")
0:13:14 event                           18 | print(f"\n{fruit}\\Veg:")
0:13:27 event                           18 | print("\n{fruit}\\Veg:")
```

### 3 · Extraction-era `src/item.py` — compatible ranges, not a single photographed file

Lines 1–44: 1:16:00 and 1:15:00. Lines 45–72: 0:42:00–0:43:00; the overlapping 43–45 establish the return and next definition. Lines 73–84: the early narrow pane at 0:22:00; no missing suffix has been filled by analogy. Lines 85–115: 1:03:00, 0:32:00 and 1:06:00. The wider views recover the constructor, append/prepend links and traversal guards that were cut off in earlier views. The next checkout is not being used to complete this version.

**1–44: Defaults, public accessors and adding at the ends.** The defaults at class scope are not assignments in the constructor. A new payload is set on the instance. Reading past an endpoint through a public navigator returns the current node; adding to an occupied chain links in a distinct node.

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
```

**45–91: Moving to the ends and inserting beside a node.** `last` and `first` move a local cursor without changing the caller’s reference. In the interior insertion case the old neighbor must remain reachable until both links have been updated. Some statements in the insertion methods are visible only in part.

```text
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
73 |                 newItem._prev[remainder not visible]
 74 |                 self._next = [remainder not visible]
 75 |         return newItem
 76 |
 77 |     def insertBefore(self, ob[remainder not visible]
 78 |         if self._object == No[remainder not visible]
 79 |             self._object = ob[remainder not visible]
 80 |             newItem = self
 81 |         else:
 82 |             newItem = Item(ob[remainder not visible]
 83 |             if self._prev != [remainder not visible]
 84 |                 newItem._next[remainder not visible]
85 |                 newItem._prev = self._prev
 86 |                 self._prev._next = newItem
 87 |                 self._prev = newItem
 88 |             else:
 89 |                 newItem._next = self
 90 |                 self._prev = newItem
 91 |         return newItem
```

**92–115: Detach and return, then a separate direct-launch test.** The four paths converge on the two link clears; the payload survives. The module guard is outside the class. Its `print(a)` is not an instruction in the fruit driver.

```text
92 |
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

**Temporary counterexample, 0:25:00–0:26:00.** Compared with the preceding listing, insert exactly this line between `rest = None` and the first test; lines 95–115 of the stable version move down one. The selection is editor highlighting, not a comment or quotation. The frame shows no execution of this variant. By 0:27:00 the line has been removed.

```text
 93 |     def extract(self):
 94 |         rest = None
 95 |         self = None
 96 |         if self._prev == None and self._next == None:
 97 |             rest = Item( )
```

**Temporary singleton alternative, event 1:07:45 and event 1:08:06.** Only the changed line below; the `None` initializer at 94 is present in *both* versions. Line 96 is back to `rest = Item( )` at 1:09:00. No run/traceback from the altered editor is visible.

```text
 96 |             rest = None
```

**Alternate names, 1:11:00 only; L49–61.** Neither display argument naming `fruit` was updated. Restored by 1:12:00. No execution attributable to this edit.

```text
 49 | iter = fruit
 50 | fruit1 = iter.extract()
 51 | print("\nIter:")
 52 | display(iter)
 53 | print("\nFruit:")
 54 | display(fruit)
 55 | 
 56 | iter = fruit1
 57 | fruit2 = iter.extract()
 58 | print("\nIter:")
 59 | display(iter)
 60 | print("\nFruit:")
 61 | display(fruit)
```

**Marker test, event 1:18:45 and 1:20:00; `src/item.py`, not the fruit driver.**

```text
113 | if __name__ == "__main__":
114 |     a = Item()
115 |     print(f"a: {a}")
```

1:21:00 is a *different*, unfinished edit: lines 112–113 appear blank; line 114 `a = Item()` is still indented at the method-definition/class-body column; the completion menu covers 115+. Not a stable two-line unguarded test or evidence that this state ran. The instructor’s general point concerned executable, unguarded module code, not this edit's correctness.

### 4 · Already-visible PowerShell scrollback, not a run of the label edits

0:01:00, before the above label experiments. The command and the beginning of the run are above the visible region. The screenshot starts with a two-value `Fruit:` group, not the initial five-fruit group. Repeated `None` below separators is printed payload text, not the console’s representation of a returned reference. The latter interpretation is consistent with the driver and separately tested behavior, not enough to identify an unseen command.

```text
Fruit:
-----
apple
mango

Iter:
-----
apple

Fruit:
-----
mango

Iter:
-----
mango

Fruit:
-----
None

Iter:
-----
None

Fruit:
-----
None
PS C:\CSCS1240F26\Item>
```

### 5 · Callback checkpoint and driver

`src/item.py`, 1:24:00–1:29:00, starts this method on 113 beneath `return rest`. At 1:25:00–1:29:00 the method is readable without an overlay. `forEach` passes the stored value directly; `withItem` is an ordinary method on another object.

```text
113 |     def forEach(self, caller):
114 |         iter = self.first( )
115 |         while True:
116 |             caller.withItem(iter._object)
117 |             if iter._next == None:
118 |                 break
119 |             iter = iter._next
120 | 
```

`test/fruit.py`, right editor 1:30:00–1:32:00. Two blank rows between `print` and the setup. See event 1:31:30 for the **actual** matching callback launch and result; its output is not iterator output.

```text
  1 | from item import Item
  2 | 
  3 | class Stand:
  4 |     def withItem(self, value):
  5 |         print("with: " + value )
  6 | 
  7 | 
  8 | fruit = Item("apple")
  9 | fruit.append("banana")
 10 | fruit.append("peach")
 11 | fruit = fruit.last()
 12 | fruit.insertBefore("mango")
 13 | fruit.prepend("acai")
 14 | 
 15 | stand = Stand()
 16 | fruit.forEach(stand)
 17 | 
```

**PowerShell context, 1:36:00.** This is scrollback, not a claim that the commands were entered precisely at this frame. Earlier untracked paths shown: `app/`, `pylintrc`, `src/pyproject.toml`, `test/clearTest.py`. The `with:` lines belong to the *older* `forEach` callback run. The next checkout introduces the Python iterator; its visible first run below is `forloop.py`, not `fruit.py`.

```text
PS C:\CSCS1240F26\Item> git restore .\src\item.py
PS C:\CSCS1240F26\Item> git restore .\test\fruit.py
PS C:\CSCS1240F26\Item> git checkout master~8
Previous HEAD position was a5c546d Item: extract( ). Cannot extract last value, but can check return for empty
HEAD is now at ed154df Item: forEach( ), withItem( ) callback
PS C:\CSCS1240F26\Item> py .\test\fruit.py
with: acai
with: apple
with: banana
with: mango
with: peach
PS C:\CSCS1240F26\Item> git checkout master~7
Previous HEAD position was ed154df Item: forEach( ), withItem( ) callback
HEAD is now at ec4fdd0 Item: python Iterator
```

### 6 · Iterator checkout and ordinary list experiments

**Separate scratch buffer, 1:35:00.** Not the linked-list driver, and not the finished saved program. The second line is still being typed; the spoken number sequence is not what the pixels show.

```text
  1 | a = [2,4,5,7,]
  2 | pr
```

**`test/forloop.py`, separate states.** Each observed launch used `py .\test\forloop.py` at `PS C:\CSCS1240F26\Item>`. In these transcriptions the list literal has a trailing comma; the list *display* does not. Do not pair an older scrollback result with a newer editor tab.

**1:36:00–1:37:00: actual elements.**

```text
  1 | a = [2,4,5,7,]
  2 | print(a)
  3 | for _ in a:
  4 |     print(_)
  5 | 
```

Visible completed output by 1:36:00. At 1:38:00, there are two executions with this output in scrollback; the instructor says he ran before saving.

```text
[2, 4, 5, 7]
2
4
5
7
```

**1:38:00: range values.**

```text
  1 | a = [2,4,5,7,]
  2 | print(a)
  3 | for _ in range(4):
  4 |     print(_)
  5 | 
```

At 1:38:00 this is the newest result, not the earlier values.

```text
[2, 4, 5, 7]
0
1
2
3
```

**1:38:52 (event): indices used to read the list.**

```text
  1 | a = [2,4,5,7,]
  2 | print(a)
  3 | for _ in range(4):
  4 |     print(a[_])
  5 | 
```

The successful new output is visible by 1:39:00. The editor already says range(5) at 1:39:00; the new failing invocation appears at the 1:39:07 event.

```text
[2, 4, 5, 7]
2
4
5
7
```

**1:39:00 editor, 1:39:07 event result: index too large.**

```text
  1 | a = [2,4,5,7,]
  2 | print(a)
  3 | for _ in range(5):
  4 |     print(a[_])
  5 | 
```

The list and its four values were printed BEFORE the traceback.

```text
[2, 4, 5, 7]
2
4
5
7
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\forloop.py", line 4, in <module>
    print(a[_])
          ~^^^
IndexError: list index out of range
```

**`src/item.py` at the Python-iterator checkpoint: largest contiguous section in these frames.** Lines 82–111 from event 1:38:52; 106–134 from 1:40:00. Both views show the same 106–111; 1:41:00 and 1:46:00 corroborate the iterator text. Assembly is supported by overlap and no intervening displayed change to these lines, not a claim about unseen edits. Line 82 begins inside an earlier insertion branch; the method declaration is above the viewport. Do not infer lines 1–81 from this fragment. Event 1:33:32 independently shows the overlapping insertion and extraction lines 82–111. The callback and iterator methods coexist.

```text
 82 |         else:
 83 |             newItem = Item(obj)
 84 |             if self._prev != None:
 85 |                 newItem._next = self
 86 |                 newItem._prev = self._prev
 87 |                 self._prev._next = newItem
 88 |                 self._prev = newItem
 89 |             else:
 90 |                 newItem._next = self
 91 |                 self._prev = newItem
 92 |         return newItem
 93 | 
 94 |     def extract(self):
 95 |         rest = None
 96 |         if self._prev == None and self._next == None:
 97 |             rest = Item( )
 98 |         else:
 99 |             if self._prev == None:
100 |                 self._next._prev = None
101 |                 rest = self._next
102 |             else:
103 |                 if self._next == None:
104 |                     self._prev._next = None
105 |                     rest = self._prev
106 |                 else:
107 |                     self._prev._next = self._next
108 |                     self._next._prev = self._prev
109 |                     rest = self._prev
110 |         self._prev = None
111 |         self._next = None
112 |         return rest
113 | 
114 |     def forEach(self, caller):
115 |         iter = self.first( )
116 |         while True:
117 |             caller.withItem(iter._object)
118 |             if iter._next == None:
119 |                 break
120 |             iter = iter._next
121 | 
122 |     def __iter__(self):
123 |         head = self.first( )
124 |         head._iter = head
125 |         return head._iter
126 | 
127 |     def __next__(self):
128 |         head = self.first( )
129 |         if head._iter == None:
130 |             raise StopIteration
131 |         target = head._iter._object
132 |         head._iter = head._iter._next
133 |         return target
134 | 
```

**`test/fruit.py`, 1:41:00; same driver confirmed at 1:48:00.** The tab has a modification dot. The code is visible; no invocation of this version or matching unprefixed classroom output is visible in the saved captures. The iterator version prints bare payloads, unlike the older `with:` callback.

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
 10 | for x in fruit:
 11 |     print( x )
 12 | 
 13 | 
```

**1:59:00, `test/forloop.py`, intermediate editor buffer (modification dot).** Four-index traversal has been restored; the scalar case is appended. No run of this combined buffer is established.

```text
  1 | a = [2,4,5,7,]
  2 | print(a)
  3 | for _ in range(4):
  4 |     print(a[_])
  5 | 
  6 | b = 7
  7 | for _ in b:
  8 |     print(_)
  9 | 
```

**1:59:49 event: only the `b` example remains.** This time the script fails on line 2 before the body runs. The earlier line-4 `IndexError` is still above it in scrollback and is not caused by this version.

```text
  1 | b = 7
  2 | for _ in b:
  3 |     print(_)
  4 | 
```

```text
PS C:\CSCS1240F26\Item> py .\test\forloop.py
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\forloop.py", line 2, in <module>
    for _ in b:
             ^
TypeError: 'int' object is not iterable
```

**2:00:56 event, one-element list:**

```text
  1 | b = [7]
  2 | for _ in b:
  3 |     print(_)
  4 | 
```

```text
PS C:\CSCS1240F26\Item> py .\test\forloop.py
7
```

**Interactive session.** `02-02-00.png` shows bare `py` after the script run, followed by the Python 3.14.7 banner and assignments; there is no script argument on the REPL launch. REPL rows have no editor gutter. In the sequence below `[I]` and `[L]` are editorial keys for the exact attribute-name outputs printed below; they were not printed literally. Empty idle prompts omitted. The 2:06:00 frame has unfinished `print` after the continuation prompt; the 2:06:26 event shows what was actually submitted. Between the two, the body has changed. The interactive display of `dir(x)` is not `print(x)` and does not print a single plain `7`.

```text
PS C:\CSCS1240F26\Item> py
Python 3.14.7 (tags/v3.14.7:823f032, Aug  5 2026, 10:51:32) [MSC v.1944 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> a = 7
>>> b = [7]
>>> dir(a)
[I]
>>> dir(b)
[L]
>>> dir(a[0])
Traceback (most recent call last):
  File "<python-input-7>", line 1, in <module>
    dir(a[0])
        ~^^^
TypeError: 'int' object is not subscriptable
>>> dir(b[0])
[I]
>>> dir(list)
[L]
>>> for x in b:
...     dir(x)
...
[I]
```

**[I]** (2:02:00, 2:05:00 and 2:06:26 event; reflowed between names for reading).

```text
['__abs__', '__add__', '__and__', '__bool__', '__ceil__', '__class__', '__delattr__',
'__dir__', '__divmod__', '__doc__', '__eq__', '__float__', '__floor__', '__floordiv__',
'__format__', '__ge__', '__getattribute__', '__getnewargs__', '__getstate__', '__gt__',
'__hash__', '__index__', '__init__', '__init_subclass__', '__int__', '__invert__',
'__le__', '__lshift__', '__lt__', '__mod__', '__mul__', '__ne__', '__neg__', '__new__',
'__or__', '__pos__', '__pow__', '__radd__', '__rand__', '__rdivmod__', '__reduce__',
'__reduce_ex__', '__repr__', '__rfloordiv__', '__rlshift__', '__rmod__', '__rmul__',
'__ror__', '__round__', '__rpow__', '__rrshift__', '__rshift__', '__rsub__',
'__rtruediv__', '__rxor__', '__setattr__', '__sizeof__', '__str__', '__sub__',
'__subclasshook__', '__truediv__', '__trunc__', '__xor__', 'as_integer_ratio',
'bit_count', 'bit_length', 'conjugate', 'denominator', 'from_bytes', 'imag',
'is_integer', 'numerator', 'real', 'to_bytes']
```

**[L]** (2:02:39 event and 2:05:00; reflowed between names for reading).

```text
['__add__', '__class__', '__class_getitem__', '__contains__', '__delattr__',
'__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__',
'__getattribute__', '__getitem__', '__getstate__', '__gt__', '__hash__', '__iadd__',
'__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__',
'__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__',
'__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__',
'__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert',
'pop', 'remove', 'reverse', 'sort']
```

## Removable appendix · Micro-Learning Insights

- **Track the last assignment, not the last method mentioned.** After `fruit = fruit.last()`, underline **peach**. `fruit.prepend("acai")` changes who comes first, not what `fruit` names. *(0:17:00–0:21:00)*
- **Read a dotted write from the object outward.** In `self._next._prev = None`, put your finger on `acai`, move to apple, then erase **apple’s backward arrow**. Only the final attribute is assigned. *(0:29:00–0:31:00)*
- **Keep two handles before making the cut.** `iter` keeps the extracted node. `rest` keeps an entry into the other chain. Neither handle promises to point at the first remaining node. *(0:34:00–0:37; 0:45:00–0:47:00)*
- **Test reverse travel after a middle extraction.** Apple reaching mango proves only half the repair. Mango must also reach apple without passing through banana. *(0:50:00–0:54:00)*
- **Test one call beyond the obvious edge.** Removing the last fruit tests singleton behavior. Extracting once more tests the *type* of the empty result: another `Item` can answer; `None` cannot. *(0:58:00–1:09:00)*
- **An editor tab does not explain old stdout.** Identify the launched file, the imported module, the saved version and the placement of `print` before attributing an output line. A `__main__` guard is a small experiment that separates direct launch from import. *(1:13:00–1:21:00)*
- **A callback separates visiting from acting.** To change what happens to a fruit, look at `withItem`; to change the order in which fruit arrives, look at `forEach`. *(1:23:00–1:32:00)*
- **Ask what the loop variable contains.** In `for _ in a`, `_` is just a name. It receives fruit or numbers when iterating over those values, but receives indexes when iterating over `range(4)`. *(1:35:00–1:39:00)*
- **Separate the fixed owner from its moving field.** Three answers can all be true: `fruit` names peach, the iterator is the `acai` node, and its next payload is `"banana"`. They describe three different roles. *(1:40:00–1:57:00)*
- **Save, advance, return.** Save the current payload, advance the cursor, then return the saved value. Reversing the first two operations skips the opening fruit; returning `None` on exhaustion is not the same as raising `StopIteration`. *(1:52:00–1:57:00)*
- **Inspect the object at the right level.** For `b = [7]`, ask `dir(b)` about the list and `dir(b[0])` about the integer. The number of attributes is less informative than which operation an object actually supports. *(2:01:00–2:06:00)*
