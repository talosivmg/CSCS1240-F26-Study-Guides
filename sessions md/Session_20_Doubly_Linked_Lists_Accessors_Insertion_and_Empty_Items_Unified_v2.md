# Following the links: how one `Item` represents a position in a list

**CSCS1240F26 · Session 20 · Worked teaching companion**

If the list contains apple, banana, and peach, what should **`fruit.prev()`** give you? There is no single answer until you know which item **`fruit`** names and which revision of **`prev()`** is running. That question drives the class. First the instructor follows references by hand, then changes how the ends behave, splices a new fruit between two existing ones, and finally asks how a list with no fruit can be represented at all.

**Study route.** Start with the raw links and getters (0:00:00), trace the extra `banana` and the revised stopping test (0:26:00), splice kiwi between its neighbors (0:55:00), then test the empty list (1:25:00). The syntax reference and knowledge check precede the numbered source listings.

**Recording and evidence.** [Session 20 on YouTube](https://www.youtube.com/watch?v=LIxPVYtUER4&t=2s). The time anchors below are elapsed from the beginning, not from the link’s two-second playback position. The saved English auto-caption track has 2,115 cues, from the opening greeting to the sign-off. The last cue starts at 1:50:24.480; its nominal end is 1:50:28.760, slightly beyond the previously probed video duration of 1:50:27.683. Intervals without a cue are not proof of silence. All 111 whole-minute screenshot targets, three event targets and one near-ending target are represented by decodable full-HD originals. These are samples, not a continuous record of typing, saving or running code. Code spelling comes from the screen. Classroom outputs, predicted results and separate tests are identified where they occur.

**How to use this guide.** Draw a box for each fruit. Put the name `fruit` outside the boxes and draw one arrow from the name to the box it refers to. Update that arrow only on an assignment to `fruit`. This keeps three different operations apart: changing a link, returning an item, and changing which item a name refers to. For the exact spellings and intermediate editor states, use the historical appendix; its line numbers are not code to type.

## Building the chain and hiding its links

### Put peach after banana, even when you start at apple (0:00:00–0:04:00)

After the previous discussion of sorting, the instructor returned to a doubly linked list. Each fruit is a separate **node**: an object that stores a fruit name and links to its two neighbors. Suppose apple already leads to banana. Asking apple to append peach must not make apple point directly to peach and lose the route to banana. Nor should peach point back to apple just because apple received the request. Find the actual last node, then connect that node and the new one in both directions.

**Annotated teaching version — guide-added comments.** The first revision uses attributes directly (editor, 0:01:00–0:03:00). The class defines the methods before the driver calls them; the body of **`last()`** runs when **`append()`** calls it, not when Python first reads its definition.

```python
class Item:                              # One node, not a separate container for all the fruit.
    object = None                        # Default data until the constructor supplies a value.
    next = None                          # No following node until a link is assigned.
    prev = None                          # No preceding node until a link is assigned.

    def __init__(self, obj = None):       # Item("apple") receives the string "apple".
        self.object = obj                # Store that string on this particular node.

    def append(self, obj):               # self can be apple even when banana is last.
        last = self.last()               # For the second append, walk from apple to banana.
        newItem = Item(obj)              # Create a separate node containing "peach".
        newItem.prev = last              # Peach points back to banana, not to apple.
        last.next = newItem              # Banana now points forward to peach.
        return newItem                   # Give the caller the peach node; do not print it.

    def last(self):                      # Start wherever this method was called.
        iter = self                      # Local name for the node being examined.
        while True:                      # Keep looking until a node has no next link.
            if iter.next == None:        # At banana before peach is appended? Yes.
                break                    # Leave the loop without stepping onto None.
            iter = iter.next             # At apple with banana ahead? Advance to banana.
        return iter                      # Return the last real node, not its fruit string.
```

The driver uses the returned node differently from the class itself: it ignores it. **`fruit`** therefore keeps referring to the original apple. A second name, **`iter`**, starts at the same apple and changes as the driver walks; this is a manually moved reference, not a call to Python's iterator protocol.

**Annotated teaching version — guide-added comments** (driver, 0:03:00).

```python
from item import Item          # Bring the node class into fruit.py.
fruit = Item("apple")          # Create apple and keep a reference to it.
fruit.append("banana")         # Link a new banana after apple; ignore the return.
fruit.append("peach")          # Link a new peach after banana; fruit still names apple.
iter = fruit                   # Both names refer to apple; no node is copied.
print( iter.object )           # Display apple's stored string.
iter = iter.next               # Only iter moves: apple → banana.
print( iter.object )           # Display banana.
iter = iter.next               # Banana → peach.
print( iter.object )           # Display peach.
iter = iter.prev               # Peach → banana, testing the back link.
print( iter.object )           # Display banana again.
iter = iter.prev               # Banana → apple.
print( iter.object )           # Display apple again.
```

**Observed terminal output** after **`py .\test\fruit.py`**, visible at 0:04:00:

```text
apple
banana
peach
banana
apple
```

The backward trip tests something the forward trip cannot: peach was linked back to the *right* node. Earlier scrollback in that frame shows another, unrelated failure: line 1, **`from item import Item`**, produced **`ModuleNotFoundError: No module named 'item'`**. After **`.\script\path.ps1`**, the next launch printed the five names. That sequence points to module discovery, not a broken link; the script's contents are not shown. Later entries containing just the paths to the source or driver are distinct from the displayed Python launch.

**Key items to scan**

- **Node / `Item("apple")`** — one new object containing the string `apple`; neighbors are initially absent.
- **`last()`** — follow successive links until the current node, not the next one, is the end.
- **`while True` / `break`** — keep walking, but stop before moving past the last real node.
- **`append()`** — creates one new node and mutates the two links that join it to the old tail.
- **`newItem.prev = last`** — makes peach lead back to banana; **`last.next = newItem`** makes banana lead forward to peach.
- **`fruit`** — remains apple because neither append result is assigned to it. **`iter`** moves without rebinding `fruit`.
- **`py .\test\fruit.py`** — executes the driver; the five printed lines test both directions.
- **`from item import Item` / `ModuleNotFoundError: No module named 'item'`** — an earlier launch could not import the class. **`.\script\path.ps1`** precedes the successful retry.

### Put the links behind methods (0:04:00–0:09:00)

The instructor stepped from **`master~15`** (commit **`6cdb33d`**) to **`master~14`** (**`7653923`**). Fourteen revisions back is one revision *nearer* the tip than fifteen. The checkout placed Git's current position, HEAD, on a historical commit rather than a branch tip; the editor picked up the different files. The new version answers a design question: if any caller can overwrite banana's next link, who keeps the forward and backward links consistent?

The stored names now begin with one underscore; the public methods keep the short names. **`_next`** means “this class manages the link,” not “Python prevents you from touching it.” In the original code, access meant both reading and assigning an attribute. Here a client can ask what follows a node without being invited to rewrite its link. The instructor dislikes unnecessary underscores but chose them to communicate that boundary.

**Annotated teaching version — guide-added comments.** Selected class members from 0:05:00–0:08:00; these are methods and fields inside `Item`, not a second class to paste after the first.

```python
_object = None                     # Default stored value; a constructor call sets the node's data.
_next = None                       # Private by convention: following node, if present.
_prev = None                       # Private by convention: preceding node, if present.

def __init__(self, obj = None):     # Item("apple") receives the new node and its fruit value.
    self._object = obj             # Put "apple" in this node's internal field.

def object(self):                  # Ask this node what it contains.
    return self._object            # Return its stored value; printing is the caller's job.

def next(self):                    # Ask for the following node.
    return self._next              # From apple, return banana; from peach, return None here.

def prev(self):                    # Ask for the preceding node.
    return self._prev              # From peach, return banana; from apple, return None here.
```

These methods are **getters**: they read stored values. A **setter** would accept a proposed change and could validate or reject it; no general-purpose setters are shown here. The class still writes its own internal links inside append and reads them inside last. Choosing which operations to expose is the design decision, not the punctuation alone. The instructor mentioned public, protected and private access in other designs, and an unnamed PEP (Python Enhancement Proposal); no specific PEP number was given.

The getter does not replace the class's own link maintenance. Here is the changed part of the append operation: the same two nodes must still agree about their connection, but the writes now go to the internal fields.

**Annotated teaching version — guide-added comments** (inside `append`, 0:05:00).

```python
last = self.last()           # Find the tail, banana when adding peach to apple → banana.
newItem = Item(obj)          # Create the separate peach node; do not change banana's data.
newItem._prev = last         # Set peach's stored predecessor to banana.
last._next = newItem         # Set banana's stored successor to peach.
return newItem               # The caller can now choose to keep a reference to peach.
```

The driver also changed in a way that matters independently of privacy. It now assigns each result of append back to **`fruit`**. That name ends at peach, not apple. To walk the same sequence as before, the driver first goes back twice.

**Annotated teaching version — guide-added comments** (0:05:00, 0:08:00).

```python
fruit = Item("apple")                # New apple; do not carry over the previous run's objects.
fruit = fruit.append("banana")       # Add banana and rebind fruit to the returned banana node.
fruit = fruit.append("peach")        # Add peach and rebind fruit again, this time to peach.
iter = fruit.prev().prev()           # Peach → banana → apple; fruit itself stays on peach.
#iter = fruit                        # Inactive alternative; would start at peach in this driver.
print( iter.object() )               # Getter returns "apple"; print displays apple.
iter = iter.next()                   # Call the getter on apple; move iter to banana.
print( iter.object() )               # Display banana.
iter = iter.next()                   # Move iter to peach.
print( iter.object() )               # Display peach.
iter = iter.prev()                   # Move iter back to banana.
print( iter.object() )               # Display banana.
iter = iter.prev()                   # Move iter back to apple.
print( iter.object() )               # Display apple.
```

A student first noticed the **`#`** on line 9. Correct: it makes the alternative a comment and Python does not execute it. The instructor then pointed to active line 8. Another student had noticed the **`()`** but thought it was an optional coding choice. With this class it is not optional if the caller wants the *previous node*. The next experiment shows why.

**Key items to scan**

- **`master~15` / `6cdb33d`** — revision with direct public fields. **`master~14` / `7653923`** — next displayed revision with accessor methods.
- **`_next`**, **`_prev`**, **`_object`** — internal following link, preceding link and stored value. The leading underscore signals intended use, not enforced privacy.
- **Getters** — `object()`, `next()` and `prev()` read those three fields. A **setter** would control a write; it is discussed, not demonstrated as an extra method here.
- **`fruit`** — now points to peach because each append *result* is assigned back to it.
- **`fruit.prev().prev()`** — calls two getters to obtain apple from peach. **`()`** performs each call.
- **`#`** — makes `#iter = fruit` inactive; unlike the parentheses on line 8 it is not part of an executed expression.

### Read the traceback from the failing lookup (0:09:00–0:14:00)

What if the driver uses the *old spelling* with the *new class*? The instructor removed the calls on line 8. Keep the apple → banana → peach structure and the assignments that leave `fruit` at peach from the preceding example.

**Annotated teaching version — guide-added comments** (0:09:00–0:11:00).

```python
iter = fruit.prev.prev         # First lookup gets a method; the second asks that method for prev.
```

**Observed** after `py .\test\fruit.py`, line 8 (0:11:00):

```text
AttributeError: 'function' object has no attribute 'prev'
```

Python successfully finds **`fruit.prev`**: it is the bound method, an operation tied to that particular node. Without a call, no previous node comes back. The second dot therefore searches the *operation*, not banana, for a member called **`prev`**. The error happens before `iter` receives a value or any fruit is printed. The spoken exchange refers to apple at one point; the displayed assignments put `fruit` at peach. Either node would have the same distinction between looking up and calling the method.

A student asked whether using the underscored name would fix it, and whether everything else would have to change. The instructor said direct access could work but defeated the point: the other lines already call methods which read the fields. The following alternatives illustrate the distinction separately; they are not consecutive statements in the failed run.

**Annotated teaching version — guide-added comments.** First line is a reconstruction of the proposed direct-field bypass, not a recovered edit; second line is the observed public form at 0:12:00.

```python
iter = fruit._prev._prev        # Bypass both getters: peach's raw link → banana's raw link → apple.
iter = fruit.prev().prev()      # Ask each node instead: same apple, but through the class interface.
```

For either alternative, the remaining driver prints apple, banana, peach, banana, apple in an independent check. Two such sequences are also visible in later classroom scrollback, but that view does not establish which line 8 ran for each one. The practical rule from the discussion is to let the class be the only code that directly uses its underscored fields; otherwise a future change to how the class stores a link can break its clients.

**Key items to scan**

- **`fruit.prev`** — retrieves the method itself; it does not follow a link. **`prev`** on that method is the missing attribute in the failed second lookup.
- **`fruit.prev.prev` / `AttributeError`** — the missing call changes what receives the second dot; Python stops on line 8.
- **`fruit._prev._prev`** — can reach apple from peach but couples the driver to the representation.
- **`fruit.prev().prev()`** — public calls return banana, then apple; later lines need no private-field changes.

### What should happen when there is no neighboring node? (0:14:00–0:22:00)

The next revision, described in class as “avoid returning None,” brings back the driver that appends **without** rebinding `fruit`. It shows two different answers to “what follows peach?” Inside the class the *stored link* is still **`None`**. Through its revised **`next()`** method peach now returns *peach itself*. The two answers are deliberate: one records the structure; the other defines what a caller gets at the boundary. The same change applies at apple's left edge in **`prev()`**.

**Annotated teaching version — guide-added comments** (0:15:00, 0:22:00, class excerpts).

```python
def next(self):                      # Ask this node to take one step forward.
    if self._next != None:           # There is an actual neighbor after apple or banana.
        return self._next            # Return that neighbor, e.g. banana after apple.
    return self                      # Peach has no next neighbor; return this same peach node.

def prev(self):                      # Same question in the other direction.
    if self._prev != None:           # Banana and peach have a real preceding node.
        return self._prev            # From banana, return apple.
    return self                      # From apple, stay at apple rather than hand back None.
```

The driver must honor the new stopping rule. Printing the current fruit *before* asking whether to stop is what includes peach exactly once. The implementation of `last()` can still stop on the raw link because it is inside the class.

**Annotated teaching version — guide-added comments** (driver, 0:15:00).

```python
from item import Item           # Use the revision whose boundary getters return self.
fruit = Item("apple")           # Create a new first node, apple.
fruit.append("banana")          # Add banana without moving fruit.
fruit.append("peach")           # Add peach after banana; fruit still names apple.
iter = fruit                    # Begin at apple.
while True:                     # The explicit check below decides when to leave.
    print( iter.object() )      # Print the current node's string, including the last one.
    if iter.next() == iter:     # Does asking for the next node leave us at this node?
        break                   # Yes at peach: stop before printing peach a second time.
    iter = iter.next()          # Otherwise move to the next real node.
```

**Trace derived from the displayed revision; output separately reproduced, not identified as a classroom run of this loop.**

| Current node | Printed | Getter in the test returns | Next action |
|---|---|---|---|
| apple | `apple` | banana, different node | Ask again and move to banana. |
| banana | `banana` | peach, different node | Ask again and move to peach. |
| peach | `peach` | peach, same node | Break; no fourth pass. |

The class paused over the value that made this guard necessary. A student described **`None`** as “nothing there,” not a choice between true and false. The instructor pressed further: it is an actual predefined value of type **`NoneType`**, and its spelling is a Python keyword; the program does not define it. Thinking about where a value lives prompted answers of memory and address. The instructor used the address of a first byte and the question of how many bytes a value occupies to bring out the missing idea: a value has a *type*. The class did not measure an address or byte count. Like `True` and `False`, `None` has a predefined spelling; it is not another name for a Boolean.

What is the next attribute *of None*? A student wondered whether Python might find the value being sought or test a true/false condition. Instead, try the precise lookup. This driver still leaves `fruit` at apple. The newly inserted lines run *before* the loop.

**Annotated teaching version — guide-added comments** (0:20:00–0:21:00, edited driver).

```python
x = fruit._prev       # Bypass the new getter. Apple has no stored predecessor: x is None.
print(x._object)      # Look up _object before print runs: None is not a fruit node.
```

**Observed:** the terminal shows a traceback at `print(x._object)` and **`AttributeError`**; the rest of the message is obscured by the editor. **Independently reproduced:** `AttributeError: 'NoneType' object has no attribute '_object'`. Nothing is printed by that line, and the loop below it is never reached. The instructor's shorthand that None “has no attributes” should be read in context: it has *none of these fruit-node fields or methods*. The lesson's caution about returning None concerns clients that assume a node, not a rule that Python methods must never return it.

A student suggested giving the object an identifier, or some non-None value. The relevant decision is what the method promises to return at the boundary. This implementation can give back a perfectly good node it already has—`self`—without inventing a fruit, changing a raw link or connecting the end to the beginning. Reading the field directly bypasses that promise.

**Key items to scan**

- **`None` / `NoneType`** — the missing raw link is a real value with a type, not a node carrying a fruit string.
- **`next()` / `prev()`** — in this revision only, return a neighbor if present, otherwise the very node on which they were called.
- **`return self`** — at peach's right edge or apple's left edge the public operation stays put; it does not write a self-link.
- **`if iter.next() == iter:`** — tests that staying-put result. **`break`** ends the loop after peach has been printed once.
- **`x = fruit._prev`** — bypasses the method and obtains `None` from apple's raw field.
- **`print(x._object)` / `AttributeError`** — lookup fails while evaluating the argument, before the print or following loop.

### One dot is not one call (0:22:00–0:25:59)

To ask what the *earlier*, unguarded getters do, the instructor returned to the revision fourteen steps back. The first checkout attempt was aborted because of local edits to `test/fruit.py`; it did not switch versions. The instructor described restoring the changed driver and reloading. The subsequent screen again shows simple getters and a driver that assigns each append result back to `fruit`. Its old traceback remains in terminal scrollback; it is not output from the new probe.

**Annotated teaching version — guide-added comments** (0:24:00–0:25:00, top of reloaded and then edited driver).

```python
fruit = Item("apple")              # Line 3 creates apple and names it fruit.
fruit = fruit.append("banana")     # Line 4 now makes fruit refer to banana.
fruit = fruit.append("peach")      # Line 5 now makes fruit refer to peach.
x = fruit.prev()                   # Look up prev on peach, call it, bind x to banana.
print(x.object())                  # Ask banana for its data, then display the string banana.
```

The teacher asked the class to evaluate the right-hand side before assigning to **`x`**. An address or ID was the classroom shorthand for a reference to a particular object, not a fruit name or an instruction to calculate an address. A student correctly tracked the assignments to peach and predicted banana; another proposed None. The instructor's “become Python” question was narrower: what is **`fruit.prev`** *before the parentheses are applied*? It is a method on the node, neither banana nor None. Only **`fruit.prev()`** returns a node here. In the displayed state that node is banana; the initial apple still exists but is no longer what `fruit` names. The first print is independently reproduced as `banana`; the visible terminal in these frames does not establish a new classroom run.

**Key items to scan**

- **`test/fruit.py`** — its local changes blocked the first checkout; a later successful switch is a separate event.
- **`fruit.prev`** — dot lookup identifies an operation tied to peach. **`fruit.prev()`** — the parentheses invoke it and return banana.
- **`x`** — receives the banana node only after the right-hand expression finishes. **`x.object()`** returns its string; outer `print` displays it.

## Tracing the caller: which item is “self”?

### 0:26:00–0:32:00 · Which fruit is asking for its previous neighbor?

At line 7 the question is not “What comes before fruit in the list?” It is “Which particular item does **`fruit`** name *right now*, and what is on that item's left?” The dot in **`fruit.prev()`** first looks up a method; the parentheses call it. The professor recalls that functions, too, are objects with identity. Looking up the method is not yet asking it for a result. The name **`self`** inside the called method refers to the item on the left of the dot. It doesn't mean “repeat,” as a student suggests, and it doesn't always mean apple. That distinction matters when several items use exactly the same method definition.

The simple getters have not changed since the earlier checkout: `prev()` returns the stored link without a guard. The new question is how Python supplies `self` and why the class’s prediction of `None` disagrees with the output.

In the driver shown at 0:26:00–0:32:00, the first append **changes apple's link** *and* the assignment **changes which node the name refers to**. The second append does the same from banana. Constructing and linking nodes does not display any of their strings.

```python
from item import Item                 # Import the course's node class.
fruit = Item("apple")                  # Make the apple node; fruit starts there.
fruit = fruit.append("banana")         # Link a new banana, then rebind fruit to banana.
fruit = fruit.append("peach")          # Link a new peach, then rebind fruit to peach.
x = fruit.prev()                      # Peach's left neighbor is the banana item.
print(x.object())                     # Ask banana for its string, then print: banana.
```

The class initially predicted **`None`**, followed by an error. That prediction makes sense *if apple is still the receiver*: its previous link is absent, **`x`** would receive **`None`**, and Python would fail to find an **`object()`** method on it before the outer print could run. But the displayed assignments make peach the receiver. A student noticed that the real output began with **`banana`**. The way to resolve the contradiction was to find the exact line that printed it, then track the name backward to its most recent assignment. A later terminal view (0:33:00) does separately show an **`AttributeError`** at the attempt to print the result of `x.object()`; the editor in that frame already shows both assignments again. Do not treat that error as the outcome of the displayed two-assignment program. A separate teaching check with apple as receiver produced `AttributeError: 'NoneType' object has no attribute 'object'` (not a transcription of the obscured classroom message).

**Key items to scan**

- **`fruit`** — names apple, then banana, then peach *only* if the caller assigns both append results back to it.
- **`self`** — the particular item receiving this call; peach for the successful `prev()` call, banana for the following `object()` call.
- **`fruit.prev()`** — calls a method; here its return value is a reference to banana, not the word `banana`.
- **`object()`** — returns the stored value; `print(x.object())` is what displays **`banana`**.
- **`append()`** — both connects the new node and returns it; the caller can ignore the return without undoing the links.
- **`last()`** — follows raw `_next` links until the first missing one, then returns that node.
- **`x`** — holds the lookup result; if it holds **`None`**, another method call on it fails with **`AttributeError`**.

### 0:32:00–0:37:00 · Why is there a banana before the apple–banana–peach walk?

A student expected the output to begin with apple because the list was *built* in apple–banana–peach order. But the construction lines are not print lines. The driver executes straight down until a statement changes control flow; the calls do their own work and return before the next driver line. After line 8 prints banana, a *different name* is positioned at the beginning by walking back twice from peach. Now, and only now, the walk starts. In this driver **`iter`** is an ordinary local name for the current item, not a call to Python's iteration machinery.

**Annotated teaching version — guide-added comments.** Continuation of the initial driver above (0:26:00 and 0:31:00); `fruit` remains at peach throughout.

```python
iter = fruit.prev().prev()    # Peach → banana → apple; start a separate walk here.
#iter = fruit                 # Commented alternative; it does not move the starting point.
print( iter.object() )        # Print apple.
iter = iter.next()            # Move the local name from apple to banana.
print( iter.object() )        # Print banana.
iter = iter.next()            # Move from banana to peach.
print( iter.object() )        # Print peach.
iter = iter.prev()            # Turn around: peach → banana.
print( iter.object() )        # Print banana.
iter = iter.prev()            # Move from banana to apple.
print( iter.object() )        # Print apple.
```

| Phase | Name/node before | Result of the operation | What appears |
|---|---|---|---|
| Build | `fruit`: apple | Two appends leave it naming peach | Nothing |
| Probe | `fruit`: peach | `x` becomes banana; its stored string is returned | `banana` |
| Position the walk | `fruit`: peach | First `prev()` returns banana; the second, called on banana, returns apple | Nothing |
| Forward visits | `iter`: apple | Print apple, advance and print banana, advance and print peach | `apple`, `banana`, `peach`, on separate lines |
| Backward visits | `iter`: peach | Retreat and print banana, retreat and print apple | `banana`, `apple`, on separate lines |

At 0:31:00 the terminal shows the six-line sequence twice. By 0:36:00 the driver has an additional **`print()`** immediately after the standalone banana. The corresponding visible output separates the probe from the walk:

```text
banana

apple
banana
peach
banana
apple
```

This was also a debugging lesson: the writer can see the sequence they intended instead of the one they wrote. The professor connects that to **pair programming**, an agile practice in which two developers work together. Here a second reader's “why banana first?” was more informative than simply running the program again.

**Key items to scan**

- **`iter`** — the moving name; rebinding it leaves the list and `fruit` unchanged.
- **`fruit.prev().prev()`** — call the second getter on the *result* of the first; two steps take peach back to apple.
- **`#iter = fruit`** — inactive alternative, not a subsequent reset to peach.
- **`print()`** — the added no-argument call inserts a blank line; it neither visits nor creates a node.
- **Pair programming** — a second reader can question an assumption about a print or assignment that the author overlooks.

### 0:37:00–0:43:00 · What should a neighbor getter do at the edge?

Moving access to links behind methods did not, by itself, prevent the caller from getting **`None`**. Ask for the last item's next neighbor and the simple getter returns whatever is stored there. The class considered three approaches: create a special end-marker object; return a value such as **`False`** (a student also suggested zero); or return the last item itself. The professor points out that one cannot assign a new meaning to **`None`** by writing an assignment to it. A separate end-marker would have to be something the program creates. A Boolean can signal failure, but then callers sometimes receive an item and sometimes a Boolean. Consistency was the professor's design preference, not a Python requirement.

After restoring the teaching files and checking out **`master~13`**, the instructor asks the class to spot what changed in **`item.py`**, rather than just notice that the long walk disappeared from **`fruit.py`**. Git, editors and command-line tools can show a **diff** (a comparison of versions); this was a suggestion, not an observed diff run. At 0:42:00 the selected driver starts a loop at apple, and the class methods show the new contract.

**Annotated teaching version — guide-added comments.** `Item` members at 0:42:00–0:43:00:

```python
    def next(self):                  # Ask for the item to the right.
        if self._next != None:       # Does a real right neighbor exist?
            return self._next        # From apple, return banana and leave the method.
        return self                  # From peach, stay on the same peach item.

    def prev(self):                  # Ask for the item to the left.
        if self._prev != None:       # Does a real left neighbor exist?
            return self._prev        # From peach, return banana.
        return self                  # From apple, return that same apple item.
```

Notice what did *not* happen: no code here changes a terminal link to point to itself. The private link remains absent; the *public method* substitutes its own receiver on return. That makes it safe, for these normally linked items, to ask the returned item for its fruit string even at the boundary. It also changes how a client must recognize the end.

**Key items to scan**

- **`None`** — the stored absence of a neighbor; still used internally in the revised implementation.
- **`False`** — proposed alternative that would mix Boolean and item returns; zero and a special end-marker object were discussed, not implemented here.
- **`return self`** — return the existing boundary item, not a new copy and not a newly installed link.
- **`master~13`** — the demonstrated revision with the “avoid returning None” change.
- **`item.py`** — contains the two changed getters; **`fruit.py`** contains the client that must use their contract.
- **Diff** — a way to locate changes between files or revisions, mentioned while the class compared the methods by eye.

### 0:43:00–0:50:59 · The same loop can print three fruits or just peach

Start with the new driver at 0:42:00: append twice **without assigning the results**. The name `fruit` still refers to the first node, apple; the second append still reaches the end through `last()`. The driver initially has only the loop. At 0:44:00 the professor is composing a separate probe above it. At 0:45:00 the probe and blank-line print are complete. A prediction of peach for the probe overlooks the fact that it takes *one step from apple*, not one step from the last appended item.

**Annotated teaching version — guide-added comments.** The 0:45:00 driver:

```python
from item import Item                  # Use the revision whose neighbor getters return an Item.
fruit = Item("apple")                   # Start a new chain with apple.
fruit.append("banana")                  # Link banana; discard the returned banana node.
fruit.append("peach")                   # Link peach after banana; fruit still names apple.
print(fruit.next().object())           # Get banana's node, then its string, then print banana.
print()                                # Visibly separate this probe from the walk.

iter = fruit                           # Second name for apple; no new node is created.
while True:                            # Repeat unless the explicit break is reached.
    print( iter.object() )             # Print this fruit before testing whether it is last.
    if iter.next() == iter:            # Did next() return the current item itself?
        break                          # Yes at peach: exit before trying to advance again.
    iter = iter.next()                 # Otherwise move to the neighbor; the link isn't changed.
```

How it was launched matters. The professor says that invoking the file directly opens the editor on this setup. At 0:51:00 the history shows **`.\test\fruit.py`** followed by **`py .\test\fruit.py`**; the latter explicitly uses the Python launcher. The fruit output follows the latter command, not the filename alone.

**Observed result** (0:45:00; command and scrollback readable together at 0:51:00):

```text
banana

apple
banana
peach
```

Now change *only how the append results are used*. At 0:47:00 the two assignments are back. The list still contains the same three strings in the same order. The start of *both* the probe and the loop changes.

```python
fruit = Item("apple")                   # Fresh run: new apple, not the prior run's chain.
fruit = fruit.append("banana")          # Link banana and make fruit name it.
fruit = fruit.append("peach")           # Link peach and make fruit name it.
print(fruit.next().object())           # Peach is last; next() gives peach itself: print peach.
print()                                # Blank line.
iter = fruit                           # The unchanged loop now starts at peach.
```

**Observed result** (0:47:00; same loop):

```text
peach

peach
```

The first peach comes from the explicit probe; the second comes from the body of the loop before its first end test. This answers the student's question about why the old three-fruit output disappeared: changing one assignment can alter later code that has not changed at all. At 0:49:00 the two calls no longer assign to `fruit`; the instructor walks through the longer case again. The selected `peach` in the adjacent terminal is earlier scrollback, not evidence that the reverted driver prints peach twice.

| Starting arrangement | Pass | Current item and loop output | Result of `iter.next()` | End test and next state |
|---|---|---|---|---|
| Keep apple | 1 | apple → `apple` | banana item | banana differs; skip `break`, call `next()` to move to banana |
| Keep apple | 2 | banana → `banana` | peach item | peach differs; move to peach |
| Keep apple | 3 | peach → `peach` | same peach item | equal; break, no fourth print |
| Keep last append result | 1 | peach → `peach` | same peach item | equal immediately; break, no second loop pass |

The condition **`while True`** never becomes false. It is the **`break`**, not a change in the condition, that stops the loop. Because the print comes first, the last fruit is printed once by the loop, not skipped. The test asks whether the next item equals the current item, *not* whether the next fruit string equals the current fruit string. Under the demonstrated contract this lets the caller recognize the last node without handling **`None`**.

**Key items to scan**

- **`fruit.append("peach")`** — links peach but leaves the name at apple when the return value is discarded.
- **`fruit = fruit.append("peach")`** — links peach and changes the name to peach, so the loop starts at its endpoint.
- **`fruit.next().object()`** — get an item first, then its stored string: banana from apple; peach from terminal peach.
- **`iter = fruit`** — establishes the loop's start from the *current* value of `fruit`.
- **`.\test\fruit.py`** — filename invoked directly; the professor reports it opens the editor on this setup.
- **`py .\test\fruit.py`** — explicitly launch the driver with Python; the three later runs are visible together at 0:51:00.
- **`while True`** — unconditional repetition; no implicit list exhaustion check.
- **`iter.next() == iter`** — boundary test on two items; the getter supplies the same node at the end rather than **`None`**.
- **`break`** — exit immediately after printing the last fruit, skipping the advance.

### 0:51:00–0:54:59 · A commit message poses a design question: why insert *after*?

Before looking at the implementation, the class reads the commit subject **`Item: insertAfter( ).`** as a clue. A useful commit message should briefly say what changed. The previous class had an append operation but no `insertAfter` method in the visible code, so adding a method is a reasonable *prediction* from the message. It isn't evidence of the new method's body.

The checkout itself supplies a useful checkpoint. At 0:51:00 **`git checkout master~12`** is at the prompt with no result yet. At 0:52:00 the fuller history shows the first attempt was rejected because local changes to **`test/fruit.py`** would be overwritten. The instructor restored the two teaching files, then retried. Only after that does the terminal report the new commit. These restore commands discard working-file changes; their use here is for disposable classroom edits, not a general instruction to discard one's work.

| PowerShell action in the displayed history | Effect in this case |
|---|---|
| `git checkout master~12` | First attempt aborts; it did not switch the code. |
| `git restore .\test\fruit.py` | Reverts the local driver edits. |
| `git restore .\src\item.py` | Also restores the item implementation before retrying. |
| `git checkout master~12` | The retry succeeds: `HEAD is now at 7630f8b Item: insertAfter( ).` |

What problem would insertion solve? **`append()`** always adds a new *last* item. If the chain is apple, banana, peach and kiwi belongs *between* two existing items, adding it after peach gives the wrong order. The professor first imagines kiwi between apple and banana, then gives alphabetical order as a reason for choosing a different gap: **apple → banana → kiwi → peach**. The request concerns where the *new item* belongs among other *items*, not which line of the Python file should contain a statement. As this discussion continues, the class raises whether insertion means replacing an item; the intended operation is to keep the old items and add one. “Insert after” names which side of an existing item is meant. The code that implements it is not visible in these foreground-terminal frames.

**Key items to scan**

- **`Item: insertAfter( ).`** — commit subject used to predict the purpose of the change before reading new source.
- **`git checkout master~12`** — distinguish text typed at a prompt, a rejected attempt, and the later successful retry.
- **`test/fruit.py`** — local changes to this path explicitly block the switch; it is not evidence that every modified file blocks checkout.
- **`append()`** — makes kiwi the last item; cannot express “after banana but before peach.”
- **`insertAfter`** — names the intended position relative to an existing item; retaining the other items distinguishes insertion from replacement.

## Insertion: keep both directions connected

### 0:55:00–1:01:00 · Insert where? Name the side, not just the action

Naming the side matters even when everyone agrees to keep all the original items. The problem with calling a method simply **`insert`** is not that it might choose a random position. Even if we agree on the current item, there are still two sensible positions.

At 0:56:00 the PowerShell input line reads `23,45,768,908,336`. The instructor asks the class to look at **`768`** and insert **`12`**. These are the two proposed results, not terminal output:

| Interpretation | Result |
|---|---|
| Immediately before 768 | 23, 45, **12**, 768, 908, 336 |
| Immediately after 768 | 23, 45, 768, **12**, 908, 336 |

The instructor suspects “before” is the more common intuition but would rather name the behavior than ask a caller to guess. Editing text gives the class a familiar comparison. A student thinks of placing the cursor in a gap or backing over a character. But backspacing, overwriting a character, and inserting a character are different actions. The instructor experiments with the keyboard's Insert/Num Lock arrangement and the editor's bar versus underscore cursor. When a typed character *replaces* what was there, the instructor corrects the mode identification: that was overwrite, not insert. In the next spoken example the cursor is between `i` and `w`: inserting `x` leaves the `w` in place to its right. The test is what happens to the old character, not whether everyone has the same key or cursor shape.

**Key items to scan**

- **`insert`** — promises an addition, but alone does not say on which side of the current item it goes.
- **`768`**, **`12`** — 768 is the anchor and 12 is the new value; neither interpretation discards 768.
- **`insertAfter`** — the course method names the side explicitly; for banana, the new item comes immediately to banana's right.
- **Insert versus overwrite** — inserting shifts the old character along; overwriting replaces it. Neither requires a preliminary backspace.

### 1:01:00–1:06:59 · What can insertion borrow from append?

“After which fruit?” and “What are we adding?” have different answers in **`fruit.insertAfter("kiwi")`**. If **`fruit`** refers to the banana item, banana is where the work happens; the argument `"kiwi"` supplies what the new item stores. In the method, Python supplies banana as **`self`** and the call supplies `"kiwi"` as **`obj`**. One does not have to pass banana a second time or assign the answer to a variable named kiwi.

The constructor and getters are unchanged. In particular, the boundary getter can hide an absent neighbor by returning the current item; the splice must inspect the stored link itself.

The original **`append`** doesn't require its caller to stand at the end. From apple, it can find banana before adding peach. The instructor asks which parts to keep for insertion. Keep “make a new item” and “connect it both ways”; discard “walk to the last item,” because banana, wherever it is, is the requested insertion point.

**Annotated teaching version — guide-added comments** (tail-seeking state, 1:02:00):

```python
def last(self):                      # Find the final item reached from this one.
    iter = self                      # Start at the caller, e.g. apple.
    while True:                      # Check each actual next link.
        if iter._next == None:       # Nothing follows the current item.
            break                    # Stay on that final item.
        iter = iter._next            # Otherwise move right, e.g. apple to banana.
    return iter                      # Return the last Item itself.

def append(self, obj):               # Example: apple.append("peach").
    last = self.last()               # Banana is the tail if apple already links to banana.
    newItem = Item(obj)              # Create a NEW peach item.
    newItem._prev = last             # Peach links back to banana.
    last._next = newItem             # Banana now links forward to peach.
    return newItem                   # The call returns peach; it does not print it.
```

The avocado-only driver at 1:02:00 shows why seeking the tail matters. Both calls below use apple as their starting point, yet banana is *retained* when peach is appended. By contrast, insertion takes the place immediately after the named item.

**Annotated teaching version — guide-added comments** (earlier avocado-only driver excerpt; use the original, tail-seeking append):

```python
fruit = Item("apple")                 # fruit names a new apple throughout these calls.
fruit.append("banana")                # Apple is last, so attach banana to it.
fruit.append("peach")                 # Starting at apple, find banana and attach peach there.
#fruit = fruit.last()                 # Inactive: do not change the anchor to peach.
fruit.insertAfter("avocado")          # Insert after apple, before banana.
```

A forward walk from apple gives `apple`, `avocado`, `banana`, `peach` with these operations; the first later terminal run shows that order. At 1:00:00 the editor also has stray text on line 16 from the cursor demonstration. That scratch state is not a verified runnable copy of the driver.

The instructor edits the method **still named `append`** as a comparison. At 1:03:00 the tail lookup has gone but the two assignments still mention `last`; no execution of this intermediate edit is shown. By 1:04:00 both refer to the caller instead. This works when the caller is already the tail, but if something already follows it, one forward link will be overwritten. That difference matters in the later runs.

**Annotated teaching version — guide-added comments** (replacement body shown in the editor, not the original tail-seeking append):

```python
def append(self, obj):               # Attach immediately to this caller.
    newItem = Item(obj)              # Make an item to hold the supplied value.
    newItem._prev = self             # Make the new item's left neighbor the caller.
    self._next = newItem             # Replace the caller's right link; no old link is saved.
    return newItem                   # The caller can keep this item by assigning the return.
```

On the driver side, the instructor starts making **`fruit = fruit.append("banana")`** explicit. The append both adds banana and returns the banana item. The assignment moves the name `fruit` from apple to banana; it does not turn an apple into a banana. At 1:06:00 the visible driver then inserts kiwi *before the peach has been appended*, assigns the peach return back to `fruit`, and also contains an avocado insertion. Asked how to connect banana to kiwi, the class gets the first pair of links right. But that driver order cannot yet illustrate slipping kiwi between an existing banana and an existing peach. The instructor will change it.

**Annotated teaching version — guide-added comments** (intermediate driver, 1:06:00; no class run shown for it):

```python
fruit = Item("apple")                 # Start afresh with apple.
fruit = fruit.append("banana")        # Apple gains banana; fruit now names banana.
fruit.insertAfter("kiwi")             # No peach yet: insert kiwi at banana's end.
fruit = fruit.append("peach")         # With edited append, banana now points to peach, not kiwi.
#fruit = fruit.last()                 # Comment, not a move to the end.
fruit.insertAfter("avocado")          # fruit is peach; add avocado after peach.
```

A separate teaching run with the direct-link append and the visible insertion algorithm gives `peach`, then `avocado` when walking forward from `fruit` in this intermediate order. This is not one of the four-fruit terminal runs below.

**Key items to scan**

- **`fruit.insertAfter("kiwi")`** — `fruit` identifies the anchor; `"kiwi"` supplies the value for a new item.
- **`fruit`**, **`self`**, **`obj`** — when `fruit` names banana, banana becomes `self` inside the method and the new value becomes `obj`.
- **`Item`**, **`None`** — each new item stores its own value; a missing actual link starts as `None`.
- **`object()`**, **`next()`**, **`prev()`** — return the stored value, following item, or preceding item respectively; the neighbor methods return the caller at their respective boundaries.
- **`last()`** — follows actual next links until it finds an item with none; returns that item, not its value.
- **`append`** — the first body searches for the tail; the edited body writes directly to the caller's next link. These are different versions.
- **`fruit = fruit.append("banana")`** — saves the returned banana item, so the next operation can target it.

### 1:07:00–1:15:59 · Inserting between two neighbors: four link changes, in order

Suppose the links run both ways between banana and peach. Adding kiwi requires more than hooking kiwi to banana: going forward from kiwi must reach peach, and going backward from peach must now reach kiwi. The instructor then asks: what if there's no peach? A student predicts apple, banana, kiwi. That's the desired result, but asking a nonexistent peach to update its previous link would fail. The instructor calls such a case an **edge case**: the ordinary plan assumes something that need not be there.

The class considers a missing item on *either* side. For insertion after a known item, the item on the left of the new kiwi is supplied by the call itself: banana. Banana need not have a predecessor. The optional neighbor this implementation must check is the one on banana's right. A student asks about the printing variable; the instructor separates the tasks. First get the links right, then decide where printing starts.

**Annotated teaching version — guide-added comments** (method visible in full at 1:07:00; banana is the caller):

```python
def insertAfter(self, obj):           # Add obj directly after banana, not after the tail.
    newItem = Item(obj)               # Make a NEW item holding "kiwi".
    if self._next != None:            # Middle: banana currently has a next item, peach.
        newItem._prev = self          # Kiwi points left to banana.
        newItem._next = self._next    # Kiwi points right to the existing peach.
        self._next._prev = newItem    # Through banana's still-old link, set peach's left to kiwi.
        self._next = newItem          # Only now change banana's right link to kiwi.
    else:                             # End: banana has no following item to repair.
        newItem._prev = self          # Kiwi points back to banana.
        self._next = newItem          # Banana points to kiwi; kiwi remains the tail.
    return newItem                    # Return the kiwi Item in either case; print nothing.
```

The class asks whether the chained expression “isn't just self.” It isn't. In **`self._next._prev = newItem`**, start at banana, follow its *current* next link to peach, and set the previous field *on peach*. Although peach's previous link *used to contain* banana, assigning to that field changes peach, not the variable `self`. The actual line has one `_next` and then `_prev`.

| Step in the middle branch | Banana's right | Kiwi's left / right | Peach's left |
|---|---|---|---|
| Before linking the new kiwi | peach | none / none | banana |
| Set kiwi's previous | peach | banana / none | banana |
| Copy banana's next into kiwi's next | peach | banana / peach | banana |
| Repair peach's previous through banana's next | peach | banana / peach | **kiwi** |
| Replace banana's next | **kiwi** | banana / peach | kiwi |

This is why the last two assignments cannot simply be swapped. If banana already points to kiwi, then writing through banana's next changes **kiwi's** previous field instead of peach's. Peach isn't necessarily destroyed: kiwi already had its own reference to it. What was lost is the *particular route through banana* that the code uses to repair peach. The empty-right-neighbor branch avoids touching that route entirely.

The driver must create this situation before testing it. At 1:12:00 the editor shows peach moved above the kiwi insertion but *still assigned* to `fruit`. As a student notices, something moved. That version builds apple → banana → peach and then inserts **after peach**, not after banana. By 1:13:00 the assignment on the peach line is gone. The corrected driver retains banana as the anchor while adding peach, exactly as the instructor explains aloud.

**Annotated teaching version — guide-added comments** (1:12:00 intermediate lines, not an observed run):

```python
fruit = fruit.append("banana")        # Starting at apple, add banana and move fruit to banana.
fruit = fruit.append("peach")         # Add peach and move fruit AGAIN, now to peach.
fruit.insertAfter("kiwi")             # Kiwi therefore follows peach, not banana.
```

With the later avocado line still in place, a separate teaching run from that retained peach produces `peach`, `avocado`, `kiwi`. The revised banana-anchored run is below; it is a different state.

**Key items to scan**

- **Edge case** — no peach follows banana; there is no previous field on a missing neighbor to update.
- **`if self._next != None:`** — checks the actual link; not the public neighbor method, which can return banana itself at the boundary.
- **`newItem._prev`** — kiwi's left link becomes banana.
- **`newItem._next`** — kiwi keeps the old right neighbor, peach, before banana's link changes.
- **`self._next._prev = newItem`** — update peach through banana's still-intact next link.
- **`self._next = newItem`** — finally point banana at kiwi; doing it earlier changes the meaning of the preceding expression.
- **`else`**, **`return newItem`** — at the tail only two links need changing; both paths return kiwi without moving the caller's variable automatically.

### 1:16:00–1:22:59 · A missing printed fruit can mean two different things

A student asks to see the actual run. The first result has apple and avocado but no kiwi; the instructor asks about saving. The next result has kiwi but no apple. Rather than treating “not printed” as “deleted,” follow two things independently: which links are in the list, and which item the print loop is told to start from.

**Annotated teaching version — guide-added comments** (banana-anchored driver visible from 1:13:00):

```python
from item import Item                  # Bring the course Item class into this driver.
fruit = Item("apple")                  # NEW chain on each execution; fruit starts at apple.
fruit = fruit.append("banana")         # Attach banana and rebind fruit to that returned item.
fruit.append("peach")                  # Banana gains peach. Ignore the return; fruit stays banana.
fruit.insertAfter("kiwi")              # Insert between banana and peach. fruit still names banana.
#fruit = fruit.last()                  # Inactive; no move to the final item.
fruit.insertAfter("avocado")           # Insert immediately after banana, now BEFORE kiwi.

iter = fruit                           # Start printing at banana, not at apple.
while True:                            # Visit one item per pass.
    print( iter.object() )             # Display the stored value in the current item.
    if iter.next() == iter:            # At peach the public next() returns peach itself.
        break                          # End after printing peach; do not print it twice.
    iter = iter.next()                 # Otherwise walk right to the next item.
```

Why does avocado come out before kiwi even though it was added later? After inserting kiwi, nothing assigns the returned kiwi to `fruit`. Both insertions therefore ask for the *same slot immediately after banana*. The second arrival goes into that slot and the first arrival moves one position farther along. After both insertions, the complete chain from apple is `apple → banana → avocado → kiwi → peach`. Starting at banana prints the last four, in order. Apple is still linked to banana on the left. A forward-only walk starting at banana cannot show it.

| Printing pass | What prints | What the neighbor check finds |
|---|---|---|
| 1 | banana | avocado differs from banana; advance |
| 2 | avocado | kiwi differs from avocado; advance |
| 3 | kiwi | peach differs from kiwi; advance |
| 4 | peach | `next()` returns peach itself; stop |

The class proposes taking the assignment off the banana line. That changes **more than the starting point**: every later method call on `fruit` now acts on apple. In the edited direct-link append body, adding peach to apple replaces apple's link to banana. This time banana really is *off the forward route from apple*. This is different from apple merely lying to the left of the printing start in the preceding run. No explicit delete operation appears, and the output alone does not establish an object's memory lifetime.

**Annotated teaching version — guide-added comments** (apple-anchored edit, 1:20:00; same forward print loop):

```python
fruit = Item("apple")                  # Fresh execution; start with a NEW apple.
fruit.append("banana")                 # Apple points to banana, but fruit remains apple.
fruit.append("peach")                  # Edited append replaces apple's right link with peach.
fruit.insertAfter("kiwi")              # apple → kiwi → peach.
fruit.insertAfter("avocado")           # apple → avocado → kiwi → peach.
iter = fruit                           # This time the same print loop starts at apple.
```

**Observed terminal output** (1:20:00 scrollback; three executions of **`py .\test\fruit.py`**). Each entry in a column is one printed line. These aren't successive changes to one live Python list: each new command runs the driver afresh.

| Output line | First run | Second run | Third run |
|---|---|---|---|
| 1 | apple | banana | apple |
| 2 | avocado | avocado | avocado |
| 3 | banana | kiwi | kiwi |
| 4 | peach | peach | peach |

The first order matches the earlier, avocado-only driver with tail-seeking append; it is not output from the visible unsaved kiwi edits. The exact saved class and driver for that run are not displayed together. The class's save discussion and the later screenshots support the second and third comparisons; separate teaching runs with the displayed method behavior reproduce those orders. At 1:21:00 the editor again assigns the banana return to `fruit`; that change does not rewrite the old terminal rows. At 1:22:00 the newer terminal group again reads `banana`, `avocado`, `kiwi`, `peach`.

The instructor suggests three symmetric extensions for students to try: **`prepend`** to accompany append, an **insert-before method** to accompany insert-after, and a **first-item method** to accompany **`last()`**. The last idea answers a practical question: if all you hold is banana, how could you deliberately start the walk at apple? These were proposals here, not three finished implementations.

**Key items to scan**

- **`fruit.append("peach")`** — the return is ignored, so banana stays the anchor in the banana-anchored run.
- **`fruit.insertAfter("avocado")`** — inserts after the same banana as kiwi did, placing avocado *before* kiwi.
- **`iter = fruit`** — selects the starting item; forward iteration does not automatically seek the beginning.
- **`print( iter.object() )`** — displays the current stored fruit name, not the Item object itself.
- **`iter.next() == iter`** — this implementation returns the same item at the end; the loop prints peach once and stops.
- **`py .\test\fruit.py`** — runs the saved driver; an unsaved editor change does not establish what the command ran.
- **`prepend`**, **insert-before method**, **first-item method**, **`last()`** — proposed opposites: add at the beginning, insert on the other side of an anchor, or find the first rather than the last node.

## The empty-list contract

### 1:23:00–1:25:00 · Going to the front from peach

The restored banana-anchored driver again shows banana, avocado, kiwi, peach at 1:23:00. Having established why apple is out of view, the class now previews a method that can find it.

The instructor describes restoring the edited item and fruit files, then moving to revision 11. This preview introduces three operations needed later: **`first()`** finds the front from the current node, **`prepend()`** adds at the front of the whole chain, and **`insertBefore()`** adds just before the current node. They are not synonyms. Here “revision 11” means an ancestor of `master` (`master~11`), not a numbered release. The class moves on rather than developing each method from scratch. The following is the visible 1:24:00 driver, with the walk condensed after its starting line; its predicted sequence is not the earlier kiwi output in the terminal.

**Annotated teaching version — guide-added comments:**

```python
fruit = Item("apple")        # Create the initial apple node.
fruit.append("banana")       # This revision finds the tail; fruit still names apple.
fruit.append("peach")        # Attach peach after banana, not directly after apple.
fruit = fruit.last()         # Rebind fruit to peach without moving any node.
fruit.insertBefore("mango")  # Put mango immediately before peach.
fruit.prepend("acai")        # Find the front from peach; put acai before apple.
iter = fruit.first()         # Start the forward print loop at acai, not peach.
```

Expected walk: acai → apple → banana → mango → peach. Here is how the visible prepend changes the front even though the caller still refers to peach:

**Annotated teaching version — guide-added comments:**

```python
def prepend(self, obj):         # Called on peach with obj = "acai".
    first = self.first()        # Find apple, the front before the insertion.
    newItem = Item(obj)         # Create the separate acai node.
    newItem._next = first       # acai points forward to apple.
    first._prev = newItem       # apple points back to acai.
    return newItem              # Return acai; the driver need not capture it to change the chain.
```

The corresponding append finds the tail first and makes the two links in the opposite direction. This differs from the shorter 1:23:00 append that attached directly to its receiver.

**Key items to scan**

- **`first()`** — from peach, follow previous links to the new front, acai.
- **`prepend()`** — insert at the front, even when called on peach.
- **`insertBefore()`** — insert at a particular node: mango belongs immediately before peach, not before apple.
- **`fruit.last()`** — gives the peach node; assigning it back to `fruit` changes the starting reference.

### 1:25:00–1:35:00 | Is a blank grocery list a list? Test the contract

One student argues that a blank shopping-list pad isn't a list, and that even one item isn't a *group*. The instructor accepts that as a possible design, but wants a caller to be able to create a list today and supply its first item later. A rule requiring an item at creation would force the caller to have one ready. This is a decision about what this class should promise, not a Python rule about how many items make a list.

The class looks back at revision 12 to see what the constructor already permits. **`Item()`** creates a real node even when no fruit is supplied. With **`obj = None`** in the signature, the constructor puts the value **`None`** in that node. The question is whether the rest of the class treats it as an empty list or as an ordinary first element.

**Annotated teaching version — guide-added comments (constructor and driver excerpts):**

```python
class Item:                              # This is the class, not a fruit value.
    _object = None                       # No stored value initially.
    _next = None                         # No forward neighbor initially.
    _prev = None                         # No backward neighbor initially.
    def __init__(self, obj = None):       # No argument? Use None as obj.
        self._object = obj               # Store the supplied or default value on this instance.
```

```python
fruit = Item()          # First experiment: a NEW node holding None.
fruit = Item("apple")   # A second NEW node; fruit now names it, not the empty node.
```

That 1:29:00 driver does **not** test adding apple to an empty node. The second assignment starts over. Its later fruit operations are consistent with the four fruit names in scrollback: apple, avocado, banana, peach. The instructor then asks what happens if the default is removed. Now calling the constructor with no argument fails before any fruit is added. The saved-during-class error, visible in full at 1:35:00, is:

```text
TypeError: Item.__init__() missing 1 required positional argument: 'obj'
```

A student reminds the instructor to account for the changed constructor; the instructor realizes the change restoring the default may not have been saved. An error in PowerShell describes the file that ran, not necessarily the code currently visible in the editor. With the default available again, the next experiment *does* append apple to the empty node. Students predict replacements, ordinary appending, or avocado first. Trace which node each call actually receives before predicting the first line.

**Annotated teaching version — guide-added comments (1:30:00–1:33:00):**

```python
fruit = Item()                 # One physical node; its stored value is None.
fruit.append("apple")          # Old append always creates a NEW node after the tail.
fruit.append("banana")         # Find apple at the end and attach banana.
fruit.append("peach")          # Find banana at the end and attach peach.
#fruit = fruit.last()          # Inactive: fruit still refers to the original node.
fruit.insertAfter("avocado")   # Insert after that original node, before apple.
iter = fruit                   # Start at the original node; do not skip its value.
while True:                    # Visit the current node before testing whether to stop.
    print( iter.object() )     # Print the stored value, including the first None.
    if iter.next() == iter:    # The last node's next() returns that node itself.
        break                  # End without printing peach twice.
    iter = iter.next()         # Otherwise advance to the next node.
```

Observed output of **`py .\test\fruit.py`** (1:31:00; full command at 1:35:00):

```text
None
avocado
apple
banana
peach
```

The instructor asks why apple is *second* immediately after the first append. A node whose stored value represents nothing is still a node: the older append finds it, leaves it intact, and links apple after it. Later avocado becomes second and pushes apple to third. The design goal is different: after adding the first apple, apple should be the **only** item.

**Key items to scan**

- **`Item()`** — creates a node that can receive calls; it does not return `None`.
- **`obj = None`** — lets the constructor omit its argument; removing the default makes `obj` required.
- **`None`** — here a possible marker for no stored item; the older display still prints it as a value.
- **`TypeError`** — the no-argument constructor call failed in the required-argument version. It did not produce a partially printed fruit list.
- **`py .\test\fruit.py`** — runs the saved driver. A traceback from an earlier run can remain after the editor has changed.
- **`fruit.append("apple")`** — mutates the old chain; unlike `fruit = Item("apple")`, it does not rebind `fruit` to a different starting node.

### 1:34:00–1:41:00 | Fix the empty case without leaving a phantom item

The first **`git checkout master~10`** is refused: changing revision would overwrite local changes to **`test/fruit.py`**. Git suggests committing or stashing; the instructor instead discusses restoring the demonstration edits. By 1:36:00 the guarded version is on screen. A refusal is not a checkout, and restoring discarded edits is not how to preserve changes one wants to keep.

For this design the class adopts an invariant: the `None` payload is intended for a **solitary empty node**, not an ordinary item somewhere in a longer chain. That lets an append decide between two different jobs. If the current object stands for an empty list, store apple *in it*. If it already holds something, go to the end, create another node and join it. A student's question about “reassigning object as fruit” is useful: what used to be `None`, and what, exactly, changes to apple? It is the attribute **`self._object`** on the existing node. It is not the caller's name, a new `self`, or the argument being reassigned.

**Annotated teaching version — guide-added comments (1:37:00):**

```python
def append(self, obj):                # Example: fruit.append("apple") supplies obj = "apple".
    if self._object == None:          # Is this node the one standing for an empty list?
        self._object = obj            # Change its stored value from None to apple.
        newItem = self                # The node to return is the existing node we just filled.
    else:                             # An item is already present; retain it.
        last = self.last()            # Find the actual tail, even if self isn't the tail.
        newItem = Item(obj)           # Allocate a distinct node for the additional value.
        newItem._prev = last          # Point it back to the previous tail.
        last._next = newItem          # Point the previous tail forward to it.
    return newItem                    # Return the filled or allocated node, not its payload.
```

Prepending uses the same empty-case decision. When the list isn't empty, it must change the *front*, even if called elsewhere in the chain. The driver deliberately starts this test over with a different empty object; banana does not get added to the earlier apple.

**Annotated teaching version — guide-added comments:**

```python
def prepend(self, obj):               # Example: prepend banana to a fresh empty list.
    if self._object == None:          # No first value yet: reuse this node.
        self._object = obj            # Store banana where None was.
        newItem = self                # Return this same, now filled, node.
    else:                             # Existing front must be retained, not overwritten.
        first = self.first()          # Follow previous links to the front.
        newItem = Item(obj)           # Make a new front node for the incoming value.
        newItem._next = first         # New front points forward to the old front.
        first._prev = newItem         # Old front points back to the new one.
    return newItem                    # Give the caller the front node we just supplied.
```

The revision's **`display(list)`** helper starts from the front, prints a divider, then prints each node's stored value, including the empty marker. The driver labels its tests before calling the helper. The empty, append and prepend displays below were also verified by independently running the transcribed helper and visible tests through the third empty display. They are not claimed as classroom terminal output.

**Annotated teaching version — guide-added comments (selected driver statements):**

```python
def display(list):                   # Here list names the supplied Item, not the built-in type.
    iter = list.first()              # Find the front even if list refers to a later node.
    print("-----")                   # Separate this display from the previous one.
    while True:                      # Always inspect the first node, even when logically empty.
        print(iter.object())         # Print None for the empty marker or the actual fruit value.
        if iter.next() == iter:      # Is there no next node to visit?
            break                    # Stop after this last printed value.
        iter = iter.next()           # Move forward when another item exists.

fruit = Item()                       # Fresh empty node. Empty: divider, then None.
fruit.append("apple")                # Same node now holds apple. Append: divider, then apple.
fruit = Item()                       # NEW empty node, unrelated to the apple test.
fruit.prepend("banana")              # Same new node now holds banana. Prepend: divider, then banana.
fruit = Item()                       # Start a third independent empty case; no banana in this one.
fruit.insertBefore("grape")          # Third visible test call; its implementation/result isn't shown here.
```

The comments describing the displays refer to the calls surrounding those statements in the historical driver. The divider and literal `None` for an empty case come from the display routine, not a value printed by the constructor. The visible grape call alone does not establish what was printed afterwards.

**Key items to scan**

- **`git checkout master~10`** — first refused, later the empty-case code is displayed. **`test/fruit.py`** is the file named in the refusal.
- **`self._object`** — the field that changes from `None` to apple in place. The node's identity and its links do not change on that branch.
- **`newItem = self`** — records the reused node as the return value without allocating another node.
- **`last._next = newItem`** — joins an existing tail to the newly allocated node in the nonempty append branch.
- **`first._prev = newItem`** — joins the old front back to the newly allocated front in the nonempty prepend branch.
- **`display(list)`** — prints a divider, then payloads starting at the first node; the empty case still shows `None`.

### 1:41:00–1:44:00 | What must be true on *every* return path?

The instructor inserts a blank line before **`return newItem`** so its indentation stands out: *both* branches lead to this return, yet only *one* executes per call. At 1:42:00 the empty branch's assignment to **`newItem`** is commented out. The nonempty branch still defines the name, but that cannot help an empty call. The instructor predicts a return of `None`; the distinction matters: an unassigned local is not automatically assigned that value.

**Annotated teaching version — guide-added comments (abbreviated failing path):**

```python
def append(self, obj):              # Called on a node holding None.
    if self._object == None:        # This branch runs; the else branch cannot run this time.
        self._object = obj          # The value DOES change to apple before the error.
        #newItem = self             # Historical edit: nothing binds the local name here.
    else:                           # Simplified nonempty path for this isolated demonstration.
        newItem = Item(obj)         # Not executed on the empty call.
    return newItem                  # Reading the unbound local raises; no value is returned.
```

**Independently verified, not a classroom traceback:** an isolated empty-path fixture raised **`UnboundLocalError`** at the return, and its stored value afterwards was still `apple`. Removing the line therefore can leave a changed object *and* a failed call. No corresponding exception from the classroom's edited version is visible.

In a working alternative shown at 1:43:00 and 1:43:18, assign the default return object **before** testing the field. This changes the organization of the code, not the intended return on either path. The instructor presents the choice between this and assigning separately in each branch as a style preference.

**Annotated teaching version — guide-added comments:**

```python
def append(self, obj):              # Same two cases as before.
    newItem = self                  # Default: we will return the existing receiver.
    if self._object == None:        # For an empty receiver, keep that default.
        self._object = obj          # Fill it; no extra node is needed.
    else:                           # An occupied list needs an additional node.
        last = self.last()          # Locate its tail.
        newItem = Item(obj)         # Rebind the local name to the NEW node instead.
        newItem._prev = last        # Connect it back to the former tail.
        last._next = newItem        # Connect the former tail forward.
    return newItem                  # Now the name is assigned on every path.
```

**Key items to scan**

- **`return newItem`** — runs after either branch, so its operand must exist whichever branch ran.
- **`newItem`** — local name for the node to return, not the item's string value.
- **`UnboundLocalError`** — independently verified consequence of reading the unassigned local; it is not a return of `None`.
- **`newItem = self`** — before the conditional it supplies a default result; the nonempty branch replaces it with a newly allocated node.

### 1:44:00–1:45:00 | Which helpers also need the empty check?

The revised class makes a useful distinction. **`first()`** finds a node based on its links; it doesn't need to inspect the fruit stored there. **`insertAfter()`**, by contrast, must decide whether there is already a fruit to insert *after*. Both methods are visible as the editor scrolls at 1:44:00–1:45:00. The latter is only a fragment in this checkout.

**Annotated teaching version — guide-added comments:**

```python
def first(self):                    # Return the first node reachable from this one.
    iter = self                     # Start with the receiver, not an assumed global head.
    while True:                     # Inspect at least this node.
        if iter._prev == None:      # There is no earlier node.
            break                   # Stop here, even if this is the solitary empty node.
        iter = iter._prev           # Otherwise walk one link toward the front.
    return iter                     # Return the node, not its stored fruit.
```

```python
def insertAfter(self, obj):              # Visible portion only; not a complete method.
    if self._object == None:             # Empty receiver: there is no existing fruit to displace.
        self._object = obj               # Fill the existing node with the incoming value.
        newItem = self                   # Remember this node, not a second one.
    else:                                # Occupied receiver: insertion needs a separate node.
        newItem = Item(obj)              # Allocate that node.
        if self._next != None:           # Is there a successor that must not be lost?
            newItem._prev = self         # Set the new node's left neighbor to the receiver.
            newItem._next = self._next   # Remember the old successor as its right neighbor.
```

The bottom of this guarded insertion is off-screen. The setup explains *why* saving that old successor matters; the visible assignments do not yet show the entire reconnection. The earlier unguarded checkout shows a complete insertion, but it is not a source for the missing lines of this version.

**Key items to scan**

- **`first()`** — follows backward links and stops on the first node with no predecessor.
- **`insertAfter()`** — fills an empty receiver directly; insertion after an occupied node begins by preserving its neighbors.
- **`newItem._next = self._next`** — on the visible nonempty path, saves the old successor on the new node before any later rewiring.

### 1:45:00–1:50:26 | Ask for the last node, then ask what it holds

Does **`last()`** need the same special case as append? A student predicts that an empty list is false, so the loop should be skipped or the call should break. The instructor goes back to the earlier implementation to test that prediction. The test isn't “while this list is nonempty.” **`while True`** enters unconditionally. Even a logically empty list here has a real node named **`veg`**. Its next link is `None`; that tells the loop to stop *on that node*, not to return the link.

**Annotated teaching version — guide-added comments (older implementation, 1:46:00–1:48:10):**

```python
def last(self):                     # Return a node, not the value stored inside it.
    iter = self                     # For veg.last(), begin on the empty veg node itself.
    while True:                     # Enter, regardless of its payload.
        if iter._next == None:      # True immediately for veg: no next node exists.
            break                   # Leave the loop with iter still pointing at veg.
        iter = iter._next           # Only reached if another node exists.
    return iter                     # Return veg itself in this case.

veg = Item()                        # Create an independent empty node; not the fruit list.
print(veg.last())                   # First driver: print the returned node's representation.
```

At 1:48:00 the earlier run in PowerShell begins with `<item.Item object at 0x0000025576758590>`. It then prints apple, avocado, banana and peach from a **separate** fruit list. In the next driver the instructor replaces the first print; these are successive tests, not two print calls in the same version:

```python
print(veg.last().object())          # Find the node first; then fetch its stored value: None.
```

Observed in that next run: `None`, `apple`, `avocado`, `banana`, `peach` on successive lines. Here `None` belongs to **veg**. Unlike the earlier five-line empty-fruit failure, there is no spurious node preceding apple in the *fruit* chain. The class checks whether the file was saved and requests a scroll back through PowerShell. Comparing runtime addresses from separate executions cannot establish that one execution recalled an earlier object. Compare two references in the *same* execution instead.

**Annotated teaching version — guide-added comments (closing driver):**

```python
fruit = Item("apple")             # Start a different object, already containing apple.
fruit.append("banana")            # Link banana at the tail; fruit still names apple.
fruit.append("peach")             # Link peach after banana.
#fruit = fruit.last()             # Inactive: the receiver for the insertion remains apple.
fruit.insertAfter("avocado")      # Insert between apple and banana.
iter = fruit                      # Begin at apple.
while True:                       # Print, then check whether another node exists.
    print( iter.object() )        # Successive payloads: apple, avocado, banana, peach.
    if iter.next() == iter:       # At peach, next() returns peach itself.
        break                     # Leave iter on peach.
    iter = iter.next()            # Advance only when the loop hasn't broken.
print(iter.last())                # Added first: ask for the last node starting at peach.
print(iter)                       # Added next: display the node where the loop stopped.
```

| Stage | Node currently named | Test or request | Result |
|---|---|---|---|
| Empty-node test | veg | No next link; return the current node | Printing the node shows an object representation; requesting its payload instead prints `None`. |
| First three fruit visits | apple → avocado → banana | Each has a successor | Each payload prints; advance once after each. |
| Fourth fruit visit | peach | No successor: `next()` returns the current node | Print `peach`, then break without advancing. |
| After the walk | peach | Ask for the last node starting at peach | It is already last; no allocation or movement. |
| Final comparison | peach | Print the current node too | Both final object representations in that execution match. |

The final two lines actually visible at 1:50:00 are identical:

```text
<item.Item object at 0x000001B896A2E060>
<item.Item object at 0x000001B896A2E060>
```

The preceding run shows one object line with a *different* address. The repeat in the final run is not a fixed address to expect next time, nor is either line the printed string `peach`: both expressions name the same peach **node**. A student notices the two identical lines, and the instructor ends the session. The closing frame at 1:50:26 retains the result; it is not a further execution. The last caption, “I will stop for today,” ends at 1:50:28.76.

**Key items to scan**

- **`last()`** — returns the endpoint node; on the solitary empty node it returns the node itself.
- **`while True`** — checks the literal `True`, not the truth of the list or its contents.
- **`veg`** — names the empty object in the first test; the four fruit values belong to another chain.
- **`veg.last().object()`** — retrieves `None` *from* the returned node; the node itself is not `None`.
- **`print(iter.last())`** — after breaking at peach, displays the endpoint starting from peach.
- **`print(iter)`** — displays that same node again in the final run; compare within a run, not across runs.

## Syntax to remember

| Pattern from the lesson | How to read it |
|---|---|
| `fruit.prev` versus `fruit.prev()` | In a getter-based revision, look up a bound method versus call it now. In the earlier raw-field revision, `prev` itself stored a link. |
| `fruit = fruit.append("banana")` | Evaluate the right side first: link banana and return its node. Only then make `fruit` refer to it. |
| `fruit.append("peach")` | Add peach without assigning the returned node. `fruit` still names the original receiver. Where peach is linked depends on which `append` implementation is loaded. |
| `if iter.next() == iter:` | In the boundary-returning revision, the current item is the last when asking for its neighbor gives the current item back. |
| `self._next._prev = newItem` | Follow the old forward link, then update that successor’s backward link. Do this while `self._next` still leads to the old successor. |
| `def __init__(self, obj = None):` | The caller may omit the argument. That permits `Item()`; the constructor signature alone does not make append handle an empty item correctly. |
| `print(veg.last().object())` | Find the last node, read its payload, then print that value. The empty node exists even though the payload is `None`. |
| `git checkout master~12` | Select a specific ancestor of `master`, not “lesson 12.” A refusal due to local edits means the switch did not happen. |


## Knowledge check

1. The three boxes are linked in order apple, banana, peach. In the original getter revision, predict `fruit.prev()` when `fruit` names apple and when it names peach. In which case can the result immediately receive `.object()`?
2. The revised `next()` gives back peach itself at the end. Why does a loop that waits for `it.next()` to be `None` no longer stop there? Where should printing occur relative to the new stopping test?
3. With `fruit` still naming banana, insert kiwi after `fruit`, then avocado after `fruit`. Starting at banana, what prints? What extra observation would show whether apple was lost?
4. In the middle of a list, what goes wrong if you set banana’s forward link to kiwi and *then* try to find peach by reading that same forward link? Which reverse link also needs attention?
5. Suppose an empty `Item()` uses `None` as its stored value. For its first append, why fill that item rather than attach a second item? If you return `newItem` after an `if`/`else`, what must be true on both paths?
6. Why can `print(veg.last())` show an item representation while `print(veg.last().object())` shows `None`? Does an address-like string from two different script invocations settle whether the objects are the same?

### Answer key

1. At apple, the stored previous link is `None`, so the original getter returns `None`. That result has no `.object()` method. At peach the link refers to the banana **item**; calling its getter returns the string `"banana"`. The call to `prev()` did not itself return that string.
2. The public method masks the missing forward link by returning the current item. Print peach **before** breaking when the next item compares equal to the current one. Otherwise you either omit the last fruit or keep visiting it. This is a change to the method’s contract and therefore to its caller, not a new link from peach back to peach.
3. `banana`, `avocado`, `kiwi`, `peach`, one per line. Both insertions are immediately after banana; the later one goes closer to it. A walk starting at banana cannot display earlier items. Follow the previous link back to apple, or traverse from the first item, to test whether apple is still linked.
4. Re-reading banana’s forward link now finds kiwi, not peach. Preserve access to the old successor until kiwi has a forward link to peach and peach has a backward link to kiwi. A forward-only display can look right even if the backward link is stale.
5. Filling the existing empty item makes apple the first real stored value; otherwise a placeholder containing `None` remains in front. If `newItem` is returned, each path must supply it: the reused item on the empty path or the newly created end item on the other. Reading an unassigned local raises `UnboundLocalError`; it does not silently produce `None`.
6. `last()` returns an `Item`; in this case it returns the starting empty item. The additional call reads that item’s stored value. Printing the wrapper and printing its contents ask different questions. Compare references **within the same live run** to establish identity; a similar-looking representation in old scrollback is not that test.

## Library of definitions

| Term | Meaning in this session |
|---|---|
| Doubly linked list | A sequence held together by a forward and a backward link at each node. From banana you can reach peach in one direction and apple in the other. |
| Node / item | One `Item` instance: it can store a fruit and links to neighboring items. It is not the fruit string itself. |
| Payload | The stored content, such as `"banana"`, read by `object()`. An item can exist even when that content is `None`. |
| Reference and rebinding | A reference lets a name designate an existing object. Assigning a returned item to `fruit` changes which item the name designates, not the identity of the old item. |
| Attribute | A named part of an object reached with a dot. `_prev` is a stored link; `prev` is the name used to look up a method. |
| Accessor / getter | A method for obtaining a result from an object. A getter can apply a boundary rule rather than simply returning the field unchanged. A setter, by contrast, is intended to update something. |
| Receiver and `self` | The object before the dot in a bound method call is the receiver. Inside the method the usual first parameter, `self`, refers to it. |
| Non-public attribute | A leading underscore marks a name as an implementation detail by convention; it is not a lock preventing direct access. |
| `None` and `NoneType` | `None` is a specific Python object, used here to represent an absent link or an empty payload. `NoneType` is its type. Which absence is meant depends on the field. |
| Method contract | What callers can count on: here, most importantly, whether a boundary call can return `None` or always returns an `Item`. |
| Traversal and cursor | Visiting nodes by following links. The local name `iter` in the classroom code records the current node; naming it `iter` does not create a Python iterator protocol. |
| Edge case | An input or position at a limit of the usual case. Inserting after peach has no old successor; appending to an empty list has no stored fruit yet. |
| Working tree and checkout | The files being edited and the Git operation that selects a revision. A change in the editor or a failed checkout is not evidence that a new revision ran. |
| Pair programming | Two people work through code together. In the class discussion its value was a second reader who could trace the instructions as written rather than as the author remembered them. |

## Historical appendix · The code on screen

Read the left gutter as an editor line number, not part of the Python program. Each subsection identifies its frame or compatible overlap. A change visible in an editor does not by itself establish a save or a new run.

### Screen record: three revisions of the fruit list and two different failure sites

The numbers left of `|` reproduce the editor gutter; **do not type those numbers or bars as Python**. These are transcriptions of editor states, not repository exports. Blank gutter rows are retained where useful. Names such as `iter`, `object`, `prev`, `newItem`, the comparison with `== None`, and the original spaces around printed expressions have not been modernized. Times identify requested capture targets. Source filenames are in `snapshots/LIxPVYtUER4-20261122-185355/`.

#### 1. `src/item.py` — direct links, `master~15` (`6cdb33d`)

Observed at 0:01:00, 0:02:00 and 0:03:00 (`00-03-00.png`, left). Lines 2–7 supply the initial defaults and store the fruit passed to the constructor. Lines 9–14 attach to the tail actually found by lines 16–22. The return on line 14 is the new node; the return on line 22 is the previously last node.

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
10 |         last = self.last()
11 |         newItem = Item(obj)
12 |         newItem.prev = last
13 |         last.next = newItem
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

#### 2. `test/fruit.py` — the original two-way walk

Observed at 0:03:00 (`00-03-00.png`, right). Lines 4–5 discard the returned nodes, so the uncommented line 8 starts on apple. The prints on lines 9–18 sample the two forward and two backward moves.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | #iter = fruit.prev.prev
 8 | iter = fruit
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

**Command and output** (`00-04-00.png`, 0:04:00): `py .\test\fruit.py` produced the following five lines after the checkout to `master~15`. The shell is PowerShell, at `C:\CSCS1240F26\Item`. Earlier scrollback shows a distinct import failure followed by `.\script\path.ps1` and an earlier successful run; none of those are output from the next checkout.

```text
apple
banana
peach
banana
apple
```

#### 3. `src/item.py` — private-by-convention fields, `master~14` (`7653923`)

Observed at 0:05:00 (`00-05-00.png`, left). The checkout message is `Item: private attributes with accessor methods.` Lines 9–16 are read-only operations in the ordinary sense: each returns a field without writing it. Append and last at lines 18–31 still use the private fields directly inside the class.

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
13 |         return self._next
14 |
15 |     def prev(self):
16 |         return self._prev
17 |
18 |     def append(self, obj):
19 |         last = self.last()
20 |         newItem = Item(obj)
21 |         newItem._prev = last
22 |         last._next = newItem
23 |         return newItem
24 |
25 |     def last(self):
26 |         iter = self
27 |         while True:
28 |             if iter._next == None:
29 |                 break
30 |             iter = iter._next
31 |         return iter
```

#### 4. `test/fruit.py` — caller saves append results and calls the getters

Observed at 0:05:00 (`00-05-00.png`, right). Each assignment on lines 4–5 moves the caller's reference to the node just added. Both calls on line 8 are necessary to get from peach back to apple. Line 9 is commented out, not a reset to fruit.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
 6 |
 7 |
 8 | iter = fruit.prev().prev()
 9 | #iter = fruit
10 | print( iter.object() )
11 | iter = iter.next()
12 | print( iter.object() )
13 | iter = iter.next()
14 | print( iter.object() )
15 |
16 | iter = iter.prev()
17 | print( iter.object() )
18 | iter = iter.prev()
19 | print( iter.object() )
20 |
```

**Separate failing edit** (`00-09-00.png` and `00-10-00.png`). The other numbered lines in listing 4 remain visible with the same text; only line 8 differs. At 0:09:18, `00-09-18-event.png` shows a repeated `git checkout master~14`, both `M script/path.ps1` and `M test/fruit.py`, and a fresh prompt—not the failing launch. The subsequent 0:11:00 terminal shows the launch and error below.

```text
 8 | iter = fruit.prev.prev
```

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
Traceback (most recent call last):
  File "C:\CSCS1240F26\Item\test\fruit.py", line 8, in <module>
    iter = fruit.prev.prev
           ^^^^^^^^^^^^^^^
AttributeError: 'function' object has no attribute 'prev'
```

The first lookup succeeded; it supplied the function-backed method, not a preceding node. By 0:12:00 the editor again shows line 8 as `iter = fruit.prev().prev()` and the terminal edge contains two later five-name sequences. The instructor discussed accessing the underscored fields; no complete intermediate editor statement for that bypass is available in these captures. Neither sequence alone identifies which edit was executed.

#### 5. `src/item.py` — return the same node at a boundary, `master~13`

Observed partial class at 0:22:00 (`00-22-00.png`, left), with the same numbered lower body in `00-15-00.png` at 0:15:00. The later frame exposes lines 4–35, including the entire signatures and both guards; lines 1–3 are above the top edge here. In the later **0:42:00** view of the same guarded revision, lines 1–32 are legible; the overlapping lines 4–32 match. The listing below is an **assembled visual reconstruction**, adding only lines 1–3 from that later frame, not from the preceding checkout. Lines 12–20 change what clients receive, whereas lines 29–35 still walk the raw internal link and stop at `None`.

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
```

#### 6. `test/fruit.py` — guarded public traversal, then a raw-field probe

First state: 0:15:00 (`00-15-00.png`, right), also visible at 0:22:00 (`00-22-00.png`). Lines 3–5 create the chain but leave `fruit` at apple. Line 9 prints before line 10 checks for no progress. The three-name output is independently verified, not inferred from the five-name scrollback behind this editor.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | iter = fruit
 8 | while True:
 9 |     print( iter.object() )
10 |     if iter.next() == iter:
11 |         break
12 |     iter = iter.next()
13 |
```

Changed state: 0:21:00 (`00-21-00.png`, right). At 0:20:00 the editor had shown the partial text `fr` and an autocomplete menu on line 7, not an executable demonstration. Here lines 7–8 read the actual predecessor field and attempt to read data from its value. Apple's raw predecessor is `None`, so control never reaches the still-present loop on lines 10–15.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | x = fruit._prev
 8 | print(x._object)
 9 |
10 | iter = fruit
11 | while True:
12 |     print( iter.object() )
13 |     if iter.next() == iter:
14 |         break
15 |     iter = iter.next()
16 |
```

**Partial observed traceback** (0:21:00, exposed left terminal strip; end of exception line hidden):

```text
print(x._object)
AttributeError: 'None[remainder not visible]
```

The visible fragment above is not a claim to the rest of the historical message. Independently running the reproduced probe gives `AttributeError: 'NoneType' object has no attribute '_object'` with no prior printed fruit in this run. At 0:22:00 the editor again shows the shorter loop driver; that change does not erase the earlier traceback from the terminal.

#### 7. `test/fruit.py` — return to `master~14`, then add the method-call probe

At 0:23:00 (`00-23-00.png`), the exposed terminal shows a checkout abort naming `test/fruit.py`, then a later checkout position; the instructor describes restoring the changed driver before returning to the older revision. The initially reloaded driver matches listing 4. Do not combine the failed checkout, the later successful one, or the old traceback with a new run.

Observed next edit at 0:24:00 and 0:25:00 (`00-24-00.png`, `00-25-00.png`, right). Lines 7–8 are new relative to the reloaded driver. They use the public methods, not the underscored fields, and `fruit` has been reassigned to peach. The rest of the two-way walk now starts on line 10.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
 6 |
 7 | x = fruit.prev()
 8 | print(x.object())
 9 |
10 | iter = fruit.prev().prev()
11 | #iter = fruit
12 | print( iter.object() )
13 | iter = iter.next()
14 | print( iter.object() )
15 | iter = iter.next()
16 | print( iter.object() )
17 |
18 | iter = iter.prev()
19 | print( iter.object() )
20 | iter = iter.prev()
21 | print( iter.object() )
22 |
```

**Independent check of that displayed state**, not observed output for a new classroom run at 0:24:00–0:25:00: the probe prints `banana`; the existing walk then prints `apple`, `banana`, `peach`, `banana`, `apple`, one per line. This differs from listing 6 in both the method's return behavior and the node to which `fruit` refers.

### Simple getters and a two-way walk · `src/item.py`, 0:27:00

Observed in `00-27-00.png` (left editor). Numbers are editor gutters, not Python to type. Lines 1–7 supply the default empty links and store the constructor argument; 9–16 simply expose the three values. Lines 18–23 create and connect a new last node, returning it; 25–31 locate the last one by reading the raw forward link, not by calling the public getter. In `00-26-00.png`, a “References to prev” tooltip covers part of this class and points to `fruit.py:7`, `fruit.py:10` twice, `fruit.py:18` and `fruit.py:20`; the following frame independently exposes the implementation.

The complete 31 lines match historical listing 3 above, including the defaults and raw `_next` end test.

### Manual walk · `test/fruit.py`, 0:27:00

Observed in `00-27-00.png` (right editor). The two assignments in 4–5 make peach the receiver at 7; 8 prints banana. Lines 10–21 reposition twice and walk forward and backward; 11 is a comment and cannot reset the position. At 0:31:00 the exposed terminal shows `banana`, `apple`, `banana`, `peach`, `banana`, `apple` on six lines, twice in succession; the command side of the prompt is covered.

See historical listing 7 above for lines 1–21 of this driver; the important new evidence here is the displayed six-line output.

### One separating print · `test/fruit.py`, 0:36:00

Observed in `00-36-00.png`, not inferred by renumbering the earlier driver. Line 9 is now an active no-argument print; the same manual walk now begins at 11. The terminal separates its first `banana` from `apple`, `banana`, `peach`, `banana`, `apple` by a blank line.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
 6 |
 7 | x = fruit.prev()
 8 | print(x.object())
 9 | print()
10 |
11 | iter = fruit.prev().prev()
12 | #iter = fruit
13 | print( iter.object() )
14 | iter = iter.next()
15 | print( iter.object() )
16 | iter = iter.next()
17 | print( iter.object() )
18 |
19 | iter = iter.prev()
20 | print( iter.object() )
21 | iter = iter.prev()
22 | print( iter.object() )
```

### Return the boundary node · `src/item.py`, 0:42:00

Observed in `00-42-00.png`, after the class moves to the `master~13` example. Lines 12–20 return a real neighbor if present; otherwise they return the very item that received the call. Lines 22–27 still connect two nodes, and lines 29–32 show the raw end test in `last()`. The bottom of this viewport cuts off the *subsequent lines*; they are not supplied from the older version.

The exposed lines 1–32 are included in historical listing 5. The later view independently confirms that listing’s opening; the lower lines are supported by the earlier view, not by this viewport.

### Original forward-loop driver · `test/fruit.py`, 0:42:00

Observed in `00-42-00.png`. Lines 4–5 build the chain without relocating `fruit`; 7–12 print each node and stop when the public getter returns that same item. There is no separate neighbor probe yet. An apple–banana–peach walk is the expected result of this state; surrounding six-fruit terminal output is from an earlier state.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | iter = fruit
 8 | while True:
 9 |     print( iter.object() )
10 |     if iter.next() == iter:
11 |         break
12 |     iter = iter.next()
```

### An incomplete probe · `test/fruit.py`, 0:44:00

Observed in `00-44-00.png`. Line 7 is an unfinished edit. The editor offers `object` as a completion, but the buffer still says `obj` followed by the existing closing parenthesis. This is not the subsequently executed probe, nor did the frame show a Python error for this unfinished line.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | print(fruit.next().obj)
 8 |
 9 | iter = fruit
10 | while True:
11 |     print( iter.object() )
12 |     if iter.next() == iter:
13 |         break
14 |     iter = iter.next()
```

### Completed probe, keep apple · `test/fruit.py`, 0:45:00

Observed in `00-45-00.png`. The full method name, call parentheses, and separating print are present; the loop starts at line 10. The probe prints banana, then a blank line separates it from the loop's apple, banana, peach.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 |
 7 | print(fruit.next().object())
 8 | print()
 9 |
10 | iter = fruit
11 | while True:
12 |     print( iter.object() )
13 |     if iter.next() == iter:
14 |         break
15 |     iter = iter.next()
```

### Completed probe, keep peach · `test/fruit.py`, 0:47:00

Observed in `00-47-00.png`. Lines 4–5 assign the returned items; all the later calls now start from peach. Terminal result: `peach`, blank line, `peach`. At 0:49:00–0:50:00 the editor again shows lines 4–5 *without* assignments (as in 0:45:00), while the terminal still shows the previous peach result; the third run is fully readable in the 0:51:00 foreground terminal.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
 6 |
 7 | print(fruit.next().object())
 8 | print()
 9 |
10 | iter = fruit
11 | while True:
12 |     print( iter.object() )
13 |     if iter.next() == iter:
14 |         break
15 |     iter = iter.next()
```

### Two different historical errors, not two failures of the same buffer

- `00-26-00.png` (0:26:00), background PowerShell: the earlier failing source line reads `print(x._object)`. The exception begins `AttributeError: 'Non[remainder not visible]`. This is a *raw attribute* access in older scrollback, not a run of the visible `print(x.object())` line. A checkout rejection and `Aborting` also precede the later revision status in this scrollback.
- `00-33-00.png` (0:33:00), background PowerShell: after the two six-line outputs comes another traceback, now at a method lookup. The readable source prefix is `print(x.object()[remainder not visible]`; the exception begins `AttributeError: 'Non[remainder not visible]`. In the right editor the whole line 8 is independently visible as `print(x.object())`. The class discussed omitting the reassignments: apple's previous lookup then returns `None`. The hidden command and precise file state at launch cannot be reconstructed from the terminal fragment; the visible editor at 0:33:00 already shows reassignments.
- `00-36-00.png` (0:36:00): the traceback remains *above* the newer successful blank-line-separated result. Treat the partially covered traceback as historical, not as a fresh failure of line 9.

### PowerShell · `00-51-00.png` and `00-52-00.png`

At 0:42:00 the exposed strip shows another checkout rejection naming `test/fruit.py` followed by `Aborting`. The 0:51:00 foreground view independently shows the subsequent restoration and move to `master~13`. Below, prompts are omitted and terminal word wrap is joined; `M` marks a modified path, not an error. Checking out an already selected revision again does not prove that the modified working files were reset.

```text
git restore .\test\fruit.py
git restore .\src\item.py
git checkout master~13
Previous HEAD position was 7653923 Item: private attributes with accessor methods.
HEAD is now at e01cc6d Item: avoid returning None.
M       script/path.ps1
git checkout master~13
HEAD is now at e01cc6d Item: avoid returning None.
M       script/path.ps1
M       test/fruit.py
.\test\fruit.py
py .\test\fruit.py
banana

apple
banana
peach
py .\test\fruit.py
peach

peach
py .\test\fruit.py
banana

apple
banana
peach
```

At the bottom of 0:51:00, `git checkout master~12` is typed but its outcome is not shown yet. By 0:52:00 the following sequence *is* shown. The terminal covers the implementation; a commit message is not source code.

```text
git checkout master~12
error: Your local changes to the following files would be overwritten by checkout:
        test/fruit.py
Please commit your changes or stash them before you switch branches.
Aborting
git restore .\test\fruit.py
git restore .\src\item.py
git checkout master~12
Previous HEAD position was e01cc6d Item: avoid returning None.
HEAD is now at 7630f8b Item: insertAfter( ).
M       script/path.ps1
```

### Historical screen code · From tail-seeking append to insertion after a named item

The gutter numbers and `|` separators below are **not Python to type**. The original panes are Sublime Text's `src\item.py` and `test\fruit.py`. Times refer to the original PNG seek targets; for example, 1:07:00 identifies `01-07-00.png`. A listing records an editor state, not necessarily the file most recently executed.

#### 0:55:00–0:59:00 · Old runs, then a number line at the prompt

`00-55-00.png` still shows three earlier `py .\test\fruit.py` invocations. The first prints `banana`, a blank line, then `apple`, `banana`, `peach`; the second prints `peach`, a blank line, then `peach`; the third repeats the first output. Below them, Git first refuses `git checkout master~12` because it would overwrite local changes to `test/fruit.py`. The subsequent visible commands are `git restore .\test\fruit.py`, `git restore .\src\item.py`, and `git checkout master~12`. The checkout reports `Previous HEAD position was e01cc6d Item: avoid returning None.`, then `HEAD is now at 7630f8b Item: insertAfter( ).` and `M       script/path.ps1`. Those old Python results precede the checkout; they are not tests of `insertAfter` at the new revision.

At 0:56:00 through 0:58:00 the input text is `23,45,768,908,336`; at 0:57:00 the comma after 768 is highlighted. At 0:59:00 the text is `23,45,768,9080,336`. These are cursor demonstrations at the prompt, not completed commands or program output. In the partly covered editor at 0:55:00 the right-hand end of an earlier driver still shows a call ending in `fruit.next().object())` and a forward loop; the foreground terminal hides the left side of several lines. Do not substitute the later avocado driver for this earlier buffer.

#### 1:00:00 and 1:02:00 · `src\item.py`, tail-seeking version

**Assembled visual reconstruction.** Lines 1–31: `01-00-00.png`. Lines 29–37: `01-02-00.png`, with overlapping `last` header and loop. The visible overlap agrees; unsampled edits elsewhere are unknown. At 1:02:00 the bottom cuts through the next line; the original tail-seeking version's entire insertion body is **not** established by that frame. Do not append the later, shifted 36–46 listing to this one.

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
```

Lines 1–10 establish the defaults, store the constructor argument, and retrieve it. Lines 12–20 return the neighbor or, at a boundary, the current item. Lines 22–27 attach a new item to the result of the tail walk on 29–35, not necessarily to the object which received the call. Line 37 establishes the separate insertion method's name and parameter.

#### 1:00:00–1:02:00 · `test\fruit.py`, avocado before banana

`01-02-00.png` shows this driver. The two appends leave `fruit` on apple; line 6 is commented out, so line 7 inserts after apple. Lines 9–14 print from that starting point. The later first run's apple/avocado/banana/peach order is consistent with these lines and the original tail-seeking append, not proof of the saved contents at this instant.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 | #fruit = fruit.last()
 7 | fruit.insertAfter("avocado")
 8 |
 9 | iter = fruit
10 | while True:
11 |     print( iter.object() )
12 |     if iter.next() == iter:
13 |         break
14 |     iter = iter.next()
```

At 1:00:00 the same visible first 14 lines have additional material below; the literal in line 16 is editor scratch during the typing-mode example, not printed output. `01-02-00.png` shows the driver ending with blank line 15 instead.

```text
15 |
16 | dg
```

#### 1:03:00–1:04:00 · `src\item.py`, removing the tail search

At `01-03-00.png`, one line has already gone. The old name `last` still appears twice, but this body no longer assigns it. No traceback is shown for this intermediate draft.

```text
22 |     def append(self, obj):
23 |         newItem = Item(obj)
24 |         newItem._prev = last
25 |         last._next = newItem
26 |         return newItem
```

At `01-04-00.png`, both uses of `last` have been replaced. Lines 24–25 now connect the new item directly to the caller. The method is **still named `append`**; it is not the full insertion method and it doesn't preserve an old successor.

```text
22 |     def append(self, obj):
23 |         newItem = Item(obj)
24 |         newItem._prev = self
25 |         self._next = newItem
26 |         return newItem
```

#### 1:05:00–1:06:00 · `test\fruit.py`, kiwi first, then a peach reassignment

At 1:05:00, line 4 rebinds `fruit` to banana. The incomplete line 5 reads `fruit.in` and the completion menu offers `insertAfter`; the menu is not executed code. At `01-06-00.png` the buffer reads:

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit.insertAfter("kiwi")
 6 | fruit = fruit.append("peach")
 7 | #fruit = fruit.last()
 8 | fruit.insertAfter("avocado")
 9 |
10 | iter = fruit
11 | while True:
12 |     print( iter.object() )
13 |     if iter.next() == iter:
14 |         break
15 |     iter = iter.next()
```

Line 5 inserts after banana before peach exists. Line 6 calls the edited append and *saves peach* as `fruit`; line 8 therefore acts on peach. The annotation in the teaching section gives a separately tested consequence; the terminal does not show a run of this intermediate driver. `01-09-00.png` and `01-10-00.png` show the same operations with three blank lines inserted after line 5: peach moves to line 9, avocado to 11, the loop to 13–18. `01-11-00.png` shows the compact ordering again.

#### 1:07:00–1:09:00 · `src\item.py`, direct-link append and both insertion branches

**Assembled visual reconstruction of the edited state.** `01-08-00.png` and `01-09-00.png` show lines 4–33 including the unchanged constructor, value/neighbor methods and changed append. `01-07-00.png` shows 22–46 including the complete insertion method, with the same append and tail-walk overlap. Lines 1–3 are outside the readable top region of the edited upper view and are not supplied from the pre-edit file. `01-14-00.png` independently confirms the complete 36–46 insertion block.

```text
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
23 |         newItem = Item(obj)
24 |         newItem._prev = self
25 |         self._next = newItem
26 |         return newItem
27 |
28 |     def last(self):
29 |         iter = self
30 |         while True:
31 |             if iter._next == None:
32 |                 break
33 |             iter = iter._next
34 |         return iter
35 |
36 |     def insertAfter(self, obj):
37 |         newItem = Item(obj)
38 |         if self._next != None:
39 |             newItem._prev = self
40 |             newItem._next = self._next
41 |             self._next._prev = newItem
42 |             self._next = newItem
43 |         else:
44 |             newItem._prev = self
45 |             self._next = newItem
46 |         return newItem
```

The short append (22–26) rewrites the caller's right link unconditionally. In contrast, 38–42 of `insertAfter` preserve and repair an existing right neighbor before rewriting the caller's link. At a tail, 43–45 only connect the new item to its left neighbor; 46 returns it in both paths. The full method is already visible at 1:07:00, before all its assignments have been explained aloud.

#### 1:12:00 then 1:13:00 · `test\fruit.py`, which append changes the anchor?

`01-12-00.png`: peach has moved **before** kiwi, but its returned item is still assigned to `fruit`. The rest of the loop is the same as the 1:06:00 driver. These are the changed operation lines **of this state**, not the later fix:

```text
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
 6 | fruit.insertAfter("kiwi")
 7 | #fruit = fruit.last()
 8 | fruit.insertAfter("avocado")
```

`01-13-00.png`: line 5 no longer assigns to `fruit`; now both insertions operate on banana, and the forward loop begins there. This is the driver used to explain why apple is not printed and avocado precedes kiwi.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit.append("peach")
 6 | fruit.insertAfter("kiwi")
 7 | #fruit = fruit.last()
 8 | fruit.insertAfter("avocado")
 9 |
10 | iter = fruit
11 | while True:
12 |     print( iter.object() )
13 |     if iter.next() == iter:
14 |         break
15 |     iter = iter.next()
```

#### 1:17:00–1:22:00 · `test\fruit.py`, changed anchor and separate terminal history

At 1:17:00 the foreground PowerShell window is still at a prompt, not displaying the new fruit results. At 1:18:00 two result groups are visible at its left. At 1:19:00 the editor highlights `last` in the class while the discussion proposes a corresponding first-item method; that proposal is not a new method visible in this source.

At `01-20-00.png` the driver **no longer assigns the banana return to `fruit` either**. The reliable upper portion reads:

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 | fruit.insertAfter("kiwi")
 7 | #fruit = fruit.last()
 8 | fruit.insertAfter("avocado")
```

That driver keeps `fruit` at apple. The foreground terminal at the same target preserves **three separate completed invocations**, not one output with 12 items:

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
apple
avocado
banana
peach
PS C:\CSCS1240F26\Item> py .\test\fruit.py
banana
avocado
kiwi
peach
PS C:\CSCS1240F26\Item> py .\test\fruit.py
apple
avocado
kiwi
peach
```

At `01-21-00.png`, line 4 again reads `fruit = fruit.append("banana")`; line 5 remains `fruit.append("peach")`. Older rows remain in the terminal. At `01-22-00.png` a further four-line group reads `banana`, `avocado`, `kiwi`, `peach`; the right half of its command is covered, so its exact invocation is not transcribed anew. Line 10 in the visible driver still begins the forward walk with `iter = fruit`.

### Screen listings | The last-node and empty-list experiments

The number to the left of `|` is the observed editor gutter, **not Python to type**. Indentation is rendered in spaces. Each source name below denotes a requested frame of the Session 20 recording. A Git status-bar identifier identifies the checked-out revision, not proof that a buffer or driver was unmodified or saved. The classroom switches between revisions and also edits them locally.

#### 1:23:00 — `item.py` and `fruit.py`, locally changed demonstration

`01-23-00.png`, editor indicator `7630f8b7`. The class pane shows a short append that links directly to `self` (22–26), a separate tail finder (28–34), and insertion with and without a successor (36–46). When a successor exists, save both of the new node's neighbors and point the old successor back before overwriting the forward link. No first-node search occurs in the driver.

```text
17 |     def prev(self):
18 |         if self._prev != None:
19 |             return self._prev
20 |         return self
21 |
22 |     def append(self, obj):
23 |         newItem = Item(obj)
24 |         newItem._prev = self
25 |         self._next = newItem
26 |         return newItem
27 |
28 |     def last(self):
29 |         iter = self
30 |         while True:
31 |             if iter._next == None:
32 |                 break
33 |             iter = iter._next
34 |         return iter
35 |
36 |     def insertAfter(self, obj):
37 |         newItem = Item(obj)
38 |         if self._next != None:
39 |             newItem._prev = self
40 |             newItem._next = self._next
41 |             self._next._prev = newItem
42 |             self._next = newItem
43 |         else:
44 |             newItem._prev = self
45 |             self._next = newItem
46 |         return newItem
```

`fruit.py`, same frame:

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit.append("peach")
 6 | fruit.insertAfter("kiwi")
 7 | #fruit = fruit.last()
 8 | fruit.insertAfter("avocado")
 9 |
10 | iter = fruit
11 | while True:
12 |     print( iter.object() )
13 |     if iter.next() == iter:
14 |         break
15 |     iter = iter.next()
```

The two insertions use the same banana receiver (6 and 8); the walk starts there (10). The newest visible four-line group is `banana`, `avocado`, `kiwi`, `peach`. Earlier scrollback above it is not the output of this exact changed driver.

#### 1:24:00 — `master~11` preview: the front of the list

`01-24-00.png`, editor `ab909a43`. The class excerpt begins at `append`; the preceding `prev` is the same four lines as above. Lines 23 and 30 now find the actual endpoint; links are changed around that endpoint, not necessarily the receiver. The beginning of `first` is visible, but its lower lines and the body of `insertBefore` are not established for this checkout by this view.

```text
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
```

`fruit.py`, same frame (two blank lines before 11):

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

Unlike the preceding driver, the walk deliberately goes from the current peach node back to the first. The five-name sequence described in the teaching section is expected; the visible terminal still contains earlier results. The instructor describes this as a preview rather than stepping through all the operations.

#### 1:25:00 — `fruit.py`, independent empty-case tests at `master~10`

`01-25-00.png`, editor `8887c710`; the same visible driver returns at 1:36:00. Lines 3–10 print from the front; each `Item()` at 12, 20 and 28 starts a different test list. The first two fills happen at 16 and 24. Line 32 is visible below the webcam overlay; no later driver lines or corresponding run are established here.

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
28 | fruit = Item()
29 | print("\nEmpty:")
30 | display( fruit )
31 |
32 | fruit.insertBefore("grape")
```

#### 1:28:00–1:35:00 — `fruit.py`, returning to `master~12` and testing the default

`01-28-00.png` (`7630f8b7`) presents a populated start, then two appends. The complete core is below; the next versions alter how lines 3–4 obtain the first fruit. The later reappearance of `7630f8b7` does not restore the *locally modified* 1:23:00 append to this listing.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 | #fruit = fruit.last()
 7 | fruit.insertAfter("avocado")
 8 |
 9 | iter = fruit
10 | while True:
11 |     print( iter.object() )
12 |     if iter.next() == iter:
13 |         break
14 |     iter = iter.next()
```

`01-29-00.png`: lines 3–4 are now the following pair. The first creation does not append anything to the second object. The later four-name output in scrollback alone cannot distinguish removal of the first line from a new run of this version.

```text
 3 | fruit = Item()
 4 | fruit = Item("apple")
```

`01-30-00.png`–`01-33-00.png`: the second assignment has become an actual append. Compare line 4, not just the fact that the word `apple` is present.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item()
 4 | fruit.append("apple")
 5 | fruit.append("banana")
 6 | fruit.append("peach")
 7 | #fruit = fruit.last()
 8 | fruit.insertAfter("avocado")
 9 |
10 | iter = fruit
11 | while True:
12 |     print( iter.object() )
13 |     if iter.next() == iter:
14 |         break
15 |     iter = iter.next()
```

`01-35-00.png`, PowerShell. The traceback is from an earlier run; its visible `fruit = Item()` call failed on line 3. The line defining the constructor *without* a default is not captured as such in these editor views; the classroom discussion explains the edit and the terminal proves a required argument at runtime. The later successful command and the unsuccessful checkout are distinct commands:

```text
TypeError: Item.__init__() missing 1 required positional argument: 'obj'
PS C:\CSCS1240F26\Item> py .\test\fruit.py
None
avocado
apple
banana
peach
PS C:\CSCS1240F26\Item> git checkout master~10
error: Your local changes to the following files would be overwritten by checkout:
        test/fruit.py
Please commit your changes or stash them before you switch branches.
Aborting
```

Physical wrapping of the long shell lines is normalized. The next change of revision is evidenced by the later status bar and changed methods; the exact restoration commands are not fully exposed in these frames.

#### 1:36:00–1:37:00 — `item.py`, the two guarded methods

**Assembled visual reconstruction:** `01-36-00.png` gives lines 1–31; `01-37-00.png` gives 13–43 with matching overlap, same checkout (`8887c710`). Nothing in the overlap suggests a text change; the capture gap is not evidence of every intervening edit or save. Lines 1–20 initialize and expose the data and neighbors; the no-neighbor accessors return the receiver, not its `None` link. Lines 22–43 choose whether to fill that receiver or allocate and connect an additional node.

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
```

#### 1:41:00–1:45:00 — `item.py`, return-path experiments and the guarded insertion fragment

`01-41-00.png`: the added blank line at 32 moves the common return to 33; it does not put the return inside `else`. `01-42-00.png`: the empty-path assignment is commented out, not moved yet. Here is that latter state; no classroom execution of the broken method is visible.

```text
22 |     def append(self, obj):
23 |         if self._object == None:
24 |             self._object = obj
25 |             #newItem = self
26 |         else:
27 |             last = self.last()
28 |
29 |             newItem = Item(obj)
30 |             newItem._prev = last
31 |             last._next = newItem
32 |
33 |         return newItem
```

`01-43-00.png` and `01-43-18-event.png`: line 23 now defaults the return to `self`. The event frame highlights the second assignment that replaces it for a nonempty receiver. **Assembled visual reconstruction** of the later state below: 22–40 from `01-43-18-event.png`; 37–67 from `01-44-00.png`; 40–70 from `01-45-00.png`. Overlapping lines and numbering agree. These are sampled editor views, not a verified saved file. In particular, do not append line 44 of the older insertion to this later one.

```text
22 |     def append(self, obj):
23 |         newItem = self
24 |         if self._object == None:
25 |             self._object = obj
26 |         else:
27 |             last = self.last()
28 |
29 |             newItem = Item(obj)
30 |             newItem._prev = last
31 |             last._next = newItem
32 |
33 |         return newItem
34 |
35 |     def prepend(self, obj):
36 |         if self._object == None:
37 |             self._object = obj
38 |             newItem = self
39 |         else:
40 |             first = self.first()
41 |             newItem = Item(obj)
42 |             newItem._next = first
43 |             first._prev = newItem
44 |         return newItem
45 |
46 |     def last(self):
47 |         iter = self
48 |         while True:
49 |             if iter._next == None:
50 |                 break
51 |             iter = iter._next
52 |         return iter
53 |
54 |     def first(self):
55 |         iter = self
56 |         while True:
57 |             if iter._prev == None:
58 |                 break
59 |             iter = iter._prev
60 |         return iter
61 |
62 |     def insertAfter(self, obj):
63 |         if self._object == None:
64 |             self._object = obj
65 |             newItem = self
66 |         else:
67 |             newItem = Item(obj)
68 |             if self._next != None:
69 |                 newItem._prev = self
70 |                 newItem._next = self._next
```

Lines 46–52 walk forward to the node without a successor; 54–60 mirror the walk backward. At 62–65 an empty insertion fills the receiver. The nonempty arm allocates and, where a successor exists, starts connecting the new node to its neighbors (67–70). Line 71 and beyond are below the reliably readable area. This does not establish the rest of the method, its final return, or the implementation of `insertBefore` in this revision.

#### 1:46:00–1:48:10 — `item.py`, earlier implementation used in the `veg` test

**Assembled visual reconstruction:** lines 1–32 appear in the 1:47:00–1:48:10 left pane; the lower portion is recovered from `01-46-00.png`, lines 17–47, with matching overlap. Both states indicate `7630f8b7`. This is the tail-finding append, *not* the shorter local edit at 1:23:00 and *not* the guarded append at `master~10`. Lines 29–35 explain how an empty node can be returned intact; lines 37–47 insert avocado between apple and banana in the occupied fruit chain.

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

`01-48-00.png` shows `git checkout master~12`, the response `HEAD is now at 7630f8b Item: insertAfter( ).`, and `M` entries for `script/path.ps1` and `test/fruit.py`. The terminal's seven-character commit abbreviation differs in length from the eight-character editor indicator, not in revision. Modified paths are not proof that every visible edit was committed.

#### 1:46:00–1:50:26 — `fruit.py`, successive print probes and actual closing output

At 1:46:00 line 4 is in progress (`print(veg.las)`); no run of that draft is established. At 1:47:00 it reads `print(veg.last())`. By 1:48:00 the first expression has changed to `print(veg.last().object())`; `01-48-10-event.png` confirms that code with the driver ending after the loop. The line at 18 is added by 1:49:00, the line at 19 by 1:50:00. **Full final visible driver**, `01-50-00.png`, retained at `01-50-26.png`:

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

The first print is about `veg`; the traversal and both final prints are about `fruit`. `01-48-00.png` shows two separate invocations of `py .\test\fruit.py`. The first printed `<item.Item object at 0x0000025576758590>` before the four fruits; the next printed `None` before the same four fruits. `01-50-00.png` shows an intervening invocation with one extra object line, `<item.Item object at 0x000001D839C1E060>`, followed by the final invocation:

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
None
apple
avocado
banana
peach
<item.Item object at 0x000001B896A2E060>
<item.Item object at 0x000001B896A2E060>
```

The second close inspection resolves both final addresses; the closing still repeats them. Only the final two object lines from **one invocation** are identical. The students' question about recalling an earlier object does not establish identity across launches; the code explains the same-run match because `iter` has already reached the endpoint.

## Micro-Learning Insights

*Removable review sheet · CSCS1240F26, Session 20*

- **Ask “which box?” before “what value?”** If `fruit` names peach, its predecessor is a banana *item*. Only the next call, `.object()`, gets `"banana"`.
- **Treat parentheses as an action.** `fruit.prev` looks up a bound method in the accessor revision; `fruit.prev()` calls it. Neither spelling is interchangeable with the internal link `fruit._prev`.
- **Distinguish an absent link from the public answer.** Peach can store `_next = None` even when `peach.next()` returns peach. Drawing a physical self-link would misrepresent this design.
- **Change the stopping test with the getter.** If the boundary answer is “you are already there,” print the current fruit and stop before assigning the same item again.
- **Follow assignments, not repeated fruit names on screen.** A method can add peach without making `fruit` refer to peach. Look to the left of `=` to see what, if anything, was rebound.
- **Trace in both directions.** After inserting kiwi between banana and peach, check peach’s previous link as well as banana’s next link. A correct forward printout tests only half the splice.
- **Repeated insertion has a direction.** Inserting kiwi and then avocado *after banana* puts avocado before kiwi. You can predict the order without running the script once you pin the reference to banana.
- **Do not diagnose deletion from a partial traversal.** A walk starting at banana skips apple even when the link back to apple is intact.
- **Test an empty list at two levels.** Print the item returned by `last()`, then its payload. An existing object containing `None` is different from the method returning `None`.
- **Trace both paths to a common return.** In the empty branch, return the existing item that was filled. In the other branch, return the item just created. Check the name is bound on each path.
- **Use a second reader to challenge remembered output.** The class untangled an extra `banana` by finding the specific `print` that displayed it *before* the familiar traversal began.
