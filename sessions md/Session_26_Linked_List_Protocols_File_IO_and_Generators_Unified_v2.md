# Reading, changing, and saving a doubly linked list

**CSCS1240F26 · Session 26 · Worked teaching companion**

If **`fruit[1]`** gives you banana, what exactly did you get: the word, the object that stores it, or a position you can use again? The distinction matters as soon as you try to change the banana, find it in another list, save it in a file, or start two walks through the same fruit. This session follows those questions through successive versions of the class’s **`Item`** project.

### Find your place in the recording

| Jump to | Question to bring to the demonstration |
|---|---|
| [00:00 · Positions and length](https://www.youtube.com/watch?v=fLQcJno8F-w&t=0s) | How does Python know what square brackets or `len` should do to a new kind of object? |
| [26:00 · Git and competing edits](https://www.youtube.com/watch?v=fLQcJno8F-w&t=1560s) | Which repository am I in, and what happens when two versions disagree? |
| [43:00 · Reading versus assigning](https://www.youtube.com/watch?v=fLQcJno8F-w&t=2580s) | Where do the index and the replacement value come from? |
| [53:00 · Finding a match](https://www.youtube.com/watch?v=fLQcJno8F-w&t=3180s) | Is “does it have banana?” really just a yes-or-no question? |
| [1:21:00 · References, nodes, and clearing](https://www.youtube.com/watch?v=fLQcJno8F-w&t=4860s) | What do you retain when a method returns the node rather than its contents? |
| [1:48:00 · Moving values between representations](https://www.youtube.com/watch?v=fLQcJno8F-w&t=6480s) | What gets copied into a native Python list? What happens if you name that result `list`? |
| [2:19:00 · File round trips](https://www.youtube.com/watch?v=fLQcJno8F-w&t=8340s) | Is the program replacing the file, extending the in-memory list, or both? |
| [2:46:00 · Exceptions, text, and types](https://www.youtube.com/watch?v=fLQcJno8F-w&t=9960s) | How does a traceback connect the caller’s choice to the operation that failed? |
| [3:14:00 · Independent traversals](https://www.youtube.com/watch?v=fLQcJno8F-w&t=11640s) | Why can one generator be on grape while another is still on banana? |

**Evidence.** [JoesTA’s recording, “CSCS1240F26 26”][1] runs 3:31:59.15. The full English auto-caption track has 4,715 cues, from the spoken opening through the closing exchange; its final cue begins at 3:31:58.64 and nominally extends beyond the media. Visual evidence consists of 212 whole-minute targets from 0:00 through 3:31:00 and 32 additional targets, ending at 3:31:58. All 244 original 1920 × 1080 PNGs decoded. Times identify requested seeks, not measured frame-exact transitions. No earlier AI-generated guide supplied lesson evidence.

**How to read the examples.** A screen listing preserves what was visible, even when it was unfinished. A teaching version adds comments or isolates a mechanism so you can follow it. “Expected” is a prediction; “independently verified” describes a separate run, not the instructor’s terminal. Returning an object is not the same as printing it. Likewise, text already visible in a terminal does not prove that the code alongside it was saved or executed.

[1]: https://www.youtube.com/watch?v=fLQcJno8F-w

**Time notation.** In the opening 53 minutes, two-field anchors give minutes and seconds. From the membership discussion onward, two-field anchors in section titles and source notes give hours and minutes (for example, `01:21` means 1 hour 21 minutes); full anchors and capture filenames also give seconds.

## A fruit by name and a fruit by position (00:00–05:59)

The recording opens mid-conversation: a math-class problem mentions 30 feet, angles and a hypotenuse called *x*. The discussion moves from cameras and resolution to possible future work in image processing. No image-processing algorithm or completed triangle calculation is shown. A course Discord post on screen at 01:00 refers to three code challenges per week and six weeks remaining; it is a dated progress post, not a new assignment announced here. The instructor turns back to a question about the class's doubly linked list: if each fruit already has a link to its neighbors, what would it mean to ask for the fruit at position one?

Start with the distinction the screen makes before that question. Searching for banana asks whether a **value** occurs; it doesn't require knowing its position. In the earlier **`has`** driver, the answer on success is a node, not the Boolean value True. Calling **`object()`** on that node retrieves its payload, the actual fruit name. On a miss the answer is **`None`**; the driver prints that answer directly rather than asking it for a payload.

**Annotated teaching version — guide-added comments.** Earlier driver, 02:00; matching PowerShell run at 03:00 (revision `0ec6bc1`).

```python
from item import Item          # Use the course's linked-node class.
b = "banana"                   # A named value to store and search for.
fruit = Item("apple")          # Make a new one-node chain.
fruit.append(b)                # Add banana to this chain; keep fruit pointing at apple.
fruit.append("grape")          # Add grape after banana.
one = fruit.has(b)             # Find the node holding banana.
print(one.object())            # Display its value: banana.
one = fruit.has("apple")       # Reuse one for the first node.
print(one.object())            # apple
one = fruit.has("grape")       # Now one refers to the last node.
print(one.object())            # grape
one = fruit.has("kiwi")        # No node matches; one is now None.
print(one)                     # None
two = Item()                   # Make a separate empty chain; fruit is unchanged.
one = two.has("kiwi")          # No kiwi in this chain either.
print(one)                     # None
```

**Observed output**, in order, one per line: `banana`, `apple`, `grape`, `None`, `None`. A later screen view of **`has(self, obj)`** (14:00 and 16:00, different revision) explains the result: it walks from the front until it finds a matching payload or runs out of nodes. Unlike a yes/no membership test, it preserves the location of a match.

**Key items to scan**

- **`has`**, **`has(self, obj)`** — look for a matching **value**; return its node if found.
- **`object()`** — retrieve banana, apple or grape *from* the returned node.
- **`None`** — no matching node in either the three-fruit chain or the new empty chain.

The next checkout asks a different question. In the 06:00 terminal history, **`git checkout master~10`** moves from `0ec6bc1 has(object)` to `91be848 at(index)`. **`at(n)`** doesn't search for a name; it counts forward from the first node. The instructor initially says “positive” integer, but zero is a valid and essential example. A class question distinguishes *at* from *add*: looking up an index neither inserts anything nor changes the chain. In Python's built-in lists negative indices count from the end, without reversing the list. For example, index −1 identifies the last item. The instructor explicitly says this custom method does **not** implement that convention.

**Annotated teaching version — guide-added comments.** Method at 04:00, with a guide-supplied exception *without a message*, since the classroom message is clipped. Local name `iter` here means the current node, not a call to Python's iterator function.

```python
def at(self, n):                     # Return a node, not the fruit string itself.
    iter = self.first()              # Start at apple even if self is a later node.
    if iter._object != None:         # An empty head has no payload to traverse.
        count = 0                    # Apple occupies position zero.
        while count < n:             # Take only the requested number of steps.
            if iter._next == None:   # At grape there is no fourth node.
                iter = None          # Clear this local reference, not the fruit chain.
                raise Exception      # Adapted: stop instead of returning a node.
            iter = iter._next        # Follow one link: apple → banana, then grape.
            count += 1               # One more link has been crossed.
    return iter                      # For n=1 return banana's node; for empty return the empty head.
```

**Annotated teaching version — guide-added comments.** This is the call order in the 05:00 driver, not the order of a generic 0, 1, 2 test. With the same three fruit values, try predicting which print calls actually finish.

```python
fruit = Item("apple")                 # A NEW chain for this driver.
fruit.append("banana")                # Index 1.
fruit.append("grape")                 # Index 2; there is no index 11.
try:                                  # One handler covers all four calls inside.
    print(fruit.at(0).object())       # Get the head, read its payload, print apple.
    print(fruit.at(11).object())      # Raises while stepping past grape; no payload is printed here.
    print(fruit.at(2).object())       # Skipped after the exception, though index 2 exists.
    print(fruit.at(3).object())       # Also skipped, not a second exception in this run.
except Exception as e:                # Control moves here directly from the failed search.
    print(e)                          # Classroom exception has a message; guide's message-free version prints a blank line.
fruit = Item()                        # Rebind fruit to a NEW empty chain, not the old apple node.
print(fruit.at(0).object())           # Empty head survives the lookup; its payload is None.
```

**Guide-derived trace:** `at(11)` starts at apple/count 0, reaches banana/1, then grape/2. Since 2 is still less than 11, it checks for another link, finds none and raises *before* incrementing again. By contrast, for a nonempty chain `at(-1)` never enters the loop: 0 is not less than −1, so it returns the first node. That's accidental behavior, not negative indexing. The class considers three possible policies: reject a negative argument, leave it unhandled, or implement backward lookup starting from the last node. No such revision is shown. **Independent check:** a small linked-node fixture gave apple at 0, banana at 1, grape at 2, apple at −1, an exception at 11, and `None` as the empty head's payload. No `at` run is visible in these stills; the earlier five-line terminal output belongs to `has`.

**Key items to scan**

- **`git checkout master~10`** — select the historical `at(index)` revision instead of the `has(object)` test.
- **`at(n)`** — count links from the head to locate a node; an index is not a stored label on each fruit.
- **`count < n`** — index 0 takes no steps; index 1 takes one. A negative request also takes no steps in this version.
- **`iter._next`** — tests whether there is a following node before moving to it.
- **`raise Exception`** — leaves the search and skips the remaining calls in that `try`; the source message is only partly visible.
- **`fruit = Item()`** — starts over with an empty node; it does not clear the original three nodes.

## Why square brackets work on one list and fail on another (06:00–13:59)

The instructor returns to **`master`** and first uses a built-in Python list. Its name, **`coro`**, is arbitrary; the numbers determine the result, not a resemblance to the word “color.” A student predicts 3. That would be right for position zero, not position one.

**Annotated teaching version — guide-added comments** (07:20).

```python
coro = [3,6,8]       # Make a built-in list: positions 0, 1, 2 hold 3, 6, 8.
print(coro[1])       # Fetch 6 first; then print it.
```

**Instructor prediction and independently verified output:** `6`. The sampled terminal does not show that particular printout. The instructor realizes the current custom class is already too far along to demonstrate failure. The revision history makes the problem visible: `1207cd3` is labeled “get/set object value at index.” After reaching that revision, the instructor moves back again with **`git checkout master~2`**, landing at `f23376c`, before the get/set change. A **detached HEAD** here means Git is showing a specific commit rather than working at the tip of a named branch; it is not a Python exception.

**Key items to scan**

- **`coro`**, **`coro[1]`** — the named built-in list and the subscript that retrieves its second value, 6.
- **`master`** — branch tip; newer code may already support the behavior a demonstration is meant to lack.
- **`git checkout master~2`** — select the second first-parent ancestor of `master`; here it exposed the earlier test program.
- **Detached HEAD** — a historical checkout, useful for reproducing the older behavior.

That historical test program has more in it than a subscript. Before adding the failing line the class runs **`py .\test\fruit.py`** (09:00). It prints apple, banana and grape, then exercises generators. A generator isn't a random-number generator here. Calling **`genny()`** makes an object that can supply values one at a time; **`yield`** pauses its function after supplying the current payload. A later request resumes *after* that yield. Compare that with a callback: there the traversal itself calls someone else's handler for each fruit.

**Annotated teaching version — guide-added comments.** Class body visible at 07:00 (later checkout); driver usage and results at 09:00–10:00 (earlier checkout). These are separate views, not a claim that both panes were captured in the same run.

```python
def genny(self):                        # Calling it creates a generator; it has not yielded yet.
    iter = self.first()                 # On the first request start at apple.
    if iter._object is not None:        # Empty head: yield nothing.
        while True:                     # Pause once per fruit.
            yield iter._object          # Give the caller apple, then banana, then grape.
            if iter._next == None:      # Resumed after grape? No fruit remains.
                break                   # Finishing the generator signals StopIteration to the caller.
            iter = iter._next           # Otherwise go to the next fruit before the next yield.
```

**Annotated teaching version — guide-added comments.** Condensed driver retaining the original call order and the intentionally skipped request in the two-generator experiment.

```python
jenny = fruit.genny()                   # NEW traversal over the existing three fruits.
print(jenny)                            # Generator description and runtime address, not apple.
try:
    print(jenny.__next__())             # First request prints apple.
    print(jenny.__next__())             # Second prints banana.
    print(jenny.__next__())             # Third prints grape.
    print(jenny.__next__())             # Fourth raises before print gets a value.
except StopIteration:                   # Catch exhausted generator, not an invalid index.
    print("List exhausted.")            # This text comes from the handler.

jenny = fruit.genny()                   # A NEW generator; old jenny is not rewound.
jenifer = fruit.genny()                 # Another NEW generator on the same fruit chain.
print(jenny.__next__())                 # apple (jenny's first)
print(jenifer.__next__())               # apple (jenifer's first)
print(jenny.__next__())                 # banana (jenny's second)
# No second request to jenifer here: the classroom line is commented out.
print(jenny.__next__())                 # grape (jenny's third)
print(jenifer.__next__())               # banana (jenifer's second)
```

**Observed, 09:00:** The first generator group prints its object description then the three fruits, with the fourth request commented out in the driver. The next group starts fresh and prints its description, the three fruits, then `List exhausted.`. The last group prints two descriptions followed by `apple`, `apple`, `banana`, `grape`, `banana`. The two live generators need not stay in step; progressing one does not consume the other's next fruit. Runtime addresses are not stable identifiers to memorize. A separate fixture reproduced the five interleaved values and the exhaustion handler.

**Key items to scan**

- **`py .\test\fruit.py`** — run the test driver belonging to the current checkout, including its earlier print calls.
- **`genny()`** — create a fresh generator over the existing fruit; no new fruit chain is made.
- **`yield`** — supply one value and preserve this call's place for the next request.
- **`jenny`**, **`jenifer`** — separate generator objects even though they read the same nodes.
- **`__next__()`** — resume that particular generator; a fourth request after three fruits raises `StopIteration`.

Only now does the instructor add the actual test: **`print(fruit[1])`**. The square brackets are an operator, with the custom object and the index as operands. Compare **`2 + 3`**: addition has two operands. Unary minus needs just one, as in **`-3`**. The syntax tells Python *which operation is requested*, not how to find position one in a new type. The student's proposed fix—assigning 0 to apple, 1 to banana, and 2 to a third fruit—would encode the correspondence by hand. The instructor wants Python to dispatch the operation to the class instead; the positional traversal already gives the class a way to find that node.

**Annotated teaching version — guide-added comment** (12:00 editor; 13:00 terminal).

```python
print(fruit[1])       # Older Item lacks subscription: evaluation fails before print can run.
```

**Observed after the generator output**, at line 45 of `test/fruit.py`:

```text
TypeError: 'Item' object is not subscriptable
```

This error does not say “banana missing” or “index too large.” Python cannot perform this operation on this version of **`Item`** at all. By contrast, **`fruit.at(1)`** explicitly names a method and returns a node. Merely having that method does not wire up brackets. The subsequent **`git checkout master`** initially aborts: replacing `test/fruit.py` would overwrite the experimental change. Git suggests committing or stashing it. The instructor wants to discard this particular edit, uses **`git restore .\test\fruit.py`**, and then successfully switches branches. Restore is not a way to preserve work you still need.

**Key items to scan**

- **`print(fruit[1])`** — first evaluate the subscript, then print its value *if* evaluation succeeds.
- **`2 + 3`** — binary `+` uses two operands; **`-3`** — unary `-` uses one.
- **`Item`** — its earlier revision supports traversal, but not the bracket operator.
- **`fruit.at(1)`** — an explicit method call; implementing it alone does not enable subscription.
- **`git checkout master`** — aborted while the local driver edit was at risk; switching succeeded only after restoration.
- **`git restore .\test\fruit.py`** — discard the working-file change; do not use this when the change should be kept.

## Where the fourth output line comes from (14:00–22:59)

Back on the branch, the short driver produces four lines. The first three are fruit names, one per loop pass. The last line is the number **3**. Where did it come from if the driver didn't call its old **`count()`** method? The answer follows the same pattern the class used to make its objects iterable: Python provides the operation's public spelling; the object's type supplies the implementation.

**Annotated teaching version — guide-added comments** (14:00 driver, line 10; later editor layout moves the final expression to line 12).

```python
from item import Item               # Import the class used by this checkout.
fruit = Item()                      # Create a new, empty linked structure.
fruit = fruit.append("apple")       # Add apple; rebind fruit to the result of append.
fruit = fruit.append("banana")      # Extend the same chain and keep the returned reference.
fruit = fruit.append("grape")       # Three stored fruits, not three independent lists.
for x in fruit:                     # Start a traversal and ask for one payload per pass.
    print(x)                        # The loop body prints apple, then banana, then grape.
print(len(fruit))                   # Ask for a length; print the returned integer 3.
```

**Observed output**, successful branch run (visible in 15:00 and 21:09 scrollback):

```text
apple
banana
grape
3
```

The loop doesn't need to know which fruit node the variable currently names. **`__iter__`** goes to the head and stores the starting position on it. **`__next__`** saves the current payload *before* moving that position. Moving first would lose the value due on this pass.

**Annotated teaching version — guide-added comments** (17:00 class view).

```python
def __iter__(self):                     # Called to start a for-loop traversal.
    head = self.first()                 # Find the apple node.
    head._iter = head                   # Reset shared traversal position to apple.
    return head._iter                   # Return that node as the iterator.

def __next__(self):                     # One request from the loop.
    head = self.first()                 # Locate the shared position keeper.
    if head._iter == None:              # After grape, position is past the end.
        raise StopIteration             # No fourth fruit; the for loop finishes normally.
    target = head._iter._object         # Save what to return, e.g. apple.
    head._iter = head._iter._next       # Move to banana; after grape move to None.
    return target                       # Return the saved fruit; the loop body prints it.
```

**Guide-derived execution trace of the observed three-fruit run:**

| Request | Position before | Value returned to loop | Position afterward | Loop output |
|---|---|---|---|---|
| First | apple | apple | banana | apple |
| Second | banana | banana | grape | banana |
| Third | grape | grape | `None` | grape |
| Fourth | `None` | no value; raises `StopIteration` | `None` | none; continue after loop |

A generator object keeps its own paused position. This class iterator instead stores the position on the head node; starting it again resets that shared field. Neither mechanism makes the fourth line of the successful run. That line comes from a *separate* count after the loop.

**Key items to scan**

- **`count()`** — the class's earlier named operation for counting all nodes; not the call in this driver.
- **`__iter__`** — reset the position to the head and return an iterator for this class.
- **`__next__`** — return the saved fruit and advance, or raise when the position is `None`.
- **`head._iter`** — position shared on the head node, unlike independent generator locals.
- **`print(len(fruit))`** — the separate statement responsible for the final **3**.

The instructor asks what happens when the class's definition of length is taken away. The fruit loop *still works*: supporting iteration and supporting **`len`** are separate contracts. Python can find length for a list or tuple because its type defines what length means. To count a custom linked chain, **`__len__`** must tell Python where to start, which nodes count, and when to stop. Defining that special method on this class does not change the meaning of length for any other class.

The instructor isn't sure whether the old counter checked the empty case. The 11:00 editor answers for that checkout: its older **`count`** has the same 0-versus-1 decision below. Calling a method by that ordinary name, however, does not make Python invoke it for length. That is the reason the name change matters even when the calculation looks familiar.

**Annotated teaching version — guide-added comments** (16:00–17:00, active implementation).

```python
def __len__(self):                     # Called by len(fruit); the required result is an integer.
    iter = self.first()                # Begin at the first node, not at the loop's exhausted position.
    if iter._object == None:           # Empty structure still has a head with payload None.
        count = 0                      # That placeholder isn't one stored fruit.
    else:
        count = 1                      # Count apple before moving anywhere.
    while True:                        # Keep walking until the current node has no successor.
        if iter._next == None:         # True at grape, or immediately for the empty head.
            break                      # Do not step to None and do not count it.
        iter = iter._next              # Apple → banana; then banana → grape.
        count += 1                     # Increment only on an actual move: 2, then 3.
    return count                       # Return 3 (or 0 when empty); no output here.
```

| Length step | Current node | Has next? | Count after step |
|---|---|---|---|
| Initialize | apple | not tested yet | 1: first payload exists |
| Advance | apple → banana | yes | 2 |
| Advance | banana → grape | yes | 3 |
| Stop | grape | no | return 3 to `len`, then `print` displays 3 |
| Separate empty case | head with `None` | no | start at 0; return 0 |

**Observed failure, then recovery.** The instructor temporarily disables the definition. The 18:00 frame shows quote characters being edited, not a reliable snapshot of the complete saved disabled file. The run itself is clear: the first three fruit lines print, then `print(len(fruit))` fails with **`TypeError`** rather than printing 3. A later editor view shows the method back, but another run still fails. The instructor suspects it was not saved, saves, and retries. The 25:00 scrollback preserves two failing runs followed by the successful one. Both tracebacks cite **line 10**; the contemporary editor's line 12 is not the traceback's source line.

```text
apple
banana
grape
Traceback (most recent call last):
  File "C:\com\joesta\Item\test\fruit.py", line 10, in <module>
    print(len(fruit))
          ~~~^^^^^^^
TypeError: object of type 'Item' has no len()
```

**Independent check:** a separately constructed three-node fixture with the shown counting logic returned 3; its empty instance returned 0. Removing the fixture's length hook produced the same final error text. The course project itself was not run for this check.

**Key items to scan**

- **`count`** — earlier ordinary method; its visible empty-head branch initializes the count to 0.
- **`len`** — built-in operation; asks the *type* of its argument how to obtain a length.
- **`__len__`** — this class's implementation; returns a count rather than displaying it.
- **`iter._object == None`** — choose 0 for the class's empty head, otherwise 1 for the first fruit.
- **`count += 1`** — count a real successor only after following its link.
- **`TypeError`** — iteration can succeed while length fails; inspect the exact expression and the saved version.

## Other ways the class uses the same links (screen examples, 07:00 and 14:00–16:00)

The surrounding code gives a useful comparison: the same apple → banana → grape links can answer a search, supply a callback, copy values, or edit the chain itself. These are editor observations, not additional demonstrations of successful runs. The instructor notes that the class may not yet have covered the “to/from” methods; seeing them here does not turn them into assigned work.

**Annotated teaching version — guide-added comments.** The two searches differ in their stopping condition: the earlier positional search counted steps; this one compares payloads. The callback visitor doesn't look for a match at all.

```python
def has(self, obj):                      # Return a matching node, not a Boolean.
    if self._object != None:             # Nonempty receiver: search the chain.
        iter = self.first()              # Begin at apple, even when called from grape.
        while iter != None:              # Continue until a match or the end.
            if iter._object == obj:      # Banana satisfies a banana search.
                break                    # Keep that node as the answer.
            iter = iter._next            # Kiwi never matches; eventually this is None.
    else:
        iter = None                      # The empty receiver cannot match kiwi.
    return iter                          # Node on success; None on failure.

def forEach(self, caller):               # The caller supplies a withItem handler.
    iter = self.first()                  # Start at the first node.
    while True:                          # Visit, then decide whether to stop.
        caller.withItem(iter._object)    # Let the handler act on this payload.
        if iter._next == None:           # No successor after grape.
            break                        # No extra callback after that last fruit.
        iter = iter._next                # Advance without changing the stored links.
```

There is no empty-payload guard in the visible callback method. By contrast, the two export methods check the head before copying anything. One builds an in-memory Python list; the other opens a file in write mode and writes one text line per payload. Their partners *append* what they read to the receiver. There is no initial clear in either import loop.

**Annotated teaching version — guide-added comments** (07:00 and 14:00; compatible visible list-helper lines).

```python
def toList(self):                        # Return a separate built-in list of the payloads.
    list = []                            # Local name in the instructor's code; initially empty.
    iter = self.first()                  # Find the linked head.
    if iter._object is not None:         # Leave the new list empty if no fruit is stored.
        while True:
            list.append(iter._object)    # Copy the current payload, not the node or its links.
            if iter._next == None:       # Last node has already been copied.
                break
            iter = iter._next            # Copy the next fruit on the next pass.
    return list                          # Expected: ['apple', 'banana', 'grape'] for this chain.

def fromList(self, list):                # Input is a sequence of values to add.
    for item in list:                    # Take one supplied value at a time.
        self.append(item)                # Extend this receiver; don't replace it with a new list.

def toFile(self, filename):              # Save text payloads as separate lines.
    with open(filename, "w") as file:    # Create or truncate this file; close it on leaving the block.
        iter = self.first()              # Begin at the head of this chain.
        if iter._object is not None:     # An empty chain writes no fruit lines.
            while True:
                file.write(iter._object + '\n')  # Write e.g. apple plus a newline; assumes text payloads.
                if iter._next == None:   # The last fruit has already been written.
                    break
                iter = iter._next        # Move only when another node exists.

def fromFile(self, filename):            # Add values from an existing text file.
    with open(filename, "r") as file:    # Read lines until the file is exhausted.
        for item in file:
            self.append(item.strip())    # Remove boundary whitespace, then append the remaining text.
```

Two other fragments change the links rather than just reading them. In the extraction fragment (15:48), consider removing banana from apple ↔ banana ↔ grape. The code makes apple point forward to grape and grape back to apple, then detaches banana. At an end, only one neighbor has to be repaired; with no neighbors the visible branch constructs a fresh empty **`Item()`**. The method header is off-screen in this view. The clearing method (14:00) takes a different approach: it advances to the next node *before* clearing the node behind it, so it doesn't lose the path to the remaining fruit.

**Annotated teaching version — guide-added comments.** Selected extraction branches, adapted to show the otherwise partly clipped outer test; not a verbatim or independently observed run.

```python
if self._prev == None and self._next == None:  # Only node: no neighbor to return.
    rest = Item()                             # Construct a NEW empty object for the remainder.
elif self._prev == None:                      # Removing the first node (e.g. apple).
    self._next._prev = None                   # Banana no longer points back to apple.
    rest = self._next                         # Return the new first node.
elif self._next == None:                      # Removing the last node (e.g. grape).
    self._prev._next = None                   # Banana is now the last node.
    rest = self._prev                         # Return the preceding node.
else:                                         # Removing the middle node, banana.
    self._prev._next = self._next             # Apple now leads straight to grape.
    self._next._prev = self._prev             # Grape now leads back to apple.
    rest = self._prev                         # In this case the result points to apple.
self._prev = None                             # Detach the removed node's backward link.
self._next = None                             # Detach its forward link; its payload is not cleared here.
return rest                                   # The returned node belongs to the remaining structure.
```

**Annotated teaching version — guide-added comments.** Clearing fragment (14:00); closing punctuation of the first print is supplied here for readability, not claimed as visible in the historical listing.

```python
def clear(self):                              # Remove stored values and sever the links.
    iter = self.first()                       # Begin on apple.
    while iter._next is not None:             # A following node can temporarily hold our place.
        iter = iter._next                     # Move onto banana before clearing apple.
        print(f"clearing: {iter._prev._object}")  # Report the preceding payload before erasing it.
        iter._prev._prev = None               # Clear the previous node's backwards link.
        iter._prev._next = None               # Clear that node's forwards link.
        iter._prev._object = None             # Erase apple; next pass handles banana.
    print(f"clearing: {iter._object}")         # No successor now; report grape itself.
    iter._prev = None                         # Disconnect the final backward link.
    iter._object = None                       # Leave no final payload; no explicit return.
```

**Independently verified with a separate linked-node fixture, not classroom output:** the callback received apple, banana, grape in order. Writing and reading a scratch file returned those same three strings, including a final newline in the file. Extracting banana returned a chain containing apple and grape; the detached node still held banana and had no neighbors. Clearing a fresh chain printed `clearing: apple`, `clearing: banana`, `clearing: grape`, in order, and converting it to a list afterward returned `[]`.

**Key items to scan**

- **`has(self, obj)`** — stop on matching payload; keep the matching node available to the caller.
- **`forEach`**, **`caller.withItem`** — the traversal calls a supplied object's method once per visited node.
- **`toList`**, **`fromList`** — return a new built-in list of payloads versus append incoming values to the existing linked structure.
- **`toFile`**, **`fromFile`** — write one text line per fruit versus read, strip and append each line.
- **`Item()`** — in the one-node extraction branch, provide a *new* empty remainder.
- **`rest`** — neighbor or new empty object to return after detaching the selected node.
- **`clear`** — move ahead before clearing the preceding node; handle the last node separately.

## Practicing the pattern; keeping versions you can revisit (22:59–25-minute discussion)

A student can follow the counting code but doesn't think they could type it from scratch yet. The instructor treats those as different stages: first read the code and predict the result; then start a blank file and reproduce it. If you stall, consult your notes for *one step*, not a whole copied solution. A useful checkpoint from this example is whether you can explain why the counter starts at 1 for apple but 0 for an empty head.

That same idea of recoverable steps leads back to Git. A student describes keeping filesystem copies named “version one,” “version two,” and eventually having many projects to distinguish. A commit gives a checkpoint an identity and a message instead. The class contrasts the compact **`git log --oneline`** history with **`git log`** for more detail; a commit message can have a short summary and a longer explanation. The instructor describes version control as a practical skill expected in software development. This is career advice, not a new course grading condition. At 25:00 the terminal confirms restoration of both working files and reports `Already on 'master'`; it does not show a new commit being made.

**Key items to scan**

- **`git log --oneline`** — a compact timeline; the labels `has(object)` and `at(index)` made two neighboring demonstrations distinguishable.
- **`git log`** — the instructor's more detailed history view, useful when a one-line description isn't enough.
- **`git restore .\src\item.py`** — discard the local class edit as well as the separate driver edit; do not mistake either restore for a commit.
- **Blank-file practice** — predict the count, reproduce the traversal, then consult a single missing step.

## What belongs in a Git repository? (26:00–31:00)

At 26:00 the history answers a practical question: *which change would I want to go back to?* The full log shows the author, timestamp, commit identifier and message. The latest visible entry, marked **`HEAD -> master`**, says `change count() to override __len__()`; the next says `get/set object value at index`. At 27:00 the compact history puts those developmental steps in perspective: there are earlier entries for a count, an iterator, a generator, conversion to/from a list and conversion to/from a file. Those are commit descriptions, not new executions. The instructor normally prefers the **`--oneline`** view: an abbreviated identifier and a useful subject often suffice to find a change. The particular command that opened the log is not visible in these two frames.

A student described the cost of repeatedly moving work to a new tool: even a well-organized personal store can be difficult for the next person to take over. Another asked whether Git is only for code and whether GitHub has a storage limit. Git can track a named collection of bytes: an image or a database file need not contain source text. That does not make a repository the best place for every large file. The instructor contrasted many small code files with a few large assets and mentioned **Git LFS** (Large File Storage); no hosting quota was given. **Git** is the version-control tool; **GitHub** is a service that hosts repositories. A Google Drive document's history—who changed it and when—illustrated what *version control* means, not proof of the instructor's conjecture that Drive uses Git internally.

The student's portfolio question was more specific: should a visitor interested in one Python or CCNA project have to clone every project? Folders in a single repository organize what a visitor sees, but an ordinary clone takes the repository, not just the folder the visitor likes. Separate repositories can give independently useful projects separate histories and clone targets. A repository inside another is possible, not a requirement or a demonstrated submodule setup. In the ordinary local layout shown, Git looks in the current directory, then its parents, for repository metadata. From inside a nested repository the nearest one wins; move above that inner root to work in the outer repository. At 31:00 Explorer shows **`.git`** selected inside **`C:\com\joesta\Item`**, alongside **`src`**, **`test`** and **`README.md`**. That usually hidden directory, not the mere presence of source files, distinguishes this version-controlled folder from an ordinary one. The instructor recommended trying unfamiliar layouts on expendable work.

**Key items to scan**

- **`HEAD -> master`** — the detailed history marks the current commit and branch together.
- **`--oneline`** — scan a compact list of identifiers and change descriptions.
- **Git** — tracks changes to file contents, not just to program text.
- **GitHub** — repository hosting; the class did not establish a numeric storage limit.
- **Git LFS** — large-file storage mentioned in response to the hosting question.
- **`.git`** — repository metadata shown in Explorer; the nearest enclosing repository matters.
- **`C:\com\joesta\Item`** — classroom project root; **`src`**, **`test`** and **`README.md`** are ordinary project contents inside it.
- **Clone boundary** — organizing several projects into folders does not make those folders separate repositories.

## Trying a different interface without committing everyone to it (31:00–35:00)

The live example had only one local branch, **`master`**. The instructor looked for the length method, used Find in Sublime Text, and checked the branch rather than guessing which revision was on screen. The search for **`__len__`** lands on line 137 of **`item.py`** (34:00/34:05). Its location in the middle of the class, rather than at the bottom among the newest additions, made sense once the instructor recalled renaming the old **`count`** method in place. This was also the point of the student's account of spending hours trying to undo a branch mistake: make the first experiment one you can abandon.

| Action | Observed result or proposed alternative |
|---|---|
| `git branch` | `* master`: the asterisk identifies the current local branch; it is the only one listed. |
| `git checkout master` | `Already on 'master'`: a check, not a transition to a different version. Both complete commands are legible in later scrollback at 52:46. |
| Hypothetical branch for `__len__` | Leave the `count` version on the original line of work; try the built-in length interface on a different line. Keep it separate, discard that experiment, or merge its changes back. No new branch or merge was demonstrated here. |

**Key items to scan**

- **`master`** — the current branch in this repository, not a universal branch-name requirement.
- **`git branch`** — lists local branches here; it did not create one.
- **`git checkout master`** — confirmed the current branch instead of changing it.
- **`item.py`**, **`count`**, **`__len__`** — Find located the renamed count implementation; the proposed branch would have let the two designs coexist during testing.

## When can Git merge changes for you? (35:00–43:00)

“Copy and paste” was a student's first answer to how people combine two similar files. The instructor pressed for the decision *before* the paste. Compare the corresponding parts. If they match, move on. If one side adds something the other never changed, there may be nothing to choose between. If both changed the same piece in incompatible ways, the intended result is not encoded in the fact that both edits exist. A person must choose one, or write a third version that preserves what matters from both. Two competing definitions of the same function do not turn into a function with both intended behaviors merely by juxtaposing them.

The class asked if Git just looks at addresses or timestamps. The instructor emphasized the **contents**: changing one byte makes a file different even if its length is unchanged. Size and time can be useful hints; neither tells you the programmer's intended result. Distinguish *different* from *conflicting*. The discussion simplified automatic merging to file-level differences. In text files, edits to the **same filename** need not conflict. What matters is how the edits relate to the common starting version, including whether they make incompatible changes to the same region.

**Independently verified clarification, not a classroom merge.** In a scratch text fixture, the base had seven lines, `a` through `g`. Git's three-file text merge combined a change of the first line to `A` with a change of the last line to `G` (exit status 0). When both sides instead changed that first `a`, one to `A` and the other to `Z`, the result included conflict markers and both alternatives (exit status 1). No repository or class file had to be changed for this test. The useful question is not “which timestamp is newer?” but “what should this part say after both changes?”

Git history does not solve every organizational problem. The instructor's example was a file that logically belongs in *two* folders. A hierarchy gives it one location; the instructor described a personal database that associates files with tags so one file can be found in multiple categories, and several files can share a category. This was a description of a separate system, not a Git feature being run. The class returned to a common learning strategy: try these operations on small disposable examples before trusting them with important work.

**Key items to scan**

- **Contents** — two files of equal size can contain different bytes.
- **Same filename** — does not by itself create a merge conflict; independent text edits can combine.
- **Common starting version** — distinguishes what each side actually changed from what was already shared.
- **Merge conflict** — incompatible changes need a human decision; preserving both intentions may require writing new code.
- **Tags versus directories** — categories can overlap without moving or duplicating the underlying file.

## The fruit driver: why it prints three names and then 3 (32:00–44:51)

The driver stayed on screen during the Git discussion. When the instructor returned to Python, it provided a baseline before experimenting with brackets. Three calls to the course **`Item`** class's append method build one linked sequence. Each assignment binds **`fruit`** to the result of the call; it is not three independent constructions, and this is not Python's built-in list append. A *node* here is an object holding one item and a link to the next object. The driver need not know which node the variable holds when iteration and length start: both methods explicitly seek the first one.

**Annotated teaching version — guide-added comments** (`test/fruit.py`, 44:00):

```python
from item import Item                 # Import the course class, not the built-in list.
fruit = Item( )                       # Start one new, empty sequence.
fruit = fruit.append( "apple" )       # Add apple; keep the Item result in fruit.
fruit = fruit.append( "banana" )      # Extend the same chain: apple, banana.
fruit = fruit.append( "grape" )       # Third item; the existing two stay in order.
for x in fruit:                       # Ask this Item for successive stored values.
    print(x)                          # One name per successful request.
print(len(fruit))                     # Ask for the count, then display the returned 3.
```

**Observed first completed `py .\test\fruit.py` run** (44:51 scrollback; full command at 52:46):

```text
apple
banana
grape
3
```

The iterator and length methods are the ones examined above. The distinction to watch in this run: the loop exhausts its saved position, but `len(fruit)` begins a **separate walk**, so it can still count all three fruits.

| Request in the driver | Saved position before | Returned to caller | Position afterward |
|---|---|---|---|
| Begin iteration | Irrelevant: reset | First node as iterator | apple |
| Next 1 | apple | `"apple"` | banana |
| Next 2 | banana | `"banana"` | grape |
| Next 3 | grape | `"grape"` | `None` |
| Next 4 | `None` | No value: raises `StopIteration` | `None`; loop ends |
| `len(fruit)` | Its own local walk starts at apple | `3` | Does not consume or reset the saved iterator position |

**Key items to scan**

- **`Item`**, **`fruit`** — the course object's append result is assigned back; one three-item sequence is traversed.
- **`__iter__`**, **`head._iter`** — find the first node and reset the stored position to it.
- **`__next__`**, **`target`** — save today's value, advance, then return that saved value.
- **`StopIteration`** — the fourth request has no value to print.
- **`__len__`**, **`len(fruit)`** — a separate walk counts one first item plus two successors and returns `3`.

## From a node lookup to a bracket lookup (33:00; return at 43:00–46:00)

The instructor wanted brackets to feel as natural on the custom chain as on a built-in list. There was already a related operation: **`at`** finds the *node* at a position; brackets should give the user the *value* in that node. The screen also showed **`has`**, a search by value rather than position. Neither helper was called by the visible driver. Reading them together explains why the object returned by a search is not necessarily the fruit name that matched it.

**Annotated teaching version — guide-added comments** (`src/item.py`, 33:00 and 44:00; the exception message is omitted in teaching code because its suffix is clipped):

```python
def at(self, n):                     # Find a node by zero-based position.
    iter = self.first( )             # Begin at apple, even if self is farther along.
    if iter._object != None:         # Skip the search for an empty first node.
        count = 0                    # Apple is position 0.
        while count < n:             # For n=1, take exactly one link.
            if iter._next == None:   # Another step was requested but none exists.
                iter = None          # Clear the local reference before the raise.
                raise Exception      # Teaching abbreviation; not a return of None.
            iter = iter._next        # Now at banana for n=1.
            count += 1               # One link followed.
    return iter                      # Return the node, not its _object field.

def has(self, obj):                  # Find the first node carrying obj.
    if self._object != None:         # The guard tests self, not self.first().
        iter = self.first( )         # Search from apple in a nonempty sequence.
        while iter != None:          # A missing link ends an unsuccessful search.
            if iter._object == obj:  # Compare the stored fruit with the target.
                break                # Keep the matching node instead of moving on.
            iter = iter._next        # Try the next node if it didn't match.
    else:
        iter = None                  # No search from a receiver with no value.
    return iter                      # Return a matching node or None, not a Boolean.

def clear(self):                     # Only the start of this method is visible.
    iter = self.first( )             # Locate the first node; remaining steps unseen.
```

For example, on apple → banana → grape, position 1 and a search for `"banana"` identify the same node. Searching for `"pear"` instead runs out of links and returns **`None`**. By contrast, moving past grape in the nonempty positional search raises **`Exception`**; execution does not resume at its final return. These are readings of the visible code, not additional classroom runs. On an empty first node the positional method skips its loop and returns that node. The screen does not establish how **`clear`** completes.

**Key items to scan**

- **`at`** — returns a node at a position; its first position requires zero link traversals.
- **`has`** — returns a node for a match or **`None`** for a miss; not simply true or false.
- **`Exception`** — on a nonempty chain, trying to walk past the last link raises instead of returning a node.
- **`clear`** — the visible opening locates the first node; the rest cannot be recovered from this view.

### First read the second number; then replace it (44:00–46:00)

To isolate the meaning of brackets from the linked implementation, the instructor used a separate, ordinary Python list of integers called **`asd`**. Read the expression inside **`print(asd[1])`** first. The index is zero-based, so 1 selects the second number, 5. The instructor queried whether the edit had been saved; the two earliest completed runs provide a visible check. One ended at `3` (fruit only); the next ended at `5` after the same fruit output. The frames do not establish the exact save action.

**Annotated teaching version — guide-added comments** (44:51/45:00, added after the fruit driver):

```python
asd = [4,5,6]                        # Create a separate built-in list of three integers.
print(asd[1])                        # Retrieve its second integer, then print 5.
```

**Observed second run:** `apple`, `banana`, `grape`, `3`, `5`, in that order on separate lines.

Now the same brackets appear to the *left* of an assignment. The right side is evaluated first; its value replaces just the element at position 1 in the existing list. Neither the name of the list nor either neighbor changes. The call to print after assignment is a new read, not output produced by the assignment.

**Annotated teaching version — guide-added comments** (46:00, full built-in list portion of the revised driver):

```python
asd = [4,5,6]                        # A new run starts from 4, 5, 6 again.
print(asd[1])                        # Initial read: 5.
asd[1] = 9                           # Mutate the same list to [4,9,6]; prints nothing.
print(asd[1])                        # Read the same position again: now 9.
```

**Observed third completed run** (`py .\test\fruit.py`, full scrollback at 52:46):

```text
apple
banana
grape
3
5
9
```

**Key items to scan**

- **`asd`** — an ordinary list separate from the custom fruit chain.
- **`print(asd[1])`** — first reads position 1, then displays its current value, initially `5`.
- **`asd[1] = 9`** — updates the second slot in place; only the following print displays `9`.

## What Python passes to a custom indexing method (46:00–52:46)

Asked which method **`asd[1]`** calls, a student initially answered with the value, five. The distinction is the lesson: five is the *result*; **`__getitem__`** is the operation used to get it. Put the brackets on an assignment target and Python uses **`__setitem__`** instead. The receiving object supplies **`self`**; the value between brackets supplies **`index`**; only assignment supplies the extra **`value`**, from the evaluated right-hand side. The parameter names are the author's choices; the double-underscore names are the protocol that Python recognizes. Integers are objects too: the example reads and writes them as list elements, but it is the *list*, not the integer `5`, that implements the demonstrated subscript operation.

At 47:00–52:00 the left pane shows how the course class implements that protocol. The right pane still executes brackets on **`asd`**, not on **`fruit`**. The observed `5` and `9` therefore demonstrate the built-in behavior the class is trying to imitate; they are not outputs of the custom methods. In the custom chain, moving one position means following one link, just as it did in the positional node search. Notice the different end products: the getter returns the stored value; the setter changes that field on an existing node. Neither replaces the node or its neighbors.

**Annotated teaching version — guide-added comments** (48:25; the two cropped error messages are omitted, not reconstructed):

```python
def __getitem__(self, index):          # Read a payload, e.g. banana at position 1.
    iter = self.first( )               # Locate the node containing apple.
    if iter._object != None:           # Only walk if this first node has a value.
        count = 0                      # No links followed yet.
        while count < index:           # For index 1, take one step, not two.
            if iter._next == None:     # Requested another step beyond the last node.
                iter = None            # Clear local pointer, not any node's link.
                raise Exception        # Teaching abbreviation: no value returned.
            iter = iter._next          # Follow apple's link to banana.
            count += 1                 # Position is now 1; the loop ends here.
    return iter._object                # Return the string, not the banana node.

def __setitem__(self, index, value):   # Receive both the position and replacement.
    iter = self.first( )               # Restart the search from the first node.
    if iter._object != None:           # In this implementation an empty head skips the write.
        count = 0                      # Start at position 0.
        while count < index:           # Walk until this node has the requested index.
            if iter._next == None:     # Detect a missing successor before following it.
                iter = None            # Only the local reference changes.
                raise Exception        # Teaching abbreviation: no assignment occurs.
            iter = iter._next          # Advance one node.
            count += 1                 # Record that step.
        iter._object = value           # Replace only this node's stored value.
    return None                        # No replacement value is returned or printed.
```

**Independently verified transfer trace, not a classroom run.** A small test fixture supplied a first-node lookup and linked apple, banana and grape; it exercised these method bodies without claiming to be the full course project. Python's built-in list snippet was also run separately and printed `5` then `9`.

| Expression on the test chain | Steps from the first node | Result / chain afterward |
|---|---|---|
| `fruit[1]` | Start at apple, count 0; follow one link to banana, count 1; stop. | Returns `"banana"`; no change. |
| `fruit[1] = "pear"` | Obtain `"pear"`; walk again to position 1. | Same three nodes; payloads now apple, pear, grape. Assignment prints nothing. |
| `fruit[-1]` | At apple, `0 < -1` is false, so the loop never starts. | Returns `"apple"`, **not** grape. This custom implementation does not implement built-in negative indexing. |
| `fruit[3]` | Follow two links to grape; count 2 is still less than 3, but grape has no next link. | Raises `Exception`; no fourth value comes back. |

Two more consequences follow from where the guards end. An empty head with no stored value makes the getter skip the loop and return `None`; the setter skips its entire write and returns `None` as well. Those cases were checked in the same fixture, not shown in the classroom terminal. Define a custom type's bracket behavior when it makes sense for that type; adopting the notation alone does not supply every feature of the built-in list.

The screen also reveals a file-loading method just above the bracket methods (47:00–52:00). This is **screen evidence**, not another invocation. It builds values from lines in a text file; it does not interpret those lines as Python expressions.

**Annotated teaching version — guide-added comments** (`src/item.py`, lines 219–222):

```python
def fromFile(self, filename):            # Add items to the existing chain from this file.
    with open(filename, "r") as file:    # Open for reading; close when leaving the block.
        for item in file:                # Receive one text line at a time.
            self.append(item.strip())    # Drop surrounding whitespace; append the remaining text.
```

**Independent example:** a two-line file containing ` apple  ` and `banana` produced stored values `['apple', 'banana']` in the fixture. The method itself printed nothing. Here the append return is ignored: adding the value is the intended effect.

A student's analogy to the inputs of a logarithm prompted a final distinction (50:00–52:00). A function can accept more than one input, as an indexed write takes both the position and a new value. Programming functions can work with objects of many types, including fruit names rather than numbers. Must the same explicit argument always give the same result? Not for a lookup on a mutable object: asking for index 1 twice gave 5 and 9 because the state changed in between. That does not contradict the mathematical idea of a function of fully specified inputs; the object and its state matter to this method. The instructor raised other kinds of functions without developing a classification. Toward the end the instructor spoke about restoring the fruit and item files before another checkout. At 52:46 the bottom prompt actually contains an unsubmitted-looking `py .\test\fruit.py`; neither a fourth result nor a completed restore/checkout is established by that still.

**Key items to scan**

- **`asd[1]`** — a read from the built-in list; **`asd`** is the receiving object, not the integer stored in it.
- **`fruit`** — a chain of course objects; the custom methods are visible alongside the separate built-in demonstration.
- **`__getitem__`** — returns the located node's payload; reading position 1 on the fruit fixture returned `"banana"`.
- **`__setitem__`** — receives a position and replacement; changes one payload, not the links.
- **`self`** — the particular object receiving the operation; its state can affect what a read returns.
- **`index`** — the caller's bracket argument; the name itself has no special power.
- **`value`** — evaluated right-hand side of assignment, such as `9` or `"pear"`.
- **`fromFile`**, **`item.strip()`** — one stored string per file line, without leading or trailing whitespace.
- **Restore versus rerun** — spoken plans and text at the active prompt are not proof of another completed command.

## Does the list have a banana? Choose the question before choosing the return value (00:53–01:02)

### A different checkout means a different experiment (00:53–00:55)

At 00:53 the last completed run in PowerShell still belongs to the indexing and length example. Its driver built the fruit list, printed the fruit and its length, then read and replaced an element of an ordinary Python list. The instructor restores the two working files and goes back to an earlier implementation. That distinction matters: the new driver keeps a reference to apple instead of repeatedly assigning the result of appending back to the name **`fruit`**.

**Observed output:** the last completed invocation before the restores prints `apple`, `banana`, `grape`, `3`, `5`, `9` on successive lines. The class’s later membership run does not print the list length or these two indexed values.

| Action visible in PowerShell | Effect in this example |
|---|---|
| **`git restore .\test\fruit.py`** | Discards the unstaged changes to the driver in the working tree, restoring this file from the index. |
| **`git restore .\src\item.py`** | Does the same for the class implementation. These are historical commands, not instructions to discard your own edits. |
| **`git checkout master~9`** | Checks out the commit nine first-parent steps before **`master`**. Git reports **`HEAD is now at 0ec6bc1 has(object)`**. The current checkout points at that commit rather than a branch tip (detached HEAD). |

**Key items to scan**

- **`asd[1]`** — reads the second element of the built-in list; **`asd[1] = 9`** changes it from 5 to 9 without touching the fruit list.
- **`fruit`** — the older membership driver keeps this name on the original apple node; its two append calls still change the links.
- **`git restore .\test\fruit.py`** and **`git restore .\src\item.py`** — restore the two working files before the historical checkout.
- **`git checkout master~9`** — selects the older membership example in relation to **`master`**; **`HEAD is now at 0ec6bc1 has(object)`** confirms the change.

### Is the caller asking for a count, a yes/no answer, or the thing that was found? (00:53–01:00)

The instructor asks what a method named **`has`** should do. One student starts to describe the three fruits; another hears a question: *Does this list have the particular thing I named?* The grocery-list example settles the practical distinction. Three items on the list tells you nothing about whether you still need to add bananas. A Boolean answer would settle that decision; returning the matching item would also let the caller work with it. These are different promises to the caller, even if both implementations search the same links.

A suggestion to clear a million entries without deleting them one by one helps sharpen the question. Clearing is a change to the list; checking membership need not change it. The instructor mentions copy and move constructors as a possible future discussion, not as an implementation of searching or clearing. The agreed starting point for the search is familiar from counting and indexed access: find the first node, inspect one node at a time, and handle an empty list separately.

**Key items to scan**

- **`has`** — its English name suggests a yes/no question; the checked-out implementation actually returns a matching node or a missing-value marker.
- Count — how many items are present, not which values appear.
- Clear — a proposed bulk change; it was not part of the search demonstration.
- Copy and move constructors — future topics mentioned, not mechanisms demonstrated here.

### Build the test list without losing the first node (00:55–00:57; class details visible at 01:05–01:06)

The driver gives the literal **`"banana"`** a name, **`b`**, so it can pass the same named value to both append and search. Apple needs no separate variable for its string. The name **`fruit`** refers to an instance of the class **`Item`**, not to the string stored inside that instance. Here a node is one such instance: it holds a payload (the stored value) and links to neighboring nodes.

**Annotated teaching version — guide-added comments; checked-out driver and class excerpts.**

```python
from item import Item           # Bring the course's node class into the driver.
b = "banana"                    # Keep a name for the string used again in the first search.
fruit = Item( "apple" )         # Create a NEW node storing apple; fruit refers to that node.
fruit.append( b )               # Link a NEW banana node after apple; ignore the returned node.
fruit.append("grape")           # Reach the current tail (banana); link a NEW grape after it.
```

```python
class Item:                           # A node can be the starting point for operations on its list.
    _object = None                    # Default: no payload.
    _next = None                      # Default: no following node.
    _prev = None                      # Default: no preceding node.
    _iter = None                      # Default: no saved iterator position.

    def __init__(self, obj = None):   # obj is apple for the first call; None if no argument is given.
        self._object = obj            # Set the new instance's own payload; don't replace the node.

    def object(self):                 # Ask this particular node what it stores.
        return self._object           # For the banana node this returns the string, not the node.

    def append(self, obj):            # Add obj at the end, starting from this receiver.
        if self._object == None:      # An empty receiver can hold the first value itself.
            self._object = obj        # Fill the existing node, rather than allocating another.
            newItem = self            # This existing node is the inserted item.
        else:                         # Apple is already occupied in the driver's first append.
            last = self.last()        # Find apple on the first append and banana on the second.
            newItem = Item(obj)       # Allocate a separate node for banana or grape.
            newItem._prev = last      # The new node points back to the old tail.
            last._next = newItem      # The old tail now points forward to the new node.
        return newItem                # The driver ignores this return; fruit still refers to apple.
```

**Resulting state (derived from the displayed code):** apple ↔ banana ↔ grape. Creating the links prints nothing. Using a named string is a convenience, not a requirement for searching: the next two successful calls pass literals directly.

**Key items to scan**

- **`Item`**, **`fruit`** — class and instance reference respectively; apple is that instance’s payload.
- **`b`**, **`"banana"`** — a name and its string value; the driver can reuse the name.
- **`append`** — changes links and returns the inserted node; ignoring its return does not undo the insertion.
- **`object()`** — reads the payload without moving to another node.
- **`_prev`** and **`_next`** — point in opposite directions between neighboring nodes; at a boundary there is no neighbor in that direction.

### Walk until the first equal payload, or until there is no next node (01:00–01:02)

The question passed to **`has`** is about a stored value. The value returned on success is the *node that holds it*. The comparison uses **`==`** (equality), not an identity test or a comparison between the node itself and the search string. The local name **`iter`** is the currently inspected node; assigning its next link to that name moves the search without moving or editing any fruit.

**Annotated teaching version — guide-added comments; method from `item.py`.**

```python
def has(self, obj):                   # Search this list for a payload equal to obj, e.g. banana.
    if self._object != None:          # A nonempty receiver has somewhere to start searching.
        iter = self.first( )          # Start at apple, even if called on another node of this list.
        while iter != None:           # Keep going as long as there is a real node to inspect.
            if iter._object == obj:   # At banana, "banana" equals the value named by b.
                break                 # Stop without advancing: iter still names the matching node.
            iter = iter._next         # On a miss move forward; after grape the next link is None.
    else:                             # An empty Item was passed as the receiver.
        iter = None                   # Nothing was compared; the result is already known.
    return iter                       # Return the matching Item, or None via either miss path.
```

**Annotated teaching version — guide-added comments; driver after list construction.**

```python
one = fruit.has( b )                  # Rebind one to the banana node, not to True.
print( one.object( ) )                # Read the stored string, then print banana.
one = fruit.has( "apple" )            # Rebind one to the first node on this fresh search.
print( one.object( ) )                # Print apple.
one = fruit.has( "grape" )            # Rebind one to the third node; the list is unchanged.
print( one.object( ) )                # Print grape.
one = fruit.has( "kiwi" )             # All three comparisons fail; rebind one to None.
print( one )                          # Print None without calling a method on it.
two = Item( )                         # Create a NEW empty instance, independent of fruit.
one = two.has( "kiwi" )               # Search two, not fruit: the empty branch returns None.
print( one )                          # Print the second None.
```

**Observed PowerShell command and output (01:02:00, `has(object)` checkout):**

```text
PS C:\com\joesta\Item> py .\test\fruit.py
banana
apple
grape
None
None
```

**Execution trace — derived from this code and these inputs.** The two identical last lines have *different causes*.

| Search | Comparisons and movement, in order | What the method returns | What the driver prints |
|---|---|---|---|
| Banana on fruit | Apple: no → banana: yes → break before advancing | Banana node | `banana` via its accessor |
| Apple on fruit | Restart at apple: yes | Apple node | `apple` via its accessor |
| Grape on fruit | Restart at apple: no → banana: no → grape: yes | Grape node | `grape` via its accessor |
| Kiwi on fruit | Apple: no → banana: no → grape: no → next link is `None`; loop stops | `None` after exhausting the chain | `None` directly |
| Kiwi on two | Payload already `None`; no loop, no comparisons | `None` from the empty branch | `None` directly |

**Key items to scan**

- **`has`** — searches by payload and returns an **`Item`** or **`None`**; it does not print.
- **`iter`**, **`self.first( )`** — local search position and the operation that sets it to the first node on each search.
- **`==`** — compares the stored payload with the supplied value, so the caller need not retain the very same string object.
- **`break`** — preserves the matching node for the final return. Without a match, following the final link produces **`None`** instead.
- **`one.object( )`** — unwraps a successful match for printing; the two unsuccessful cases print **`one`** directly.

## Why the empty receiver exists, and why a Boolean might be easier to use (01:02–01:14)

A student questions whether the last search can run at all: does **`two`** exist if there is no kiwi? The instructor takes the statements in execution order rather than guessing from the fruit names. Calling **`Item()`** does not name the class itself as the result. It constructs a distinct instance. The class call creates the instance via **`__new__`** and supplies it to **`__init__`** for initialization; the initializer neither supplies the instance as its own return value nor looks for kiwi. Uppercase class names and lowercase instance names are a naming convention the instructor uses, not how Python decides what call to make.

| Step | Name or expression being evaluated | Effect |
|---|---|---|
| 1 | **`two = Item( )`** | No explicit payload; the parameter **`obj`** defaults to **`None`**. Initialization sets the instance’s **`_object`** to that value; **`two`** now names the instance. |
| 2 | Right side of **`one = two.has( "kiwi" )`** | Python looks up the callable method on **`two`**. Inside that call **`self`** refers to this instance and **`obj`** is the string kiwi. |
| 3 | Test the receiver’s payload | The nonempty test fails. The empty branch returns **`None`**, which becomes the new value of **`one`**. Neither **`two`** nor the original fruit list is replaced. |
| 4 | **`print( one )`** | The value is printable; this is the final **`None`** in the observed run. |

**Key items to scan**

- **`Item()`**, **`__new__`**, **`__init__`** — call the class, create an instance, then initialize its payload; the three operations are related but not interchangeable.
- **`two = Item( )`** — leaves **`two`** referring to a real, empty instance, not to **`None`**.
- **`one = two.has( "kiwi" )`** — evaluate the right side before rebinding **`one`**.
- **`self`** — the implicitly supplied receiver; **`obj`** — the explicit search value here, but the optional initial payload in the constructor.
- **`_object`** — the slot holding the payload. **`print( one )`** can display **`None`** without trying to call a node method.

### The hazard is what the next caller does (01:08–01:14)

The instructor asks why the empty test succeeds yet the design might still be poor. Someone suggests zero, but zero can be a real value. Students favor **`True`** or **`False`** for a question phrased as yes/no. With the historical design, a caller who assumes every result has a node’s methods can accidentally call **`object()`** on **`None`**. Printing **`None`** is fine; asking it for an attribute it does not provide raises **`AttributeError`**. The instructor likens this to forgetting to check a null result in other languages: the error occurs at the later use, not merely because the search returned a missing value.

One question compares the memory needed by a Boolean and **`None`**. The discussion was conjecture; there was no size measurement. **`None`** is a real object representing absence, not an object that literally has no attributes. The design argument does not depend on memory size: return a Boolean if the caller only needs a decision; return a node if the caller needs to work with the match, and specify how to check a miss. The class discusses the Boolean alternative; it does not replace the displayed method or rerun it with Boolean output here.

**Annotated teaching version — separate caller-side illustration, guide-added comments.**

```python
match = fruit.has("kiwi")          # The historical method returns None on this three-node list.
print(match is not None)           # Convert the search result into a yes/no decision: False.
match = fruit.has("banana")        # A new search returns the actual banana node.
if match is not None:              # Only ask a node for its stored value when one was found.
    print(match.object())          # Print banana; this branch would be skipped for kiwi.
```

**Independently verified output** (separate fixture using the displayed methods, not classroom output):

```text
False
banana
```

**Key items to scan**

- **`True`**, **`False`** — unambiguous Boolean answers when membership alone is the question.
- **`None`** — the historical miss marker; it is neither an empty **`Item`** nor the numeric value zero.
- **`object()`** — valid for the returned node but not for a miss; an unchecked miss can produce **`AttributeError`**.
- **`is not None`** — distinguishes a successful node lookup from a miss without making assumptions about the stored value.

## What exactly does a position give back? (01:14–01:20)

Does asking for position one give the number one, the word banana, or an object holding banana? The instructor asks for the *type* as well as the value. The positional method **`at`** starts at the first node and counts links. The integer counts steps; it is not the thing the method returns. The right pane later shows the separate draft **`x = fruit[1]`**. It records the indexing question, not an observed subscript run or proof that brackets dispatch to **`at`** in this checkout.

**Annotated teaching version — guide-added comments.** The exception text is cut off; this teaching version deliberately substitutes a plain exception at that line, not an invented message.

```python
def at(self, n):                     # Ask for the node n steps forward from the first node.
    iter = self.first( )             # Start with the apple node, not the string "apple".
    if iter._object != None:         # Only count links when the list holds a value.
        count = 0                    # Apple is at position zero.
        while count < n:             # For n = 1, take exactly one forward step.
            if iter._next == None:   # The requested step would leave the list.
                iter = None          # Clear the local name before raising; this is not a return.
                raise Exception()    # Teaching substitute for the partially visible message.
            iter = iter._next        # Move apple → banana on the first step.
            count += 1               # Record that one link was followed.
    return iter                      # Return the node reached; a separate accessor reads its payload.
```

In this version, a nonempty three-node list at position zero returns the apple node without stepping. Position one returns the banana node after one step. A request beyond grape raises instead of reaching the final return. The empty case differs from **`has`**: the loop is skipped and the initial empty node is returned, not the sentinel **`None`**. These are code-derived behaviors, not additional terminal runs by the instructor.

How do we know the first result is a node? At 01:16–01:20 the instructor follows **`first()`** rather than accepting “the first one is apple” as an answer. Its first assignment copies a reference to the receiver; walking backward follows references to other instances. The result is still an instance of **`Item`**, never a literal fruit string or a position. To say which instance, point to the node that the caller supplied or that the walk reached. You need not know a numeric memory address to distinguish that node from its stored string. The instructor uses this to separate three questions: *Which name? Which object does it refer to? What type is that object?*

**Annotated teaching version — guide-added comments; endpoint methods.**

```python
def first(self):                # Start from any node in this linked list.
    iter = self                 # A second name for the same node; no copy of a node is made.
    while True:                 # Walk backward until there is no predecessor.
        if iter._prev == None:  # Apple has no preceding node.
            break               # Keep apple as the answer.
        iter = iter._prev       # From grape: banana, then apple.
    return iter                 # Return the apple Item, not "apple".

def last(self):                 # Mirror walk used by append.
    iter = self                 # Start at the receiving node.
    while True:                 # Keep moving toward the tail.
        if iter._next == None:  # Grape has no following node in the completed chain.
            break               # Stop before leaving the chain.
        iter = iter._next       # From apple: banana, then grape.
    return iter                 # Return the tail node itself.
```

**Key items to scan**

- **`at`** — returns the node reached after counting links, not its index or payload.
- **`first()`** — follows backward links to the first node; **`last()`** does the forward counterpart.
- **`Item`** — type of the apple or banana node; **`None`** — absence of a link or payload, with a role determined by the field.
- **`has`** — unlike positional access, returns **`None`** on an empty-list miss.
- **`x = fruit[1]`** — later editor draft only. Do not assign the search run’s output to this line.

### What the other visible link operations do (00:54–01:00; 01:05–01:07; 01:17–01:18)

The class did not run each of the following methods during this discussion. They are useful screen evidence for the central distinction between receiving a node, receiving a stored value, and changing links. The examples below explain the corresponding lines of the historical listing; the illustrative inputs are separate from the recorded run.

**Annotated teaching version — guide-added comments; neighboring nodes.**

```python
def after(self):                    # Ask the banana node for its following neighbor.
    if self._next != None:          # Banana has a following node, grape.
        return self._next           # Return grape's node, not its string.
    return self                     # At grape, stay on grape rather than returning None.

def before(self):                   # Ask the banana node for its preceding neighbor.
    if self._prev != None:          # Banana has a preceding node, apple.
        return self._prev           # Return apple's node.
    return self                     # At apple, stay on apple.
```

**Annotated teaching version — guide-added comments; link updates visible on screen.** Imagine inserting pear just before banana in apple ↔ banana. The previous apple must point *forward* to pear as well as pear pointing *back* to apple. Changing only the link on banana would give different paths depending on which direction you travel.

```python
def insertBefore(self, obj):          # Example receiver: banana; obj: pear.
    if self._object == None:          # An empty receiver needs no separate node.
        self._object = obj            # Store pear in this existing node.
        newItem = self                # Return that same node below.
    else:                             # Banana already holds its own value.
        newItem = Item(obj)           # Create pear as a separate node.
        if self._prev != None:        # Apple exists before banana.
            newItem._next = self      # Pear points forward to banana.
            newItem._prev = self._prev  # Pear points back to apple.
            self._prev._next = newItem  # Apple now points forward to pear.
            self._prev = newItem      # Banana now points back to pear.
        else:                         # If inserting before the head, there is no apple to update.
            newItem._next = self      # New head points forward to old head.
            self._prev = newItem      # Old head points back to new head.
    return newItem                    # Returns pear's node, not the old banana.
```

The screen also shows part of the opposite insertion and the end of another head-insertion branch. The following are *fragments*, not complete standalone methods. A branch not visible below the editor edge is not silently supplied.

```python
# Visible head-insertion branch; method header is off-screen.
first = self.first()                  # Find the existing first node, e.g. apple.
newItem = Item(obj)                   # Create a separate new first node.
newItem._next = first                 # New first points forward to apple.
first._prev = newItem                 # Apple now points back to the new first node.
# The enclosing method then returns newItem.
```

```python
# Visible part of insertAfter(self, obj); example: insert pear after apple.
if self._object == None:              # Empty receiver: reuse it rather than creating a neighbor.
    self._object = obj                # Store pear in the existing node.
    newItem = self                    # Keep a reference to that same node.
else:                                 # Otherwise create a NEW node.
    newItem = Item(obj)               # Make pear separate from apple.
    if self._next != None:            # Banana currently follows apple.
        newItem._prev = self          # Pear points back to apple.
        newItem._next = self._next    # Pear points forward to banana.
        self._next._prev = newItem    # Banana points back to pear.
        self._next = newItem          # Apple now points forward to pear.
    # The alternate branch begins on screen; its body is below the view.
```

**Annotated teaching version — guide-added comments; `extract`, guard reconstructed from the visible tests.** Removing banana from apple ↔ banana ↔ grape should leave apple ↔ grape and a separate banana node. The return value refers to the *remainder*, not the node that was just removed.

```python
def extract(self):                          # Detach this receiver from its neighbors.
    rest = None                             # Placeholder until a branch identifies the remainder.
    if self._prev == None and self._next == None:  # Neither neighbor exists (guard punctuation restored).
        rest = Item( )                      # The remainder is a NEW empty Item, not None.
    else:
        if self._prev == None:              # Receiver was the head, e.g. apple.
            self._next._prev = None         # Banana becomes the head; it has no previous link.
            rest = self._next               # Return that surviving neighbor below.
        else:
            if self._next == None:          # Receiver was the tail, e.g. grape.
                self._prev._next = None     # Banana becomes the tail; it has no next link.
                rest = self._prev           # The remainder is accessible via banana.
            else:                           # Banana has both apple and grape as neighbors.
                self._prev._next = self._next  # Apple now points forward to grape.
                self._next._prev = self._prev  # Grape now points back to apple.
                rest = self._prev           # Keep apple as an entry point to the remainder.
    self._prev = None                       # Detached receiver no longer points backward.
    self._next = None                       # Nor forward; its own payload is unchanged.
    return rest                             # Return a node in the remainder, or a new empty Item.
```

Two other traversal interfaces illustrate why “what comes back?” matters. **`forEach`** sends each payload to another object’s **`withItem`** method. A Python loop instead calls the iterator protocol and receives each payload as a return value. Neither operation returns each *node* the way **`at`** does.

**Annotated teaching version — guide-added comments; visible traversal methods.**

```python
def forEach(self, caller):                # caller supplies a method to apply to each payload.
    iter = self.first( )                  # Start at apple.
    while True:
        caller.withItem(iter._object)     # Give the caller apple, then banana, then grape.
        if iter._next == None:            # Grape has no following neighbor.
            break                         # Stop after dispatching grape, not before it.
        iter = iter._next                 # Otherwise continue to the next node.

def __iter__(self):                       # Start/restart Python's iteration over this chain.
    head = self.first( )                  # Locate apple.
    head._iter = head                     # Save apple as the next node to read.
    return head._iter                     # Return that Item to service next-value requests.

def __next__(self):                       # Produce one value per successful request.
    head = self.first( )                  # Locate the node holding the shared traversal position.
    if head._iter == None:                # After grape there is no remaining node.
        raise StopIteration               # A fourth request ends the three-fruit loop.
    target = head._iter._object           # Save the current string before moving.
    head._iter = head._iter._next         # Advance apple → banana → grape → None.
    return target                         # Return the saved string, not the new position.
```

The tail of the counting routine provides one more contrast: start the nonempty count at one, check for a next link, advance, then increment. On the three-fruit fixture the count progresses 1 at apple → 2 at banana → 3 at grape; the no-next test ends the loop before a fourth increment. The opening lines of this routine are not visible in these captures.

**Annotated teaching version — guide-added comments; nonempty path of the count fragment.**

```python
count = 1                       # The first occupied node, apple, is already included.
while True:                     # Repeat until the current node is the last one.
    if iter._next == None:      # True at grape; there is no fourth node.
        break                   # Keep the current count of three.
    iter = iter._next           # Move to banana, then to grape.
    count += 1                  # Add one for the node just reached.
return count                    # Return the integer, not the last node.
```

**Key items to scan**

- **`after()`**, **`before()`** — return a neighbor when it exists, otherwise return the receiver, not **`None`**.
- **`insertBefore`** — connects the new node in both directions; if the receiver is empty it fills it instead.
- **`insertAfter`** — the visible occupied-middle branch redirects both neighboring links. The tail branch body is off-screen.
- **`extract`** — isolates the receiver, reconnects any surviving neighbors, and returns a way into the remainder.
- **`forEach`**, **`withItem`** — give the stored value to a caller-supplied operation before testing whether to stop.
- **`__iter__`**, **`__next__`**, **`StopIteration`** — reset the saved position, return one payload at a time, then signal exhaustion on the next request.
- **`at`** — returns a node; the counting fragment returns an integer. Compare what is returned, not just the fact that both walk links.

## What is created when `Item()` is called? (01:21–01:25)

The line **`two = Item( )`** looks like a function call. But the definition on the left starts with **`class Item:`**, not a function definition named Item. The parentheses ask the class to construct an instance. There are now two separate structures: the apple node referred to by **`fruit`**, linked to banana and grape, and a new, empty node referred to by **`two`**. Creating the second one does not clear or otherwise modify the first.

**Annotated teaching version — guide-added comments** (class excerpt and selected driver lines; 01:21):

```python
class Item:                           # Define the kind of object used for each node.
    _object = None                    # Default: no stored fruit value.
    _next = None                      # Default: no following node.
    _prev = None                      # Default: no preceding node.
    _iter = None                      # Default: no saved iteration position.

    def __init__(self, obj = None):   # Item("apple") supplies apple; Item() uses None.
        self._object = obj            # Set the stored value on THIS instance.

    def object(self):                 # Ask this particular node what it holds.
        return self._object           # Return the stored object, not the enclosing node.

# Selected from the driver, not consecutive lines:
fruit = Item( "apple" )               # Create an apple node; fruit refers to it.
two = Item( )                         # Create a different, empty node; two refers to it.
```

The instructor asks why **`__new__`** matters when there is no such definition in this file. Its inherited implementation creates the instance; **`__init__`** then initializes that already-created instance. The instructor describes asking the operating system for memory and receiving the address of its first byte. That is a model for *why an object can be located*, not a requirement that Python make an OS allocation for every object or pass an integer into this initializer. In Python code **`self`** is the instance. It is the conventional name of the first method parameter, not a reserved keyword like Java’s `this`. A separate instrumented class verified the order: its creation hook ran before its initializer, which received an instance of the class. The lesson’s Item does **not** override the creation hook.

The low-level picture is byte-addressable memory: even if the machine reads several bytes together, a byte can be identified by its address. The instructor recalls the earlier Session 14 discussion and the rule of thumb that a type tells the implementation how to interpret an object’s storage. The language-level picture here is four *names* with readily available defaults. The four assignments are in the class body, not four per-instance assignments in the initializer. Reading an unset instance link finds its class default; assigning a link on one instance changes that instance, not every Item. None of the construction or assignment statements prints a result.

**Key items to scan**

- **`class Item:`** — defines the node type; calling it constructs a node rather than calling a separate function named Item.
- **`two = Item( )`** — binds **`two`** to a new, empty node; **`fruit`** still refers to its original node.
- **`__new__`** — creates the instance; inherited here. **`__init__`** — initializes that instance with the supplied fruit or the default.
- **`self`** — the actual instance received by this method; not an integer to use in address arithmetic.
- **`_object`** — stored value; **`object()`** — returns that value. **`_next`** and **`_prev`** — forward and backward neighbor references. **`_iter`** — saved iteration position, separate from those links.

## At index 1: a banana node or the word “banana”? (01:25–01:34)

The question shifts from “where does this object live?” to “what should this caller get back?” The editor first contains **`x = fruit[1]`**; by 01:26 it also contains **`y = fruit.at(1)`**. They express a similar lookup intention, but writing brackets does not implement subscripting. The screens here establish the named method, not a successful run of either new line or a definition of a matching subscript method. Trace the named call on its own. The receiver is the apple node referred to by **`fruit`**. Its **`first()`** method finds the first node even if asked from somewhere farther along the chain; its answer is a node, not its fruit string.

**Annotated teaching version — guide-added comments** (01:26–01:28; the exact right-hand end of the exception message is hidden, so the teaching version deliberately uses a bare exception):

```python
def first(self):                      # Start at the node the call was made on.
    iter = self                       # For fruit, this is already apple.
    while True:                       # Otherwise, keep trying to move left.
        if iter._prev == None:        # No predecessor: this is the first node.
            break                     # Do not step beyond it.
        iter = iter._prev             # Move one node toward the beginning.
    return iter                       # Give the first node itself back to the caller.

def at(self, n):                      # Request position n, counting the first node as 0.
    iter = self.first( )              # Start at apple for the three-fruit example.
    if iter._object != None:          # The empty placeholder skips the search.
        count = 0                     # Apple is position 0.
        while count < n:              # To reach 1, move forward exactly once.
            if iter._next == None:    # Still need to move, but have reached the tail?
                iter = None           # Clear this local reference, not the tail node.
                raise Exception       # Classroom code supplies a partly hidden message.
            iter = iter._next         # Change our local position from apple to banana.
            count += 1                # Banana is position 1.
    return iter                       # Return a node; no string extraction or printing.
```

| Call step | Current position | Decision / result |
|---|---|---|
| Find the first node | apple, no predecessor | Return the apple node to the local variable in `at`. |
| Count 0, need 1 | apple has a successor | Move to banana and set count to 1; the chain itself is unchanged. |
| Count 1, need 1 | banana | Stop; bind `y` to the banana node. Assignment produces no output. |
| Ask the result for its value | `y.object()` | Returns the string `banana`. An outer print would display it. |

This behavior is not native-list indexing in disguise. In a separate fixture consistent with the visible guards, index 0 returned apple, index 3 on the three fruits raised an exception, and index −1 returned the first node because the loop never started. Asking the empty placeholder for index 1 returned that placeholder because the outer guard skipped the loop. These are consequences of the shown code, not additional classroom tests or a recommended index contract.

What makes a node return useful? A student says the literal grape string seems the most natural *result*. Another asks what happens when the information becomes richer than a single fruit on standard output. Both are reasonable requirements. If the caller receives just the fruit, it can use the fruit immediately. If it receives the node, it can obtain that same fruit **and** examine the neighbors. The fruit value alone need not identify the node that held it. The suggestion that an address might contain several values is resolved by distinguishing a byte location from an object with several named attributes: the same byte location does not simultaneously hold two different byte values; a structured object can refer to several other objects. In Python, returning the stored string still returns an object reference, just a reference to a *different object*.

**Annotated teaching version — guide-added comments** (the two accessors are visible at 01:21–01:25; the final three lines are an independently tested use, not classroom output):

```python
def after(self):                      # Request the next node, not its stored fruit.
    if self._next != None:            # Banana has a successor.
        return self._next             # Return the grape node.
    return self                       # At the tail, stay on that node.

def before(self):                     # Request the previous node.
    if self._prev != None:            # Banana has a predecessor.
        return self._prev             # Return the apple node.
    return self                       # At the head, stay on that node.

y = fruit.at(1)                       # Save the banana node; fruit still refers to apple.
print(y.object())                     # Display its stored fruit: banana.
print(y.before().object(), y.after().object())  # Display apple grape.
```

**Output — independently verified values for these lookups:** `banana`; then `apple grape`.

The search method **`has`** makes a different starting request—find a value rather than an index—but this version also returns a node on success. In the driver, the search for banana, apple and grape is followed by **`one.object()`**; the two kiwi failures are printed *without* calling a method on them. The instructor contrasts this design with returning a Boolean, supplying separate node/value operations, or returning both. The method name alone does not settle its contract. The five search results were already in terminal scrollback; they are not evidence of either new index assignment running.

The complete search driver and its two distinct misses are worked just above. The new question here is what a successful lookup allows **after** it returns: the node retains links that the string alone does not.

**Key items to scan**

- **`x = fruit[1]`** — bracket notation typed into the editor; no corresponding completed run in these screens.
- **`y = fruit.at(1)`** — on the worked three-node chain, bind `y` to the banana node, leaving **`fruit`** at apple.
- **`first()`** — follow `_prev` until no predecessor exists; return that node. The local `iter` here is just a variable naming the current node.
- **`count < n`** — one hop for position 1; zero hops for position 0. A needed hop beyond the tail raises instead of returning a node.
- **`before()`** and **`after()`** — from banana return the apple and grape nodes respectively; at the corresponding boundary they return the current node, not `None`.
- **`has`** — this version returns the first matching node or `None`, not a Boolean. **`one.object()`** — extract the stored fruit from a successful result.

### What the helper methods do with the same links (screen evidence, 01:21–01:35 and 01:39–01:40)

Several methods pass through the editor while the class discusses references. Read them as different answers to “which object do I need next?” They are screen evidence, not a claim the instructor executed all of them. A node containing no fruit can be *reused*. An occupied node needs a *new* neighbor. Appending banana to apple, for example, does not replace the object referred to by `fruit`: it connects two nodes and returns the new banana node. Prepending does the mirror operation at the other end. The following teaching extracts use class method bodies; they are not one standalone script.

**Annotated teaching version — guide-added comments** (01:21, 01:24, 01:27:06):

```python
def last(self):                       # Find grape, even if called from banana.
    iter = self                       # Start at the supplied node.
    while True:
        if iter._next == None:        # No following node: keep this node as the answer.
            break
        iter = iter._next             # Otherwise take exactly one forward step.
    return iter                       # Return the last node, not the last fruit string.

def append(self, obj):                # Add obj at the far end, not next to the receiver.
    if self._object == None:          # An empty receiver can hold the first fruit itself.
        self._object = obj            # Fill that same node.
        newItem = self                # No new node was created on this branch.
    else:
        last = self.last()            # For the first append, the old tail is apple.
        newItem = Item(obj)           # Make a NEW banana node.
        newItem._prev = last          # Banana now points back to apple.
        last._next = newItem          # Apple now points forward to banana.
    return newItem                    # The caller may keep the node that now holds banana.

def prepend(self, obj):               # Add at the beginning instead.
    if self._object == None:          # Empty receiver: same reuse rule as append.
        self._object = obj            # Put the first value here.
        newItem = self                # Return this same object below.
    else:
        first = self.first()          # Locate the old head, e.g. apple.
        newItem = Item(obj)           # Make a different node for the new first value.
        newItem._next = first         # New head points forward to apple.
        first._prev = newItem         # Apple now points back to the new head.
    return newItem                    # The former head is still a valid node.
```

Inserting is local rather than global. Suppose apple, banana and grape are already connected. To place kiwi immediately *after banana*, first give kiwi its two neighbors, then make those neighbors point back to kiwi. **`insertBefore`** performs the mirror operation. A missing neighbor is not an error: at an endpoint there is only one pair of links to install. This transfer example was tested on a separate fixture; the classroom driver does not insert kiwi.

**Annotated teaching version — guide-added comments** (01:39–01:40):

```python
def insertAfter(self, obj):           # Insert beside THIS node, not necessarily the tail.
    if self._object == None:
        self._object = obj            # Fill an empty placeholder, e.g. with kiwi.
        newItem = self                # Return the same node rather than extending the list.
    else:
        newItem = Item(obj)           # Allocate a new kiwi node.
        if self._next != None:        # With banana as receiver, grape is the old successor.
            newItem._prev = self      # Kiwi points back to banana.
            newItem._next = self._next  # Save grape before replacing banana's next link.
            self._next._prev = newItem  # Grape now points back to kiwi.
            self._next = newItem      # Banana now points forward to kiwi.
        else:
            newItem._prev = self      # Inserting after the tail: no grape-side link to fix.
            self._next = newItem      # Old tail and new tail now point to each other.
    return newItem                    # Return the inserted node, not its string.

def insertBefore(self, obj):          # Mirror the operation on the receiver's left.
    if self._object == None:
        self._object = obj            # Reuse an empty receiver.
        newItem = self                # No new object was required.
    else:
        newItem = Item(obj)           # Make the new node.
        if self._prev != None:        # E.g. inserting before banana, with apple before it.
            newItem._next = self      # Kiwi points forward to banana.
            newItem._prev = self._prev  # Kiwi points back to apple.
            self._prev._next = newItem  # Apple now points forward to kiwi.
            self._prev = newItem      # Banana now points back to kiwi.
        else:
            newItem._next = self      # Inserting before the head: only one neighbor.
            self._prev = newItem      # Old head points back to the new one.
    return newItem                    # Return the node now holding the inserted object.
```

Extraction works in the opposite direction. At 01:35, the visible *tail* of a method shows two alternatives: if the removed node has a predecessor but no successor, the predecessor becomes the tail. If it has both neighbors, reconnect them to each other. Then disconnect the removed node and return a reference into the *remaining chain*. This is not the same as clearing the node’s fruit. The later checkout at 01:40 separately shows the start of **`extract`**, including a one-node case that creates a fresh empty Item for the remainder. The first-node-with-successor body is not visible here; these are supported fragments rather than a complete single-revision algorithm.

**Annotated teaching fragments — guide-added comments** (early method tail; later opening separately):

```python
# Earlier tail, in the branch with a predecessor (outer guard off screen):
if self._next == None:                # Removing the tail, e.g. grape after banana.
    self._prev._next = None           # Banana becomes the end.
    rest = self._prev                 # Keep a way to reach the remaining nodes.
else:                                 # Middle case, e.g. remove kiwi between banana and grape.
    self._prev._next = self._next     # Banana points forward to grape.
    self._next._prev = self._prev     # Grape points back to banana.
    rest = self._prev                 # The remaining chain is accessible from banana.
# Common exit in the method, outside those branches:
self._prev = None                     # The removed node no longer points left.
self._next = None                     # Nor right; its own payload was not erased.
return rest                           # Return a node in the remainder, not the removed payload.
```

```python
# Later opening, expressed as a teaching fragment (clipped punctuation supplied):
rest = None                           # Start without a remainder reference.
if self._prev == None and self._next == None:  # Only this one node exists.
    rest = Item( )                    # Create a NEW empty node for the remaining list.
```

**Independently verified state, not classroom output:** inserting kiwi after banana yielded apple → banana → kiwi → grape. Extracting kiwi in that fixture restored apple → banana → grape, returned the banana node and left the detached kiwi node holding `kiwi` with neither link set. The unobserved first-node-with-successor case was not used or reconstructed for that test.

**Key items to scan**

- **`last()`** — walks forward to the node with no successor; **`append`** uses it to add at the end.
- **`prepend`** — links ahead of the old first node, or fills an empty receiver in place.
- **`insertAfter`** — place a new node immediately after the receiver; **`insertBefore`** — place it immediately before, keeping the other direction of travel valid.
- **`extract`** — visible branches separate a node from its neighbors without clearing the node’s own payload; **`rest`** names a route into what remains.

### Three ways to walk the same fruit (screen evidence, 01:26, 01:30–01:35)

Counting, visiting, and iterating have different results even though they all start at the first node. Counting returns *one number*. Visiting calls a method supplied by another object *once per node*. Iteration returns *one stored fruit per request*, while saving where the next request should begin. Here the local variable named `iter` in the visitor and counter is not the persistent **`_iter`** attribute maintained by the iterator methods.

Compare the visitor, iterator and count implementations in the earlier worked traversal. Here the revealing test is the *empty placeholder*, not the familiar three-fruit path.

**Independently verified:** the callback received apple, banana, grape in that order. The iteration requests returned apple, banana, grape, then stopped on the fourth request. The count was 3. On an empty placeholder, the count was 0 but the shown iterator yielded `None` once: it tests for a missing *node*, not for a missing *payload*. No count, visitor or iteration results are shown in the classroom terminal here.

**Key items to scan**

- **`count()`** — includes an occupied first node before counting the forward moves.
- **`forEach(self, caller)`** — visits nodes; **`caller.withItem(iter._object)`** passes a value, not a node, to another object’s method.
- **`__iter__`** — resets **`_iter`** on the head; starting another traversal of the same chain resets that same position.
- **`__next__`** — saves the current value, advances, then returns the saved value. **`StopIteration`** — signals there is no next node to serve.

## What survives `clear()`? (01:35–01:47)

Before studying clearing, the instructor restores the two edited files, checks out an earlier revision and asks to reload both editor buffers. At 01:36 the terminal identifies the move from **`0ec6bc1`** to **`761d45c`**; the right pane now builds apple, banana, grape and calls **`fruit.clear( )`**. The five old search results remain above the Git messages. They are not output from clearing. The instructor asks what “clear everything” should leave: no variable? No object? Or a usable *empty list*? The class settles on the last answer: the name still refers to an Item, but it has no fruit or neighbors.

An Item contains references to other objects; following a link is different from owning an independently allocated copy of everything downstream. The instructor introduces **`del`** and garbage collection in this context. More precisely, deleting a name removes *that binding*, not every other reference to the object, and does not instruct Python to recursively delete all its attributes’ targets. A separate alias test kept an apple node alive and readable through a second name after the first name was deleted. Similarly, assigning a link to **`None`** drops that link’s former reference; it does not prove the target’s total reference count is zero. Nor is Python syntax preventing reassignment of **`self`**: changing or deleting the local parameter would not remove the caller’s binding. Clearing the object in place has the effect the caller needs. The visible clearing method contains no `del` statement.

**Annotated teaching version — guide-added comments** (01:41–01:47; the first debug print’s closing punctuation is supplied for this runnable teaching version):

```python
def clear(self):                       # Empty the connected list without rebinding fruit.
    iter = self.first( )               # Begin at apple even if asked from a later node.
    while iter._next is not None:      # Is there a live successor to move to first?
        iter = iter._next              # Move to banana; apple is now the predecessor.
        print( f"clearing: {iter._prev._object}" )  # Report apple BEFORE erasing it.
        iter._prev._prev = None        # Remove the old node's backward link.
        iter._prev._next = None        # Remove the old node's forward link to our current node.
        iter._prev._object = None      # Empty the old node's stored fruit; iter stays put.
    print( f"clearing: {iter._object}" )  # The final node has no successor: report grape here.
    iter._prev = None                  # Break grape's remaining link back to banana.
    iter._object = None                # Empty grape as well. Its next link was already None.
```

The guard **`is not None`** asks whether this link is the singleton meaning “no next node.” The instructor contrasts the readability of English-like identity operators (**`is`**, **`not`**) with the C-style inequality spelling **`!=`**, alongside other Python keywords such as **`and`** and **`or`**. The screen at 01:41 shows both spellings *in different methods*: `!= None` in the search guard, `is not None` in the clear loop. They have similar outcomes for these simple nodes; inequality compares values and can be customized, while identity asks whether something is that very object.

**Trace — the three-fruit case, derived from the shown code and verified in a separate fixture:**

| Stage | Where `iter` is | Diagnostic line | Which references or values change? |
|---|---|---|---|
| Enter | apple | none yet | Find first; apple and banana are linked, as are banana and grape. |
| Loop pass 1 | moves to banana | `clearing: apple` | Target is banana’s predecessor, apple. Its backward link was already `None`; clear its forward link and payload. Banana still points back to that emptied node. |
| Loop pass 2 | moves to grape | `clearing: banana` | Target is grape’s predecessor, banana. Its backward link still points to the emptied apple node; now clear that backward link, its forward link and its payload. |
| Guard fails; run the tail code | grape | `clearing: grape` | No next node to step to. Clear grape’s backward link and payload. All three old nodes are empty and disconnected. |
| Return to caller | `fruit` still names the original apple node | caller prints its state | The caller did not receive a replacement object; its node has payload `None` and next link `None`. |

Why does the instructor move forward only to reach *backward*? If the code severed apple’s forward link and then tried to use that link to reach banana, it would have lost the path. The saved position on banana keeps the traversal going. A student spots that clearing the predecessor’s previous link is redundant on pass 1. Correct—but on pass 2 banana still has a predecessor, so the same line is necessary. Similarly, clearing the predecessor’s *next field* removes a link pointing to the current node; it does **not** change the local variable that still refers to that current node.

**Annotated teaching version — guide-added comments** (new driver at 01:36):

```python
from item import Item                 # Import the version with the clearing method.
b = "banana"                          # The variable refers to the banana string.
fruit = Item( "apple" )               # Create a NEW first node for this driver.
fruit.append( b )                     # Connect a banana node after apple.
fruit.append("grape")                 # Connect a grape node after banana.
fruit.clear( )                        # Mutate the connected nodes; debug prints come from here.
print(f"cleared: {fruit}, {fruit.object()}")  # Show the surviving object and its now-empty payload.
print( fruit._next )                  # Show the former first node's next link: None.
```

**Independent run of the reconstructed teaching fixture, not a classroom run** (module name and object address reflect that particular test):

```text
clearing: apple
clearing: banana
clearing: grape
cleared: <item_fixture.Item object at 0x0000019776652BA0>, None
None
```

The first three lines are debugging prints inside the method; the last two come from the driver. The first former node survives because `fruit` still names it. If another name still refers to the former banana or grape node, that object also survives, but now empty. That was verified by retaining aliases to both nodes; it is not necessary to observe the garbage collector to explain the result. With a single node the loop does not run; the tail code still reports and empties it. A separately tested already-empty node therefore produced `clearing: None`. The method has no explicit return and does not reset `_iter`; the demonstration builds a fresh chain, not one mid-iteration.

The instructor notices the test print left in committed code: every call now reports to standard output whether the caller wants that or not. A student asks about removing it. The instructor is looking at history and leaves it in place; a branch and later merge are discussed as a way to preserve a deliberate historical change, not actions performed on screen. Unit tests, functional tests and testing utilities are noted as subjects to revisit, not demonstrated. At approximately 01:47 the next question is posed: what would a “to-list” operation do? Distinguish converting this home-grown doubly linked representation to Python’s built-in list from appending another value to the original structure; the screen still shows the clearing driver at 01:47.

**Key items to scan**

- **`0ec6bc1`** — earlier search revision in terminal history; **`761d45c`** — checkout associated with the clearing driver. Reloading both files matters before interpreting the panes.
- **`fruit.clear( )`** — empties existing linked nodes; does not rebind the caller’s `fruit` to `None` or a new list.
- **`del`** — can remove a name; another alias can keep the object accessible. **`self`** — changing the local name does not change what the caller’s name refers to.
- **`None`** — here means an absent neighbor or empty payload, not proof that another object has been deallocated.
- **`is not None`** — identity guard for a live successor. **`is`** tests identity; **`not`** negates. **`!=`** tests inequality. **`and`** and **`or`** are other word-form operators mentioned in contrast.
- **`iter._prev._prev`** — the old node’s backward field; already absent for apple but still meaningful for banana.
- **`iter._prev._next`** — the old node’s forward field; clearing it does not destroy the local reference to the new current node.
- **`iter._prev._object`** — old node’s payload, printed before erasure. The tail’s own payload is cleared outside the loop.
- **`print( fruit._next )`** — caller’s check for the original first node’s missing forward link, distinct from the method’s diagnostics.

## Keep the fruit, change the container (01:48–01:54)

Can we take the values out of the custom doubly linked list and use them as an ordinary Python list? Yes, without taking the linked list apart. The earlier search and clear methods help explain the choice of traversal: a search can stop at the first match; a clear operation disconnects nodes. A conversion must visit **every occupied node in order and leave every link intact**.

**Annotated teaching version — guide-added comments** (01:48 driver; 01:49–01:50 method). The two blocks are the driver and the method inside the class, not consecutive lines of one file.

```python
from item import Item              # Load the classroom node/list type.
b = "banana"                       # Name the second value.
fruit = Item( "apple" )            # Create the first node; fruit refers to apple.
fruit.append( b )                  # Link banana after apple; fruit still refers to apple.
fruit.append("grape")              # Add grape at the end of that same chain.
list = fruit.toList( )             # Bind the returned Python list to the name list.
for item in list:                  # Read values from the NEW Python list, not from the nodes.
    print( item )                  # Expected lines: apple, then banana, then grape.
```

```python
def toList(self):                      # Called on fruit; build a separate container.
    list = []                          # A fresh, initially empty Python list on EACH call.
    iter = self.first( )               # Begin at apple even if called on the grape node.
    if iter._object is not None:       # An empty Item has no first value to copy.
        while True:                    # At least one value is available; test the link below.
            list.append(iter._object)  # Copy the VALUE reference, not the Item node.
            if iter._next == None:     # Grape has just been copied. Is it the last node?
                break                  # Yes: leave the loop without moving past it.
            iter = iter._next          # Otherwise move the local position to the next node.
    return list                        # Give the caller the new list; this does not print it.
```

| Node being inspected | Value appended | Python list after append | Next action |
|---|---|---|---|
| apple | `"apple"` | `["apple"]` | Follow link to banana. |
| banana | `"banana"` | `["apple", "banana"]` | Follow link to grape. |
| grape | `"grape"` | `["apple", "banana", "grape"]` | No successor: break and return. |
| Empty `Item()` instead | none | `[]` | Guard skips the loop; return the empty list. |

The copy is of the *container*: **`toList()`** does not clone each stored object. The expected printed order above follows the method and was checked with an independent fixture. The old terminal text on the left at 01:48 is **not** evidence of this driver running.

**Key items to scan**

- **`toList()`** — allocates a Python list each time; it does not clear the source.
- **`self.first( )`** — starts at the first linked node, wherever the caller was in the chain.
- **`iter._object`** — the stored value; **`iter._next`** — the link used to advance.
- **`list.append(iter._object)`** — uses the built-in list's append; the tail value is copied before the stop test.
- **`return list`** — returns the separate container; the driver's **`print( item )`** displays its values.

### Reverse the conversion: which object is `self`?

At 01:49–01:53 a student predicted that “from list” would pull something out of a list. More precisely, **`fromList()`** reads the Python list without removing anything. Python already knows how to iterate that input: no manual indexing is needed. The receiving **`Item`** supplies the other half of the operation.

**Annotated teaching version — guide-added comments** (01:50; method and driver excerpts).

```python
def fromList(self, list):       # In fruit.fromList(list), self is fruit; list is the argument.
    for item in list:           # item is a string this pass: apple, banana, then grape.
        self.append(item)       # Use Item.append to grow fruit, not list.append to grow the input.
```

```python
list = ["apple", "banana", "grape"]  # Original Python container.
fruit = Item( )                      # NEW, empty custom chain; no values yet.
fruit.fromList( list )               # Fills fruit; returns no explicit value. Don't rebind fruit.
for item in fruit:                   # Now iterate the custom chain, not the Python argument.
    print( item )                    # One stored fruit per line.
```

**Observed output** (single-loop run, 01:53:20; the command is legible in later scrollback as **`py .\test\fruit.py`**):

```text
apple
banana
grape
```

The class question was whether **`self`** was the input list. Read the expression to the left of the dot: **`fruit.fromList( list )`** binds the method to **`fruit`**. The local lowercase **`item`** changes from one input string to the next; it is not the class **`Item`**. At 01:52 the editor even offers a definition of **`append`** at **`item.py:23`**. A highlighted matching spelling is not proof of which method runs: look at the object before the dot. The first append fills the empty node with apple; later ones attach banana and grape. The method ignores the node returned by each append. No assignment or deletion empties the source list.

At 01:54 the copied second loop *initially also named fruit*. The point of the edit was to change only the source of that second loop:

```python
for item in fruit:             # First loop: read the custom chain.
    print( item )              # apple, banana, grape on separate lines.
for item in list:              # Revised second loop: read the original Python list instead.
    print( item )              # Same three lines again; neither container was consumed.
```

**Observed output** of the separate six-line run (visible in 01:56 scrollback; the later function draft is not part of this result):

```text
apple
banana
grape
apple
banana
grape
```

**Key items to scan**

- **`fruit`**, **`self`** — the custom receiver in **`fruit.fromList( list )`**; its chain gains the values.
- **`fromList()`** — appends to the receiver; it neither returns a replacement chain nor removes the input.
- **`Item`** — class name; **`item`** — one current value, such as `"banana"`.
- **`append`**, **`item.py:23`** — the editor points to the classroom definition; **`self.append(item)`** uses it, whereas the earlier **`list.append(...)`** acted on a built-in Python list.
- **`py .\test\fruit.py`** — runs the driver; one printing loop produced three lines, two produced six.

## When `list` stops meaning the list constructor (01:54–02:03)

The six lines were correct. The instructor's “very bad” choice was a *name*, not a failed conversion. In this driver, assigning a list literal to **`list`** hides the built-in class of the same name. Defining a function with that name changes the binding yet again. It does not alter Python’s built-in list class for everyone. The order here is top-to-bottom execution of module statements, not arithmetic operator precedence.

**Annotated teaching versions — guide-added comments.** Each labeled change below belongs to a *separate* driver state. In each error run the first loop over **`fruit`** had already printed apple, banana and grape.

```python
# 01:56: after assigning the three-fruit list and printing fruit
def list( ):                   # NOW list names a function object, not the three-fruit list.
    pass                       # Empty body; defining it does not call it.
for item in list:              # Requests iteration of the FUNCTION itself: fails here.
    print( item )              # No lines from this loop.
```

**Observed error**, line 13: **`TypeError: 'function' object is not iterable`**. This is not an attempt to iterate the function's return value: there are no call parentheses after the name in the loop.

```python
# 01:59: inserted BEFORE the def list statement, after printing fruit
list()                         # list still names the three-fruit container. Try to call it: fails.
```

**Observed error**, line 9: **`TypeError: 'list' object is not callable`**. An *iterable* can supply successive values to a loop. A *callable* can be invoked using parentheses. The built-in class can be called to create a list, but an ordinary three-fruit list object cannot. Because execution stops here, it never reaches the later function definition.

```python
# Another run, preserved in 02:00:09 scrollback, line 9
q = list()                     # The right side still fails; assigning to q cannot rescue it.
```

**Observed error:** **`TypeError: 'list' object is not callable`** again. At 02:00:00 the *editor* briefly shows that assignment both above and below the list literal (lines 3 and 11). The observed line-9 error belongs to the earlier run, not to this two-call buffer.

```python
# 02:00:09 onward: the lower call has been removed.
q = list()                          # Before shadowing: call the BUILT-IN; q receives [].
list = ["apple", "banana", "grape"] # Now the same spelling refers to a list INSTANCE.
fruit = Item( )                     # Create a fresh empty Item for this run.
fruit.fromList( list )              # Append the three actual fruit names to it.
for item in fruit:                  # Iterate fruit while list still names the Python input.
    print( item )                   # Three successful prints before any error.
def list( ):                        # Now replace the driver's binding with a FUNCTION.
    pass                            # If called it would finish without an explicit return.
for item in list:                   # Line 15 in the editor: a function is not an iterable.
    print( item )                   # No fourth fruit or second set of output.
```

**Observed output and final error, traceback abbreviated** (full last traceback visible at 02:04):

```text
apple
banana
grape
TypeError: 'function' object is not iterable
```

| Last binding executed in the final version | What `list` refers to now | What the program can do at this point |
|---|---|---|
| None in this module yet | Built-in list class | `q = list()` constructs `[]`; nothing is printed. |
| Assignment of the literal | The Python list of three strings | Iterate it or pass it as data; don't call it. `q` is still a separate empty list. |
| `def list( ):` | A function object | Could call it, but `for item in list:` asks for iteration and fails. |

A student thought the variable began with an uppercase L. At 01:56–01:58 the instructor traced that impression to the editor's font and syntax highlighting: **`list`** has the same lowercase spelling in the definition and loop. The instructor tried a fresh buffer, which still recognized Python, and recommended occasionally reading code without coloring to test whether you recognize identifiers, keywords, operators and punctuation yourself. Color is not a runtime rule. In new code, a name like **`fruit_names`** would leave the constructor available.

The class also recalled container literals. **`[]`** and an unshadowed **`list()`** both make empty lists. A precision point on the spoken shorthand (independently checked): **`()`** is an empty tuple, whereas **`(1)`** groups an integer and **`(1,)`** makes a one-element tuple. **`{}`** is an empty dictionary; **`set()`** is needed for an *empty* set, but **`{"apple"}`** is a valid nonempty set literal.

**Key items to scan**

- **`list`** — one spelling, successively a class, a particular list, then a function in the final driver.
- **`fruit`** — separate custom chain; its successful prints precede every traceback described here.
- **`q = list()`** — stores a newly constructed empty list only if name lookup still finds the built-in class.
- **`pass`** — an empty function body; does not put any items into the function or call it.
- **`TypeError: 'function' object is not iterable`** — the loop used the function as its data source.
- **`TypeError: 'list' object is not callable`** — parentheses asked a three-fruit list instance to act like a constructor.
- **`fruit_names`** — guide-suggested replacement for the input variable, not a classroom edit.
- **`[]`**, **`list()`** — empty lists; **`()`**, **`(1)`**, **`(1,)`** — empty tuple, integer, one-item tuple respectively.
- **`{}`**, **`set()`**, **`{"apple"}`** — empty dictionary, empty set, set containing apple.

## Walk the saved versions: a generator and two meanings of “next” (02:03–02:06)

The instructor restored the two edited files and recalled checkout commands with the command-line history (Up Arrow), responding to a student's question about toggling between revisions. The visible history explains why a generator suddenly appears where the class expected another conversion. Moving from **`master~6`** toward **`master`** selects progressively more recent ancestors of the same branch, not new features written in that instant.

| Classroom PowerShell command | Visible result or purpose |
|---|---|
| `git restore .\src\item.py` and `git restore .\test\fruit.py` | Restore these working files before moving on from the naming experiment. |
| `git checkout master~5` | `843e915 generator` (from `52fc8cc fromList( list )`). |
| `git checkout master~4` | `2a9805c before/after back to prev/next`. |
| `git checkout master~3` | `dae3d01 toFile, fromFile`; the driver still illustrates the generator. |

**Key items to scan**

- **`master~6`** — the saved `fromList` version; local driver experiments need not be part of its commit.
- **`git restore`** — here targets two individual working files; it is not just a view operation.
- **`master`**, **`master~5`**, **`master~4`**, **`master~3`** — branch tip versus first-parent ancestor positions; the checkout result identifies which version the files now represent.
- **Up Arrow** — recall a prior terminal command for reuse or editing; not a Git command itself.

### `yield` pauses a traversal instead of building a Python list

The instructor specifically deferred a full generator lesson: when asked what made the method a generator, the class had not supplied **`yield`**. The implementation and test driver are nevertheless visible (02:04 driver; 02:07 implementation). Compare the line that used to append to the Python list with the line below that hands back one value and pauses. This is a reading of the screen code, not a claim that this generator driver was run here.

**Annotated teaching version — guide-added comments:**

```python
def genny(self):                    # Calling this gives the caller a NEW generator object.
    iter = self.first( )            # On the first request, locate apple, even if self is grape.
    if iter._object is not None:    # Empty chain: no values will be yielded.
        while True:                 # Continue until the yielded node has no successor.
            yield iter._object      # Give out one fruit and pause here; remember the position.
            if iter._next == None:  # On resuming after grape, recognize the end.
                break               # Finishing signals exhaustion, not a fourth value.
            iter = iter._next       # On other resumptions, move to banana or grape.
```

```python
fruit = Item( )                      # Start a new chain for this version.
fruit = fruit.append( "apple" )      # The empty node now holds apple; append returns that node.
fruit = fruit.append( "banana" )     # Same chain; fruit is rebound to its NEW banana node.
fruit = fruit.append( "grape" )      # Same chain; fruit now refers to the grape node.
for item in fruit:                   # Ordinary iteration over the custom sequence.
    print( item )                    # Expected: apple, banana, grape.
print( )                             # Separate the demonstrations with a blank line.
jenny = fruit.genny()                # Create the generator; no fruit has been requested yet.
print( jenny )                       # Displays the generator object, NOT its three values.
print( jenny.__next__( ) )           # First request: apple.
print( jenny.__next__( ) )           # Resume: banana.
print( jenny.__next__( ) )           # Resume: grape. A fourth print is commented out on screen.
```

The driver next creates a *new* generator with the same chain and prints that object. Its **`try`** block starts another series of next calls; **`except StopIteration:`** is visible, but a camera overlay obscures parts of the calls and the ending of the printed message. The following is a **guide adaptation**, not a transcription of the hidden lines:

```python
jenny = fruit.genny()                 # New traversal: does not resume the old one at grape.
try:                                 # Attempt four requests on a three-value generator.
    for request in range(4):          # A compact guide loop in place of the screen's print lines.
        print(next(jenny))            # The fourth next raises BEFORE print can display anything.
except StopIteration:                # Handle normal exhaustion of this manual traversal.
    print("exhausted")                # Guide-chosen message; not the screen's literal.
```

| Request on the fresh generator | What happens before the next pause | Result |
|---|---|---|
| 1 | Enter the method, find first node, reach `yield`. | `apple` |
| 2 | Resume after apple's yield, follow the link. | `banana` |
| 3 | Resume after banana's yield, follow the link. | `grape` |
| 4 | Resume after grape's yield, find no next node, leave method. | Raises `StopIteration`; handler runs. |

The three yields and fourth-request exhaustion were independently checked with the screen method in a small linked-node fixture. No runtime address for the generated object, or classroom generator output, is established by these frames.

**Key items to scan**

- **`genny()`**, **`jenny`** — the method creates a generator; the variable refers to that particular traversal, not to a fruit.
- **`yield`** — supplies one value now; the following link test waits until the next request.
- **`__next__`**, **`next(jenny)`** — request one value from the generator; distinct from a node's navigation method below.
- **`try`**, **`except StopIteration:`** — attempt successive requests and catch exhaustion, not an extra printed fruit.

### A node's neighbor is not an iterator's next value

At 02:04–02:05 the instructor recalled trying *before/after* as names to avoid confusing the node methods with Python's built-in **`next()`**. The commit message records a change back to *prev/next*. These methods belong to the classroom **`Item`** class; they don't redefine the module-level built-in the way the earlier list variable hid its namesake. Their behavior also differs: at an end, they return the same node, not an exhaustion exception.

**Annotated teaching version — guide-added comments** (02:05; excerpts from inside the class):

```python
def __init__(self, obj = None): # Older constructor: one optional starting value.
    self._object = obj          # Item("apple") stores apple; Item() stores None for empty.

def object(self):               # Ask this node for the stored value, not for another node.
    return self._object         # On the banana node this returns the string "banana".

def next(self):                 # Navigate from the current Item, e.g. banana.
    if self._next != None:      # Is another node linked after it?
        return self._next       # On banana, return the grape NODE, not the string.
    return self                 # On grape, stay at grape.

def prev(self):                 # Navigate one node backward instead.
    if self._prev != None:      # Is there an earlier neighbor?
        return self._prev       # On banana, return the apple node.
    return self                 # On apple, stay at apple.
```

**Key items to scan**

- **`Item`**, **`object()`** — a node is an object in its own right; the accessor returns its stored fruit.
- **`next()`** — the built-in requests a value from an iterator; **`fruit.next()`** in this class is node navigation.
- **`prev()`** — returns the previous node or the same node at the beginning; no built-in `prev` was needed for the example.

## Persist one value per line, then read it back (02:06–02:11)

The instructor asked the class to predict **`toFile()`** and **`fromFile()`** by analogy with the list methods. A file name is the input identifying *where* to write or read; it isn't the contents. The methods agree on a format: one string value per line. The file helper commit did not update the fruit driver to demonstrate them. The expected file contents below were verified separately, not observed in a classroom file.

**Annotated teaching version — guide-added comments** (02:09 originals show the full write expression):

```python
def toFile(self, filename):                 # filename names the destination, not an Item.
    with open(filename, "w") as file:       # Open for writing; an existing file is truncated.
        iter = self.first( )                # Begin at the first node, not necessarily self.
        if iter._object is not None:        # Empty chain: create/truncate the file but write no values.
            while True:                     # Write the current value before checking for the end.
                file.write(iter._object + '\n')  # Write e.g. apple followed by a newline.
                if iter._next == None:      # After writing grape, nothing follows it.
                    break                   # Finish; leaving with closes the file.
                iter = iter._next           # Otherwise proceed to the next node.

def fromFile(self, filename):              # Read into THIS chain; don't clear it first.
    with open(filename, "r") as file:      # Open the named text file for reading.
        for item in file:                  # Each pass gets one line, usually including its newline.
            self.append(item.strip())      # Remove end whitespace; append the resulting string.
```

**Independently verified file contents**, using apple, banana, grape in a fixture. Shown as a string representation so the final newline is visible, not as a classroom print:

```text
'apple\nbanana\ngrape\n'
```

Suppose a file instead contains a single line **`"  grape  \n"`**. Iteration reads that whole line; **`strip()`** returns **`"grape"`**, not a modified file. Appending to a chain already containing apple and banana produces apple → banana → grape. Leading and trailing spaces in a value are therefore *not* preserved. The writer concatenates a string with a newline; it is not a general serializer for arbitrary Python objects. Embedded newlines in a value would also defeat the one-value-per-line assumption.

A student asked about going from a Python list to a file, or from a file to a Python list, using the new methods. Yes, an **`Item`** can serve as an intermediary, but the instructor asked why use one if only the built-in list and the file are wanted. The suggested name “Jenny” was just a name in the question; it need not imply the generator. If using the classroom helpers, call them in sequence on the same object, **not** as a chained call on the return of the importing method:

```python
fruit = Item( )                         # Guide example: create an empty intermediate chain.
fruit.fromList(["apple", "banana"])     # Mutate fruit; the call itself returns None.
fruit.toFile("fruit.txt")               # Call on fruit, not on that None result.
restored = Item( )                      # A NEW chain to receive the file, not the original fruit.
restored.fromFile("fruit.txt")          # Append one stripped value per line into restored.
values = restored.toList()              # Expected: ["apple", "banana"]; no print implied.
```

**Key items to scan**

- **`toFile()`**, **`open(filename, "w")`** — opens the named destination before checking for data, writes string values, closes on leaving the block.
- **`'\n'`** — separates successive values and also terminates the last written value.
- **`fromFile()`**, **`for item in file:`** — read from the named file line by line; the method grows the existing chain.
- **`"  grape  \n"`**, **`strip()`**, **`"grape"`** — the line's whitespace disappears from the appended value, not from the source file.
- **`Item`** — optional intermediary; neither direction of ordinary Python-list/file conversion inherently needs a linked list.

## Build the chain from an object, a Python list, or a file (02:11–02:18)

At **`master~2`**, commit **`f23376c`**, the change isn't a method at the bottom of the class: it's how the object is constructed. The class compared an empty **`Item()`** with **`Item(stuff)`**. The second call has *one* positional argument. Even if that argument is itself a Python list, it goes into the first parameter as one stored object. That is different from initializing a node for each list element. The instructor wanted to avoid “create empty, then call a loader” boilerplate while preserving the earlier calls.

**Annotated teaching version — guide-added comments** (02:14–02:15:10):

```python
class Item:                                  # Class for nodes in the custom chain.
    _object = None                           # Default: no stored value.
    _next = None                             # No successor until linked.
    _prev = None                             # No predecessor until linked.
    _iter = None                             # Default slot for the class's iteration position.

    def __init__(self, obj=None, fromList=None, fromFile=None):  # All three inputs optional.
        self._object = obj                   # Set this instance's value first; apple, or None.
        if not fromList is None: self.fromList(fromList)  # If supplied, append its elements next.
        if not fromFile is None: self.fromFile(fromFile)  # Independently, append file lines LAST.
```

Notice the dots in **`self.fromList(fromList)`**: the qualified name is a *method*, while the bare name inside the parentheses is the supplied *argument*. **`self.fromFile(fromFile)`** likewise passes the filename to the loader. The two checks mean “was a value other than `None` supplied?”, not “does this container have at least one value?” An empty input list is supplied but contributes no nodes.

| Call (illustrative; `pylist` must already exist) | How its arguments bind | Result before any later append |
|---|---|---|
| `Item()` | All three default to `None`. | Empty custom list. |
| `Item("apple")` or `Item(obj="apple")` | Only `obj` receives apple. | One stored fruit. |
| `Item(pylist)` | `obj` receives the Python list itself. | One stored object: the entire list, not its separate strings. |
| `Item(None, pylist)` | Skip the initial object; supply `fromList` positionally. | One stored value per element; the 02:15 editor uses this form as an illustration, with `pylist` not defined there. |
| `Item(fromList=pylist)` | Supply the list by keyword; other defaults remain. | Same individual values, without the placeholder first argument. |
| `Item(fromFile=filename)` | Only the file argument is supplied. | One stripped string per input line. |

**Question from class:** What happens if a caller supplies *both* a list and a file? The instructor considered adding a check; the visible version has none. It uses two independent **`if`** statements, not exclusive alternatives, and neither loader calls **`clear()`**. In a guide fixture, starting with `obj="apple"`, supplying `["banana"]`, then a file containing `"  grape  \n"` produced **`['apple', 'banana', 'grape']`**. This is an independently checked consequence, not a newly imposed class requirement.

**Key items to scan**

- **`master~2`**, **`f23376c`** — saved version labeled `Construct Item from list or file.`
- **`Item()`**, **`Item(stuff)`** — create an empty chain versus one node storing the argument; `stuff` in the editor illustrates a name, not a demonstrated value.
- **`obj`**, **`fromList`**, **`fromFile`** — optional starting object, iterable input expected to be a Python list, and input filename respectively.
- **`self.fromList(fromList)`**, **`self.fromFile(fromFile)`** — methods on the new instance; the unqualified names provide their data.
- **`if`**, **`clear()`** — both independent conditions can succeed and neither clears; in the fixture the final order was **`['apple', 'banana', 'grape']`**.

### What grows, what moves, and what the final loop prints

The instructor returned to **`master`** and reloaded the working code. At 02:17–02:18 the quiz was what the loop would show after three appends. One student suggested apple. Apple is the *first* output, not the only one. Why, if **`fruit`** has just been assigned the result of appending grape? The chain has grown, and the name now refers to the new last node. Iteration over the custom chain can still begin at its first value. Notice that this is different from the earlier unassigned calls, which kept **`fruit`** referring to the original apple node.

**Annotated teaching version — guide-added comments** (02:17 left pane, 02:18 driver):

```python
def append(self, obj):                   # Add a value after the current last node.
    if self._object == None:             # First call on an empty receiver, e.g. with apple.
        self._object = obj               # Fill this existing node; no second node is needed.
        newItem = self                   # This is the node to return.
    else:                                # A value already exists, e.g. apple before adding banana.
        last = self.last()               # Locate the old tail; it need not be the receiver.
        newItem = Item(obj)              # Allocate a new node holding banana (later grape).
        newItem._prev = last             # Banana points back to apple.
        last._next = newItem             # Apple now points forward to banana.
    return newItem                       # Return the new end, not the first node or a copy.

fruit = Item( )                          # Create a new, empty custom list for this driver.
fruit = fruit.append( "apple" )          # Same original node, now containing apple.
fruit = fruit.append( "banana" )         # Banana is linked after apple; fruit now names banana.
fruit = fruit.append( "grape" )          # Grape is linked after banana; fruit now names grape.
for it in fruit:                         # Iterate all values, starting at the first node.
    print(it)                            # Expected lines: apple, banana, grape, not just grape.
```

**Expected output for the 02:18 driver:**

```text
apple
banana
grape
```

At 02:17 the scrolled right pane had shown **`print(len(fruit))`**. At 02:18 the driver instead shows the loop and the terminal shows a command at the prompt with no fresh result below it. Do not turn that old length line into part of the loop run. The instructor then returned to the idea of assigning the Python list returned by **`toList()`**.

The adjacent screen code also shows how the opposite insertion works. This is useful for predicting which reference moves and which nodes have to be relinked. The **`last()`** fragment below is limited to what is visible, not an independently recovered whole method.

```python
def prepend(self, obj):                  # Guide-added notes: insert at the FRONT instead.
    if self._object == None:             # Empty receiver has room for the first value.
        self._object = obj               # Fill it rather than create a redundant node.
        newItem = self                   # The receiver itself will be returned.
    else:                                # Nonempty example: banana is currently first.
        first = self.first()             # Save that original first node.
        newItem = Item(obj)              # Make a new apple node to insert in front.
        newItem._next = first            # Apple points forward to banana.
        first._prev = newItem            # Banana points back to apple.
    return newItem                       # Return the inserted front node.

def last(self):                          # Visible traversal fragment; ending is off-screen.
    iter = self                          # Start at the receiving node, wherever it is.
    while True:                          # Search forward until no link remains.
        if iter._next == None:           # At grape, there is no next node.
            break                        # Leave this loop with iter still on grape.
        iter = iter._next                # Otherwise move to the next linked node.
```

**Key items to scan**

- **`master`** — the terminal reports `Switched to branch 'master'` before the final driver experiment.
- **`fruit`**, **`return newItem`** — the driver can keep the returned end node or ignore it; either way the same chain gains its new value.
- **`append`**, **`prepend`** — write reciprocal links at opposite ends; both reuse an initially empty node for the first value.
- **`last()`** — the visible loop follows forward links and stops on the final node.
- **`print(len(fruit))`** — separate scrolled driver state, not a fourth line of loop output.
- **`toList()`** — return a separate Python list when that is the desired destination; no `print` is implied by conversion.

## Two ways to print the same three fruit (02:19–02:20)

The instructor asks what a second loop will show after converting the custom chain into a Python list. The class predicts apple, banana, grape again. The order is the same, but the loops are not reading the same container: the first reads **`fruit`**, the second reads **`pylist`**. Lowercase **`pylist`** is the spelling used in the driver; the instructor remarks on capitalization, not on a Python requirement.

**Annotated teaching version — guide-added comments.** The right-hand editor is `test\fruit.py`; these statements are visible together at 02:20.

```python
from item import Item                # Use the course's linked-item class.
fruit = Item( )                      # Start a NEW, empty collection for this run.
fruit = fruit.append( "apple" )      # Add apple; keep the returned Item reference.
fruit = fruit.append( "banana" )     # Add banana to the same chain; keep the return.
fruit = fruit.append( "grape" )      # The chain now holds apple, banana, grape.
for it in fruit:                     # Take the values from the custom collection.
    print(it)                        # Display one name per line.
pylist = fruit.toList()              # Get a NEW built-in list, in the same order.
for it in pylist:                    # Now take the values from the built-in list.
    print(it)                        # Display the same three names, not the list brackets.
```

**Observed PowerShell output** from **`py .\test\fruit.py`** (02:21; two-loop display):

```text
apple
banana
grape
apple
banana
grape
```

The left editor explains where the second set of values comes from. **`toList()`** collects the values before returning anything. By contrast, **`fromList()`** adds incoming values to the existing custom collection, and **`genny()`** offers one value at a time. These are visible methods; this driver does not call the latter two. In particular, seeing `genny` is not proof that the first loop calls it.

The list-conversion methods are unchanged from the preceding demonstration. This time the second loop is active: the new evidence is what it prints beside the first loop, not a new traversal algorithm.

Notice where the end test occurs: grape goes into the list (or is yielded) *before* the code tests whether another node exists. Also, the local name **`list`** refers to the result or argument in these methods, not the built-in constructor. This code makes no claim to copy the nodes themselves.

**Key items to scan**

- **`fruit`** — the course collection; each assignment keeps the reference returned by its custom append method.
- **`pylist`** — a separate Python list from the same three stored values; no second linked chain was constructed.
- **`toList()`** — starts with `[]`, walks from `first()` and returns a list even for an empty collection.
- **`list`** — local name used for the Python list in these methods.
- **`fromList()`** — calls `self.append(item)` for every input value; it does not replace the old contents.
- **`genny()`** — yields one stored value at a time; exhaustion happens on the request after the last yield.
- **`py .\test\fruit.py`** — runs the driver; the two `print(it)` loops account for its six lines here.

## A file write that prints nothing: how do you know it worked? (02:20–02:24)

The instructor adds **`fruit.toFile("fruit.dat")`** after the two loops. The terminal still shows the same six fruit names. A student points out that not getting an error is not the same thing as seeing the file. The instructor rejects the idea that the machine is smarter: it follows the literal instructions; the person has to decide what evidence would answer the question.

**Annotated teaching version — guide-added comments.** Writer from the left editor (write-mode body legible at 02:27), applied to the earlier filename.

```python
def toFile(self, filename):                     # Export the collection to the named file.
    with open(filename, "w") as file:           # Create the file or discard its old contents.
        iter = self.first( )                    # Start at apple in this example.
        if iter._object is not None:            # Empty collection? Write no lines.
            while True:                         # One occupied node per pass.
                file.write(iter._object + '\n') # Write the name plus a line ending, not to the screen.
                if iter._next == None:          # Grape was written; is this the last node?
                    break                       # Yes: the with-block will close the file.
                iter = iter._next               # Otherwise advance to banana, then grape.

fruit.toFile("fruit.dat")                       # Driver call. No print; fruit still has three items.
```

The file is opened *before* the empty guard, so an empty collection in write mode can still replace a file with an empty one. For the nonempty case, the professor checks two different things:

| Question | Observed PowerShell action | Evidence |
|---|---|---|
| Does the name exist in the current working directory? | `dir` (02:21:36) | `C:\com\joesta\Item` contains `fruit.dat`, length 22. The instructor says it was not there before; the listing shows its presence now. |
| What was actually stored? | `type fruit.dat` (02:22) | Three lines: `apple`, `banana`, `grape`. Not the six lines that the driver displayed. |

The instructor contrasts the Windows/DOS file-display spelling **`type fruit.dat`** with **`cat fruit.dat`**, a spelling also understood in this PowerShell. Do not transfer meanings mechanically between shells: the lesson is how to inspect the file, not that every command has the same meaning everywhere. When the class turns to reading the file in Python, the filename needs quotes. The 02:23 editor briefly has `fruit.fromFile(fruit.dat)`; the corrected call uses a string, not an attempt to look up an attribute named `dat` on `fruit`.

**Key items to scan**

- **`fruit.toFile("fruit.dat")`** — writes the three stored values; it does not print, remove, or double them.
- **`"w"`** — truncates an existing destination when opened, or creates a new one.
- **`file.write(iter._object + '\n')`** — sends text and a line ending to the file rather than standard output.
- **`dir`** / **`ls`** — directory listings in the demonstrated PowerShell; evidence of existence, not of contents.
- **`type fruit.dat`** / **`cat fruit.dat`** — read the text back independently of the Python driver's output.
- **`"fruit.dat"`** — the literal filename; dropping these quotes changes the expression, not just its presentation.

## Predict the read-back: two files, one mixed-up loop, six values (02:23–02:31)

Would writing the same filename replace it, merge it, or create another copy? Write mode replaces the contents. To distinguish new evidence from an old file, the instructor chooses **`fruit2.dat`** for *both* the write and the subsequent read. The previously created **`fruit.dat`** stays untouched. The old print loops are unnecessary now: only the display *after* the read should answer the next question. But commenting just the print in the remaining old loop prevents the program from starting.

**Annotated teaching version — guide-added comments.** Broken fragment at 02:25, then the repair seen by 02:27. These are different states.

```python
for it in pylist:              # Still promises an indented executable body.
    #print(it)                 # A comment does not count as that body.

fruit.toFile("fruit2.dat")     # Line 13: first active line encountered after that header.
```

**Classroom error:** `IndentationError`; the instructor reads “expected an indented block after the for statement.” The pointer at line 13 does not mean there is a bad filename. Line 10 has no body. The student who suggested checking whether the file was saved raised a useful diagnostic question, but saving the same broken text does not supply the missing statement.

```python
pylist = fruit.toList()         # Still builds the Python list; it is no longer printed.
#for it in pylist:              # Disable the loop header as well ...
    #print(it)                  # ... as its old body.
fruit.toFile("fruit2.dat")      # Write only the original three values to the new destination.
fruit.fromFile("fruit2.dat")    # Read into the SAME collection, which still has three values.
for it in fruit:                # Traverse the six items now in that collection.
    print(it)                   # One name per line; this loop alone prints all six.
```

**Annotated teaching version — guide-added comments.** The reader at 02:27–02:30 contains no reset.

```python
def fromFile(self, filename):         # Add text records to the receiving Item.
    with open(filename, "r") as file: # Read existing contents without writing to the file.
        for item in file:             # First line contains apple and its line ending.
            self.append(item.strip()) # Strip surrounding whitespace; append apple, banana, grape.
```

**Guide-derived trace of the overwrite trial** (confirmed by independent fixture). Each comma-separated row below represents a sequence of stored values, not a printed Python list.

| Step | File | Values in memory |
|---|---|---|
| After construction and three appends | Not yet used by this run | apple, banana, grape |
| Write to `fruit2.dat` | apple, banana, grape | apple, banana, grape |
| Read line 1 | Unchanged | apple, banana, grape, apple |
| Read line 2 | Unchanged | apple, banana, grape, apple, banana |
| Read line 3; reach end of file | Unchanged | apple, banana, grape, apple, banana, grape |

**Observed driver output (02:26)**:

```text
apple
banana
grape
apple
banana
grape
```

**Observed file output, `cat fruit2.dat` (02:31)**:

```text
apple
banana
grape
```

This resolves the student's “copy/paste” explanation: the *write* does not move items out of memory, and the *read* does not clear memory or change the disk file. The file was written before the last three values were added. The 02:26 directory listing shows both files at length 22. A possible switch to clear the collection before reading is discussed, not implemented here. Another student asks about rerunning the script: the new process builds a **new** three-fruit collection, and write mode discards the old disk contents. It does not start with the six items left in the previous process.

**Key items to scan**

- **`fruit2.dat`** — new test name used for both directions, so it cannot be confused with the original **`fruit.dat`**.
- **`IndentationError`** — the surviving `for` header has no executable body; disable both header and print.
- **`fruit.fromFile("fruit2.dat")`** — changes the existing collection in memory, not the file.
- **`item.strip()`** — removes surrounding whitespace, including the line ending; `append` receives the fruit name.
- **`"r"`** — read the file, one text line per iteration; a read never implicitly appends to the file.
- **`for it in fruit:`** — in the repaired driver, one loop over six values accounts for output that earlier came from two loops over three.

## Two kinds of append, and why the file is one group behind (02:31–02:39)

Now change only the writer's open mode from **`"w"`** to **`"a"`**. What survives another run? The file does. The newly constructed **`fruit`** still starts with apple, banana, grape every time. In the single-write driver, a run adds one fresh group of three to disk, reads the *entire* enlarged file into memory after the starting three, then prints memory. No comparison checks for duplicate fruit.

**Annotated teaching version — guide-added comments.** Changed implementation line (02:33:35; mode selected at 02:35) and driver order. The indented line is a method fragment, not a standalone `with` block.

```python
    with open(filename, "a") as file:  # Keep previous records; the existing write loop adds three.
```

```python
fruit.toFile("fruit2.dat")     # Add this run's starting three to the existing FILE.
fruit.fromFile("fruit2.dat")   # Add every line of that enlarged file to fruit IN MEMORY.
for it in fruit:               # Iterate memory, including its original three.
    print(it)                  # Standard output only; printing never reopens the file.
```

**Run trace, independently reproduced with the visible methods and a separate linked-node fixture.** Begin with the preceding overwrite trial's one group already on disk. “Group” means three consecutive lines, apple/banana/grape; it is not a nested data structure or an extra loop in the source.

| Writer mode | Lines on disk before → after write | Lines in fresh memory before → after read | Printed lines |
|---|---:|---:|---:|
| `"w"`, repeat control | 3 → 3 | 3 → 6 | 6 |
| `"a"`, first rerun | 3 → 6 | 3 → 9 | 9 |
| `"a"`, next | 6 → 9 | 3 → 12 | 12 |
| `"a"`, next | 9 → 12 | 3 → 15 | 15 |
| `"a"`, next | 12 → 15 | 3 → 18 | 18 |

The class tests a suspected counting mistake rather than explaining it away. The terminal at 02:32 and in the revisited scrollback at 02:36–02:37 shows successive driver outputs of four and five groups. At 02:33, reading the file gives four groups, not five. At 02:38 the driver prints six groups; the instructor then reports counting five in the file. The “one cycle behind” observation holds for this order of operations and the absence of a final save. It is not a rule of file I/O. Repeating the print loop alone cannot make a file grow; repeatedly running an appending writer can, eventually consuming disk space.

One student's request to “run fruit two” reveals another distinction. The command **`py fruit2.dat`** actually asks Python to execute the data. Python reads the first line as a name in a program, not as the string `"apple"`. For inspection, the student wanted **`cat fruit2.dat`** instead.

**Observed terminal error (02:33):**

```text
NameError: name 'apple' is not defined. Did you mean: 'tuple'?
```

**Key items to scan**

- **`"a"`** — file append; the operating system keeps old records and writes new ones at the end.
- **`fruit`** — memory-only starting collection is recreated each launch; `self.append(...)` adds values to it during reading.
- **`print(it)`** — writes to standard output; does not append to Python memory or the disk file.
- **`py fruit2.dat`** — interprets data as source, leading to **`NameError`** on `apple`.
- **`cat fruit2.dat`** — shows what is on disk without executing the lines or adding any new ones.
- **One group behind** — at the six-group display the initial in-memory group accounts for the difference from the five-group file.

## Give the writer a choice without breaking old calls (02:38–02:45)

A student suggests saving again after reading, so the file would match the larger list. Another wonders how much the list might grow with repeated appends. The effect depends on *which* write appends. By 02:40 the driver has a new final write at line 19, after the display loop, outside it. A hard-coded append at both calls would append the enlarged list as well as the initial three. The earlier one-group-behind trace no longer describes that program. The class did not establish a squaring rule.

Would callers have to specify a mode every time? At first, yes. The new parameter is required even though the body momentarily still uses a literal append mode.

**Annotated teaching version — guide-added comments.** Successive writer headers (02:41–02:43); excerpts, not adjacent definitions in one file.

```python
def toFile(self, filename, mode):          # Requires a second explicit argument besides the filename.
    with open(filename, "a") as file:     # Transitional body: still hard-coded, even if mode supplied.
```

```python
fruit.toFile("fruit2.dat")                 # Fails to bind mode; the body never opens the file.
```

**Classroom result (02:40–02:41):** missing required argument; `TypeError` and `'mode'` are visible in partially obscured scrollback. A separate Python fixture with a required parameter also raised `TypeError` before the body ran. This is a call-signature failure, not an invalid file mode.

```python
def toFile(self, filename, mode="w"):      # Old one-filename calls now default to overwrite.
    with open(filename, mode) as file:    # Use the caller's choice for the unchanged node-writing loop.
```

A student argues for append by default to avoid replacing accumulated records. The instructor chooses overwrite because existing callers assumed overwrite, not because it is always more common. Merely writing a default into the signature is insufficient: at 02:42 `open` still shows `"a"`; by 02:43 it receives **`mode`**. The instructor reports a return to two groups with the default. Then the driver exercises both choices:

```python
fruit.toFile("fruit2.dat", "a")  # First write: explicitly append this run's starting three.
fruit.fromFile("fruit2.dat")     # Load the whole updated file after those initial three in memory.
for it in fruit:                 # Display the resulting list before the final save.
    print(it)                    # No file change here.
fruit.toFile("fruit2.dat")       # Final write: overwrite with exactly what was just displayed.
```

**Independent fixture result (not classroom output):** starting with six lines on disk and a fresh three-fruit collection, the explicit append produced nine disk lines; reading made twelve in memory; the final default write left twelve on disk. Contrast the earlier single-write experiment where the file stayed behind. Could `a` come from the command line? The instructor agrees that **`sys.argv`** could supply it *if* the driver were written to read command-line arguments. A Python method argument is not automatically a shell argument; that extension is discussed, not demonstrated.

The instructor's next question concerns the **value** allowed through, not the parameter's name. The wrapper promises to write fruit *text*. Forwarding every string that `open` accepts gives callers choices the method may not be able to use. Two successive edits test that idea.

**Annotated teaching version — guide-added comments.** Alternative line 13 calls, not a single run:

```python
fruit.toFile("fruit2.dat", "x")   # Exclusive creation: cannot open this already existing file.
```

```python
fruit.toFile("fruit2.dat", "wb")  # Open for binary writing; the existing body still writes str.
```

**Observed versus independently verified:** the first edit is lowercase **`"x"`** (02:45). Contrary to the class's initial uncertainty, it is a real mode: exclusive creation. The next frame already has **`"wb"`** in the editor but shows a partially obscured `FileExistsError` in the terminal from the preceding experiment. In a separate run, `"x"` succeeded on a new path and raised `FileExistsError` on an existing one. Binary output is not intrinsically bad, as the instructor emphasizes, but it expects bytes. In the separate fixture `"wb"` opened and truncated the existing file, then `file.write` raised `TypeError: a bytes-like object is required, not 'str'` on the first fruit; the file was left at zero bytes. That is **not** the traceback visible behind the 02:45:23 editor.

**Key items to scan**

- **`mode`** — required at first; adding a parameter without a default breaks the one-filename call before file access.
- **`mode="w"`** — preserves the old call and its original overwrite behavior once the body uses the parameter.
- **`open(filename, mode)`** — passes through the caller's actual value; different modes change when or how failure occurs.
- **`fruit.toFile("fruit2.dat", "a")`** — append at the first save; the final one-argument save overwrites instead.
- **`sys.argv`** — suggested source of a command-line choice; not read by the shown driver.
- **`"x"`** — exclusive creation; an existing destination causes `FileExistsError` before writing.
- **`"wb"`** — binary write mode; the shown text-producing body is not a bytes writer.

### Two other code fragments visible beside the file demonstration

The split editor exposes two boundaries worth recognizing even though the driver does not exercise them in this interval. In the clearing fragment (02:19), setting the stored value to **`None`** *before* printing it would lose the diagnostic's original name; the code prints the current value first. In the indexing fragment (02:27), requesting a position beyond the final node raises an exception, not a fruit or the value `None`. Only part of the method is visible; do not infer the normal return path from the guard.

**Annotated teaching version — guide-added comments.** Fragments, not standalone programs. The enclosing branch above the first two lines and the rest of the second method are off-screen.

```python
            iter._prev._next = None    # Disconnect the previous node's forward link.
            iter._prev._object = None  # Empty that previous node's stored value.
        print( f"clearing: {iter._object}" )  # Display the current value before erasing it.
        iter._prev = None             # Remove the current node's backward link.
        iter._object = None           # Mark the current node empty.
```

```python
def __getitem__(self, index):          # Begin looking up a requested position.
    iter = self.first( )               # Start at the first node.
    if iter._object != None:           # Only enter this visible branch for a nonempty first node.
        count = 0                      # Start counting at position zero.
        while count < index:           # More steps needed to reach the requested position?
            if iter._next == None:     # There is no next node to take that step to.
                iter = None            # Clear this local reference; do not return it.
                raise Exception( "Index beyond end." )  # Stop with this message.
```

**Key items to scan**

- **`None`** — marks cleared links or an empty stored value in the shown code; it is not the return from the out-of-range branch.
- **`iter._prev`** — previous node from the current traversal position; changing one of its fields changes the node, not just a local variable.
- **`__getitem__`** — indexed-access method; the visible branch raises `Exception("Index beyond end.")` when no successor exists and another step is required.

## Why a valid file mode can still break the writer (02:46–02:52)

A student sees two red lines: is Python complaining about **`"wb"`** twice? Compare two runs of **`py .\test\fruit.py`** (02:48:00). Both start at line 13 of **`fruit.py`**, but they stop at different operations inside **`item.py`**. In the earlier run, exclusive creation (**`"x"`**) failed at line 210 because **`fruit2.dat`** already existed. In the next run, opening that file for binary writing *succeeded*. Only when the method tried to put the first fruit into the open file did it fail. These are two runs; the two locations *within* the second traceback describe one failure.

**Annotated teaching version — guide-added comments** (02:46:00; method body shown at its original relative indentation). The driver has created one custom list and appended the strings apple, banana and grape. It saves the ordinary Python list from **`fruit.toList()`** in **`pylist`**, but the loop to print that copy is commented out. The following writer walks the custom list itself.

```python
from item import Item                   # Load the custom collection type used by the class.
fruit = Item( )                         # Start a new empty collection on this run.
fruit = fruit.append( "apple" )         # Add apple and rebind fruit to the result of append.
fruit = fruit.append( "banana" )        # Add banana; the collection now has two stored strings.
fruit = fruit.append( "grape" )         # Add grape; the file writer will visit these three in order.
pylist = fruit.toList()                 # Obtain an ordinary Python list; the display loop is commented out.
fruit.toFile("fruit2.dat", "wb")        # Request binary overwrite using the original unguarded method below.
```

```python
def toFile(self, filename, mode="w"):             # Without a mode argument, open for text overwrite.
    with open(filename, mode) as file:           # "wb" opens/truncates fruit2.dat before any fruit is written.
        iter = self.first( )                     # Start with the node holding apple, not with the text itself.
        if iter._object is not None:             # Don't write the empty node's placeholder.
            while True:                          # Visit one node each time through the loop.
                file.write(iter._object + '\n')  # First build the string 'apple\n', then try to write it.
                if iter._next == None:           # With a successful write, is this the final fruit?
                    break                        # Stop after writing grape; don't follow a nonexistent link.
                iter = iter._next                # Otherwise move from apple to banana, then to grape.
```

**Observed terminal results, separate runs (02:48:00):**

```text
FileExistsError: [Errno 17] File exists: 'fruit2.dat'
```

```text
TypeError: a bytes-like object is required, not 'str'
```

Read **`Traceback (most recent call last):`** from the bottom up to locate the operation, then climb back to its cause. Line 214 shows the failed write; line 13 tells you who requested binary mode. Neither line alone explains the mismatch. The instructor contrasts this display order with tools that list the most recent call first. The editor offers a different kind of navigation at 02:49: hovering over the call points to the definition at **`item.py:209`** and another reference at **`fruit.py:19`**. Those are places to inspect the source, not extra calls in this traceback. **`str`** is Python's text type; **`bytes`** represents immutable bytes, and **`bytearray`** holds mutable bytes. A binary stream wants bytes-like data, not the characters in a string. Appending the newline makes the type easy to see; it isn't the reason the original apple is text. The first failure happens before the loop tests the next link. Opening in binary write mode may already have erased the old contents; a failed write is not a rollback. A separate disposable-file run verified that an existing file was empty after this mismatch.

**Key items to scan**

- **`py .\test\fruit.py`** — the same command can produce different errors after an argument or method changes.
- **`fruit.py`**, **`item.py`** — driver line 13 calls the writer; method line 210 opens the file, line 214 writes one item in this revision.
- **`item.py:209`**, **`fruit.py:19`** — the editor popup's definition and reference locations are static navigation aids, not traceback frames.
- **`"x"`**, **`fruit2.dat`**, **`FileExistsError`** — exclusive creation refuses an existing destination before traversal starts.
- **`"wb"`**, **`TypeError`** — binary overwrite opens the destination, then rejects the first text value.
- **`fruit.toList()`**, **`pylist`** — a separate returned Python list; its commented print loop does not produce the fruit output.
- **`Traceback (most recent call last):`** — read the last call for the failing operation and earlier calls for its context.
- **`iter._object + '\n'`** — produces a string ending in a newline; **`iter._next`** is a node link, not part of the file content.
- **`str`**, **`bytes`**, **`bytearray`** — text, immutable byte data, and mutable byte data, respectively.

## Narrow the method's promise before opening the file (02:51–02:55)

Could the writer recognize both text and binary? The instructor sets text encodings aside rather than claiming to have implemented binary serialization. The immediate design problem is simpler: the method accepts any mode string but only knows how to write text lines. Instead of passing the caller's request straight to the file opener, inspect it first. The instructor calls that untrusted request **`hope`**; **`mode`** is supposed to be the approved local value. One option discussed was doing the work only in the accepted branches and otherwise doing nothing. The class instead takes the explicit-error route so that the caller can decide what to do.

**Annotated teaching version — guide-added comments** (guard excerpt after the colon repair; the separate comparison mistake is preserved).

```python
def toFile(self, filename, hope="w"):   # Omitting the request asks for text overwrite.
    if hope == "w":                    # Did the caller request the supported overwrite operation?
        mode = 'w'                     # Yes: assign the local mode used by open.
    elif hope == "a":                  # Intended second option: append without erasing the file.
        mode == "a"                    # As displayed: compares, does NOT assign; mode has no value here.
    else:                              # Includes the driver's otherwise legitimate open mode "wb".
        raise Exception("invalid mode") # Report rejection to the caller before opening or truncating.
    with open(filename, mode) as file: # Reached for "w"; use the same text-writing traversal as before.
        ...                            # Editorial omission: unchanged loop, shown in the preceding section.
```

The first attempted guard didn't even reach the mode check: the **`elif`** header lacked its colon at 02:54:38. Importing the module failed at line 212. After the colon was restored, the very same driver call reached the **`else`** and raised the planned exception at line 215. Now binary mode is rejected *by the method*, not by the file object's first write. There is a further screen-only trap: **`mode == "a"`** reads the local variable to compare it; neither the comparison nor a correct **`elif`** header initializes that variable. The intended assignment would be **`mode = "a"`**. No append-branch repair or test is established here.

| Requested operation | First relevant check | Result and provenance |
|---|---|---|
| Import the missing-colon version | Parse the entire module before running the driver | Observed: `SyntaxError: expected ':'`; no fruit operations start. |
| `"wb"`, colon fixed | Neither accepted string matches | Observed: `Exception: invalid mode`; the guard stops before `open`. |
| `"w"`, including default | First condition succeeds | Uses text overwrite; each fruit can be written with its newline. |
| `"a"`, code as displayed | Second condition succeeds; its body reads `mode` | Independently tested guard: `UnboundLocalError`, not a successful append. |

**Key items to scan**

- **`hope`** — the caller's request, not yet an approved file mode.
- **`mode`** — local value used by the opener; the overwrite branch assigns it.
- **`elif`** — needs a colon before its indented body; the missing colon breaks import, not just the append path.
- **`else`**, **`raise Exception("invalid mode")`** — reject unrecognized requests before touching the destination.
- **`mode == "a"`** versus **`mode = "a"`** — testing a local variable is not assigning one. Fixing the colon alone doesn't make the append case work.

## What should the caller do after rejection? (02:55–02:59)

The instructor asks whether the exception must travel all the way back to the command line and end the program. No: put the operation that can fail in **`try`**, and put the caller's decision in **`except Exception as ex`**. Once the handler finishes, execution continues *after* the block. It doesn't resume at the failed write. The driver tries three policies, with different consequences for the data on disk.

**Annotated teaching version — guide-added comments** (02:57:01; closing characters on the binary call are independently visible in the next wider view).

```python
try:                                    # Attempt the guarded method on the existing three-fruit list.
    fruit.toFile("fruit2.dat", "wb")    # Raises before a file write: the guard rejects "wb".
except Exception as ex:                 # Bind the raised exception object to ex.
    print(ex)                           # Display its message, invalid mode; do not retry the write.
```

**Observed output, 02:57:01:**

```text
invalid mode
apple
banana
grape
```

Only the first line comes from the handler. The three fruit lines come from the later loop, not from the failed write. That distinction answers the practical question: the *program* survived, but the requested operation didn't succeed. Earlier binary overwrite could have left an empty file; this is consistent with reading nothing new here, not proof of a separately displayed file inspection.

**Annotated teaching version — guide-added comments** (02:58:00; replacement handler, same attempted binary call).

```python
except Exception as ex:                 # Catch the same rejected request.
    pass                                # Do nothing: neither report the failure nor write replacement data.
```

**Observed terminal tail at 02:58:00:** apple, banana, grape, apple, banana, grape on separate lines, with no diagnostic in that run. Why do duplicates appear even though the handler writes nothing? The driver starts with three nodes each run; the file persists *between* runs. After printing, the earlier driver wrote the current three fruits with its final default-mode call. Reading those records appends them to the three already in memory. Suppressing an error can leave stale data looking like success.

**Annotated teaching version — guide-added comments** (02:59–03:01; new recovery policy and subsequent driver code).

```python
try:                                     # First ask for the binary write, as before.
    fruit.toFile("fruit2.dat", "wb")     # The method rejects this request without opening the file.
except Exception as ex:                  # This broad handler catches more than just invalid modes.
    fruit.toFile("fruit2.dat", "w")      # Retry explicitly: overwrite with apple, banana, grape as text.

fruit.fromFile("fruit2.dat")             # Append those three file lines to the SAME three-node collection.
for it in fruit:                         # Iterate over the six stored values, in order.
    print(it)                            # Print one fruit per line; this loop owns the fruit output.
fruit.toFile("fruit2.dat")               # Default text overwrite: the file now represents all six items.
```

The original read helper (02:46, 02:59) is why reading is not a reset:

```python
def fromFile(self, filename):            # Work on the existing Item, not a newly constructed list.
    with open(filename, "r") as file:    # Open the file as text for reading.
        for item in file:                # Receive one line, often with its trailing newline.
            self.append(item.strip())    # Remove surrounding whitespace, then append the result.
```

**Guide-derived trace of the retry version; independently reproduced with a small linked-node fixture and a disposable file, not the course project.** The six-line terminal tail was already present at 02:58; the 02:59 still is not proof of a fresh retry run.

| Point reached | In-memory sequence | File contents or visible effect |
|---|---|---|
| Binary request rejected | apple, banana, grape | No open, no change on disk. |
| Text retry completes | Same three | File replaced with apple, banana, grape, each on its own line. |
| First, second, third line read | Original three, then apple → banana → grape appended | File unchanged while reading; six nodes now. |
| Print loop, then default write | Same six; no new list constructed | Prints the two trios; overwrites the file with those six lines. |

The instructor presents forced overwrite as one possible decision, not a universal recovery rule. It can destroy an existing file. Catching every **`Exception`** can also mistake an unrelated failure for a bad mode. Choose a recovery that addresses the cause rather than simply making the error disappear.

**Key items to scan**

- **`try`** — isolates the first call that can reject the request.
- **`except Exception as ex`**, **`Exception`** — handle a broad class of failures; **`ex`** names the particular exception object.
- **`print(ex)`** — reports `invalid mode` without fixing the failed write.
- **`pass`** — a syntactically valid do-nothing handler; later reads still see whatever the file already held.
- **`fruit.toFile("fruit2.dat", "w")`** — actively retries in text overwrite mode; the caller, not the class, chooses this policy.
- **`fruit.fromFile("fruit2.dat")`**, **`item.strip()`** — read, trim and *append* each line to the existing nodes; do not replace them.
- **`print(it)`** — prints each in-memory fruit; the last no-mode write changes the file, not the console.

## Does the plus sign make something text? (02:59–03:07)

A student points back to the writer's line 220: if we removed the plus and newline, would the item automatically be binary? The instructor offers two ways to investigate: find out what **`+`** does with these operands, or inspect the type of the stored item. The class chooses the operator first. Removing a text suffix from the *code* would leave the original stored string **`"apple"`**, not encoded bytes. The demonstration asks the same question with a smaller pair of values so that arithmetic addition and text concatenation can be seen side by side.

**Annotated teaching version — guide-added comments** (03:02–03:05; four successful statements, followed by the additional failing one).

```python
print(2 + 3)            # Add two integers; print 5.
print(type(2))          # Inspect the unquoted 2; print its class, int.
print('2' + '3')        # Join two one-character strings in order; print 23, not 5.
print(type('2'))        # The same digit inside quotes is a str; print its class.
print(2 + '3')          # Try int plus str; evaluating the argument fails before print runs.
```

**Observed successful terminal tail (03:03:35; preceding fruit lines omitted):**

```text
5
<class 'int'>
23
<class 'str'>
```

A missing closing parenthesis in the first **`type`** test briefly prevented the whole file from being parsed. The above four lines of output belong to the repaired run. The later mixed expression, by contrast, is legal syntax. The four preceding prints still run before evaluation reaches it. A student predicts it will break: should the result be arithmetic five, or the two-character string twenty-three? Python doesn't choose between these intentions. The class identifies unsupported operand types; the right side of the terminal is obscured at 03:05. **Independently verified full diagnostic:**

```text
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

The instructor connects this to **operator overloading**: a class designer could specify a meaning for adding unlike values in a custom class. That was a design proposal, not a rewrite of built-in addition or a class implemented here. First decide which result makes sense, then explicitly convert an operand or reject it.

**Key items to scan**

- **`+`** — its behavior depends on the operands; it is not an instruction to convert to binary or to text.
- **`"apple"`** — already a string before the writer adds a newline.
- **`2 + 3`** — integer addition yields `5`.
- **`'2' + '3'`** — concatenation yields `'23'`; printing that string displays `23` without quotes.
- **`type`**, **`type(2)`**, **`type('2')`** — inspect the object, not the appearance of the character: `int` versus `str`.
- **`2 + '3'`** — operand evaluation fails at runtime; the surrounding print emits no fifth result.
- **Operator overloading** — defining what a particular operator means for a class, not asking Python to guess a conversion.

## Which direction of conversion has a natural meaning? (03:06–03:13)

If the goal is calculation, converting the input text **`'2'`** to a number is useful. But what would the same conversion do to the word *dog* or *fruit*? A learner suggests asking for a valid number rather than continuing as if the input were numeric. Conversely, writing down a number as characters doesn't pose that same problem. The class tests the distinction by inspecting characters on the last line of the editor. These are scratch examples, not successive successful executions; the mixed-type error remains earlier in the file.

| Exact scratch text and capture | As one Python numeric literal? | Could those characters be stored in a string? |
|---|---|---|
| `133.45` (03:09) | Yes: one decimal point. | Yes, e.g. `'133.45'`. |
| `-133e45` (03:10) | Yes: a signed value in scientific notation; `e45` scales by ten to the 45th power. | Yes. The `e` here is an exponent marker, not an imaginary number or a caret operator. |
| `-133t45` (03:11) | No: `t` isn't an exponent marker. | Yes; text doesn't require numeric grammar. |
| `-133.45.87` (03:12–03:13) | No: two radix points cannot delimit one number. | Yes; two period characters are ordinary text. |

**Independently checked interpretation, not classroom output:** passing those four spellings *as strings* to **`float()`** returned `133.45` and `-1.33e+47` for the first two and raised **`ValueError`** for each of the last two. This is a conversion test on valid string inputs, not an attempt to run malformed unquoted source. If one needs integer arithmetic, not every valid floating-point spelling is an integer spelling, either. The proposed design is therefore conditional: try to convert the string for arithmetic and raise an exception if conversion fails; or convert the number to text when joining is what the caller meant. Neither policy is the default behavior of mixed **`+`**.

**Key items to scan**

- **`'2'`** — a digit stored as text may be convertible for arithmetic; a word such as *dog* isn't a decimal integer.
- **`133.45`**, **`-133e45`** — numeric notation allows a decimal point and a specifically placed exponent marker.
- **`-133t45`**, **`-133.45.87`** — one unexpected letter or an extra radix point defeats the numeric interpretation, not the text interpretation.
- **`float()`**, **`ValueError`** — a separate conversion check distinguishes acceptable input text from text that cannot denote a float.
- **`+`** — choose whether to add numerically or concatenate *before* choosing a conversion direction.

### The generator on screen, and the return to version control (03:12–03:13)

The instructor wonders whether there is time to return to generators; a student mentions another class in fifteen minutes. The editor shows **`genny`** in **`item.py`** (also visible at 02:46). It uses the same links as the writer, but hands a value to its caller instead of putting a line in a file. The driver on screen loops over **`fruit`**; it does not call **`genny()`** explicitly. The method is screen evidence, not proof that this final discussion ran it.

**Guide trace for the three original nodes:** first request → apple (paused at the first yield); second → follow the link and yield banana; third → follow the link and yield grape; fourth → resume after that yield, test the final link, break and signal exhaustion. No line in this method prints the values. The adjacent **`fromList`** helper loops over a supplied ordinary list and calls **`self.append(item)`** for each element; like the file reader, it adds to the receiver rather than constructing a replacement. A nearby screen-only indexing fragment starts at the first node and tests whether a requested index runs past the last link.

These unchanged helpers are worked in the earlier conversion and indexing sections. The numbered listings preserve which portions were actually on screen here.

At the end the instructor says the checkout is already on **`master`** and asks why the earlier driver didn't return, then begins explaining that **`fruit.py`** wasn't committed. This is a spoken transition, not a visible confirmation that this frame executed a checkout or restored an earlier driver; the following checkout reveals which generator revision was selected.

**Key items to scan**

- **`genny`**, **`genny()`**, **`yield`** — create a generator and supply one node's value per request, retaining the position while paused.
- **`item.py`** — contains the helper definition; the driver iterates **`fruit`** without an explicit call to the helper.
- **`fromList`**, **`self.append(item)`** — append elements from a Python list to the existing collection; this helper doesn't print them.
- **`__getitem__`**, **`count < index`** — the visible index search starts at zero and checks for a missing next link before proceeding.
- **`master`**, **`fruit.py`** — the spoken checkout question concerns which version was actually recorded, not the semantics of a generator.

## Three fruits, two ways to walk the list (03:14–03:23)

The instructor wanted the revision that demonstrated generators. At 03:14 the editor still showed the file-I/O driver, not the generator example: it made a Python list, tried writing `fruit2.dat` in binary mode with a text-mode fallback, and read from the file. Its last expression, **`2 + '3'`**, also appears in a terminal traceback: adding two integers had produced `5`; joining two strings had produced `23`; mixing an integer and a string raised **`TypeError`**. That is an earlier run, not a generator error. The terminal shows **`git checkout master`** reporting `Already on 'master'` and marking `src/item.py` and `test/fruit.py` modified. The instructor describes looking through the one-line Git log, leaving the listing, checking out an ID and reloading the editors. A sliver of terminal at 03:15 shows `Aborting` above a later switch notice; the intervening commands are obscured. The confirmed endpoint is `HEAD is now at 843e915 generator` (03:20), a detached checkout of the named commit, not a new branch. `git switch -c <new-branch-name>` and `git switch -` are Git’s on-screen suggestions, not commands shown being run.

The earlier file-mode and mixed-type examples explain the outgoing buffer. At this transition the evidence to follow is the **checkout**, not the older error in scrollback.

**Key items to scan**

- **`pylist = fruit.toList()`** — creates the native list; its own printing loop is disabled.
- **`fruit.toFile`** — the outgoing buffer asks for `"wb"`, with `"w"` as its exception fallback; distinct from generator exhaustion.
- **`fruit.fromFile`** — loads from `fruit2.dat` before the driver iterates `fruit` again.
- **`git checkout master`** — stayed on `master` with modified files; it did not select the generator revision.
- **`843e915 generator`** — the later confirmed checkout; reloading exposed a different `fruit.py` under the same filename.
- **`2 + '3'`** — fails with **`TypeError`** in the earlier driver; unrelated to running out of fruits.

### Establish a baseline: what does the ordinary loop print?

The generator revision starts with an empty custom **`Item`**, not a built-in Python list. It appends three strings and keeps the object reference returned by each call. The ordinary **`for item in fruit:`** loop establishes both the order and the stopping behavior against which the generator will be compared. This setup appears at 03:15–03:22; its output is visible at 03:20.

**Annotated teaching version — guide-added comments**

```python
from item import Item              # The course's linked-list class.
fruit = Item()                     # Start a new, empty custom list.
fruit = fruit.append("apple")      # Store apple; keep the returned Item reference.
fruit = fruit.append("banana")     # Same chain now contains apple → banana.
fruit = fruit.append("grape")      # Same chain now ends in grape.
for item in fruit:                 # Ask the Item iterator for one string per pass.
    print(item)                    # Display the string returned on this pass.
print()                            # Separate this output from the generator experiment.
```

**Observed output, first group (03:20):**

```text
apple
banana
grape

```

Where does the loop keep its place? At 03:22–03:23 the instructor returns to the class's own iterator methods. The **head** is the first node (apple), reachable even if `fruit` refers to another node. The class stores the next node to read in the head's **`_iter`** attribute. Here is the implementation visible at 03:23, formatted for teaching as methods inside `Item`:

```python
def __iter__(self):                 # Called when the loop asks fruit for its iterator.
    head = self.first()             # Find the apple node from anywhere in the chain.
    head._iter = head               # Reset the shared position to apple.
    return head._iter               # Give the loop the Item object with __next__.

def __next__(self):                 # One request on that Item iterator.
    head = self.first()             # Find the node holding the shared position.
    if head._iter == None:          # No node remains after grape.
        raise StopIteration         # Signal completion; no string or None is returned.
    target = head._iter._object     # Save the current string before advancing.
    head._iter = head._iter._next   # Apple → banana → grape → None.
    return target                   # Hand the saved string to the loop, not the node.
```

| Loop request | `head._iter` before | Returned to loop / displayed | `head._iter` after |
|---|---|---|---|
| 1 | apple node | `apple` | banana node |
| 2 | banana node | `banana` | grape node |
| 3 | grape node | `grape` | `None` |
| 4 | `None` | `StopIteration` raised; nothing displayed | `None`; loop ends |

The fourth request is part of normal iteration. A `for` loop handles that signal quietly. The class has **one** `_iter` slot on the head of this chain: asking this implementation for another ordinary iterator resets that same slot. This matters when the class later wants two traversals to progress independently.

**Key items to scan**

- **`Item()`** — creates the empty custom list; `append` adds the three named values.
- **`for item in fruit:`** — prints strings, not nodes, and stops without a traceback.
- **`__iter__`** — finds apple, resets the shared position, and returns the head node as iterator.
- **`_iter`** — an attribute on that head; after the grape request it is already `None`.
- **`__next__`** — saves the string, advances the position, then returns the saved string.
- **`StopIteration`** — the fourth request raises it; the loop does not print `None`.

### A method that can pause at apple (03:15–03:21)

The instructor asks what would happen if a method could *remember where it stopped*. Compare the screen's **`toList()`**, which returns only after it has collected every fruit, with **`genny()`**, which can give back apple before it ever follows apple's link to banana. `genny` is the method name; **`jenny`** is the driver's variable. The similar-sounding names are different in the editor.

**Annotated teaching version — guide-added comments** (methods within `Item`; 03:15–03:19):

```python
def toList(self):
    list = []                       # Build a NEW Python list, not another linked chain.
    iter = self.first()             # Start at the first node.
    if iter._object is not None:    # Empty Item uses None as the first payload.
        while True:
            list.append(iter._object)  # Accumulate apple, then banana, then grape.
            if iter._next == None:  # Grape has no successor.
                break
            iter = iter._next       # Move the local reference to the next node.
    return list                     # Return all three strings together (or []).

def fromList(self, list):
    for item in list:               # For example, read apple, banana, grape in order.
        self.append(item)           # Extend this chain; do not create a new Python list.

def genny(self):                    # Calling fruit.genny() creates a generator object.
    iter = self.first()             # Runs on its FIRST request, not at object creation.
    if iter._object is not None:    # An empty first node yields no item, not even None.
        while True:                 # Repeat until the last node's next link is absent.
            yield iter._object      # Give back this string; pause HERE with iter unchanged.
            if iter._next == None:  # Runs on RESUME: is there a node after this one?
                break               # No successor: leave loop and finish the method.
            iter = iter._next       # Otherwise move to banana (later grape) and loop.
```

The names `iter` and `list` are local variables in this code; they are not calls to Python’s built-ins. Each call to **`fruit.genny()`** creates a new generator. It takes a request on *that object* to start or resume its body. In reply to the question about calling it “via the print function”: **`print(jenny)`** displays the object without advancing it. In **`print(jenny.__next__())`**, the inner call gets one string and *then* the outer print displays it. Python's `next(jenny)` asks for the same next item.

**Annotated teaching version — guide-added comments** (first driver block):

```python
jenny = fruit.genny()               # New generator on the existing three-node list.
print(jenny)                        # Show the generator description; still before apple.
print(jenny.__next__())             # Start the body: yield and display apple.
print(jenny.__next__())             # Resume AFTER yield; move to banana and display it.
print(jenny.__next__())             # Resume again; move to grape and display it.
# print(jenny.__next__())           # Disabled in the first version; no fourth request.
```

**Observed output, first generator only (03:20):**

```text
<generator object Item.genny at 0x00000172B0F10E10>
apple
banana
grape
```

**Trace of one generator, including the later extra request.** The instructor corrects “banana” to “apple” for the first result. Notice the difference from the ordinary iterator: after yielding grape, this generator is *paused at grape*, not yet finished. It has not tested grape’s next link.

| Request | Where this generator starts/resumes | What happens | Result and saved position |
|---|---|---|---|
| Creation / object print | Body not started | Construct/describe the generator; no fruit examined | No fruit returned; still unstarted |
| 1 | Top of `genny` | Find apple, pass guard, yield | `apple`; paused at apple |
| 2 | Just after apple's yield | Apple links to banana; move, loop, yield | `banana`; paused at banana |
| 3 | Just after banana's yield | Banana links to grape; move, loop, yield | `grape`; paused at grape |
| 4 (if requested) | Just after grape's yield | Grape’s link is `None`; break and reach end of method | `StopIteration` to caller; generator closed, no fourth fruit |

For an empty `Item`, the first request finds an empty payload, skips the whole loop, and the generator ends without yielding. Unlike the ordinary iterator's explicit `raise`, **`genny()`** just finishes; the generator machinery supplies the end-of-iteration signal. The chain itself has not lost any fruit.

**Key items to scan**

- **`toList()`** — builds and returns `['apple', 'banana', 'grape']` before its caller receives anything.
- **`fromList`** — reads an incoming Python list and appends each member to the receiving chain.
- **`genny()`** / **`fruit.genny()`** — calling this generator method creates an unstarted traversal of `fruit`.
- **`jenny`** — the name for that particular generator, not the method or a fruit string.
- **`yield iter._object`** — gives back the current string and leaves that generator paused on the same node.
- **`iter = iter._next`** — advances only on resumption and only if the next link exists.
- **`print(jenny)`** — displays an object description containing a run-dependent address; consumes nothing.
- **`print(jenny.__next__())`** — requests one fruit and displays it only if the request succeeds.

## What if we ask for one more? (03:21–03:28)

The class predicts trouble when there is “no next.” The instructor enables the fourth unguarded call at line 17. The 03:24:00 terminal is still showing an earlier successful run; the 03:24:24 frame shows the new failure. The first three fruits still print. The fourth request raises *inside the argument* to `print`, so no fourth value is printed and execution never reaches the later `try` block in that run.

**Annotated teaching version — guide-added comments** (revision: immediately after the third successful request):

```python
print(jenny.__next__())             # Fourth request on THIS generator; raises before print.
```

**Observed failure (03:24:24; traceback location and final line):**

```text
  File "C:\com\joesta\Item\test\fruit.py", line 17, in <module>
    print( jenny.__next__( ) )
StopIteration
```

Now the instructor comments line 17 out again. Will assigning **`jenny = fruit.genny()`** work after the first generator has already supplied all three fruits? Yes: the right-hand side creates a *different generator* for the *same list*, then `jenny` is rebound to it. Nobody has rewound the old generator, and no new apple, banana or grape node was created.

**Annotated teaching version — guide-added comments** (second block, 03:25–03:28):

```python
print()                             # Separate this run through the list from the first.
jenny = fruit.genny()               # Fresh generator; start position is not inherited.
print(jenny)                        # A different generator description; no fruit consumed.
try:
    print(jenny.__next__())         # First request: apple.
    print(jenny.__next__())         # Second: banana.
    print(jenny.__next__())         # Third: grape.
    print(jenny.__next__())         # Fourth: finishes genny; outer print does not run.
except StopIteration:               # Catch exactly the end-of-iteration signal.
    print("List exhausted.")        # The handler prints this; it is NOT a fourth yield.
```

**Observed, second block of the successful rerun (03:26):**

```text
<generator object Item.genny at 0x00000270A8901A40>
apple
banana
grape
List exhausted.
```

The first generator description in that run ended in `0x00000270A8900E10`, the second in `0x00000270A8901A40`. The difference is visible evidence supporting the class’s answer to “Is it the same generator object?” Do not depend on the particular addresses. The instructor calls printing a message when an exception is caught a poor general habit; here it deliberately makes the catch visible. A normal `for` loop would handle exhaustion without that message.

**Key items to scan**

- **Line 17** — active and outside `try` in the failing run; disabled again to let later experiments execute.
- **`jenny = fruit.genny()`** — creates a new generator and reuses a variable name, not the old generator’s position.
- **`except StopIteration:`** — intercepts the fourth request’s signal; unrelated exceptions are not caught here.
- **`List exhausted.`** — printed by the handler, not returned by `__next__` or emitted automatically by Python.

## Two readers, one list: who gets banana next? (03:28–03:31)

Two *separate calls* to the same method on the same `fruit` object produce two independent readers. The second variable is spelled **`jenifer`** on screen (one `n`); the instructor pronounces it “Jennifer.” The class’s prediction exercise hinges on which of its lines is commented: `jenifer`’s **first** call is active; its **second scheduled** call is skipped. The code does not alternate evenly.

**Annotated teaching version — guide-added comments** (03:29, confirmed at 03:29:47):

```python
print()                             # Separate this experiment from the handled fourth call.
jenny = fruit.genny()               # Fresh traversal A on the existing fruit chain.
print(jenny)                        # Describe A; do not ask for apple yet.
jenifer = fruit.genny()             # Fresh traversal B, not another name for A.
print(jenifer)                      # Describe B; both bodies are still unstarted.
try:
    print(jenny.__next__())         # A: apple. B has not started.
    print(jenifer.__next__())       # B: apple too. A did not advance B.
    print(jenny.__next__())         # A: banana; B is still paused at apple.
    # print(jenifer.__next__())     # Skipped: B does NOT move to banana here.
    print(jenny.__next__())         # A: grape, its third fruit.
    print(jenifer.__next__())       # B: banana, only its second fruit.
except StopIteration:
    print("List exhausted.")        # No fourth request on either reader here; not printed.
```

**Observed final group (03:26 and 03:30, same successful run):**

```text
<generator object Item.genny at 0x00000270A8900E10>
<generator object Item.genny at 0x00000270A8901A40>
apple
apple
banana
grape
banana
```

| Step | Request | What prints | A (`jenny`) after | B (`jenifer`) after |
|---|---|---|---|---|
| Setup | Two generator calls, then descriptions printed as each is made | Two object descriptions | Not started | Not started |
| Line 36 | A, first | `apple` | At apple | Not started |
| Line 37 | B, first | `apple` | At apple | At apple |
| Line 38 | A, second | `banana` | At banana | At apple |
| Line 39 | Comment only | Nothing | At banana | At apple |
| Line 40 | A, third | `grape` | At grape | At apple |
| Line 41 | B, second | `banana` | At grape | At banana |
| Guide continuation, not a classroom call | A, fourth | No fruit; `StopIteration` | Closed | Still at banana |
| Guide continuation | B, third | `grape` if printed | Closed | At grape |
| Guide continuation | B, fourth | No fruit; `StopIteration` | Closed | Closed |

**Independently verified, not a classroom run:** The annotated driver and the reconstructed 43-line driver produced the same fruit sequence and handler message on a small linked-node fixture supplying the unseen constructor, `first`, and `append`. The test also confirmed that A’s extra request raises, B still yields `grape`, and B’s following request raises. An empty generator raised on its first request. This checks the traversal logic, not the instructor’s entire project.

The two simultaneously live generators have distinct addresses. The *same address strings* also appear earlier in this terminal run; addresses can be reused after earlier objects cease to be live. They are not permanent traversal IDs. The reliable prediction is based on calls: each live generator remembers its own node and paused instruction, not an index shared by `fruit`.

**Key items to scan**

- **`jenifer`** — a second generator from a second `fruit.genny()` call, not an alias for `jenny`.
- **Line 39** — commented out; the later `jenifer` request gets banana, not grape.
- **`apple, apple, banana, grape, banana`** — five outputs from two readers; neither is asked past its last fruit in this block.
- **Paused at grape** — A has yielded its last fruit but only another request actually closes it; B can still proceed independently.

### Other traversal tools visible in the editor (03:16, 03:23, 03:25)

The instructor scrolls through other methods while locating the iterator and generator. They show how the same chain can support different questions. These are **screen observations**, not additional calls demonstrated in the terminal. The inputs below illustrate their visible logic.

The corresponding working examples appear in the sections on length, position, membership and clearing. At this checkout, the useful contrast is the *result* each method produces: a count, a node, a search result, or a changed chain. None is the suspended traversal created by `genny()`. The numbered appendix preserves the visible fragments without attaching new terminal output to them.

**Key items to scan**

- **`forEach`** — calls `caller.withItem` with each payload; unlike `genny`, does not pause for its caller to request the next value.
- **`count()`** — starts at 1 for apple, adds one per following node, returns 3 for this chain.
- **`at(2)`** — returns the grape node; `at(3)` reaches the explicit `Index beyond end.` exception instead.
- **`has`** — the visible loop stops at a matching payload or when it runs out of nodes; no return line was established here.
- **`_prev._next`** — the cleanup fragment changes links and payloads; walking with `genny` only changes the generator’s local reference.

At 03:31 the instructor checks whether the generator explanation is clear, then transitions from 1240 to the 1200 computer-essentials class. Binary, bits and bytes are mentioned as a connection to that other class, not taught as another generator example. The closing Discord screen (03:31:58) shows an older course-progress post, not a new generator run or a new policy announcement.

## Syntax to remember

| Expression or command | What to look for |
|---|---|
| `fruit.at(1)` | This version returns a node. Ask its `object()` method for the stored banana. |
| `fruit[1]` | Subscription asks for `__getitem__`; the custom get/set revision reads the payload. Earlier revisions do not all support brackets. |
| `fruit[1] = value` | Assignment asks for `__setitem__` with position `1` and the value evaluated on the right. |
| `len(fruit)` | The type must implement the length protocol. A method merely named `count` is not enough. |
| `fruit.has(b)` | Look for a payload equal to `b`. On a hit the result is a node, not `True`. |
| `fruit.toList()` / `fruit.fromList(values)` | Export values into a new native list, or append supplied values to an existing custom chain. |
| `open(name, 'w')` / `open(name, 'a')` | Replace old file contents or retain them and add new records. Neither operation itself clears the linked list. |
| `yield value` / `next(generator)` | Suspend this execution with a value; resume that same execution on the next request. |
| `git log --oneline` | Find a saved change by its subject and abbreviated commit ID. This does not switch versions. |

## Knowledge check

1. A native list holds apple, banana, grape. Index `1` works on it but raises `TypeError` on an early `Item`. Does the exception mean there is no second fruit? What would have to change to support both reading and assignment?
2. Two versions of a search return different things when banana is present. One returns `True`; the other returns the node holding banana. Which answer lets the caller move to grape without a second search? What should the caller check if it asks for kiwi?
3. A student remembers an earlier successful run. Now an editor shows a different method body and the terminal still shows the old output. What evidence would justify pairing the new body with the output?
4. The assignment `list = fruit.toList()` can succeed. Why might a subsequent `list()` call fail, and why would iterating over the assigned result still work?
5. You write the three fruit names to a text file, then read each back by appending it to the same three-item chain. Where are there now six entries? How would you test whether a second write *replaces* or *appends* on disk?
6. The program accepts a file mode, opens a binary stream, and tries to write a fruit name with a newline. Why is “the mode was accepted” not enough? Where in the traceback would you look to distinguish a bad mode from a bad write?
7. Start two new generator objects on the same apple → banana → grape chain. Ask the first for two items, the second for one, then the first for two more. Which requests return fruit? Which request raises?

### Answer key

1. No. The early object can contain banana without implementing bracket lookup. Reading asks the type for `__getitem__`; indexed assignment asks it for `__setitem__`, with both the index and the evaluated right-hand value. A position error would be a different question, reached only after there is a lookup implementation.
2. The node preserves the place in the chain and has access to its links; a Boolean reports only whether a match occurred. Check for `None` before using a missing result as a node. A `print` that displays banana is not proof that the returned object was the string itself.
3. Identify the checkout, file, and saved revision, then a subsequent invocation and its output. A detached checkout or unsaved buffer can change what you are looking at without changing old scrollback. If the sequence is not visible, label the predicted behavior rather than turning it into an observed run.
4. The assignment binds the name `list` to a list *instance*. That instance can be iterated, but it is not the callable built-in `list` type. Calling it can raise `TypeError`. Keep the name for the result, such as `pylist`, distinct from the constructor you may need later.
5. The six entries are in the receiving **in-memory** chain. Reading need not change the file at all. Start with a known file, write a distinguishable new batch, and inspect the file itself: `'w'` truncates before writing; `'a'` retains what was already on disk. Keep the receiver’s starting contents fixed when comparing runs.
6. Opening the file and writing to it are separate operations. A valid binary mode still requires bytes-like data, not a `str`. Read the final exception type/message and the innermost relevant source line; then follow the caller above it to see how the mode or data reached that line.
7. First generator: apple, banana. Second generator: apple. The first generator’s next request returns grape; its following request raises `StopIteration`. The two calls that created the generators produced independent suspended executions, not two names for one walk.

## Library of definitions

| Term | Meaning in this lesson |
|---|---|
| Node / stored object | The `Item` at a position is a container with links. The object it stores may be the string `'banana'`. A method can return one or the other. |
| Class / instance | `Item` names a type. Calling it creates an instance. An empty instance can have methods even when it contains no fruit. |
| Name / reference | A name such as `fruit` lets code refer to an object. Assigning a different object to the name is **rebinding**; changing the existing object is **mutation**. |
| Identity / equality | “Is it the very same object?” and “Do the values compare equal?” are different questions. Consult the displayed comparison, not the English word *same*. |
| Method / receiver | In `fruit.has(b)`, `fruit` supplies the instance on which the method acts. `b` supplies a separate argument to search for. |
| Index / subscript | An index selects a position. The bracket operation is subscription; a custom class supplies its behavior, not necessarily native-list bounds behavior. |
| Special method / protocol | A name such as `__len__` connects Python syntax or a built-in operation with behavior provided by a type. A similar-sounding ordinary method is not automatically used. |
| Operand / operator | In `2 + 3`, the two numbers are operands; `+` asks for an operation whose meaning depends on their types. |
| Guard / sentinel | A guard checks a case before proceeding. `None` can mark a missing result or link, not the string `'None'` and not an empty `Item` instance. |
| Iterable / iterator | An iterable can supply an iterator. An iterator remembers where its next request should continue and signals exhaustion when no value remains. |
| Generator / yield | Calling a generator function produces a generator object. `yield` supplies a value and suspends that execution; another request resumes it. |
| Callable / shadowing | `list` normally refers to a callable type. Reusing the name for a value hides that type in the current scope; it does not change the built-in everywhere. |
| Checkout / working tree | The checkout selects a version to inspect. Working-tree files and unsaved editor buffers are distinct places where changes can exist. |
| Serialization / round trip | Writing values into a representation that can be read back. One fruit name per text line preserves names under the example’s assumptions, not the identities and links of the original nodes. |
| Traceback / exception handler | A traceback names the chain of calls leading to a failure. A matching handler changes where execution goes; it does not resume the failed expression mid-line. |

**Supplemental references.** The [Python data model](https://docs.python.org/3/reference/datamodel.html) distinguishes object identity from implementation-specific memory addresses and documents special methods. The [built-in functions reference](https://docs.python.org/3/library/functions.html) documents `open`, `len`, `iter`, and `next`; the [expressions reference](https://docs.python.org/3/reference/expressions.html) describes subscription and yield expressions. The [Git restore manual](https://git-scm.com/docs/git-restore) distinguishes restoring working-tree paths from switching revisions. These are references for language and tool behavior, not evidence of what appeared on the classroom screen.

## Appendix A · Numbered classroom screen listings

Read this as a record of successive editor states, not as a single program. Gutter numbers are not Python source. Unseen suffixes remain marked even when a teaching version explains the likely behavior. When the same routine reappears, its filename, visible range and checkout matter more than its name alone.

### A.1 · Positions, subscription and length

#### Screen sequence 1 — looking up names and positions

Numbers at left reproduce the Sublime Text gutter; **do not type the numbers or bars**. Blank numbered lines are part of the visible layout. Times identify requested capture targets, not every intervening edit. These are separate working states of `test/fruit.py` and `src/item.py`, not one program assembled from every checkout.

**1.1 · `test/fruit.py` — `has(object)` driver.** `00-02-00.png` (including full line 22). Each successful search returns a node whose `object()` is printed; neither missing-kiwi result has a node to unwrap. In `00-03-00.png` the terminal shows `HEAD is now at 0ec6bc1 has(object)`, then `py .\test\fruit.py`, then `banana`, `apple`, `grape`, `None`, `None`.

```text
  1 | from item import Item
  2 |
  3 | b = "banana"
  4 | fruit = Item( "apple" )
  5 | fruit.append( b )
  6 | fruit.append("grape")
  7 |
  8 | one = fruit.has( b )
  9 | print( one.object( ) )
 10 |
 11 | one = fruit.has( "apple" )
 12 | print( one.object( ) )
 13 |
 14 | one = fruit.has( "grape" )
 15 | print( one.object( ) )
 16 |
 17 | one = fruit.has( "kiwi" )
 18 | print( one )
 19 |
 20 | two = Item( )
 21 | one = two.has( "kiwi" )
 22 | print( one )
```

**1.2 · `src/item.py` — `at(index)` revision.** `00-04-00.png` and `00-05-00.png`. Lines 145–146 are the tail of a preceding counter, not the start of `at`. The search begins at the head, advances once per position, and returns a node; trying to cross a missing link raises instead. The visible exception literal ends at the edge of the pane.

```text
145 |             count += 1
146 |         return count
147 |
148 |     def at(self, n):
149 |         iter = self.first( )
150 |         if iter._object != None:
151 |             count = 0
152 |             while count < n:
153 |                 if iter._next == None:
154 |                     iter = None
155 |                     raise Exception( "Index beyond e[remainder not visible]
156 |                 iter = iter._next
157 |                 count += 1
158 |         return iter
```

**1.3 · `test/fruit.py` — driver in that checkout.** `00-04-00.png`, `00-05-00.png`. Line 8 really uses **11**, not 1; on an exception, lines 9–10 are skipped. Lines 14–15 start over with an empty object. No run of this particular driver is established by the 04:00–05:00 scrollback.

```text
  1 | from item import Item
  2 |
  3 | fruit = Item( "apple" )
  4 | fruit.append("banana")
  5 | fruit.append("grape")
  6 | try:
  7 |     print( fruit.at( 0 ).object( ) )
  8 |     print( fruit.at( 11 ).object( ) )
  9 |     print( fruit.at( 2 ).object( ) )
 10 |     print( fruit.at( 3 ).object( ) )
 11 | except Exception as e:
 12 |     print( e )
 13 |
 14 | fruit = Item( )
 15 | print( fruit.at( 0 ).object( ) )
```

`00-06-00.png` shows the exact recorded sequence: `git checkout master~10` switched from `0ec6bc1 has(object)` to `91be848 at(index)`; `git checkout master` then reported `Switched to branch 'master'`. The stills do not show the full text of every commit message on the newer branch.

#### Screen sequence 2 — the built-in list and the earlier generator checkout

**2.1 · `test/fruit.py` on the branch, then unsaved/disk-uncertain edits.** `00-07-00.png`: through line 12 below, with a blank line 13. `00-07-20.png`: the following full state. The short branch driver already uses iteration and length; the extra two lines concern a *built-in* list, not `Item`.

```text
  1 | from item import Item
  2 |
  3 | fruit = Item( )
  4 | fruit = fruit.append( "apple" )
  5 | fruit = fruit.append( "banana" )
  6 | fruit = fruit.append( "grape" )
  7 |
  8 | for x in fruit:
  9 |     print(x)
 10 | print(len(fruit))
 11 |
 12 | coro = [3,6,8]
 13 | print(coro[1])
```

**Later buffer only**, `00-08-00.png`: lines 12–13 still match; lines 14–15 now read:

```text
 14 |
 15 | print
```

The same frame shows a *completed* `git restore .\test\fruit.py` at the terminal. Neither the bare name on line 15 nor that restore is evidence of an executed `print(fruit[1])` here. At 09:00 the terminal records `HEAD is now at 1207cd3 get/set object value at index`, then `git checkout master~2`, then `HEAD is now at f23376c Construct Item from list or file.`

**2.2 · `test/fruit.py`, historical generator driver at `f23376c`.** Assembled visual reconstruction: lines 13–18 from `00-10-00.png`; 19–43 from `00-09-00.png`, independently overlapping 19–38 at 10:00. The earlier 1–12 are off-screen, so the setup from a newer driver has not been spliced in. The first group stops after three requests; the next restarts and catches the fourth request; the final group interleaves two independent generators. Line 39 is visibly commented out.

```text
 13 | print( jenny )
 14 | print( jenny.__next__( ) )
 15 | print( jenny.__next__( ) )
 16 | print( jenny.__next__( ) )
 17 | #print( jenny.__next__( ) )
 18 |
 19 | print( )
 20 | jenny = fruit.genny()
 21 | print( jenny )
 22 | try:
 23 |     print( jenny.__next__( ) )
 24 |     print( jenny.__next__( ) )
 25 |     print( jenny.__next__( ) )
 26 |     print( jenny.__next__( ) )
 27 | except StopIteration:
 28 |     print( "List exhausted." )
 29 |
 30 | print( )
 31 | jenny = fruit.genny()
 32 | print( jenny )
 33 | jenifer = fruit.genny()
 34 | print( jenifer )
 35 | try:
 36 |     print( jenny.__next__( ) )
 37 |     print( jenifer.__next__( ) )
 38 |     print( jenny.__next__( ) )
 39 | #    print( jenifer.__next__( ) )
 40 |     print( jenny.__next__( ) )
 41 |     print( jenifer.__next__( ) )
 42 | except StopIteration:
 43 |     print( "List exhausted." )
```

**Observed run at 09:00:** `py .\test\fruit.py` first prints the three fruit names. It then displays a generator description with address `0x0000023D35540E10` followed by apple/banana/grape. A second description ends in `0x0000023D355FD080`, followed by apple/banana/grape and `List exhausted.`. The final two descriptions use those two addresses in that order; the ensuing lines are apple/apple/banana/grape/banana. These are addresses from this run only. There is no subscript failure in this first run.

**2.3 · Separate edit states of line 45.** `00-11-00.png`, `00-11-02.png`, `00-12-00.png`, respectively; line 44 is blank. The middle buffer offers `fruit` as a completion; the suggestion is not completed code. Only the last state matches the terminal traceback in `00-13-00.png`.

```text
 45 | print
```

```text
 45 | print(fru)
```

```text
 45 | print(fruit[1])
```

**Observed outcome:** `TypeError: 'Item' object is not subscriptable` at line 45, *after* the generator output. A first `git checkout master` was refused because of the changed `test/fruit.py`. Subsequent scrollback (`00-21-09.png`) confirms `Aborting`, `git restore .\test\fruit.py`, a second `git checkout master`, then `Switched to branch 'master'`.

**2.4 · `src/item.py`, earlier generator checkout, callback/iterator/count fragment.** `00-11-00.png`. Lines 110–114 detach the current node in a preceding method; 116–122 send each payload to `caller.withItem`. The two iteration methods use the first node to hold the cursor. This file has `count`, not `__len__`, at line 137. The visible older version already checks the empty-head payload at 139–142; the instructor later wonders aloud whether the earlier counter handled that case. The bottom of this earlier counter is off-screen; do not complete it from the branch version.

```text
110 |                     self._next._prev = self._prev
111 |                     rest = self._prev
112 |         self._prev = None
113 |         self._next = None
114 |         return rest
115 |
116 |     def forEach(self, caller):
117 |         iter = self.first( )
118 |         while True:
119 |             caller.withItem(iter._object)
120 |             if iter._next == None:
121 |                 break
122 |             iter = iter._next
123 |
124 |     def __iter__(self):
125 |         head = self.first( )
126 |         head._iter = head
127 |         return head._iter
128 |
129 |     def __next__(self):
130 |         head = self.first( )
131 |         if head._iter == None:
132 |             raise StopIteration
133 |         target = head._iter._object
134 |         head._iter = head._iter._next
135 |         return target
136 |
137 |     def count(self):
138 |         iter = self.first( )
139 |         if iter._object == None:
140 |             count = 0
141 |         else:
142 |             count = 1
143 |         while True:
144 |             if iter._next == None:
145 |                 break
```

#### Screen sequence 3 — branch driver, class methods and length experiments

**3.1 · `test/fruit.py` restored branch driver.** `00-14-00.png`: line 10 immediately follows line 9. Compare `00-15-00.png` onward: lines 10 and 11 are then blank and the same expression is at line 12 in the editor. The traceback is from line **10**, so it must not be described as a line-12 execution.

```text
  1 | from item import Item
  2 |
  3 | fruit = Item( )
  4 | fruit = fruit.append( "apple" )
  5 | fruit = fruit.append( "banana" )
  6 | fruit = fruit.append( "grape" )
  7 |
  8 | for x in fruit:
  9 |     print(x)
 10 | print(len(fruit))
```

Later editor layout, last lines only:

```text
  9 |     print(x)
 10 |
 11 |
 12 | print(len(fruit))
```

**3.2 · `src/item.py`, branch version: detachment, visiting, iteration, length and searches.** Assembled visual reconstruction of compatible *displayed ranges*, not a dump of a saved file: 98–133 from `00-15-48.png`; 134–148 from `00-17-00.png` (overlap at 124–133); 149–169 from `00-16-00.png`; 170–171 independently visible in `00-14-00.png` (overlap at 167–169). The method header above line 98 is not visible; the end of its guard is clipped. Lines 99–114 reconnect surviving neighbors, remove both links from the extracted node, and return the rest. Lines 116–135 visit or iterate without removing anything; 137–148 count without consuming the iteration cursor. The `at` and `has` searches at 150 and 162 return nodes, not strings.

```text
 98 |         if self._prev == None and self._next == None[remainder not visible]
 99 |             rest = Item( )
100 |         else:
101 |             if self._prev == None:
102 |                 self._next._prev = None
103 |                 rest = self._next
104 |             else:
105 |                 if self._next == None:
106 |                     self._prev._next = None
107 |                     rest = self._prev
108 |                 else:
109 |                     self._prev._next = self._next
110 |                     self._next._prev = self._prev
111 |                     rest = self._prev
112 |         self._prev = None
113 |         self._next = None
114 |         return rest
115 |
116 |     def forEach(self, caller):
117 |         iter = self.first( )
118 |         while True:
119 |             caller.withItem(iter._object)
120 |             if iter._next == None:
121 |                 break
122 |             iter = iter._next
123 |
124 |     def __iter__(self):
125 |         head = self.first( )
126 |         head._iter = head
127 |         return head._iter
128 |
129 |     def __next__(self):
130 |         head = self.first( )
131 |         if head._iter == None:
132 |             raise StopIteration
133 |         target = head._iter._object
134 |         head._iter = head._iter._next
135 |         return target
```

```text
137 |     def __len__(self):
138 |         iter = self.first( )
139 |         if iter._object == None:
140 |             count = 0
141 |         else:
142 |             count = 1
143 |         while True:
144 |             if iter._next == None:
145 |                 break
146 |             iter = iter._next
147 |             count += 1
148 |         return count
149 |
150 |     def at(self, n):
151 |         iter = self.first( )
152 |         if iter._object != None:
153 |             count = 0
154 |             while count < n:
155 |                 if iter._next == None:
156 |                     iter = None
157 |                     raise Exception( "Index beyond e[remainder not visible]
158 |                 iter = iter._next
159 |                 count += 1
160 |         return iter
161 |
162 |     def has(self, obj):
163 |         if self._object != None:
164 |             iter = self.first( )
165 |             while iter != None:
166 |                 if iter._object == obj:
167 |                     break
168 |                 iter = iter._next
169 |         else:
170 |             iter = None
171 |         return iter
```

**3.3 · `src/item.py`, branch helper views: clear, convert, generate, save/load.** `00-14-00.png` supports 173–202; `00-07-00.png` independently shows 187–222. The overlapping bodies match, but the two views are separated by checkouts: the join documents compatible text, not uninterrupted editor history. Line 177 reaches the pane edge after the closing quote, so a closing parenthesis is not supplied. Lines 173–183 destroy links/payloads, 185–198 copy values between representations, 200–207 yield without destroying nodes, and 209–222 write/read text lines. No file I/O run is shown in these frames.

```text
173 |     def clear(self):
174 |         iter = self.first( )
175 |         while iter._next is not None:
176 |             iter = iter._next
177 |             print( f"clearing: {iter._prev._object}"[remainder not visible]
178 |             iter._prev._prev = None
179 |             iter._prev._next = None
180 |             iter._prev._object = None
181 |         print( f"clearing: {iter._object}" )
182 |         iter._prev = None
183 |         iter._object = None
184 |
185 |     def toList(self):
186 |         list = []
187 |         iter = self.first( )
188 |         if iter._object is not None:
189 |             while True:
190 |                 list.append(iter._object)
191 |                 if iter._next == None:
192 |                     break
193 |                 iter = iter._next
194 |         return list
195 |
196 |     def fromList(self, list):
197 |         for item in list:
198 |             self.append(item)
199 |
200 |     def genny(self):
201 |         iter = self.first( )
202 |         if iter._object is not None:
203 |             while True:
204 |                 yield iter._object
205 |                 if iter._next == None:
206 |                     break
207 |                 iter = iter._next
208 |
209 |     def toFile(self, filename):
210 |         with open(filename, "w") as file:
211 |             iter = self.first( )
212 |             if iter._object is not None:
213 |                 while True:
214 |                     file.write(iter._object + '\n')
215 |                     if iter._next == None:
216 |                         break
217 |                     iter = iter._next
218 |
219 |     def fromFile(self, filename):
220 |         with open(filename, "r") as file:
221 |             for item in file:
222 |                 self.append(item.strip())
```

**3.4 · `src/item.py` quote edit, not the saved failure state.** `00-18-00.png`. Two double-quote characters are visible *before the caret* at 137; three are visible at 150. Do not “correct” this to matching delimiters or infer that the observed failing run executed this buffer. Compare the active original definition at 137 in listing 3.2 with the shifted header below.

```text
137 |     ""
138 |     def __len__(self):
139 |         iter = self.first( )
140 |         if iter._object == None:
141 |             count = 0
142 |         else:
143 |             count = 1
144 |         while True:
145 |             if iter._next == None:
146 |                 break
147 |             iter = iter._next
148 |             count += 1
149 |         return count
150 |     """
151 |
152 |     def at(self, n):
```

**3.5 · `src/item.py` reactivated buffer.** `00-19-00.png`, `00-21-00.png`, `00-21-56.png` and `00-22-00.png`. At 137 and 150 the delimiter text is gone; the header is still at 138. The 21:56 caret rests at the `1` that counts the first nonempty node, and the selection at 22:00 emphasizes the loop. This buffer state alone does not prove that the corresponding saved code had run.

```text
137 |
138 |     def __len__(self):
139 |         iter = self.first( )
140 |         if iter._object == None:
141 |             count = 0
142 |         else:
143 |             count = 1
144 |         while True:
145 |             if iter._next == None:
146 |                 break
147 |             iter = iter._next
148 |             count += 1
149 |         return count
150 |
151 |
152 |     def at(self, n):
```

**Run record:** `00-21-09.png` shows an earlier success (`apple`, `banana`, `grape`, `3`) and a later line-10 failure (`apple`, `banana`, `grape`, then `TypeError: object of type 'Item' has no len()`). In `00-25-00.png`, that length failure appears twice, followed by a fresh `py .\test\fruit.py` and the four-line success. The three subsequent commands are `git restore .\test\fruit.py`, `git restore .\src\item.py` and `git checkout master`. The response to the last one is `Already on 'master'`. None of this terminal history makes the 18:00 mid-edit quotation state a runnable artifact.

### A.2 · Version history, branches, merges and bracket operations

#### The `Item` history behind the length and subscript examples (26:00–27:00)

At `00-26-00.png` the log marks the current commit `HEAD -> master`. The visible subject is `change count() to override __len__()`; below it are `get/set object value at index`, `Construct Item from list or file.`, `toFile, fromFile` and `before/after back to prev/next`. The following is the visible compact history at `00-27-00.png`, a later terminal state. Long lines are rejoined across terminal soft wraps. These are records of earlier work, **not** definitions of the methods or evidence that these operations were run in this discussion.

```text
1207cd3 get/set object value at index
f23376c Construct Item from list or file.
dae3d01 toFile, fromFile
2a9805c before/after back to prev/next
843e915 generator
52fc8cc fromList( list )
7160e4c toList( )
761d45c clear( ) to empty
0ec6bc1 has(object)
91be848 at(index)
3907fbe count
ec4fdd0 Item: python Iterator
ed154df Item: forEach( ), withItem( ) callback
a5c546d Item: extract( ). Cannot extract last value, but can check return for empty
8887c71 Item: empty list edge case
ab909a4 Item: first( ), prepend( ), insertBefore( )
7630f8b Item: insertAfter( ).
e01cc6d Item: avoid returning None.
7653923 Item: private attributes with accessor methods.
6cdb33d Item: call last( ) from append( ).
4847342 Item: addition of last( ).
fc6c333 Item: addition of append( ).
2499ca0 Item: addition of constructor.
0203781 Fruit: use of prev attribute.
f70c1ad Fruit: use of second reference to fruit.
47413b4 Item: with object, next, and prev attributes. Fruit: direct assignments to object, and next attributes.
```

#### `src/item.py`: iteration, length, positional and value search (32:00–44:00)

**Assembled visual reconstruction; numbers are editor gutter numbers, not Python to type.** Lines 123–149 are supported by `00-32-00.png` and `00-34-05.png`; lines 150–174 by `00-33-00.png` and `00-44-00.png`. Visible overlapping lines agree; this is not a claim about unobserved edits or a complete saved file. The previous method is cut off at the top of the first view. Line 157 runs beyond the right edge in the available views. The remainder of `clear` is below this view.

```text
123 |
124 |    def __iter__(self):
125 |        head = self.first( )
126 |        head._iter = head
127 |        return head._iter
128 |
129 |    def __next__(self):
130 |        head = self.first( )
131 |        if head._iter == None:
132 |            raise StopIteration
133 |        target = head._iter._object
134 |        head._iter = head._iter._next
135 |        return target
136 |
137 |    def __len__(self):
138 |        iter = self.first( )
139 |        if iter._object == None:
140 |            count = 0
141 |        else:
142 |            count = 1
143 |        while True:
144 |            if iter._next == None:
145 |                break
146 |            iter = iter._next
147 |            count += 1
148 |        return count
149 |
150 |    def at(self, n):
151 |        iter = self.first( )
152 |        if iter._object != None:
153 |            count = 0
154 |            while count < n:
155 |                if iter._next == None:
156 |                    iter = None
157 |                    raise Exception( "Index beyond e[remainder not visible]
158 |                iter = iter._next
159 |                count += 1
160 |        return iter
161 |
162 |    def has(self, obj):
163 |        if self._object != None:
164 |            iter = self.first( )
165 |            while iter != None:
166 |                if iter._object == obj:
167 |                    break
168 |                iter = iter._next
169 |        else:
170 |            iter = None
171 |        return iter
172 |
173 |    def clear(self):
174 |        iter = self.first( )
```

Lines 124–135 start from the first node, save a traversal position there, and return the current value before the next request. Lines 137–148 use a different, local traversal; the `while` aligns with the `if`, **outside** its `else`. Lines 150–160 return the positional node, not its payload; lines 162–171 return a matching node or `None`. The `clear` header and first statement establish only where its traversal starts. The successful name-and-length output is recorded with the corresponding driver below.

#### `test/fruit.py`: three distinct visible driver states

The right-hand editor pane first builds and prints a linked sequence, then adds a read from an unrelated built-in list, then changes an element in that built-in list. These listings are individually transcribed from the identified frames; line counts include visible trailing blank lines. The numbers belong to the editor gutter. The repeated invocation and all three completed outputs are fully visible together in `00-52-46.png`.

**State A — `00-32-00.png`, `00-44-00.png`.** Lines 3–6 create the sequence; lines 8–9 print one name per iteration; line 10 prints its length. No numeric built-in list appears in this state.

```text
 1 |from item import Item
 2 |
 3 |fruit = Item( )
 4 |fruit = fruit.append( "apple" )
 5 |fruit = fruit.append( "banana" )
 6 |fruit = fruit.append( "grape" )
 7 |
 8 |for x in fruit:
 9 |    print(x)
10 |print(len(fruit))
```

Observed first completed run:

```text
PS C:\com\joesta\Item> py .\test\fruit.py
apple
banana
grape
3
```

**State B — `00-44-51.png`, `00-45-00.png`.** Lines 12–13 create a new built-in list and print its second number. At this point the output ends with `5`; the assignment that later produces `9` is not in this buffer.

```text
 1 |from item import Item
 2 |
 3 |fruit = Item( )
 4 |fruit = fruit.append( "apple" )
 5 |fruit = fruit.append( "banana" )
 6 |fruit = fruit.append( "grape" )
 7 |
 8 |for x in fruit:
 9 |    print(x)
10 |print(len(fruit))
11 |
12 |asd = [4,5,6]
13 |print(asd[1])
14 |
```

Observed second completed run:

```text
PS C:\com\joesta\Item> py .\test\fruit.py
apple
banana
grape
3
5
```

**State C — `00-46-00.png`, `00-52-00.png`.** Lines 14–15 update the same built-in list and read it again. No subscript in this driver uses `fruit` as its receiver.

```text
 1 |from item import Item
 2 |
 3 |fruit = Item( )
 4 |fruit = fruit.append( "apple" )
 5 |fruit = fruit.append( "banana" )
 6 |fruit = fruit.append( "grape" )
 7 |
 8 |for x in fruit:
 9 |    print(x)
10 |print(len(fruit))
11 |
12 |asd = [4,5,6]
13 |print(asd[1])
14 |asd[1] = 9
15 |print(asd[1])
16 |
17 |
```

Observed third completed run:

```text
PS C:\com\joesta\Item> py .\test\fruit.py
apple
banana
grape
3
5
9
```

**Command-state check.** Above these runs the full foreground shell at `00-52-46.png` shows `git branch` → `* master`, then `git checkout master` → `Already on 'master'`. Beneath the third output the command `py .\test\fruit.py` is present again with the caret at its end and no output below it. It is not counted as a fourth completed run. The restore and checkout mentioned in the 52-minute speech are not terminal results in this image.

#### `src/item.py`: file input and the subscript protocol (47:00–52:00)

**Observed partial file** (`00-47-00.png`, `00-48-25.png`, `00-50-00.png`; same numbered region, with different text selections). The first two lines are the tail of a preceding block; its header is outside the view. Lines 219–222 read one file line at a time; 224–234 walk to the requested position and return the payload; 236–247 walk to it and replace that payload. On the right, the fruit driver uses the built-in list for its numeric example; the left pane shows the custom implementation, not a new terminal test of it.

```text
216 |                        break
217 |                    iter = iter._next
218 |
219 |    def fromFile(self, filename):
220 |        with open(filename, "r") as file:
221 |            for item in file:
222 |                self.append(item.strip())
223 |
224 |    def __getitem__(self, index):
225 |        iter = self.first( )
226 |        if iter._object != None:
227 |            count = 0
228 |            while count < index:
229 |                if iter._next == None:
230 |                    iter = None
231 |                    raise Exception( "Index beyond e[remainder not visible]
232 |                iter = iter._next
233 |                count += 1
234 |        return iter._object
235 |
236 |    def __setitem__(self, index, value):
237 |        iter = self.first( )
238 |        if iter._object != None:
239 |            count = 0
240 |            while count < index:
241 |                if iter._next == None:
242 |                    iter = None
243 |                    raise Exception( "Index beyond e[remainder not visible]
244 |                iter = iter._next
245 |                count += 1
246 |            iter._object = value
247 |        return None
248 |
```

Line 234 is outside the nonempty guard, whereas line 246 is inside the guard but **outside the loop**. That difference matters: a valid index of zero needs no loop iterations, yet still permits a replacement in a nonempty first node. Lines 231 and 243 contain partial strings; their unseen suffixes and closing punctuation are deliberately not supplied.

### A.3 · Membership and the missing result

#### `fruit.py`: the driver still on screen at 00:53:00

Numbers at the left are the editor gutter, **not** Python source to type. This is the end of the earlier length/index demonstration, before the `master~9` membership checkout. Lines 4–6 keep the node returned by each append. Lines 12–15 read index one of a separate built-in list, overwrite that element and read it again.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit = fruit.append( "apple" )
 5 | fruit = fruit.append( "banana" )
 6 | fruit = fruit.append( "grape" )
 7 |
 8 | for x in fruit:
 9 |     print(x)
10 | print(len(fruit))
11 |
12 | asd = [4,5,6]
13 | print(asd[1])
14 | asd[1] = 9
15 | print(asd[1])
```

Observed terminal history already contains three invocations of `py .\test\fruit.py`. All print `apple`, `banana`, `grape`, `3` on separate lines. The second has an additional `5`; the third has `5` and `9`. The final output is consistent with the full buffer above; the earlier outputs are earlier runs, not three outcomes of the same unchanged file. The PowerShell window obscures most of the adjacent older `item.py`; fragments behind it are not used to fill any of the membership checkout's code.

#### `test\fruit.py`: checkout `0ec6bc1 has(object)` (00:54–01:15)

Shown in full at `00-55-00.png`; line 24 is blank in this state. Unlike the driver above, the name `fruit` stays on apple. Lines 8–15 unwrap three found nodes; lines 17–22 print two misses directly. Terminal details are recovered from `01-01-56.png` (checkout) and `01-02-00.png` (run), not from assumptions about tab focus.

```text
 1 | from item import Item
 2 |
 3 | b = "banana"
 4 | fruit = Item( "apple" )
 5 | fruit.append( b )
 6 | fruit.append("grape")
 7 |
 8 | one = fruit.has( b )
 9 | print( one.object( ) )
10 |
11 | one = fruit.has( "apple" )
12 | print( one.object( ) )
13 |
14 | one = fruit.has( "grape" )
15 | print( one.object( ) )
16 |
17 | one = fruit.has( "kiwi" )
18 | print( one )
19 |
20 | two = Item( )
21 | one = two.has( "kiwi" )
22 | print( one )
23 |
24 |
```

**Observed PowerShell, selected lines.** Git’s advice between the checkout and confirmation is omitted here. A `git checkout master` command is sitting at the prompt at 01:01:56, without confirmation that it executed. The 01:02 run follows the detached-HEAD confirmation, not a verified switch back to `master`.

```text
PS C:\com\joesta\Item> git restore .\test\fruit.py
PS C:\com\joesta\Item> git restore .\src\item.py
PS C:\com\joesta\Item> git checkout master~9
Note: switching to 'master~9'.
```

```text
HEAD is now at 0ec6bc1 has(object)
PS C:\com\joesta\Item> py .\test\fruit.py
banana
apple
grape
None
None
```

#### `src\item.py`: recoverable portions of the membership-era editor

**Assembled visual reconstruction, not one proven saved file.** The gutter and overlapping methods identify the sections below. The display samples an editor, not the repository’s bytes. Later frames show an editor modification indicator; matching visible lines can be compared but cannot prove that off-screen lines remained unchanged. Line ranges omitted between blocks were not legible in these views. The bracketed suffix markers are editorial, not code.

**Lines 1–34 — `01-05-00.png`, `01-06-00.png`; later view `01-19-00.png`.** The defaults describe a node with no links or saved traversal position. Initialization sets its own payload; the accessors return that payload or a neighboring node. Append fills an empty receiver or adds a fresh tail and returns it.

```text
  1 | class Item:
  2 |     _object = None
  3 |     _next = None
  4 |     _prev = None
  5 |     _iter = None
  6 |
  7 |     def __init__(self, obj = None):
  8 |         self._object = obj
  9 |
 10 |     def object(self):
 11 |         return self._object
 12 |
 13 |     def after(self):
 14 |         if self._next != None:
 15 |             return self._next
 16 |         return self
 17 |
 18 |     def before(self):
 19 |         if self._prev != None:
 20 |             return self._prev
 21 |         return self
 22 |
 23 |     def append(self, obj):
 24 |         if self._object == None:
 25 |             self._object = obj
 26 |             newItem = self
 27 |         else:
 28 |             last = self.last()
 29 |
 30 |             newItem = Item(obj)
 31 |             newItem._prev = last
 32 |             last._next = newItem
 33 |         return newItem
 34 |
```

**Lines 40–73 — `01-17-00.png`, `01-18-00.png`.** The header/first branch before line 40 is off-screen. The visible tail links a new node before the first; `last` and `first` then walk to opposite ends without passing the last valid node. `insertAfter` redirects both links when the receiver already has a successor; the body of line 73’s alternate branch and its continuation are outside the view.

```text
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
 71 |                 self._next._prev = newItem
 72 |                 self._next = newItem
 73 |             else:
```

**Lines 77–112 — `00-57-00.png` / `00-58-00.png` for 77–110; `00-54-00.png` / `00-55-00.png` for 92–112.** The overlap fixes the placement of the two methods. `insertBefore` fills the empty receiver or puts a new node between it and its predecessor. `extract` identifies the remaining list, repairs the gap and clears both links on the detached receiver. Line 96 ends at the pane edge; its last punctuation is not transcribed.

```text
 77 |
 78 |     def insertBefore(self, obj):
 79 |         if self._object == None:
 80 |             self._object = obj
 81 |             newItem = self
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
 96 |         if self._prev == None and self._next == None[remainder not visible]
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
```

**Lines 113–133 — `00-55-00.png` and `01-07-00.png` (overlap 113–125).** `forEach` offers a payload to another object's method before checking whether it reached the tail. In the iterator protocol, the head stores the current position. Each successful next call saves the payload before advancing, and the call after the last payload raises.

```text
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
```

**Lines 139–169 — `01-01-00.png` shows 140 onward; `01-15-00.png` and `01-16-00.png` include the preceding `else:` on 139.** The count fragment increments only after moving. `at` returns the node reached by counting steps (or raises when a needed next link is absent); `has` stops on an equal payload or returns `None`. The end of the exception literal and its closing syntax on 155 are not visible.

```text
139 |         else:
140 |             count = 1
141 |         while True:
142 |             if iter._next == None:
143 |                 break
144 |             iter = iter._next
145 |             count += 1
146 |         return count
147 |
148 |     def at(self, n):
149 |         iter = self.first( )
150 |         if iter._object != None:
151 |             count = 0
152 |             while count < n:
153 |                 if iter._next == None:
154 |                     iter = None
155 |                     raise Exception( "Index beyond e[remainder not visible]
156 |                 iter = iter._next
157 |                 count += 1
158 |         return iter
159 |
160 |     def has(self, obj):
161 |         if self._object != None:
162 |             iter = self.first( )
163 |             while iter != None:
164 |                 if iter._object == obj:
165 |                     break
166 |                 iter = iter._next
167 |         else:
168 |             iter = None
169 |         return iter
```

#### `test\fruit.py`: later editor change (01:16–01:20), no observed run

The `01-16-00.png` right pane still shows lines 1–22 of the five-search driver above, but the formerly blank last line now assigns a subscript expression. The modified-tab indicator is visible; no new invocation or result for this line appears. The previous terminal's `None` is not the value of `x`.

```text
23 |
24 | x = fruit[1]
25 |
```

### A.4 · Nodes, references and clearing the chain

#### `fruit.py`: search driver, followed by an indexing edit (01:21–01:35)

Numbers before the vertical bars reproduce the editor gutter; do not type them. In this buffer the first three searches print the stored values, the fourth prints a missing match, and the fifth searches a separate empty Item. Line 24 is visible in the editor, not established as part of the run in the underlying PowerShell window.

**Earlier buffer — `01-21-00.png`, also `01-25-00.png`.**

```text
  1 | from item import Item
  2 |
  3 | b = "banana"
  4 | fruit = Item( "apple" )
  5 | fruit.append( b )
  6 | fruit.append("grape")
  7 |
  8 | one = fruit.has( b )
  9 | print( one.object( ) )
 10 |
 11 | one = fruit.has( "apple" )
 12 | print( one.object( ) )
 13 |
 14 | one = fruit.has( "grape" )
 15 | print( one.object( ) )
 16 |
 17 | one = fruit.has( "kiwi" )
 18 | print( one )
 19 |
 20 | two = Item( )
 21 | one = two.has( "kiwi" )
 22 | print( one )
 23 |
 24 | x = fruit[1]
 25 |
```

**Later buffer — `01-26-00.png`, confirmed at `01-27-06.png`.** Lines 1–23 remain as displayed above; the following is the later ending, not a continuation after the blank line 25 in the earlier listing. The assignment does not print a result.

```text
 24 | x = fruit[1]
 25 | y = fruit.at(1)
 26 |
 27 |
```

**Observed PowerShell scrollback** (`01-21-00.png`, checked again at `01-35-00.png`). The editor conceals the remainder of the invocation. Output order agrees with the five search prints. No captured command proves the bracket or named index assignment was run.

```text
PS C:\com\joesta\Item> py .\test\fr[remainder not visible]
banana
apple
grape
None
None
```

#### `item.py`: earlier edited buffers, with the search checkout in terminal history (01:21–01:35)

**Assembled visual reconstruction, two separate visible ranges.** The buffers show modification dots. Matching lines in overlapping windows support the displayed ranges; these are not claimed to be a saved copy of commit `0ec6bc1`. Lines 1–34 are from `01-21-00.png` and `01-22-00.png`; 35–43 from `01-24-00.png`; 44–73 from `01-27-00.png` / `01-27-06.png`, which also corroborate 40–43. The class supplies defaults and initializes a payload; the two directional accessors stay on the current node at the boundary. Appending/prepending may reuse an empty node or allocate and link a new one; the endpoint walkers return nodes, not fruit strings.

```text
  1 | class Item:
  2 |     _object = None
  3 |     _next = None
  4 |     _prev = None
  5 |     _iter = None
  6 |
  7 |     def __init__(self, obj = None):
  8 |         self._object = obj
  9 |
 10 |     def object(self):
 11 |         return self._object
 12 |
 13 |     def after(self):
 14 |         if self._next != None:
 15 |             return self._next
 16 |         return self
 17 |
 18 |     def before(self):
 19 |         if self._prev != None:
 20 |             return self._prev
 21 |         return self
 22 |
 23 |     def append(self, obj):
 24 |         if self._object == None:
 25 |             self._object = obj
 26 |             newItem = self
 27 |         else:
 28 |             last = self.last()
 29 |
 30 |             newItem = Item(obj)
 31 |             newItem._prev = last
 32 |             last._next = newItem
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
 71 |                 self._next._prev = newItem
 72 |                 self._next = newItem
 73 |             else:
```

The next earlier-revision range begins mid-method. Lines 74–102 are not recovered *in this state* in these frames. `01-35-00.png` supplies 103–136; `01-30-00.png` and `01-31-00.png` confirm the overlap 109 onward; `01-26-00.png`, `01-28-00.png` and `01-33-00.png` supply 133–166. The upper fragment reconnects the remainder of a chain and detaches the current node. `forEach` hands each payload to the caller; the iterator keeps its next position on the first node; `count` adds forward hops; `at` returns a node or can raise. The last visible search line advances past a nonmatch.

```text
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
135 |     def count(self):
136 |         iter = self.first( )
137 |         if iter._object == None:
138 |             count = 0
139 |         else:
140 |             count = 1
141 |         while True:
142 |             if iter._next == None:
143 |                 break
144 |             iter = iter._next
145 |             count += 1
146 |         return count
147 |
148 |     def at(self, n):
149 |         iter = self.first( )
150 |         if iter._object != None:
151 |             count = 0
152 |             while count < n:
153 |                 if iter._next == None:
154 |                     iter = None
155 |                     raise Exception( "Index beyond e[remainder not visible]
156 |                 iter = iter._next
157 |                 count += 1
158 |         return iter
159 |
160 |     def has(self, obj):
161 |         if self._object != None:
162 |             iter = self.first( )
163 |             while iter != None:
164 |                 if iter._object == obj:
165 |                     break
166 |                 iter = iter._next
```

The message on line 155 extends beyond the pane. Lines 167 onward are not used to complete this earlier editor state; the later checkout independently exposes them.

#### `fruit.py`: clearing driver after checkout (01:36–01:47)

**Observed — `01-36-00.png`; confirmed at `01-46-00.png` and `01-47-00.png`.** The instructor describes restoring the two edited files before checkout and reloading both buffers. The terminal shows two `git restore` command prefixes, a `git checkout` prefix, the old position `0ec6bc1` and the new position `761d45c`; their argument strings extend behind the editor. The earlier detached-HEAD advice (`git switch -c <new-branch-name>`, `git switch -`) was Git help, not commands shown being executed.

```text
  1 | from item import Item
  2 |
  3 | b = "banana"
  4 | fruit = Item( "apple" )
  5 | fruit.append( b )
  6 | fruit.append("grape")
  7 |
  8 | fruit.clear( )
  9 |
 10 | print(f"cleared: {fruit}, {fruit.object()}")
 11 |
 12 | print( fruit._next )
```

Lines 3–6 rebuild the three nodes; line 8 empties them; lines 10 and 12 query the surviving receiver. The displayed terminal search output predates this driver. No clearing output is observed in these frames.

#### `item.py`: later checkout, insertion, extraction opening and clearing (01:36–01:47)

**Assembled visual reconstruction of separated ranges, not a complete file.** At `01-37-00.png` / `01-38-00.png`, lines 1–34 independently match the header, initializer, accessors and append code in the earlier listing above. Lines 35–60 are off screen in this later checkout. `01-39-00.png` gives lines 61–94; `01-40-00.png` confirms the overlap from 66 and supplies 95–99. Each insertion handles an empty node, an internal neighbor, or an endpoint. Extraction starts by testing whether both links are missing; the right edge conceals the suffix of line 96.

```text
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
 71 |                 self._next._prev = newItem
 72 |                 self._next = newItem
 73 |             else:
 74 |                 newItem._prev = self
 75 |                 self._next = newItem
 76 |         return newItem
 77 |
 78 |     def insertBefore(self, obj):
 79 |         if self._object == None:
 80 |             self._object = obj
 81 |             newItem = self
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
 96 |         if self._prev == None and self._next == None[remainder not visible]
 97 |             rest = Item( )
 98 |         else:
 99 |             if self._prev == None:
```

The later checkout’s lines 100–138 are not reconstructed from these frames. For lines 139–172 see `01-36-00.png`; for the overlap and remainder see `01-41-00.png`, `01-42-18.png`, `01-44-00.png`, `01-45-52.png` and `01-47-00.png`. A return of `iter` from the search means either the matching node or `None`. In `clear`, line 174 moves before lines 176–178 erase the predecessor; lines 179–181 handle the tail after the loop. The first diagnostic print’s closing punctuation is outside the actual editor pane, while the second diagnostic fits.

```text
139 |         else:
140 |             count = 1
141 |         while True:
142 |             if iter._next == None:
143 |                 break
144 |             iter = iter._next
145 |             count += 1
146 |         return count
147 |
148 |     def at(self, n):
149 |         iter = self.first( )
150 |         if iter._object != None:
151 |             count = 0
152 |             while count < n:
153 |                 if iter._next == None:
154 |                     iter = None
155 |                     raise Exception( "Index beyond e[remainder not visible]
156 |                 iter = iter._next
157 |                 count += 1
158 |         return iter
159 |
160 |     def has(self, obj):
161 |         if self._object != None:
162 |             iter = self.first( )
163 |             while iter != None:
164 |                 if iter._object == obj:
165 |                     break
166 |                 iter = iter._next
167 |         else:
168 |             iter = None
169 |         return iter
170 |
171 |     def clear(self):
172 |         iter = self.first( )
173 |         while iter._next is not None:
174 |             iter = iter._next
175 |             print( f"clearing: {iter._prev._object}"[remainder not visible]
176 |             iter._prev._prev = None
177 |             iter._prev._next = None
178 |             iter._prev._object = None
179 |         print( f"clearing: {iter._object}" )
180 |         iter._prev = None
181 |         iter._object = None
182 |
183 |
```

Line 161 uses `!= None`; line 173 uses `is not None`. At `01-41-00.png` the former operator is selected, not evidence that the clear method was rewritten with a different operator. Later highlights on individual link assignments likewise do not establish a source revision.

### A.5 · List conversion, shadowing and file constructors

#### Screen record: converting `Item` data and testing the name `list` (01:48–02:03)

Numbers left of `|` are editor gutter numbers, not Python to type. A marked suffix was hidden by a pane boundary or camera overlay. The quoted listings show editor states, not a promise that a particular buffer was saved or run. Times identify source PNG targets.

**`src/item.py` — 01:48:00, `toList` checkout.** The preceding indexed-traversal fragment counts links, raises if it cannot advance, and otherwise returns the reached node. The following search returns a matching node or `None`; clearing moves ahead before disconnecting the node behind it. The conversion differs from clearing: it does not disconnect any nodes.

```text
151 |             count = 0
152 |             while count < n:
153 |                 if iter._next == None:
154 |                     iter = None
155 |                     raise Exception( "Index beyond e[remainder not visible]
156 |                 iter = iter._next
157 |                 count += 1
158 |         return iter
159 |
160 |     def has(self, obj):
161 |         if self._object != None:
162 |             iter = self.first( )
163 |             while iter != None:
164 |                 if iter._object == obj:
165 |                     break
166 |                 iter = iter._next
167 |         else:
168 |             iter = None
169 |         return iter
170 |
171 |     def clear(self):
172 |         iter = self.first( )
173 |         while iter._next is not None:
174 |             iter = iter._next
175 |             print( f"clearing: {iter._prev._object}"[remainder not visible]
176 |             iter._prev._prev = None
177 |             iter._prev._next = None
178 |             iter._prev._object = None
179 |         print( f"clearing: {iter._object}" )
180 |         iter._prev = None
181 |         iter._object = None
182 |
183 |     def toList(self):
184 |         list = []
```

**`src/item.py` — 01:49:00, lines 183–192.** A new built-in list receives one stored object per linked node. Line 188's closing parenthesis is under the pointer at 01:49; the entire line is independently legible at 01:50 in the next checkout, which retains the method. The later view supports the recovered character, not an uninterrupted observation of the saved file.

```text
183 |     def toList(self):
184 |         list = []
185 |         iter = self.first( )
186 |         if iter._object is not None:
187 |             while True:
188 |                 list.append(iter._object)
189 |                 if iter._next == None:
190 |                     break
191 |                 iter = iter._next
192 |         return list
```

**`test/fruit.py` — 01:48:00–01:49:00, `7160e4c toList( )`.** Apple starts the chain; banana and grape are appended without rebinding `fruit`. The loop iterates the Python list returned by the method, not the custom nodes. Earlier terminal scrollback is from the `has(object)` demonstration, not output evidence for this version.

```text
 1 | from item import Item
 2 |
 3 | b = "banana"
 4 | fruit = Item( "apple" )
 5 | fruit.append( b )
 6 | fruit.append("grape")
 7 |
 8 | list = fruit.toList( )
 9 | for item in list:
10 |     print( item )
```

**`src/item.py` and `test/fruit.py` — 01:50:00, `52fc8cc fromList( list )`.** The left pane retains lines 183–192 printed above and adds these lines below; right pane starts with a built-in list and iterates the custom result. The screen definition popup at 01:52 says `Definition of append`, `item.py:23`, `References`, `item.py:188`; those matching names do not imply identical receiver types.

```text
193 |
194 |     def fromList(self, list):
195 |         for item in list:
196 |             self.append(item)
```

```text
 1 | from item import Item
 2 |
 3 | list = ["apple", "banana", "grape"]
 4 | fruit = Item( )
 5 | fruit.fromList( list )
 6 | for item in fruit:
 7 |     print( item )
```

At 01:53:00 a blank line 8 is visible; the 01:53:20 terminal shows `apple`, `banana`, `grape` on separate lines and a returned prompt. The invocation `py .\test\fruit.py` is fully legible in the 01:56:00 scrollback, which shows this three-line output and a separate later six-line output.

**`test/fruit.py` — two intermediate editor states.** Lines 1–7 were unchanged. At 01:54:00 the copied loop still reads `fruit`:

```text
 8 | for item in fruit:
 9 |     print( item )
```

At 01:55:00 it reads `list` instead; the in-progress definition below it has no visible body yet. The earlier six-line success is not a run of this unfinished definition.

```text
 8 | for item in list:
 9 |     print( item )
10 |
11 | def list( ):
12 |
```

**`test/fruit.py` — 01:56:00.** Lines 1–7 still create the chain from the Python list and print its fruit. Executing line 10 rebinds `list` to a function before line 13 attempts iteration. This arrangement remains legible at the enlarged scale at 01:57–01:58; differences in font are not differences in case.

```text
 8 |
 9 |
10 | def list( ):
11 |     pass
12 |
13 | for item in list:
14 |     print( item )
```

Observed after the three fruit lines, line 13: `TypeError: 'function' object is not iterable`.

**`test/fruit.py` — 01:59:00.** Lines 1–7 remain the same. Line 9 now tries to call the list instance; because the exception occurs first, the later `def` isn't executed on this run.

```text
 8 |
 9 | list()
10 |
11 | def list( ):
12 |     pass
13 |
14 | for item in list:
15 |     print( item )
```

Observed after the three fruit lines, line 9: `TypeError: 'list' object is not callable`. At 02:00:09 the terminal separately preserves a run with `q = list()` on line 9 and the same error. That traceback establishes the changed statement, not the complete intervening editor buffer.

**`test/fruit.py` — 02:00:00, two-call intermediate editor state.** `q = list()` occurs above and below the fruit setup. No traceback at line 11 was observed for this exact buffer.

```text
 1 | from item import Item
 2 |
 3 | q = list()
 4 |
 5 | list = ["apple", "banana", "grape"]
 6 | fruit = Item( )
 7 | fruit.fromList( list )
 8 | for item in fruit:
 9 |     print( item )
10 |
11 | q = list()
12 |
13 | def list( ):
14 |     pass
15 |
16 | for item in list:
17 |     print( item )
```

**`test/fruit.py` — 02:00:09–02:03:00, upper call only.** The removed lower call allows execution to reach the definition. This time the built-in construction at line 3 works, the three values print, then the later iteration fails. The last traceback's complete gutter reference is legible in the 02:04 terminal after a new checkout; it is not a generator-run error.

```text
 1 | from item import Item
 2 |
 3 | q = list()
 4 |
 5 | list = ["apple", "banana", "grape"]
 6 | fruit = Item( )
 7 | fruit.fromList( list )
 8 | for item in fruit:
 9 |     print( item )
10 |
11 |
12 | def list( ):
13 |     pass
14 |
15 | for item in list:
16 |     print( item )
```

Observed after `apple`, `banana`, `grape`, line 15: `TypeError: 'function' object is not iterable`.

#### Screen record: generator driver, neighbors and file methods (02:04–02:11)

**`test/fruit.py` — 02:04:00, `843e915 generator`.** The driver rebinds `fruit` after each insertion, iterates the chain, then obtains two separate generator objects. The first manually requests three values and comments out the fourth request. The second uses a `try` block and an exhaustion handler; the camera covers the right ends of several lines. No generator output is established by the neighboring terminal (it shows the previous errors, file restores and checkout).

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit = fruit.append( "apple" )
 5 | fruit = fruit.append( "banana" )
 6 | fruit = fruit.append( "grape" )
 7 |
 8 | for item in fruit:
 9 |     print( item )
10 |
11 | print( )
12 | jenny = fruit.genny()
13 | print( jenny )
14 | print( jenny.__next__( ) )
15 | print( jenny.__next__( ) )
16 | print( jenny.__next__( ) )
17 | #print( jenny.__next__( ) )
18 |
19 | print( )
20 | jenny = fruit.genny()
21 | print( jenny )
22 | try:
23 |     print( jenny.__next__( ) )
24 |     print( jenny.__next__[remainder not visible]
25 |     print( jenny.__next__[remainder not visible]
26 |     print( jenny.__next__[remainder not visible]
27 | except StopIteration:
28 |     print( "List exhauste[remainder not visible]
```

**`src/item.py` — 02:05:00, neighbor and append revision.** `object` returns the stored object; `next` and `prev` return nodes, falling back to `self` at a boundary. `append` either fills the receiver or constructs a new node, connects both directions, and returns that node. The terminal history at 02:06 identifies the intervening checkout as `2a9805c before/after back to prev/next`.

```text
 7 |     def __init__(self, obj = None):
 8 |         self._object = obj
 9 |
10 |     def object(self):
11 |         return self._object
12 |
13 |     def next(self):
14 |         if self._next != None:
15 |             return self._next
16 |         return self
17 |
18 |     def prev(self):
19 |         if self._prev != None:
20 |             return self._prev
21 |         return self
22 |
23 |     def append(self, obj):
24 |         if self._object == None:
25 |             self._object = obj
26 |             newItem = self
27 |         else:
28 |             last = self.last()
29 |
30 |             newItem = Item(obj)
31 |             newItem._prev = last
32 |             last._next = newItem
33 |         return newItem
```

**`src/item.py` — `dae3d01 toFile, fromFile`, 02:07:00–02:10:00.** The method between the conversions yields one value at a time. The file writer uses the same “process the node, then test its link” structure, but writes a newline after each value. The reader strips then appends each line. Line 212 is clipped in the narrow 02:07 view; the full line below is independently visible at 02:09:00, including the single quotes around `\n`. No matching file-operation driver run was shown.

```text
196 |             self.append(item)
197 |
198 |     def genny(self):
199 |         iter = self.first( )
200 |         if iter._object is not None:
201 |             while True:
202 |                 yield iter._object
203 |                 if iter._next == None:
204 |                     break
205 |                 iter = iter._next
206 |
207 |     def toFile(self, filename):
208 |         with open(filename, "w") as file:
209 |             iter = self.first( )
210 |             if iter._object is not None:
211 |                 while True:
212 |                     file.write(iter._object + '\n')
213 |                     if iter._next == None:
214 |                         break
215 |                     iter = iter._next
216 |
217 |     def fromFile(self, filename):
218 |         with open(filename, "r") as file:
219 |             for item in file:
220 |                 self.append(item.strip())
```

#### Screen record: constructor revision and return to `master` (02:11–02:18)

**`src/item.py` — 02:12:00–02:13:00, `f23376c Construct Item from list or file.`** This version places the search/clear section two lines later than the earlier captured one. The now wider pane reveals the entire first clearing print in this revision, but not the end of line 157. The upper edge partly covers line 157; the readable message extends through the period. Neither the exception nor a clear operation is demonstrated anew here.

```text
157 |                     raise Exception( "Index beyond end.[remainder not visible]
158 |                 iter = iter._next
159 |                 count += 1
160 |         return iter
161 |
162 |     def has(self, obj):
163 |         if self._object != None:
164 |             iter = self.first( )
165 |             while iter != None:
166 |                 if iter._object == obj:
167 |                     break
168 |                 iter = iter._next
169 |         else:
170 |             iter = None
171 |         return iter
172 |
173 |     def clear(self):
174 |         iter = self.first( )
175 |         while iter._next is not None:
176 |             iter = iter._next
177 |             print( f"clearing: {iter._prev._object}" )
178 |             iter._prev._prev = None
179 |             iter._prev._next = None
180 |             iter._prev._object = None
181 |         print( f"clearing: {iter._object}" )
182 |         iter._prev = None
183 |         iter._object = None
```

**`src/item.py` — 02:14:00–02:15:10, constructor and neighboring methods.** Lines 2–5 set class defaults; line 8 assigns an instance value. Each optional loader is tested independently, in list-then-file order. The remaining visible accessors still return node values or neighbors as before. Line 29 is the bottom of this view, not the end of `append`.

```text
 1 | class Item:
 2 |     _object = None
 3 |     _next = None
 4 |     _prev = None
 5 |     _iter = None
 6 |
 7 |     def __init__(self, obj=None, fromList=None, fromFile=None):
 8 |         self._object = obj
 9 |         if not fromList is None: self.fromList(fromList)
10 |         if not fromFile is None: self.fromFile(fromFile)
11 |
12 |     def object(self):
13 |         return self._object
14 |
15 |     def next(self):
16 |         if self._next != None:
17 |             return self._next
18 |         return self
19 |
20 |     def prev(self):
21 |         if self._prev != None:
22 |             return self._prev
23 |         return self
24 |
25 |     def append(self, obj):
26 |         if self._object == None:
27 |             self._object = obj
28 |             newItem = self
29 |         else:
```

**`test/fruit.py`, line 3 — three distinct editor states**, with the three append calls and generator driver still visible below. The temporary names illustrate argument positions. No successful run of either placeholder expression is established.

02:12:00–02:13:00:

```text
 3 | fruit = Item(stuff )
```

02:14:00:

```text
 3 | fruit = Item( )
```

02:15:00–02:16:00:

```text
 3 | fruit = Item(None, pylist )
```

**`src/item.py` — 02:16:00, shifted file-method section.** At these line numbers the writer still appends the newline; the reader still calls `strip` on the incoming line and then appends to the receiver. Opening uses the supplied `filename`; it is not a variable containing the file's text.

```text
205 |                 if iter._next == None:
206 |                     break
207 |                 iter = iter._next
208 |
209 |     def toFile(self, filename):
210 |         with open(filename, "w") as file:
211 |             iter = self.first( )
212 |             if iter._object is not None:
213 |                 while True:
214 |                     file.write(iter._object + '\n')
215 |                     if iter._next == None:
216 |                         break
217 |                     iter = iter._next
218 |
219 |     def fromFile(self, filename):
220 |         with open(filename, "r") as file:
221 |             for item in file:
222 |                 self.append(item.strip())
```

**`src/item.py` — 02:17:00, after return to `master` (confirmed in 02:18 scrollback).** The insertion branches use reciprocal links in opposite directions. The visible part of `last` stops when there is no successor; its ending is not visible and is not supplied as source.

```text
25 |     def append(self, obj):
26 |         if self._object == None:
27 |             self._object = obj
28 |             newItem = self
29 |         else:
30 |             last = self.last()
31 |
32 |             newItem = Item(obj)
33 |             newItem._prev = last
34 |             last._next = newItem
35 |         return newItem
36 |
37 |     def prepend(self, obj):
38 |         if self._object == None:
39 |             self._object = obj
40 |             newItem = self
41 |         else:
42 |             first = self.first()
43 |             newItem = Item(obj)
44 |             newItem._next = first
45 |             first._prev = newItem
46 |         return newItem
47 |
48 |     def last(self):
49 |         iter = self
50 |         while True:
51 |             if iter._next == None:
52 |                 break
53 |             iter = iter._next
```

**`test/fruit.py` — 02:17:00, scrolled fragment.** Only this driver line is visible in the right pane; its surrounding code and any fresh result are not established by this frame.

```text
10 | print(len(fruit))
```

**`test/fruit.py` — 02:18:00, loop driver.** The three assignments retain the node returned from each append. The loop requests each fruit value in order. The terminal shows `py .\test\fruit.py` at its bottom prompt with no following result at this target; earlier printed fruit and an error farther up are scrollback, not evidence of this run.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit = fruit.append( "apple" )
 5 | fruit = fruit.append( "banana" )
 6 | fruit = fruit.append( "grape" )
 7 |
 8 | for it in fruit:
 9 |     print(it)
```

### A.6 · Round trips, file contents and mode arguments

#### Screen record: converting the chain and writing `fruit.dat`

Numbers at the left are the editor gutter, **not Python to type**. Timestamps below identify the source PNG by its time (for example, 02:19:00 is `02-19-00.png`). Each listing is a visible state or an explicitly identified changed excerpt, not a claim that every buffer shown was saved or run.

**`item.py`, 02:19:00, lines 179–207.** The opening lines are the tail of an earlier method; its heading is above the viewport. The three following methods are complete in this frame. Lines 185–194 accumulate built-in list values and return that list; 196–198 append input values to the receiver; 200–207 pause at each yield rather than building a result list. None of these helpers prints; the diagnostic on line 181 belongs to the earlier method.

```text
179 |             iter._prev._next = None
180 |             iter._prev._object = None
181 |         print( f"clearing: {iter._object}" )
182 |         iter._prev = None
183 |         iter._object = None
184 |
185 |     def toList(self):
186 |         list = []
187 |         iter = self.first( )
188 |         if iter._object is not None:
189 |             while True:
190 |                 list.append(iter._object)
191 |                 if iter._next == None:
192 |                     break
193 |                 iter = iter._next
194 |         return list
195 |
196 |     def fromList(self, list):
197 |         for item in list:
198 |             self.append(item)
199 |
200 |     def genny(self):
201 |         iter = self.first( )
202 |         if iter._object is not None:
203 |             while True:
204 |                 yield iter._object
205 |                 if iter._next == None:
206 |                     break
207 |                 iter = iter._next
```

**`test\fruit.py`, 02:19:00 → 02:20:00.** At 02:19:00 lines 1–9 already match the full later listing below; the last in-progress line is:

```text
 11 | pylist
```

At 02:20:00 there is a completed second loop, followed by a member-access edit, not a completed file operation. The autocomplete menu below line 15 is not code.

```text
 11 | pylist = fruit.toList()
 12 | for it in pylist:
 13 |     print(it)
 14 |
 15 | fruit.
```

**`test\fruit.py`, 02:21:00, right pane.** Lines 3–6 start a new collection and keep the reference returned after each append. The first and second loops print the two representations; line 15 has a file effect, not a third printout.

```text
  1 | from item import Item
  2 |
  3 | fruit = Item( )
  4 | fruit = fruit.append( "apple" )
  5 | fruit = fruit.append( "banana" )
  6 | fruit = fruit.append( "grape" )
  7 |
  8 | for it in fruit:
  9 |     print(it)
 10 |
 11 | pylist = fruit.toList()
 12 | for it in pylist:
 13 |     print(it)
 14 |
 15 | fruit.toFile("fruit.dat")
 16 |
```

**PowerShell, 02:21:00–02:22:00.** Above the runs at 02:21:00 is *older* Git scrollback: `git checkout master~2` with `HEAD is now at f23376c Construct Item from list or file.`, followed by `git checkout master` and `Switched to branch 'master'`. Do not ascribe these driver runs to the earlier detached revision. The visible `py .\test\fruit.py` commands show, in order, one group, two groups, then two groups of output. Here a group means the literal three lines below. At 02:21:36 `dir` lists `22 fruit.dat` under Length/Name in `C:\com\joesta\Item`. At 02:22:00 the completed command below reads the file; a second `type fruit.dat` sits at the current prompt without a new result.

```text
PS C:\com\joesta\Item> type fruit.dat
apple
banana
grape
```

#### Screen record: isolating the read-back loop

**`test\fruit.py`, 02:23:00, new lines after the first file write.** An unquoted expression is visible while editing; no run of that expression is established. Compare the quoted version in the next state.

```text
 15 | fruit.toFile("fruit.dat")
 16 |
 17 | fruit.fromFile(fruit.dat)
```

**02:24:00, driver lines 7–18.** The original `for it in fruit:` above `pylist` has gone; its intended replacement is now below the read. Line 10 remains active without an executable body. Both file calls still use the original name.

```text
  7 |
  8 |
  9 | pylist = fruit.toList()
 10 | for it in pylist:
 11 |     #print(it)
 12 |
 13 | fruit.toFile("fruit.dat")
 14 |
 15 | fruit.fromFile("fruit.dat")
 16 | for it in fruit:
 17 |     print(it)
 18 |
```

**02:25:00 and 02:25:05, driver; changed filename, not yet the repaired loop.** The popup at 02:25:00 is a references tooltip, not Python. The 02:25:05 gutter still shows the active header and comment below it:

```text
  9 | pylist = fruit.toList()
 10 | for it in pylist:
 11 |     #print(it)
 12 |
 13 | fruit.toFile("fruit2.dat")
 14 |
 15 | fruit.fromFile("fruit2.dat")
 16 | for it in fruit:
 17 |     print(it)
```

The terminal is behind the editor; the visible suffix of a wrapped message includes `nt on line 10`. The instructor reads the missing-indented-block diagnosis and notices it was the *earlier* loop whose print was disabled. Do not treat the error as a failed file write: the driver did not parse.

**02:26:00 and 02:27:00, repaired driver (`test\fruit.py`).** The correction is the first character on line 10. The observed run at 02:26:00 prints the six values beneath this listing; `ls` lists `fruit.dat` and `fruit2.dat`, each at length 22. At 02:31:00 `cat fruit2.dat` shows only the first three names.

```text
  1 | from item import Item
  2 |
  3 | fruit = Item( )
  4 | fruit = fruit.append( "apple" )
  5 | fruit = fruit.append( "banana" )
  6 | fruit = fruit.append( "grape" )
  7 |
  8 |
  9 | pylist = fruit.toList()
 10 | #for it in pylist:
 11 |     #print(it)
 12 |
 13 | fruit.toFile("fruit2.dat")
 14 |
 15 | fruit.fromFile("fruit2.dat")
 16 | for it in fruit:
 17 |     print(it)
 18 |
```

```text
PS C:\com\joesta\Item> py .\test\fruit.py
apple
banana
grape
apple
banana
grape
```

**`item.py`, 02:27:00, lines 208–231, write mode.** This single frame continues the numbered region visible at 02:19; its own lines 203–207 overlap the generator ending. The two frames do not establish an entire saved file between times. The write loop emits each value before testing the last link. The read method strips and appends without clearing. At 224 a different method begins; the normal continuation below 231 is off-screen, not reconstructed.

```text
208 |
209 |     def toFile(self, filename):
210 |         with open(filename, "w") as file:
211 |             iter = self.first( )
212 |             if iter._object is not None:
213 |                 while True:
214 |                     file.write(iter._object + '\n')
215 |                     if iter._next == None:
216 |                         break
217 |                     iter = iter._next
218 |
219 |     def fromFile(self, filename):
220 |         with open(filename, "r") as file:
221 |             for item in file:
222 |                 self.append(item.strip())
223 |
224 |     def __getitem__(self, index):
225 |         iter = self.first( )
226 |         if iter._object != None:
227 |             count = 0
228 |             while count < index:
229 |                 if iter._next == None:
230 |                     iter = None
231 |                     raise Exception( "Index beyond end." )
```

#### Screen record: file growth, then edits to the writer's interface

**`item.py`, 02:33:35; selection at 02:35:00.** The traversal and reader still match lines 211–222 above. The changed literal at line 210 lets data survive the next run. It is distinct from the append call on line 222, which adds the read text to the Python object.

```text
209 |     def toFile(self, filename):
210 |         with open(filename, "a") as file:
```

**PowerShell, 02:32:00, 02:33:00, 02:36:00–02:38:00.** The 02:32 frame shows two complete driver command/output blocks: four groups, then five. At 02:33 the file is first executed as source, then displayed as data. The error is reproduced below; the `cat` output that follows it is four complete apple/banana/grape groups. The 02:36–02:37 frames revisit scrollback; these are not evidence of two additional runs. A later visible driver command at 02:38 is followed by six groups.

```text
PS C:\com\joesta\Item> py fruit2.dat
Traceback (most recent call last):
  File "C:\com\joesta\Item\fruit2.dat", line 1, in <module>
    apple
NameError: name 'apple' is not defined. Did you mean: 'tuple'?
PS C:\com\joesta\Item> cat fruit2.dat
```

**`test\fruit.py`, 02:39:00 versus 02:40:00.** At 02:39:00 the one-argument call at 13 is selected, 16–17 print the list, and 19 has no code. By 02:40:00 the driver has an extra top-level write *after* the loop. The two calls have no mode argument yet:

```text
 13 | fruit.toFile("fruit2.dat")
 14 |
 15 | fruit.fromFile("fruit2.dat")
 16 | for it in fruit:
 17 |     print(it)
 18 |
 19 | fruit.toFile("fruit2.dat")
```

**`item.py`, writer signature changes; each pair below is a separate screen state.** In the first three snapshots only the signature is being edited; line 210 still selects append independently. At 02:41 the missing-argument error is from attempting to call the required-parameter version. The visible error starts `TypeError: Item[remainder not visible]`; `'mode'` appears on the wrapped line. It is not a binary/text mismatch.

02:40:00 — trailing comma and space, before the parameter name:

```text
209 |     def toFile(self, filename, ):
210 |         with open(filename, "a") as file:
```

02:41:00 — required parameter:

```text
209 |     def toFile(self, filename, mode):
210 |         with open(filename, "a") as file:
```

02:41:10 — assignment sign typed, no default value yet:

```text
209 |     def toFile(self, filename, mode=):
210 |         with open(filename, "a") as file:
```

02:42:00 — default entered, old literal still in `open`; the completion menu covers the right part of 210:

```text
209 |     def toFile(self, filename, mode="w"):
210 |         with open(filename, "a") a[remainder not visible]
```

02:43:00 — completed parameterized writer, visible together with its body:

```text
209 |     def toFile(self, filename, mode="w"):
210 |         with open(filename, mode) as file:
211 |             iter = self.first( )
212 |             if iter._object is not None:
213 |                 while True:
214 |                     file.write(iter._object + '\n')
215 |                     if iter._next == None:
216 |                         break
217 |                     iter = iter._next
```

**`test\fruit.py`, 02:43:00–02:44:00, complete mixed-mode driver.** The call at 13 appends the initial values; 15 reads the enlarged file into memory. The last save does not specify a mode: it overwrites with the current in-memory result. The exposed left edge of the terminal at 02:43:00 shows two groups and then four groups in separate output blocks; their commands are hidden behind the editor.

```text
  1 | from item import Item
  2 |
  3 | fruit = Item( )
  4 | fruit = fruit.append( "apple" )
  5 | fruit = fruit.append( "banana" )
  6 | fruit = fruit.append( "grape" )
  7 |
  8 |
  9 | pylist = fruit.toList()
 10 | #for it in pylist:
 11 |     #print(it)
 12 |
 13 | fruit.toFile("fruit2.dat", "a")
 14 |
 15 | fruit.fromFile("fruit2.dat")
 16 | for it in fruit:
 17 |     print(it)
 18 |
 19 | fruit.toFile("fruit2.dat")
 20 |
 21 |
```

**`test\fruit.py`, 02:45:00 — change to the first call only:**

```text
 13 | fruit.toFile("fruit2.dat", "x")
```

**02:45:23 — next first-call edit:**

```text
 13 | fruit.toFile("fruit2.dat", "wb")
```

At 02:45:23 line 19 still has no mode argument and the writer still calls `file.write(iter._object + '\n')`. The terminal edge shows an existing-file failure from the preceding exclusive-create experiment, not a result for this new binary mode. The editor covers the rest of that error line; the full historical message is not supplied here.

### A.7 · Tracebacks, text modes and mixed types

#### Text-file writer before validation — `item.py` and `fruit.py` (02:46–02:52)

Numbers to the left of `|` are the editor gutter, **not Python to type**. These are observations of editor buffers unless identified as terminal evidence. Spaces show the visible indentation levels; tabs and spaces cannot be distinguished from pixels. A missing suffix is marked where it would have appeared. An incomplete expression followed by an edit cursor is an unfinished edit, not evidence of hidden text.

**`item.py`, assembled compatible views:** lines 197–225 from `02-46-00.png`, 226–230 from `02-51-00.png`. The writer's signature, loop and adjacent reader overlap in these views; timing between stills isn't a continuous record. The visible tail of a list loader appends values; the generator hands out one node value at a time. The writer instead opens the requested mode and writes text records. The reader appends rather than clearing the existing nodes.

```text
197 |         for item in list:
198 |             self.append(item)
199 |
200 |     def genny(self):
201 |         iter = self.first( )
202 |         if iter._object is not None:
203 |             while True:
204 |                 yield iter._object
205 |                 if iter._next == None:
206 |                     break
207 |                 iter = iter._next
208 |
209 |     def toFile(self, filename, mode="w"):
210 |         with open(filename, mode) as file:
211 |             iter = self.first( )
212 |             if iter._object is not None:
213 |                 while True:
214 |                     file.write(iter._object + '\n')
215 |                     if iter._next == None:
216 |                         break
217 |                     iter = iter._next
218 |
219 |     def fromFile(self, filename):
220 |         with open(filename, "r") as file:
221 |             for item in file:
222 |                 self.append(item.strip())
223 |
224 |     def __getitem__(self, index):
225 |         iter = self.first( )
226 |         if iter._object != None:
227 |             count = 0
228 |             while count < index:
229 |                 if iter._next == None:
230 |                     iter = None
```

Line 231 is vertically cut off at the bottom; a later guard revision places the analogous raise at line 237. The rest of the index-lookup method is not visible. Lines 200–207 stop after the last yield; lines 209–217 use that same stopping test *after* a write, so a failure on the first write prevents any advance.

**`fruit.py`, `02-46-00.png`.** Three successive appends assign the result back to `fruit`; lines 10–11 are comments, but line 9 is active. Line 13 asks for binary overwrite. Lines 15–19 are active source; an unhandled error at 13 prevents them from executing on that run.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit = fruit.append( "apple" )
 5 | fruit = fruit.append( "banana" )
 6 | fruit = fruit.append( "grape" )
 7 |
 8 |
 9 | pylist = fruit.toList()
10 | #for it in pylist:
11 |     #print(it)
12 |
13 | fruit.toFile("fruit2.dat", "wb")
14 |
15 | fruit.fromFile("fruit2.dat")
16 | for it in fruit:
17 |     print(it)
18 |
19 | fruit.toFile("fruit2.dat")
```

**Two runs, not two exceptions in a single call.** At `02-47-00.png`, `02-48-00.png` and `02-49-30.png`, PowerShell shows `PS C:\com\joesta\Item> py .\test\fruit.py` and separate tracebacks. Earlier scrollback records driver line 13 as `fruit.toFile("fruit2.dat", "x")`; `C:\com\joesta\Item\src\item.py`, line 210, is the `open` statement. The terminal ends that traceback with `FileExistsError: [Errno 17] File exists: 'fruit2.dat'`. The next driver line 13 is the `"wb"` call from the listing; method line 214 is the write, with `TypeError: a bytes-like object is required, not 'str'`. `02-49-00.png` shows the editor's definition popup for `toFile`: `item.py:209`, with a reference at `fruit.py:19`. That navigation hint identifies the definition, not the line where execution failed.

#### `item.py`: the proposed mode guard and its two distinct defects (02:53–03:00)

**Edit in progress, `02-53-00.png`.** The instructor has renamed the argument and begun a comparison. Autocomplete covers the right side of the following old opener; the incomplete condition is visible up to the cursor.

```text
209 |     def toFile(self, filename, hope="w"):
210 |         if hope ==
211 |         with ope[remainder not visible]
```

**Next edit, `02-54-00.png`.** There is not yet a body under `else:`. The append test lacks its colon and the following line is a comparison, not an assignment.

```text
209 |     def toFile(self, filename, hope="w"):
210 |         if hope == "w":
211 |             mode = 'w'
212 |         elif hope == "a"
213 |             mode == "a"
214 |         else:
215 |
216 |         with open(filename, mode) as file:
```

**Next edit, `02-54-38.png`.** The rejection body now exists, but the colon has not been restored yet. Beneath it is the writer, now six lines lower than the original; the traceback from the earlier binary-write failure still says line 214, not line 220.

```text
209 |     def toFile(self, filename, hope="w"):
210 |         if hope == "w":
211 |             mode = 'w'
212 |         elif hope == "a"
213 |             mode == "a"
214 |         else:
215 |             raise Exception("invalid mode")
216 |         with open(filename, mode) as file:
217 |             iter = self.first( )
218 |             if iter._object is not None:
219 |                 while True:
220 |                     file.write(iter._object + '\n')
221 |                     if iter._next == None:
222 |                         break
223 |                     iter = iter._next
224 |
225 |     def fromFile(self, filename):
226 |         with open(filename, "r") as file:
227 |             for item in file:
228 |                 self.append(item.strip())
229 |
230 |     def __getitem__(self, index):
```

**Colon repaired, comparison retained.** Lines 209–216 are independently clear at `02-56-00.png`. The lower lines are consistent with `03-00-00.png`; declaration prefixes on that horizontally scrolled frame are recovered for 209, 225 and 230 from `02-54-38.png`, not read through the hidden margin. Line 237 is vertically clipped; its upper portion supports the displayed error text, not a view of the rest of this method. This is an assembled segment, not a full file recovered from Git.

```text
209 |     def toFile(self, filename, hope="w"):
210 |         if hope == "w":
211 |             mode = 'w'
212 |         elif hope == "a":
213 |             mode == "a"
214 |         else:
215 |             raise Exception("invalid mode")
216 |         with open(filename, mode) as file:
217 |             iter = self.first( )
218 |             if iter._object is not None:
219 |                 while True:
220 |                     file.write(iter._object + '\n')
221 |                     if iter._next == None:
222 |                         break
223 |                     iter = iter._next
224 |
225 |     def fromFile(self, filename):
226 |         with open(filename, "r") as file:
227 |             for item in file:
228 |                 self.append(item.strip())
229 |
230 |     def __getitem__(self, index):
231 |         iter = self.first( )
232 |         if iter._object != None:
233 |             count = 0
234 |             while count < index:
235 |                 if iter._next == None:
236 |                     iter = None
237 |                     raise Exception( "Index beyond end[remainder not visible]
```

On `02-55-00.png`, one run fails while importing `Item` at driver line 1: `SyntaxError: expected ':'` in `item.py` line 212. The following run reaches driver line 13 and method line 215, and ends with `Exception: invalid mode`. This proves rejection of `"wb"`; neither run tests the `"a"` branch. The displayed `mode == "a"` at 213 is preserved rather than corrected here.

#### `fruit.py`: caller-side error policies (02:56–03:01)

**First edit, `02-56-00.png`** — an indented call in a new `try` block, before the handler was entered. Its last characters run behind the narrow pane edge. The later, wider `02-58-00.png` shows the entire corresponding binary call, but in a different handler state.

```text
13 | try:
14 |     fruit.toFile("fruit2.dat", "wb[remainder not visible]
```

**Report the failure, `02-57-00.png` and `02-57-01.png`.** The handler prints the message attached to `ex`. Below it, the later reader and loop still run. The terminal for this run shows `invalid mode`, `apple`, `banana`, `grape`, then a new prompt.

```text
13 | try:
14 |     fruit.toFile("fruit2.dat", "wb[remainder not visible]
15 | except Exception as ex:
16 |     print(ex)
```

**Suppress it, `02-58-00.png`.** There is no diagnostic in the handler. The visible terminal now includes a later run printing two trios without `invalid mode`.

```text
13 | try:
14 |     fruit.toFile("fruit2.dat", "wb")
15 | except Exception as ex:
16 |     pass
```

**Retry, `03-00-00.png`** (also visible at `02-59-00.png`). The driver's complete visible code, now with the chosen text fallback, is below. Lines 19–23 read back, display and write out the combined values. A terminal tail already visible in the `pass` state is not evidence of a new invocation at the moment the retry was edited.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit = fruit.append( "apple" )
 5 | fruit = fruit.append( "banana" )
 6 | fruit = fruit.append( "grape" )
 7 |
 8 |
 9 | pylist = fruit.toList()
10 | #for it in pylist:
11 |     #print(it)
12 |
13 | try:
14 |     fruit.toFile("fruit2.dat", "wb")
15 | except Exception as ex:
16 |     fruit.toFile("fruit2.dat", "w")
17 |
18 |
19 | fruit.fromFile("fruit2.dat")
20 | for it in fruit:
21 |     print(it)
22 |
23 | fruit.toFile("fruit2.dat")
```

#### `fruit.py`: operator trials, repair and numerical scratch line (03:02–03:13)

The instructor appends these experiments below line 23 of the retry driver; they aren't a replacement for the file code. An integer sum and string concatenation produce different results; asking for the types distinguishes unlike objects that print alike. The final mixed expression raises rather than guessing which result the caller wanted.

**`03-02-00.png`: second print being composed.** It hasn't reached the completed two-string addition in this still.

```text
25 | print(2 + 3)
26 | print('2')
```

**`03-03-00.png`: type checks being inserted; preserve the incomplete nesting.** The previous terminal tail `5` then `23` belongs to an earlier run. It cannot have come from this buffer as shown.

```text
25 | print(2 + 3)
26 | print(type(2)
27 | print('2' + '3')
28 | print(type())
```

**`03-03-35.png`: repaired and run.** The visible terminal retains the preceding syntax error (`SyntaxError: '(' w[remainder not visible]`); the *next* run prints the fruit sequence, then the four results below. No print outputs from the failing parse should be attributed to that invocation.

```text
25 | print(2 + 3)
26 | print(type(2))
27 | print('2' + '3')
28 | print(type('2'))
```

```text
5
<class 'int'>
23
<class 'str'>
```

**`03-05-00.png`: add mixed operands to the repaired version.** Lines 25–28 above are unchanged. The terminal displays a traceback pointing at the new line after those four results. The diagnostic's right edge is hidden by the editor; the complete wording in the teaching section is from a separate run.

```text
29 | print(2 + '3')
```

**Line 31 as scratch notation, four separate editor states, not consecutive lines.** Below line 29 a blank line separates the examples; the instructor compares characters acceptable in numbers with characters acceptable in text. No successful interpretation or execution of these editor edits is demonstrated.

| Target frame | Observed gutter and line content |
|---|---|
| `03-09-00.png` | `31 │ 133.45` |
| `03-10-00.png` | `31 │ -133e45` |
| `03-11-00.png` | `31 │ -133t45` |
| `03-12-00.png`, `03-12-27.png`, `03-13-00.png` | `31 │ -133.45.87` |

#### Return to the list helpers — `item.py`, 03:13:00

The editor scrolls up. The start of the preceding list-building method remains above the pane. Its visible end uses a next link and returns `list`; the following helper appends each element. The definition prefix on 196 is horizontally hidden, so the line below does not silently supply it. The `genny` body at 200–207 is readable and matches the earlier first listing (including the full declaration visible at `02-46-00.png`); that method is not a newly demonstrated run here.

```text
192 |                     break
193 |                 iter = iter._next
194 |         return list
195 |
196 | [left edge not visible]fromList(self, list):
197 |         for item in list:
198 |             self.append(item)
199 |
```

The closing spoken checkout question does not supply a new screen listing or a visible Git result in this frame.

### A.8 · Generator state and independent traversals

#### `fruit.py` before the generator checkout — 03:14:00

Observed editor listing. The numbers before `|` are the editor gutter, **not code to type**. Lines 9–11 create a native list but leave its printing loop commented; 13–23 try two write modes, load, iterate and call the writer again. Lines 25–29 contrast numeric addition and string concatenation. The terminal contains a previous `py .\test\fruit.py` run ending on line 29 (`TypeError: unsupported operand type(s) for +: 'int' and 'str'`); the buffer is marked modified. The full scrollback is not proof of an execution of this exact buffer.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit = fruit.append( "apple" )
 5 | fruit = fruit.append( "banana" )
 6 | fruit = fruit.append( "grape" )
 7 |
 8 |
 9 | pylist = fruit.toList()
10 | #for it in pylist:
11 |     #print(it)
12 |
13 | try:
14 |     fruit.toFile("fruit2.dat", "wb")
15 | except Exception as ex:
16 |     fruit.toFile("fruit2.dat", "w")
17 |
18 |
19 | fruit.fromFile("fruit2.dat")
20 | for it in fruit:
21 |     print(it)
22 |
23 | fruit.toFile("fruit2.dat")
24 |
25 | print(2 + 3)
26 | print(type(2))
27 | print('2' + '3')
28 | print(type('2'))
29 | print(2 + '3')
```

The visible terminal then reports `git checkout master`, `Already on 'master'`, and `M` beside `src/item.py` and `test/fruit.py`. Another `py .\test\fruit.py` sits at the prompt with the insertion cursor; it is not an observed second execution there. In the 03:15:00 terminal sliver the word `Aborting` precedes a later `Note: switching to [remainder not visible]`. By 03:20:00 the entire later confirmation reads `HEAD is now at 843e915 generator`. The intervening remedy and exact checkout command are not recoverable from these stills.

#### `fruit.py` after checkout — compare the three driver states

**A. First generator, no unguarded fourth request (03:15:00 and 03:21:00).** Lines 3–9 populate and traverse the list by the `Item` protocol. Lines 11–17 describe a new generator and consume only three results. Lines 19–28 construct a second generator and *do* request a fourth item, this time under a narrow exception handler. The 03:20:00 terminal shows `py .\test\fruit.py`, the first group of three strings, generator descriptions, the next groups of strings and `List exhausted.`; it is not the later failing run.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit = fruit.append( "apple" )
 5 | fruit = fruit.append( "banana" )
 6 | fruit = fruit.append( "grape" )
 7 |
 8 | for item in fruit:
 9 |     print( item )
10 |
11 | print( )
12 | jenny = fruit.genny()
13 | print( jenny )
14 | print( jenny.__next__( ) )
15 | print( jenny.__next__( ) )
16 | print( jenny.__next__( ) )
17 | #print( jenny.__next__( ) )
18 |
19 | print( )
20 | jenny = fruit.genny()
21 | print( jenny )
22 | try:
23 |     print( jenny.__next__( ) )
24 |     print( jenny.__next__( ) )
25 |     print( jenny.__next__( ) )
26 |     print( jenny.__next__( ) )
27 | except StopIteration:
28 |     print( "List exhausted." )
29 |
```

**B. Unguarded fourth call enabled (03:24:24; still enabled at 03:25:00).** This is the changed region, not an extra call to append to the preceding listing. The terminal at 03:24:00 still shows earlier output; at 03:24:24 the screen pairs the active line with the separate run that fails there. The interpreter reports `File "C:\com\joesta\Item\test\fruit.py", line 17, in <module>` followed by `StopIteration`; the later handler has not been entered.

```text
12 | jenny = fruit.genny()
13 | print( jenny )
14 | print( jenny.__next__( ) )
15 | print( jenny.__next__( ) )
16 | print( jenny.__next__( ) )
17 | print( jenny.__next__( ) )
```

**C. Line 17 disabled again; full later driver region (assembled visual reconstruction).** 03:27:00 supplies lines 8–34, including the restored `#` on line 17. 03:29:00 and 03:29:47 supply lines 22–43, overlapping the second `try` and generator setup; the reproduction below starts at the restored line 17. Lines 1–7 are not visible in the lower view. This is a reading across compatible regions, not one screenshot of a saved file or a claim that no edit occurred between stills.

```text
17 | #print( jenny.__next__( ) )
18 |
19 | print( )
20 | jenny = fruit.genny()
21 | print( jenny )
22 | try:
23 |     print( jenny.__next__( ) )
24 |     print( jenny.__next__( ) )
25 |     print( jenny.__next__( ) )
26 |     print( jenny.__next__( ) )
27 | except StopIteration:
28 |     print( "List exhausted." )
29 |
30 | print( )
31 | jenny = fruit.genny()
32 | print( jenny )
33 | jenifer = fruit.genny()
34 | print( jenifer )
35 | try:
36 |     print( jenny.__next__( ) )
37 |     print( jenifer.__next__( ) )
38 |     print( jenny.__next__( ) )
39 | #    print( jenifer.__next__( ) )
40 |     print( jenny.__next__( ) )
41 |     print( jenifer.__next__( ) )
42 | except StopIteration:
43 |     print( "List exhausted." )
```

Lines 31 and 33 call the method separately, so line 37 really does print a second `apple`. Only line 39 is skipped; line 41 is `jenifer`’s second actual request. **Observed successful run, 03:26:00 PowerShell** (old line-17 traceback above this command omitted):

```text
PS C:\com\joesta\Item> py .\test\fruit.py
apple
banana
grape

<generator object Item.genny at 0x00000270A8900E10>
apple
banana
grape

<generator object Item.genny at 0x00000270A8901A40>
apple
banana
grape
List exhausted.

<generator object Item.genny at 0x00000270A8900E10>
<generator object Item.genny at 0x00000270A8901A40>
apple
apple
banana
grape
banana
PS C:\com\joesta\Item>
```

#### `item.py` — the ordinary iterator and its neighboring helpers

**Assembled visual reconstruction, lines 114–168:** 03:23:00 supplies 114–141 and 03:25:00 supplies 141–168. The overlapping `while True:` on line 141 joins the two visible parts of `count`; the stills do not establish unseen file history. This is not the whole class. Line 117 calls a supplied object's `withItem` method; lines 122–133 are the ordinary iterator being compared with the generator. The head keeps a reference to the next node, not an integer index.

```text
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
135 |     def count(self):
136 |         iter = self.first( )
137 |         if iter._object == None:
138 |             count = 0
139 |         else:
140 |             count = 1
141 |         while True:
142 |             if iter._next == None:
143 |                 break
144 |             iter = iter._next
145 |             count += 1
146 |         return count
147 |
148 |     def at(self, n):
149 |         iter = self.first( )
150 |         if iter._object != None:
151 |             count = 0
152 |             while count < n:
153 |                 if iter._next == None:
154 |                     iter = None
155 |                     raise Exception( "Index beyond end." )
156 |                 iter = iter._next
157 |                 count += 1
158 |         return iter
159 |
160 |     def has(self, obj):
161 |         if self._object != None:
162 |             iter = self.first( )
163 |             while iter != None:
164 |                 if iter._object == obj:
165 |                     break
166 |                 iter = iter._next
167 |         else:
168 |             iter = None
```

Lines 135–146 count the first payload if present, then count each forward link followed. Lines 148–158 walk by index and raise a general `Exception` when asked to step beyond the last node of a nonempty chain. Lines 160–168 show an object-value search, but the following line is below the readable pane. **Lines 169–176 unavailable in these editor views**; no missing return, method header or condition is supplied from inference. The terminal does not show separate calls to those helpers in this segment.

#### `item.py` — cleanup tail, list conversion and the generator

**Assembled visual reconstruction, lines 177–207:** 03:16:00 shows 177–204. Line 205, just below that viewport, is fully legible separately at 03:15:00, 03:17:00 and 03:27:00; the latter frames also show the blank lines at the end. Lines 177–181 are only the tail of an earlier method. The known methods beginning at 183 build a Python list, append from a Python list, or yield one payload at a time.

```text
177 |             iter._prev._next = None
178 |             iter._prev._object = None
179 |         print( f"clearing: {iter._object}" )
180 |         iter._prev = None
181 |         iter._object = None
182 |
183 |     def toList(self):
184 |         list = []
185 |         iter = self.first( )
186 |         if iter._object is not None:
187 |             while True:
188 |                 list.append(iter._object)
189 |                 if iter._next == None:
190 |                     break
191 |                 iter = iter._next
192 |         return list
193 |
194 |     def fromList(self, list):
195 |         for item in list:
196 |             self.append(item)
197 |
198 |     def genny(self):
199 |         iter = self.first( )
200 |         if iter._object is not None:
201 |             while True:
202 |                 yield iter._object
203 |                 if iter._next == None:
204 |                     break
205 |                 iter = iter._next
206 |
207 |
```

Line 188 appends *all* payloads before line 192 returns a list. In contrast, line 202 suspends traversal immediately; lines 203–205 run on the next request to that same generator. After grape, the `break` ends the method. There is no explicit `raise StopIteration` in `genny`; the explicit raise at line 130 belongs to the other iterator.

#### Closing display — historical course-progress note, not Python

03:31:58 shows Discord rather than the generator editor. An older October 26 post links a screenshot to that morning's discussion. The embedded scratchpad visibly reads as follows (numbers at left are its editor gutter); it is **not** newly executed code, a generator trace, or evidence that the grading schedule was restated in this closing segment.

```text
 1 | Grand Jury
 2 | Withdrawal
 3 | Progress
 4 |
 5 | 16 3030|1
 6 | 11 2080|1
 7 | 10 2070|1  A
 8 |  9 2060|1  B
 9 |  8 2058|1  C
10 |  7 2056|1  D
11 |  6 2054|2  F
12 |  5 2050|5  F
13 |  4 2022|2  F
14 |  3 2020|1  F
15 |  2 2005|2  F
16 |  1 2000|2  F
17 |        ---
18 |        18
19 |
20 |  17 - 2 registered
21 |
22 | 3 code challenges per week
23 | 6 weeks left --> 18 more challenges
```

## Appendix B · Micro-Learning Insights

*Removable recall sheet. Use each cue to predict a result before looking up the worked example.*

- **Start by asking what the caller needs.** For banana, `True` answers existence, the string supplies a value, and the node returned by `has` preserves a place from which to navigate. Those are three different contracts. (53:00–1:35:00)
- **Read one operation from the outside in, then evaluate it from the inside out.** In `print(one.object())`, first identify what `one` refers to. The method retrieves its stored object; only then does `print` display it. A miss cannot be used as if it were a node. (1:01:00–1:11:00)
- **Count from the right end only if the class promises it.** In a native Python list, `-1` selects the last element, not “move backward once from the last.” A doubly linked chain makes backward travel possible; it does not implement negative indexing automatically. (03:26–05:54)
- **Let syntax tell you which hook to inspect.** `fruit[1]` asks for a read; `fruit[1] = replacement` asks for a write. The same brackets send different information according to their role in the statement. (43:00–50:00)
- **Give a Git experiment a recoverable starting point.** A commit can preserve a checkpoint. A dirty working file and an unsaved editor buffer are not that checkpoint. A restore may throw away edits; don’t use it just to find out whether edits exist. (24:00–43:00)
- **Keep the node, its links, and its contents separate on paper.** Draw a box for a node, arrows to its neighbours, and a separate arrow to its stored object. Crossing out one arrow does not erase all the other references. (1:21:00–1:47:00)
- **Check the binding, not just the spelling.** `list()` normally constructs a list. After `list = fruit.toList()`, the parentheses ask a particular list instance to act like a function instead. Renaming the result avoids shadowing. (1:48:00–2:03:00)
- **Change one thing to test a file round trip.** A fresh filename helps distinguish a file this run created from a file that was already there. Inspect the file and the receiving chain independently: importing into a nonempty receiver can duplicate values even when the file does not. (2:20:00–2:40:00)
- **Separate “may open” from “may write this value.”** `'wb'` is a valid file mode. A successful open still does not make a string a bytes-like object. Look at the failing operation before changing the mode again. (2:44:00–2:52:00)
- **Trace the operation, not the appearance of the data.** Digits inside quotes are still text. Decide whether you want concatenation or arithmetic before converting anything; a name such as `'banana'` cannot be converted to an integer just because another operand is numeric. (2:59:00–3:13:00)
- **Write down whose `next` it is.** The last demonstration spells the variables `jenny` and `jenifer`. Commenting out one request for `jenifer` leaves *her* walk one fruit behind; it does not alter the list or rewind `jenny`. (3:26:00–3:31:58)
