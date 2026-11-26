# Doubly linked lists: where did banana go?

**CSCS1240F26 · Session 18 · Compressed technical companion**

Apple, banana, peach, banana, apple. The working program prints those five lines. Remove the two assignments that save the results of `append` and both method calls still run. To predict what prints now, track two things separately: **which node receives each call** and **which node owns each link that gets written**.

## 1. Build a node and read its links

The session works directly with `Item` nodes. Each holds a payload in `object` and references to neighbors in `prev` and `next`. A missing neighbor reads as `None`. The first node is the **head** and the final reachable node is the **tail**. The driver has no separate list object keeping track of those endpoints.

**Classroom implementation with teaching comments.** Save this complete opening revision as `item.py` in a fresh practice folder. Put the drivers beside it and start each run in a fresh Python process. In the classroom project these files occupied `src/item.py` and `test/fruit.py`.

```python
class Item:                         # Define the node type shared by all three fruits.
    object = None                   # Default payload on the class.
    next = None                     # Default forward link until an instance overrides it.
    prev = None                     # Default backward link until an instance overrides it.

    def __init__(self, obj=None):   # self is the new node. Omit obj to use None.
        self.object = obj           # Store "apple" on this instance for Item("apple").

    def append(self, obj):          # self receives the node before the dot.
        newItem = Item(obj)         # Make another node. Its initializer gets this obj.
        newItem.prev = self         # On the first call, banana gains a link back to apple.
        self.next = newItem         # Apple gains a link to banana, replacing any successor.
        return newItem              # Deliver the banana node to the caller. No output.

    def last(self):                 # Search from the receiver along its reachable links.
        iter = self                 # Local cursor starts at the receiver.
        while True:                 # Repeat until the explicit break takes effect.
            if iter.next == None:   # Look ahead while still holding the current node.
                break               # No successor. Leave this loop with the tail in iter.
            iter = iter.next        # A successor exists. Move there and test again.
        return iter                 # Outside the loop, return the existing endpoint.
```

Calling `Item("apple")` starts ordinary instance creation. `__new__` supplies the instance, then `__init__` receives it as `self` and stores the argument. The class call gives the driver the node. Calling this initializer again directly would overwrite the payload on the same node and leave its links in place. The two parameters spelled `obj` in `append` and `__init__` belong to separate calls. During the banana append, the outer `self` is apple while the initializer’s `self` is banana.

**Key Items to Scan**

- `from item import Item` makes the class name available. `Item("apple")` creates one instance.
- `Item.next` stays `None` when an assignment to `self.next` creates a link on apple. Banana still reads the class default for its own forward link.
- `object` is the course’s field name for the payload. Renaming it to `value` would require changing both the writer and its readers.
- `append` in this revision attaches immediately after its receiver. Its name alone offers no guarantee that the receiver is the tail.

## 2. Save each return and walk from peach

**Classroom baseline.** Save as `fruit.py` beside the opening `item.py` and run `python fruit.py`. The right side of each assignment finishes before the name on the left changes. The return from the first call therefore determines who receives the second call.

```python
from item import Item            # Load the node class from the adjacent module.

fruit = Item("apple")            # fruit starts at the single apple node.
fruit = fruit.append("banana")   # Call on apple, link banana, then bind fruit to banana.
fruit = fruit.append("peach")    # Call on banana, link peach, then bind fruit to peach.

iter = fruit.prev.prev           # Peach.prev gives banana. Banana.prev gives apple.
# iter = fruit                   # Inactive alternative. The walk still starts at apple.
print(iter.object)               # Read apple's payload, then display apple.
iter = iter.next                 # Follow apple's forward link. Only iter moves.
print(iter.object)               # Display banana.
iter = iter.next                 # Follow banana's forward link to peach.
print(iter.object)               # Display peach once at the turn.

iter = iter.prev                 # Follow peach's backward link to banana.
print(iter.object)               # Display banana again.
iter = iter.prev                 # Follow banana's backward link to apple.
print(iter.object)               # Display apple again.
```

**Output reported in class and reproduced independently**

```text
apple
banana
peach
banana
apple
```

**Build trace.** The fruit names in the table identify node objects. Only the `.object` fields contain the strings. Each link write affects exactly one instance.

| Operation | Field or name before | State after the operation |
|---|---|---|
| Construct apple | No driver node | `fruit` names apple. Both links read `None`. |
| Make banana inside apple’s call | Apple has no successor | Banana exists locally with two empty links. |
| `newItem.prev = self` | Banana.prev is `None` | Banana points back to apple. Apple.next is still `None`. |
| `self.next = newItem` | Apple.next is `None` | Apple points forward to banana. The pair now links both ways. |
| Return and save banana | `fruit` names apple | `fruit` names banana. The existing links stay in place. |
| Make peach inside banana’s call | Banana has no successor | Peach exists locally with two empty links. |
| `newItem.prev = self` | Peach.prev is `None` | Peach points back to banana. Banana.next is still `None`. |
| `self.next = newItem` | Banana.next is `None` | Banana points forward to peach. Apple.next still reaches banana. |
| Return and save peach | `fruit` names banana | `fruit` names peach. Peach.next still reads `None`. |

**Key Items to Scan**

- `fruit = fruit.append(...)` combines mutation inside the method with rebinding in the caller.
- `iter = fruit.prev.prev` reaches apple through banana. `iter = iter.next` moves the cursor without copying a payload or moving `fruit`.
- `iter` is a hand-written cursor here. No call to the Python built-in `iter()` or iterator protocol drives this walk.

## 3. Drop the assignments and isolate the first failed lookup

**Classroom variation.** For each trial below, use the opening `item.py` and start a new driver with this common setup. At the end of the second call apple’s only forward link leads directly to peach.

```python
from item import Item     # Use the original append-after-receiver implementation.
fruit = Item("apple")     # Fresh apple. Its prev and next both read None.
fruit.append("banana")    # Banana.prev is apple. Apple.next is banana. Ignore the return.
fruit.append("peach")     # Peach.prev is apple. Replace apple.next with peach.
```

Banana was constructed. The overwritten field was **apple’s `next`**, so going forward from apple can no longer reach banana. This tells us about reachability. It supplies no observation of when Python destroys the detached node.

**Trial A · two backward lookups.** Add this block immediately after the common setup.

```python
iter = fruit.prev.prev    # Apple.prev gives None. The second .prev raises on None.
print(iter.object)        # Unreachable. The assignment above did not finish.
```

**Trial B · one backward lookup.** Start again with the same setup and this block.

```python
iter = fruit.prev         # Apple's prev exists and supplies None. Assignment succeeds.
print(iter.object)        # Evaluating None.object raises before print receives a value.
```

**Trial C · start at apple.** This version reaches two print calls. A successful read of the end marker occurs between the second print and the exception.

```python
iter = fruit              # Start at apple. fruit still names this same node.
print(iter.object)        # Print apple.
iter = iter.next          # Apple's replacement link takes the cursor to peach.
print(iter.object)        # Print peach.
iter = iter.next          # Peach.next supplies None. This line completes.
print(iter.object)        # None has no object field. No third fruit is printed.
```

| Driver start | Successful result before failure | First illegal lookup and historical line |
|---|---|---|
| `fruit.prev.prev` | Apple supplies `None`. No fruit printed. | `None.prev` at line 7 in the early driver, line 10 after the first arithmetic insertion, line 11 in the later layout. |
| `fruit.prev` | `iter` receives `None`. No fruit printed. | `None.object` at the first print, early line 9 or later line 13. |
| `fruit` | `apple` then `peach` printed. Cursor advances to `None`. | `None.object` at line 17. The backward walk is never reached. |

The corresponding messages are `AttributeError: 'NoneType' object has no attribute 'prev'` and `AttributeError: 'NoneType' object has no attribute 'object'`. The reported lines refer to the classroom driver. The compact listings have different line numbers.

**Key Items to Scan**

- Ask “whose `next`?” twice. With bare calls both writes target apple. With saved returns the second write targets banana.
- A link containing `None` is legal to read. A later request for `prev` or `object` on that value raises.
- Read the traceback backward to find where the cursor acquired `None`. Trace forward from construction to find the earlier write that skipped banana.
- Compare the fruit lines within one command’s output. Earlier output still visible in scrollback belongs to its own invocation.

## 4. Read punctuation in context

In `iter = fruit.prev.prev` the class counted seven meaningful tokens in reading order: `iter`, `=`, `fruit`, `.`, `prev`, `.`, `prev`. Five form the right side. Evaluation begins by resolving `fruit`, then proceeds through `(fruit.prev).prev`. The first lookup supplies the **owner** for the second. The assignment takes effect only when that expression succeeds.

In `fruit.append("banana")`, Python looks up the attribute **named** `append` on `fruit`. The parentheses call the resulting bound method. The instructor called the dot “member of” to emphasize its two required sides. The name on the right is written literally. Python does not look for a separate variable called `append`.

**Classroom arithmetic experiments, grouped for practice.** Run these assignments together as a separate script. The comments give checked values. The assignments produce no printed output. When the class moved the arithmetic above the known failing traversal, reaching that later failure showed the earlier statements could run.

```python
a = +5             # Unary plus applies to one operand. a receives positive 5.
b = 3+5            # Binary plus adds two operands. b receives 8.
a = -5             # Unary minus negates 5. Rebind a to -5.
b = 3-5            # Binary minus subtracts 5 from 3. b becomes -2.
a = +5             # Rebind the same name from -5 to 5.
a = +5.3           # 5.3 is one float literal. Unary plus applies to that value.
```

**Precedence** describes which operations bind most tightly. **Associativity** explains grouping among operators at the same precedence where the grammar permits it. With the class’s `2 + 3 + 4 + 5`, the successive totals are 5, 9, 14. The two attribute lookups also chain left to right. The dot inside `5.3` belongs to the spelling of a number. No attribute lookup occurs there.

**Two deliberate syntax failures.** Compile each line in its own file. A driver containing either as written cannot begin executing. The class recorded the trailing-dot diagnostic and reported rejection of the leading dot. Both isolated examples independently give `SyntaxError: invalid syntax`.

```python
fruit.               # Missing the attribute name after the dot.
```

```python
.append("banana")    # Missing the receiving expression before the dot.
```

Restoring `fruit.append("banana")` makes the statement parse again. It still discards the returned node. A valid expression such as `fruit.prev.prev` can instead fail during execution because the first lookup returned `None`.

**Key Items to Scan**

- `=` identifies assignment. Locate the right-hand value before changing the left-hand name.
- `-5` and `3-5` use the same glyph with one and two operands respectively. Say “negate five” or “subtract five from three” to make the operation explicit.
- `5.3` is one numeric token. `fruit.prev` has a receiver, a dot and a named attribute.
- `SyntaxError` prevents a file from running. `AttributeError` identifies an unsupported lookup reached in a syntactically valid program.

## 5. Find the last reachable node

Read `last` in the opening `item.py`. The call `fruit.last()` has no explicit argument. The receiver supplies `self`. The method’s local `iter` can visit peach while `self` and the driver’s `fruit` still refer to apple. A `for` loop draws successive values from an iterable. This `while` loop instead repeats a condition and advances its own cursor.

**Trace the classroom search on the damaged apple → peach chain**

| Step | Local cursor | Operation or result |
|---|---|---|
| Enter `last` | Unbound → apple | `iter = self` starts from apple. No links change. |
| Test on apple | Apple | `iter.next == None` is false because apple.next reaches peach. |
| Move | Apple → peach | `iter = iter.next` follows that link. Repeat the loop. |
| Test on peach | Peach | `iter.next == None` is true. Execute `break` and skip the move. |
| Leave the method | Peach | `return iter` hands the caller the peach node. No output. |

For a single apple the first test succeeds and the method immediately returns that same apple. A look-ahead test lets the method stop **on** the endpoint. Searching existing links cannot recover banana.

**Trial D · start the display at the returned tail.** Use the fresh bare-call setup from section 3, then run the following caller. This classroom revision makes the method’s return visible through a print.

```python
iter = fruit.last()       # Start on apple, find peach, bind the caller's iter to peach.
print(iter.object)        # Fetch the string from the returned node. Print peach.
iter = iter.next          # Peach has no successor. Binding iter to None succeeds.
print(iter.object)        # None.object raises before the second print can run.
```

The source reports `peach` followed by `AttributeError: 'NoneType' object has no attribute 'object'` at driver line 15. The same exception message occurred at line 17 in trial C, after two fruit lines. The output prefix distinguishes the starting positions.

**Key Items to Scan**

- `while True` enters the loop on the first test. The inner `break` is its normal exit on a finite chain with a missing final link.
- `break` leaves the nearest loop. The less-indented `return iter` then leaves the method with a node.
- `iter.next == None` is the classroom comparison. It tests the successor, preserving the current node for return.
- The method’s cursor and the driver’s cursor are separate names, even when they refer to the same instance.

## 6. Follow both returns in the unfinished append

**Last classroom revision.** In a fresh practice copy of the opening `item.py`, replace only `append` with this indented method. Keep `__init__` and `last`. The class inserted one call before constructing the new node.

```python
    def append(self, obj):       # On both bare calls, self is the original apple.
        self.last()              # Find the tail. Its return is discarded here.
        newItem = Item(obj)      # Resume append. Create the banana or peach node.
        newItem.prev = self      # Write apple into the new node's backward field.
        self.next = newItem      # Write apple's forward field again on the second call.
        return newItem           # The outer return delivers the new node to the driver.
```

| Call and phase | Reference or field before | Result and next action |
|---|---|---|
| Banana call enters `last` | `self` is apple, apple.next is `None` | Search returns apple. `append` discards it and resumes. |
| Set banana.prev | Banana.prev is `None` | Banana now points back to apple. |
| Set apple.next | Apple.next is `None` | Apple now points to banana. Return banana. The driver discards it. |
| Peach call enters `last` | `self` is still apple, apple.next is banana | Search returns banana. `append` discards this useful endpoint. |
| Set peach.prev | Peach.prev is `None` | Peach now points back to apple. Apple still leads to banana. |
| Set apple.next | Apple.next is banana | Apple now leads to peach. Return peach. The driver discards it. |

The inner return identifies an **existing endpoint** for the method that called it. The outer return identifies the **newly created node** for the driver. An ignored inner return leaves the attachment point wrong. An ignored outer return can be harmless once both links have already been written to the correct nodes.

**Closing discrepancy.** Two completed classroom commands, `py .\test\fruit.py`, each printed only `peach` before failing on line 15. The visible modified driver started with `iter = fruit`. Paired with the displayed class, that start produces `apple`, then `peach`, then a failure at line 17. A tail-start driver reproduces the peach-only result. The precise saved file at launch is unknown. The source records no completed classroom repair.

**Key Items to Scan**

- A nested call pauses `append`. When `last` returns, execution continues at `newItem = Item(obj)` with the outer `self` still pointing at apple.
- Check the save indicator, working directory, exact command, output prefix and failing line together. Git revision `4847342` identifies a checkpoint. The `M` on `script/path.ps1` marks a modification. Neither certifies the current editor buffer.
- Isolate revisions in a practice copy. Make one change, save it, rerun from fresh nodes and label the output.

## 7. Repair both links and test the middle node

**Supplemental workbench.** The supplied guide completes the design after the class stops. Keep the opening class and its `last` method in a fresh `item.py`. Replace only `append` with the method below. The caller may retain the original head across multiple calls.

```python
    def append(self, obj):       # The receiver can be any node on the path to the tail.
        tail = self.last()       # Keep the existing endpoint: first apple, then banana.
        newItem = Item(obj)      # Build the new endpoint. Its next link reads None.
        newItem.prev = tail      # On call two, set peach.prev to banana.
        tail.next = newItem      # Now set banana.next to peach. Apple.next stays banana.
        return newItem           # Return the fresh node after both link writes complete.
```

**Repaired second append, called on apple**

| Operation | Apple.next | Banana.next | Peach.prev |
|---|---|---|---|
| Call `self.last()` and save banana | Banana | `None` | Peach does not exist yet |
| Construct peach | Banana | `None` | `None` |
| `newItem.prev = tail` | Banana | `None` | Banana |
| `tail.next = newItem` | Banana | Peach | Banana |
| Return peach | Banana | Peach | Banana |

**Run the repair test.** Save as `check_repair.py` beside the repaired `item.py` and run `python check_repair.py`. The first output line checks the three positions. The second checks the identities of both predecessors. Short-lived names make each lookup easy to inspect.

```python
from item import Item                # Import the revision that saves and uses the tail.

fruit = Item("apple")                 # Keep the head for both appends.
banana = fruit.append("banana")       # Save the returned node for an identity check.
peach = fruit.append("peach")         # Call on apple again. The attachment uses banana.
middle = fruit.next.object            # Apple.next is banana. Read its payload string.
end = fruit.last().object             # Find the tail node first, then read "peach".
print(fruit.object, middle, end)      # Display apple banana peach, separated by spaces.
print(peach.prev is banana,           # Does the back link reach that exact banana?
      banana.prev is fruit)           # Does banana lead back to the original apple?
```

**Independently reproduced output**

```text
apple banana peach
True True
```

**Supplemental boundary test.** This separate driver uses the repaired `item.py`. Here both appended returns are deliberately discarded, as in the failed classroom experiment. Checking identity distinguishes a specific node even if two nodes could hold equal payloads.

```python
from item import Item                # Use the repaired class in a fresh process.

head = Item("apple")                  # One-node chain: head is also its tail.
print(head.last() is head)            # Search returns the same instance. Print True.
head.append("banana")                 # Bare call mutates links. head still names apple.
head.append("peach")                  # Search from apple and attach peach to banana.
middle = head.next                    # Capture the node now reachable in the middle.
end = middle.next                     # Advance exactly once more. This must be peach.
print(middle.object, end.object)      # Print banana peach.
print(end.prev is middle)             # Identity check on the new pair. Print True.
print(head.prev is None,              # The head has no predecessor.
      end.next is None)               # The tail has no successor. Print True True.
```

**Independently reproduced output**

```text
True
banana peach
True
True True
```

**Key Items to Scan**

- `tail = self.last()` chooses the attachment point. `newItem.prev` and `tail.next` must describe opposite directions of the same neighboring pair.
- Checking only `fruit.last().object` gives `"peach"` for both the broken and repaired lists. Check the middle and the return trip.
- `is` tests object identity. A payload comparison alone cannot establish that a link reaches the intended instance.

## 8. Transfer the practice to the front of the chain

The class proposed six exercises: use other payloads, build nodes from command-line arguments, build them from standard input, read values from a file, traverse nodes to write their values to a file, and design `prepend`. Arguments arrive when the program launches. Standard input is a stream read while it runs. Reading a file supplies payloads for construction. Writing a file consumes the payloads produced by traversal. The instructor also suggested Git practice: stage a change with `add`, record the revision with `commit` and share commits with `push`.

**Optional supplemental implementation of the prepend prompt.** Add this method inside the repaired `Item` class. This design accepts a receiver anywhere along the chain, walks backward to its head and returns the new head. The class proposed the exercise and did not specify or demonstrate this implementation.

```python
    def prepend(self, obj):      # Add before the first reachable node.
        head = self              # Begin with the caller's node, which could be peach.
        while head.prev is not None:  # A predecessor exists, so keep walking back.
            head = head.prev     # Advance to the preceding node. self stays unchanged.
        newItem = Item(obj)      # New node starts with no predecessor or successor.
        newItem.next = head      # New head points forward to the old head.
        head.prev = newItem      # Old head now points back to its new neighbor.
        return newItem           # Give the caller the new starting point.
```

**Run separately as `check_prepend.py`** with that method added. This teaching setup inserts banana before an existing apple → peach pair so the receiver and the old head are different nodes.

```python
from item import Item                # Load the practice class including prepend.

apple = Item("apple")                 # Original head.
peach = apple.append("peach")         # Original tail. Peach.prev now reaches apple.
first = peach.prepend("banana")       # Walk back to apple. Insert banana before it.
print(first.object,                   # Read banana from the newly returned head.
      first.next.object,              # Follow banana.next to the old head: apple.
      first.last().object)            # Search to the unchanged tail. Print the three.
print(first.prev is None,             # New first node has no predecessor.
      apple.prev is first,            # Old head reaches the particular inserted node.
      peach.prev is apple)            # The original pair still links in reverse.
```

**Independently reproduced output**

```text
banana apple peach
True True True
```

**Key Items to Scan**

- `while head.prev is not None` tests for a real predecessor before stepping. A singleton begins at the head, so this loop makes zero moves.
- `newItem.next = head` and `head.prev = newItem` join the same pair. The old head’s forward link is preserved.
- Vary the input source or the link algorithm one at a time. A working Git checkpoint makes a one-line regression easier to isolate.

## Syntax reference and library of definitions

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `from … import …` | Bind a name supplied by a module. | `from item import Item` | Makes the class available. Construction happens later. |
| `class` and instance | Define a type, then call it to create a particular object. | `Item("apple")` | The apple node and its string payload have separate identities. |
| `__new__` | Obtain the new instance during ordinary construction. | Called as part of `Item("apple")` | The initializer then receives the instance. |
| `__init__` | Initialize an already-created instance. | `self.object = obj` | Repeating this initializer rewrites the same node’s payload. |
| `def` | Define a function, or a method in a class body. | `def last(self):` | The body runs when called. The colon and indentation delimit the body. |
| `self` and bound method | The method’s first parameter receives the node before the dot. | `fruit.append("peach")` | The value bound to `fruit` at call time chooses the receiver. |
| Parameter and argument | A parameter is a local name. An argument supplies a value. | `obj` receives `"banana"` | The initializer and append have separate local parameters. |
| `obj=None` | Provide a default when the caller omits an argument. | `Item()` | Creates a node whose payload is `None`. |
| Class and instance attributes | A class supplies defaults. An instance can own an overriding field. | `next = None` and `self.next = newItem` | Writing apple’s link leaves the class default available to other nodes. |
| Payload and links | Store data in one field and references to neighbors in two others. | `object`, `next`, `prev` | Traversal moves among nodes. Reading `.object` retrieves data. |
| Head, tail, reachability | The endpoints delimit the path available by following links. | Apple → peach | A detached banana cannot be found on that forward path. |
| `None` | Singleton value marking an absent neighbor here. | `fruit.prev` on apple | Safe to retrieve. Further attribute access can fail. |
| `=` and rebinding | Evaluate the right side, then bind the target. | `fruit = fruit.append("banana")` | Binding moves only after the call returns. |
| Attribute assignment | Mutate a field on the object to the left of the dot. | `tail.next = newItem` | The field owner determines which link gets replaced. |
| `.` and chained lookup | Look up a named member on the preceding object. | `fruit.prev.prev` | The first result supplies the owner for the second lookup. |
| `()` in a method call | Invoke the selected method with the supplied arguments. | `fruit.last()` | No explicit argument is needed for the bound receiver. |
| `return` | Finish a function or method and deliver a value. | `return iter` | Searching returns an existing node. Appending returns a new node. |
| Bare call | Execute a call without binding its final result. | `self.last()` | The search runs, but its returned endpoint is lost here. |
| `print(...)` | Evaluate arguments, then display their values. | `print(iter.object)` | A failed lookup prevents that call from printing. Multiple arguments normally print with spaces. |
| `#` | Begin a comment outside a string. | `# iter = fruit` | The apparent assignment on that line never runs. |
| Token and operand | A token is a unit of source text. An operand is acted upon. | `+5` | The unary operator and its operand have different roles. |
| Unary `+` and `-` | Apply a sign to one numeric operand. | `+5` and `-5` | Yield 5 and −5 for the classroom integers. |
| Binary `+` and `-` | Add or subtract two numeric operands. | `3+5` and `3-5` | Yield 8 and −2. |
| Float literal | A decimal point can be part of one number token. | `5.3` in `+5.3` | The sign applies to the number. No member is selected. |
| Precedence and grouping | Determine how operations bind within an expression. | `2 + 3 + 4 + 5` | Repeated addition groups from the left, yielding 5, 9, 14. |
| `while` and `True` | Repeat a block while its condition is true. | `while True:` | The class uses an internal exit test. |
| `if` and `==` | Choose a branch using a condition, here an equality test. | `if iter.next == None:` | The classroom code tests the next link before advancing. |
| `break` | Exit the nearest enclosing loop immediately. | `break` on peach | Skips the cursor advance. Execution resumes after the loop. |
| Local scope and cursor | A call has its own name bindings for its traversal position. | `iter = self` | Moving this local cursor leaves the driver’s `fruit` in place. |
| `for` | Obtain successive values from an iterable. | Contrast with `last()` | The classroom search follows links explicitly using `while`. |
| `is` | Test whether two references denote the same object. | `peach.prev is banana` | Distinguishes two nodes even if their payloads compare equal. |
| `is not` | Test whether two references denote different objects. | `head.prev is not None` | Stops the prepend search when there is no predecessor. |
| Parenthesized continuation | An open call can continue onto another physical line. | Multiline `print(...)` | Commas separate arguments. The closing parenthesis completes one call. |
| `SyntaxError` | The file has a grammatical defect and cannot run. | `fruit.` | Python rejects the trailing dot before the first statement executes. |
| `AttributeError` | A reached lookup cannot supply the requested attribute. | `None.object` | Identify the receiving value and the failing name in the traceback. |

## Knowledge check

1. Apple and peach are the endpoints in both the good and damaged structures. Which single field on apple reveals the difference, and which field on peach checks the opposite direction?
2. With bare appends, why does changing `iter = fruit.prev.prev` to `iter = fruit.prev` move the failure to a later line? Does the first print produce a blank line?
3. Which of `fruit.`, `+5.3` and `fruit.prev.prev` prevents a script from starting? For the other two, explain what each dot does.
4. Trace `last()` on one apple and then on apple → peach. Where does `break` transfer control? What does `fruit` name after each call?
5. In the repaired second append called on apple, which existing node owns the new forward link? What incomplete state exists after the backward write but before the forward write?
6. The terminal shows one `peach` and an error at line 15. The editor shows `iter = fruit`. How would you identify the saved revision involved?

### Answer key

1. Inspect apple.next and peach.prev. Both reach banana on the intact chain. On the damaged chain apple.next reaches peach and peach.prev reaches apple. Checking only the endpoint values misses both defects.
2. Apple.prev legally supplies `None`. The shorter version assigns it to `iter`. Reading `iter.object` then raises during argument evaluation. There is no call to `print` and no blank line.
3. `fruit.` lacks a member name and raises `SyntaxError` before execution. The dot in `5.3` is part of the numeric literal. The two dots in `fruit.prev.prev` perform successive lookups. That expression runs successfully from peach on the intact chain, and fails at the second lookup from apple.
4. On a singleton the first test breaks. On the damaged chain the cursor advances once and breaks at peach. In each case control reaches `return iter` outside the loop. `fruit` still names apple.
5. Banana owns the forward link. Immediately after `newItem.prev = tail`, peach points back to banana while banana.next still reads `None`. Only the next write makes peach reachable from apple.
6. Note the unsaved indicator, check the on-disk starting expression and exact file path, then save or isolate each candidate and run it with fresh nodes. Tail-start reproduces one peach and line 15. Head-start reproduces apple, peach and line 17. The two historical output groups establish the observed result, not the precise saved source.

## Technical references

The session Markdown supplies the classroom record. These Python documentation sections clarify language behavior. The repair and prepend workbenches are supplemental practice.

- [Class and instance variables](https://docs.python.org/3/tutorial/classes.html#class-and-instance-variables)
- [Instance creation and initialization](https://docs.python.org/3/reference/datamodel.html#object.__init__)
- [Attribute references](https://docs.python.org/3/reference/expressions.html#attribute-references)
- [The break statement](https://docs.python.org/3/reference/simple_stmts.html#the-break-statement)
- [Syntax errors and exceptions](https://docs.python.org/3/tutorial/errors.html)
