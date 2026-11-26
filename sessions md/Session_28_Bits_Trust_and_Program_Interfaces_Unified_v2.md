# What do these bits mean?

CSCS1240F26 · Session 28 · Worked teaching companion

A student asks what actually passes between two people when one pays the other digitally. Sending a copy of the bits for 200 does not erase the sender's copy. Nor does seeing those bits tell the recipient who sent them, whether they were entitled to spend anything, or even whether 200 is a price. Those are separate questions about **representation**, **integrity**, and **trust**. Keep them separate and the later discussions of GPS units, file types, and programs exchanging data become easier to follow.

**Key items to scan**

- **Representation** — the rule that makes a bit pattern mean an amount, a grade, or something else.
- **Integrity** — whether a record or history still matches what was recorded or independently expected.
- **Trust** — why a person or program should accept a record or a particular version of history.

**How to use this companion.** Read the discussions in class order. The timestamps are elapsed recording times. The worked checks give independently tested examples for ideas that were discussed but not run on screen; the screen record explains exactly what the pictures establish.

**Recording and coverage.** [CSCS1240F26 28, JoesTA](https://www.youtube.com/watch?v=kkYVXBrP4c8). The automatic English transcript contains 3,053 cues, starting at 0:00:00.08; its last cue ends at 1:53:48.48, slightly beyond the video duration of 1:53:47.15. The 123 full-screen samples include every whole minute from 0:00:00 through 1:53:00, eight additional discussion targets, and 1:53:46. A screen sample is not continuous footage. No classroom code listing, shell invocation, or program output was recoverable from these samples. Speaker names and caption spellings are not assumed where unclear.

## 1 · What problem are you preparing to solve? 0:00:00–0:12:12

### An adviser, a program, and a mismatch

The instructor put an informal conversation on the record because it could help students who were not in the room. Spring registration had opened. A student choosing classes could use the registration fair, register online, or consult their assigned adviser. But the person on the roster might not be the best match for the student's current goal. A computing instructor's advising roster might be full; a student who started undecided might have changed programs without a corresponding adviser change. The useful step was to check the actual assignment, not assume that a change of major had updated everything.

The instructor suggested contacting the enrollment advising center in the commons or by email to request a particular adviser, and offered informal advice regardless of the assignment. For a student whose interests cross two fields, ask whether directed studies could fit *before* assuming transfer is the only choice. These were options discussed that semester, not guarantees of approval or a statement of current registration policy.

**Key items to scan**

- Assigned adviser — check whether this person still matches the program and goal you have now.
- Directed studies — a possible way to combine interests, subject to the program's rules and advising review.

### Security today, security tomorrow

Does cybersecurity belong with computer science or with IT? The instructor initially answered “computer science,” then revised the answer after listening to a student. Think of the person who keeps a service running today: they patch systems, replace failed components, monitor for problems, and respond to incidents. Now think of someone asking whether tomorrow's systems could protect information differently. Both are doing security work. “Today” versus “tomorrow” distinguishes the *problem each person is tackling*, not two mutually exclusive professions.

A student then asked what happens if encryption stops working. The instructor turned the question around: is a particular protection becoming **ineffective**, or has keeping information secret become **unnecessary**? Those are not the same future. A fingerprint, suggested in the discussion, can help answer *who is this?* It does not by itself stop someone reading a message in transit. The class raised future computing power, AI, and quantum computing as possibilities, not demonstrations of a machine that can defeat every protection. The instructor's historical cat-and-mouse analogy explained why defenders revise designs; it did not establish a date when encryption will become obsolete.

**Key items to scan**

- **Ineffective** — the need remains, but the chosen protection no longer meets it.
- **Unnecessary** — a stronger claim: the problem itself no longer needs that protection.
- Authentication versus confidentiality — recognizing a person is not the same as concealing their message.

The hypothetical future led to cryptocurrency. Was each coin a strong cipher? A peer mentioned transactions, blocks, and verification. Rather than settle for a definition containing equally unfamiliar words, the instructor set that question aside long enough to explain *how* the class might reason its way to an answer.

## 2 · Start with the sprint, then generalize 0:12:13–0:20:01

A student had finished a sprint in a parking lot and wanted to know how far they had run. Imagine a wrist device: press at the start, record a GPS position; press at the finish, record another. Now the distance formula has a job to do. The student wasn't asking to memorize another formula. They wanted to connect a physical question to inputs a machine could store and an answer it could compute. No coordinates, measured distance, or device run were supplied.

| Stage in the proposed design | What it contributes | What it cannot establish alone |
|---|---|---|
| Start press | First position; defines one endpoint. | The route the runner will follow. |
| Finish press | Second position; gives a pair to compare. | Whether the runner went straight, curved, or doubled back. |
| Calculate | In a common flat coordinate system, square the change along each axis, add those squares, and take the square root. | Raw latitude/longitude differences are not distances in meters; real GPS positions need a suitable geographic calculation or projection. |

A counterexample makes the measurement issue clear: finish where you started and endpoint separation is zero, even if the sprint was a lap. If the demanded output is *distance traveled*, the design may need intermediate measurements. That is a guide clarification, not a device the class built.

The instructor used the student's idea to argue for numerical methods before, or in closer contact with, abstract calculus. A computer works with finite representations; measured positions can be noisy and real data seldom fit the neat functions of a textbook exercise. The suggestion was to start with what the computer can do to particular numbers, ask where that approach fails, and then generalize. Calculus and clean worked examples still have a purpose. A clean case is a reference against which you can diagnose a messy one. It was a curricular proposal, not an announcement that a degree requirement had changed.

**Key items to scan**

- Endpoint separation — answers how far apart two positions are; it does not necessarily measure the path.
- Numerical analysis — the concrete question is how to get a useful answer from the numbers a machine can represent, including their limitations.
- Computer as tool — a person can reason about the problem unaided; simulation, calculation, and visualization make ideas easier to test.

### How many behaviors can a black box have?

Recall the earlier one-input box. Feed in a zero or a one. If you can predict what comes out for both inputs, you know its entire behavior under the class's assumptions. Adding an input wire doubles the number of input patterns. The class had found four different one-input boxes and sixteen two-input boxes. For three inputs the instructor gave only the hint “powers of two.” The following worked check derives the answer rather than crediting it to the lecture.

### How many boxes? • save as `boxes.py`

Annotated teaching version — guide-added code and comments; not a classroom run.

```python
for inputs in (1, 2, 3):          # Try one, two, then three binary input wires.
    rows = 2 ** inputs            # Each additional wire doubles the input patterns.
    boxes = 2 ** rows             # Choose a 0 or a 1 independently for each row.
    print(inputs, rows, boxes)    # Display wires, rows per table, different functions.
```

`python boxes.py` — independently verified output:

```text
1 2 4
2 4 16
3 8 256
```

With one input, fix the row order as 0, then 1. The four possible output columns are `00`, `01`, `10`, and `11`: always off, copy the input, invert it, always on. With two inputs, one particular box has **four rows**, not sixteen; sixteen counts the different boxes you could build. This assumes a deterministic, memoryless box with one binary output. It is a counting argument, not a practical instruction to enumerate a million-input device.

**Key items to scan**

- **`rows`** — counts the distinct input patterns one box must handle.
- **`boxes`** — counts the possible complete output columns for those same rows.

In general, for *n* binary inputs, there are 2ⁿ input rows. There are two choices for the output of *each* row, so the number of complete Boolean functions is 2 raised to the power of 2ⁿ. The instructor's million-input thought experiment highlights a distinction: a rule can describe a space too large to list, even by machine. Neural networks were mentioned as a possible connection; no network was constructed.

## 3 · How should data be arranged for the work? 0:20:01–0:30:21

The student had watched singly linked, doubly linked, and circular lists being explained. The class's fruit example and a mention of heaps had raised a better question than “Can I implement one?”: *given my own problem, which operations should be easy?* A forward link helps when you need the next item; a backward link matters if you must move the other way; a circular connection changes what signals the end of a traversal. These contrasts explain the student's question. No heap-versus-list benchmark or new fruit implementation was demonstrated.

The instructor placed a data structures course after basic programming: once you can sequence statements, select with an **`if`**, and repeat with a loop, you can apply those operations to organized collections. Discrete mathematics supplies another way to describe collections and relationships; the class already knew Python sets, lists, and tuples. They are not interchangeable just because they all collect things. The aim is to choose a representation for the work, not to memorize an inventory of containers.

**Key items to scan**

- **`if`**, sequence, loop — ways to decide, order, and repeat the work done to stored data.
- Data structure versus algorithm — how the items are arranged versus what steps you perform on them.
- Clean versus messy problem — use the clean case to learn what should happen, then identify which assumptions fail on your own data. The instructor's imaginary bridge would not behave like every tidy physics exercise.

The instructor described a common curriculum they had encountered: several calculus courses, linear algebra and differential equations, with numerical methods later. A weekend search had not turned up their preferred alternative; that was a report of a search, not proof no such program exists. A student's memory of mostly learning typing in school brought up ACM's work on K–12 computing education. The instructor did not verify a publication date or adoption rate. The larger question was *what is education for?* Factory work joining the same two widgets on time illustrated training for a known job. Farming, taxes, and life outside work illustrated problems whose inputs aren't conveniently specified. The class's analogy to refactoring was about reorganizing education around those needs, not discarding everything previously learned.

**Key items to scan**

- ACM — Association for Computing Machinery; cited in discussion of school computing education, not as proof of a local requirement.
- Widget analogy — meeting a repeatable production target is useful but does not teach every form of judgment.

## 4 · Build the word before building the ledger 0:30:22–0:35:59

When the class returned to blockchain, the instructor asked for everyday uses of *block* and *chain*. A city block, a building block, and blocking an opponent do not have one technical meaning. Which sense helps with data? A student offered “some fundamental piece of something.” A bracelet connects links; a chain to tow a truck connects links for a different purpose. The material is not what transfers. The useful idea is that separate pieces stand in a relationship.

At first, students suggested whole computers or the currencies themselves as the pieces. The instructor moved down a level: here the piece is a **group of bits**. A byte is one possible group; the term **block** does not promise every group is one byte or has any particular length. Link successive groups, and you can represent what was recorded at different times. This is a starting model. It does not yet explain who may add a record or why anyone should believe its history.

**Key items to scan**

- **Group of bits** — the data-level unit in the analogy, not a physical computer or a coin.
- **Block** — a group treated as one unit; **chain** — relationships among units, not a guarantee that a record is true.

## 5 · “I thought I gave you the $200 itself” 0:36:00–0:49:35

A receipt saying someone gave you $200 is paper. Stealing the receipt does not put $200 in the thief's hand. But what makes the cash itself acceptable? A recipient checks physical features and relies on others to accept the notes. Cash can be stolen or counterfeited; paper was never a perfectly secure baseline. Digital currency changes the engineering problem. It does not invent the need for trust or guarantee anonymity.

The student's objection is worth following in order. The instructor first sent a group of bits that meant **100 in decimal**. Making a copy left two identical encodings of 100. Then the instructor explicitly switched to **200** for a different experiment: the recipient changes a receipt to 300 and claims the other person still owes 100. “I thought that I gave you the $200 itself,” the student objects. Both people are reasoning sensibly, but about different objects: one about a payment, the other about a record that purports to describe it.

| Classroom move | What changes | What does not follow |
|---|---|---|
| Copy the bits for 100. | Another copy exists. | The sender has not thereby transferred ownership or created a second valid spend. |
| Write 300 on a receipt for 200. | The recipient's local story now differs by 100. | The payer does not acquire a new debt just because that record was edited. |
| Record “you give me $200 today; I give you $50 tomorrow.” | A series of records can retain amounts, direction, parties, and time. | It is not the same thing as overwriting the first amount with the second. |
| Later claim a previous block said 500 rather than 200. | A competing version of the past is proposed. | The rest of the participants need not accept that history. |

The detour through gold, paper dollars, and Fort Knox asked what a holder trusts if not everyone demands redemption at once. The instructor provisionally entertained a question about inflation, then left the economics unresolved. Do not infer that fiat dollars are promises of gold or that inflation is simply the ratio of banknotes to gold reserves. For the ledger question the useful point is simpler: a representation has value in part because of the institutions, rules, and people that accept it.

**Key items to scan**

- **100 in decimal** — a copying example, not the initial balance of the later transaction.
- **200** — original example payment; 300 and 500 are different proposed alterations, not successive legitimate balances.
- Transaction record — might identify parties, time, and amount; comparing names on a record alone does not authenticate either party.

### What if the check value matches?

The student wanted to know what prevented them from changing bits on their own machine. Nothing in the idea of a bit prevents a local edit. The stronger question is whether that edit will be *accepted elsewhere*. Someone suggested adding a checksum. Like checking paper, ink, and the thread embedded in a note, this gives the recipient something to test. It helps if the data change while the expected value stays put. But if both the data and the check value come from the same untrusted copy, agreement may show only that the pair is internally consistent.

**Key items to scan**

- Checksum — recompute a value from the data and compare; ask where the expected value came from.
- Local edit versus accepted spend — changing your copy is easy; it does not decide what counts as the shared record.

## 6 · Linked history is not the same as trusted history 0:49:35–0:58:02

A learner supplied a better analogy from familiar work: use **`git log`** to look back through commits, or check out an earlier state. A commit ID looks arbitrary, but Git computes it from the commit object; a commit also records its parent or parents when it has them. If an earlier commit is replaced and later commits are rebuilt on that different parent, the new history has different IDs. Compare two supposed copies at the same point and the discrepancy becomes visible. Merely creating a new commit legitimately also changes the tip. A difference is a reason to investigate, not a verdict about who deceived whom. No repository was cloned or edited on screen in this lesson. [Supplemental: Git's snapshots and integrity checks](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F).

The instructor proposed an independent person checking the whole chain. Agreement gives more confidence than consulting only the file you already doubt. A student immediately found the next question: *who trusts the third party?* A blockchain system adds rules about valid records and accepted histories. In a distributed system, peers need a way to agree. Hash links help expose changes; by themselves they do not confer authority, hide contents, or make an external claim true. The instructor said “be as sure as we can,” not “impossible to alter.” Smart contracts and proof of work were named, not implemented or required of every chain. See [NIST's Blockchain Technology Overview](https://csrc.nist.gov/pubs/ir/8202/final) for the distinction between a tamper-evident ledger and its consensus mechanism.

**Key items to scan**

- **`git log`** — familiar history-viewing command a student suggested; no classroom invocation or output was captured.
- Commit ID — computed identifier; a different valid commit is a different object, not the same commit with an arbitrary new name.
- Independent comparison — can uncover disagreement; deciding which history to accept requires more than a matching local check value.

### Could I call a grade record Ethereum?

The next question exposed a different uncertainty: where in a group of bits does it say which coin it is? The instructor answered on two levels. The physical pattern has bits in a particular order. The reader applies a format that says what fields in that order mean. A student compared it to a word spelled identically in different languages. A type or format field can help choose the reader, but writing “Bitcoin” on a grade record cannot make it a valid Bitcoin transaction.

The teacher's example was a transcript: **A, B, A+, B minus**. Read those bits under some other scheme and the would-be amount field might instead be an F in the grade scheme. No actual encoding or conversion between grades and money was specified. When the student imagined giving someone a fake coin, the instructor asked “Will they accept it?” and “Did you win or lose?” You cannot infer a gain from a different label when neither the value nor acceptance has been established. The student said they still didn't see the connection. A useful way back in: a file reader can accept a sequence of bytes as text even though an image decoder rejects the very same bytes. Neither reader changes a single byte. The utility discussed later applies that idea to real files.

**Key items to scan**

- **A, B, A+, B minus** — the same-bit-pattern thought experiment, not a grade-to-currency exchange rate.
- Format — determines how to interpret fields; validation — determines whether the resulting record satisfies the system's rules.

## 7 · Choose an interface before choosing a language 0:58:03–1:16:00

The student had more interesting problems than time and returned to the sprint. Could a microcontroller they already owned and a purchased GPS module become a prototype? Numerical analysis is part of that project, the instructor said, but the larger skill is breaking a need into parts and deciding how to represent each part. The video shared in Discord prompted the instructor to distinguish computer science from coding. Code expresses an idea so a machine can carry it out; the idea about what is worth measuring and which inputs would suffice comes first. The instructor had seen the beginning of the linked video, not necessarily all of it.

Treat the GPS unit temporarily as a **black box**. The student need not first learn how satellite positions are calculated. They *do* need to know what inputs the unit accepts, what outputs it produces, and how the controller should interpret the bits it receives. Outputs of one component are inputs to the other. Start with one successful exchange, then look for what transfers to another sensor. The instructor's informal word “triangulation” wasn't a position calculation performed in class; GPS positioning uses estimated ranges, not simply angles. No device protocol or connector was identified.

**Key items to scan**

- **Black box** — leave the internal mechanism aside for a stated purpose; do not leave the interface unspecified.
- Sprint prototype — personal motivation for choosing and testing an interface, not a completed device or a graded assignment.
- Failed experiment — can reveal a wrong assumption; a project needn't already promise a marketable product for investigating it to teach something.

### What transfers from Python to C?

“Which is the best language?” drew a practical answer: the one you know well. That is a starting point, not a rule to ignore a device's runtime or timing limits. For the imperative styles the class knew in C, C++, Java, Python, and Lua, trace the same operations: evaluate an expression to get a value; ordinarily execute statements in sequence; select a block with **`if`**; return to a block in a loop if the condition calls for another pass. The spelling varies; the decisions about data and control still have to be made. A block of statements here is *not* a blockchain block.

The instructor had once dismissed Python, then discovered which tedious work it could save. A student replied that doing something the tedious way can still buy *understanding*. Learning C may pay off when reading an existing C program or working close to microcontroller hardware. By contrast, keeping sprint progress in flat files or a database need not be hand-built at that level. It is possible to split the project: choose a suitable tool for moving bits to and from a device, then another for storing and comparing results. The lesson did not settle on a device, storage schema, or programming language for the finished product.

**Key items to scan**

- **`if`** — select which statements execute; a loop repeats a sequence instead of selecting it only once.
- Embedded work — low-level detail may be the lesson or a hardware requirement. For bookkeeping, the same detail may just cost time.
- Language choice — separate “can express this algorithm” from “fits this device, existing code, and programmer.”

C++ supplied a historical example. Its name plays on the C increment operator **`++`**. Classes and methods let a programmer express relationships that could be built more manually in C with structures and function pointers. The instructor found constructing such machinery instructive, not something they wanted to repeat every time. One correction matters if you try switching compilers: C and C++ share much syntax, but C++ is **not** a strict superset of C. Valid C source is not guaranteed to be valid C++ source. The session did not show either compiler running.

**Key items to scan**

- **`++`** — increment in C; wordplay in the name C++, not a claim that C++ accepts every C program.
- Class and method — explicit language support can replace repeated manual setup without changing the underlying problem you are solving.

## 8 · Show the destination, then choose what to open up 1:16:00–1:22:59

A learner who took notes as comments while following videos wanted a problem that would give the notes a purpose. Robotics offered another version of the sprint question. Where should the end of a robot arm be, and which way should it face? More than one joint can contribute to the same motion. Its available independent movements are its **degrees of freedom**. Even a sequence of joint positions that reaches the right point might hit an obstacle on the way. Seeing how fluidly a newer arm moves makes the numerical work less obvious, not less necessary. The class mentioned matrices as a way to work with these quantities; it did not supply a matrix or compute a trajectory.

The instructor normally starts from the bottom. In another course, students worked from AND, OR, and NOT gates toward a virtual CPU. A student's request to see where it was all going changed the teaching: show the previous semester's finished machine, then inspect individual pieces. The instructor worried that someone shown the view from 20,000 feet might never go down to ten. The converse risk is spending weeks on gates without knowing why they are being assembled. The same tension applies when learning Python: building your own structures exposes mechanisms; importing a library can show what is possible and give you a reason to investigate its internals. A temporary course restriction on imports is not a restriction of the language.

**Key items to scan**

- **Degrees of freedom** — independent ways a mechanism can move; the arm's position, orientation, and path matter to the task.
- AND, OR, NOT → virtual CPU — the destination that gave the bottom-up work a purpose; no CPU was built in this session.
- Representation choice — before complicating an operation, ask whether organizing the data differently would simplify it.

## 9 · How does Unix know what kind of file this is? 1:23:01–1:28:54

In the Unix/Linux puzzle-box exercise, the student might have compressed data, an image, or something else. The instructor asked not just what **`file`** says but *how it could know*. Look for recognizable evidence in the bytes. A format may specify particular leading bytes, often called a **magic number** or signature. A header may carry further information needed to interpret the body. The real utility uses several kinds of tests; it doesn't simply read a filename extension, and not every format has a decisive signature. See the [`file(1)` manual, Description](https://man7.org/linux/man-pages/man1/file.1.html). No filename, options, or output were demonstrated in class.

### A signature without a filename clue • save as `signature.py`

Annotated teaching version — guide-added code and comments; not a classroom run.

```python
from pathlib import Path                           # Work with a path, not a guess from its suffix.
from tempfile import TemporaryDirectory            # Keep this check away from existing files.
with TemporaryDirectory() as folder:               # Create a disposable workspace for this example.
    item = Path(folder) / 'puzzle.dat'              # The suffix does not say PNG.
    item.write_bytes(bytes.fromhex('89504e470d0a1a0a'))  # Write only the PNG signature.
    header = item.read_bytes()[:8]                 # Read these eight bytes, with no decoding as text.
    print(header.hex())                            # Display the bytes in hexadecimal.
```

`python signature.py` — independently verified output:

```text
89504e470d0a1a0a
```

That is the PNG signature, **not a working image**. The exercise deliberately omits the image chunks: a matching prefix is a useful clue, not proof that every remaining byte is valid. The instructor proposed investigating an existing utility; this tiny check is not its implementation. Supplemental reference: [PNG specification, file signature](https://www.w3.org/TR/png-3/#5PNG-file-signature).

**Key items to scan**

- **`file`** and **magic number** — the utility looks for format clues such as signatures; this snippet examines just one clue.
- **`puzzle.dat`** — a deliberately uninformative name; examine contents rather than relying on the extension.
- **`[:8]`** — take the first eight bytes, not eight text characters decoded from the file.


### What language is the tool itself written in?

Wanting to read the implementation of `file` led to the distinction between a *language* and a *program that implements that language*. A learner can write Python while CPython does much of the interpreter's work in C. Jython is an implementation for the Java platform. The instructor was referring to **CPython**, not Cython. Which language implements an interpreter is separate from how that interpreter represents and executes the user's program.

Would a Python version of `file` be shorter? The instructor expected high-level facilities to save work but did not measure a line count. Python's slicing can select a range of bytes with one expression. A **`for`** loop can request one item after another because an iterable supplies an iterator and that iterator knows how to produce the next item. Those behaviors did not come for free; a library or type implements them. Recall the earlier custom-class work: **`__iter__`** supplies an iterator; **`__next__`** on the iterator supplies successive items or signals exhaustion. The benefit is being able to reuse this interface rather than rebuilding the mechanism in every caller.

The instructor suggested pairing an explanation of the “anatomy” of `file` with its actual source and sending back a useful link. You can look for branches and comparisons you recognize from Python before you know every piece of C syntax. Another candidate is **`ls`**: to list directory entries and distinguish files from directories it consults filesystem information. Listing entries is not the same as reading every file's contents, and `ls` is not a Python built-in. Neither tool was run on screen.

**Key items to scan**

- **CPython** — an implementation of Python; Jython demonstrates that implementation language and user language need not be the same.
- **`for`**, **`__iter__`**, **`__next__`** — the loop requests values through a protocol, rather than having to know each collection's storage layout.
- **`ls`** — second proposed source-reading exercise: ask what directory information the program needs.

## 10 · Calling a program is not calling a function 1:28:54–1:36:01

The student imagined telling Python to go to a directory and run a C file. The instructor accepted the direction and asked the missing design question: *how will the two exchange data?* A compiled executable can accept command-line arguments when it starts, read a stream such as standard input, and write a reply to standard output. A socket is another possible communication channel. A raw C source file is not normally the executable. Nor does launching the executable let the parent invoke any arbitrary internal function by name.

Then the student asked whether two terminals had to be open. A **process** is a running instance of a program. A **shell** such as Bash or PowerShell is itself a program; interactively it accepts a command, arranges for execution and returns to a prompt. A **terminal** provides an interface to it. In batch use the shell takes commands from a script instead. A parent can ask the operating system to start a child directly, much as programs ask the OS to allocate resources. The parent can wait without showing a second window; a child is still a separate process. The spoken “dot slash” was a fragment about familiar shell usage, not an observed command.

**Key items to scan**

- **Process** — the running instance; a parent and child can exchange data without sharing an interactive window.
- **Shell** — an interpreter of commands; launching an executable does not always require one.
- **Terminal** — the interface the user sees; counting terminals does not tell you how many processes exist.

### A child program without a second terminal • save as `exchange.py`

Annotated teaching version — guide-added code and comments; not a classroom run.

```python
import subprocess                                  # Ask the operating system to run another program.
import sys                                         # Use the same Python executable for the child.
child = 'print(input().upper())'                    # Child reads one line and prints it in capitals.
result = subprocess.run(                           # Start the child and wait for completion.
    [sys.executable, '-c', child],                  # Pass executable and arguments directly, no shell.
    input='apple\n', text=True,                     # Send the word and its line ending to child stdin.
    capture_output=True, check=True,               # Collect the streams; raise if the exit status fails.
)
print(result.stdout.strip())                       # Parent now displays the captured word: APPLE.
print(result.returncode)                           # Separate value: successful process exit status, 0.
```

`python exchange.py` — independently verified output:

```text
APPLE
0
```

| Step | What crosses the boundary? | Who receives it? |
|---|---|---|
| Start | Executable and arguments identify the child and its work. | Operating system starts another Python process. |
| Send | The parent supplies `apple` plus a newline. | The child's `input()` reads one line from standard input. |
| Collect | The child writes `APPLE` plus a newline and exits successfully. | The parent collects standard output and the separate exit status. |
| Display | The parent prints the stripped word, then the status. | Two lines appear in the parent's terminal. |

A compiled C executable that reads and writes the same streams can play the child's role. This demonstrates a stream interface, not a call to an arbitrary C function inside that executable. Supplemental reference: [Python subprocess management](https://docs.python.org/3/library/subprocess.html).

**Key items to scan**

- **`input=`** — supplies the child's standard input; not the same thing as a command-line argument.
- **`result.stdout`** — text the child wrote; **`result.returncode`** — process status, not the text or a C function's return value.

A different route is to expose a compiled library function to Python. Then the caller passes arguments across a binding in the same process, and the function returns a value; this is not text written to a child's standard output. The lecture moved between these models in conversation. Choose one before drawing your data flow.

The instructor offered a reading strategy for C source: at the top level look for *declarations* and *function definitions*. A declaration supplies information such as a name and type. A function definition has a body, with statements that perform work and possibly parameters that receive arguments. This is a point about the grammar of a translation unit, not a claim that C lacks loops, assignments, or statements. Assigning a new value to an existing variable isn't the same as declaring it. It gives the student something specific to look for when reading `file`.

**Key items to scan**

- Executable versus library — exchange data with a running program, or call an exposed function; pick the mechanism that matches the intended interface.
- C declaration versus function body — first locate what names are introduced, then where the work is implemented.

## 11 · What makes a measurement useful? 1:36:01–1:44:18

The learner wanted the power to solve problems without necessarily spending a career at a terminal. The instructor's answer was that technical knowledge helps evaluate feasibility, choose a starting point, and collaborate with someone who may implement or maintain the solution. A person who knows syntax still needs a problem worth solving. A person with an idea still needs a way to test whether it can work. The instructor's view that AI raises the bar for routine coding was career advice, not labor-market data. The discussion of open source, proprietary code, and license keys returned to the same distinction: the value isn't explained merely by noting that a program is represented by bits. What does *this* program do for someone?

Take the proposed athlete wearable. Fitbits in a drawer illustrated why features alone do not guarantee a useful design. The student wanted something affordable and easy to wear that could produce a heat map after a game. At least three jobs hide in that request: make the device practical to wear, collect usable observations, and turn a stream of measurements into a map that changes a decision. NFL players and NASCAR or Formula One vehicles and drivers supplied examples. The test is whether someone can use the result in the *next* game or race. No sensor selection, performance result, or prototype was demonstrated. The instructor proposed starting simple, not building an entire sports analytics system at once.

**Key items to scan**

- Heat map — a desired interpretation of location observations, not the raw stream itself.
- Feasibility — ask whether the project can collect suitable data and whether its result supports a real decision.
- Open source versus proprietary — a discussion about how value and access are organized; it does not make software licensing irrelevant.

## 12 · What can a device do, and what do we actually know? 1:44:19–1:53:47

Speculation about neural interfaces and cloud services led to a more immediate example: giving an AI agent access to utility bills and a bank account. Seeing a bill and paying it are different powers. So are remembering a conversation and making it visible to someone else. Before authorizing a convenience, ask which data the service needs, what action it may take, who can review it, and whether the user should confirm a financial transaction. The class raised these risks; it did not document a bank integration or a disclosure.

The instructor imagined a computer buried at an unknown spot in the woods. It might be hard for anyone to get at, including its owner. That makes the cost of unusability vivid. It doesn't establish that every improvement to usability weakens security. A useful design question is how to preserve the legitimate action without granting unnecessary authority.

**Key items to scan**

- Read access versus payment authority — different permissions with different consequences.
- Buried computer — an extreme analogy: keeping everybody out defeats the owner's purpose as well.

### The ceiling box, the thermostat, and the reported smoke detector

A blinking box in the room invites an explanation. Resist collapsing three different sources of evidence into one story. The instructor said the wall thermostat was controlled remotely by buildings and grounds. The ceiling device was a *different* object: a reported `W-100` sticker and a blinking light did not establish a microphone, a Wi-Fi link, or a connection to building management. Asking the authorized facilities owner for the model and documentation would test those claims without treating speculation as observation.

A student separately recalled a conference talk about a school bathroom device said to detect smoking and signs of verbal aggression. The important distinction is between sensing sound, classifying it in real time, retaining a conversation, and being able to change behavior in a future update. Each needs different evidence. The anecdote and a separate story about a television do not establish what the classroom device did. Likewise a device *could* process locally, or it *could* send data for analysis; the room discussion did not verify either architecture. For connected devices, useful follow-up questions are what leaves the device, who can receive it, and who authorizes software changes.

The instructor's closing question was simple: *Why is it blinking?* Keep that observation separate from the most dramatic explanation you can imagine. The learner planned to narrow the investigation, share a useful link, and continue the challenges. The closing conversation did not establish a definitive challenge count or deadline.

**Key items to scan**

- Observation versus inference — a blink can be observed; recording, transmission, or server-side analysis require independent evidence.
- Internet of Things — networked embedded devices can sense useful parameters, but the purpose of the device does not settle every question about the resulting data.
- Update authority — a device's present behavior, possible future features, and the party allowed to change it are separate questions.

## Syntax and interfaces to remember

| Pattern | Meaning in this lesson | Evidence |
|---|---|---|
| `2 ** rows` | Two possible outputs per input row; count whole output columns. | Guide calculation based on the spoken black boxes. |
| `file` / `ls` | Classify an object / list directory entries. Different questions about filesystem objects. | Spoken research suggestions, no historical run. |
| `[:8]` | Take a prefix of the fixture's bytes without interpreting the rest. | Independent signature example. |
| `subprocess.run(...)` | Launch a child and wait for it. | Independent process example, not an API named in class. |
| `stdout` / `returncode` | Captured output data / process termination status. | Independent process example. |

## Knowledge check

1. A box has two binary inputs and one binary output. A student says, “I need a sixteen-row table.” What two counts have been confused?
2. You receive a string of bits representing 200. You copy it and both copies have matching checksums. What have you learned, and what have you *not* learned about a payment?
3. Two people compare Git history and their tips differ. Why is that useful evidence but not proof that someone cheated?
4. Why can a sprint-timer project proceed before its author understands how satellite navigation works? What still has to be understood?
5. A file is named `puzzle.dat`. Its opening bytes match those of PNG files. What may you infer, and what stronger claim would require more evidence?
6. A child program prints `APPLE` and exits successfully. Which result would you inspect to recover the word? Do you need another terminal window?
7. You see a light blink on a ceiling-mounted device. Name an observation, a plausible hypothesis, and a way to check it without treating the hypothesis as fact.

### Answer key

1. **Four rows, sixteen possible functions.** There are four input pairs: `00`, `01`, `10`, `11`. For each row you can independently choose one of two outputs. Sixteen counts the possible filled-in output columns, not the rows needed by one box.
2. **The copies agree; ownership has not been established.** A checksum can agree on two copies of the same record, including two copies of an incorrect record. It cannot by itself establish who authorized the transfer, whether the sender had the funds, or which transaction history is accepted.
3. **You have discovered a discrepancy, not its cause.** Someone might simply have added a legitimate commit. Compare the relevant histories against a trusted reference before interpreting a difference. A computed identifier is not a witness to somebody's intent.
4. **Use the device's interface.** The project can treat the GPS unit as a black box if it knows which requests it accepts and how to interpret its replies. That does not settle whether the returned measurements are frequent or precise enough for sprint timing; those are project tests, not facts established in class.
5. **The prefix is consistent with PNG.** It is a clue about the format, not proof the file is complete, decodable, or contains a particular picture. The practice fixture supplies a counterexample: it contains only the signature.
6. **Read standard output; inspect exit status separately.** A parent can launch the child and capture the text without opening an interactive shell or another window. In the practice run, `APPLE` was the captured data; `0` was the exit status.
7. **The light blinked; its meaning is not yet known.** For example, periodic status reporting is one possible explanation. Identify the model through authorized facilities information and check its documentation. A blink alone does not establish that a microphone exists or that a recording is underway.

## Library of definitions

| Term | Meaning in this session |
|---|---|
| Algorithm / data structure | A method for doing the work / an arrangement of the data the method operates on. An arrangement is useful relative to the operations a problem needs. |
| Numerical method | A procedure for obtaining numerical results, often approximations. The instructor argues for encountering the practical problem before abstracting it. |
| Truth table / Boolean function | A table has one row for each input pattern; the function specifies an output for each row. Changing an output gives a different function. |
| Bit / byte / block | A bit has two possible values. A byte here is eight bits. A block is a grouped portion of data; the word alone does not fix its length or meaning. |
| Encoding / interpretation | Agreed rules for writing information as bit patterns and reading it back. A grade sequence and an amount need not use the same rules. |
| Ledger / transaction | A history of records / an event recorded in it. A record may describe a transfer without being a physical object that changes hands. |
| Checksum / cryptographic hash | A computed check value / a digest designed with stronger resistance to certain kinds of collisions and manipulation. Neither alone establishes who approved a transaction. |
| Consensus | The rules by which participants reach agreement on accepted history. Agreement is distinct from concealment of data or truth of a claim about the outside world. |
| Confidentiality / integrity / authentication | Limiting who can read something / detecting or preventing unauthorized change / establishing the claimed identity or origin. They answer different questions. |
| Abstraction / interface | Setting aside internal details for a purpose / the operations and data conventions a component exposes. A black box must still have an intelligible interface. |
| Header / magic number | A leading region of a file / a recognizable value that helps identify a format. A recognizable value is evidence, not a complete validation. |
| Process / shell / terminal | A running program with operating-system resources / a command interpreter that is itself a program / an interface through which a user interacts with programs. |
| Standard input / standard output / exit status | A conventional incoming stream / a conventional outgoing stream / a separate value reporting how a process finished. |
| Declaration / function definition | A declaration introduces a name and its type or other properties. A function definition supplies the body that implements the function. Declaring a name is not the same as assigning a value to it. |

## Historical screen record: a resource, not a code listing

At **1:01:00**, the class refers to a video a participant shared. Discord shows a YouTube preview from **EO** titled **“Stanford CS Professor: AI Can Code. That’s Why You Should Learn |…”**. The thumbnail reads **“Now is the time to learn to code”** and the post says **“Worth the watch!”**. The same card is legible at **1:28:49** while the discussion has moved on to combining programs. That is why an image alone cannot tell you what is being taught at that minute. The link in the card is a recommended resource, not the Session 28 source. The guide does not attribute the linked video's unviewed contents to the instructor.

On a second review of the teaching screen, the post still supplies no editor gutter, executable program, terminal command, or output to transcribe. The opening and closing captures show recording software over the Discord view. Numbering the practice programs as historical lines would falsely attribute them to the instructor. The original full-resolution PNGs are retained in the session workspace.

## Micro-Learning Insights — removable appendix

- **Use the shared video as a question, not an authority.** The EO preview reads “Stanford CS Professor: AI Can Code. That’s Why You Should Learn”; the instructor had seen its beginning. Ask which part of the sprint project needs a problem definition before it needs code. (1:01:00)

- **Count the choices twice.** For two binary wires, first count four input patterns; then choose between two outputs on *each* row. That is why four rows and sixteen functions are both right. (0:18:00)
- **Ask which object is moving.** “I gave you 200” can describe a physical handoff, copied data, or a new ledger entry. Following one of those stories while your partner follows another creates the apparent paradox. (0:40:00–0:45:00)
- **Use the class's question about the verifier.** If an independent copy disagrees, you found a discrepancy. Before accepting one copy, explain why its source or the system's acceptance rule deserves trust. (0:52:00–0:54:00)
- **A bit pattern needs a reader.** Try the instructor's grade sequence, `A`, `B`, `A+`, `B−`. No bit is inherently a grade or a currency amount; interpretation comes from rules outside the individual bit. (0:56:00–0:58:00)
- **Turn “learn GPS” into an interface question.** What do I send, what arrives back, and how do I interpret it? That gives a sprint-timer project a first test without requiring a satellite-navigation textbook first. (1:02:00–1:04:00)
- **Separate the algorithm from its spelling.** Sequence, selection, and repetition still need to be designed when you change languages. Learn the second language for the capability or level of control it adds. (1:07:00–1:14:00)
- **Study a familiar command backwards.** Before reading the source of `file`, predict what evidence it would need to distinguish compressed data from an image. Then compare your prediction with the documented tests. (1:23:00–1:28:00)
- **Draw the process boundary.** Arguments at launch, bytes on a stream, and an exit status are three different channels. Drawing the arrows prevents confusing a function return with text a child printed. (1:29:00–1:34:00)
- **Let curiosity produce a checkable claim.** “That light blinked” invites investigation; “it is recording us” assumes a capability and an action. Keep the observation even if your first hypothesis turns out to be wrong. (1:48:00–1:52:00)
