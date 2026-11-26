# From an object ID to the bits of a float

**CSCS1240F26 · Session 33 · Worked teaching companion**  
[JoesTA’s recording, November 16, 2026](https://www.youtube.com/watch?v=0GC7x1ONDFw) · Unified v2

The first program starts with **123.45**, takes its object ID apart one byte at a time, and puts that number back together. The second starts with **12.34** and asks which bytes actually encode the number. Keep those questions separate: the first byte of the ID in this run is `f0`; the first byte of the second float’s little-endian value is `ae`. Neither byte makes sense until you know **which number it belongs to, which end comes first, and how to interpret its bits**.

**Find the demonstrations:** [select a byte with a mask](https://www.youtube.com/watch?v=0GC7x1ONDFw&t=474s) · [reassemble the bytes](https://www.youtube.com/watch?v=0GC7x1ONDFw&t=1140s) · [read the first run and its mysterious brace](https://www.youtube.com/watch?v=0GC7x1ONDFw&t=2095s) · [separate the float object from its value](https://www.youtube.com/watch?v=0GC7x1ONDFw&t=2774s) · [interpret the exponent and precision](https://www.youtube.com/watch?v=0GC7x1ONDFw&t=3393s).

**Evidence.** The title and recording ID match the requested session. The complete published English auto-caption track (1,128 cues) and the readable page transcript both reach the instructor’s sign-off. The video stream is 1:01:40.48; caption timestamps are approximate. The visual record comprises 62 one-minute samples from 0:00:00 to 1:01:00 and 21 additional samples, ending at 1:01:38. A frame shows an editor or terminal state, not every intervening keystroke or save. Observed output below is classroom output; guide-added comments, worked traces and separately tested examples are identified as such. Runtime addresses are historical data, not addresses to use on another run.

## From Base64 to a question about one integer · 0:00:00–0:04:21

Last class used Base64 and UTF-8; this class keeps the part underneath those examples: operations on bits. At 0:01:00 the roadmap shows **`< base64, utf-8`** immediately above **`jStruct.py`**. The actual editor tab at 0:01:36 reads **`jstruct.py`**. The question in this file is different from “How do I encode text?” It is “If I have one number, how can I keep its eight-bit pieces separately without losing their positions?”

The imports prompt a design discussion before any arithmetic. **CPython**, the commonly used implementation of Python written in C, lets a programmer write most of a project for readability and choose a different language for a part that needs it. The instructor imagines a Python doubly linked list with a million items: *if* it proved too slow, its implementation could be written in C and made available as a Python module. This is a reason to learn more than one language, not a benchmark or a rewrite performed in class. The file imports **`ctypes`**, but the byte calculations themselves need only integers and a byte container.

Why ask about width at all? At the level of a Python name, you need not reserve space for a reference yourself. Once you promise to put a number into a fixed number of byte slots, however, the width matters. The class contrasts 32- and 64-bit systems with a small embedded device; a student suggests an Arduino. The point is not that all these devices run the same Python, but that one desktop's representation is not a language guarantee.

**Key items to scan**

- **`< base64, utf-8`** — the roadmap's previous topic; bit operations supply the connection, not a new encoding exercise.
- **`jStruct.py`** / **`jstruct.py`** — roadmap spelling and actual tab spelling, respectively.
- **CPython** — distinguishes one implementation's storage choices from rules all Python implementations must follow.
- **`ctypes`** — visible import that starts the C/Python discussion; not needed to practice masking or shifting.
- **Million-item list / Arduino** — two different design questions: where performance matters, and how much storage the target has.

## Reserve the byte slots; distinguish the object from its ID · 0:04:21–0:08:28

The example binds **`obj`** to the floating-point value **`123.45`**. The number the code will split is **`objId`**, an integer returned by **`id(obj)`**. It is *not* the digits of 123.45, a conversion of that float to an integer, or the float's binary encoding. The instructor recalls three familiar inspection tools: **`id()`** asks which object, **`type()`** asks what kind of object, and **`dir()`** lists available names. Only the first is used to obtain the integer here. **`hex(objId)`** produces a string that makes the integer easier to read in pairs of hex digits; it doesn't change the integer.

First the program makes room. On a typical 64-bit build, **`sys.maxsize`** is 2⁶³ − 1. Its **`bit_length()`** is 63; adding one accounts for the sign position of that platform size type. The variable **`idSize`** therefore gets 64 here, and dividing by eight gives eight slots. On a typical 32-bit build the same reasoning gives 32 and four. Despite the classroom print label, this estimates the width of the platform size type; it does not query a universal maximum width for object IDs. Python guarantees that an ID is constant and unique for an object's lifetime; once lifetimes do not overlap, IDs can be reused. See the supplemental Python references for [sys.maxsize](https://docs.python.org/3.13/library/sys.html#sys.maxsize) and [id](https://docs.python.org/3.13/library/functions.html#id).

**Annotated teaching version — guide-added comments.** Safe excerpt from the visible setup; the independent sample below is not classroom output.

```python
import sys                                 # Provides the platform's maximum size value.
idSize = (sys.maxsize.bit_length() + 1)    # Here: 63 + 1 gives a width of 64 bits.
print(f"Python ID size: {idSize} bits")    # Display the historical label and calculated width.
octet = bytearray(int(idSize/8))           # Create a NEW container of eight zero bytes here.
print(f"Bytes: {octet}")                  # Display it before filling any slots.
obj = 123.45                               # Give the floating-point object a name.
objId = id(obj)                            # Keep its identity integer, not its numeric value.
print(f"obj id: {hex(objId)}")             # Format that integer in hex, then display the string.
```

Independently verified sample output (the third line identifies the object in this one run; do not expect its literal value to recur):

```text
Python ID size: 64 bits
Bytes: bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00')
obj id: 0x20718eb6f10
```

Why **`bytearray`** rather than **`bytes`**? Each indexed element represents a single byte, an integer from 0 through 255. Both types can hold a sequence of bytes, but only the former lets the program replace an element in place. A list can hold the float itself; a byte slot cannot. To store something larger you must choose a representation across several slots. This is the connection back to packing and regrouping bits in the Base64 discussion, not a claim that an object's identity stores the object's contents.

The instructor uses contiguous storage to explain indexing: in a row of equal-width entries, an index tells you how far along the row to go. Adding room *at the same physical location* isn't always possible. That is not a prohibition on growing a Python bytearray: it supports mutable-sequence operations such as appending. The discussion of “only bytes” describes these byte containers, not every Python container or library. [Supplemental Python reference: bytearray objects](https://docs.python.org/3.13/library/stdtypes.html#bytearray-objects).

**Key items to scan**

- **`obj`**, **`123.45`** — name and floating-point value; neither is the bit pattern the example splits.
- **`id()`** / **`id(obj)`** → **`objId`** — the identity operation, the actual call, and the name that holds its integer result.
- **`type()`** and **`dir()`** — contrast knowing the object's kind or its available names with identifying the object itself; neither is called in the example.
- **`hex(objId)`** — returns a hexadecimal string for display; leaves the stored identity integer unchanged.
- **`sys.maxsize`**, **`bit_length()`**, **`idSize`** — the example turns a platform size limit into a width estimate, not an ID specification.
- **`bytearray`** — mutable sequence; construction with a length makes that many zero bytes, not one byte containing that length.
- **`bytes`** — immutable counterpart; its existing positions cannot be assigned new values.

## Why `0xff` keeps just the last byte · 0:08:28–0:15:00

The instructor asks why hexadecimal is useful in the first place. Four bits have 16 possible patterns, exactly enough for the symbols 0–9 and a–f. Decimal 15 needs two written digits, but hexadecimal **`f`** is one digit: **`1111`** in binary, or 8 + 4 + 2 + 1. Put two such digits together and **`0xff`** is eight ones. Those ones are instructions for which columns of the *other* number to preserve when applying bitwise AND, **`&`**.

A student asks about the *size* of the result. Separate the width of your drawing from the value the drawing represents. On the scratchpad at 0:12:00, eight ones start under the **left** end of a longer number. That is where the text was typed, not where the mask's ones place belongs. Match ones places at the **right**, then fill unused columns at the left with zeros. Neither leading zeros nor a line wrap change the number. The instructor corrects the group to eight ones and notices spaces causing wrapping while preparing the written calculation.

| Column you inspect | Calculation in that column | What gets through |
|---|---|---|
| Above the last eight | Input bit AND 0 | Always 0, whether the input was 0 or 1 |
| Within the last eight | Input bit AND 1 | Exactly the input bit: 0 stays 0, 1 stays 1 |
| All eight selected bits together | Possible suffixes range from 00000000 to 11111111 | Value 0–255, not necessarily a number that needs eight printed digits |

The scratch number ends in **`10001000`**. In the guide's calculation below, only the 128 and 8 places in that group contribute: the result is 136. The 0:14:14 still shows only the leading zeros of the answer. The completed low-byte result appears later in the scratchpad (0:20:00; see the historical appendix). This calculation checks the legible input, not a classroom terminal run.

**Annotated teaching version — guide-added comments; independently verified.**

```python
n = 0b1010101010101001101010101010101001010001000  # The scratchpad digits, read as binary.
low = n & 0xff                                   # Keep its rightmost eight columns.
print(f"{low:08b}", low)                         # Display the eight-bit pattern and its value.
```

```text
10001000 136
```

**Key items to scan**

- **`f`**, **`1111`** — one hexadecimal digit and its four-bit spelling, both worth 15.
- **`0xff`** — eight low ones; higher positions are effectively zero in this mask.
- **`&`** — bitwise AND: one mask bit passes the corresponding input bit, zero blocks it.
- **`10001000`** — low byte of the handwritten input, worth 136; it fits in one slot regardless of the length of the input.
- **Least significant bit** — the ones place, at the right; line up place values before doing any column arithmetic.

## Keep the next byte, then move it into range · 0:15:00–0:18:59

The next source line is **`octet[1] = (objId & 0xff00) >> 8`**. Instead of eight low ones, **`0xff00`** has eight ones followed by eight zeros. The ones keep the second-lowest group; the zeros blank out the first group. Masking alone is only half the job: those eight selected bits still have their old place values. **`>> 8`** discards the eight low positions, bringing the selected group down to a number from 0 through 255. The parentheses make the intended order explicit: evaluate the AND first, then shift its result.

| Step using the class scratch digits | Relevant bit positions | Result and why |
|---|---|---|
| Take the low 16 input bits | `01010010 10001000` | First byte to store is at the right; second is immediately to its left. |
| AND with the second mask | `11111111 00000000` | At 0:17:00 the scratch result ends `01010010 00000000`: hex `0x5200`, still in the second-byte position. |
| Right-shift eight places | `00000000 01010010` | At 0:17:54 the result ends `01010010`: decimal 82, now small enough for one slot. |
| Assign element 1 | Slot 0: `10001000`; slot 1: `01010010` | The second write changes slot 1, not the first byte or the original integer. |

For the third group the code uses **`0xff0000`** and shifts right 16. Each additional pair of zero hex digits moves the window one byte further up; the shift then removes those positions again. In the visible file, eight explicit assignments handle shifts from 0 to 56. That is a 64-bit example, not a width-independent program: a four-slot allocation cannot accept a write to index 4. Choosing the least significant byte first is a decision about the array's order; it does not require knowing the machine's native byte order.

**Annotated teaching version — guide-added loop over the same eight masks; independently verified.** This uses the written scratch number in place of the changing identity integer, so the results are reproducible.

```python
n = 0b1010101010101001101010101010101001010001000  # The written input, unchanged by the loop.
octet = bytearray(8)                             # Start a NEW eight-slot result, initially zero.
for i in range(8):                               # Visit every slot from 0 through 7 once.
    shift = 8 * i                                # Which group? 0, 8, 16, ..., 56 bits up.
    mask = 0xff << shift                         # Place eight ones over that group of input bits.
    octet[i] = (n & mask) >> shift               # Keep them, slide them down, replace this slot.
print(list(octet))                               # Display unsigned byte values, low group first.
```

```text
[136, 82, 85, 77, 85, 5, 0, 0]
```

The final two zeros are meaningful: the scratch number has no ones that high. A stored zero is still a byte in its assigned position. Knowing which place each byte came from is what makes it possible to put the integer back together.

**Key items to scan**

- **`octet[1] = (objId & 0xff00) >> 8`** — select the second group, move it to the bottom, then store it in the second slot.
- **`0xff00`** — selects positions 8–15; for the written input the masked number is `0x5200`, not yet a one-byte value.
- **`>> 8`** — removes the eight now-zero low positions; `0x5200` becomes `0x52` (82).
- **`0xff0000`** — selects the third-lowest group; shift right 16 to obtain that group's byte value.
- **Little-endian byte order** — the least significant group, 136 here, goes in slot zero; subsequent slots hold successively higher groups.

## Start putting the bytes back: why OR rather than AND? (0:19:00–0:24:31)

The first byte in the instructor’s drawing is **`10001000`**. It came from the rightmost eight positions of the original number, so it is already in the right place. The statement **`mem = octet[0]`** gives the name **`mem`** that byte’s integer value. It does not remove the byte from the array. The next byte, **`01010010`**, belongs *to the left* of the first. If we combine the two immediately, the two groups compete for the same eight positions. We must position the second group before combining it.

The instructor pauses over the symbols in the program. Bitwise **`&`** keeps a 1 only where *both* inputs have a 1; that is why it made a useful filter earlier. Bitwise **`^`** (exclusive OR, or XOR) sets a bit when *exactly one* input has a 1. Bitwise **`|`** (inclusive OR) sets a bit when *at least one* input has a 1, even if both do. Ordinary **`=`** assigns a result to a target; it does not test equality. Put an operation before the equals sign and the old value participates in the update: **`&=`**, **`^=`**, and **`|=`** are AND-, XOR-, and OR-assignment. The scratchpad actually shows a bar followed by a plus in the last slot; the working program uses bar–equals.

**Annotated teaching version — guide-added comments.** This independently run two-byte example uses the class’s hand-drawn values, not a live object ID. The two-step spelling comes from the scratchpad at 0:24:00.

```python
octet = bytearray([0b10001000, 0b01010010])  # First slot: 0x88; next slot: 0x52.
mem = octet[0]                            # Start the rebuilt integer with 0x88.
temp = octet[1] << 8                      # Move 0x52 up one byte: 0x5200.
mem = mem | temp                          # Keep bits from both positions: 0x5288.
print(hex(mem))                           # Display the integer in hexadecimal.
```

Independently verified output:

```text
0x5288
```

In the actual program **`mem |= octet[1] << 8`** performs that update without naming the intermediate result. For this integer variable, read it as “shift the next byte, OR that shifted value with the old integer, then rebind the name to the result.” The array and its two entries remain unchanged. AND would erase both groups here: where one positioned input carries a bit, the other input is zero. XOR would happen to agree with OR for disjoint groups, but “keep a 1 from either input” is the rule we want.

**Key items to scan**

- **`mem = octet[0]`** — starts **`mem`**, the integer being rebuilt, with the low byte **`10001000`**.
- **`01010010`** — the next byte, not yet in its old position until shifted left eight places.
- **`&`**, **`&=`** — require a 1 on both sides; the assignment form stores the AND result.
- **`^`**, **`^=`** — require a 1 on exactly one side; the assignment form stores the XOR result.
- **`|`**, **`|=`** — keep a 1 from either or both sides; the assignment form updates the accumulated integer.
- **`=`** — binds the target to the result on the right. The scratchpad’s **`temp`** names a shifted value, not another byte-array slot.
- **`mem |= octet[1] << 8`** — shifts before combining, so the first and second bytes occupy distinct positions.

## The student’s objection: what width are we shifting in? (0:24:31–0:31:07)

If you shift **`01010010`** left eight places, do you get **`0101001000000000`**, or do you get zero? The instructor asks the class to defend an answer rather than guess which one an exam expects. A student describes a limited range of place values: once a bit moves past that range, it no longer counts. The instructor accepts the reasoning. The two answers describe *different destinations*.

In an eight-position register, a left shift drops the bit that passes the left edge and brings in a zero at the right. After three shifts the low byte reads **`10010000`**, a value visible on the scratchpad at 0:28:00. After eight shifts none of the original eight bits remains. But storing the *input* in one byte does not force the *result* into one byte. The class reserves room for a 64-bit ID. Python’s integer shift itself does not wrap at eight bits or at 64 bits; here its result fits within the instructor’s 64-position drawing.

**Annotated teaching version — guide-added comments.** The following independently tested comparison makes the width assumption explicit. The final mask models keeping only eight positions; it is not an extra operation in the class’s packing code.

```python
second = 0b01010010                       # The second array element is 0x52.
wide = second << 8                        # Shift into a wider result: 0x5200.
narrow = wide & 0xff                      # Retain only the lowest eight bits: 0.
print(f"{wide:016b}", f"{narrow:08b}")     # Display 16 and 8 positions, respectively.
```

Independently verified output:

```text
0101001000000000 00000000
```

In the wider model, put the two values above each other with the *right edges aligned*. The instructor’s scratchpad shows the following OR at 0:31:00. Spaces within bytes are added here for reading; each row shows the low 16 positions, not all 64. The other 48 positions are zero at this stage.

```text
00000000 10001000    old mem: the first byte is still at the right
01010010 00000000    shifted second byte: eight empty places at the right
-----------------    inclusive OR, one column at a time
01010010 10001000    both bytes recovered in their original order
```

A zero in one OR input lets the other input’s bit through. Read the top half of the result from the second row and the bottom half from the first. Checking those 16 positions against the *right edge* of the original scratch number gives the same pattern. This check does not assert that the entire scratch line was 64 digits long.

**Key items to scan**

- **`01010010`** — eight input bits; their storage width does not determine the width of a Python shift result.
- **`10010000`** — one intermediate low-byte state, not the eight-shift answer or the combined result.
- **`0101001000000000`** — the wide result of the left shift, before OR fills its low byte.
- **`& 0xff`** — in the width comparison only, deliberately discards positions above the low eight and leaves zero.
- **`01010010 10001000`** — after OR, these are the low 16 bits. The classroom model has room for another six bytes.

## Reverse all eight placements, then ask what was recovered (0:31:07–0:34:03)

To split the original integer, a mask kept one group in place and a right shift brought it down to array-element size. To reverse that operation, take each element *unchanged* and shift it left by the same distance. The byte at index 2 goes up 16 places; the one at index 7 goes up 56. No shift or OR here modifies the source array. Only the accumulated integer changes.

**Annotated teaching version — guide-added comments.** The code below follows the program’s eight placements. Its input is a teaching fixture derived from the *43 written digits* on scratch line 1 at 0:20:00, with leading zeros added to fill eight bytes. It is not the recorded ID of the float. Hexadecimal makes each two-digit byte easy to track.

```python
octet = bytearray([0x88, 0x52, 0x55, 0x4d, 0x55, 0x05, 0x00, 0x00])  # Low byte first.
mem = octet[0]            # Index 0 needs no shift.
mem |= octet[1] << 8      # Bring 0x52 back above 0x88.
mem |= octet[2] << 16     # Place 0x55 in the third byte position.
mem |= octet[3] << 24     # Place 0x4d in the fourth.
mem |= octet[4] << 32     # This 0x55 belongs in the fifth, not the third.
mem |= octet[5] << 40     # The sixth byte holds 0x05.
mem |= octet[6] << 48     # OR with a positioned zero; the number stays the same.
mem |= octet[7] << 56     # Highest byte is also zero in this fixture.
print(hex(mem))           # Display the number without leading zero padding.
```

Independently verified output:

```text
0x5554d555288
```

The derived trace shows the whole *64-position representation*, unlike the unpadded printout. Read each pair of hex digits as one byte; the next source byte belongs immediately to the left of the bytes already restored.

| Array index | Byte (hex) | Shift left | Accumulated integer, shown as eight hex byte pairs |
|---:|---:|---:|---|
| 0 | 88 | 0 | `00 00 00 00 00 00 00 88` |
| 1 | 52 | 8 | `00 00 00 00 00 00 52 88` |
| 2 | 55 | 16 | `00 00 00 00 00 55 52 88` |
| 3 | 4d | 24 | `00 00 00 00 4d 55 52 88` |
| 4 | 55 | 32 | `00 00 00 55 4d 55 52 88` |
| 5 | 05 | 40 | `00 00 05 55 4d 55 52 88` |
| 6 | 00 | 48 | `00 00 05 55 4d 55 52 88` |
| 7 | 00 | 56 | `00 00 05 55 4d 55 52 88` |

Now return to the program rather than the scratch fixture. **`obj`** still names the float **`123.45`**. **`objId`** is the integer originally obtained from its identity, **`octet`** holds the separated bytes of that integer, and **`mem`** holds the integer reconstructed from the bytes. The logical test at this point is numeric equality, **`mem == objId`**. It is *not* whether the two integer objects themselves have identical identities. Nor did copying the ID serialize the float’s value: rebuilding the integer does not, by itself, produce a float.

The instructor then introduces a separate, implementation-dependent operation intended to refer to the still-live original object as **`back`**. The program has **`print(back)`** and **`print(type(back))`** ready to display the value and type. This is the setup and prediction, not terminal output at 0:34:00. Matching value and type would be consistent with the original float; those two outputs alone do not prove object identity. An integer from another run, or one whose original object no longer exists, is not a reusable object reference. No raw-memory operation is needed to test the numerical round trip.

**Key items to scan**

- **`octet`** — stores one byte at each index; the class chose least-significant byte first.
- **`mem`** — gathers those bytes into one integer. Its numeric value, not its own identity, is the round-trip result.
- **`obj`** and **`123.45`** — the original live float; its value is not the byte sequence being packed.
- **`objId`** and **`mem == objId`** — the original identity number and a safe numerical check (guide-added, not a visible classroom assertion).
- **`back`**, **`print(back)`**, **`print(type(back))`** — the proposed object reference, then the two separate value and type displays. Neither display is itself an identity comparison.

## Reading the run: where is 123.45, and what do the eight bytes contain? (0:34:55–0:38:04)

After locating the program in the `src` directory, the instructor runs it from the project prompt. Read the two lines labeled **`Bytes:`** as a before-and-after pair. The program first creates **`octet`**, an eight-element byte array whose elements all start at zero. It then sets **`obj = 123.45`** and obtains **`objId = id(obj)`**. Look at which of those two names the masks operate on: it is **`objId`**, not **`obj`**. The program splits the integer identifying the float into eight bytes. It does **not** split the number 123.45 into its floating-point digits or its stored numeric payload.

Observed PowerShell command and output (0:34:55; the same run remains on screen at 0:38:00). This is a record of the class run, not an instruction to run the original program:

```text
(Numerical) PS C:\com\joesta\Numerical> py .\src\jstruct.py
Python ID size: 64 bits
Bytes: bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00')
obj id: 0x2847db4adf0
Bytes: bytearray(b'\xf0\xad\xb4}\x84\x02\x00\x00')
0x2847db4adf0
123.45
<class 'float'>
```

A student asks about eight bits per element (0:35:22–0:35:58). Yes: index 0 holds one byte, index 1 holds another, and the eighth byte is at index 7. In the first printout, **`\x00`** is Python’s way of *showing one zero byte*; the backslash, x, and two zeros are not four elements. In the second printout the contents have changed, not the length. The program provides the label, but Python supplies the mixed byte-array display without the instructor formatting each element.

Why do the last three lines differ? **`hex(mem)`** returns a string showing the reconstructed integer; the surrounding print call displays it, and it matches the earlier object ID exactly. Here, in CPython, the ID identifies the location of the live float object. The subsequent classroom-only low-level step used that address to get back to the original object; **`print(back)`** then displayed its value and **`print(type(back))`** its type. The integer had not turned into a floating-point encoding. The printed address belongs to this run; it is not a permanent address for 123.45. The program’s size label reports its width calculation on this system, not a guarantee that every Python ID has 64 significant bits.

**Key items to scan**

- **`Bytes:`** — the same label precedes the initial zero array and the filled array; compare what follows it.
- **`octet`** — eight mutable slots, numbered 0 through 7; **`\x00`** represents one zero-valued slot.
- **`obj = 123.45`** — gives the float object a name. **`objId = id(obj)`** obtains its identity as an integer; **`objId`**, not **`obj`**, supplies the bits that fill the array.
- **`hex(mem)`** — formats the reassembled integer as `0x2847db4adf0`. This is the address again, not the float’s value.
- **`print(back)`** — displays `123.45`; **`print(type(back))`** displays `<class 'float'>`.

## The brace puzzle: compare whole bytes, not printed characters (0:38:04–0:45:26)

The two displays looked nothing alike. The instructor copied them into a plain-text editor and stripped off the hexadecimal markers to compare them. The class could match **`f0`**, **`ad`**, and **`b4`** at the start of the array to pairs at the *right* of the number. Then a student spotted **`}`** where two hex digits seemed to be missing. These observations lead to two separate questions: in what **order** were the bytes written, and by what **notation** is each byte being shown?

For order, write the ID with all eight two-digit slots. Ordinary hexadecimal writes the most significant digit on the left; the code instead placed the *least* significant byte at array index 0. Read the rows below in opposite directions to find the same byte pairs. Do not reverse the digits *within* a pair: `f0` is still `f0`, not `0f`. The top row’s leading zeros do not change the integer. This is the little-endian order chosen by the program, not a test of the machine’s native memory order.

| Reading the same fixed number (guide reconstruction) | Eight byte values, left to right |
|---|---|
| Hex number, highest place first | `00 00 02 84 7d b4 ad f0` |
| Array, index 0 through index 7 | `f0 ad b4 7d 84 02 00 00` |

A student wondered whether half the ID was numeric data and the other half an address (0:40:55). There is no dividing line of that kind here: all eight slots came from the **same integer**. The instructor’s test was to compare the bits. One hexadecimal digit corresponds to four bits, a *nibble*, and two nibbles make a byte. Using place weights 8, 4, 2, 1, the class worked up to three bytes. A student caught the skipped D: without its four bits the grouping would shift and the comparisons would no longer line up.

| Pair | Left digit: four bits | Right digit: four bits | Joined byte |
|---|---|---|---|
| `f0` | F = 15 → `1111` | 0 → `0000` | `11110000` |
| `ad` | A = 8 + 2 → `1010` | D = 8 + 4 + 1 → `1101` | `10101101` |
| `b4` | B = 8 + 2 + 1 → `1011` | 4 → `0100` | `10110100` |

The joined groups above are visible at 0:43:00. For the brace, the instructor connected the display to character encodings and a student asked where that character had actually come from. At 0:45:26 the instructor deferred a fuller answer and said they would post something in Discord; that is a promise, not an observed post. Here is the missing link, checked separately: array index 3 contains **`0x7d`**, decimal 125, binary `01111101`. Python’s byte-array representation shows this printable ASCII byte as **`}`** instead of writing `\x7d`. It does not decode the array as UTF-8, insert a ninth byte, or interpret a brace as Python syntax. A display choice changes how a byte *looks*, not what is stored.

**Key items to scan**

- **`f0`**, **`ad`**, **`b4`** — the first three array bytes, also the last three pairs of the numeric hex form in reverse pair order.
- **Little-endian** — the least significant byte is at the lowest index; reversing whole byte positions gives the usual high-to-low written number.
- **Nibble** — four bits per hex digit. Keep the leading zero in `0100` when joining B and 4.
- **`0x7d`** and **`}`** — two ways to display byte 125, in the fourth slot. Only one byte is stored.

## Reproduce the display without using an address

Use the digits from the screen as a **fixed integer**, not as a live object ID. This lets you reproduce the representation on another run or machine. Nothing below accesses memory by address. The loop is a shorter teaching version of the class’s eight mask-and-shift assignments; the two calls at the end test what happens when the reader agrees or disagrees with the writer’s byte order.

**Annotated teaching version — guide-added comments; independently executed:**

```python
pattern = 0x2847db4adf0                # Fixed input copied from the screen; just a number.
octets = bytearray(8)                  # Create a new array of eight zero bytes.
print(f"Bytes: {octets}")              # Show the zero-filled starting state.
for i in range(len(octets)):           # Visit indices 0 through 7, lowest place first.
    octets[i] = (pattern >> (8 * i)) & 0xff  # Move the chosen byte down; keep its eight bits.
print(f"Bytes: {octets}")              # Show the same array after filling it; } appears.
print(octets.hex(" "))                 # Show every byte as a two-digit hex pair, including 7d.
print(octets[3], chr(octets[3]))       # Show 125 and its corresponding character: }.
print(hex(int.from_bytes(octets, "little")))  # Read index 0 as the lowest byte: original number.
print(hex(int.from_bytes(octets, "big")))     # Read index 0 as the highest byte: different number.
```

**Independently verified output (fixed data, not a second classroom run):**

```text
Bytes: bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00')
Bytes: bytearray(b'\xf0\xad\xb4}\x84\x02\x00\x00')
f0 ad b4 7d 84 02 00 00
125 }
0x2847db4adf0
0xf0adb47d84020000
```

Follow the fourth pass: index 3 moves the number right by 24 bit positions. The mask keeps only `7d`; the assignment puts that integer into slot 3 without changing the original number. Later, the little-endian reader gives that byte its original place back. The big-endian reader uses the *same unchanged slots* but gives each a different weight. This is why the classroom statement “unstuff them in the right order” matters.

**Key items to scan**

- **`bytearray(8)`** — creates eight zeros; assigning to a slot changes this array rather than creating a new one.
- **`(pattern >> (8 * i)) & 0xff`** — isolates exactly the byte for index `i`; nothing is fetched from an address.
- **`octets.hex(" ")`** — requests uniform hex pairs so the fourth slot appears as `7d`, not `}`.
- **`chr(octets[3])`** — makes the numeric-byte-to-character connection explicit for the single value 125.
- **`int.from_bytes`** — turns the ordered bytes back into an integer. Only the `"little"` interpretation undoes this particular write order.

## Why does an eight-byte number occupy 24 bytes? (0:46:00–0:54:56)

The earlier example recovered **`123.45`** from a reference. Now the question changes: what belongs to the float object, and which part actually encodes its number? The roadmap names **`floatBytes.py`** next. At 0:46:14 its launch command is at the PowerShell prompt, not yet followed by a result. At 0:48:00 the completed run is visible. Its new input is **`12.34`**; the identity from the previous example is not the address of this float.

*Annotated teaching version — guide-added comments; safe opening statements from the editor at 0:47:00. The size and address below belong to the classroom run.*

```python
import sys                      # Supplies the object-size query.
f = 12.34                       # Bind f to the float whose value will be examined.
objectSize = sys.getsizeof(f)    # 24 bytes in the displayed interpreter, including overhead.
address = id(f)                 # Identify this live object; does not read its contents.
print(f, objectSize, hex(address))  # Adapted display: value, size, identity in hexadecimal.
```

*Observed PowerShell command and output, 0:48:00; spacing and the wrapped byte row reflowed for print. These are the instructor’s labels, including the precision label.*

```text
py .\src\floatBytes.py

float value: 12.34, object memory (bytes): 24, object address: 0x1fb4537adf0

Byte values of object:
0x3  0x0  0x0  0x0  0x0  0x0  0x0  0x0
0xf0  0xe5  0x78  0x15  0xfc  0x7f  0x0  0x0
0xae  0x47  0xe1  0x7a  0x14  0xae  0x28  0x40

Float type value bytes:
0xae  0x47  0xe1  0x7a  0x14  0xae  0x28  0x40

Sign bit:         0 (Positive)
Biased Exponent:  1026 (Binary: 10000000010)
Mantissa:         1000101011100001010001111010111000010100011110101110
Precision:        2^52 -> 2.220446049250313e-16

value: 12.34, type: <class 'float'>
```

To read these bytes, first decide *whose format* applies. The classroom interpreter’s object header tells Python about the object. The IEEE format describes only its numeric payload. The type answers two practical questions: how much data follows the header here, and how should those bits be interpreted? In the caption at 0:51:50 the size is called “24 bits”; the three eight-**byte** groups in the run settle the units.

| Byte offsets in this run | Read these eight bytes as… | What the class finds |
|---|---|---|
| 0–7 | A little-endian reference count, least-significant byte first | `03 00 00 00 00 00 00 00` means 3, not 3 followed by seven significant hex zeros. The count includes references held by the runtime; the dump alone does not name three variables. |
| 8–15 | A reference to the type object | `f0 e5 78 15 fc 7f 00 00` represents `0x00007ffc1578e5f0`. At 0:53:21 the instructor starts to say this is the float’s own ID, then corrects it: this is the type reference, **not** `0x1fb4537adf0`. |
| 16–23 | The value, in the order its bytes were stored | `ae 47 e1 7a 14 ae 28 40` is the eight-byte float payload. Read as a single numeric word, most significant byte first, it is `4028ae147ae147ae`. |

The editor’s explanatory text gives a reference-count *example* of 1; the live bytes give 3. Reference counting lets this implementation know when an ordinary object no longer has any references and can be reclaimed. That is not a rule that a collector must scan every object looking for a zero. Nor is this header layout a Python file format: size, identity and object bookkeeping are implementation details. To study the number, use just its payload, as in the next example.

**Key items to scan**

- **`123.45`** — preceding reference demonstration; **`12.34`** — new float whose bytes are on screen.
- **`floatBytes.py`** — classroom script that separates the object dump from “Float type value bytes.”
- **`sys.getsizeof(f)`** — whole object size here, 24 bytes; **`id(f)`** — identity of the live float, not its bit pattern.
- **`ob_refcnt`** — count of references, 3 in the run; **`ob_type`** — reference to the float’s type, not the float’s own ID.
- **`ob_fval`** — last eight bytes, the part to interpret using the binary64 format.

## Read the fields of 12.34 without reading process memory (0:54:56–0:59:11)

Why does the first byte printed start with **`ae`**, when the instructor says the sign bit is zero? The dump is in little-endian *byte* order. The sign is the high bit of the **last** byte, **`40`** hexadecimal, or `01000000` in binary. Do not reverse the bits inside each byte. Here is the safe alternative: ask **`struct.pack`** to make a new eight-byte representation of the *value*. This does not access an address, depend on the classroom header layout or alter the original float.

*Annotated teaching version — guide-created and independently executed, not the instructor’s script.*

```python
import struct                                # Encode the value in a specified format.
f = 12.34                                    # Same numeric input as in class.
b = struct.pack('<d', f)                     # d: binary64; <: least-significant byte first.
word = int.from_bytes(b, 'little')           # Read that same byte order as an unsigned integer.
sign = word >> 63                            # Move just the high bit down to position zero.
E = (word >> 52) & 0x7ff                     # Keep the 11 bits immediately below the sign.
T = word & ((1 << 52) - 1)                   # Keep every one of the 52 stored fraction bits.
print(b.hex(' '))                            # Show all eight bytes, separated by spaces.
print(sign, E, E - 1023)                     # Sign, stored exponent, actual exponent.
print(f'{T:052b}')                           # Exactly 52 binary digits, including leading zeros.
print((1 + T / 2**52) * 2**(E - 1023))      # Rebuild this positive, normal float.
```

*Independently verified output:*

```text
ae 47 e1 7a 14 ae 28 40
0 1026 3
1000101011100001010001111010111000010100011110101110
12.34
```

The instructor’s script gets the same fields one byte at a time. The interesting boundary is the penultimate byte **`0x28`**: its upper four bits (`0010`) finish the exponent; its lower four (`1000`) start the fraction. Dropping that whole byte, or using it twice without separate masks, would corrupt both fields. The following safe fixture follows the classroom grouping, with an actual loop in place of its six written-out OR assignments; its input comes only from the packed value above.

*Annotated teaching version — independently tested; continue from the previous example.*

```python
sign_mask = b[7] & 0x80                      # 0x40 & 0x80 = 0, an unset sign.
E_bytes = ((b[7] & 0x7f) << 4) | ((b[6] & 0xf0) >> 4)  # Join 1024 and 2.
T_bytes = (b[6] & 0x0f) << 48               # Put the 8 from 0x28 into the top fraction nibble.
for i in range(5, -1, -1):                   # Take bytes 5, 4, 3, 2, 1, 0 in that order.
    T_bytes |= b[i] << (8 * i)               # Fill this byte’s position; earlier bits stay set.
print(sign_mask, E_bytes, hex(T_bytes))      # Check all three fields against the word method.
```

*Independently verified output:* `0 1026 0x8ae147ae147ae`.

| Byte used | Part added | Fraction accumulator after this step (hex) |
|---|---|---|
| 6: `28` | Low nibble `8`, shifted 48 | `8000000000000` |
| 5: `ae` | Shift 40 | `8ae0000000000` |
| 4: `14` | Shift 32 | `8ae1400000000` |
| 3: `7a` | Shift 24 | `8ae147a000000` |
| 2: `e1` | Shift 16 | `8ae147ae10000` |
| 1: `47` | Shift 8 | `8ae147ae14700` |
| 0: `ae` | No shift | `8ae147ae147ae` |

*Guide-derived trace checked against the code at 0:49:00 and output at 0:48:00.* The printed “Mantissa” row contains all 52 stored bits, including the low byte; no stored fraction bit is skipped. It does not print the **implicit leading one** of a normal number, because there is no separate bit for that one in the payload. The sign mask, unlike a normalized sign digit, would equal 128 rather than 1 on a negative input; it still works as a truth test.

**Key items to scan**

- **`ae`** — first byte in memory order; **`40`** — last byte, containing the zero sign bit.
- **`struct.pack`**, **`'<d'`** — produce eight little-endian binary64 bytes for the value, without reading process memory.
- **`int.from_bytes(b, 'little')`** — turns those bytes into the word whose high bit is bit 63.
- **`0x28`** — split this byte, giving `2` to the exponent and `8` to the fraction.
- **`&`**, **`<<`**, **`>>`**, **`|`** — select bits, move them into position, then join disjoint fields. **`|=`** updates the accumulator.
- **Implicit leading one** — included in the normal number’s precision, but not among the 52 digits printed after `Mantissa:`.

## What the IEEE diagram explains, and what “precision” measures (0:55:33–1:01:38)

The instructor opens the local **`IEEE Standard 754-2019.pdf`**. In its contents (0:56:00), §3.4, *Binary interchange format encodings*, leads to printed page 19. Figure 3.1, *Binary interchange floating-point format*, shows how to split the word: sign **`S`**, biased exponent **`E`**, and trailing significand field **`T`** (0:57:00–0:57:23). At 0:58:00 PowerShell scrollback shows the document command `& '.\doc\IEEE Standard 754-2019.pdf'`. The page identifies zero and all-ones exponents as reserved: zeros/subnormals use the former, infinities/NaNs the latter. The normal-number calculation below is for neither reserved case.

| Figure 3.1 applied to this word | What the bits mean |
|---|---|
| `S = 0` (bit 63) | Use the positive sign. |
| `E = 10000000010₂ = 1026` (bits 62–52) | Subtract the fixed bias: `1026 − 1023 = 3`. The instructor’s spoken “more like two” comes from noticing the low `10₂`; removing a high bit would subtract 1024, **not** the bias 1023. |
| `T = 0x8ae147ae147ae` (bits 51–0) | The integer is 2443202797848494. Divide by `2^52` to place those digits after the binary point, then add 1 for the unstored leading bit. |
| Normal value: `(-1)^S × (1 + T/2^52) × 2^(E−1023)` | The binary significand begins `1.10001010…₂`; multiplying by `2^3` moves the point three places, to `1100.01010…₂`. This gives a number between 8 and 16, as 12.34 must be. |

Like decimal scientific notation, this uses a significand times a power, but the base is two. For this normal number the *fractional part* is between 0 and 1; the full significand is at least 1 and less than 2. The transcript says “54 bits” and describes normalization between 0 and 1 (0:56:33). Here the correct count is 52 stored fraction bits plus one implicit bit: **53 bits of precision**. The largest normal *unbiased* exponent is +1023; the displayed 1026 is a *biased field*, not an exponent of 1026 in the value formula.

The instructor calls “mantissa” the older name and recalls its association with the fractional part of a logarithm. Use **significand** for the complete factor multiplying the power of two; the displayed `Mantissa:` line gives only its trailing field. A large exponent changes the scale, not the number of available significant bits. That is why precision matters even before a calculation overflows or underflows.

*Independently tested precision check — guide-added comments; not a classroom run.*

```python
import math                                  # Provides the spacing at a specified float.
print(2**52, 2**-52)                         # Count of fraction patterns; gap just above 1.
print(math.ulp(12.34))                       # Gap to the next larger float at this magnitude.
print(format(12.34, '.48f'))                 # Display the binary64 approximation in decimal.
```

```text
4503599627370496 2.220446049250313e-16
1.7763568394002505e-15
12.339999999999999857891452847979962825775146484375
```

The historical `Precision: 2^52 -> 2.220446049250313e-16` combines a positive exponent in its label with the numerical value of **2⁻⁵²**. That small number is the spacing just above 1, not a fixed absolute gap everywhere. At 12.34 the exponent is 3, so the adjacent spacing is eight times that large: **2⁻⁴⁹**. Python’s short display `12.34` is not claiming that the stored value equals the exact decimal fraction. At 1:00:36 the instructor previews computations with very large or very small values; their applications are left for Wednesday, not demonstrated here. The sign-off at 1:01:20 invites questions in Discord.

**Key items to scan**

- **`IEEE Standard 754-2019.pdf`** — local document consulted in class; Figure 3.1 distinguishes a stored field from the number inferred from it.
- **`S`** — sign; **`E`** — stored exponent, 1026 here; **`T`** — trailing significand bits, not the complete significand.
- **53 bits of precision** — for a normal binary64 value, one inferred leading digit plus 52 stored digits; no 54th bit.
- **Significand** — in this example `1.10001010…₂`; multiply it by `2³`, not `2²`.
- **2⁻⁵²** — gap above 1; **2⁻⁴⁹** — gap above 12.34. An ULP is one unit in the last place at the selected magnitude.

## Syntax to remember

| Pattern | Read it as an action |
|---|---|
| `value & 0xff` | Keep just the last eight bits; their value fits in one byte. |
| `(value & 0xff00) >> 8` | Select the next eight bits, then move them into the low byte position. |
| <code>mem &#124;= octet[i] &lt;&lt; (8 * i)</code> | Put one byte back in its original place and preserve all positions already filled. |
| `octets.hex(' ')` | Show every byte with two hex digits, including printable bytes such as `7d`. |
| `rawBytes[16:]` | In the classroom layout only: skip the two eight-byte header fields and keep the rest. |
| `struct.pack('<d', f)` | Encode the value as an eight-byte little-endian double, independent of the Python object header. |


## Knowledge check

1. The class ID ends in `adf0`. Why does masking it with `0xff00` not yet give a value you can assign to a single byte? What two results should you expect, before and after shifting?
2. You have stored `f0` at index 0 and `ad` at index 1. A classmate combines them with OR and gets `fd`. Which step did they omit? Would their result be correct if the container were only eight bits wide?
3. The printed byte array contains a `}` between two hex escapes. Does this add punctuation to the float, end a Python expression, or record a byte? How would you check without interpreting text?
4. After reassembly the first program prints the original hex number. Has it reconstructed the floating-point encoding of `123.45`? Why did the instructor need a separate step to display the float again?
5. In the second run the display reports 24 bytes. Which eight bytes does the binary64 standard describe? What does the middle group identify?
6. The biased exponent for `12.34` is `1026`. What exponent actually scales the value? Explain why the answer fits even without looking at the bits. Does moving the binary point give a float more significant digits?

### Answer key

1. AND clears the low eight bits but leaves the selected bits **in their original positions**: the intermediate integer is `0xad00`. Shift it right eight places to obtain `0xad`. A byte slot accepts the latter value; it does not automatically slide the high bits into place.
2. `0xf0 | 0xad` overlays both bytes in the **same** eight columns. Move `0xad` left eight places before combining: `0xad00 | 0xf0` gives `0xadf0`. An eight-bit destination cannot hold both bytes. Deliberately narrowing the correct result to eight bits keeps `f0`, not `fd`.
3. It records one byte, `7d`. In a bytes representation Python can show printable ASCII characters directly. Displaying the sequence with `.hex(' ')` makes that slot unambiguous without decoding it as text.
4. No. It reconstructed the integer identifying the live object, just as reassembling the digits of a street address does not rebuild the building. The classroom program’s implementation-specific final step referred back to that existing object; numeric equality between the two ID values was already testable without it.
5. IEEE binary64 describes the final eight **payload** bytes in this particular object layout. The first group was interpreted as the reference count and the middle group as the pointer to the **type object**. That pointer is not the address printed for the float instance.
6. `1026 − 1023 = 3`. The normalized significand is at least 1 and less than 2; multiplying by 8 puts the value between 8 and 16, just where `12.34` belongs. Moving the binary point changes scale, not the number of available significand bits.

## Library of definitions

| Term | Meaning in this session |
|---|---|
| Identity; object ID | The identity of one live Python object; `id(obj)` returns an integer representing it. In CPython this is its address. An ID is not the object’s value or a persistent serial number. |
| Octet; nibble | An octet is eight bits, displayed by two hexadecimal digits. One digit covers four bits, sometimes called a nibble. |
| Most/least significant | The positions with the greatest/least numerical weight. The least-significant bit is the ones place, regardless of which byte a machine stores first. |
| Mask | An integer whose bit pattern selects positions. With AND, a mask bit of one passes the corresponding input bit; a zero clears it. |
| Shift | Moves bits between place values. A right shift here brings a selected byte down to the ones end; a left shift restores its original place. |
| Augmented assignment | Combines an operation with writing the result back to the target. For the integer accumulator, <code>mem &#124;= part</code> preserves the bits already set and adds any set bits in `part`. |
| Byte order; little-endian | Byte order says which byte occupies the first storage position. Least-significant byte first is little-endian; it does not reverse the bits or hex digits *within* a byte. |
| Representation versus decoding | `repr`-style bytes output describes stored byte values; decoding interprets a byte sequence as characters under a chosen text encoding. The displayed brace needed no UTF-8 conversion. |
| Header; payload | Runtime bookkeeping versus the data representing the value. The 24-byte float object observed in class includes both; binary64 alone occupies eight bytes. |
| Reference count; type pointer | The count tracks references in the demonstrated CPython build. The type pointer identifies the type object, which provides the information and operations appropriate to the instance. Layout and counts are implementation-dependent. |
| Binary64 | IEEE 754 double-precision binary format: one sign bit, eleven exponent bits and 52 stored fraction bits. It specifies the numeric encoding, not a Python object header. |
| Biased exponent | A stored offset exponent. For a normal binary64 number, subtract 1023 to get the power of two that scales the significand. |
| Fraction field; significand | The field stores the bits after the binary point. For a normal value, the implied leading 1 plus those bits form a 53-bit significand. The classroom output calls its stored field “Mantissa.” |
| Precision versus range | Precision controls how finely neighboring values can be distinguished; range controls how large or small their magnitudes can be. A bigger exponent does not buy extra fraction bits. |

## Supplemental references

These references clarify Python guarantees and implementation-specific behavior; they are not evidence that a particular line ran in class.

- [Python built-ins: `id`, `bytes`, `bytearray`](https://docs.python.org/3/library/functions.html#id) — identity is guaranteed during an object’s lifetime; interpreting it as an address is a CPython detail.
- [CPython C API: common object structures](https://docs.python.org/3/c-api/structures.html#pyobject) — distinguishes the reference count from the pointer to the corresponding type object; build details matter.
- [Python `struct`: packed binary data](https://docs.python.org/3/library/struct.html) — an explicit byte order and `d` format let you study the double value without reading an object header.
- [Python tutorial: floating-point arithmetic](https://docs.python.org/3/tutorial/floatingpoint.html) — why the machine value can be a binary approximation even when the displayed decimal looks exact.

## Historical screen-code appendix

Editor numbers are a gutter for reference, not text to type. The arithmetic listings preserve the classroom spelling; annotations and independently executed versions appear in the main lesson.

### The source file before the hand calculation · 0:02:00 and 0:08:00

Assembled visual reconstruction of **`jstruct.py`** in Sublime Text. The first frame supports lines 1–30; the second supports lines 10–37 and supplies the lower portion. The overlapping lines and gutter agree, but two editor views do not prove a save or an execution. Line 2 extends beyond the right edge. This is a **non-runnable historical listing**: the numbers and bar at the left reproduce the gutter, not Python source. The platform-specific operation below the arithmetic is deliberately not transcribed.

```text
 1 | """
 2 | Note: The Python language specification only guarantees that id() returns a u[remainder not visible]
 3 | """
 4 |
 5 | import sys
 6 | import ctypes
 7 |
 8 | idSize = (sys.maxsize.bit_length() + 1)
 9 | print(f"Python ID size: {idSize} bits")
10 |
11 | octet = bytearray(int(idSize/8))
12 | print(f"Bytes: {octet}")
13 |
14 | obj = 123.45
15 | objId = id(obj)
16 | print(f"obj id: {hex(objId)}")
17 |
18 | octet[0] = objId & 0xff
19 | octet[1] = (objId & 0xff00) >> 8
20 | octet[2] = (objId & 0xff0000) >> 16
21 | octet[3] = (objId & 0xff000000) >> 24
22 | octet[4] = (objId & 0xff00000000) >> 32
23 | octet[5] = (objId & 0xff0000000000) >> 40
24 | octet[6] = (objId & 0xff000000000000) >> 48
25 | octet[7] = (objId & 0xff00000000000000) >> 56
26 |
27 | print(f"Bytes: {octet}")
28 |
29 | mem = octet[0]
30 | mem |= octet[1] << 8
31 | mem |= octet[2] << 16
32 | mem |= octet[3] << 24
33 | mem |= octet[4] << 32
34 | mem |= octet[5] << 40
35 | mem |= octet[6] << 48
36 | mem |= octet[7] << 56
37 | print(hex(mem))
```

- **1–16:** The triple-quoted opening note is text, not an executable check. The program then obtains a width estimate, allocates the zero-filled array, and obtains the float object's identity integer. The two print labels describe the container and identity, respectively.
- **18–27:** Compare the adjacent masks: the two `f` digits move left as zero pairs are appended; the right shifts increase by eight. Index zero needs no shift. Both array displays use the same `Bytes:` label; their positions distinguish *before* from *after* filling.
- **29–37:** The later inverse is already visible on screen: the low byte starts the integer, and each higher byte is shifted left to its own position and combined with it. Line 37 asks for hexadecimal display of the rebuilt number, not its ID. No command or resulting terminal lines accompany these editor views.

The later editor at 0:23:00 shows the file under `C:\com\joesta\Numerical\src\jstruct.py`. Line 38 is blank. Line 39 is an implementation-specific address-to-object operation, not a numeric conversion. Its operational expression is intentionally omitted. The remaining gutter, visible at 0:23:00 and 0:34:00, reads:

```text
40 |
41 | print(back)
42 | print(type(back))
```

Lines 41–42 request separate value and type displays; the later PowerShell run is transcribed in the main lesson at 0:34:55. Overlapping views at 0:35:00, 0:36:52 and 0:38:00 corroborate the arithmetic listing and print statements; seeing the same scrollback again does not establish a second execution.

### Hand calculation: first align the ones places · 0:12:00–0:14:14

These numbered rows are **plain-text scratch work, not Python and not terminal output**. At 0:11:00 only the first row is populated. At 0:12:00 a second row of eight ones appears at the left margin:

```text
 1 | 1010101010101001101010101010101001010001000
 2 | 11111111
```

At 0:13:00 the mask has leading zeros, so its eight ones end under the eight rightmost bits of the input. At 0:14:00 the `AND` label and dashed rule are present; at 0:14:14 the instructor has entered only the blocked columns of the answer. The line below is in progress, not clipped. The final eight low-result bits are not visible in this sampled state.

```text
 1 | 1010101010101001101010101010101001010001000
 2 | 0000000000000000000000000000000000011111111 AND
 3 | -------------------------------------------
 4 | 00000000000000000000000000000000000
```

### Hand calculation: the second mask before and after shifting · 0:17:00–0:18:00

At 0:17:00 the eight ones have moved one byte left. Read straight down: only columns beneath those eight mask ones can survive in row 4; the eight low bits must now all be zero.

```text
 1 | 1010101010101001101010101010101001010001000
 2 | 0000000000000000000000000001111111100000000 AND
 3 | -------------------------------------------
 4 | 0000000000000000000000000000101001000000000
```

At 0:17:54 (also 0:18:00) rows 1–3 still show the mask calculation, but row 4 has been changed to the **right-shifted** answer. It no longer shows the raw AND. The instructor's verbal “into the abyss” refers to the low positions dropped during that right shift.

```text
 1 | 1010101010101001101010101010101001010001000
 2 | 0000000000000000000000000001111111100000000 AND
 3 | -------------------------------------------
 4 | 0000000000000000000000000000000000001010010
```

### Plain-text scratchpad: the mask, vocabulary, and hand-worked OR (0:20:00–0:32:00)

This is the right-hand scratch buffer, **not Python source or printed output**. The complete state below is visible at 0:31:00; the lower rows are independently legible again at 0:32:00. Gutter numbers and the two spaces after each number are editorial separators. The 43 binary digits on line 1 are the digits actually written, not a transcription padded out to 64. Lines 2–4 use that same written width. The eight mask bits at the right preserve **`10001000`**; rows 23–28 later work on just the rightmost 16 positions. Line 18 really ends in a plus, unlike line 30 in the Python file.

```text
 1  1010101010101001101010101010101001010001000
 2  0000000000000000000000000000000000011111111 AND
 3  -------------------------------------------
 4  0000000000000000000000000000000000010001000
 5
 6  mem
 7  10001000
 8
 9
10  & AND
11  ^ XOR
12  | OR
13
14  = assignment
15
16  &=
17  ^=
18  |+
19
20  temp = octet[1] << 8
21  mem = mem | temp
22
23  mem:
24  0000000010001000
25  0101001000000000
26  ---------------- OR
27  0101001010001000
28  0101001010001000
29
30  10010000
```

At 0:20:00 the mask example occupies rows 1–4 and row 6 is blank. At 0:21:00 rows 6–7 identify the first byte, row 10 reads **`& AND`**, and row 11 has only the caret symbol and a space. At 0:22:00 row 14 reads **`= assignment`**, row 16 holds **`&=`**, and row 17 is still just a caret. At 0:22:10 rows 17–18 show **`^=`** and **`|+`**. The program itself uses **`|=`**: do not type the scratch spelling in its place.

Rows 20–21 separate the shift from the OR update. In the completed diagram, rows 24–25 are the two operands; rows 27 and 28 visibly contain the *same* 16 digits, matching the low 16 on row 1. The remaining eight digits on row 30 are an earlier narrow-width scratch state, not a third operand or the result of combining the two bytes.

### Earlier scratch states: the meaning changes as the rows are edited (0:24:00–0:30:00)

Each entry below describes a separate captured state of the same working area. The line numbers refer to the frame named in that row. Do not read the edits as successive Python assignments or infer the exact keystrokes between stills.

| Requested frame | Numbered scratch state as visible | Significance |
|---|---|---|
| 0:24:00 | 23: `mem = 10001000`; 24: `01010010` | Original low byte and the next unshifted byte, on separate rows. |
| 0:25:00 | 23: `mem = 10001000`; 24: blank | Code and scratchpad are side by side; this is not yet the shifted result. |
| 0:26:00 | 23: `mem = 10001000`; 24: `0101001000000000` | The wide interpretation: the next group with eight zeros at the right. |
| 0:26:10 | 24: `0101001000000000`; 25: blank; 26: `0000000` | **Seven** typed zeros in this still. The class discusses an eight-bit zero outcome; the partial row is not an eight-digit display. |
| 0:27:00 | 24: `0101001000000000`; 26: `01010010` | The wider result remains above the restored eight-digit starting group. |
| 0:28:00 | 24: `0101001000000000` (selected); 26: `10010000` | A different eight-position state, consistent with three one-place shifts of the original byte when overflow is discarded. |
| 0:29:00, 0:29:44, 0:30:00 | 23: `mem:`; 24: `0000000010001000`; 25: `0101001000000000`; 26: `---------------- OR`; 27–28: blank; 29: `10010000` | Two aligned operands above a rule. The instructor discusses the other leading zeros; they are not written on these rows. The completed result rows first appear among these samples at 0:31:00. |

### The brace scratchpad · 0:38:46–0:45:00

**Observed right-hand plain-text buffer: manual comparison, not Python and not terminal output.** At 0:38:46 the copied text retains an opening quote on line 32; there is no visible closing quote or byte-array wrapper on this line. Line 34 is the number as printed.

```text
32  '\xf0\xad\xb4}\x84\x02\x00\x00
33
34  0x2847db4adf0
```

At 0:39:00 some escape markers have gone but others remain; the numeric prefix is gone from line 34. At 0:40:00 the brace is still there even though all escape markers have been removed. These are edits to copied *text*, not mutations of the byte array.

```text
32  f0adb4}\x84\x02\x00\x00
33
34  2847db4adf0
```

```text
32  f0adb4}84020000
33
34  2847db4adf0
```

At 0:42:00 and 0:42:13, line 33 contains four separated nibbles, with a gap between A and B, and the numeric comparison has moved down to line 35. The student’s question about D helps explain the gap; the caret is not part of the data.

```text
32  f0adb4}84020000
33  1111 0000 1010  1011
34
35  2847db4adf0
```

At 0:43:00, three complete bytes are visible. The instructor later selects two leading bits of the third byte (0:44:00) and then the first `f` on line 32 (0:44:10). The same three-byte conversion remains visible at 0:45:00. Nothing on these lines shows a fourth binary group for the brace; its value `01111101` in the teaching section is an independent check, not recovered classroom text.

```text
32  f0adb4}84020000
33  11110000 10101101 10110100
34
35  2847db4adf0
```

### `floatBytes.py`: object-size notes, numeric fields and observed run

*Assembled visual reconstruction.* The left gutter below is the Sublime Text line number, not source to type. The header and wrapping are visible at 0:51:00, 0:51:51 and 0:53:53; the byte processing at 0:47:00, 0:47:51 and 0:49:00; the two formatting statements at 0:53:00. Overlapping lines agree; this establishes the visible sections, not the contents of an independently recovered file. The bracketed row is an editorial omission, not an editor comment. The safe alternative in the main text supplies eight value bytes without reading process memory.

```text
 1  """
 2  Python Object:
 3  Offset Size Field Meaning
 4  0-7 8 bytes ob_refcnt (Reference Count)\x01\x00\x00\x00\x00\x00\x00\x00 -> 1
 5  8-15 8 bytes ob_type (Type Pointer)`c\x0f#\xf9\x7f\x00\x00 -> Memory address pointing to float type object
 6  16-23 8 bytes ob_fval (The actual float value)\xaeG\xe1z\x14\xae(@ -> IEEE 754 double-precision value
 7  """
 8
 9  import ctypes
10  import sys
11
12  f = 12.34
13  objectSize = sys.getsizeof(f)
14  address = id(f)
15  print(f"\nfloat value: {f}, object memory (bytes): {objectSize}, object address: {hex(address)}")
16  print("\nByte values of object:")
17  [Raw object-memory read omitted; supplies rawBytes for the following display.]
18  for byte in rawBytes:
19      print(f"{hex(byte)}  ", end = "")
20
21  print("\n\nFloat type value bytes:")
22  floatBytes = rawBytes[16:]
23  for byte in floatBytes:
24      print(f"{hex(byte)}  ", end = "")
25
26  sign = floatBytes[7] & 0x80
27  exponent = (floatBytes[7] & 0x7f) << 4 | ((floatBytes[6] & 0xf0) >> 4)
28  mantissa = (floatBytes[6] & 0x0f) << 48
29  mantissa |= floatBytes[5] << 40
30  mantissa |= floatBytes[4] << 32
31  mantissa |= floatBytes[3] << 24
32  mantissa |= floatBytes[2] << 16
33  mantissa |= floatBytes[1] << 8
34  mantissa |= floatBytes[0]
35
36  print(f'\n\n{"Sign bit:":<18}{sign} ({"Negative" if sign else "Positive"})')
37  print(f'{"Biased Exponent:":<18}{exponent} (Binary: {exponent:b})')
```

- **Lines 1–7:** The opening triple-quoted text is an illustration, not console output. Its `-> 1` does not contradict the 3 observed during the run: the two records are different. The printable `G`, `z`, `(` and `@` on line 6 stand among escaped bytes in that illustrative string; the terminal prints hex instead.
- **Lines 12–24:** The first loop displays every captured byte; `end = ""` keeps them on one line until the terminal wraps it. The slice on line 22 skips the **16 header bytes**, not 16 bytes of the number. The second loop prints only the eight value bytes. The `py .\src\floatBytes.py` result is fully visible at 0:48:00. At 0:55:00 a later bare `.\src\floatBytes.py` is followed by another prompt without intervening text; that command is not the source of a second observed dump.
- **Lines 26–34:** Row 26 leaves the sign in its original position, so its mask gives `0` for positive 12.34 or `128` for the negative counterpart. The seven remaining high-byte bits and four from the next byte make the 11-bit exponent. Lines 28–34 use that next byte’s *other* four bits plus six whole bytes, assembling exactly 52 fraction bits. No stored value byte is skipped. The implicit one is never read from a byte.
- **Lines 36–37:** Each field label is left-aligned in a width of 18, then the extracted value is displayed. The positive label comes from the false branch of the sign test. The terminal also shows the mantissa, precision and final type/value lines; their print statements lie below the recovered gutter and are not reconstructed from the output.

### The reference actually opened: IEEE 754-2019

At 0:46:00, lines 7–10 of `Roadmap.md` list `floatBytes.py`, `doc/IEEE Standard 754-2019.pdf`, `ieee754App.py`, and `ulp.py` in that order. A roadmap entry is not evidence that the corresponding application ran. The PDF contents list §3.4 on printed page 19 (0:56:00); at 0:57:00 Figure 3.1 is on screen, and at 0:57:23 “biased exponent” is highlighted. The caption under the diagram reads **Figure 3.1—Binary interchange floating-point format**. Its three widths are `1`, `w`, and `t = p − 1`: the trailing field does not explicitly store the first digit of a normal significand.

## Micro-Learning Insights

*Removable appendix · Use these prompts to reconstruct the lesson without the listings.*

- **Ask “bytes of what?” first.** At 0:07:54 the source number is `id(obj)`. At 0:47:07 the subject changes to the object holding `12.34`. Only the second demonstration reaches the floating-point payload.
- **Align the ones place, not the left edge.** Leading zeroes do not change a positive integer. Adding them on paper makes it clear why `0xff` leaves the bottom eight bits and rules out every result above 255 (0:10:12–0:14:44).
- **Selection and placement are different jobs.** In `(value & 0xff00) >> 8`, stop after the parentheses: the chosen bits are still one byte too far left. Only the shift makes them fit at index 1 (0:15:43–0:18:44).
- **Name the width before predicting a shift.** The student’s “shift all the bits out” argument works for an eight-bit destination. Python’s integer expression can keep the widened intermediate. The assumption, not the drawing, decides the answer (0:25:49–0:27:58).
- **Reassemble without overlap.** An OR accumulates fields because their destination positions have already been separated. If the low byte changes as you insert the next byte, inspect the shift first (0:28:20–0:31:07).
- **Group hex digits in pairs before changing order.** In the first run `f0 ad b4 7d` are successive bytes at the low end. Reverse the order of the *pairs*, not the digits inside them, to compare against ordinary hexadecimal notation (0:38:04–0:40:12).
- **Make a mixed byte display uniform.** `}` and `\x7d` can denote the same byte. For diagnosis use `.hex(' ')` rather than trying to decode the entire buffer as a string (class question, 0:43:24–0:45:26; guide clarification).
- **A pointer to a type is not the instance’s ID.** At 0:53:21 the instructor catches this mix-up. Label the three groups “reference count / type / data” before interpreting any individual byte of the 24-byte object.
- **Check a decoded exponent against familiar magnitudes.** `12.34` lies between `2**3` and `2**4`; a claimed normalized exponent of 2 cannot fit. This is a quick independent check on the mental estimate at 0:58:13.
- **Use the diagram rather than a memory aid.** Figure 3.1 of the classroom reference, *IEEE Standard 754-2019*, separates the sign, biased exponent, and trailing field. The diagram labels the last width `p − 1`, a reminder that precision and stored fraction length differ (0:57:00).
- **Distinguish the field from the precision.** The printed 52-bit fraction does not display the implicit leading 1. Normal binary64 values have 53 significant binary digits, not 52 or 54. A larger exponent spreads those representable values farther apart; it does not create new digits (0:56:33–1:00:58).
