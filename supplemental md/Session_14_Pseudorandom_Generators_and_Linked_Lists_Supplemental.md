# Pseudorandom generators and linked lists

**CSCS1240F26 · Session 14 · Compressed technical companion**

Jenny and Genny are different generators. Why do they return the same number? Apple and peach are different nodes. Why do two expressions eventually reach the same peach? For each assignment, predict **what the right side reads** and **where the left side puts the result**. That distinction explains both demonstrations.

Classroom values and historical line numbers below come from the supplied Session 14 Markdown. All code comments are instructional. Examples marked **practice** use controlled inputs or altered code. Run each named driver in a fresh Python process. Addresses vary between processes.

## 1. Build the next integer from the previous one

In `lcg.py`, the first line begins with `#`. The phrase “object/class call” is a comment. The first executable action is `import time`. Read `int(time.time())` from the inside out. The first `time` names a module, the dot selects its `time` function, and `()` calls it. The result counts seconds from the Unix epoch (January 1, 1970, UTC). `int` truncates toward zero. A timestamp needs both a unit and a reference point, just as “28 minutes since our 9 a.m. start” identifies 9:28.

A **linear congruential generator (LCG)** carries an integer forward. For current state `x`, multiplier `a`, increment `c` and modulus `m`, its successor is `(a * x + c) % m`. The subscript in the mathematical notation Xₙ₊₁ counts steps. The parameter table discussed in class uses `m > 0`, `0 < a < m` and `0 ≤ c < m`, with an initial residue between 0 and m − 1. The Python class can start with a whole-second count because its first update reduces it modulo m.

### Worked calculation · practice input

```python
stamp = 2.75                   # Teaching clock reading in seconds.
x = int(stamp)                 # Drops .75. The seed is 2.
a = 1103515245                 # Multiplier from the classroom program.
c = 12345                      # Increment applied after multiplying.
m = 2**31                      # Exponentiation gives 2147483648.
x = (a * x + c) % m            # Read old x before rebinding the name.
                               # The next recurrence reads 59559187.
print(x)                       # Displays 59559187.
```

| Operation | Input | Result | What is stored in x? |
|---|---|---|---|
| Truncate | 2.75 | 2 | Seed 2 |
| Multiply | 1103515245 × 2 | 2207030490 | Still 2 |
| Add | 2207030490 + 12345 | 2207042835 | Still 2 |
| Reduce | 2207042835 % 2147483648 | 59559187 | Still 2 during evaluation |
| Assign | Completed right side | Name rebound | 59559187 |

In the actual module this update executes **inside the class body**, once as the class is defined in the driver’s fresh process. It precedes every call to the generator method. Starting twice within one second can repeat a sequence. For example, 1234.2 and 1234.9 both truncate to 1234. A fixed seed makes a code change easier to test. With seed 1, the class-body update gives 1103527590 and the first method update gives 377401575.

**Key Items to Scan**

- `_x` first receives the seed, then immediately receives its successor. A diagnostic below the second assignment displays the successor.
- `class Lcg:` starts an indented body. Capitalization and the leading underscore in `_x` are PEP 8 naming conventions. Python still permits attribute access.
- A stored time count and a running clock have different requirements. Volatile memory can lose contents without power. Flash can retain a count, but retention alone cannot advance it. Year interpretation (Y2K) and exhausting a fixed-width count are different representation limits.

## 2. Remainders, cycles and the limits of a pattern test

At 14:00, `14 % 12` leaves 2. The quotient counts complete groups and the remainder says how much is left. On a clock face the residue 0 is commonly labeled 12. In the generator the stored residue stays an integer from 0 through m − 1.

```python
print(5 % 1)                   # Five whole groups of one leave 0.
print(8 % 2)                   # Even: four complete pairs, prints 0.
print(9 % 2)                   # Odd: one left over, prints 1.
print(5 % 0)                   # Raises ZeroDivisionError before print runs.
```

A **prime** has exactly two distinct positive divisors. Seven is prime, one is not, and six and twelve have additional factors. The possible remainders modulo 7 are still 0 through 6: try dividends 7 through 13. In the special multiplicative case `c = 0` with prime m, a suitable multiplier and nonzero seed can give a period of m − 1. Primality alone cannot promise that period.

### Trace the three classroom cycles · practice driver

The source describes these as diagram values. The runnable version below emits the states one per line, including the first repeated seed. The stopping test is appropriate for these three known cycles, all of which return to their seed.

```python
def cycle(seed, a, c, m):      # Accept the start and the three rule inputs.
    x = seed                   # Keep seed for comparison as x changes.
    print(x)                   # Display the starting state before moving.
    while True:                # Take at least one step, even at the seed.
        x = (a * x + c) % m    # From 8, doubling modulo 9 gives 7.
        print(x)               # Include the state that closes the cycle.
        if x == seed:          # Test after moving to count a real return.
            break              # Leave the loop. The function ends here.

cycle(1, 2, 0, 9)              # First column below, period 6.
cycle(3, 2, 0, 9)              # Same rule, different orbit, period 2.
cycle(0, 4, 1, 9)              # Mixed generator visits all nine residues.
```

| Update | Seed 1, double mod 9 | Seed 3, double mod 9 | Seed 0, multiply by 4 and add 1 mod 9 |
|---|---|---|---|
| Start | 1 | 3 | 0 |
| 1 | 2 | 6 | 1 |
| 2 | 4 | 3 · stop | 5 |
| 3 | 8 | Finished | 3 |
| 4 | 7 | Finished | 4 |
| 5 | 5 | Finished | 8 |
| 6 | 1 · stop | Finished | 6 |
| 7 | Finished | Finished | 7 |
| 8 | Finished | Finished | 2 |
| 9 | Finished | Finished | 0 · stop |

An **increment of zero** gives a multiplicative generator (Lehmer generator). A nonzero increment gives a mixed generator, technically an affine update. A full period alone is a weak test of apparent randomness. With a = c = 1 and m = 9, a counter visits every residue in numerical order. The class also saw paired values that concealed a relationship when the lists were read separately. X began 233, 232, 75, 2, 61, 108 and Y began 0, 1, 158, 231, 172, 125. Every corresponding sum leaves 233 modulo 256.

The glibc row labeled “used by GCC” supplied the classroom constants. With the same modulus, Borland C/C++ listed a = 22695477 and c = 1. The table describes parameters in use. Its glibc output column names bits 30 through 0, whereas the Python driver returns a fraction. RANDU was mentioned in the displayed material as a poor parameter choice. GCC stands for GNU Compiler Collection.

**Key Items to Scan**

- **Period** counts transitions. The written sequence 3, 6, 3 has period two. A seed change can select an entirely different cycle.
- **Determinism** means the same inputs give the same sequence. Surprise, lack of control and sensitivity to the starting state do not establish randomness. A game-world seed illustrates reproducibility.
- **Probability** assigns chances and **statistics** examines observed patterns. Neither equal frequencies nor an unfamiliar clock seed establishes unpredictability. Deterministic hashing also supplies no fresh randomness; the class used it as a comparison.

## 3. Run one method with two possible state owners

**Classroom reconstruction · `src/lcg.py`.** This complete file uses the early listing for the bounded branches and the later class-body diagnostic. The source could not read the right ends of two later scaling expressions. The grouping below uses parentheses for continuation, so comments can accompany each step. The original had explicit backslashes. Copy the following two blocks consecutively into one file, preserving the method’s four-space indentation.

```python
import time                         # Bind the module in lcg.py.

class Lcg:                          # Execute this body at class definition.
    _x = int(time.time())           # Call the clock, truncate, store the seed.
    _multiplier = 1103515245        # Shared coefficient a.
    _increment = 12345              # Shared additive constant c.
    _modulus = 2**31                # Shared modulus m.
    _x = (_multiplier * _x + _increment) % _modulus  # First update.
    print(f"_x: {_x}")              # Once per definition, before driver IDs.
```

```python
    def next(self=None, min=None, max=None):  # Ordinary method with defaults.
        if self == None:                      # Class call supplied no receiver.
            print(f"class id: {id(Lcg)}")      # Format the class ID, then print.
            Lcg._x = (Lcg._multiplier * Lcg._x       # Read old class state.
                      + Lcg._increment) % Lcg._modulus  # Write class residue.
            if min == None or max == None:    # Missing min skips the max test.
                return Lcg._x / Lcg._modulus  # One missing bound: fraction.
            else:                             # Both bounds supplied.
                span = max - min              # Width, e.g. 30 - (-10) = 40.
                minmax = Lcg._x / Lcg._modulus * span + min  # Stretch, shift.
                return minmax                 # The class keeps integer _x.
        else:                                 # self is Jenny or Genny here.
            print(f"object id: {id(self)}")    # Diagnostic precedes any return.
            self._x = (self._multiplier * self._x       # May read class _x.
                       + self._increment) % self._modulus  # Write on self.
            if min == None or max == None:    # If min exists, check max.
                return self._x / self._modulus  # Return this object's fraction.
            else:                             # Both endpoints exist.
                span = max - min              # Compute the requested width.
                minmax = self._x / self._modulus * span + min  # Scale result.
                return minmax                 # self._x stays an integer.
```

`jenny.next()` supplies Jenny as the first argument automatically. `Lcg.next()` supplies no instance, so the explicit default `self=None` survives and the method names `Lcg` itself. This unusual signature is an ordinary method. There is no `@classmethod` decorator. A standalone function with a parameter called `self` receives only what the caller supplies. The method named `next` is also distinct from Python’s iterator hook `__next__`. For `None` tests, `is None` is the usual identity spelling; the class used `== None`.

### Scale the return without changing the stored integer

**Practice · `src/bounds.py`.** Run with `py .\src\bounds.py` from the practice project root. Names `min` and `max` are parameters here and shadow the built-in functions of those names. Use keyword arguments on the class call: a first positional number would bind to `self`. On an instance call Python already supplies that first argument.

```python
from lcg import Lcg                  # Import also runs the class diagnostic.
Lcg._x = 1103527590                  # Teaching reset before each comparison.
print(Lcg.next())                    # Class ID, then 0.1757413032464683.
Lcg._x = 1103527590                  # Restore the same input state.
print(Lcg.next(min=10))              # Missing max: same fraction.
Lcg._x = 1103527590                  # Each call advances first.
print(Lcg.next(max=20))              # Missing min: skip the right comparison.
Lcg._x = 1103527590                  # Reuse the identical numerator.
print(Lcg.next(min=10, max=20))      # Class ID, then 11.757413032464683.
print(Lcg._x)                        # Displays 377401575, still an integer.
```

| Temperature calculation | Result | Why this step matters |
|---|---|---|
| Normalize an updated state, `x / m` | Fraction in [0, 1) for these constants | Zero is possible. The greatest state is m − 1. |
| Use teaching fraction 0.25, limits −10 and 30 | Width = 40 | Subtract the lower endpoint, including its sign. |
| Multiply by width | 0.25 × 40 = 10 | Distance above the lower endpoint. |
| Add the lower endpoint | 10 + (−10) = 0 | Result on the temperature scale [−10, 30). |

For the ordered classroom limits, the lower endpoint is included and the upper is excluded. Choosing a negative minimum exposes a forgotten shift that a test with zero would hide. Giving `span` a name makes the arithmetic easier to inspect. Scaling the return never alters the integer used by the next update.

**Key Items to Scan**

- `self._x` on the right can find the class attribute. The assignment on the left creates or updates an attribute on the receiving instance. Jenny’s first write leaves Genny’s lookup unchanged.
- `or` short-circuits. If the minimum is missing, Python already knows the result. The scaling branch needs **both** endpoints.
- **Print / return** serve different readers. The method prints an ID as deliberate instrumentation. Its numeric return goes to the caller, who may display it, store it or discard it.

## 4. Match seven calls to six displayed fractions

**Classroom driver with one practice reset · `src/view.py`.** Save beside `lcg.py` and run `py .\src\view.py`. In the reported lettered run the class body printed `_x: 1007915657`. That was an already-updated integer. The reset below reproduces the rest of that run from this known point. The first clock-derived line on your machine will vary.

```python
import lcg                         # Bind the module; class body has run.
lcg.Lcg._x = 1007915657            # Practice only: replay after initialization.
jenny = lcg.Lcg()                   # Construct J, initially without local _x.
genny = lcg.Lcg()                   # Construct independent instance G.
lenny = lcg.Lcg                     # No (): alias the existing class.
print(f"id jenny: {id(jenny)}")     # Identify J in this process.
print(f"id genny: {id(genny)}")     # A different live object.
print(f"id lenny: {id(lenny)}")     # This is the class ID.
print(f"id lcg: {id(lcg)}")         # Module object has its own ID.
print(f"id lcg.Lcg: {id(lcg.Lcg)}")  # Matches lenny within the same run.

print(f"a   {lenny.next()}")        # Inner call prints ID before a appears.
print(f"b   {jenny.next()}")        # J reads class state, writes J's state.
print(f"c   {genny.next()}")        # G makes the same first local update.
jenny.next()                       # Advances J. Only the object's ID prints.
print(f"d   {lenny.next()}")        # Class now makes the step J made at b.
print(f"e   {jenny.next()}")        # Advances from the unprinted return's state.
print(f"f   {genny.next()}")        # Reaches J's earlier undisplayed value.
```

### Lettered run · read one row per call

In the source, class ID **2021985949376** also identified Lenny; the module ID was **2021984147136**. Jenny’s ID **2021984535952** and Genny’s **2021984486736** distinguish the receivers. Each method call printed its receiver’s ID. Fractions at letters a–f are classroom-reported. Intermediate integers and the bare return follow from replay. A dash under J or G means that instance still reads the current class value.

| Step / old integer read | Class after | J local after | G local after | Return and display |
|---|---:|---:|---:|---|
| Initial, before calls | 1007915657 | — | — | Class body displayed its state |
| a · class 1007915657 | 703681166 | — | — | 0.32767707761377096 |
| b · class 703681166 | 703681166 | 1253808815 | — | 0.5838502268306911 |
| c · class 703681166 | 703681166 | 1253808815 | 1253808815 | 0.5838502268306911 |
| Bare J · 1253808815 | 703681166 | 224157372 | 1253808815 | 0.10438141040503979, discarded |
| d · class 703681166 | 1253808815 | 224157372 | 1253808815 | 0.5838502268306911 |
| e · J 224157372 | 1253808815 | 1452920389 | 1253808815 | 0.6765687786974013 |
| f · G 1253808815 | 1253808815 | 1452920389 | 224157372 | 0.10438141040503979 |

**Read the original line numbers.** In the earlier corrected, unlettered run, driver lines 13, 14, 15, 17, 18, 19, 20 used the same call order. Its displayed decimals were 0.39060565223917365, 0.029102258384227753, 0.029102258384227753, **no decimal at 17**, 0.029102258384227753, 0.5764116561040282 and 0.7909301412291825. Line 4 bound Genny to an instance. Line 15 printed her first return. Neither line 15 nor line 20 rebound the name `genny`. Labels make those claims testable across runs whose digits differ.

### Module found, class name missing

**Practice · separate drivers beside `lcg.py`.** Run each block by itself. The first recreates the line-1 wrong-module experiment. The next recreates the line-23 bare-name failure from `view as.py`. The last gives both working alternatives.

```python
import lcg1                      # Deliberate misspelling: ModuleNotFoundError.
```

```python
import lcg                       # Succeeds; lcg.Lcg is available.
penny = Lcg                      # NameError: no bare Lcg in this driver.
```

```python
import lcg                       # Bind the module for qualified access.
penny = lcg.Lcg                  # Alias its class without constructing it.
from lcg import Lcg              # Bind the very same class by its short name.
print(penny is Lcg)              # True: two lookup routes, one class.
penny = Lcg                      # Classroom spelling now succeeds.
print(f"   {penny.next()}")       # Class ID, then the returned fraction.
```

The later completed Penny run began at `_x: 394753958`. After the original seven calls, Penny’s class call printed `class id: 2255015872816` followed by `0.38841727236285806`, matching Genny’s preceding fraction. The short practice driver establishes the same binding; reproducing that decimal also requires the same start and seven preceding updates.

**Key Items to Scan**

- `lcg`, `lcg.Lcg` and `lcg.Lcg()` select a module, a class and a new instance respectively. In a filename the dot in `view.py` marks an extension.
- The first lookup through a new instance sees the **current** class state. Construction does not freeze the then-current number.
- `id` identifies an object for its lifetime. Test two live references in one process with `is`. Similar addresses or equal fractions cannot establish that two names refer to the same object.

## 5. Find the file and the revision the driver actually uses

The generator driver and module both live in `src`. Running the script adds its own directory to the module search path. The shell prompt can still be one directory higher. The Item project instead puts `item.py` in `src` and `fruit.py` in `test`. If `item` cannot be imported, no node code runs. Inspect this environment before debugging links.

| PowerShell action | What it shows or changes |
|---|---|
| `cd ..\Lcg\`, then `ls` | Select the generator project and list its directories. A listing does not launch the driver. |
| `py '.\src\view as.py'` | Launch the driver with a space in its filename. Quoting preserves it as one argument. |
| `.\script\path.ps1` or `& .\script\path.ps1` | Execute PowerShell commands in the script. A silent assignment produces no listing. |
| `echo .\script\path.ps1` | Display the pathname argument. |
| `cat .\script\path.ps1` | Display the contents. In class the file contained the assignment below. |
| `py .\test\fruit.py` | Launch the Item driver once its module is findable. |

```powershell
$env:PYTHONPATH="$env:PYTHONPATH;C:\CSCS1240F26\Item\src"
```

PowerShell first expands the previous value inside double quotes, appends a Windows path-list separator and the classroom source directory, then assigns the new string to the process environment. Repeated execution appends another entry. Spaces around `=` are legal in PowerShell. For the separate practice project, run the following from its root in a fresh shell to append **its** source directory. The course also suggested putting a scratch driver beside its module to isolate a path failure. No package installation is needed for sibling files.

```powershell
$env:PYTHONPATH="$env:PYTHONPATH;$PWD\src"
py .\test\fruit.py
```

### Read the Git history as a sequence of changes

| Command or revision in the source | Evidence and consequence |
|---|---|
| `git status` and `git log --oneline` | Tip `843e915` was on `master`. Tracked `script/path.ps1` was modified. Untracked entries existed and nothing was staged. An untracked file can be present without being ignored. |
| `git rev-list --count HEAD` / `master` / `HEAD..master` | Historical counts 22, 22, 0 respectively. The range counts commits reachable from master but excluded by HEAD. It does not change the checkout. |
| `master~22`, then `master~21` | First lookup failed with a pathspec error. In this linear history the starting commit is step zero, so 21 first-parent steps reach the oldest of 22 commits. |
| `git checkout 47413b4` | Detached HEAD at the early direct-link version. The source reports `item.py` changed from 5561 bytes at the developed version to 66 bytes here. |
| `master~20` → `f70c1ad` | The driver gained a second reference. The node class stayed the same. |
| `master~19` → `0203781` | First attempt refused to overwrite edits in `test/fruit.py`. After the instructor restored that file and retried, the driver used `prev`. The unrelated modified path script remained. |
| `2499ca0` | Added optional initialization; the later required-argument edit was a separate state. |

**Historical command with a destructive effect.** `git restore .\test\fruit.py` discarded the instructor’s unstaged edits to that tracked file. `Aborting` was the result of the preceding failed checkout. To preserve an experiment, copy, commit or stash it before switching. A changed-on-disk editor indicator calls for comparing the buffer with the checked-out file. A traceback above the most recent prompt may belong to an older run. No repository changes are needed to use the examples here.

**Key Items to Scan**

- `__pycache__` is generated cache data. In the source listing, `d----` marked its directory and `-a---` appeared on files. Edit the Python source for the intended revision.
- `PYTHONPATH` supplements the module search path for the launched process. `pip` and virtual environments were discussed as other contexts for imports.
- A commit records parents. The linked-list analogy fits a first-parent walk; merges can have multiple parents. Commit messages describe changes for a reader. Python executes the saved driver.

## 6. Build three nodes and keep the first one reachable

A classical array provides indexed access into contiguous element storage. Extending that block can require moving it. The fruit example instead follows references. Apple knows its successor. Adding a reverse link lets banana find its predecessor. Python’s built-in list is a separate structure. The instructor uses one `Item` class for nodes and keeps a reference to the first node, without a separate list container.

**Classroom · `src/item.py` at `47413b4`.** This class also serves the traversal and backward-link revisions. Each attribute initially resolves to the class default until the driver writes one on an instance. `object` here is an attribute in the class namespace. The built-in `object` remains available elsewhere.

```python
class Item:                     # Define the node type; no node exists yet.
    object = None               # Payload absent until an instance has a value.
    next = None                 # No successor by default.
    prev = None                 # No predecessor by default.
```

**Classroom · direct-access `test/fruit.py`.** Use the path setup in section 5.

```python
from item import Item                # Locate the module, then bind its class.
fruit = Item()                       # Construct the first node, A.
fruit.object = "apple"               # Store text on A; fruit still refers to A.
fruit.next = Item()                  # Create B, then store B in A.next.
fruit.next.object = "banana"         # Reach B through A.next; write B.object.
fruit.next.next = Item()             # Create P, then write B.next = P.
fruit.next.next.object = "peach"     # Follow two links; write P.object.
print(fruit.object)                  # apple
print(fruit.next.object)             # banana
print(fruit.next.next.object)        # peach
```

| After operation | Payload written | Forward link written | Other relevant state |
|---|---|---|---|
| `fruit = Item()` | None | None | Head is A |
| `fruit.object = "apple"` | A holds apple | — | No B yet |
| `fruit.next = Item()` | B still reads None | A.next → B | B.prev still None |
| `fruit.next.object = "banana"` | B holds banana | — | A still holds apple |
| `fruit.next.next = Item()` | P still reads None | B.next → P | P.prev still None |
| `fruit.next.next.object = "peach"` | P holds peach | — | P.next and all prev links are None |

### Diagnose the second reference before and after the move

**Classroom diagnostic sequence · `f70c1ad` and subsequent edits.** Retain the import and construction from the direct driver; **replace its three final prints** with this block. The first revision moved and printed the payloads. Later edits added the end comparison, the start comparison and finally the direct route to peach. This listing combines the completed diagnostics.

```python
iter = fruit                    # Both names now refer to A; no node was copied.
print(fruit)                    # Node representation, including runtime ID text.
print(iter)                     # Same representation while both point at A.
print()                         # Print a blank separator; no state changes.
print(iter.object)              # apple
iter = iter.next                # Read A.next, then rebind only iter to B.
print(iter.object)              # banana
iter = iter.next                # Now read B.next: the same syntax reaches P.
print(iter.object)              # peach
print(fruit)                    # Still the original apple node.
print(iter)                     # Now the peach node.
print(fruit.next.next)          # A.next is B, B.next is P: same node as iter.
```

One historical run showed the same apple representation ending in `BC18590>` twice at the start. After the walk, apple still ended in `BC18590>` and peach ended in `BC0C690>`. In a later run the last two representations both ended in `368C690>`. Compare within a run. Addresses do not specify adjacency. The student’s suggestion `iter = fruit.next` works on the first move; repeating it selects banana again because `fruit` never moved.

**Key Items to Scan**

- `fruit.next` is a reference to the banana **node**. `fruit.next.object` is its **payload**, the string `"banana"`.
- `iter = fruit` copies a reference. `iter = iter.next` moves a local name. Writing `fruit.next` changes a link inside a node.
- Preserve the head. If the only name for apple moves forward and no back link exists, banana supplies no route to recover apple. Here `iter` is an ordinary variable that shadows the built-in name. No iterator protocol is invoked.

## 7. Wire the return path and locate the first failing lookup

**Classroom · `0203781`.** Keep the four-line `Item` class. Replace the driver with both blocks below in order. The reverse links belong to the destination nodes; constructing a successor by itself supplies only a forward route.

```python
from item import Item                # Same node class as the direct version.
fruit = Item()                       # A is the head.
fruit.object = "apple"               # A holds apple.
fruit.next = Item()                  # Set A.next to B; B.prev still None.
fruit.next.prev = fruit              # Right side gives A; owner of prev is B.
fruit.next.object = "banana"         # Fill B's payload.
fruit.next.next = Item()             # Set B.next to P; P.prev still None.
fruit.next.next.prev = fruit.next    # RHS finds B; A.next.next finds P.
                                     # Assign P.prev = B. Both pairs now link.
fruit.next.next.object = "peach"     # P has no successor.
```

```python
iter = fruit                         # Start at A while fruit retains the head.
print(iter.object)                   # apple
iter = iter.next                     # A.next reaches B.
print(iter.object)                   # banana
iter = iter.next                     # B.next reaches P.
print(iter.object)                   # peach
iter = iter.prev                     # P.prev leads back to B.
print(iter.object)                   # banana
iter = iter.prev                     # B.prev leads back to A.
print(iter.object)                   # apple
```

| Action | Cursor before | Link read | Cursor after | Following display |
|---|---|---|---|---|
| Start | Unbound | Head reference | A | apple |
| Forward | A | A.next = B | B | banana |
| Forward | B | B.next = P | P | peach |
| Back | P | P.prev = B | B | banana |
| Back | B | B.prev = A | A | apple |

### Overshoot peach · two historical edits

For each experiment use a fresh two-way driver and insert one of these blocks **after the peach print, before the first backwards move**. The first three fruit names display in both runs. The original additions began at line 18. An unhandled exception prevents the two backwards moves from running.

```python
iter = iter.next                     # Historical line 18: P.next yields None.
print(iter.object)                   # Line 19: lookup fails before print runs.
```

```python
iter = iter.next                     # Same legal move to None.
print(iter)                          # New line 19 displays None by itself.
print(iter.object)                   # Now line 20 raises the same AttributeError.
```

The exception text is `AttributeError: 'NoneType' object has no attribute 'object'`. Printing the receiver exposed the bug. Three preceding prints establish that this file parsed and started executing. A syntax error in this driver would have prevented those prints. A logic error, such as restarting at `fruit.next` on both moves, can instead finish with the wrong fruit.

### Append cherry and walk to the boundary · complete practice extension

Cherry was proposed in class. The implementation below is **supplemental**. Append this block to the successful two-way driver. The guard also works when the starting reference is `None`. The function assumes a forward chain ending in `None`.

```python
peach = fruit.next.next              # Save P, the existing tail.
cherry = Item()                      # Allocate a fourth node C.
cherry.object = "cherry"             # Put the text inside C.
peach.next = cherry                  # Forward edge exists; C.prev still None.
cherry.prev = peach                  # Now the new pair works in both directions.
print(cherry.prev is peach)          # True: C leads back to the original P.

def show_forward(current):           # current is a local traversal name.
    while current is not None:       # Test before reading the payload.
        print(current.object)        # Displays one value per reachable node.
        current = current.next       # At C this yields None; head stays at A.

show_forward(fruit)                  # apple, banana, peach, cherry on four lines.
show_forward(None)                   # Empty start: no iterations and no output.
```

| New operation | P.next | C.prev | Consequence |
|---|---|---|---|
| Create C and store its payload | None | None | C is separate from the chain. |
| `peach.next = cherry` | C | None | Forward traversal reaches C. |
| `cherry.prev = peach` | C | P | Reverse traversal from C reaches P. |
| Cursor reads C.next | C | P | Cursor becomes None; the guard stops before a lookup. |

**Key Items to Scan**

- The head’s `prev` and the tail’s `next` can legitimately remain `None`. A forward walk can pass even if a reverse link is missing. Test both directions.
- Draw one box per node, one arrow per link and a separate arrow for the cursor. Move only the arrow named on the left side of the assignment.
- Print the object left of the failing dot. `print(None)` succeeds; asking for `None.object` is the failure. A guard changes control flow, whereas the historical diagnostic only exposed state.

## 8. Change the construction contract

**Classroom · optional-value `src/item.py` at `2499ca0`.** Replace the class used in the earlier examples. `__new__` creates an instance in ordinary construction; `__init__` receives that instance as `self` and initializes its state. The caller supplies `obj`. Merely binding `fruit = Item` would refer to the class and call neither hook. The source describes that as a temporary editor change with no matching classroom run.

```python
class Item:                          # Same node type, revised public interface.
    object = None                    # Class-level payload default.
    next = None                      # No forward neighbor until linked.
    prev = None                      # No backward neighbor until linked.

    def __init__(self, obj=None):    # Allow an omitted payload.
        self.object = obj            # Write an attribute on this new instance.
```

**Classroom · optional driver.** Follow this setup with the unchanged two-way walk in section 7 and run `py .\test\fruit.py`. It still prints apple, banana, peach, banana, apple. Building the nodes takes fewer assignments, while traversing them requires the same links.

```python
from item import Item                 # Use the optional-value revision.
fruit = Item()                        # A now owns object = None.
fruit.object = "apple"                # Replace only A's initial value.
fruit.next = Item("banana")            # Positional input initializes B.
fruit.next.prev = fruit               # Still the caller's job to link B back.
fruit.next.next = Item(obj="peach")    # Keyword obj supplies P's payload.
fruit.next.next.prev = fruit.next     # Wire P back to the existing B.
```

**Classroom change · required-value revision.** In the same class replace only its initializer by the following method. This is an indented excerpt. Keep the three class attributes as above.

```python
    def __init__(self, obj):          # No default: caller must supply obj.
        self.object = obj             # Reached only after arguments bind.
```

The unchanged driver’s first call, `fruit = Item()` at historical line 3, now raises `TypeError` before `fruit` is bound. No fruit prints. An isolated reproduction gives `TypeError: Item.__init__() missing 1 required positional argument: 'obj'`. The source reports that the end of the classroom error was obscured. The later valid `Item(obj="peach")` call cannot rescue line 3. In isolation its right side requires a bound `Item`, and the assignment target separately needs a working `fruit.next`.

**Practice · working required-value driver.** This is an independent complete setup. It also distinguishes enforcing an argument from validating a payload.

```python
from item import Item                 # Load the class requiring obj.
fruit = Item("apple")                 # Initialize A during construction.
fruit.next = Item("banana")            # Set A.next to the initialized B.
fruit.next.prev = fruit               # Set B.prev to A.
fruit.next.next = Item(obj="peach")    # Set B.next to the initialized P.
fruit.next.next.prev = fruit.next     # Set P.prev to B.
print(fruit.next.next.object)         # peach: the later assignment can now run.
empty = Item(None)                    # One explicit argument satisfies binding.
print(empty.object)                   # None: the class accepts this value.
```

**Key Items to Scan**

- `obj=None` in a definition sets a default. `obj="peach"` in a call supplies a keyword argument. `self.object = obj` stores the supplied value on one instance.
- `Item()` and `Item(None)` can produce identical payloads with the optional revision. Only the explicit argument works with the required revision. A value can also be something other than text.
- **Future application.** If each symbol in an ordered alphabet stores its successor, the program can navigate symbols by following links. The instructor proposed the idea; no symbolic arithmetic procedure or assignment was demonstrated.

## 9. Consolidated syntax reference and library of definitions

### Expressions and generator state

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `#` | Start a source comment. | `# object/class call` | The words do not invoke anything. |
| `import` | Load a module and bind its name. | `import time` | Its members remain qualified. |
| `.` | Look up a member on the object to its left. | `time.time` | Selects the function; the call follows. |
| `()` | Call the selected callable. | `time.time()` | Empty parentheses supply no explicit arguments. |
| `int(...)` | Convert a numeric value to an integer. | `int(2.75)` | Gives 2 by truncation toward zero. |
| Epoch | Reference instant for elapsed time. | Unix seconds | Representation needs units and an origin. |
| `=` | Evaluate the right side, then assign. | `x = (a*x+c) % m` | Old x participates before x changes. |
| `**` | Raise a base to a power. | `2**31` | Produces 2147483648. |
| `*` and `+` | Multiply, then add the increment. | `a*x + c` | Interchanging a and c changes the sequence. |
| `%` | Return the remainder. | `9 % 2` | Gives 1. With modulus zero, raises `ZeroDivisionError`. |
| `/` | True division. | `377401575 / 2**31` | Gives a float; the stored state remains an integer. |
| `-` and scaling | Subtract endpoints to find width, then stretch and shift. | `30 - (-10)` | Width 40; add −10 after multiplying. |
| Seed / state | Initial integer / current integer carried to next update. | `_x` | The first class print is already one step past the seed. |
| PRNG / LCG | A deterministic sequence generator / one using modular multiply-and-add. | `(a*x+c) % m` | Same rule and same state give the same successor. |
| Cycle / period | Repeating route / number of transitions per circuit. | 3 → 6 → 3 | Period 2. A long period can still have an obvious pattern. |
| Prime / parity | Two positive divisors / evenness or oddness. | 7 is prime; `9 % 2` is 1 | Divisors of 7 do not restrict residues to 1 and 7. |
| `while True` | Repeat until a statement exits the loop. | `cycle(...)` | Performs a step before the repeat test. |
| `if` / `else` | Execute the suite selected by a condition. | `if x == seed:` | Only the selected path runs. |
| `==` | Compare values for equality. | `x == seed` | A repeated integer closes these example cycles. |
| `break` | Exit the innermost loop immediately. | First return to seed | The function then ends with implicit `None`. |
| `or` | Evaluate the right operand only if the left is false here. | `min == None or max == None` | Either missing bound selects the unscaled return. |

### Objects, calls and names

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| `class`, `:`, indentation | Define a class and group its body. | `class Lcg:` | Class assignments execute when the class is defined. |
| `def` | Define a callable for later invocation. | `def next(...):` | Its body waits for a call. |
| `_name` | Single leading underscore signals internal use. | `Lcg._x` | Convention permits access for the controlled replay. |
| `self` / bound method | Instance supplied by lookup through that instance. | `jenny.next()` | Spelling a standalone parameter self does not bind an object. |
| Default parameter | Fallback when the caller omits that argument. | `self=None` | Enables this unusual zero-argument class call. |
| Class attribute | Value looked up on the class. | `Lcg._modulus` | Both instances can read shared constants. |
| Instance attribute | Value stored on a particular object. | `self._x` | A local write shadows the class value for that instance. |
| `None` / `NoneType` | Singleton marking absence / its type. | `peach.next` | Can be printed; has no fruit payload. |
| `is` / `is not` | Test whether two references designate the same object, or different ones. | `penny is Lcg` | Identity differs from equality of values. |
| `id(...)` | Return an identity integer for a live object. | `id(lenny)` | Matches the class ID within one run. |
| `f"...{expr}..."` | Evaluate the expression and interpolate its result. | `f"a   {lenny.next()}"` | Inner diagnostic appears before the lettered line. |
| `print(...)` | Display arguments and normally end the line. | `print(iter.object)` | `print()` alone displays a blank line. |
| `return` | End the call and pass a value to the caller. | `return minmax` | A caller may discard a return after state has changed. |
| `from ... import ...` | Bind a selected module member locally. | `from lcg import Lcg` | Makes bare `Lcg` valid in the driver. |
| Namespace | Context in which Python resolves a name. | `lcg.Lcg` | Loading a module need not bind its members as bare names. |
| `__new__` | Create the instance in ordinary construction. | `Item("banana")` | Creation precedes initialization. |
| `__init__` | Initialize the new instance. | `self.object = obj` | The caller receives the node; its stored fruit is an attribute. |
| Positional argument | Match by argument position. | `Item("banana")` | Supplies obj after Python supplies self. |
| Keyword argument | Match by parameter name. | `Item(obj="peach")` | Distinct from setting a default in a signature. |
| Required parameter | Must receive an argument. | `def __init__(self, obj):` | Omission fails; explicit None still binds. |

### Nodes, failures and tools

| Syntax or concept | Definition and purpose | Lesson example | Important consequence or distinction |
|---|---|---|---|
| Node / payload | An entry and the data it holds. | `fruit` / `fruit.object` | Print a node to inspect its representation, or its payload to see apple. |
| Head / alias | Starting reference / second name for one object. | `iter = fruit` | Both names initially reach A. |
| Rebinding | Change which object a name refers to. | `iter = iter.prev` | Moving the cursor changes no node fields. |
| Attribute assignment | Change a field on the selected owner. | `fruit.next.prev = fruit` | Writes A into B.prev. |
| Successor / predecessor | Next / previous neighboring node. | `next` / `prev` | Separate writes establish the two directions. |
| Guarded traversal | Check that a real node exists before reading it. | `while current is not None:` | Empty chains produce no payloads. |
| `ModuleNotFoundError` | Import cannot locate the requested module. | `import lcg1` | Fails before creating either generator. |
| `NameError` | An unqualified name has no binding. | `penny = Lcg` | The module can already have loaded successfully. |
| `AttributeError` | An object lacks the requested attribute. | `iter.object` | The receiver is None after the overrun. |
| `TypeError` | A call violates the required argument contract here. | `Item()` | Argument binding fails before the initializer body. |
| `PYTHONPATH` | Extra search locations supplied to the process. | `Item\src` | Windows uses a semicolon between entries. |
| Working tree / index / commit | Editable files / staged content / recorded revision. | `git status` | An editor buffer may differ from all three. |
| HEAD / first parent | Checked-out position / ancestry step. | `master~20` | Detached HEAD can name a commit directly. |
| `git restore` / `git stash` | Replace tracked working content / set changes aside. | `test/fruit.py` | Restore discarded this experiment; stash was discussed. |

## 10. Knowledge check

1. With clock reading 2.75, what integer would a diagnostic immediately after the class-body recurrence display? Which value does the first method call read?
2. Compare the period of doubling modulo 9 from seeds 3 and 1. Could an obviously predictable rule also have a period of 9?
3. At which steps does Jenny actually own an integer? Why does d match b and c, and which result would an extra Genny call after f match?
4. Only `max=20` is passed. Which bound test is skipped, and what changes inside the generator?
5. The module imported successfully, but `penny = Lcg` raises. Give two repairs. How would you test that they designate the same class?
6. After the second `iter = iter.next`, predict `iter is fruit.next.next`. Which write would let that node return to banana?
7. Explain why the overrun printed three fruits but the required-argument failure printed none. What did the additional `print(iter)` prove?
8. Git displays `Aborting` and the editor still shows your diagnostic prints. Which revision ran? What evidence would you collect before switching?

### Answer key

1. **59559187.** The initial seed 2 has already been multiplied, incremented and reduced. The first method call reads 59559187 if no intervening writer changes that state.
2. **Two and six transitions.** A counter adding one modulo 9 visits every residue in order and has period nine.
3. **From b onward.** Jenny first reads the class and writes herself. Genny does the same at c. Neither writes the class, leaving it the identical starting integer for d. After f, one more Genny call matches e.
4. **The maximum test is skipped.** The minimum defaults to None, making the left operand true. The selected owner still advances its integer once and returns a normalized fraction.
5. **Use `lcg.Lcg` or bind `Lcg` with `from lcg import Lcg`.** Once both names exist, `lcg.Lcg is Lcg` is true.
6. **True.** Both routes reach peach. `fruit.next.next.prev = fruit.next` stores the banana node in peach’s backward link.
7. **They fail at different stages.** The overrun fails on the payload lookup after the third node has displayed. The new print reveals a legal None reference. The required initializer fails at the first construction attempt, before any payload can display.
8. **The refusal leaves the checkout unchanged; it runs no Python.** Check status and HEAD, compare the saved driver with the buffer, and associate output with a new invocation. Preserve edits before a checkout that would replace them.

### Technical references

Classroom attribution throughout comes from the supplied Markdown. These official references clarify the language and tools.

- [Python tutorial · Classes, namespaces and instance variables](https://docs.python.org/3/tutorial/classes.html#class-and-instance-variables)
- [Python time · Seconds since the epoch](https://docs.python.org/3/library/time.html#time.time)
- [Python language reference · Binary arithmetic operations](https://docs.python.org/3/reference/expressions.html#binary-arithmetic-operations)
- [Python tutorial · Module search path](https://docs.python.org/3/tutorial/modules.html#the-module-search-path)
- [PEP 8 · Naming conventions](https://peps.python.org/pep-0008/#naming-conventions)
- [Git · Revision ancestry](https://git-scm.com/docs/gitrevisions) and [restoring working-tree files](https://git-scm.com/docs/git-restore)
- [Microsoft PowerShell · Environment variables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables)
