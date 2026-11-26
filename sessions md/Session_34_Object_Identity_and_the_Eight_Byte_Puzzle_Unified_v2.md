# Session 34 · Why is there an X in my bytearray?

**CSCS1240F26 · JoesTA · Worked teaching companion**

The class starts with a number that changed overnight and ends with a letter that seems not to belong among hex digits. The same question solves both puzzles: **what does this particular number or symbol represent?** First follow a float’s identity into eight byte slots. Then read what Python prints, one byte at a time. By the end, you should be able to explain the capital `X` without changing a single stored bit.

**Recording and coverage.** [Original recording, as supplied](https://www.youtube.com/watch?v=2nL-Kl0Etc8&t=2135s) · [Play from the opening](https://www.youtube.com/watch?v=2nL-Kl0Etc8). This guide covers the full 55:41 class, not just the linked moment. The saved English auto-caption track contains 1,036 cues, from 00:00.880 through the farewell. Its final timing extends to 55:43.040, slightly past the capture manifest’s 55:41.350 duration. Gaps between cues do not establish silence. Speaker names have not been inferred.

**How to read the evidence.** The original screen captures cover every whole minute from 00:00 through 55:00, plus five event targets and the 55:40 closing target. Times identify requested captures, not exact keystrokes. *Observed* means the screen shows it; *independently verified* means a separate safe example was run, not that the original classroom project was rerun. The screen-code appendix preserves editor states separately from terminal history. The final recall sheet can be detached without losing the worked lesson.

## Which number changed: the float, or its ID?

At the start of class the instructor asks how a program could still produce **`123.45`** when one of its large hexadecimal numbers differed from last class. Start by following the names: **`obj`** refers to the float; **`objId`** is an integer identifying that object. Breaking the *second* number into bytes and putting it back together does not break up the float. That distinction explains the whole demonstration. [Captions 00:41–01:53, 02:21–04:48; `01m00s.png`, lines 14–25.]

First, check what actually ran. In PowerShell, the first input named an incomplete path. Python reported **`[Errno 2] No such file or directory`**. That is a file lookup failure, not a bug inside the script: none of its Python statements had run. The instructor mentioned using Tab to complete the name. The next completed command did name the script. [`02m16s_event.png`; captions 02:00–02:20.]

| Command as entered | What happened |
|---|---|
| `py .\src\jst` | Python could not open the named file. |
| `py .\src\jstruct.py` | Python ran the file under `src` and returned to the PowerShell prompt after the following output. |

Observed output of the **second** command:

```text
Python ID size: 64 bits
Bytes: bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00')
obj id: 0x258f38eadf0
Bytes: bytearray(b'\xf0\xad\x8e\xf3X\x02\x00\x00')
0x258f38eadf0
123.45
<class 'float'>
```

Read the two matching hex numerals first: the integer survived the round trip. Now read the last two lines: the final displayed value was a float. In the class account, the last step treats the recovered address as referring to the original, still-live object; it is not a recipe for turning eight bytes of floating-point data into a new object. The editor tab for **`jstruct.py`** has a modification dot. The instructor said the code had not changed since last class; the stills establish what the shell ran and what the editor showed, not that the on-disk file and unsaved buffer were identical. [`01m00s.png`, `02m16s_event.png`; captions 01:09–01:48, 03:16–03:36.]

**Key items to scan**

- **`obj`** — the name for the float with displayed value **`123.45`**.
- **`objId`** — the identity integer; its hexadecimal display was `0x258f38eadf0` in the script run shown.
- **`py .\src\jst`** — failed at file lookup with **`[Errno 2] No such file or directory`**.
- **`py .\src\jstruct.py`** — successful recorded invocation of **`jstruct.py`**; the modified editor tab is not proof of the saved revision.

## Why reserve eight bytes before writing the ID?

The instructor turns the hex-number puzzle into a source-reading question: *Which line displayed it?* Trace the setup first. The program estimates a width, converts a count of bits to a count of bytes, and allocates storage. Only then does it ask for the float’s ID. [Captions 04:32–07:41, 10:54–11:48; `05m00s.png`–`07m00s.png`, lines 5–16.]

Annotated teaching version — visible setup with guide-added comments; the separate classroom import of `ctypes` is not needed for these lines.

```python
import sys                                # Supplies the platform value sys.maxsize.
idSize = (sys.maxsize.bit_length() + 1)    # Here the width calculation gives 64.
print(f"Python ID size: {idSize} bits")    # Displays Python ID size: 64 bits.
octet = bytearray(int(idSize/8))           # Allocate 8 slots, each initialized to zero.
print(f"Bytes: {octet}")                   # Shows the eight zero bytes before any writes.
obj = 123.45                              # Bind obj to the float, not to its printed text.
objId = id(obj)                           # Keep its integer identity; obj still refers to the float.
print(f"obj id: {hex(objId)}")             # Line 16 displays that integer in base 16.
```

**`sys.maxsize.bit_length()`** counts bits needed for a *platform limit*, not for this specific ID. Adding one gives the width used by this script on this interpreter; the label is not a Python promise that every ID always needs 64 significant bits. Dividing 64 by 8 yields 8.0; **`int(idSize/8)`** supplies the integer length 8. A **`bytearray`** of that length gives eight adjacent byte positions that can be changed individually. The zero initialization is behavior of this Python constructor, not something to assume of arbitrary allocated memory. The instructor also distinguishes the operating system’s protection of one process from another from Python’s initialization of the bytes. The array’s contents are what we inspect; its own object ID is not needed to locate its individual slots. [Captions 05:06–07:30; `06m00s.png`, line 11; initial bytes in `02m16s_event.png`.]

**Key items to scan**

- **`sys.maxsize.bit_length()`** — counts the bits in the platform limit; the script adds one and displays 64.
- **`int(idSize/8)`** — converts the bit count to a whole number of byte slots: eight here.
- **`bytearray`** — mutable byte storage; constructing it by length gives all zeros before the first assignment.
- **`id(obj)`** — returns the identity integer for the live float; **`hex(objId)`** spells that integer in base 16 without changing it.
- **`print(f"obj id: {hex(objId)}")`** — line 16 answers the instructor’s question: the braces insert the formatted ID after the literal label.

## Ask three different questions about the same object

Why might the ID change if the program did not? A student proposes randomness; the class also considers different bases. Neither explains what the number *denotes*. Changing base only changes the spelling of an integer. A fresh execution can assign the same numeric value to an object with a different identity. An ID is stable during an object’s lifetime; it need not repeat across processes, nor does variation make it a random-number generator. The instructor describes IDs as addresses for this implementation. The portable distinction is *which object*, not *which numeric value*. [Captions 02:49–04:48, 11:16–12:25, 16:25–17:53; `04m00s.png` shows the selected ID.]

The highlighted **`123.45`** gives the instructor a reason to ask what *type* adds. Its radix point makes this literal a **`float`** rather than an integer. The instructor’s two memory questions are “the amount of memory” and “the organization of that memory”: how much storage is involved, and how are those bits interpreted? This is a way to reason about a representation, not a rule that an entire Python object occupies exactly eight bytes or that its value starts at its ID. The instructor asks for an explanation that will stick better than repeating the definition. Try the contrast below: the same two digits can be a number or two characters. [Captions 07:30–11:22; `08m00s.png`–`11m00s.png`, line 14.]

In PowerShell the instructor next enters **`py`** by itself. That starts an interactive Python 3.12.7 prompt, distinct from the completed script run above it. At this prompt a bare expression displays its result; an assignment alone does not. A student supplies 64. When **`a`** is requested twice, the interpreter displays 64 twice. Asking **`id(a)`** instead changes the question, not the object. [Captions 12:38–14:19; `13m00s.png`, `14m00s.png`, `15m00s.png`.]

Annotated teaching version — enter these lines at the Python prompt; guide-added comments give the observed results from this classroom session, not IDs to expect on your machine.

```python
a = 64       # Bind a to the integer 64; no result is displayed.
a            # Display: 64.
a            # Display: 64 again; nothing has reassigned a.
id(a)        # Display the integer object's ID: 140730337849752 in this session.
b = "64"     # Bind b to the two-character string; a is unchanged.
b            # Display: '64'. The quotes identify a string representation.
id(b)        # Display the string object's ID: 1705094706544 in this session.
```

Before the last input was submitted, the instructor asked what *would not* appear. It would not display the string `'64'`: the function returns an integer, not the object’s text. And it could not return the ID just displayed for the integer **`a`**: both objects are alive and they are distinct. At `16m00s.png`, **`id(b)`** is entered but there is no result yet; `17m00s.png` shows the returned number. The single quotes Python displayed for **`b`** and the double quotes used to write **`"64"`** delimit the same string; neither pair is stored among its two characters. [Captions 14:21–17:53; `15m00s.png`–`17m00s.png`.]

The instructor calls **`dir`**, **`id`**, and **`type`** three useful questions to bring to an unfamiliar Python object. Use the last two to keep today’s answers separate: *What kind of object is this?* and *Which object is this?* Neither is the same as asking the prompt to display the object’s value. [Captions 11:50–12:13.]

**Key items to scan**

- **`123.45`** — the radix point makes this literal a **`float`**; the digits of its ID do not encode its numeric value.
- **`py`** — starts an interactive interpreter here; a bare expression at `>>>` displays a representation without `print()`.
- **`a`** — refers to the integer 64; its displayed value is not its identity.
- **`b`** — refers to **`"64"`**, two characters; the prompt displays it as `'64'`.
- **`id(a)`** and **`id(b)`** — return integers that must differ for these two simultaneously live, distinct objects.
- **`dir`** — asks which names are available on an object; mentioned as a tool, not run in this example.
- **`id`** — asks which object; unique among live objects, not a permanently assigned number for each value.
- **`type`** — asks what kind of object; use it to distinguish an integer, a string and a float.

## What does a name give you: the item or its location? (18:00–24:46)

With the two interactive IDs still in view, a student asks whether the other number on the screen is “like the address of” the float. In the script, **`obj = 123.45`** binds a name to the float; **`objId = id(obj)`** gives a second name to its integer identity. The script's earlier output shows **`obj id: 0x258f38eadf0`**. The two decimal IDs above come from the interactive session; neither is the float's displayed ID. Ordinary Python code uses the first name to work with the float and has no need to know the second number.

When a student asks whether the location changes, the instructor describes a program asking for enough storage for an object and being given an available location. On a different run it may get a different one; freed space can also be reused. The model explains why an ID is not a permanent serial number. Its limit matters: on the classroom CPython interpreter an object's identity corresponds to its address, but Python does not promise a portable storage layout. The “eight bytes” the class is about to split encode the **identity integer** at the chosen width. They are not a dump of the float's digits, the integer 64, or the characters of the string; the demonstration does not establish the size or layout of any of those entire objects.

**Independent check on this distinction:** encoding the number `123.45` as a little-endian IEEE 754 binary64 value gives `cd cc cc cc cc dc 5e 40` (verified with `struct.pack('<d', 123.45)`). The script instead shows `f0 ad 8e f3 58 02 00 00`. Both sequences occupy eight bytes; only the second came from splitting the recorded ID. No object memory was read for this check.

A student suggests a warehouse: a computer or its parts might be stored in different places. The instructor asks for a concrete order-picking scenario. The worker receives an aisle, a bin, and a quantity. The worker need not know whether the order calls for cookies, keys, or, as the class jokes, gorillas. If the question is *where do I go?*, the bin label helps. If the question is *what did I get?*, hand over the item, not the bin label. That is why it can be useful to ask either question without treating their answers as interchangeable.

**Key items to scan**

- **`obj = 123.45`** — binds the script's name to a float, separate from the prompt's integer and string.
- **`objId = id(obj)`** — names an integer identifying that float; **`obj id: 0x258f38eadf0`** is its hexadecimal display in the recorded script run.
- **Warehouse aisle and bin** — directions for finding the item, not a description of the item itself.

## Split one integer into eight slots, then put it back together (24:47–26:08)

The editor and the earlier script output answer two different questions. Where did the eight slots come from? In this run **`sys.maxsize.bit_length() + 1`** gave 64; the script used that width, not the size of the float, to allocate the array. What changed between the two lines starting with `Bytes:`? The same array was printed before and after replacing individual zeros. It was not thrown away and replaced by a second array. The 25:00 editor shows both the extraction and its inverse; the 26:00–27:00 terminal shows the result already in scrollback, not a new execution at each timestamp.

In the recorded ID, **`0x258f38eadf0`**, the rightmost pair is **`f0`**. Two hexadecimal digits hold eight bits. Looking one pair further left gives `ad`, then `8e`. The editor writes those groups to successive slots starting at zero. The key is not the computer’s native byte order: the assignments themselves specify which group goes in which slot. [`01m00s.png` and `05m00s.png`, lines 18–30; captions 01:36–01:53 and 02:37–02:49.]

Annotated teaching version — guide-added comments on editor lines 18–27. The first two lines below are guide-added setup for an independent, repeatable run: copy the **integer**, not a live memory address. Each assignment changes the same array; none changes the ID or the float.

```python
objId = 0x258f38eadf0  # Use the recorded number as fixed data.
octet = bytearray(8)   # Start a NEW eight-byte array, initially all zeros.
octet[0] = objId & 0xff                      # Keep the low 8 bits: f0.
octet[1] = (objId & 0xff00) >> 8             # Keep the next group; slide ad down to fit.
octet[2] = (objId & 0xff0000) >> 16          # Put 8e in slot 2.
octet[3] = (objId & 0xff000000) >> 24        # Put f3 in slot 3.
octet[4] = (objId & 0xff00000000) >> 32      # Put 58 in slot 4; it displays as X.
octet[5] = (objId & 0xff0000000000) >> 40    # Put 02 in slot 5.
octet[6] = (objId & 0xff000000000000) >> 48  # This high group is 00.
octet[7] = (objId & 0xff00000000000000) >> 56  # The highest group is also 00.
print(f"Bytes: {octet}")                     # Display all 8 bytes, including the zeros.
```

Why both operations? For slot 1, the **`&`** mask leaves `0xad00`: the intended bits are still one byte too far left. **`>> 8`** moves them down to `0xad`, a value that fits in one slot. In the terminal’s byte representation, **`X`** is the printable character for byte `0x58`; it is one of the eight bytes, not a separator and not an extra character after `\xf3`.

Independently verified display from the fixture:

```text
Bytes: bytearray(b'\xf0\xad\x8e\xf3X\x02\x00\x00')
```

To reverse the process, restore the position of each byte before combining it with the running integer. The editor writes out all seven left shifts. Here is the same sequence with guide-added comments; continue with the `octet` just filled, **without resetting it**.

```python
mem = octet[0]          # Start with the low byte: f0.
mem |= octet[1] << 8    # Insert ad one byte to the left: adf0.
mem |= octet[2] << 16   # Put 8e above the two bytes already present.
mem |= octet[3] << 24   # Restore f3; the low four bytes are now complete.
mem |= octet[4] << 32   # The byte displayed as X contributes 58 here.
mem |= octet[5] << 40   # Put 02 in its original position.
mem |= octet[6] << 48   # A high zero sets no bits; mem stays unchanged.
mem |= octet[7] << 56   # The final zero also leaves mem unchanged.
print(hex(mem))         # Display the reconstructed identity integer.
```

Independently verified output:

```text
0x258f38eadf0
```

**Trace of the fixture.** Read down the last column: OR keeps the earlier bits, rather than replacing the partial result.

| Slot | Shift (bits) | Byte stored, in hex | Integer after restoring this slot |
|---:|---:|---:|---|
| 0 | 0 | `f0` | `0xf0` |
| 1 | 8 | `ad` | `0xadf0` |
| 2 | 16 | `8e` | `0x8eadf0` |
| 3 | 24 | `f3` | `0xf38eadf0` |
| 4 | 32 | `58` | `0x58f38eadf0` |
| 5 | 40 | `02` | `0x258f38eadf0` |
| 6 | 48 | `00` | unchanged |
| 7 | 56 | `00` | unchanged |

This trace explains why reading the byte-array display left to right is not the same as reading the hex numeral left to right. Slot 0 holds the *rightmost* pair from the numeral. Also notice a puzzle: the fifth byte is `58` in the trace, but the array prints an uppercase letter there. No slot is missing.

**Key items to scan**

- **`sys.maxsize.bit_length() + 1`** — the script's way of getting 64 for this environment; not a measurement of the float's storage.
- **`bytearray(int(idSize/8))`** — the classroom allocation: 64 bits give eight initially zero slots.
- **`0x258f38eadf0`** — fixed input and reconstructed result in the separate arithmetic fixture.
- **`(objId & 0xff00) >> 8`** — selects the second-lowest byte and moves it down to `0xad`.
- **`mem |= octet[1] << 8`** — restores `ad` above `f0` while preserving the low byte.
- **`print(f"Bytes: {octet}")`** — shows the container's representation, not eight guaranteed pairs of hex digits.
- **`objId & 0xff`** — extract `f0`, the rightmost byte of **`0x258f38eadf0`**, without moving it.
- **`&`**, **`>> 8`** — select bits with AND, then move them to the right; the mask by itself would leave `ad00`, too large for one slot.

## Where does the escape end? (26:09–34:25)

A student had spotted a curly brace in a previous display. The instructor had called that display hexadecimal but hadn't yet followed up on the objection. This time the surprise isn't a brace. In the 28:00 terminal the selected text is **`f3X`**. The class plays “one of those things is not like the other”: there are lowercase x's after backslashes and one uppercase X with no backslash of its own. Is it part of the preceding number? No. Conventional hexadecimal digits are 0–9 and A–F (upper- or lowercase); X is not one. Inventing a different digit alphabet, as the instructor notes one could do, would be changing the notation, not explaining this Python output.

There is a second, independent test. Suppose the third character *were* a legal hex digit. At 29:00 the plain-text scratch tab has only `3`; by 30:00 it shows **`3fa`** and **`001111111010`** on successive lines. This is hand-worked notation, not a command or terminal output. Group the second line by the first:

| Hex digit | Four bit positions (8, 4, 2, 1) | Why |
|---|---|---|
| `3` | `0011` | 2 + 1 |
| `f` | `1111` | 8 + 4 + 2 + 1 |
| `a` | `1010` | 8 + 2 |

A student answers “12.” Twelve *what*? There are twelve written bit positions when each digit gets four. A single byte has only eight. More precisely, leading zeros can be dropped, but even then this particular value is 1018, larger than the maximum byte value 255. The point is not that another hex digit is impossible in a numeral: it's that **`\xhh`** represents exactly *one byte* and consumes exactly *two hex digits* after the lowercase x. It does not keep consuming characters until the next backslash.

The instructor reads the display aloud, one escape at a time: `\xf0`, `\xad`, `\x8e`, `\xf3`. When the familiar cue stops, a student suggests “Surrender.” Instead, ask what was *not* said this time: there is no backslash before **`X`**. At 31:00 the single X is selected. The next question is not “What is X in hexadecimal?” but “Which byte can Python show as the printable character X?” This is a choice of representation; it doesn't convert the array into a mixture of strings and numbers.

| Printed fragment (in order) | What the display accounts for |
|---|---|
| `\xf0` `\xad` `\x8e` `\xf3` | Four bytes; each escape contributes one value. |
| `X` | One byte, hexadecimal `58` (decimal 88); displayed as its printable ASCII character. |
| `\x02` `\x00` `\x00` | The final three bytes, including two actual zeros. |

At 33:00 two different display choices are visible in the editor. **`hex(objId)`** requests hexadecimal for the *whole integer*. Printing the byte array instead requests the array's normal representation: `b'…'` encloses the displayed byte sequence, and some values appear as characters. The student who answers “itself” has part of the answer: yes, the display shows `X` itself, but the stored thing is still a byte with value 88. The same reasoning explains why a printable brace could appear without being a hexadecimal digit.

**Key items to scan**

- **`f3X`** — a misleading visual group; `f3` finishes one escape and `X` represents the next byte.
- **`3fa`**, **`001111111010`** — the scratchpad's legal three-digit counterexample; its value cannot fit in one byte.
- **`\xhh`** — one backslash, lowercase x, exactly two hex digits: one byte.
- **`X`** — an unescaped printable ASCII character representing `0x58`, not an escape marker or a hex digit.
- **`hex(objId)`** — explicitly formats the identity integer in base 16; the array's default display follows a different rule.

## Same printed digits; different inputs to a function (34:26–39:54)

The instructor returns to the integer **`a`** and the string **`b`**. What counts as “the same 64”? Ask a function that cares about type. **`bin(a)`** succeeds: it takes the integer and returns a *string* spelling its binary representation, prefix included. **`bin(b)`** fails before it can return anything. In the 35:30 event frame the word `object` is highlighted in the error. The message names both the type supplied and the type the operation needed. It isn't complaining about the spelling of the two digits.

Annotated teaching version — guide-added comments. Separate prompt entries, with a fresh prompt following the exception, as on screen (35:00–37:00):

```python
bin(a)          # a is 64: the shell displays the returned string '0b1000000'.
bin(b)          # b is '64': raises TypeError; no binary string is returned.
```

Observed last line of the traceback:

```text
TypeError: 'str' object cannot be interpreted as an integer
```

The class identifies it as a **`TypeError`** and infers that `bin` expects a number; more precisely it needs an integer or an object usable as one. The instructor tries conversion by itself before nesting the calls. This separates parsing decimal digits from formatting the result in another base. The instructor mentions decimal alongside `bin` and `hex`; Python has no corresponding built-in `dec()` or `dac()`. For the ordinary decimal spelling of this integer, `str(a)` returns `'64'`.

```python
int(b)          # First check: '64' parses to the integer 64; b is not rebound.
bin(int(b))     # Inner call gives 64; outer call returns the string '0b1000000'.
```

Observed prompt results, respectively (36:00 and 37:00):

```text
64
'0b1000000'
```

A student says the two inputs are both 64 “in some way”: one is 64 numerically; the other represents 64 as a string. The conversion makes the *arguments passed to the outer binary formatter* equal; it doesn't make the original objects the same type. The quotes around the binary result are the interactive shell's way of representing a string, not extra digits in the number.

**Key items to scan**

- **`a`**, **`bin(a)`** — integer input 64; the returned binary spelling is `'0b1000000'`.
- **`b`**, **`bin(b)`** — text input `'64'`; no automatic parsing takes place here.
- **`TypeError`** — the first failure signals an incompatible input type, not a wrong binary calculation.
- **`int(b)`** — returns integer 64 without changing the original string.
- **`bin(int(b))`** — parse the decimal text first, then format the number in binary.

The class tests the distinction two more ways. **`type(a)`** and **`type(b)`** ask what objects they *are*. **`print(a)`** and **`print(b)`** ask what text to send to the screen. The first pair distinguishes the objects; the second pair does not. These are successive observations, not a change of type between frames.

Annotated teaching version — guide-added comments. The first two results are visible at 38:00, and the final two at 39:00:

```python
type(a)         # The shell displays <class 'int'>.
type(b)         # The shell displays <class 'str'> even after the earlier int(b).
print(a)        # Writes 64 on a line, using the integer's decimal spelling.
print(b)        # Writes 64 on a line, using the string's two characters.
```

Observed output of the two print calls:

```text
64
64
```

Neither print call changes its input. The equal-looking lines show why the instructor started with the odd `X`: what you *see* depends on the display operation as well as on the stored value. Matching screen text cannot establish identical types or identical internal bits. Conversely, different spellings of the same byte, like `X` and `58` in a hex column, need not mean different underlying values.

**Key items to scan**

- **`type(a)`** — identifies the original object as `<class 'int'>`.
- **`type(b)`** — identifies the original object as `<class 'str'>`; conversion didn't reassign the name.
- **`print(a)`** — writes decimal digits for the number, with no quotes.
- **`print(b)`** — writes the string's characters, with no quotes; same printed line, different source type.

## Why does a capital X appear among the bytes? · 40:00–43:20

The instructor selects the **`X`** in the earlier PowerShell result. The line is a display of an eight-byte array, not a string decoded from a message. Python can show a byte whose value is 88 as the printable ASCII character **`X`**. It shows the neighboring byte whose value is 142 as **`\x8e`**. Neither byte changed when Python chose how to print it. At 42:00 the selection has moved from the letter to the digits **`8e`**. Why doesn’t that byte get a letter too?

Observed PowerShell scrollback, 40:00–43:00 (one previously completed run):

```text
Bytes: bytearray(b'\xf0\xad\x8e\xf3X\x02\x00\x00')
```

**Key items to scan**

- **`X`** — one byte written as a printable character, not part of the preceding escape.
- **`\x8e`** — also one byte; **`8e`** gives its value in hexadecimal. Its value is outside the ASCII range.
- **`bytearray(b'…')`** — Python’s readable display of the byte values. The wrapper and quotation marks are not stored bytes.

## Read the ASCII chart: outside the code, or defined but nonprinting? · 43:20–47:46

The class looks up an [ASCII table on Wikimedia Commons](https://commons.wikimedia.org/wiki/File:ASCII-Table-wide.svg) (43:48 event). **ASCII** stands for *American Standard Code for Information Interchange*; the instructor connects the standard to the American National Standards Institute. Read *across* a row: the headings **Decimal**, **Hex**, and **Char** give two ways of writing one number and the symbol or control it identifies. For example, the row for 88 says 58 in hex and X in the character column. Changing the base used to write the number does not change its meaning.

ASCII uses seven bits, so the chart ends at 127, not 255. Within those 128 entries, printable characters occupy decimal 32–126: space, punctuation, numeral characters, uppercase letters and lowercase letters. Space has a position and occupies a byte even though it leaves a blank. The first 32 entries and the last entry, 127, are different: they are **control characters**. The instructor recalls typewriters and teletypes used as consoles: a code could ask the equipment to *do* something rather than put ink on paper. The chart’s backspace and carriage return are examples; start of text and end of text instead mark boundaries in communication.

| Byte in the classroom display | What the chart tells you | Why it looks that way |
|---|---|---|
| `X` | Decimal 88, hex `58`, uppercase X | Printable ASCII: Python shows the letter. |
| `\x00` | 0, `[NULL]` | ASCII defines the code, but not a printable glyph. Both final zero bytes are present. |
| `\x02` | 2, `[START OF TEXT]`; the next row is 3, `[END OF TEXT]` | It is a communication control, not the printed digit `2`. |
| `\xf0`, `\xad`, `\x8e`, `\xf3` | Decimal 240, 173, 142, 243 | Not on the chart at all. In an eight-bit byte, the leftmost bit is 1; its place alone is worth 128. |

This gives two answers to “there’s no symbol for it.” A zero is *in* ASCII but has no printable glyph. **`0x8e`** is *outside* ASCII: its bits are **`10001110`**. Likewise, the chart’s last row, **`127`** (hex **`7F`**, **`[DEL]`**), is defined but nonprinting. The suggestion in the captions that the larger values exceed 100 is not the deciding test: decimal 101, for example, is on the chart as lowercase e. The boundary is 127/128.

The instructor calls “print the character if possible, otherwise hex” an imprecise shortcut. It predicts the classroom line, but some nonprinting ASCII values have short escapes. Here is the distinction without using an address or changing any classroom object.

*Annotated teaching version — guide-added example and comments; independently executed:*

```python
controls = bytearray([0, 2, 9, 10, 13, 127])  # Make six bytes, including tab, line feed and carriage return.
print(controls)                               # Show escapes, not a real tab or line break.
```

```text
bytearray(b'\x00\x02\t\n\r\x7f')
```

**Key items to scan**

- **ASCII** — seven bits, 128 codes, decimal 0–127; not every code prints a mark.
- **Decimal**, **Hex**, **Char** — read across one row: 88, 58, X are three descriptions of the same entry.
- **Control characters** — 0–31 and **`127`** are defined without printable glyphs; **`7F`** is **`[DEL]`**.
- **`0x8e`** / **`10001110`** — the bit worth 128 is set, so this byte cannot be a seven-bit ASCII code.
- **`\t`**, **`\n`**, **`\r`** — in Python’s bytes display, tab, line feed and carriage return have short spellings rather than hex escapes.

## The student’s A–F question: when is a letter a hex digit? · 48:00–53:16

A student asks what happens when the printable character *itself* looks like a hexadecimal digit: would a through f just appear as letters? Yes. The chart shows uppercase A–F at decimal 65–70 (hex 41–46), and lowercase a–f at decimal 97–102 (hex 61–66). Those are printable bytes. The *position in the notation*, not the shape of the letter, tells you whether it is being used as a hex digit. At 49:00 the chart is visible as an image; the instructor remarks that its text cannot be highlighted there.

The instructor turns this into an exam-style question in Sublime Text’s plain-text scratch tab: how would you read **`\xaaa`**? Start at **`\x`**, take exactly the next **two** hex digits, and stop. Those two digits are **`aa`**; together they account for the entire byte **`10101010`**. There is still a third **`a`**, with no escape in front of it. Look up lowercase a on the chart: decimal 97, hex **`61`**, bits **`01100001`**. A printed letter can represent an entire byte all by itself.

| Part of the scratch notation | How to read it | Bits stored |
|---|---|---|
| `\xaa` | Take `aa` as two four-bit hex digits: `1010` then `1010`. | `10101010` (170) |
| `a` | Read one printable ASCII character, not a third digit of the escape. | `01100001` (97) |

At 50:00 the scratch line underneath is still being typed. By 50:55 it reads `0000000 0000000 10101010`. Those first two groups actually have **seven** zeros apiece in the editor; a full zero byte needs eight. Around 50:21 the instructor catches the more important width mistake: a wider container could hold a number written with three hex digits, but one byte cannot consume all three. At 51:00 the ASCII chart is back; at 52:00 the third **`a`** is selected, and by 53:00 its full **`01100001`** group is selected. These are handwritten notes, not output from running a program.

*Annotated teaching version — guide-added, independently executed; isolates just the two bytes in question:*

```python
pair = bytearray(b'\xaaa')   # The escape supplies 170; the unescaped a supplies 97.
print(list(pair))            # Show the two stored integers without character formatting.
print(pair.hex(' '))         # Show both bytes uniformly as hex, separated by a space.
print(pair)                  # The same bytes, now in Python’s mixed display.
```

```text
[170, 97]
aa 61
bytearray(b'\xaaa')
```

The scratch tab’s line 4 contains two leading zero escapes and **five** trailing zero escapes around the pair. Read literally as the contents of a bytes literal, that would describe **nine** bytes. The scratch line itself is only a fragment ending with a quote; it was not a newly printed version of the live eight-byte array. The four binary groups on line 5 explain only the start of the fragment.

**Key items to scan**

- **`\x`** — notation announcing two hex digits for one byte, not bytes stored for the slash and x.
- **`\xaaa`** — split after **`aa`**: the escape supplies **`10101010`**, and the remaining **`a`** supplies a second byte.
- **`61`** / **`01100001`** — the hex and eight-bit binary forms of lowercase a, decimal 97. Uppercase A instead has hex value 41.
- **`pair.hex(' ')`** — makes both byte boundaries visible as `aa 61`; it does not modify the pair.

## Count the stored bytes, not the escape markers · 53:17–close

Returning to PowerShell, the instructor compares the initial all-zero array with the filled one. At first there are eight identical **`\x00`** groups. In the filled display, counting hex escapes gives only seven. Look between **`\xf3`** and **`\x02`**: the capital **`X`** is the missing *count*, not missing data. An escape takes four visible characters to spell one byte; this ordinary printable letter takes just one. Neither the displayed line length nor the number of backslashes tells you the array length.

| Position, counting from 1 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|
| Observed spelling | `\xf0` | `\xad` | `\x8e` | `\xf3` | `X` | `\x02` | `\x00` | `\x00` |
| Same bytes, all hex | `f0` | `ad` | `8e` | `f3` | `58` | `02` | `00` | `00` |

*Annotated teaching version — copies the eight observed byte values into a fresh local array. The output below was independently executed, not produced by rerunning the classroom script:*

```python
sample = bytearray([0xf0, 0xad, 0x8e, 0xf3, 0x58, 0x02, 0, 0])  # Eight values, including X at position five.
print(len(sample))       # Count stored byte values, regardless of how they print.
print(sample.hex(' '))   # Use two digits for every byte, including X and both zeros.
```

```text
8
f0 ad 8e f3 58 02 00 00
```

The class stops after resolving the count. The last captions are a sign-off; the 55:40 frame shows the recording controls, not a new exercise.

**Key items to scan**

- **`\x00`** — one zero byte each time it occurs; adjacent identical zeros still occupy separate positions.
- **`\xf3`**, **`X`**, **`\x02`** — three consecutive bytes, at positions four, five and six. The letter is not part of either escape.
- **`len(sample)`** — counts eight stored values; **`sample.hex(' ')`** reveals all eight boundaries without looking up any letters.

## Syntax to remember

| When you need to… | Expression to recognize | What to expect in this lesson |
|---|---|---|
| Inspect what an object is, not how it prints | `type(b)` | With `b = "64"`, the result is the class `str`. |
| Ask which live object a name refers to | `id(obj)` | An integer identity, not the numeric value `123.45`. |
| Display an integer in another base | `hex(objId)` or `bin(a)` | A string with a prefix; no change to the original number. |
| Isolate the second-lowest byte | `(objId & 0xff00) >> 8` | The mask keeps just that position; shifting moves `0xad` down into the range of one slot. |
| Restore that byte to its place | `octet[1] << 8` | Moves `0xad` back to `0xad00`. Bitwise OR combines it with the low byte already in `mem`. |
| Read an escaped byte | `\xf3X` in a bytes display | Read `\xf3` first, then `X`: two bytes, not a three-digit hex number. |
| Inspect all bytes in the same notation | `octet.hex(' ')` | One two-digit hex group per stored byte, including `58` for the capital `X`. This is a guide-added inspection aid. |

The prefix `0x` introduces a hexadecimal integer in Python source. By contrast, `\xhh` inside a bytes literal consumes two hex digits for one byte. This is why `0x3fa` is a valid integer even though it cannot fit in one byte slot.

## Knowledge check

1. The same script prints `123.45` again tomorrow, but its `obj id:` line changes. Which fact stayed the same? What guarantee did Python actually make?
2. With `a = 64` and `b = "64"`, predict which of `print(b)`, `bin(b)`, and `bin(int(b))` succeeds. Explain the failure without using the phrase “because it’s Python.”
3. The classroom ID ends in `adf0`. What goes into `octet[0]` and `octet[1]`? After reconstructing just those two positions, what does `mem` hold?
4. In `\xf3X\x02`, how many bytes are represented? Why does each get its particular spelling? Would counting backslashes find all the bytes?
5. Read `\xaaa` inside a bytes literal from left to right. Give the bytes as two-digit hex groups. How would you tell whether a *display* or the *array* had changed?

### Answer key

1. The float’s **value** stayed the same. The program can receive different identities in different processes, even when the source and numeric value are unchanged. Identity is stable during one object's lifetime, not across launches. A repeated address would not entail that the original object survived.
2. `print(b)` displays `64` without quotation marks. `bin(b)` raises `TypeError`: two characters that look numeric do not supply an integer to `bin`. `int(b)` first parses the text as the decimal integer 64; the outer `bin` then returns the string `'0b1000000'`. None of these calls rebinds `b`.
3. Slot 0 holds `0xf0`; slot 1 holds `0xad`. In the reverse direction, start with `0xf0`, shift `0xad` left eight places to `0xad00`, and combine their disjoint bit positions: `0xadf0`. The program chose the low byte first; this example alone does not establish the host's native byte order.
4. Three bytes: `f3`, `58`, `02`. `f3` is outside ASCII; `58` is printable capital `X`; `02` is the ASCII control STX (start of text). Two have hex escapes and one does not. Counting only the escapes loses the printable byte.
5. `aa 61`. The escape consumes **exactly two hex digits**. The third `a` is an ordinary lowercase letter. To compare contents rather than punctuation, check `len(octet)` and `octet.hex(' ')`: they give the count and one unambiguous group per byte.

## Library of definitions

| Term | Meaning in the classroom example |
|---|---|
| Name / binding | An assignment associates `obj` with a float object. A name is not a box containing a machine address you can portably read. |
| Object identity | What distinguishes this live object from others. `id()` exposes it as an integer. Two names may refer to the same object. |
| Value / representation | `64` is an integer value; decimal `64` and binary `0b1000000` can name that same integer. A representation is a way of showing something, not necessarily its storage layout. |
| Type | The kind of object, constraining what its values mean and which operations apply. The instructor connects type to the size and organization of storage; not every object of a type has the same total size. |
| Literal / radix point | Source notation for a value. `123.45` is a float literal; its point separates the whole and fractional portions. Quotes make `"64"` text instead. |
| Bit, nibble, byte (octet) | One binary digit, four bits, and eight bits respectively. One hex digit names a nibble; two name a full byte. |
| Mask / shift | A bitwise AND selects positions, and a shift moves them. Together these let each indexed byte hold its part of a wider integer. |
| Contiguous storage | The array’s **data bytes** occupy consecutive positions. This does not make the identity of the `bytearray` object the address of its first data byte. |
| Little-endian order | Store the least significant byte first. The explicit assignments in this example produce that ordering. |
| ASCII / control character | A seven-bit character code. Some values name controls such as NUL, STX and DEL rather than printable marks. Values above 127 are outside ASCII. |
| Escape / printable character | `\x02` spells one byte using an escape; `X` shows one byte as a glyph. Neither spelling takes extra space **in the array**. |
| REPL | The interactive read–evaluate–print loop. It echoes the representation of expression results; assigning a name alone produces no result line. |

**Supplemental references** (not records of classroom activity): [Python built-in functions: `id`, `bin`, `int`, `print`, `type`](https://docs.python.org/3/library/functions.html); [Python bytes and bytearray](https://docs.python.org/3/library/stdtypes.html#binary-sequence-types-bytes-bytearray-memoryview); [RFC 20: ASCII format for network interchange](https://www.rfc-editor.org/rfc/rfc20.html).

## Historical screen-code appendix

### `jstruct.py` · reading the classroom file, not rerunning it

**Assembled visual reconstruction, not a saved-file claim.** The opening at 05:00 supplies lines 1–30; the lower view at 48:00 supplies the remainder and overlaps the masks, array print and start of the reconstruction. The editor shows a modification indicator. These compatible views do not prove there were no edits between them or that the open buffer exactly matches the earlier launched file. **Left-hand numbers are the editor gutter, not Python to type.** Long lines may wrap in print without acquiring a new gutter number. Line 2 runs off the editor’s right edge; the bracketed words mark unavailable text.

```text
 1  """
 2  Note: The Python language specification only guarantees that id() returns a unique and constant[remainder not visible]
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
39  back = ctypes.cast(mem, ctypes.py_object).value
40
41  print(back)
42  print(type(back))
```

**Reading the groups.** Lines 8–16 establish the example’s width, allocate zeroed storage, and print the float object’s identity. Lines 18–27 replace the eight slots with bytes of that **integer**, lowest first. Lines 29–37 reverse the arithmetic: reposition each byte and combine the results, then display the integer in hex. Lines 39–42 are a historical, implementation-specific step, not conversion of a binary64 payload. The separately observed 02:16 scrollback ends in `0x258f38eadf0`, `123.45` and `<class 'float'>`. No live-memory cast is needed to verify the preceding arithmetic; the saved address must not be used as a pointer in a new run.

### The `3fa` scratch tab · paper-and-pencil reasoning in an editor

At 30:00 the class is testing whether three hex digits can describe one byte slot. These are **plain-text scratch lines**, not Python instructions or console results:

```text
1  3fa
2  001111111010
```

At 53:00 the same tab shows a different state. Rows 2–3 are blank. Line 4 is a fragment with a closing quote, not a complete bytes literal; row 5 is a partial manual expansion, not the output of converting the entire row.

```text
1  3fa
2
3
4  \x00\x00\xaaa\x00\x00\x00\x00\x00'
5  0000000 0000000 10101010 01100001
```

The first two groups on line 5 visibly have fewer than eight zero digits; a full-width rendering of either zero byte is `00000000`. The useful distinction is in the next two groups: `10101010` for `\xaa`, then `01100001` for literal `a`. If line 4’s body is interpreted separately as bytes, it has **nine**, not eight: two zero bytes, `aa`, `61`, and five more zero bytes. This hand-edited hypothetical is not a new run of the original eight-slot program.

### Python prompt · the failure and the recovery in one terminal view

At 39:00 PowerShell contains a Python REPL. This **observed scrollback excerpt** starts after the two names have been set. Only lines beginning with `>>>` were inputs; do not type the prompt marks or the returned lines. The fresh prompt after the exception is why the instructor could test the next expression without restarting.

```text
>>> bin(a)
'0b1000000'
>>> bin(b)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object cannot be interpreted as an integer
>>> int(b)
64
>>> bin(int(b))
'0b1000000'
>>> type(a)
<class 'int'>
>>> type(b)
<class 'str'>
>>> print(a)
64
>>> print(b)
64
```

Read the quotation marks as evidence: the two successful binary conversions returned strings, the integer conversion returned a number, and the last two lines of output look alike because they were written by `print`.


## Micro-Learning Insights

*Removable recall sheet • Session 34*

- **Find the line that produced the mystery number.** At 05:00, `objId = id(obj)` is followed by `hex(objId)` inside a print. The first operation asks *which object*; the second changes only how the answer looks. Neither extracts floating-point digits from `123.45`.
- **Use the warehouse question both ways.** The bin location tells a picker where to go; it does not tell you whether the bin holds cookies or keys. Ask whether a number in the program names the location or describes the item.
- **Treat a fresh ID as ordinary, not random.** Do not use a change of address between runs as evidence that the float changed, the source was edited, or `id()` is a random-number generator.
- **Name the unit before allocating.** The width reported in the classroom is 64 **bits**; `bytearray` takes a count of **bytes**. The result starts with eight zero-valued slots, not 64 slots or eight printable zero characters.
- **Use one byte to check both directions.** Slot 1 holds `ad`. When unpacking the ID, move that byte down eight bits; when rebuilding the integer, move it back up eight. If your low pair does not end in `adf0`, inspect the order.
- **Do not diagnose type from printout alone.** Both `print(a)` and `print(b)` display `64`. At the REPL, compare `type(a)` and `type(b)` before deciding whether `bin` can use the object.
- **Advance one token, not one glyph.** In `\xf3X`, consume exactly `\xf3`, then consume `X`. In `\xaaa`, the same rule gives `aa` followed by `61`. Neither example needs another backslash to mark the printable byte.
- **Ask two questions of an ASCII chart.** Is the byte in the seven-bit range? If yes, is it printable? `f3` fails the first test, `02` fails the second, and `58` passes both. The class’s *ASCII TABLE* displayed decimal, hex and character columns for precisely this lookup.
- **Keep storage and display separate when counting.** The final array has eight bytes even though the displayed spellings have different lengths. For a self-check, compare `len(octet)` with the number of groups in `octet.hex(' ')`, not the number of `\x` prefixes.
