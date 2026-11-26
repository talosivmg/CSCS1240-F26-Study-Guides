# From bits to a usable answer

**CSCS1240F26 · Session 35 · Worked teaching companion**  
[Watch the recording: JoesTA, *CSCS1240F26 35*](https://www.youtube.com/watch?v=n88OXtsO6ys)

A float with the value **`123.45`** has an identity, but the bytes of its identity are not the bytes that encode the number. That distinction opens the central question of this class: **what gives a pattern of bits its meaning?** The instructor builds an answer with an integer, two food symbols, the fraction in **`5.75`**, and a floating-point display. The closing square-root puzzle asks a different question: even when you understand the representation, how close does your answer need to be?

| Find your place | Start watching here |
|---|---|
| Course progress, asking for help, and the scheduling problem | [0:00:00](https://www.youtube.com/watch?v=n88OXtsO6ys&t=0s) |
| Object identity, eight bytes, and what a cast does or does not change | [0:28:06](https://www.youtube.com/watch?v=n88OXtsO6ys&t=1686s) |
| Why five needs a base, a place, and an agreed meaning for each symbol | [0:50:51](https://www.youtube.com/watch?v=n88OXtsO6ys&t=3051s) |
| Taking the point seriously: five and three quarters; when more precision matters | [1:18:50](https://www.youtube.com/watch?v=n88OXtsO6ys&t=4730s) |
| Inside the IEEE 754 teaching app: fractional bits, normalization, and spacing | [1:45:13](https://www.youtube.com/watch?v=n88OXtsO6ys&t=6313s) |
| Square roots without a calculator; choosing guesses, tests, and tolerances | [2:17:03](https://www.youtube.com/watch?v=n88OXtsO6ys&t=8223s) |

**Evidence.** The full published English auto-caption track has 3,915 cues, from the greeting through the sign-off at 2:51:46.200. The video ends at 2:51:47.083. Brief stretches have no caption; captions are not verbatim code. The visual record consists of all 172 whole-minute targets and 36 additional stills (208 total) at 1920 × 1080, including a closing frame. Times identify requested seeks, not every intervening keystroke. In this guide, *observed* means visible in the recording, *reported* means stated in class, and *independently verified* means a separate check, not a replay of the professor’s machine. Classroom policies are historical remarks. A second pass added 13 of the event stills for rapid edits and app results. The appendices keep original screen evidence separate from guide-added teaching comments.

## Reading the progress report before Thanksgiving (0:00:35–0:06:31)

The class had one week of classes and a Monday left after Thanksgiving break, followed by finals. The instructor had run a progress query because the time remaining was short and the class was spread across different challenges. A useful distinction was easy to miss: **3030** was a challenge identifier; **21** was the number of code challenges offered so far. To assess your own progress, the question was “How many of the 21 have I done?”, not “How close is my latest challenge number to 3030?”

At 0:02:00, Sublime Text showed a record of the SQLite query **`select * from progress;`**. The query reads all columns from **`progress`**; it displays the counts already available there rather than calculating a count in this statement. The first result, **`3030|3`**, fit the instructor’s report that three people had finished challenge 3030. The screen showed a class distribution, not a personal checklist or a grade formula. The instructor wanted a better script so students could see their own completion information; that was a proposed improvement, not a tool demonstrated in class.

The instructor made a decision for this offering: **3030 was the last officially required challenge**. More challenges could be offered to someone who wanted them, but would not be required. The spread in progress helped explain that decision; it did not explain *why* people were at different points. The instructor invited students to describe what had worked, what had held them up, and what they planned to do next. They could post to the class channel or send a private message. One student planned to use the break for catch-up. The caution was practical: a week that looks empty on a calendar can fill with family obligations, other courses, and all the work deferred until break. Start the plan from what can still be done, not what should already have happened.

**Key items to scan**

- **3030** — last required challenge in the instructor’s announcement for this class; later work, if offered, would be optional.
- **21** — count of challenges, the denominator for a personal completion check; not a grading percentage.
- **`select * from progress;`** — query text visible in an editor. **`3030|3`** was an aggregate result; no live database execution or individualized report was shown.
- **Feedback** — name the obstacle, what helped, and the next step. A direct message was an alternative to a public post.

## Helping with a challenge: share the error, not just the milestone (0:06:31–0:12:27)

Students who had met the requirement still had something useful to contribute: help someone through a particular challenge. A student raised the obvious difficulty: how do you ask someone who has solved it if you do not know who that is? Rather than identify classmates from the progress display, the instructor suggested giving people time to watch the recording and inviting each person to post where they were. That would make it possible to address a question to someone with relevant experience without asking the whole channel to guess who could help.

The class described what a useful Discord post could contain: a GitHub note or a one-line way of viewing a structure, what the author thought was complete, where it stopped working, and the error that appeared. An invitation to ping the author gave the next person seeing that error somewhere to start. The value was not limited to getting one person unstuck. Two people can try different approaches to the same problem and get different messages. Comparing what each tried with what the machine reported gives both people practice interpreting errors they would not see by following only the instructor’s approach. No specific GitHub command or challenge error was demonstrated in this exchange.

**Key items to scan**

- **Self-reported progress** — a proposed way to find an experienced peer, not permission to publish someone else’s standing.
- **Discord help post** — pair the particular challenge or attempted approach with the actual symptom; invite a follow-up from someone who encounters it later.
- **Error messages** — evidence about what happened on a particular attempt. Comparing different attempts is part of learning to read that evidence.

## Turning a technical notebook into something you can use (0:12:27–0:14:53)

Writing notes and using them later are different problems. The instructor asked what sort of notes would help not only here, but also in later programming, data structures, discrete mathematics, or other technical courses. A student described taking screenshots in a networking course, keeping them in OneNote, and typing key phrases alongside the pictures. That made a picture searchable later, but it did not make the whole collection coherent. Using AI to turn pictures and context into structured text was another experiment. A participant mentioned a tool as “Gemini notebook”; the class did not demonstrate it. The unresolved design question was what a useful technical resource should let its reader *do* before deciding what tool should produce it.

An earlier post visible in the class channel at 0:04:00 illustrates the distinction. It repeats “type = the amount of memory and the organization of that memory.” The reply asks for the idea in the writer’s own words and for an example or two. This was a prompt in chat, not a complete technical definition taught during the note-taking discussion. It gives a concrete test of a note: can you use it to explain an example, rather than merely find and repeat the sentence?

**Key items to scan**

- **Screenshot + searchable phrase** — captures the visual example and supplies words to find it again in OneNote; does not organize the examples by itself.
- **AI-assisted notes** — discussed as a possible way to structure captured material, not a finished class tool or a substitute for deciding what the notes should explain.
- **Own words + example** — the visible follow-up to a repeated definition asks the writer to show what the words mean in practice.

## Why place two computing courses together? (0:15:08–0:19:45)

A student whose morning math course conflicted with a computing class preferred the Thursday arrangement. Two computing courses close together were easier to plan around, and ideas from the first remained fresh in the second. One suggestion was to keep Essentials at its time and go straight from Essentials into Problem Solving. That was a suggestion, **not** the Thursday order the instructor described: on Thursdays, roughly two hours of 1240 were followed, after a short break, by roughly two hours of 1200.

The benefit of that block was avoiding an interruption just when students had settled into a problem. Its cost was nearly four consecutive hours on one subject: what helped one student concentrate might overwhelm someone else by the third hour. The instructor wanted more students’ experiences before drawing a general conclusion. Asked about combining the courses, the instructor described a personal ideal rather than a curriculum decision: one recurring computer science course in which the class could build on what it had just learned and follow the next natural question, rather than stopping at the boundaries between problem solving, C++, data structures, and Unix/Linux.

**Key items to scan**

- **Thursday as described** — 1240, a short break, then 1200; a real arrangement against which to compare proposed alternatives.
- **Contiguous time** — less effort spent getting back into a problem, but a longer stretch that may not suit everyone.
- **One recurring course** — the instructor’s hypothetical way to teach connected subjects, not an announced merger or timetable change.

## Scheduling the campus: possible, preferable, or merely familiar? (0:20:02–0:25:40)

“Put the computing classes together” sounds like a local change. But a student who needs both computing and English can lose access to English if one of those sections moves. Psychology and other liberal arts requirements matter for the same reason. Now add the sections to be offered, the instructors available to teach them, the limited time blocks in a week, and the clock time required by credit hours. A change that fixes one student’s conflict can create another elsewhere.

The instructor posed two different questions. First, can we prove that there is a conflict-free schedule **for everybody** under those conditions? The instructor doubted it but offered no impossibility proof. Second, even if perfection is out of reach, could another combination work *better*? The existing approach was described as a **heuristic**: start from what worked reasonably well in the past and move a section when enough people report a conflict. That produces a timetable, not a guarantee that it is the best one. Programs and courses change between semesters, so even a good arrangement needs to be reconsidered. The instructor connected the challenge to scheduling and logistics beyond universities.

A student pictured several three-dimensional cubes of requirements: time, people, and the objectives the schedule should serve. Some conditions determine whether a timetable works at all; others distinguish better and worse timetables that could work. Giving a preference a **weight** means making it count more or less when comparing alternatives. Putting computing courses together might score well for someone who wants continuity; separating them might better serve someone who needs a break. The class drew a brief analogy to matrices and weights in AI, not a working AI scheduler. At 0:25:00 the Discord image makes the scale of the puzzle visible: a small colored cube in one hand beside a much larger, more finely divided cube. The remark about solving the inside before the outside was a puzzle analogy, not a scheduling algorithm.

The workplace parallel was having a large dataset and being asked to pivot it into one simple result. A useful first question is not “Which chart should I make?” but “What result does the recipient actually need?” Identify that deliverable, then break the work into smaller steps. In the timetable problem, “give every student access to required courses” and “keep related classes adjacent when possible” are different deliverables; keeping them distinct makes it possible to judge what a proposed change actually improves.

**Key items to scan**

- **Conflict-free for everybody** — a feasibility question about the whole campus, not just clashes between computing sections. No proof or implemented solver was shown.
- **Heuristic** — reuse a historically workable timetable, then adjust for reported conflicts; a fix can displace the conflict elsewhere.
- **Weight** — the relative importance of a preference such as adjacent classes versus time for a break; no numerical scoring rule was specified.
- **Cube comparison** — the image shows the change in scale, while the discussion explains the interacting requirements.
- **Deliverable first** — decide what the timetable or chart must show before breaking the data work into manageable pieces.

## 0:26:00–0:30:35 · What did the program actually take apart?

The class now puts the earlier note about type to a test: can we use it to explain a real object, rather than just repeat the definition? The instructor hopes to return to the earlier “70% of math” discussion in the remaining weeks. A student spots π in the numerical work; the instructor postpones it. The roadmap lists **`jStruct.py`**, then **`floatBytes.py`**, the IEEE standard and the GUI. These are different demonstrations, not successive names for the same program. A visible roadmap note also distinguishes a single input **`a`** and its dependent output **`b = f(a)`** from giving the function a vector of inputs **`A`** and getting a vector **`B`** out. That is planning text on the screen, not a vector computation performed here.

The first program starts with **`123.45`**. Does it take apart the bytes encoding that number, or the *number that identifies the object*? Follow the right-hand side of the assignment. **`id(obj)`** takes the float as its argument and returns an **integer identity**. All eight later masks operate on that integer. They never fetch the bytes encoding the float's numerical value. The float is still there, unchanged.

Annotated teaching version — safe opening of the classroom script, guide-added comments. The results shown below the block are from the first classroom run, not predictions of your own object's ID.

```python
import sys                                  # Supplies this build's sys.maxsize.
idSize = (sys.maxsize.bit_length() + 1)     # Here 63 magnitude bits plus 1 gives 64.
print(f"Python ID size: {idSize} bits")     # Prints the program's width label.
octet = bytearray(int(idSize/8))           # A new, mutable sequence of eight zero bytes.
print(f"Bytes: {octet}")                    # Print the initial contents, before any masks.
obj = 123.45                               # Bind a name to the float; do not convert it.
objId = id(obj)                            # A different quantity: an integer identifying obj.
print(f"obj id: {hex(objId)}")              # Format that integer in base 16, including 0x.
```

Observed in PowerShell (**0:30:45**; original command **`py .\src\jstruct.py`**):

```text
Python ID size: 64 bits
Bytes: bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00')
obj id: 0x1d9a06cadf0
```

The label is the program's shorthand, not a measurement of this particular ID's significant bits. **`sys.maxsize.bit_length() + 1`** uses the platform's signed-size limit as a width proxy. On this build it gives 64; the displayed ID needs only 41 significant bits when written without leading zeros. The program allocates room for eight bytes and then uses eight hard-coded indices; it is not a general platform-width detector or a portable serialization of Python identities. Likewise, **`bytearray`** is the particular built-in mutable byte sequence used here, not the only array facility available to Python (the standard library also provides **`array.array`**).

**Key items to scan**

- **`jStruct.py`** — roadmap spelling; the PowerShell invocation uses **`py .\src\jstruct.py`**. This is the identity experiment.
- **`floatBytes.py`** — the later object-layout/value-bytes experiment. Seeing π in scrollback does not mean either byte experiment is computing π.
- **`a`**, **`b = f(a)`** — one independent input and dependent scalar output in the roadmap; **`A`** and **`B`** are its input/output vectors.
- **`123.45`** — float value. **`id(obj)`** — integer identity of the object; in classroom CPython this corresponds to its address, not its contents.
- **`sys.maxsize.bit_length() + 1`** — yielded a working width of 64; **`idSize/8`** gives eight byte slots here.
- **`bytearray`** — eight zero-initialized slots in this example; later assignments change these slots, not the float. **`array.array`** is a separate typed-array facility.
- **`hex(objId)`** — makes an integer easier to read; converting it to hexadecimal is not what separates it into bytes.

## 0:28:56–0:31:46 · Eight slots out, one integer back

To see why the byte display contains a letter, write the first run's integer as eight hex pairs, most significant first: **`00 00 01 d9 a0 6c ad f0`**. Slot zero gets the *rightmost* pair. In the classroom's unrolled code, **`objId & 0xff`** keeps just that low byte. For slot one, the mask moves one byte left; after masking, the right shift returns the selected bits to the low end so the result fits in one slot. At the other end of the listing, a left shift and a bitwise OR put each piece back into place.

Safe teaching version — guide-added comments; independently executed with the first displayed number as **fixed integer data**. The loop factors the eight classroom assignments into one pattern. It does not look up an object, read an address or change any float.

```python
n = 0x1d9a06cadf0                          # An ordinary integer copied from the screen.
octet = bytearray(8)                       # Eight new, initially zero slots.
mem = 0                                    # Start a separate integer at zero.
for i in range(8):                         # Fill slots 0 through 7, low byte first.
    shift = 8 * i                          # Place of this byte: 0, 8, ..., 56 bits.
    octet[i] = (n & (0xff << shift)) >> shift  # Keep one byte and align it at bit 0.
    mem |= octet[i] << shift               # Restore that byte; preserve earlier bytes.
print(f"Bytes: {octet}")                    # Display all eight; one looks like a letter.
print(hex(mem))                            # 0x1d9a06cadf0: the original integer.
assert mem == int.from_bytes(octet, 'little') == n  # Check by an independent route.
```

Classroom order: finish all eight extractions, print the array, then rebuild, starting with **`mem = octet[0]`** rather than zero. The following trace pairs each extraction with the corresponding rebuild step for study. Values independently verified for that same fixed integer:

| Slot | Bit shift | Byte, hex (decimal) | Integer after restoring this slot |
|---:|---:|---|---|
| 0 | 0 | `f0` (240) | `0xf0` |
| 1 | 8 | `ad` (173) | `0xadf0` |
| 2 | 16 | `6c` (108) | `0x6cadf0` |
| 3 | 24 | `a0` (160) | `0xa06cadf0` |
| 4 | 32 | `d9` (217) | `0xd9a06cadf0` |
| 5 | 40 | `01` (1) | `0x1d9a06cadf0` |
| 6 | 48 | `00` (0) | `0x1d9a06cadf0` |
| 7 | 56 | `00` (0) | `0x1d9a06cadf0` |

Observed during the first classroom run and reproduced with the fixed integer:

```text
Bytes: bytearray(b'\xf0\xadl\xa0\xd9\x01\x00\x00')
0x1d9a06cadf0
```

Read **`\xadl`** as two bytes: the escape **`\xad`** ends after exactly two hex digits; the lowercase **`l`** is a separate byte, **`0x6c`**. Python shows printable ASCII characters directly when convenient in this representation and uses escapes for other bytes. It has not decoded the array as text or put an extra character in it. The two zero high bytes account for the difference between the short printed hex number and a full eight-byte encoding. The code's index choices establish *little-endian order* (least significant byte first); they do not measure the host's native byte order.

**Key items to scan**

- **`00 00 01 d9 a0 6c ad f0`** — eight-byte, high-end-first way to write the first ID; the classroom bytearray displays them in reverse significance order.
- **`objId & 0xff`** — select the low byte, here **`0xf0`**; **`>>`** brings a selected higher byte down to slot-sized range.
- **`mem = octet[0]`** — start the classroom rebuild with 240; **`<<`** restores place, **`|=`** combines disjoint bit positions into the integer.
- **`\xadl`** — escape **`\xad`**, then printable **`l`** (**`0x6c`**). Count bytes, not characters in the printed representation.
- **`int.from_bytes(octet, 'little')`** — safe numerical check; rebuilding an address-shaped integer is not retrieving an object or recreating its value.

## 0:31:46–0:38:49 · Same value, or the very same object?

The first classroom run ends with **`123.45`** and **`<class 'float'>`**. Those lines answer *what value?* and *what type?* They do not settle *which object?* At first, the instructor describes **`back`** as a copy at a different location. A student asks whether the addresses are the same and another points out that the program printed the value, not the second ID. The instructor's response at **0:33:38** is the debugging lesson: “make me prove it.” Comparing different runs or printing the same value twice would not prove it.

Annotated teaching version — completed classroom diagnostic footer, with guide-added comments. Here **`obj`** and **`back`** already refer to live objects from the same run. The unsafe classroom operation that obtained the second reference is not needed to study this diagnostic.

```python
print(back)                             # Display the value: 123.45.
print(type(back))                       # Display its type: <class 'float'>.
print(f"id obj: {id(obj)}")             # Keep the literal label; insert the first ID.
print(f"id back: {id(back)}")           # Ask for the second ID independently, in this run.
```

A student's question about printing literal text matters here. With the **`f`** prefix, text outside braces is literal; inside **`{id(obj)}`**, Python calls **`id(obj)`** and substitutes its decimal rendering. The braces themselves don't print. Both the *label* and the *argument* must change on the second line. The editor briefly shows a second `id obj:` label with an empty call. The instructor reports saving; the terminal later shows a completed run, not an error from that intermediate edit.

Observed second-run excerpt, visible by **0:36:00** and unobstructed in later scrollback at **0:50:00**:

```text
obj id: 0x1eb898cadf0
Bytes: bytearray(b'\xf0\xad\x8c\x89\xeb\x01\x00\x00')
0x1eb898cadf0
123.45
<class 'float'>
id obj: 2111136640496
id back: 2111136640496
```

**`0x1eb898cadf0`** and **`2111136640496`** are two notations for the same integer. This ID differs from the first run's, but the two IDs *within* the second run match. The two names refer to one live float: the classroom code reconstructed its identifying number, not a second float with an independent location. The instructor acknowledges that copying the data was the original intent; the test distinguishes intent from behavior. A student suggests the final value explains the result. More precisely, equal values alone are not sufficient: it matters that the operation referred back to this particular live object.

Safe comparison — independently executed, guide-added comments. This time ordinary Python assignment makes the alias; a text-to-float conversion supplies the equal-value comparison. No particular ID number is needed.

```python
obj = 123.45                            # First float, kept alive by this name.
back = obj                              # Another name for it; no float is copied.
print(back is obj, back == obj)         # True True: same object, equal value.
other = float('123.45')                 # Separately obtain a float of equal value.
print(obj == other, obj is other)       # True False in the independent run.
```

The instructor introduces **`ctypes`** as a Python bridge to C data types and explains that CPython is an interpreter program written in C, installed and maintained like other software. The classroom's low-level reinterpretation depended on that implementation and a still-live object. Python guarantees an ID unique among objects alive at the same time and constant over that object's lifetime; an ID is not a portable pointer, a permanent identifier or an instruction to retrieve arbitrary memory. An incorrect or expired address can crash a process. Use the fixed-integer round trip for the bits and ordinary assignment for the identity question.

**Key items to scan**

- **`123.45`** and **`<class 'float'>`** — the first run's value and type; neither distinguishes alias from copy.
- **`obj`**, **`back`** — distinct names, one observed live float in the second run.
- **`f`**, **`{id(obj)}`** — the prefix enables evaluation of the expression **`id(obj)`** in braces. `id obj:` is just a label.
- **`0x1eb898cadf0`** = **`2111136640496`** — hex versus decimal, not two locations. Compare IDs *within the same run*.
- **`is`** — same object; **`==`** — equal value. The separately created float can satisfy the latter without satisfying the former.
- **`ctypes`** — C interoperability, not a promise that an ID is a safe address on any Python implementation.

## 0:38:49–0:47:52 · Which sense of “cast” fits?

The class tries several meanings: casting a fishing lure (moving something), casting a spell (causing an effect), a cast around a broken arm (a framework), actors in a cast, and casting ceramics or iron. The instructor chooses a skillet: pour molten iron into a shaped mold, let it cool, then break the mold away. A student connects that to a circuit's source and destination. The useful part is choosing a form for something; the misleading part is imagining that *every* software cast pours data into a new block. The integer rebuild did copy bits into a separate integer, but the classroom reference operation did **not** manufacture a new float.

At **0:42:06** the class returns to actual lines: line 15 takes the identity; line 18 takes the low byte of that identity. Slot 0 is not the whole object and slot 7 is not the number 56: **`56`** is the number of bit positions used to align the highest slot. This is where the two-part classroom meaning of **type** becomes useful: “How big is it?” and “How are those bits organized and interpreted?” For a specified fixed-width layout, equal widths let the same pattern fit in both representations. Size alone tells you neither what the represented value is nor whether it is safe or meaningful to treat it as another type. Python integers in general are not restricted to a fixed 64-bit layout.

At **0:44:57** a student proposes file copying: a bit stream is transferred and new storage allocated. The instructor instead offers a shortcut or symbolic link: two routes to the same target, no second copy of the target's contents. A shortcut is not literally a Python name, but it gets the *number of targets* right. Similarly, the important part of **aliasing** is not that two name strings have the same spelling or occupy the same slot; it is that following either name reaches the same object. The next question is different again: if a *specified sequence of 64 bits* stays fixed, what number will two different formats assign to it?

**Key items to scan**

- **Cast-iron mold** — helps explain a chosen form; it does not establish copying or new allocation by a software cast.
- **`56`** — highest shift in an eight-byte word; slot 7 contributes bits beginning at position 56, not necessarily the value 56.
- **Type** — for this fixed-width discussion, ask about both amount of storage and interpretation. Equal size does not imply equal meaning.
- **Aliasing** — two names for one target, like the useful part of the shortcut analogy. Copying file contents to another file is a different operation.

## 0:47:52–0:50:51 · Convert a number, or interpret its bits?

Consider a 64-bit integer field and a 64-bit floating-point field. They occupy the same width, but one assigns powers of two directly to integer positions, while the other divides the bits into sign, exponent and fraction. A *bit reinterpretation* keeps the pattern and changes the rule for reading it. In contrast, **`int(obj)`** asks for a numerical conversion. The editor shows that bare expression on line 45 at **0:49:00**: no assignment, no **`print`**, and no observed run of that edit. In a script, it would compute 123 and discard the returned integer; it would not change **`obj`** or put `123` on the terminal.

Safe teaching version — guide-added comments, independently verified. Encode only the numeric value into an explicitly ordered, eight-byte buffer; never inspect the memory surrounding a Python object.

```python
import struct                              # Convert numbers to/from specified byte formats.
obj = 123.45                               # Same value used in the identity experiment.
print(int(obj), obj)                       # 123 123.45: truncate toward zero; obj unchanged.
raw = struct.pack('>d', obj)               # Eight binary64 value bytes, high end first.
print(raw.hex())                           # 405edccccccccccd: NOT either classroom ID.
print(int.from_bytes(raw, 'big'))          # 4638387438405602509: same bits read unsigned.
print(struct.unpack('>d', raw)[0])         # 123.45: use the matching float format again.
```

So **`123`** is the number obtained by dropping the fractional part; **`4638387438405602509`** is a different number obtained by assigning integer place weights to the entire binary64 pattern. In the classroom's low-byte-first order the float payload is **`cd cc cc cc cc dc 5e 40`**, not the first ID's **`f0 ad 6c a0 d9 01 00 00`**. These sequences answer different questions even though both occupy eight slots.

At **0:49:28–0:50:18** the instructor turns to **`floatBytes.py`** and proposes a more approachable graphical IEEE 754 view. A different file is visible behind the terminal at **0:50:00**. Its opening note has columns for offset, size, field and meaning: a reference-count field, a pointer-related row, and an “actual float value” row. These illustrate a particular object layout, not the portable definition of a Python float. For instance, the visible value-field notation decodes (independent little-endian binary64 check) to **`12.34`**, not the earlier **`123.45`**. The `floatBytes.py` command line is being prepared; the output still visible belongs to the earlier identity experiment, not this new script.

A screen-only fragment on the right selects the upper half of byte 6. Here is what that visible term does, isolated from the obscured assignment. Safe teaching fixture, independently run; the byte source is the visible note, not a read of a Python object's memory:

```python
import struct                                            # Decode only supplied bytes.
floatBytes = bytes.fromhex('ae 47 e1 7a 14 ae 28 40')    # Fixed eight bytes from the note.
print(struct.unpack('<d', floatBytes)[0])                # 12.34, little-endian binary64.
print(hex(floatBytes[6]))                                # 0x28: byte 6 (count from 0).
print((floatBytes[6] & 0xf0) >> 4)                       # 0x28 & 0xf0 = 0x20; shifted, 2.
```

That final **`2`** is four selected bits, not the whole float or even the whole exponent. At **0:50:51** the instructor asks what pattern represents **`5.75`**. That is now a question about the *value*, with a floating-point format to specify, not about where its object lives.

**Key items to scan**

- **`int(obj)`** — numeric conversion of the value to **`123`**; no rebinding of **`obj`**, no visible output without **`print`** in a script.
- **`struct.pack('>d', obj)`** — encode the numeric value as big-endian binary64; the matching unpack returns it. No object ID is involved.
- **`4638387438405602509`** — the same binary64 bits interpreted as an unsigned integer, not the result of converting the float numerically.
- **`cd cc cc cc cc dc 5e 40`** — little-endian bytes of **`123.45`**; **`f0 ad 6c a0 d9 01 00 00`** — bytes of the first run's identity number.
- **`(floatBytes[6] & 0xf0) >> 4`** — takes the upper four bits of the note's `0x28` byte; the integer returned is **`2`**.
- **`floatBytes.py`** — new editor example; **`12.34`** is independently decoded from the visible note, not a terminal result. **`5.75`** is the spoken next example, not another identity test.

## 0:51:01 — Five of something: the count, the width, and the measurement

What else must you know before you can write down “five” for a machine? At 0:51:00 the **IEEE 754 Encoding & Decoding** window already shows **`5.75`**. Its first steps separate the positive sign (sign bit **`0`**), integer part (five written **`101`**), and fractional part (three quarters written **`0.11`** in binary). It shows the eight exponent bits **`10000001`**: the stored value is 129, and subtracting the bias of 127 gives an exponent of 2. The next heading is “Step 5: Mantissa / Fraction (23 bits)”; the field contents are not visible here. Instead of starting with that entire format, the instructor isolates the five. What exactly does even that part tell us?

The first scratch comparison is **`00005`** against **`0000000101`** (0:52:00). Read the first as decimal and the second as binary: both count five. A student asks for the interpretive framework, much as the word *cast* could refer to molding or to fishing. The instructor had assumed, without saying so, that everyone would read the lone digit **`5`** as an ordinary decimal count. The student’s objection brings out a second unspoken assumption: are we describing only the value, or also how much space its representation occupies?

| Scratch (0:54:30) | Read it from right to left | Width if each written place is stored |
|---|---|---|
| `101` | Take 1, skip 2, take 4: total 5. | 3 bits |
| `0101` | Same total; the added 8's place contributes zero. | 4 bits |
| `00101` | Same total; the added 16's place also contributes zero. | 5 bits |

As a number, five is unchanged by any number of leading zeros. As a fixed-width field, those positions still take space. The instructor calls these different *types* in the exercise: a type tells the reader how much memory to allow and how the contents are organized. This is not a claim that typing more leading zeros into a text editor changes the type of a Python integer. Counting place weights outward gives 1, 2, 4, 8, 16, 32, 64, 128, **256**; the caption's “266” does not fit the doubling rule.

Now put the zeros on the other side of a decimal point. In the instructor's *measurement* example (0:55:57–0:57:22), **`5.00`** gives the same numerical value as **`5`** but reports a reading to the nearest hundredth of a unit rather than to the nearest unit. The zeros convey what the observer measured; they do not introduce a nonzero fraction or guarantee accuracy. In **`5.75`**, the fractional part really does change the amount. That brings the class back to a question it can answer without knowing an encoding standard: what does each place contribute?

**Key items to scan**

- **IEEE 754 Encoding & Decoding** — the visible `5.75` example separates sign, integer/fraction conversion, and exponent; it is not an execution of the editor scratch.
- **`0`**, **`101`**, **`0.11`** — respectively the positive sign bit, binary integer part, and binary fractional part in the displayed example.
- **`10000001`** — stored exponent 129; the actual exponent here is 129 − 127 = 2.
- **`00005`**, **`0000000101`** — padded spellings in different bases, both representing a count of five under the stated readings.
- **`101`**, **`0101`**, **`00101`** — same unsigned binary value; three, four, or five positions if width is part of the declaration. Each new position has twice the weight; 128 is followed by **256**.
- **`5`**, **`5.00`**, **`5.75`** — distinguish a count, reported precision, and an actual nonzero fractional part.

## 0:57:52 — Why the same three marks can mean 575, five, or two

The instructor asks the class to start with elementary-school arithmetic rather than memorize a new conversion rule. Addition still combines quantities and subtraction still takes them away. What changes in ordinary positional notation is the base: how many distinct digits are available, the weight of the next position, and therefore when a carry is needed. Decimal convention supplies ten symbols, zero through nine. Binary needs only two. Borrowing **`0`** and **`1`** from decimal is convenient for people; it is neither a requirement on the shapes nor evidence that a circuit reads printed numerals.

The editor deliberately puts **`575`** on its own line below **`5.75`**. The first question is *not* how to convert the fraction. The instructor intended decimal **`575`** and asks what a passerby would probably see. The class says five hundreds, seven tens, five ones. When “70 times 10” slips into the conversation, the written line settles the coefficient: it is **`7*10`**, already seventy. Writing the weights as powers explains how to transfer the method. Here **`^`** means “raised to the power” in scratch mathematics, not an instruction to run in Python.

| Scratch notation (0:59:00–1:03:00) | Guide-added reading |
|---|---|
| `5*100 + 7*10 + 5*1` | 500 + 70 + 5 = 575. |
| `5*10^2 + 7*10^1 + 5*10^0` | Same sum; the weights are successive powers of ten. The rightmost weight is 1. |
| `5*2^2 + 7*2^1 + 5*2^0` | The weights are now 4, 2, 1. The marks `5` and `7` must be given new digit meanings if this is binary. |

A student spots the trap: how can a binary digit have value seven? It can't. But the *mark* shaped like a seven need not mean seven. Suppose that **`5`** and **`7`** are the only two symbols allowed. The string alternates first mark, other mark, first mark. Agree which mark means “count this place” before doing any multiplication. Reading the answer back in familiar decimal does not force the input symbols to retain their decimal meaning.

| Agreed binary key for `575` | Contributions at weights 4, 2, 1 | Decimal count, independently checked |
|---|---|---|
| `5` stands for 1; `7` stands for 0 | `1×4 + 0×2 + 1×1` | 5 |
| `5` stands for 0; `7` stands for 1 | `0×4 + 1×2 + 0×1` | 2 |

**Key items to scan**

- **`0`**, **`1`** — the familiar binary digit names; exactly two distinct symbols are needed, not these particular glyphs.
- **`5.75`** versus **`575`** — separate examples in the scratchpad; the latter makes the place weights and the assumed alphabet easier to isolate.
- **`7*10`** — contributes seventy; multiplying seventy by ten again would count the tens place twice.
- **`^`** — the scratchpad's power notation; changing the base changes the weights, not their left-to-right order.
- **`5`**, **`7`** — in the invented binary alphabet either mark can count the place, but the same key must be used in every position.

## 1:04:35 — Pick two symbols, then agree what they mean

The class tries to break the habit of seeing a familiar numeral and immediately hearing its decimal name. The scratchpad lists **`0, 1`**, **`true, false`**, **`yes, no`**, **`rocks, potatoes`**, and **`on, off`**; Tom and Jerry are also suggested aloud. The deliberately odd pair is useful: nobody is tempted to multiply the actual weight of a potato by four. All the arithmetic needs is a rule saying whether that position contributes its weight or contributes nothing.

The instructor calls the symbol that counts the place the **preferred symbol**. This is classroom terminology, not a ranking of the physical states. Students suggest that one of something seems better than none, that people favor truth or like to hear “yes,” and then challenge whether anyone necessarily prefers a device to be on. A student would choose pizza, but does not especially care about the relative merits of zero and one. Nor does choosing pizza make its bit the *most significant bit*: significance belongs to the position, not the digit. The very same pizza can occupy the fours place or the ones place.

Why write the choice down? A programmer across the country who sees the two food names cannot recover the author's convention from the names alone. A short key such as **`pizza = 1`** lets that programmer read, reuse, or adapt the notation without silently reversing every decision. There need not be a deeper reason to pick pizza over lasagna. There does have to be agreement about which was picked.

**Key items to scan**

- **`0, 1`**, **`true, false`**, **`yes, no`** — different alphabets for two alternatives; the list order is not itself a declaration of the active symbol.
- **`rocks, potatoes`**, **`on, off`** — the first pair removes familiar arithmetic associations; even the apparently natural second pair needs an agreed interpretation.
- **Preferred symbol** — the one that contributes the place weight. It does not designate the leftmost bit, a more expensive state, or the instructor's favorite food.
- **`pizza = 1`** — a guide-written key for the class's proposed convention; a future reader then knows that the other food represents zero.

## 1:07:33 — Does a zero cost less? From electrical states to a possible CPU project

A student proposes a different reason to choose: what if the idle state uses less energy? Scale that up to a data center being switched on before an application is ready. Would every cell have to be activated? Would the idle choice change the electricity bill? The instructor separates two questions. For the logical exercise, assume the two alternatives have exactly the same cost; nothing in the arithmetic favors either. For a real circuit, **current** and **voltage** are physical quantities, not the names of digits. A designer can distinguish two ranges of an electrical quantity and assign meanings to them. The assignment of names is arbitrary; the power required by a particular implementation need not be. The class's proposed 50% saving is a question, not a measurement or a universal property of zero.

At 1:12:27 this distinction suggests a possible connection to the **1200 CPU project**. In that class the processor is designed by connecting simple logic gates. No gate schematic is displayed here. The instructor imagines starting *after* the processor and its elementary instructions exist, then building the software tools to use it in problem solving. The proposal need not start by translating ordinary source code directly into a circuit. First ask what the existing processor can already do:

| Layer in the instructor's proposed chain | What it makes possible |
|---|---|
| Interacting gates | Arrange simple components so that bits move and combinations of inputs produce a useful result, such as a sum. |
| CPU instruction set | Select a small operation: move a value from here to there, add two values, or go to a different location. |
| Machine code and assembly mnemonics | The processor has encodings for its instructions; human-readable names let a programmer work with those same operations and write the first programs. |
| Higher-level language | Express a larger action in C or Python without spelling out every low-level step. One high-level action can require several lower-level operations; this is not a fixed one-to-one mapping. |

The teacher uses Python's readability and lack of the usual statement-ending semicolon as examples of what a programmer no longer has to manage in the same way as in C. Types still matter, as the three-, four-, and five-bit examples showed. No CPU was built, assembled, or benchmarked during this discussion; the linked hardware/software project is an idea for the future.

**Key items to scan**

- **Current** and **voltage** — possible physical carriers of distinguishable states. The logical key alone cannot predict idle power.
- **1200 CPU project** — the existing course design is the proposed starting point; building software tools around it is the suggested next step.
- **Instruction set** — the CPU's available primitive operations, illustrated by moving, adding, and changing location.
- **Machine code / assembly mnemonic** — the encoded operation versus the human-readable name for it; neither is a high-level statement.
- **C and Python** — examples of languages that let a person specify larger actions; abstraction does not eliminate representation and type decisions.

## 1:15:46 — Return to lines 16 and 17: which food pattern is five?

The two editor rows are mirror choices: pizza–lasagna–pizza on line 16 and lasagna–pizza–lasagna on line 17. The instructor actually types **`lazanga`**, not the conventional English spelling. A student chooses the second row, then says pizza is preferred. Instead of deciding by which word sounds preferable, mark every occurrence of that word and check the places. Both rows can be decoded with the same key; the question is which yields which number.

There is one more error to catch first. At 1:16:00 both selected rows still read **`10^2`**, **`10^1`**, **`10^0`**; the 1:17:00 frame still shows those tens. A student sees the hundreds place where a fours place ought to be. In the ensuing exchange the instructor acknowledges intending base two. The following is the *intended binary calculation*, independently verified, not a transcription of corrected rows at 1:17:00.

| Editor row / order of the marks | If pizza contributes the place | If `lazanga` contributes the place |
|---|---|---|
| 16: `pizza`, `lazanga`, `pizza` | 4 + 0 + 1 = **5** | 0 + 2 + 0 = **2** |
| 17: `lazanga`, `pizza`, `lazanga` | 0 + 2 + 0 = **2** | 4 + 0 + 1 = **5** |

The teacher's “it could be either” applies *before* the key is specified. With two alternatives, saying which one counts completely determines the other one. With ten decimal digits that declaration would leave eight other digit values unaccounted for even after identifying zero and one. The positional method still works in decimal; it is the one-preferred-symbol shortcut that is specific to two alternatives.

**Key items to scan**

- **`lazanga`** — the literal scratch spelling for the other food; its coefficient is zero if pizza is preferred, one if it is preferred instead.
- **`10^2`**, **`10^1`**, **`10^0`** — stale decimal weights on the two visible food rows. Use binary weights 4, 2, 1 for the classroom question.
- **Line 16 versus line 17** — pizza preferred gives five then two; reversing the key gives two then five. No terminal output is needed to decide.

## 1:18:00 — From whole places to fractional places: what does 5.75 mean?

Once the food-symbol key is agreed, read across the places. Changing the key swaps which row means five; it does not change the column weights. What does *not* change is the value of the column: in **`2^0`**, the base is two and the exponent zero identifies the ones place. The symbol in that place says whether to count it. This distinction matters even more when the instructor returns to **`5.75`**: “five point seven five” tells someone how to write it, but not yet why it means five and three quarters.

| Decimal place | Contribution in 5.75 |
|---|---|
| Ones, 10⁰ | Five ones contribute 5. |
| Tenths, 10⁻¹ | Seven tenths contribute 0.7. |
| Hundredths, 10⁻² | Five hundredths contribute 0.05. |

The last two contributions total 75 hundredths. A **negative exponent** means take the reciprocal of the corresponding positive power: 10⁻² is 1/100, not a negative number. The **radix point** is the mark separating whole places from fractional places in any base; “decimal point” names the base-ten case. A student asks whether memory actually stores the point. The instructor distinguishes writing a string of symbols, where the dot is a character, from encoding a number in bits, where the format can tell the reader where the point belongs without storing a dot. Both can represent 5.75; reading the bits requires knowing which representation was used.

**Key items to scan**

- **`2^0`** — the power labels the ones place; pizza, lasagna, 1 or 0 supplies the digit under an agreed convention.
- **`5.75`** — five ones, seven tenths and five hundredths: five and 75/100, or five and 3/4.
- **Negative exponent** — 10⁻¹ and 10⁻² give weights of 1/10 and 1/100.
- **Radix point** — explicit in the text `5.75`; its position can be implied by a binary number format.

## 1:21:32 — Build 5.75 in binary by testing one place at a time

The whole part is already known: five is one four, no twos and one one, or **`101`** in binary. What goes to its right? Simply replacing every 10 with a 2 in the decimal expansion leaves coefficients 5, 7 and 5, none of which is a binary digit. The instructor starts that substitution in the editor, then backs up: write down the *place weights* first and decide which ones the number actually needs. Moving right, each weight is half the one before it. In class the instructor initially says two squared is two; a student questions it, and the corrected column is four. “Four” is the familiar decimal name for the weight, not a digit one can write in binary.

| What the row means | Fours: 2² | Twos: 2¹ | Ones: 2⁰ | Halves: 2⁻¹ | Quarters: 2⁻² | Eighths: 2⁻³ |
|---|---:|---:|---:|---:|---:|---:|
| Weight in familiar units | 4 | 2 | 1 | 1/2 | 1/4 | 1/8 |
| 0.5: take the half; nothing remains | 0 | 0 | 0 | 1 | 0 | 0 |
| 0.25: skip the half; take the quarter | 0 | 0 | 0 | 0 | 1 | 0 |
| 0.75: take a half, then the remaining quarter | 0 | 0 | 0 | 1 | 1 | 0 |

The radix point belongs between the ones and halves columns. Starting with 0.75, half fits, so write 1 and subtract 0.5. A quarter fits the 0.25 still due, so write another 1 and subtract 0.25. The remainder is zero: every subsequent bit can be zero. The two parts therefore join as **`101.11`** in base two (scratch line 37 at 1:29:00). Reading it back gives 4 + 0 + 1 + 1/2 + 1/4 = 5.75. A student describes the mental hurdle: you can *say* “four” or “one half” in familiar units while the *digits you enter* still come from an alphabet of only zero and one.

**Key items to scan**

- **`101`** — count the four, skip the two, count the one; this is five before the binary point.
- **0.100₂ and 0.010₂** — the leading fractional 1 has different weights: half in the first row, quarter in the second.
- **0.110₂** — one half plus one quarter, no eighth; the final zero does not change the value.
- **`101.11`** — exact binary representation of 5.75; a zero remainder, not a chosen precision limit, ends the calculation.

## 1:29:21 — Try 0.7: the next place is not necessarily the last

One decimal digit was enough to write **`0.7`**. How many binary digits will it take? When someone initially says there is no half in seven tenths, the instructor brings the question back to magnitude: 0.7 is at least 0.5. At each place, compare the weight with the **remainder** (the part not yet accounted for). If it fits, write 1 and subtract it. If it does not, write 0, leave the remainder alone, and try the next smaller weight. A zero is a digit to keep, not permission to skip a column.

| Place tested | Remainder before | Decision; binary prefix | Amount still due |
|---|---:|---|---:|
| 1/2 = 0.5 | 0.7 | Fits: write 1 → 0.1₂ | 0.2 |
| 1/4 = 0.25 | 0.2 | Too big: write 0 → 0.10₂ | 0.2 |
| 1/8 = 0.125 | 0.2 | Fits: write 1 → 0.101₂ | 0.075 |

These are the classroom steps, checked with exact fractions. At 1:31:00 the three scratch lines show `0.7`, `0.101`, and `.075`. The middle line is the *binary prefix*, worth 0.625 in decimal, not the finished conversion. The last line is the *decimal remainder*, not more digits to append. The class names the sixteenths place but does not finish the expansion; the instructor says they picked seven tenths without first checking whether it terminates.

*Independently derived continuation, not classroom output:* 1/16 = 0.0625 fits into 0.075, giving 0.1011₂ and leaving 0.0125. Neither 1/32 nor 1/64 fits; 1/128 and 1/256 both do. The eight-bit prefix is 0.10110011₂ = 0.69921875, still 0.00078125 short. In fact the exact expansion is **0.1(0110)₂**, where the parentheses mean repeat `0110` forever. A finite binary fraction with *n* places is an integer divided by 2ⁿ. Reducing it can only remove factors of two; it cannot leave the factor of five in the denominator of 7/10. That is why no finite extension can make the remainder zero here, whereas 3/4 fits in two places.

**Key items to scan**

- **`0.7`** — seven tenths, not seven hundredths and not the 0.75 from the earlier example.
- **Remainder** — 0.2 after taking the half; still 0.2 after rejecting the quarter; 0.075 after taking the eighth.
- **0.101₂** — classroom stopping point on screen; it accounts for 0.625, not all of 0.7.
- **0.1(0110)₂** — independently checked exact expansion of 7/10; any finite prefix leaves an error.

## 1:31:12 — When a small representation error matters

If writing 0.7 exactly takes infinitely many bits, what does the machine do when it runs out of room? A numeric type provides a finite representation. Its numerical **precision** is the amount of significant detail it can retain; its **range** is how large or small a magnitude it can represent. Extending a fraction from three bits to eight retains more detail about the same value. In the hand calculation, **truncation** means stopping and dropping all the places beyond the cutoff. **Rounding** is different: it selects a representable value according to a rule, so the result need not be the lower prefix we wrote on the board. The class uses truncation to explain lost precision; this is not a claim that every floating-point operation simply chops off the remaining bits.

A student asks if Python has a configurable limit or an allocation default. The instructor answers at two levels: a machine has finite memory, and a floating-point format specifies how its available bits are interpreted. **IEEE 754** is not a Python-only convention. In the on-screen *IEEE 754 Encoding & Decoding* example at 1:34:00, the input is still 5.75, **not** 0.7. It shows a positive sign bit of 0, integer part `101`, fractional part `0.11`, and the form ±1.fraction × 2ᴱ. The eight-bit stored exponent `10000001` means 129; subtracting the **bias** of 127 recovers E = 2. As a check on the point-position question, moving the point in 101.11₂ two columns left gives 1.0111₂; multiplying by 2² restores 101.11₂. The visible 23-bit fraction heading describes this example's format, not a measured limit for a Python installation.

Why care about the last few bits? A student contrasts scaling a recipe with choosing a language for astronomical calculations. Doubling one recipe, halving another or converting units involves a few operations and then a fresh problem. A data analysis may instead combine millions of ordinary values to form an average; earlier approximations can affect later operations. Errors can accumulate, though not every error has the same sign or grows to a significant size. Very large astronomical quantities or very small quantities in quantum work are another reason to check whether the type covers the range and detail needed. The instructor advises learning which language, library and numerical procedure actually produce the result: agreeing on a representation does not make all algorithms or libraries identical.

The banking analogy at 1:37:14 separates a displayed cent balance from an interest calculation that can carry more decimal places internally. Discarded fractions of a cent can matter across many accounts. The instructor presents this as an illustration and does not recall the precise historical details; the exchange establishes neither a bank's rounding policy nor the cause of a rocket failure mentioned in passing.

**Key items to scan**

- **Precision** — how closely a finite set of bits can distinguish nearby values; eight fractional places approximate 0.7 more closely here than three.
- **Range** — whether the magnitude itself fits, a separate question for astronomical or quantum-scale values.
- **Truncation** — retaining only 0.101₂ drops the positive 0.075 remainder in this hand calculation.
- **Rounding** — choose a nearby representable value according to a rule; not necessarily the same as taking a prefix.
- **IEEE 754** — shared conventions for floating-point representation and arithmetic; the app illustrates one format, not Python's memory allocation or a settings dialog.
- **Bias** — the app's stored exponent 129 is offset by 127: the actual power of two is 2.
- **Recipe, average, interest** — a few independent calculations, many combined values, and different displayed versus internal precision are three distinct error questions.

## 1:38:33 — Choose the measurement for the decision it must support

A student proposes a plant monitor: a camera supplies pictures, an Arduino supplies measurements, Python gathers them, and Hermes sends a package to a language model. The hoped-for answer is practical: does the plant need water, or might humidity or pH be wrong? If a picture is reduced to four squares, a blob might be enough to detect that a plant is present. The same picture may not distinguish a healthy leaf from a damaged one. The student connects this to the cost of model calls and the difference between a model used for recipes or a picture book and one used for scientific images. This is a project and analogy, not an on-screen model test: image detail, a model's specialization and numerical precision are different things. Price alone does not establish any of them.

The instructor adds a question for anyone delegating the work to an agent: which language and libraries did it actually use, and what did those choices do to the result? A reassuring answer about a salad would not establish that a system can spot a tiny defect in a plate leaving a factory. Decide what change you must detect before judging whether the output is good enough.

At 1:42:50 the discussion moves upstream of the software. An **analog-to-digital converter (ADC)** turns a sensor signal, such as a voltage corresponding to light, distance or temperature, into one of a finite set of digital codes. **Resolution** asks how small a step those codes can distinguish; **accuracy** asks how close the reading is to the actual quantity. A room-temperature reading for a hobby project need not distinguish the same small deviations as a laboratory or manufacturing-line measurement. If the sensor signal uses only part of the converter's input span, only part of the available code range is used. This is the point of the 3 V/5 V discussion, not a blanket wiring recommendation.

A device could report the same wrong temperature on every trial. That would show **repeatability** (agreement under the same conditions) without accuracy. Extra displayed decimal places would not fix the error either. The instructor closes with a cost question: does the task require tenths or hundredths, and what precision, accuracy and repeatability are worth paying for? A more detailed computation downstream cannot recover detail the sensor never distinguished.

**Key items to scan**

- **Plant monitor** — a four-square blob may answer “present?” without answering “healthy?”; judge the data against the question.
- **Agent/library choice** — find out what performed the calculation before relying on a scientific or inspection result.
- **Analog-to-digital converter (ADC)** — maps the incoming signal to codes; the sensor's signal span determines how much of the available range gets used.
- **Resolution** — a converter suited to tenths may miss a change that must be detected at hundredths.
- **Accuracy** — closeness to the true temperature, not the number of digits displayed.
- **Repeatability** — repeated readings can agree with one another while all are wrong by the same amount.

## 1:45:01 — From a number in a text box to a calculation

At **`5.75`**, the encoder shows two easy-to-check pieces: 5 is **`101`** in binary, and three quarters is **`0.11`**. A recipe might tolerate a small change in an ingredient; a numerical application in another domain may not. To tell whether an approximation matters, first find out **what number the program actually stores**. The instructor opens the Kivy program behind the display and offers to share these tools in a course repository on request. That is an offer, not confirmation of an upload.

The window is assembled before it does any arithmetic. Its title and input row stay put while the long explanation below them scrolls. At 1:48:00 the input contains `5.75` above a blank output area: creating the field with default text does not itself create an explanation.

**Annotated teaching version — guide-added comments; abridged Kivy layout, not a second classroom run.** The miniature below echoes the input on a press so the wiring can be checked without running the encoder. The classroom callback calculates the report instead. The two sizing helpers are illustrated separately rather than guessing their clipped arguments.

```python
from kivy.app import App                         # Supplies the application life cycle.
from kivy.uix.boxlayout import BoxLayout         # Groups widgets into rows or columns.
from kivy.uix.label import Label                 # Displays the title and the report.
from kivy.uix.textinput import TextInput         # Holds the editable text, not a number yet.
from kivy.uix.button import Button               # Emits the press event.
from kivy.uix.scrollview import ScrollView       # Shows a movable window into a long report.

class IEEE754(App):
    def build(self):                             # Kivy asks for the widget tree at startup.
        rootLayout = BoxLayout(orientation='vertical', padding=15)
        title = Label(text='[b]IEEE 754 Encoding & Decoding[/b]',
                      markup=True, size_hint_y=None, height=40)
        rootLayout.add_widget(title)              # Keep a fixed-height heading above the input.
        inputLayout = BoxLayout(size_hint_y=None, height=50, spacing=10)
        self.input_text = TextInput(text='5.75', multiline=False,
                                    hint_text='Enter a decimal number')
        encodeBtn = Button(text='Encode')         # Creating it alone does not convert anything.
        encodeBtn.bind(on_press=self.calculate)   # Pass the method itself; do not call it now.
        inputLayout.add_widget(self.input_text)   # The entry and button share one row.
        inputLayout.add_widget(encodeBtn)
        rootLayout.add_widget(inputLayout)
        scroll = ScrollView()                     # Only the report needs to scroll.
        self.output = Label(markup=True, size_hint_y=None, halign='left', valign='top')
        # The original binds label width and texture-size changes here (below).
        scroll.add_widget(self.output)            # Keep a reference so calculate can replace text.
        rootLayout.add_widget(scroll)
        return rootLayout                         # Give the assembled tree to Kivy, not to print().
    def calculate(self, instance):                # Guide-only wiring check, not the original calculation:
        self.output.text = self.input_text.text   # Press Encode: echo '5.75' into the label.

if __name__ == '__main__':                       # Only launch when this module is the entry point.
    IEEE754().run()                               # Create one app and enter Kivy's event loop.
```

A long label needs both a wrap width and enough height to expose later lines to the scroll view. In the visible **`self.output.bind(...)`** fragment, a width change calls a **`lambda *x`** that sets `text_size`; a `texture_size` change calls another that sets `height`. The texture is Kivy's rendered text surface. The callbacks accept event arguments without needing names for each one. **Guide illustration, not recovered suffixes:**

```python
# In build(), after creating the label; sample right-hand values supplied by the guide.
self.output.bind(width=lambda label, w: setattr(label, 'text_size', (w, None)))
# On a width change, wrap at that width without prescribing the text's height.
self.output.bind(texture_size=lambda label, size: setattr(label, 'height', size[1]))
# When the rendered text grows, make the label tall enough to scroll to the bottom.
```

The instructor describes pressing Enter or Encode. The code shown explicitly binds **`on_press`** on the button; the single-line text input has no corresponding Enter binding in the recovered layout. Treat the two statements differently: the button path is established by code; Enter-triggered calculation is not independently established.

**Key items to scan**

- **`IEEE754(App)`** — the class supplies the screen; inherited `run()` starts the event loop.
- **`build()`** — returns the root layout, with a title, an input row and a scrollable report.
- **`TextInput(text='5.75', multiline=False)`** — starts with the characters `5.75` on one line.
- **`encodeBtn.bind(on_press=self.calculate)`** — schedule the callback for a press; parentheses after `calculate` would call it during setup instead.
- **`self.output.bind(...)`** — relates rendered report size to label dimensions; changes presentation, not the encoded bits.
- **`lambda *x`** — small unnamed event handler accepting the arguments Kivy supplies.

## 1:47:02 — Fail one input without losing the window

Enter **`5.75w`** and the output turns red: **`Error: Please enter a valid floating-point number.`** (1:48:12). The right side of the assignment must finish before Python can give `val` its new value. Converting those characters with **`float(...)`** raises **`ValueError`** at the trailing `w`; it does not return a partial `5.75`. The handler changes the existing label and returns control to the event loop. At 1:49:00 the screen again shows `5.75` with sign bit 0. By 1:50:00 it shows `-5.75`, sign bit 1, but the *same* magnitude pieces `101` and `.11`.

**Annotated teaching version — guide-added comments; callback opening with display strings shortened except the observed error.**

```python
def calculate(self, instance):                    # Kivy supplies the widget that fired the event.
    try:
        val = float(self.input_text.text)          # '5.75' -> 5.75; '5.75w' raises instead.
    except ValueError:
        self.output.text = '[color=ff0000]Error: Please enter a valid floating-point number.[/color]'
        return                                    # No sign calculation, packing, or crash on this input.
    steps = []                                    # A NEW report for this press, not last time's list.
    steps.append('Encoding process')              # Collect text; append does not display it yet.
    sign = 1 if val < 0 else 0                    # -5.75 -> 1; +5.75 -> 0.
    steps.append(f'sign = {sign}')                # Preserve sign before working on the magnitude.
    absoluteValue = abs(val)                      # Either signed input now gives 5.75.
    if absoluteValue == 0:
        steps.append('Value is zero.')            # Handle zero before trying to normalize it.
        self.output.text = '\n\n'.join(steps)      # One label update, with blank lines between steps.
        return                                    # Zero does not reach the ordinary packing path here.
    integerPart = int(absoluteValue)              # 5.75 -> 5; discard, do not round, the fraction.
    fractionPart = absoluteValue - integerPart    # 5.75 - 5 -> 0.75; use it in the next loop.
```

The handler catches a particular conversion error, not every conceivable numeric input. For example, the guide's separate check shows `-0.0 < 0` is false, although packed negative zero has a sign bit of 1: the early report is not an inspector for signed zero. The historical listing also shows the output label is eventually replaced on the ordinary path. None of these assignments prints a terminal result.

**Key items to scan**

- **`float(self.input_text.text)`** — interpret characters as a numeric value; don't silently drop a suffix.
- **`except ValueError`** — respond to the failed conversion at the point where it can be handled.
- **`steps = []`** — reset the explanation on each successful conversion; **`'\n\n'.join(steps)`** turns its parts into one report.
- **`abs(-5.75)`** — returns `5.75` without changing the signed `val` later passed to the packer.
- **`int(5.75)`** — returns `5`; subtraction retrieves `.75` for the fraction loop.

## 1:50:19 — Write the integer digits; generate the fraction digits

**`bin(5)`** gives the string **`'0b101'`**. Character 2 is only the first `1`; the slice **`[2:]`** keeps that character *and everything after it*, producing `'101'`. Indexes start at zero. An explicit end is excluded and a slice can stop beyond the available text: `'abcd'[2:5]` gives `'cd'`, not an error.

The right side of the radix point needs another operation. Doubling `.75` moves the next binary digit to the units position. Record that digit, subtract it, and repeat with **only the remainder**. This is what line 88 does: `f -= bit`, not “subtract one from bit.”

**Annotated teaching version — guide-added comments; numeric core independently checked.**

```python
integerBinary = bin(5)[2:]                # '0b101' -> '101'; slicing does not change the integer 5.
fractionBinary = []                        # Begin a fresh sequence of digit strings.
f = 0.75                                   # Copy the fraction into a working remainder.
for _ in range(24):                        # Allow at most 24 fractional places; ignore the counter.
    f *= 2                                # First: 1.5; second: 1.0.
    bit = int(f)                          # Each doubled remainder is below 2, so this is 0 or 1.
    fractionBinary.append(str(bit))       # Save '1', then another '1'; join needs strings.
    f -= bit                              # Remove the whole unit: 0.5, then 0.0.
    if f == 0:
        break                             # No remaining fraction; trailing zeros add no value here.
frac_str = ''.join(fractionBinary)         # '11'; the report puts '0.' in front to display 0.11.
```

| Pass | Remainder in | Doubled | Digit (weight) | Remainder out | Preview so far |
|---|---:|---:|---|---:|---|
| 1 | 0.75 | 1.5 | 1 (one half) | 0.5 | `0.1` |
| 2 | 0.5 | 1.0 | 1 (one quarter) | 0.0; break | `0.11` |

The ordinary loop uses **`range(24)`**: 24 opportunities, with counter values 0 through 23, not 1 through 24. **`_`** is an ordinary name used by convention for a value that is not needed. In response to a question about specifying a start or an increment, the editor temporarily shows `range(24:48)` and `range(24:48:2)`. Those colon forms are **not executable Python**; no classroom run of either edit is shown. Colons belong in slices; commas separate arguments to `range`. The original one-argument header is back at 1:54:30.

**Annotated teaching version — corrected alternatives; independently checked, not classroom runs.**

```python
list(range(24, 48))        # 24 through 47 inclusive: 24 counters, just like range(24).
list(range(24, 48, 2))     # 24, 26, 28, ..., 46: only 12 counters; 48 is excluded.
```

With the digit loop the counter itself is unused: changing the start does not skip fractional places. Changing the number of passes changes the *cap*. For `.75`, both alternatives still stop after two digits; for `.3`, a 12-pass cap gives `010011001100`, versus `010011001100110011001100` after 24. The invalid colon forms each raised `SyntaxError` in a separate compile check; they were not needed to produce any app output.

**Key items to scan**

- **`bin(5)[2:]`** — cut off `0b`, not the first two significant binary digits.
- **`range(24)`** — bounded work even when an exact mathematical expansion never terminates.
- **`f *= 2`** — bring the next fractional digit across the point; **`int(f)`** reads that digit.
- **`f -= bit`** — keep what is left after the digit has been accounted for.
- **`break`** — `.75` is done after two passes; a shorter preview is not a shorter IEEE storage word.
- **`''.join(fractionBinary)`** — assemble the collected strings in their original order, without separators.

## 1:56:31 — Read the actual 32-bit word, not the preview string

The program takes **two different paths** from the decimal input. One explains place values by repeated doubling. The other hands the original signed value to **`struct.pack('!f', val)`**, which produces a four-byte IEEE binary32 encoding. The displayed preview is not padded, normalized and fed into this call. It can end because the remainder vanished or because the loop reached its cap; the packed field always has a fixed width and can reflect **rounding**, not simple truncation.

**Annotated teaching version — guide-added comments; encoding lines follow the screen, normal-value decode is a separately verified teaching reconstruction.**

```python
import struct
val = -5.75                                # Keep the sign: do not pack absoluteValue.
packed = struct.pack('!f', val)            # Four bytes in network (big-endian) order: c0 b8 00 00.
integerField = struct.unpack('!I', packed)[0]  # Same bytes, read as unsigned integer 3233284096.
binaryValue = f'{integerField:032b}'        # Format as binary, padding with zeros to 32 places.
signBit = binaryValue[0]                   # '1' -> negative. This indexes text, not the float.
exponentBits = binaryValue[1:9]            # '10000001': eight places, excluding position 9.
mantissaBits = binaryValue[9:]             # '01110000000000000000000': 23 stored places.
biasedExponent = int(exponentBits, 2)      # Read the text in base 2 -> integer 129.
E = biasedExponent - 127                   # Remove the bias -> actual power of two is 2.
sig = 1 + int(mantissaBits, 2) / 2**23    # For this normal value: 1 + .25 + .125 + .0625.
recovered = (-1)**int(signBit) * sig * 2**E  # -1 × 1.4375 × 4 = -5.75; no printing implied.
```

**Independent result:** `5.75` packs to `40b80000`; `-5.75` to `c0b80000`. Only the highest bit changes. Reading `!I` does **not** numerically convert `-5.75` into an integer by dropping `.75`. It gives a convenient integer view of exactly the four bytes the encoder just chose. `unpack` returns a tuple; `[0]` takes its only member. The report names the 32-bit binary and hex forms and then describes decoding: extract sign, extract exponent, restore the leading digit, combine. The visible code collects these steps as text; the reconstruction above independently calculates the value rather than assuming what an off-screen string expression did.

Python's ordinary `float` on the checked interpreter has 53 binary digits of significand precision (usual IEEE binary64). This does not give the Kivy encoder 53 stored fraction bits. For binary32, its **1 sign + 8 exponent + 23 stored fraction bits = 32 total bits**. A normal significand has 24 significant binary digits including the unstored leading `1`. See the supplemental [Python `struct` reference](https://docs.python.org/3/library/struct.html#format-characters) and [IEEE single-format field table](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_math.html).

**Key items to scan**

- **`'!f'`** — choose a binary32 floating-point payload with its most significant byte first.
- **`'!I'`** — view those same four bytes as an unsigned integer; byte order stays the same.
- **`:032b`** — keep leading zeros so a positive sign bit does not disappear from position 0.
- **`[0]`, `[1:9]`, `[9:]`** — take one sign digit, eight exponent digits, then the 23-digit fraction from the fixed-width string.
- **`int(exponentBits, 2)`** — determines the stored exponent number, not the exponent after bias removal.

## 1:56:55 — How do you find a module you have never used?

A student asks how programmers would even know a module like `struct` exists, or find a module for analyzing features of a picture. The instructor enjoys browsing technical manuals but notes that even a standard library is too large to learn in one sitting. Start with the operation you need, find the relevant part of the API, and read outward as time permits. Here the useful question is “How do I pack a floating-point value into bytes of a specified width?”, not “What does the entire library contain?”

The instructor suggests using AI to **locate the official documentation**, not as the authority for the answer. A class participant describes confidently proposed fixes for door-reader/access-control systems and a supposedly quick Microsoft app setup whose practical work was understated. Their point is about verification: a generated instruction set can sound plausible without matching a real system, its prerequisites, or the work required. The editor stays on the callback during much of this exchange; a live visit to the docs is not shown.

**Key items to scan**

- **API** — the documented operation, its inputs and result; here, verify what `pack` and `unpack` do with `!f` and `!I`.
- **First-party documentation** — check the publisher's format table rather than treating a tutorial's interpretation or an AI assertion as the specification.
- **Small reproducible check** — use `5.75` with known binary digits before trusting the same operation on a value that is hard to inspect.

## 2:02:27 — Why `.5`, `.7`, `.3`, and a third look different

The student asks how the encoder finds the digits *after* the first useful place. A single fractional digit is not a decimal digit relabeled in base two. It says whether to include one particular weight: one half, one quarter, one eighth, and so on. The question leads from a half and a quarter to a decimal that needs many binary places. Read the input **and** the output label: at 2:03:00 `.7` has been typed, but the report below it still describes `-5.75`. The later `.7` report names `0.7` itself. Likewise `.33` appears at 2:06:00 above the earlier `.3` report.

| Confirmed screen state | Binary fraction preview in Step 2 | What changes |
|---|---|---|
| `.5`, 2:03:16 | `0.1` | The half uses just one digit, then the remainder is zero. Stored exponent 126 means −1. |
| `.7`, 2:04:00 | `0.101100110011001100110011` | A half fits, a quarter does not; the loop continues to its cap. Stored exponent still 126. |
| `.3`, 2:04:30 | `0.010011001100110011001100` | A half does not fit, a quarter does; the first `1` is one place farther right. Stored exponent 125 means −2. |
| A finite string of threes, 2:07:00 | `0.010101010101010101010101` | The preview alternates. A finite decimal input is near, but is not, the exact fraction one third. |

For `.7`, work with exact tenths to see *why* a pattern comes back. Doubling `7/10` gives `1 + 2/5`; the first digit is `1`. The following remainders are `4/5`, `3/5`, `1/5`, then `2/5` again. Those four passes emitted `0, 1, 1, 0`; the same remainder means the next four exact digits must repeat. For `.3`, the first doubled value is `3/5`, too small to emit `1`. Double again: `6/5` emits `1` and leaves `1/5`. This is how the missing fractional components are found, not guessed. **Independent binary32 check:** decoding the stored `.7` gives `0.699999988079071`; decoding `.3` gives `0.30000001192092896`. One rounded down and the other up.

The instructor suggests a trailing-zero display option; the frames do not show it implemented. Seeing only `0.1` for `.5` tells you nothing about the allocated word length. Nor does seeing 24 preview digits tell you that any subsequent mathematical digits would be zero. The preview stops; the number system doesn't.

**What changes with the base?** In decimal, no *finite* string of threes equals one third. In binary its exact expansion is `0.010101…`: multiplying `1/3` by two gives `2/3` (write `0`), then doubling that gives `4/3` (write `1`, leave `1/3`). In base three, the first place after the point is worth a third all by itself, so `(0.1)₃` is exact. One third is **rational in every base**: it is the ratio of integers 1 and 3. The classroom wording about becoming “irrational” describes a change in whether the digits *terminate*, not a change of number type. In lowest terms a fraction terminates in base two exactly when its denominator is a power of two; 3, 5 and 10 are not. Supplemental comparison: [Python's floating-point tutorial](https://docs.python.org/3/tutorial/floatingpoint.html).

**Key items to scan**

- **`0.1` in binary** — one half exactly; not decimal one tenth.
- **`.7` versus `.3`** — the leading binary one is in the half place for `.7`, the quarter place for `.3`.
- **Repeated remainder** — in exact arithmetic it guarantees the subsequent digits repeat.
- **`1/3`** — an exact rational number; `0.333…` is an infinite decimal expansion, while any finite run of threes falls short.
- **`0.1` in base three** — the same exact third written with one fractional digit.

## 2:08:28 — Store the scale once: why `.0075` has an exponent of 119 on screen

At 2:09:00 the input is **`.0075`**, not `.75`. Step 2 starts `0.000000011110101110000101` in binary. The first one is in the **eighth** fractional place. Dropping the seven zeros without recording their effect would change the value. Instead move the binary point to put that first one just before it and remember the scale: a significand near `1.92` in decimal units, multiplied by **`2**-8`**. In the field the instructor types `.0075` alongside `7.5 x 10^-3` as a scratchpad. That is its decimal scientific notation, not a calculation the GUI accepted or a binary exponent of −3.

| Encoding question | `.0075` in the normal binary32 case |
|---|---|
| Where does the first `1` have to go? | Between `2⁻⁸ = 0.00390625` and `2⁻⁷ = 0.0078125`, so the binary exponent is −8. |
| Why not store that `1`? | Every nonzero normal binary significand begins `1.`; restore the known digit on decode and use 23 physical places for the following bits. |
| What does an eight-bit unsigned field store? | Add 127 to −8: 119. Written in binary that is `01110111`. No separate exponent-sign bit is needed. |
| What did the report actually show? | Step 4: `119 - 127 = -8`. Step 5: normalized fraction `11101011100001010001111`. These are not the leading-zero digits of Step 2. |
| What value do the stored bits recover? | Independent check: word `3bf5c28f` decodes to `0.007499999832361937`, near the input, not exactly the decimal fraction. |

The **exponent** sets the *range* by moving the point. The **significand** sets the *precision* by keeping a limited number of significant binary digits at that scale. The app calls the 23 stored digits “Mantissa / Fraction”; for a normal value the whole significand is `1.` followed by those digits. Shifting the point does not buy arbitrary accuracy; it avoids using significant places to store predictable leading zeros.

**Supplemental boundary of the rule, not additional classroom runs.** An eight-bit exponent field can hold 0 through 255, but only codes 1 through 254 mean ordinary normal exponents (−126 through +127). A zero exponent field with a zero fraction encodes signed zero; with a nonzero fraction it describes a *subnormal*, whose leading digit is 0 and scale is `2⁻¹²⁶`. An all-ones exponent with a zero fraction represents infinity, and with a nonzero fraction represents NaN. Thus “put the one back” and “subtract 127” are not a complete decoder for arbitrary 32-bit patterns. The teaching app specifically short-circuits zero; no general special-value handler is established by the inspected callback. See the supplemental [IEEE arithmetic format table](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_math.html).

**Key items to scan**

- **`.0075 = 7.5 × 10⁻³`** — decimal scientific notation; a different base gives a different exponent.
- **Normalization** — express the magnitude as a significand starting with one times a power of two, when the value is normal.
- **`01110111`** — unsigned field 119; removing the bias gives the binary exponent −8.
- **Implicit leading bit** — one useful digit not physically stored for a normal value; it is not always one for special encodings.
- **Range versus precision** — the exponent chooses a scale; the fraction sets how closely spaced the values are on that scale.

## 2:15:17 — Measure the distance to the next number

“How small is the last place?” needs a **value and a format**. The roadmap points to `ulp.py`; a separate window, **Advanced ULP Demonstrator & Magnitude Comparator**, offers presets, a magnitude slider, and a custom number field with **Update**. Its analysis uses Python `math.ulp(x)`, not the previous app's `!f` packer. For these positive examples, one **ULP** (unit in the last place) is the upward spacing between the chosen number and the next representable float. A bit in its spelling can be zero; what is nonzero is the *weight* of that place.

| Comparator output (observed and independently matched) | At `1.0` (2:16:18) | At `1000000000.0` (2:16:49) |
|---|---|---|
| Previous float | `0.9999999999999999` | `999999999.9999999` |
| Next float | `1.0000000000000002` | `1000000000.0000001` |
| `math.ulp(x)` = next − x | `2.220446049250313e-16` | `1.1920928955078125e-07` |
| Relative spacing, ULP / x | about `2.22e-16` | about `1.19e-16` |

**Annotated teaching version — independently run Python check, not source code recovered from `ulp.py`.**

```python
import math
for x in (1.0, 1_000_000_000.0):              # Use the two confirmed GUI states.
    after = math.nextafter(x, math.inf)       # Move exactly one representable float upward.
    gap = after - x                           # Subtract x, not the previous-to-next distance.
    assert gap == math.ulp(x)                 # The two gaps match for these positive examples.
    print(x, gap, gap / x)                    # Absolute spacing versus a fraction of x.
```

At one, `2.220446049250313e-16` is **2⁻⁵²**, not 2⁻¹⁶. The `e-16` means a power of **ten**. In binary32 the next number above one is instead `1 + 2⁻²³`. In ordinary binary64 the absolute gap doubles when the binary exponent goes up by one; it is constant *within* each interval between successive powers of two. The relative gap stays of similar order, not exactly constant. That is why adding a tiny term at a large magnitude can leave the stored value unchanged even while the same term matters at a smaller magnitude. Independent example: `1_000_000_000.0 + 1e-8 == 1_000_000_000.0` is `True` on the checked interpreter. Supplemental reference: [Python `math.ulp`](https://docs.python.org/3/library/math.html#math.ulp).

**Key items to scan**

- **`math.ulp(1.0)`** — spacing above a binary64 one is `2.220446049250313e-16`; it is small, not zero.
- **`math.nextafter(x, math.inf)`** — fetch the actual neighbor rather than guessing a decimal digit to add.
- **`gap / x`** — compare precision across magnitudes without confusing a larger absolute gap with a proportionally larger error.
- **`ulp.py` versus `ieee754App.py`** — the comparator's binary64 numbers do not describe the earlier encoder's binary32 word.

## 2:17:03 — What would you do without the square-root key?

The instructor takes away the calculator and Python’s **`math.sqrt`**. A student offers 3 as the first target. “What do you square?” identifies the operation but uses the word being defined. The class’s second attempt gives a test you can actually perform: find a number which, multiplied by itself, gives 3. For the principal square root, look for the nonnegative number. Writing a fractional exponent names the answer, but doesn’t tell someone with only multiplication how to find it. Even perfect recall of √3 would not be a method for the next input.

So the instructor changes the target to **70**. Eight times eight is 64; nine times nine is 81. On the nonnegative numbers, increasing the number increases its square. Thus 64 < 70 < 81 proves that 8 < √70 < 9. This is a **bracket**: two endpoints known to be on opposite sides of the answer, even though neither is the answer. One student proposes 8.1 and reasons that 70 is nearer 64 than 81. That suggests where to look; it does not determine the tenths digit. Test the halfway guess, 8.5: its square is 72.25, not the predicted 73 or 74. Now the upper bound is 8.5. A separate arithmetic check gives 8.1² = 65.61 (too low) and √70 ≈ 8.3666002653. Those are checks of the discussion, not outputs from the guessing app.

**Key items to scan**

- **`math.sqrt`** — the library operation set aside so that “multiply the guess by itself and compare” has to do the work.
- **70** — the spoken target here; it is not the random target displayed in the next example.
- **Bracket** — 8 and 9 bound √70 because their squares straddle 70; testing 8.5 cuts that interval to (8, 8.5).

## 2:21:05 — Eight guesses for √69: keep what each square tells you

The Square Root Guesser now shows **`Target Random Number: 69`**. The number the student enters is a candidate *root*, not a candidate target. **`Submit`** records the guess and displays its square; a small square means to move the *guess* upward. “It’s not a number” would be misleading: √69 is a real number, just not a whole number or a terminating decimal. And although 69 is close to 64, its root is **above** 8, not below 8 as one later remark suggests.

At 2:21:30 the game has one row. At 2:22:00 it already has 8 and 9; the input field contains 8.2, but the latest square is still 81 and the counter is still 2. At 2:23:00 there are four submissions; at 2:24:00 there are eight. The first two columns below transcribe that eight-row history; the last column is the reasoning the game leaves to the player. Each interval is open because neither endpoint squares to exactly 69.

| Guess | Square shown | What can still contain √69? |
|---:|---:|---|
| 8 | 64 | Above 8. A lower bound alone doesn’t yet give an upper bound. |
| 9 | 81 | (8, 9). An upper bound is now established. |
| 8.2 | 67.24 | (8.2, 9). Raise the lower bound. |
| 8.3 | 68.89 | (8.3, 9). The square is only 0.11 low. |
| 8.4 | 70.56 | (8.3, 8.4). The root lies above 8.3 and below 8.4, so its tenths digit is 3. |
| 8.32 | 69.2224 | (8.3, 8.32). Too high despite being close. |
| 8.31 | 69.0561 | (8.3, 8.31). Lower the upper endpoint again. |
| 8.301 | 68.9066 | (8.301, 8.31). Raise the lower endpoint; its exact square is 68.906601. |

One student starts from the remainder above 64. That is useful if you account for what happens to *both* factors: (8 + h)² = 64 + 16h + h². The missing 5 is therefore 16h + h², not just h². For example, h = 0.3 adds 4.8 + 0.09 to 64, exactly the 68.89 in the history. This is a guide explanation of the suggestion, not a formula the student finished on screen.

Students also float 8.302 and roughly 8.305. Neither appears in the captured eight submissions. Independently squared, they give 68.923204 and 68.973025 respectively: both would narrow the interval from below. The independent reference value is √69 ≈ 8.3066238629. A student can keep naming finer digits indefinitely; a useful program needs a rule for picking the next value and a reason to stop.

**Key items to scan**

- **`Target Random Number: 69`** — compare every square with this fixed target, not with the last guess.
- **`Submit`** — typed 8.2 is not yet the third trial until the history and counter change.
- **`Guess`** and **`Square`** — 8.31 is a root candidate; 69.0561 is the value that proves it too high.
- **`Total Guesses: 8`** — the last certified row is 8.301 → 68.9066, not the later spoken suggestions.

## 2:24:41 — √10 in the terminal: bisect the root interval, not the squared error

The screen changes from the guessing game to a completed PowerShell command, **`py .\src\squareRoot.py`**. At 2:25:00 the output begins **`guess: 2.5, diff: 3.75`** and finishes **`x: 10, sqrt: 3.1622314453125`**. Here **`diff`** is 10 minus the *square* of the guess: 10 − 2.5² = 3.75. It is not the distance from 2.5 to √10. Positive means keep the guess as a lower bound; negative means keep it as an upper bound. This sign convention explains every change of direction in the output.

A student proposes “divide by two.” Once there are two bounds, halve the distance *between them*: the midpoint of 2.5 and 3.75 is 3.125. Dividing 3.75 itself by two would take you below the already established lower bound. The method is **bisection**. Its guarantee is that the *interval width* halves, not that each successive square is closer to 10. Compare trials 7 and 8 below: the square at trial 8 is farther away, yet the retained interval is narrower.

**Teaching reconstruction — guide-added comments, independently run.** One bracket and stopping rule that reproduce all 13 printed pairs are shown below. The instructor did not open the solver source; these initial bounds, variable names, and tolerance are not recovered classroom code. The setup is for this target and bracket, not a general-purpose square-root routine.

```python
lo, hi = 0.0, 5.0                # 0² < 10 < 5²; the first midpoint is 2.5.
target, tol = 10, 0.001          # Compare squared guesses with 10, allowing this residual.
while True:                     # Each pass tests one candidate root.
    guess = (lo + hi) / 2       # Halfway between the current ROOT bounds.
    diff = target - guess*guess # First residual: 10 − 6.25 = +3.75.
    if abs(diff) <= tol:        # A small miss in either direction is acceptable.
        break                   # Stop at guess 3.1622314453125 for this fixture.
    if diff > 0:                # The square is too small.
        lo = guess              # Raise only the lower endpoint; keep the upper one.
    else:                       # Outside tolerance and the square is too large.
        hi = guess              # Lower only the upper endpoint; keep the lower one.
print(guess)                    # This teaching print displays 3.1622314453125.
```

| Trial(s) | Printed guess(es), in order | Effect on the bracket (guide analysis) |
|---|---|---|
| 1–3 | 2.5 → 3.75 → 3.125 | Low, high, low. Now (3.125, 3.75); trial 3 leaves a positive residual of 0.234375. |
| 4–7 | 3.4375 → 3.28125 → 3.203125 → 3.1640625 | Four upper endpoints in succession. The lower endpoint stays 3.125. |
| 8–11 | 3.14453125 → 3.154296875 → 3.1591796875 → 3.16162109375 | Four lower endpoints in succession. The upper endpoint stays 3.1640625. |
| 12 | 3.162841796875 | High: residual −0.0035682320594787598. Keep the preceding lower bound. |
| 13 | 3.1622314453125 | Low, but residual +0.0002922862768173218 is small enough for the reconstructed test. |

**Key items to scan**

- **`py .\src\squareRoot.py`** — observed command for the *10* run, not the GUI for 69 or a run for 33.
- **`guess: 2.5, diff: 3.75`** — 2.5 is low because its square is 3.75 short of 10.
- **`diff`** — the signed residual; its sign directs the next bracket update, while its magnitude can be used for a separate stopping test.
- **Bisection** — use (lower + upper)/2; retain the side that still contains the root.
- **`x: 10, sqrt: 3.1622314453125`** — the program’s final report, not an assertion that the displayed number squared is exactly 10.

## 2:25:57 — How does a machine get the first bounds? The hidden 33

A student knows immediately that √69 lies between 8 and 9. Where would a program get that knowledge? The instructor makes the question harder: suppose a person plays the machine, and will tell the class only “high” or “low.” Students suggest 0, 3 and 9; one asks whether they are guessing the original number or its square root. That is the central distinction. For a proposed root of 3, the comparison is **3² against the target**, not 3 against the target. Seeing the instructor apparently draw a 3, or recalling a list of perfect squares, is information the program does not have unless someone supplies it.

A second student asks how an application could compare anything if its input variable had no value. It couldn’t. In the game the *guesser* does not know the target, but the person giving feedback does. A real solver can be passed the target and calculate the feedback itself. The jelly-bean raffle analogy makes the narrower point that a first guess gives you something to test; a random guess by itself carries no promise of success.

Once the instructor identifies the hidden target as 33, guessing 3 gives 9, so it is low. Halving 3 moves the wrong way. Double it to 6: 36 is **high**, as the instructor immediately corrects. From *these two tests* the bracket is (3, 6), not yet (5, 6). To establish the latter, add the fact that 5² = 25 < 33. √33 ≈ 5.7445626465 is an independent check, not a terminal result. Hearing “low by 24” gives more information than hearing “low”: it tells you how far the *square* missed, not that the root guess should be raised by 24.

What if 6 were still low for a different target? From a positive starting value, repeated doubling gives 3, 6, 12, … until a square crosses the target. If a first positive guess is too high for a positive target, repeated halving can find the other side. The zero suggestion deserves care: zero is a valid lower bound, but doubling it never moves it. A student proposes a random start followed by powers, or escalating after a trial counter reaches a limit. Those are strategies to compare, not timed results from this class. Repeated unrelated draws may never cross the target if the generator’s range is too small. Once a bracket exists, bisection offers predictable progress instead of hoping for a lucky draw.

**Key items to scan**

- **3² against the target** — 9 is below 33 even though the guess being revised is 3.
- **High/low feedback** — enough to pick a direction; it requires a real target or a comparison service that knows it.
- **3 → 6** — doubling tests a larger root candidate; 36 > 33 changes the response to “high.”
- **0 as a seed** — legitimate lower bound for positive inputs, but a bad starting point for repeated doubling.
- **24** — the residual 33 − 9, not a root correction of 24.

## 2:38:21 — Use prior knowledge for a start; use interpolation for the next guess

The instructor restores access to the target and separates two design decisions: how to *start*, and how to *correct* the estimate. A student suggests a table of perfect squares. Find 69 between the tabulated 64 and 81, then read their roots 8 and 9. The table supplies the bracket; it does not supply √69. You could then bisect, or ask the student’s next question: if 69 is nearer one end, can the next guess reflect that?

**Interpolation** uses how far the target lies between the two *squares*, rather than placing every guess halfway between the two *roots*. The instructor names this alternative without working the formula on screen. Here is an independently checked continuation of the class’s values:

| Calculation | What it does |
|---|---|
| (69 − 64)/(81 − 64) = 5/17 | Measure the fraction of the squared-value interval needed to reach 69. |
| 8 + (5/17)(9 − 8) ≈ 8.2941176471 | Move the same fraction of the way from root 8 to root 9. Its square is about 68.79239, so this guess is low. |
| 8.3 + (69 − 68.89)(8.31 − 8.3)/(69.0561 − 68.89) ≈ 8.3066225166 | Reuse the same idea with the narrower pair already supplied by the game. Its square is about 68.99997763, still low but much closer. |

The pairs (8, 64) and (9, 81) lie on a curve, not on a straight line. Interpolation treats the small piece between the points as straight when selecting a trial; squaring that trial tests the approximation instead of assuming it worked. The instructor invites students to try writing their thinking as a program after other work, not to submit a particular unseen implementation.

**Key items to scan**

- **Perfect-square table** — 64 and 81 provide an initial root interval without a long search from zero.
- **Interpolation** — use 5/17 of the distance from 8 to 9 for target 69; unlike bisection, the next trial need not be centered.
- **Error correction** — square the interpolated guess and recheck which bound can be replaced; a better prediction is still a prediction.

## 2:40:16 — Close enough for what? Residual, root error and float spacing

The class adds a second input to the proposed program: besides the target, specify a **tolerance**, or permitted miss. A student recalls the earlier rocket example: the consequences of being wrong determine how large a miss is acceptable. The instructor thinks the program stopped at something like a thousandth but does not show the code. The screen does show the last residual. For target 10, the last square misses by about 0.0002922863; the root guess misses √10 by only about 0.00004621486. Why two numbers? Factoring the difference of squares gives |10 − g²| = |√10 − g| × (√10 + g) for a nonnegative guess g. The two measures have different units. Say whether your tolerance applies to the squared result, the root value, or a bound on that value. A requirement to report to the nearest tenth is a requirement on the *reported root*, not a prescription that the squared residual must be at most 0.1.

Demanding more digits eventually meets a different limit. The **ULP** (unit in the last place) measures a step between nearby representable values at a particular magnitude. Recall the comparator at 2:17:00: it showed **`math.ulp(x)`** = 1.1920928955078125e-07 for x = 1000000000.0, with next float 1000000000.0000001. That absolute step is 2⁻²³; divided by a billion, the relative step is about 1.19 × 10⁻¹⁶. The later spoken “2 to the minus 16” is not the displayed absolute step, and neither that expression nor 10⁻¹⁶ is a universal absolute accuracy for square roots. Independently checked for Python’s binary64 float near √10, the spacing is 2⁻⁵¹ ≈ 4.44 × 10⁻¹⁶. An exactly representable result such as √9 = 3 needs no rounding error at all.

A tighter tolerance can require more iterations, but it cannot create a float between adjacent floats. Eventually rounding may make a calculated midpoint equal to an endpoint: the computed interval stops shrinking. In production, detect lack of progress or limit iterations, and distinguish that outcome from meeting the user’s tolerance. The classroom result was still far from that floating-point limit; its *stopping decision* explains the missing digits, not exhaustion of all representable guesses.

**Key items to scan**

- **Tolerance** — specify which quantity must be close and by how much. The last displayed residual is consistent with a 0.001 threshold; it does not prove that threshold.
- **Root error** — about 0.00004621486 for the printed √10 estimate, different from the residual of about 0.0002922863.
- **ULP** — local spacing, not the maximum error a particular application may accept.
- **`math.ulp(x)`** — at one billion the absolute upward gap is about 0.0000001192; the relative gap is about 1.19 × 10⁻¹⁶.

## 2:42:41 — Why not round every intermediate result?

A student connects the question to trigonometry: in a chain of calculations with arc lengths, angles and angular velocity, rounding one value before multiplying can change the final answer. The calculator’s own finite precision does not make early *extra* rounding harmless. Carry the available digits through the calculation, then round the result for its intended use. Conversely, the fact that some numerical error is possible does not by itself make an answer unusable. Ask whether that error is large enough to matter in the final quantity.

The instructor contrasts the angular velocity of a bicycle wheel with a planetary calculation. It is the decision the number will support—not simply that one object is larger—that determines the needed precision. A classroom rounding rule, an engineering acceptance limit, and a processor’s representable spacing answer three different questions. The instructor frames choosing a useful margin as a matter of human judgment: a program can enforce the chosen test, but the test has to fit the application.

**Key items to scan**

- **Intermediate rounding** — discarding digits in an angle *before* multiplying can carry that error into the velocity.
- **Application tolerance** — a bicycle estimate and an orbital calculation need not have the same acceptable error, even for the same kind of quantity.

## 2:45:24 — How many decimals of π does the measurement justify?

The instructor opens a saved copy of **How Many Decimals of Pi Do We Really Need?** The screen at 2:46:00 identifies NASA/JPL Edu and **Oct. 24, 2022**. That is the displayed article date, not the observation date for the distance. **Supplemental source check:** the [official JPL article](https://www.jpl.nasa.gov/edu/news/how-many-decimals-of-pi-do-we-really-need/) says the Voyager figures were updated March 4, 2026, and identifies 3.141592653589793 as the value used for JPL’s high-accuracy interplanetary navigation calculations. Neither the article header nor the saved copy establishes Voyager’s current position. The instructor mentions doing a related calculation in a later class. Here the class reads examples; it does not run a π-calculation program.

In the first example (2:47:00), Voyager 1 was about 16 billion miles away *at the time of writing*. The article imagines a circle with a deliberately generous **radius** of 20 billion miles, a distance it says Voyager would not reach until the **late 2030s**. The **diameter** is therefore 40 billion miles, not the circumference. Multiply diameter by π rounded to 15 places after the decimal and the circumference is a little over 125 billion miles. The useful question is how much length those missing digits of π could have changed, not whether π itself ever terminates.

**Independent check, not quoted article text:** holding that hypothetical diameter exact, 40,000,000,000 × 3.141592653589793 = 125,663,706,143.59172 miles. Comparing with a higher-precision calculation changes the result by about 0.00000954 mile, or 0.60 inch. This isolates the rounding of π; it does not assign sub-inch accuracy to a measured spacecraft distance.

Page 4, visible at 2:48:00, poses a *different* question. Take a circle of radius about 46 billion light-years; require the circumference to be accurate to the diameter of a hydrogen atom. The article says **37 decimal places**, or **38 total digits including the leading 3**, would suffice. This is an illustration of the enormous span of length scales that a finite precision can cover. It is not a claim that all NASA calculations use 37 places, that Voyager’s example required 37, or that this precision is a telescope manufacturing specification.

**Key items to scan**

- **How Many Decimals of Pi Do We Really Need?** — NASA/JPL Edu article used in class; cover dated **Oct. 24, 2022**.
- **Radius / diameter** — the Voyager-scale illustration uses 20 billion miles / 40 billion miles; it is hypothetical, not an exact measurement.
- **Late 2030s** — the timing in the article for reaching the hypothetical distance, rather than the spoken shorthand “2030.”
- **15 decimal places** — enough for the article’s Voyager-scale example; the numerical check isolates only π rounding.
- **37 decimal places / 38 total digits** — the separate 46-billion-light-year radius and hydrogen-atom diameter criterion, counting the leading 3 only in the total.

## 2:48:23 — Numerical methods and a real manufacturing question

The instructor contrasts neat symbolic textbook problems with problems he wanted to answer about a mechanical project or motorcycle. A symbolic formula would describe the result exactly *for its model*, but he could not always fit the actual problem to a convenient model with a useful closed-form solution. In his own airborne-industry work he recalls using numerical analysis, Monte Carlo analysis and finite element analysis regularly. That is a report about his work and an argument for teaching numerical reasoning earlier, not a claim that calculus is unnecessary. Today’s repeated square-and-compare method illustrates the distinction: you can get a defensible answer without first finding a new symbolic formula for the root.

The methods he names address different difficulties. **Numerical analysis** asks how to compute and assess approximations, including whether this root search converges and when it should stop. **Monte Carlo analysis** uses repeated random samples to study a quantity or the range of outcomes; it is not the same thing as randomly guessing a root and hoping to be right. **Finite element analysis** approximates behavior across a shape by dividing it into smaller pieces and relating their behavior. These brief definitions explain the names; neither Monte Carlo nor a finite element model is demonstrated here.

A student notices the **James Webb Space Telescope** reference on the NASA page and asks about allowable deviation. A small angular error in reflected light can translate into a large positional error over a distance, so polishing a surface to look smooth is not enough: what range of deviation would still allow it to do its job? The class discusses finding shareable, non-proprietary documentation, but supplies no numerical mirror specification or inspection result. The closing point is that even exceptionally precise manufacturing has **tolerances**: a part can be acceptable without being mathematically exact.

**Key items to scan**

- **Numerical analysis** — the √10 example tests an approximation and measures the miss rather than demanding an exact finite decimal.
- **Monte Carlo analysis** — repeated random sampling, named from the instructor’s practice; not the bisection algorithm.
- **Finite element analysis** — approximate a larger structure through connected smaller pieces; cited as experience, not run on screen.
- **James Webb Space Telescope** — the visible NASA link prompts the optical-deviation question; the article passage does not provide a mirror tolerance.
- **Tolerances** — manufacturing acceptance means within the required limits, not a claim of an exactly perfect surface.

## Syntax to remember

| Pattern | What to predict before you run it |
|---|---|
| `id(obj)` vs. `int(obj)` | For the live float `123.45`, the first gives its identity; the second returns the integer `123`. Neither changes the float. |
| `n & 0xff` | Keep the lowest eight bits. To extract a different byte, move the mask and shift the selected bits back down. |
| `bin(5)[2:]` | `bin(5)` gives `'0b101'`; the slice removes the prefix and keeps `'101'`. |
| `range(24, 48, 2)` | The step is 2; it starts at 24 and ends at 46. Commas separate arguments. Colons in a slice have a different job. |
| `struct.pack('!f', val)` | Encode the signed value as four bytes of binary32, most significant byte first. This is not a dump of the Python object. |
| `struct.unpack('!I', packed)[0]` | Read the same four bytes as an unsigned integer, then take that value out of the one-element tuple. |
| `math.nextafter(x, math.inf)` | Move from the current float to the next one upward. Subtracting the starting value gives the upward gap. |
| `target - guess*guess` | For the √10 run, a positive residual means the guess is low. It does not say to add that amount to the guess. |

## Knowledge check

1. A program splits an object's **`id()`** into eight bytes, rejoins them, and gets the starting integer. What has that test established? What has it *not* established about **`123.45`**?
2. Suppose pizza means 1, lasagna means 0, and the left-to-right weights are 4, 2, 1. Read *pizza, lasagna, pizza*. Now exchange only the meanings of the two foods. Why does the result change even though the symbols stayed put?
3. Start with the fractional part of **`5.75`**. Take two steps of the multiply-by-two procedure. What does each step keep, and why can the loop stop? Would the same stopping argument work for exact decimal **`0.3`**?
4. Normalize binary **`101.11`**. For a normal binary32 value with bias 127, what should the stored exponent be? Which field changes if you enter **`-5.75`** instead?
5. You know the square root of 70 lies between 8 and 9. Could you tell from that alone that 8.3 is good enough? What would you check before spending time on a better estimate?
6. The NASA example mentions 37 decimal places. Is that the number of places JPL says it uses to navigate spacecraft? Explain the difference.

### Answer key

1. The chosen byte order, splitting operation, and rebuilding operation preserved an **integer identity token**. A round trip would also work on a made-up integer. It neither encodes the float’s numerical value nor makes the token a portable way to retrieve an object after its lifetime.
2. With pizza as 1 the occupied places contribute 4 + 1 = **5**. With lasagna as 1 only the middle place contributes: **2**. Reversing the codebook is not the same as reversing the order of the bits.
3. Doubling 0.75 gives 1.5: record **1**, retain **0.5**. Doubling 0.5 gives 1.0: record **1**, retain **0**. Nothing remains, so **`.11`** is exact. In lowest terms 0.3 is 3/10; the denominator includes a factor of 5. No finite string of halves, quarters, eighths, and so on has that exact value. Stopping after a fixed number of bits there is a precision decision.
4. **`101.11₂ = 1.0111₂ × 2²`**. The true exponent is 2; the stored field contains 129, or **`10000001`**. The leading 1 of a normal binary significand is implicit. Negating this particular nonzero value changes the sign bit, not the exponent or the fraction.
5. No. Squaring 8.3 gives **68.89** in exact decimal arithmetic, so 8.3 is low. A bracket or residual tells you about error; the application's tolerance tells you whether that error matters. A bound on squared-value error is not automatically the same numerical bound on root error. Improving the guess is worthwhile only if the result can be represented and the application needs the improvement.
6. No. JPL gives **3.141592653589793** for its highest-accuracy interplanetary navigation calculations. The **37 places** (*38 total digits*, counting the leading 3) belong to a different thought experiment: computing a universe-sized circle's circumference to the diameter of a hydrogen atom. The physical scale and the error allowance determine the needed precision.[3]

## Library of definitions

| Term | Meaning in this lesson |
|---|---|
| Object identity | What distinguishes this particular live Python object from another. Its identity integer is not a serialized copy of the object's value. |
| Byte order | Which end of an integer is stored first when it is split into bytes. Reconstruction must use the same choice. |
| Conversion vs. reinterpretation | A conversion computes a value in another type, as **`int(123.45)`** does; reinterpretation reads a fixed pattern under another format. |
| Base (radix); place value | The base gives the ratio between neighboring place weights. In binary the first two places right of the point are one half and one quarter. |
| Bit; codebook | A bit distinguishes two states. A codebook says which symbol or physical state represents each choice. |
| Binary point | The boundary between whole-number and fractional places in base two. Moving it changes scale unless an exponent compensates. |
| Rational vs. irrational | A rational number is a ratio of integers. Its digits may repeat forever in a particular base. An irrational number, such as π, is not such a ratio. |
| Normalization; significand | Rewrite a nonzero normal binary value as **`1.fraction × 2^exponent`**. The significand includes the leading 1; the normal format need not store that 1. |
| Exponent bias | The offset between the exponent used to scale the number and the unsigned value stored in the exponent field. For a normal binary32 value, subtract 127 to recover the exponent. |
| Unit in the last place (ULP) | The place value of the least significant stored significand bit at a given scale. It is not one universal error allowance for all magnitudes. |
| Precision, accuracy, repeatability | Precision can mean available significant digits in a numerical format, or closeness among repeated measurements. Accuracy concerns closeness to the target; repeatability asks whether measurements agree under the same conditions. Extra displayed digits alone prove neither. |
| Resolution and dynamic range | Resolution distinguishes neighboring measurable levels; dynamic range concerns the span the instrument can use. A sensor that uses only part of a converter's input span leaves some levels unused. |
| Bracket, residual, tolerance | A bracket encloses the sought value. For a square-root guess, the residual compares its square with the input. A tolerance states the error the application will accept. |
| Heuristic; weight | A heuristic is a workable rule without an optimality guarantee. A weight says how strongly one preference counts when comparing possible schedules. |
| Alias; equality | Aliases reach the same live object. Two separately created objects may instead have equal values. |
| Callback | A function or method registered to run on an event. Binding the Encode button does not perform the conversion during window construction. |
| Bisection vs. interpolation | Bisection tries the middle of the root bracket. Interpolation uses the target’s position between measured squares to choose a different trial. |

**Source notes.** Timestamps throughout point to [the class recording](https://www.youtube.com/watch?v=n88OXtsO6ys).[1] For the distinction between an exact fraction and its stored approximation, see the supplemental [Python floating-point tutorial](https://docs.python.org/3/tutorial/floatingpoint.html).[2] The publisher's version of [*How Many Decimals of Pi Do We Really Need?*](https://www.jpl.nasa.gov/edu/news/how-many-decimals-of-pi-do-we-really-need/) independently confirms the two different π examples; the current page is not a substitute for the document visible in class.[3] For the lifetime guarantee and CPython-specific note on **`id()`**, see [Python’s built-in functions reference](https://docs.python.org/3/library/functions.html#id).[4]

## Appendix A · Historical screen record

These are transcriptions from stills, not runnable files. Gutter numbers identify editor lines; bracketed notes mark material not visible or deliberately omitted. Terminal and interface histories are labeled separately.

### Historical screen record · Roadmap and the π detour (0:26:00–0:28:00)

Observed editor notes, not a list of completed exercises. **`Roadmap.md`** at **0:27:00** (`t_01620s.png`) puts the identity program, a value-bytes program, the IEEE document and GUI next to one another. Lines 27–29 use a scalar input/output example and then a vector input/output example; they do not specify what function to implement. Gutter numbers below are not part of the text. Soft wraps are joined within their original numbered lines.

```text
 1  * kivySteps
 2  * binOp.py
 3  * truthTableWIP.py
 4  * byteOrder.py
 5  * base64, utf-8
 6  * jStruct.py
 7  * floatBytes.py
 8  * doc/IEEE Standard 754-2019.pdf
 9  ieee754App.py
10  ulp.py
11  doc/Accuracy of Mathematical Functions.pdf
12  doc/How Many Decimals of Pi Do We Really Need.pdf
13  < squareRootGuesser.py
14  - squareRoot.py
15  < zodiac.py piPolygon.py, 360 c/d .py,
16  - piCalc.py
17  < see below
18  poly2.py
19  ellipseScale.py
20  expScale.py
21  expSlopeAtOne.py
22  expSlope.py
23  poly.py
24
25  triangleScaler.py
26
27  Start simply with an independent variable "a" and a dependent variable "b".
28  We can think of b as a function of a: b = f(a).
29  We can think of putting a vector "A" of values into f and getting a vector "B" out.
```

At **0:28:00** (`t_01680s.png`), a PowerShell window shows an older numeric table above the line **`.\src\piCalc.py`**. It helps explain the π recognition; this is *not* output of the identity script. The column headings and the invocation that produced the table are not in this view. Selected first and last visible rows (spacing normalized):

```text
24     | 3.1326286133 | 3.1596599421 | 0.2610523844401031
196608 | 3.1415926453 | 3.1415926539 | 3.195793299683854e-05
393216 | 3.1415936698 | 3.1415926537 | 1.5978971709439223e-05
```

### Historical screen record · `jstruct.py`: integer round trip, first footer (0:28:40–0:33:00)

Assembled visual reconstruction, not one photographed file or a polished substitute. The Sublime tab reads **`jstruct.py`**, lower-case. In `t_01720s.png` (**0:28:40**), lines 1–30 are on screen, but line 2 extends beyond the editor's right edge. `t_01740s.png` (**0:29:00**) independently shows lines 4–33; `t_01800s.png` (**0:30:00**) supplies the overlapping lines 19–42. Their shared statements agree; this does not prove that every character outside the sampled views was unchanged. The longer note visible later is quoted separately.

```text
 1  """
 2  Note: The Python language specification only guarantees that id() returns[remainder not visible]
 3  """
 4
 5  import sys
 6  import ctypes
 7
 8  idSize = (sys.maxsize.bit_length() + 1)
 9  print(f"Python ID size: {idSize} bits")
10
11  octet = bytearray(int(idSize/8))
12  print(f"Bytes: {octet}")
13
14  obj = 123.45
15  objId = id(obj)
16  print(f"obj id: {hex(objId)}")
17
18  octet[0] = objId & 0xff
19  octet[1] = (objId & 0xff00) >> 8
20  octet[2] = (objId & 0xff0000) >> 16
21  octet[3] = (objId & 0xff000000) >> 24
22  octet[4] = (objId & 0xff00000000) >> 32
23  octet[5] = (objId & 0xff0000000000) >> 40
24  octet[6] = (objId & 0xff000000000000) >> 48
25  octet[7] = (objId & 0xff00000000000000) >> 56
26
27  print(f"Bytes: {octet}")
28
29  mem = octet[0]
30  mem |= octet[1] << 8
31  mem |= octet[2] << 16
32  mem |= octet[3] << 24
33  mem |= octet[4] << 32
34  mem |= octet[5] << 40
35  mem |= octet[6] << 48
36  mem |= octet[7] << 56
37  print(hex(mem))
38
39  [low-level address-to-object operation deliberately not reproduced]
40
41  print(back)
42  print(type(back))
```

Lines 14–16 select the **identity**, not the float's internal data. Lines 18–27 write one byte per slot; lines 29–37 reassemble exactly that integer. Line 39 is readable in the original, not cropped: it uses C interoperability to treat the rebuilt number as identifying an existing Python object and assigns the resulting object to **`back`**. Lines 41–42 ask only for its value and type. Nothing in this first footer checks whether the two names refer to different objects.

Observed PowerShell run, fully readable at **0:30:45** (`t_01845s.png`); also visible at **0:31:00**. Note the printable lowercase `l` after `\xad`. The bare script path below the output is a separate command, followed by a prompt with no intervening result text; it is not the invocation above the seven result lines.

```text
(Numerical) PS C:\com\joesta\Numerical> py .\src\jstruct.py
Python ID size: 64 bits
Bytes: bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00')
obj id: 0x1d9a06cadf0
Bytes: bytearray(b'\xf0\xadl\xa0\xd9\x01\x00\x00')
0x1d9a06cadf0
123.45
<class 'float'>
(Numerical) PS C:\com\joesta\Numerical> .\src\jstruct.py
(Numerical) PS C:\com\joesta\Numerical>
```

### Historical screen record · The diagnostic was edited before it was run (0:34:00–0:49:00)

Separate observations of the same editor footer, not one combined file. At **0:34:00**, line 43 is blank. At **0:34:40** (`t_02080s.png`) the text reaches the closing brace and parenthesis but lacks the closing string quote. The still does not show that draft being run:

```text
43  print(f"id obj: {id(obj)})
```

At **0:35:00** (`t_02100s.png`), line 43 is complete. At this moment line 44 still repeats the label `id obj:` and has an empty call. No error from this state is on screen.

```text
43  print(f"id obj: {id(obj)}")
44  print(f"id obj: {id()}")
```

At **0:36:00** (`t_02160s.png`), the second label *and* the argument are different. The earlier value and type prints remain above the new comparison; **0:37:00** and **0:38:00** confirm the same footer:

```text
41  print(back)
42  print(type(back))
43  print(f"id obj: {id(obj)}")
44  print(f"id back: {id(back)}")
```

The later terminal run is partly visible beside this footer and fully readable in older scrollback at **0:50:00**, `t_03000s.png`. It answers the test directly. Its identity and bytes differ from the first run; do not put these new decimal identity lines below the *first* run's hex number.

```text
(Numerical) PS C:\com\joesta\Numerical> py .\src\jstruct.py
Python ID size: 64 bits
Bytes: bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00')
obj id: 0x1eb898cadf0
Bytes: bytearray(b'\xf0\xad\x8c\x89\xeb\x01\x00\x00')
0x1eb898cadf0
123.45
<class 'float'>
id obj: 2111136640496
id back: 2111136640496
```

At **0:43:00** (`t_02580s.png`) the source note is readable with wrapping. The full text below belongs to this view; it recovers the subject of the earlier clipped line but does not prove that its off-screen suffix was identical earlier. Line 2 is one editor line, not five separate Python comments:

```text
 1  """
 2  Note: The Python language specification only guarantees that id() returns a unique and constant integer for the lifetime of the object. While CPython uses memory addresses, other implementations (like PyPy or Jython) may use arbitrary integer counters or garbage collector handles.
 3  """
```

At **0:48:45** (`t_02925s.png`), line 45 is blank; at **0:49:00** (`t_02940s.png`) its complete visible text is:

```text
45  int(obj)
```

This expression returns an integer; it neither assigns it nor prints it in a script. The existing terminal output ends in the identity comparison and does not demonstrate a run of line 45.

### Historical screen record · The next file, behind PowerShell (0:50:00)

In `t_03000s.png` a different editor document is behind the terminal. The opening triple quote and the next three gutter numbers are visible. The first field is named **`ob_refcnt`**; the byte sequence in that row is note text, not a fresh object dump in the foreground. Prefix transcribed through the last visible character at the right edge:

```text
 1  """
 2  Python Object:
 3  Offset Size Field Meaning
 4  0-7 8 bytes ob_refcnt (Reference Count)\x01\x00\x00\x00\x00\x00\x00\x00 -[remainder not visible]
```

The next two rows have hidden left sides and clipped right sides. Their field labels cannot be recovered here in full. These excerpts start at the first byte of each readable sequence and continue to the cropped right edge; no offset, field name or missing sentence ending has been supplied:

```text
[left portion omitted/obscured]`c\x0f#\xf9\x7f\x00\x00 -> Memory addr[remainder not visible]
[left portion omitted/obscured]\xaeG\xe1z\x14\xae(@ -> IEE[remainder not visible]
```

The last sequence contains eight bytes: **`ae 47 e1 7a 14 ae 28 40`**. Read as little-endian binary64, it gives **`12.34`** in a separate safe calculation. That verifies the interpretation of the *note's bytes*, not the identity, value or output of a new classroom run. Farther down are visible pieces of a size display, byte printing with an empty `end` string and masks involving `floatBytes[6]`. For example, the unobscured term **`((floatBytes[6] & 0xf0) >> 4)`** selects the high four bits of that byte and shifts them to the low end. The missing left sides conceal the full expressions and their assignment targets; this still is not a source for a reconstructed runnable program.

The completed prompt line is **`.\src\floatBytes.py`**. On the next line **`py`** is followed by the cursor and the visible path **`.\src\floatBytes.py`**. This still does not establish how much was typed versus suggested or show any result beneath it. The captioned proposal to run the GUI is later than this still. The visible identity run above the new prompt belongs to **`jstruct.py`**, not either float-value tool.

### Historical scratchpad — widths, weights, and two possible keys (0:52:00–1:17:00)

These are observed Sublime Text scratch states, not Python, a saved source file, or terminal output. Numbers left of the bar reproduce the editor gutter; the bars are editorial separators, not text to type. An empty numbered line was visibly blank. The open PowerShell window contains application startup messages (including Kivy), not an evaluation of the expressions. The caret, selection shading, and matching-text highlights are not characters. Source filenames below are requested-time PNGs.

**Padded then unpadded.** At 0:52:00 (`t_03120s.png`) only the first two lines below are present. At 0:53:00 (`t_03180s.png`) the editor shows this four-line state. Read the first numeral as decimal and the second as binary; the appended decimal example asks a different question about precision.

```text
1 | 00005
2 | 0000000101
3 |
4 | 5.00
```

At 0:54:00 (`t_03240s.png`) the numbered text is just `1 | 5` and `2 | 101`. At 0:54:30 (`t_03270s.png`) the successive widths are present:

```text
1 | 5
2 | 101
3 | 0101
4 | 00101
```

The later `5.00` appears on line 6 at 0:57:00 (`t_03420s.png`), not on line 4 of the width comparison. Line 8 reads `5.75` at 0:58:00 (`t_03480s.png`) and line 10 reads `575` at 0:59:00 (`t_03540s.png`). Neither one overwrites the other. By 1:01:00 (`t_03660s.png`) line 12 expands `575`; line 13 is still blank there.

**A change of base in progress.** At 1:02:00 (`t_03720s.png`) the final term on line 15 still has a ten. Carets here mean powers in arithmetic notation.

```text
12 | 5*100 + 7*10 + 5*1
13 | 5*10^2 + 7*10^1 + 5*10^0
14 |
15 | 5*2^2 + 7*2^1 + 5*10^0
16 |
```

At 1:03:00 (`t_03780s.png`), line 15 instead reads:

```text
15 | 5*2^2 + 7*2^1 + 5*2^0
```

Changing the weights alone does not assign binary values to the marks `5` and `7`. The class considers both assignments rather than evaluating them as the decimal coefficients five and seven.

**Two-state alphabets.** At 1:05:00 (`t_03900s.png`) the in-progress line 17 shows `0 1`, with the caret after `0`. At 1:06:00 (`t_03960s.png`), the comma-separated list reads:

```text
16 |
17 | 0, 1
18 | true, false
19 | yes, no
20 | rocks, potatoes
21 | on, off
22 |
```

**Food-labelled place values in progress.** At 1:08:00 (`t_04080s.png`), line 15 once again uses tens; the last coefficient on line 16 is still absent, not clipped. The five symbol pairs now occupy lines 18–22.

```text
15 | 5*10^2 + 7*10^1 + 5*10^0
16 | pizza*10^2 + lazanga*10^1 + *10^0
17 |
```

**Full later scratch state, 1:16:00 (`t_04560s.png`).** All 24 gutter lines below are from one original. Both food rows are already legible by 1:09:00 (`t_04140s.png`); their selection at 1:16:00 does not alter the text. `lazanga` is the editor's spelling throughout these rows.

```text
 1 | 5
 2 | 101
 3 | 0101
 4 | 00101
 5 |
 6 | 5.00
 7 |
 8 | 5.75
 9 |
10 | 575
11 |
12 | 5*100 + 7*10 + 5*1
13 | 5*10^2 + 7*10^1 + 5*10^0
14 |
15 | 5*10^2 + 7*10^1 + 5*10^0
16 | pizza*10^2 + lazanga*10^1 + pizza*10^0
17 | lazanga*10^2 + pizza*10^1 + lazanga*10^0
18 |
19 | 0, 1
20 | true, false
21 | yes, no
22 | rocks, potatoes
23 | on, off
24 |
```

Lines 1–10 keep count, width and measured fraction distinct. Lines 12–15 show ordinary decimal weights in this particular revision. Lines 16–17 exchange the two labels in every position; lines 19–23 give additional possible alphabets. At 1:17:00 (`t_04620s.png`) the cursor is back on line 16, but both food rows **still show powers of ten**. The class acknowledges the need for powers of two during the following discussion; the verified binary totals in the teaching section use that intended correction, not the literal tens in this frame.

### 1:20:00–1:33:00 — Numbered scratchpad: from 575 to 5.75 to an unfinished 0.7

The active Sublime Text plain-text tab is titled **`5`**. This is the instructor's handwritten-in-the-editor working, not an executable file, printed output or evidence of a save. The numbers left of the vertical bars reproduce the editor gutter; neither the bars nor those numbers are part of the text. The caret `^` denotes a power here. The spelling `lazanga` is on screen.

*Assembled visual reconstruction:* lines 1–15 come from `t_04800s.png` (1:20:00); lines 16–44 from `t_05580s.png` (1:33:00). The overlap at lines 16–26 agrees. The later frame does not establish whether off-screen lines 1–15 remained unchanged. Spaces in the column diagram represent visible alignment, not a claim about tabs or trailing spaces.

```text
 1 | 5
 2 | 101
 3 | 0101
 4 | 00101
 5 |
 6 | 5.00
 7 |
 8 | 5.75
 9 |
10 | 575
11 |
12 | 5*100 + 7*10 + 5*1
13 | 5*10^2 + 7*10^1 + 5*10^0
14 |
15 | 5*10^2 + 7*10^1 + 5*10^0
16 | pizza*2^2 + lazanga*2^1 + pizza*2^0
17 | lazanga*2^2 + pizza*2^1 + lazanga*2^0
18 |
19 | 0, 1
20 | true, false
21 | yes, no
22 | rocks, potatoes
23 | on, off
24 |
25 | 5.75
26 | 5*10^0 + 7*10^-1 + 5*10^-2
27 |
28 |
29 | 5*2^0 + 7*2^-1 + 5*2^-2
30 |
31 | 2^2 + 2^1 + 2^0 . 2^-1  2^-2  2^-3
32 | 4   + 2   + 1  .  1/2   1/4   1/8
33 | 0   + 0   +  0 .  1     0     0
34 | 0   + 0   +  0 .  0     1     0
35 | 0   + 0   +  0 .  1     1     0
36 |
37 | 101.11
38 |
39 | 0.7
40 | 0.101
41 | .075
42 |
43 |
44 |
```

- Lines 1–23 link leading zeros, decimal place values and alternative names for binary digits. Lines 25–26 move the decimal point relative to the same digits 5, 7 and 5. Line 29 preserves an abandoned setup; it is not the binary expansion of 5.75.
- Lines 31–35 are a chart, not equations to execute. Read each digit under its weight: the three rows select a half, a quarter, then both. Line 37 joins the earlier whole part to the fractional part.
- Lines 39–41 change bases *between* lines: decimal input, partial binary answer, decimal amount left. They do not show a completed expansion or a program result.

#### Earlier editor states: why the chart changed

These are separate observations of lines that changed while the instructor built the final chart. Blank lines and unchanged context are omitted here; line numbers refer to that particular frame, not the final listing above.

| Requested frame | Observed editor state | What changed |
|---|---|---|
| 1:22:00 · `t_04920s.png` | L29 `5*10^0 + 7*10^-1 + 5*10^-2` | A second copy of the decimal expansion; `10` selected. |
| 1:22:30 · `t_04950s.png` | L29 `5*2^0 + 7*2^-1 + 5*2^-2`; L33 `101.` | The base has changed but the coefficients have not. The separate whole-part prefix has no fractional digits yet. |
| 1:23:00 · `t_04980s.png` | L31 `2^2 + 2^1 + 5*2^0` | Mid-edit: an extra coefficient 5 remains in what becomes a row of place weights. |
| 1:24:00 · `t_05040s.png` | L32 `2   +`; L34 `101.` | Initial 2 under 2²; the spoken correction follows. |
| 1:24:35 · `t_05075s.png` | L32 `4   +  2   +  1  .` | Correct whole place values; the alignment is adjusted in the next minute frame. |
| 1:26:00 · `t_05160s.png` | L32 ends `1/2   1/4   1/8` | The chart now gives the values on both sides of the point. |
| 1:27:00, 1:27:30, 1:28:00 · `t_05220s.png`, `t_05250s.png`, `t_05280s.png` | L33, then L34, then L35 populated | The half, quarter and three-quarters rows appear in sequence; at 1:28:00 L37 still reads `101.`. |
| 1:29:00 · `t_05340s.png` | L37 `101.11` | Whole and fractional parts finally appear together. |
| 1:30:00 → 1:31:00 · `t_05400s.png`, `t_05460s.png` | L39 `0.7`; L40 `0.` becomes `0.101`; L41 `.075` | New input, partial answer and remainder. No repeating tail is typed here. |

### 1:46:28 — `ieee754App.py`: numbered visual reconstruction

Sublime Text shows `C:\com\joesta\Numerical\src\ieee754App.py`. The numbers before the bars are the editor gutter, **not Python to type**. The display is assembled from overlapping views of the same filename, not recovered from a source repository. The code below follows the ordinary `range(24)` state; the temporary edits are recorded separately. A bracketed marker starts exactly where readable text stops. A literal `[` can therefore appear immediately before the marker. The editor is too narrow to establish the endings of some settings and explanatory strings; the rendered GUI message cannot establish the exact syntax used to produce it.

#### Layout, event wiring, and scrollable label — lines 1–57

The top is visible at 1:46:28 and 1:46:55 and confirmed at 1:56:47. Lines 30–59 come from 1:46:47; lines 49–78 also appear at 1:47:00. Line 30 and the overlapping widget/return lines anchor the assembly. The `sp` font-size strings are UI settings; they do not assign numeric precision.

```text
  1 | from kivy.app import App
  2 | from kivy.uix.boxlayout import BoxLayout
  3 | from kivy.uix.label import Label
  4 | from kivy.uix.textinput import TextInput
  5 | from kivy.uix.button import Button
  6 | from kivy.uix.scrollview import ScrollView
  7 | import struct
  8 |
  9 | class IEEE754(App):
 10 |     def build(self):
 11 |         rootLayout = BoxLayout(orientation='vertical', padding=15, spaci[remainder not visible]
 12 |
 13 |         title = Label(
 14 |             text='[b]IEEE 754 Encoding & Decoding[/b]',
 15 |             markup=True,
 16 |             size_hint_y=None,
 17 |             height=40,
 18 |             font_size='28sp'
 19 |         )
 20 |         rootLayout.add_widget(title)
 21 |
 22 |         inputLayout = BoxLayout(size_hint_y=None, height=50, spacing=10)
 23 |         self.input_text = TextInput(
 24 |             text='5.75',
 25 |             multiline=False,
 26 |             font_size='26sp',
 27 |             hint_text='Enter a decimal number'
 28 |         )
 29 |         encodeBtn = Button(
 30 |             text='Encode',
 31 |             background_color=(0.1, 0.6, 0.8, 1),
 32 |             font_size='26sp'
 33 |         )
 34 |         encodeBtn.bind(on_press=self.calculate)
 35 |
 36 |         inputLayout.add_widget(self.input_text)
 37 |         inputLayout.add_widget(encodeBtn)
 38 |         rootLayout.add_widget(inputLayout)
 39 |
 40 |         scroll = ScrollView()
 41 |         self.output = Label(
 42 |             size_hint_y=None,
 43 |             markup=True,
 44 |             font_size='24sp',
 45 |             halign='left',
 46 |             valign='top'
 47 |         )
 48 |
 49 |         self.output.bind(
 50 |             width=lambda *x: setattr(self.output, 'text_size', (self.out[remainder not visible]
 51 |             texture_size=lambda *x: setattr(self.output, 'height', self.[remainder not visible]
 52 |         )
 53 |         scroll.add_widget(self.output)
 54 |         rootLayout.add_widget(scroll)
 55 |
 56 |         return rootLayout
 57 |
```

Lines 13–20 place a bold title using Kivy markup. Lines 22–38 store the entry on the app object, bind the button press to the calculation, then put both widgets into the root. Lines 40–56 configure a left/top-aligned report, attach property-change helpers, put it in a scroll view and return the entire layout. The input constructor, the press binding and the intervening code are visible; an Enter-to-calculate binding is not.

#### Callback entry, input error, zero guard and preview — lines 58–94

The opening is visible at 1:47:00 and 1:48:30; the broader viewport at 2:02:00 supplies the readable right-edge prefixes of lines 62, 66, 69 and 73. Lines 73–102 appear at 1:50:00 and 1:51:00. Their overlap fixes the indentation and the separation between the zero return and the ordinary conversion.

```text
 58 |     def calculate(self, instance):
 59 |         try:
 60 |             val = float(self.input_text.text)
 61 |         except ValueError:
 62 |             self.output.text = '[color=ff0000]Error: Please enter a vali[remainder not visible]
 63 |             return
 64 |
 65 |         steps = []
 66 |         steps.append("[b]=== ENCODING PROCESS (Base 10 -> IEEE 754) ===[[remainder not visible]
 67 |
 68 |         sign = 1 if val < 0 else 0
 69 |         steps.append(f"[b]Step 1: Sign Bit (S)[/b]\n- Value is {'negativ[remainder not visible]
 70 |
 71 |         absoluteValue = abs(val)
 72 |         if absoluteValue == 0:
 73 |             steps.append("Value is zero. IEEE 754 representation consist[remainder not visible]
 74 |             self.output.text = "\n\n".join(steps)
 75 |             return
 76 |
 77 |         integerPart = int(absoluteValue)
 78 |         fractionPart = absoluteValue - integerPart
 79 |
 80 |         integerBinary = bin(integerPart)[2:]
 81 |
 82 |         fractionBinary = []
 83 |         f = fractionPart
 84 |         for _ in range(24):
 85 |             f *= 2
 86 |             bit = int(f)
 87 |             fractionBinary.append(str(bit))
 88 |             f -= bit
 89 |             if f == 0:
 90 |                 break
 91 |         frac_str = "".join(fractionBinary)
 92 |
 93 |         steps.append(f"[b]Step 2: Binary Conversion[/b]\n- Integer part[remainder not visible]
 94 |
```

At 1:48:12 the foreground app displays the entire **rendered** error, `Error: Please enter a valid floating-point number.`, for `5.75w` (lowercase `w`). The later editor partly covers this message. On the ordinary path the preview is built from the magnitude; the sign is handled separately. At 1:50:00 `-5.75` produces sign 1 with integer `101` and fractional `0.11`. Line 88 subtracts the extracted integer from the doubled remainder. Line 91 joins the collected character digits without separators.

#### Temporary edits to line 84 — do not splice these into the ordinary listing

| Requested frame | Exact visible editor line (ignoring indentation here) |
|---|---|
| 1:53:00 | `84  for _ in range(24):` |
| 1:54:00 | `84  for _ in range(24:48):` |
| 1:54:14 | `84  for _ in range(24:48:2):` |
| 1:54:30 | `84  for _ in range(24):` |

The two colon forms appear during an explanation of start, stop and increment. They are edit states, not runs or syntax-error tracebacks. The guide's separate compile check rejects them; the intended comma-separated ranges and their different iteration counts are illustrated in the lesson.

#### Four-byte encoding, decoding report and driver — lines 95–121

Lines 95–104 are visible across 1:50:00, 1:52:00 and 1:57:00; the upper lines of the decoding report are visible at 1:57:00. Crucially, the earlier 1:46:37 event shows the bottom of this file, including lines 118–121; the bottom is **not** inferred from a generic Kivy template. The overlapping format, slice and exponent lines support the assembly, not an assertion that a later buffer was saved or rerun.

```text
 95 |         packed = struct.pack('!f', val)
 96 |         integerField = struct.unpack('!I', packed)[0]
 97 |         binaryValue = f"{integerField:032b}"
 98 |
 99 |         signBit = binaryValue[0]
100 |         exponentBits = binaryValue[1:9]
101 |         mantissaBits = binaryValue[9:]
102 |
103 |         biasedExponent = int(exponentBits, 2)
104 |         unbiasedExponent = biasedExponent - 127
105 |
106 |         steps.append(f"[b]Step 3: Normalization & Scientific Notation[/b[remainder not visible]
107 |         steps.append(f"[b]Step 4: Unbiased Exponent[/b]\n- Exponent E =[remainder not visible]
108 |         steps.append(f"[b]Step 5: Mantissa / Fraction (23 bits)[/b]\n- N[remainder not visible]
109 |
110 |         steps.append(f"[b]Step 6: Final 32-bit Binary & Hex Representati[remainder not visible]
111 |
112 |         steps.append("[b]=== DECODING PROCESS (IEEE 754 -> Base 10) ===[[remainder not visible]
113 |         steps.append(f"1. [b]Extract Sign Bit:[/b] {signBit} ->multiplie[remainder not visible]
114 |         steps.append(f"2. [b]Extract Exponent Bits:[/b] {exponentBits} =[remainder not visible]
115 |         steps.append(f"3. [b]Reconstruct Mantissa:[/b] Implicit leading[remainder not visible]
116 |         steps.append(f"4. [b]Compute Final Value:[/b] (-1)^{signBit} × 1[remainder not visible]
117 |
118 |         self.output.text = "\n\n".join(steps)
119 |
120 | if __name__ == '__main__':
121 |     IEEE754().run()
```

Lines 95–97 use `val`, not `frac_str`. They yield and then format the unsigned integer view of the packed four bytes. Lines 99–104 divide the 32-character word into fields and remove the ordinary exponent bias. Lines 106–116 build explanatory output strings: normalization, unbiased exponent, fraction, final binary and hex, then the reverse interpretation. No independent binary-input widget is visible here. Line 118 replaces the existing label in one operation. Lines 120–121 launch an instance only when the file is run as the main module. No terminal invocation is inferred from that guard.

#### A separate tool, not a second version of the encoder

At 2:16:00 the open `Roadmap.md` identifies the lesson's standard, encoder and upcoming tool; these are **roadmap entries**, not executable statements or evidence that the listed documents were opened at this point.

```text
  8 | * doc/IEEE Standard 754-2019.pdf
  9 | ieee754App.py
 10 | ulp.py
 11 | doc/Accuracy of Mathematical Functions.pdf
 12 | doc/How Many Decimals of Pi Do We Really Need.pdf
```

At 2:16:18 the separate ULP comparator reports `0x1.0000000000000p+0` for one. At 2:16:49 it reports `0x1.dcd6500000000p+29` for one billion, next-minus-current `1.1920928955078125e-07`. These are observations of the running comparator, not a recovered listing of `ulp.py` or output from `ieee754App.py`.

### 2:18:00 — Historical listing 6.1: numerical roadmap, not solver source

Sublime Text shows **`C:\com\joesta\Numerical\doc\Roadmap.md`**. The file is an agenda and scratch note. Lines 1–12 name tools and readings; lines 13–25 place the guessing game and the numerical solver among other examples. Lines 27–29 distinguish an independently supplied value **`a`** from a result **`b = f(a)`** and suggest passing a vector of inputs to obtain a vector of outputs. The note does not establish that any of the listed future examples ran during this discussion.

Observed numbered editor lines, `t_08280s.png` through `t_08460s.png`; right edge of the wrapped sentences checked in `t_08340s.png`. The gutter is not text to enter. Lines 27 and 29 each occupy two display rows; they are rendered below as single logical lines. At 2:18:00 the caret is at the end of line 13. At 2:43:00 it is on line 25; this is not evidence that the file on that line was executed.

The visible 29 agenda lines match the earlier roadmap listing in this appendix. In particular, lines 13–14 name the guessing game and solver; neither line contains their implementation.

### 2:21:30 — Historical listing 6.2: Square Root Guesser, recorded interface states

This is GUI history, not Python or terminal output. The fixed target is 69; the latest-square field and count change as guesses are submitted. Alignment below is editorial. The interface also shows **`Enter Guess:`**, **`Submit`** and **`Reset Game`**; no reset is supported by these frames.

| Target time / source | Input and history state |
|---|---|
| 2:21:30 / `t_08490s.png` | Blank entry; `Square of Guess: 64`; `Total Guesses: 1`. |
| 2:22:00 / `t_08520s.png` | `8.2` in entry; `Square of Guess: 81`; `Total Guesses: 2`. Only 8 and 9 are in the history. |
| 2:23:00 / `t_08580s.png` | Entry blank; `Square of Guess: 68.89`; `Total Guesses: 4`. |
| 2:24:00 / `t_08640s.png` | Entry blank; latest square 68.9066, eight recorded guesses, as transcribed below. |

```text
Target Random Number: 69
Square of Guess: 68.9066
Total Guesses: 8

Guess     Square
8         64
9         81
8.2       67.24
8.3       68.89
8.4       70.56
8.32      69.2224
8.31      69.0561
8.301     68.9066
```

### 2:25:00 — Historical listing 6.3: squareRoot.py command and completed trace

Observed PowerShell, `t_08700s.png`, checked against `t_08730s.png`. The environment and path are part of the prompt; the command begins at **`py`**. Thirteen guess/difference lines follow, then the reported root and a new prompt. Previous Kivy/OpenGL initialization and shutdown messages sit *above* this command and are not output from the calculation below. No source listing, function signature, tolerance literal, or alternative run for 33 is visible.

```text
(Numerical) PS C:\com\joesta\Numerical> py .\src\squareRoot.py
guess: 2.5, diff: 3.75
guess: 3.75, diff: -4.0625
guess: 3.125, diff: 0.234375
guess: 3.4375, diff: -1.81640625
guess: 3.28125, diff: -0.7666015625
guess: 3.203125, diff: -0.260009765625
guess: 3.1640625, diff: -0.01129150390625
guess: 3.14453125, diff: 0.1119232177734375
guess: 3.154296875, diff: 0.050411224365234375
guess: 3.1591796875, diff: 0.019583702087402344
guess: 3.16162109375, diff: 0.004152059555053711
guess: 3.162841796875, diff: -0.0035682320594787598
guess: 3.1622314453125, diff: 0.0002922862768173218
x: 10, sqrt: 3.1622314453125
(Numerical) PS C:\com\joesta\Numerical>
```

Read each row by squaring **`guess`** and subtracting that square from 10. The last two rows straddle √10; the last, positive residual says the final guess is a little low. At 2:25:30 the screen highlights the 10 in the summary line. At 2:42:00 part of the last difference is highlighted instead. Neither selection is another execution.

## Micro-Learning Insights

*Removable review sheet · CSCS1240F26, Session 35*

- **Make a help request findable.** In the opening discussion, posting where you are on a challenge was meant to help someone recognize a shared sticking point. “Here’s the challenge and the error I see” invites a more useful reply than a public ranking.
- **Ask what a byte array is a byte array *of*.** Eight bytes taken from an object’s identity describe that integer in a chosen order. Eight bytes encoding a numerical value would answer a different question. A successful round trip proves only the transformation you tested.
- **State the codebook before reading the bits.** The pizza–lasagna pattern can mean 5 or 2 without moving a single symbol. Ask separately which state means 1 and which column is worth 4.
- **Say the fraction aloud.** “Five plus a half plus a quarter” explains why decimal **`5.75`** and binary **`101.11`** agree. Saying just “point one one” leaves the place weights hidden.
- **Watch the remainder, not just the digits.** In the fraction loop, a remainder of zero is evidence of an exact finish. Reaching the loop limit with a nonzero remainder means only that the display stopped producing bits.
- **Undo the point shift.** Moving the point in **`101.11₂`** two places left gives **`1.0111₂`**; the factor **`2²`** restores the scale. The exponent field records that scale with a bias, not the number of bits after the point.
- **Ask which format made the display.** The app’s 32-bit field breakdown has a sign, eight exponent bits, and 23 stored fraction bits. A Python float and the app’s binary32 representation are not interchangeable measurements of spacing.
- **Check the application’s units.** A converter may have many levels while a sensor uses only some of them. In the square-root puzzle, an error measured in the square and an error measured in the root are likewise different quantities.
- **Separate the first guess from the stopping rule.** Knowing that 64 and 81 are neighboring squares gives you a starting bracket for 70. It does not tell you how many decimals a caller needs.
- **Ask who produced the error.** Entering `5.75w` reaches the callback and produces a red error label. By contrast, `range(24:48:2)` was a temporary editor draft; the class did not show a traceback from running it.
- **Use the docs to check the format.** The instructor’s suggestion was to use AI to find the official documentation, then verify the operation there. In `struct`, the difference between `!f` and `!I` is exactly the distinction this example needs.
- **Read the scale and the tolerance together.** In JPL’s *How Many Decimals of Pi Do We Really Need?*, 15 decimal places for navigation and 37 for a universe-sized circle describe different tasks. Neither is a rule to “always keep this many digits.”[3]
