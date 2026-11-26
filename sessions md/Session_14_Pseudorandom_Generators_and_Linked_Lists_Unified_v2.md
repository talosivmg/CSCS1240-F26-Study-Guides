# Session 14 · Following state through Python objects

**CSCS1240F26 · JoesTA · Worked teaching companion**

If Jenny and Genny are different objects, why do they return the same number? If `iter` moves from apple to peach, why does `fruit` still point to apple? These are two versions of the same question: **which name did the statement read, and where did it put the result?** First the class tracks integers inside a pseudorandom number generator. Then it tracks references inside a three-node list. In both demonstrations, predict the next state *before* you read the next line of output.

| Find it in the recording | What to look for |
|---|---|
| [0:00:00](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=0s) | Transfer credit, ambiguous job titles, and questions worth asking an adviser. |
| [0:12:14](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=734s) | Read the generator from its first character: import, class body, clock seed, and first update. |
| [0:35:00](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=2100s) | What makes a sequence pseudorandom? Test the rule, the seed, and the period separately. |
| [1:04:00](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=3840s) | Class versus instance calls; identity diagnostics, range scaling, and the unprinted return. |
| [1:45:00](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=6300s) | Read the lettered run, then test what importing a module actually names. |
| [2:05:06](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=7506s) | Move from integer state to links: Git revisions, module search paths, and the fruit driver. |
| [2:26:03](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=8763s) | Walk without losing the first node; compare two references before and after a move. |
| [2:44:00](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=9840s) | Wire the return path, overrun the list, then change what a new node requires. |

**Source and coverage.** [Supplied recording](https://www.youtube.com/watch?v=QzEXMGvlFf4&t=5341s), title *CSCS1240F26 14*. The supplied link opens partway through; the companion starts at the beginning. Two saved caption exports agree on all 4,261 English auto-generated cues, from 0:00:00.080 to the closing “have a good day” at 3:04:20.800. The last cue nominally ends at 3:04:23.787, just past the recorded video probe of 3:04:22.750. Gaps between individual cues are not evidence of silence. The 1920 × 1080 stills cover every whole minute from 0:00:00 to 3:04:00 (185 images), plus 3:04:20. The 62 older three-minute stills corroborate selected states. Capture times are requested seeks, not exact decoded timestamps.

**How to read the evidence.** *Observed* means legible in a classroom frame. *Reconstruction* joins supported facts or traces their consequences. *Independently verified* means a separate local check, not the original class run. Guide-added comments belong to teaching examples; the numbered listings near the end preserve editor text without those comments. Where a window covers a line or a terminal result, the guide marks it rather than completing it from memory. Historical course announcements are not current policy.

## Choosing a program: ask what transfers, not just what it is called (0:00:00)

A student asks why a Binghamton University recruiter notice belongs in a computing course. The instructor's answer: it is an invitation to investigate, not a direction to transfer. The class Discord notice says Thursday, October 1, 11 a.m.–1 p.m., in the Commons (0:01:00). Those are historical event details, not a current invitation. In the instructor's account, computer science and cybersecurity were transfer programs; IT was a career program. The labels describe how the curriculum was planned, not what a graduate is permitted or required to do.

The distinction that matters to a student is between getting credit *at all* and getting credit *toward the intended requirement*. A receiving school might accept a course as an elective yet still require its own course of the same name. A transfer program has made an effort to align curricula; it cannot make the receiving school's decision. The practical question for an adviser, recruiter, or preferably a faculty member in the destination program is: “If I take this course here, exactly which requirement will it satisfy there?” Ask early enough to change plans.

**Key items to scan**

- **Binghamton University** — the historical campus visit prompted the discussion, not a recommendation that everyone transfer.
- **Transfer program / career program** — curriculum planning for further study versus career preparation; neither label binds the student.
- **Elective credit** — a course may count toward graduation without replacing the destination program's required course.

### Does “engineer” in a job title tell you what to study? (0:02:00–0:08:59)

An engineering science student wanted to be a data engineer. One class member suggested starting with computing; another associated “real” engineering with building physical things. The instructor challenged the exclusion: building software or designing systems for data can also be engineering. The useful question is *what the person will design and what knowledge that work requires*, not whether “engineer” appears in both titles. A student proposed creating something to help others as one possible description of engineering.

When asked to distinguish IT from computer science, a student proposed hardware versus code. The instructor instead offered a **present versus future** distinction, explicitly as his own framing. Keeping a service running, fixing today's bugs, and putting out today's fires illustrate IT. Designing something better for tomorrow illustrates the research and design emphasis he associates with computer science and engineering. A factory making today's product can be using a design that an engineer worked out earlier. This is an analogy about kinds of work, not a rule that IT workers never design or computer scientists never fix bugs.

The robotics question tests that distinction. A shell and a program sound separable until one asks how the mechanism is controlled and what movements its design permits. The class considered purely mechanical controls; the instructor contrasted them with modern machines whose movements depend on electronics, computers, and often networks. In his account, computer engineering bridges the electronics, computer science, and robotics sides rather than assigning each machine to one side of a hardware/code divide.

**Key items to scan**

- **Data engineering / software engineering** — counterexamples to equating engineering only with building physical machines.
- **Present versus future** — the instructor's contrast between keeping an existing system working and designing a later one.
- **Robotics and computer engineering** — the motion you want, the mechanism that can perform it, and its electronic control have to work together.

### Questions worth asking before choosing (0:09:00–0:12:14)

The class briefly reflected on step-by-step explanation. A learner described understanding more when given the “blueprints”: the intermediate reasoning, not just the conclusion. That preference matters in the code reading that follows, where the instructor repeatedly asks students to point to the particular character that supports an interpretation.

The transfer discussion also returned to cost. The Career Development Center's contacts were not limited to public universities; Elmira and RIT came up. The instructor reported that students had transferred to RIT with scholarship or aid packages and encouraged students not to rule a school out solely by its posted tuition. This was historical advice to investigate an individual offer, not a promise of aid.

**Key items to scan**

- **Blueprints** — explain the intermediate steps a learner would otherwise have to guess.
- **Career Development Center** — a route to universities and employers, not just members of one university system.
- **Sticker price** — distinguish posted tuition from what a particular aid offer would leave to pay.

## Read the program from the top, not from its apparent purpose (0:12:14–0:19:26)

The instructor switches from **`Item`** to **`Lcg`**. The PowerShell frame at 0:13:00 shows **`cd ..\Lcg\`** followed by **`ls`**; the project contains **`apprentice`**, **`doc`**, and **`src`**. A submitted **`.\src\lcg.py`** is followed by a new prompt with no output between them. The next entry is still being typed. At 0:14:00 the split editor shows **`view.py`** on the left and **`lcg.py`** on the right. The former uses the class; the latter defines it. The terminal's older Git counts belong to the Item directory, not to a run of either pane.

| Observed action or state | Guide-added reading note |
|---|---|
| **`cd ..\Lcg\`** | From the Item directory, go up one directory and into Lcg. |
| **`ls`** | List the project contents; listing the three directories does not run any Python. |
| **`.\src\lcg.py`** | A command appears in scrollback. Nothing here establishes what application handled the file association. |
| Split editor, 0:14:00 | The class definition and its caller can be read side by side. This screen is not printed program output. |

**Key items to scan**

- **`Item`** — previous project; its Git scrollback is not evidence about Lcg.
- **`Lcg`** — the new project directory and, inside its Python module, the case-sensitive class name.
- **`cd ..\Lcg\`** and **`ls`** — switch project and inspect the directory.
- **`apprentice`**, **`doc`**, **`src`** — three visible subdirectories; the Python file path uses the last one.
- **`.\src\lcg.py`** — submitted command, not evidence that the driver produced any numbers.
- **`lcg.py`** — class definition; **`view.py`** — code that uses it.

### What does Python actually have to do here? (0:13:30–0:19:26)

Asked about line 1, a student thought it was a call. “Be the machine,” the instructor replies: look at the first character before interpreting the English. The **`#`** makes the rest of the line a comment. The phrase “object/class call” is for the reader; it neither creates an object nor calls anything. This left-to-right exercise is a reading discipline, not a claim that Python runs every character as soon as it sees it.

**Annotated teaching version — guide-added comments; opening excerpt, right pane, 0:14:00.**

```python
# object/class call         # The whole line is a comment, not an invocation.
import time                 # Make the time module available under the name time.

class Lcg:                  # Begin the class definition; the colon introduces its body.
    _x = int(time.time())   # Store a whole-second starting value on the class.
```

The instructor asks what **`import time`** does *if it works*. Imports are not guaranteed to succeed just because an identifier follows the keyword. Here Python can load the standard **`time`** module and bind its name. The instructor likens having its code available to copying it in. Notice the difference from a literal paste: the class still asks for a member *through the module name*. The blank third line does nothing in this file. The **`class`** keyword is followed by a programmer-chosen identifier and a colon; the displayed character is **`:`**, despite the caption's “semicolon.” Indentation tells the reader which assignments and method definition belong to the class. The comment and import above are outside it.

The capital L in **`Lcg`** signals a class by naming convention, not by a Python rule that a lowercase name could not name one. The leading underscore in **`_x`** asks clients not to use this internal state directly. It does not prevent access. The instructor dislikes decorative underscores but uses one here precisely to signal intent. He mentions PEPs (Python Enhancement Proposals) in connection with style without giving a proposal number. **Supplemental:** PEP 8 recommends CapWords for class names and a leading underscore for non-public attributes; neither is an access restriction.[3]

**Key items to scan**

- **`#`** — ignore the rest of this source line as a comment; English that sounds like an action is not a call.
- **`import time`** — request the module and bind its name if successful; **`time`** is the module the class later consults.
- **`class`**, **`Lcg`**, **`:`** — keyword, chosen name, and start of an indented definition. Capitalization is a convention; the colon and body structure are syntax.
- **`_x`** — internal class attribute; the single leading underscore communicates intended use rather than enforcing privacy.
- **PEP** — Python Enhancement Proposal, mentioned in discussing style, not a numbered requirement supplied by the class.

## Two names spelled “time,” two calls, one stored integer (0:19:27–0:27:59)

In **`int(time.time())`**, the first **`time`** names the imported module; the second selects a function that belongs to that module. Calling the period a separator is not quite enough. What does it separate? The instructor recommends reading **`.`** aloud as “member of” to remember the relationship. In this case the selected member is a module function, not a method on an individual generator instance. Its adjacent parentheses request a call with no arguments; the outer parentheses pass the resulting number to **`int`**. Only then can the assignment store the result in **`_x`**. Referring to the function by name, without the call parentheses, would not invoke it.

**Annotated teaching version — guide-added comments; split into steps for explanation, not historical source.**

```python
import time                   # Bind the module name, not every member name by itself.
stamp = time.time()           # Call its time function; the result can include a fractional second.
whole = int(stamp)            # Drop the fractional part; for a sample 2.75 this gives 2.
```

The class body does this initialization as the class is defined, not each time a caller asks for a new number. Neither the class assignment nor the split teaching statements print a timestamp. At 0:22:00 the editor even shows a “References to time” popup at line 5; that is editor assistance, not output from the function.

How can a number represent a date? A student says the class began at 9 a.m. and 28 minutes have passed. There are two pieces: a reference point and a count in a chosen unit. The instructor applies the same idea to the Unix epoch: count seconds from the start of January 1, 1970 (UTC; supplemental Python time reference).[2] A CPU can count intervals; the interpretation of that number as time depends on the reference and representation.

A question about a battery prompts a distinction. Volatile memory loses its stored contents without power; one must not assume it restarts as a meaningful pattern of zeros. Nonvolatile storage retains a bit pattern across a power loss. The instructor connects that persistence to startup instructions and to storing information about time; flash is an example that does not need continuous power simply to retain data. Distinguish retaining a stored count from advancing a clock while power is off: persistence alone does not do the counting.

The instructor mentions Y2K and is tentative about the date of a later time-representation limit. The common issue he wants students to notice is that storage size and the interpretation of the bits restrict what a representation can express. Y2K's year interpretation and a count overflowing its allotted range should not be treated as the same mechanism. The code on screen neither simulates nor demonstrates either failure.

**Key items to scan**

- **`int(time.time())`** — inner call first, integer conversion second, assignment last.
- **`time`**, **`.`**, **`time.time()`** — imported module, member selection, and invocation of the selected function.
- **`int`** — converts a fractional timestamp to a whole number by truncating toward zero; it does not round to nearest.
- **`_x`** — receives the resulting integer; no printed value follows merely from assigning it.
- **Epoch** — reference instant; with the Unix example the units counted from it are seconds.
- **Volatile / nonvolatile** — whether a previously stored bit pattern survives without power; flash illustrates retention, not a running clock.
- **Representation limit** — the number of bits and their interpretation determine what can be represented.

## Why assign to the same name twice? Follow the old value through line 9 (0:28:00–0:34:59)

The next three lines give names to the multiplier, increment, and modulus. The instructor asks what two adjacent asterisks mean. They are two characters but one exponentiation operator. The powers of two begin 1, 2, 4, 8, 16, 32; the actual expression **`2**31`** gives 2,147,483,648, not 62. The precise total below was checked separately; the class describes its size rather than printing it.

**Annotated teaching version — guide-added comments; class-body excerpt at 0:14:00, discussed 0:28:00 onward.**

```python
    _x = int(time.time())                         # Start with the integer part of the time reading.
    _multiplier = 1103515245                      # Factor to multiply the previous state by.
    _increment = 12345                            # Add this amount after the multiplication.
    _modulus = 2**31                              # Wrap at 2147483648.
    _x  = (_multiplier * _x + _increment) % _modulus  # Store the remainder as the new state.
```

A student starts by saying that **`_x`** “equals” the calculation. The instructor presses for the specific character(s) that show why the first assignment was necessary. It is the **`_x`** on the *right* of **`=`**. Python needs its *previous* value for the multiplication. It completes the right-hand expression before rebinding the name on the left; this is assignment, not a mathematical assertion that the two versions of the state are equal. The calculation is the state update of a linear congruential generator: given the same previous integer and constants it produces the same next integer.

Why the **`%`**? The class switches to clocks. At 14:00, one complete group of 12 hours and two extra hours have elapsed from zero. For the afternoon example, **`14 % 12`** gives 2, just what is needed to display the hour as two o'clock. A classmate notes that 24-hour notation goes from 23:59 to 00:00. The analogy is about wrapping; a remainder of zero and the conventional display “12” on a twelve-hour dial are not the same label. The **`/`** operator answers a different question: how many groups including the fractional part, rather than what whole-number remainder is left.

**Independently verified worked trace — guide-created clock reading 2.75, not a classroom timestamp or run.**

| Step | Arithmetic | Why it matters |
|---|---|---|
| Convert the reading | **`int(2.75)`** → 2 | The old state is 2, not 2.75 or 3. |
| Multiply | **`1103515245 * 2`** → 2,207,030,490 | Read old state before overwriting it. |
| Add | **`2207030490 + 12345`** → 2,207,042,835 | Finish the quantity in the parentheses. |
| Wrap | **`2207042835 % 2147483648`** → 59,559,187 | One whole modulus fits; retain what is left. |
| Store | **`_x`** now holds 59,559,187 | No output is printed by this assignment. The new state is available to the next update. |

The useful prediction does not require knowing the actual time: with this positive modulus, the remainder is at least zero and strictly less than 2,147,483,648. That property is what makes the later division by the modulus meaningful.

**Key items to scan**

- **`_multiplier`** — 1103515245; multiplies the *previous* state.
- **`_increment`** — 12345; added before taking the remainder.
- **`_modulus`** — **`2**31`**, or 2,147,483,648; controls where the integer wraps.
- **`_x`**, **`=`** — read the old state on the right, finish the expression, then replace the state on the left.
- **`%`** — remainder after division; **`14 % 12`** gives 2. **`/`** gives a quotient instead.
- **`int(2.75)`**, **`1103515245 * 2`**, **`2207030490 + 12345`**, **`2207042835 % 2147483648`** — fixed-input steps of the separate check, ending at 59,559,187.

## From “I didn't see it coming” to a reproducible sequence (0:35:00–0:49:59)

### What would count as random? (0:35:00–0:44:59)

The generator in the editor has a rule for finding its next number. How can that produce anything random? The instructor opens Wikipedia's *Linear congruential generator* ([address visible at 0:36:00](https://en.wikipedia.org/wiki/Linear_congruential_generator)) and asks the class to say what “random” means before doing the arithmetic. A student suggests “unpredictable.” The useful follow-up is: unpredictable **to whom, given what information**?

One student had planned around two long labs and brought a charger, but forgot a badge. Surprise is real; it describes what the person knew beforehand. Lack of control is a different claim: you can't pause aging or move the class meeting time at will, yet the passage of time and a scheduled class are quite predictable. The class tests several other explanations: an unexpected move, a discovery by accident, a miracle, and the thought experiment of someone who knew *all* the starting conditions. These are competing accounts, not a class verdict on whether the universe is deterministic. A student also asks what the **butterfly effect** means. The instructor's image of a butterfly here and a much larger disturbance far away illustrates how a small cause could have a large later effect. Difficulty following that chain isn't proof that there was no chain.

**Key items to scan**

- **Unpredictability** — to whom, given what information? Forgetting a badge surprised the student, but doesn't establish a law of randomness.
- **Control** — the class can't reschedule the meeting at will; knowing when it starts is still possible.
- **Butterfly effect** — a small difference at the start may matter much later; sensitivity to a cause and absence of a cause are different claims.

### An algorithm can look random without ceasing to be an algorithm (0:45:00–0:49:59)

This generator is **pseudorandom**: with the same start and the same rule, it gives the same sequence, in order. Someone who doesn't know the start may not anticipate any particular next value. The instructor points out that randomness is a subject of research, mentions NIST (National Institute of Standards and Technology) as a place to explore, and connects the need for unpredictability to cryptography. NIST was a spoken suggestion, not the website demonstrated.

A student asks whether randomness *is* probability. A chance assigned to an event and the mechanism that produces an outcome aren't the same. Probability and statistics give us ways to describe or assess outcomes; a sequence that looks evenly distributed can nevertheless be generated by a fixed rule. The instructor strongly recommends studying statistics. Another student asks whether data science means “data programming” for creating sites or games. The instructor describes **data science** as an umbrella for data engineering, analytics and machine learning; rather than promise a course pathway based on an ambiguous term, the instructor offers a separate conversation and invites follow-up on Discord.

**Key items to scan**

- **Pseudorandom** — a fixed initial state and rule reproduce the same numbers; “I can't guess it” doesn't make the rule nondeterministic.
- **Probability** — describes the chance of an event, not the identity of the generating mechanism.
- **Statistics** — helps evaluate observed patterns; the instructor's course recommendation, not an enrollment requirement stated here.
- **NIST** — spoken resource on randomness; the visible page is Wikipedia, not NIST.
- **Data science** — the instructor names data engineering, analytics and machine learning; the student's “data programming” label wasn't resolved.

### Read the rule from right to left: use today's state to obtain tomorrow's

The article scrolls to the formula at 0:46:00; it is selected at 0:47:00–0:49:00, before the instructor returns to it in speech around 0:49:00. Start with one number. Multiply it by the fixed **multiplier**, add the **increment**, then keep only the remainder on division by the **modulus**. This last number is not discarded: it becomes the input to the next step. That dependence on the preceding value is a **recurrence**.

| Notation on the page | What it does |
|---|---|
| X₀ | Seed: the starting state; the page specifies 0 ≤ X₀ < m. |
| Xₙ → Xₙ₊₁ | Current integer state → next integer state. The subscript is a step number, not a multiplier. |
| Xₙ₊₁ = (aXₙ + c) mod m | Multiply first, then add, then take the remainder. |
| m > 0; 0 < a < m; 0 ≤ c < m | The integer parameter ranges displayed for modulus, multiplier and increment. |

The opening diagram supplies three unusually useful test cases (visible at 0:36:00). The following are its states, independently checked by iterating the pictured rules; they are **not classroom terminal output**. Count steps to the first return to the starting state, not the number of printed entries including both copies of the start.

| Start and rule | States in the diagram, including the first repeated state | Period |
|---|---|---:|
| Start 1; double, mod 9 | 1 → 2 → 4 → 8 → 7 → 5 → 1 | 6 |
| Start 3; same rule | 3 → 6 → 3 | 2 |
| Start 0; multiply by 4, add 1, mod 9 | 0 → 1 → 5 → 3 → 4 → 8 → 6 → 7 → 2 → 0 | 9 |

Why did 8 turn into 7 in the first row? Doubling gives 16, and removing one group of 9 leaves 7. Changing only the seed can put you in a *different cycle*, not just a different place in one cycle. Changing the parameters in the third row gives a cycle that visits every residue before repeating. The page calls the zero-increment case a *multiplicative congruential generator* (also Lehmer RNG), the nonzero case a *mixed congruential generator*. Its terminology note: adding a nonzero constant technically makes the transformation affine, although “linear congruential” is the established name.

**Key items to scan**

- **Recurrence** — a new state is computed from the last state; it is not sampled independently of it.
- **Multiplier** — 2 doubles the previous state in the first two rows; 4 multiplies it in the third.
- **Increment** — 0 adds nothing in the first two rows; 1 is added before taking the remainder in the third.
- **Modulus** — 9 limits all states in the pictured examples to 0 through 8.
- **Seed** — changing 1 to 3 changes the period from 6 to 2 with the same doubling rule.
- **Period** — number of steps until the state repeats in a cycle, not a measure of how surprising the order looks.
- **History** — the instructor describes the algorithm as a decades-old classic. The visible article dates the Lehmer generator to 1951 and the linear congruential generator of W. E. Thomson and A. Rotenberg to 1958 (0:47:00).

## What the remainder can tell you (0:50:00–0:57:59)

### Zero, one, two: test the operation, not just the number

The instructor asks what happens as the modulus varies. It's easy to hear “divide” and give the **quotient**. The generator needs the **remainder**: how much is left *after* taking out complete groups? A student's “five divided by one is five” is correct about the quotient but wrong about what modulo returns. For integer inputs the distinction resolves all three cases (0:50:00–0:54:00).

Annotated teaching version — guide-added examples; results independently verified in Python. The last expression deliberately stops with an error.

```python
print(5 % 1)    # Five complete groups of one; none left: prints 0.
print(8 % 2)    # Four complete pairs; prints 0 (even).
print(9 % 2)    # Four complete pairs and one left; prints 1 (odd).
print(5 % 0)    # No defined remainder; raises before print can display a value.
```

Result: the first three calls display `0`, `0`, `1`, in order. The fourth raises `ZeroDivisionError`; it does **not** return zero. With modulus 1 the recurrence's remainder is always zero, regardless of the multiplication and addition. With modulus 2 the only possibilities are 0 and 1. This gives a useful parity test even when the original integer is large. For any positive integer modulus m the possible integer remainders run from 0 through m − 1.

**Key items to scan**

- **Quotient** — five groups of one fit in five; the quotient is five.
- **Remainder** — after those five groups, nothing is left; **`5 % 1`** is zero.
- **`% 2`** — returns 0 for even, 1 for odd integer inputs; not the fractional result of division.
- **`% 0`** — no remainder exists; Python raises **`ZeroDivisionError`**.
- **Practice** — the instructor treats remainders as a fundamental skill worth recalling in an interview. Explaining 9 modulo 2 in terms of complete pairs is a better check than memorizing an answer.

### Does a prime divisor allow fewer remainders?

The selected heading at 0:55:00 reads **“m prime, c = 0”**. A student asks why the primality of m matters. First settle what a **prime** is: a positive integer greater than one with exactly two distinct positive divisors, one and itself. The class settles on seven as an example. Six factors as 2 × 3 as well as 1 × 6; twelve admits 3 × 4 and 2 × 6. A student included one among the primes; the correction matters because one has only **one** positive divisor, not two.

Now distinguish “what divides 7 exactly?” from “what might be left when I divide *another number* by 7?” For instance, 9 divided by 7 leaves 2. The independently checked dividends 7, 8, 9, 10, 11, 12, 13 produce the remainders 0, 1, 2, 3, 4, 5, 6. Primality does not shrink that set to one or two special answers. It can matter to the behavior under *repeated multiplication*. The article's visible special case has no increment, a nonzero starting state and a suitable multiplier for a period of m − 1. It does **not** say any multiplier with a prime modulus guarantees that period. The instructor poses the broader parameter-choice problem, then explicitly passes over deriving a good choice.

The screen also offers a useful counterexample: set multiplier and increment both to 1 and the state is just a counter wrapping at the modulus. With modulus 9 and start 0, the sequence is 0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 0 (separately checked). A full period and equal counts here are perfectly compatible with an obvious pattern. The page cites RANDU as a historical warning about poor parameters; this was visible context, not a classroom reproduction of it.

**Key items to scan**

- **Prime** — 7 has two distinct positive divisors; 1 does not. The factors of 7 are not the possible remainders modulo 7.
- **“m prime, c = 0”** — names one parameter family in the article, not a requirement for all generators.
- **Counterexample** — adding 1 modulo 9 visits every state in predictable order; a long period alone doesn't establish randomness.

## Where the constants come from, and why the clock is only a starting point (0:57:00–1:04:59)

### Reading a parameter table without mistaking popularity for quality

At 0:57:00 the heading changes to *Parameters in common use*. Its introduction explicitly says the table illustrates popularity, not necessarily good choices. At 0:59:00 the instructor identifies the row labeled **glibc (used by GCC)**; the full multiplier in that row is 1103515245, even though the blue selection doesn't cover its leading digit. The instructor corrects “increment” to “multiplier” when pointing to that number. GCC here is the **GNU Compiler Collection**, not just the C compiler; the instructor mentions GNU's broadly used utilities and its C++ compiler as well. The row supplies the constants, not the Python code: the table labels the indicated output “bits 30..0,” whereas this class returns a fraction or a scaled fraction.

Just above the table is another screen-only example of structure hiding in two lists: the article's X values start 233, 232, 75, 2, 61, 108; the corresponding Y values start 0, 1, 158, 231, 172, 125. Each corresponding pair adds to 233 modulo 256. Inspecting the two lists separately would miss that relationship. These six paired values are visible at 0:57:00 and independently checked, not numbers produced by the Python driver.

| Article row at 0:59:00 | Modulus | Multiplier | Increment | What the comparison shows |
|---|---:|---:|---:|---|
| glibc (used by GCC) | 2³¹ | 1103515245 | 12345 | Matches the three constants in the Python editor. |
| Borland C/C++ | 2³¹ | 22695477 | 1 | Even with the same modulus, the update rule can differ. |

**Key items to scan**

- **glibc (used by GCC)** — the reference row the instructor used; appearance in the table is not a security or quality endorsement.
- **X and Y modulo 256** — each of six displayed pairwise sums has the same residue, 233.
- **GNU Compiler Collection** — expansion of GCC given in class; a collection, not a single language compiler.
- **1103515245** — multiplier; **12345** — increment. Swapping them changes the sequence.
- **2³¹** — positive modulus, written **`2**31`** in Python; the table's bit-output column is not a Python `return` statement.

### The seed is replaceable; the recurrence is not re-seeded on every call

At 1:00:00 the editor is back on screen, with the time expression selected. **`time.time()`** supplies a clock reading in seconds since the epoch; **`int(time.time())`** discards the fractional part for these positive readings. The clock is quite predictable. What's often unknown is the exact second when the program started. Two program starts in the same second can therefore receive the same seed. A changing seed is convenient for variety; a fixed seed is useful when checking whether a change to the program altered its behavior.

Annotated teaching version — guide-added comments; class-body excerpt from `lcg.py` (0:35:00; 1:00:00). Assignments do not print anything.

```python
import time                                    # Make the time-reading function available.
class Lcg:                                     # Define one class; its body runs now.
    _x = int(time.time())                      # Start from the current whole second.
    _multiplier = 1103515245                   # Multiply the current state by this integer.
    _increment = 12345                         # Add this before taking the remainder.
    _modulus = 2**31                           # Keep a remainder from 0 through 2147483647.
    _x = (_multiplier * _x + _increment) % _modulus  # Replace the seed with the first update.
```

The second assignment to **`_x`** is easy to miss. It advances the state *while the class is defined*, before the first call to its method. It does not read a new time. Under the instructor's proposed experiment, substitute a constant for the clock and run again. In a separately executed fixture using seed 1, the state after the class body was `1103527590`; the next class update gave `377401575`. The captured class does not show the historical clock value, so these are **not** the instructor's sequence.

When asked about prediction without being told the seed, a student compares reproducible game worlds in Minecraft. The instructor's point is that observing outcomes can reveal structure in a deterministic sequence; a hidden start is no guarantee of security. The class also discusses secure hashes, contrasting fast forward computation with the difficulty of reversing it. Keep the two ideas separate: hashing is deterministic, and extra deterministic calculations don't manufacture new randomness. No cryptographic implementation or security measurement is demonstrated here.

**Key items to scan**

- **`time.time()`** — clock source, not a random-number source.
- **`int(time.time())`** — whole-second seed; two positive readings such as 1234.2 and 1234.9 both become 1234 in a separate check.
- **`_x`** — initially holds the seed, then the first modular update, then later current states. The class doesn't re-read the clock on every call.
- **Fixed seed** — the instructor proposes replacing the clock with a constant to repeat the same sequence for comparison.
- **Speed versus quality** — the instructor calls this classic calculation simple and fast, not the best generator for every use; the parameter table demonstrates why the choices matter.
- **Hashing** — discussed by analogy; deterministic transformations aren't by themselves a source of fresh unpredictability.

## One method, two places to keep the next state (1:04:00–1:09:59)

### What is actually supplied for the first parameter?

The instructor turns from the arithmetic to the signature **`def next(self=None, min=None, max=None):`**. The first parameter is conventionally called **`self`**. It is a name, not a reserved word or a numeric address. For a call on an instance, Python supplies that object as the first argument. **`id(self)`** *reports* its identity; it isn't what is stored in the parameter. Calling this ordinary method through the class without supplying an object leaves the instructor's chosen default, **`None`**, in place. That does not make `None` a general-purpose reference to a class: the class branch explicitly names **`Lcg`**. The class itself is an object, too.

The instructor invokes **`__new__`** and **`__init__`** to recall object creation. More precisely, during ordinary construction Python uses the first to create an instance, then invokes the second to initialize it if appropriate. The parameter refers to the object, not an integer that the program must turn back into an object. At 1:09:00 a student asks what would happen to a function with the same first parameter defined *outside* the class. The question is still being clarified; no standalone definition or result appears by 1:09:59. The distinction to carry forward: writing the name `self` by itself does not supply any argument.

**Key items to scan**

- **`def next(self=None, min=None, max=None):`** — one method with three defaults; a call through an instance still receives that instance automatically.
- **`self`** — conventional first parameter, holding the object in an instance call; spelling is convention, not special syntax.
- **`id(self)`** — returns an identity integer for the current object; its actual digits aren't portable output.
- **`None`** — the explicit default used here to choose the class path, not the class itself.
- **`Lcg`** — the class object explicitly named inside that path.
- **`__new__`** — creates an instance; **`__init__`** initializes the newly created instance in ordinary construction.

### Follow a call into the correct branch

The method is fully visible even though only its signature and first decision are being discussed here. Read the two routes as a pair. They use the same arithmetic and return policy, but they print a different identity and store the updated integer in different places. These excerpts continue inside the class shown above; line wrapping and comments are for teaching.

Annotated teaching version — guide-added comments, with the same two branches as the 1:00:00 editor.

```python
    def next(self=None, min=None, max=None):           # No explicit bounds means return a fraction.
        if self == None:                               # Class call without an instance: use Lcg.
            print(f"class id: {id(Lcg)}")              # Print the identity of the class itself.
            Lcg._x = (Lcg._multiplier * Lcg._x +
                      Lcg._increment) % Lcg._modulus   # Replace the class state with its next remainder.
            if min == None or max == None:             # Either bound missing? Do not scale.
                return Lcg._x / Lcg._modulus           # Give the caller a fraction; don't print it here.
            else:
                span = max - min                       # Both bounds present: compute the interval width.
                minmax = Lcg._x / Lcg._modulus * span + min  # Stretch, then shift to the lower bound.
                return minmax                          # Return that scaled number to the caller.
        else:                                          # A bound instance call supplies the actual object.
            print(f"object id: {id(self)}")            # Print this instance's identity.
            self._x = (self._multiplier * self._x +
                       self._increment) % self._modulus  # Read old state; save the new state on THIS object.
            if min == None or max == None:             # One omitted bound also suppresses scaling here.
                return self._x / self._modulus         # Fraction from this instance's updated state.
            else:
                span = max - min                       # Width in the caller's requested units.
                minmax = self._x / self._modulus * span + min  # Scale this instance's fraction.
                return minmax                          # Return the calculation, not the identity.
```

With positive modulus, integer remainders run from zero to one less than the modulus, so the fraction is at least zero and less than one for these constants. The code scales only when *both* bounds are supplied. For a separate reset to class state `1103527590` before **each** call, the unbounded result was `0.1757413032464683`; with bounds 10 and 20 the result was `11.757413032464683`. With only 10 as the minimum or only 20 as the maximum, the result was again `0.1757413032464683`. These are independently tested return values, not classroom prints. Call with named bounds when demonstrating this unusual class-call signature, so neither bound is mistaken for the first parameter.

**Key items to scan**

- **`Lcg._x`** — updated on the class; a new instance can read it until it has a state of its own.
- **`self._x`** — read on the right, assigned on the left; assignment gives that instance its own state without changing the class state.
- **`class id:`** / **`object id:`** — messages printed inside the method, reporting the class or instance used. Neither is its random return value.
- **`min == None or max == None`** — one missing bound is enough to return the unscaled fraction.
- **`span = max - min`** — with 10 and 20 the width is 10; multiply the fraction by that width, then add the lower bound.

## One method, two places to keep the next number · 1:10:00–1:19:59

### Which object, if any, did the caller supply? · 1:10:00

A student asks what would happen if the method were *outside* the class. The instructor postpones the question to run the example rather than pretending the question is already clear. The code on screen shows the version *inside* **`Lcg`**. It lets the caller ask the class for a number or ask a particular generator for one. The plain-language question behind **`if self == None:`** is “Did this call arrive without a particular generator?” This is a choice made by this code, not a test required in every Python method.

The instructor talks about **`__new__`** and **`__init__`** to explain why a constructed object has an identity even without a custom initializer. Keep two events separate: calling a class creates and initializes an instance; *later*, looking up this ordinary method through that instance supplies it as the first argument when called. No constructor reruns on each call to **`next()`**. Looking up the function through the class does not supply an instance; in the zero-argument call used here, the default **`None`** survives. Python does not decide which path to take by searching for evidence of an earlier constructor call. A standalone function would not get an instance automatically just because its first parameter was named **`self`**; no such outside-the-class version is run here. The Python tutorial’s discussion of method objects gives the same distinction.[1]

Why the two prints? A caller should usually be able to request a number without the library writing an unsolicited message to standard output. Here the extra message is deliberate debugging evidence: it identifies the path *before* the result comes back. A method whose job actually is to display something is the instructor's exception to that design preference.

**Key items to scan**

- **`lcg.py`**, **`Lcg`** — the module file and its generator class, respectively.
- **`if self == None:`**, **`None`** — in this example the default survives a zero-argument class call and selects class state.
- **`self`** — the particular generator supplied for an instance call; the spelling is not what supplies it.
- **`Lcg._x`** — writing this name advances the integer on the class. **`self._x`** — writing this name advances the integer for the calling instance.
- **`__new__`**, **`__init__`** — creation and initialization, not steps repeated when an existing generator asks for its next number.
- **`next()`** — the course's generator method; a diagnostic print inside it and its numeric return have different jobs.

### Why divide, then multiply, then add? · 1:12:00–1:19:00

The recurrence has already produced an integer. A student suggests that the *next* operation is what gives the remainder. “No, this is division now,” the instructor answers. **`%`** has already wrapped the integer into the interval from zero up to, but not including, the positive modulus. **`/`** now turns that integer into a fraction of the modulus. Zero can come out; one cannot. Scaling the returned number does **not** replace the stored integer with a fraction.

Annotated teaching version — guide-added comments; class branch shown at 1:13:00–1:18:00. The instance branch has the same operations with **`self`** instead of **`Lcg`**.

```python
if min == None or max == None:            # Either endpoint missing? Use the unscaled result.
    return Lcg._x / Lcg._modulus          # Send a fraction in [0, 1) back to the caller.
else:                                    # Both endpoints are available.
    span = max - min                     # From -10 to 30, the width is 40, not 20.
    minmax = Lcg._x / Lcg._modulus * span + min  # Stretch first, then shift the origin.
    return minmax                        # Return a value on the requested scale.
```

There is an important correction to the oral shorthand “if they are both none.” Both missing is *one* way to enter the first branch. The connective is **`or`**, so even *one* missing endpoint selects that branch. The alternative needs both endpoints. A student asks why not put the calculation directly in the final expression. You can substitute **`(max - min)`** for **`span`**, not the sum of the endpoints. Naming it makes “multiply by the width” easier to see. If the width were reused it could be calculated once; readability, not a claimed speedup, is the reason here. The instructor even suggests that the normalized fraction could have had a name.

The earlier temperature challenge supplies a reason to keep the final addition. The distance from −10 to 30 degrees is 40. Multiplying a fraction by 40 starts the new scale at zero, not −10. Add the minimum to shift it. The instructor notes the endpoints need not be positive or even integers. For this ordered pair the resulting interval is **`[-10, 30)`**.

| Step | Temperature example (guide-chosen fraction; arithmetic independently checked) | What it changes |
|---|---|---|
| Width | 30 − (−10) = 40 | Size of interval only |
| Stretch | 0.25 × 40 = 10 | Distance above its lower end |
| Shift | 10 + (−10) = 0 | Temperature on the requested scale |

**Key items to scan**

- **`%`** — bounds the stored integer; **`/`** — converts it to a fraction for the caller.
- **`min == None or max == None`**, **`or`** — either missing endpoint prevents custom scaling, even if the other was provided.
- **`(max - min)`**, **`span`** — equivalent width expressions in this calculation. The name makes the scale factor readable.
- **`minmax`** — the transformed return value, not the integer used for the next recurrence.
- **`self`**, **`Lcg`** — the two branches read their own updated integer; the conversion is otherwise the same.
- **`[-10, 30)`** — negative lower limit included; positive upper limit excluded for this example.

## Run the driver: module, class, and two instances · 1:19:00–1:33:00

### A successful import, then a deliberate failure · 1:19:00–1:24:00

The instructor rearranges the editor into two columns so the caller and the implementation can be seen together. The program actually launched is **`view.py`**. In PowerShell the command and first two result lines are visible at 1:21:00 (excerpt from the *first* successful run):

```text
PS C:\CSCS1240F26\Lcg> py '.\src\view.py'
id jenny: 1206589228432
id genny: 1206589179536
```

The dot in the filename **`view.py`** separates its extension; it is not Python attribute access. At the top of that file **`import lcg`** asks for the module without its **`.py`** suffix. Why did the import work when this library hadn't been packaged and installed? The class account and the source-directory listing put the two files together. For this script launch it is the *script directory*, not necessarily the shell's current directory, that makes its sibling available. The prompt is one level above **`src`**. The instructor recalls virtual environments and packages installed with **`pip`** as another context in which Python finds modules; no new package installation is demonstrated here.

To test the explanation, the instructor deliberately tries a name that cannot be found. The failed run identifies the offending source line and stops there:

```text
import lcg1
ModuleNotFoundError: No module named 'lcg1'
```

The lesson is not “a module must begin with a lowercase letter.” Matching the actual importable name matters. Lowercase filenames and avoiding spaces are the instructor's practical naming advice; the class begins with an uppercase letter in **`lcg.Lcg`**. At 1:23:00 the terminal failure is already scrollback alongside an editor showing the correct import again. Do not treat it as an error caused by that repaired text.

**Key items to scan**

- **`view.py`** — the driver launched in PowerShell; **`src`** — directory containing the driver and its sibling module.
- **`import lcg`**, **`.py`** — import by the module name, not by typing the filename extension.
- **`import lcg1`**, **`ModuleNotFoundError`** — a deliberate wrong-name test stops before either generator can be constructed.
- **`lcg.Lcg`** — the attribute named `Lcg` on the imported module named `lcg`; case distinguishes the two names here.
- **`pip`** — mentioned as an installation route, not used for this sibling import.

### What do the missing parentheses change? · 1:23:00–1:29:00

The driver makes two apparently similar names and then does something quite different with a third. Use the spellings on screen: the first two are **`jenny`** and **`genny`**, not two variables spelled “Jenny.” The module/member distinction also explains the earlier **`time.time()`**: find the member called **`time`** in the imported module, then call it. It need not follow that the module lives in a literal `time.py` file.

Annotated teaching version — guide-added comments; setup in the driver at 1:25:00.

```python
import lcg           # Make the sibling module available under this name.
jenny = lcg.Lcg()    # Call the class: create a new generator; bind jenny to it.
genny = lcg.Lcg()    # Call again: create another generator, not another name for jenny.
lenny = lcg.Lcg      # No call. Bind lenny to the class object already in the module.
```

The instructor uses memory size and the organization of stored information as a way to think about *type*. Two objects can have the same type without occupying the same place. That picture explains why an identity comparison is useful; it is not a measurement of byte offsets or proof that Python allocates copies of all the class attributes into each instance. In fact the location of the first write to each instance matters in this example. A brief remote-audio check interrupts the discussion at 1:27:00–1:28:00; the identity comparison resumes afterward.

**Key items to scan**

- **`jenny`**, **`genny`** — different objects made by two separate calls to **`lcg.Lcg()`**.
- **`lenny = lcg.Lcg`** — a new name for the class, not a third generator instance.
- **`time.time()`**, **`time`** — module name on the left, member looked up and called on the right.

### When does a number become information? · 1:29:00–1:33:00

The corrected run yields a fresh set of identity numbers. The instructor asks what a single one means. In isolation it is just data; beside the number for another live object it can answer a question. The class notices that the two instances differ. The useful equality comes later: **`id(lenny)`** is exactly **`id(lcg.Lcg)`**. A student's answer “the file” for **`lcg`** is refined: the file provides the code; the imported **module object** is what has this identity while the program runs. The class object is a member of it, not the module itself.

Annotated teaching version — guide-added comments; output beside each line from the same corrected run, visible at 1:30:00–1:33:00. These numbers are not constants to expect on another run.

```python
print(f"id jenny: {id(jenny)}")        # id jenny: 3214240679312
print(f"id genny: {id(genny)}")        # id genny: 3214240630416
print(f"id lenny: {id(lenny)}")        # id lenny: 3214236774416
print(f"id lcg: {id(lcg)}")            # id lcg: 3214240290496
print(f"id lcg.Lcg: {id(lcg.Lcg)}")    # id lcg.Lcg: 3214236774416
```

**Key items to scan**

- **`id(lenny)`**, **`id(lcg.Lcg)`** — equal in the same run because these names refer to the very same class.
- **`lcg`**, **module object** — a distinct runtime object, not the generator class, either instance, or the pathname string.
- **`id(jenny)`**, **`id(genny)`** — different for two simultaneously live instances even when their first returned numbers match.

## Seven calls, six printed fractions · 1:33:00–1:44:59

### Match each value to a caller, not just to “before” · 1:33:00–1:40:59

Start with **`lenny.next()`**: no explicit argument and no bound instance. The program prints the class identity from inside the method and returns a fraction. The driver's outer print displays that fraction. Next, **`jenny.next()`** also has empty parentheses, yet the diagnostic says `object id:` and matches Jenny's earlier ID. Empty parentheses mean *no explicit arguments*, not *no receiver*. **`genny.next()`** reports still another ID but gives the *same number* as Jenny. How can the objects be different if the numbers match?

Annotated teaching version — guide-added comments. These are the unlettered driver calls and the values from the corrected run, not the earlier 1:21:00 run.

```python
print(f"   {lenny.next()}")  # Line 13: class ID, then 0.39060565223917365.
print(f"   {jenny.next()}")  # Line 14: Jenny's ID, then 0.029102258384227753.
print(f"   {genny.next()}")  # Line 15: Genny's ID, then 0.029102258384227753.

jenny.next()                 # Line 17: advance Jenny; her ID prints, but discard the return.
print(f"   {lenny.next()}")  # Line 18: class ID, then 0.029102258384227753.
print(f"   {jenny.next()}")  # Line 19: Jenny's ID, then 0.5764116561040282.
print(f"   {genny.next()}")  # Line 20: Genny's ID, then 0.7909301412291825.
```

The integer on the class is initially available to both instances by attribute lookup. Neither constructor call in this version sets an instance's own integer. At the first instance call Python reads the class value, calculates a successor, then writes a new attribute on *that instance*. It does not update the class. Genny therefore reads the same class integer Jenny started from. After that first write each has its own saved integer; equal starting values do not make them the same object. Jenny's extra call advances just her state. A bare call can change state and print a diagnostic even when nobody prints or saves its return.

Trace of the corrected run (terminal at 1:38:00–1:41:00, driver line numbers at 1:42:00). The printed fractions are observed; the integer-state progression is reconstructed from the shown recurrence and independently arithmetic-checked. The normalized display is a view of the integer, **not** the value fed back into the multiplication.

| Driver line | Reads from → writes to | Integer after update | Fraction the outer print displays |
|---|---|---:|---|
| 13 | class → class | 838819251 | 0.39060565223917365 |
| 14 | class → Jenny's own attribute | 62496624 | 0.029102258384227753 |
| 15 | class → Genny's own attribute | 62496624 | 0.029102258384227753 |
| 17 | Jenny → Jenny | 1698509545 | None displayed; only her diagnostic prints |
| 18 | class → class | 62496624 | 0.029102258384227753 |
| 19 | Jenny → Jenny | 1237834606 | 0.5764116561040282 |
| 20 | Genny → Genny | 1698509545 | 0.7909301412291825 |

Notice the second class call: it finally makes the same step both instances had made earlier. It does not copy a value *back* from either instance. Line 20 reaches the step Jenny took at line 17; that fraction looks new in the terminal because line 17 discarded it.

**Key items to scan**

- **`lenny.next()`** — advances class state; its second fraction matches the first fraction of both instances.
- **`jenny.next()`** — line 17 has no outer print, but advances Jenny one extra step; line 19 exposes the difference.
- **`genny.next()`** — line 20 starts from Genny's own saved integer, not from Jenny's most recent one.
- **`self._x`** — first lookup can find the class value; assignment writes the new value on the individual receiver. Subsequent reads find the instance's own value.

### Give competing explanations testable names · 1:41:00–1:44:59

A student connects repeated calls to compound functions: an earlier result becomes a later input. The instructor asks which “Jenny,” which “earlier,” and *which line*. This isn't pedantry. Driver line 4 binds **`genny`** to a new generator; line 15 calls it and *prints* a returned float; line 20 calls it again. It is the saved integer inside the generator that carries forward. The variable bound on line 4 is not reassigned on line 15. Nor does the text printed at line 15 become a new argument at line 20.

A plain editor could run this exercise, but an editor with line numbers lets the class point to the same event. A student also points out that the terminal decimals alone are hard to map back to callers. The instructor agrees, proposes lettered labels A through F for the six displayed results, reports saving, then decides to start the explanation again from the beginning—the “butterfly” reference. At the 1:44:00 still the cursor is in the *unlettered* first print. The completed edited text or output is not established by that still.

**Key items to scan**

- **`genny`** — names the same generator at lines 4, 15 and 20; construction, result display and later advancement are three different events.
- **Line numbers** — “Genny at line 15” is a reproducible reference; “Jenny before” is not.
- **A–F** — proposed output labels for six displayed fractions; the seventh call deliberately has no corresponding displayed fraction.

## 1:45:00–2:00:10 | Three names, two instances, one class-level starting state

### 1:45:07 | What does Jenny actually hold?

“Jenny with a G or Jenny with a J?” is a necessary question here. In **`view.py`**, **`jenny = lcg.Lcg()`** makes one instance, and **`genny = lcg.Lcg()`** makes another. **`lenny = lcg.Lcg`** does not call the class at all: the name refers to the class itself. The question is not which of their printed IDs to pick, or whether the answer is line number 5 or 9. It is: *when the driver first reads an instance's* **`_x`**, *what number does it find, and where did that number come from?* Assignment gives a name a referent; it is not an assertion that all three names refer to one thing.

To settle the question, the instructor adds a print inside the class, outside the method. Notice its position **after** the second assignment to **`_x`**. Here **`int(time.time())`** obtains the system's current time as whole seconds (drops the fractional part for this positive timestamp). That first number seeds the calculation; the number actually printed has **already gone through one generator step**. It is neither the clock reading itself nor a memory address.

Annotated teaching version — guide-added comments (class-body excerpt, new line visible at 1:49:00):

```python
import time                                  # Make the system-clock function available.
class Lcg:                                   # Execute this body to define the class.
    _x = int(time.time())                    # Start with the current time in whole seconds.
    _multiplier = 1103515245                 # Multiply the previous integer by this.
    _increment = 12345                       # Add this before taking the remainder.
    _modulus = 2**31                         # Use this as the divisor for the remainder.
    _x = (_multiplier * _x + _increment) % _modulus  # Replace the clock seed with its successor.
    print(f"_x: {_x}")                       # Display that class value now, not per instance.
```

**Observed output at 1:49:00**, before any driver IDs: `_x: 1007915657`. Why only once, when the driver makes two objects? During this run Python imports the module and executes the class body to build the class. The assignments and the print happen immediately; the method definition makes a callable for later, but **its body does not run at definition time**. Neither construction re-executes the class body. Both instances initially find the same class attribute; they are nevertheless different objects. The instructor notes that object creation happens behind the scenes; the class as shown has no initializer giving each instance its own starting value. When a student asks if the instances' values could ever differ, the answer is yes—the calls below show how. A later run can also begin with another number because the system clock has moved; that is a different question from two instances' values *within* one run.

**Key items to scan**

- **`view.py`** — driver that imports the module, creates two instances, and also keeps a name for the class.
- **`jenny = lcg.Lcg()`**, **`genny = lcg.Lcg()`** — two constructor calls; different object IDs, initially the same looked-up value.
- **`lenny = lcg.Lcg`** — no parentheses: two names for the same class, so their IDs match.
- **`int(time.time())`** — whole-second seed from the operating system's notion of current time, not a clock coded into this class.
- **`_x`** — one leading underscore, not a double-underscore name; line 5 sets a seed, line 9 replaces it, and the added class-body print shows the replacement.
- **`id(...)`** — identifies a live object in this run; an ID is not its generator state. Compare IDs within a run rather than memorizing them.

### 1:51:27–1:59:38 | Follow the write, not just the printed letter

The call on **`lenny`** and the calls on the two instances enter different branches of the *same* ordinary method. In this deliberately unusual definition **`self=None`** is a default, not a decorator: retrieving the method via the class supplies no instance, whereas a call via an instance supplies that object automatically. The class branch writes **`Lcg._x`**. An instance without its own value can *read* that class attribute, but assigning to **`self._x`** creates a value on this particular instance. Future class writes no longer determine that instance's reads. This is why “same at first” does not mean “the three variables always change together.”

Annotated teaching version — guide-added comments; excerpt of the two no-bounds paths used in this driver, not the complete method:

```python
    def next(self=None, min=None, max=None):        # No receiver passed by lenny.next().
        if self == None:                            # Take this path for the class call.
            print(f"class id: {id(Lcg)}")            # Report the class before returning a value.
            Lcg._x = (Lcg._multiplier * Lcg._x + Lcg._increment) % Lcg._modulus
            # Only the class has been assigned to; instances need not own an _x yet.
            if min == None or max == None:          # Either bound missing: return a fraction.
                return Lcg._x / Lcg._modulus        # Return to the caller, not directly to the screen.
        else:                                      # jenny.next() or genny.next(): self is that object.
            print(f"object id: {id(self)}")          # This prints even if the caller ignores the result.
            self._x = (self._multiplier * self._x + self._increment) % self._modulus
            # Read the previous state first, then store the new integer on this instance.
            if min == None or max == None:          # No bounds were passed in the driver.
                return self._x / self._modulus      # Divide for the result; keep _x as the integer.
```

Separate bounded branches in the editor compute **`span = max - min`**, use the normalized fraction and span in a calculation named **`minmax`**, and return it. No call here supplies bounds. The full right ends of those expressions are not established by these captures; they are neither needed for this trace nor silently filled in below.

Annotated teaching version — guide-added comments (driver calls, 1:56:00; values refer to the run introduced at 1:49:00):

```python
jenny = lcg.Lcg()                 # New instance J; no private copy of _x has been assigned.
genny = lcg.Lcg()                 # New instance G; its own identity, same lookup initially.
lenny = lcg.Lcg                   # Alias for the class, not a third instance.
print(f"a   {lenny.next()}")       # Class advances first: 0.32767707761377096.
print(f"b   {jenny.next()}")       # J reads the updated class value, then saves her own successor.
print(f"c   {genny.next()}")       # G starts where J started; b and c are equal.
jenny.next()                      # Advance J again; its ID prints, its returned fraction does not.
print(f"d   {lenny.next()}")       # Class catches up to the earlier b/c result.
print(f"e   {jenny.next()}")       # J advances from the call whose return the driver discarded.
print(f"f   {genny.next()}")       # G now reaches J's earlier, undisplayed fraction.
```

The instructor asks which values stay the same after the first class call. A useful precision: neither *instance* has been assigned a local value at that point, but a fresh lookup through either instance would now see the **updated class value**, not the old class value frozen at construction. This distinction makes the actual output predictable. Inside a formatted print, the method first emits its own class/object ID line and returns its fraction; only then can the driver's print display the letter and fraction.

Observed first output and letters from the 1:49:00 terminal; intermediate integer states and the bare call's undisplayed fraction were **independently recomputed** from the observed class value. “Class” in an instance column means it still reads the current class attribute rather than having a local one.

| Driver step | Class integer after | J's local integer after | G's local integer after | Driver's displayed fraction |
|---|---:|---:|---:|---|
| Class body prints `_x: 1007915657` | 1007915657 | — | — | No driver call yet |
| Construct J and G; alias class | 1007915657 | class | class | IDs, not fractions |
| a: class call | 703681166 | class | class | 0.32767707761377096 |
| b: J | 703681166 | 1253808815 | class | 0.5838502268306911 |
| c: G | 703681166 | 1253808815 | 1253808815 | 0.5838502268306911 |
| Bare call: J | 703681166 | 224157372 | 1253808815 | None displayed; returned 0.10438141040503979 |
| d: class call | 1253808815 | 224157372 | 1253808815 | 0.5838502268306911 |
| e: J | 1253808815 | 1452920389 | 1253808815 | 0.6765687786974013 |
| f: G | 1253808815 | 1452920389 | 224157372 | 0.10438141040503979 |

A student notices that the earlier run had *different numbers* at e and f. Yes: with a new whole-second seed, the sequence can change between runs. Within this run J is one advance ahead of G. Had the bare call been printed, its value would have matched f. If G now advanced once more, its result would match e. The useful prediction is the relationship, not an address or a decimal to memorize.

**Key items to scan**

- **`lenny`** — the class alias; its first call advances the shared state before either instance creates its own state.
- **`self=None`** — class lookup here leaves the default in place; instance lookup supplies the actual object.
- **`Lcg._x`** — write the class's integer; visible through instances without their own override.
- **`self._x`** — read a previous value, then assign the successor on this specific instance.
- **`span = max - min`**, **`minmax`** — bounded-return path visible in the editor, not exercised by the lettered driver.
- **`jenny.next()`** — at line 17 the instance advances and its ID prints; no outer print requests the returned number.
- **`b`, `c`, `d`** — equal fractions in this particular call order; **`e`, `f`** are one step apart.

## 2:00:20–2:04:24 | What a directory row and an import actually name

A student notices the **Mode** column in the PowerShell listing rather than the file the instructor wants to open. The narrow terminal first shows modes without enough room for names; widening it puts **`d----`** next to **`__pycache__`** and **`-a---`** next to the Python files. The first character identifies the directory; the instructor tentatively identifies the file flag as archive. Python created the cache directory in the course of working with modules. It is not the driver to edit. The actual driver choices here include **`view.py`** and **`view as.py`**—there is a space in the second filename.

In **`view as.py`** the instructor deliberately shows two imports that would not normally both be necessary. **`import lcg`** binds the *module* name. To get its class through that name, use **`lcg.Lcg`**. **`from lcg import Lcg`** also binds the *class* directly to the local name **`Lcg`**. The two spellings refer to the same class only when the respective names have been made available; Python will not invent the capitalized name from the lowercase module import.

Annotated teaching versions — guide-added comments; contrasting the described failure with the visible 2:04:00 editor state:

```python
import lcg                       # Module name exists; earlier qualified calls can work.
# from lcg import Lcg             # Suppress the direct binding for the instructor's failure case.
# ... earlier driver calls ...
penny = Lcg                      # NameError here if there is no other binding for Lcg.
```

```python
from lcg import Lcg               # Restored in the editor: the bare class name is available.
penny = Lcg                      # Alias the class; no parentheses and no new instance.
print(f"   {penny.next()}")       # Method reports class ID; outer print displays the fraction.
```

Alternatively the module-only spelling is **`penny = lcg.Lcg`**. The instructor reports that the failure was at line 23; the upper traceback is covered in part in the frame. Beneath it is a later completed run whose last ID line is `class id: 2255015872816`, followed by `0.38841727236285806`. The displayed class call has reached the same fraction as G's preceding call; there is no third instance named Penny. Those values belong to this later run, not to the trace starting at `1007915657`. A separate local import test confirmed that the module-only plus bare-name version raises **`NameError`** and that both correctly bound spellings identify the same class.

**Key items to scan**

- **Mode**, **`d----`**, **`-a---`** — columns/flags in this PowerShell listing; the directory and ordinary file entries differ.
- **`__pycache__`** — Python-created cache directory, not a source module to import by that name here.
- **`view.py`**, **`view as.py`** — separate driver files; the latter demonstrates direct and qualified class names.
- **`import lcg`** → **`lcg.Lcg`** — bind the module, then select a member with a dot.
- **`from lcg import Lcg`** → **`Lcg`** — bind that particular member in the driver.
- **`penny = lcg.Lcg`** — valid module-qualified alternative; Penny refers to the class.
- **`NameError`** — the bare class name was missing from this driver's namespace, not the module missing from disk.

## 2:05:06–2:11:38 | From grocery-list links to the first revision of Item

### 2:05:15 | An array index is not a link to a neighbor

Think of writing *bread* on the refrigerator list, then *milk* when the last milk is used. One item follows another; at the store you visit the entries, perhaps thinking about the order of aisles. The instructor contrasts three ways a computer can organize that idea. In the classic **array** model described here, same-type elements occupy contiguous storage. An index locates an element quickly, but extending the same block in place is not always possible—the next memory can already be occupied. In a **singly linked list** an item instead holds a link to its successor: bread can tell you where milk is, not where bread's predecessor is. In a **doubly linked list** you give each item a previous link as well; from a middle item you can follow neighbors both ways, provided those links have actually been set. This is not a claim about Python's built-in list being a chain of these custom items.

Many presentations have one class for the list and another for a **node**, an entry holding a value and links. The instructor chooses just one class: an **`Item`** can serve as the starting point for a chain of items. It is a design choice, not a requirement of Python. Trees and graphs are mentioned as other data structures, not implemented here. Although the intended structure is doubly linked, the *first checkout* wires only forward references.

**Key items to scan**

- **Array** — in the instructor's classical model, contiguous same-type storage supports direct indexed access; growth might require moving to other storage.
- **Singly linked list** — each item leads to its next item; there is no previous link to follow.
- **Doubly linked list** — next and previous links enable traversal both ways when connected.
- **Node**, **`Item`** — the instructor's one-class design puts a value and neighbor references on each item rather than adding a separate list wrapper.

### 2:08:31 | Read repository state before attributing behavior to a file

The switch from the generator project to the **`Item`** directory brings a new repository and a new timeline. Seeing **`.gitignore`** prompts the instructor to check **`git status`**. The report distinguishes one modified tracked file, **`script/path.ps1`**, from untracked entries such as **`app/`**, **`pylintrc`**, **`src/pyproject.toml`**, and **`test/clearTest.py`**. Nothing was staged. A working directory can legitimately contain material not committed to the repository; an untracked file is not automatically a mistake or an ignored file.

| Command or observed state | What it established in this project |
|---|---|
| `git log --oneline` | One row per displayed commit. Tip `843e915 (HEAD -> master) generator` is far more developed than the first version. |
| `git rev-list --count master` | Printed `22`: count of commits reachable from `master`. The actual option is `--count`, not `--reverse` despite the oral “reverse list” description. |
| `git checkout 47413b4` | Checkout by the oldest displayed short ID, rather than the proposed ancestor-relative alternative using a tilde. |
| `HEAD is now at 47413b4` | Detached HEAD: inspect that specific revision, not the moving branch tip. The terminal still reports `M` for `script/path.ps1`. |

The instructor connects this to list traversal: a commit knows its parent(s); in a simple linear history, walking backward is natural, and a parent does not supply a direct “next child” pointer. Treat “singly linked list” as the analogy for this walk, not a claim that all Git histories are linear—merges can have multiple parents. Counting commits includes the starting commit; it is not a count of links to follow from the tip. One visible check on what changed is the directory listing: `item.py` is 5561 bytes before checkout (2:06:00), but only 66 bytes in the early checkout (2:15:00). The log near the root previews the sequence: direct assignments first, then a second reference to fruit, then use of the previous link, then a constructor. A later feature in a commit message does not establish that it already exists in the checked-out class.

**Key items to scan**

- **`Item`**, **`.gitignore`** — new repository context and the clue that prompts a status check.
- **`git status`**, **`script/path.ps1`** — a tracked local modification; the checkout still reports it.
- **`app/`**, **`pylintrc`**, **`src/pyproject.toml`**, **`test/clearTest.py`** — listed as untracked, not additions to the commit.
- **`git log --oneline`** — distinguishes tip `843e915` from first displayed historical version `47413b4`.
- **`git rev-list --count master`** — observed `22`, not a checkout and not a reverse-order option.
- **`git checkout 47413b4`** — changes the inspected revision; detached HEAD is expected here.

## 2:12:06–2:19:59 | Build a three-item chain, then explain why the import worked

### 2:12:13 | What the first class and its driver can already do

The instructor opens **`src\item.py`** and **`test\fruit.py`**, then runs the driver. Formal unit testing and functional testing are deferred: this is a small executable demonstration, not a test framework. The class provides three defaults. **`object`** is the value to keep; **`next`** and **`prev`** are the forward and backward neighbors. Initially all are **`None`**. The editor colors the name **`object`** like Python's built-in. But **`Item.object`** is a name inside the class's namespace; it has not replaced Python's built-in globally. The instructor might prefer another name for readability. Syntax highlighting is a hint, not a name-resolution test.

Annotated teaching version — guide-added comments on the 2:13:00 editor state:

```python
# src\item.py
class Item:                         # Define one kind of entry, not a separate list container.
    object = None                   # No value has been supplied yet.
    next = None                     # No following entry by default.
    prev = None                     # No previous entry by default.
```

```python
# test\fruit.py
from item import Item               # From the module found as item, bind the class Item.
fruit = Item( )                     # New first entry; fruit keeps referring to this one.
fruit.object = "apple"              # Store apple on the existing first entry.
fruit.next = Item( )                # Create a NEW second entry; link apple's entry to it.
fruit.next.object = "banana"        # Follow one link; store banana on the second entry.
fruit.next.next = Item( )           # Create a NEW third entry; point banana's next at it.
fruit.next.next.object = "peach"    # Follow two links; store peach on the third entry.
print( fruit.object )               # Start at fruit; display apple.
print( fruit.next.object )          # Follow one next link; display banana.
print( fruit.next.next.object )     # Follow two next links; display peach.
```

Observed in PowerShell after `py .\test\fruit.py` (2:14:00); separately reproduced using the two files and an isolated search path:

```text
apple
banana
peach
```

The variable **`fruit`** never moves. Reading a longer dotted expression visits more nodes; it does not reassign the starting variable. The right-hand **`Item( )`** is a new object before it is installed as a neighbor. In this revision the final forward link is still **`None`**, and **all** backward links are still **`None`**. Merely declaring a slot does not connect anything in reverse.

| After driver line | Reachable chain from fruit | What remains unconnected |
|---|---|---|
| 4 | apple | Apple has no successor yet. |
| 5–6 | apple → banana | Banana has no successor; neither entry has a previous neighbor. |
| 7–8 | apple → banana → peach | Peach has no successor; no backward links have been assigned. |

**Key items to scan**

- **`src\item.py`**, **`test\fruit.py`** — class definition and its driver live in different directories.
- **`Item.object`**, **`object`** — a class-scoped name for stored data, distinct from the global built-in name.
- **`next`**, **`prev`** — neighbor slots; the driver only assigns forward links in this version.
- **`None`** — default for a missing value or missing neighbor; not the text `"None"`.
- **`fruit`**, **`Item( )`** — the first name continues to point at apple; each new call creates a separate entry.
- **`fruit.next.next.object`** — start at apple, go to banana, then peach, then read the stored string.

### 2:14:18–2:19:59 | Inspect a script instead of running it

How does **`from item import Item`** work if **`fruit.py`** is in **`test`** and **`item.py`** is in **`src`**? Earlier the generator driver and module lived together. This driver uses a different arrangement: the instructor has an environment setup script. **PowerShell**, like Python, can interpret commands interactively or read them from a file. A **`.ps1`** file contains PowerShell instructions—not Python instructions. Here the first attempt to *look at* **`script\path.ps1`** instead executes it.

| At the PowerShell prompt | Observed effect at 2:18:00 |
|---|---|
| `.\script\path.ps1` | Run the script; no source listing appears. |
| `& .\script\path.ps1` | The call operator also invokes the script; still no source listing. |
| `echo .\script\path.ps1` | Prints the argument `.\script\path.ps1`; this is the filename, not its contents. |
| `cat .\script\path.ps1` | Reads the file and displays the assignment below. |

Observed script contents (PowerShell; explanation follows so it is not mistaken for script output):

```powershell
$env:PYTHONPATH="$env:PYTHONPATH;C:\CSCS1240F26\Item\src"
```

Read the **right** side first: expand the previous **`$env:PYTHONPATH`** inside double quotes, add **`;`**, then append the class's source directory. Assign the combined string back to the **left** side. The dollar sign introduces the variable expression; **`env:`** selects the environment, and **`PYTHONPATH`** is the variable name. The semicolon separates path entries on Windows. This is an addition to a list of search locations, not a move of either Python file. The instructor emphasizes preserving paths already present rather than replacing them. Uppercase follows the spelling convention being shown; retain it when reproducing the example.

**Independently verified teaching check:** Starting a separate PowerShell process with this variable set to `before` yielded `before;C:\CSCS1240F26\Item\src` after the assignment. Running a scratch copy of the Python driver in a clean module-search setup failed with `ModuleNotFoundError: No module named 'item'` without the source path, and printed the three fruits with that path supplied. These are independent checks, not additional classroom runs. The instructor advises not to put spaces around the assignment, referring to older command-shell habits; the same isolated PowerShell check used spaces around **`=`** successfully. Distinguish the exact script as shown from a requirement of PowerShell.

**Key items to scan**

- **`from item import Item`**, **`fruit.py`**, **`test`**, **`item.py`**, **`src`** — Python must first locate the source module before it can bind the named class.
- **PowerShell**, **`.ps1`**, **`script\path.ps1`** — the shell can run a file of commands instead of waiting for interactive input.
- **`echo`** — displays its supplied pathname; **`cat`** displays the file's text in this example.
- **`$env:PYTHONPATH`**, **`env:`**, **`PYTHONPATH`** — access the search-path setting through PowerShell's environment namespace.
- **`;`**, **`=`** — separate Windows path entries, then assign the expanded new string; the script preserves the old value.

### 2:20:00 — Separate a search-path problem from a list problem

If the test still cannot find **`item.py`**, the instructor suggests putting the test beside the module and running it there. That does not repair the original search path. It isolates the environment problem so you can study the list and ask for path help separately.

**Key items to scan**

- **`item.py`** — the file Python must locate before any node construction can run.
- **Same-directory test** — the instructor’s fallback, not a requirement that all Python tests live beside their modules.

### 2:21:06 — What exactly is stored in `fruit.next`?

The class question was not just “Is the next thing new?” but “A new *what*?” **`fruit.next = Item( )`** evaluates the right side to a new instance. It then stores a reference to that instance on the first one. It neither replaces **`fruit`** nor stores a copy of apple's contents. Immediately after that assignment the first node still holds `apple`, and the second node's data still reads as `None`. The dot after **`fruit.next`** is what lets the next line reach into that *other* node. By the end, peach's forward link and all three backward links still read as `None`. The growing chains of dots work; the next revision asks for a more convenient way to read the same items.

**Key items to scan**

- **`Item`**, **`item.py`**, **`fruit.py`** — node class, module defining it, and separate driver building the three nodes.
- **`fruit`** — reference to the first node, not another name for the string `apple`.
- **`object`** — this class's data attribute; `fruit.object` holds `apple`.
- **`next`**, **`fruit.next`** — forward link; from apple it reaches the banana node, not the banana string.
- **`fruit.next = Item( )`** — creates a distinct node before linking to it.
- **`prev`**, **`None`** — a backward link exists as a name but has no neighbor here; `None` marks absence, not an empty fruit string.

## 2:24:37 — Use Git history to change the traversal, not the nodes

The class is comparing prepared versions of the same project. The instructor describes 22 commits and asks whether **`master~22`** is available. Count the starting commit as distance zero: in a first-parent chain of 22 commits, the last available distance is 21, not 22. Here is what the PowerShell scrollback actually establishes (2:25:00, full text visible at 2:30:00).

| Command | Result in this repository |
|---|---|
| `git checkout master~22` | `error: pathspec 'master~22' did not match any file(s) known to git`; no checkout completed. |
| `git checkout master~21` | `HEAD` at `47413b4`: the direct assignments to object and next. |
| `git checkout master~20` | `HEAD` moved to `f70c1ad`: `Fruit: use of second reference to fruit.` Same class; changed driver. |
| `git log --oneline` | Shows `f70c1ad (HEAD)` and its predecessor `47413b4`, with the author's short descriptions. |

A student's question about “second reference” exposes two different histories. A *commit message* is the author's reminder of what changed in a file. It does not tell Python to advance to the next fruit. The new executable statement **`iter = fruit`** does exactly the opposite of advancing: it gives a second name to the *current first node*. Only a later assignment follows a link. The instructor chose **`iter`** because the intention is to iterate; this assignment does not define or call Python's **`__iter__`** method.

**Key items to scan**

- **`master~22`** — twenty-two first-parent steps from `master`; not a resolvable target in this example.
- **`HEAD`** — the checked-out commit, not the first node of the Python list.
- **`git log --oneline`** — compare short commit IDs and human-written messages.
- **`iter = fruit`** — a second reference, not a second node and not a move to banana.
- **`iter`** — the instructor's traversal variable here; **`__iter__`** is a distinct Python method name, not implemented in this listing.

### 2:26:03 — Hold on to apple while another name walks forward

The setup still builds apple → banana → peach. Instead of lengthening every expression from the beginning, keep the current node in **`iter`**. Assignment evaluates the right-hand side using the *old* current node, then binds the same name to the resulting next node. It changes no link and no fruit data.

Annotated teaching version — guide-added comments; continuation after the three-node setup, as seen at 2:26:00.

```python
iter = fruit                         # Two names now reach the SAME apple node.
print( iter.object )                 # Read the current node's data: apple.
iter = iter.next                     # Read apple.next; now iter refers to banana.
print( iter.object )                 # Read the new current node's data: banana.
iter = iter.next                     # Read banana.next, not apple.next; now at peach.
print( iter.object )                 # Display peach; fruit still refers to apple.
```

Independently verified for this traversal with the pictured setup: `apple`, `banana`, `peach`, one per line. The plain three-line output that remains in the terminal at 2:26:00 is scrollback from the direct-access example; it is not evidence of a new run of the checkout. The class later runs an instrumented version.

**Key items to scan**

- **`iter`** — the current node during the walk; first apple, then banana, then peach.
- **`iter.object`** — read the current fruit string without moving anywhere.
- **`iter = iter.next`** — read the link on the node currently named, then reassign the name; the repeated line therefore reaches a different fruit each time.

## 2:29:11 — Prove when two names do and do not refer to the same object

Before running the program, the instructor asks two questions: Is there any syntax error? If it runs, what should it display? A student correctly predicts that two names for one node should print the same thing. Why, then, do the two object displays differ? First notice *what* gets printed: **`print(fruit)`** displays the node itself in its default object representation; **`print(fruit.object)`** displays its data. Next ask *when*. The first experiment adds just these statements **after** the walk:

Annotated teaching version — guide-added comments, 2:30:00.

```python
print(fruit)                         # Original first node: the holder of apple.
print(iter)                          # Current last node after two moves: peach's holder.
```

Observed output from **`py .\test\fruit.py`** at 2:30:00, including the preceding traversal:

```text
apple
banana
peach
<item.Item object at 0x000001C6B3FF8590>
<item.Item object at 0x000001C6B3FEC690>
```

The two representations name the same *type*, not the same *instance*. The instructor cautions against reading a relationship into similar-looking addresses. Memory ultimately comes from the operating system; creation close together in time may yield nearby addresses, but neither proximity nor a particular address is guaranteed. Use links to decide which node is next, not the spacing of address numbers. Compare identities within the same run, not across launches.

The next experiment puts the same diagnostic at the beginning *as well as* the end. Notice the two different roles of the word “print”: the blank **`print()`** adds a visual separator, whereas printing a node does not move the traversal at all.

Annotated teaching version — guide-added comments; after the unchanged setup, 2:33:00–2:37:00.

```python
iter = fruit                         # Start both names on apple.
print(fruit)                         # Show the first node itself.
print(iter)                          # Show the same node: these first two lines match.
print()                              # Print a blank line before the fruit names.
print( iter.object )                 # Display apple, not its node representation.
iter = iter.next                     # First move: apple to banana.
print( iter.object )                 # Display banana.
iter = iter.next                     # Second move: banana to peach.
print( iter.object )                 # Display peach.
print(fruit)                         # Still the original apple node.
print(iter)                          # Now a different node, holding peach.
```

Observed output, same invocation, separate launch (2:34:00):

```text
<item.Item object at 0x000002092BC18590>
<item.Item object at 0x000002092BC18590>

apple
banana
peach
<item.Item object at 0x000002092BC18590>
<item.Item object at 0x000002092BC0C690>
```

| Execution point | What the right side reads | `fruit` after | `iter` after | What the following display establishes |
|---|---|---|---|---|
| `iter = fruit` | First node itself | apple node | apple node | Same object twice; its data is `apple`. |
| First `iter = iter.next` | Apple node's next link | apple node | banana node | Data is `banana`. |
| Second `iter = iter.next` | Banana node's next link | apple node | peach node | Data is `peach`; the final object displays differ. |

**Class question:** Why are lines 15 and 17 identical? Should the first say `iter = fruit.next`? On the *first* move those right sides would reach the same node, because the names still agree. On the *second* move starting from `fruit.next` again would return to banana; starting from `iter.next` reaches peach. The key debugging move was to print the references on both sides of the suspected change. The first pair confirmed the student's initial prediction instead of dismissing it; the second pair located the effect of reassignment. Neither reading nor printing **`iter.object`** caused it.

**Key items to scan**

- **`print(fruit)`**, **`print(iter)`** — show the holders themselves; equal representations at the start, different ones after two moves in this class and run.
- **`print(fruit.object)`**, **`iter.object`** — read stored data; a fruit name is not the node's identity.
- **`py .\test\fruit.py`** — launching it again starts a new run; do not expect the old addresses to repeat.
- **`print()`** — empty output line between initial identities and fruit data, not a fourth fruit.

### 2:37:56 — Find the same peach starting from two different places

How do you name the current node *relative to the beginning*? At this point **`iter`** already refers to peach. Starting at **`fruit`** takes two links to get there. Taking one more link from the current node is a different question: peach's next link is still **`None`**. A draft **`print()`** is visible at 2:38:00; it is not evidence of another run. The completed diagnostic at 2:39:00 adds the following final line, after the previous two object prints:

Annotated teaching version — guide-added comment.

```python
print(fruit.next.next)               # From apple, go to banana and then peach; same node as iter.
```

Last three lines of the **new** observed 2:39:00 run, in order:

```text
<item.Item object at 0x0000026A93698590>
<item.Item object at 0x0000026A9368C690>
<item.Item object at 0x0000026A9368C690>
```

The instructor recommends keeping the starting reference when traversing a list. “Make a copy” here means copy the **reference** with **`iter = fruit`**, not clone the list. If the only way back to apple is the name `fruit`, rebinding that name to banana loses that route. A forward-only (singly linked) chain has no way to follow banana backwards to apple. Although this class already has a **`prev`** attribute, it hasn't been wired in this revision. A separate moving name works without relying on backward links.

**Key items to scan**

- **`fruit`** — preserve this starting name so apple stays reachable.
- **`iter`**, **`iter = fruit`** — begin with an alias, then move the traversal name without moving the start.
- **`fruit.next.next`** — peach measured from apple; the final two node displays match.
- **`prev`**, **`None`** — in this version backward links are unset; walking *forward* past peach would also yield `None`.
- **`print()`** — the temporary last line seen before `print(fruit.next.next)` was entered, not the source of the three object displays.

## 2:41:10 — Git refuses to discard the experiment; the editor notices the later replacement

The instructor saved diagnostic edits to the test file. Checking out the next historical state would overwrite them. A student asks whether to answer “yes.” This message is not asking a question: **`Aborting`** means the attempt is already over. A repeated command sitting on the prompt at 2:42:00 is not a second successful checkout.

| Observed action | Effect |
|---|---|
| `git checkout master~19` | Fails: `error: Your local changes to the following files would be overwritten by checkout:` names `test/fruit.py`, recommends committing or stashing, then prints `Aborting`. |
| `git restore .\test\fruit.py` | The instructor deliberately discards the tracked file's local working-copy edits rather than force the checkout. This is **not** the action to take if a learner wants to keep those edits. |
| `git checkout master~19` | Now succeeds: previous `HEAD` was `f70c1ad`; new `HEAD` is `0203781`, `Fruit: use of prev attribute.` The separate `M` for `script/path.ps1` remains. |

**Class question:** Did the red editor indicator mean the file had ceased to exist? No. The instructor explains that Git changed the file *on disk* while the editor was showing its earlier content. The editor detected the difference; returning focus refreshed it. At 2:43:00 the terminal reports the new commit while the editor still shows the diagnostic prints. At 2:44:00 it shows the backward-link version. A student wanted to retain a mistaken attempt for later comparison. Keep a copy, commit, or stash the edits *before* replacing them; a screen buffer is not a backup. The instructor mentions forcing a checkout as a possibility, not as the demonstrated command.

**Key items to scan**

- **`Aborting`** — Git refused the first checkout; no yes/no prompt is pending.
- **`git restore .\test\fruit.py`** — deliberately discard the driver's working edits; not a general “save my work” command.
- **`git checkout master~19`** — succeeds on retry after restore; `0203781` supplies a different driver.
- **`M`** — `script/path.ps1` is still reported modified; the restore targeted a different file.
- **Editor change indicator** — the disk and the displayed buffer can disagree until the editor refreshes.

## 2:44:00–2:48:18 | Give banana a way back to apple

At the checkout, a student wanted to know whether overwritten work would be gone. The instructor distinguished throwing away an uncommitted edit from putting it aside with **`git stash`**. Stashing was discussed as an alternative, not performed here. The terminal shows the earlier refusal to overwrite the locally changed driver, then a restore and a successful checkout. The instructor points to the commit message: the change is the *use* of the backward link. The class already has the field. It is the driver that now fills it in.

**Key items to scan**

- **`git stash`** — the instructor's alternative for setting work aside rather than deliberately discarding it.
- **`git restore .\test\fruit.py`** — in this demonstration the driver was restored before retrying the checkout; the local experiment was not saved by that command.
- **`0203781`** — the checkout whose message reads `Fruit: use of prev attribute.`; its driver links neighbors in both directions.

The refreshed driver is visible at 2:44:00, but the five-line result only becomes visible in a later run at 2:48:00. Draw three boxes, the instructor suggests, and put a fruit name inside each one. The boxes are **nodes**: each holds a value and can refer to other boxes. Putting the string `"banana"` in the second box does not tell it how to get back to apple. Two assignments build that connection: apple's **`next`** points to banana's box, and banana's **`prev`** points to apple's. Similarly, peach's predecessor is the *second node*, not the string `"banana"` and not the first node. This is a **doubly linked list**. The question “where does next-previous take you?” is easiest to settle by tracing the arrows from an explicit starting box.

Annotated teaching version — guide-added comments (class and driver shown separately; 2:45:00–2:48:00):

```python
# item.py
class Item:                         # Describe the boxes; this does not create a fruit node.
    object = None                   # Default stored value until a node receives its own.
    next = None                     # No following node by default.
    prev = None                     # No preceding node by default.
```

```python
# test/fruit.py
from item import Item               # Bring the class name into the driver.
fruit = Item( )                     # Make a NEW first node; fruit keeps referring to it.
fruit.object = "apple"              # Store the string in that node.
fruit.next = Item( )                # Make a NEW second node; link apple forward to it.
fruit.next.prev = fruit             # Link the second node back to the first node itself.
fruit.next.object = "banana"        # Store banana in the second node.
fruit.next.next = Item( )           # Make a NEW third node; link banana forward to it.
fruit.next.next.prev = fruit.next   # Link the third node back to the second, not to apple.
fruit.next.next.object = "peach"    # Store peach there; its next link is still None.
```

Now read the same boxes without changing any arrows. The driver uses **`iter`** as an ordinary variable for the current node, not as a call to Python's iterator machinery. Reassigning it leaves the nodes and the starting reference **`fruit`** alone.

Annotated teaching version — guide-added comments:

```python
iter = fruit             # Start at apple; no node is copied.
print( iter.object )     # apple
iter = iter.next         # Move to banana.
print( iter.object )     # banana
iter = iter.next         # Move to peach.
print( iter.object )     # peach

iter = iter.prev         # Follow peach's backward link, not another forward link.
print( iter.object )     # banana
iter = iter.prev         # Return to the first node.
print( iter.object )     # apple
```

The terminal at 2:48:00 shows **`py .\test\fruit.py`** followed by these lines; a separate local reconstruction produced the same five lines:

```text
apple
banana
peach
banana
apple
```

| Driver lines | Current node before move | Link followed | Current value printed |
|---|---|---|---|
| 12–13 | Not yet chosen | Start at fruit | apple |
| 14–15 | apple | Next | banana |
| 16–17 | banana | Next | peach |
| 19–20 | peach | Previous | banana |
| 21–22 | banana | Previous | apple |

**Key items to scan**

- **Nodes** — three different `Item` instances; the strings are what they hold.
- **`next`** — apple's goes to banana; banana's goes to peach; peach's is still `None`.
- **`prev`** — banana's goes to apple; peach's goes to banana; apple's is still `None`.
- **Doubly linked list** — adjacent nodes can be reached in either direction because *both* references have been set.
- **`fruit`** — stays at the first node even when the traversal reaches peach.
- **`iter`** — a movable reference; reassigning it does not alter a stored link.
- **`py .\test\fruit.py`** — runs the driver; each `print` displays the current node's string, not the node's identity.

## 2:48:18–2:52:11 | Which line actually fails past peach?

Asked what could go wrong, a student proposed a fourth fruit, cherry. The group counted forward links: **`fruit.next.next`** already reaches peach. Appending beyond peach would require a new node at **`fruit.next.next.next`**, a value for that node, and a backward link to peach if the return path is to work. Assigning the string alone would not build such a node. Cherry remained a discussion, not a fourth fruit printed by the driver.

The experiment the class *did* run was smaller: keep the same three fruits and ask for one more forward step. First predict whether the source can start at all (syntax), then whether an operation fails while running (an exception), then whether a program that finishes gives the wrong result (a logic error). The three fruit lines already printed rule out a syntax error in this driver. They do not prove every later line works.

Annotated teaching version — guide-added comments, changed fragment after peach was printed:

```python
iter = iter.next         # Reading peach.next succeeds: iter now holds None.
print( iter.object )     # Fails while evaluating the argument; there is no fourth print result.
```

Observed first overrun (traceback readable at 2:52:00 in the preceding run's scrollback):

```text
apple
banana
peach
AttributeError: 'NoneType' object has no attribute 'object'
```

The instructor's next revision asks to see the reference itself *before* asking for its stored value. It isolates the line that failed without repairing the bug.

Annotated teaching version — guide-added comments, diagnostic revision at 2:52:00:

```python
iter = iter.next         # The fourth position is absent; assignment itself is legal.
print(iter)              # Displays None. No access to .object is requested here.
print( iter.object )     # Now line 20: None has no attribute named object.
```

| Diagnostic run | State/result |
|---|---|
| The first three displays | `apple`, `banana`, `peach`, each on its own line |
| Move beyond peach | `iter` changes from the peach node to `None`; nothing is printed by the assignment |
| Print the reference | `None` appears on its own line |
| Evaluate the next print argument | `AttributeError: 'NoneType' object has no attribute 'object'`; execution stops before either backward step |

Both failures and their preceding output were independently reproduced. A useful debugging question is not “Can Python print nothing?”—it can display **`None`**—but “What was the object to the left of the dot when the attribute was requested?” The instructor leaves handling that boundary for later; the diagnostic is not a guard that prevents the error.

**Key items to scan**

- **`fruit.next.next`** — the existing peach node, not an unused place for cherry.
- **`fruit.next.next.next`** — where a link to a hypothetical fourth node would go; the destination was never constructed in class.
- **`iter = iter.next`** — legal on peach; fetching its default next link gives `None`.
- **`print(iter)`** — the diagnostic that reveals `None` without inspecting an attribute.
- **`None`** — a valid Python value representing no linked node, not a node with an empty string.
- **`AttributeError`** — the exception comes from looking up `object` on `None`; the following backward walk is not reached.

## 2:52:11–2:59:16 | Set the value when creating the node

The instructor restores the driver and moves to another revision. This time **`item.py`** changes too. Earlier, making a node and placing a fruit in it took two statements. A student asks why add a method if the output is exactly the same. The answer is in the *caller's work*, not the walk: callers can now hand a new node its value immediately. The forward and backward links still need to be set.

Annotated teaching version — guide-added comments, optional-value revision:

```python
class Item:                            # Same kind of node as before.
    object = None                      # Class-level default stored value.
    next = None                        # Default forward link is unchanged.
    prev = None                        # Default backward link is unchanged.

    def __init__(self, obj = None):    # Python supplies the new node; caller may supply a value.
        self.object = obj              # Give THIS instance banana, peach, or the default None.
```

Calling **`Item( )`** asks Python to make a fresh instance. The instructor recalls the earlier LCG example and the two objects called Jenny: a shared class does not mean a shared identity. In ordinary construction here, **`__new__`** creates the instance; **`__init__`** then initializes it. What the instructor calls the “constructor” is this initialization hook. **`self`** refers to the instance being initialized; it is not an address number the caller must calculate or pass. **`obj`** is the separate input chosen by the caller. Before this revision the class needed no custom initializer; now it has a specific job.

The 2:54:00 editor briefly shows the contrast below; no corresponding run of the bare-class version is shown. These are alternatives, not successive steps of the fruit walk.

```python
fruit = Item       # Refers to the class itself; creates no fruit node and invokes no initializer.
fruit = Item( )    # Calls the class, creates a new node, and rebinds fruit to that node.
```

The driver deliberately keeps the two-step apple initialization alongside the more compact banana and peach calls:

```python
from item import Item                          # Use the class that accepts an optional obj.
fruit = Item( )                                # New instance: its own object attribute begins at None.
fruit.object = "apple"                         # Replace that None with apple on this same instance.
fruit.next = Item( "banana" )                  # New instance; positional argument sets its value to banana.
fruit.next.prev = fruit                        # The initializer does not build the backward link.
#fruit.next.object = "banana"                  # Original inactive assignment; value already set.
fruit.next.next = Item( obj = "peach" )        # New instance; keyword argument names the value parameter.
fruit.next.next.prev = fruit.next              # Link peach back to the existing banana node.
#fruit.next.next.object = "peach"              # Original inactive assignment; no second write needed.
```

The instructor notes that the examples use strings but the stored value need not be text. The walk is unchanged, so the unchanged output is a useful check: after the constructor checkout, the 2:58:00 terminal again shows **`py .\test\fruit.py`** printing `apple`, `banana`, `peach`, `banana`, `apple` on five lines. This is a later successful run, not the earlier `None` diagnostic. The optional-value reconstruction also produced those five lines independently.

**Key items to scan**

- **`item.py`** — now defines how a newly created node gets its own stored value.
- **`Item`** — the class itself; assigning the bare name does not create or initialize a node.
- **`Item( )`** — a call making a new instance; in this revision its stored value starts as `None`.
- **`__new__`** — creation step; the new instance is what the initializer receives.
- **`__init__`** — automatically initializes that instance; the driver never calls the method by name.
- **`self`** — this particular node; assigning `self.object` does not replace another node's fruit.
- **`obj`** — the input for the stored value; omitting it selects the default `None` in this version.
- **`Item( "banana" )`** — supplies banana by position; no separate write to its value is needed.
- **`Item( obj = "peach" )`** — supplies peach by the parameter's name; the result is still a node.
- **`py .\test\fruit.py`** — the later run tests that combining creation and initialization preserved the walk in both directions.

## 2:59:16–3:01:46 | Require the input: why line 8 cannot rescue line 3

What happens if the initializer has no default? The instructor changes its signature, leaves the driver alone, and runs it again. Now the very *first* node request fails. Apple never gets written, and this invocation prints no fruit.

Annotated teaching version — guide-added comments, changed method and failing call in their respective files:

```python
# In class Item (item.py):
    def __init__(self, obj):    # No default: the caller must now supply obj.
        self.object = obj       # Runs only if Python can bind both parameters.
```

```python
fruit = Item( )                 # Driver line 3: no value for obj; raises TypeError here.
```

The 3:00:00 terminal shows the failing call and the start of the TypeError. Its right-hand text is covered by the editor. The instructor identifies the missing positional argument as **`obj`**; independent execution of this signature and call gives the full message:

```text
TypeError: Item.__init__() missing 1 required positional argument: 'obj'
```

A student asks whether the later line **`fruit.next.next = Item( obj = "peach" )`** would work on its own. In the driver it never runs: an unhandled exception on line 3 stops execution. In a *fresh* environment running just line 8, Python first evaluates its right side. It needs the `Item` name from the import. Even if the class is available and the peach node is created, placing it on the left still requires an existing first node and its next node. The keyword argument solves the peach initializer's input requirement, not these earlier setup steps.

The design decision is whether an omitted value should be accepted. Removing the default requires the caller to *supply an argument*. It does not by itself validate the value: an independently checked **`Item(None)`** still stores `None`. That is different from forbidding an absent argument. Other attributes, such as the link beyond the last node, have a good reason to start unset.

**Key items to scan**

- **`def __init__(self, obj):`** — requires an input for `obj`; no value is substituted when it is omitted.
- **`obj`** — the missing argument, not the automatically supplied `self`.
- **`TypeError`** — occurs during the first construction attempt, before any fruit has been printed in that run.
- **`fruit.next.next = Item( obj = "peach" )`** — an acceptable call on its right side does not create the earlier nodes needed by its left side.
- **`Item(None)`** — explicitly supplies an argument; to disallow that value would require a different check.

## 3:01:46–3:04:20 | What a next link can stand for

The instructor stops the demonstration here, encourages students to try it themselves, and estimates there are roughly eighteen more revisions in mind—not eighteen additional revisions demonstrated today. The linked list has served two purposes: it provides a usable way to connect things and gives a reason to introduce Python features as they become useful.

A final question for later work returns to symbolic arithmetic: suppose a base has an ordered set of symbols, but you do not want the program to navigate it using integer indices. Instead of asking for symbol zero, one, two, and three, what if each symbol knew its successor? The same step that took the fruit walk from apple to banana could move from one symbol to the next. This is a proposed way to **navigate symbols**, not a demonstrated arithmetic algorithm, and the instructor explicitly says no immediate work on it is required.

In response to a scheduling question, the instructor said the next **Essentials lab** would start at **12:30**, earlier than its previous time. This was the announcement in the recording, not a verified current schedule. The closing target at 3:04:20 shows the recording software over the editors as the instructor signs off.

**Key items to scan**

- **Navigate symbols** — follow a successor reference instead of choosing an integer index; arithmetic without numbers was a possible future application.
- **Essentials lab** — the lab named in the closing schedule question.
- **12:30** — the start time announced to that class, not an assignment deadline.

## Syntax to remember

| Pattern | Read it in this lesson |
|---|---|
| `int(time.time())` | Call the module function, truncate the returned seconds, then store the integer. |
| `(a * x + c) % m` | Calculate the successor using old `x`; keep the remainder, not the quotient. |
| `state / modulus * span + min` | Normalize, stretch the fraction to the requested width, then shift the lower endpoint. |
| `lcg.Lcg` versus `lcg.Lcg()` | Refer to the class versus construct a new generator instance. |
| `if self == None:` | In this method, select the class branch when a zero-argument class call supplies no instance. |
| `print(f"   {jenny.next()}")` | Evaluate the method, then format and display its return. The three spaces indent the displayed number. |
| `from item import Item` | Locate the lowercase module, then bind the uppercase class in the driver. |
| `iter = iter.next` | Read the current node’s forward link, then move the name. The nodes stay put. |
| `fruit.next.prev = fruit` | Store a reference to apple in the banana node. This changes a node, not the starting name. |
| `Item(obj = "peach")` | Supply the value by parameter name. Contrast `Item( )`: omission works only with the optional initializer. |
| `git checkout master~19` | Request the first-parent ancestor 19 steps behind the branch tip. A refusal is not a change of revision. |
| `cat .\script\path.ps1` | Display the script text in PowerShell; running it and echoing its pathname are different operations. |

## Knowledge check

1. The destination college says it will accept your computing course. What question is still unanswered?
2. Start at 3, double the state, and take the remainder modulo 9. Which number comes back first? What does this tell you about a seed and a period?
3. In the lettered run, why can `b`, `c`, and `d` match even though Jenny and Genny have different IDs? Why is there an ID between `c` and `d` that has no fraction beside it?
4. Why does `penny = Lcg` fail in a file that has already successfully used `lcg.Lcg`? Name two ways to make the reference work without creating an instance.
5. Begin at apple with `iter = fruit`. After `iter = iter.next` twice, which name still finds apple? Which statement would actually change a link rather than move the traveler?
6. A successful two-way traversal prints five fruit names. An edited driver instead moves forward once past peach. Which statement can succeed with `None`, and which operation fails? How does this differ from the final missing-argument failure?
7. Git prints `Aborting` after warning about local edits. Has the checkout happened? If you want to keep your experiment, should you repeat the instructor’s restore?

### Answer key

1. **Which requirement will it satisfy?** An elective credit is not necessarily credit for the destination program’s required course, even when the titles match. Ask the receiving department before relying on the transfer.
2. **3 → 6 → 3.** Doubling 6 yields 12; removing one 9 leaves 3. The period is two updates. Starting at 1 with the very same rule visits six states, so a new seed need not just shift your position in a single cycle.
3. **They start those advances from the same class integer.** Jenny and Genny each read it, compute its successor, and save the result on themselves. The class was not changed by their writes; its next advance therefore makes that same step. The bare `jenny.next()` between the prints still changes her state and prints her ID inside the method; no outer print displays her returned fraction.
4. **The class exists; its short name is not bound in the driver.** `import lcg` binds the module name. Use `penny = lcg.Lcg`, or first use `from lcg import Lcg` and then `penny = Lcg`. Parentheses would change either class reference into a construction request.
5. **`fruit` still finds apple; `iter` now finds peach.** The two assignments rebind a name but do not change a node. In contrast, `fruit.next.prev = fruit` writes a reference into the banana node, giving it a route back to apple.
6. **Reading peach’s `next` and assigning it to `iter` succeeds.** There is no fourth node, so `iter` now holds `None`. `print(iter)` can display that value; looking up `iter.object` raises `AttributeError` before the outer print runs. The final `TypeError` occurs much earlier: the driver calls `Item( )` with a required `obj` missing. That invocation never even prints apple.
7. **No.** `Aborting` is the outcome, not a prompt to answer. The instructor deliberately restored the one tracked driver before retrying. To retain your edits, save them separately, commit, or stash first; do not use restore as a backup.

## Library of definitions

### Generator arithmetic and state

| Term | Meaning |
|---|---|
| Deterministic | The same rule applied to the same state produces the same next state. |
| Pseudorandom number generator (PRNG) | Algorithm that yields numbers that may appear unpredictable to someone who lacks the initial conditions; that appearance does not imply cryptographic security. |
| Linear congruential generator (LCG) | PRNG that multiplies its current integer state, adds a fixed increment and keeps the remainder modulo a positive integer. |
| Seed; state | The seed is the starting value. The state is the changing value carried forward. In this class they already differ after the class definition's last assignment to `_x`. |
| Recurrence relation | A rule that defines the next value using the current one. |
| Multiplier; increment; modulus | Multiply by `a`, add `c`, then reduce modulo `m`. The editor uses `1103515245`, `12345`, `2**31`, respectively. |
| Quotient; remainder | With 9 divided into groups of 2, the integer quotient is 4 and the remainder is 1. The generator keeps the latter for its state. |
| Parity | Even or odd; testing an integer modulo 2 distinguishes them. |
| Prime number | Positive integer greater than one with exactly two distinct positive divisors. Its list of divisors is not the list of possible remainders when dividing by it. |
| Cycle; period | Repeating route through the states; its length in updates. A modulo counter can have a full period and yet be easy to predict. |
| Multiplicative vs. mixed LCG | Labels on the displayed article: increment zero versus nonzero. |
| Probability; statistics | Chance assigned to a possible event; ways to study patterns in observed outcomes. These help characterize randomness, not define it as lack of control. |
| Epoch-based time | Clock value counted from a reference instant; converting a positive seconds value to `int` drops its fractional part. |

### Names, calls, and storage

- **Module** — an importable unit; the first `time` in `time.time()` is the module, not the value returned by its function.
- **Class / instance** — `lcg.Lcg` is the class; separate calls to `lcg.Lcg()` create Jenny and Genny as individual instances.
- **Attribute / member selection** — a named part of an object; `.` identifies which object the name is being looked up on.
- **Calling versus referring** — `lcg.Lcg()` constructs; `lcg.Lcg` names something already there. A callable is something parentheses can invoke.
- **Class attribute / instance attribute** — `Lcg._x` belongs to the class; assignment to `self._x` in an instance call stores state for that object.
- **Non-public name** — `_x` says “implementation detail; callers should not rely on this directly,” not “Python will reject access.”
- **Volatile / nonvolatile memory** — storage that loses or retains its previous contents without power. Retaining a count does not imply independently advancing it.
- **Return versus print** — `return` gives a value to a caller; `print` writes a visible line. A bare method call can print internally while its returned value is discarded.
- **Bound method** — looking up this method through `jenny` supplies that instance when the method is called. Naming a standalone parameter `self` does not do this.
- **Identity / equality** — two simultaneously live objects have different identities even when their `_x` attributes hold equal integers.
- **Normalization / scaling** — divide state by the modulus to get a fraction; multiply by the width and add the minimum to move to a requested interval.
- **Namespace** — the set of names available in a particular scope. The module can have a class named `Lcg` without the driver having a bare name `Lcg`.

### Nodes, calls, and failures

| Pattern/term | Meaning in the example |
|---|---|
| Node / payload | The banana node is a distinct `Item`; `"banana"` is the value kept in its `object` attribute. |
| Predecessor / successor | Peach's predecessor is the banana node; banana's successor is the peach node. These refer to whole nodes, not strings. |
| Doubly linked list | Each adjacent pair has a reference in each direction; the forward and backward references are assigned separately here. |
| `fruit.next.next.prev = fruit.next` | From peach, remember the existing banana node; do not construct another banana. |
| `iter = iter.prev` | Rebind the traveling variable to the preceding node; this does not edit the list. |
| `None` / `NoneType` | `None` is the value returned by the absent next link; `NoneType` is its type. `print(None)` is legal. |
| `AttributeError` | The attribute being requested is not available; here the receiver was `None`, not a fourth fruit. |
| `Item` versus `Item( )` | A class reference versus a call to construct a distinct instance. |
| `__new__` / `__init__` | Creation first; initialization of the newly created instance afterward in this example. The latter does not return the fruit payload. |
| `self.object = obj` | Put the argument into that new instance's attribute; do not change the class attribute for all instances. |
| Default parameter / positional argument / keyword argument | `obj = None` allows omission; `Item( "banana" )` supplies the input by position; `Item( obj = "peach" )` supplies it by name. |
| Required argument / `TypeError` | With `def __init__(self, obj):`, `Item( )` fails before its method body can run. Explicit `None` would still satisfy argument binding. |

- **Alias / rebinding** — `iter = fruit` makes a second name for the same apple node. Later `iter = iter.next` points only that name elsewhere.
- **Head / traversal** — the head is the starting node kept reachable by `fruit`; traversal means visiting nodes by following actual links. The driver does this step by step, without a `for` loop.

### Tools and course vocabulary

- **Module search path / environment variable** — directories Python checks for an import, and a process setting that can add to them. Here `PYTHONPATH` helps a driver in `test` find a module in `src`.
- **Working tree / staging area / commit** — editable files, selected changes for the next commit, and a recorded revision. An open editor buffer need not match any of them.
- **Branch / detached HEAD** — a branch such as `master` names a line of development. Checking out a particular ancestor directly makes `HEAD` point at that commit rather than follow the branch tip.
- **Tracked / untracked** — Git already knows the tracked script; a separate untracked file can exist on disk without being part of the checked-out revision.
- **Transfer credit / equivalency** — whether credit is accepted and which requirement it satisfies are separate decisions by the receiving school.

## Sources

[1] https://docs.python.org/3/tutorial/classes.html

[2] https://docs.python.org/3/library/time.html

[3] https://peps.python.org/pep-0008

## Historical screen-code appendix

**The numbered gutter is not Python to type.** Each listing identifies its source frame or frames. Examples that appear unchanged in multiple views are listed once; a change to a call, a signature, or a diagnostic is a separate state. Use the Windows paths in the terminal blocks to identify the instructor’s working directory, not as paths required on your machine.

### Opening PowerShell scrollback, before the split editor (0:13:00)

**Observed on screen.** Source: `QzEXMGvlFf4_20261121_184202_one-minute/target-00780s.png`. The following consecutive commands and results are in the lower half of the foreground window. The first three count commits in **`Item`**; they are not Lcg results. The range **`HEAD..master`** asks about commits reachable from master but not HEAD. The displayed answer is 0 in that direction; this is not a checkout command.

```text
PS C:\CSCS1240F26\Item> git rev-list --count HEAD
22
PS C:\CSCS1240F26\Item> git rev-list --count master
22
PS C:\CSCS1240F26\Item> git rev-list --count HEAD..master
0
PS C:\CSCS1240F26\Item> cd ..\Lcg\
PS C:\CSCS1240F26\Lcg> ls
```

The listing below that command names three directories: **`apprentice`**, **`doc`**, **`src`**. Lower down, the final two visible prompt lines are:

```text
PS C:\CSCS1240F26\Lcg> .\src\lcg.py
PS C:\CSCS1240F26\Lcg> .\src\v
```

The first has a later prompt and no intervening output. The second has the caret just after **`v`** and is a draft, not an observed invocation of **`view.py`**. The Item/Fruit commit descriptions farther up the screen are older project history; the camera overlay covers some of them. Later split-editor frames leave only a narrow strip of this terminal: numbers such as 22 are not evidence that the newly displayed generator printed anything.

### The generator definition, right editor pane: `lcg.py` (0:14:00–0:34:00)

**Observed screen listing.** The following 31 numbered lines can be read together in the 0:14:00 frame `QzEXMGvlFf4_20261121_184202_one-minute/target-00840s.png` and were checked again in `target-02040s.png` (0:34:00) and the older original `20261111_212819_fresh/CSCS1240F26-14_target-033m.png` (0:33:00). These are editor gutter numbers, **not characters to type**. Spacing represents visible indentation columns; an image does not distinguish indentation bytes. No claim is made about text below the viewport, saves, or intervening edits between samples.

```text
 1 | # object/class call
 2 | import time
 3 |
 4 | class Lcg:
 5 |     _x = int(time.time())
 6 |     _multiplier = 1103515245
 7 |     _increment = 12345
 8 |     _modulus = 2**31
 9 |     _x  = (_multiplier * _x + _increment) % _modulus
10 |
11 |     def next(self=None, min=None, max=None):
12 |         if self == None:
13 |             print(f"class id: {id(Lcg)}")
14 |             Lcg._x  = (Lcg._multiplier * Lcg._x + \
15 |                     Lcg._increment) % Lcg._modulus
16 |             if min == None or max == None:
17 |                 return Lcg._x / Lcg._modulus
18 |             else:
19 |                 span = max - min
20 |                 minmax = Lcg._x / Lcg._modulus * span + min
21 |                 return minmax
22 |         else:
23 |             print(f"object id: {id(self)}")
24 |             self._x  = (self._multiplier * self._x + \
25 |                     self._increment) % self._modulus
26 |             if min == None or max == None:
27 |                 return self._x / self._modulus
28 |             else:
29 |                 span = max - min
30 |                 minmax = self._x / self._modulus * span + min
31 |                 return minmax
```

**How to read the groups.** Lines 5–9 first take whole seconds, then immediately replace that state using the multiplier, increment and modulus; these assignments happen while defining the class. Lines 11–21 define what a later call does without an instance: print the class identity, advance the class state, and return either a fraction or a rescaled value. Lines 22–31 perform the corresponding actions on the instance supplied as **`self`**. The identity labels are printed by the method; the numerical results leave the method by **`return`**. The screen only shows the definitions here, not a run.

### The driver beside it, left editor pane: `view.py` (0:14:00–0:34:00)

**Observed screen listing.** Both `target-00840s.png` and `target-02040s.png` show this left pane; the older 0:15:00 frame `20261111_212819_fresh/CSCS1240F26-14_target-015m.png` also shows these lines. It creates two distinct instances and keeps a third name for the class itself. It compares identities, then requests values in two groups, with an extra unprinted return for Jenny between the groups. The visible f-strings in lines 13–15 and 18–20 contain three leading spaces inside each string before its opening brace.

```text
 1 | import lcg
 2 |
 3 | jenny = lcg.Lcg()
 4 | genny = lcg.Lcg()
 5 | lenny = lcg.Lcg
 6 |
 7 | print(f"id jenny: {id(jenny)}")
 8 | print(f"id genny: {id(genny)}")
 9 | print(f"id lenny: {id(lenny)}")
10 | print(f"id lcg: {id(lcg)}")
11 | print(f"id lcg.Lcg: {id(lcg.Lcg)}")
12 |
13 | print(f"   {lenny.next()}")
14 | print(f"   {jenny.next()}")
15 | print(f"   {genny.next()}")
16 |
17 | jenny.next()
18 | print(f"   {lenny.next()}")
19 | print(f"   {jenny.next()}")
20 | print(f"   {genny.next()}")
21 |
```

**How to read the groups.** Lines 3–5 differ by two significant parentheses: Jenny and Genny each receive a newly created instance; Lenny receives a reference to the existing class. Lines 7–11 print identities, so the Lenny/class pair gives the same identity but the two newly created objects do not. In lines 13–15 and 18–20 the inner call runs before the outer formatted print. At line 17 the method still prints its own identity message and updates Jenny's state, even though no outer print displays the returned number. Those are consequences of the visible source, not a transcript of this terminal. No Lcg driver output was identified in the original frames before 0:35:00.

The unchanged generator and unlettered driver are also legible at 0:35:00 and 1:00:00. The 1:10:00 view shows blank lines 32–33 after the generator method. At 1:11:00 and 1:42:00 the driver still has no lettered output labels. Moving a selection or exposing two blank lines does not itself establish a new execution.

### PowerShell: the first run, the failed import, and the corrected run

At 1:21:00 (`target-04860s.png` and `CSCS1240F26-14_target-081m.png`), the project-root listing includes `apprentice`, `doc`, and `src`. Two command lines immediately above the working Python invocation are `.\src\lcg.py` and `& '.\src\view.py'`; there is no visible intervening output establishing what either accomplished. The following block *is* visible after the explicit Python launcher command:

```text
PS C:\CSCS1240F26\Lcg> py '.\src\view.py'
id jenny: 1206589228432
id genny: 1206589179536
id lenny: 1206583623248
id lcg: 1206588839616
id lcg.Lcg: 1206583623248
class id: 1206583623248
   0.3558370894752443
object id: 1206589228432
   0.9723668657243252
object id: 1206589179536
   0.9723668657243252
object id: 1206589228432
class id: 1206583623248
   0.9723668657243252
object id: 1206589228432
   0.8757509840652347
object id: 1206589179536
   0.05966655211523175
```

Separate failed-import state. The source-directory listing visible at 1:30:00 includes `__pycache__`, `lcg.py`, `view as.py`, and `view.py`. The traceback names line 1 of `view.py`; the spelling below comes from the terminal, not a reconstruction of the rest of the failing file (`target-05400s.png`):

```text
PS C:\CSCS1240F26\Lcg> py '.\src\view.py'
Traceback (most recent call last):
  File "C:\CSCS1240F26\Lcg\src\view.py", line 1, in <module>
    import lcg1
ModuleNotFoundError: No module named 'lcg1'
```

After repair, a separate successful invocation with new IDs and decimals is visible immediately below that traceback (1:30:00, enlarged by 1:33:00, `target-05580s.png`). It is this run, **not** the first, that the class reads line by line. The first five lines answer identity questions; subsequent unindented lines come from the method and indented decimals from the driver.

```text
PS C:\CSCS1240F26\Lcg> py '.\src\view.py'
id jenny: 3214240679312
id genny: 3214240630416
id lenny: 3214236774416
id lcg: 3214240290496
id lcg.Lcg: 3214236774416
class id: 3214236774416
   0.39060565223917365
object id: 3214240679312
   0.029102258384227753
object id: 3214240630416
   0.029102258384227753
object id: 3214240679312
class id: 3214236774416
   0.029102258384227753
object id: 3214240679312
   0.5764116561040282
object id: 3214240630416
   0.7909301412291825
```

### `view.py` — the lettered identity-and-state driver (1:45:00, 1:56:00, 2:00:00)

Historical editor text; numbers before `|` are the editor gutter, not Python to type. The driver shows both construction and aliasing before the first call. Line 17 matters as much as the six lettered lines: it advances Jenny even though the driver discards the result.

```text
 1 | import lcg
 2 |
 3 | jenny = lcg.Lcg()
 4 | genny = lcg.Lcg()
 5 | lenny = lcg.Lcg
 6 |
 7 | print(f"id jenny: {id(jenny)}")
 8 | print(f"id genny: {id(genny)}")
 9 | print(f"id lenny: {id(lenny)}")
10 | print(f"id lcg: {id(lcg)}")
11 | print(f"id lcg.Lcg: {id(lcg.Lcg)}")
12 |
13 | print(f"a   {lenny.next()}")
14 | print(f"b   {jenny.next()}")
15 | print(f"c   {genny.next()}")
16 |
17 | jenny.next()
18 | print(f"d   {lenny.next()}")
19 | print(f"e   {jenny.next()}")
20 | print(f"f   {genny.next()}")
21 |
```

Lines 7–11 print IDs for the two instances, the class alias, the module, then the class named through that module. Lines 13–20 call the generator in the order class, J, G, J, class, J, G. The **earlier** completed invocation visible at 1:45:00 is `py '.\src\view.py'`. Its a/b/c/d/e/f results are respectively `0.6867692675441504`, `0.5324593731202185`, `0.5324593731202185`, `0.5324593731202185`, `0.2810817933641374`, `0.5813100980594754`. The first number visible *after the invocation* is an ID line, not an `_x:` line. The later diagnostic run below is separate.

### `lcg.py` — the added diagnostic runs during class definition (1:45:00–1:58:00)

Earlier editor state, 1:45:00 and 1:48:00: the critical transition is visible across lines 8–13. There is no class-body print between the initial update and the method definition. Line 9's suffix is off the edge **in these earlier frames**.

```text
 8 |     _modulus = 2**31
 9 |     _x  = (_multiplier * _x + _increment) %[remainder not visible]
10 |
11 |     def next(self=None, min=None, max=None):
12 |         if self == None:
13 |             print(f"class id: {id(Lcg)}")
```

Later state — assembled visual reconstruction. The 1:58:00 frame establishes the header through the new line 10 and the beginning of the method; the 1:49:00 frame gives lines 4–34, including the continued assignments and the longest visible prefixes of the range expressions. The 1:51:00 frame corroborates the shifted line numbers. The overlaps support one post-diagnostic editor state, not a byte-for-byte claim about a saved file between stills. Two right-edge suffixes remain unavailable here.

```text
 1 | # object/class call
 2 | import time
 3 |
 4 | class Lcg:
 5 |     _x = int(time.time())
 6 |     _multiplier = 1103515245
 7 |     _increment = 12345
 8 |     _modulus = 2**31
 9 |     _x  = (_multiplier * _x + _increment) % _modulus
10 |     print(f"_x: {_x}")
11 |
12 |     def next(self=None, min=None, max=None):
13 |         if self == None:
14 |             print(f"class id: {id(Lcg)}")
15 |             Lcg._x  = (Lcg._multiplier * Lcg._x + \
16 |                     Lcg._increment) % Lcg._modulus
17 |             if min == None or max == None:
18 |                 return Lcg._x / Lcg._modulus
19 |             else:
20 |                 span = max - min
21 |                 minmax = Lcg._x / Lcg._modulus * span +[remainder not visible]
22 |                 return minmax
23 |         else:
24 |             print(f"object id: {id(self)}")
25 |             self._x  = (self._multiplier * self._x + \
26 |                     self._increment) % self._modulus
27 |             if min == None or max == None:
28 |                 return self._x / self._modulus
29 |             else:
30 |                 span = max - min
31 |                 minmax = self._x / self._modulus * span[remainder not visible]
32 |                 return minmax
33 |
34 |
```

Lines 5–10 calculate and display the initial class integer. Lines 12–22 select the class branch when no instance is supplied; lines 23–32 update a particular instance instead. For both branches, one missing bound selects the direct fraction return; the unexercised two-bound path computes a span. The explicit backslashes in lines 15 and 25 continue a statement onto the next physical line.

Observed later run at 1:49:00; the formatting here preserves output labels and sequence. ID values are specific to this invocation.

```text
_x: 1007915657
id jenny: 2021984535952
id genny: 2021984486736
id lenny: 2021985949376
id lcg: 2021984147136
id lcg.Lcg: 2021985949376
class id: 2021985949376
a   0.32767707761377096
object id: 2021984535952
b   0.5838502268306911
object id: 2021984486736
c   0.5838502268306911
object id: 2021984535952
class id: 2021985949376
d   0.5838502268306911
object id: 2021984535952
e   0.6765687786974013
object id: 2021984486736
f   0.10438141040503979
```

The consecutive `object id:` and `class id:` between c and d are not a missing line of numeric output. One belongs to the otherwise undisplayed `jenny.next()`; the other belongs to `lenny.next()` inside the print for d.

### `view as.py` — adding a directly imported class name (2:04:00)

Historical editor buffer. The spaces inside this driver's output labels are intentional: **there are no a–f letters here**. The tab shows a modification indicator. The traceback and completed output are terminal history, not proof that this exact buffer was saved or that the intervening successful run used this exact text.

```text
 1 | import lcg
 2 | from lcg import Lcg
 3 |
 4 | jenny = lcg.Lcg()
 5 | genny = lcg.Lcg()
 6 | lenny = lcg.Lcg
 7 |
 8 | print(f"id jenny: {id(jenny)}")
 9 | print(f"id genny: {id(genny)}")
10 | print(f"id lenny: {id(lenny)}")
11 | print(f"id lcg: {id(lcg)}")
12 | print(f"id lcg.Lcg: {id(lcg.Lcg)}")
13 |
14 | print(f"   {lenny.next()}")
15 | print(f"   {jenny.next()}")
16 | print(f"   {genny.next()}")
17 |
18 | jenny.next()
19 | print(f"   {lenny.next()}")
20 | print(f"   {jenny.next()}")
21 | print(f"   {genny.next()}")
22 |
23 | penny = Lcg
24 | print(f"   {penny.next()}")
```

Line 2 brings the bare class name into the driver; line 23 aliases it without parentheses. With line 2 omitted, the instructor reports the undefined-name error at line 23, not at the earlier module-qualified uses. At 2:03:00 the completed shell line `& '.\src\view as.py'` is followed by a fresh prompt with an in-progress Python command; do not read the suggested command text as proof of execution. At 2:04:00 the traceback is partially covered; below it a completed run starts `_x: 394753958`. Its seven displayed fractions, in order, are `0.9650485399179161`, `0.9644171688705683`, `0.9644171688705683`, `0.9644171688705683`, `0.47373678628355265`, `0.38841727236285806`, `0.38841727236285806`. The seventh follows a class-ID line, not an object-ID line.

### `src\item.py` and `test\fruit.py` — checkout `47413b4` (2:12:00–2:14:00)

The commit message describes direct assignments to the value and next attributes. The class has no constructor definition, append routine, or automatic back-link setup. The driver constructs each entry explicitly; labels below are editor gutter numbers seen together at 2:13:00.

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
 5 |
```

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit.object = "apple"
 5 | fruit.next = Item( )
 6 | fruit.next.object = "banana"
 7 | fruit.next.next = Item( )
 8 | fruit.next.next.object = "peach"
 9 |
10 | print( fruit.object )
11 | print( fruit.next.object )
12 | print( fruit.next.next.object )
13 |
14 |
```

Class lines 2–4 are defaults. Driver lines 3–8 create three entries and connect two forward links; lines 10–12 read at zero, one, and two links from the same starting reference. At 2:14:00 the confirmed terminal sequence is:

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
apple
banana
peach
```

### `script\path.ps1` — a displayed assignment, not a printed search path (2:18:00–2:19:00)

This is terminal text, not a numbered Python editor buffer. The assignment is the **file content** returned by `cat`. Neither invoking the script nor echoing its pathname printed that content. The terminal's modified-file status means this is working-copy evidence, not a verified copy of the script inside commit `47413b4`.

```text
PS C:\CSCS1240F26\Item> .\script\path.ps1
PS C:\CSCS1240F26\Item> & .\script\path.ps1
PS C:\CSCS1240F26\Item> echo .\script\path.ps1
.\script\path.ps1
PS C:\CSCS1240F26\Item> cat .\script\path.ps1
 $env:PYTHONPATH="$env:PYTHONPATH;C:\CSCS1240F26\Item\src"
```

### The traversal revisions at `f70c1ad`

The early four-line node class and direct-access driver are listed above at `47413b4`; they are visible again at 2:22:00, and the direct three-fruit run is visible at 2:25:00. The next checkout changes how the driver reaches the nodes.

### Second reference: `test/fruit.py` at `f70c1ad`

2:26:00, `target-08760s.png`. Lines 1–8 still build the direct links; line 10 gives the first node a second name. Lines 11–15 replace three different lookup expressions with one repeated data read and two moves. Returning to the prompt after checkout is not a run of this driver.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit.object = "apple"
 5 | fruit.next = Item( )
 6 | fruit.next.object = "banana"
 7 | fruit.next.next = Item( )
 8 | fruit.next.next.object = "peach"
 9 |
10 | iter = fruit
11 | print( iter.object )
12 | iter = iter.next
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
```

### First diagnostic: print the two nodes after walking

2:30:00, `target-09000s.png` and `CSCS1240F26-14_target-150m.png`. This is a live edit of the second-reference driver. The terminal overlaps part of the gutter; line positions through 15 agree with the visible prior listing, and the added print statements follow a blank line. After the three fruits, the two representations end in `FF8590>` and `FEC690>` respectively; the case and full values appear in the matching worked example.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit.object = "apple"
 5 | fruit.next = Item( )
 6 | fruit.next.object = "banana"
 7 | fruit.next.next = Item( )
 8 | fruit.next.next.object = "peach"
 9 |
10 | iter = fruit
11 | print( iter.object )
12 | iter = iter.next
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
16 |
17 | print(fruit)
18 | print(iter)
```

### Second diagnostic: compare before and after in one run

2:33:00, `target-09180s.png` and `CSCS1240F26-14_target-153m.png`; the complete terminal result is legible at 2:34:00 in `target-09240s.png`. Lines 11–13 are new: the first two show the same object and the third emits the blank separator. Lines 20–21 show different objects after the two moves. This run has its own address values; the older launch is still above it in scrollback.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit.object = "apple"
 5 | fruit.next = Item( )
 6 | fruit.next.object = "banana"
 7 | fruit.next.next = Item( )
 8 | fruit.next.next.object = "peach"
 9 |
10 | iter = fruit
11 | print(fruit)
12 | print(iter)
13 | print()
14 | print( iter.object )
15 | iter = iter.next
16 | print( iter.object )
17 | iter = iter.next
18 | print( iter.object )
19 |
20 | print(fruit)
21 | print(iter)
```

### Third diagnostic: name the last node from the first

2:39:00, `target-09540s.png` and `CSCS1240F26-14_target-159m.png`. At 2:38:00 (`target-09480s.png`), the intermediate line 22 instead reads `print()`; no execution of that intermediate version is established. In this completed version line 22 reads `print(fruit.next.next)` and the last two object representations match exactly in the same launch.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit.object = "apple"
 5 | fruit.next = Item( )
 6 | fruit.next.object = "banana"
 7 | fruit.next.next = Item( )
 8 | fruit.next.next.object = "peach"
 9 |
10 | iter = fruit
11 | print(fruit)
12 | print(iter)
13 | print()
14 | print( iter.object )
15 | iter = iter.next
16 | print( iter.object )
17 | iter = iter.next
18 | print( iter.object )
19 |
20 | print(fruit)
21 | print(iter)
22 | print(fruit.next.next)
```

### 2:45:00–2:48:00 | Backward links in `test/fruit.py`, not a new class method

Observed at `target-09900s.png` (2:45:00); the full driver is corroborated at `CSCS1240F26-14_target-168m.png` and `target-10080s.png` (2:48:00). At checkout `0203781` the class provides default attributes. The driver now wires both directions and keeps the first node reachable through `fruit` while `iter` moves.

`item.py` at 2:45:00:

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
 5 |
```

`test/fruit.py` at 2:45:00:

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit.object = "apple"
 5 | fruit.next = Item( )
 6 | fruit.next.prev = fruit
 7 | fruit.next.object = "banana"
 8 | fruit.next.next = Item( )
 9 | fruit.next.next.prev = fruit.next
10 | fruit.next.next.object = "peach"
11 |
12 | iter = fruit
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
16 | iter = iter.next
17 | print( iter.object )
18 |
19 | iter = iter.prev
20 | print( iter.object )
21 | iter = iter.prev
22 | print( iter.object )
23 |
```

Lines 3–10 create the boxes, write the links, and store the three names. Lines 12–17 display each value on the outward trip; lines 19–22 display the two predecessors on the return trip. These are explicit assignments, not a loop or a Python iterator protocol implementation.

**Observed terminal, 2:48:00.** The earlier `git checkout master~19` was refused because of local changes in `test/fruit.py`. `git restore .\test\fruit.py` and a subsequent `git checkout master~19` are visible. The successful checkout reports `Previous HEAD position was f70c1ad Fruit: use of second reference to fruit.` and `HEAD is now at 0203781 Fruit: use of prev attribute.` It also lists `M` beside `script/path.ps1`; the restore targeted the driver, not that other file. The object-address strings *above* these commands are earlier scrollback. The post-checkout run is:

```text
PS C:\CSCS1240F26\Item> py .\test\fruit.py
apple
banana
peach
banana
apple
```

### 2:50:00–2:52:00 | Two distinct overrun revisions

**First edit, observed** at `target-10200s.png` (2:50:00) and `target-10260s.png` (2:51:00). Driver lines 1–17 match the preceding listing. Replace lines 18 through the end with the following tail. Line 18 succeeds; the failure is on 19, before the return trip begins.

```text
18 | iter = iter.next
19 | print( iter.object )
20 |
21 | iter = iter.prev
22 | print( iter.object )
23 | iter = iter.prev
24 | print( iter.object )
25 |
```

**Second edit, observed** in `target-10320s.png` (2:52:00). Lines 1–17 still match the first driver; this is a *different* tail. Line 19 now prints the current value itself. The old failing access is still active, one line lower.

```text
18 | iter = iter.next
19 | print(iter)
20 | print( iter.object )
21 |
22 | iter = iter.prev
23 | print( iter.object )
24 | iter = iter.prev
25 | print( iter.object )
26 |
```

**Observed terminal, same 2:52:00 target.** The visible invocations are both `py .\test\fruit.py`. The earlier one prints apple, banana, peach and then shows `line 19, in <module>`. The later one prints apple, banana, peach, **then `None`**, and shows `line 20, in <module>`. Both identify `print( iter.object )` and end with `AttributeError: 'NoneType' object has no attribute 'object'`. The edit changes what one can see before the exception, not whether the walk can continue.

### 2:53:00–2:59:00 | Revision `2499ca0`: optional constructor value; a separate bare-class edit

`item.py` observed at `target-10380s.png` (2:53:00). It keeps the three class-level defaults and adds the initializer on lines 6–7. There is no link-setting code inside that method. The terminal at this point still contains the earlier overrun, then a restore and a checkout that moves from `0203781` to `2499ca0`; the instructor calls this going to “18.” Part of the new commit message and checkout command is behind the editor, so they are not reconstructed as verbatim terminal lines.

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
 5 |
 6 |     def __init__(self, obj = None):
 7 |         self.object = obj
 8 |
```

**Transient editor variant:** at `target-10440s.png` and `CSCS1240F26-14_target-174m.png` (2:54:00) the modified driver tab shows the first line below. At `target-10500s.png` (2:55:00) the call is back. The two lines are **alternative observations**; no run of the bare-name state is established.

```text
 3 | fruit = Item
```

```text
 3 | fruit = Item( )
```

**Full optional-value driver, observed** at `target-10620s.png` (2:57:00), with the webcam now clear of its upper lines. This listing is from that one frame, not a splice across the bare-name edit. Lines 3–4 deliberately preserve the old two-step pattern. Lines 5 and 8 create and initialize their respective nodes in one call; lines 7 and 10 are comments in the original. The remainder is the two-way walk, not the overrun diagnostic.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | fruit.object = "apple"
 5 | fruit.next = Item( "banana" )
 6 | fruit.next.prev = fruit
 7 | #fruit.next.object = "banana"
 8 | fruit.next.next = Item( obj = "peach" )
 9 | fruit.next.next.prev = fruit.next
10 | #fruit.next.next.object = "peach"
11 |
12 | iter = fruit
13 | print( iter.object )
14 | iter = iter.next
15 | print( iter.object )
16 | iter = iter.next
17 | print( iter.object )
18 |
19 | iter = iter.prev
20 | print( iter.object )
21 | iter = iter.prev
22 | print( iter.object )
23 |
```

**Observed result:** the 2:58:00 terminal (`target-10680s.png`) shows a *new* `py .\test\fruit.py` after checkout, then `apple`, `banana`, `peach`, `banana`, `apple` on separate lines and the PowerShell prompt.

### 3:00:00–3:04:00 | Required-value initializer; unchanged driver now fails

Observed signature in `target-10800s.png` (3:00:00), with clear punctuation in `target-10860s.png` (3:01:00). Lines 1–5 are unchanged relative to the optional class; lines 6–8 now read as follows. The visible driver still has `fruit = Item( )` on line 3. Removing the default does not remove the parameter: Python must receive a value before it can enter line 7.

```text
 6 |     def __init__(self, obj):
 7 |         self.object = obj
 8 |
```

**Observed terminal:** a new `py .\test\fruit.py` is followed immediately by the traceback showing `fruit = Item( )`. The visible start of its diagnostic reads `TypeError: Item.__init__() missing 1 required[remainder not visible]`; a closing quote wraps onto the next line. The five fruit names above this invocation belong to the preceding successful run. The independently reproduced full message is in the teaching section, not a transcription of the hidden screen text. The unchanged state at 3:04:00 is not evidence of an additional run; the 3:04:20 closing target brings the recording controls to the front.

## Micro-Learning Insights

This appendix is detachable. Each cue is a question or action you can use on a new example.

- **Ask “as what?” (0:01:00).** “We accept this course” leaves out the most important word: as an elective, or as the required course?
- **Read the character before the phrase (0:14:00).** `# object/class call` does nothing. The interpreter sees the comment marker before a human sees the word *call*.
- **Name the zero point (0:23:00).** “28 minutes” becomes a time of day only when you add “since the 9 a.m. start.” Use the same question with a clock seed.
- **Try the smallest moduli (0:50:00).** Modulo 1 always leaves zero. Modulo 2 reveals parity. Modulo 0 raises. Those cases distinguish remainder from division immediately.
- **Count transitions, not labels (0:36:00).** In the Wikipedia *Linear congruential generator* diagram, `3 → 6 → 3` takes two steps, not three. A long cycle by itself does not establish unpredictability.
- **Use a negative lower endpoint (1:18:00).** With −10 to 30, multiplying by 40 gives the width but cannot produce the lower endpoint without the final shift. A zero minimum hides the omission.
- **Name the writer (1:33:00).** `class id:` comes from inside the method. The indented decimal comes from outside it. Seeing the first without the second can be exactly right.
- **Give “before” a line number (1:41:00).** “Genny at line 15” identifies both the object and the moment. It is more useful for testing a prediction than “Jenny’s earlier number.”
- **Read the right side of an assignment first (1:49:00).** The diagnostic prints `_x` *after* the update. It is not the unmodified clock reading.
- **Inspect rather than invoke (2:18:00).** `cat` showed the script’s actual assignment; `echo` showed its pathname. Neither observation alone reports the current value of the environment variable.
- **Draw boxes for objects, arrows for references (2:26:00–2:48:00).** Draw `fruit` and `iter` pointing to apple. Move only the `iter` arrow. Later, draw the reverse arrows in the nodes; moving the traveler never creates those links.
- **Read the commit message’s verb (2:45:00).** “Use of prev attribute” points to a driver change: the field already existed, but until the driver assigned it, there was no route back.
- **Predict the failing lookup (2:50:00).** The extra move to `None` succeeds. It is the subsequent `.object` on `None` that fails. A diagnostic between the two operations makes the distinction visible.
- **Separate omission from a supplied value (3:00:00).** Removing `= None` means the caller must pass *something*. It does not, by itself, forbid passing `None`.
