# Counting and indexing a doubly linked list

**CSCS1240F26 · Session 25 · Compressed technical companion · v1.2**

Apple is already one stored item, but reaching apple from the head takes zero moves. Count the items in apple → banana → grape and the answer is **3**. Ask for index **2** and the answer is the **grape node**. Read its stored value with `.object()`. This difference explains both counters and both stopping conditions.

**Practice setup.** Use an empty practice folder. Collect the blocks headed **item.py** in order in one file, keeping the indentation under `class Item`. Save each named driver beside it and run it with `py filename.py`. Each driver starts fresh. The constructor, link-building helpers and practice drivers identified as supplemental are teaching code. The classroom algorithms retain their decisions and return behavior with normalized spacing and guide-added comments. Only the count driver's three lines are reported classroom terminal output.

## 1. Build the three states a counter must distinguish

An empty list in this design has a real head node. Its value and both links are `None`. A singleton also lacks neighbors, but its value is `"apple"`. A test of the next link alone gives the same answer for both. A populated middle node, by contrast, can lead you to either neighbor.

### item.py · practice scaffold

These support methods provide the minimum construction, navigation and value access needed for this lesson. The original implementations are outside the supplied excerpts. This practice `append` fills an empty head or attaches a new tail. The demonstrations use fruit strings and reserve `None` for the empty representation.

```python
class Item:                            # One node can be the entry point to the chain.
    def __init__(self, obj=None):      # Item() supplies None; Item("apple") supplies apple.
        self._object = obj             # Store the payload on this particular node.
        self._prev = None              # A new node has no predecessor.
        self._next = None              # It has no successor either.

    def first(self):                   # Can be called on apple, banana or grape.
        node = self                    # Local cursor begins on the receiving node.
        while node._prev is not None:  # Keep going while a predecessor exists.
            node = node._prev          # Read the old node's link, then rebind node.
        return node                    # The head's backward link is None.

    def object(self):                  # Read the payload of the receiving node.
        return self._object            # The caller gets "apple" or, on empty, None.
```

```python
    def append(self, obj):             # Reconstructed helper; attach one payload.
        tail = self.first()            # Start at the shared head.
        if tail._object is None:       # The empty placeholder can hold the first value.
            tail._object = obj         # Fill that same node without adding a link.
            return tail                # End this branch and give back the filled node.
        while tail._next is not None:  # Find the last node reachable from the head.
            tail = tail._next          # Only the local cursor changes on this line.
        added = Item(obj)              # Allocate, for example, the banana node.
        added._prev = tail             # Banana's backward link points to apple.
        tail._next = added             # Apple's forward link now points to banana.
        return added                   # Driver can ignore this node; the links persist.
```

### item.py · classroom count method, lines 135–146

```python
    def count(self):                   # Count values in the chain containing self.
        iter = self.first()            # Even a call on banana begins at the head.
        if iter._object == None:       # Classroom equality check on the stored value.
            count = 0                  # A placeholder contributes no stored fruit.
        else:                          # The first node already contains a value.
            count = 1                  # Credit apple before following any links.
        while True:                    # Both starting cases reach this loop.
            if iter._next == None:     # Tail reached; its contribution is settled.
                break                  # Leave this loop and proceed to the return.
            iter = iter._next          # Read apple's successor, then rebind to banana.
            count += 1                 # Equivalent here to count = count + 1.
                                       # Credit the successful move only once.
        return count                   # Return one integer to the caller.
```

### count_driver.py · classroom count driver

```python
from item import Item                  # Load the class from the neighboring module.
fruit = Item()                         # Case 1: one physical node, zero fruit.
print(fruit.count())                   # count returns 0; print displays 0.
fruit = Item("apple")                   # Case 2: bind fruit to a new singleton.
print(fruit.count())                   # The first node contributes 1.
fruit = Item("apple")                   # Case 3: start from another fresh singleton.
fruit.append("banana")                 # Mutate this chain; discard the helper's result.
print(fruit.count())                   # Apple and banana contribute 2 together.
```

| Case | Operation | Cursor / counter after | Next action |
|---|---|---|---|
| Empty | Inspect head payload | Empty node / 0 | Enter shared loop |
| Empty | Test successor → absent | Empty node / 0 | Break, return 0, display 0 |
| Singleton | Inspect head payload | Apple / 1 | Enter shared loop |
| Singleton | Test successor → absent | Apple / 1 | Break, return 1, display 1 |
| Pair | Inspect head payload | Apple / 1 | Enter shared loop |
| Pair | Follow apple's successor | Banana / 1 | Credit the move |
| Pair | Increment | Banana / 2 | Test successor again |
| Pair | Test successor → absent | Banana / 2 | Break, return 2, display 2 |

**Key Items to Scan**

- `_object` answers “is there a stored value?” and `_next` answers “can I move forward?”
- `iter` here is a local variable. Rebinding it leaves both links intact.
- **Empty, singleton, pair** exercise two different initial counts and the first successful move. The reported output was `0`, `1`, `2` on separate lines.

## 2. What limits the count?

A million-item experiment gives one measurement. To reason about a billion or a trillion, first ask what the counter represents. In `count = 2`, **count** is a name, **2** is its value and **int** is the value's type. A memory address concerns the location of a particular object. Width concerns how many binary choices a representation can encode.

| Fixed width | Number of patterns | Unsigned range starting at zero |
|---|---|---|
| 8 bits | 2⁸ = 256 | 0 through 255 |
| 16 bits | 2¹⁶ = 65,536 | 0 through 65,535 |
| 32 bits | 2³² = 4,294,967,296 | 0 through 4,294,967,295 |
| 64 bits | 2⁶⁴ = 18,446,744,073,709,551,616 | 0 through 18,446,744,073,709,551,615 |

Each bit doubles the number of possible patterns. Eight bits give **256 choices**. Including 0 through 256 would require **257**. Assigning some patterns to negative values changes their interpretation. A capacity adequate for one account can be inadequate for aggregate transaction counts.

Python integers can grow to use additional storage. Common fixed-width integer types in C, C++ and Java illustrate a different representation choice. Growing an integer takes resources, allocating a chain takes memory per node, and counting still visits each node. The class posed the large cases as questions. It reported no timing benchmark. Arrays and stacks were mentioned as possible later settings for representing large numbers.

**Key Items to Scan**

- **Patterns versus largest value** — subtract one from the number of patterns for a zero-based unsigned maximum.
- **Representation versus traversal** — integer capacity, available node memory and time to follow links answer three different questions.

## 3. Read the proposed interface from the caller's side

The commit message `at(index)` invites a prediction about what a caller receives. The list already supports navigation from either end. A convenience method can package the forward walk behind one positional request. For built-in colors, the equivalent idea is already familiar.

### expressions.py · classroom expressions, isolated for practice

```python
color = ['red', 'white', "blue"]       # A list display creates three slots, 0 to 2.
color[1]                              # Read 'white'; a bare script expression is quiet.
fav = color[1]                        # Bind the selected string; color stays intact.
n = 2 + 3                             # Apply binary + to 2 and 3; bind n to 5.
r = (2 + 3)                           # Group a single sum; r is the integer 5.
r = (2 + 3) * 4                       # Grouping gives 5 first, then 5 * 4 = 20.
r = 2 + 3 * 4                         # Multiply first: 3 * 4 = 12, then 2 + 12 = 14.
m = (2, 3)                            # The comma builds a two-entry tuple.
p = [2, 3]                            # A list display builds a separate mutable pair.
p[1] = 6                              # Write the second slot; same list becomes [2, 6].
m[1] = 6                              # At runtime the tuple rejects this slot write.
```

These were distinct editor revisions and discussion alternatives. This isolated sequence lets you run them together. Before the last line, nothing prints. Left uncaught, that line ends the script with `TypeError: 'tuple' object does not support item assignment`. The tuple stays `(2, 3)`. The source literal in the editor still reads `[2, 3]` after the list in memory changes.

The punctuation tells you what to do only in context. In `fruit.append("banana")`, parentheses call a method and the string is an **argument**. In `(2 + 3)`, they group an expression. In `color[1]`, the brackets request subscription and `1` is the index operand. In `2 + 3`, the operator sits between its two operands, hence **binary infix**. Prefix and postfix notation were mentioned for later. The incomplete editor states `n ==` and `r = (2 + 3 * 4` were typing snapshots. The completed assignment uses one equals sign, and the discussed ungrouped expression is valid Python.

### collections_demo.py · supplemental collection checks

```python
members = {2, 2, 3}                   # A set retains two distinct elements.
labels = {'first': 2, 'second': 2}    # Unique keys can map to duplicate values.
print(members == {3, 2})              # Membership matches regardless of order: True.
print(len(members), len(labels))      # Two members and two key/value pairs: 2 2.
```

| Type | What identifies an entry? | Repetition | Can this collection change? |
|---|---|---|---|
| List | Integer position in order | Values may repeat | Yes, replace a slot with `p[1] = 6` |
| Tuple | Integer position in order | Values may repeat | Slots are fixed; a name can still be rebound |
| Set | Membership, without an ordinal index | Equal members collapse | A set's membership can change |
| Dictionary | Key; insertion order is preserved | Keys are unique, values may repeat | A key can be assigned a new value |

**Key Items to Scan**

- `color[1]` yields white. Changing the expression to `color[11]` raises built-in `IndexError` before a new value can bind to `fav`.
- `=` binds or assigns; `==` compares. The operation `m[1] = 6` is valid syntax whose receiver rejects it at runtime.
- Ask three separate questions about a collection: how entries are located, whether duplicates survive, and whether existing entries can change.

## 4. Implement the walk to index n

Index zero means “stay at the head.” Index two means “take two forward links.” The method returns the reached **node**, so the caller can decide whether to read its value or keep navigating. The implementation uses its own position counter and can stop as soon as it reaches the target.

### item.py · classroom at method, lines 148–158

```python
    def at(self, n):                   # n is the requested index; self may be banana.
        iter = self.first()            # Follow predecessors back to apple first.
        if iter._object != None:       # Only a populated head enters the search.
            count = 0                  # Already at apple; zero forward moves so far.
            while count < n:           # Does the target still lie ahead?
                if iter._next == None: # Is another move required at a node with no next?
                    iter = None        # Clear this local name; grape still exists.
                    raise Exception("Index beyond end.")  # Exit through the handler.
                iter = iter._next      # On success, rebind to the successor node.
                count += 1             # Record one completed move; test again.
        return iter                    # Return the reached node, including an empty head.
```

**Trace separate calls.** Start each call on apple → banana → grape. During a successful iteration there is a moment after rebinding and before incrementing when the cursor has moved but the count has yet to catch up. Each row below describes one transition. The shared rows apply identically to the listed requests.

| Request n | Operation | Cursor / count after | Consequence |
|---|---|---|---|
| 0, 2, 3, 11 | Find head, test its payload, initialize | Apple / 0 | First loop test |
| 0 | Test `0 < 0` → false | Apple / 0 | Return apple node; no link test |
| 2, 3, 11 | Test `0 < n`, then follow available link | Banana / 0 | Increment pending |
| 2, 3, 11 | `count += 1` | Banana / 1 | Back to loop test |
| 2, 3, 11 | Test `1 < n`, then follow available link | Grape / 1 | Increment pending |
| 2, 3, 11 | `count += 1` | Grape / 2 | Back to loop test |
| 2 | Test `2 < 2` → false | Grape / 2 | Return grape node; no tail-link test |
| 3, 11 | Test `2 < n` → true; inspect absent link | Grape / 2 | Enter failure branch |
| 3, 11 | `iter = None` | None / 2 | Chain remains intact |
| 3, 11 | Raise `Exception` | Call exits | No increment or normal return |

**Two unchecked boundaries.** For an empty `Item()`, the payload guard fails. Even `empty.at(11)` returns that very same empty node; `.object()` then returns `None`. On a populated list, `at(-1)` never enters the loop and returns apple. Built-in lists use a negative index to count back from the end. The classroom method establishes neither built-in negative indexing nor general index validation.

**Micro-learning.** Predict the failure by stating *all* its conditions: a populated head, a requested position farther ahead, and a current node with no successor. A tail node is therefore a legitimate success at 2 and the site of failure at 3 or 11. Starting at banana first walks back to apple, then walks forward. Neither method changes a link.

**Key Items to Scan**

- `!= None` checks the stored object in the source. The practice scaffold uses identity tests `is None` and `is not None` for the singleton sentinel.
- `count < n` measures remaining forward progress. An already-satisfied request can run the loop body zero times.
- `break` in `count` reaches its method's return. `raise` in `at` bypasses that method's return and searches callers for a matching handler.

## 5. Follow the exception through the driver

Python evaluates the nested expression from the receiver outward. First `fruit.at(0)` returns the apple node. That node receives `.object()` and returns the string. Only then can `print` display apple. At 11, the first call raises, so neither later call takes place for that line.

### lookup_driver.py · classroom driver through line 15

```python
from item import Item                  # Use the assembled sibling module.
fruit = Item("apple")                  # Position 0 holds apple.
fruit.append("banana")                 # Add position 1; keep fruit bound to apple.
fruit.append("grape")                  # The last reachable position is now 2.
try:                                  # All four requests share this handler.
    print(fruit.at(0).object())        # Node -> payload -> display apple.
    print(fruit.at(11).object())       # at raises at grape; no payload or print.
    print(fruit.at(2).object())        # Skipped even though this position exists.
    print(fruit.at(3).object())        # Skipped too; no second exception here.
except Exception as e:                # Bind the raised object to e in this handler.
    print(e)                          # Display its message: Index beyond end.
fruit = Item()                        # Rebind to a new empty node after the handler.
print(fruit.at(0).object())            # Empty node -> None payload -> display None.
```

**Independent output from the practice implementation**

```text
apple
Index beyond end.
None
```

| Driver location | Result of evaluating that line | Next line that executes |
|---|---|---|
| 7 · index 0 | Prints apple | 8 · index 11 |
| 8 · index 11 | Raises in `item.py`, line 155 in the source listing | 11 · matching handler |
| 11–12 · handler | Binds exception; displays just its message | 14 · new empty item |
| 14–15 · reset and lookup | New receiver; prints its stored `None` | 17 if running the scratchpad |
| 17 · `lunch = fruit[2]` | Unsupported subscription on the fresh receiver | Unhandled failure; lines 20 onward unreachable in that run |

The final row describes the later buffer line, intentionally outside the runnable driver above. It was a proposed spelling. On the practice `Item`, trying it independently raises `TypeError: 'Item' object is not subscriptable`. The later color and tuple failures need **isolated** tests. A `try` covering the earlier four calls cannot catch an exception after that block.

### probes.py · supplemental independent requests

```python
from item import Item                  # Same revision behavior, fresh setup.
fruit = Item("apple")                  # Keep the head of the new chain.
banana = fruit.append("banana")        # Practice helper also gives us its new node.
fruit.append("grape")                  # Complete the three-item setup.
for n in (0, 11, 2, 3):               # Try the classroom requests in order.
    try:                              # A new protected request every iteration.
        node = banana.at(n)           # Even from banana, position 0 is apple.
        print(n, node.object())       # On success print the index and stored fruit.
    except Exception as e:            # Handle this one lookup, then continue the loop.
        print(n, e)                   # 3 and 11 fail at the same missing successor.
empty = Item()                        # One empty placeholder for the identity check.
print(empty.at(11) is empty)          # True: lookup returned that exact object.
print(fruit.at(-1).object())           # 0 < -1 is false; the value is apple.
```

```text
0 apple
11 Index beyond end.
2 grape
3 Index beyond end.
True
apple
```

**Key Items to Scan**

- **Handler placement** determines coverage. Put one handler around four requests to stop on the first failure, or one inside a loop to observe every outcome.
- **Return type** matters at each dot. Returning the string directly from `at` would make a successful caller ask a string for `.object()`.
- **Two different 11s** appear in the source. The custom `at(11)` raises the general `Exception`. Built-in `color[11]` raises `IndexError` if independently reached.

## 6. Where does traversal remember its position?

Counting and indexing need a cursor only until their method returns. A callback can consume each value during one traversal. By contrast, a Python iterator must remember its next position *between* requests. The classroom version uses the head node as both the iterator and the owner of its saved cursor.

### item.py · classroom callback and iterator methods, lines 114–133

```python
    def forEach(self, caller):         # caller supplies an object with a withItem method.
        iter = self.first()            # Begin at the head, even from a middle node.
        while True:                    # Deliver before checking for a successor.
            caller.withItem(iter._object)  # Read this payload, then pass it to caller.
            if iter._next == None:     # The last payload has now been delivered.
                break                  # Leave the loop; implicit return is None.
            iter = iter._next          # Advance this call's local position only.
```

```python
    def __iter__(self):                # iter(fruit) asks the type to start traversal.
        head = self.first()            # Locate the node that owns the saved position.
        head._iter = head              # Next request should deliver apple.
        return head._iter              # Return the head node as the iterator.

    def __next__(self):                # next(position) asks for a single value.
        head = self.first()            # All requests read the same head's field.
        if head._iter == None:         # Is there no node left to deliver?
            raise StopIteration        # Tell the consumer that iteration is finished.
        target = head._iter._object    # Read the payload of the saved current node.
        head._iter = head._iter._next  # Read old cursor's successor; overwrite
                                       # the head's cursor field with that reference.
        return target                  # Deliver the value saved before the advance.
```

### traversal.py · supplemental call sites

```python
from item import Item                  # Callback and protocol methods live on Item.
class Printer:                        # Supply the interface that forEach expects.
    def withItem(self, value):         # forEach supplies one node's payload.
        print(value)                  # Show the delivered value, once per visit.
fruit = Item("apple")                  # Start with the first classroom value.
fruit.append("banana")                 # Build the two-node iterator example.
fruit.forEach(Printer())               # Callback prints apple, then banana.
position = iter(fruit)                 # __iter__ returns the head with cursor at apple.
print(next(position))                  # __next__ saves apple, advances, returns apple.
print(next(position))                  # Saves banana, advances to None, returns banana.
try:                                  # Probe the protocol's termination explicitly.
    next(position)                    # Third request raises before returning a value.
except StopIteration:                 # A for loop consumes this signal automatically.
    print("finished")                 # Teaching output for the exhaustion branch.
```

**Output** (one line per delivery): `apple`, `banana`, `apple`, `banana`, `finished`.

| Request | Saved cursor before | Action and saved cursor after | Result |
|---|---|---|---|
| `iter(fruit)` | No previous traversal required | Set head's cursor to apple | Head node |
| First `next` | Apple | Save apple's value; set cursor to banana | String apple |
| Second `next` | Banana | Save banana's value; set cursor to None | String banana |
| Third `next` | None | Guard raises; cursor stays None | `StopIteration` |

**Boundary insight.** This revision resets the same head cursor whenever `iter(fruit)` runs. Two consumers of the same chain therefore interfere. Also, an empty head is an actual node: `forEach` delivers its `None` once and the iterator yields `None` once before exhaustion, even though `count()` returns 0. Both behaviors follow from where the guards occur.

**Key Items to Scan**

- **Callback** — `caller.withItem` receives each payload during one method call, including the tail's.
- **Protocol** — `iter` initializes and `next` requests. `StopIteration` signals ordinary exhaustion; `None` can be an actual delivered value.
- **Ownership** — `head._iter` persists after a call. Local `iter` in `count` and `at` disappears when the call ends.

## 7. Distinguish walking a link from rewriting it

In the cropped classroom branch at lines 106–112, the current node has two neighbors. The enclosing method name and earlier cases are missing. The following **supplemental middle-node-only function** makes that branch runnable with banana in the middle. Its precondition is a node with both neighbors.

### detach_demo.py · reconstruct only the visible branch

```python
from item import Item                  # Use the practice scaffold for the setup.
def detach_middle(self):               # Teaching function; pass the middle node.
    self._prev._next = self._next      # Apple owns the _next being written.
                                       # Grape's backward link still points to banana.
    self._next._prev = self._prev      # Grape owns the _prev; point it back to apple.
    rest = self._prev                  # Save apple before clearing banana's links.
    self._prev = None                  # Banana no longer points back to apple.
    self._next = None                  # Banana no longer points forward to grape.
    return rest                        # Caller receives the saved apple node.
head = Item("apple")                   # Build a fresh chain for this mutation.
middle = head.append("banana")         # Hold banana even after it is detached.
tail = head.append("grape")            # Hold grape to inspect its backward link.
rest = detach_middle(middle)           # Rewire the neighbors; keep the returned node.
print(rest is head, tail._prev is head)  # Both identity checks display True.
print(head.count(), middle.count())    # Remaining chain has 2; detached banana has 1.
```

| After operation | Apple.next | Grape.prev | Banana.prev / next | Saved rest |
|---|---|---|---|---|
| Before removal | Banana | Banana | Apple / grape | Unbound |
| Write predecessor's next | Grape | Banana | Apple / grape | Unbound |
| Write successor's prev | Grape | Apple | Apple / grape | Unbound |
| Save previous node | Grape | Apple | Apple / grape | Apple |
| Clear banana's prev | Grape | Apple | None / grape | Apple |
| Clear banana's next | Grape | Apple | None / None | Apple |
| Return rest | Grape | Apple | None / None | Caller receives apple |

**Key Items to Scan**

- For `self._prev._next`, resolve `self._prev` first to identify the **owner** of the destination field. The assigned value comes from `self._next`.
- Saving `rest` preserves a reference. Moving the save below the first clear would return `None` in this branch.

## 8. Read the revision before interpreting the output

The same editor and terminal displayed several revisions. Match the saved driver, active revision, executed command and the output **immediately below that command**. A previous run stays in scrollback after checkout. An editor tooltip pointing to `item.py:23` helped locate `append`; it supplied no runtime value.

| Checkpoint | What the supplied Markdown establishes |
|---|---|
| `master~11` · `3907fbe count` | After returning here from `master~10`, `py .\test\fruit.py` displayed 0, 1, 2 on separate lines. |
| `master~10` · `91be848 at(index)` | The next checkout exposes the positional method. The earlier count output remains visible. No corresponding index-driver run is reported. |
| `master~9` · `0ec6bc1 has(object)` | Final checkout exposes the start of a value search. Its return and a call result are outside the available evidence. |

`HEAD` identifies the checked-out Git position. The node called `head` is the first item in memory. In Git, `master~10` follows ten first-parent steps from `master`. Checking out a commit can put the worktree in detached-HEAD state. Git suggested `git switch -c <new-branch-name>` to keep work on a new branch, or `git switch -` to return to the previous checkout. These were suggestions in the terminal; no checkout is needed for the practice files.

### Visible membership search · classroom prefix only

```python
    def has(self, obj):                # Search by stored value; the return is off-screen.
        if self._object != None:       # The guard checks the RECEIVER's payload.
            iter = self.first()        # Then restart the search at the head.
            while iter != None:        # A node, including the tail, is available.
                if iter._object == obj:  # Compare this payload with the requested value.
                    break              # Leave the loop on the first match.
                iter = iter._next      # Otherwise advance, eventually reaching None.
```

The fragment establishes what stops the scan. A `break` alone cannot tell you whether the finished method returns a node, a Boolean, or something else. Keep it out of the runnable module.

### interfaces.py · supplemental complete alternatives

The class left a choice for later: search by value or support brackets. Here are two small, **guide-added** interfaces on a subclass. The membership method explicitly promises a Boolean. For brackets this example deliberately chooses to return the **payload**, delegating navigation to `at`. This minimal adapter inherits `at`'s unusual empty and negative-index behavior.

```python
from item import Item                  # Inherit the assembled classroom algorithms.
class PracticeItem(Item):              # Add practice interfaces to the head's type.
    def contains_value(self, obj):     # Specify a Boolean result for this alternative.
        node = self.first()            # Begin at apple, even from another position.
        while node is not None:        # Examine the current node before advancing.
            # Test for a payload first; an empty head skips the equality test.
            if node._object is not None and node._object == obj:  # Both must hold.
                return True           # First matching payload settles the search.
            node = node._next          # A mismatch advances to the next node.
        return False                  # All reachable nodes have been exhausted.

    def __getitem__(self, n):          # Python routes head[n] to this special method.
        return self.at(n).object()     # First get the node; then return its payload.
                                       # A failed at(n) skips the accessor and return.
fruit = PracticeItem("apple")          # This head supports the new bracket interface.
fruit.append("banana")                 # Attach ordinary nodes through inherited append.
fruit.append("grape")                  # Three payloads now occupy positions 0 to 2.
print(fruit[2])                        # Two forward links; display grape.
print(fruit.contains_value("banana"))  # First match occurs on the second node: True.
print(fruit.contains_value("pear"))    # Walk past grape; no match: False.
```

**Key Items to Scan**

- **Interface versus implementation** — `fruit[2]` becomes available when its type implements subscription. Spelling it with brackets still entails the linked walk.
- **Method contract** — classroom `at` returns a node. The practice adapter returns a payload. The visible `has` prefix establishes no return contract.
- **Separate evidence** — a method on screen, a completed save, a driver invocation and displayed output are distinct observations.

## 9. Syntax reference and library of definitions

### Nodes, ownership and traversal

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| Node / doubly linked list | One object stores a payload and references to its two neighbors. | Apple ↔ banana ↔ grape | Positions are determined by walking links. |
| Head / empty sentinel | The first node may represent an empty chain. | `Item()` | No successor describes both an empty node and a singleton. |
| `class` / inheritance | Define a type or extend a base type. | `PracticeItem(Item)` | The subclass inherits `at` and adds bracket support. |
| `from … import` | Bind a class from another module in this file. | `from item import Item` | Drivers need the sibling module and its completed methods. |
| `__init__` / default | Initialize a new instance; omitted argument uses the default. | `obj=None` | The constructor call yields an instance; the initializer sets fields. |
| `def`, `self` | Define a method with a receiver parameter. | `def at(self, n)` | In `fruit.at(2)`, Python supplies fruit as self and 2 as n. |
| Parameter / argument | A parameter names a formal input; an argument supplies its value. | `n` / `11` | The commit message's “index” corresponds to parameter n. |
| Attribute / underscore | Dot lookup accesses a field or method on its owner; a leading underscore signals internal use. | `iter._object` | Reading a payload and reading a link ask different questions. |
| Reference / rebinding | Assign a name to another existing object. | `iter = iter._next` | Read the old node's link first; rebinding leaves links intact. |
| Mutation | Write a field or slot on an existing object. | `tail._next = added` | Other references to that tail see the new link. |
| Method call | Parentheses invoke a callable with its arguments. | `fruit.append("banana")` | Ignoring a returned node leaves mutations in place. |
| `first()` | Walk backward until no predecessor exists. | `banana.first()` | Counting and indexing use the same origin. |
| `object()` | Return a node's stored payload. | `fruit.at(2).object()` | The receiver of the accessor is the node returned by at. |
| `if` / `else` | Select a branch based on a condition. | `if iter._object == None` | Empty count starts at 0; populated count starts at 1. |
| `while` / `True` | Repeat while the condition holds; True stays true. | `while True` | The count loop needs an explicit exit at the tail. |
| `<` | Test whether another forward move is needed. | `count < n` | Equality at the target stops the lookup before inspecting the next link. |
| `+=` | Augmented assignment adds to this integer and rebinds its name. | `count += 1` | Credit one successful advance. |
| `break` | Exit the nearest enclosing loop. | Tail test in count | Execution resumes after the loop. |
| `return` | Finish this call and hand a value back to its caller. | `return iter` | Returning a node makes a chained method call possible. |
| Identity / sentinel | Test whether references denote the same object. | `empty.at(11) is empty` | `is None` and `is not None` test the singleton's identity. |
| `and` | Test the right operand only if the left is truthy. | Empty guard in contains_value | An empty placeholder skips the payload comparison. |
| Callback | Supply an object whose method receives a value. | `caller.withItem` | The traversal controls when the callback runs. |
| `iter()` / `__iter__` | Get an iterator through the type's protocol. | `iter(fruit)` | This implementation resets a cursor on the head. |
| `next()` / `__next__` | Request one value or signal exhaustion. | `next(position)` | Save the old payload before advancing the shared cursor. |
| `StopIteration` | Exception marking exhausted iteration. | Third request on a pair | A for loop ends normally on this signal. |
| `for … in` | Request successive values from an iterable. | `for n in (0, 11, 2, 3)` | Each loop body runs a fresh independent lookup. |

### Expressions, types, failures and tools

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `=` | Evaluate the right side, then bind or write the target. | `fav = color[1]` | Failed lookup prevents the new binding. |
| `==` / `!=` | Compare for equality or inequality. | `iter._object == obj` | Equality can compare distinct objects by value. |
| `+` / infix operator | Combine two operands using addition. | `2 + 3` | Infix places the operation between the values. |
| `*` / precedence | Multiply; multiplication binds more tightly than addition. | `2 + 3 * 4` | The intermediate product is 12 and the sum is 14. |
| Grouping parentheses | Evaluate a parenthesized expression as a unit. | `(2 + 3) * 4` | The grouped sum is 5 and the product is 20. |
| Tuple / comma | Pack ordered entries into a fixed sequence of slots. | `(2, 3)` | A comma distinguishes this pair from grouping a scalar. |
| List display | Create a mutable ordered sequence. | `[2, 3]` | `p[1] = 6` replaces the second slot in place. |
| Subscription / index | Use a key or zero-based position on an object. | `color[1]` | Returns white; `[2, 3]` by itself constructs a list. |
| `__getitem__` | Supply the type's item-reading operation. | `fruit[2]` | A regular method named at is insufficient on its own. |
| Set / membership | Retain distinct elements without ordinal slots. | `{2, 2, 3}` | Equals `{3, 2}` even though the written order differs. |
| Dictionary / mapping | Associate each unique key with a value. | `{'first': 2}` | A second key can also map to 2. |
| `len()` | Request the number of entries in a supported object. | `len(members)` | Counts distinct set members; here the result is 2. |
| Type / bit width | Type governs supported operations; a fixed width limits distinct patterns. | 8 bits, 256 patterns | Python int can grow; the chain still consumes memory. |
| `raise` / `Exception` | Construct or raise an exception and transfer control. | Index beyond end. | No result flows into the outer accessor or print. |
| `try` / `except` | Protect a suite; handle a matching failure. | Four driver requests | First failure skips the rest of that suite. |
| `as` | Bind the caught exception object within its handler. | `except Exception as e` | `print(e)` displays its message. |
| `print()` | Display its evaluated arguments. | `print(n, e)` | Multiple arguments have a space between them by default. |
| `TypeError` | Operation unsupported for this receiver or operand type. | `m[1] = 6` | The tuple write fails at runtime despite a valid index. |
| `IndexError` | Built-in sequence position falls outside the available range. | `color[11]` | The custom at instead explicitly raises base Exception. |
| Git `HEAD` / revision | Name the currently checked-out position in recorded history. | `91be848` | Old terminal output can belong to a different revision. |
| `~n` / checkout | Follow n first-parent steps; checkout selects a revision. | `master~10` | Inspect the resulting commit and the actual command that ran. |

## 10. Knowledge check

1. An empty head and apple alone both have no successor. What distinguishes their counts, and where does the empty case return?
2. Holding the banana node, you call `at(2)`. Name the starting node, the two moves, the returned object and the operation that displays its value.
3. Why does the call for 11 fail at the same node as 3? Which calls actually run in the classroom driver's single try?
4. Predict `empty.at(11) is empty`, `fruit.at(-1).object()` on three fruit, and the third `next` on apple → banana.
5. Which gives 14: `(2 + 3) * 4` or `2 + 3 * 4`? Why does changing the second slot of `[2, 3]` succeed while the identical write on `(2, 3)` fails?
6. What are the largest ordinary unsigned eight-bit value and the number of patterns? What still limits counting a trillion nodes?
7. After only the first neighbor write in the detach branch, which link is still wrong? What might be lost if you clear banana's predecessor before saving it?
8. Why is the presence of `at` insufficient for `fruit[2]`, and how would you establish which driver produced an old line of terminal output?

### Answer key

1. Inspect the head's payload. Empty starts at 0, apple at 1. Both enter the loop, break on the missing successor and reach the shared return.
2. `first()` walks back to apple. Forward moves reach banana and then grape. `at` returns the grape node. `print(banana.at(2).object())` reads its payload and displays grape.
3. Both requests need another step at grape, whose successor is absent. The driver runs 0 and 11; the failure at 11 skips 2 and 3. The handler prints the message before the empty-head test runs.
4. The identity test is True, the negative request yields apple, and the third next raises StopIteration. An empty node, a stored None, and exhaustion describe different states.
5. The ungrouped expression multiplies 3 by 4 before adding 2. The grouped expression yields 20. A list permits slot reassignment. A tuple preserves its slot references and raises TypeError.
6. Largest 255; 256 patterns. Python's growing integers remove the fixed eight-bit cutoff. Allocating nodes and visiting them still cost memory and time.
7. Grape still points backward to banana until its predecessor is rewritten to apple. Clearing banana's predecessor prematurely loses that route to apple and would make the later save produce None.
8. Subscription looks for the special method on the type. Match the revision, saved driver and invocation, then read the lines following that invocation. In this session the only reported fruit-driver output in the classroom shell was the count run.

### Official technical references

- [Python built-in types: numeric types, sequences, sets and mappings](https://docs.python.org/3/library/stdtypes.html) · [Iterator protocol](https://docs.python.org/3/library/stdtypes.html#iterator-types)
- [Python expressions: subscriptions](https://docs.python.org/3/reference/expressions.html#subscriptions) · [Operator precedence](https://docs.python.org/3/reference/expressions.html#operator-precedence)
- [Python data model: item access](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) · [Handling exceptions](https://docs.python.org/3/tutorial/errors.html#handling-exceptions)
- [Git revision notation](https://git-scm.com/docs/gitrevisions) · [Checking out a revision](https://git-scm.com/docs/git-checkout)

**Source boundary.** Classroom claims come from the supplied Session 25 Markdown, including its historical and micro-learning appendices. The reconstructed scaffold, isolated runs and alternative interfaces demonstrate behavior independently. The original removal method's other branches and the final return of the historical membership method remain unknown.
