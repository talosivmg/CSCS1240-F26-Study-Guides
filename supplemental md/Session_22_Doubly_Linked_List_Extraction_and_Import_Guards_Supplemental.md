# Doubly linked lists in Python

**CSCS1240F26 · Session 22 · Compressed technical companion v1.2**

## What survives an extraction?

Keep a reference to the node you want to remove. Call `remaining = selected.extract()`. After the call, `selected` still holds its original fruit, with both links cleared. `remaining` gives you a way into the surviving chain. This return convention is the detail that makes the fruit driver work.

| Receiver before the call | Raw previous | Raw next | What the caller gets back |
|---|---|---|---|
| Empty node or populated singleton | `None` | `None` | A fresh empty `Item()` in either case |
| Head | `None` | Successor | Successor, now the head |
| Tail | Predecessor | `None` | Predecessor, now the tail |
| Interior | Predecessor | Successor | Predecessor, which can reach both surviving segments |

A node with two neighbors proves there are at least three nodes. One neighbor proves there are at least two. With neither neighbor, inspect `_object` to distinguish an empty representative from a populated singleton. This class reserves `None` as its empty payload. A conventional design might keep a separate list owner and node type. Here any connected `Item` can serve as the list handle. A singly linked node offers one direction of travel. This doubly linked node records both.

**Class file for practice.** Copy the six class blocks in reading order into `src/item.py` with their displayed indentation. The first starts the class. Subsequent blocks continue it. Together they reproduce the executable statements of the source Markdown's assembled `a5c546d` class (lines 1–111). All instructional comments are added here. Use the separate driver and launch commands later in this guide.

```python
class Item:                        # Define the node type; each Item can serve as a list
                                   # handle.
    _object = None                 # Default payload for a node with no data.
    _next = None                   # Default forward link; no successor yet.
    _prev = None                   # Default backward link; no predecessor yet.

    def __init__(self, obj = None):  # A call such as Item("apple") supplies obj =
                                     # "apple".
                                     # Omitting obj supplies None; Python passes the new
                                     # node as self.
        self._object = obj         # Store the payload on this instance; leave both link
                                   # defaults available.

    def object(self):              # Expose the stored value to a caller such as
                                   # display().
        return self._object        # Return "apple" for the apple node; this method
                                   # prints nothing.

    def next(self):                # Take at most one forward step from this receiver.
        if self._next != None:     # Test the raw link; peach has no successor in the
                                   # fruit chain.
            return self._next      # For apple, hand back the banana node.
        return self                # At peach, hand back peach itself so the caller can
                                   # detect the tail.

    def prev(self):                # Take at most one backward step.
        if self._prev != None:     # A populated predecessor permits a move.
            return self._prev      # For apple after prepending acai, return the acai
                                   # node.
        return self                # At acai, return acai itself.
```

**Key Items to Scan**

- `Item()` constructs an object even when its payload is `None`. `__new__` creates the instance and `__init__` initializes it.
- `node.next()` and `node.prev()` return the receiver at an endpoint. Use raw links to ask whether a neighbor exists.
- `del selected` would remove a name. It would perform none of the reciprocal-link repairs required by extraction.

## Find an endpoint and add fruit

`append` and `prepend` accept a receiver anywhere in the chain. They fill an empty representative in place. Once it holds data, insertion at an end requires reaching that end first. The driver appends from apple and later prepends from peach.

```python
    def append(self, obj):         # Add obj at the far end, even when self is near the
                                   # head.
        if self._object == None:   # Is this node the empty representative?
            self._object = obj     # Fill its existing payload slot in place.
            newItem = self         # Keep the original node as the insertion result.
        else:                      # A populated receiver needs another node.
            last = self.last()     # Walk from self to the tail; before peach is added,
                                   # that is banana.

            newItem = Item(obj)    # Build a new node to hold obj, for example "peach".
            newItem._prev = last   # Point peach backward to banana.
            last._next = newItem   # Point banana forward to peach, completing the pair.
        return newItem             # Return the inserted node on either path.

    def prepend(self, obj):        # Add obj at the front, even when called on peach.
        if self._object == None:   # An empty representative can be filled directly.
            self._object = obj     # Write obj into that same node.
            newItem = self         # Keep its identity as the result.
        else:                      # A populated chain needs a new head.
            first = self.first()   # Walk backward from peach to the current head, apple.
            newItem = Item(obj)    # Create the acai node with no neighbors of its own
                                   # yet.
            newItem._next = first  # Set acai's forward link to apple.
            first._prev = newItem  # Set apple's backward link to acai; acai's backward
                                   # link stays absent.
        return newItem             # Hand acai to the caller; ignoring it still leaves
                                   # the links changed.
```

```python
    def last(self):                # Find the tail from any connected node.
        iter = self                # Copy the receiver reference into a local cursor; no
                                   # node is copied.
        while True:                # Repeat until the raw forward link is absent.
            if iter._next == None:  # At peach, there is no further forward step.
                break              # Leave the loop with the cursor still on peach.
            iter = iter._next      # Otherwise rebind the cursor to its successor, e.g.
                                   # apple to banana.
        return iter                # Return the endpoint node.

    def first(self):               # Find the head by following the opposite direction.
        iter = self                # Start this cursor at the receiver, which may be
                                   # peach.
        while True:                # Repeat the backward walk until an endpoint check
                                   # succeeds.
            if iter._prev == None:  # At acai, the raw predecessor is absent.
                break              # Leave the cursor on acai for the return.
            iter = iter._prev      # Follow one backward link, e.g. peach to mango.
        return iter                # Return the first node; the caller's original handle
                                   # stays put.
```

**Key Items to Scan**

- `return newItem` gives the caller the inserted node. An unassigned call still changes the list.
- `iter = iter._next` moves only the local cursor. A caller that wants its own handle moved must assign the returned reference.
- `while True` with `break` leaves the endpoint available to the following `return`.

## Insert beside a known receiver

Compare the two roles of `banana` when mango is inserted before peach. Its reference must be saved on mango, and its forward link must be redirected to mango. Overwriting peach's backward link too early would lose the route to banana used by the historical code.

```python
    def insertAfter(self, obj):    # Insert immediately after self; obj is the new
                                   # payload.
        if self._object == None:   # Test whether the receiver has any data yet.
            self._object = obj     # Fill the empty receiver.
            newItem = self         # Retain that receiver for the return.
        else:                      # A populated receiver needs a separate node.
            newItem = Item(obj)    # Create it before changing any existing link.
            if self._next != None:  # Is there an old successor to preserve?
                newItem._prev = self  # Give the new node a backward link to the
                                      # receiver.
                newItem._next = self._next  # Save the old successor in the new node's
                                            # forward field.
                self._next._prev = newItem  # Follow self's old forward link; redirect
                                            # that neighbor back to newItem.
                self._next = newItem  # Only now redirect self forward to newItem.
            else:                  # At the tail, the new node has no old successor to
                                   # join.
                newItem._prev = self  # Link the new tail back to the receiver.
                self._next = newItem  # Link the receiver forward to the new tail.
        return newItem             # Return the filled or inserted node.
```

```python
    def insertBefore(self, obj):   # Insert beside the receiver, e.g. immediately before
                                   # peach.
        if self._object == None:   # Check the empty-payload convention first.
            self._object = obj     # Fill the receiver without allocating a second node.
            newItem = self         # Keep the filled receiver as the result.
        else:                      # A populated receiver gets a new neighbor.
            newItem = Item(obj)    # Create mango while peach still knows its old
                                   # predecessor.
            if self._prev != None:  # Peach has banana before it, so enter this branch.
                newItem._next = self  # Give mango a forward link to peach.
                newItem._prev = self._prev  # Give mango a backward link to banana.
                self._prev._next = newItem  # Follow peach back to banana; change
                                            # banana's forward field to mango.
                self._prev = newItem  # Now change peach's backward field to mango.
            else:                  # At a head there is no former predecessor to repair.
                newItem._next = self  # Point the new head forward to the receiver.
                self._prev = newItem  # Point the receiver backward to the new head.
        return newItem             # Return the inserted node; peach remains the driver's
                                   # handle.
```

| Interior insertion before peach | Banana's forward field | Mango's previous / next | Peach's previous field |
|---|---|---|---|
| Mango allocated | peach | absent / absent | banana |
| `newItem._next = self` | peach | absent / peach | banana |
| `newItem._prev = self._prev` | peach | banana / peach | banana |
| `self._prev._next = newItem` | mango | banana / peach | banana |
| `self._prev = newItem` | mango | banana / peach | mango |

**Key Items to Scan**

- `insertBefore` and `insertAfter` work relative to the receiver. `prepend` and `append` identify an endpoint first.
- A new head or tail has only one neighbor. Inserting between two nodes requires repairing both old neighbors.
- These routines return a node even when they fill the existing empty representative.

## Read the extraction method in execution order

This is the last class block. Use the indentation to find the next statement that actually runs. In source line 96, `rest = Item( )` skips all three neighbor-repair branches and resumes at line 109. Clearing the links is redundant on that path and essential on others. The payload is never assigned here.

```python
    def extract(self):             # Detach the node supplied as self; the caller may
                                   # keep using it.
        rest = None                # Reserve a local name for the surviving chain's entry
                                   # point.
        if self._prev == None and self._next == None:  # Test the predecessor first. If
                                                       # it exists, Python skips the next
                                                       # test.
                                                       # Only two absent links enter the
                                                       # first suite.
            rest = Item( )         # Allocate a distinct empty remainder; next execute
                                   # line 109.
        else:                      # At least one neighbor exists.
            if self._prev == None:  # With no predecessor, the surviving successor must
                                    # exist.
                self._next._prev = None  # Follow self forward; clear the successor's
                                         # backward field.
                rest = self._next  # Save that successor before clearing the receiver's
                                   # forward link.
            else:                  # A predecessor exists, so self is a tail or an
                                   # interior node.
                if self._next == None:  # No successor selects the tail branch.
                    self._prev._next = None  # Clear the predecessor's forward field; it
                                             # becomes the tail.
                    rest = self._prev  # Save that predecessor as the remainder handle.
                else:              # Both neighbors exist; take the interior branch.
                    self._prev._next = self._next  # Read node 6 on the right, then write
                                                   # node 4's forward field.
                                                   # Node 6 still points backward to 5 at
                                                   # this instant.
                    self._next._prev = self._prev  # Read node 4 on the right, then write
                                                   # node 6's backward field.
                    rest = self._prev  # Save node 4 as the entry to the surviving chain.
        self._prev = None          # Clear the receiver's backward link on every path.
        self._next = None          # Clear its forward link too; its payload survives.
        return rest                # Return the saved Item to the caller; display is a
                                   # separate action.
```

| Initial shape | L95 · both absent | L98 · previous absent | L102 · next absent | Branch result before cleanup |
|---|---|---|---|---|
| Isolated | True | skipped | skipped | L96 creates empty object |
| Head | False | True | skipped | L100 saves successor |
| Tail | False; right operand skipped | False | True | L104 saves predecessor |
| Interior | False; right operand skipped | False | False | L108 saves predecessor |

**Key Items to Scan**

- `node.extract()` binds `self` to the selected node. To extract the first node from a second-node handle, call `second.first().extract()`. Keep the first-node reference separately if you need it.
- `rest = None` initializes a local variable. Every completed branch replaces it with an `Item`.
- `self._next._prev` is a field on the successor. Its old value can refer to `self` while its owner is a different object.

## Trace the exact writes

**Classroom thought experiment.** Remove node 5 from ten linked nodes. Read the right side of each assignment first. On line 106 it gives you 6. Then follow `self._prev` to 4 and write 4's `_next`. Node 5's original links still exist for line 107. A single forward traversal can already look right after line 106 while a backward traversal still finds 5.

| Executed operation | 4.next | 6.prev | 5.prev / 5.next | Local rest or result |
|---|---|---|---|---|
| Start, after L94 | 5 | 5 | 4 / 6 | `None` |
| L106 · write predecessor's forward field | 6 | 5 | 4 / 6 | `None` |
| L107 · write successor's backward field | 6 | 4 | 4 / 6 | `None` |
| L108 · save predecessor | 6 | 4 | 4 / 6 | node 4 |
| L109 · clear receiver's previous | 6 | 4 | absent / 6 | node 4 |
| L110 · clear receiver's next | 6 | 4 | absent / absent | node 4 |
| L111 · return | 6 | 4 | absent / absent | caller receives 4 |

The surviving order is `1, 2, 3, 4, 6, 7, 8, 9, 10`. From the returned 4 you can go backward to 1 or forward to 10. There is no search for node 5 inside `extract()`. The caller located it before the method began.

**Endpoint traces.** Each column is a fresh two-node teaching fixture, `A ↔ B`. A dash denotes an absent link. Follow the columns separately. The single survivor keeps its payload.

| Step | Remove head A | Remove tail B |
|---|---|---|
| Before | A.next = B, B.prev = A | A.next = B, B.prev = A |
| Repair neighbor | L99 B.prev becomes absent; A.next still B | L103 A.next becomes absent; B.prev still A |
| Save handle | L100 rest = B | L104 rest = A |
| Clear self._prev | L109 A.prev stays absent | L109 B.prev becomes absent |
| Clear self._next | L110 A.next becomes absent | L110 B.next stays absent |
| Return | L111 yields B; A is isolated | L111 yields A; B is isolated |

**Isolated receiver.** L94 initializes `rest`. L95 reads two absent links. L96 creates a second, empty node. L109 and L110 each write `None` to an already absent field. L111 returns the second node. This sequence is identical for an empty receiver and for the only apple in a chain.

**Key Items to Scan**

- Hand-trace three identities first: receiver, predecessor, successor. Say whose field changes on every write.
- Repair the neighbors, save a surviving handle, then clear the receiver. Rebinding the local name `self` cannot repair a field owned by another node.
- The surviving handle is allowed to be a tail or interior node. Call `first()` if the next operation requires the head.

## Build and display the classroom fruit chain

**Driver file for practice.** Put this block and the two blocks on the next topic into `test/fruit.py`, in order. The first two reproduce the source's recovered driver through line 30. The third is a labeled practice continuation. The class file must be importable as `item`. In the historical driver `list` and `iter` are ordinary local names that shadow Python built-ins. No iterator protocol is used.

```python
from item import Item            # Locate module item, execute it on initial load, then
                                 # bind its Item here.

def display(list):               # Accept a handle at any position; list is a parameter
                                 # in this function.
    iter = list.first()          # Find the head; iter now refers to acai on the first
                                 # call.
    print("-----")               # Print the separator before the payloads.
    while True:                  # Keep visiting nodes until the tail's self-return
                                 # signals completion.
        print(iter.object())     # Read the current node's payload first; print that
                                 # value on its own line.
        if iter.next() == iter:  # Call next() on the cursor. At peach it returns peach
                                 # itself.
                                 # The historical == comparison works by identity for
                                 # this Item class.
            break                # Exit here, after printing peach once.
        iter = iter.next()       # Otherwise move to the returned neighbor; first move is
                                 # acai to apple.

fruit = Item("apple")            # Create apple; fruit holds this node.
fruit.append("banana")           # Append banana; discard the returned banana handle.
fruit.append("peach")            # Append peach; fruit still refers to apple.
fruit = fruit.last()             # Find peach, then rebind fruit to it; this assignment
                                 # changes no links.
fruit.insertBefore("mango")      # Insert mango between banana and peach; discard the
                                 # returned mango.
fruit.prepend("acai")            # Prepend acai from peach; discard the returned acai.
print("\nFruit:")                # Print a blank line followed by Fruit:.
display(fruit)                   # Print the chain starting at acai; fruit still
                                 # identifies peach.
```

| Executed call or assignment | Driver's fruit handle afterward | Chain from first to last |
|---|---|---|
| `Item("apple")` | apple | apple |
| `append("banana")` | apple | apple, banana |
| `append("peach")` | apple | apple, banana, peach |
| `fruit = fruit.last()` | peach | apple, banana, peach |
| `insertBefore("mango")` | peach | apple, banana, mango, peach |
| `prepend("acai")` | peach | acai, apple, banana, mango, peach |

**First display, iteration by iteration.** `display` prints the separator once. Each loop prints its current payload before testing the endpoint. On a successful move the historical code calls `next()` again to advance.

| Current payload printed | Result of current.next() | Comparison with current | Next action |
|---|---|---|---|
| acai | apple | False | Move to apple |
| apple | banana | False | Move to banana |
| banana | mango | False | Move to mango |
| mango | peach | False | Move to peach |
| peach | peach | True | Break; display returns `None` implicitly |

**Key Items to Scan**

- `print(iter.object())` calls the accessor first and sends its payload to standard output.
- Testing `iter.next() == None` here would never recognize the tail. Testing before printing would omit the final fruit.
- `display(fruit)` starts at `first()`, so a tail handle produces the entire chain.

## Keep the detached node and the remainder

The class driver uses two names with different lifetimes. First select the target into `iter`. Then evaluate `iter.extract()` and rebind `fruit` to its result. The call detaches the target before that rebinding. Merely dropping the return value would still perform the mutation.

```python
iter = fruit.first()    # Save acai as the selected node before changing the chain.
fruit = iter.extract()  # Detach acai, receive apple, then rebind fruit to apple.
print("\nIter:")        # Print a blank line and the detached-item label.
display(iter)           # Show acai by itself; its next() now returns itself.
print("\nFruit:")       # Label the next report as the remaining fruit.
display(fruit)          # Start at apple; print apple, banana, mango, peach.

iter = fruit.last()     # From the survivors, select the tail peach.
fruit = iter.extract()  # Detach peach; fruit now holds mango, the new tail.
print("\nIter:")        # Print the heading for the detached peach.
```

**Practice continuation for the same file.** Exact driver text beyond line 30 is unavailable. These lines complete the display and select the subsequent nodes to match the reported groups. The last survivor display is a tested extension.

```python
display(iter)                  # Print the detached peach under the existing Iter: label.
print("\nFruit:")              # Label the survivors after removing peach.
display(fruit)                 # Start at apple even though fruit refers to mango.
iter = fruit.first().next()    # first() gives apple; one next() gives banana.
fruit = iter.extract()         # Detach banana; receive its predecessor apple.
print("\nIter:")               # Start the banana report.
display(iter)                  # Print banana alone.
print("\nFruit:")              # Label the two remaining nodes.
display(fruit)                 # Print apple, then mango.
iter = fruit.first()           # Select apple from the two-node chain.
fruit = iter.extract()         # Detach apple; receive the last survivor, mango.
print("\nIter:")               # Start the apple report.
display(iter)                  # Print the isolated apple.
print("\nFruit:")              # Practice-only final survivor heading.
display(fruit)                 # Print mango; its public next() returns itself.
```

| Target | Returned handle | Detached Iter: display | Fruit: display from first() |
|---|---|---|---|
| acai · head | apple | acai | apple, banana, mango, peach |
| peach · tail | mango | peach | apple, banana, mango |
| banana · interior | apple | banana | apple, mango |
| apple · head | mango | apple | mango (practice prediction, tested) |

The master reports the five-fruit opening and the detached/survivor groups through detached apple. Each heading begins after a blank line, followed by `-----` and one fruit per line. The original report's ending is outside the supplied evidence.

**Key Items to Scan**

- `iter` still points to the same object after extraction. Its payload survives even after the old neighbor references have been cleared.
- `fruit.first().next()` calls `first` on the survivor handle and `next` on the apple it returns.
- `display` prints values and falls through to `None`. `extract` returns an object and produces no output.

## Test the fifth of ten and the empty boundary

**Supplemental program.** Save this block as `test/check_links.py`. Run it with the same class and search path as the fruit driver. The numbers label nodes in the instructor's thought experiment. The checks distinguish a correct return, a correct forward link, a correct reverse link, and isolation of the receiver. A failed `assert` stops an ordinary run at the first false condition.

```python
from item import Item          # Load the class for a fresh test chain.

head = Item(1)                 # Keep a handle on the starting node.
tail = head                    # The tail is initially that same node.
for value in range(2, 11):     # Visit 2 through 10; 11 is excluded.
    tail = tail.insertAfter(value)  # Link after the old tail; retain the new tail.
selected = head                # Begin the search for node 5 at node 1.
for step in range(4):          # Four forward moves reach position 5.
    selected = selected.next() # Rebind to nodes 2, 3, 4, then 5.
left = selected.prev()         # Save node 4 before mutation.
right = selected.next()        # Save node 6 before mutation.
rest = selected.extract()      # Remove 5; return an entry on the surviving chain.
assert rest is left            # Identity check: the returned node is exactly 4.
assert left.next() is right    # Check the 4-to-6 repair.
assert right.prev() is left    # Check the independent 6-to-4 repair.
assert selected._prev is None  # Verify the actual raw backward link is absent.
assert selected._next is None  # Verify the actual raw forward link is absent.
print(rest.object(), selected.object(), right.object())  # Print 4 5 6.

for payload in (None, "apple"):  # Exercise empty and populated singleton inputs.
    selected = Item(payload)     # New isolated receiver for each case.
    rest = selected.extract()    # Both link tests succeed; create empty remainder.
    assert rest is not selected  # The returned Item is a distinct instance.
    assert rest.object() is None # That separate object has an empty payload.
    assert rest._next is None    # The remainder has no forward neighbor.
    assert rest._prev is None    # It has no backward neighbor either.
    print(selected.object())     # The original payload prints: None, then apple.
```

**Expected output, independently reproduced**

```text
4 5 6
None
apple
```

**Key Items to Scan**

- `is` checks whether two names identify the same node. Matching payloads would be weaker evidence.
- A successful `assert` is silent. One-sided repair can satisfy the forward assertion and fail the reverse one.
- A fresh fixture for each boundary keeps a previous extraction from changing the starting conditions.

## Debug a believable but broken chain

**Supplemental fault demonstration.** Save as `test/broken_links.py`. This script deliberately performs only the first neighbor repair on a three-fruit chain, then fixes the missing reverse link. It also tries the student's proposed integer link, 17, on a separate node. The caught exception leaves that damaged node untouched by any later checks.

```python
from item import Item           # Use the same class in an independent process.

apple = Item("apple")           # Build three nodes with banana in the middle.
banana = apple.append("banana") # Keep the inserted node for the extraction test.
mango = banana.append("mango")  # Mango's predecessor is initially banana.
apple._next = mango             # Only the forward bypass is repaired.
print(apple.next().object())    # Follow apple to mango; print mango.
print(mango.prev().object())    # Reverse still reaches banana; print banana.
mango._prev = apple             # Repair the missing reverse bypass.
banana._prev = None             # Detach banana on the backward side.
banana._next = None             # Detach it on the forward side as well.
print(mango.prev().object())    # Corrected backward traversal now prints apple.

bad = Item("apple")             # New isolated test; keep the good chain intact.
bad._prev = 17                  # Integer violates the neighbor-type invariant.
try:                            # Attempt extraction so the fault is observable.
    bad.extract()               # Tail branch tries to write 17's _next attribute.
except AttributeError as error:  # Bind the raised exception inside this handler.
    print(type(error).__name__) # type gives the class; __name__ gives its name.
```

**Expected output, independently reproduced**

```text
mango
banana
apple
AttributeError
```

| Symptom | First incorrect operation or assumption | Small discriminating check |
|---|---|---|
| Forward display skips banana, reverse still visits it | Only the predecessor's forward link changed | Read `mango._prev` before clearing banana |
| Remainder handle is lost | Saving `self._next` after clearing it reads `None` | Trace the value at the save line |
| Integer link fails at source L103 | `self._prev._next = None` tries to write an attribute on integer 17 | Check the type of the immediate field owner; later cleanup cannot run |

**Key Items to Scan**

- A leading underscore marks an internal field by convention. The class declaration supplies no runtime check against assigning 17.
- The historical routine assumes a valid chain. The `except` handler above belongs to the practice driver.
- Break one relationship at a time, predict which traversal detects it, then rerun the same check after repair.

## Decide which file may print

A class definition creates the class and its methods. Their bodies run when called. The historical class-only file therefore produced no output on direct execution. Adding `a = Item()` and `print(a)` at module level changed both a direct run and an initial import. To keep that test available for direct execution, add the following **module-level footer** after the class in `src/item.py`. Start the `if` at the left margin.

```python
if __name__ == "__main__":  # Compare Python's module variable to a string literal.
    a = Item()              # Only the entry-point module creates this test node.
    print(a)                # Display its default representation, including its class.
```

| Saved footer and launch | Name inside item.py | First observable result |
|---|---|---|
| Class only, direct run | `__main__` | Quiet completion |
| Unguarded pair, direct run | `__main__` | `<__main__.Item object at 0x0000017AD9C986E0>` |
| Guarded pair, direct run | `__main__` | `<__main__.Item object at 0x000002243E7386E0>` |
| Guarded pair, driver launch | `item` | Driver's `Fruit:` heading after its leading newline |
| Unguarded pair, driver launch | `item` | `<item.Item object at 0x0000021A514786E0>` before `Fruit:` |

These hexadecimal strings are the master’s reported values from separate runs. Predict the module and class prefix when repeating the test. The number can change. In a fresh process the import loads the class module before control returns to the driver. `print(a)` prints the default representation. `print(a.object())` would print the empty payload, `None`.

**Read an edit as a revision.** The master records a temporary `print()`, the incomplete header `if __name__ =`, a commented guard with an indented assignment, and an active guard with a flush-left body. A lone `print()` would emit a newline. The incomplete header cannot parse. A stray top-level indent or an active header without an indented suite can raise `IndentationError`. These transient edits have no paired successful classroom runs. The final body was reindented. The master records no fresh run after restoration.

**Challenge 2070.** The described submission is an importable class with `next`, `prev`, `first`, `last`, `append`, `prepend`, `insertAfter` and `insertBefore`. The grader supplies its own driver. Unrequested standard output can interfere. The instructor placed `extract` and later empty-list handling outside this challenge's earlier scope. The oral overview does not establish the complete grading contract.

**Key Items to Scan**

- `__name__` is an unquoted variable. `"__main__"` is the string assigned to the entry-point module's name.
- Importing the guarded module still defines `Item`. Both test statements must sit inside the guard.
- Match terminal output to the saved revision and launch command. Previous scrollback can remain visible after an edit.

## Diagnose the failed import at line 1

The classroom layout put `item.py` in `src` and `fruit.py` in a sibling `test` directory. At the repository prompt, `py .\test\fruit.py` began with `from item import Item` and failed at line 1. The traceback named `C:\CSCS1240F26\Item\test\fruit.py` and ended with `ModuleNotFoundError: No module named 'item'`. No fruit was constructed.

| Classroom action at the PowerShell prompt | What the action established |
|---|---|
| `py .\src\item.py` | The class file could run directly; this did not test import from the driver. |
| `py .\test\fruit.py` | Lookup of module `item` failed before Python could bind `Item`. |
| `.\script\path.ps1` then repeat the driver command | Same error. The helper's successful launch did not validate its path. |
| `cat .\script\path.ps1` | Its setting ended in `C:\CSCS1240F25\Item\src`. |
| Append `C:\CSCS1240F26\Item\src` to the shell's `PYTHONPATH`, rerun | Lookup succeeded. The guarded driver began its fruit report. |

Both paths ended in `Item\src`. The differing component was `F25` versus `F26`. The change was observed in the shell's environment; a saved edit to the helper was not established. Python normally starts a script's module search with its directory. It can also search directories on `PYTHONPATH`. Launching from the repository prompt does not recursively add sibling directories. An IDE can supply search paths through its run configuration. Formal packaging was deferred.

**Practice launch.** In a new PowerShell window, start in the practice project root containing `src` and `test`. The first command adds the actual local `src` for this shell's child Python processes. The next commands run the named files. These use Windows separators.

```powershell
$env:PYTHONPATH = "$(Resolve-Path .\src);$env:PYTHONPATH"
py .\src\item.py
py .\test\fruit.py
py .\test\check_links.py
py .\test\broken_links.py
```

**Revision context.** The master identifies `8887c71` as the earlier empty-list checkout and `a5c546d` as the assembled extraction version. The historical `git checkout master~9` selected a revision nine first-parent steps from `master` and changed the checked-out working tree. Seeing a revision selected establishes which code was available for inspection. A separate invocation supplies execution evidence.

**Key Items to Scan**

- Read the failing line, locate the file it needs, compare the full effective path, change one setting, rerun the identical command.
- `$env:PYTHONPATH` belongs to the PowerShell process. Windows separates entries with `;`. macOS and GNU/Linux use `:`.
- A lookup failure precedes class use. A syntax failure occurs when Python parses the file. An `AttributeError` in `extract` occurs after the class has loaded and a method has been called.

## Syntax reference · objects and links

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `class` | Define a type and its namespace. | `class Item:` | Executing the definition makes methods available. |
| `def` | Define a callable with parameters and an indented body. | `def extract(self):` | The body runs on a call. |
| `self` and bound method | Receive the instance on which a method was called. | `selected.extract()` | For this class, `Item.extract(selected)` supplies that instance explicitly. |
| `__new__` | Construct an instance during a normal class call. | `Item("apple")` | The inherited creation step precedes initialization. |
| `__init__` | Initialize the new object. | `self._object = obj` | Writes the payload on this instance. |
| Default parameter | Supply a value when the argument is omitted. | `obj = None` | `Item()` creates an empty representative. |
| `None` | Singleton value marking absence. | `_prev = None` | An absent link and an empty `Item` are different values. |
| Payload and links | Store data and the two neighboring references separately. | `_object`, `_prev`, `_next` | An isolated receiver keeps its payload. |
| Class and instance attributes | Class attributes provide shared defaults; an instance write sets its own field. | `_next = None` then `last._next = newItem` | Other nodes retain their prior links or defaults. |
| Reference and alias | A name or field can identify an existing object. | `tail = head` | An assignment can add a handle without creating a node. |
| Rebinding | Change which object a name identifies. | `fruit = fruit.last()` | The links survive the handle move. |
| Chained attribute write | Evaluate the right side, then follow the target to the field owner. | `self._prev._next = self._next` | Node 4 receives node 6 in its forward field. |
| Reciprocal-link invariant | Adjacent nodes agree in opposite directions. | 4.next is 6 and 6.prev is 4 | Forward success alone cannot prove the reverse repair. |
| `object()` | Call the classroom payload accessor. | `iter.object()` | Returns the fruit value for `print`. |
| `prev()` / `next()` | Take one step, or return the receiver at the corresponding end. | `peach.next()` | Returns peach even though its raw forward link is absent. |
| `first()` / `last()` | Follow raw links to an endpoint. | `fruit.first()` | Produces the head from a tail handle. |
| `append` / `prepend` | Insert at an endpoint, returning the inserted node. | `fruit.prepend("acai")` | Can begin at peach and add before apple. |
| `insertBefore` / `insertAfter` | Insert adjacent to the receiver. | `fruit.insertBefore("mango")` | Banana and peach acquire new neighbor references. |
| `extract()` and `return` | Detach the receiver and send a survivor handle back to the caller. | `return rest` | The result can be a predecessor or a fresh empty node. |
| `del` | Remove a binding in the name-deletion case. | `del selected` | Other references can keep the node alive. |

## Syntax reference · control flow and diagnostics

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `if` / `else` and indentation | Select one suite, then resume at the enclosing indentation. | L96 → L109 | The common cleanup runs on every normal path. |
| `==` / `!=` | Test equality or inequality. | `self._next != None` | Historical spelling. With this class's default equality, `iter.next() == iter` detects the same object. |
| `and` | Evaluate the right operand only if the left is truthy. | Two missing-link tests at L95 | A present predecessor skips the successor test there. |
| `is` / `is not` | Compare object identity or distinctness. | `rest is not selected` | Two empty nodes can hold identical data and remain distinct. |
| `while True` | Repeat a suite until an explicit exit. | `display` traversal | Check the endpoint after printing. |
| `break` | Exit the innermost loop immediately. | Tail reached in `last()` | Execution continues after the loop. |
| `for ... in` | Bind the next element on each iteration. | `for payload in (None, "apple")` | Each input receives a fresh test node. |
| Tuple | Collect an ordered group of inputs. | `(None, "apple")` | The loop first tests empty, then singleton. |
| `range` | Supply successive integers with an exclusive stop. | `range(2, 11)` | Gives 2 through 10; `range(4)` gives four moves. |
| `assert` | Raise `AssertionError` if a check is false in a normal run. | `assert rest is left` | Successful checks produce no output; optimization can disable them. |
| `try` / `except` | Attempt a suite and handle a matching raised exception. | `except AttributeError` | Only the damaged-link fixture catches this error. |
| `as` in a handler | Bind the caught exception to a local name for the handler. | `as error` | The handler can inspect the actual exception. |
| `type()` | Obtain the class of an object. | `type(error)` | Produces the exception class. |
| `.__name__` on a class | Read its name string. | `type(error).__name__` | Produces `AttributeError` for the integer-link fault. |
| `print()` and `\n` | Write values to standard output; newline escape starts a new line. | `print("\nFruit:")` | Adds a blank line before the heading. A multi-argument print separates values by spaces. |
| `from ... import ...` | Load a module and bind a selected name. | `from item import Item` | Top-level module work can print before driver output. |
| Module `__name__` | Identify the module's execution role. | `if __name__ == "__main__":` | Direct launch runs the guarded suite; import under `item` skips it. |
| Traceback / `ModuleNotFoundError` | Locate the first failing import and report missing module lookup. | `fruit.py` line 1 | No fruit method has run at that point. |
| `PYTHONPATH` | Add module search directories through the process environment. | `C:\CSCS1240F26\Item\src` | Match the actual directory containing `item.py`. |
| `master~9` | Identify the ninth first-parent ancestor. | `git checkout master~9` | Selects repository state; does not run Python. |

## Test your understanding

1. Two nodes each have absent links. What observation distinguishes an empty representative from a populated singleton? What object comes back when you extract either?
2. On `A ↔ B ↔ C ↔ D ↔ E`, remove D. Write the first two neighbor assignments in terms of C and E. Which identifier must the caller keep to inspect the isolated payload?
3. After only the first repair in question 2, predict the result of walking forward from C and backward from E. Why could a printed forward list give false confidence?
4. The fruit handle is peach. Predict the return of `fruit.insertBefore("mango")`, the value of `fruit.object()` immediately afterward, and the first value displayed after `fruit.prepend("acai")`.
5. Which part of `if self._prev == None and self._next == None` does Python skip at node 5 of ten? Which line runs immediately after L96 on the singleton path?
6. A driver prints `<item.Item object at ...>` before `Fruit:`. Which file produced the extra output? Where should the assignment and print be indented?
7. The driver fails at line 1 with `No module named 'item'`. The helper ran, and its path ends in `Item\src`. Which components would you compare before changing the class?

### Answer key

1. Read `_object`. The empty node has `None` and the singleton holds its payload. Both calls construct and return a distinct, empty `Item`. Either original receiver still exists.
2. Set `C._next = E`, then `E._prev = C`. Keep the caller's reference to D. The return value is C, through which A, B and E remain reachable.
3. From C, `next()` reaches E. From E, `prev()` still reaches D. Only the reverse traversal exposes the stale field after that first repair.
4. Insertion returns mango. The driver discards that result, so `fruit.object()` still yields `"peach"`. Display begins at acai because `first()` walks back to the new head.
5. With a predecessor present, Python skips `self._next == None` in L95. After L96 on the singleton path, execution continues at L109, which clears `self._prev`.
6. `item.py` printed while loading. Put both `a = Item()` and `print(a)` inside the module-level main guard. Keep the class outside that guard so the driver can import it.
7. Compare the traceback, saved helper text, current environment value, and actual directory containing `item.py`. In class the stale component was `CSCS1240F25`. The working value used `CSCS1240F26`. Repeat the identical driver command to isolate the effect of that single change.

### Technical references

- [Python class and instance variables](https://docs.python.org/3/tutorial/classes.html#class-and-instance-variables) · Attribute ownership and lookup.
- [Python evaluation order](https://docs.python.org/3/reference/expressions.html#evaluation-order) · Right-hand values in chained attribute writes.
- [Python import statement](https://docs.python.org/3/reference/simple_stmts.html#the-import-statement) · Module loading and name binding.
- [Python top-level environment](https://docs.python.org/3/library/__main__.html#idiomatic-usage) · Direct execution and guarded tests.
- [Initialization of the module search path](https://docs.python.org/3/library/sys_path_init.html) · Script directory and `PYTHONPATH`.
- [Git revision notation](https://git-scm.com/docs/gitrevisions#_specifying_revisions) · First-parent ancestry.
