# One class, two instances

**CSCS1240F26 · Session 13 · Class and instance identity and initialization**

What should Python display after `a.value = 12` if `b` was created from the same class? It depends on who owns `value`. Follow three objects through this lesson: the class `Item`, the first instance bound to `a`, and the second bound to `b`. At each print, identify the object, the operation and the source of the result.

**Running the examples.** Each Python block is a separate program unless labeled as a continuation. Save a block in a fresh practice file named `demo.py` and run `py .\demo.py` in PowerShell from that folder. No third-party imports are needed. Classroom observations below come from the supplied Markdown. Comments are added for study. A **practice** label identifies a guide-added experiment. Concrete classroom IDs belong to their individual runs.

## 1. Give the number its question back

The first explicit Python launch printed `2534258504736`. The earlier direct invocation `.\demo.py` showed no text before the next prompt. In the numbered run only `print(id(Item))` was active. The class owned `7`, but Python had been asked to display the class object's identity. The instructor's mnemonic, *information = data + meaning*, was text in a scratch area. The expression that produced the digits supplies their meaning.

**Classroom four-probe revision.** Identify four results even when the first wraps across several terminal rows.

```python
class Item:            # Define a type named Item. The colon opens its body.
    value = 7          # Executing this body binds value to 7 on the class.

print(dir(Item))       # Return a list of names, then display that one list.
print(type(Item))      # Ask what kind of object Item is: <class 'type'>.
print(Item.value)      # Read the value owned by Item; display 7.
print(id(Item))        # Get the live class's integer ID, then print it.
```

| Expression inside print | Classroom result in the four-probe run | What to inspect |
|---|---|---|
| `dir(Item)` | One list ending in `'value'` | Entries are names. Among the supplied names were `__new__` and `__init__`. The exact directory varies with Python version. |
| `type(Item)` | `<class 'type'>` | Defining a class makes a new type. That class is also an object Python can inspect. |
| `Item.value` | `7` | The dot selects an attribute on the object to its left. |
| `id(Item)` | `2369932880832` | This is a separate launch from the opening one. An ID is stable during the object's lifetime. |

**A diagnostic habit from the opening discussion.** In the login/register project, the student had installed `rsa`, while the intended `rsa.generate_private_key(...)` belonged to an RSA module in `cryptography`. Check the installed package and the imported object before debugging the call. The class discussion supplied no installation or tested fix. A second question concerned `return_value = function(args)`. Naming a returned value lets you inspect or reuse it. Passing it directly to another call also works. Calling a helper from a larger function can avoid copying its implementation into several places.

**Key Items to Scan**

- `class Item:` defines a type. The capital letter is a naming convention. The keyword and class body determine what Python creates.
- `id(Item)`, `type(Item)` and `Item.value` ask three different questions of the same object.
- `print(...)` displays its argument and returns `None`. Obtaining a value and displaying it are separate operations.

## 2. Find the first operation that cannot run

The instructor used `cd C:\CSCS1240F26\` and `ls` to locate `demo.py`, then ran the saved file explicitly. A change in the editor and a traceback beside it can belong to different revisions. Read the latest command, the quoted failing line and the output *after that command*. Change one thing, save, and run again.

**Classroom indentation failure.** Line 5 is valid as an assignment on its own. The parser flags it because the class header on line 1 still needs a body.

```python
class Item:            # Start a class whose next statement must be indented.



value = 7              # Line 5 starts at the left margin. Parsing fails here.
```

Restoring the indented `value = 7` in section 1 repaired the definition. The next identity-only run printed `1713038239648`. Removing a blank line just moved the diagnostic. A dedent ends a multiline body in Python, where C, C++ or Java commonly use braces. The same header-and-suite pattern occurs with `if`, `for` and `def`.

**Practice · two legal temporary bodies.** Run this as one program. The two classes have different names so both alternatives remain available for inspection.

```python
class WithPass:        # First unfinished class, with no application data yet.
    pass               # Explicitly do nothing; this counts as a statement.

class WithEllipsis:    # Second unfinished class accepts an expression as its body.
    ...                # Evaluate Ellipsis and discard it; no value field is set.

print("value" in dir(WithPass))      # Search the name list; display False.
print("value" in dir(WithEllipsis))  # Same membership check; display False.
```

**Classroom rename failure.** Start this as a new script. Compare the spelling at declaration and use.

```python
class Item:            # A fresh definition makes a fresh class for this run.
    notvalue = 7       # Install the name notvalue on Item.

print(dir(Item))       # The list ends with 'notvalue'.
print(type(Item))      # This still displays <class 'type'>.
print(Item.value)      # Line 6: lookup fails before print receives an argument.
print(id(Item))        # Unreached because the preceding lookup raised.
```

| Revision in the source | First result affected | Remaining execution |
|---|---|---|
| Class has `notvalue = 7`, driver asks for `Item.value` | `AttributeError: type object 'Item' has no attribute 'value'` on line 6 | The list and type have already printed. No new 7 or final ID. |
| Restore `value = 7` and rerun | `Item.value` prints `7` | Final class ID in this repaired run was `2788779862080`. |
| Change only `7` to `11` and rerun | The list still contains `'value'`. The same lookup now prints `11`. | Final ID was `1510962937584`. The controlled edit identifies the source of the printed value. |

**Practice · move the assignment outside a valid class.** Predict the last successful print.

```python
class Item:            # Give Item a syntactically complete body.
    pass               # This class body creates no attribute called value.

value = 7              # Dedented: bind value in the module's namespace.
print(value)           # The module-level lookup succeeds and prints 7.
print(Item.value)      # Class lookup raises AttributeError at runtime.
```

**Key Items to Scan**

- `IndentationError` is a syntax error. Python parses the file before executing even an earlier print.
- `pass` and `...` both satisfy an unfinished suite. A blank line or a line comment cannot supply the required statement.
- `AttributeError` here identifies a failed lookup at runtime. Output from earlier successful calls remains visible.

## 3. Identify the class and its instances in one run

Read `a = Item()` right to left for the assignment: resolve the class, call it with no explicit arguments, then bind `a` to the returned instance. The parentheses signal a call. Their presence alone does not tell you whether the callable is a class, function or method. `b = Item()` makes another instance. Neither assignment prints.

**Classroom identity probes, consolidated as a runnable file.** The four output lines immediately below are from the same 48:15 run in the source.

```python
class Item:                # Define the class that both instances will share.
    value = 11             # This run starts with a class-level value of 11.

print(hex(id(Item)))       # id gets the class ID; hex formats it; print displays it.
a = Item()                 # Construct one Item and bind its reference to a.
print(a)                   # Default display includes the module and class name.
print(hex(id(a)))          # Format a's integer ID as lowercase hexadecimal text.
b = Item()                 # A second class call produces a different live Item.
print(hex(id(b)))          # Ask about b when reusing the expression written for a.
```

```text
0x23ca394c7f0
<__main__.Item object at 0x0000023CA3EA8590>
0x23ca3ea8590
0x23ca3e98550
```

The first line identifies the class. Lines two and three describe **one** instance; their hexadecimal digits agree after removing padding and normalizing case. The fourth identifies the other instance. `__main__` is the module running as the script. The address-like default representation shown in this interpreter is an implementation detail. Python guarantees the lifetime identity relationship. Allocation and garbage collection can allow reuse after an object's lifetime.

| Earlier revision, one launch per row | Output to compare | What changed |
|---|---|---|
| Print `id(Item)` then `id(a)` | `1434179590848` then `1434183959952` | Distinct live objects even though both results are decimal integers. |
| Add `print(a)` before `print(id(a))` | Class `1377438455616`; instance field `0x00000140B5FE86E0`; decimal `1377442891488` | The field and the final integer represent the same number. |
| Format only the class ID in hex | Class `0x13f95960380`; instance field `0x0000013F95D386E0`; decimal `1372608235232` | Formatting the class tells you nothing about the instance ID's display base. |
| Format the instance ID too | Class `0x29833fada80`; instance field `0x0000029834448590`; final `0x29834448590` | Compare the last two directly. The earlier run's numbers do not belong here. |
| Activate a second instance, initially print `id(b)` | Last line `1713454875984` | The four-line example above changes this probe to hex in a fresh run. |

**How the instructor isolated these probes.** A `#` disables the rest of a source line. A matched pair of `'''` delimiters makes the intervening source a multiline string. The apparent calls and scratch arithmetic inside that string are just characters. The source records a lone one-number run (`2604214551952`), then a separate failed launch. An edit visible beside the failure does not recover the entire failed script.

| Failed classroom revision | Why execution stopped | Repair |
|---|---|---|
| `'''` on line 33, detected at line 35 | `SyntaxError: unterminated triple-quoted string literal`. Even earlier prints are skipped. | Pair the intended opening and closing delimiters. Check which driver statements are now outside them. |
| `print(hex(id(Item))` on line 8 | `SyntaxError: '(' was never closed`. Three calls opened, two closed. | `print(hex(id(Item)))` closes `id`, `hex` and `print` in order. |
| `print(hex(id(a))` on line 12 of a later edit | Same parser error, this time while formatting the instance. | `print(hex(id(a)))`. Save and rerun before interpreting the old scrollback. |

**Key Items to Scan**

- `Item` names a class object. `Item()` calls it. `id(a)` returns an integer, while `hex(id(a))` returns text.
- Identify the argument before comparing the digits. Cross-run values, ID ordering and spacing do not identify relationships.
- In a copied probe for a second instance, check the name inside `id(...)` as well as the surrounding format.


## 4. Decode the hexadecimal field four bits at a time

The class returned to the earlier field `0000013F95D386E0`. Count its 16 hex digits, including padding and excluding `0x`. Four bits have 2 × 2 × 2 × 2 = 16 possible settings. Each hex digit therefore encodes four bits exactly, and this field displays 16 × 4 = **64 bits**. Width describes the printed field. It does not measure the size of the `Item` or establish a physical RAM location. Decimal lacks this direct grouping because ten is not a power of two.

| Hex digit | Decimal | Four bits | Reading the weights 8, 4, 2, 1 |
|---|---:|---|---|
| `1` | 1 | `0001` | Use the ones place. |
| `3` | 3 | `0011` | Use 2 + 1. |
| `A` | 10 | `1010` | Use 8 + 2. Counting starts at zero, so A is the eleventh symbol. |
| `B` | 11 | `1011` | Use 8 + 2 + 1. |
| `C` | 12 | `1100` | Use 8 + 4. |
| `D` | 13 | `1101` | Use 8 + 4 + 1. |
| `E` | 14 | `1110` | Use 8 + 4 + 2. |
| `F` | 15 | `1111` | Use every place. |

**Practice · verify the classroom digits in both directions.** `int(text, base)` parses the supplied text using its base. `hex` and `bin` go the other way, formatting an integer as text. The spoken “dec” supplies no Python built-in; printing an integer already uses decimal by default.

```python
field = "0000013F95D386E0"   # Save the padded field from the mixed-base run.
number = int(field, 16)      # Parse the hex digits; the result is an integer.
print(number)                # Decimal output: 1372608235232.
print(hex(number))           # Same quantity, formatted as 0x13f95d386e0.
print(bin(15))               # Format 15 in base two; display 0b1111.
spoken = int("13F", 16)      # Parse the three hex digits the class discussed.
print(spoken)                # Display 319, or 1*256 + 3*16 + 15.
typed = int("0001001100011111", 2)  # Parse the completed scratch line as binary.
print(hex(typed))            # Display 0x131f; the scratchpad has an extra 0001.
```

The spoken `13F` is `0001 0011 1111`. The editor scratchpad ended as `0001 0011 0001 1111`, which is `131F`. Preserving four positions per digit exposes the extra group. Neither sequence was a printed classroom result.

**Key Items to Scan**

- `0x` marks hex and `0b` marks binary. Neither prefix counts toward field width.
- Leading zero padding preserves a field's width. A zero *inside* a sequence can change the value.
- `hex(15)` returns `'0xf'`. `int("F", 16)` returns the integer `15`. Identify the conversion direction before predicting the type.

## 5. Trace who owns the value

The class stores one plain integer attribute. A new `Item` can read that attribute without receiving its own copy. Assigning through `a.value` gives that particular instance its own entry. The term **shadowing** explains why later reads through `a` and `b` can differ. The instructor's “static property” refers to the shared class attribute in this example. There is no Python `property` declaration here.

**Classroom comparison and type probes.** Run as one file. The opening file had a default of `7` and would produce `7, 7, 12, 11` for the four numeric reads. The source independently reconstructs that opening file; the classroom run established for the following revision starts at `11`.

```python
class Item:                # Both items use this one class in the current run.
    value = 11             # Bind value in the class namespace.

a = Item()                 # Create the first instance; no own value yet.
b = Item()                 # Create the second instance; no own value yet.
print(a.value)             # Fall back to Item.value: 11.
print(b.value)             # A distinct instance finds the same class value: 11.
a.value = 12               # Set an attribute on a; leave the class and b alone.
Item.value = 11            # Write on the class. Its stored number remains 11.
print(a.value)             # a's nearer binding now supplies 12.
print(b.value)             # b still consults the class and supplies 11.
print(type(a))             # Display the class object: <class '__main__.Item'>.
print(type(a).value)       # type(a) yields Item; read Item.value; display 11.
print(type(dir))           # Inspect the function itself: builtin_function_or_method.
print(type(id))            # id is another built-in function of the same type.
print(type(type))          # The type of type is type: display <class 'type'>.
```

| Operation just completed | `Item.value` | `a`'s own field | `b`'s own field | Result or next read |
|---|---:|---|---|---|
| Class body finishes | 11 | No instance | No instance | The class now owns the default. |
| `a = Item()` | 11 | Absent | No instance | `a` can read 11 from the class. |
| `b = Item()` | 11 | Absent | Absent | `b` can also read 11. |
| `print(a.value)` | 11 | Absent | Absent | Displays 11. |
| `print(b.value)` | 11 | Absent | Absent | Displays 11. |
| `a.value = 12` | 11 | 12 | Absent | `a` is the same item with new state. |
| `Item.value = 11` | 11 | 12 | Absent | Class write leaves the override in place. |
| `print(a.value)` | 11 | 12 | Absent | Displays 12. |
| `print(b.value)` | 11 | 12 | Absent | Displays 11. |
| `print(type(a).value)` | 11 | 12 | Absent | Starting at `Item` bypasses `a`'s 12. |

**Reading the output.** A completed classroom comparison printed `11, 11, 12, 11` on separate lines. A later run added the class display, `11`, then `<class 'builtin_function_or_method'>` twice and `<class 'type'>`. The earlier terminal ending in two elevens established the starting reads only. An assignment is silent, so missing output alone cannot prove a write occurred.

**Practice · give the class a distinguishable new value.** Append this block to the preceding one. The value `15` is a transfer case from the source's answer key. The alias is an added test to distinguish rebinding a name from modifying an existing item.

```python
alias = a                  # A second name now refers to the existing first item.
print(alias is a)          # Identity comparison: both names reach one item. True.
print(a is b)              # Two separate calls produced two items. False.
Item.value = 15            # Replace the class default; b will now find 15.
print(alias.value)         # The original item's own 12 still wins. Displays 12.
print(b.value)             # No own field: read the revised class default, 15.
alias = b                  # Rebind this name only; neither item changes.
print(alias is b)          # The same-object test now displays True.
print(a.value)             # The original first item still holds 12.
```

**Key Items to Scan**

- Read the left side of the dot on **writes** to identify the owner. On **reads**, check where that owner can find the name.
- `type(a).value` resolves `type(a)` before selecting `value`. `type(dir)` passes a function object without running `dir()`.
- `alias = a` creates another reference. `a.value = 12` changes an item's state. Neither expression constructs an item.

## 6. Construct an item that remembers its argument

The closing revision **replaces** the class-body `value = 11` with an initializer. The public call changes from `Item()` to `Item("abc")`. In this ordinary class call, inherited `__new__` creates an instance; the class-call machinery then invokes `__init__` with that instance. The initializer configures it and finishes with `None`. The **class call** yields the initialized item to its caller. The earlier directory listed both special methods before the class supplied its own initializer. Python needs no separate `new` keyword.

**Classroom failure · argument matching.** The first draft had an unfinished `self.value =` in the editor, with no documented run. The following completed version did run.

```python
class Item:                       # This revision has no class default for value.
    def __init__(self, value):    # Python supplies self; the caller supplies value.
        self.value = value        # Would bind the argument to this instance.

a = Item()                        # Line 5: missing value, so a receives no result.
print(a.value)                    # Unreached. The body above also never ran.
```

The reported error was `TypeError: Item.__init__() missing 1 required positional argument: 'value'`. The class call has begun, but matching arguments fails before entry into the initializer body. Two parameter names and one implicitly supplied receiver leave **one** required argument for the caller.

**Classroom repair · conventional receiver.** Run this revised program on its own.

```python
class Item:                       # Define a type whose instances store a value.
    def __init__(self, value):    # Bind the new item to self and "abc" to value.
        self.value = value        # Read the local name on the right.
                                  # Store that reference on the item on the left.

a = Item("abc")                   # Call the class; bind a after initialization.
print(a.value)                    # Read the saved string; display abc.
```

**Classroom experiment · rename the receiver.** A student asked whether the first parameter must be called `self`. The instructor changed both occurrences in a separate run. Both successful versions printed lowercase `abc`, without quotes.

```python
class Item:                       # A fresh run begins with this class definition.
    def __init__(me, value):      # The first parameter still receives the new item.
        me.value = value          # Access that same receiver under its new name.

a = Item("abc")                   # The caller still supplies just the string.
print(a.value)                    # Follow a to its value field and print abc.
```

| Operation in `a = Item("abc")` and the following print | State immediately afterward |
|---|---|
| Evaluate the callable and `"abc"` argument | The class and a string reference are available. Evaluating a literal need not create a fresh string each time. |
| Inherited `__new__` supplies the new item | There is a new `Item` distinct from the string. The assignment to `a` is still pending. |
| Enter `__init__(me, value)` | Local `me` refers to the new item. Local `value` refers to the supplied string. |
| `me.value = value` | The item now also refers to that same string through its own attribute. No copy is required. |
| Reach the end of `__init__` | The initializer returns `None`. The item's field outlasts the local parameter. |
| The class call completes | Its result is the new item. |
| Bind `a` to that result | The caller can reach the item as `a` and the string as `a.value`. |
| `print(a.value)` | The lookup yields the saved string; the outer call displays `abc` and returns `None`. |

**Key Items to Scan**

- `self` is the conventional receiver name for ordinary instance methods. Its position gives it its role here. `me` works when the body uses the same name.
- `value` is local to the initializer. `me.value` belongs to the instance. Matching spellings do not imply shared storage.
- To predict a failure, ask whether the right-hand call completed. A failed call cannot bind its result to `a`.


## 7. Syntax reference and library of definitions

### Names, calls and class bodies

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| Data and information | Interpret an output by recovering the question the program asked. | `2534258504736` | The producing probe identifies these digits as a class ID. |
| `class` | Define a type and bind its name after its body executes. | `class Item:` | `Item` is itself an object that can be inspected or called. |
| `:` and indentation | Open a suite and group its statements by indentation. | `value = 7` inside `Item` | A dedented assignment at module level binds a different name. |
| `pass` | Supply a no-operation statement. | `class WithPass:` with `pass` | Satisfies the suite requirement without setting a field. |
| `...` | An expression denoting the Ellipsis object. | Body of `WithEllipsis` | Its unused value makes a legal placeholder. |
| `#` | Begin a comment outside a string. | `#print(dir(Item))` | The disabled probe produces no output. |
| `'''...'''` | Delimit a multiline string literal. | Quoted-out driver | Calls inside become text. An unpaired delimiter prevents parsing. |
| `=` and binding | Associate a name or attribute with the right-hand result. | `alias = a` | Another name can reach the same object without copying it. |
| `()` and callable | Apply a callable to its arguments. | `Item()` | Empty parentheses supply zero explicit arguments. The class call produces an instance. |
| Function and method | A function groups reusable behavior. A method can bind a function to its receiving instance. | `id(a)`; `__init__` | Looking up a function by name can pass the function itself as data. |
| `def` | Define a function with a parameter list and a body. | `def __init__(me, value):` | Defining the method does not execute its body. |
| Parameter and argument | A parameter is a local name in a definition. An argument supplies an object to a call. | `value` receives `"abc"` | Required arguments must be supplied before the body can run. |
| `print(...)` | Display values on the output stream. | `print(a.value)` | The lookup must succeed first. The print call itself returns `None`. |
| `None` | Python's object for absence of a value. | End of `__init__` | The initializer's implicit return differs from the instance yielded by the class call. |

### Inspection, identity and number representation

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `dir(...)` | Obtain a list of attribute names for inspection. | `dir(Item)` | The presence of `'value'` tells you a name, not the number it holds. |
| `in` | Test membership in a collection. | `"value" in dir(WithPass)` | Build the name list first, then test for the string. Result is `False` here. |
| `type(...)` | Return the type object of an argument. | `type(a)`; `type(Item)` | The first yields `Item`. The second yields `type`. |
| `id(...)` | Return an integer identifying one live object. | `id(a)` | Stable for its lifetime. Other objects can reuse an ID after that lifetime. |
| `is` | Test whether two references designate the same object. | `alias is a` | Yields `True` or `False` irrespective of the objects' attribute values. |
| `__main__` | Module name for the script being run directly. | `__main__.Item` | The default display identifies the class in its module. Its exact formatting can vary. |
| Hex digit and bit | One base-16 position encodes four binary positions. | `F` ↔ `1111` | Count leading padding when measuring field width. |
| `hex(...)` | Format an integer as base-16 text with `0x`. | `hex(15)` | Returns `'0xf'`. The integer's value is unaltered. |
| `bin(...)` | Format an integer as base-two text with `0b`. | `bin(15)` | Returns `'0b1111'`, with no fixed-width padding. |
| `int(text, base)` | Parse digits using a specified base and return an integer. | `int("13F", 16)` | Produces decimal `319`; `int("0011", 2)` produces `3`. |
| Nested calls | Evaluate each required argument before its receiving call. | `print(hex(id(a)))` | The intermediate values are an ID integer and a hex string. The final action displays the string. |

### Ownership, construction and errors

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `.` and attribute | Select a named member using the object on the left. | `Item.value` | A bare `value` in a method uses name resolution instead. |
| Class attribute | Binding made on the class, available to these plain instances through lookup. | `Item.value = 11` | A write through the class changes the fallback for instances without an override. |
| Instance attribute and shadowing | A field on one item can take precedence over a plain class attribute of the same name. | `a.value = 12` | `b` continues to read the class value. Reassigning a field does not alter the item's identity. |
| `type(a).value` | Obtain the instance's class and then read the class field. | `type(a).value` yields `11` | This read begins at the class even when `a.value` is `12`. |
| Instantiation | Create an instance by calling an ordinary class. | `a = Item("abc")` | Assignment gets the constructed item after initialization succeeds. |
| `__new__` | Supply the new object during ordinary construction. | Inherited by `Item` | A distinct instance exists before its initializer body begins. |
| `__init__` | Initialize the already-created instance. | `def __init__(self, value):` | Sets state and must return `None`. Returning the instance from this method would be an error. |
| `self` and receiver | Conventional first parameter naming the instance being acted on. | `me.value = value` | `me` demonstrates that the role survives a consistent rename. |
| `self.value = value` | Read the local parameter and bind its reference to an instance field. | Store `"abc"` | The caller can retrieve the string after the initializer returns. |
| `SyntaxError` | A source form cannot be parsed. | Unclosed `(` or `'''` | The script cannot begin executing. |
| `IndentationError` | A syntax error in block structure. | Empty class suite | Look back from the flagged line to the header requiring a body. |
| `AttributeError` | A requested attribute lookup fails. | `Item.value` after rename | Earlier prints can succeed before this runtime exception. |
| `TypeError` | An operation or call violates its required interface. | `Item()` with required `value` | Argument matching fails before the initializer body runs. |
| Traceback | Locate the runtime call or lookup that failed. | `demo.py` line 5 in the closing failure | Match it to the saved revision. A parse diagnostic locates invalid source before execution. |

## 8. Knowledge check

1. The screen shows `7` and then a large integer. Which two probes distinguish the class's data from the class's identity? Why can the first stay the same across launches?
2. Imagine a valid `pass` body with `value = 7` at the left margin below it. What does `print(value)` do? What happens at `print(Item.value)`? How does that differ from the empty-suite failure?
3. In the 48:15 run, which two lines refer to the same instance? What would go wrong if you compared the decimal `b` ID from the previous launch to the final hex line?
4. Convert `13F` to groups of four bits. What extra group would change the result to `131F`? How many bits wide is a field of 16 hex digits?
5. With `a.value = 12`, a class default of `11` and no own field on `b`, predict `a.value`, `b.value` and `type(a).value` after `Item.value = 15`.
6. The final initializer has two parameters. Why does `Item()` report just one missing argument? What are the initializer's return and the class call's result in the successful version?
7. Why can `print(a.value)` display `abc` after `me` and `value` have gone out of scope? What does consistently changing `me` back to `self` change for the caller?

### Answer key

1. `print(Item.value)` reads the saved class attribute. `print(id(Item))` displays the current class object's identity. Executing the same class-body assignment in another process installs the same numeric value on a fresh class.
2. The first print displays `7` from module scope. The second raises `AttributeError` because the valid class body never installed `Item.value`. With no indented body, parsing raises `IndentationError` and neither print runs.
3. Lines two and three describe `a`. Compare the hex digits after accounting for padding and case. A new invocation created different objects, so converting a prior ID's base cannot identify the new `b`.
4. `0001 0011 1111`. Insert `0001` just before `1111` to obtain `131F`. Sixteen hex digits occupy 64 bit positions.
5. `12, 15, 15`. The first lookup finds the instance field, the second falls back to the class, and the last reads the class directly.
6. Python supplies the new receiver. The omitted argument is `value`. On success `__init__` returns `None`, the class call yields the initialized item, and the assignment binds `a` to it.
7. The instance field still refers to the string, and `a` refers to the instance. Renaming the local receiver consistently preserves the call syntax and the displayed result.

### Official Python references

- How class definitions create types, how methods bind, and how instances access class data. [Classes — class and instance variables](https://docs.python.org/3/tutorial/classes.html#class-and-instance-variables)
- Object identity and the order of creation and initialization. [Data model — objects, values and types](https://docs.python.org/3/reference/datamodel.html#objects-values-and-types) · [Object creation and initialization](https://docs.python.org/3/reference/datamodel.html#object.__new__)
- Signatures and return types for the inspection and conversion functions. [Built-in functions](https://docs.python.org/3/builtins/functions.html#id)
- Reading parse diagnostics and runtime failures. [Errors and exceptions](https://docs.python.org/3/tutorial/errors.html#syntax-errors)

**Evidence limit.** Classroom code and observations are drawn solely from the supplied Session 13 Markdown. Its appendices include partially obscured edits and older console output. Consolidated listings omit inactive regions; standalone practice examples and their results describe reproducible Python behavior. A rerun can verify that behavior without recovering a hidden edit or reproducing a classroom allocation.
