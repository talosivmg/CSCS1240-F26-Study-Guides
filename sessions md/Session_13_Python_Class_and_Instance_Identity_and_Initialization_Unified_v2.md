# One class, two objects: following `Item` through a Python run

**CSCS1240F26 · Session 13 · Worked teaching companion · Unified v2**

When the class first runs the script, it gets a number. The useful question is not “What number should I memorize?” but “Which object did the program ask about?” Keep that question in mind when the instructor adds parentheses to **`Item`**, puts a name to the left of a dot, or changes one object’s value. The lesson moves from identifying things to explaining how a particular instance remembers its own data.

| Jump into the recording | Question to carry into the example |
|---|---|
| [Opening and first run](https://www.youtube.com/watch?v=ysBjBxdMvwU&t=2s) | How does an unexplained number become information? |
| [Reading the class definition](https://www.youtube.com/watch?v=ysBjBxdMvwU&t=480s) | Which statements belong to the class, and which ask Python about it? |
| [From a class to an instance](https://www.youtube.com/watch?v=ysBjBxdMvwU&t=1260s) | Why do the decimal identity and a hexadecimal display look different? |
| [Reading a class call](https://www.youtube.com/watch?v=ysBjBxdMvwU&t=2280s) | What does calling the class produce, and what does assignment do afterward? |
| [Two instances, one class default](https://www.youtube.com/watch?v=ysBjBxdMvwU&t=2880s) | Why can one object report 12 while the other still reports 11? |
| [Initializing an instance](https://www.youtube.com/watch?v=ysBjBxdMvwU&t=3300s) | Why does a method with two parameters need only one argument from the caller? |

**How to read the evidence.** The local English auto-captions contain 1,200 cues, from the greeting at 0:00:01.680 to the sign-off. The last cue ends nominally at 1:03:48.440; the capture manifest records a video duration of 1:03:48.083. The small overhang is caption timing, not extra footage. All 64 whole-minute screenshots are present, plus 14 event frames and a closing frame at 1:03:47. Times identify seek targets, not exact decoded timestamps. The transcript and stills complement each other: they do not establish every keystroke, and older terminal output can remain beside an edited file. Guide-added comments explain the examples; numbered listings near the end preserve what was visible. No earlier guide was used as lesson evidence.[1]

## From the project discussion to a Python class (00:00–04:59)

The September 21 announcement concerned that week only: the instructor would be out of town and mostly unavailable Wednesday, September 23, and Friday, September 25. This class would **not meet Wednesday**, **would meet Thursday**, and did not normally meet Friday. The Friday mention mattered to the Essentials class. This is a historical schedule note, not a current cancellation.

The instructor pointed the class to the **Link** project, its repository and the discussion in **Joe’s TA**; students could look at the work or post a question under the project. The messages visible at 02:00 show why a project discussion can be more useful than a bare error report. A student working on a login/register page had installed **`rsa`**. The reply distinguished that module from the **`cryptography`** import containing the **`rsa.generate_private_key(...)`** operation the student had in mind. The useful diagnostic question was *which package did you actually install?* No installation, fix or test of that project was shown.

A second message showed **`return_value = function(args)`**. It asks whether using a variable is better than calling functions inside functions. These are different choices: saving a result gives it a readable name, but a result can also become another call’s input without an intermediate name. The visible reply recommends calling functions from other functions to organize long routines and reuse code rather than copy and paste. The class is about to use both ideas: a name for an object and one function’s result as another’s input.

**Key items to scan**

- **September 23–25** — this class would **not meet Wednesday**, **would meet Thursday**, and ordinarily had no Friday meeting.
- **Link** — the project whose repository and question thread the class was invited to visit; no repository URL was shown in the discussion.
- **Joe’s TA** — the server displaying the project conversation, not the Python editor.
- **`rsa`** — the package the student said they had installed.
- **`cryptography`** and **`rsa.generate_private_key(...)`** — the reply associated the intended operation with a different import; check the library, not just the familiar name.
- **`return_value = function(args)`** — names the result; nesting calls instead can pass the result onward directly.

Returning to the previous Thursday, the class recalled doing arithmetic in any base with the chosen symbols. Python could be taught the same rules; Joe had taken up the idea, and others were invited to join **anybase**. This was a bridge, not a new conversion exercise. The next question was whether the class had already started on *classes*. Rather than assume they had, the instructor found a small file to take apart “one bite at a time.”

In PowerShell, the working commands were **`cd C:\CSCS1240F26\`** and **`ls`**. The directory contained **`demo.py`**, shown as 354 bytes with a September 17, 6:24 AM last-write time. The date prompted the question whether it had been used or only prepared; it did not establish an earlier demonstration. In the 05:00 frame the command **`.\demo.py`** has returned to a prompt without a printed result, and the file is open in Sublime Text. Its full listing would inspect a class, create two objects, and change values. The instructor put that longer experiment aside to isolate one question first.

**Key items to scan**

- **anybase** — invitation to explore arithmetic with different bases and symbols, not a new implementation shown here.
- **`cd C:\CSCS1240F26\`** — changed to the course working directory in the recorded PowerShell session.
- **`ls`** — listed the directory and exposed the file’s name, size and modification time.
- **`demo.py`** — the pre-existing classroom file inspected in Sublime Text.
- **`.\demo.py`** — the initial direct invocation; the number discussed next belongs to a different command.

## What does the number mean? (05:00–08:29)

At 05:37 PowerShell shows **`py .\demo.py`** followed by **`2534258504736`**. Without knowing what produced those digits, could anyone say what they meant? A student described data as raw information. The instructor started the relationship the other way around: **data** are raw facts, figures or symbols; **information** adds the meaning that lets someone interpret them. At 08:00, the editor records the relationship in words: **`information = data + meaning`**. It is a teaching mnemonic, not arithmetic the program executed.

The small active example gives the number its meaning. The lines after the identity probe were set aside inside a triple-quoted string, not run as a second experiment. Here are the relevant statements; the inactive lower block is omitted. The 09:00 editor view confirms the opening delimiter beneath this excerpt.

**Annotated teaching version — guide-added comments**

```python
class Item:            # Define a class and give that class object the name Item.
    value = 7          # Store 7 on the class; this statement does not print it.

#print(dir(Item))      # Disabled: do not list the names available on Item yet.
#print(type(Item))     # Disabled: do not print the class object's type yet.
#print(Item.value)     # Disabled: do not print 7 yet.
print(id(Item))        # Ask for the class object's identity, then display that integer.
```

Observed first explicit Python run:

```text
PS C:\CSCS1240F26> py .\demo.py
2534258504736
```

Read **`print(id(Item))`** from the inside out. **`id(Item)`** returns an integer identifying the live class object; the outer call prints it. No call creates an instance here, and this is not the integer stored in the class. The instructor used location in memory as an intuition but cautioned against treating the digits as a physical RAM address. Use an identity to distinguish live objects within a run; do not expect this same number when relaunching Python.

**Key items to scan**

- **data** — `2534258504736` as a string of unexplained digits.
- **information** — those digits understood as the identity of the class in this run.
- **`information = data + meaning`** — the instructor’s relationship, typed as explanatory text.
- **`py .\demo.py`** — explicitly launched Python on the classroom file.
- **`2534258504736`** — observed first identity output, not the stored value `7`.
- **`id(Item)`** — obtains the identity of the class itself, not an instance of it.
- **`print(id(Item))`** — displays that returned integer; `id` does not print by itself.
- **`#`** — makes the rest of a source line a comment; the three prefixed print calls did not execute.
- **`'''`** — marks a multiline string here; the lower program is text while enclosed, not a sequence of executed statements.

## Read the class header, then deliberately break its body (08:30–14:05)

The instructor read **`class Item:`** one piece at a time. **`class`** is a Python keyword, part of the language rather than a name chosen for this file. The separating space matters: the next word, **`Item`**, is the programmer’s identifier. Think *classification* or *category*: we are defining a new kind of object. Sublime’s colors help the reader notice a keyword; the colors are not what give the word its meaning. The instructor recalled “35ish” keywords, an approximation rather than a count to memorize.

The final **`:`** introduces the body. The class has already seen a colon followed by an indented block with **`if`**, **`for`** and **`def`**. What if the next actual statement after the header is all the way back at the left margin? At 11:39 there are three blank lines and then an unindented assignment on line 5. This excerpt is intentionally invalid.

**Annotated teaching version — guide-added comments**

```python
class Item:            # Python now expects at least one statement inside the class.



value = 7              # Line 5: the name starts at the left margin, outside the class.
```

Observed: **`py .\demo.py`** flagged line 5, underlined `value` and reported **`IndentationError`**. It did not execute the later identity print. The assignment is valid Python on its own; the error is that the preceding class header still has no body. Removing one blank line does not give it one. Blank lines may separate the header and the body; the indentation of the next statement is decisive.

**Annotated teaching version — guide-added comments (restored classroom statements)**

```python
class Item:            # Introduce the class again in a new launch of the script.
    value = 7          # Now this indented assignment belongs to Item.

print(id(Item))        # Back at the left margin: print the identity of this run's class.
```

Observed in the terminal by 13:00, below the earlier failure:

```text
PS C:\CSCS1240F26> py .\demo.py
1713038239648
```

If there is nothing to define *yet*, the instructor recalled two ways to give Python a real statement: an indented **`pass`** does nothing; an indented **`...`** is an expression whose result is unused. Either can stand alone as a temporary body. Neither would set the class attribute to 7. The examples below illustrate the spoken alternatives; they are separate definitions, not edits shown running in class.

**Annotated teaching version — guide-added comments (alternatives independently checked; neither prints anything)**

```python
class Item:            # First alternative: define the class without an attribute.
    pass               # The no-operation statement satisfies the body requirement.
```

```python
class Item:            # Second alternative: a separate definition in a fresh example.
    ...                # Evaluate Ellipsis and discard it; this is still a statement.
```

Compare the instructor’s C, C++ and Java analogy: braces mark both ends of a block in those languages. In this Python definition, look for the colon, then the indented statements, then the return to the outer indentation. The lack of a closing brace does not mean the class includes the rest of the file.

**Key items to scan**

- **`class Item:`** — starts the definition of the new class; it does not make an individual `Item()` instance.
- **`class`** — reserved keyword, recognizable independently of editor highlighting.
- **`Item`** — the chosen class identifier; the capital `I` is part of this name.
- **`:`** — in this multiline header, signals that an indented body follows.
- **`if`**, **`for`** and **`def`** — earlier examples with a header and an indented block.
- **`value = 7`** — inside the class it gives the class an attribute; flush left after an otherwise empty header it exposes the missing body.
- **`py .\demo.py`** — a new launch, not a continuation of the previous process.
- **`IndentationError`** — the unindented line on line 5 meant the class beginning on line 1 had no body.
- **`1713038239648`** — identity displayed on the successful post-repair launch; not the first run’s identity.
- **`pass`** — explicit no-operation statement for an unfinished body.
- **`...`** — a different valid placeholder expression; not a synonym for a comment.

## Four questions about the same class (14:06–15:56)

The instructor removed the three comment markers and ran the script. The console suddenly looked crowded. Before trying to understand every name, the class was asked to separate the output into **four results**. Only the first result wraps across many terminal rows; it is still just one Python list.

**Annotated teaching version — guide-added comments (active portion, 15:00)**

```python
class Item:            # Create the class object for this run.
    value = 7          # Give it one explicitly named attribute, value.

print(dir(Item))       # Print a list of the names available on the class.
print(type(Item))      # Print the type of the class object, not of an Item() instance.
print(Item.value)      # Read the named attribute on Item and print 7.
print(id(Item))        # Print the class object's identity for this launch.
```

| In execution order | Observed result at 15:00 | How to read it |
|---|---|---|
| **`dir(Item)`** | A list beginning `['__class__', '__delattr__', '__dict__', '__dir__', '__doc__',` and ending `'__str__', '__subclasshook__', '__weakref__', 'value']` (middle omitted) | The quotes enclose names, not the contents of the attributes. The final `'value'` is the name the instructor supplied. |
| **`type(Item)`** | `<class 'type'>` | The object being examined is the class itself. |
| **`Item.value`** | `7` | The dot asks for what is stored under that name on the class. |
| **`id(Item)`** | `2369932880832` | A new run has its own class identity. The repeated `7` and the changing identities answer different questions. |

Which name in that long directory was not supplied by Python? The instructor selected **`value`** and changed the declaration to **`notvalue`**. A small trap makes this a better test than just rereading the list: the later **`print(Item.value)`** still asks for the old name. At 15:56 the edit is saved, but the terminal still displays the *preceding* run’s `'value'`, `7` and identity. Those are not results of the rename.

**Annotated teaching version — guide-added comments (changed active portion, 15:56)**

```python
class Item:            # In a fresh run, this defines a fresh class.
    notvalue = 7       # The 7 is now attached to the name notvalue, not value.

print(dir(Item))       # The list now includes 'notvalue' in place of 'value'.
print(type(Item))      # Renaming the attribute does not change <class 'type'>.
print(Item.value)      # Looking up the old name fails before print can display a value.
print(id(Item))        # Not reached if the previous lookup raises an exception.
```

Independently running that saved active excerpt produced the directory ending in `'notvalue']`, then `<class 'type'>`, then `AttributeError: type object 'Item' has no attribute 'value'`. There was no new `7` or identity after that failure. This predicts what to look for when the classroom terminal next changes: distinguish what the class **defines** from what the remaining code **asks for**.

**Key items to scan**

- **four results** — list of names, type of the class, stored attribute value, identity; wrapping the list does not create extra results.
- **`dir(Item)`** — names available on this class, including ordinary infrastructure and the application’s own attribute.
- **`type(Item)`** — returns `type`; the enclosing print displays `<class 'type'>`.
- **`Item.value`** — accesses `7` before the rename and fails when the new class lacks `value`.
- **`id(Item)`** — returned the class identity printed as `2369932880832` in the four-result classroom run.
- **`value`** — the instructor-supplied name among the names in the original directory listing.
- **`notvalue`** — replaces the declaration’s name, not every later use of the old spelling.
- **`print(Item.value)`** — must resolve the dotted lookup first; if it raises `AttributeError`, nothing is printed by this call.

## What belongs to the class? Change one name, then one value

At 0:16:00 the next classroom run confirms the rename test: the directory ends in **`'notvalue'`**, the type prints, and the line-6 traceback follows. A runtime error does not erase output from calls that finished first.

Restoring the spelling answers a second question: what is the type of a class? **`Item`** is not just the name of a recipe; the class itself is a Python object. Calling **`type(Item)`** asks about *that object*, not about something made by calling the class. For this ordinary classroom class the answer is **`type`**. A student put it another way: defining a class makes a new type. The instructor emphasized that functions are objects too, and that even the built-in **`type`** itself has a type. The apparent circularity needn't be resolved to use the test: first identify the argument inside the parentheses.

*Annotated teaching version — guide-added comments (restored run, 0:17:00–0:19:00).*

```python
class Item:                # Create the class afresh when this script runs.
    value = 7              # Give the class itself the attribute named value.
print(dir(Item))           # The directory ends with 'value' again.
print(type(Item))          # The class object is an instance of type.
print(Item.value)          # Read 7 from the class and display it.
print(id(Item))            # Display this run's integer identity for the class, not for 7.
```

Observed after the directory: `<class 'type'>`, `7`, `2788779862080`, each on its own line. To test where the seven came from, the instructor changed only the class-body assignment and ran the script again. The dot in **`Item.value`** asks for a member (attribute) of the object to its left; here that object is the class itself.

*Annotated teaching version — guide-added comments (changed definition, 0:20:00).*

```python
class Item:                # A new run creates a new class object under the same name.
    value = 11             # Change the candidate source of the printed number from 7 to 11.
print(dir(Item))           # The attribute name is still 'value'.
print(type(Item))          # Display the type of the class object: <class 'type'>.
print(Item.value)          # Does the changed definition change what this lookup prints? Yes: 11.
print(id(Item))            # Display this launch's class ID, separately from the 11.
```

Observed after the directory in this separate run: `<class 'type'>`, `11`, `1510962937584`. The changed output is evidence that the lookup read the assignment. The final number is *not* the member value: it's the identity of the class object during this run. These different numbers across launches are not stable identifiers to memorize.

The editor also contains a way to test the “everything is an object” claim directly. These three probes are *inside* the quoted-out area at 0:17:00; the results here are from a separate guide run, not from the classroom terminal. Although all three names can be called, they don't all have the same type.

*Annotated teaching version — guide-added comments (independent execution of the three visible probes).*

```python
print(type(dir))           # Ask about the built-in function itself; do not call dir().
print(type(id))            # Inspect id itself, not the integer returned by calling it on an object.
print(type(type))          # Ask about the callable type object itself; its type is type.
```

Independently verified output:

```text
<class 'builtin_function_or_method'>
<class 'builtin_function_or_method'>
<class 'type'>
```

**Key items to scan**

- **`Item`** — class object being inspected before any instance is made.
- **`value`** — class-body name restored to make the existing lookup work; its value changes from 7 to 11 in a later run.
- **`notvalue`** / **`'notvalue'`** — renamed attribute and its name as shown in the directory; not an alternate spelling Python resolves automatically.
- **`dir(Item)`** — lists available names, including names supplied by Python as well as the one defined here.
- **`type(Item)`** — returns **`type`**; printing the result displays `<class 'type'>`.
- **`type(dir)`** and **`type(id)`** — inspect the built-ins without calling them; both print as `<class 'builtin_function_or_method'>` in the separate test.
- **`type(type)`** — the type object is itself an instance of `type`.
- **`Item.value`** — reads from the class; the dot is the member-access operator.
- **`AttributeError`** — line 6 requests a name the renamed class doesn't have.
- **`id(Item)`** — identifies the class object, not its attribute or a future instance.

## From one class to one instance: choose which lines actually run

Around 0:21:00 the instructor stops printing the directory and changes the experiment. Rather than delete the other probes, he puts them between **`'''`** delimiters. The intervening lines then belong to a multiline string, not to executable calls. This has the effect of setting them aside; it is not the same syntax as a Python line comment. For this first experiment, the only active inspection after the class definition is of an instance.

*Annotated teaching version — guide-added comments (active excerpt of the 0:21:00 editor; the class still defines `value = 11` above it).*

```python
a = Item()                 # Call the class to create a new Item; then give that object the name a.
print(id(a))               # Display the identity of the new instance, not of its class.
```

The class is the object on the right *before* the call; the result of calling it is a different object. Assignment evaluates the whole right-hand side first. **`a`** is just the instructor's chosen identifier. Binding it to the result doesn't copy the class or reassign the class's value. By 0:21:27 the terminal shows a one-number run (`2604214551952`), consistent with this excerpt; the visible script is already being rearranged for the next test.

That rearrangement exposes a debugging trap: each triple-quote delimiter changes whether the lines below it are inside or outside a string. In the 0:21:27 editor, lines 3/7 enclose three class probes, and lines 9/12 enclose the two **`a`** lines. The later **`b = Item()`** experiment now appears outside that second pair. The failed launch in scrollback points at a `'''` on line 33 and reports `SyntaxError: unterminated triple-quoted string literal (detected at line 35)`. Python must parse the *whole* file before it can execute any of it. There is no successful output from even an early print in that failed launch. The screen doesn't establish every line of the saved failing version.

At 0:22:00 line 9 is blank. Now the instructor can compare two objects in one process rather than trying to compare numbers left behind by different launches.

*Annotated teaching version — guide-added comments (active probes from 0:22:00, still using the class with value 11).*

```python
print(id(Item))            # Display the identity of the class itself, before an instance exists here.
a = Item()                 # Create one new instance and make a refer to it.
print(id(a))               # Display the identity of that instance.
```

Observed in that order:

```text
1434179590848
1434183959952
```

Two different IDs in the same run distinguish two live objects. The second number doesn't say what the instance *contains*. The instructor's next question was what Python would show if asked for **`a`** itself.

**Key items to scan**

- **`'''`** — paired delimiters temporarily turn enclosed probes into string text; removing one changes the interpretation of later lines.
- **`a = Item()`** — first call the class to create an instance, then bind the name **`a`** to that result.
- **`b = Item()`** — a separate later experiment, exposed in the transient edit; no successful output from this line is established there.
- **`SyntaxError`** — an unclosed triple-quoted string stops the whole script before execution, unlike the earlier lookup error.
- **`id(Item)`** — the class identity, `1434179590848` in the two-number run.
- **`id(a)`** — the instance identity, `1434183959952` in that same run.

## Which number belongs to the object between the angle brackets?

The next edit adds **`print(a)`** between creating the instance and printing its integer ID. Read all three output lines before deciding which two refer to the same thing.

*Annotated teaching version — guide-added comments (active excerpt, 0:23:00–0:25:15).*

```python
print(id(Item))            # First line: the class object's ID, written in decimal.
a = Item()                 # This fresh launch constructs a fresh instance.
print(a)                   # Second line: display Python's default description of this Item instance.
print(id(a))               # Third line: display the same instance's ID, written in decimal.
```

Observed together:

```text
1377438455616
<__main__.Item object at 0x00000140B5FE86E0>
1377442891488
```

Inside the angle brackets, **`__main__.Item`** identifies the class defined in the running script. A student recognized the **`0x`** prefix as the clue to an address. In this interpreter's default object display, the number after `at` is written in hexadecimal (base 16). It matches **the third line**, not the first: `0x00000140B5FE86E0` and `1377442891488` denote the same number. This is the earlier number-systems lesson applied to a live object: changing base changes the available symbols and the way a quantity is written, not the quantity. Don't expect these literal address values on a rerun, or rely on every Python object printing in this format.

**Key items to scan**

- **`print(a)`** — asks the instance for its printable form; it does not request the class ID or read the attribute `value`.
- **`__main__.Item`** — identifies the class and the module where it was defined in this run.
- **`0x`** — prefix introducing a hexadecimal numeral; not part of the numeral's width.
- **`id(a)`** — an integer; its default decimal display and the address in this instance's description represent the same value here.

## Make the comparison fair: print each identity in hexadecimal

The instructor first wraps the *class* identity in **`hex()`**. Read the nested call from the inside out: find the integer ID, produce a hexadecimal string from it, then display that string. An early attempt on line 8 was `print(hex(id(Item))` — two closing parentheses for three opening ones. The 0:26:00 terminal records `SyntaxError: '(' was never closed`, then a separate, corrected launch.

*Annotated teaching version — guide-added comments (corrected mixed-base run, 0:26:00–0:28:00).*

```python
print(hex(id(Item)))       # Convert the class ID to hex and display it.
a = Item()                 # Create a new instance in this launch.
print(a)                   # Display this instance's default description, including its address in hex.
print(id(a))               # Still display the instance ID as a decimal number.
```

Observed together:

```text
0x13f95960380
<__main__.Item object at 0x0000013F95D386E0>
1372608235232
```

Notice what *didn't* change: the last line still uses decimal. Comparing the first two hexadecimal strings as though they were both the instance ID would compare different objects. The instructor explains why hex is a useful display choice: four binary positions have exactly **16** possible settings, just as one hex position has exactly 16 possible numerals. Ten is not a power of two. When written in complete groups of four, four binary symbols can therefore be replaced by one hex symbol, with no information lost.

A brief aside mentions **`bin()`** and a name transcribed as “dec.” The direction matters: **`hex()`** and **`bin()`** turn an integer *into text*, not text into a number. There is no Python built-in named **`dec`**. Ordinary integer printing already gives the decimal display used above. This is a clarification, not a third conversion run in class.[4]

**Key items to scan**

- **`hex()`** — makes a hexadecimal string from an integer; `hex(id(Item))` formats the *class* identity here.
- **`bin()`** — makes a binary string from an integer; mentioned, not used for the displayed class run.
- **`dec`** — not a Python built-in; don't copy the caption as a callable name.
- **`SyntaxError`** — the missing outer closing parenthesis prevents all three prints from running.
- **16** — the number of distinct values in four bits and in one hexadecimal position.

Around 0:28:55 the instructor makes the last print hexadecimal too. A separate failed attempt on line 12, `print(hex(id(a))`, produced the same `SyntaxError: '(' was never closed` as the earlier line-8 mistake. The next launch succeeded. The instructor wondered if the editor was trying to help with parentheses; the traceback establishes the missing delimiter, not its cause.

*Annotated teaching version — guide-added comments (active probes reconstructed from the 0:28:55–0:29:00 editor and terminal; the terminal covers some line beginnings).*

```python
print(hex(id(Item)))       # Display the class ID, this time 0x29833fada80.
a = Item()                 # Make a fresh instance in this run.
print(a)                   # Its default description includes the padded hexadecimal address.
print(hex(id(a)))          # Convert this instance's ID to hex explicitly, without the padding.
```

Observed together:

```text
0x29833fada80
<__main__.Item object at 0x0000029834448590>
0x29834448590
```

Ignore the prefix and the leading zeros when comparing the last two spellings: the digits `29834448590` match. Don't ignore the *internal* differences between the class and instance numbers: the first is genuinely a different ID. When a comparison surprises you, ask two questions separately: “Which object did I ask about?” and “How was its number formatted?”

**Key items to scan**

- **`hex(id(a))`** — format the instance ID rather than the class ID; the returned string here is `0x29834448590`.
- **`0x0000029834448590`** — the same instance identity with a fixed-width hexadecimal field.
- **`0x29833fada80`** — the class object's distinct ID from this launch.

## Count the field, then convert it four bits at a time

Why would a program write five extra zeros? A student suggested the same number of characters might be displayed every time. Think of a form or a printed report with a fixed-width field: unused places are filled instead of being dropped. At 0:30:00 the leading zeros of the newer instance address are selected; at 0:31:00–0:33:00 the instructor works from an *earlier* output, selecting **`0000013F95D386E0`**. Count its 16 hexadecimal numerals, including the padding but excluding the prefix. The field represents **64 bits**. That tells us the *display width*, not how many nonzero places the value needs and not the size of the whole object.

| Question | Calculation | What is being counted? |
|---|---|---|
| Why one hex numeral per four bits? | 2 × 2 × 2 × 2 = 16 | All settings of four binary positions, from `0000` to `1111`. |
| How wide is this field? | 16 hex positions × 4 = 64 bits | Include leading zero positions; do not count `0x`. |
| How many hex places for that many bits? | 64 bits ÷ 4 = 16 hex positions | Reverse the operation. |

For the next question the instructor ignores the leading zeros and selects **`13`** from that older address: not decimal thirteen, but the two hex numerals **`1`** and **`3`**. Give *each* numeral four binary places before joining the groups. The first group **`0001`** is visible on editor line 34 at 0:34:00, inside the quoted scratch area, not as Python output. The class explains the second group aloud: three is two plus one, with no eights or fours. What about **`F`**, the next numeral? Base 16 uses `0`–`9`, then `A`–`F` for the remaining six values. `F` is the highest one. The table completes the conversion as a guide calculation, not a claim that all three groups had been typed in the 0:34:00 frame.

| Hex numeral in order | 8 | 4 | 2 | 1 | Four-bit group |
|---|---:|---:|---:|---:|---|
| `1` | 0 | 0 | 0 | 1 | `0001` |
| `3` | 0 | 0 | 1 | 1 | `0011` |
| `F` (15) | 1 | 1 | 1 | 1 | `1111` |

Thus the first three non-padding hex numerals can be read as `0001 0011 1111`. Keeping the four places inside each group preserves its boundary; it is not the same as stripping leading zeros separately from every group and joining the leftovers.

**Key items to scan**

- **`0000013F95D386E0`** — the earlier address field selected for counting, distinct from the later `298…` run.
- **64 bits** — 16 hexadecimal positions times four bits per position.
- **`13`** — two hex positions selected from the address, converted individually rather than as decimal thirteen.
- **`1`** / **`0001`** — one in a four-bit field; the only completed group visible in the 0:34:00 editor.
- **`3`** — two plus one; its four-bit spelling is `0011`.
- **`F`** — the sixth letter used after `9`, representing 15, or four one-bits.

## Finish the hex conversion: one symbol for four bits (35:00–36:55)

How can a letter stand for a number? Decimal uses up all ten of its single-digit symbols at **`9`**. Hexadecimal needs sixteen symbols before it rolls over to a second place. Starting at zero matters: **`A`** is the *eleventh symbol*, but its value is *ten*. The captions momentarily put B and C one place too high. Continue counting from ten to get the mapping below (independently checked).

| Hex digit | A | B | C | D | E | F |
|---|---:|---:|---:|---:|---:|---:|
| Decimal value | 10 | 11 | 12 | 13 | 14 | 15 |
| Four bits | 1010 | 1011 | 1100 | 1101 | 1110 | 1111 |

To convert **`F`**, ask which of the four place weights **`8, 4, 2, 1`** you need to make fifteen. You need all four, so write **`1111`**. A leading zero does not change a number's value, but keeping every group four digits wide makes boundaries between neighboring hex digits unambiguous. This is why hex makes a compact way to display identities: a shorter *representation*, not a smaller number.

There are two examples to keep straight. In the spoken question the instructor takes **`1`**, then **`3`**, then asks for **`F`**. The independently checked conversion of those three digits is **`0001 0011 1111`**. In the editor scratchpad, line 34 actually ends as **`0001001100011111`** (37:00). Grouping those exact digits gives **`0001 0011 0001 1111`**, or **`131F`**: there is an additional group for one. The scratchpad contains notation inside a quoted block, not a Python calculation or printed result.

**Key items to scan**

- **`9`**, **`A`** — the last shared decimal digit and the next hex digit; A represents ten, not eleven.
- **`F`**, **`8, 4, 2, 1`**, **`1111`** — fifteen uses every binary place in one four-bit group.
- **`1`**, **`3`**, **`F`** → **`0001 0011 1111`** — four bits for each digit in the spoken example.
- **`0001001100011111`** → **`0001 0011 0001 1111`** → **`131F`** — the distinct, literally typed example; spaces are guide-added.

## Which of these three lines is different? (36:55–39:18)

The instructor returns to the three-line run already on screen and asks which line is different. It is the *first*: **`hex(id(Item))`** identifies the class. The padded address inside **`print(a)`** and the shorter number from **`hex(id(a))`** on the third line both identify the instance. The earlier worked run shows the full output; no new execution is implied by the discussion at 38:00.

**Key items to scan**

- **`hex(id(Item))`** — the class, not the instance, supplies the distinct first identity.
- **`print(a)`**, **`hex(id(a))`** — two ways to display information about the same live instance.

## Read a class call in execution order (39:18–43:49)

The instructor gives two clues for reading **`a = Item()`**. First, capitalizing the class name and using a lowercase name for an instance makes these roles easier to spot. That is a naming convention, not what makes Python construct an instance. Second, an identifier followed by an opening parenthesis is a useful cue to look for a *call*. In earlier procedural programs a function grouped steps that could be called again without rewriting them. In object-oriented code the instructor uses *method* when thinking about behavior belonging to an object or class. The familiar parentheses do not by themselves tell you whether the thing being called is a plain function, a method, or—as here—a class.

One student predicts “create an object”; another describes storing a return value. They are describing different steps of the same statement. First look up **`Item`** and call it with **`()`** (no explicit arguments). For this class the call produces a new instance. *Then* assignment makes **`a`** refer to that result. The class body has no explicit return statement and the assignment has no output. You don't need to write a separate function named Item to make the call work. The instructor calls this the *constructor* and speaks of returning an address; at Python level the call returns the object, not the numeric ID you could obtain from it.

**Key items to scan**

- **`Item`** vs. **`a`** — uppercase class and lowercase instance name here are reading aids, not an execution rule.
- **`()`**, **`a = Item()`** — call first, then bind the name to the resulting instance; empty parentheses supply no explicit arguments.
- **Function** — a reusable group of statements; **method** — function-like behavior associated with an object or class. The bare `Item()` expression calls the class.
- **Constructor** — the class call builds a new instance even though this short class supplies no custom construction methods.

## What Python supplies: creation, initialization, and the instance itself (43:49–47:00)

Where did the new object come from if the class body only set a number? The instructor scrolls back to the list from **`dir(Item)`**. In that earlier output the selection moves from **`__new__`** (45:00) to **`__init__`** (46:00). The methods already exist without either appearing in the class definition.

| Stage in the ordinary call here | What happens to this particular Item |
|---|---|
| Creation: `__new__` | Produces the new instance. This is the creation/allocation step; the application did not supply its own version. |
| Initialization: `__init__` | Receives that newly created instance so its starting state can be set. There is no custom initializer here, and this is not a second new object. |
| The class call completes | The instance, not the initializer's return value, becomes the value assigned to `a`. |

This is what someone means by *instantiating* a class or “newing up” an object. The instructor contrasts Python's class-call syntax with a **`new`** keyword in languages such as Java or C++. Python does not require that keyword. In ordinary class definitions you more often customize the initialization step than the creation step. The customary first parameter **`self`** in an instance method gives its body a way to refer to the particular object being initialized or used—not the class as a whole. The instructor compares that role to **`this`** in other languages: a useful analogy, not a Python spelling requirement. No custom initializer or method body is shown in this example.

**Key items to scan**

- **`dir(Item)`** — the earlier list is evidence that the class has names the instructor did not define explicitly.
- **`__new__`** — creation: supplies the instance for this ordinary call.
- **`__init__`** — initialization: works with the instance already created; an initializer does not return the instance.
- **`new`**, **instantiation** — the cross-language expression and the technical term for creating an instance; the Python spelling here is `Item()`.
- **`self`**, **`this`** — ways of talking about the particular instance; `self` is the Python naming convention discussed, not a reserved keyword.

## Create a second Item: compare identities within one run (47:00–48:49)

The class exists once in each execution of the definition, but a second call to **`Item()`** makes another instance. The instructor activates two previously quoted lines. Keep the class and first three print results from the earlier hexadecimal run in mind; the new lines add one more result without changing what **`a`** refers to.

**Annotated teaching version — guide-added comments.** First revision, appended to the active first-instance example:

```python
b = Item()              # Make a second instance, not another name for a.
print(id(b))            # Print b's integer ID directly, in decimal in this version.
```

Observed run (48:00):

```text
0x18ef195df60
<__main__.Item object at 0x0000018EF1DF8590>
0x18ef1df8590
1713454875984
```

The instructor wants a like-for-like comparison. The editor shows the inner formatting expression selected and copied from the line about **`a`**. The important change when reusing it is the argument: ask about **`b`**, not **`a`** again.

**Annotated teaching version — guide-added comments.** Replacement for the same two lines; a fresh script invocation, not a third instance in the preceding run:

```python
b = Item()              # This run creates its own second instance.
print(hex(id(b)))       # Show b's identity in the same notation used for a.
```

Observed run (48:15):

```text
0x23ca394c7f0
<__main__.Item object at 0x0000023CA3EA8590>
0x23ca3ea8590
0x23ca3e98550
```

Within this run, the last two lines differ: **`a`** and **`b`** are distinct living objects of the same class. Across runs even the class ID changes. The instructor notes that allocation and garbage collection occur behind the scenes and memory can be reused. Neither tomorrow's numerical address nor the ordering or spacing of IDs is a property of the class. Test the same/different relationship while the objects are alive, not a particular literal from an earlier run.

**Key items to scan**

- **`Item()`**, **`a`**, **`b`** — a second call makes a new instance; it does not move or overwrite the first.
- **`print(id(b))`** — the original second-instance line prints `1713454875984` in decimal in its own run.
- **`print(hex(id(b)))`** — the revised line prints `0x23ca3e98550` in a new run, distinct from `0x23ca3ea8590` for `a`.
- **Identity lifetime** — current, simultaneous instances are distinguishable; a later run or later allocation can use different or reused addresses.

## Two equal values do not make one object (48:49–50:58)

Now ask a different question: not “Are these the same instance?” but “What does each instance find under the name **`value`**?” The class body assigned **`value = 11`**. Neither of these freshly constructed instances supplies its own value by that name, so both lookups find the class attribute. Dot notation names the object to start from on the left and the attribute to look up on the right.

**Annotated teaching version — guide-added comments.** Additional active lines in the 49:00–50:00 revision, following the identity displays for the class and the two instances from the previous state:

```python
print(a.value)          # a has no value of its own yet; print the class value, 11.
print(b.value)          # A different instance finds that same class value, 11.
```

Observed output of the corresponding run (50:00):

```text
0x1b4575cf0b0
<__main__.Item object at 0x000001B457928590>
0x1b457928590
0x1b457918550
11
11
```

Next the instructor proposes changing **`a.value`** to twelve. Distinguish the two assignment targets in the editor. One sets an attribute on one instance; the other explicitly sets the attribute on the class back to the same value it already had.

At 50:58 the editor exposes the writes, but the last terminal run still ends in `11`, `11`. Those two lines establish the starting values, not what happened after either assignment. The next completed run makes the difference visible.

**Key items to scan**

- **`print(a.value)`** and **`print(b.value)`** — read the shared 11 through two distinct instances.
- **`a.value = 12`** versus **`Item.value = 11`** — the target changes from an individual item to the class itself. Neither assignment prints.


## One class value, two objects: which one changed? (51:00–54:48)

The class starts with **`value = 11`**. Making two items gives us two distinct objects, but neither one has a value of its own yet. When asked for either object's value, Python finds the same **class attribute** on **`Item`**. The instructor asks what happens if we give just the first object a different number. The crucial question isn't merely “What is the name of the attribute?” It's “What is to the left of the dot when we assign it?”

*Annotated teaching version — guide-added comments. Attribute experiment from `demo.py`; the identity prints are omitted here so the changing values stand together.*

```python
class Item:                  # Define the class, not either of the two items.
    value = 11               # Store 11 on the class itself.

a = Item()                   # Create the first item; it can look up the class's 11.
b = Item()                   # Create a different item with the same fallback.
print(a.value)               # No value on a yet: print the class's 11.
print(b.value)               # No value on b yet: print 11 again.
a.value = 12                 # Give a its own value, 12; a is still the same item.
Item.value = 11              # Set the class attribute, not a's attribute, to 11.
print(a.value)               # Find the nearer value on a: print 12.
print(b.value)               # Still look on Item for b: print 11.
```

Observed numeric portion of the comparison run, visible at 52:00:

```text
11
11
12
11
```

| After this step | Class attribute | `a` reads | `b` reads |
|---|---:|---:|---:|
| Both constructed | 11 | 11 (class) | 11 (class) |
| `a.value = 12` | 11 | 12 (instance) | 11 (class) |
| `Item.value = 11` | 11 | 12 (instance) | 11 (class) |

This is **shadowing**: the name still exists on the class, but the value on the individual item takes precedence for that item. The assignment did not change the identity of either item or change the integer 11 into 12. The identity lines earlier in the run measured the class and the two *items*, not their integer attributes. The instructor calls a shared setting a “static property” and contrasts it with an independent setting. In this particular Python class, writing **`a.value`** does *not* update every item. Writing **`Item.value`** changes what items without their own value will find; here the instructor deliberately writes 11, the same number as before. “Property” in this discussion means an attribute; the code does not declare a Python property descriptor.[2]

**Key items to scan**

- **`value = 11`** — in the class body, this supplies the shared starting value.
- **`Item`** — the class object; the two calls create different instances, not copies of every class attribute.
- **Class attribute** — a name stored on the class; both items initially find 11 there.
- **`a.value`** — reads 11 before assignment and 12 afterward; assigning it leaves the other item alone.
- **`Item.value`** — still 11 after the class-level assignment; **`b.value`** continues to find it.
- **Shadowing** — the instance's own 12 wins over the class's 11 when reading from the first item.

## Ask for the class of the item, then read that class (52:00–54:48)

There's a way to get back to the class even when the starting name refers to an item. In the same experiment, **`type(a)`** gives the class that made the first item. Reading **`type(a).value`** therefore gives 11, not the 12 found directly on the item. The instructor also inspects the *tools* being used: passing a function's name without parentheses asks what kind of object the function itself is. It does not run the function on the item.

*Annotated teaching version — guide-added comments. Continuation of the preceding example, with its existing class and items; these lines are active in the later run.*

```python
print(type(a))               # Display a's class: Item, defined in the running script.
print(type(a).value)         # Get Item, then read its class attribute: 11.
print(type(dir))             # Inspect dir itself, not a list of names from dir(...).
print(type(id))              # Inspect id itself, not the identity returned by id(...).
print(type(type))            # Inspect type itself: its type is type.
```

Observed final five lines of the **separate run** at 52:35, after the identity and four numeric lines:

```text
<class '__main__.Item'>
11
<class 'builtin_function_or_method'>
<class 'builtin_function_or_method'>
<class 'type'>
```

The first line does not mean that the item *is* a class. It tells us that the item is an instance of the class defined in the running script. The class is itself an object: it can have attributes of its own and can be passed to functions. The instructor does not claim a separate intended conclusion for the built-in-function probes; their observed results provide a useful comparison.

**Key items to scan**

- **`type(a)`** — returns the class of the item; the surrounding print displays `<class '__main__.Item'>`.
- **`type(a).value`** — reaches the class through the item and returns 11, bypassing the item's own 12.
- **`type(dir)`** and **`type(id)`** — both report `builtin_function_or_method`; neither expression calls the inspected function.
- **`type(type)`** — reports `<class 'type'>`, a different result from the two built-in functions.

## Start a fresh item: why is only one argument missing? (55:00–57:50)

At 55:00 File Explorer shows both **`demo.py`** and **`demo - Copy.py`**. The instructor mentions putting the work in a repository; no commit is demonstrated here. The editor is reduced to the class and its 11, then the 11 is replaced with an initializer. This is a **new version**, not a method added alongside the old class attribute. The purpose of **`__init__`** is now to give each new item the value supplied by its caller.

*Annotated teaching version — guide-added comments. Completed no-argument version at 57:00–57:15.*

```python
class Item:                       # This version no longer defines value = 11.
    def __init__(self, value):    # Requires the new item and a value to store on it.
        self.value = value        # Would store that supplied value on this item.

a = Item()                        # No value supplied: initialization raises TypeError.
print(a.value)                    # Never reached; nothing is printed by this line.
```

Observed after running **`py .\demo.py`** at 57:15 (terminal wrapping joined):

```text
Traceback (most recent call last):
  File "C:\CSCS1240F26\demo.py", line 5, in <module>
    a = Item()
TypeError: Item.__init__() missing 1 required positional argument: 'value'
```

The instructor's prediction question has two parts: does the program work, and, if not, *which* exception explains the failure? There are two names in the method definition but only **one** missing argument. Python supplies the new item as the first argument. It cannot supply the second argument, and this version gives no default. Argument matching fails before the initializer body runs. The right-hand call never finishes, so the assignment to the top-level name does not complete either.

**Key items to scan**

- **`demo.py`** and **`demo - Copy.py`** — both filenames are visible before the rewrite; their presence is not a demonstrated repository commit.
- **`__init__`** — initializes an already-created item; here it needs a caller-supplied value.
- **`self`** — receives the new item automatically; it is not the missing argument.
- **`Item()`** — supplies no explicit argument for `value` in this version.
- **`py .\demo.py`** — the PowerShell command used to run the file; each invocation is a separate run.
- **`TypeError`** — names `value` as the one required positional argument missing from the call.

## Give the new item a value, then change only the receiver's name (57:50–59:45)

There's nothing special about a number as an initializer argument. The instructor tries a string instead. The screen literal is lowercase **`"abc"`**. At 58:00 the editor already shows the fix while the terminal still shows the *previous* failure; by 58:12 a new invocation has produced its own result.

*Annotated teaching version — guide-added comments. First successful version (58:12).*

```python
class Item:                       # Define this run's class.
    def __init__(self, value):    # self gets the new item; value gets the string.
        self.value = value        # Keep a reference to "abc" on this item.

a = Item("abc")                  # Construct and initialize a new item; bind a to it.
print(a.value)                    # Read its saved string and print abc, without quotes.
```

Observed in the first successful run:

```text
PS C:\CSCS1240F26> py .\demo.py
abc
```

Next, a student asks whether the first parameter always has to be “self.” Its *role* here is fixed, but the spelling **`self`** is a Python naming convention, not a reserved word. The instructor demonstrates this by changing the parameter and its use together, without changing what the caller supplies. Other languages sometimes call the corresponding object “this”; that is an analogy, not the Python spelling required here.

*Annotated teaching version — guide-added comments. Renamed version (59:00–59:02); this is a fresh program invocation.*

```python
class Item:                       # Define the class for this run.
    def __init__(me, value):      # me now names the new item; value still gets "abc".
        me.value = value          # Write to that item's attribute via the renamed parameter.

a = Item("abc")                  # Same caller syntax; no explicit argument for me.
print(a.value)                    # Same result: display abc.
```

Observed in the **second** successful run:

```text
PS C:\CSCS1240F26> py .\demo.py
abc
```

**Key items to scan**

- **`"abc"`** — the string the caller supplies; the quotes mark a literal in source and are not printed.
- **`Item("abc")`** — supplies one explicit argument for the initializer's second parameter.
- **`self`** — conventional first parameter name for an instance method; use it in ordinary code for readability.
- **`me`** — classroom substitution for `self`; it receives the same item because of its position, not its spelling.
- **`me.value = value`** — the dot specifies where to keep the string; the bare name on the right supplies what to keep.
- **`print(a.value)`** — displays `abc` in each successful run; it is not output from the initializer.

## Follow the two references: the new item and the string it remembers (59:45–1:03:47)

The instructor returns to an earlier list of class names in the terminal and highlights **`__new__`** and **`__init__`** in turn. The earlier errors in that scrollback are *not* fresh failures of the renamed initializer. The question now is what happens between the call and the print in the successful example.

| Order | What happens for `a = Item("abc")` | What exists afterward |
|---|---|---|
| 1 | Evaluate the right side, including the argument expression `"abc"`, before assigning to `a`. | A string object is available to pass; the expression need not allocate a fresh copy of that literal every time. |
| 2 | Calling the class uses `__new__` to create the new item in this example. | There is an item distinct from the string. |
| 3 | Python's normal class-call machinery then calls `__init__` on that item, with the caller's string. | `me` refers to the item; `value` refers to `"abc"`. |
| 4 | `me.value = value` takes the right-hand reference and stores it as an attribute on the item. | The attribute now refers to the same string as the parameter, not to a copied string. |
| 5 | Initialization finishes and the class call yields the item, which is bound to `a`. | The initializer's local parameter names are no longer needed. The item still has its attribute. |
| 6 | `print(a.value)` reads the attribute through `a`. | The screen shows `abc`. |

The instructor uses “address” as a way to picture a reference. The initializer receives the *object*, not an integer address that the student must look up or pass. Nor does the student need to write a call to the initializer inside the allocation method: the normal construction machinery coordinates the two. See the Python data model for this ordering.[3] The assignment's two uses of **`value`** are also different: the right side is a name local to this particular call; the name after the dot identifies an attribute on the item. They could be spelled differently if the corresponding uses were changed consistently.

That difference answers the final question: how does an ordinary function call “remember” a value once its parameters are gone? The call doesn't have to remember it. The *item* holds the reference. Returning from the initializer does not erase the string while the item still refers to it, and **`a`** lets the rest of the program reach that item. The demonstration ends there; it shows no object deletion or further program run.

**Key items to scan**

- **`__new__`** — creates the item in this ordinary construction; the class uses the inherited implementation here.
- **`__init__`** — receives that item and the supplied string, and initializes the item rather than returning it to the caller itself.
- **`value`** — the local parameter referring to the incoming string; getting the same spelling as the attribute does not make it the same storage location.
- **`me.value`** — the attribute kept on the individual item, readable after the initializer returns.
- **`a`** — refers to the completed item, not to the string or the initializer's return value.

## Syntax to remember

| Write this | Question it answers in this lesson |
|---|---|
| `Item` / `Item()` | Am I referring to the class itself or calling it to make an instance? The no-argument call stops working once the new initializer requires a value. |
| `dir(Item)` | What attribute names can Python find here? Look for the name added by the class, not just the inherited names. |
| `type(a)` / `type(a).value` | What class is this instance of? What does the attribute on **that class** contain? The second expression does not read the instance’s own override. |
| `id(a)` / `hex(id(a))` | Which live object is this? How would the same integer look in base 16? |
| `a.value = 12` | Give this instance its own attribute; do not rebind `a` or assign through `Item`. |
| `def __init__(self, value):` | Define what to do with an already-created instance and the argument supplied by its caller. |
| `self.value = value` | Read the parameter on the right; store its object reference under an attribute name on this instance. |

## Knowledge check

1. A student sees one integer from the script and calls it “the value of the item.” Which two expressions should they look for to decide whether it was the class’s identity or the class attribute?
2. In the original class, suppose you put `pass` in the body, move `value = 7` flush left, and then try `print(Item.value)`. Is this the same error as giving the class no indented body? Explain the difference.
3. The instance display has a padded hexadecimal address, while the last line has fewer digits and lowercase letters. How can you test whether these lines identify the same object? Why would comparing with a line from yesterday’s run be a bad test?
4. In the version with a class default of 11, `a` and `b` start as separate instances. After `a.value = 12`, predict `a.value`, `b.value`, and `type(a).value`. Which assignment could change the default consulted by `b` without changing `a.value`?
5. You see `a = Item()` fail after the initializer is rewritten. The error says one positional argument is missing, although the definition has two parameters. Identify the two inputs in the succeeding call and explain why `print(a.value)` can still display the string after initialization is over.

### Answer key

1. **`print(id(Item))`** displays a number identifying the live class object. **`print(Item.value)`** reads the named class attribute and displays its contents. A line of digits by itself cannot tell you which question was asked.
2. These fail at different stages. An empty multiline class header has no suite, so the parser raises **`IndentationError`** before the program runs. An indented **`pass`** is a valid suite. With the assignment flush left, **`value`** is instead a name at module level; looking up the absent **`Item.value`** raises **`AttributeError`** at runtime.
3. Within one run, interpret both hex fields as base-16 integers, ignoring the prefix, case and leading zero padding. Compare their values with **`id(a)`**, not **`id(Item)`**. The runtime can use a different identity integer on another launch, or reuse a number after an object’s lifetime ends.
4. The answers are **12, 11, 11**. For these plain attributes, lookup starts with the instance’s own data; **`b`** has no override, so it reads the class default. For example, assigning **`Item.value = 15`** would make **`b.value`** read 15 but leave **`a.value`** at 12. That last assignment is a transfer case, not a classroom edit.
5. Python supplies the new instance as the first argument to **`__init__`**. The caller supplies the string **`"abc"`** for **`value`**. Saving the reference as an instance attribute makes it accessible through **`a.value`** after the local parameter is no longer in scope. Returning the string from the initializer is neither necessary nor correct.

## Library of definitions

| Term | What it names here |
|---|---|
| Data / information | A numeral in the terminal is data. Connecting it to the specific `id` call gives it meaning: the instructor’s working distinction. |
| Class / type | `Item` defines a kind of object and is itself a Python object. In this example `type(Item)` is `type`, while `type(a)` is `Item`. |
| Instance / instantiation | `a` refers to one concrete `Item`. Instantiation is making such an instance, not defining another class. |
| Name / binding / identity | A name such as `a` is bound to an object; `id(a)` is an integer identifying that object during its lifetime. The name and integer are not the object itself. |
| Attribute / member | A name looked up after a dot. The receiver to its left matters: `Item.value` and `a.value` need not agree. “Property” is used informally in class; the demo does not define a Python `property` descriptor. |
| Class attribute / instance attribute | The class body sets a default visible via instances; assignment to `a.value` stores a separate entry on `a` in this example. |
| Shadowing | An instance attribute of the same name takes precedence over this class’s plain default on a read. The class attribute has not been erased. |
| Function / method / callable | A callable can be invoked with parentheses. An instance method is defined on the class and receives the instance when bound to it. A class is callable too; that does not make the class an ordinary function. |
| Construction / initialization | Construction makes the instance available. `__new__` creates/returns it; for the ordinary class here, the call machinery then invokes `__init__` on it to set its starting state. |
| Parameter / argument / receiver | `value` is a parameter in the definition; `"abc"` is the argument supplied in the call; the new instance is the receiver passed in first. `self` is the conventional name for it, not a keyword. |
| Hexadecimal numeral / bit | One hex position has 16 possibilities, exactly enough to represent four binary positions. Leading zeros can keep a display width fixed without changing its number. |
| Exception / traceback | An exception reports a failure. A traceback helps locate the call or expression involved; a parse-time diagnostic instead points to invalid source syntax. |

## Sources

The recording and its matched local captions and frames are the historical sources.[1] The Python references below clarify the language rather than supply missing classroom events: class and instance namespaces and method binding[2]; identity versus an address and the roles of creation and initialization[3]; and what the built-in inspection and base-formatting operations return[4].

[1] <https://www.youtube.com/watch?v=ysBjBxdMvwU&t=2s> — CSCS1240F26 13

[2] <https://docs.python.org/3/tutorial/classes.html> — Python tutorial: Classes

[3] <https://docs.python.org/3/reference/datamodel.html> — Python reference: Data model

[4] <https://docs.python.org/3/builtins/functions.html> — Python: Built-in functions

## Historical screen-code appendix

Read these listings as evidence of successive editor states, not as a single program. The numbers before each vertical bar are editor line numbers, **not code to type**. A later traceback may belong to a different saved revision than the adjacent editor.

### A.1 · The starting file before the instructor narrows it down — `demo.py`, 05:00

The numbers before the vertical bars reproduce the Sublime Text gutter; do not type them. Source: `00-05-00.png`, lines 1–29 of `C:\CSCS1240F26\demo.py`. This is the longer experiment visible when the file is first opened. Line 8 is blank: unlike the later state, these two instance constructions are executable here. No classroom run of this entire visible state is established by this frame.

```text
 1 | class Item:
 2 |     value = 7
 3 |
 4 | #print(dir(Item))
 5 | #print(type(Item))
 6 | #print(Item.value)
 7 | print(id(Item))
 8 |
 9 | a = Item()
10 | print(id(a))
11 |
12 | b = Item()
13 | print(id(b))
14 |
15 | print(a.value)
16 | print(b.value)
17 |
18 | a.value = 12
19 | Item.value = 11
20 |
21 | print(a.value)
22 | print(b.value)
23 |
24 | print(type(a))
25 | print(type(a).value)
26 |
27 | print(type(dir))
28 | print(type(id))
29 | print(type(type))
```

- Lines 1–7 define the class, give it `value = 7`, and print its identity; three other probes are commented out.
- Lines 9–16 create two distinct objects, not two names for a single object. With no individual `value` yet, each sees the `7` on its class. Lines 18–22 give `a` its own `12` and change the class’s value to `11`; `a` now sees `12`, while `b` still looks to the class and sees `11`.
- Lines 24–25 ask first for `a`’s type and then for the attribute on that type, not on `a` itself. Lines 27–29 inspect the objects named `dir`, `id` and `type` without calling those inner names.
- **Independent run of the visible lines, not classroom output:** three distinct numeric identities, followed by `7`, `7`, `12`, `11`, `<class '__main__.Item'>`, `11`, `<class 'builtin_function_or_method'>`, `<class 'builtin_function_or_method'>`, and `<class 'type'>`, each on its own line. The three identities vary. The single number in the 05:37 classroom terminal is not output of this complete listing.

### A.2 · Isolate identity and write a mnemonic — `demo.py`, 05:37–11:00

**Assembled visual reconstruction.** `00-09-00.png` supplies lines 1–29 (also visible at 10:00 and 11:00); `00-08-00.png` supplies lines 19–32. Their shared lines 19–29 and line numbers agree. The earlier lower viewport contains the closing quotes; the later upper viewport shows the opening quotes. The composite explains the matching visible states, not an assertion that a complete saved file was captured at a single instant.

```text
 1 | class Item:
 2 |     value = 7
 3 |
 4 | #print(dir(Item))
 5 | #print(type(Item))
 6 | #print(Item.value)
 7 | print(id(Item))
 8 | '''
 9 | a = Item()
10 | print(id(a))
11 |
12 | b = Item()
13 | print(id(b))
14 |
15 | print(a.value)
16 | print(b.value)
17 |
18 | a.value = 12
19 | Item.value = 11
20 |
21 | print(a.value)
22 | print(b.value)
23 |
24 | print(type(a))
25 | print(type(a).value)
26 |
27 | print(type(dir))
28 | print(type(id))
29 | print(type(type))
30 |
31 | information = data + meaning
32 | '''
```

Here the multiline string starts **at line 8**, before either `Item()` call. Its contents are text: no instances are constructed and no `data` or `meaning` variables are evaluated there. At 07:00, line 31 read `informat` with the caret after the `t` and line 32 already showed `'''`; the full mnemonic is visible by 08:00. At 09:00 the editor highlights `class`; at 10:00, `Item`; at 11:00, `:`. These are selections, not changes to the definition.

The earlier 05:37 terminal places two distinct invocations in order. After the first, no textual result is visible before the next prompt. Only the second, explicitly invoking Python, produced the displayed number. At 06:00 that same number is selected, not recalculated.

```text
PS C:\CSCS1240F26> .\demo.py
PS C:\CSCS1240F26> py .\demo.py
2534258504736
```

### A.3 · Remove the indentation, then restore it — `demo.py`, 11:39–14:00

At `00-11-39.png` the indented class assignment has become a top-level assignment on line 5. The altered opening appears below; the subsequent string text begins with `a = Item()` at line 12. The blank lines are part of the evidence.

```text
 1 | class Item:
 2 |
 3 |
 4 |
 5 | value = 7
 6 |
 7 | #print(dir(Item))
 8 | #print(type(Item))
 9 | #print(Item.value)
10 | print(id(Item))
11 | '''
12 | a = Item()
```

The top-level assignment is not a class body. The classroom run flags the first nonblank line after the header. The right side of the exception text is covered by the editor; the visible wrap continues on the following terminal row.

```text
PS C:\CSCS1240F26> py .\demo.py
  File "C:\CSCS1240F26\demo.py", line 5
    value = 7
    ^^^^^
IndentationError: expected an indented block after class defini[remainder not visible]
line 1
```

At `00-12-00.png` one fewer blank line separates the header and assignment, but it is **still not indented**. The terminal still reports the prior line-5 failure; it is not a record of executing this line-4 edit.

```text
 1 | class Item:
 2 |
 3 |
 4 | value = 7
 5 |
 6 | #print(dir(Item))
 7 | #print(type(Item))
 8 | #print(Item.value)
 9 | print(id(Item))
10 | '''
11 | a = Item()
```

At `00-13-00.png` the assignment is back inside the class. The opening delimiter follows the one active print; `00-14-00.png` shows the successful number selected, not another launch.

```text
 1 | class Item:
 2 |     value = 7
 3 |
 4 | #print(dir(Item))
 5 | #print(type(Item))
 6 | #print(Item.value)
 7 | print(id(Item))
 8 | '''
```

```text
PS C:\CSCS1240F26> py .\demo.py
1713038239648
```

### A.4 · Four probes, then a renamed declaration — `demo.py`, 15:00–15:56

At `00-15-00.png` the first three print calls are no longer comments. The opening delimiter beneath line 7 still excludes the instance experiment.

```text
 1 | class Item:
 2 |     value = 7
 3 |
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | print(id(Item))
 8 | '''
```

PowerShell shows another `py .\demo.py` call. The first result is a list ending in `'value']`; the right-hand portion of several rows is hidden by the editor. Readable names include `__class__`, `__delattr__`, `__dict__`, `__dir__`, `__doc__`, `__firstlineno__`, `__format__`, `__ge__`, `__getattribute__`, `__gt__`, `__hash__`, `__init__`, `__init_subclass__`, `__lt__`, `__module__`, `__ne__`, `__new__`, `__reduce__`, `__repr__`, `__setattr__`, `__sizeof__`, `__str__`, `__subclasshook__`, `__weakref__` and `value`. This is not a purported full transcription. The next three output lines are completely visible:

```text
<class 'type'>
7
2369932880832
```

At `00-15-56.png` the declaration and the use no longer agree. The editor’s status shows the file saved; the terminal still contains the earlier directory (with `value` selected), type, `7`, and identity.

```text
 1 | class Item:
 2 |     notvalue = 7
 3 |
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | print(id(Item))
 8 | '''
```

The definition now installs `notvalue`, but line 6 requests `value`. A fresh execution prints the directory and class type before that lookup fails; it cannot reach line 7. This is a prediction from the edited text, separately checked in the teaching section, not a claim that the older scrollback records execution of this version.

### A.5 · `demo.py` — the renamed attribute, the repair and the value-change test (0:16:00–0:20:00)

Numbers to the left of `|` identify source lines; do not type the numbers or the separator. The blocks below are source records, not the teaching versions with added comments. Output uses the instructor's actual per-run IDs. All the launches identified here show the same PowerShell command, `py .\demo.py`, at `PS C:\CSCS1240F26>`; the prompt itself is not part of the command.

**Renamed state, `00-16-00.png` (partial).** The original top lines and the line reproduced by the traceback are independently visible. The terminal overlays the left edge of several editor lines. The later restored listing below shows the original four-probe arrangement after the repair, not proof of every covered character before it.

```text
 1 | class Item:
 2 |     notvalue = 7
 3 |
 4 | print(dir(Item))
```

Terminal identifies this line in the failed run:

```text
 6 | print(Item.value)
```

The directory ended with `'notvalue'`, then `<class 'type'>` printed. Lookup failed on line 6:

```text
Traceback (most recent call last):
  File "C:\CSCS1240F26\demo.py", line 6, in <module>
    print(Item.value)
          ^^^^^^^^^^
AttributeError: type object 'Item' has no attribute 'value'
```

Earlier, separate scrollback still visible at 0:16:00: `IndentationError: expected an indented block after class definition on line 1`; a launch printing `1713038239648`; and a launch printing the directory ending in `'value'`, then `<class 'type'>`, `7`, `2369932880832`. None of those IDs is output from the renamed-attribute failure.

**Restored editor, `00-17-00.png`, lines 1–29.** This is the full readable numbered region of the editor, not the bottom of the file. The triple quote on line 8 is as important as the code above it: the subsequent gray text is not this run's driver.

```text
 1 | class Item:
 2 |     value = 7
 3 |
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | print(id(Item))
 8 | '''
 9 | a = Item()
10 | print(id(a))
11 |
12 | b = Item()
13 | print(id(b))
14 |
15 | print(a.value)
16 | print(b.value)
17 |
18 | a.value = 12
19 | Item.value = 11
20 |
21 | print(a.value)
22 | print(b.value)
23 |
24 | print(type(a))
25 | print(type(a).value)
26 |
27 | print(type(dir))
28 | print(type(id))
29 | print(type(type))
```

Lines 4–7 respectively ask for available names, the class object's type, the value stored on the class, and the class object's identity. Lines 9–29 show the further experiments set aside: constructing two instances, inspecting their attributes, assigning separately to an instance and the class, and inspecting types. No output for those disabled lines should be inferred from this run.

Observed directory for the restored name, legible with the terminal foreground at `00-19-00.png` (reflowed for reading; item order retained):

```text
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__',
'__firstlineno__', '__format__', '__ge__', '__getattribute__',
'__getstate__', '__gt__', '__hash__', '__init__', '__init_subclass__',
'__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__',
'__reduce_ex__', '__repr__', '__setattr__', '__sizeof__',
'__static_attributes__', '__str__', '__subclasshook__', '__weakref__',
'value']
```

The remaining output was `<class 'type'>`, `7`, `2788779862080`. At `00-20-00.png`, lines 1–29 match this numbered excerpt except for the following changed source line:

```text
 2 |     value = 11
```

The new run displayed the directory ending in `'value'`, then `<class 'type'>`, `11`, `1510962937584`. The unchanged spelling but changed printed number is the useful experimental control.

### A.6 · `demo.py` — move the quote boundaries before interpreting the IDs (0:21:00–0:22:00)

**`00-21-00.png`, lines 1–13.** Editor-only transitional state (modified-buffer indicator visible). The class still owns 11. Here lines 4–7 are inside one string, lines 9–10 are active and line 11 begins a further quoted region. The terminal still shows the previous 11 run.

```text
 1 | class Item:
 2 |     value = 11
 3 | '''
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | print(id(Item))
 8 | '''
 9 | a = Item()
10 | print(id(a))
11 | '''
12 | b = Item()
13 | print(id(b))
```

**`00-21-27.png`, lines 3–14.** In this edit a delimiter on line 7 now releases the class-ID print. Lines 9/12 enclose the `a` experiment instead; the visible `b` lines following 12 are outside that string. Do not infer a successful execution of those `b` lines: the terminal shows a one-number run, then a completely separate parser failure. Its line-33 text is established by the terminal, not by extrapolating down from the editor.

```text
 3 | '''
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | '''
 8 | print(id(Item))
 9 | '''
10 | a = Item()
11 | print(id(a))
12 | '''
13 | b = Item()
14 | print(id(b))
```

Separate launch results (0:21:27; full error legible in `00-24-00.png` scrollback): first `2604214551952`; then:

```text
  File "C:\CSCS1240F26\demo.py", line 33
    '''
    ^
SyntaxError: unterminated triple-quoted string literal (detected at line 35)
```

**`00-22-00.png`, lines 7–14.** Line 9 is now blank. The class and instance IDs can both print; line 12 opens the string containing the later material. This run printed `1434179590848` followed by `1434183959952`.

```text
 7 | '''
 8 | print(id(Item))
 9 |
10 | a = Item()
11 | print(id(a))
12 | '''
13 | b = Item()
14 | print(id(b))
```

### A.7 · `demo.py` — add the object display, then change the formatting of one ID at a time (0:23:00–0:33:00)

**`00-23-00.png`, lines 1–15.** Adding `print(a)` shifts the instance ID to line 12 and the next delimiter to line 13. Reading the probes in order prevents accidentally comparing the class ID with the instance address.

```text
 1 | class Item:
 2 |     value = 11
 3 | '''
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | '''
 8 | print(id(Item))
 9 |
10 | a = Item()
11 | print(a)
12 | print(id(a))
13 | '''
14 | b = Item()
15 | print(id(b))
```

Observed output, particularly clear at `00-24-00.png` and `00-25-00.png`:

```text
1377438455616
<__main__.Item object at 0x00000140B5FE86E0>
1377442891488
```

**Line 8, error then correction.** This is not an error from `print(a)`. The first edit supplied too few closing parentheses; the displayed traceback points to line 8 and reports `SyntaxError: '(' was never closed`. Compare the line as shown by the traceback at `00-26-00.png` with the corrected line as shown in the editor at `00-27-00.png`:

```text
 8 | print(hex(id(Item))
```

Corrected active region:

```text
 8 | print(hex(id(Item)))
 9 |
10 | a = Item()
11 | print(a)
12 | print(id(a))
13 | '''
```

Now only the class ID is explicitly formatted in hex. Observed corrected run:

```text
0x13f95960380
<__main__.Item object at 0x0000013F95D386E0>
1372608235232
```

**Line 12, separate error then correction.** The instruction to “keep these consistent” prompted the second edit. The traceback visible at `00-28-55.png` shows this *different* malformed line and again reports `SyntaxError: '(' was never closed`:

```text
12 | print(hex(id(a))
```

In the corrected editor at `00-29-00.png` the terminal overlaps the start of line 12; its readable tail is `t(hex(id(a)))`. The main teaching version reconstructs the full call as `print(hex(id(a)))` from that tail, the spelling in the traceback, and the new output. This is not a wholly visible line at 0:29:00. At 0:39:00, line 12 is independently legible as `print(hex(id(a)))` in the later editor view. The class-ID call on line 8 remains `print(hex(id(Item)))`.

Observed corrected run, after the line-12 error, not the earlier line-8 repair:

```text
0x29833fada80
<__main__.Item object at 0x0000029834448590>
0x29834448590
```

At `00-30-00.png`, five leading zeros are highlighted in this last instance display. From `00-31-00.png` through `00-33-00.png`, the selected field is instead `0000013F95D386E0` in the *preceding* mixed-base output. Its width, not the lower run's class identity, is what the class counts.

### A.8 · `demo.py` — the lower quoted region becomes a scratchpad (0:34:00)

**`00-34-00.png`, lines 13–36.** This is a single lower-editor view, not the unseen bottom of the earlier failed script. It shows the two matching triple quotes and the planned class/instance probes between them. Near the bottom, `information = data + meaning` is a note; `0001` is the first four-bit group being worked by hand. Neither is terminal output, and neither line executes inside this string.

```text
13 | '''
14 | b = Item()
15 | print(id(b))
16 |
17 | print(a.value)
18 | print(b.value)
19 |
20 | a.value = 12
21 | Item.value = 11
22 |
23 | print(a.value)
24 | print(b.value)
25 |
26 | print(type(a))
27 | print(type(a).value)
28 |
29 | print(type(dir))
30 | print(type(id))
31 | print(type(type))
32 |
33 | information = data + meaning
34 | 0001
35 |
36 | '''
```

Lines 14–31 retain the material the instructor is *not* testing now. Lines 33–34 show what the number-system discussion added to this region. At this moment only `13` is selected in the earlier terminal address, and only `0001` is typed on line 34; the following four-bit groups belong to the discussion or later editor states.

### A.9 · `demo.py`: quoted scratchpad and the one-instance driver (35:00–43:00)

Numbers before `|` are editorial copies of the Sublime gutter, not Python to type. This is an **assembled visual reconstruction**, not a separately recovered file: lines 1–29 are legible at 39:00 and the overlapping tail at 37:00 supplies lines 30–36. Lines 19–29 agree; 35:00 and 36:00 independently show the opening and closing delimiters at 13 and 36. The scratchpad's final digits are visible at 37:00; they are below the 39:00 viewport. The visible overlap supports the combination, not a claim that an unseen save or execution took place.

```text
 1 | class Item:
 2 |     value = 11
 3 | '''
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | '''
 8 | print(hex(id(Item)))
 9 |
10 | a = Item()
11 | print(a)
12 | print(hex(id(a)))
13 | '''
14 | b = Item()
15 | print(id(b))
16 |
17 | print(a.value)
18 | print(b.value)
19 |
20 | a.value = 12
21 | Item.value = 11
22 |
23 | print(a.value)
24 | print(b.value)
25 |
26 | print(type(a))
27 | print(type(a).value)
28 |
29 | print(type(dir))
30 | print(type(id))
31 | print(type(type))
32 |
33 | information = data + meaning
34 | 0001001100011111
35 |
36 | '''
```

Lines 1–2 create the class. The string at 3–7 keeps earlier probes from running. Line 8 prints the class ID; 10–12 create one instance and show it two ways. The string beginning at 13 means that neither the second instance nor the later type probes run, and that line 33 is a teaching formula rather than an attempted addition. Line 34 changes in the following observed states; the gutters label **separate revisions of the same scratch line**, not successive Python statements:

| Frame | Gutter | Exact scratch text |
|---|---:|---|
| 35:00 | 34 | `0001001100` |
| 36:00 and event 36:09 | 34 | `000100110001` |
| 37:00 | 34 | `0001001100011111` |

At 38:00–43:00 the last completed PowerShell `py .\demo.py` run ends with these three lines, not with the scratch string:

```text
0x29833fada80
<__main__.Item object at 0x0000029834448590>
0x29834448590
```

### A.10 · `demo.py`: what the old terminal history establishes (38:00 and 44:00–47:00)

At 38:00 the scrollback above that final run preserves several *previous* invocations of **`py .\demo.py`**. Read down the table in the order visible; each row represents a separate invocation. The two misses are genuine parse errors in earlier saved states, not failures of the corrected line in the editor alongside them.

| Earlier run in the 38:00 scrollback | Actual output, in order (semicolons separate display lines) |
|---|---|
| Two numbers | `1434179590848`; `1434183959952` |
| Added object display | `1377438455616`; `<__main__.Item object at 0x00000140B5FE86E0>`; `1377442891488` |
| Malformed class-ID expression | Line 8, `print(hex(id(Item))` → `SyntaxError: '(' was never closed` |
| Hex class ID, decimal instance ID | `0x13f95960380`; `<__main__.Item object at 0x0000013F95D386E0>`; `1372608235232` |
| Malformed instance-ID expression | Line 12, `print(hex(id(a))` → `SyntaxError: '(' was never closed` |

At 44:00 the terminal is scrolled back still farther. At the top, the visible tail of a run is `<class 'type'>`, `7`, `2788779862080`; the start is off-screen. The next command shows a directory listing, then `<class 'type'>`, `11`, `1510962937584`. Here is the observed list, rewrapped for reading without changing the names or order:

```text
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__',
 '__firstlineno__', '__format__', '__ge__', '__getattribute__', '__getstate__',
 '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__',
 '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__',
 '__setattr__', '__sizeof__', '__static_attributes__', '__str__',
 '__subclasshook__', '__weakref__', 'value']
```

A separate invocation below it shows `2604214551952`. The next reports line 33 of `C:\CSCS1240F26\demo.py`, displays `'''` and reports `SyntaxError: unterminated triple-quoted string literal (detected at line 35)`. These are not fresh outputs from the one-instance driver at right. The reason to revisit this history is the selection in the list: `__new__` at 45:00, `__init__` at 46:00–47:00. The instructor points out the preexisting machinery without writing either method. In the neighboring editor, 40:00 places the caret after `Item()`; 41:00 selects the call, 42:00 selects `Item(` and 43:00 selects `Item`, highlighting matches. The focus changes, not the expression's executable spelling.

### A.11 · `demo.py`: activating a second instance and changing its display (48:00 and 48:15)

The earlier upper lines 1–12 remain legible in both frames. These are the distinct lower excerpts where the active code changes; the new string boundary at line 16 leaves the attribute reads below it inactive.

48:00 — decimal ID of `b`:

```text
13 |
14 | b = Item()
15 | print(id(b))
16 | '''
17 | print(a.value)
18 | print(b.value)
```

The editor has selected `hex(id(a))` in line 12 and reports ten characters copied. The last completed `py .\demo.py` at left has four outputs:

```text
0x18ef195df60
<__main__.Item object at 0x0000018EF1DF8590>
0x18ef1df8590
1713454875984
```

48:15 — hexadecimal ID of `b`, **new invocation**:

```text
13 |
14 | b = Item()
15 | print(hex(id(b)))
16 | '''
17 | print(a.value)
18 | print(b.value)
```

```text
0x23ca394c7f0
<__main__.Item object at 0x0000023CA3EA8590>
0x23ca3ea8590
0x23ca3e98550
```

Both revisions call the class again at line 14. Only the formatting of the last printed ID changes in the source. New allocations in the new run account for the different concrete numbers on the other lines. Do not convert the decimal result from the *first* run and expect it to equal the hex result from the *second*.

### A.12 · `demo.py`: expose just the first two reads, then expose the instance assignment (49:00–50:58)

49:00 — the visible changed region puts the new quotation boundary at 19. At this instant, the console still ends at the previous `0x23ca3e98550`; it does not yet show two elevens. 50:00 corroborates this source state and shows the separate six-line result below.

```text
14 | b = Item()
15 | print(hex(id(b)))
16 |
17 | print(a.value)
18 | print(b.value)
19 | '''
20 | a.value = 12
21 | Item.value = 11
22 |
23 | print(a.value)
24 | print(b.value)
25 |
26 | print(type(a))
27 | print(type(a).value)
28 |
29 | print(type(dir))
30 | print(type(id))
31 | print(type(type))
```

50:00 — observed `py .\demo.py` result. Lines 17–18 account for the last two outputs; lines 20–24 are still quoted.

```text
0x1b4575cf0b0
<__main__.Item object at 0x000001B457928590>
0x1b457928590
0x1b457918550
11
11
```

50:58 — **observed editor**, from a single frame; the boundary has moved to 25. This transcribes the visible program down to the final legible line. The closing boundary and lower lines are not in this viewport; the older closing line 36 is not assumed to persist.

```text
 1 | class Item:
 2 |     value = 11
 3 | '''
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | '''
 8 | print(hex(id(Item)))
 9 |
10 | a = Item()
11 | print(a)
12 | print(hex(id(a)))
13 |
14 | b = Item()
15 | print(hex(id(b)))
16 |
17 | print(a.value)
18 | print(b.value)
19 |
20 | a.value = 12
21 | Item.value = 11
22 |
23 | print(a.value)
24 | print(b.value)
25 | '''
26 | print(type(a))
27 | print(type(a).value)
28 |
29 | print(type(dir))
```

Here line 20 sets `value` on the first instance; line 21 **assigns**, rather than prints, `Item.value`. Lines 23–24 now read through `a` and `b`. But the last completed command in the same 50:58 terminal shows only six outputs. The address literals prove this is a different invocation from the one at 50:00, even though both end with `11` and `11`:

```text
0x19f6ba5d400
<__main__.Item object at 0x0000019F6BE28590>
0x19f6be28590
0x19f6be18550
11
11
```

No additional `12` appears in that terminal result. The editor predicts the next pair; the six printed lines cannot establish which unprinted assignments ran.

### A.13 · `demo.py`: compare the class and two instances (51:00)

The numbers before the bars are the editor gutter, not Python to type. This first state is a visible excerpt (lines 1–29 of `00-51-00.png`); later lines are offscreen. Lines 3 and 7 put the first three print statements inside a triple-quoted string. Line 25 starts a different string: the type probes below it are not active here. Do not use the later blank line 25 to fill in this earlier state.

```text
 1 | class Item:
 2 |     value = 11
 3 | '''
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | '''
 8 | print(hex(id(Item)))
 9 |
10 | a = Item()
11 | print(a)
12 | print(hex(id(a)))
13 |
14 | b = Item()
15 | print(hex(id(b)))
16 |
17 | print(a.value)
18 | print(b.value)
19 |
20 | a.value = 12
21 | Item.value = 11
22 |
23 | print(a.value)
24 | print(b.value)
25 | '''
26 | print(type(a))
27 | print(type(a).value)
28 |
29 | print(type(dir))
```

Lines 8–15 distinguish the class from the two newly created instances. Compare the object representation on line 11 with the hexadecimal identity on line 12: in this classroom run, the digits for `a` match apart from case and padding. Lines 17–24 test a different question: reading a shared attribute and then giving `a` a value of its own. The editor has the final two prints by 51:00, but the **last run then visible** ends in just `11`, `11` (its class identity is `0x19f6ba5d400`). The later run ending in four values is visible at 52:00. These are not the same execution.

Observed comparison run, visible at 52:00. The first four lines after the command identify the class, display the first item, show that item's identity, then show the second item's identity. Addresses are historical observations, not expected constants.

```text
PS C:\CSCS1240F26> py .\demo.py
0x224dee8f220
<__main__.Item object at 0x00000224DF408590>
0x224df408590
0x224df3f8550
11
11
12
11
```

### A.14 · `demo.py`: reactivate the type probes (52:00–54:00)

*Assembled visual reconstruction:* `00-53-00.png` supplies lines 1–29; `00-52-35.png` supplies 30–38 and displays the overlap at 10–29. The overlap agrees, including the now-blank line 25; the join describes the compatible editor views, not unseen keystrokes. The root-level notes at lines 33–34 are data inside triple quotes, not active arithmetic or a Python binary literal.

```text
 1 | class Item:
 2 |     value = 11
 3 | '''
 4 | print(dir(Item))
 5 | print(type(Item))
 6 | print(Item.value)
 7 | '''
 8 | print(hex(id(Item)))
 9 |
10 | a = Item()
11 | print(a)
12 | print(hex(id(a)))
13 |
14 | b = Item()
15 | print(hex(id(b)))
16 |
17 | print(a.value)
18 | print(b.value)
19 |
20 | a.value = 12
21 | Item.value = 11
22 |
23 | print(a.value)
24 | print(b.value)
25 |
26 | print(type(a))
27 | print(type(a).value)
28 |
29 | print(type(dir))
30 | print(type(id))
31 | print(type(type))
32 | '''
33 | information = data + meaning
34 | 0001001100011111
35 |
36 | '''
37 |
38 |
```

Lines 26–27 distinguish “what is `a`?” from “what is the value on its class?” Lines 29–31 ask about `dir`, `id`, and `type` themselves, without invoking the first two. The later comparison-and-type run is now visible at 52:35; its leading addresses differ from the comparison-only run.

```text
PS C:\CSCS1240F26> py .\demo.py
0x1f4b7d3d390
<__main__.Item object at 0x000001F4B82E8590>
0x1f4b82e8590
0x1f4b82d8550
11
11
12
11
<class '__main__.Item'>
11
<class 'builtin_function_or_method'>
<class 'builtin_function_or_method'>
<class 'type'>
```

### A.15 · `demo.py`: clear the driver and begin the initializer (55:15–56:00)

Two distinct in-progress buffers. `00-55-15.png` shows only the class and its class attribute after the longer driver disappears. At `00-56-00.png`, the initializer has replaced the class attribute, but the assignment still has no right-hand expression. Neither image shows either draft being run; the older PowerShell text is unchanged.

At 55:15:

```text
 1 | class Item:
 2 |     value = 11
 3 |
```

At 56:00:

```text
 1 | class Item:
 2 |     def __init__(self, value):
 3 |         self.value = 
 4 |
```

### A.16 · `demo.py`: no explicit value (57:00–57:15)

`00-57-00.png` shows a complete assignment inside the initializer and a call with empty parentheses outside the class. `py .\demo.py` is present on the input line at 57:00; the traceback is visible at `00-57-15.png`. Line 5 fails before `a` can receive a result, so line 6 does not print an old or default value.

```text
 1 | class Item:
 2 |     def __init__(self, value):
 3 |         self.value = value
 4 |
 5 | a = Item()
 6 | print(a.value)
 7 |
```

```text
PS C:\CSCS1240F26> py .\demo.py
Traceback (most recent call last):
  File "C:\CSCS1240F26\demo.py", line 5, in <module>
    a = Item()
TypeError: Item.__init__() missing 1 required positional argument: 'value'
```

### A.17 · `demo.py`: supply the string (58:00–58:12)

In `00-58-00.png`, the argument is already in the editor; the terminal error alongside it still quotes the *old* `a = Item()`. `00-58-12.png` adds a new invocation and the first `abc`. Line 3 remembers the argument on the new object, and line 6—not line 3—produces the output.

```text
 1 | class Item:
 2 |     def __init__(self, value):
 3 |         self.value = value
 4 |
 5 | a = Item("abc")
 6 | print(a.value)
 7 |
```

```text
PS C:\CSCS1240F26> py .\demo.py
abc
```

### A.18 · `demo.py`: rename the first parameter (59:00 through the closing explanation)

`00-59-00.png` and `00-59-02.png` show `me` at lines 2–3 and a second `abc` after a second successful invocation. The class-call syntax and the printed attribute have not changed. The final editor highlights at `01-02-00.png` and `01-03-00.png` point out the string argument and the repeated `value` spelling without introducing another revision.

```text
 1 | class Item:
 2 |     def __init__(me, value):
 3 |         me.value = value
 4 |
 5 | a = Item("abc")
 6 | print(a.value)
 7 |
```

```text
PS C:\CSCS1240F26> py .\demo.py
abc
```

At `01-00-00.png` the terminal is looking back: `__new__` is selected in an older name list; at `01-01-00.png` it is `__init__`. The old list runs end respectively in `<class 'type'>`, `7`, `2788779862080` and `<class 'type'>`, `11`, `1510962937584`; another older invocation prints `2604214551952`. Older errors also visible in scrollback include `AttributeError: type object 'Item' has no attribute 'value'` and a triple-quoted-string `SyntaxError` pointing to line 33. These do not come from the six-line `me` version and are not additional runs of it. At `01-03-47.png` OBS is in front of the editor with its Stop Recording control visible; the still does not establish a click.

## Micro-Learning Insights · removable appendix

- **Give a number its question back.** Before interpreting terminal digits, find the expression inside `print`. In this lesson `Item.value` and `id(Item)` both produce integers but answer different questions.
- **Use indentation as evidence.** Before reasoning about `value = 7`, ask whether it is inside the class. An indented `pass` can satisfy the parser; it cannot turn an unindented assignment into a class attribute.
- **Change one recognizable value.** The instructor changes 7 to 11 and reruns. That distinguishes a live connection to `Item.value` from a coincidental 7 in earlier output.
- **Read nested calls inside out.** In `print(hex(id(a)))`, identify the object, convert its identity integer to text, then display the text. Conversion did not make another `Item`.
- **Compare within the same run.** A padded uppercase address and a shorter lowercase hex number can represent the same integer. Discard neither until you have accounted for base, case, and leading zeros.
- **Convert four bits at a time.** For the instructor’s discussed hex prefix `13F`, retain one four-bit group per numeral: `0001 0011 1111`. The final `F` uses all four place weights. Do not copy the extra `0001` group from the editor scratchpad into that conversion.
- **Read the left side of the dot.** After `a.value = 12`, `type(a).value` still asks the class for its value. Changing the expression from `a` to `type(a)` changes where you look.
- **Predict the next line that will not run.** If `Item()` fails during initialization, assignment to `a` does not complete and the following print never displays a value. Start debugging at the reported call, not the absent output.
- **Rename the receiver as a test, not a style recommendation.** The instructor’s alternate `me` works when the body uses it consistently. Use `self` in ordinary Python so other readers recognize the role immediately.
- **Distinguish a short-lived name from lasting data.** `value` disappears as a local parameter when the method finishes; the object still has its attribute. Matching spellings on the two sides of `self.value = value` do not make them the same storage location.
