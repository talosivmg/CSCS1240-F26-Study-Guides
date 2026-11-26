# Collections, call stacks, and the missing banana

**CSCS1240F26 · Session 17 · Compressed technical companion**

After `g = f.remove(2)`, the set has changed and `g` holds `None`. After `fruit.append("banana")`, apple has changed and the caller has discarded a perfectly good node. To predict the next line, keep three records: **which object received the call, which field changed, and where the returned value went**.

The examples follow the supplied Session 17 Markdown. **Classroom** identifies code or results reported there. **Practice** identifies added drivers, tests or implementations. All instructional comments are added. Enter deliberate failures individually in a fresh REPL or run them as separate scripts. A bare expression echoes in the REPL except when its value is `None`. In a script, use `print` to see a result.

## 1. Choose the collection by the question

| Question | Python collection | What the choice preserves |
|---|---|---|
| What is third, and may I replace it? | `list` | Positions and duplicates. Entries can change in place. |
| What is third, and should the entries stay fixed? | `tuple` | Positions and duplicates. A name can be rebound, but the tuple's slots stay fixed. |
| Which distinct values occur? | `set` | Membership. Repeating a member adds no occurrence. Display order provides no position guarantee. |
| What value goes with this key? | `dict` | Key–value associations in insertion order. An integer key still selects by key. |

**Classroom inputs, with REPL results in comments.** The type of an object and its identity answer different questions.

```python
a = list()       # Call list with no values to make an empty list.
b = []           # Build a second empty list with its own identity.
type(a)          # The REPL displays <class 'list'>.
type(b)          # Same type as a, even though this is another list.
c = tuple()      # With no entries the result is the empty tuple, ().
type(c)          # <class 'tuple'>
d = (3)          # Parentheses group one integer expression.
type(d)          # <class 'int'>
e = (2, 3)       # The comma constructs a pair in the order 2, then 3.
type(e)          # <class 'tuple'>
f = set()        # Make an empty set for the membership experiment.
type(f)          # <class 'set'>
```

**Practice · change one token at a time.** The trailing comma in `(3,)` makes a one-item tuple. Empty braces already mean dictionary. The broader classroom suggestion that sets have no literal needs a qualification: a **nonempty** set has a brace display.

```python
print(type((3,)))      # First form the tuple, then inspect it: <class 'tuple'>.
print(type({}))        # With no entries, braces build a dict: <class 'dict'>.
print({2, 2, 4})       # Only 2 and 4 survive. Their order is unspecified.
print(a is b)          # Using the setup above: False, two separate lists.
```

**Key Items to Scan**

- `(3)` groups a value. `(3,)` and `()` are tuples of length one and zero.
- `set()` starts a membership collection. `{}` starts a mapping.
- `type(a)` identifies the class. `a is b` tests whether two names designate one object.

## 2. Track a set mutation and its return separately

**Classroom · REPL, continuing with the empty `f`.** Inspect the receiver first. `dir(f)` includes `add`, `remove` and `pop`. Looking up those names performs no mutation. A list append has an end position. A set insertion promises membership.

```python
dir(f)            # Inspect the available names; remove is in the inventory.
f.add([2, 4, 6])  # Deliberate error: the brackets build ONE list argument.
                  # Hashing that list fails before any member is inserted.
```

The reported Python 3.14.7 message was `TypeError: cannot use 'list' as a set element (unhashable type: 'list')`. At a new prompt, the same `f` is still empty.

```python
f.add(2)          # Store 2. The method returns None.
f.add(4)          # The same set now contains 2 and 4.
f.add(6)          # The same set now contains 2, 4 and 6.
g = f.remove(2)   # Delete 2, then bind g to the method's return, None.
type(g)           # <class 'NoneType'>. Evaluating g twice still gives no echo.
h = f.pop()       # Remove one remaining member; save that member in h.
h                 # Classroom display: 4. The assignment itself was silent.
f                 # Classroom display: {6}, still a set.
```

**Practice · replace repeated typing with a loop.** Use a fresh set. The list after `in` supplies successive integers, so the list itself is never offered as a member.

```python
f = set()                  # Reset before the four-pass test.
for v in [2, 4, 6, 6]:     # Bind v to one integer on each pass, including both 6s.
    result = f.add(v)      # Mutate f if needed; result receives None on every pass.
print(f, result)           # Contains 2, 4, 6; also prints None.
```

| Experiment / operation | Members before | Members after | Return or control flow |
|---|---|---|---|
| Classroom `add([2, 4, 6])` | empty | empty | `TypeError`; REPL accepts the next command |
| `add(2)` | empty | 2 | `None` |
| `add(4)` | 2 | 2, 4 | `None` |
| `add(6)` | 2, 4 | 2, 4, 6 | `None` |
| Practice only: second `add(6)` | 2, 4, 6 | 2, 4, 6 | `None`; loop ends |
| Classroom `remove(2)` | 2, 4, 6 | 4, 6 | `g` gets `None` |
| Classroom `pop()` | 4, 6 | 6 in this run | `h` gets 4 in this run |

**Key Items to Scan**

- **Hashable** means a stable hash during the object's lifetime, with equal objects hashing equally. Ordinary lists cannot be set members.
- `remove(2)` selects a member by value. `pop()` returns whichever member the set removes.
- Inspect `f` to learn what changed. Inspect `g` or `h` to learn what came back. Looking at a name does not repeat the call.

## 3. Follow a function call into and out of a formatted string

**Classroom editor · `example.py`.** Run the blocks in this section in order to see the successive bindings. The guide uses equivalent quote delimiters in some f-strings so the examples also work before Python 3.12. The unusual closing parentheses inside printed labels are intentional.

```python
def doNothing():        # Bind the function object now; defer its body until called.
    pass                # Perform no action. Reaching the end returns None.

print(f"dir(doNothing): {dir(doNothing)}\n")    # Inspect names on the function.
print(f"type(doNothing): {type(doNothing)}\n")  # Display <class 'function'>.
print(f"id(doNothing): {id(doNothing)}\n")      # Display this live object's ID.
print(f"call of doNothing(): {doNothing()}\n")  # Only the call in braces runs.
                                                # Format None; print one string.
```

The first line of output lists attributes such as `__call__` and `__name__`. The integer identity varies. The last line reads `call of doNothing(): None`. Each string ends in `\n` and `print` supplies another newline, leaving a blank line. Count arguments at the current pair of call parentheses: punctuation in a label and commas inside a nested call do not divide the arguments to `print`.

```python
def doNothing():          # Rebind this name for subsequent calls.
    return None           # Explicitly deliver the value previously returned implicitly.
print(f"call of doNothing()): {doNothing()}\n")  # Label ends in ()): result None.

def douglasAdams():       # A different name retains a different function.
    return 42             # Pass integer 42 to the waiting expression.
print(f"call of douglasAdams()): {douglasAdams()}\n")  # Displays the label and 42.
```

```python
def doNothing(milk):          # This version requires one explicit argument.
    if milk == "fresh":       # Compare the supplied string to the literal.
        return "spoiled"      # Stop here for "fresh"; the result is data.
    else:                     # A different input takes this path.
        return milk           # Return the same value the caller supplied.
print(f'doNothing("fresh")): {doNothing("fresh")}\n')  # Displays the label, then spoiled.
```

**Trace the selected call.** A stack accepts work at one end. Push sheet A, push B, pop B, then pop A. The **call stack** similarly keeps unfinished callers waiting in last-in, first-out order. A set's `pop` has no such ordering. The instructor's return-address analogy distinguishes the place to enter a function from the place to resume the caller. The function's `id` is neither a return address nor a Python instruction pointer.

| Step | Active operation | Result and next action |
|---|---|---|
| 1 | Module reaches the f-string | Literal label is text. Formatting pauses at the replacement field. |
| 2 | Call `doNothing("fresh")` | Bind the argument to local parameter `milk` in a new call frame. |
| 3 | Compare `milk == "fresh"` | `True` selects `return "spoiled"`. |
| 4 | Return to the caller | The call frame finishes. The field formats the returned string. |
| 5 | Call outer `print` | Displays `doNothing("fresh")): spoiled`, then a blank line. |

**Key Items to Scan**

- `doNothing` names an object. `doNothing()` executes its current body. A later `def` rebinds the name for later calls.
- `f"...{expression}..."` evaluates the expression. Doubled braces in literal text produce literal braces. In the classroom's double-quoted label, `\"` contributes a quote to the output.
- **Traceback** reports active frames on an exception. Read the bottom message, inspect the last listed frame, then follow callers upward to find the source of the bad value. The displayed set traceback points to `<python-input-13>`, line 1, in `<module>`.

## 4. Compare the two flour revisions

**Classroom editor · required flour.** `def` replaces the one-parameter binding. Python does not keep an overload to select by argument count. The following driver uses the classroom's two actual strings.

```python
def doNothing(milk, flour):      # Both arguments must be supplied to enter the body.
    if milk == "fresh":          # Only fresh milk reaches the flour comparison.
        if flour != None:        # Test inequality with None; "" also passes here.
            return "matzah"      # "fresh", "white" takes this exit.
        else:                    # Fresh milk and explicit None arrive here.
            return milk          # Hand back the original "fresh".
    else:                        # Skip the flour test for non-fresh milk.
        return "spoiled"         # Result for "old", regardless of flour.
print(f'doNothing("fresh", "white"): {doNothing("fresh", "white")}\n')
# Two inner arguments, one outer string. Output ends in matzah.
```

**Classroom editor · optional flour.** Two edits affect behavior: the default changes which calls are legal, and the inner `else` changes what fresh milk returns. The source does not establish the driver below this definition. The print calls here are **practice inputs**.

```python
def doNothing(milk, flour=None):  # Omission now binds flour to None.
    if milk == "fresh":           # Test milk before inspecting flour.
        if flour != None:         # Compare specifically with None, as in class.
            return "matzah"       # "white" and "" both reach this return.
        else:                     # Fresh milk with omitted or explicit None.
            return "spoiled"      # Changed from returning milk in the earlier revision.
    else:                         # "old" bypasses the inner test.
        return "spoiled"          # Same result through a different branch.
print(doNothing("fresh"))         # spoiled
print(doNothing("fresh", "white"))  # matzah
```

| Call with these inputs | Required flour | Optional flour | Discriminating detail |
|---|---|---|---|
| `("fresh", "white")` | `"matzah"` | `"matzah"` | Both comparisons succeed. |
| `("fresh", None)` | `"fresh"` | `"spoiled"` | Same arguments isolate the changed inner return. |
| `("fresh",)` | `TypeError` | `"spoiled"` | Only the defaulted version enters the body. |
| `("fresh", "")` | `"matzah"` | `"matzah"` | Empty string differs from `None` despite being false in a truth test. |
| `("old", "white")` | `"spoiled"` | `"spoiled"` | The outer alternative skips the flour test. |

**Key Items to Scan**

- `flour=None` supplies a value only when that argument is omitted. An explicit value travels unchanged.
- A function call returns one object, which can itself contain several values. The caller decides whether to store, display or discard it.

## 5. Use a key, inspect a view, or collect a snapshot

**Classroom · REPL.** The receiver determines what brackets mean. In `j[2]`, count from position zero. In `i[2]`, find the association for integer key 2. Python uses hashing and equality for key lookup. A hash is not a unique encoding of a key.

```python
i = dict()         # A new empty mapping; type(i) displays <class 'dict'>.
dir(i)             # Inspect method names, including keys, values, items and fromkeys.
i["one"] = 1       # Add the string key "one" with integer value 1.
j = [5, 7, 9]      # Positions 0, 1 and 2 hold 5, 7 and 9 respectively.
j[2]               # The third element: 9.
j["2"]             # Deliberate TypeError: a string cannot index this list.
```

At the next prompt, continue with `i`. The list error reads `TypeError: list indices must be integers or slices, not str`. Slices were mentioned and deferred.

```python
i[2] = 2           # Create a second association; no intervening keys are needed.
i["one"]           # Look up a stored key: 1.
i.keys()           # dict_keys(['one', 2])
i.values()         # dict_values([1, 2])
k = i.items()      # Keep a live view yielding pairs in key, value order.
k                  # dict_items([('one', 1), (2, 2)])
i[2] = 37          # Replace only the value for existing key 2.
k                  # dict_items([('one', 1), (2, 37)]); no new view was requested.
```

| Classroom probe | First failure or result | Diagnostic question |
|---|---|---|
| `dict.items()` | `TypeError: unbound method dict.items() needs an argument` | The method exists. Which dictionary supplied the receiver? |
| `dict.item()` | `AttributeError: type object 'dict' has no attribute 'item'` | Singular attribute lookup fails before any call. The classroom interpreter suggested `items`. |
| `i.items()` | `dict_items([('one', 1), (2, 2)])` before the edit | The plural method is bound to the two-entry instance. Each pair is a tuple. |

**Practice · freeze the pairs before editing.** Start with a new dictionary. The tuples yielded by the view have fixed slots. Assign through the dictionary key to replace a stored value. Capturing the pairs in a list is a shallow snapshot.

```python
i = {"one": 1, 2: 2}        # Restore the two original associations.
k = i.items()               # Keep access to the live mapping.
snapshot = list(k)          # Consume the view now, saving both current pair tuples.
i[2] = 37                   # Mutate i after the snapshot was collected.
print(k)                    # dict_items([('one', 1), (2, 37)])
print(snapshot)             # [('one', 1), (2, 2)]
print(dict.fromkeys(i, 0))  # Iterate i's keys to build a NEW dict with value 0.
                            # Displays {'one': 0, 2: 0}; i still contains 1 and 37.
```

| Operation | `i[2]` | Second pair available via `k` | Second pair saved in `snapshot` |
|---|---:|---|---|
| Obtain `k` | 2 | `(2, 2)` | No snapshot yet |
| Evaluate `list(k)` | 2 | `(2, 2)` | `(2, 2)` |
| Assign through `i[2]` | 37 | `(2, 37)` | `(2, 2)` |
| Display both | 37 | Current pair prints | Earlier pair prints; earlier terminal output also stays as printed |

**Practice · test a hash collision.** The two unequal keys retain separate values, even on CPython where these two particular hashes coincide.

```python
pairs = {-1: "first", -2: "second"}  # Two unequal integer keys.
print(hash(-1), hash(-2))            # CPython here: -2 -2.
print(pairs[-1], pairs[-2])          # first second; equality distinguishes the keys.
```

**Key Items to Scan**

- `dir(i)` inventories attributes. `i.keys()` exposes stored keys. `dict.fromkeys(i, 0)` builds a new mapping.
- `i.items()` yields key–value tuples. Changing `i` affects the next view display without rewriting past terminal output.
- The same list under two names also shares mutations. `list(k)` explicitly creates a separate outer collection.

## 6. Identify which parser sees the quotes

**Classroom · Python name lookup first.** A quoted key supplies characters as data. An unquoted key requires an existing binding before the mapping can be built.

```python
m = {"red": "apple", "blue": "sky"}  # Colon pairs each key with a value.
n = {red: "apple", "blue": "sky"}    # Deliberate NameError while looking up red.
                                     # The assignment to n never finishes; m survives.
```

In the classroom, evaluating `json` before import raised `NameError` with a suggestion to import the module. Python shipping the library does not bind its name in the caller.

**Classroom · deliberately invalid Python.** Isolate this one input. Python's parser stops at the conflicting quotes, before `json.loads` can run or `p` can be assigned. The reported message was `SyntaxError: invalid syntax. Is this intended to be part of the string?`.

```python
p = json.loads("{"red": "apple", "blue":"sky"}")  # Quote before red closes the string.
```

**Classroom correction and practice round trip.** In a fresh script use the successful `m` assignment above, then this block. JSON objects associate string names with values. Arrays preserve ordered elements. Both can nest. Python first constructs a `str`; decoding that string produces ordinary Python data.

```python
import json                     # Bind the module in this scope.
type(m)                         # At the prompt: <class 'dict'>.
dir(json)                       # Inspect the module for load, loads, dump and dumps.
p = json.loads('{"red": "apple", "blue":"sky"}')  # Outer single quotes delimit Python.
                                                  # Inner double quotes reach JSON.
print(p)                        # {'red': 'apple', 'blue': 'sky'}
print(type(p), type(json))      # <class 'dict'> <class 'module'>
text = json.dumps(m)            # Practice: encode m into a new string.
print(text)                     # {"red": "apple", "blue": "sky"}
print(json.loads(text) == m)    # Decode first, compare mapping contents, then print True.
```

| Stage | Data or error | What can execute next? |
|---|---|---|
| Parse the malformed Python call | `SyntaxError` | No function call takes place. |
| Evaluate the repaired Python argument | One string containing JSON double quotes | Pass that text to `loads`. The outer Python quotes are absent. |
| Decode the object | `{'red': 'apple', 'blue': 'sky'}` | `p["red"]` now retrieves `"apple"`. Python's display may use single quotes. |
| Practice boundary: `json.loads("{'red': 'apple'}")` | Valid Python string, invalid JSON object syntax | The decoder runs and raises `json.JSONDecodeError`. |

**Key Items to Scan**

- `"red"` is a string literal. Bare `red` is a name to resolve. A missing binding and a syntax failure occur at different stages.
- `loads` takes text to data. `dumps` takes data to text. The classroom demonstrated loading; encoding is practice.
- `import json` binds the module. `type(p)` identifies the decoded data. Printing a dictionary does not serialize it to JSON.

## 7. Construct a node, then connect it

**Classroom · restored `src/item.py`.** Here is the complete working class from the supplied guide, using the later spelling `xobj`. For the practice runs, save it as `item.py` in a new folder beside `fruit.py`. Run `py fruit.py` from that folder. This makes the import work without the classroom project's path setup. The classroom instead ran `py .\test\fruit.py` with `src` on its import path.

```python
class Item:                         # One class describes every fruit node.
    object = None                   # Shared fallback for a node's payload.
    next = None                     # No successor until an instance sets a link.
    prev = None                     # No predecessor until an instance sets a link.

    def __init__(self, obj=None):   # self is the new node; obj is the supplied fruit.
        self.object = obj           # Write on this node. Item() stores None here.

    def append(self, xobj):         # self is the existing receiver; xobj is the payload.
        newItem = Item(xobj)        # Resolve Item at call time; initialize a NEW node.
                                    # Its initializer's self is banana on the first call.
        newItem.prev = self         # Banana now points back to apple; apple is unchanged.
        self.next = newItem         # Apple now points forward to banana.
        return newItem              # Give the node reference to the caller, without
                                    # printing.
```

A class call uses `__new__` to obtain an instance and `__init__` to initialize it. The initializer implicitly returns `None`. The **class call** yields the instance. An instance read can find a class default; writing `self.next` sets a field on that receiver. Separate constructions alone never establish neighbors.

**Practice · two disconnected nodes.** In a separate driver, import the class before running this block.

```python
from item import Item             # Bring the class into the driver's namespace.
fruit = Item("apple")             # This node owns the apple payload.
other = Item()                    # The omitted obj uses its default, None.
print(fruit.object, other.object)  # apple None
print(other.next, other.prev)      # None None; construction created no links.
```

**Classroom · identity driver prefix.** The earlier editor version printed only `id(Item)`. The successful three-ID run had these extra comparisons. A class is itself an object. Binding a second name to the class does not construct a node.

```python
from item import Item       # Bind the uppercase class; no lowercase item binding.
thing = Item                # Alias this very class.
print(id(Item))             # Classroom: 1499181725904.
print(id(thing))            # Classroom: 1499181725904, the same live object.
fruit = Item("apple")       # Class call returns a distinct initialized instance.
print(id(fruit))            # Classroom: 1499185972624. New run, different numbers.
```

**Two separate line-2 failures.** After the same import, `print(id(item))` and `print(type(item))` each raised `NameError: name 'item' is not defined. Did you mean: 'Item'?`. Python must resolve the innermost name before either inspector or `print` can run. No identities or fruit printed in either run. `<module>` in a traceback identifies top-level execution. To inspect the module itself, a separate `import item` binds the lowercase name.

**Key Items to Scan**

- **Receiver** is the object before the dot. `fruit.append("banana")` passes apple as `self` and the string as `xobj`. Inside `Item(xobj)`, the initializer receives banana's **node** as `self` and banana's **string** as `obj`.
- `obj`, `nobj` and `xobj` were successive append parameter spellings. Change the use in `Item(...)` to match the local parameter. Initializer `obj` lives in another scope. The letter `n` has no logical-negation meaning.
- `ItemOne` illustrates Pascal case, `itemOne` camel case, and `item_one` snake case. The intermediate editor spelling `Item_one` was mixed. The naming edits showed style, not successful imports. Case-sensitive lookup is a language rule. Lowercase filenames and the instructor's camel-case method preference are conventions.

## 8. Save the returned node and walk the chain

**Classroom · working driver.** Save as `fruit.py` beside the restored class. This version omits the independent identity prints. The name `iter` shadows Python's built-in `iter`. Here it holds a node used as a manual cursor. No iterator protocol or hidden movement occurs.

```python
from item import Item             # Load the restored class.
fruit = Item("apple")             # fruit starts on apple.
fruit = fruit.append("banana")    # Use old fruit (apple) on the right.
                                  # Only after linking, rebind fruit to banana.
fruit = fruit.append("peach")     # Now banana receives the call. fruit ends on peach.
iter = fruit.prev.prev            # Read peach.prev -> banana; banana.prev -> apple.
print(iter.object)                # apple
iter = iter.next                  # Move the cursor from apple to banana.
print(iter.object)                # banana
iter = iter.next                  # Move from banana to peach; fruit still names peach.
print(iter.object)                # peach
iter = iter.prev                  # Move back from peach to banana.
print(iter.object)                # banana
iter = iter.prev                  # Move back from banana to apple.
print(iter.object)                # apple
```

**Link writes, in execution order.** A, B and P stand for the apple, banana and peach **nodes**. `None` marks an absent neighbor. A link already listed persists until another write targets that exact field.

| Operation completed | Receiver / changed field | `fruit` after operation | Other relevant state |
|---|---|---|---|
| `Item("apple")` assigned | A.object = apple | A | A.prev and A.next resolve to `None`. |
| `Item("banana")` inside append | B.object = banana | A | B.prev and B.next resolve to `None`. |
| `newItem.prev = self` | B.prev = A | A | A.next is still `None`. |
| `self.next = newItem` | A.next = B | A | Now A.next.prev is A. |
| `return newItem` | No link changes | A until assignment | Banana reference is the value of the call. |
| Driver assignment | No link changes | B | Local `newItem` can go out of scope. A.next still reaches B. |
| `Item("peach")` inside append | P.object = peach | B | Existing A–B links persist. |
| `newItem.prev = self` | P.prev = B | B | B.next is still `None`. |
| `self.next = newItem` | B.next = P | B | Both directions between B and P now work. |
| `return newItem` | No link changes | B until assignment | Return P to the waiting caller. |
| Driver assignment | No link changes | P | A.prev and P.next are both `None`. |

**Classroom extension · add these two lines after the working driver.** The extra move succeeds. The next argument evaluation prevents the sixth print.

```python
iter = iter.prev       # Classroom line 17: A.prev exists and yields None.
print(iter.object)     # Line 18: None has no object attribute. AttributeError.
```

| Cursor operation | Before | After | Print or boundary result |
|---|---|---|---|
| First hop in `fruit.prev.prev` | P | B (intermediate) | No print; read the second `prev`. |
| Second hop, bind `iter` | B | A | `apple` |
| `iter = iter.next` | A | B | `banana` |
| `iter = iter.next` | B | P | `peach` |
| `iter = iter.prev` | P | B | `banana` |
| `iter = iter.prev` | B | A | `apple` |
| Extra `iter = iter.prev` | A | `None` | Binding succeeds. |
| Evaluate `iter.object` | `None` | `None` | `AttributeError: 'NoneType' object has no attribute 'object'` |

**Key Items to Scan**

- The class method mutates two nodes. Only the driver's assignment rebinds `fruit`. Bare `fruit` on a script line would look up the node and discard it without an extra display.
- `print(iter.object)` reads the current payload. Only the explicit cursor assignments change where the next read starts.
- A link whose value is `None` can be read. Looking up a fruit field **on** `None` is the failure.

## 9. Make the error move by changing one input

**Classroom diagnostic · initializer disabled.** Reproduce in a separate practice folder. Use the complete `item.py` from section 7 and comment out exactly its two initializer lines, leaving all three class defaults and the entire `append` method active. Run each of the following drivers separately with that version.

```python
from item import Item          # This copy has no custom initializer.
fruit = Item("apple")          # Driver line 2: TypeError before fruit can be assigned.
```

```python
from item import Item          # Same disabled-initializer class, new process.
fruit = Item()                 # Line 2 succeeds. object resolves to the class default.
fruit = fruit.append("banana")  # Line 3 enters append. Item(xobj) fails inside it.
                                # Both link writes, return and rebinding are skipped.
```

| Same exception, different site | Last successful step | First failure | Inference |
|---|---|---|---|
| Driver line 2 | Import class | `Item("apple")` | Inherited construction accepts no payload. No node is assigned. |
| Driver line 3 → `item.py` line 10 | Construct the no-argument node and enter `append` | `newItem = Item(xobj)` with banana | `fruit` still names the first node. Its `next` remains `None`. |
| Restored initializer, saved | `Item("apple")` and both append calls | Only the optional extra boundary print fails | The valid five-word walk distinguishes construction from traversal. |

Both failed runs reported `TypeError: Item() takes no arguments` and printed no fruit. During the attempted boundary test, the editor showed an uncommented initializer while the copy on disk still had it disabled. Save **both** files, rerun the same command, then compare its new output with the intended test. A previous traceback can remain visible beside a repaired editor buffer.

**Key Items to Scan**

- **Locate the first operation that failed.** Driver line 3 is the caller. The innermost frame identifies construction at class-file line 10. Link assignments come later.
- **Go to Definition** located `append` at `item.py:9`. Find References identified a caller at `fruit.py:4`. These tools show code locations; a traceback reports the path of a particular run.
- Restore the initializer in the practice copy before exploring the missing banana.

## 10. Find the missing banana

**Classroom · revised driver setup.** Run this setup afresh before each of the three alternatives below. Use the restored `item.py` from section 7. The only change to construction is that neither append return is assigned.

```python
from item import Item          # Use the original attach-after-receiver method.
fruit = Item("apple")          # Keep apple as the driver's starting node.
fruit.append("banana")         # Set banana.prev and apple.next; discard returned banana.
fruit.append("peach")          # Apple receives this call too. Replace apple.next.
```

| Operation completed | `fruit` | Forward link from apple | What else changed? |
|---|---|---|---|
| Construct apple | apple | `None` | Apple has no predecessor. |
| Initialize banana | apple | `None` | New banana has no links yet. |
| Write banana.prev | apple | `None` | Banana points back to apple. |
| Write apple.next | apple | banana | Both directions now connect apple and banana. |
| Return banana | apple | banana | Caller discards it. Apple still reaches banana. |
| Initialize peach | apple | banana | New peach has no links yet. |
| Write peach.prev | apple | banana | Peach already points back to apple. Apple still points to banana. |
| Write apple.next | apple | peach | Replace the only path from apple to banana. Peach.next is `None`. |
| Return peach | apple | peach | Caller discards it. Apple and peach still reach each other. |

The banana call did execute. Commenting it out would skip construction and both initial link writes. The final forward path alone cannot distinguish those histories. Banana once pointed **to** apple; that does not create an arrow **from** apple back to banana. Reachability from retained nodes can be tested. The source provides no measurement of reclamation timing.

**Alternative A · original two-hop start.** The walker assignment itself fails. This run prints no fruit.

```python
iter = fruit.prev.prev    # Line 6: apple.prev yields None; None.prev raises.
print(iter.object)        # Never reached. There is no new cursor binding here.
```

**Alternative B · start on apple.** The second print provides evidence about the first link. The two backward moves still in the historical file are unreachable after line 11.

```python
iter = fruit              # Line 6: bind the cursor directly to apple.
print(iter.object)        # Line 7: apple
iter = iter.next          # Line 8: apple.next yields peach.
print(iter.object)        # Line 9: peach
iter = iter.next          # Line 10: peach.next yields None; assignment succeeds.
print(iter.object)        # Line 11: lookup fails before print can run.
```

**Alternative C · reverse the second move.** The student's proposed single change makes a different prediction possible. Does peach point back to banana or to the receiver that created peach?

```python
iter = fruit              # Start at apple again, using the fresh setup.
print(iter.object)        # apple
iter = iter.next          # Apple -> peach.
print(iter.object)        # peach
iter = iter.prev          # Changed line 10: peach.prev is apple.
print(iter.object)        # Line 11: apple
iter = iter.prev          # Line 13: apple.prev is None.
print(iter.object)        # Line 14: None has no object field.
```

| Driver | Actual output before error, in order | First failing lookup |
|---|---|---|
| A · two hops back | No fruit | Line 6, second `.prev`: `'NoneType' object has no attribute 'prev'` |
| B · twice forward | apple, peach | Line 11, `.object` on `None` |
| C · forward, then back | apple, peach, apple | Line 14, `.object` on `None` |

**Key Items to Scan**

- **Receiver** predicts the overwritten field. With saved returns, the second write targets `banana.next`. With bare calls it targets `apple.next` again.
- `return newItem` returns the node itself. An ID number would require an explicit `id(...)` call.
- A tail-only test can pass for a method that fails from the head. Call twice on the same head to distinguish attaching after the receiver from appending at the end.

## 11. Practice repair: find the endpoint before writing

The instructor proposed finding the last node before appending and assigned adding a node before the first. The supplied source shows only the unfinished text `def prepend` and later Git history. **The complete module and driver below are supplemental implementations.** They express those two behaviors for a finite, acyclic, nonempty chain of nodes. A single node is both endpoints. There is no separate empty-list container in this design.

**Practice module · `ends.py`.** Save the whole block in a new file, then put the following driver beside it. Using a separate module lets you rerun the original failure with `item.py`.

```python
class Item:                          # Practice version with endpoint-aware methods.
    def __init__(self, obj=None):    # Initialize one node; optional payload as in class.
        self.object = obj            # Store this node's value.
        self.prev = None             # New node begins without a predecessor.
        self.next = None             # New node begins without a successor.

    def last(self):                  # Start at the receiver, including an interior node.
        node = self                  # A local cursor leaves the caller's binding alone.
        while node.next is not None:  # Check the neighbor before stepping onto it.
            node = node.next         # Move forward one node per successful test.
        return node                  # Its next is None, so this is the tail itself.

    def first(self):                 # Mirror the search along predecessor links.
        node = self                  # Start with a known node.
        while node.prev is not None:  # A predecessor exists, so the move is safe.
            node = node.prev         # Walk one link toward the head.
        return node                  # No predecessor: return the actual head.

    def append(self, obj):           # Attach beyond the last reachable node.
        tail = self.last()           # Call last on this receiver and keep its result.
        new = Item(obj)              # Allocate a separate node for the new payload.
        new.prev = tail              # New node now points back; tail.next is still None.
        tail.next = new              # Finish the pair without replacing an occupied link.
        return new                   # Caller may retain this tail or discard the return.

    def prepend(self, obj):          # Attach before the first reachable node.
        head = self.first()          # Walk backward even if called on banana.
        new = Item(obj)              # This new node has no predecessor.
        new.next = head              # Establish new -> old head first.
        head.prev = new              # Establish old head -> new; other links persist.
        return new                   # Give the new head to the caller.
```

**Practice driver · `check_ends.py`.** Run `py check_ends.py`. `"pear"` is a teaching input. The first append starts on a singleton, the second starts on the same apple, and prepend starts in the middle. Identity checks test the actual links even if two payloads could look alike.

```python
from ends import Item                     # Select the practice implementation.
apple = Item("apple")                     # Both endpoint searches would return apple.
apple.append("banana")                    # Bare call: apple.next now reaches banana.
apple.append("peach")                     # Bare call: last() walks to banana first.
banana = apple.next                       # Save the middle node reached from apple.
pear = banana.prepend("pear")              # first() walks to apple; link pear before it.
print(pear.next is apple)                 # True: the new head leads to the old head.
print(apple.prev is pear)                 # True: the old head leads back to pear.
print(banana.next.prev is banana)         # True: peach's predecessor is this banana.
node = pear                               # Begin a guarded walk at the returned head.
while node is not None:                   # Skip the body on the first absent node.
    print(node.object)                    # Print each payload once while node is valid.
    node = node.next                      # Last move sets node to None; next test stops.
```

| Moment | Cursor or link before | Operation and result |
|---|---|---|
| Second append begins | Apple.next is banana | `last` starts on apple; guard succeeds. |
| Advance | Cursor is apple | Move to banana. Its `next` is `None`; stop and return banana. |
| Attach peach | Banana.next is `None` | Set peach.prev = banana. Banana.next is still `None`. |
| Finish append | Peach already points back | Set banana.next = peach. Apple.next still names banana. |
| Prepend from banana | Banana.prev is apple | `first` moves to apple; apple.prev is `None`, so return apple. |
| Connect pear forward | Pear has two absent links | Set pear.next = apple. Apple.prev is still `None`. |
| Finish prepend | Apple.prev is `None` | Set apple.prev = pear. Pear.prev and peach.next remain `None`. |
| Walk: pear | Cursor is pear | Print `pear`; advance to apple. |
| Walk: apple | Cursor is apple | Print `apple`; advance to banana. |
| Walk: banana | Cursor is banana | Print `banana`; advance to peach. |
| Walk: peach | Cursor is peach | Print `peach`; advance to `None`. |
| Terminate | Cursor is `None` | Guard is false; no fifth payload access. |

**Key Items to Scan**

- `while node.next is not None` stops **on** the tail. `while node is not None` processes each node, then stops **after** the tail. Those guards serve different purposes.
- A two-way link is complete only after both fields are set. Test both directions and both outer `None` boundaries.
- Endpoint search can require one move per intervening link. After finding the endpoint, attachment takes two link writes.

## 12. Match the result to the saved revision

A file saved in the editor can still differ from its Git revision. Before changing code to explain an unexpected output, match the driver, imported module, saved state and newest invocation. In class, `py` opened Python 3.14.7, and the interactive shell accepted bare `exit`. `exit()` is the call form. The subsequent `py .\test\fruit.py` belonged to PowerShell.

| Historical action | Result reported by the source | What it establishes |
|---|---|---|
| `git checkout master~17` | `fc6c333`, addition of append. `script/path.ps1` was marked modified. | The subsequent five fruit lines match a bidirectional walk. They do not identify the hidden early driver body. |
| `git checkout master~16` | Aborted because checkout would overwrite edits to `src/item.py` and `test/fruit.py`. | `~16` follows the first parent sixteen times from `master`. The attempted switch did not occur. |
| `git checkout master` | Also aborted for those files. | Saving to disk and preserving edits in a commit are separate operations. Git suggested commit or stash. |
| Restore, then revisit history | Restoration discussed; exact commands and intermediate responses unavailable. Later `git log --oneline` showed `843e915 (HEAD -> master) generator`. | The later log establishes the eventual branch. Restoring working files can discard edits. These are historical actions. |
| Read history chronologically | `fc6c333` adds append → `4847342` adds last → `6cdb33d` calls last from append. | Later history also names accessors, avoiding None, and first/prepend/insertBefore. Their implementations were outside this lesson. |

**Key Items to Scan**

- **Saved buffer**, **working tree**, **commit** and **HEAD** locate different states. Use a fresh command and its own output to diagnose a run.
- A definition popup locates code. A printed prefix proves which output calls completed. An aborted checkout supplies no evidence that another revision ran.

## Syntax reference and library of definitions

### Collections and observation

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `list()`, `[]` | Construct an ordered, editable sequence. | `b = []` | Two empty lists can have equal contents and different identities. |
| `tuple()`, `()` | Construct an empty immutable sequence. | `c = tuple()` | Fixed slots can still refer to mutable objects. |
| Comma in a tuple | Mark an item sequence, including a singleton. | `(3,)` | `(3)` just groups an integer. |
| `set()`, set display | Construct a collection of distinct hashable members. | `{2, 2, 4}` | `set()` makes the empty set; `{}` makes a dictionary. |
| `dict()`, `{key: value}` | Construct a mapping. A colon associates a key and value. | `{"red": "apple"}` | A comma separates entries. Keys are retained for lookup. |
| `type(x)` | Return the class of the supplied object. | `type(g)` | `NoneType` confirms that the name has a value. |
| `dir(x)` | Inspect available attribute names. | `dir(f)` | Discovering `add` does not call it. |
| `id(x)` | Return an integer identifying a live object. | `id(thing)` | Compare identities in one run; the digits are process-specific. |
| `is` | Test object identity. | `thing is Item` | Aliasing a class creates no second class. |
| `hash(x)` | Compute a hash for a hashable value. | `hash(-1)` | Collisions exist; unequal keys sharing a hash can coexist. |
| `add(x)` | Insert one hashable set member. | `f.add(6)` | Repeating 6 preserves the same membership. |
| `remove(x)` | Remove the named set member. | `g = f.remove(2)` | The mutation returns `None`. |
| `pop()` on a set | Remove and return an arbitrary member. | `h = f.pop()` | 4 was observed; selection order is unspecified. |
| `for ... in ...` | Bind a loop name to each successive value. | `for v in [2, 4, 6, 6]:` | Four calls receive integers rather than one list. |
| Index vs. key | Brackets dispatch to the receiving collection. | `j[2]` or `i[2]` | Select list position 2 or mapping key 2. |
| Subscript assignment | Add or replace a key's associated value. | `i[2] = 37` | The existing dictionary now exposes 37 through the same view. |
| `keys()` | Return a live view of mapping keys. | `i.keys()` | Keys here are `"one"` and `2`. |
| `values()` | Return a live view of mapping values. | `i.values()` | Values initially are `1` and `2`. |
| `items()` | Return a live view yielding key–value tuples. | `k = i.items()` | The value for key 2 changes in subsequent displays. |
| `list(view)` | Collect current elements into a list. | `snapshot = list(k)` | The earlier integer pair survives this later replacement. |
| `dict.fromkeys` | Build a new mapping from keys and a common value. | `dict.fromkeys(i, 0)` | Returns a new dictionary without changing `i`. |

### Calls, control flow, and text

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `def` and `(...)` | Bind a function now; call it later using parentheses. | `doNothing("fresh")` | Redefining a name changes which function later calls reach. |
| Parameter / argument | A local input name / the value supplied for one call. | `milk` receives `"fresh"` | Matching spellings across functions are unnecessary. |
| Default parameter | Supply a fallback when the caller omits an argument. | `flour=None` | An explicit empty string is still an argument. |
| `pass` | Execute a statement with no action. | Body of `doNothing()` | Reaching the end implicitly returns `None`. |
| `return` | End this call and supply one object to the waiting caller. | `return newItem` | A result can be saved, used immediately or discarded. |
| `print` | Display its evaluated arguments. | `print(iter.object)` | Argument evaluation may fail before the output call. |
| `f"...{expr}..."` | Evaluate and format each replacement field into one string. | `f"{doNothing()}"` | A call written only in literal label text does not execute. |
| `\n`, `\"` | Encode a newline or double-quote character in a string. | `"\"fresh\"\n"` | The print terminator adds another newline in the demo. |
| `if`, `else` | Select a suite from the condition. Nest to test a second condition only on one path. | `if milk == "fresh":` | Non-fresh milk skips the inner flour comparison. |
| `==` | Compare values for equality. | `json.loads(text) == m` | Separate dictionaries can have equal contents. |
| `!=` | Compare for inequality. | `flour != None` | The classroom comparison accepts an empty string as different from None. |
| Call stack / LIFO | Track unfinished calls and resume the most recent caller first. | Driver → append → initializer | A normal return resumes a caller. An unhandled exception unwinds frames. |
| Traceback | Report active frames leading to an exception. | `fruit.py:3` → `item.py:10` | The last frame locates failure; an earlier caller may have supplied the input. |
| `import` | Bind a module name for access to its members. | `import json` | The module and decoded data have different types. |
| `from ... import ...` | Bind the requested name from a module. | `from item import Item` | This import alone leaves lowercase `item` unbound. |
| JSON object / array | Represent named members / ordered elements in interchange text. | `{"red": "apple"}` | JSON requires double quotes around names and string values. |
| `json.dumps` | Serialize supported Python data to a string. | `json.dumps(m)` | The original mapping remains usable. |
| `json.loads` | Decode JSON text to a Python value. | `json.loads(text)` | A JSON object decodes to a dictionary. |
| `SyntaxError` | Input cannot be parsed as Python. | Conflicting outer quotes | No call reaches the JSON decoder. |
| `NameError` | Lookup cannot find a required binding. | `id(item)` | `id` never receives an argument. |
| `TypeError` | Operation or call cannot accept the supplied object or arguments. | `j["2"]` | The syntax is valid; the list rejects the index type. |
| `AttributeError` | Requested attribute is missing on this receiver. | `None.prev` | Trace the previous move that produced `None`. |
| `json.JSONDecodeError` | The decoder received text that violates JSON syntax. | Single-quoted JSON keys | Python already built a valid string and called the decoder. |

### Nodes, traversal, and revisions

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `class` / instance | Define a type / create an individual object of that type. | `Item("apple")` | `thing = Item` holds the class itself. |
| `__new__` | Obtain the instance during construction. | Class call `Item(xobj)` | The classroom class used inherited creation behavior. |
| `__init__` | Initialize the newly created instance. | `self.object = obj` | It must return `None`; the class call yields the node. |
| `self` / bound method | Receive the instance through which the method is called. | `fruit.append("peach")` | The receiver may be banana or apple depending on rebinding. |
| Class default / instance field | A lookup may fall back to the class; a write through an instance belongs to it. | `next = None`; `self.next = newItem` | Changing apple's forward link leaves banana's fields alone. |
| `.object` | The node's stored payload. | `iter.object` | Reads a fruit string and leaves the cursor in place. |
| `.next` / `.prev` | References to successor / predecessor nodes. | `fruit.prev.prev` | Evaluate left to right; each next lookup needs a valid owner. |
| Assignment / rebinding | Evaluate the right side before assigning the name. | `fruit = fruit.append("banana")` | The old receiver is used for the call; the new binding follows the return. |
| Mutation | Change a field of a live object. | `self.next = newItem` | The change survives discarding the result. |
| Reachability | Access an object by a retained name or path. | `apple.next` | Losing a local name can leave another path intact. |
| `None` / endpoint | Use a distinguished value for an absent neighbor. | `peach.next` at the tail | Assigning that value succeeds; retrieving its fruit fails. |
| `while` | Re-evaluate a guard before each loop body. | `while node is not None:` | A guarded walk stops before reading a missing payload. |
| `is not None` | Test identity against the sentinel. | `node.next is not None` | This guard stops the endpoint search on the last node. |
| `first()` / `last()` | Practice searches for the head or tail from the receiver. | `tail = self.last()` | A singleton is both. No link is changed by the search. |
| `prepend` / `append` contract | Insert before the head / after the tail. | `banana.prepend("pear")` | The original append only achieved its promise on a tail receiver. |
| `#` | Ignore the rest of the physical line as a comment outside a string. | Disabled initializer | A commented-out call never runs; ignoring a return still executes the call. |
| `master~n` / `HEAD` | Follow the first parent n times / identify the checked-out state. | `master~17`, `HEAD -> master` | A refused checkout leaves the previous state in place. |

## Knowledge check

1. Predict the types of `(3)`, `(3,)`, `{}` and `set()`. Which can represent an empty collection of distinct members?
2. After `g = f.remove(2)` the REPL echoes nothing for `g`. How could you show both the mutation and the return? What could you predict about the next pop?
3. Compare `doNothing("fresh", None)` across the two flour revisions. Which edit explains the difference? If the inner call raises, does the outer print display its label?
4. Why does `k` now show `(2, 37)` while `snapshot` still shows `(2, 2)`? Which operation created a second outer collection?
5. What has already succeeded when `json.loads` raises `JSONDecodeError`? How does that differ from the classroom quote failure?
6. After two bare append calls on apple, which node receives the second call, which link is overwritten and where does peach's predecessor point?
7. The extended working driver prints five fruit names and fails at line 18. Which prior line succeeded, and what value did it produce? Compare with the driver failing at line 6.
8. A saved driver fails at `Item(xobj)`. What must already have executed? What must you check before editing the argument list?
9. Which pair of calls tests the proposed tail search more effectively than always saving its return? How would you check the reverse link after prepend?

### Answer key

1. `int`, `tuple`, `dict` and `set`, respectively. Use `set()` for the empty set. A trailing comma creates a one-entry tuple.
2. Inspect `f` for members 4 and 6 and use `print(g)` for `None`. The next pop removes and returns either remaining member. The classroom observed 4.
3. Required flour returns `"fresh"`; optional flour returns `"spoiled"`. Supplying explicit `None` isolates the changed inner `else`. The default affects omission. If the inner call raises, the f-string never finishes and the outer print never runs.
4. `k` remains a view of `i`. `list(k)` collected the then-current tuples before the replacement. Neither reading `k` nor replacing a value edits the earlier printed text.
5. Python successfully parsed the program, constructed the argument string and called the decoder. In the classroom attempt, Python could not parse the call at all.
6. Apple receives both calls. The peach call replaces `apple.next` and writes `peach.prev = apple`. From the retained apple and peach there is no path to banana.
7. Line 17 read `apple.prev` and bound `iter` to `None`. Line 18 failed reading `.object`. In the two-hop failure, the second `.prev` on line 6 fails before `iter` is assigned and before any fruit prints.
8. The no-argument first construction and entry into `append` succeeded. Its nested construction fails before either link write. Verify the imported class, save state of both files and newest command output. Restore the accepting initializer for the working run.
9. Call `apple.append("banana")` and `apple.append("peach")` on the same receiver. The repaired method preserves `apple.next.object == "banana"`. After prepending pear, verify both `pear.next is apple` and `apple.prev is pear`.

## Technical references

- Python collection behavior: [set operations](https://docs.python.org/3/library/stdtypes.html#types-set) and [dictionary view objects](https://docs.python.org/3/library/stdtypes.html#dictionary-view-objects).
- Python execution and object behavior: [formatted string literals](https://docs.python.org/3/reference/lexical_analysis.html#f-strings), [instance initialization](https://docs.python.org/3/reference/datamodel.html#object.__init__) and [reading exceptions](https://docs.python.org/3/tutorial/errors.html#exceptions).
- Python's JSON module: [decoding strings](https://docs.python.org/3/library/json.html#json.loads) and [encoding strings](https://docs.python.org/3/library/json.html#json.dumps).
- Git: [checkout and local modifications](https://git-scm.com/docs/git-checkout) and [ancestry notation](https://git-scm.com/docs/gitrevisions).

**Source limits.** Classroom attribution throughout comes from the supplied Session 17 Markdown. The clipped final milk driver, unseen exact Git restore commands and unfinished prepend body cannot be recovered from it. The function listings reproduce editor behavior; their outputs are independently tested. The endpoint repair is a practice implementation.
