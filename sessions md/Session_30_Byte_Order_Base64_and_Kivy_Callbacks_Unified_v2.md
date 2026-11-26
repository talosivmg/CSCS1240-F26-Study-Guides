# From byte order to the Encode button

CSCS1240F26 · Session 30 · Worked teaching companion · Unified v2

When you press **Encode**, why do six typed characters turn into eight different ones? And how does that particular button know which Python method to call? The class answers those questions in order. First, a reader and a writer have to agree about what the bits mean. RFC 4648 makes that agreement precise for Base64. Finally, Kivy connects a press in the window to the Python code that acts on the input. The conversion and the click are separate mechanisms; understanding both lets you predict what should happen before running the program.

**Find your place in the recording**

| Start | What to watch for |
|---|---|
| [00:00](https://www.youtube.com/watch?v=2DAz1gVHTFM&t=0s) | Why a machine reports `little` and what the 16-byte calculation actually measures. |
| [15:00](https://www.youtube.com/watch?v=2DAz1gVHTFM&t=900s) | Follow the question about Internet governance to the document that specifies Base64. |
| [28:32](https://www.youtube.com/watch?v=2DAz1gVHTFM&t=1712s) | Regroup three bytes into four alphabet indexes; test the result in the converter. |
| [36:07](https://www.youtube.com/watch?v=2DAz1gVHTFM&t=2167s) | Read the app from its entry point back to the widgets; locate the font-size change. |
| [52:33](https://www.youtube.com/watch?v=2DAz1gVHTFM&t=3153s) | Distinguish registering a method from calling it, then diagnose the two apple experiments. |

**Evidence.** [JoesTA’s recording, as supplied](https://www.youtube.com/watch?v=2DAz1gVHTFM&t=155s) opens at a playback offset; this guide starts at the beginning. The saved same-ID transcript contains 1,832 ordered timestamped entries, from the greeting at 00:00 through “recording” at 1:03:42. It reports a duration of 1:03:45; the video stream runs to 1:03:50.083. Cue durations and caption authorship are not recorded in this export. The final seconds are not treated as evidence of either speech or silence.

The visual record includes every whole-minute target, the closing view and selected events. A timestamp names the requested seek, not a frame-exact edit or keystroke. In the worked examples, guide-added comments teach the behavior; historical listings preserve visible spelling and mark hidden text. A separately tested example checks the mechanism, not which saved file produced an earlier classroom result.

## Two lines of output, two different questions (0:00–3:08)

The instructor opens **`Roadmap.md`** to find where the class left off. They had built a Kivy window up to a button that did something and a text label, then looked at binary and bitwise operators and truth tables. The last marked entry is **`byteOrder.py`**. Next on the roadmap is **`base64, utf-8`**. Other filenames below it are plans, not a list of things already demonstrated.

The instructor asks what *big endian* and *little endian* actually mean. The first answer is to run a small program. Here is the command at the PowerShell prompt in the Numerical project, with its two observed output lines (1:59–2:00):

```text
py .\src\byteOrder.py
System byte order is little endian.
The PyObject header size on this system is 16
```

Those lines answer different questions. The first reports a convention: which end of a multi-byte number occupies the first byte in memory? The second reports a size calculated by the script. A size of 16 doesn't tell you the order, and the word *little* doesn't tell you the size. The Kivy/OpenGL messages higher up in that terminal belong to earlier activity; the byte-order script did not open a graphics window.

**Annotated teaching version — guide-added comments** (first two lines of the file, visible at 3:00):

```python
import sys                                          # Make the interpreter's system information available.
print(f"System byte order is {sys.byteorder} endian.")  # Insert the string 'little' here on the class machine.
```

The value comes from **`sys.byteorder`**, an attribute, not a function to call. Python evaluates the expression inside the braces, inserts its value into the formatted string, then prints the completed sentence. Do not copy the class machine's answer as a constant for another machine. [Supplemental reference: Python's definition of native byte order](https://docs.python.org/3.12/library/sys.html#sys.byteorder).

**Key items to scan**

- **`Roadmap.md`** — the first four marked entries are **`kivySteps`**, **`binOp.py`**, **`truthTableWIP.py`**, and **`byteOrder.py`**.
- **`base64, utf-8`** — the next roadmap entry, not the output of the byte-order program.
- **`py .\src\byteOrder.py`** — runs the Python script from the Numerical project; the script prints two lines.
- **`sys.byteorder`** — reports **`'little'`** or **`'big'`** for the platform; it does not read an order marker from a particular object or input file.
- **`f"...{sys.byteorder}..."`** — fills the braces with the attribute's value; the surrounding words and final period are literal text.

## When the same bytes mean two different numbers (3:15–6:21)

Start with bits. In ordinary written binary, the rightmost place counts ones, the next counts twos, and the next counts fours: their weights are 2⁰ = 1, 2¹ = 2 and 2² = 4. Moving a **1** to a different place changes what it contributes. The **least significant** position has the smallest weight; the **most significant** has the largest weight in the chosen width. For bytes of a multi-byte integer, the idea is the same, but crossing one byte moves eight binary places. *First in memory* need not mean *largest contribution*.

The instructor compares a sender with a receiver and a program storing a value with one retrieving it. Both ends need the same rule. Could you fix a mismatch? A student suggests moving each value to the other end. The instructor distinguishes two cases: if the original is still accessible, reread it in the correct order; if a stream has already passed, rearrange what you kept. That requires knowing the unit, field boundaries, and intended order. The instructor invites students to try it, rather than demonstrating a swap program in class.

**Transfer example — guide-added comments; independently verified, not a classroom run.** Treat exactly two bytes as one unsigned integer. The first byte is hexadecimal **`01`**, the second **`02`**. The high byte counts groups of 256; the low byte counts units.

```python
data = bytes.fromhex("01 02")                 # Keep the two original bytes in their received order.
print(int.from_bytes(data, "big"))            # 01 counts 256s; 02 counts ones: print 258.
print(int.from_bytes(data, "little"))         # 01 counts ones; 02 counts 256s: print 513.
print(int.from_bytes(data[::-1], "little"))   # Make reversed bytes 02 01; read as little: print 258.
```

| Bytes passed to reader | Order requested | Calculation | Verified integer printed |
|---|---|---|---:|
| 01 02 | big | 1 × 256 + 2 | 258 |
| 01 02 | little | 1 + 2 × 256 | 513 |
| 02 01 | little | 2 + 1 × 256 | 258 |

The second `print` changes only the reader's interpretation; it doesn't change the bytes. The third `print` uses a reversed copy and changes the interpretation relative to the first line. It produces the same number as the first line without reversing the bits *inside* either byte. If the format already says “big endian,” specifying that order directly is simpler than reversing anything. If the order is unknown, the bytes alone do not choose between 258 and 513.

**Key items to scan**

- **Least significant / most significant** — smallest/largest place weight; not synonyms for first/last address.
- **`01`**, **`02`** — byte values in the order received. **`01 02`** is one two-byte field in this example.
- **Big endian** — most significant byte first; **`int.from_bytes(data, "big")`** prints 258 when passed to **`print()`**.
- **Little endian** — least significant byte first; the unchanged bytes yield 513 here.
- **`data[::-1]`** — creates the reversed byte sequence for this field; the original **`data`** is unchanged. Don't reverse an entire multi-field message indiscriminately.

## What the script actually measures (6:21–10:20)

The class moves from a Python attribute to C-sized quantities. Why use another language's types? The instructor notes that different parts of a project can be written in different languages and used together at run time. Here, Python's **`ctypes`** library exposes C-compatible types and their storage sizes; the class hasn't written or compiled a new C program.

The instructor prompts the class: *Everything in Python is an object. What does every object have?* Students supply a type and an address. More precisely, **`type()`** gives the object's type, and **`id()`** gives an integer identifying that object for its lifetime. In CPython that identity is its memory address; that's not a promise other Python implementations must keep. Another student says the type tells how much memory something takes and how it is arranged. That's a useful starting point for interpreting a fixed C layout; it doesn't mean knowing that two objects are strings tells you their text, length, or total memory use. [Supplemental reference: Python on object identity](https://docs.python.org/3.12/library/functions.html#id).

**Annotated teaching version — guide-added comments** (second part of the classroom script, expression wrapped for reading):

```python
import ctypes                                       # Use C-compatible types without reading any raw address.
pyObjectHeaderSize = (                              # Save the sum of two sizes, not the contents of an object.
    ctypes.sizeof(ctypes.c_ssize_t)                 # Count bytes for a signed size-sized integer: 8 in our check.
    + ctypes.sizeof(ctypes.c_void_p)                # Add the width of a generic pointer: another 8 here.
)
print(f"The PyObject header size on this system is {pyObjectHeaderSize}")  # Display the sum, 16.
```

**`ctypes.sizeof`** returns the size of the specified C-compatible type or buffer in **bytes**. The precise visible spelling is **`c_ssize_t`** (signed), not **`c_size_t`** (unsigned). **`c_void_p`** represents the C generic object-pointer type. The script measures how much space a pointer occupies, *not* how big the thing it could point to is. It never passes a particular Python object into either size call. The class terminal shows the sum, not the individual operands. A separate local run returned 8 and 8, then printed the same two lines as the class run. [Supplemental reference: ctypes types and sizes](https://docs.python.org/3.12/library/ctypes.html#ctypes.sizeof).

**Key items to scan**

- **`type()`** — identifies what kind of object a value is; it does not measure a particular object's total storage.
- **`id()`** — identifies a live object; interpreting the result as its address is specific to CPython.
- **`ctypes`** — provides the C-compatible types used for these two width measurements.
- **`ctypes.sizeof`** — gives a byte count; neither call reads memory at a supplied address.
- **`c_ssize_t`** versus **`c_size_t`** — signed versus unsigned size-sized integer; the script uses the former.
- **`c_void_p`** — the generic pointer type; its own width, not a pointed-to object's size, contributes to the sum.
- **`pyObjectHeaderSize`** — script variable holding the calculated total, 16 on the class machine.

### Is a pointer to an unspecified type the same as a pointer to nothing?

The class compares an address, pointer, reference and link: each helps express *where another thing is*, though the words do not have identical language-level rules. The phrase “pointer to nothing” can hide two distinctions. A C **`void *`** doesn't specify the pointed-to object's type. It can point to a real object. A **null pointer**, on the other hand, points to no object. The class mentions zero and whether C itself defines null. C does standardize null pointer constants; for example, the integer constant **`0`** is a null pointer constant, and the standard **`NULL`** macro expands to an implementation-defined null pointer constant. That is a rule about values and conversions, not a requirement to infer a pointer's stored bit pattern from the spelling zero. [Supplemental reference: WG14 C11 committee draft N1570, §§6.3.2.3 and 7.19](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf).

Python's **`None`** is an actual object representing absence; **`NoneType`** is its type, not a second spelling for that value. Here's a safe way to check the distinctions without following any address.

**Transfer example — guide-added comments; independently verified:**

```python
import ctypes                              # Supply the Python wrapper for the C pointer type.
thing = None                               # This name refers to the existing None object.
print(type(thing).__name__)                # Print the type's name: NoneType.
print(id(thing) == id(None))               # Both names/expressions refer to the same object: True.
print(ctypes.c_void_p().value is None)     # A new null pointer wrapper exposes its value as None: True.
```

**Key items to scan**

- **`void *`** — generic pointer to an object of unspecified type; not necessarily null.
- **Null pointer**, **`0`**, **`NULL`** — distinguish the null pointer value, a language-defined way to express a null pointer constant, and a standard library macro.
- **`None`** and **`NoneType`** — a Python value representing absence and the name of that value's type, respectively.
- **`ctypes.c_void_p().value`** — reads the wrapper's value; for the null pointer constructed here it is **`None`**. No address is dereferenced.

## Bytes in memory, bits on a link (10:21–11:58)

A student asks whether little endian takes the least significant part first. Yes—but first *what*? For a multi-byte number in memory, it's the least significant **byte** at the lowest address. A serial link sends a sequence of **bits**; its rules for bit order within each unit are a separate question. The student's “natural order” is a mnemonic, not a universal rule. Check the data format rather than assuming it matches your own machine.

The instructor contrasts one-bit-at-a-time communication with the usual byte-addressed memory model. Adjacent addresses identify individual bytes; a larger operation can access several at once. A 64-bit quantity spans eight eight-bit bytes. That doesn't make the bytes individually unaddressable, guarantee a particular cycle count, or give each bit its own address. To test a single bit in a byte already read, use a bitwise operation, as in this independent connection to the earlier operators review.

**Transfer example — guide-added comments; independently verified:**

```python
b = 0b00000101           # The fours and ones bits are set: the byte's numeric value is 5.
print(b & 0b00000100)    # Keep only the fours bit: print 4, not 1.
print(b & 0b00000010)    # Keep only the twos bit: print 0 because that bit is clear.
```

**Key items to scan**

- **Byte order** — which byte of a multi-byte number comes first; **bit order** — which bit of a unit is sent or numbered first under a particular convention.
- **Byte-addressable** — an individual byte has an address even when a larger read fetches several bytes.
- **`&`** — bitwise AND; a one in the mask preserves the corresponding input bit. A nonzero result such as 4 means the selected bit was set.

## Does every object tell you its byte order? (12:00–13:20)

At 12:19 the instructor asks what sits at the address of a Python object. Think of a **header** as a common beginning, before the object's type-specific information. A student asks whether that header announces the byte order. No: in this example the native order is obtained from the interpreter, not from a tag in each header. The instructor also relates the header to the type and the memory that follows it. One refinement matters: in the normal CPython 3.12 release layout the common **`PyObject`** contains a reference count and a pointer to the type object, not a universal byte count of everything that follows. **`PyVarObject`** adds a size field for types that use that structure; this isn't a universal allocation length in bytes either. The class calculation models a layout; it does not inspect an object's header or establish a 16-byte rule for all Python builds. [Supplemental reference: CPython 3.12 common object structures](https://docs.python.org/3.12/c-api/structures.html#c.PyObject).

**Key items to scan**

- **Header / `PyObject`** — the common start of the classroom CPython model contains reference-count and type information; it does not carry a per-object byte-order flag.
- **`PyVarObject`** — extends the base structure with size information for types using that layout.

## Opening the converter: what comes from Kivy? (13:25–14:59)

The instructor now wants an application that uses these representations. From PowerShell the class launches **`py .\src\base64App.py`**. At 13:46 the **Base64 Encoder / Decoder** window has an empty input area, two buttons side by side, an empty output area, and a clipboard button. Gray hints indicate where text and results belong; they are not a sample input or an encoded result. The two action buttons suggest a useful question even before typing: *Which direction am I converting?*

| What is visible in the first open window | What it lets the learner distinguish |
|---|---|
| Input Text / Base64 String: | One input area accepts either kind of text; the next action determines how it is treated. |
| Encode to Base64 / Decode from Base64 | Separate directions, not two names for the same operation. No click or conversion is established by this initial view. |
| Output Result: / Copy Output to Clipboard | The result belongs in the lower area; copying a result is a separate action from computing it. |

Asked whether he wrote it in Kivy, the instructor says yes. He contrasts Kivy with the earlier GUI toolkit (the caption renders the name “flat”). High-level GUI work feels familiar: a **widget** is a visible control or display element, such as a button or label; a **container hierarchy** groups these into a layout, for example a button row within the window. The visible arrangement illustrates the idea but doesn't reveal the app's exact widget classes. A difference becomes important when drawing individual lines, pixels, or other shapes: the instructor describes Kivy as taking an **OpenGL** approach. The terminal agrees about this run's graphics system; it does not show the Python implementation of the converter. The earlier progression from blank window to working controls is what makes the interface recognizable even before the encoding has been explained.

**Key items to scan**

- **`py .\src\base64App.py`** — launches the separate GUI demonstration; not a second option to the byte-order script.
- **Base64 Encoder / Decoder** — title of the initially empty converter; **Base64 Converter** is the heading inside it.
- **Encode to Base64 / Decode from Base64** — choose the direction before interpreting a result.
- **Copy Output to Clipboard** — acts on output after a conversion, not on the original text.
- **Widget / container hierarchy** — buttons and labels are individual elements; containers organize elements together.
- **OpenGL** — the graphics system named by the instructor and the Kivy log, distinct from the code that performs an encoding.

## Who decides how two computers read the same message? (15:00–21:25)

Before entering anything in the open converter, the instructor asks who owns or governs the Internet. A student suggests several governments. The useful distinction is between owning **a network** and owning **the Internet**: many parties operate networks, and governments can regulate activities within their jurisdictions, but no single party owns or directs the whole interconnected system. So how can two programs written by strangers agree on what a string means? Compare three different kinds of coordination in the class's browser tour.

| Class example | Who does what? |
|---|---|
| The Internet Society homepage stresses access, trust and safe use. | The **Internet Society (ISOC)** advocates for an open, accessible Internet. Its mission is not authority over every network. [Internet Society: About](https://www.internetsociety.org/about-internet-society/) |
| You want a domain name. You check whether it is available, choose a registrar and pay to register it. | The **Internet Corporation for Assigned Names and Numbers (ICANN)** coordinates globally unique identifiers. The registrar deals with the registrant; the registry maintains registrations for its domain. You do not normally buy a domain directly from ICANN. [ICANN: What Does ICANN Do?](https://www.icann.org/resources/pages/what-2012-02-25-en) |
| Two independent programs need the same rule for translating bytes to text. | The **Internet Engineering Task Force (IETF)** develops and reviews technical specifications in an open process. A common published specification lets each programmer implement the rule without having to know the other programmer. [IETF: About RFCs](https://www.ietf.org/process/rfcs/) |

The instructor likens naming operations to the present-day focus of IT and protocol design to forward-looking computer science. Treat this as a way to remember the examples, not an organizational chart: ICANN and the IETF are not two departments commanded by ISOC. Open participation also does not mean an absence of governance or review. Individuals, including people working for companies and governments, can contribute technical proposals and discuss them in working groups.

The browser goes from **About RFCs** to the **RFC Editor**. An RFC is a *Request for Comments*, a numbered publication in a public technical series. The IETF page gives TLS, QUIC and WebRTC as examples of protocols described there. The name is historical: the document circulated for review while an IETF proposal is developing is an **Internet-Draft**. Anyone can author one; it need not be adopted or published. A published RFC's text is stable. A later RFC can update part of it or obsolete it; errata can be recorded separately. Not every RFC is an Internet Standard, and not every RFC originates in the IETF. [IETF: Internet-Drafts](https://www.ietf.org/participate/ids/) · [IETF: About RFCs](https://www.ietf.org/process/rfcs/)

Search the RFC Editor for **`base64`**, as the instructor does. The relevant result is S. Josefsson's [RFC 4648, *The Base16, Base32, and Base64 Data Encodings*](https://www.rfc-editor.org/info/rfc4648/). Its header says **`Obsoletes: 3548`**, *Standards Track*, October 2006; the search result labels it *Proposed Standard*. That date is the publication date, not a running “last edited” date. The abstract identifies choices that could make two implementations disagree: line feeds, padding, non-alphabet characters, the choice of alphabet, and canonical encoding (one prescribed representation). The introduction gives the practical reason to care: some storage and transfer environments accept only US-ASCII data, and even modern tools can benefit from a text representation of binary data.

**Key items to scan**

- **Internet Society (ISOC)** — open access and trust; not an owner of the Internet.
- **ICANN** — coordinates unique identifiers. A **registrar** registers an available name for a customer; a **registry** maintains the records for its domain.
- **IETF** — develops shared technical specifications; participation and implementation do not depend on a single network owner.
- **Internet-Draft** — working proposal open for discussion, not an assigned RFC number or a guarantee of adoption.
- **About RFCs** — the IETF's explanation of the series; **RFC Editor** — authoritative place to find the actual publication.
- **`base64`** — the class search that turns a general Internet question into a specific document.
- **`Obsoletes: 3548`** — use RFC 4648 in place of its predecessor; the older publication was not silently rewritten.

## Read the address before interpreting the encoding (21:26–24:06)

When Firefox puts the address bar in focus, the full address becomes visible: **`https://www.rfc-editor.org/info/rfc4648/`**. Read it from left to right. **`https`** is the scheme (here, it identifies the protocol to use); the colon terminates the scheme. **`//`** introduces the authority, which in this address is just the host **`www.rfc-editor.org`**. The next slash starts the path **`/info/rfc4648/`**. A slash-separated path can look like folders and a file without naming any physical file on a server. As the instructor notes, software can route that path to code that supplies the requested resource; REST (Representational State Transfer) is the architectural style mentioned here, not another kind of character encoding.

ASCII is a seven-bit character code. If a system accepts only ASCII, raw bytes from, say, an image cannot simply be treated as a string of printable letters. One real application of Base64 is the content-transfer encoding used by MIME (Multipurpose Internet Mail Extensions): a mail body can carry binary data in printable form. It is the representation required by the format that matters, not a claim that present-day Internet hardware can transport only seven bits. [RFC 2045 §6.8](https://www.rfc-editor.org/info/rfc2045/#section-6.8)

**Supplemental clarification.** Unusual characters in a browser address are not automatically Base64. For example, in a URL path the UTF-8 bytes for **`é`** can appear as **`%C3%A9`**: percent encoding writes each byte as a percent sign followed by two hexadecimal digits. Thus **`café`** can appear as **`caf%C3%A9`** (separately checked). An internationalized identifier can display Unicode to the reader. A particular application may choose Base64url for a data field, but percent encoding, Unicode text and Base64url are different things. [RFC 3986 §2.1, *Percent-Encoding*](https://www.rfc-editor.org/info/rfc3986/#section-2.1) · [RFC 3987, *Internationalized Resource Identifiers*](https://www.rfc-editor.org/info/rfc3987/)

**Key items to scan**

- **`https://www.rfc-editor.org/info/rfc4648/`** — the classroom URL. **`https`** names the scheme; **`//`** introduces the authority; **`www.rfc-editor.org`** is the host.
- **`/info/rfc4648/`** — path to the RFC information resource, not proof of a directory on disk. **REST** concerns resources and their representations.
- **ASCII** — limited character repertoire; **MIME** — mail format with defined ways to carry content such as binary attachments.
- **`é` → `%C3%A9`** — two UTF-8 bytes represented in a path. **`café` → `caf%C3%A9`** is percent encoding, not Base64.

## Fit any byte into a printable alphabet (24:06–30:50)

A student catches a mismatch: doesn't ASCII have 128 values, not 256? Yes. The 128 describe the **character set from which we choose output symbols**. The 256 describe **the possible values of one input byte**. The 64 describe **how many distinct data symbols the encoding uses**. These counts answer different questions.

| Bits | Number of patterns | What the number means here |
|---|---:|---|
| 8 | 2⁸ = 256 | One input octet (eight-bit byte) can contain any unsigned value from 0 to 255. “256 possibilities” does not mean 256 is a byte value. |
| 7 | 2⁷ = 128 | US-ASCII codes range from 0 to 127, including controls; not all are printable. |
| 6 | 2⁶ = 64 | Each group of six input bits supplies an index from 0 to 63 into the printable output alphabet. |

How could one of only 64 symbols stand for any of 256 bytes? It cannot, one for one. The instructor asks the class to factor 24: a student supplies both **`3 × 8`** and **`4 × 6`**. Take three bytes in input order, concatenate their bits (write one immediately after another), then move the boundaries. Reading each byte most significant bit first, cut the same 24 bits into four six-bit groups. Use each number as an index into the agreed alphabet. The result is four printable characters. Each represents six input bits; it is not a six-bit ASCII character, and the numbers involved are not “64-bit values.” A four-character output stored as ASCII bytes takes more space, not less.

[Section 4, Table 1 of RFC 4648](https://www.rfc-editor.org/info/rfc4648/#section-4) is the table actually on screen. Indices 0–25 are uppercase **`A–Z`**; 26–51 are lowercase **`a–z`**; 52–61 are **`0–9`**. At 62 and 63, standard Base64 uses **`+`** and **`/`**. [Section 5](https://www.rfc-editor.org/info/rfc4648/#section-5) defines a separate URL- and filename-safe alphabet, **base64url**, which substitutes **`-`** and **`_`** at just those two positions. **`=`** is a separate padding marker, not the symbol for index 0 (that is uppercase **`A`**). If the data never use indices 62 or 63, looking at the output may not reveal which alphabet was selected. Use the surrounding format's specification rather than guessing.

**Key items to scan**

- **Input byte** — any of 256 values may arrive, even though the *output* uses only a printable subset of ASCII.
- **`3 × 8` = `4 × 6`** — both describe the same 24 bits. Change the boundaries, not the order or the bits.
- **`A–Z`**, **`a–z`**, **`0–9`** — indices 0–61, in that order; case matters.
- **`+`**, **`/`** — indices 62 and 63 in standard Base64. **`-`**, **`_`** — those positions in **base64url**.
- **`A`** — six zero data bits. **`=`** — a marker for a missing data-symbol position at the end, not an additional value.

## Explain the converter's two results, byte for byte (30:50–35:59)

The Kivy window starts with an input box, two buttons (**Encode to Base64** and **Decode from Base64**), an **Output Result** box and **Copy Output to Clipboard**. In this demonstration the class uses the encode direction. At first the lower box contains only a placeholder. The upper box then contains **`abc!@#`**: lowercase letters even though the captions render the spoken letter names as “ABC.” After encoding, the lower box shows **`YWJjIUAj`** (32:00). The input is still visible; encoding displayed a second representation rather than replacing the message.

**Worked trace — guide reconstruction from the visible input; values independently checked.** Each character in this particular input has the same single-byte value in ASCII and UTF-8. Read each row from left to right; four six-bit groups from each row become four output characters.

| Three input bytes (hex) | Same bytes, eight bits apiece | Same bits, six bits apiece | Indices → output |
|---|---|---|---|
| `abc`: `61 62 63` | `01100001 01100010 01100011` | `011000 010110 001001 100011` | `24 22 9 35` → `YWJj` |
| `!@#`: `21 40 23` | `00100001 01000000 00100011` | `001000 010100 000000 100011` | `8 20 0 35` → `IUAj` |

A student spots a repeated lowercase character even though no input character repeats. Look at the last group in each row: both end in **`100011`**. Index 35 gives lowercase **`j`** both times. The input characters **`c`** and **`#`** differ, but the final six bits of their bytes agree. The punctuation was not discarded either: the entire second block **`IUAj`** encodes **`!@#`**. Reading the alphabet table in reverse and reassembling groups of eight recovers the bytes. There is no secret key involved; this is encoding, not encryption.

**Key items to scan**

- **Encode to Base64** — reads the source field; **Output Result** displays the conversion. **Decode from Base64** and **Copy Output to Clipboard** are visible controls, not demonstrated actions in these examples.
- **`abc!@#` → `YWJjIUAj`** — two complete three-byte blocks, eight output characters and no padding.
- **`100011` → `j`** — both **`c`** and **`#`** end in these six bits. This is why the output has a repeated letter.
- **`!@#` → `IUAj`** — punctuation is legal *input*; it does not have to belong to the *output* alphabet.

### Why adding one letter makes the result four characters longer

The instructor appends a lowercase **`t`**. At 34:27 the input already reads **`abc!@#t`** but the result still reads **`YWJjIUAj`**. At 34:34 the result reads **`YWJjIUAjdA==`**. The first eight symbols are unchanged: the first six bytes have not moved. The new byte starts its own final group. Here is how it fills that group:

| Final-group step | What happens to the bits |
|---|---|
| Start with `t` | Hex `74`, binary `01110100`: eight real input bits. |
| Read the first six bits | `011101` is index 29 → lowercase `d`. Two real bits, `00`, remain. |
| Finish the second index | Append four zero **bits** on the right of `00` to make `000000`: index 0 → uppercase `A`. No byte was added to the message. |
| Mark the two unused positions | Only two data symbols were needed. Add two **characters**, `==`, to finish the last four-character group: `dA==`. |

Deletion makes the interface behavior particularly clear: at 34:51 the shorter input is back, while the *longer* output is still displayed. By 34:52 the result is again **`YWJjIUAj`**. A changed input field and the last calculated result need not refer to the same run. The group-count rule for padded Base64 is simple: three remaining bytes need four data symbols and no padding; two need three data symbols and one pad; one needs two data symbols and two pads. Follow the enclosing format if it explicitly permits omitted padding. Count **bytes**, not visible letters, for arbitrary Unicode text. [RFC 4648 §§3.2, 4](https://www.rfc-editor.org/info/rfc4648/#section-3.2)

**Independent check — guide-created Python, not classroom source or console output.** This directly checks the two results visible in the app and shows how to reverse the second one.

```python
import base64                            # Use Python's standard Base64 implementation.
message = b"abc!@#"                      # The six original bytes, without a newline.
print(base64.b64encode(message))         # Display the bytes spelling YWJjIUAj.
longer = message + b"t"                  # Make seven bytes; message is still six bytes.
encoded = base64.b64encode(longer)       # Return the padded representation as bytes.
print(encoded)                           # Display the twelve encoded characters as bytes.
print(base64.b64decode(encoded))         # Recover and display the original seven bytes.
```

**Independently executed output** (the `b` prefix and quotes are Python's display notation, not part of the app's result):

```text
b'YWJjIUAj'
b'YWJjIUAjdA=='
b'abc!@#t'
```

The next difficulty is not mathematical. The instructor tries to make the letters easier to count. A student suggests resizing or using Ctrl-plus. Resizing changes how much space Kivy gives the panels, but the letters stay small. At 35:59 the converter is no longer visible, Kivy reports `Leaving application in progress...`, and PowerShell shows **`py .\src\base64App.py`** at the prompt with the caret at the end. This frame shows the previous application exiting, not a successful rerun of that command.

**Key items to scan**

- **`t` → `dA==`** — one byte yields two data symbols. Four zero *bits* finish the last index; two **`=`** *characters* mark unused positions.
- **`abc!@#t` → `YWJjIUAjdA==`** — the original two groups remain intact; only the final group is new.
- **`YWJjIUAj`** — also appears briefly alongside the *longer* input before re-encoding. Always check which edit the displayed result belongs to.
- **`base64.b64encode`** — returns encoded bytes. **`base64.b64decode`** — returns recovered bytes in the independent check; **`print`** is what displays them.
- **Kivy resizing** — moving the window boundary changes widget layout; it did not enlarge the characters in this demonstration.
- **`py .\src\base64App.py`** — command at the prompt after the app exits; a typed command is not evidence of a new run.

## Start at the bottom: why importing the app need not open a window (36:00–39:17)

The instructor had tried giving the converter more screen space. Kivy spaced the widgets out, but the letters were still small. To fix that, the class needed to find the code that *creates the text box*, not the code that resizes the window. They started at the last two lines of **`base64App.py`** and followed the path back to the box.

Annotated teaching version — guide-added comments (editor, 36:38–39:19):

```python
if __name__ == "__main__":  # True for the top-level file being run; false on ordinary import.
    Base64App().run()        # Create a new app instance, then start Kivy on that instance.
```

At 36:00 PowerShell shows **`py .\src\base64App.py`** at the prompt, below messages from an application loop that had already exited. That still is not itself proof the new command has executed. At 36:12 the instructor reports the app is running, and the window is visible in subsequent frames. The guard controls the two operations on its indented line, not the entire module: Python still processes imports and defines classes when another program imports this file. A student spotted the benefit: the other program can use those types without this line launching the GUI.

That also answers a practical question about the current doubly linked-list challenge. The instructor’s driver imports the submitted **`Item`** class. A top-level debugging print can add unexpected standard output before the driver prints anything, causing the challenge to report an error even if the class methods work. Keep tests in a separate driver (the instructor suggested a fruit example), or put them under the same guard in the submitted module. This is a reason to separate demonstrator output from the work a reusable class does, not a ban on testing. When asked about the required upload filename, the instructor *thought* the system renamed the file to **`submission`** and offered to double-check. That was not confirmation of a naming rule.

**Key items to scan**

- **`base64App.py`** — defines reusable classes as well as a guarded launch action.
- **`py .\src\base64App.py`** — visible PowerShell invocation; the earlier loop-exit lines are previous output.
- **`__name__ == "__main__"`** — distinguishes directly running this module from importing it.
- **`Base64App().run()`** — constructs the app first, then calls the inherited runner.
- **`Item`** — the grader’s driver should be able to import and exercise this class without unrelated prints.
- **`submission`** — instructor’s tentative account of the grader’s internal naming, not a verified filename requirement.

## Follow the build from the title bar to the editable text (39:17–45:45)

There are two titles to find on screen. “Base64 Encoder / Decoder” is the operating-system window title; “Base64 Converter” is a bold label *inside* that window. The code responsible for each is different. **`Base64App`** inherits from Kivy’s **`App`**. The inherited runner looks for **`build`** by that name; the local name **`rootLayout`**, in contrast, was the instructor’s choice. The visible imports are from Kivy: the application, layout and widget classes, and the clipboard. There is no Base64 library import doing the actual conversion.

Annotated teaching version — guide-added comments; excerpts of the start and end of the build, with a shorter layout of the first two labels (40:00–44:01):

```python
class Base64App(App):                         # This window uses Kivy’s app lifecycle.
    def build(self):                          # Called at startup; supplies the root widget.
        self.title = "Base64 Encoder / Decoder"  # Window chrome, not a label inside the form.
        rootLayout = BoxLayout(orientation="vertical", padding=15, spacing=10)  # Top to bottom.
        rootLayout.add_widget(Label(          # First child: the bold heading inside the form.
            text="Base64 Converter", font_size="20sp",
            size_hint_y=None, height=40, bold=True))  # Explicit height instead of a vertical share.
        rootLayout.add_widget(Label(          # Next child: identifies what may go in the field.
            text="Input Text / Base64 String:", size_hint_y=None,
            height=25, halign="left", valign="middle"))  # Alignment settings on this label.
        # The remaining children are built here; see the next examples.
        return rootLayout                     # Hand Kivy the container, not the text in a field.
```

The outer **`BoxLayout`** is a container. It gives the whole group an inset (**`padding`**) and separates successive children (**`spacing`**); it does not need a visible border. In this vertical layout the sequence of **`add_widget`** calls predicts the arrangement: heading, input caption, editable field, row of buttons, output header, result area, copy button. Start with a visible neighbor and follow the nesting and insertion order to find the code for a control.

**Key items to scan**

- **`App`**, **`Base64App`**, **`build`** — framework base class, concrete app subclass, and named startup hook respectively.
- **`self.title`** — writes the title bar; **`Label`** creates the distinct on-form heading or caption.
- **`rootLayout`**, **`BoxLayout`** — chosen variable name and actual container class; the root stacks children vertically.
- **`padding`**, **`spacing`** — space inside the outer edge versus space between children.
- **`add_widget`** — attaches an already constructed child; creating it alone does not place it in the form.
- **`return rootLayout`** — makes the assembled tree available to the runner.

### One constructor, one font-size change

The upper dark rectangle is a **`TextInput`** instance, not the caption above it. The app keeps it as **`self.inputText`** so the button handlers can later read what was typed. A student found the closing parenthesis on editor line 119: everything between it and the opening parenthesis on 112 configures *one* widget. Splitting keyword arguments onto separate lines was the instructor’s readability preference, not a different calling convention.

Annotated teaching version — guide-added comments, original input state (44:01):

```python
self.inputText = TextInput(      # Create the editable field; save its reference on this app.
    multiline=True,             # Allow more than one line of input.
    hint_text="Enter text to encode or Base64 string to decode...",  # Prompt while empty.
    size_hint_y=0.25,           # Request a relative share of the available layout height.
    background_normal="",       # Clear the default normal background image for this widget.
    background_color=(0.1, 0.1, 0.1, 1),  # Dark gray, opaque: low equal red/green/blue.
    foreground_color=(0.9, 0.9, 0.9, 1)   # Near-white, opaque: high equal red/green/blue.
)
rootLayout.add_widget(self.inputText)  # Attach that same field below the two labels.
```

The fourth number in the color settings is alpha (opacity), on the same zero-to-one scale. The “normal” in **`background_normal`** is a widget state, not a geometric normal; the instructor deferred a fuller explanation. The faint hint shown over the dark area is not the user’s input; it disappears when the user types. With no explicit **`font_size`** here, the field uses its default, even though the heading specifies a size. Asked how to find the correct spelling, a student proposed checking whether there is an underscore. The instructor recommended checking the Kivy application programming interface (API) for the class and its properties; the heading also supplied a local example. No documentation page visit is needed to explain the edit.

Annotated teaching version — guide-added comment, **inserted keyword argument** between the size hint and background in the existing constructor, not a stand-alone script (45:37):

```python
    font_size="24sp",           # Set the input field's font; the separate result field is unchanged.
```

Observed GUI (45:44): after the instructor’s save-and-rerun sequence, the empty input shows the larger “Enter text to encode or Base64 string to decode...” while “Result will appear here...” below is still small. The edit was **`24sp`**, not the alternative 28 mentioned aloud. This result was visible in the form, not printed by the program.

**Key items to scan**

- **`TextInput`**, **`self.inputText`** — widget class and the app’s reference to this particular editable instance.
- **`multiline=True`**, **`hint_text`** — permits multiple lines; supplies an instruction only while empty.
- **`size_hint_y=0.25`** — requests relative vertical space, not a larger font.
- **`background_normal`** — normal-state background image property, set to an empty string here.
- **`background_color`**, **`foreground_color`** — dark gray background and nearly white entered text; fourth component is opacity.
- **`font_size`**, **`24sp`** — the exact property and the input’s new size. The separate result field did not inherit this edit.

## Give two buttons one row and two different jobs (45:45–51:59)

To find the output, the class first passed two buttons. Why introduce another layout between input and output? The outer box stacks children vertically. To make the buttons neighbors from left to right, **`encodeLayout`** is itself a horizontal box. The layout organizes the visible buttons; there is no separate visible “encode layout” control. A button has to be constructed, wired to an action, and placed. None of those three operations substitutes for the other two.

Annotated teaching version — guide-added comments; equivalent compact formatting of the button construction (46:00–50:15):

```python
encodeLayout = BoxLayout(orientation="horizontal", spacing=10,   # One row rather than two stacked buttons.
                         size_hint_y=None, height=45)             # Give that row an explicit height.
encodeButton = Button(text="Encode to Base64",                    # A new button object, not a new class.
                      background_color=(0.2, 0.6, 1.0, 1.0))     # Blue-toned encode control.
encodeButton.bind(on_press=self.encode)                           # Register the method; do not convert yet.
decodeButton = Button(text="Decode from Base64",                  # A second object of the same button class.
                      background_color=(0.2, 0.8, 0.4, 1.0))     # Green-toned decode control.
decodeButton.bind(on_press=self.decode)                           # Give this object's press a different action.
encodeLayout.add_widget(encodeButton)                             # Left child in this horizontal row.
encodeLayout.add_widget(decodeButton)                             # Right child in the same row.
rootLayout.add_widget(encodeLayout)                               # Place the entire row below the input.
```

In **`encodeButton.bind(on_press=self.encode)`**, the object before the dot is the button, not the application class. **`on_press`** names its event; **`self.encode`** gives Kivy the method belonging to *this app* to call later. The visible form shows “Encode to Base64” on the left and “Decode from Base64” on the right. Merely constructing the form has not put an encoded string in the result field.

A student compared this binding with another UI toolkit and described the difficulty of finding the correct parameter in its API documentation. The instructor discussed documentation quality, the other toolkit’s newer declarative approach, and Kivy's relative maturity as personal impressions, not a benchmark. The captions render the other toolkit as “Flutter”; the exact product identification is uncertain. The classroom takeaway was to find both the documented event and the handler it expects rather than assume every toolkit spells them alike.

Another student asked for the source because constructing objects was starting to make sense and they wanted to experiment. The instructor said this example was **not then on GitHub**, offered to post it in a demo repository, and invited a Discord reminder. No URL or completed publication was demonstrated. The recently assembled demos were not presented as a model of project organization. The instructor was more hesitant about sharing the linked-list solution while its challenge was active: typing and trying code was the learning exercise, not merely recovering text from the video.

**Key items to scan**

- **`encodeLayout`** — one horizontal child of the vertical root; its own children are the two conversion buttons.
- **`Button`**, **`encodeButton`**, **`decodeButton`** — one class, two objects with different text, colors and actions.
- **`encodeButton.bind(on_press=self.encode)`** — register the encode action on that button without calling it during construction.
- **`on_press`**, **`self.encode`**, **`self.decode`** — framework event name versus the app’s chosen methods that respond to it.
- **GitHub / demo repository** — sharing was offered, not shown completed; the linked-list source was a separate decision.

### From the button row to the output and clipboard (screen evidence)

Below that row, the program builds a second horizontal container for “Output Result:” and a status message. **`self.statusLabel`** starts empty; it is not the result field. Despite its name, **`self.outputLabel`** is a read-only multiline text input. A person cannot type into it, but the app can still change its text when a conversion finishes. The enclosing **`ScrollView`** holds the result field; the gold-colored copy button sits below it.

Annotated teaching version — guide-added comments; shortened from the pre-font-edit screen state (39:32–39:38). The output-header container is assumed already created: its full constructor extends beyond the editor edge.

```python
outputHeaderLayout.add_widget(Label(text="Output Result:", halign="left", valign="middle"))  # Static caption.
self.statusLabel = Label(text="", color=(0.3, 1.0, 0.3, 1.0),    # Initially no green feedback.
                         halign="right", valign="middle")        # Put status in the other header position.
outputHeaderLayout.add_widget(self.statusLabel)                   # Keep feedback separate from the result.
rootLayout.add_widget(outputHeaderLayout)                         # Place that header beneath the buttons.
outputScroll = ScrollView(size_hint_y=0.3)                        # Container for the result field.
self.outputLabel = TextInput(readonly=True, multiline=True,       # User cannot edit; handlers can assign text.
    hint_text="Result will appear here...", background_normal="", # Placeholder, not an initial result.
    background_color=(0.1, 0.1, 0.1, 1), foreground_color=(0.9, 0.9, 0.9, 1))  # Match the input colors.
outputScroll.add_widget(self.outputLabel)                         # Put the field inside the scroll view.
rootLayout.add_widget(outputScroll)                               # Place the scroll view on the form.
copyButton = Button(text="Copy Output to Clipboard", size_hint_y=None, height=45,
                    background_color=(0.9, 0.6, 0.1, 1.0))        # New, separate action below the result.
copyButton.bind(on_press=self.copyOutput)                         # Register what to do when it is pressed.
rootLayout.add_widget(copyButton)                                 # Add the final visible control.
```

The conversion itself happens in two layers. The app method reads a widget and displays a result; the similarly named class method in **`Base64`** takes a string and *returns* a string. Keeping those jobs apart lets us test the conversion without creating a GUI. The following methods are screen evidence, not a claim that the instructor narrated all their branches.

Annotated teaching version — guide-added comments; handler excerpts (39:31):

```python
def encode(self, instance):                      # Kivy supplies the widget on a press.
    source = self.inputText.text                 # Read the editable box, not its hint.
    try:                                         # A conversion problem should appear in the GUI.
        encoded = Base64.encode(source)          # Ask the helper for a string; no printing.
        self.outputLabel.text = encoded          # Replace the result field's previous contents.
        self.statusLabel.text = ""               # Clear any earlier clipboard feedback.
    except Exception as e:                       # If conversion/display raises an exception here...
        self.outputLabel.text = f"Encoding Error: {str(e)}"  # Show its reason in the field.
        self.statusLabel.text = ""               # Do not leave a stale success status.

def decode(self, instance):                      # Other button, same input and result objects.
    source = self.inputText.text                 # Read what the user supplied for decoding.
    try:                                         # Invalid alphabet or unusable UTF-8 can raise.
        decoded = Base64.decode(source)          # Turn encoded text back into ordinary text.
        self.outputLabel.text = decoded          # Display the returned text, not the encoded input.
        self.statusLabel.text = ""               # This action supersedes old copy feedback.
    except Exception as e:                       # On failure there is no decoded text to display.
        self.outputLabel.text = f"Decoding Error: {str(e)}"  # Replace the result with the reason.
        self.statusLabel.text = ""               # Clear feedback on the failure path too.
```

The copy handler reads the result’s text, **not** the input. The observed guard requires nonempty data and rejects an **`Encoding Error`** prefix; it begins a second prefix check whose full literal is clipped. On the accepted path, **`Clipboard.copy(data)`** writes the output and the status becomes “Copied to clipboard!”; otherwise the status becomes “Nothing valid to copy!” without a copy call. That message is separate from the result. Here is the supported branch behavior without pretending to recover the missing suffix:

```python
data = self.outputLabel.text                          # Read the current output (may be empty).
# Inside the accepted branch of the visible guard:
Clipboard.copy(data)                                  # Send the output, not the hint, to the clipboard.
self.statusLabel.text = "Copied to clipboard!"         # Change the status, not the result itself.
# In the alternative branch instead:
# self.statusLabel.text = "Nothing valid to copy!"     # No clipboard write on that path.
```

Independent handler fixture (stand-ins for the fields, not a Kivy run): encoding “A” left the input as “A”, set the output to “QQ==” and cleared the old status. Decoding “?” set the output to `Decoding Error: Invalid Base64 character encountered: '?'` and cleared the status. Those are tested field values, **not** historical GUI output. For the clipboard branches the described messages are expected from the visible statements, not evidence of a clipboard test.

**Key items to scan**

- **`self.statusLabel`** — initially empty green feedback; a new conversion clears it on success or failure.
- **`self.outputLabel`**, **`ScrollView`** — a program-writable, user-read-only text field and its parent container.
- **`Base64`** — conversion helper; it returns text to a GUI handler rather than printing or drawing it.
- **`self.inputText.text`**, **`self.outputLabel.text`** — source read from one widget; result or exception message assigned to the other.
- **`Encoding Error`** — visible excluded output prefix. The second checked prefix extends off-screen.
- **`Clipboard.copy(data)`** — writes the accepted result; the feedback message goes into the separate status label.

### Inside the converter: three input bytes, four alphabet positions (screen evidence)

The imports promised no library shortcut. The upper half of the file shows how the promise is kept: **`Base64.encode`** first turns text into UTF-8 bytes, then processes up to three bytes at a time. It does not look up a whole input character in an alphabet of 64 symbols. It builds an integer, splits *that* into six-bit positions, and looks those positions up in the class’s alphabet ending in **`+/`**. These details were visible during scrolling at 36:05–36:18; the following small inputs are independent fixtures, not additional classroom runs.

Annotated teaching version — guide-added comments; encoder excerpts from lines 13–47. Indentation is shown relative to the current block. The full branches are numbered in the historical appendix.

```python
if not text:                         # An empty input has nothing to encode.
    return ""                        # Return an empty string, not padding.
octets = text.encode("utf-8")        # For "ABC", obtain bytes 65, 66, 67.
encoded = []                         # New collection for this call, not retained across presses.
i = 0                                # Begin at the first byte.
while i < len(octets):               # Stop when the next starting index is outside the input.
    chunk = octets[i:i+3]            # Take at most three bytes, including a short final group.
    i += 3                           # Next pass starts after that group.
    integer = 0                      # Reset the accumulator for this group, not the output list.
    for octet in chunk:              # Add bytes in their original order.
        integer = (integer << 8) + octet  # Make room for one byte, then insert its value.
    # The appropriate length branch appends alphabet symbols (see below).
```

| Independent input | Integer after packing | What the visible length branch does | Verified return |
|---|---:|---|---|
| `A` | 65 | Shift left four bits; two six-bit indices remain, then append two equals signs. | `QQ==` |
| `AB` | 16706 | Shift left two bits; emit three alphabet symbols, then one equals sign. | `QUI=` |
| `ABC` | 4276803 | Already 24 bits wide as a group: extract at shifts 18, 12, 6 and 0. Indices 16, 20, 9 and 3 select Q, U, J and D. | `QUJD` |

For example, the exact one-byte branch is short enough to follow by hand:

```python
if len(chunk) == 1:                                     # For "A", only byte 65 remains.
    integer <<= 4                                       # 65 becomes 1040: make two six-bit groups.
    encoded.append(cls.alphabet[(integer >> 6) & 0x3F])  # Top index 16 contributes Q.
    encoded.append(cls.alphabet[integer & 0x3F])         # Bottom index 16 contributes Q.
    encoded.append("==")                                # No second or third byte: add both pad markers.
# After the whole input loop, not inside the one-byte branch:
return "".join(encoded)                                 # Collect the pieces into "QQ==".
```

The reverse method **`Base64.decode`** trims surrounding whitespace and removes trailing equals signs before mapping symbols to numbers. It separately counts the equals signs but the recovered code never uses that count. Its real control is the number of nonpadding indices in the current group. Consequently this is a converter, not a strict validator of all Base64 padding arrangements.

Annotated teaching version — guide-added comments; decoder excerpts from lines 49–85:

```python
base64String = base64String.strip()           # Ignore surrounding whitespace, not arbitrary interior spaces.
if not base64String:                          # No remaining characters?
    return ""                                 # No bytes to reconstruct.
padding = base64String.count("=")             # Count is recorded; no later branch reads it.
cleanedString = base64String.rstrip("=")      # For "QQ==", keep "QQ" only.
values = []                                   # A fresh list of alphabet positions.
for char in cleanedString:                    # Translate each remaining symbol, in order.
    if char not in cls.alphabet:              # "?" has no entry in this alphabet.
        raise ValueError(f"Invalid Base64 character encountered: '{char}'")  # Signal a failure, not text.
    values.append(cls.alphabet.index(char))   # Both Qs contribute position 16.
```

```python
octetArray = bytearray()                      # New destination for decoded bytes.
i = 0                                         # Start at the first alphabet index.
while i < len(values):                        # Handle at most four indices per pass.
    chunk = values[i:i+4]                     # "QQ" contributes the partial group [16, 16].
    i += 4                                    # Advance to the next four-symbol group.
    integer = 0                               # Start this group's packed bits afresh.
    for value in chunk:                       # Append six bits per alphabet index.
        integer = (integer << 6) + value      # With [16, 16], first 16, then 1040.
    if len(chunk) == 2:                       # Two symbols carry one byte plus four padding bits.
        integer >>= 4                         # Discard those four bits: 1040 becomes 65.
        octetArray.append(integer & 0xFF)     # Keep one byte: 65.
    # For three indices, drop two bits and append two bytes; for four, append three.
return octetArray.decode("utf-8")             # Turn the reconstructed bytes back into text: "A".
```

An invalid symbol and invalid decoded text fail at different places. A question mark raises **`ValueError`** before any byte extraction. By contrast, `/w==` contains legal symbols but produces byte 255, which cannot by itself be decoded as UTF-8; the final conversion raises **`UnicodeDecodeError`**. Both distinctions were independently checked on the recovered helper; neither is a claim that the instructor entered those strings. Conversely `QQ` without padding still returned “A”, and the single symbol `Q` returned an empty string because none of the length branches handled a one-index group. Do not mistake these implementation behaviors for a general input specification.

**Key items to scan**

- **`Base64.encode`**, **`@classmethod`** — call the helper on the class; **`cls.alphabet`** supplies its lookup table ending in **`+/`**.
- **`text.encode("utf-8")`** — produces bytes first; three *bytes* need not mean three human-readable characters.
- **`integer << 8`**, **`0x3F`** — make room for the next byte, then use a six-bit mask for an alphabet index.
- **`"".join(encoded)`** — returns the completed string after all chunks, rather than printing individual symbols.
- **`Base64.decode`**, **`rstrip("=")`** — remove trailing padding and process the remaining indices in groups of up to four.
- **`integer << 6`**, **`0xFF`** — append a six-bit index during unpacking, then isolate each reconstructed eight-bit byte.
- **`ValueError`**, **`UnicodeDecodeError`** — invalid alphabet character versus bytes that cannot form UTF-8 text.

## Making the buttons respond: give Kivy a method, not its result

### Two buttons, one event name, different actions (0:52:19–0:56:46)

Putting a blue Encode button and a green Decode button side by side tells Kivy where to draw them. It does not tell Kivy which operation either button should perform. For that, the app calls **`bind`** on each *button*. The button supplies the event name, **`on_press`**; the programmer chooses what should respond to that event on that particular button. The instructor considered this association more important than the styling and said that, at the time, no Kivy code challenge was expected and the Kivy example would be posted. That was a plan, not evidence of publication.

**Annotated teaching version — guide-added comments.** These are the two registrations from the editor (lines 131 and 137), after the respective buttons have been created:

```python
encodeButton.bind(on_press=self.encode)  # Remember this app's encode method for this button.
decodeButton.bind(on_press=self.decode)  # Same event, different button and different handler.
```

The class asked whether spelling **`self.encode`** automatically runs the method. Python looks up the method on the current app and produces a *bound method*: it already knows which app is **`self`**. No conversion occurs. The outer parentheses call the button's registration method **now**; there are no parentheses after the handler. When Kivy dispatches a press **later**, it calls that handler with the button that sent the event. The instructor called the argument an “event object”; for this particular event, **`instance`** is the button instance, not the input text or a separate mouse-event record. The method's name need not be “encode”; it is the callback value, not a string Kivy searches for by that spelling.

| When | What happens | What does *not* happen |
|---|---|---|
| Building the interface | Register the bound app method on this button. | No input is read and no result is displayed merely by binding. |
| A user presses Encode | Kivy calls the registered method, effectively `self.encode(encodeButton)`. | The button does not itself implement Base64 conversion. |
| Inside the handler | The app supplies access to its widgets; the other argument identifies the pressed button. | The event argument is not the source string. |

**Key items to scan**

- **`bind`** — a method available on the widget through Kivy's event machinery; not a class and not a call on the app in these two lines.
- **`on_press`** — the button's press event. Preserve its spelling; inventing another name does not create a press event.
- **`self.encode`** — a callable bound to this app; Python looks it up now without executing its body. **`self.decode`** supplies the other button's action.
- **`self`** — the app whose data and widgets the callback accesses. **`instance`** — the button Kivy passes when it calls the registered handler.

### From a press to text in the result box (0:53:13–0:56:00)

Follow the registered method back to the two handlers in the earlier worked listing. `instance` identifies the pressed button, but neither handler needs it to find the message: both read `self.inputText.text`. The app, not the button, owns the references to the input, result and status widgets. This is why the same button event can produce different conversions.

Neither handler prints or explicitly returns its result; each updates an existing widget and normally returns **`None`**. A converter's return value, text displayed by the GUI, and terminal output are three different things. The input field is read before the `try`, so this block is not a catch-all for every possible failure in the app. The later startup failures below occur *before a handler is called*: these `except` blocks cannot catch them. The Copy handler reads the existing result instead; the earlier screen record shows its two status branches.

**Independently verified transfer example, not classroom input or output.** A small fixture registered a bound method with a real Kivy 2.3.1 event dispatcher, used ordinary fields instead of GUI widgets and used Python's standard Base64 converter instead of the class project. With input `apple`, registration left the output empty and made no calls. Dispatching `on_press` supplied the same button to the handler, set the result field to `YXBwbGU=` and cleared the status to `''`. Those are field values, not text the classroom app printed.

**Key items to scan**

- **`self.outputLabel`** — despite its name, the read-only **`TextInput`** whose text the handlers replace. Its hint is not a result.
- **`self.inputText.text`** — the current source text read by either handler; pressing Decode does not automatically make the preceding output the new input.
- **`Base64.encode(source)`** and **`Base64.decode(source)`** — calls to the converter. Their results become the output widget's text.
- **`self.statusLabel.text = ""`** — clears a separate status display; does not erase the conversion result.
- **`except Exception as e`** — catches exceptions in the corresponding `try` and uses their message in the result field.
- **`self.copyOutput`** — callback attached to the Copy button, beginning by reading the result field.
- **`return rootLayout`** — the builder returns the interface. The two conversion callbacks instead change state and return **`None`** on normal completion.

### Why `self.encode()` is a different experiment (0:56:31–1:00:05)

A student connected this to a previous `Item` example: if a constructor parameter were given `self.add_one`, would making an instance automatically run it? No. Python lets a method travel as a value like other objects; this is what *first-class* means here. What the receiver does matters. It could store the value, call it, or ignore it. Kivy specifically registers it for later. The instructor's “address” analogy helps distinguish a reference from the action itself; the programmer passes a Python callable, not a numeric address. A *callback* is the callable you hand to someone else for them to invoke when needed. The instructor compared it to leaving a way to call you back and briefly recalled the earlier `Item` / `for` discussion; `Item` was not changed here.

**Annotated teaching version — guide-added comments.** Compare these alternatives, not two consecutive statements:

```python
encodeButton.bind(on_press=self.encode)    # Supply the method itself; the press supplies instance later.
```

```python
encodeButton.bind(on_press=self.encode())  # Attempt to call NOW, without the required instance.
```

The second line is visible at 0:58:17. In a call, **`on_press=`** names a keyword argument; it is not a standalone assignment. Before `bind` can receive a value, Python must evaluate the argument expression. In general an inner call passes its *return value* outward if it succeeds. But here **`self.encode()`** is missing the required button argument. Python raises **`TypeError`** before entering that handler or calling `bind`; it does not first run the conversion and hand Kivy `None`. No classroom execution of this edit is established. The separately run matching-signature fixture reported `TypeError: DemoApp.encode() missing 1 required positional argument: 'instance'`.

**Key items to scan**

- **`on_press=`** — names the event in a keyword argument to `bind`; the expression after `=` must produce a usable callback.
- **`self.encode()`** — the inner parentheses request immediate execution. Here that fails with **`TypeError`** because the pressed-button argument is absent.
- **First-class callable** — a function or bound method can be passed as a value. **Callback** describes its role when another object invokes it later; it is not a special kind of Python declaration.

### Two real startup failures: a missing name and a non-callable value (1:00:06–1:02:17)

To test the student's “what if it's just a value?” question, the instructor changed *only the proposed Encode callback* in two runs. A suggestion to “put it in quotes” fixed one problem and exposed another. Neither run failed because somebody pressed a button or because the Base64 input was invalid: the app could not finish building its interface.

**Annotated teaching version — guide-added comments, first run.** The observed PowerShell launch was `py .\src\base64App.py`; the screen identified Kivy 2.3.1 and Python 3.12.7. Its traceback ends at line 131 in `build`.

```python
encodeButton.bind(on_press=apple)   # Look up a Python name; none named apple is defined here.
```

**Observed terminal error, 1:00:50:**

```text
NameError: name 'apple' is not defined. Did you mean: 'tuple'?
```

The suggested word is diagnostic text, not a repair. Python cannot produce the argument value, so Kivy never gets a chance to check it.

**Annotated teaching version — guide-added comments, second run:**

```python
encodeButton.bind(on_press="apple")  # Supplies a real string, but Kivy needs something callable.
```

**Observed terminal error, 1:01:08:**

```text
AssertionError: 'apple' is not callable
```

This time Python successfully produces a string and enters `EventDispatcher.bind`. Line 444 in Kivy's `_event.pyx` rejects the value. Quoting a method name would have the same conceptual problem: a string containing its spelling is not the method. To repair the registration, pass the bound method itself as in the first example. The late editor still shows the quoted test value; a successful repaired classroom run is not shown.

**Key items to scan**

- **`apple`** — unquoted name; no object found, hence **`NameError`** before the binding call.
- **`"apple"`** — valid string value; Kivy receives it but raises **`AssertionError`** when it checks whether it is callable.
- **`py .\src\base64App.py`** — observed relaunch from the project directory. A traceback during `build` is a startup error, not an error message displayed in the GUI result widget.

### Where is `bind` actually implemented? (student question at 0:57:53 and 1:00:10)

**Supplemental source answer — not a website shown in class.** Open [Kivy 2.3.1's `EventDispatcher.bind`, `kivy/_event.pyx`, lines 440–453](https://github.com/kivy/kivy/blob/2.3.1/kivy/_event.pyx#L440-L453). This is the release named in the classroom terminal. The method iterates over the keyword names and values; line 444 checks `callable(value)`. For `on_press`, it looks up the button's existing event observers and registers a weak-method wrapper for the supplied callback. If an `on_` name is not in that registry, this version skips it; a typo can leave the button inactive rather than immediately crashing. “Use the framework's event name” is the practical rule.

The matching press is the other half: [Kivy's button behavior](https://github.com/kivy/kivy/blob/2.3.1/kivy/uix/behaviors/button.py#L133-L152) dispatches `'on_press'` for an accepted touch. [The dispatch implementation](https://github.com/kivy/kivy/blob/2.3.1/kivy/_event.pyx#L725-L731) notifies the registered observers with the dispatching object. A [widget inherits the dispatcher machinery](https://github.com/kivy/kivy/blob/2.3.1/kivy/uix/widget.py#L292-L295); the student's proposed search for the method directly inside the app class would miss it. Kivy's [event API example](https://kivy.org/doc/stable/api-kivy.event.html#kivy.event.EventDispatcher.bind) contrasts a one-object press callback with a property-change callback that receives the object *and* its new value. Check the signature for the particular event, not just the word “callback.” The class ended with a reminder to follow up on the source; it did not show a link being posted.

**Key items to scan**

- **`EventDispatcher.bind`** — registers; the implementation is in `_event.pyx`, not in the classroom app's conversion methods.
- **`callable(value)`** — the exact test explaining the quoted-apple traceback. A callable must also accept the arguments that dispatch will supply.
- **`dispatch('on_press')`** — signals a press on this button; the registered handler receives the dispatching button.

## Syntax to remember

| Pattern | What it lets you predict |
|---|---|
| `sys.byteorder` | Reads the native byte order; it does not convert any stored value. |
| `ctypes.sizeof(...)` | Returns a count in bytes for the C-compatible type or object passed in, not the size of every Python object. |
| `if __name__ == "__main__":` | A direct launch enters this suite. An ordinary import defines the module’s classes without running the guarded launch. |
| `self.inputText` | An attribute of this app instance. A handler can use the widget after the layout-building call has finished. |
| `encodeButton.bind(on_press=self.encode)` | Look up a method on the app and give it to the button for later. The event name belongs to Kivy; the handler name belongs to the program. |
| `self.encode()` | Evaluate a call *now*. Failing to supply a required argument can stop evaluation before `bind` is even entered. |

## Knowledge check

1. You are given two bytes from a file and told the original machine was little-endian. Is that alone enough to decide how to read a two-byte field? What fact is missing?
2. A classmate says, “There are 256 possible byte values, so a 64-symbol alphabet loses information.” Use the input `abc` to explain the mistake. Why does the punctuation in `abc!@#` not have to appear in its encoded output?
3. The converter already has two complete groups of three input bytes. Add one ASCII character. How many alphabet symbols describe the new byte, and what else appears in a padded result?
4. Which change would make the input characters easier to read: widening the app window, changing the title label, or setting the input widget’s font size? Explain why those changes are not equivalent.
5. A test driver imports your module to exercise a class. Where should you put the statement that opens your demo window? What other kind of side effect caused concern in the class?
6. Compare `on_press=apple`, `on_press="apple"`, and `on_press=self.encode`. Assume the name `apple` is undefined and the app defines `encode`. Which expression fails before registration, which supplies the wrong kind of object, and which can work when pressed?

### Answer key

1. You need the **file format’s rule**. A writer may deliberately serialize the field in big-endian order on a little-endian machine. Native order describes the host, not every file the host writes. Reading the same bytes with the opposite rule generally changes the integer; reversing bits inside each byte is a different operation.
2. Base64 does not insist on one output symbol per input byte. The three bytes for `abc` supply 24 bits. Reading them in groups of six gives four indexes, which select `YWJj`. The same rule processes the bits of `!@#`; those punctuation characters are data going **in**, not candidates that must appear in the output alphabet.
3. The new byte contributes eight bits: six select the first symbol, and the remaining two plus four zero fill bits select the second. Two `=` markers complete the four-character final group. The decoder must yield one data byte here, not three; fill bits and padding do not add characters to the original message.
4. Set the input widget’s `font_size`. A layout can allocate more room without changing the size of a glyph. The title and the input are different widget instances, so changing the title’s text size need not affect the input. Check the output widget independently.
5. Put the launch under the `__main__` guard. An import still executes top-level statements; it merely skips that guarded suite when the condition is false. Even a diagnostic `print` outside the guard can contaminate the standard output a grading driver expects.
6. Bare `apple` requires Python to resolve a name; if undefined, lookup fails before the registration call. Quoted `"apple"` resolves to a string, but Kivy cannot call that string. `self.encode` resolves to a bound method and can be registered. Event dispatch later supplies the button instance; parentheses at registration would instead attempt an immediate call.

## Library of definitions

| Term | Meaning in this lesson |
|---|---|
| Significance; byte order | Significance is the weight a digit has in a number. Byte order specifies where the least- and most-significant **bytes** go in a multibyte representation. A serial link’s bit order is a separate agreement. |
| Identity; type; header | Identity distinguishes a live object from all other live objects; type governs how the object behaves. A header is implementation bookkeeping, not the object’s entire payload. |
| Pointer; `void *`; null pointer | A pointer can designate an object. `void *` can carry an object pointer without naming its pointed-to type. A null pointer designates no object; an untyped pointer need not be null. |
| ASCII; octet | ASCII defines 128 code positions. An octet is eight bits and admits 256 patterns. Base64 can represent any octets, not just ASCII input. |
| Alphabet; sextet; padding | The alphabet maps a six-bit group (a sextet) to a printable symbol. In padded Base64, `=` marks unused character positions in the last output group. It is not a 65th data value. |
| RFC; URL | A Request for Comments is a published document in the RFC series; not every RFC is an Internet Standard. A URL locates a resource. Its path can select an application route rather than a directory on disk. |
| API; widget; layout | An application programming interface defines the names and behavior code can use. A widget is an interface object such as a button. A layout is a container that sizes or positions its children. |
| Inheritance; bound method | Inheritance lets a class use behavior defined by a base class. A bound method already carries the instance on which the underlying function will operate. |
| Event; callback; dispatch | A button press is an event. A callback is a callable handed to another object for later invocation. Dispatch is the step that delivers the event to registered listeners. |
| Entry point; standard output | The entry point is where the app starts when launched directly. Standard output is the stream used by ordinary `print`; text there is observable even without a GUI window. |

## Historical screen record

The following listings are evidence, not paste-ready programs. Editor gutter numbers identify original rows where legible; they are not statements. A clipped line ends at the readable prefix. Separate views of the same filename can be different revisions. Use the worked examples above for runnable or adapted code.

### Opening roadmap — a planning document, not executable Python

**`Roadmap.md`**, Sublime Text; source frame **`CSCS1240F26_30_00h01m33s.png`**. The instructor uses the first four marked entries to recall earlier work; line 5 points ahead to the converter. The rest of the visible page is an agenda and mathematical planning note, not evidence that the class ran those programs or covered each topic. The gutter numbers below are for locating text; they are not part of the file.

```text
 1  * kivySteps
 2  * binOp.py
 3  * truthTableWIP.py
 4  * byteOrder.py
 5  < base64, utf-8
 6  jStruct.py
 7  floatBytes.py
 8  doc/IEEE Standard 754-2019.pdf
 9  ieee754App.py
10  ulp.py
11  doc/Accuracy of Mathematical Functions.pdf
12  doc/How Many Decimals of Pi Do We Really Need.pdf
13  < squareRootGuesser.py
14  squareRoot.py
15  < zodiac.py piPolygon.py, 360 c/d .py,
16  piCalc.py
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
30  Consider the domain and codomain of f to be continuous (smooth) sets.
31  Consider A to be an ordered subset of the domain sampled at some regular interval "h".
```

### `byteOrder.py` — the six-line editor example and its two output lines

**Observed source**, **`C:\com\joesta\Numerical\src\byteOrder.py`**, at **`CSCS1240F26_30_00h03m00s.png`**; the unobstructed text in **`CSCS1240F26_30_00h13m03s.png`** confirms the expression in line 2. The script first asks the interpreter its native byte order, then adds the sizes of two C-compatible types. The screen shows a blank line 3 and an empty final editor line 7; there are no guide-added Python comments in this listing.

```text
1  import sys
2  print(f"System byte order is {sys.byteorder} endian.")
3
4  import ctypes
5  pyObjectHeaderSize = ctypes.sizeof(ctypes.c_ssize_t) + ctypes.sizeof(ctypes.c_void_p)
6  print(f"The PyObject header size on this system is {pyObjectHeaderSize}")
7
```

Lines 1–2 print the value returned by the attribute lookup inside a sentence. Lines 4–5 ask about widths of C types, not an object's contents. Line 6 prints their sum with no period after the number. Different caret positions and selections in later editor views are not revisions to the program.

Observed PowerShell command and output at **`CSCS1240F26_30_00h01m59s.png`** (also at 2:00):

```text
(Numerical) PS C:\com\joesta\Numerical> py .\src\byteOrder.py
System byte order is little endian.
The PyObject header size on this system is 16
```

**Later transition, not another copy of that output.** At **`2DAz1gVHTFM_t823.png`** (13:43), scrollback preserves the two output lines above and then shows these separate prompts:

```text
(Numerical) PS C:\com\joesta\Numerical> .\src\byteOrder.py
(Numerical) PS C:\com\joesta\Numerical> py .\src\base64App.py
```

No intervening printed result is visible for the first of those two commands. The Base64 command is followed by a black window at 13:44 and the rendered **Base64 Encoder / Decoder** interface at 13:46 (**`2DAz1gVHTFM_t824.png`**, **`2DAz1gVHTFM_t826.png`**). At 13:53 (**`2DAz1gVHTFM_t833.png`**) PowerShell shows Kivy's startup and exit messages and the same launch text at the input cursor; that prompt alone does not prove a second run. The script filename and interface are recoverable here, but its Python source is not visible in these opening frames.

### From the roadmap to the RFC and the converter (15:00–35:59)

**Roadmap, not program source.** At 15:00 Sublime Text shows `C:\com\joesta\Numerical\doc\Roadmap.md`; line 5 reads `< base64, utf-8`. The bullets and less-than sign are roadmap marks, not executable Python. No converter implementation is visible in this section.

**Browser record.** The sampled pages progress from `www.internetsociety.org` (16:00), to `www.icann.org/en` (17:00), to `www.ietf.org` (18:00), then the IETF's *About RFCs* (18:45–19:00). At 20:00 the RFC Editor search field contains `base64`; the first result is RFC 4648, marked `PROPOSED STANDARD`. At 20:23 the document shows `Request for Comments: 4648`, `Obsoletes: 3548`, `Category: Standards Track` and `October 2006`. At 22:00–23:00 the address bar shows `https://www.rfc-editor.org/info/rfc4648/`. Table 1 (24:09–28:00) explicitly ends with `62 +`, `63 /`, `(pad) =`; the separate URL/filename-safe alphabet is listed as section 5 in the contents. At 29:00–30:27 the selection shifts within the section 4 paragraph about combining the bits of three eight-bit groups.

**Observed application states.** The window title is `Base64 Encoder / Decoder`; its heading is `Base64 Converter`. The table transcribes the two fields as displayed, not as predicted by a library. Gray hints are placeholders, not conversion results. The 1080p detail targets corroborate letter case and two brief stale-output states.

| Capture target | Upper field | Lower field | Screen evidence |
|---|---|---|---|
| 31:00 | `Enter text to encode or Base64 string to decode...` | `Result will appear here...` | Separate `Encode to Base64`, `Decode from Base64`, and `Copy Output to Clipboard` buttons. |
| 31:20; 31:25–31:28 | `a`, then `abc!@#` | Placeholder | Typing has not generated a result. |
| 32:00–34:22 | `abc!@#` | `YWJjIUAj` | Six input bytes, eight displayed characters. At 32:19 the input is selected, not replaced. |
| 34:27 | `abc!@#t` | `YWJjIUAj` | New input, old result. The appended letter is lowercase. |
| 34:34–34:50 | `abc!@#t` | `YWJjIUAjdA==` | Padded result. At 34:46 the six-character prefix is selected; `t` remains visible beside it. |
| 34:51 | `abc!@#` | `YWJjIUAjdA==` | The input has reverted, but the previous result remains. |
| 34:52–35:09; 35:51 | `abc!@#` | `YWJjIUAj` | Short result restored. In the 35:51 frame the app is narrower. |

At 35:40 PowerShell is in front, showing Kivy v2.3.1 and Python v3.12.7 startup information; this is not an encoded result. By 35:59 the converter is no longer visible. The last log message reads `Leaving application in progress...`; the active `(Numerical)` PowerShell prompt shows `py .\src\base64App.py` with the caret at the end. No new execution result is attached to that prompt in this frame.

### `base64App.py` — conversion helper, Kivy form and handlers

Assembled visual reconstruction of the earlier, pre-font-edit buffer. The gutter numbers and vertical bars below are not Python to type. Overlapping numbered views recover the file from its first import to its launch statement, but two lines extend past the right edge. The sequence does not establish that every line was visible in one saved version at one instant. Requested frame times are seek targets, not measured presentation timestamps. No class-run output is inferred from this listing.

#### Lines 1–85: convert strings without any Kivy widgets

Lines 1–7 import the UI dependencies. Lines 10–47 use a class alphabet to turn UTF-8 bytes into Base64 symbols; lines 49–85 map those symbols back to bytes and interpret them as UTF-8. The recorded padding count has no later use in this version. Source: `Unified_v2_detail3` frames `detail3_02165.000s.png` (1–30), `detail3_02176.550s.png` (25–54), `detail3_02176.850s.png` (46–75), and `detail3_02177.120s.png` (66–95). In particular line 61 is complete in the 2176.850 s frame despite being covered in a later view.

```text
  1 | from kivy.app import App
  2 | from kivy.uix.boxlayout import BoxLayout
  3 | from kivy.uix.label import Label
  4 | from kivy.uix.textinput import TextInput
  5 | from kivy.uix.button import Button
  6 | from kivy.uix.scrollview import ScrollView
  7 | from kivy.core.clipboard import Clipboard
  8 |
  9 |
 10 | class Base64:
 11 |     alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
 12 |
 13 |     @classmethod
 14 |     def encode(cls, text: str) -> str:
 15 |         if not text:
 16 |             return ""
 17 |
 18 |         octets = text.encode("utf-8")
 19 |         encoded = []
 20 |
 21 |         i = 0
 22 |         while i < len(octets):
 23 |             chunk = octets[i:i+3]
 24 |             i += 3
 25 |
 26 |             integer = 0
 27 |             for octet in chunk:
 28 |                 integer = (integer << 8) + octet
 29 |
 30 |             if len(chunk) == 1:
 31 |                 integer <<= 4
 32 |                 encoded.append(cls.alphabet[(integer >> 6) & 0x3F])
 33 |                 encoded.append(cls.alphabet[integer & 0x3F])
 34 |                 encoded.append("==")
 35 |             elif len(chunk) == 2:
 36 |                 integer <<= 2
 37 |                 encoded.append(cls.alphabet[(integer >> 12) & 0x3F])
 38 |                 encoded.append(cls.alphabet[(integer >> 6) & 0x3F])
 39 |                 encoded.append(cls.alphabet[integer & 0x3F])
 40 |                 encoded.append("=")
 41 |             else:
 42 |                 encoded.append(cls.alphabet[(integer >> 18) & 0x3F])
 43 |                 encoded.append(cls.alphabet[(integer >> 12) & 0x3F])
 44 |                 encoded.append(cls.alphabet[(integer >> 6) & 0x3F])
 45 |                 encoded.append(cls.alphabet[integer & 0x3F])
 46 |
 47 |         return "".join(encoded)
```

```text
 48 |
 49 |     @classmethod
 50 |     def decode(cls, base64String: str) -> str:
 51 |         base64String = base64String.strip()
 52 |         if not base64String:
 53 |             return ""
 54 |
 55 |         padding = base64String.count("=")
 56 |         cleanedString = base64String.rstrip("=")
 57 |
 58 |         values = []
 59 |         for char in cleanedString:
 60 |             if char not in cls.alphabet:
 61 |                 raise ValueError(f"Invalid Base64 character encountered: '{char}'")
 62 |             values.append(cls.alphabet.index(char))
 63 |
 64 |         octetArray = bytearray()
 65 |         i = 0
 66 |         while i < len(values):
 67 |             chunk = values[i:i+4]
 68 |             i += 4
 69 |             integer = 0
 70 |             for value in chunk:
 71 |                 integer = (integer << 6) + value
 72 |
 73 |             if len(chunk) == 2:
 74 |                 integer >>= 4
 75 |                 octetArray.append(integer & 0xFF)
 76 |             elif len(chunk) == 3:
 77 |                 integer >>= 2
 78 |                 octetArray.append((integer >> 8) & 0xFF)
 79 |                 octetArray.append(integer & 0xFF)
 80 |             elif len(chunk) == 4:
 81 |                 octetArray.append((integer >> 16) & 0xFF)
 82 |                 octetArray.append((integer >> 8) & 0xFF)
 83 |                 octetArray.append(integer & 0xFF)
 84 |
 85 |         return octetArray.decode("utf-8")
```

#### Lines 86–141: form construction through the button row

The app starts building the vertical root at 88–92, adds the two headings at 93–111 and retains the editable text widget at 112–120. The horizontal row at 122–140 is built locally and attached to the root as a single child. Source: `detail3_02401.000s.png` (83–112, including the full root constructor on 92); `detail3_02641.000s.png` (97–126, including the full hint on 114); `detail3_02378.150s.png` (123–151). Their line numbers agree before the later insertion.

```text
 86 |
 87 |
 88 | class Base64App(App):
 89 |     def build(self):
 90 |         self.title = "Base64 Encoder / Decoder"
 91 |
 92 |         rootLayout = BoxLayout(orientation="vertical", padding=15, spacing=10)
 93 |         rootLayout.add_widget(
 94 |             Label(
 95 |                 text="Base64 Converter",
 96 |                 font_size="20sp",
 97 |                 size_hint_y=None,
 98 |                 height=40,
 99 |                 bold=True,
100 |             )
101 |         )
102 |
103 |         rootLayout.add_widget(
104 |             Label(
105 |                 text="Input Text / Base64 String:",
106 |                 size_hint_y=None,
107 |                 height=25,
108 |                 halign="left",
109 |                 valign="middle",
110 |             )
111 |         )
112 |         self.inputText = TextInput(
113 |             multiline=True,
114 |             hint_text="Enter text to encode or Base64 string to decode...",
115 |             size_hint_y=0.25,
116 |             background_normal="",
117 |             background_color=(0.1, 0.1, 0.1, 1),
118 |             foreground_color=(0.9, 0.9, 0.9, 1)
119 |         )
120 |         rootLayout.add_widget(self.inputText)
```

```text
121 |
122 |         encodeLayout = BoxLayout(
123 |             orientation="horizontal", spacing=10, size_hint_y=None, height=45
124 |         )
125 |
126 |         encodeButton = Button(
127 |             text="Encode to Base64",
128 |             background_color=(0.2, 0.6, 1.0, 1.0)
129 |         )
130 |         encodeButton.bind(on_press=self.encode)
131 |
132 |         decodeButton = Button(
133 |             text="Decode from Base64",
134 |             background_color=(0.2, 0.8, 0.4, 1.0)
135 |         )
136 |         decodeButton.bind(on_press=self.decode)
137 |
138 |         encodeLayout.add_widget(encodeButton)
139 |         encodeLayout.add_widget(decodeButton)
140 |         rootLayout.add_widget(encodeLayout)
141 |
```

#### Lines 142–212: show a result, report a problem, or copy text

Lines 142–157 put the fixed output caption next to a changeable status label. Lines 159–180 install the read-only field, its scroll container and the copy control before returning the root. Lines 182–200 update the *same* result and status objects on each press; 202–208 use the result as the clipboard source. Only the last two lines are under the direct-run guard. Source: `detail3_02372.430s.png` (140–169), `detail3_02377.500s.png` (154–182), `detail3_02371.300s.png` (173–202), and `detail3_02184.900s.png` (187–212); guard corroborated by `CSCS1240F26_30_00h37m00s.png`. The clipped header dimension and second prefix test are not completed from the apparent intent.

```text
142 |         outputHeaderLayout = BoxLayout(orientation="horizontal", size_hint_y=None, heig[remainder not visible]
143 |         outputHeaderLayout.add_widget(
144 |             Label(
145 |                 text="Output Result:",
146 |                 halign="left",
147 |                 valign="middle",
148 |             )
149 |         )
150 |         self.statusLabel = Label(
151 |             text="",
152 |             color=(0.3, 1.0, 0.3, 1.0),
153 |             halign="right",
154 |             valign="middle",
155 |         )
156 |         outputHeaderLayout.add_widget(self.statusLabel)
157 |         rootLayout.add_widget(outputHeaderLayout)
158 |
159 |         outputScroll = ScrollView(size_hint_y=0.3)
160 |         self.outputLabel = TextInput(
161 |             readonly=True,
162 |             multiline=True,
163 |             hint_text="Result will appear here...",
164 |             background_normal="",
165 |             background_color=(0.1, 0.1, 0.1, 1),
166 |             foreground_color=(0.9, 0.9, 0.9, 1)
167 |         )
168 |         outputScroll.add_widget(self.outputLabel)
169 |         rootLayout.add_widget(outputScroll)
170 |
171 |         copyButton = Button(
172 |             text="Copy Output to Clipboard",
173 |             size_hint_y=None,
174 |             height=45,
175 |             background_color=(0.9, 0.6, 0.1, 1.0)
176 |         )
177 |         copyButton.bind(on_press=self.copyOutput)
178 |         rootLayout.add_widget(copyButton)
179 |
180 |         return rootLayout
```

```text
181 |
182 |     def encode(self, instance):
183 |         source = self.inputText.text
184 |         try:
185 |             encoded = Base64.encode(source)
186 |             self.outputLabel.text = encoded
187 |             self.statusLabel.text = ""
188 |         except Exception as e:
189 |             self.outputLabel.text = f"Encoding Error: {str(e)}"
190 |             self.statusLabel.text = ""
191 |
192 |     def decode(self, instance):
193 |         source = self.inputText.text
194 |         try:
195 |             decoded = Base64.decode(source)
196 |             self.outputLabel.text = decoded
197 |             self.statusLabel.text = ""
198 |         except Exception as e:
199 |             self.outputLabel.text = f"Decoding Error: {str(e)}"
200 |             self.statusLabel.text = ""
201 |
202 |     def copyOutput(self, instance):
203 |         data = self.outputLabel.text
204 |         if data and not data.startswith("Encoding Error") and not data.startswith("Deco[remainder not visible]
205 |             Clipboard.copy(data)
206 |             self.statusLabel.text = "Copied to clipboard!"
207 |         else:
208 |             self.statusLabel.text = "Nothing valid to copy!"
209 |
210 |
211 | if __name__ == "__main__":
212 |     Base64App().run()
```

#### Later input-font revision — not part of the listing above

At 45:37 (`detail3_02737.000s.png`), editor line 116 adds an input font size. The closing parenthesis moves from 119 to 120, the insertion into the root from 120 to 121, and the encode button binding from 130 to 131 (corroborated by `detail3_02759.000s.png`). At 45:44 the input hint is visibly bigger than the output hint (`detail3_02744.000s.png`); that is a visible UI result, not console output. No output-field font edit is needed to account for this state.

```text
112 |         self.inputText = TextInput(
113 |             multiline=True,
114 |             hint_text="Enter text to encode or Base64 string to decode...",
115 |             size_hint_y=0.25,
116 |             font_size="24sp",
117 |             background_normal="",
118 |             background_color=(0.1, 0.1, 0.1, 1),
119 |             foreground_color=(0.9, 0.9, 0.9, 1)
120 |         )
121 |         rootLayout.add_widget(self.inputText)
```

### `base64App.py` — the button registrations, result widgets and callbacks

**Later buffer, after the input-font insertion.** In the 0:52:00–0:56:00 views, the one-line insertion shifts the button, result widgets and handlers down by one row relative to the earlier listing. At 0:54:00 the horizontal row still ends in `height=45`. The 0:53:24–0:53:26 views show the output header and read-only `TextInput`; 0:55:43–0:56:00 shows both conversion handlers and the start of `copyOutput`. The unchanged construction and method bodies are printed in the earlier lines 122–203 rather than repeated. The header constructor is still clipped (now line 143); the full later clipboard guard is not established. The registrations used for the subsequent experiment are:

```text
131         encodeButton.bind(on_press=self.encode)
137         decodeButton.bind(on_press=self.decode)
```

### `base64App.py`, line 131 — editor experiments versus runs

Each entry is a different time, **not successive lines of a file**. None of the edits changes the purpose of line 137, the Decode registration. In particular the classroom terminal did not report a `TypeError` for the parenthesized edit.

| Target | Line 131 as seen in the editor or the executed traceback | Evidence |
|---|---|---|
| 0:52:28 | `encodeButton.bind(on_press=self.encode)` | The original registration. |
| 0:53:00 | `encodeButton.bind(on_press=self.encode` | Incomplete editor line; no closing parenthesis visible, no execution shown. |
| 0:53:09 | `encodeButton.bind(on_press=self.encode)` | Outer call closed again. |
| 0:58:17 | `encodeButton.bind(on_press=self.encode())` | Added inner call; discussion, no corresponding classroom traceback. |
| 0:59:00 | `encodeButton.bind(on_press=self.encode)` | Back to the method reference. |
| 1:00:26 | `encodeButton.bind(on_press=)` | Blank keyword value during edit; not a demonstrated run. |
| 1:00:50 | `encodeButton.bind(on_press=apple)` | Executed line in the first traceback. |
| 1:01:00 | `encodeButton.bind(on_press="apple)` | Only the opening string quote in this transient editor state; bare-name traceback remains behind it. |
| 1:01:08 | `encodeButton.bind(on_press="apple")` | Executed line in the second traceback; the full editor line is also visible at 1:02:00 and 1:03:08. |

**Observed run evidence.** At 1:00:43 the already-open “Base64 Encoder / Decoder” window has empty input/result placeholders. At 1:00:46 PowerShell shows the preceding application leaving, followed by the new command `py .\src\base64App.py` at the `(Numerical) PS C:\com\joesta\Numerical>` prompt. At 1:00:50 the new run has reached `Base64App().run()` (line 213) and `root = self.build()` in Kivy; it stops at line 131. The terminal ends with:

```text
NameError: name 'apple' is not defined. Did you mean: 'tuple'?
```

At 1:01:05 that *older* bare-name traceback is still visible. In the second traceback at 1:01:08, the application again fails during `build`, this time **inside** `kivy._event.EventDispatcher.bind` after the line `encodeButton.bind(on_press="apple")`. The reported source line is 444; the terminal ends with:

```text
AssertionError: 'apple' is not callable
```

The last stills of the editor retain the invalid string. The closing 1:03:50 captures show the OBS recording controls, not a restored binding, a successful conversion, or a posted Kivy link.

## Micro-Learning Insights

Removable recall sheet · CSCS1240F26 · Session 30

- **Ask “first in what sense?”** At 03:32–05:59 the class discusses significance and reversing order. Before swapping anything, decide whether the unit is a bit in a stream or a byte in a multibyte field.
- **Read the measuring expression, not just its print label.** At 07:56–13:00 the number 16 comes from adding two sizes. That observation is narrower than “every Python object has a 16-byte header.”
- **Follow the document trail.** At 20:15 the instructor checks which RFC obsoletes which. For Base64, use RFC 4648 and then locate the section for the alphabet you actually need: standard or URL-safe.
- **Count choices separately from the highest value.** The student’s ASCII question at 27:06 exposes the distinction: 128 code positions run from 0 to 127; 256 possible byte values run from 0 to 255.
- **Draw the boundaries twice.** At 28:32–30:20 the same 24 bits are divided first into three octets, then into four sextets. No bit is discarded; the alphabet lookup happens only after regrouping.
- **Use a one-byte change as a test.** Start with `abc!@#`, then append one ASCII character. A previously complete group stays unchanged; the extra byte creates a new padded group. Look at the end of the result.
- **Find the correct widget before changing a property.** Around 42:11–45:36, the title already has large type but the input does not. Follow the constructor for the text you want to enlarge; resizing the container is a different experiment.
- **Keep imported tools quiet.** At 37:18–38:35 the instructor links the GUI entry point to the Item challenge. A useful test under the main guard can remain in the file without printing during an ordinary import.
- **Trace a binding in two directions.** Around 52:33–58:51, start at `encodeButton` to identify the event source, then follow `self.encode` to the receiving method. The button and the app are distinct objects.
- **Debug the earliest failure first.** Around 1:00:18–1:01:34, quoting `apple` fixes name lookup, not callability. A different error after a small change can be evidence that execution reached the next check.
- **Test what you think the framework does.** The student asks to see `bind` rather than accepting that Python “just knows.” The useful question is who **stores** the callable and who **invokes** it later—not whether its name sounds like an event.
