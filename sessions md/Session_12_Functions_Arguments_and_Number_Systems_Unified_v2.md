# Functions, arguments, and counting with unfamiliar digits

**CSCS1240F26 · Session 12 · Worked teaching companion**

At one point the program prints `doNothing("fresh")): not fresh`. Later the instructor writes `4` in a number system where that mark means *two*. Neither example is a contradiction. In the first, a label tells you what the programmer wrote, not which argument Python received. In the second, a digit gets its value from the class’s ordered alphabet, not from its appearance. In both halves of the session, the useful question is: *What rule produced this result?*

| Start | Where to turn | What you can practice |
|---|---|---|
| 0:00:00 | The project Hub and a minimal Python function | Locate work, then distinguish a missing function body from one that deliberately does nothing. |
| 0:10:17 | Inspect a function; call it; change what it returns | Predict what `dir`, `id`, `type`, `return`, and the outer `print` each inspect or produce. |
| 0:20:26 | Fresh milk, flour, and eggs | Follow the actual arguments through a changing signature, a nested decision, and two different print statements. |
| 0:46:03 | Build a place-value diagram | Use `1327` to explain positions, powers, fractional places, and the radix point. |
| 1:14:00 | Invent five digits | Count past the last digit, test whether a numeral is legal, and read `q#qa` without importing decimal digit names. |
| 1:49:09 | Count with shapes | Predict a carry from the pattern of squares, triangles, and diamonds. |
| 1:59:20 | Return to the project Hub | Choose between an issue, a pull request, or collaborator access; distinguish a drafted post from a published one. |

**Recording and evidence.** [Open Session 12 from the beginning](https://www.youtube.com/watch?v=Mn4XZ43JmJ4). The [supplied link](https://www.youtube.com/watch?v=Mn4XZ43JmJ4&t=739s) points into the same recording. The saved transcript contains 2,884 timestamped rows, from the greeting at 0:00:00 to the last sign-off cue at 2:09:42. Its export reports 2:09:46; it gives neither cue-end times nor track authorship. The archived video probe reports 2:09:52.95. Caption starts alone do not establish that speech was continuous or that the ending was silent.

**Reading the evidence.** The visual record contains 130 whole-minute targets from 0:00:00 to 2:09:00, six additional event captures, and a separately preserved 27-picture set whose last target is 2:09:20. Times attached to pictures are requested seek times, not individually measured decoded timestamps. *Observed* means visible in a sampled frame; a classroom explanation can also come from speech. *Reconstructed* identifies a connection made from compatible evidence. *Independently verified* means a separate calculation or Python run, not an extra classroom result. For the original f-string spellings that reuse double quotes inside braces, use Python 3.12 or later.

## 0:00:00–0:05:16 · A project hub: where to discuss an idea and where to change the code

Suppose you have some code ready and want a classmate to try it. The instructor’s proposal was to put the code in your own **public GitHub repository**, commit it, and share its URL. The GitHub [Hub](https://github.com/joes-ta/Hub), renamed from *Project*, was an index of those repositories, not a single repository into which everyone put their code. The README still said *Project* at 0:01:00. A **Discord forum** gave each project a friendlier place to discuss ideas and direction. The instructor described giving a project lead responsibility and inviting Bronson to help maintain the hub; the invitation was a plan, not a completed action shown on screen.

The README linked [scriptz0340/Link](https://github.com/scriptz0340/Link) and [Wolfster427/AnyBase](https://github.com/Wolfster427/AnyBase). The instructor counted eight commits in the first project. Why didn’t its completed task appear at first? GitHub’s Issues view initially showed open issues. Removing the open-only restriction revealed issue #1, **Create venv and example flet app**, marked **Closed** (0:04:00). On the second project’s Code tab (0:05:00), the single initial commit contained **`.gitignore`** and **`README.md`**. Both projects served the same goal: practice managing a repository with others, proposing changes through issues and pull requests, and letting students lead work on technologies they chose. These links and counts describe the class screen, not a current repository audit.

**Key items to scan**

- **Public GitHub repository** — holds an individual project’s shared code and saved revisions; the Hub links to it.
- **Discord forum** — for project conversation; the **anybase** forum was announced next to the AnyBase repository card.
- **Issue** — a specific task or suggestion. **Create venv and example flet app** was **Closed**, not missing.
- **Pull request** — proposes a change to a shared project; mentioned as a collaboration opportunity, not performed in this demo.
- **Commit** — a saved revision. The AnyBase Code tab showed one initial commit with **`.gitignore`** and **`README.md`**.

## 0:05:17–0:08:36 · Why a function that does nothing still needs a body

The previous class covered command-line arguments. The instructor said that was enough for a couple of upcoming challenges, but not the whole week. Earlier challenges had generally disallowed student-defined functions; now the class would write its own. In the demo repository, **`git log --oneline`** showed **`7cf1688`** (`example: user defined functions`) above **`17e38e5`** (`example: sys.argv`). The instructor isolated one piece of **`src/example.py`** at a time so a run would answer one question: what is the least Python will accept after a function header?

**Annotated teaching version — guide-added comments; shortened reproduction of the failing layout.**

```python
def doNothing():       # Name a function with no inputs; its body must follow.
    #pass              # A comment is not a statement in the body.

"""                    # Back at the left margin: this cannot be the body.
print('not active')    # Sample text inside the string, not a call that runs.
"""                    # End the sample multiline string.
```

**Observed command and error** (0:07:00 and 0:07:05; the 0:08:00 editor shows the commented body):

```text
PS C:\CSCS1240F26\F26Demo> py .\src\example.py
  File "C:\CSCS1240F26\F26Demo\src\example.py", line 4
    """
    ^^^
IndentationError: expected an indented block after function definition on line 1
```

The arrow points at line 4, but the cause is above it. **`def doNothing():`** promises an indented block. The parser skips the comment and blank line, then finds a left-aligned string instead of an indented statement. Nothing in the script runs. The instructor used **`"""`** delimiters to set examples aside: the enclosed lines are string contents, not executed statements. This is not special block-comment syntax. A string in the documentation position, for example the first statement *inside* a function, can instead serve as its **docstring**. Here the string is outside the function.

**Key items to scan**

- **`git log --oneline`** — puts a short identifier and description on each revision’s row: **`7cf1688`** follows the **`17e38e5`** command-line-arguments example.
- **`src/example.py`** — the edited file; **`py .\src\example.py`** ran the saved file from the demo repository.
- **`def doNothing():`** — defines the name and empty parameter list; the colon requires a body.
- **`#pass`** — a comment; its indentation does not make it an executable body.
- **`"""`** — starts or ends a triple-quoted string. A **docstring** documents its enclosing object in a particular position; not every such string is a docstring.
- **`IndentationError`** — Python reached unindented line 4 still waiting for the body promised on line 1.

## 0:08:37–0:10:16 · Two legal placeholders, neither of which prints anything

A student wondered whether **`pass`** meant the function had succeeded. Think instead of passing your turn: it is a real statement that deliberately takes no action. Removing the comment marker gives Python the body it requires. The next edit replaces that one statement with three periods, **`...`**. Read these as two separate versions.

**Annotated teaching version — guide-added comments; first body (0:09:00).**

```python
def doNothing():       # Define the function; don't run its body yet.
    pass               # If called, take no action and reach the end.
```

**Annotated teaching version — guide-added comments; replacement body (0:09:30).**

```python
def doNothing():       # Same no-input function in the replacement version.
    ...                # Evaluate the ellipsis literal, discard it, reach the end.
```

**Result:** no output from either definition-only script (independently verified). After the original error, the classroom terminal shows one silent script invocation by 0:09:30 and two by 0:10:00; the instructor reports running both versions. A silent command returning to the shell is not a printed success message. Neither definition calls its function. If called, either body finishes normally and implicitly returns **`None`**; the ellipsis body does *not* return the ellipsis object.

**Key items to scan**

- **`pass`** — a keyword used as a no-operation statement, not a test result.
- **`...`** — a literal value used here as a standalone expression; not the keyword **`pass`**.
- **`None`** — what the caller receives when either body reaches its end without an explicit return value.

## 0:10:17–0:14:26 · Which names can you inspect in interactive Python?

In a second PowerShell window, the instructor started Python with **`py`** so each expression could be tried on its own. First, entering **`dir`** displayed the built-in function itself; **`dir()`** actually called it. With no argument it listed names available in that interactive scope—not the attributes of the function named **`dir`**. With an object argument, the same tool lists attribute names for that object. The instructor compared the list to a telephone directory or a shell directory listing: it tells you what names are there rather than executing each named thing.

**Annotated teaching version — guide-added comments; each line is a separate interactive input, so the syntax error does not cancel the later commands.**

```python
dir                    # Show the function object without calling it.
dir()                  # List names in this interactive scope.
type(dir)              # Ask what kind of object the built-in function is.
dir(pass)              # Invalid: a statement cannot occupy the argument slot.
dir(...)               # Valid: the three dots are a value with attributes.
type(...)              # Identify that value's type: ellipsis.
```

**Observed REPL results** (0:11:00–0:13:00; long attribute list deliberately excerpted):

| Input | Displayed result |
|---|---|
| `dir` | `<built-in function dir>` |
| `dir()` | `['__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']` |
| `type(dir)` | `<class 'builtin_function_or_method'>` |
| `dir(pass)` | `SyntaxError: invalid syntax`; the carets mark `pass`. |
| `dir(...)` | A list containing, for example, `'__class__'`, `'__dir__'` and `'__repr__'`. |
| `type(...)` | `<class 'ellipsis'>` |

The failure happens before **`dir(pass)`** can call anything: an argument position requires an expression that supplies a value. **`pass`** is a statement, like the statement forms introduced by **`if`** and **`for`**. **`...`** *does* supply an object, although as a line by itself in a script it has no visible effect. A student likened the dots to a pause; the instructor preferred “to be continued”: use the dots to suggest an implementation will follow, and **`pass`** if there is no definite plan. That distinction is a writing preference, not a language-enforced schedule.

**Key items to scan**

- **`py`** — with no filename, launched the interactive Python prompt; here results of bare expressions can be displayed automatically.
- **`dir`** vs. **`dir()`** — the first names a function object; the second calls it with no argument and lists the current scope’s names.
- **`type(dir)`** — identifies an instance of the built-in-function-or-method type, not a class definition written by the instructor.
- **`dir(pass)`** — gives **`SyntaxError`** because **`pass`** cannot supply a value, just as an **`if`** or **`for`** statement cannot be passed as an argument.
- **`dir(...)`** — succeeds because **`...`** denotes an object; **`type(...)`** reports **`ellipsis`**.

## 0:14:27–0:18:34 · Inspect the function itself, then call it

What changes when you put parentheses after **`doNothing`**? Without them you can inspect the function’s attributes, type and identity. With them you *call* it and get the result of its body. The instructor made the four print statements active, returned to the terminal, and noticed a save had been missed. The point of running again after saving is to test the editor version you are looking at—not to explain a previous run from the newly edited text.

**Annotated teaching version — guide-added comments; active lines at 0:15:00.**

```python
def doNothing():                                      # Create a no-input function object.
    pass                                              # The body does nothing when called.

print(f"dir(doNothing): {dir(doNothing)}\n")          # Print the attribute names, not their values.
print(f"type(doNothing): {type(doNothing)}\n")        # Print the function's type.
print(f"id(doNothing): {id(doNothing)}\n")            # Print its identity for this run.
print(f"call of doNothing(): {doNothing()}\n")        # Call it; print the returned None.
```

**Observed** after **`py .\src\example.py`** (0:15:00–0:17:00). The first line is abbreviated; the other three lines are literal:

```text
dir(doNothing): ['__annotate__', '__annotations__', …, '__type_params__']

type(doNothing): <class 'function'>

id(doNothing): 2227746256176

call of doNothing(): None
```

The text before each colon is a label the instructor typed. Inside each f-string, Python evaluates what is in **`{...}`**, inserts the result, and only then lets **`print()`** display the completed text. **`\n`** adds a newline inside the string; the print call adds its own line ending. The identity number describes this live function object, not its return value or a number other runs must reproduce. A class was compared to a blueprint: the type describes a kind of object; an *instance* is one actual object of that kind. To *instantiate* means to create an instance. The word **`function`** in this output names the type to which this particular function object belongs.

A function that completes normally returns a value even when its author has not supplied one. Here the value is **`None`**. That is not a missing object or the string `"None"`. The instructor discusses inspecting it with **`dir(None)`** and **`type(None)`**; the sampled 0:18:00 REPL still shows the earlier ellipsis commands. The following short check verifies the distinction separately.

**Annotated teaching version — guide-added comments; independent check, not class scrollback.**

```python
print('__class__' in dir(None))   # Does the list of attributes include this name? True.
print(type(None))                 # The type of the value None is NoneType.
```

**Independently verified output:**

```text
True
<class 'NoneType'>
```

**Key items to scan**

- **`doNothing`** — the function object. **`dir(doNothing)`**, **`type(doNothing)`**, and **`id(doNothing)`** inspect it without running its body.
- **`function`** — the type reported for that user-defined function, as distinct from the earlier built-in-function type.
- **`doNothing()`** — calls the function and gets **`None`**; no output comes from the body.
- **`{...}`** — in an f-string, evaluates the expression between the braces; the surrounding label is ordinary text.
- **`print()`** and **`\n`** — print displays the completed string; the extra newline separates the demonstration results.
- **`dir(None)`** reveals attribute names; **`type(None)`** gives **`NoneType`**, the type, not the value **`None`** itself.

## 0:18:35–0:20:25 · Make the return value explicit, then change it

Could the result be **`None`** just because the print statement supplied that word? The instructor put the earlier inspection code inside a string and activated a new function. This time the function says exactly what to return. Then a different function returns a number. Compare the two independent program states, not two functions that happened to run in the same invocation.

**Annotated teaching version — guide-added comments; explicit return at 0:19:00.**

```python
def doNothing():                                     # No inputs in this version either.
    return None                                      # End the call and hand back None explicitly.
print(f"call of doNothing()): {doNothing()}\n")      # Print None; the extra ) is in the label.
```

**Observed later in terminal history at 0:20:00:** `call of doNothing()): None`.

**Annotated teaching version — guide-added comments; number-returning version at 0:20:00.**

```python
def douglasAdams():                                      # Define a different no-input function.
    return 42                                            # Hand the integer 42 back to the caller.
print(f"call of douglasAdams()): {douglasAdams()}\n")    # Insert 42 into the label and print it.
```

**Observed at 0:20:00:** `call of douglasAdams()): 42`. In both versions the extra closing parenthesis before the colon is literally inside the string; it is not a call with mismatched parentheses. Changing **`return None`** to a function with **`return 42`** changes the *value supplied to the caller*. Neither return statement prints. A caller could use that value without displaying it at all.

**Key items to scan**

- **`return None`** — explicitly sends back the same value the earlier no-op body supplied implicitly.
- **`return 42`** — sends back the integer, not the characters `"42"` and not terminal output.
- **`douglasAdams()`** — the expression inside the braces that produces the **`42`** displayed by the outer print.

## 0:20:26–0:21:59 · The input’s name is not the input’s value

Up to now the parentheses in each function header have been empty. In **`def doNothing(milk):`**, the word **`milk`** gives the function a name to use for an incoming value. Is it an argument or a parameter? At the *definition* it is a **parameter**. In **`doNothing("fresh")`**, the value supplied at the *call* is the **argument**. It need not be written as a literal: what matters to the comparison is the value supplied, not how the caller obtained it.

**Annotated teaching version — guide-added comments; active source at 0:21:00; the nested double quotes are preserved as shown and were tested with Python 3.14.**

```python
def doNothing(milk):                                  # Name this call's incoming value milk.
    if milk == "fresh": return "spoiled"              # Equal strings: hand back the new string.
    else: return milk                                 # Otherwise hand the incoming value back unchanged.
print(f"doNothing(\"fresh\")): {doNothing("fresh")}\n")  # Call with fresh; show the returned spoiled.
```

**Independently verified output** for those lines, not the terminal output shown beside the 0:21:00 editor:

```text
doNothing("fresh")): spoiled
```

Read from the braces outward: the argument supplies `"fresh"`; **`milk == "fresh"`** compares values; the true branch returns `"spoiled"`; the outer print displays it. The function did not mutate the argument or turn a container of milk into anything. The **`else`** branch in the editor returns its input. At this point in the recording the terminal still shows the earlier 42 result; the branch demonstration continues next.

**Key items to scan**

- **`def doNothing(milk):`** — **`milk`** is the **parameter**, a local name in the definition.
- **`doNothing("fresh")`** — `"fresh"` is the **argument**, the actual value supplied for this call.
- **`milk == "fresh"`** — tests equality, not assignment. Equal strings take the branch returning `"spoiled"`.
- **`else`** — for inputs not equal to `"fresh"`, return the input rather than replacing it.

## 0:22:00–0:26:46 | Read the call, not its label

If you do nothing with fresh milk long enough, it spoils. That joke makes the first rule memorable: the function looks for the exact string **`"fresh"`**. It does not look for that word *somewhere inside* a longer string. Its parameter **`milk`** names whichever object the caller supplies on this call. It does not hold real milk or require a string. [Transcript 0:22:01–0:23:42; editor 0:22:00 and 0:23:20.]

**Annotated teaching version — guide-added comments.** These are successive replacements for the print line, grouped here to compare them; in class they were separate runs. The unusual extra closing parenthesis belongs to the printed label. The classroom f-strings reuse the enclosing quote character inside the replacement field. The following caller replacements use the same function definition. Separately tested outputs use Python 3.14.9; they are not additional classroom runs.

```python
def doNothing(milk):                        # Bind the supplied object to milk for this call.
    if milk == "fresh": return "spoiled"    # Exactly equal? Send back a different string.
    else: return milk                       # Otherwise send back the original object.
print(f"doNothing(\"fresh\")): {doNothing("fresh")}\n")           # First run: fresh → spoiled.
print(f"doNothing(\"fresh\")): {doNothing("not fresh")}\n")       # Changed call, old label: not fresh.
print(f"doNothing(\"not fresh\")): {doNothing("not fresh")}\n")   # Repair the wording, not the rule.
print(f"doNothing(\"not fresh\")): {doNothing(27)}\n")            # New input; old label again. Returns integer 27.
```

Observed result lines in order (0:23:00, 0:23:20, 0:24:00, 0:26:00; intervening prompts/blank lines omitted):

```text
doNothing("fresh")): spoiled
doNothing("fresh")): not fresh
doNothing("not fresh")): not fresh
doNothing("not fresh")): 27
```

In the second line, the words before the colon are ordinary text in an f-string, not a record Python generates about the call. The quoted part is not a Python comment, even though the instructor describes it as “my comment.” Python evaluates what is inside **`{...}`**, gets the returned object, formats it into the string, and only then does the outer **`print()`** display the line. The call with **`27`** neither changes the number to a string inside the function nor crashes: integer 27 and string `"fresh"` compare unequal, so the original integer comes back. Displaying it as text happens afterward.

Before running the numeric example the class is asked to distinguish three questions: Can Python parse it? If so, does execution raise an exception? If it finishes, did it do what the author *meant*? A misleading label can survive both parsing and execution. The instructor uses this to motivate checking incoming types when a function needs a particular type; no check is added here. [Transcript 0:24:18–0:26:40.]

**Key items to scan**

- **`milk`** — parameter name in the definition; bound anew each call, not declared as a string.
- **`"fresh"`** — exact string that selects the special result; `"not fresh"` is different.
- **`==`** — asks about equality; here comparing integer **`27`** with the string is valid and false.
- **`return milk`** — gives the caller the original object on the unequal path.
- **`{...}`** — evaluated part of an f-string; compare the call inside it with the hard-coded label outside.
- **`print()`** — displays the completed string; `\"` embeds a quote in its label and `\n` adds a newline before print's own line ending.

## 0:26:51–0:31:47 | Two required ingredients: an omitted argument is not `None`

The new version asks for milk *and* flour. **`milk`** and **`flour`** are parameters in the definition; **`"fresh"`** and **`"white"`** are the arguments in the first call. Without names in the call, position decides what goes where. The professor corrects “two arguments” to “two parameters” while looking at the definition. The returned word actually on screen is **`"matzah"`**. Notice that adding flour did more than introduce a second test: nonfresh milk now returns `"spoiled"`, whereas the earlier one-parameter definition returned the nonmatching input unchanged. [Transcript 0:26:51–0:29:21; editor 0:27:00–0:29:00.]

**Annotated teaching version — guide-added comments.** The first two tests below are separate calls to the *same* definition, not two prints made by one classroom run. The label here has only one closing parenthesis before the colon.

```python
def doNothing(milk, flour):                    # Both positions need supplied values.
    if milk == "fresh":                        # Only fresh milk reaches the flour question.
        if flour != None: return "matzah"      # White flour is not None: take this return.
        else: return milk                      # Explicit None with fresh milk: return "fresh".
    else: return "spoiled"                     # Nonfresh milk: skip the inner test entirely.
print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", "white")}\n")  # First version of caller.
```

Observed by 0:30:00: `doNothing("fresh", "white"): matzah`. The terminal also shows an extra previous result of 27. The instructor says the file had not been saved before one run (0:29:11–0:29:16). The unchanged older result does not describe the newly visible branch.

Now remove the second argument but leave the function and the label alone. The failure happens *before* the first condition in the body can run:

```python
print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh")}\n")  # Cannot bind flour; outer print never runs.
```

Observed traceback ends at 0:31:00:

```text
TypeError: doNothing() missing 1 required positional argument: 'flour'
```

A student considers the two inputs when predicting whether one will work. The diagnosis names the exact missing parameter. Supplying **`None`** instead is not another spelling of *omit*: it is an actual Python object that fills the slot. In the same required-parameter version, replace the failing line with:

```python
print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", None)}\n")  # Bind flour to None; return milk.
```

Observed by 0:32:00: `doNothing("fresh", "white"): fresh`. Only the actual call changed, so the displayed `"white"` is stale for both the error line and the successful retry. The 0:31:00 editor already shows the new argument while the terminal still shows the missing-argument traceback. [Transcript 0:29:28–0:31:35.]

| Actual arguments | What is bound | Outer check | Inner check | Outcome |
|---|---|---|---|---|
| `"fresh", "white"` | milk = fresh; flour = white | True | `"white" != None` is True | Return `"matzah"`; caller prints it. |
| `"fresh"` only | No complete binding for this required pair | Not reached | Not reached | Raise `TypeError`; no labeled result. |
| `"fresh", None` | milk = fresh; flour = None | True | `None != None` is False | Return `milk`, here `"fresh"`; caller prints it. |

**Key items to scan**

- **`milk`**, **`flour`** — first and second parameters respectively; both required in this version.
- **`"fresh"`**, **`"white"`** — positional arguments; the *second* one binds flour regardless of its spelling.
- **`if flour != None`** — nested inequality check. **`"matzah"`** is the return value for fresh milk and white flour.
- **`None`** — actual value meaning no flour in this example, sufficient to satisfy the required parameter.
- **`TypeError`** — this call is grammatical, but Python cannot supply the missing `flour` at runtime.

## 0:31:49–0:36:45 | Make omission legal, then distinguish two identical results

The instructor connects positional arguments with familiar imperative languages, then gives the second parameter a fallback. In the new definition, **`flour=None`** means a caller *may* omit flour; it can still supply a second positional argument. Notice a second change at the same transition: the inner **`else`** initially stops returning milk. [Transcript 0:32:03–0:34:36; editor 0:32:00–0:35:00.]

**Annotated teaching version — guide-added comments.** First defaulted version:

```python
def doNothing(milk, flour=None):               # One supplied value is now enough: use None for flour.
    if milk == "fresh":                        # True for this test; do not jump to the outer else.
        if flour != None: return "matzah"      # False: the omitted second value defaulted to None.
        else: return "spoiled"                 # This inner else supplies the actual result here.
    else: return "spoiled"                     # Same word, different path; not reached on this call.
print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh")}\n")  # Only one actual argument.
```

Observed by 0:34:00: `doNothing("fresh", "white"): spoiled`. The result proves neither that the milk failed its test nor that the caller supplied white flour. Two different paths have the same result. To tell them apart, change the inner fallback to return the milk; now a fresh input with no flour can be distinguished from a nonfresh input. Keep the default in place.

**Annotated teaching version — guide-added comments.** The three caller lines below are a teaching replay of successive tests, not one classroom driver. The `"whole wheat"` test with `"fresh"` is reconstructed from the spoken edit and the successive results; the complete intermediate call is not pictured. This replay uses short outer prints to expose just the returned values.

```python
def doNothing(milk, flour=None):               # Omitting the second value still supplies None.
    if milk == "fresh":                        # Only this branch asks about the flour.
        if flour != None: return "matzah"      # Explicit whole wheat overrides the fallback.
        else: return milk                      # Changed line: fresh with no flour now returns "fresh".
    else: return "spoiled"                     # Still the outcome whenever milk is not exactly "fresh".
print(doNothing("fresh"))                      # Omitted flour → None → inner else → fresh.
print(doNothing("fresh", "whole wheat"))       # Fresh and flour supplied → matzah.
print(doNothing("xfresh", "whole wheat"))      # First condition fails → spoiled; flour is never tested.
```

Independently verified short-print output:

```text
fresh
matzah
spoiled
```

Classroom scrollback at 0:36:00 has the same result order, but every line starts with the still-unchanged label `doNothing("fresh", "white"):`. The screen at 0:35:00 catches a trailing comma after the single actual argument as the instructor starts the next edit; it does not show an already-completed whole-wheat call. The instructor says the challenges that week could use straightforward positional arguments rather than these extra features; this is historical guidance, not a verified assignment contract. [Transcript 0:34:37–0:36:36.]

**Key items to scan**

- **`flour=None`** — default in the definition: omitted flour gets `None`; an explicit second argument takes priority.
- **`else`** — indentation decides which test owns it. The inner fallback handles fresh/no-flour; the outer one handles nonfresh milk.
- **`return "spoiled"`** — initially appears twice; the output alone does not identify the branch.
- **`return milk`** — changing the inner fallback makes the one-argument fresh result distinguishable.
- **`"xfresh"`** — forces the outer fallback; `"whole wheat"` is bound, but not inspected by that branch.

## 0:36:45–0:40:20 | Add eggs: definition errors first, positional surprise next

A student suggests **`eggs`** for a third ingredient. The instructor tries leaving it required *after* the already-defaulted flour. Python rejects the definition itself. A student suggests quotes. That creates a different problem: a string literal is a value one could pass in a call, not a parameter name one can declare here. The failures are separate attempts in order, not consecutive lines in a working script. [Transcript 0:37:12–0:38:56; terminal 0:38:20.]

**Annotated teaching version — guide-added comments.** These are two deliberately invalid header examples, each with a guide-added placeholder body so it can be tested in isolation:

```python
def doNothing(milk, flour=None, eggs):         # Invalid: a required positional parameter follows a default.
    pass                                      # Not executed; the header fails to compile.
```

Observed: `SyntaxError: parameter without a default follows parameter with a default`.

```python
def doNothing(milk, flour=None, "eggs"):       # Invalid: a quoted literal cannot be a parameter name.
    pass                                      # Not executed; this is not a call that passes the word eggs.
```

Observed: `SyntaxError: invalid syntax`. Neither is the missing-argument **`TypeError`** from the earlier example: there the definition was legal and the *call* failed. The ordering rule here concerns ordinary positional parameters, not every possible kind of Python parameter.

Move required eggs *before* defaulted flour. A student asks whether the call lacks a value. It doesn't: `"whole wheat"`, still second in the call, is now the value for eggs. The name of the ingredient is not a binding rule. With **`"xfresh"`** first, the corrected version returns `"spoiled"` even before eggs is used. In the next run a diagnostic print inside the function makes the binding visible. [Transcript 0:39:02–0:40:16; scrollback 0:40:00–0:41:00.]

**Annotated teaching version — guide-added comments.** This runnable replay uses the bodies and values from the lesson but a short outer print. For the *first* legal state omit the diagnostic line; for the *second* include it.

```python
def doNothing(milk, eggs, flour=None):         # Required milk and eggs first; omitted flour gets None.
    print(eggs)                                # Added in the second state; prints whole wheat before returning.
    if milk == "fresh":                        # False for xfresh. Neither flour branch executes.
        if flour != None: return "matzah"      # Would require fresh milk with flour supplied.
        else: return milk                      # Would require fresh milk and no flour.
    else: return "spoiled"                     # Both legal runs reach this return.
print(doNothing("xfresh", "whole wheat"))      # Whole wheat binds eggs, not flour.
```

| Legal state | Output in classroom scrollback, in order |
|---|---|
| Corrected order, no diagnostic | `doNothing("fresh", "white"): spoiled` |
| Same call, with diagnostic | `whole wheat` then `doNothing("fresh", "white"): spoiled` |

**Key items to scan**

- **`eggs`** — an unquoted name in the definition. `"eggs"` in its place is invalid syntax.
- **`SyntaxError`** — Python rejects these headers before it can bind arguments or execute the body.
- **`TypeError`** — contrast the earlier valid definition whose call lacked the required flour argument.
- **`def doNothing(milk, eggs, flour=None):`** — second position now means eggs; third position is optional.
- **`"xfresh"`** — bound to milk in both legal runs; it still fails the outer equality test.
- **`print(eggs)`** — diagnostic inside the function; reveals `whole wheat` even though the returned value is `"spoiled"`.

## 0:40:20–0:45:59 | Name the arguments, then decide what your function is for

Keep the corrected definition, but specify **`flour`** and **`eggs`** by name in the call. Now `"whole wheat"` really is flour, although eggs appears first in the definition. The initial unlabelled string still binds milk by position. You need to know the parameter names to do this—one reason to look at a function's definition rather than infer its interface from a call. The instructor calls these “keyword parameters”; from the caller's side they are *keyword arguments*. [Transcript 0:40:20–0:42:41; terminal 0:41:00 and full editor 0:42:00.]

**Annotated teaching version — guide-added comments.** Use the preceding definition *with* its diagnostic; replace its caller with the actual final line:

```python
print(f"doNothing(\"fresh\", \"white\"): {doNothing("xfresh", flour ="whole wheat", eggs=3)}\n")  # Eggs prints first, then the caller displays spoiled.
```

Observed run, 0:41:00:

```text
3
doNothing("fresh", "white"): spoiled
```

| Step | What Python does | What appears |
|---|---|---|
| 1 | Bind milk to `"xfresh"`, flour to `"whole wheat"`, eggs to integer `3`. | Nothing yet. |
| 2 | Run the function's own **`print(eggs)`**. | `3` |
| 3 | Compare `"xfresh"` with `"fresh"`; skip the flour test and return `"spoiled"`. | Nothing from the return itself. |
| 4 | Insert the returned word into the caller's f-string; print the result. | `doNothing("fresh", "white"): spoiled` |

The final question shifts from grammar to design: what do you want to teach Python to do? The class suggests trying it; the instructor recommends intentionally breaking a small example, posting an issue with it, and asking classmates to predict or diagnose the result. An instructive reproduction here would change *only* a header or an argument, then ask whether failure occurs before the call, during binding, or not at all. The instructor offers numbers or a linked list as the next topic, chooses numbers, and weighs freehand drawing in Paint.NET against creating a diagram in Draw. This is the spoken transition, not a demonstration of a linked list or a completed diagram in the 0:45:00 capture. [Transcript 0:42:50–0:45:59.]

**Key items to scan**

- **`flour`** — named in the caller; receives `"whole wheat"` even though it is the third parameter.
- **`eggs`** — named separately; receives the integer `3`, irrespective of this keyword's written order relative to flour.
- **`print(eggs)`** — makes the new binding visible without changing the return value.
- **Keyword argument** — pairs a parameter name with a supplied value; the name must match the interface.
- **Predict, change one thing, run** — compare syntax failure, call failure, and an unexpected but legal result before reporting the issue.

## Why use a diagram instead of a drawing? · 0:46:03–0:49:47

In Paint the instructor could draw any line freehand. For place value he wanted several *identical* boxes, straight edges, and labels he could move without redrawing them. He compared that sort of work with Visio. The desktop draw.io template chooser already showed **`Untitled Diagram.drawio`**, with **Blank Diagram** selected; it also offered a Class Diagram, Flowchart and Org Chart. Instead of assuming the class knew the tool, he opened a browser to show where to find it. At 0:47:00 the search box reads **`drawio`**; “Untitled Diagram - Page-1” and “Security-first diagramming for teams | draw.io” are separate results. The first points to the editor, the second to the product site.

The instructor described two ways to work: open the online editor to share a diagram and work on it together, or use the desktop application, which he prefers for speed and for avoiding browser clutter. At 0:48:00–0:49:00 the product page advertises **Start Diagramming** and **Desktop App**, and says “Free forever • No sign-up required • Open source.” He chose the online editor for class. The site’s claim that you bring your own storage is not the same as a promise that no provider ever needs permission: storing the diagram with an outside service entails trusting the application with the access it requests. No account creation, storage authorization, shared editing or save is shown here.

Why not automatically store the diagram on **GitHub**? GitHub hosts repositories that use **Git**. The instructor recalled adding changes and committing them. If a diagramming integration makes commits for small edits, the history may fill with changes the author did not deliberately group or explain. He wants that history to be useful to his future self. This is his reason for preferring control, not a prohibition on versioning diagrams, and the class did not demonstrate an automatic commit.

**Key items to scan**

- **`Untitled Diagram.drawio`** — proposed desktop filename, not evidence that a file was saved.
- **Blank Diagram** — empty starting template, contrasted with ready-made diagram types.
- **`drawio`** — literal search; the landing page was at **`www.drawio.com`**, the working editor at **`app.diagrams.net`** (0:50:00). These are classroom screen references, not live checks.
- **Start Diagramming** — web editor route; collaboration is a capability discussed, not an observed second user.
- **Desktop App** — alternative for local work; the site described it as free and open source.
- **Git** — adding and committing determine what enters recorded history; no Git command was run in this demonstration.
- **GitHub** — possible storage integration; the objection was unclear or noisy automated commits, not Git itself.

## Make reusable boxes and recover a bad paste · 0:49:48–0:51:40

At 0:50:00 the web editor has a dark blank canvas, shapes on the left and diagram settings on the right. **Grid** and **Page View** are unchecked; **Connection Arrows**, **Connection Points** and **Guides** are checked. The instructor dislikes having the grid/page layout reappear and does not need a print page for this drawing. The searchable shape libraries offer much more than he needs: a rectangle with editable text is enough to make one cell of the diagram. The red “Unsaved changes. Click here to save.” banner describes the document state, not a successful save.

| Classroom action | Guide-added step note: what changes |
|---|---|
| Put `10` in a rectangle. | A labeled shape can serve as a template; at 0:51:00 one `10` box is selected. |
| Ctrl + wheel; press the wheel and drag. | Zoom changes magnification; panning moves the view across the canvas. Neither changes the number in a box. |
| Try Ctrl+C, Ctrl+V. | The instructor reports unexpected clipboard content and wonders whether the copy registered. The pasted content itself is not legible in a frame. |
| Ctrl+Z; select the rectangle; Ctrl+C, Ctrl+V. | Undo the unwanted change, identify the object to be copied, then paste a copy. At 0:52:00 two `10` boxes are visible below a new `1327` box. |

He distinguished the immediate paste mistake from an occasional, surprising “big long string” that might appear on another paste. If that happens, capture or post the evidence so the class can investigate. He did not display or diagnose that string here.

**Key items to scan**

- **Grid** / **Page View** — optional visual aids, both off on this canvas; an instructor preference, not a diagramming requirement.
- **Connection Arrows**, **Connection Points**, **Guides** — enabled aids; the handles/arrows around an object are interface controls, not math symbols.
- **`10`** — text inside a reusable rectangle, not a value produced by running code.
- **Ctrl + wheel** versus **wheel-drag** — change the scale versus the visible region.
- **Ctrl+C**, **Ctrl+V**, **Ctrl+Z** — copy the selection, paste the clipboard, undo the unwanted paste.

## Why does `1327` mean one thousand three hundred twenty-seven? · 0:51:43–0:54:35

A student supplied the four-digit number. “I just picked it” answers *why those digits?* The instructor was asking something else: *what makes that particular written sequence mean that particular amount?* On the canvas **`1327`** has no thousands comma. In ordinary base ten the four positions, from left to right, count thousands, hundreds, tens and ones. A **digit** tells how many units of that position to take; the **place value** is the size of each such unit. Their product is the contribution, not necessarily the place value itself. Think of the elementary-school blocks the class recalled: three hundred-blocks contribute three hundred, not three or one hundred.

At 0:53:00 the fourth box still repeats `3x100`. The completed upper row at 0:54:00 reads `1x1000 + 3x100 + 2x10 + 7x1`; the diagram’s lowercase `x` denotes multiplication. It settles the caption’s inconsistent “100,000s” and “tenth's” wording: the first place here is *thousands* and the third is *tens*, not tenths. The instructor then separated out a row of weights with no digits multiplying them.

| Digit and place | Contribution | Running total | Guide-added step note |
|---|---:|---:|---|
| 1 thousand | 1 × 1000 = 1000 | 1000 | Start with the largest occupied unit. |
| 3 hundreds | 3 × 100 = 300 | 1300 | Add three of the next smaller unit. |
| 2 tens | 2 × 10 = 20 | 1320 | This 2 adds twenty, not two. |
| 7 ones | 7 × 1 = 7 | 1327 | Add the seven individual units. |

*Guide calculation, checked independently in Python.* The base assumption matters: with the usual digit values, the very same string is legal in base eight, but then its weights are 512, 64, 8 and 1. The sum is 512 + 192 + 16 + 7 = 727 in decimal. That is a transfer example answering the instructor’s question, not a conversion he performed on screen.

**Key items to scan**

- **`1327`** — a numeral (written representation); the base and symbol meanings determine which number it names.
- **Digit** — `3` says to take three units in its column.
- **Place value** — that column weighs 100 in decimal; its contribution is 300.
- **`1x1000 + 3x100 + 2x10 + 7x1`** — completed screen row; add products, not bare digits or bare weights.

## Build the powers row, then extend it in both directions · 0:54:35–1:00:59

How do you get 100 out of tens? Multiply two tens: 10 × 10 = 100. The **exponent** counts the factors of the base for a positive whole exponent. One ten is 10¹; three tens multiplied give 10³ = 1000. The class’s sticking point was 10⁰: a student initially suggested zero, confusing “no factors of ten” with multiplying ten *by* zero. Start at 100 and take one step right at a time. Each step divides the weight by ten: 100 → 10 → 1. Setting the ones column to zero would break that rule and would make seven ones contribute nothing. In fact, multiplying no factors leaves the multiplication starting value, one. For a nonzero base, exponent zero gives one; this lesson does not require assigning a value to 0⁰.

| Position relative to ones | Weight | Guide-added step note |
|---|---:|---|
| Three left: 10³ | 1000 | 10 × 10 × 10; the 1 in `1327` occupies this column. |
| Two left: 10² | 100 | 10 × 10; two factors of ten, not ten times two. |
| One left: 10¹ | 10 | One factor of ten. |
| Ones: 10⁰ | 1 | Divide the preceding weight by 10; unlike 10 × 0, the result is not 0. |
| One right: 10⁻¹ | 1/10 | Divide 1 by 10. This is one tenth or 0.1, not a negative quantity. |
| Two right: 10⁻² | 1/100 | Divide by 10 again. This is one hundredth or 0.01. |

At 0:58:00 the four whole-number columns match the first four rows above. The rightmost new column at 0:59:00 still looks like the ones column. At 1:00:00 both fractional columns temporarily read one tenth; at 1:01:00 the second reads one hundredth. Those copied labels are work in progress. Both the spoken pattern and the settled diagram support the fractions in the table; the values were also checked independently in Python.

Moving left extends the same pattern to 10⁴, 10⁵, 10⁶ and beyond. A position farther left is **more significant**: a change of one unit in that digit changes the number by a larger amount. This compares *weights*, not the written sizes of individual digits. Writing the most significant digit on the left is a convention; the instructor isn’t claiming that quantities themselves have a natural left and right. Moving right past ones calls for **negative exponents**: take the reciprocal of the corresponding positive power. The instructor prefers saying “one tenth” over treating “a decimal” as a synonym for a fraction: *decimal* identifies a base, not the presence of a fractional part. To lower the stakes for learners with bad experiences of math, he calls this “playing with numbers”: look for the repeating change rather than memorizing an exception for each box.

**Key items to scan**

- **Exponent** — the `2` in 10² explains why the weight is 10 × 10 = 100.
- **10⁰** — the ones weight is 1; it is not 10 × 0.
- **More significant** — a one-digit step in the thousands column changes the value by 1000; in the tens column it changes it by 10.
- **Negative exponents** — 10⁻¹ = 1/10 and 10⁻² = 1/100. The minus is on the exponent, not on the value.
- **Reciprocal** — 1 divided by the positive power; it explains why successive places to the right get smaller.

## Where does zero fit, and what does the point separate? · 1:01:01–1:06:02

“Is zero positive or negative?” Neither. Calling it *real* is true but does not answer why: negative and positive numbers can be real, too. The class backed up to familiar sets. Using the instructor’s counting convention, **natural numbers** begin at 1. **Whole numbers** include those and zero. **Integers** also include negative whole-number values, but no value between successive integers. **Real numbers** include integers and values between them; they *can* have a nonzero fractional part, but are not required to. For example, 1/10 is real and not an integer, whereas 0 is whole, integer and real. A student tried defining “real” by whether a quantity was tangible or acquirable; the instructor countered that even things called imaginary are mathematical objects people work with. Tangibility is not the distinction. His aside about how recently zero gained acceptance was a historical impression, not a dating result or a reason to exclude zero from these sets.

Back at the diagram, how could a reader know when the weights drop below one without seeing any exponents? Insert a **radix point** between the ones column and the first fractional column. By 1:06:00 a small box containing a period is selected between the boxes for 1 and 1/10. It marks a boundary; it does not contribute another digit or weight. The caption renders the term “radics,” but the instructor spells it R-a-d-i-x. A student asked, “decimal point?” Yes, when the base is ten: **decimal point** is the base-ten name; radix point works for any base. The whole side includes exponent *zero*, not just strictly positive exponents.

| Read from left to right | Guide-added reading note |
|---|---|
| … 100, 10, 1 | The last whole-unit column is 1 = 10⁰. |
| point | Separate whole-unit digits from fractional digits, without consuming a digit position. |
| 1/10, 1/100, … | Each step right divides the preceding place weight by the base. These are weights; the instructor has not appended fractional digits to `1327`. |

**Key items to scan**

- **Natural numbers** — in this discussion 1, 2, 3, …; the starting convention matters.
- **Whole numbers** — 0, 1, 2, …; zero need not be positive to belong.
- **Integers** — …, −2, −1, 0, 1, 2, …; no nonzero fractional part.
- **Real numbers** — include the integers and values such as 1/10; “real” is not a synonym for “positive” or “tangible.”
- **Radix point** — separator after the ones digit, irrespective of base.
- **Decimal point** — that same separator specifically in base ten.

## Change the base, not the reasoning · 1:06:02–1:09:49

The instructor uses the familiar decimal diagram to introduce **generalization** and **abstraction**. Keep the instruction “multiply each digit’s value by its position’s weight, then add”; replace the particular base ten with a choice of base. The ones place still weighs one. Moving one place left multiplies the weight by the base, and moving one place right divides it by the base. “Only the base changes” names the common rule: the *resulting* weights and the number of distinct single-digit values change together. A label `base 10` is visible by 1:08:00; it is not a binary example on the canvas.

One suggestion was base two because computers use **binary**; another was ninety. When a student talked about which bases were “relevant,” the instructor asked who decides. Practical use is one consideration, not a mathematical restriction. What does “the computer understands zeros and ones” really mean? The class proposed different voltages. The instructor distinguished detecting two distinguishable states from knowing the written characters `0` and `1`, or the English meanings of true and false. High/low voltage or current, on/off, yes/no, true/false and 1/0 are ways of implementing or labeling the distinction, not identical physical phenomena. In a previous Essentials class the two labels had been Tom and Jerry; chicken and egg was another suggested pair. Those names illustrate a two-symbol alphabet; there was no hardware measurement, hardware redesign or on-screen Tom/Jerry calculation here.

**Key items to scan**

- **Generalization** — go from powers of 10 to powers of any chosen integer base of at least two in this positional scheme.
- **Abstraction** — keep the rule for positions while setting aside what particular digit shapes look like.
- **Binary** — two single-digit values; the two written symbols need not look like conventional zero and one.
- **`base 10`** — label of the existing diagram, not the base of all arithmetic.
- **Tom and Jerry** — a previous class’s proposed pair of labels; labels only work if everyone knows which value each represents.

## Choose an alphabet before counting · 1:09:49–1:13:59

What makes base ten *ten*? There are ten distinct single-digit values to represent before another place is needed. The familiar ordered **digit alphabet** is `0,1,2,3,4,5,6,7,8,9`, visible at 1:11:00. The answer “zero through nine” is correct about our notation but leaves out the important choice: the shapes themselves are conventional. The instructor noted that the forms of Arabic numerals have changed; having a different glyph does not change the arithmetic if its value is agreed. He mentioned Joe’s “any base” project as a future illustration of using arbitrary symbols, not as code being executed or a specification for an assignment.

Base ninety would demand ninety distinguishable digit symbols for this scheme. To keep the alphabet manageable, a student chose **base five**; the instructor proposed leaving base two for learners to try using the same reasoning. The class distinguished *numbers* from *numerals*: the five choices can be characters, not five pre-existing number names. At 1:12:00 a second box still reads `base 10`. At 1:13:00 it reads `base 5`, with **`a,t`** visible beside it in an active text field. Do not read the insertion caret as another character. The subsequent speech within this interval includes a suggestion that sounds like “a pound,” but there is no later still before 1:13:59 to establish the complete ordered alphabet from these frames. The instructor asks for the simplest operation; students offer addition and subtraction before arriving at **counting**. Before you can add using new symbols, you must know which symbol starts the count and which comes next.

**Key items to scan**

- **Digit alphabet** — ordered symbols with agreed digit values; the ten decimal glyphs are conventional, their number is not arbitrary once the base is fixed.
- **Base five** — five distinct digit values; it does not require choosing the glyphs `0` through `4`.
- **`a,t`** — the two visible lowercase characters separated by a comma in the in-progress 1:13:00 frame, not a completed list.
- **Counting** — the starting operation the class identifies before moving to addition or subtraction.

## 1:14:00–1:15:47 · Five symbols, but in which order?

Suppose the only marks available for counting are **`a,t,4,q,#`**, in exactly that order. You cannot sort them alphabetically or biggest-first: their assigned order is what gives them values. Start counting at zero. The first mark, **`a`**, means none; **`t`** means one; **`4`** means two, despite its familiar shape; **`q`** means three; and **`#`** (called *hash* or *pound*) means four. On the diagram the letters are lowercase. Base five fixes the number of different digits available, not their shapes, the length of a numeral, or the largest number it can describe. [Diagram: 1:14:00; the same five marks form a vertical count at 1:15:00.]

What happens after the last mark? A student correctly proposes adding a column and starting the right-hand column over, but proposes beginning the two-place sequence with **`aa`**, **`at`**, and so on. Keep the useful part of that idea: the right-hand digit *does* return to its first mark. What changes on the left? The class sets the question aside to work out the weights of the columns, then returns to the carry.

**Key items to scan**

- **`a,t,4,q,#`** — the ordered alphabet; changing the order changes what the individual marks mean.
- **`a`**, **`t`**, **`4`**, **`q`**, **`#`** — decimal values 0, 1, 2, 3, 4 respectively. There is no sixth digit.
- **`aa`**, **`at`** — the proposed start of the next group, not its correct start; their left-hand zero adds no value.

## 1:15:48–1:21:00 · Change the base; keep the exponents

The decimal diagram already reads **`1x1000 + 3x100 + 2x10 + 7x1`**: one thousand, three hundreds, two tens and seven ones make 1,327. To adapt the diagram, keep the ones column and the succession of exponents. Change the base of each power from ten to five. The columns keep the same *positions*; their *sizes* do not all stay the same. When a student asks about squares, cubes and roots, the instructor sets roots aside: the task is to evaluate each power, not to undo it.

Start at the ones place. A first answer of zero for **5⁰** is corrected to one. Why is one a useful anchor rather than an isolated fact to memorize? Walk right from the five-place: dividing five by five once must give one. Walk back left: multiplying one by five restores five. This is a guide explanation of the pattern; the class debates how intuitive the zero-exponent rule is and the instructor invites an explanation, rather than offering a proof. For the positive bases used here the zero power is one; there is no need to generalize the discussion to zero raised to zero.

| Move from the ones column | Place weight (written in decimal) | What changed |
|---|---:|---|
| Start: 5⁰ | 1 | Counts individual units. |
| One left: 5¹ | 5 | Five ones trade for one unit of this place. |
| Two left: 5² | 25 | Five groups of five. |
| Three left: 5³ | 125 | Five groups of twenty-five. |
| One right of ones: 5⁻¹ | 1/5 | Divide one by five. The weight is smaller, not negative. |
| Two right: 5⁻² | 1/25 | Divide the preceding fifth by five again. |
| Three right: 5⁻³ | 1/125 | Continue the same rule one more step; discussed, not boxed in the six-column diagram. |

In decimal you already use this pattern: moving from ones to tens to hundreds multiplies the unit by ten each time. In base five, multiply by five going left; divide by five going right. The class gives the correct *sizes* 5, 25 and 125, but the instructor does not yet accept them as the whole answer. Keep track of which system those answers are *written* in. [Frames: 1:16:00 shows the base change in progress; 1:20:00 shows the four whole-place answers; 1:21:00 adds the two fractional answers.]

**Key items to scan**

- **`1x1000 + 3x100 + 2x10 + 7x1`** — the existing decimal model: each digit counts groups of its column's size.
- **5⁰** — the ones place; stepping right from five divides by five to reach one, not zero.
- **5¹, 5², 5³** — the successive whole-place weights are 5, 25 and 125 in decimal.
- **5⁻¹, 5⁻², 5⁻³** — one fifth, one twenty-fifth and one hundred twenty-fifth. A minus sign in the exponent signals a reciprocal, not a negative count.

## 1:21:00–1:28:01 · Right quantities, wrong writing system

The instructor asks for larger and larger place values. Multiplying by five gives the following trail. A student starts to misread the last entry as 3,900, stops, and supplies 390,625. The arithmetic is not what the instructor objects to. Imagine someone claims these *spellings* were written using only five different digits. How soon can you disprove that claim? [Speech: 1:21:23–1:26:57; diagram: 1:27:00.]

| Power | Decimal spelling on the diagram | What this reveals about the spellings used so far |
|---|---:|---|
| 5⁰ | 1 | First mark: `1`. |
| 5¹ | 5 | Add `5`. |
| 5² | 25 | Add `2`; `5` was already used. |
| 5³ | 125 | No new mark. |
| 5⁴ | 625 | Add `6`: four distinct marks so far. |
| 5⁵ | 3125 | Add `3`: the running inventory is now `1,5,2,6,3`. |
| 5⁶ | 15625 | Five characters long, but uses only familiar marks; the `5` occurs twice. |
| 5⁷ | 78125 | `7` and `8` are both new. The running inventory already exceeds five. |
| 5⁸ | 390625 | This one spelling *by itself* uses six different marks: `3,9,0,6,2,5`. |

Those last two rows furnish different tests. You need not wait for a single entry to use six different characters: the collection as a whole runs out of alphabet sooner. And because the actual alphabet is known here, there is an even quicker test: the decimal glyph `5` isn't one of the agreed digits at all. The failure is a notation failure, not a limit on how large a quantity base five can represent. There is also no rule limiting a numeral to five *positions*: a long numeral made of repeated legal digits is fine.

There is another way to catch the switch. In saying *twenty-five*, you have read `25` as two **tens** and five ones. That is a decimal reading, not a reading in groups of five. A student instead decomposes twenty as four groups of five and adds one more group. That makes five groups of five: trade them for one unit of the next column. In ordinary base-five digits the trade is 40₅ + 10₅ = 100₅; in the class alphabet it is **`#a + ta = taa`**. These written equations are guide reconstructions of the spoken grouping, independently checked, not equations shown on the canvas. [Speech: 1:27:15–1:28:01, 1:30:14–1:30:35.]

**Key items to scan**

- **`3125` → `15625`** — length grows; the inventory of five distinct decimal glyphs does not.
- **`78125`** — the collection first exceeds five distinct glyphs here, counting the earlier whole-place spellings.
- **`390625`** — independently defeats a five-glyph alphabet within a single spelling.
- **`#a + ta = taa`** — four fives plus one five make one twenty-five; nothing requires inventing a digit for five.

## 1:28:02–1:31:55 · A carry counts a completed cycle

Return to the count that stopped at **`#`**. Use up the ones digit, put it back to **`a`**, then record *one completed group of five* on the left. The digit for one is **`t`**, so the next numeral is **`ta`**. Writing **`aa`** or even **`aaa`** still says zero: every contribution is zero, regardless of its weight. In decimal, the same event takes you from 9 to 10: the 1 records one completed group of ten, and the 0 records no leftover ones. [1:28:30 event: the count ends in `#, ta`; 1:29:00: `aaa` is written at the top while `ta` stays at the bottom.]

| Decimal counts (guide cross-check) | Class numerals in order | How the counter advances |
|---|---|---|
| 0, 1, 2, 3, 4 | `a`, `t`, `4`, `q`, `#` | Fill the ones place without a carry. |
| 5, 6, 7, 8, 9 | `ta`, `tt`, `t4`, `tq`, `t#` | Hold one five; cycle the five ones digits again. |
| 10, 11, 12 | `4a`, `4t`, `44` | A second rollover gives two fives; the new left digit is the class glyph `4`. |

The event frame shows only through `ta`. The longer count spells out the class's spoken continuation using the visible alphabet; the entire sequence was independently checked. One carry changes the left column; it does not skip any quantities or jump straight to a third column.

Now the earlier place-value question has two good answers, depending on the notation requested. If you write with the conventional glyphs for zero and one, a unit in each successive whole column looks like `1`, `10`, `100`, `1000` in its *own* base. Those strings indicate **one in the named column, zero in all columns to its right**, not necessarily *one, ten, one hundred, one thousand* in decimal. The diagram sets the following rows one under the other. The last row below translates the pattern into the class alphabet; it is a guide translation, not a row of the historical drawing.

| Way of identifying the column | Fourth whole place | Third | Second | Ones | First fraction | Second fraction |
|---|---:|---:|---:|---:|---:|---:|
| Power | 5³ | 5² | 5¹ | 5⁰ | 5⁻¹ | 5⁻² |
| Size written in decimal | 125 | 25 | 5 | 1 | 1/5 | 1/25 |
| Base-relative notation shown by 1:32:00 | `1000` | `100` | `10` | `1` | `1/10` | `1/100` |
| Same notation with class digits (guide translation) | `taaa` | `taa` | `ta` | `t` | `t/ta` | `t/taa` |

The fraction bar here means division; it isn't an extra digit. `1/10` in the base-relative row means one divided by that base's `10`, which is a fifth in this example. It does not mean a decimal tenth. Comparing the rows is how an ordinary instinct from decimal becomes a method for converting between bases.

**Key items to scan**

- **`#` → `ta`** — the last single digit is followed by one five and zero ones.
- **`a`**, **`aa`**, **`aaa`** — three spellings of zero. A leading zero has no effect on a positional numeral's value.
- **`t`** — one. In `ta` it counts fives; in `tt` the rightmost `t` counts ones.
- **`t#` → `4a`** — one five and four ones roll into two fives and zero ones.
- **`100`** — one in the third whole column; in base five its value is twenty-five, and its spelling with the class digits is `taa`.
- **`1/10`** — reciprocal of one whole base, not automatically one tenth.

## 1:31:56–1:36:59 · Does a base-five counter skip values? Read a four-place number

A student recalls building a base-two counter in Computer Essentials. If a base-five counter has columns worth 1, 5 and 25, can it still count through 9? Would it have to skip 10, 15 or 20 on its way to 25? The distinction is between *what a column is worth* and *how many of those units a digit indicates*. Keep incrementing the ones column for an ordinary count by ones. If you deliberately advance only the fives column, then you count by fives. With the class digits, 5, 10, 15 and 20 in decimal are `ta`, `4a`, `qa` and `#a`; 25 is `taa`. The positions stay the same; the digit in each position can vary. These spellings are independently checked illustrations of the student's question.

The instructor asks for a four-digit base-five numeral. One spoken suggestion gives “q, pound, 4, t, a”: written with the diagram’s letter case that would be `q#4ta`, five positions rather than four. This is a reconstruction of the spoken attempt, not a claim that this five-digit string appeared on screen. The canvas also shows **`q#$`**: `$` is not in the alphabet. Those are separate mistakes. Each of the four slots can contain *any one of five* legal digits, and digits may repeat. Compare decimal: repeating 3 in a longer numeral does not invalidate it. The accepted example on screen is **`q#qa`**. It has four positions and uses `q` twice. [Speech: 1:33:18–1:35:14; diagrams: 1:34:00 and 1:35:00.]

What does it mean? The class first considers counting “one q, one pound…”; that counts *marks on the page*, not the units each mark contributes. Read the leftmost `q` as three units in the fourth whole place. In base five that place is worth 125 in decimal, **not one thousand**. The second `q` counts the same number of units, but its place is worth only five. The instructor steers the class from the misleading name “tens place” to “five to the first” and anchors the ones column at “five to the zero.”

| Digit read left to right | Quantity indicated | Decimal place weight | Contribution | Running decimal total |
|---|---:|---:|---:|---:|
| `q` | 3 | 5³ = 125 | 3 × 125 = 375 | 375 |
| `#` | 4 | 5² = 25 | 4 × 25 = 100 | 475 |
| `q` | 3 | 5¹ = 5 | 3 × 5 = 15 | 490 |
| `a` | 0 | 5⁰ = 1 | 0 × 1 = 0 | 490 |

The proposed place-by-place expansion is audible at 1:35:48–1:36:13. The table completes and **independently verifies** that expansion: the value is **490 in decimal**. It is not a claim that a total was written or announced by 1:36:59. At 1:36:00 the actual canvas is still mid-edit: the first two coefficients have changed to `q` and `#`, but the last two remain `2` and `7` from the earlier decimal template. Read the digit, identify the position's unit, multiply the two, then add the contributions. This works without asking what a glyph “looks like” in decimal.

**Key items to scan**

- **`ta`, `4a`, `qa`, `#a`, `taa`** — the decimal multiples 5, 10, 15, 20, 25; intermediate quantities are still available in the ones column.
- **`q#4ta`** — the spoken suggestion reconstructed with screen glyphs: all five digits are legal, but there is one position too many for this request.
- **`q#$`** — invalid, because `$` is outside the alphabet. Repetition, by contrast, is allowed.
- **`q#qa`** — four legal digits; its two `q` marks mean three units each, but their contributions differ: 375 versus 15.
- **5¹, 5⁰** — call these the five-place and the ones place; avoid calling the second whole position “tens” when the base is not ten.
- **490 in decimal** — the guide's verified completion, not output on the sampled canvas.

## Writing the base in its own digits (1:37:00–1:42:59)

The number on the diagram is **`q#qa`**. Its rightmost **`a`** counts how many ones it contains. At 1:38:00 the instructor has written this contribution as **`ax5⁰`**. As a translation for a decimal reader, that makes sense. As an expression written *entirely in the invented numeral system*, what is wrong with it? A student suggests a negative exponent and one half, then notes that one half would belong to base two. A negative exponent does give a fractional weight, but one place to the right in base five would be one fifth. Neither fraction is needed here: the final digit of `q#qa` is in the ones place. The problem is that neither **`5`** nor **`0`** belongs to the agreed list **`a,t,4,q,#`** (discussion 1:37:27–1:38:42).

Start with nothing, written **`a`**. Count forward through **`t`**, **`4`**, **`q`**, **`#`**. At the next count the ones place is full: reset it to **`a`** and record one complete group to its left using **`t`**. The result is **`ta`**, not a new single digit. This is how to say “five” without borrowing a decimal digit. Translate the exponent zero as **`a`**, too. The corrected ones term has *three* occurrences of **`a`**, for three different reasons. Here and below, a caret in a plain-text expression transcribes a raised exponent on the screen; the screen uses the letter **`x`** for multiplication, not as a digit.

| Part of `ax(ta)^a` | What it tells you |
|---|---|
| First `a` | Coefficient: the rightmost digit of `q#qa` says there are no units here. |
| `(ta)` | Base: the entire two-digit numeral means one group of five plus zero ones. Dropping its `a` would leave `t`, meaning **one**, not five. Parentheses show exactly what is raised to a power. |
| Raised `a` | Exponent: this is the first position counted from the right, so no moves to the left have been made. Its weight is five to the zero power, or one. |

A student asks why the second `a` was left in and suggests using only `t` for the base (1:38:54–1:39:40). The ones term is a poor test of that proposed edit: its coefficient is zero, so its contribution will be zero either way. Try the next digit, **`q`**. Now there are three groups; each group must weigh five, not one. The instructor writes **`qx(ta)^t`**: the coefficient changes with the given digit, the exponent advances one step, but the base stays **`ta`**. Next comes **`#x(ta)^4`**. The third position from the right is two moves from the ones place. Its exponent is therefore the *third glyph* in the alphabet, not the ordinary decimal number four. The three completed terms are visible at 1:42:00 and 1:48:00.

| Digit in `q#qa`, from right | Custom term, caret transcription | Decimal check: digit × place weight |
|---|---|---|
| `a` | `ax(ta)^a` | 0 × 5⁰ = 0 × 1 = 0 |
| `q` | `qx(ta)^t` | 3 × 5¹ = 3 × 5 = 15 |
| `#` | `#x(ta)^4` | 4 × 5² = 4 × 25 = 100 |
| `q` | `qx(ta)^q` **(guide completion)** | 3 × 5³ = 3 × 125 = 375 |

**Independent check, not classroom output:** 375 + 100 + 15 + 0 = **490** in decimal. On the actual diagram the leftmost box still reads **`qx1000`**, not the completed power term. The decimal-looking row **`qx1000 + #x100 + qx10 + ax1`** and the partially rewritten row are separate diagram states, not a decimal calculation of 490. The positional pattern `1000, 100, 10, 1` is a scaffold; those strings are not even spelled with the custom alphabet. Under the new alphabet the equivalent group sizes are `taaa, taa, ta, t` from left to right. The point of the exercise was to reason *without* translating every step into decimal; the decimal column checks the result afterward.

**Key items to scan**

- **`a,t,4,q,#`** — ordered base-five alphabet. Individually, **`a`**, **`t`**, **`4`**, **`q`**, **`#`** represent nothing and the next four counts.
- **`q#qa`** — four digits, read from the right for the ones, fives, twenty-fives, and one-hundred-twenty-fives places.
- **`ax5⁰`** — intermediate mixed notation: **`5`** and **`0`** are decimal glyphs, though the intended weight is correct.
- **`ta`** — one complete group and zero leftover ones; the two characters together spell the base.
- **`x`** — multiplication in these boxes; it is not a custom digit. An asterisk was mentioned orally as an alternative.
- **`ax(ta)^a`** — no ones; coefficient, base, and exponent have different jobs.
- **`qx(ta)^t`** — three fives; **`#x(ta)^4`** — four twenty-fives. The base does not change.
- **`qx1000`** — still visible at the left of the lower row. **`qx(ta)^q`** completes the pattern independently; it was not shown there.
- **`qx1000 + #x100 + qx10 + ax1`** — the scaffold above the power row, not a sum using decimal place weights. **490** — the separately checked decimal value of the custom numeral.

## A glyph's name is not its count (1:43:02–1:49:09)

A student asks whether to call the exponent “4 A,” then whether to call it “the fourth,” “the second,” or “the third.” The instructor turns the question around: why say “ten to the two” for a familiar decimal power? “Ten groups of ten” explains the arithmetic; it does not explain the *names* “ten” and “two.” We say those words because we share names and meanings for the written signs. Changing the alphabet breaks that habitual association. The decimal row and the custom row appear together at 1:45:00 (`06300s.png` also shows both).

Read the ordinary decimal list aloud: zero is the *first* symbol, one the *second*, two the *third*. Its sequence starts at nothing. In the custom list **`a → t → 4`**, you likewise take just two steps from nothing to reach the third symbol. Calling its shape “four” can identify it for a classmate; it does **not** say how many steps it stands for here. So the exponent is written **`4`** in this notation, has value two in decimal, and gives the *square* of the base. It is not the third power because it occupies the third slot, and not the fourth power because it looks like a decimal four. The agreement that matters is the order and meaning, not what sound the group uses to name a mark. The instructor acknowledges that familiar marks with unfamiliar meanings are causing real difficulty and changes the example rather than insisting on a name.

**Key items to scan**

- **`a → t → 4`** — start at nothing; two successor steps lead to the third symbol.
- **`4`** — distinguish the mark's familiar name, its third position in the alphabet, and its assigned value of two. In the custom exponent it means “squared.”

## Count with shapes: what happens after the last symbol? (1:49:09–1:54:04)

The class first proposes circle, square, triangle. The instructor rejects the circle because it resembles an O or zero; cylinder and trapezoid are mentioned, but the choice on the canvas at 1:50:00 is **square, right-pointing triangle, diamond**, left to right. A student offers “four, three, four” by counting corners. That would answer a question about shapes, but it cannot explain this numeral system: the square and the diamond both have four vertices yet must denote different counts. Here the class *assigns* values by order, not geometry.

Use **`□ ▷ ◇`** as text stand-ins for the three unfilled drawn shapes. The horizontal group is the key; the instructor makes a separate vertical counting list. Read one row at a time, with the ones place on the right. Start at **`□`** (nothing), advance to **`▷`**, then **`◇`**. To count once more, the rightmost place has no unused symbol. Reset it to the first symbol, and put the next count in the place to its left: **`▷□`**. Writing **`□□`** instead would say *no* groups and *no* ones—back to nothing. That is why the instructor praises the class for avoiding the “square square” trick (1:51:39–1:52:06).

| Number of completed groups | What the ones place does | Numerals in increasing order | Decimal check |
|---|---|---|---|
| No complete group | Uses the three symbols once | `□`, `▷`, `◇` | 0, 1, 2 |
| One complete group | Restarts at square with triangle held at left | `▷□`, `▷▷`, `▷◇` | 3, 4, 5 |
| Two complete groups | Restarts at square with diamond held at left | `◇□`, `◇▷`, `◇◇` | 6, 7, 8 |
| Three complete groups | Both lower places have cycled; start a new place | `▷□□` | 9 |

The first nine counts are worked through in the discussion and diagram (1:51:05–1:54:04; 1:54:00 frame). The final row is a **guide reconstruction of the carry**, not a claim that the entire three-shape row is already drawn in that frame. After **`◇◇`**, the ones place resets to square and sends a count left. The threes place already has a diamond, so it too resets to square and sends a count left. The new triangle is worth one group of nine. This explains both zeros: they are not decorations after the triangle, but the new counts in the two exhausted places.

**Key items to scan**

- **Square, right-pointing triangle, diamond** (**`□ ▷ ◇`**) — three digits in this agreed order; not counts of vertices.
- **`□`**, **`▷`**, **`◇`** — the three single-place numerals, from nothing to two steps past nothing.
- **`▷□`** — one completed group and no singles, the first carry. **`□□`** would still be zero.
- **`◇◇`** — both places at their last digit. **`▷□□`** — next count after both places reset, one nine and no threes or ones.

## Read the columns, not just the rows (1:54:07–1:56:59)

The instructor adds squares on the *left* of earlier rows and observes that the values do not change. For example, **`▷◇`** and **`□▷◇`** both say one three and two ones. The extra square says there are no nines. Padding on the left makes columns easier to compare, even though the hand-placed shapes are not perfectly aligned. A student recalls a binary display without leading zeros and asks that a possible teaching app show all the places. That is a display preference, not a new rule of arithmetic or a demonstration of an app (1:54:46–1:55:26).

Now look *down* the padded rows in the 1:56:00 frame. The ones place cycles square, triangle, diamond every three rows. One column left, the threes place holds square for three rows, then triangle for three, then diamond for three. Another place left, a square must last for all nine combinations of the two lower digits. Only then does the nines place change to a triangle. This last change is described at 1:56:13–1:56:36; it is not yet a completed row in the 1:56:00 still. The class suggests copying a group of three shapes three times rather than copying one shape nine times. It works because the repeated blocks have structure, not because copying changes a place's value.

**Transfer example — independently checked, not a classroom row:** After `▷□□` (9), the next two numerals are `▷□▷` and `▷□◇`. The left triangle still counts one nine, the middle square still counts no threes, and only the ones digit changes: 9 + 0 + 1 = 10; 9 + 0 + 2 = 11. To predict a more distant row, ask how many full cycles of the column to the right must finish before this column advances.

**Key items to scan**

- **`▷◇` / `□▷◇`** — two ways to write five: a leading square adds zero groups of nine.
- **Ones, threes, nines** — a single glyph stays unchanged for one, three, and nine consecutive rows respectively when counting from zero; the shape order itself never changes.
- **Leading zero** — a zero-valued symbol on the left exposes an empty higher place; unlike inserting a zero on the right, it leaves the value alone.
- **Copy three three times** — classroom suggestion for drawing a nine-row block; no program or completed teaching app is shown here.

## 1:57:00–1:59:19 | Counting without saying “one, two, three”

Return to the diagram’s nine rows. You can predict the next one without giving any shape a familiar number name. That is the point of the instructor’s remark that the usual numbers “don’t exist” here: their **names and written digits** are unnecessary for this task, not that the quantities disappear. For practice, the instructor asks the class to repeat the earlier exercise using the base-three shapes. No submission or deadline is stated. (Captions 1:57:01–1:58:08; diagram at 1:57:00.)

**Prediction, not a tenth fully visible row in that frame:** after `□◇◇` comes `▷□□`. Both lower places have reached diamond, so both reset to square; the leftmost advances to triangle. The positions are therefore the ones, threes, and nines places, even if a student calls the shapes by entirely different names. A student’s earlier shortcut fits this logic: copy a block of three repeated symbols three times, rather than copying one symbol nine times. During the transition, a participant suspected a Discord stream problem; the instructor asked listeners to check and report it. The draw.io loading window at 1:59:00 is not proof of a stream crash. No new calculation is demonstrated by that window.

**Key items to scan**

- **Square → right-pointing triangle → diamond** — the chosen order of digits; the names are optional, the order and rollover are not.
- **`□□◇` → `□▷□`** — the first carry shown here: the last place resets, the middle advances.
- **`□◇◇` → `▷□□`** — predicted carry across two exhausted positions; this is why the left symbol can last nine rows.

## 1:59:20–2:02:13 | How should a public project index identify contributors?

A student organizing the class project hub asked whether to put a contributor’s real name next to a project. The instructor asked what problem the extra identifier would solve: a GitHub user ID already links the work to the account. Use that unless the person *wants* their name or email included. Contributors can decide what to publish in their own repositories; a class index need not make that decision for them. Someone floated a private repository during this discussion; the Hub shown later is explicitly **public**. (1:59:20–2:00:14; 2:01:25–2:01:59; 2:03:00 frame.)

Leaving an email out of an index does not, on its own, keep it out of repository history. The instructor recalled an earlier discussion of how a Git **commit** records its author, and of using **`git config`** to change **`user.name`** and **`user.email`**. Think of the account name associated with a project link and the author details attached to a commit as two different places an identity can appear. The instructor warned that an account email might be exposed; the recording does not establish that every installation automatically uses its sign-up email. No configuration command, old or new value, or changed commit is shown here. (2:00:14–2:00:51.)

The student also asked how to arrange the index: perhaps list consenting contributors with their projects, or group projects by category so a visitor can find a topic. The instructor deliberately delegated the choice instead of prescribing a layout. What already existed was much simpler: a README with two project links. (2:00:54–2:02:13.)

**Key items to scan**

- **GitHub user ID** — identifies a project account without adding a separate real-name or email directory.
- **Public** — the demonstrated Hub is readable outside the class; contributor consent matters when copying information into it.
- **Commit** — a saved revision with author information; a README is not the only public surface to check.
- **`git config`** — the tool mentioned for choosing Git settings; **`user.name`** and **`user.email`** are the author name and email settings discussed.
- **Contributor list vs. project categories** — competing design ideas left to the student, not assigned fields or a finished layout.

## 2:02:00–2:05:54 | How to contribute without permission to edit the Hub directly

The [Hub repository](https://github.com/joes-ta/Hub) is an index, not a single repository containing everyone’s program. On screen, the rendered **`README.md`** has the heading **Project** and links to two independently hosted projects: [Link](https://github.com/scriptz0340/Link) and [AnyBase](https://github.com/Wolfster427/AnyBase). Someone looking for a project can start at the Hub; someone developing it can work in its own repository. The README explains the learning goals: manage a repository with other developers, try features such as pull requests, practice delegating control, and explore more technologies than one class project would cover. These are classroom links as displayed, not newly verified destinations. (2:02:00 frame; 2:06:00 Discord history.)

GitHub’s **Projects** feature came up as a possible organizational aid; it is distinct from grouping links under headings in a README. At 2:03:00 the pointer rests on Projects, but **Code** is still the selected tab. No board or GitHub organization was created on screen. The instructor had intentionally avoided requiring an organization: use features everyone can access freely, introduce issues and pull requests gradually, and do not make setup or cost a barrier. Some capabilities might be worthwhile in a funded professional setting. That is a rationale for this class’s choices, not a claim that the Projects tab or all organizations cost money. (2:02:13–2:04:12.)

A student wanted a place for everyone’s ideas. The instructor separated the right to *suggest* something from the right to *change the repository directly*. Anyone participating through the public Hub could submit an **issue** to raise a question or request, or a **pull request** to propose specific changes for review. A **collaborator** can be given more direct access. The instructor offered the student organizing the Hub collaborator rights; no invitation, acceptance, or rights change is visible. By contrast, the instructor said the class demo and notes repositories were private and that students who asked had been added as collaborators. The public Hub lets students practice a different situation: “I want to contribute” to someone else’s project even though I do not have write access. (2:04:12–2:05:54.)

| What the contributor wants | Classroom route | What that route does **not** establish |
|---|---|---|
| Let others find a project | Create a public project repository, commit the work there, and share its URL for the Hub index. An earlier Discord message visible at 2:06:00 asks for exactly that sequence. | The index does not copy the code or transfer ownership. |
| Discuss a problem or propose a specific edit | Open an issue for the discussion; use a pull request for the proposed change. | Submitting either is not permission to write directly or a guarantee the change will be accepted. |
| Help maintain the index | The instructor proposed granting the organizer collaborator rights. | The ordinary Code page seen at 2:05:00 does not confirm a grant. |

**Key items to scan**

- **`README.md` / Project** — the Hub’s existing introduction and two-link index, not an assignment to use a particular directory structure.
- **Link; AnyBase** — separate project repositories linked from the README.
- **Code** — selected repository view showing `.gitignore` and `README.md` on `main`.
- **Projects** — a visible tab suggested for future use; hovering over it is not the same as opening a board.
- **GitHub organization** — an additional way to organize work the instructor intentionally did not require.
- **Issue** — raises a matter for discussion. **Pull request** — proposes a concrete change for review.
- **Collaborator** — an explicitly authorized participant; public visibility alone does not give everyone those privileges.

## 2:05:54–2:09:42 | Give ideas a home in Discord; distinguish a draft from a published post

Students wanted somewhere to discuss what should be added without requiring everyone to work directly in GitHub. Earlier messages visible in the Joe’s TA Discord server at 2:06:00 already point to a project-specific **anybase** forum and say the repository formerly called Project was renamed **Hub**. The new channel would instead be for improvements to the Hub itself. This also explains why the name in the browser can be Hub while the GitHub README heading still says Project.

| Step | Choice and visible result |
|---|---|
| Choose the kind of channel | The instructor first says “text channel,” then chooses a **forum channel**. A forum gives separate ideas their own posts rather than mixing them in a single running conversation. |
| Choose a recognizable name | “Ideas” and “contribution” are suggested; the instructor chooses **`hub`** to match the repository. A student suggests placing it directly below **`general`** because new work is easier to find there. At 2:07:00 that placement is visible in the Joe’s TA server. |
| Invite discussion | At 2:07:00–2:08:00 the forum offers **New Post** and search. Meanwhile, a student puts forward a project idea about collecting air blown through a straw in a balloon; the instructor defers it. There is no built or tested measuring device in this exchange. |
| Consider an integration | The class discusses sending notifications when repository activity occurs. A student calls this a **webhook** and compares it loosely to an API. The idea is to have an event trigger a notification instead of waiting for someone to check manually. No endpoint, precise destination, configured integration, or delivered notification is shown. (2:07:22–2:07:58.) |
| Explain the forum’s purpose | The instructor says other channels have a readme. At 2:09:00 a Discord post composer is open with the title **`README`** and the body `This is a place for discussing the github Hub enhancements`. The **Post** button is still present and the first-post checklist item remains unchecked. This is a draft in Discord, not a commit to GitHub’s `README.md`. |

The final older-set frame (2:09:20) shows the course server’s `general` channel, **not** a published post in the Joe’s TA forum. The instructor checks whether anyone is still there and announces the recording will stop (2:09:39–2:09:42). The sources establish the new forum and its position, not whether the draft was later posted or a webhook enabled.

**Key items to scan**

- **anybase** — the existing Discord forum for that project’s ideas and direction. **Hub** — the name of the shared GitHub project index.
- **Forum channel / `hub`** — the actual Discord channel type and name chosen for Hub ideas. **`general`** — the channel immediately above it in Joe’s TA.
- **New Post** — starts a separate forum discussion; not a GitHub issue or commit.
- **Webhook** — proposed event-triggered message about repository activity; compared to an API, not set up in this demonstration.
- **`README` / Post** — the title and submit control in a Discord draft. **`README.md`** — a different artifact, the existing GitHub file.

## Syntax to remember

| Pattern | Meaning here |
|---|---|
| `def doNothing(milk, flour):` | Declare two required parameters; naming them does not declare types. |
| `doNothing("fresh", "white")` | Supply arguments by position: first milk, then flour. |
| `if milk == "fresh":` | Compare values; only the exact matching string enters the nested branch. |
| `if flour != None:` | Classroom spelling: test inequality with `None`, not whether an argument was supplied. |
| `return milk` | End this call and send its bound milk object back to the caller. Do not print it yet. |
| `def doNothing(milk, flour=None):` | Define the value to use when the second argument is omitted. |
| `doNothing("fresh", None)` | Explicitly supply the object `None`; this also works when flour has no default. |
| `def doNothing(milk, eggs, flour=None):` | Two required ordinary positional parameters precede the defaulted one. |
| `flour ="whole wheat", eggs=3` | In the final call, bind by name. These equals signs do not test equality. |
| `print(f"... {expression}...")` | Evaluate the expression first, format its value, then display; text outside braces is not checked against the call. |

## Knowledge check

1. The arrow in an indentation error points at a triple-quoted string below `def doNothing():`. Why can changing the preceding `#pass` to `pass` fix it? Why can you type `dir(...)` but not `dir(pass)`?
2. A function body consists only of `pass`. What is the difference between `type(doNothing)` and `type(doNothing())`? Which operation actually calls the function?
3. The terminal says `doNothing("fresh")): not fresh`. What two parts of the print statement must you read separately to explain the line?
4. Compare a required `flour` parameter with `flour=None`. When does omission prevent the body from starting? Does explicitly passing `None` have that effect?
5. Given `def doNothing(milk, eggs, flour=None):`, predict the diagnostic printed by `print(eggs)` for `doNothing("xfresh", "whole wheat")`. What changes if the call uses `flour="whole wheat", eggs=3` after `"xfresh"`? Does the returned milk-branch result change?
6. Start at the ones column and move two places right in base five. What are those two weights in decimal? Why is neither negative?
7. The legend is `a,t,4,q,#`. What follows `t#`? Read `q#qa` in decimal, explaining why its two `q` digits contribute different quantities.
8. Use the shape alphabet square, right-pointing triangle, diamond. What follows `□◇◇`? Which column changes most often? (The glyphs here stand for drawn shapes.)
9. You cannot edit the Hub README directly. What would you open to suggest a project? How would you propose the actual edit? What evidence would you need before saying the class published the Discord README?

### Answer key

1. The header promises an indented suite of statements. A comment supplies no statement; the next top-level string exposes the missing suite. `pass` fills the body without performing an action. The three ASCII dots form a literal for the Ellipsis object, so they can supply an argument to `dir`; `pass` is a statement, not an argument expression.
2. Without parentheses, `doNothing` is the function object and its type is `function`. With the inner parentheses, Python first calls the function. The body finishes without an explicit return value, so `type` receives `None` and yields `NoneType`. The `type` operation itself does not execute the function it inspects.
3. The text before the replacement field is an ordinary label, extra parenthesis included. In this revision the expression inside the braces passes `"not fresh"`. It does not compare equal to `"fresh"`, so the one-parameter function returns its argument; the outer print combines that value with the unchanged label.
4. A missing required argument prevents binding and raises `TypeError` before execution enters the body. `None` is a real object: passing it explicitly fills even a required slot. With a default of `None`, omitting flour also fills the slot. What the function *then returns* depends on the body in that revision, not on the default alone.
5. The second positional argument goes to eggs, so the first diagnostic is `whole wheat`. Naming eggs and flour gives eggs the integer `3`, so it prints `3` instead. Both calls still pass `"xfresh"` as milk: the equality test fails, the flour test is not reached, and the outer print displays the returned `spoiled` after the diagnostic.
6. Each step right divides the weight by five: 1 → 1/5 → 1/25. The negative exponent counts divisions; it does not make a positive fraction negative. A denominator written `100` in conventional base-five digits denotes twenty-five, not a hundred.
7. `t#` is one group of five and four singles. One more completes a second group, so `4a` follows: `4` is the digit for two and `a` is zero. In `q#qa`, the weights are 125, 25, 5, 1; the digit values are 3, 4, 3, 0. Their contributions are 375, 100, 15, 0, totaling **490**. This is a checked calculation, not a number printed by the classroom diagram.
8. `▷□□` follows. Both diamonds are already the last digit, so the ones and threes places reset; the nines place advances from square to triangle. The ones column changes every step. The shape’s number of corners plays no role.
9. An **issue** can request inclusion and give the project URL; a **pull request** proposes particular changes for review. Neither grants general write access. The GitHub README was already rendered, but the Discord composer still offered **Post**. To establish publication you would need a posted item or other confirming evidence, not merely a filled-in title and text.

## Library of definitions

### Reading Python

| Pattern or term | Lesson-specific meaning |
|---|---|
| `def name(...):` | Define a user function; a colon opens the mandatory indented body. Defining the function does not execute its body. |
| `#` vs. `"""` | A comment marker ignores the rest of a source line. Triple quotes delimit a multiline string; the instructor uses one to keep example text from executing. |
| Docstring | A string in the relevant documentation position (e.g., first statement in a function). Triple quotes alone do not make arbitrary text a docstring. |
| Statement / expression | `pass` is a statement: it instructs Python to take no action. An expression such as `...` supplies a value. An argument slot requires an expression. |
| `pass` / `...` | No-operation statement versus ellipsis literal. When used alone in these bodies, both let control reach the end. |
| `dir()` / `dir(obj)` | Without an argument, lists names in this scope. With the function object as argument, lists that object’s attribute names. |
| Object / class / instance | `doNothing` is a particular function object, an instance of the `function` type. A class describes a kind of object; instantiation makes an instance. |
| `id(obj)` | Integer representing the object’s identity during its lifetime; do not memorize the class run’s number. |
| Function call | `douglasAdams` refers to an object, whereas `douglasAdams()` runs the body and yields `42`. |
| `return` vs. `print` | The former exits the function and gives the caller a value. The latter displays text. Falling off the end of a normal function call here yields `None`. |
| `None` / `NoneType` | The value used here to signify no other result; `NoneType` is the type of that value. Neither name means `"None"` the string. |
| Parameter / argument | `milk` names the input in a definition; `"fresh"` is the actual argument in the call. |
| f-string / literal label | Text outside `{…}` prints as written. The expression inside supplies a value; `\n` gives an added line break. |
| Git and Discord | GitHub repository holds the code; the Hub lists links, the project forum hosts discussion, an issue tracks a suggestion, and a pull request proposes a code change. |

- **Positional argument** — a supplied value matched by its place in the call. After reordering the definition, second position feeds `eggs`, not `flour`.
- **Keyword argument** — a named argument at a call site, such as `eggs=3`. In the demonstrated definition both eggs and flour can also be supplied positionally; neither is keyword-only.
- **Default value** — the value used for an omitted parameter, here `None` for flour. Defaults belong to the definition, not the descriptive label in a print statement.
- **Binding** — associating an argument or default with its parameter for this call. If a required slot is missing the body does not begin.
- **Literal** — source notation for a particular value, such as `27` or `"fresh"`; quoted `"eggs"` is therefore not a parameter declaration.
- **Conditional / nested branch** — an `if` chooses a path; an indented second `if` is reached only on the enclosing path. The two `else` clauses answer different questions.
- **Side effect** — an observable action other than yielding a return value. The inner `print(eggs)` displays `3` before the caller prints its own line.
- **`SyntaxError`** — code cannot be parsed/compiled, as with the two invalid headers. **Runtime exception** — failure during execution of otherwise legal code, such as the missing argument's `TypeError`. **Logic error** — legal code whose behavior does not meet the author's intention; inspect inputs, labels and branches even after a successful run.

### Diagrams and number systems

| Term | Meaning in this demonstration |
|---|---|
| Diagram object | A shape with editable text, such as one `10` rectangle. It can be selected, duplicated and positioned independently. |
| Zoom / pan | Enlarge or shrink the view / move the visible region without changing the diagram’s data. |
| Clipboard / undo | Copy places selected content on the clipboard; paste uses its current contents. Undo reverses the unwanted change when the wrong thing is pasted. |
| Commit history | The sequence of recorded Git changes; grouping and explaining edits deliberately makes it more useful later. GitHub is a hosting service, not the same thing as Git. |
| Number / numeral / digit glyph | The quantity / written representation of that quantity / individual written symbol. `1327` has four digit glyphs. |
| Positional notation / place value | The position determines the weight; a digit’s value times that weight is its contribution. |
| Base (radix) / digit alphabet | The base tells how many single-digit values there are and sets the ratio of adjacent weights. An ordered alphabet assigns symbols to those values. |
| Power / exponent | In 10³, three is the exponent, ten is the base, and the power has value 1000. |
| Significance | Size of a position’s effect per unit of digit value; it does not mean that its printed glyph is larger. |
| Reciprocal / fractional place | 1 divided by a value / position with weight less than one. For example, 10⁻² is the hundredths weight. |
| Radix point / decimal point | Mark separating whole and fractional digits in any base / the base-ten name for that mark. |
| Natural, whole, integer, real | Under the convention here: counting from one; nonnegative integers including zero; whole numbers and their negatives; numbers on the real number line, including integers and nonintegers. |
| Generalization / abstraction | Apply the decimal pattern to other bases / retain the common positional rule while ignoring the particular choice of glyph shapes. |
| Binary | Base two: two available digit values suffice; an implementation needs distinguishable states, not printed numerals. |

- **Carry / rollover:** once all digit values in a place are used, reset that place to zero and increment the next place left. `#` becomes `ta` when incremented.
- **Leading zero:** a zero-valued mark to the left of the significant digits of an integer. `aaa` has the same value as `a`.
- **Base conversion:** writing the same quantity in a different number system, not changing the quantity. `q#qa` in the class alphabet and 490 in decimal name the same amount.
- **Coefficient** — how many units of a place weight to take. The `q` at the left of `q#qa` takes three units of 125.
- **Vertex** — a corner of a shape; the number of vertices does not determine a shape digit’s value.

### Project collaboration

- **README:** a short orientation document. GitHub renders `README.md` in the Hub; the proposed Discord `README` is a forum post, not that file.
- **Commit author metadata:** identifying details stored with a recorded revision; distinct from an account label manually displayed in an index.
- **Issue:** a repository discussion for a problem, request or idea. **Pull request:** a proposal to review and incorporate particular changes.
- **Collaborator:** someone granted access beyond that of a public visitor; having a public URL is not a grant of write rights.
- **Forum channel:** a Discord area organized around separate titled posts, here used to collect ideas for the Hub itself.
- **Webhook:** an event-triggered way for one service to notify another. **API** was a student’s analogy, not a configuration recipe or proof of a working endpoint.

## Historical appendix · Numbered screen code and diagram record

Use this appendix to check what was on screen. In the code listings, gutter numbers, vertical separators, and explicit visibility markers are not Python to type. The diagram transcriptions that follow are hand-worked notation, not program output. Each entry has its own frame evidence.

### 1. `src/example.py` · Function bodies, inspection and the first input (0:07:00–0:21:59)

The gutter numbers are the editor’s line numbers, **not source to type**. These are separate observed revisions, not successive pieces of one runnable file. A line containing three quotes matters as much as a line of Python here: it changes which examples are inside a string. The fullest early view is given once; excerpts in subsequent revisions show their own active lines and adjoining boundaries. Times identify capture targets, not independently probed frame times.

#### 0:07:00–0:08:00 · A commented body; later examples already in the buffer

**Observed numbered listing:** `00-08-00.png`, lines 1–31. The 0:07:00 and `00-07-05-event-missing-body.png` terminals obscure the left side of the editor but clearly show the failed run. The 0:08:00 frame independently exposes the source of the missing body. In the listing below, lines 6 onward are *inside the string opened at line 4*, not calls executed by that run. The screen ends partway into the next line below 31; the source is not a complete file.

```text
 1 | def doNothing():
 2 |     #pass
 3 |
 4 | """
 5 |
 6 | print(f"dir(doNothing): {dir(doNothing)}\n")
 7 | print(f"type(doNothing): {type(doNothing)}\n")
 8 | print(f"id(doNothing): {id(doNothing)}\n")
 9 | print(f"call of doNothing(): {doNothing()}\n")
10 |
11 | def doNothing():
12 |     return None
13 | print(f"call of doNothing()): {doNothing()}\n")
14 |
15 | def douglasAdams():
16 |     return 42
17 | print(f"call of douglasAdams()): {douglasAdams()}\n")
18 |
19 | def doNothing(milk):
20 |     if milk == "fresh": return "spoiled"
21 |     else: return milk
22 | print(f"doNothing(\"fresh\")): {doNothing("fresh")}\n")
23 |
24 | def doNothing(milk, flour):
25 |     if milk == "fresh":
26 |         if flour != None: return "matzah"
27 |         else: return milk
28 |     else: return "spoiled"
29 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", "whit[remainder not visible]
30 |
31 | def doNothing(milk, flour=None):
```

Line 2 is a comment and line 4 is at the left margin. The shell command **`py .\src\example.py`** ends with **`IndentationError: expected an indented block after function definition on line 1`**, pointing at line 4. Lines 11–31 are prewritten examples, not successive definitions already run. The end of line 29 and the rest of the file are not supplied by inference.

#### 0:09:00 · The `pass` revision

**Observed excerpt:** `00-09-00.png`. The function now has an indented statement; the inspection prints are still inside the block opened on line 4. There is no call in the active excerpt, so the shell can return without printing a result.

```text
 1 | def doNothing():
 2 |     pass
 3 |
 4 | """
```

#### 0:09:30–0:10:00 · The ellipsis revision

**Observed excerpt:** `00-09-30-event-ellipsis.png`; the dots remain visible in `00-10-00.png` and the independently sampled `00600s.png`. The body changed from a statement to a literal expression. The first silent invocation was already in scrollback at 0:09:30. By 0:10:00 there are two consecutive silent invocations, consistent with the instructor’s account of trying both placeholders.

```text
 1 | def doNothing():
 2 |     ...
 3 |
 4 | """
```

#### 0:14:00 · The blank-body edit, not another successful placeholder

**Observed excerpt:** `00-14-00.png`. An insertion caret is indented on line 2; no body token is visible there. The shell now shows another line-4 indentation failure after the two silent commands. A still does not prove which intermediate edits had been saved at the moment of each execution.

```text
 1 | def doNothing():
 2 |
 3 |
 4 | """
```

#### 0:15:00–0:17:00 · `pass` with the inspection calls active

**Observed numbered excerpt:** `00-15-00.png`; `00900s.png` confirms the active rows and delimiter. Line 4 no longer has the earlier triple quotes. The remaining examples are set aside beginning at line 10. Compare the literal printed label on line 9 with the extra `)` in the inactive label on line 13.

```text
 1 | def doNothing():
 2 |     pass
 3 |
 4 |
 5 |
 6 | print(f"dir(doNothing): {dir(doNothing)}\n")
 7 | print(f"type(doNothing): {type(doNothing)}\n")
 8 | print(f"id(doNothing): {id(doNothing)}\n")
 9 | print(f"call of doNothing(): {doNothing()}\n")
10 | """
11 | def doNothing():
12 |     return None
13 | print(f"call of doNothing()): {doNothing()}\n")
```

**Observed terminal** (`00-16-00.png`, repeated at `00-17-00.png`): the first print displayed a list starting with `'__annotate__'` and ending with `'__type_params__'`; the next three results were:

```text
type(doNothing): <class 'function'>

id(doNothing): 2227746256176

call of doNothing(): None
```

The first three calls inspect the function object. The fourth actually runs it and interpolates its returned value. The identity is preserved as screen evidence, not an expected identity for another run.

#### 0:19:00 · Explicitly returning `None`

**Observed numbered excerpt:** `00-19-00.png`. Line 1 opens a string; line 11 closes it. The active lines are 12–14; line 15 starts another string. The newest diagnostic still visible in the 0:19:00 terminal is the *earlier* implicit `None`. A separate `call of doNothing()): None` line is visible in the shell at 0:20:00.

```text
 1 | """
 2 | def doNothing():
 3 |     pass
 4 |
 5 |
 6 |
 7 | print(f"dir(doNothing): {dir(doNothing)}\n")
 8 | print(f"type(doNothing): {type(doNothing)}\n")
 9 | print(f"id(doNothing): {id(doNothing)}\n")
10 | print(f"call of doNothing(): {doNothing()}\n")
11 | """
12 | def doNothing():
13 |     return None
14 | print(f"call of doNothing()): {doNothing()}\n")
15 | """
```

#### 0:20:00 · Returning the integer `42`

**Observed numbered excerpt:** `00-20-00.png` and `01200s.png`. Here the string starting at line 1 does **not** end at line 11; line 11 is blank. It ends at 15, leaving 16–18 active and a new string beginning at 19. The shell shows two separate invocations of `py .\src\example.py`, with the explicit-`None` result followed by `call of douglasAdams()): 42`.

```text
10 | print(f"call of doNothing(): {doNothing()}\n")
11 |
12 | def doNothing():
13 |     return None
14 | print(f"call of doNothing()): {doNothing()}\n")
15 | """
16 | def douglasAdams():
17 |     return 42
18 | print(f"call of douglasAdams()): {douglasAdams()}\n")
19 | """
```

#### 0:21:00 · The one-parameter function is active

**Observed numbered excerpt:** `00-21-00.png`. Line 15 is now blank, line 19 ends the earlier quoted region opened at 1, and line 24 begins the next one. The active call on line 23 supplies `"fresh"`. Its label contains an extra `)` before the colon. The classroom terminal beside it still ends with the older **42**; the teaching section gives a separate test of this version.

```text
15 |
16 | def douglasAdams():
17 |     return 42
18 | print(f"call of douglasAdams()): {douglasAdams()}\n")
19 | """
20 | def doNothing(milk):
21 |     if milk == "fresh": return "spoiled"
22 |     else: return milk
23 | print(f"doNothing(\"fresh\")): {doNothing("fresh")}\n")
24 | """
```

### 2. Numbered screen history | `example.py`, 0:22:00–0:45:59

The left column reproduces editor gutter numbers; do **not** type it. Empty numbered rows are visible blanks, not inferred omissions. The vertical bar is editorial. These are separate screen states of `C:\CSCS1240F26\F26Demo\src\example.py`. Quoted examples remain in the file while only one demonstration is active. The terminal is a separate PowerShell window, not a transcript of the current buffer. Frame times refer to filename targets, not continuous observation.

### 0:22:00 — active one-parameter version, with earlier examples still visible

Source: `00-22-00.png`, upper and lower editor regions. Lines 1 and 19 enclose the earlier examples; they are now text, not active definitions. Lines 20–23 show the live branch and its caller. Line 24 begins another quoted region. The left terminal still ends with the earlier Douglas Adams result (42); the fresh-call result appears in the next minute's scrollback.

```text
 1 | """
 2 | def doNothing():
 3 |     pass
 4 | 
 5 | 
 6 | 
 7 | print(f"dir(doNothing): {dir(doNothing)}\n")
 8 | print(f"type(doNothing): {type(doNothing)}\n")
 9 | print(f"id(doNothing): {id(doNothing)}\n")
10 | print(f"call of doNothing(): {doNothing()}\n")
11 | 
12 | def doNothing():
13 |     return None
14 | print(f"call of doNothing()): {doNothing()}\n")
15 | 
16 | def douglasAdams():
17 |     return 42
18 | print(f"call of douglasAdams()): {douglasAdams()}\n")
19 | """
20 | def doNothing(milk):
21 |     if milk == "fresh": return "spoiled"
22 |     else: return milk
23 | print(f"doNothing(\"fresh\")): {doNothing("fresh")}\n")
24 | """
25 | def doNothing(milk, flour):
26 |     if milk == "fresh":
27 |         if flour != None: return "matzah"
28 |         else: return milk
29 |     else: return "spoiled"
30 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", "whit[remainder not visible]
31 | 
```

### 0:23:00–0:26:00 — the changing caller, same function body

At 0:23:20 the label still advertises fresh, but the evaluated argument is not fresh. At 0:24:00 the matching label fixes that discrepancy; its prefix is behind the terminal, so that version below is an assembled reconstruction using the visible prefix in `00-23-20-event-stale-label.png` and complete remainder in `00-24-00.png`. The integer caller is fully readable at `00-26-00.png`. Compare only the replacement of line 23 in each run:

0:23:20, stale label:

```text
23 | print(f"doNothing(\"fresh\")): {doNothing("not fresh")}\n")
```

0:24:00, repaired label (assembled):

```text
23 | print(f"doNothing(\"not fresh\")): {doNothing("not fresh")}\n")
```

0:25:00–0:26:00, integer and stale label:

```text
23 | print(f"doNothing(\"not fresh\")): {doNothing(27)}\n")
```

The command visible for these runs is `py .\src\example.py`, from `PS C:\CSCS1240F26\F26Demo>`. Selected result lines, one per successive run (blank lines and prompts omitted):

```text
doNothing("fresh")): spoiled
doNothing("fresh")): not fresh
doNothing("not fresh")): not fresh
doNothing("not fresh")): 27
```

### 0:29:00 — two required parameters; old and upcoming text bracket the live block

`00-29-00.png` shows lines 7–37; the right end of 30 is legible in the same frame (unlike 0:22:00). Line 19 is now blank, 24 closes the previous quoted region, and 31 opens the next one. Lines 25–30 bind fresh/white, choose the inner true branch and print the result. Lines 32–37 below them are *not* a second active demonstration here. The terminal still displays the earlier 27 in this frame; by 0:30:00, `doNothing("fresh", "white"): matzah` is visible after a separate invocation.

```text
 7 | print(f"dir(doNothing): {dir(doNothing)}\n")
 8 | print(f"type(doNothing): {type(doNothing)}\n")
 9 | print(f"id(doNothing): {id(doNothing)}\n")
10 | print(f"call of doNothing(): {doNothing()}\n")
11 | 
12 | def doNothing():
13 |     return None
14 | print(f"call of doNothing()): {doNothing()}\n")
15 | 
16 | def douglasAdams():
17 |     return 42
18 | print(f"call of douglasAdams()): {douglasAdams()}\n")
19 | 
20 | def doNothing(milk):
21 |     if milk == "fresh": return "spoiled"
22 |     else: return milk
23 | print(f"doNothing(\"not fresh\")): {doNothing(27)}\n")
24 | """
25 | def doNothing(milk, flour):
26 |     if milk == "fresh":
27 |         if flour != None: return "matzah"
28 |         else: return milk
29 |     else: return "spoiled"
30 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", "white")}\n")
31 | """
32 | def doNothing(milk, flour=None):
33 |     if milk == "fresh":
34 |         if flour != None: return "matzah"
35 |         else: return "spoiled"
36 |     else: return "spoiled"
37 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh")}\n")
```

### 0:30:00–0:31:00 — insufficient arguments, then explicit `None`

The 0:30:00 editor shows the one-argument call; the 0:31:00 traceback reproduces its whole line even though the editor behind it has already changed. Here is the error-producing version from the traceback, with its reported gutter number:

```text
30 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh")}\n")
```

```text
PS C:\CSCS1240F26\F26Demo> py .\src\example.py
Traceback (most recent call last):
  File "C:\CSCS1240F26\F26Demo\src\example.py", line 30, in <module>
    print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh")}\n")
                                             ~~~~~~~~~^^^^^^^^^
TypeError: doNothing() missing 1 required positional argument: 'flour'
```

The replacement line below is partly hidden in `00-31-00.png`; the whole spelling is recovered in the visible, subsequently inactive line 30 of `00-32-00.png`. This is evidence for the text of the repair, not evidence that the later defaulted function produced the earlier result.

```text
30 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", None)}\n")
```

Class result by 0:32:00: `doNothing("fresh", "white"): fresh`. Unlike omission, an explicit `None` satisfies the second required parameter; the inner false branch returns milk.

### 0:32:00–0:34:00 — defaulted flour, two copies of `"spoiled"`

Source: `00-32-00.png`, also `00-33-00.png` and `00-34-00.png`. Line 24 is now blank; 31 closes the prior block, leaving the definition at 32 and the caller at 37 active. In the first run using the default, 35 supplies the result, not 36. Scrollback still ends in `fresh` at 0:33:00; by 0:34:00 the new line is `doNothing("fresh", "white"): spoiled`.

```text
16 | def douglasAdams():
17 |     return 42
18 | print(f"call of douglasAdams()): {douglasAdams()}\n")
19 | 
20 | def doNothing(milk):
21 |     if milk == "fresh": return "spoiled"
22 |     else: return milk
23 | print(f"doNothing(\"not fresh\")): {doNothing(27)}\n")
24 | 
25 | def doNothing(milk, flour):
26 |     if milk == "fresh":
27 |         if flour != None: return "matzah"
28 |         else: return milk
29 |     else: return "spoiled"
30 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", None)}\n")
31 | """
32 | def doNothing(milk, flour=None):
33 |     if milk == "fresh":
34 |         if flour != None: return "matzah"
35 |         else: return "spoiled"
36 |     else: return "spoiled"
37 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh")}\n")
38 | 
```

### 0:35:00–0:37:00 — inner fallback changed; positional caller edited

`00-35-00.png` and `02100s.png` show the middle of the edit, not a completed additional argument. Line 35 has changed, so the terminal's new `fresh` result is distinct from the earlier `spoiled` run. The caret on line 37 is not a source character.

```text
32 | def doNothing(milk, flour=None):
33 |     if milk == "fresh":
34 |         if flour != None: return "matzah"
35 |         else: return milk
36 |     else: return "spoiled"
37 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", )}\n")
38 | 
```

By `00-36-00.png`, the first positional string is `"xfresh"` and the second is `"whole wheat"`. Line 37 below uses only the legible prefix: the closing parenthesis of the inner call is visible; the rest does not fit in the editor. Scrollback shows separate runs giving `fresh`, `matzah`, then `spoiled`, all with the same hard-coded label. The intermediate fresh/whole-wheat call is supported by speech and outputs, not a complete screen listing of that intermediate caller.

```text
32 | def doNothing(milk, flour=None):
33 |     if milk == "fresh":
34 |         if flour != None: return "matzah"
35 |         else: return milk
36 |     else: return "spoiled"
37 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("xfresh", "whole wheat")[remainder not visible]
```

### 0:38:00–0:39:00 — two independent definition errors, then a repair

At `00-38-00.png`, required eggs follows defaulted flour. At `00-38-20-event-parameter-order.png`, the terminal has come forward and exposes *both* failing lines at line 32. The latter uses double quotes. No line in either function body runs. These are separate replacements:

```text
32 | def doNothing(milk, flour=None, eggs):
```

```text
32 | def doNothing(milk, flour=None, "eggs"):
```

```text
PS C:\CSCS1240F26\F26Demo> py .\src\example.py
  File "C:\CSCS1240F26\F26Demo\src\example.py", line 32
    def doNothing(milk, flour=None, eggs):
                                    ^^^^
SyntaxError: parameter without a default follows parameter with a default
PS C:\CSCS1240F26\F26Demo> py .\src\example.py
  File "C:\CSCS1240F26\F26Demo\src\example.py", line 32
    def doNothing(milk, flour=None, "eggs"):
                                    ^^^^^^
SyntaxError: invalid syntax
```

At `00-39-00.png` the visible header suffix is now `eggs, flour=None):`, but the terminal still ends with the errors above. The full corrected header is independently legible at `00-42-00.png`:

```text
32 | def doNothing(milk, eggs, flour=None):
```

The corrected positional call first displays the stale labeled `spoiled` with no diagnostic; that run is visible at `00-40-00.png`. The instructor then adds the body print. By `00-41-00.png`, scrollback shows a separate invocation that prints `whole wheat` before the stale labeled `spoiled`. The wider terminal partly obscures the editor at 0:40:00; do not read the older single-line output as the result of a definition already printing eggs.

### 0:41:00–0:45:00 — keyword caller and diagnostic print

`00-42-00.png`, overlapping left and right parts of the same frame, shows the widest resolved final listing. Lines 16–20 have part of their gutter behind the webcam; the row sequence and text match the corresponding numbered rows at 0:32:00. Line 31 is the closing delimiter, not a comment marking the start of the new definition. The print now at 33 shifts the driver to 38. The space *before* the equals in `flour ="whole wheat"` is visible; do not insert a space after it when transcribing. The later 0:45:00 views show blank rows 39 and 40, not a further function.

```text
16 | def douglasAdams():
17 |     return 42
18 | print(f"call of douglasAdams()): {douglasAdams()}\n")
19 | 
20 | def doNothing(milk):
21 |     if milk == "fresh": return "spoiled"
22 |     else: return milk
23 | print(f"doNothing(\"not fresh\")): {doNothing(27)}\n")
24 | 
25 | def doNothing(milk, flour):
26 |     if milk == "fresh":
27 |         if flour != None: return "matzah"
28 |         else: return milk
29 |     else: return "spoiled"
30 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("fresh", None)}\n")
31 | """
32 | def doNothing(milk, eggs, flour=None):
33 |     print(eggs)
34 |     if milk == "fresh":
35 |         if flour != None: return "matzah"
36 |         else: return milk
37 |     else: return "spoiled"
38 | print(f"doNothing(\"fresh\", \"white\"): {doNothing("xfresh", flour ="whole wheat", eggs=3)}\n")
39 | 
40 | 
```

Terminal, `00-41-00.png`, last two completed invocations, preserved here as separate runs:

```text
PS C:\CSCS1240F26\F26Demo> py .\src\example.py
whole wheat
doNothing("fresh", "white"): spoiled

PS C:\CSCS1240F26\F26Demo> py .\src\example.py
3
doNothing("fresh", "white"): spoiled
```

In the first, the second positional value occupies the eggs slot. In the second, the name `eggs` selects `3` regardless of keyword order. The diagnostic does not return either value: the failing milk test still returns `"spoiled"` in both runs.

### 3. Historical screen record: the draw.io diagram, not a Python program

The following are discrete draw.io canvas states. Spaces, table columns and row descriptions replace the distances and outlines of separate boxes. The multiplication mark is shown as a lowercase `x`; exponents were raised on the screen. No line numbers, equals signs or program output were present in these diagram rows. The partially covered code editor and PowerShell scrollback behind the desktop chooser at 0:46:00 belong to earlier work; they do not constitute another execution.

**1. Where the drawing was made.** At 0:46:00 the desktop dialog proposes `Untitled Diagram.drawio`; `Blank Diagram` is selected beside `Class Diagram`, `Flowchart` and `Org Chart`. At 0:47:00 Google shows `drawio` in the query box, “These are results for draw io,” “Untitled Diagram - Page-1,” and “Security-first diagramming for teams | draw.io.” At 0:48:00–0:49:00 `www.drawio.com` shows “Security-first diagramming for teams,” “Bring your storage to our online tool, or save locally with the desktop app. Your data stays yours—we can't access it.” The visible choices are “Start Diagramming” and “Desktop App”; a GitHub header link is *not* evidence of a repository change. By 0:50:00 the untitled diagram at `app.diagrams.net` has a blank canvas, a shape palette and “Unsaved changes. Click here to save.” No save or change of storage provider is established by these frames.

**2. From a manipulable rectangle to the sum.** The 0:51:00 selected box reads `10`. At 0:52:00 a selected `1327` sits above two boxes that each read `10`. The following rows are separate states, not lines in a program:

| Original frame | Material diagram text or state |
|---|---|
| `00-53-00.png` | `1327` above `1x1000`, `3x100`, `2x10`, `3x100`. Third box in text edit; fourth still repeats the hundreds expression. |
| `00-54-00.png` | `1327` above `1x1000 + 3x100 + 2x10 + 7x1`; a second, offset copy of that entire sum below has a selection outline/alignment guide. The plus signs occupy their own little boxes. |
| `00-55-00.png`, `03300s.png` | Upper sum remains. Below it the four weights now read `1000`, `100`, `10`, `1`. There is a further `10` box below `100`. That isolated box is not an equation asserting 100 = 10. |

**3. Recovering the powers, including the provisional labels.** Read each exponent row from left to right across boxes. Treat it as the instructor’s working diagram, not a list of true equations at every instant.

| Original frame | Weight row | Power row below it |
|---|---|---|
| `00-56-00.png` | 1000, 100, 10, 1 | 10², 10², 10¹, 10² (rightmost box in text edit) |
| `00-57-00.png` | 1000, 100, 10, 1 | 10², 10², 10¹, 10⁰ (rightmost box selected) |
| `00-58-00.png` | 1000, 100, 10, 1 | 10³, 10², 10¹, 10⁰ |
| `00-59-00.png` | 1000, 100, 10, 1, 1 | 10³, 10², 10¹, 10⁰, 10⁰ |
| `01-00-00.png`, `03600s.png` | 1000, 100, 10, 1, 1/10, 1/10 | 10³, 10², 10¹, 10⁰, 10⁻¹, 10⁻¹ |
| `01-01-00.png` | 1000, 100, 10, 1, 1/10, 1/100 | 10³, 10², 10¹, 10⁰, 10⁻¹, 10⁻² |

In the completed rows the value above a power is its weight; the earlier duplicate boxes cease to be mathematical mismatches as their labels change. The same settled columns remain on screen at 1:02:00–1:04:00 during the *spoken* classification of numbers. The diagram does not display definitions of the natural, whole, integer or real numbers.

**4. Add notation without changing the weights.** At 1:05:00 the `1327` header and sum are in view; connection controls near `10` are not a radix point. At 1:06:00 a selected small outlined box contains `.` between the `1` and `1/10` columns. By 1:07:00 the point is visible without selection handles. At 1:08:00 and 1:09:00 a separate `base 10` label is visible to the right of the powers. No fractional digit was appended to the displayed `1327`; these are possible place weights, not a changed value.

**5. Two alphabets under construction.** The 1:10:00 minute frame and `04200s.png` show `base 10` and the highlighted placeholder `Text`. At 1:11:00 the text to its right is `0,1,2,3,4,5,6,7,8,9`. At 1:12:00 that row remains and a lower box still reads `base 10`. At 1:13:00 the wider canvas shows these two separate rows:

```text
base 10    0,1,2,3,4,5,6,7,8,9
base 5     a,t
```

The last character after the `t` that looks like a vertical stroke is the text insertion caret. The blue arrow is a draw.io connection/clone control, not part of the alphabet. The lower row is unfinished in this frame; it does not show the full selection the class discusses afterward. The first four powers, two fractional powers, and their corresponding weights remain elsewhere on the canvas.

### 4. Historical diagram record · Bases and counters, 1:14:00–1:36:59

These are text transcriptions of the classroom draw.io canvas at **`app.diagrams.net`**, not code, commands, or terminal output. The tab reads `Untitled Diagram - draw.io`; the red status reads `Unsaved changes. Click here to save.` It establishes the visible editing state, not that a save subsequently occurred. The numbered states below are editorial reference numbers, not source line numbers. Boxes are represented as table cells, superscripts as mathematical typography; `x` is the actual letter-shaped multiplication mark used in the products. Times identify requested capture targets, not every change between them.

**1. Decimal model and ordered digit legends — 1:14:00; 1:15:00 (`04500s.png` corroborates the count).** The product boxes give a familiar decimal reference against which the class later changes the base. The small boxed `.` sits between the ones and first fractional column. The two legends are separate from the later vertical counting list.

```text
1x1000 + 3x100 + 2x10 + 7x1

1000   100   10   1   1/10   1/100
 10³   10²  10¹  10⁰  10⁻¹    10⁻²

base 10    0,1,2,3,4,5,6,7,8,9
base 5     a,t,4,q,#
```

**2. Editing the copied row — 1:16:00–1:21:00.** There is a difference between the in-progress state and the finished mathematical comparison. The first two power boxes have been changed by 1:16:00; at 1:17:00 the completed powers also appear in a second, selected row offset to the right. The later lower row is aligned with the power columns and its labels are replaced by evaluated weights. The following are visible states of that lower working row (the 1:16 row is the mixed power row before the duplicate appears).

| Target | First | Second | Third | Fourth | Fifth | Sixth |
|---|---:|---:|---:|---:|---:|---:|
| 1:16:00 | 5³ | 5² | 10¹ | 10⁰ | 10⁻¹ | 10⁻² |
| 1:17:00 | 5³ | 5² | 5¹ | 5⁰ | 5⁻¹ | 5⁻² |
| 1:18:00 | 5³ | 5² | 5¹ | 1 | 5⁻¹ | 5⁻² |
| 1:19:00 | 5³ | 5² | 5 | 1 | 5⁻¹ | 5⁻² |
| 1:20:00 (`04800s.png` also) | 125 | 25 | 5 | 1 | 5⁻¹ | 5⁻² |
| 1:21:00 | 125 | 25 | 5 | 1 | 1/5 | 1/25 |

**3. Extending the decimal-value row — 1:22:00–1:27:00.** At 1:22:00 `3125` and `625` are already visible left of `125`. At 1:23:00 the leftmost editing box reads just `781` followed by the insertion caret, beside `15625`; it does not yet say `78125`. At 1:24:00 the box to its left is partly under the pointer. The unobstructed 1:27:00 frame below establishes the large labels. For example, the older 1:25:00 `05100s.png` only shows the suffix `90625` at the left boundary; it is not an independent view of the entire label. This is a row of decimal spellings, not one giant numeral.

```text
390625    78125    15625    3125    625    125    25    5    1
```

**4. Vertical counter — successive states, not one merged list.** Read down each column. The count has no sixth mark at 1:28:00; the event frame (`01-28-30-event-base-five-carry.png`) shows `ta` directly below `#`. At 1:29:00 the first line becomes `aaa` to illustrate leading zeros; `05400s.png` independently shows that spelling at 1:30:00. The `#` followed by a vertical caret in the event image is the editing cursor, not an extra digit. Later spoken continuations do not appear in these lists.

| Row | 1:28:00 | 1:28:30 | 1:29:00 |
|---|---|---|---|
| 1 | `a` | `a` | `aaa` |
| 2 | `t` | `t` | `t` |
| 3 | `4` | `4` | `4` |
| 4 | `q` | `q` | `q` |
| 5 | `#` | `#` | `#` |
| 6 | — | `ta` | `ta` |

**5. Putting two representations under the same powers — 1:31:00 versus 1:32:00.** The first two rows below appear at both targets. The third is visible at 1:32:00 but absent at 1:31:00. In the context of the class discussion, it supplies the general zero/one *positional pattern*. It does not mean that a new `0` or `1` was added to the `a,t,4,q,#` alphabet.

```text
   5³     5²    5¹    5⁰    5⁻¹    5⁻²
  125     25     5     1    1/5    1/25
 1000    100    10     1   1/10   1/100
```

**6. Three distinct example states — 1:34:00, 1:35:00, 1:36:00.** At 1:34:00 the selected text reads `q#$`, with a literal dollar sign not in the legend. At 1:35:00 it reads `q#qa`; `05700s.png` independently shows the same accepted four glyphs. It is not `Q#QA`. At 1:36:00 `q#qa` is still visible, now alongside these boxes:

```text
qx1000 + #x100 + 2x10 + 7x1
```

The second box of products is selected. Only the first two coefficient labels match the new numeral; the final `2` and `7` still match the older decimal diagram. Do not substitute `q` and `a` into that historical line or report 490 as its displayed result. The student's verbal proposal instead uses all four intended coefficients and decimal weights 125, 25, 5 and 1. The completed evaluation in the teaching text is an independent reconstruction from that proposal and the glyph legend.

### 5. Diagram record: from `q#qa` to the shape counter (1:37:00–1:56:59)

These are edits to a draw.io canvas at `app.diagrams.net`, not source code or terminal output. The tab reads `Untitled Diagram - draw.io` and the red banner says `Unsaved changes. Click here to save.` The evidence establishes the canvas state, not a saved file. The identifiers below are requested capture times, not verified frame presentation times. For the text transcriptions, `x` preserves the screen’s multiplication marker, `^` denotes the visually raised exponent, and spaces around `+` separate diagram boxes. The diagram has no line-number gutter.

| Capture | Lower expansion below the scaffold; observations |
|---|---|
| `01-37-00.png` | `qx1000 + #x100 + qx10 + ax` — the last box is in edit mode with the caret after `x`. The upper row ends in `ax1`. |
| `01-38-00.png` | `qx1000 + #x100 + qx10 + ax5^0` — caret appears after the raised `0`. The oral objection is to borrowing the symbols, not to the ones place. |
| `01-39-00.png` | `qx1000 + #x100 + qxt + axta^a` — the third box is selected, and the last has no visible grouping parentheses yet. Not a finished expansion. |
| `01-40-00.png`; `06000s.png` | `q#qa` now appears over the rows. The third term's space is empty between plus boxes. Boxes overlap at far right; portions of `ax(ta)` and a raised `a` can be seen, but the two instances cannot be reliably transcribed separately. |
| `01-41-00.png` | `qx1000 + qx(ta)^t + qx(ta)^t + ax(ta)^a` — the second box from the left has the copied text selected and still matches the third. The copy is an intermediate drawing state, not a second `q` at the hundred-like position of `q#qa`. |
| `01-42-00.png`; `01-48-00.png` | `qx1000 + #x(ta)^4 + qx(ta)^t + ax(ta)^a` — the copied box now has the `#` coefficient and the raised `4`; the leftmost `qx1000` remains. The row above still reads `qx1000 + #x100 + qx10 + ax1`. |

At `01-43-00.png` the decimal `10^0` box has blue selection handles. The oral question about “ten to the two” comes later (1:43:14–1:43:55); do not claim this still shows `10^2` selected. The alphabet comparisons at `01-44-00.png` through `01-47-00.png` show `0,1,2,3,4,5,6,7,8,9` and **`a,t,4,q,#`**. The first entry in the separate vertical count at the right reads **`aaa`**, not `a`; below it are `t`, `4`, `q`, `#`, `ta`. Three zero-valued digits still denote nothing. That diagram entry is distinct from the one-digit symbol `a` in the key.

At `01-49-00.png` and the separate `01-49-00-event-shape-transition.png`, the diagram still shows `1327` above `1x1000 + 3x100 + 2x10 + 7x1`. Across six aligned columns are `1000, 100, 10, 1, 1/10, 1/100`, then `10^3, 10^2, 10^1, 10^0, 10^-1, 10^-2`, then `5^3, 5^2, 5^1, 5^0, 5^-1, 5^-2`, then `125, 25, 5, 1, 1/5, 1/25`. The base-five weight row also extends left through `625` and `3125`; the farther-left entry is clipped. A separate lower row repeats `1000, 100, 10, 1, 1/10, 1/100`. The decimal and custom expansion boxes remain. No shape alphabet is visible yet in either 1:49 capture.

**Shape-table states.** `□`, `▷`, `◇` below stand for outline drawings: square, right-pointing triangle, diamond. They are not literal keyboard characters typed by the instructor. In the transcriptions, one diagram row means one numeral; alignments have been regularized, not treated as arithmetic operators.

| Capture | Visible state |
|---|---|
| `01-50-00.png`; `06600s.png` | Large horizontal key `□ ▷ ◇`; the preceding custom-base diagram is still visible to the left. At `01-51-00.png` a square overlaps the triangle during positioning; not a new fourth symbol. |
| `01-52-00.png` | Horizontal key and a separate vertical sequence `□`, `▷`, `◇`, then `▷□`; the triangle at the left of the first two-place row is selected. |
| `01-53-00.png` | Below the first cycle: `▷□`, `▷▷`, `▷◇`, followed by a selected right-column block `□`, `▷`, `◇`; the corresponding higher-place shapes for that block are not yet visible. |
| `01-54-00.png` | Vertical list down to `◇◇`, including `◇□` and `◇▷`; the left diamond of `◇◇` is selected. A lone square lies on the next row at right. It is not a completed numeral for nine. |
| `01-55-00.png`; `06900s.png` | Leading squares at left of the triangle and diamond groups. A square is selected; lower squares overlap near `◇◇`. Below are another square, triangle, diamond in the right column only. These are working shapes, not three completed higher numerals. |
| `01-56-00.png` | Nine three-column rows. Middle-column shapes selected; nine leading squares at left. Lower rows partly outside the viewport. The instructor subsequently describes changing to triangle in the nines place; this image does not show that completed carry. |

**One-frame reconstruction, `01-56-00.png`.** Rows from top to bottom; `│` separates places for this transcription only. Notice that the left glyph stays unchanged while all pairs of the other two run through their possibilities.

```text
□ │ □ │ □
□ │ □ │ ▷
□ │ □ │ ◇
□ │ ▷ │ □
□ │ ▷ │ ▷
□ │ ▷ │ ◇
□ │ ◇ │ □
□ │ ◇ │ ▷
□ │ ◇ │ ◇
```

### 6. Closing diagram and interface record (1:57:00–2:09:20)

At `01-57-00.png` the same nine complete shape rows as in the preceding transcription are still visible. The additional evidence here concerns what did and did not change afterward. Only the top of the next row is in view; it is not needed to establish the sequence. At `01-58-00.png` the diagram is larger and fewer shape rows fit. The earlier base-five sketch beside it remains visible: keys `a,t,4,q,#` and `q#qa`, and two rows of boxed terms. This is not a second narrated calculation. At `02-00-00.png` and the independently inspected `07200s.png`, the `1/5` box is selected. The two rows beneath `q#qa` read as follows (spaces separate boxes; superscripts are represented by a caret and parentheses in the transcription):

```text
qx1000 + #x100 + qx10 + ax1
qx1000 + #x(ta)^(4) + qx(ta)^(t) + ax(ta)^(a)
```

The first box on the second row still reads `qx1000`; the screen does **not** show it rewritten as another power of `ta`. The glyph resembling the digit `4` is the third member of this particular alphabet. The display is a diagram, not executable Python: the `x` is a multiplication mark in the drawing, and the carets above are editorial, not typed operators.

**GitHub: a viewed document, not an edit.** `02-02-00.png` shows an existing rendered README. The heading is `Project`; the file label is `README.md`, with `First two project links` beside it. The four numbered aims, as displayed, are:

1. You get experience managing a repo (especially with other developers).
2. You get experience with additional features, e.g. pull requests.
3. I get to try delegating control to you.
4. We get to explore more cool technologies than otherwise.

The two links are `https://github.com/scriptz0340/Link` and `https://github.com/Wolfster427/AnyBase`. `02-03-00.png` shows `joes-ta / Hub`, the `Public` badge, the `main` branch, and `Code` selected; the pointer is on `Projects`. Subsequent originals `02-04-00.png`, `02-05-00.png`, and `07500s.png` still show the Code page. There is no observed board, invitation, merge, or permission-change result.

**Discord: a newly visible channel and an unsubmitted-looking draft.** In the Joe’s TA server, `02-07-00.png` shows `hub` below `general`, `Search or create a post...`, and `Get Started (1 of 5 Completed)`. `02-08-00.png` still offers `New Post`. In `02-09-00.png` the composer reads:

```text
README
This is a place for discussing the github Hub enhancements
```

The visible insertion caret is not punctuation; no final period is visible. `Post` remains available. The setup checklist shows `Set recommended permissions` checked, while `Create post guidelines`, `Create tags`, `Set a default reaction for posts`, and `Make the first post!` are unchecked. The `07760s.png` capture is the *course* server’s `general` channel at 2:09:20, not the project server forum. No posted README is visible there. The pre-existing message about character sets in that chat is not a closing regular-expression demonstration.

## Micro-Learning Insights

This final appendix is a detachable set of practice cues. The worked examples, definitions, and historical record are complete without it.

### Inspect the function, not just its output

- **0:01:00–0:05:00:** Keep your project repository distinct from its listing in the Hub. Check the issue *state* before assuming a task was never created.
- **0:06:00:** Read the short commit message as well as its hash: `7cf1688` is the user-functions example, `17e38e5` the earlier `sys.argv` example. `git log --oneline` is inspection, not checkout.
- **0:07:05:** Start with the line the traceback points to, then read backward to the promise Python could not fulfill: a body after `def`.
- **0:09:00 / 0:09:30:** Passing a turn and writing “to be continued” explain the instructor’s style preference. Only `pass` is a keyword; only `...` can be an argument.
- **0:10:17–0:13:00:** Parentheses change the question: `dir` identifies a callable, `dir()` lists current names, `dir(...)` inspects the ellipsis object.
- **0:18:35–0:20:00:** Trace who *returns* and who *prints*: `return 42` supplies the integer to the f-string; `print` displays it.

### Debug one decision at a time

- **0:23:20 — trust the braces.** The literal prefix says fresh while the evaluated call supplies `"not fresh"`. Read them independently before explaining the result.
- **0:24:18–0:26:40 — predict the kind of failure.** Ask “parse, bind, run, intended result?” rather than guessing that an integer passed as milk must crash.
- **0:29:11 — save the file you intend to test.** The instructor notices a missing save; a repeat of the old `27` does not refute the new flour branch.
- **0:30:23–0:32:00 — no flour can mean two different things.** Omitting a required argument is an error; supplying `None` fills the slot. A default changes only the former rule.
- **0:34:06–0:36:11 — different paths can look identical.** Distinct return values or a small diagnostic can expose the branch actually taken. Changing flour cannot affect a path that never reads it.
- **0:39:42–0:41:00 — let the function report its local value.** Printing eggs turns a hidden positional mismatch into the line `whole wheat`. Switching to keyword arguments changes that line to `3` without changing the returned `spoiled`.
- **0:43:45–0:44:36 — decide what the function should do.** Pick a specific prediction first. A deliberately broken small example can become a useful class issue if peers can identify the expected behavior and isolate the failure.

### Build a diagram you can reason with

- **Choose the drawing tool by the task (0:46:16).** Freehand drawing in Paint is useful when you want a free-form mark. For six aligned place weights, make one text rectangle in draw.io and reuse it.
- **A web editor is not necessarily your storage provider (0:47:14–0:49:26).** The visible `www.drawio.com` page pairs “Start Diagramming” with “Desktop App.” A storage integration introduces a permission and history decision even when the editor requires no account of its own.
- **Protect the history you intend to read (0:48:45).** The instructor’s objection to auto-committing was future comprehension. No repository needs to be modified to understand that trade-off.
- **Recover before diagnosing (0:50:48).** Undo a bad paste, select the intended shape and copy again. Capture an unexplained long string if it appears; guessing its format is not a diagnosis.
- **Ask what stays unchanged (0:56:24–0:57:32).** One step right divides the weight by ten on both sides of the ones column. That same rule explains 10⁰ and negative exponents.
- **A copied label can lag behind the idea (1:00:00–1:01:00).** Two adjacent `1/10` boxes appear briefly; the final pair is `1/10`, `1/100`. Don't read an in-progress duplicate as a new rule.
- **Mindset can be a problem-solving tool (1:00:24–1:00:52).** “Play with numbers” meant following a pattern despite anxiety about the word *math*, not dispensing with arithmetic.
- **Name the general case (1:05:10–1:06:41).** Say *radix point* if the base could change. The ones place belongs on the whole side even though its exponent is neither positive nor negative.
- **Make the two states distinguishable first (1:08:20–1:09:17).** `1` and `0` describe an encoding; they are not tiny printed digits the circuits must recognize. Tom and Jerry makes the arbitrariness of the labels easier to remember.

### Test a notation at its boundaries

- Write down the ordered legend first. The most misleading digit in this exercise is the one that looks familiar: `4` counts two, not four (1:14:06–1:14:38).
- To recover a forgotten weight, start at one and walk. Walk left for ×5 and right for ÷5; this produces 125, 25, 5, 1, 1/5 and 1/25 in the order drawn (1:19:15–1:21:00).
- Ask two different questions: “How many characters are in this string?” and “How many *different* characters have I needed?” `15625` has five characters but only four distinct ones, and adds none to the prior inventory (1:26:02–1:26:26).
- Test a counter at the boundary. If your alleged next number after `#` is `aa`, it has gone back to zero without recording the completed cycle. Look instead for `ta` (1:28:30 event).
- Read `10` aloud as “one group of the base and zero ones” before naming a quantity. This prevents importing a ten into a group of five (1:29:18–1:31:41).
- A counter's column labels are not its only possible readings. Changing the digit *within* a column is why there can be counts between five and twenty-five (1:31:56–1:33:18).

### Read shapes as digits

- Treat `ta` as one base numeral when reading `ax(ta)^a`: the `a` inside parentheses belongs to the base; the one above the line is an exponent (1:38:54–1:40:28).
- Use a nonzero digit in a non-unit place to test a disputed base: the three fives in `qx(ta)^t` reveal an error that zero ones can hide (guide check of 1:40:14–1:40:28).
- Do not count corners to assign a digit; square and diamond would both get four, defeating the stated order (1:50:14–1:50:51).
- Read across one row to decode a value; read down one column to see how long a digit holds. The middle column at 1:56:00 repeats in blocks of three.
- Left padding tells a true but often unwritten fact: there are zero groups of the higher weight. This explains the visual preference for showing omitted binary zeros in the class discussion (1:54:46–1:55:26).

### Make collaboration and its status visible

- Start with the public account already linked to a project; ask before adding someone else’s name or email. Then think separately about the author details carried by commits (1:59:20–2:00:51).
- Underline vs. pointer: `Code` is selected while the cursor rests on `Projects`. This small UI distinction rules out claiming a project board was opened (2:03:00).
- Decide whether you need a conversation, a proposed change or direct maintenance access. Those lead, respectively, to an issue, a pull request or an explicitly granted collaborator role (2:04:33–2:05:54).
- Put the new `hub` forum just below `general`: the student’s suggestion solves a specific discoverability problem rather than merely tidying the sidebar (2:06:27–2:06:52).
- A webinar or recording can show a control without showing its result. At 2:09:00 `Post` is an available action, not proof of a published post.
