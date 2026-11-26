# CSCS1240F26 · Session 32 | From grammar rules to UTF-8 bytes

**Worked teaching companion** · [Class recording: CSCS1240F26 32, JoesTA](https://www.youtube.com/watch?v=5aVtm-oPRzQ)

The app’s opening result makes a useful puzzle: **16 characters, 23 bytes**. If a character is a number, why doesn’t each character take one byte? The instructor takes a deliberate detour through a C language standard to teach a reading skill: follow a named rule down to the symbols it actually permits. The same skill unlocks the UTF-8 rule table. By the end, you can take the bits of one character number, predict the exact bytes Python will build, and read the number back from those bytes.

**How to use the companion.** Follow the classroom sequence on a first read. To work the encoding by hand, jump to the four byte patterns, then the two-octet scratch trace. Use the knowledge check before consulting the answer key. The historical appendix preserves screen states and their limits separately from the explanations; its gutter numbers are not part of any program. The last appendix can be removed without losing any worked example.

**Source coverage.** The public recording title, channel, and video ID match this session. The full retrieved English auto-caption track has 2,197 ordered cues, from 0:00:01.120 to the final sign-off at 1:49:33.560. The video stream is 1:49:34.083 long. There are brief intervals without captions; that does not establish silence. Visual evidence comprises 158 verified frames, including every whole-minute target from 0:00:00 through 1:49:00 and additional event frames, including the opening app result and rapid editor scroll. Timestamps identify requested seeks, not exact decoded-frame timestamps. Displayed code, speech, guide reconstructions, and independent Python results are labeled separately. No earlier AI guide supplies lesson evidence.

## From text in a box to a question about bytes (0:00:01–0:03:29)

At the opening, Kat's Discord post is a link to *How I released a game that has no assets*. The instructor raises the distinction between writing code and supplying assets, but postpones interpreting the video rather than assuming how the game works. An earlier post on the same screen names **`binOp.py`**, **`base64App.py`** and [the F26Demo repository](https://github.com/joes-ta/F26Demo); it reports that the Base64 app had been committed in its broken state. That is a report about a previous session, not a failure of today's UTF-8 app.

At 0:01:07 the instructor proposes reversing the order of the previous topics: before using Base64 to represent bytes, find out how text became bytes.

### The opening experiment: why do 16 characters need 23 bytes? (0:02:09–0:02:30)

The instructor returns to an app seen at the end of the previous class and asks how the bits and bytes on its screen got there. Start with its actual input: **`Hello, 🌍! Café 🚀`**. Before the breakdown, the window already says **`Characters: 16 | UTF-8 Bytes: 23`**. Its result area still reads **`Results will appear here...`** (0:02:18, `t00138.png`). At 0:02:24 (`t00144.png`), the same field and counter appear above four populated sections. These are before-and-after observations, not evidence that the instructor typed this particular string. It is also the default in the editor.

| Observed result heading | What appears under it |
|---|---|
| Original Text: | The text from the field. Both emoji positions render as small missing-glyph boxes in the GUI, rather than as recognizable pictures. |
| Hexadecimal Byte Stream: | The 23 bytes below, with a literal `0x` before each. |
| Binary Byte Stream: | The **same** bytes, in the same order, each written with eight binary digits. |
| Decoded Result: | Visibly matches Original Text, including the two glyph boxes. The editor default and the reported bytes independently identify the underlying symbols. |

**Observed GUI streams** (display wrapping reflowed only; groups are individual bytes):

```text
0x48 0x65 0x6C 0x6C 0x6F 0x2C 0x20
0xF0 0x9F 0x8C 0x8D 0x21 0x20 0x43 0x61 0x66
0xC3 0xA9 0x20 0xF0 0x9F 0x9A 0x80

01001000 01100101 01101100 01101100 01101111 00101100 00100000
11110000 10011111 10001100 10001101 00100001 00100000 01000011 01100001 01100110
11000011 10101001 00100000 11110000 10011111 10011010 10000000
```

The first byte, **`0x48`**, and the first binary group, **`01001000`**, are two ways to write the same number for **`H`**. The 13 ASCII characters each take one byte. The single precomposed **`é`** takes two; **`🌍`** and **`🚀`** take four apiece. That accounts for all 23 bytes without treating any space or punctuation as invisible. A missing picture in the font is not, by itself, a missing character in the data. *Independent check:* Python's UTF-8 codec applied to the editor literal reproduced the 16/23 counts and both complete displayed streams using the screen's formatting expressions. This was not a second classroom GUI run.

**Key items to scan**

- **`binOp.py`**, **`base64App.py`**, **F26Demo** — the Discord post identifies the previous committed work, not an error from the UTF-8 run.
- **`Hello, 🌍! Café 🚀`** — the prefilled example, including the exclamation mark immediately after the globe.
- **`Characters: 16 | UTF-8 Bytes: 23`** — two different measurements; Python string length counts code points here, not encoded bytes.
- **`Results will appear here...`** — the counts are initialized before the full report exists.
- **`0x48`**, **`01001000`** — the same first byte in hexadecimal and binary, not two successive transformations of the text.
- **`é`** → `C3 A9`; **`🌍`** → `F0 9F 8C 8D`; **`🚀`** → `F0 9F 9A 80` — the three characters that make the byte count exceed the character count.

### Two different events in the same window (0:02:49–0:03:05)

The instructor locates **`utf8.py`** and recalls going through the Kivy interface last time. The editor shows a useful design distinction the narration does not spell out: editing the text recalculates the counts; pressing **`Process Full UTF-8 Breakdown`** builds the detailed report. The app does not need a button press to tell you the starting length. Conversely, the text-change handler shown here does not clear or rebuild an old report. If the user edits after processing, the counts can describe the new text while the report still describes the previous press. That last point follows from the callbacks, not an observed on-screen edit.

**Annotated teaching version — guide-added comments; selected controls in execution order; the layout is abbreviated, not a complete Kivy file.** The root is a vertical box; the heading and prompt sit above a single-line field, followed by a count label, button and scroll container. **`build`** creates the controls; binding a method registers it for a future event rather than calling it now.

```python
# Inside build(self); selected layout arguments only. The source's line 68 is clipped.
rootLayout = BoxLayout(orientation='vertical', padding=15)  # Stack the controls vertically.
# Two labels above the field supply the title and prompt; see the numbered listing.
self.messageText = TextInput(text="Hello, 🌍! Café 🚀", multiline=False,
                             size_hint_y=None, height=45)  # One line, initially the 16-character sample.
self.messageText.bind(text=self.messageChange)            # Subscribe to future text changes.
rootLayout.add_widget(self.messageText)                   # Place this field beneath the prompt.
self.statisticLabel = Label(text="", markup=True,
                            size_hint_y=None, height=30)  # Start with a new, empty count label.
rootLayout.add_widget(self.statisticLabel)                # Place it beneath the input.
processButton = Button(text="Process Full UTF-8 Breakdown", size_hint_y=None,
                       height=50, background_color=(0.1, 0.5, 0.8, 1))  # New button for the report.
processButton.bind(on_press=self.process)                 # A press requests a report; no call yet.
rootLayout.add_widget(processButton)                      # Place the button after the counts.
self.resultLabel = Label(text="Results will appear here...", halign='left',
                         valign='top', markup=True)       # New report label, initially a placeholder.
# Line 116 also binds the result label's size; only its prefix survives in the screen record.
scroll = ScrollView(size_hint=(1, 1))                     # New container for the result label.
scroll.add_widget(self.resultLabel)                       # Put the report inside the container.
rootLayout.add_widget(scroll)                             # Add the container, not a second report.
self.updateStats(self.messageText.text)                   # Set counts to 16 and 23 before any edits.
return rootLayout                                         # Return the assembled layout to the app.
```

```python
def messageChange(self, instance, value):  # Kivy supplies the field and its new text.
    self.updateStats(value)               # Forward the new text, not the widget, for counting.

def updateStats(self, text):              # Same operation at startup and after an edit.
    charCount = len(text)                 # 16 code points for the default text.
    encoded = encode(text)                # Obtain its encoded bytes.
    octetCount = len(encoded)             # 23 bytes; the existing count label is updated next.
    # The historical label assignment is right-clipped; its rendered values appear above.
```

On a press, **`process`** reads the current value of the text field and encodes it once for this report. Each list comprehension works through that one byte sequence; it does not create a different encoding. The decoder receives the bytes themselves, not the space-separated hex or binary *display strings*.

```python
# Normal path inside process(self, instance); guide-added comments.
text = self.messageText.text                          # Read the current field, not a hard-coded example.
encoded = encode(text)                                # The demo text yields 23 bytes.
hexDisplay = " ".join([f"0x{b:02X}" for b in encoded])  # e.g. byte 72 → 0x48.
binaryDisplay = " ".join([f"{b:08b}" for b in encoded]) # Same byte → 01001000.
decoded = decode(encoded)                             # Recover text from these bytes.
result = (                                            # Adjacent strings make one report.
    f"[b]Original Text:[/b]\n{text}\n\n"                 # Source text below a bold heading.
    f"[b]Hexadecimal Byte Stream:[/b]\n{hexDisplay}\n\n" # Two uppercase hex digits per byte.
    f"[b]Binary Byte Stream:[/b]\n{binaryDisplay}\n\n"   # Eight bits per byte.
    f"[b]Decoded Result:[/b]\n{decoded}"                 # Recovered text comes last.
)
self.resultLabel.text = result                         # Change the on-screen label; no print call.
```

The result label aligns its text at the top left and interprets markup, so **`[b]`** marks headings rather than becoming part of the report. The visible **`except`** branch instead constructs a red **`Error:`** message from an exception; no error is shown in the opening breakdown. The direct-execution guard calls **`Utf8().run()`**. At 0:02:35 **PowerShell 7 (x64)** shows Kivy 2.3.1, Python 3.12.7 and **`Start application main loop`**—startup messages, not the breakdown. Exception handling does not mean every malformed byte input raises an exception.

**Key items to scan**

- **`TextInput`**, **`Button`**, **`Label`** — supply the input, action and display widgets. **`BoxLayout`** arranges them; **`ScrollView`** holds the report.
- **`Utf8().run()`**, **PowerShell 7 (x64)** — the guarded call starts the app; the console shows startup information while the GUI shows the byte stream.

- **`utf8.py`** — the source file shown beside the Explorer listing; the screen is a scroll through code, not a sequence of edits.
- **`build`**, **`rootLayout`** — create the window contents and return the assembled vertical layout.
- **`self.messageText.bind(text=self.messageChange)`** — rerun the count calculation when the field's text changes.
- **`self.updateStats(self.messageText.text)`** — initialize the counts before any user edit; `len(text)` and `len(encoded)` measure different objects.
- **`Process Full UTF-8 Breakdown`**, **`process`** — the button is bound to the method that rereads the field and prepares a full report.
- **`0x{b:02X}`**, **`{b:08b}`** — render one byte with leading zeros as needed; the space passed to `join` separates neighboring byte representations.
- **`self.resultLabel.text`** — assigning the completed string changes the GUI, whereas `decode` returns a string to its caller.
- **`ScrollView`** — holds the result label, not the input or counts. **`except Exception as e`** — handles a raised exception in the guarded processing code, not every potentially invalid input.

The opening scroll also shows the two conversion functions. At 0:03:05–0:03:29 the instructor singles out their shifts and masks: why do these operations produce UTF-8 rather than an arbitrary arrangement of bits? The class turns to the standard before working through the arithmetic.

## Follow the document trail: RFC → character set → standards body (0:03:29–0:08:30)

Searching the RFC Editor for UTF-8 leads to **RFC 3629**, *UTF-8, a transformation format of ISO 10646* (0:04:00). The header identifies F. Yergeau, November 2003, **STD 63**, and **`Obsoletes: 2279`**. The page actually labels this RFC an **Internet Standard**. Its title distinguishes two jobs: the coded character set supplies numbered positions for characters; the transformation format tells you how to represent those numbers as bytes. To understand the dependency, the instructor follows the number in the title instead of trying to infer the rules from the Python masks alone.

The class asks: what makes a specification a **formal standard**? One participant describes a shared reference everyone can return to. The instructor adds the approval step: Microsoft can publish a specification others implement, but that act alone is not adoption as an ISO standard. The contrast is about who approved *this document*, not whether corporate work is useful or whether a widely adopted convention exists. The instructor connects this to seven-bit ASCII and ANSI (the American National Standards Institute), and to the economics of distributing documents: sales and membership can help support the work; some standards cost money, others or their working drafts are freely available.

At 0:05:00–0:07:00 the screen is a search overview for **`ISO 10646`**, not a standards text. At 0:08:00 the [ISO catalogue entry](https://www.iso.org/standard/69119.html) itself calls the subject **Universal Coded Character Set (UCS)**, identifies the 2017 publication as edition 5, and marks it **Withdrawn**. The listing shows 2,693 pages and JTC 1/SC 2. Its abstract mentions the structure of the codespace, the Basic Multilingual Plane and supplementary planes. A historical catalogue entry can explain what a document covers without being the edition to implement today.

**Supplemental clarification.** ISO's English name is **International Organization for Standardization**, not “International Standards Organization.” ISO describes national members nominating experts and voting on drafts, and document sales funding development and maintenance ([ISO: About us](https://www.iso.org/about)). The **International Electrotechnical Commission (IEC)** is global, not Europe's counterpart to ANSI ([IEC: About us](https://www.iec.ch/about-us)). Likewise, an RFC number identifies a document; here the *status label* is what establishes that RFC 3629 is an Internet Standard.

**Key items to scan**

- **RFC 3629 / STD 63** — the UTF-8 standard on screen; RFC 2279 is its predecessor.
- **UCS** — the character set referred to by ISO/IEC 10646, distinct from the UTF-8 byte format.
- **Formal standard** — look for the approving organization, publication and edition, not just a familiar company or document title.
- **Withdrawn** — the status of the particular 2017 edition shown, not withdrawal of the UCS itself.
- **ISO** and **IEC** — distinct international bodies; a joint publication can carry both names.

## Find the working group, then check which C document you opened (0:08:30–0:13:43)

The plain [Open Standards directory](https://www.open-std.org/) at 0:09:00 is useful precisely because its links expose the organization. Read **`ISO/IEC JTC1/SC22/WG14`** from left to right: joint technical committee → subcommittee → working group. **SC 2** lists character sets; **SC 22** lists programming languages and operating systems, including **WG 9** for Ada and **WG 14** for C. **SC 34** covers document description and processing languages; **SC 35** covers user interfaces. The numbers are addresses, not versions or steps in an approval vote. The instructor's aside about whether there was another JTC was tentative; this directory is not a census of committees.

Who actually writes the document? In the instructor's account, people with different backgrounds work together, often with employers supporting their participation. The **WG14** home page at 0:11:00 points separately to projects, a document register, meetings, defect reports and older rationales. It says C23, ISO/IEC 9899, was adopted by ISO and IEC in 2024. A firm can contribute expertise or fund a participant without unilaterally deciding what all implementations must do.

The [next PDF, N3998](https://www.open-std.org/JTC1/SC22/WG14/www/docs/n3998.pdf), is a different object. The viewer shows **793 pages** at 0:12:00. At 0:13:00 the cover explicitly reads **`ISO/IEC 9899:202y (en) — N3998 working draft`** (*Information technology — Programming languages — C*). The placeholder year and “working draft” matter more than the template words “INTERNATIONAL STANDARD.” The captions' “3938” is not the number on the cover. The instructor's “one bite at a time” strategy is practical: use the outline to look up **Language**, **Library**, then the syntax annex. The language provides the building blocks; the standard library specifies facilities a programmer would otherwise have to write or obtain. The instructor compares Python's relatively small keyword set with all the programs and library facilities one can build using it. The spoken “30 or so” estimates are not definitive keyword counts.

**Supplemental clarification.** ISO lists **ISO/IEC 9899:2024** as the published fifth edition. Its scope includes syntax, constraints and semantic rules, not just the list of patterns in an annex ([ISO/IEC 9899:2024](https://www.iso.org/standard/82075.html)). Cite the publication for the adopted edition; cite N3998 when reporting this screen.

**Key items to scan**

- **JTC 1 → SC 22 → WG 14** — the actual route to C; **SC 2** leads toward character sets instead.
- **WG 9**, **SC 34**, **SC 35** — Ada working group, documents subcommittee and user-interfaces subcommittee, respectively.
- **C23 / ISO/IEC 9899:2024** — adopted C edition named on the WG14 site; the year in the nickname need not be the publication year.
- **N3998** — separately opened C working draft with a 793-page viewer count and a placeholder date, not the published C23 PDF.
- **Language**, **Library**, **Annex A** — look for language rules, supplied facilities, and a compact syntax summary in different places.

## Read a grammar without mistaking the notation for source code (0:13:43–0:18:25)

The instructor goes to **Annex A**, an appendix. The outline labels it informative; the page is a *summary*, with references back to the clauses. **A.2.1 Lexical elements** gives five possibilities for a token: *keyword, identifier, constant, string-literal, punctuator*. It also lists **preprocessing-token** separately, with alternatives such as *header-name* and *pp-number*. A keyword table is therefore not the entire vocabulary a C compiler encounters. **`return`** is literally on that table; a name a programmer chooses belongs to a different category. The visible N3998 table (transcribed in the historical appendix) has **54 entries**, not “30 or so.” That is a count of *this draft table*, not of all C editions or of Python.

At 0:14:43 the instructor names **Backus–Naur form (BNF)**: a notation for saying exactly which arrangements of symbols are possible. Wikipedia is suggested for background on BNF and its variants. The class looks for **Extended BNF (EBNF)** and **Augmented BNF (ABNF)**, using a duplicate browser tab to keep its place. The document actually shown at 0:17:00 is the ABNF RFC: §2.1 explains that a rule name begins with a letter and can continue with letters, digits and hyphens. For example, **`<rulename>`** and **`<RULENAME>`** refer to the same rule; angle brackets are not required in that notation. This says nothing about whether two different capitalizations of a *C variable* are interchangeable.

**Supplemental clarification.** [RFC 5234 §2.1](https://www.rfc-editor.org/rfc/rfc5234#section-2.1) defines ABNF rule names; [ISO/IEC 14977:1996](https://www.iso.org/standard/26153.html) defines an Extended BNF notation. The search for both does not make RFC 5234 the EBNF standard. Read the notation conventions in the document you are actually using.

**Key items to scan**

- **Annex A / A.2.1** — summary of lexical rules, including *token* and *preprocessing-token*; the clause numbers point back to their definitions.
- **A.2.2 Keywords** — literal C spellings such as `return`, `int`, `nullptr`, `_Countof`; these are N3998 examples, not a list of what every C compiler accepts.
- **BNF** — uses named rules to define possible symbol sequences. **EBNF** and **ABNF** are related notations, not interchangeable standards.
- **RFC 5234 §2.1** — changing the case of an ABNF rule name does not create a different rule.

## Build a C identifier one character at a time (0:18:05–0:23:59)

On the C draft page, the colon means “is defined as”; indented **separate lines** below it give alternatives. Items **on the same line** must appear in sequence. Italics mark names of categories to expand (*nonterminals*); bold literal spellings such as **`while`** are things one can type (*terminals*). Roman prose such as “XID_Start character” describes a class of characters. It does **not** tell you to type the literal words **`XID_Start character`** into your program.

**Annotated teaching version — guide-added notes beside the two observed productions; this is notation, not runnable C.**

```text
identifier:                          ← name of the category being defined
    identifier-start                 ← choose this line to get the first character
    identifier identifier-continue   ← OR take an existing identifier and extend it once
```

At 0:18:46 the instructor asks what looks circular. If the *only* rule referred to **`identifier`** again, there would be no way to get started. But the **first production supplies a base case**: one permissible start already is an identifier. The second production is **recursive**: it uses that whole existing identifier, adds one allowable continuation, and yields another complete identifier. Nothing requires another continuation; nothing in this production fixes the number of repetitions.

**Worked derivation — guide example, not text typed in class.** Borrow **`b2`** from the earlier Python listing and test that *spelling* against the C rule. Extend it once more to see why the result can be fed back into the rule.

| Candidate so far | Rule applied | Result |
|---|---|---|
| Empty | Choose `nondigit` → `b` for `identifier-start`. | `b` is already a complete identifier. |
| `b` | Choose `digit` → `2` for `identifier-continue`. | `b2` is one identifier, not two tokens in this spelling. |
| `b2` | Choose `nondigit` → `_` for another continuation. | `b2_` is a complete identifier; we can stop. |
| Try `2` first | `digit` is listed under continue, not start. | This route cannot produce `2b` as an identifier. |

The search for actual characters is the next useful move. **`identifier-start`** offers *nondigit*, an XID_Start character, or a universal character name of that class. **`identifier-continue`** also allows *digit*, and has XID_Continue alternatives. At 0:22:20 the instructor asks which starter to skip if trying first to investigate something *other than another italicized rule*. The class defers *nondigit* and searches the PDF for **`XID_Start character`** instead. The 0:23:35 screen makes both routes useful: it lists **`_`** and the upper- and lowercase basic Latin letters under *nondigit*, **`0`** through **`9`** under *digit*, and has a green find match on the XID phrase. “Nondigit” here names that defined production, not literally anything that isn't a digit; a space or **`!`** does not qualify through it.

The very same page has separate **Constraints** and **Semantics** headings. It requires an identifier to conform to **Normalization Form C** as specified in ISO/IEC 10646 and relates XID characters to properties of code points. A name can match the broad shape and still have to meet another requirement; the syntax summary alone is not a promise that an entire program is valid. At 0:23:49 the instructor pivots from the heading *Semantics* to a new question: what is grammar, and how does it relate to syntax?

**Key items to scan**

- **`identifier-start`** — the base case: a single `b` suffices. **`identifier identifier-continue`** — one extension such as `b` → `b2`; the result can be extended again.
- **`nondigit`** — the displayed rule enumerates `_` and basic Latin letters; it does not mean arbitrary punctuation.
- **`digit`** — one of `0` through `9`; it can extend `b` to `b2` but cannot start this name.
- **`XID_Start`**, **`XID_Continue`** — property names used by the draft to describe eligible characters; not literal source spellings to append.
- **Constraints / Normalization Form C** — a separate condition beyond choosing grammar alternatives. **Semantics** — the adjacent prose explains how terms are interpreted.

## How did the language get its rules? (0:24:00–0:31:12)

English speakers did not wait for someone to publish a rulebook before they could talk to each other. That was the class's way into **grammar**: the rules according to which smaller units combine, not merely a collection of words. A Google AI Overview on screen defined grammar in terms of a *natural* language (0:25:00). The instructor questioned that restriction: C has a grammar, too. When “human language” didn't settle what *natural* meant, the instructor asked the class to split the phrase and explain *natural*. A student's answer, “organic,” gave them a useful test: did speakers develop the language in use over time, or did someone deliberately design it?

English, French, German and Spanish were the instructor's examples of **natural languages**. A student pointed out that writing systems can be planned. That raises a different question: *How is the message conveyed?* The language search on screen (0:28:00) mentioned grammar and vocabulary conveying meaning through speech, signs or writing. Writing down English does not by itself turn it into a designed language. A student's Internet analogy also separated the history of a whole system from the authorship of a rule: no single person planned the whole Internet, yet particular protocols have deliberately written specifications.

Klingon in *Star Trek* supplied a **constructed language**: its fictional setting did not prevent people from deliberately designing a language for expression. A student proposed *Star Wars*; the instructor explicitly declined to decide that example. C and Python are also deliberately designed, but for them the lesson emphasized another property: a **formal language** has precisely specified rules for which symbol sequences belong to it. The C draft actually gives rules you can follow. The instructor's conjecture about whether a Klingon BNF exists was not the test. *Constructed* answers a question about origin; *formal* answers one about specification. A formal grammar describes structure, not the entire meaning of a program.

**Key items to scan**

- **Grammar** — rules for combining units; C's identifier rules are as much an example of structure as a rule for combining English words.
- **Natural languages** — English and the other classroom examples developed through use; speaking, signing and writing are ways of conveying meaning, not tests of origin.
- **Constructed language** — Klingon was the class's deliberately designed example; C and Python were designed too.
- **Formal language** — the C draft specifies permitted symbol sequences with explicit rules; deliberate design alone was not the distinction being illustrated.

## A name you can build, character by character (0:31:12–0:36:56)

“Can these symbols be arranged this way?” is a question of **syntax**. “What does this arrangement designate?” is a question of **semantics**. The instructor connected the first to familiar syntax errors and the second to a person being identified by the name Joe. The semantics search shown at 0:32:00 emphasized meaning in language, signs and symbols. Applied to C, an **identifier** names an entity; its spelling alone does not say which entity it denotes in a particular use. The PDF's *Constraints* and *Semantics* headings matter: the displayed identifier rule is not the whole specification.

At 0:32:24 the instructor previewed **context-free grammar** and warned against concluding that printing a grammar in BNF settles every question about C. A precise way to read *context-free* here: a production can expand its named category without testing what symbols surround that category. It does **not** mean each character can be interpreted in isolation. The same digit can be accepted *after* an initial letter and rejected *as* the initial character because those are different grammar categories. Nor does matching a spelling rule establish the meaning or validity of a whole program. Language and type theory were signposts for later study, not a proof about C's classification.

The instructor briefly identified a **token** as a keyword, identifier, constant, string literal or punctuator, then returned to the identifier example. In the draft's A.2.2 keyword table, **`int`** and **`return`** are actual words, not categories that must be expanded. By contrast, the italic category **`nondigit`** has its own rule. A spoken slip calls it terminal, but the visible *nondigit: one of* list settles the issue: *nondigit* is the nonterminal; the literal **`C`** chosen from its list is a terminal. The name *nondigit* is not permission to choose anything that isn't a number: a space does not appear on that list.

**Worked teaching trace — classroom pair, guide-added explanation** (0:35:51–0:36:49; rules visible at 0:35:50 and 0:36:00). No compiler was run in class here.

| Input and position | Rule to follow | What you have afterward |
|---|---|---|
| **`C3`**, first character **`C`** | Choose **`identifier-start`**, then **`nondigit`**, then the literal capital letter. | **`C`** already counts as an identifier; no continuation is required. |
| **`C3`**, next character **`3`** | Choose **`identifier identifier-continue`**. Keep **`C`** as the identifier already built; the continuation may be a **`digit`**. | **`C3`** is a permitted identifier spelling. Nothing is declared just by checking its spelling. |
| **`3C`**, first character **`3`** | Starting over requires a fresh **`identifier-start`**. A decimal digit is not a start alternative. | The attempt fails at the first character. A legal letter later cannot repair it. |

For familiar basic characters, the draft lists underscore and both cases of the alphabet as nondigits; its digits are 0 through 9. **`C3`** and **`c3`** have different spellings and case matters. The instructor used the basic subset for the example. The fuller text on screen (0:24:00; 0:31:00; 0:33:00) also admits **`XID_Start`** and **`XID_Continue`** branches and corresponding universal character names. It requires Unicode Normalization Form C, notes that whether **`$`** may act as a nondigit is implementation-defined, and says some names are reserved. Thus neither “only ASCII ever works” nor “anything except a digit can start a name” follows from the example. Even a keyword whose letters fit the pattern is not thereby available as an ordinary variable name.

**Key items to scan**

- **Syntax** — tests whether the order and spelling fit the rules; **semantics** — asks what a valid form means or designates.
- **Context-free grammar** — expands a named category by its production; it need not decide what an identifier refers to.
- **Token** — a lexical unit; **`int`** and **`return`** are keywords, whereas **`C3`** can be an identifier.
- **Identifier** — a name. **`identifier-start`** is required once; **`identifier identifier-continue`** extends a name already formed by one character.
- **`nondigit`** — a category with its own list; **`C`** and underscore are concrete choices. **`digit`** — a category including **`3`**, usable in a continuation.
- **`C3`**, **`3C`**, **`c3`** — start with a letter and append a digit; reverse them and the start fails; change letter case and the name differs.
- **`XID_Start`** and **`XID_Continue`** — the draft's additional character-property branches; the basic letter/underscore example does not exhaust the rule. **`$`** — the draft explicitly leaves this nondigit choice to the implementation.

## Read the grammar from the largest unit down (0:37:00–0:42:00)

The draft starts with small pieces. The instructor deliberately worked backward from near the library summary to see what the small pieces ultimately make. Annex A.4 first turned up the **preprocessor**, the stage that handles directives before the resulting C unit is compiled. At 0:38:00 its own grammar is on screen: the **`# include`** rule takes preprocessing tokens and a newline; the **`# define`** and **`# undef`** rules show how the same notation can describe giving a macro name a replacement and removing a definition. Those are reference rules, not commands the class executed. The instructor briefly flagged IEEE 754 and floating-point numbers as a topic to return to, not something completed here.

Back in A.3.4, “External definitions,” the question was: *What must the first item be?* The answer is one **`external-declaration`**. That one item is already a **`translation-unit`**. A second alternative allows a unit already built to be followed by one more external declaration. Each use of the second alternative makes a longer unit; the first is what lets you start. Here *external* refers to a top-level item, not to a requirement to write the C keyword **`extern`**. Follow the next rule and the item can be either a **`function-definition`** or a **`declaration`**. The instructor's point was that a complex program can be understood in terms of a small number of repeated forms. More precisely, this rule describes one translation unit; a complete program need not consist of a single translation unit.

**Annotated teaching example — guide-created C fragment, not a classroom file or run.** Read these two successive top-level items as an application of the displayed grammar:

```c
int C3;                          /* One declaration; it alone forms a unit. */
int answer(void) { return 3; }   /* Append one function definition to that unit. */
```

The second line counts as one top-level item even though its body contains a return statement. The statement belongs *inside* the function body, not alongside the two external declarations. Reading the fragment predicts no printed result; the grammar exercise neither calls the function nor specifies an entire executable. For a new production with this shape, find the shortest complete alternative first, then identify exactly one item that the other alternative appends. It is the same reasoning that grew **`C`** into **`C3`**, now applied to a larger unit.

**Key items to scan**

- **Preprocessor** — handles directives before compilation; **`# include`**, **`# define`** and **`# undef`** are directive forms visible in A.4, not top-level declarations in A.3.4.
- **`translation-unit`** — one external declaration to start, then zero or more appended declarations of the same top-level category.
- **`external-declaration`** — one item in that sequence, not necessarily a declaration containing **`extern`**.
- **`declaration`** — **`int C3;`** supplies the first item in the teaching fragment. **`function-definition`** — the complete **`answer`** function supplies the second.
- **`C`** → **`C3`** — grow by one continuation character. The top-level grammar has the same base-and-extension pattern but appends a whole declaration instead.

## Why a UTF-8 document defines words like SHALL (0:42:15–0:45:56)

The detour into C had a purpose: return to a real document that uses formal notation. The instructor reopened the UTF-8 RFC and asked the class to unpack *transformation format* just as it had unpacked *natural language*. *Transform* means change; *format* describes how something is structured. UTF-8 changes the **representation** of a character number into an octet sequence, not the character's meaning. At 0:43:00 the title is visible: *UTF-8, a transformation format of ISO 10646*. The participant who noticed the dim, unplugged laptop offered a practical aside: an apparent software slowdown may have a physical cause. The class did not establish its cause by a test.

The next reading tool was not a new encoding calculation but a reference inside the specification. RFC 3629 §2 says that its requirement words take their meanings from RFC 2119. The same section uses **`U+HHHH`** for a UCS character number written with four to six hexadecimal digits. [RFC 3629](https://www.rfc-editor.org/rfc/rfc3629) At 0:45:00 the reference's hover card names RFC 2119, BCP 14, *Key words for use in RFCs to Indicate Requirement Levels*; this is a reference preview, not evidence that the class read the full document. The instructor encouraged students to read it.

The class tried to rank *must*, *shall*, *should* and *may* by how strong each sounded in conversation. That is exactly the ambiguity the RFC removes. **Standards clarification:** the suggestion that *shall* is slightly weaker than *must* is understandable in ordinary conversation, but **`SHALL`** is **not** weaker than **`MUST`** under RFC 2119. And a recommendation is more than a wish. The following is the cited standard's meaning, not a transcript of the speculative ranking. [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119)

| Words in RFC 2119 | What an implementer can decide |
|---|---|
| **`MUST`**, **`REQUIRED`**, **`SHALL`** | An absolute requirement; failing it violates the specification, whether or not the implementation seems to work. |
| **`MUST NOT`**, **`SHALL NOT`** | An absolute prohibition; no choice to perform the prohibited behavior. |
| **`SHOULD`**, **`RECOMMENDED`** | Follow the recommendation unless a particular circumstance supplies a valid reason; understand and weigh the implications before departing. |
| **`SHOULD NOT`**, **`NOT RECOMMENDED`** | Normally avoid it. A particular reason can justify doing it after the consequences are weighed. |
| **`MAY`**, **`OPTIONAL`** | Truly optional. RFC 2119 also calls for interoperability between implementations that include the option and those that omit it, apart from the option's own functionality. |

**Worked reading — actual standard, not a classroom execution.** RFC 3629 §3 says UTF-8 implementations **`MUST`** protect against decoding invalid sequences. [RFC 3629](https://www.rfc-editor.org/rfc/rfc3629) If an implementation interprets an invalid sequence as though it were valid, that is not just a missed recommendation. Under RFC 2119, replacing **`MUST`** with **`SHALL`** would leave the obligation unchanged; replacing it with **`SHOULD`** would change it. [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) The lowercase English word *may* in a student's question is not by itself a declaration that any feature is optional. First find the governing document and its conventions.

**Key items to scan**

- **Transformation format** — change the representation while retaining the character being represented; **`U+HHHH`** names a character number rather than its UTF-8 byte sequence.
- **RFC 3629 §2** — the UTF-8 specification tells the reader where to find its keyword definitions. [RFC 3629](https://www.rfc-editor.org/rfc/rfc3629)
- **`MUST`**, **`REQUIRED`**, **`SHALL`** — equally binding. **`MUST NOT`** and **`SHALL NOT`** — equally prohibitive. [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119)
- **`SHOULD`** / **`RECOMMENDED`**, **`SHOULD NOT`** / **`NOT RECOMMENDED`** — recommendations in opposite directions; neither licenses an unexamined exception. [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119)
- **`MAY`** / **`OPTIONAL`** — the implementer really can choose, with the interoperability qualification in RFC 2119 §5. [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119)

## What was the stakeholder actually promised? (0:45:56–0:48:59)

A student suggested assigning numbers to obligation levels. The instructor asked what *level one* would mean. A number is a symbol just as a word is: without a shared definition it does not tell a developer whether an omitted feature is a defect. This matters when somebody is paying for the result. The instructor contrasted writing a personal tool with delivering software after hours of work and hearing, “that's not what I asked for.” The buyer, end user or other party with an interest in the outcome is a **stakeholder**.

A **software requirements specification (SRS)** records the behavior the parties expect and gives them something to review before that mismatch becomes expensive. The instructor called requirements commitments between the parties, like a contract; the point was agreement on delivery, not that every SRS necessarily has a particular legal status. The search at 0:48:00 used **`srs software requirements specification`**. Its result distinguished *what* the system must do from *how* it is internally built, tied the document to estimating, scheduling, testing and quality assurance, and showed purpose, scope, definitions and product perspective in an example structure. The result mentioned ISO/IEC/IEEE 29148; the class did not inspect that standard or prescribe this particular template.

**Transfer example — hypothetical agreement, not a class assignment or an RFC requirement.** Suppose the parties agree to use RFC 2119 terms. “The exporter SHALL save a UTF-8 text file” gives a reviewer an acceptance check: request an export and check the actual file and its encoding. “The exporter MAY also display a preview” leaves the preview optional; its absence alone does not fail the first requirement. Neither sentence dictates a particular library or internal design. Agreeing on those tests before implementation addresses the instructor's example of a stakeholder rejecting a completed delivery. By 0:48:50 the discussion returns to the UTF-8 definition and the symbols in its encoding description.

**Key items to scan**

- **Stakeholder** — the person or group whose need the delivery has to meet; a working program can still be the wrong program for that person.
- **Software requirements specification (SRS)** — agreed, reviewable behavior and scope; serves as a baseline for implementation and testing.
- **`srs software requirements specification`** — the on-screen search illustrated *what, not how*; its overview is not itself an adopted standard.
- **Numeric level** — no more self-explanatory than a word. State what the symbol obligates someone to do and how a reviewer can tell whether it was done.

## Read the two sides of the UTF-8 table | [0:49:00](https://www.youtube.com/watch?v=5aVtm-oPRzQ&t=2940s)

Look at the first row of the [RFC 3629 page used in class](https://www.rfc-editor.org/info/rfc3629/). On the left, `0000 0000-0000 007F` tells you **which character numbers** the row covers. On the right, `0xxxxxxx` tells you **how one of those numbers is written in UTF-8**. The left is in hexadecimal (base 16); the right shows individual binary digits. The hyphen separates two inclusive endpoints, not two terms to subtract. The space *within* each endpoint groups digits of the same number.

| Character number (hex), as displayed | UTF-8 octet sequence (binary), as displayed |
|---|---|
| `0000 0000-0000 007F` | `0xxxxxxx` |
| `0000 0080-0000 07FF` | `110xxxxx 10xxxxxx` |
| `0000 0800-0000 FFFF` | `1110xxxx 10xxxxxx 10xxxxxx` |
| `0001 0000-0010 FFFF` | `11110xxx 10xxxxxx 10xxxxxx 10xxxxxx` |

The directions visible below the table give it a reading order: choose the one applicable row, set its fixed high-order bits, and fill the `x` positions with bits of the character number, starting with the rightmost bit in the last octet. An **octet** is exactly eight bits. The table shows the shapes; it is not by itself a complete validity test. For example, RFC 3629 excludes the surrogate code points U+D800 through U+DFFF from UTF-8 even though those numbers fall inside the third printed interval.

At [0:50:00](https://www.youtube.com/watch?v=5aVtm-oPRzQ&t=3000s) the browser shows the RFC's formal byte-sequence rules across a page break. The instructor describes copying the material and removing the intervening page furniture. At 0:50:40 the table and the rules are together in a Sublime Text scratch tab, not in the neighboring `utf8.py` tab. The rules offer another way to read the table: `UTF8-1 = %x00-7F` permits a single byte from hex `00` to `7F`; the two-byte rule requires a lead byte and then a `UTF8-tail`. They are rules describing valid bytes, not Python to run. The RFC itself says the authoritative definition is in Unicode, not in its ABNF grammar.

**Key items to scan**

- **`Char. number range`** — find the input number between the two hex endpoints.
- **`UTF-8 octet sequence`** — read each space-separated group on the right as one eight-bit output unit.
- **`x`** — fill this position with a bit of the character number; a printed `0` or `1` is fixed.
- **`UTF8-1`** — the grammar's single-octet alternative, `%x00-7F`.
- **`UTF8-tail`** — a continuation byte allowed after a lead byte; the grammar gives its range as `%x80-BF`.

### Why hex fits binary | [0:51:11](https://www.youtube.com/watch?v=5aVtm-oPRzQ&t=3071s)

A student observes that hex is used often. The instructor asks *why it is convenient here*. Start at the right-hand end of a binary number. One place to the left doubles the weight: `1 → 2 → 4 → 8 → 16`. The instructor initially skips eight, then restores it. Starting at the right of a hex number, the weights are `1 → 16 → 256`. It takes **four** moves left in binary to do what **one** move left does in hex.

That makes conversion a local operation: replace each hex digit with its four binary digits, keeping the order. Four bits have 16 different settings, not four possibilities. The symbols `0` through `9` supply the first ten hex digits; `A` through `F` supply the six remaining values, 10 through 15. Leading zeros within each four-bit group keep the next digit in the correct position.

**Key items to scan**

- **Place weight** — the rightmost place weighs one in either base (`2⁰ = 16⁰ = 1`); the next weighs two in binary but sixteen in hex.
- **`8, 4, 2, 1`** — weights from left to right *inside* one group of four bits.
- **Four bits ↔ one hex digit** — `0000` through `1111` give the same sixteen choices as `0` through `F`.

## A symbol, its number, and its appearance | [0:54:19](https://www.youtube.com/watch?v=5aVtm-oPRzQ&t=3259s)

Asked what Unicode looks like, a student recalls color changes on Minecraft servers and describes apparently random symbols. The instructor opens [unicode.org](https://www.unicode.org/). At 0:56:00 the page places a kiss-face emoji above `U+1F618` and a cross above `U+271E`. The assignments aren't random to a reader of the standard: a **code point** is a number, and these assigned numbers identify particular characters. `U+` introduces a hexadecimal code point; writing the same number in decimal or binary doesn't change which character it identifies. UTF-8 answers a different question: *which bytes represent that character?*

A colored emoji is not the same thing as a color value. The class contrasts a colored shape in the character repertoire with specifying a color using **RGB** or **HSL**. An application can attach its own formatting meaning to text; the numbered symbol alone is not a universal command to recolor subsequent text. A `unicode colors` search leads to a [ResearchGate figure page](https://www.researchgate.net/figure/Sample-mapping-of-Char-symbol-digit-UNICODE-and-Color_fig1_50418049) at 0:58:00. Its visible “Context 2” mentions a mapping table used to decrypt a message, not a Unicode-wide color rule. The instructor leaves the specific color question open; no Minecraft formatting scheme is demonstrated.

**Key items to scan**

- **Code point** — the number associated with a character; `U+1F618` identifies the pictured kiss-face emoji.
- **`U+271E`** — the cross tile supplies a second, shorter hexadecimal example.
- **RGB / HSL** — ways of specifying color, unlike naming a Unicode character that happens to appear colored.

## Work the cross one hex digit at a time | [0:58:17](https://www.youtube.com/watch?v=5aVtm-oPRzQ&t=3497s)

The first three digits of `271E` are straightforward. The last digit exposes a useful arithmetic slip. The middle column below shows the subtraction test: starting at eight, put a `1` wherever the weight fits, subtract that weight, then try the next one. These are **guide-checked results**, not a corrected transcription of the editor.

| Hex digit | Weights that fit | Four-bit result |
|---|---|---|
| `2` | Only the two's place is needed. | `0010` |
| `7` | Eight is too large; take four, then two, then one. | `0111` |
| `1` | No eight, four or two; take one. | `0001` |
| `E` = 14 | Take eight: **six remain**. Take four: **two remain**. Take two: zero remain. | `1110` |

The class correctly identifies `E` as 14, but the spoken subtraction leaves four after taking eight. The scratchpad consequently shows **`0010 0111 0001 1100`**, not the correct `0010 0111 0001 1110`. Check the symptom by reading back the final group: `1100` is eight plus four, or 12, which is hex `C`. The displayed bits spell `271C`, a different code point. No repair is visible in the later scratch states in this interval.

The grouping demonstration still works. The same *displayed* bits occur as `00100111 00011100` and `0010011100011100`. A four-bit group is a **nibble**, half an octet: four nibbles, two octets' worth of bits, or sixteen written bits describe the same width. Spaces help human eyes; they aren't extra data bits. Nor are these two groups of eight already the UTF-8 encoding of the cross. They are a way to *write its number* (and in this particular scratch line, the wrong number).

**Key items to scan**

- **`E = 14 = 1110`** — include the two's place after eight and four.
- **`1100`** — exactly what was written; it means 12, so it cannot be the final nibble of `271E`.
- **Nibble** — four bits, one hex digit. Two nibbles form one octet.
- **`00100111 00011100`** — regrouping the observed bits doesn't change their value or fix an incorrect bit.

## A five-digit code point | [1:01:42](https://www.youtube.com/watch?v=5aVtm-oPRzQ&t=3702s)

The emoji's number **`U+1F618`** needs a fifth hex place. Read its digits left to right: `1 → 0001`, `F → 1111`, `6 → 0110`, `1 → 0001`, `8 → 1000`. A student suggests four remains after taking eight from fifteen; the class checks and gets **seven**, which still needs four, two, and one. Hence every bit is set in `F`. By contrast, six uses four and two but not one, and eight uses just the highest place.

| Ways to read the same number (observed, 1:05:00) | What the spaces show |
|---|---|
| `0001 1111 0110 0001 1000` | Five hex digits, each expanded into a nibble. |
| `0001 11110110 00011000` | Group from the right in eights: two full octets' worth of bits and a nibble left over at the high end. |
| `00011111011000011000` | All twenty written positions in one string. The first three zeros add width but no value. |

Read the high end first, but line up octet boundaries from the low end. Only 17 bits are significant here: the emoji does not fit in 16 bits, even though the previous number fit on a 16-bit line. A wider written field (the RFC even prints 32-bit-wide endpoints) is not a promise that 32 data bits are required, nor that a character has a fractional-byte UTF-8 encoding. The instructor compares the spaces here with colons used to visually separate parts of some network addresses. Such separators belong to the written notation, not to the underlying bits.

**Key items to scan**

- **`1F618`** — maps to `0001 1111 0110 0001 1000` digit for digit.
- **`F = 1111`** — `15 − 8 = 7`, so all three lower weights are needed.
- **20 written / 17 significant** — dropping only the three leading zeros changes the field width, not the number.
- **Most significant** — leftmost/highest-weight positions; the extra half-group is on this end when the low-order bits are paired into octets.

## Why UTF-8 keeps ASCII intact | [1:05:36](https://www.youtube.com/watch?v=5aVtm-oPRzQ&t=3936s)

Return to `0000 0000-0000 007F`. Asked how many bits are represented, it is tempting to count eight visible digits in one endpoint and answer “eight.” But those are **hex digits**: a group of four can display 16 bits; two such groups can display 32. That is the table's writing convention. To find the largest *value* in its first row, ignore the leading zero digits and evaluate `7F` (`f` and `F` denote the same hex digit).

| Step | Manual calculation | Result or reason |
|---|---|---|
| Expand the last four hex digits | `007F → 0000 0000 0111 1111` | One nibble per hex digit. |
| Drop the leading zero octet | `0111 1111` | Eight positions remain; the 128's bit is off. |
| Add the set places | `64 + 32 + 16 + 8 + 4 + 2 + 1` | The maximum is **127**, not 128. |
| Check against the next number | `1000 0000` | Exactly 128. The previous pattern is one less. |

The scratchpad eventually pairs the two patterns with `127` and `128` (1:10:00). **ASCII**, the American Standard Code for Information Interchange, is a seven-bit code: 128 values **including zero**, numbered 0 through 127. The instructor names ANSI, the American National Standards Institute, and distinguishes standard ASCII from what is often called “extended ASCII.” Eight-bit extensions are not the definition of standard ASCII.

A global character repertoire needs more than 128 values, but much existing text and software already uses ASCII. Unicode preserves the ASCII assignments at its beginning; UTF-8 also preserves their byte values. Its first-row template **`0xxxxxxx`** has one fixed leading zero and seven bits copied from the number. It is the *octet representation* that has the extra leading zero; the ASCII number itself has only seven available bit positions. When reading at the start of a UTF-8 character, a leading zero means this character ends in that very octet.

**Key items to scan**

- **`007F`** — hex endpoint, decimal 127; leading zeros and letter case don't change it.
- **`0111 1111` vs. `1000 0000`** — the first is the maximum seven-bit value; the second is the next value, 128.
- **ASCII** — 128 seven-bit codes, not an eight-bit set of 256 codes.
- **`0xxxxxxx`** — one complete UTF-8 byte, with the seven ASCII bits unchanged.

### Where the next range begins and ends | [1:12:14](https://www.youtube.com/watch?v=5aVtm-oPRzQ&t=4334s)

The instructor asks for a *relative* interpretation of `80` hex. The useful answer is **one more than `7F`**: the second row picks up exactly where ASCII ran out. For the high end, the scratchpad shows lowercase `07ff`, then `0000 0111 1111 1111` (1:14:20). The last eight ones already allow 256 different low-octet patterns, but there are **three more** usable positions to their left: 256, 512 and 1024. The next unused place is 2048. Eleven ones therefore mean **2047**, one less than 2048.

Keep the count distinct from the endpoint. Eleven bits can represent 2048 numbers from 0 through 2047. The first 128 of them already belong to the one-octet form. Only 128 through 2047 belong to this second row: 1920 values. The question at 1:14:55 is now precise: *how do those eleven character bits fit in an octet sequence?* The table has the spaces ready: five `x` positions in the first byte, six in the second.

**Key items to scan**

- **`0080`** — hex for 128; the second row starts immediately after `007F`.
- **`07ff`** — the editor's spelling of the upper bound; eleven set bits give decimal 2047.
- **`110xxxxx 10xxxxxx`** — two octets with eleven free positions; the printed prefix bits aren't part of the character number.
- **2048 vs. 1920** — all values possible with eleven bits vs. values in this row after removing the 128 single-octet cases.

*Verification note:* The guide's corrected conversions, bit lengths, counts and decimal endpoints were checked separately in Python. The classroom evidence here is the RFC page, Unicode tiles, captions and manual editor arithmetic—not program output.

## How eleven bits travel in two bytes (1:15:00–1:18:50)

At **1:15:45**, the class has a number that can need eleven bits. An eight-bit byte cannot hold them all. But sending two unrestricted bytes would leave the receiving machine with a question: is this one character or two? The instructor asks what is “11-ish” about the two-byte row of the UTF-8 table. The useful answer is **eleven `x` positions**, not “eleven bits are on.” An `x` can hold either a zero or a one. The other positions have fixed values that tell the reader how to interpret the bytes.

**Observed table, explained:** read **`110xxxxx 10xxxxxx`** from left to right. Put the five most significant bits of the character number after `110` in the first octet (eight-bit byte); put the six least significant bits **after** `10` in the second. The visible mask resolves an ambiguity in the spoken word order. At 1:16:25 the selection isolates the six variable positions; at 1:17:00 it isolates the two fixed bits. Only the eleven variable positions carry the number.

**Guide-worked boundary example, independently checked with Python's UTF-8 codec; not a classroom run.** Take **`U+0080`**, decimal 128, the first value that does not fit the one-byte row. Put the same number in eleven payload positions, split it, and insert the prefixes:

| Step | Actual bits | What the step does |
|---|---|---|
| Write 128 in eleven positions | `00010 000000` | The space separates five high bits from six low bits; it is not transmitted. |
| Form the first byte | `110` + `00010` = `11000010` = `C2` | The fixed prefix announces a candidate two-byte sequence. |
| Form the second byte | `10` + `000000` = `10000000` = `80` | The second byte is a continuation, not a new character. |
| Read the two bytes back | `C2 80` → `00010` + `000000` → `U+0080` | Check the allowed lead and tail; discard their prefixes; join the data, high part first. |

**The wire question (1:17:06).** Imagine receiving the octets in order and inspecting each from its high bit downward. At a character boundary, a leading `0` identifies a complete one-byte character once its remaining seven bits arrive: its value is 0–127, and the next byte begins a new character. Seeing only `1` is not enough to know a length. Even `11` is shared by several lead shapes; `110` distinguishes the two-byte shape. A byte starting `10` is a continuation and cannot begin a valid character. This is a way to read a byte stream, not a requirement about which bit a particular physical network sends first. Nor does recognizing a lead shape prove the remaining bytes are valid.

**Key items to scan**

- **`x`** — a position that carries a zero or one from the character number, not a literal one.
- **`0xxxxxxx`** — one octet, seven data bits, values 0–127.
- **`110xxxxx 10xxxxxx`** — five high bits and six low bits; together they carry eleven bits.
- **`U+0080` → `C2 80`** — `80` is the character number in the first notation and the second byte in the second notation; the entire encoding is two bytes.
- **`10xxxxxx`** — a continuation byte; the `10` comes before the six data bits.

## Reading three and four octets in order (1:18:50–1:24:03)

**`U+07FF`** is 2047; add one and the hexadecimal number becomes **`U+0800`**, decimal 2048. That carry is why the next row starts at `0800`, not `0080`. The upper endpoint in the third row is **`FFFF`**: four hexadecimal digits, four bits per digit, sixteen ones, decimal 65,535. The class corrects the count aloud to **65,536 possibilities for a whole sixteen-bit field**, from zero through 65,535. That is not the number of legal three-byte characters: the row starts above zero and surrogate code points are excluded. The scratchpad briefly contains part of the range rather than just the upper endpoint (1:20:20); by 1:28:00 it shows `0000 FFFF` and its full binary expansion. Neither state is transmitted data.

**Observed RFC table in the editor; notes and bit counts added by the guide.** The left column is a *character number in hex*. The right column is the layout of the *octets sent*. Do not read the digits on the left as the bytes on the right.

| Number to represent | Octets in transmission order | Payload positions |
|---|---|---|
| `U+0000`–`U+007F` | `0xxxxxxx` | 7 |
| `U+0080`–`U+07FF` | `110xxxxx 10xxxxxx` | 5 + 6 = 11 |
| `U+0800`–`U+FFFF`, excluding `U+D800`–`U+DFFF` | `1110xxxx 10xxxxxx 10xxxxxx` | 4 + 6 + 6 = 16 |
| `U+10000`–`U+10FFFF` | `11110xxx 10xxxxxx 10xxxxxx 10xxxxxx` | 3 + 6 + 6 + 6 = 21 |

At **1:21:31**, the class reasons from the receiving end again. With `111` as the first three bits, the one- and two-byte forms are out, but both longer forms are still possible. A fourth bit of zero gives **`1110`**: take the low four bits of this octet, then the low six bits of each of the next two. Joining them in that order recovers the original sixteen-bit field. The instructor suggests shifts and bitwise OR: shift the high group left to make room, then place the next group in the newly empty low positions. The fixed prefixes are not part of the recovered number.

At **1:22:38**, the instructor leaves working out the fourth range's endpoints to the class but does explain how to read its bytes: **`11110`** leaves three data bits in its lead, followed by three groups of six. Use the shortest form belonging to the number's range. **Guide verification:** `U+07FF` encodes as `DF BF`; its immediate successor `U+0800` encodes as `E0 A0 80`. Likewise `U+FFFF` is `EF BF BF`, and `U+10000` requires `F0 90 80 80`. The four-byte mask has twenty-one slots; the largest permitted value is `U+10FFFF`, not the largest arbitrary twenty-one-bit integer. [RFC 3629 §3, UTF-8 definition](https://www.rfc-editor.org/rfc/rfc3629#section-3).

**Key items to scan**

- **`07FF` → `0800`** — from 2047 to 2048: one more in value, one more byte in the encoding.
- **`FFFF`** — four hex `F`s represent sixteen ones; the value is 65,535, not 65,536.
- **`1110xxxx`** — three-byte lead shape; join **4, 6, 6** data bits in order.
- **`11110xxx`** — four-byte lead shape; join **3, 6, 6, 6** data bits in order.
- **Shortest form** — each encodable character number belongs to one row; a longer spelling is not another valid choice.

## Reading the RFC as a grammar (1:24:03–1:31:39)

At **1:24:11**, the instructor returns to the RFC's *Augmented Backus–Naur Form* (ABNF). The table answered “Where do I put each bit?” The grammar answers “Which byte sequences are legal?” Start with three rules rather than trying to parse the whole page at once:

| Observed RFC rule | How to read it — guide-added notes |
|---|---|
| `UTF8-octets = *( UTF8-char )` | A UTF-8 string is zero or more encoded characters in order. The empty byte string qualifies. The instructor connects this prefix repetition operator with the idea of a **Kleene star**. |
| `UTF8-char = UTF8-1 / UTF8-2 / UTF8-3 / UTF8-4` | Choose one alternative for a single encoded character. This grammar uses `/` for “or”; other notations may use a vertical bar. It does not mean divide. |
| `UTF8-1 = %x00-7F` | One byte whose hex value is anywhere from `00` through `7F`, inclusive. `%x` introduces a hexadecimal value in ABNF; the instructor contrasts the familiar programming prefix `0x`. |

**At 1:26:46–1:27:43**, the instructor treats **`UTF8-tail`** as a named rule rather than literal text to send: look it up, even though the plain-text RFC does not italicize it. Read **`UTF8-2 = %xC2-DF UTF8-tail`** as two things *in sequence*: one byte in `C2`–`DF`, then one instance of `UTF8-tail`, defined as **`%x80-BF`**. The two ranges are **not** alternative spellings for the same byte. The instructor moves these two definitions together in the scratchpad to work on one dependency at a time.

**At 1:28:02–1:31:19**, turn each hex digit into a four-bit group (a **nibble**) to see how byte ranges and bit patterns relate. `C` is 12 (`1100`); `D` is 13 (`1101`); `F` is `1111`. A tab did not lay out the scratch columns as intended, so the instructor says they will use spaces. These are worksheet columns, not characters in UTF-8 data.

| Role | Hex endpoints | Binary endpoints (guide-verified) | What stays fixed |
|---|---|---|---|
| Two-byte lead | `C2` through `DF` | `1100 0010` through `1101 1111` | Every allowed lead starts `110`; not every byte starting `110` is allowed. |
| Continuation | `80` through `BF` | `1000 0000` through `1011 1111` | Every byte in this entire interval starts `10`; its other six bits vary. |

At 1:30:00 the screen shows only `1011` beneath `BF`; the complete `1011 1111` above is a verified conversion, not a claim that the complete worksheet entry was captured. Conversely, at 1:31:00 the selected first four positions of the *mask* read `110x`. The discussion supplies `0` for that `x` to make the nibble `1100`, hence `C`. It could also be `1`, giving `1101`, hence `D`.

**Why the exact RFC ranges matter — guide clarification, not an additional class exercise.** If the two-byte lead rule said merely `110xxxxx`, it would admit `C0` and `C1`. Those leads with ordinary tails would spell numbers below 128 using two bytes. For example, discarding the prefixes of `C0 80` leaves eleven zeros, but `U+0000` has the one-byte spelling `00`; `C0 80` is invalid. `C2 80` is the first legal pair because its payload reaches 128. The three- and four-byte branches in the displayed grammar make analogous checks that a shape-only test would miss:

| Lead(s), hex | Second byte, hex | Remaining bytes | What the rule prevents or allows |
|---|---|---|---|
| `E0` | `A0`–`BF` | one tail | Excludes three-byte spellings of values below `U+0800`. |
| `E1`–`EC` or `EE`–`EF` | `80`–`BF` | one tail | Both trailing bytes use the ordinary tail range. |
| `ED` | `80`–`9F` | one tail | Excludes `U+D800`–`U+DFFF`, the UTF-16 surrogate code points, which are not Unicode scalar values. |
| `F0` | `90`–`BF` | two tails | Excludes four-byte spellings of values below `U+10000`. |
| `F1`–`F3` | `80`–`BF` | two tails | All three following bytes are ordinary tails. |
| `F4` | `80`–`8F` | two tails | Caps the result at `U+10FFFF`; higher lead bytes are not legal four-byte starters. |

A **Unicode scalar value** is a code point in `U+0000`–`U+10FFFF` other than a surrogate. Recognizing the prefix gives a *candidate length*; validating the whole sequence means checking the lead value, the number of remaining bytes, and their allowed ranges. In the RFC's shorthand, `2( UTF8-tail )` means **exactly two** tails, unlike `*( UTF8-char )`, which may repeat zero or more times. By 1:31:37 the instructor is ready to carry this picture back to the implementation. [RFC 3629 §4, exact grammar and Unicode authority note](https://www.rfc-editor.org/rfc/rfc3629#section-4); [RFC 5234 §3, ABNF operators](https://www.rfc-editor.org/rfc/rfc5234#section-3).

**Key items to scan**

- **`*( UTF8-char )`** — zero or more complete encoded characters; `*` is prefix repetition here.
- **`/`** — pick one alternative; adjacent rule elements instead mean “then.”
- **`%x00-7F`** — ABNF hexadecimal range, both endpoints included; `0x` is the programming-notation comparison, not the RFC's spelling.
- **`%xC2-DF UTF8-tail`** — a permitted two-byte starter followed by exactly one permitted continuation.
- **`%x80-BF`** — the reusable tail range; `80` and `BF` are the lowest and highest bytes with prefix `10`.
- **`2( UTF8-tail )`** — exactly two ordinary tails, not an arbitrary repetition.
- **`E0`, `ED`, `F0`, `F4`** — even a well-shaped `10` second byte must meet a narrower range after these leads.
- **Scalar value** — excludes the surrogate interval; a prefix alone cannot establish one.

## From the byte patterns to Python: what an encoder returns (1:31:40–1:35:38)

The instructor comes back from the RFC to **`utf8.py`** and considers looking at the decoder first, then chooses to send text to **`encode(text)`**. Start with the **`H`** in the opening app: its number is 72, so it already fits in one byte. The **`é`** in **`Café`** is one character but its number is 233, so it takes two bytes. **`ord(char)`** gives the Unicode code point, not a position in an ASCII-only table; the distinction matters as soon as the letter is not ASCII. The six Kivy imports above the function serve the app; the conversion itself uses ordinary Python integers, a list and bytes. (Source: encoder at 1:31:50 and 1:32:25; range highlighted at 1:34:00.)

Annotated teaching version — guide-added comments. The four branches below are all visible; the instructor works the two-byte one by hand. The three- and four-byte comments use code points already written on the lesson scratchpad, not new classroom runs.

```python
def encode(text):  # One call converts a whole string; it does not print it.
    encodedOctets = []  # Create a fresh list for this call, not for each character.
    for char in text:  # Visit the characters in text order, including spaces.
        codePoint = ord(char)  # Get the Unicode number: H → 0x48, é → 0xE9.
        if codePoint <= 0x7F:  # Through 127 inclusive, all data fits below the leading zero.
            encodedOctets.append(codePoint)  # Add one integer; H contributes 0x48.
        elif codePoint <= 0x7FF:  # The earlier test failed: 0x80 through 0x7FF need two bytes.
            b1 = 0xC0 | (codePoint >> 6)  # Move the upper five data bits down and set the 110 prefix.
            b2 = 0x80 | (codePoint & 0x3F)  # Keep six low bits and set the 10 prefix.
            encodedOctets.extend([b1, b2])  # Add two integers in order, not a nested two-item list.
        elif codePoint <= 0xFFFF:  # Up to 0xFFFF, this branch uses three bytes.
            b1 = 0xE0 | (codePoint >> 12)  # For U+271E, 0x02 with the 1110 prefix gives E2.
            b2 = 0x80 | ((codePoint >> 6) & 0x3F)  # Middle six bits: 0x1C with the 10 prefix gives 9C.
            b3 = 0x80 | (codePoint & 0x3F)  # Low six bits: 0x1E with the 10 prefix gives 9E.
            encodedOctets.extend([b1, b2, b3])  # Append E2, 9C, 9E for this example.
        elif codePoint <= 0x10FFFF:  # The remaining range through U+10FFFF takes four bytes.
            b1 = 0xF0 | (codePoint >> 18)  # For U+1F618, the top group is 0; the lead byte is F0.
            b2 = 0x80 | ((codePoint >> 12) & 0x3F)  # Next group 0x1F: the second byte is 9F.
            b3 = 0x80 | ((codePoint >> 6) & 0x3F)  # Next group 0x18: the third byte is 98.
            b4 = 0x80 | (codePoint & 0x3F)  # Last group 0x18: the fourth byte is also 98.
            encodedOctets.extend([b1, b2, b3, b4])  # Append all four; previously encoded characters stay in place.
    return bytes(encodedOctets)  # After the loop, return an immutable byte sequence.
```

Read the comparisons in order: **`<=`** includes each endpoint, and reaching an **`elif`** means every smaller case has already failed. That is why the two-byte case starts at 128 without writing a second comparison. Moving a number six places right leaves the next group in the low positions; masking with **`0x3F`** then discards everything except those six positions. The lead byte has a different fixed prefix for each length. All following bytes begin with **`10`**. Nothing in the function displays the bytes or changes the input text.

**Independent check, not a classroom run.** The following driver supplied the default text from editor line 85 to the recovered encoder without starting Kivy. It reproduced the opening GUI’s 23 bytes, including the last **`80`**; the driver prints numbers and hex, whereas the app sets a label.

```python
text = "Hello, 🌍! Café 🚀"           # 16 code points; editor default, confirmed by the observed bytes.
data = encode(text)                 # Return 23 bytes; leave text unchanged.
print(len(text), len(data))         # Verification print, not a line in the class encoder.
print(data.hex(" ").upper())        # Make each returned byte visible as two hex digits.
```

Independently verified display:

```text
16 23
48 65 6C 6C 6F 2C 20 F0 9F 8C 8D 21 20 43 61 66 C3 A9 20 F0 9F 9A 80
```

| What determines the width? | Independently verified result (hex) | What to notice |
|---|---|---|
| U+007F → U+0080 | `7F` → `C2 80` | One more in the input requires a second output byte. |
| U+07FF → U+0800 | `DF BF` → `E0 A0 80` | The last 11-bit value and the first value requiring three bytes. |
| U+271E | `E2 9C 9E` | Uses the named code point, not a mistyped binary copy on the scratchpad. |
| U+D7FF; U+E000 | `ED 9F BF`; `EE 80 80` | The two valid scalar values immediately outside the surrogate interval. |
| U+FFFF → U+10000 | `EF BF BF` → `F0 90 80 80` | Crossing this boundary needs a fourth byte. |
| U+1F618; U+10FFFF | `F0 9F 98 98`; `F4 8F BF BF` | A lesson example and the upper endpoint. |

A limit of this implementation: the **`<= 0xFFFF`** branch also admits surrogate code points. In a separate check, giving it a Python string containing U+D800 returned **`ED A0 80`**; Python's strict UTF-8 decoder rejected those bytes. [RFC 3629 §3](https://www.rfc-editor.org/rfc/rfc3629#section-3) explicitly excludes U+D800–U+DFFF. Correct bytes on the lesson fixture do not make the function a complete validator.

Two smaller guide fixtures isolate what the GUI is formatting. For **`Aé`**, the encoder returned **`41 C3 A9`**; the GUI’s format expressions gave **`0x41 0xC3 0xA9`** or **`01000001 11000011 10101001`**. Python’s standard UTF-8 decoder returned **`Aé`** from those bytes. Adding **`€`** and **`😀`** exercises all four branches in one call: **`Aé€😀`** gave **`0x41 0xC3 0xA9 0xE2 0x82 0xAC 0xF0 0x9F 0x98 0x80`**. The encoder returns bytes; the spaces and **`0x`** prefixes are added only for display.

**Key items to scan**

- **`utf8.py`**, **`encode(text)`** — classroom file and text-to-bytes function; no `print` inside it.
- **`ord(char)`**, **`H`**, **`é`**, **`Café`** — Unicode numbers 72 and 233 for the two example letters; character count is not byte count.
- **`encodedOctets`** — one fresh list per input string; encoding the next character adds to the same list.
- **`<=`**, **`elif`** — test inclusive upper limits `0x7F`, `0x7FF`, `0xFFFF`, `0x10FFFF` in ascending order.
- **`append`** adds one integer; **`extend`** adds each integer from a two-, three- or four-byte group in order.
- **`>>`** moves higher bits into position; **`0x3F`** keeps the low six bits after a shift or directly from the original.
- **`bytes(encodedOctets)`** — returns the accumulated immutable bytes; the GUI and the separate verification prints are different ways to display them.
- **U+D800–U+DFFF** — forbidden UTF-8 inputs that this encoder's range checks do not exclude.
- **`Aé`**, **`€`**, **`😀`** — the small fixtures show that a new character appends to the same list: three bytes for `Aé`, ten for `Aé€😀`.

## The hand calculation: make two bytes from one unchanged number (1:35:06–1:47:16)

The two-byte rule, **`110xxxxx 10xxxxxx`**, leaves five data positions in the lead byte and six in the continuation byte. The instructor chooses an 11-bit pattern rather than a particular glyph. Grouping into two columns of eight and adding a missing leading zero makes the input easier to check: **`00000101 00101010`**. The leftmost five zeros are display padding. The eleven significant bits split as **`10100 | 101010`**. In hex the starting number is **`0x052A`** (1322 decimal). The two output bytes are not the two halves of that padded input.

First move the upper five bits down. In **`codePoint >> 6`**, six low bits fall off the right of the *result*; **`codePoint`** itself has not been reassigned. Align the shifted result with **`0xC0`** by their right edges. Bitwise OR sets a position if either input has a one, so **`11000000`** supplies the prefix without disturbing the five data positions. In the handwritten row the instructor briefly uses a bit from the wrong row, then notices the lower five positions of the prefix are all zeros. At 1:40:03 the typed result is **`11010110`**; at 1:40:50 it is **`11010100`**. The change corrects the worksheet, not the Python encoder.

For the tail, start *again* from the original 1322, not the shifted 20 or the first byte. In **`0x80 | (codePoint & 0x3F)`**, the parentheses isolate the six data bits first. A zero in an AND mask clears that position unconditionally; a one preserves what was there, including a zero. At 1:43:15 the handwritten AND result is **`00101011`**. The instructor catches the last-column slip: zero AND one gives zero, not one. At 1:44:00 the row reads **`00101010`**; that is the payload before the final OR, not the finished byte. With the six low bits retained, OR with **`0x80`** sets the leading one, while the next bit stays zero. (1:41:00–1:46:05.)

Annotated teaching version — guide-added comments. These expressions isolate the exact lesson input; the prints were run separately, not seen in the editor.

```python
codePoint = 0x052A                      # Binary 00000101 00101010; unchanged throughout.
b1 = 0xC0 | (codePoint >> 6)            # 11000000 OR 00010100 → 11010100.
b2 = 0x80 | (codePoint & 0x3F)          # Keep 00101010; add 10000000 → 10101010.
encodedOctets = []                      # Start this separate one-character fixture empty.
encodedOctets.extend([b1, b2])          # Append 212, then 170, producing [212, 170].
print(bytes(encodedOctets).hex(" ").upper())  # Display D4 AA, not the original 05 2A.
```

| Step | Current input or operation | Result | Why these columns matter |
|---|---|---|---|
| 1 | `00000101 00101010 >> 6` | `00010100` = 20 | The lower six fall away; retain `10100` for the lead. |
| 2 | `11000000` OR `00010100` | `11010100` = `D4` | The set prefix bits come from C0; the other ones come from the shifted value. |
| 3 | original `00000101 00101010` AND `00000000 00111111` | `00101010` = 42 | Align at the least significant end; only the last six positions can survive. |
| 4 | `10000000` OR `00101010` | `10101010` = `AA` | Add the `10` continuation prefix without changing those six bits. |
| 5 | write the lead, then the continuation | `11010100 10101010` | Strip `110` and `10` and the surviving groups read `10100 101010` again. |

The final two rows of the editor place the bytes directly above the pattern (1:46:55–1:47:00). The learner can now check positions rather than trust the appearance of a long string of ones and zeros: the lead is in the allowed **`C2`**–**`DF`** range, the tail in **`80`**–**`BF`**, and the eleven recovered bits match the starting number. The independent Python result was **`D4 AA`**; the classroom evidence is typed binary scratch, not a terminal run or a character printed by Python.

**Key items to scan**

- **`110xxxxx 10xxxxxx`** — reserve three lead prefix bits and two continuation prefix bits; the `x` positions carry the number.
- **`00000101 00101010`**, **`0x052A`**, **`10100 | 101010`** — one input shown in padded binary, hex and its five-plus-six split; the vertical bar here marks a visual division.
- **`codePoint >> 6`** — yields 20 and does not change **`codePoint`**; zeros can pad that positive result on the left.
- **`0xC0`**, **`11010100`** — the lead marker ORed with 20 gives `D4`, the corrected first byte.
- **`0x80 | (codePoint & 0x3F)`** — keep the low six (42), then OR with **`0x80`** for the second byte, `AA`.
- **Bitwise AND / OR** — use `&` to pass or clear each input bit, `|` to set prefix bits; do not substitute Python's Boolean `and`/`or`.

## Reading the bytes back: decoding is not validation

At 1:32:11 the instructor considers starting with the decoder, then works the encoder instead. The opening editor scroll (0:02:56–0:03:12) shows the reverse path. In **`Café`**, **`C3 A9`** occupies two byte positions but supplies one **`é`**. Similarly, to reverse the hand calculation, keep **`10100`** from **`D4`** and **`101010`** from **`AA`**; shifting the first group left six positions makes room for the second. Together they recover **`0x052A`**.

**Annotated teaching version — guide-added comments.** This executable excerpt is restricted to one- and two-byte candidates and unrecognized leads. Do not pass three- or four-byte leads to it: the source handles them in distinct branches shown immediately below. **`decode`** returns text; it does not display it.

```python
def decode(octetStream):                 # A sequence of byte values, not a string of hex digits.
    chars = []                           # New output list for this call.
    i = 0                                # Start at byte index zero.
    while i < len(octetStream):           # Stop after the last consumed byte.
        b = octetStream[i]                # Inspect the first byte at the current position.
        if (b & 0x80) == 0:               # Leading bit 0: e.g. 0x41 already gives the number for A.
            codePoint = b                # For 0x41 the code point is already 65.
            i += 1                       # One byte consumed.
        elif (b & 0xE0) == 0xC0:          # Leading bits 110 select the two-byte path.
            if i + 1 >= len(octetStream): # No second byte available?
                break                    # Stop; keep characters completed earlier.
            b2 = octetStream[i+1]         # For é this is 0xA9.
            codePoint = ((b & 0x1F) << 6) | (b2 & 0x3F)  # Combine the high five and low six data bits.
            i += 2                       # Move past both bytes.
        # Historical three- and four-byte branches omitted here, not replaced by guesses.
        else:
            i += 1                       # Unrecognized lead, e.g. a stray 0x80: skip it.
            continue                     # Do not append a character on this pass.
        chars.append(chr(codePoint))      # e.g. chr(0xE9) appends é.
    return "".join(chars)                 # Return text; displaying it is a separate action.
```

| Step within the screen's 23-byte stream (zero-based indices) | What happens to `C3 A9` |
|---|---|
| At index 16 | `C3 & E0` gives `C0` (hex): there must be another byte for this branch. Index 17 exists. |
| Separate the parts | `C3 & 1F` gives `03`; shifting left six gives `C0`. `A9 & 3F` gives `29`. |
| Combine | `C0` bitwise-OR `29` gives `E9`. Converting that number to a character produces `é`. |
| Next pass | Advance to index 18, the space before the rocket. Neither `C3` nor `A9` is processed again as a separate character. |

For longer sequences, the visible code checks that two or three more bytes exist before indexing them. Here are the relevant complete lines as two **partial teaching fragments**; the numbered listing preserves the visible characters of each bit-assembly expression.

```python
# Inside the three-byte branch selected by (b & 0xF0) == 0xE0:
if i + 2 >= len(octetStream):  # Missing final byte? Avoid reading outside the sequence.
    break                      # Keep the characters completed so far.
b2 = octetStream[i+1]          # Read the middle byte.
b3 = octetStream[i+2]          # Read the final byte. The formula starts by shifting lead data 12 bits.
# Line 50 shifts lead data by 12 and masked middle data by 6, then starts masking b3.
i += 3                         # The next pass starts after all three bytes.
```

```python
# Inside the four-byte branch selected by (b & 0xF8) == 0xF0:
if i + 3 >= len(octetStream):  # Missing any required byte? Stop before indexing it.
    break                      # Keep the characters completed so far.
b2 = octetStream[i+1]          # Read the first following byte.
b3 = octetStream[i+2]          # Read the second.
b4 = octetStream[i+3]          # Read the third. The formula starts by shifting lead data 18 bits.
# Line 58 shifts lead data by 18 and masked second-byte data by 12; its third term is clipped.
i += 4                         # The next pass starts after this entire character.
```

**Independent test — not classroom output.** The restricted decoder excerpt above was run on these small fixtures; none enters an omitted branch. The comparator was Python's strict UTF-8 decoder. In the classroom the button instead passes freshly *encoded text* into the decoder: that round trip does not test how it handles unrelated or malformed bytes.

| Hex bytes supplied | Restricted source-path return | Strict UTF-8 result | Why the outcomes agree or differ |
|---|---|---|---|
| `41 C3 A9` | `'Aé'` | `'Aé'` | `41` gives `A`; the two-byte sequence gives `é`. |
| `C3 41` | `'Á'` | `UnicodeDecodeError` | The second byte is not a continuation, but masking it with `3F` still extracts a number. |
| `41 80 42` | `'AB'` | `UnicodeDecodeError` | `80` cannot start a character; the classroom path skips it between `A` and `B`. |
| `41 C3` | `'A'` | `UnicodeDecodeError` | The final lead needs another byte: `break` preserves the prefix instead of reporting the truncation. |
| `C0 80` | `'\x00'` (one NUL) | `UnicodeDecodeError` | Two zero-valued payloads combine to zero, but this is not a legal encoding of that number. |
| `41 D4 AA` | `A` followed by U+052A | same text | Advance one byte past `41`, two past `D4 AA`; index 3 ends this three-byte input. |
| `41 C2` | `'A'` | `UnicodeDecodeError` | As with a final `C3`, a lone `C2` stops the loop before appending another character. |
| `C2 41` | U+0081 | `UnicodeDecodeError` | Masking the ASCII `41` as though it were a continuation gives low bits `01`. |

**Supplemental standard.** [RFC 3629 §3](https://www.rfc-editor.org/rfc/rfc3629#section-3) says a decoder **MUST** protect against decoding invalid sequences; [RFC 2119 §1](https://www.rfc-editor.org/rfc/rfc2119#section-1) makes that an absolute requirement. In [RFC 3629 §4](https://www.rfc-editor.org/rfc/rfc3629#section-4), only **`C2`**–**`DF`** may lead a two-byte sequence, and every continuation byte must be in **`80`**–**`BF`**. The source checks how many bytes remain but not whether these two ranges hold. A successful round trip of freshly encoded text does not test these cases.

For valid longer inputs, §3 describes filling the code point from the payload groups. The three-byte groups occupy bit positions starting at 12, 6 and 0; the four-byte groups start at 18, 12, 6 and 0. The following are **supplemental, independently tested expressions**, not transcriptions of the clipped lines. Each group comes from the low data bits of one byte:

```python
b, b2, b3 = bytes.fromhex("E2 9C 9E")              # The three bytes encoded for U+271E.
cp3 = ((b & 0x0F) << 12) | ((b2 & 0x3F) << 6) | (b3 & 0x3F)  # Recover 0x271E.
b, b2, b3, b4 = bytes.fromhex("F0 9F 98 98")       # A new group: the four bytes for U+1F618.
cp4 = (((b & 0x07) << 18) | ((b2 & 0x3F) << 12)   # Position the first two groups.
       | ((b3 & 0x3F) << 6) | (b4 & 0x3F))         # Add the last two; recover 0x1F618.
```

A separate teaching decoder using these completions returned the original **`Hello, 🌍! Café 🚀`** from the 23 observed bytes. That verifies the construction, not the unreadable source suffixes or a late GUI run.

**Key items to scan**

- **`encode`**, **`decode`** — opposite directions here: text to bytes, then bytes to returned text. The app's own round trip is not a validity test for arbitrary bytes.
- **`i`**, **`octetStream[i]`** — the index advances by bytes consumed, not characters produced.
- **`(b & 0xE0) == 0xC0`** — recognize the leading `110` pattern; alone it also admits forbidden leads such as `C0`.
- **`((b & 0x1F) << 6) | (b2 & 0x3F)`** — combine the data parts of `C3 A9` to obtain `E9`, the number for `é`.
- **`break`** — stop on a truncated candidate and return completed text. **`continue`** — skip an unrecognized lead without appending.
- **`chr(codePoint)`**, **`"".join(chars)`** — turn one recovered number into a character, then return the accumulated characters as a single string.
- **`UnicodeDecodeError`** — the strict comparator rejects malformed sequences even where the screen's readable paths return plausible-looking text.
- **MUST** — the standard requires protection against decoding invalid sequences; a length check alone is insufficient.

## Why practice doing this by hand? (1:47:21–1:49:33)

A participant remarks that it looks like a lot of ones and zeros. The instructor's answer is practical: once the columns, mask and operation are fixed, the calculation becomes methodical. The class could revisit **Base64** with this new understanding, but does not do so in this closing discussion. The instructor also mentions **microcontroller registers**, including possible Arduino hardware work, as another place to use bitwise operations. No Arduino, register program or new Base64 demo is shown. The invitation to try the calculation at home is followed by the class sign-off; the last capture shows recording controls, not another program result.

**Key items to scan**

- **Base64** — a proposed return to an earlier bit-grouping example, not a second walkthrough here.
- **Microcontroller registers** — the instructor's prospective hardware application of setting or isolating selected bits; no hardware demonstration followed.
- **Try it at home** — take a two-byte code point, predict both groups, then check both the fixed prefixes and the recovered input bits.

## Syntax to remember

| Pattern | How to read it here |
|---|---|
| `identifier identifier-continue` | In the C production, extend one already formed name by one permitted continuation. Do not insert a space into the actual name. |
| `%xC2-DF UTF8-tail` | In ABNF, match one lead in that inclusive hex range **then** one legal continuation byte. |
| `*( UTF8-char )` | Repeat a whole encoded character zero or more times; this is not one or more arbitrary bytes. |
| `codePoint >> 6` | Divide the nonnegative number into groups: drop the low six bits to obtain the high part for the two-byte lead. |
| `codePoint & 0x3F` | Retain exactly the six least significant positions. For the worksheet input the retained value is 42. |
| `0x80 \| (codePoint & 0x3F)` | Set the continuation prefix without disturbing those low six bits: the example yields `AA`. |
| `ord(char)` / `chr(codePoint)` | Move between a one-character Python string and its number; neither operation by itself produces a UTF-8 byte stream. |
| `bytes(encodedOctets)` | Return the accumulated octet values as a bytes object. Hex pairs, binary strings and the Kivy label are separate display choices. |

## Knowledge check

1. A student writes an identifier starting with the digit `7` and argues that digits appear in *identifier-continue*. What step of the grammar did the student skip? How does the same recursive pattern show up at the scale of a C translation unit?
2. A software requirement changes from **SHOULD** to **SHALL**. Under RFC 2119, is this just a change of tone? What if **MUST** is replaced by **SHALL** instead?
3. The class wrote the cross’s number as `271E` in hexadecimal. A classmate wants to transmit the two bytes `27 1E` and call them UTF-8. What has the classmate represented correctly, and what step is missing?
4. Why is `007F` the end of the one-octet range rather than `0080`? Predict the UTF-8 bytes on either side of that boundary. Which of these numbers is a count, and which is a largest value?
5. In the final scratch example, the upper group is `10100` and the lower group is `101010`. Restore the two UTF-8 octets. Could you substitute the lower six bits for the shift in the first-octet expression?
6. A reader sees an octet beginning with `10` at the position where a new character should start. What can the reader conclude? Is counting leading bits in each first octet enough to establish that a whole byte string is valid?

### Answer key

1. The base case supplies a legal start *before* any continuation can extend it. A digit may extend an already formed identifier; it cannot establish the first one on this path. At the larger scale, an external declaration can form a translation unit; adding another external declaration to an existing translation unit produces a larger one. Recursion extends a structure that can already exist.
2. **SHOULD** allows a justified exception after weighing its implications. **SHALL** is an absolute requirement in this vocabulary, synonymous with **MUST**. Changing SHOULD to SHALL changes what an implementation may omit; swapping MUST and SHALL does not. A project needs to agree on its vocabulary rather than rely on how forceful a word sounds. [RFC 2119, §§1, 3](https://www.rfc-editor.org/rfc/rfc2119#section-1).
3. `27 1E` does spell the number `271E` as two big-endian octets, but the RFC table calls for three UTF-8 octets at this code point. Write `0010 011100 011110` in groups of four, six and six payload bits. Insert the groups into `1110xxxx 10xxxxxx 10xxxxxx`: the result is `11100010 10011100 10011110`, or `E2 9C 9E`. Grouping the number for human reading did not encode it.
4. `0xxxxxxx` leaves seven places for the value: 128 possibilities, numbered 0 through 127 inclusive. `U+007F` fits and gives `7F`. `U+0080` needs two octets. The value groups are `00010` and `000000`; after prefixing them the bytes are `11000010 10000000`, or `C2 80`.
5. Attach `110` to `10100`, then `10` to `101010`: `11010100 10101010`, or `D4 AA`. The shift moves the **upper** five bits into position; the mask keeps the **lower** six. Using the lower group twice would drop information and could overwrite a prefix bit. The scratch result is visible near 1:46:55; the separate Python test reproduced it.
6. `10` marks a continuation, not a start. A valid decoder needs context: a preceding legal lead must call for that continuation, and enough valid continuation bytes must follow. Some leads restrict the *next* byte more tightly than `80`–`BF`, so width alone cannot prove validity. [RFC 3629, §§3–4](https://www.rfc-editor.org/rfc/rfc3629#section-4).

## Library of definitions

| Term | Meaning in this lesson |
|---|---|
| Specification; standard | A specification describes required behavior. Formal standardization adds adoption through an established standards process; a useful specification is not automatically a formal standard. |
| Requirements specification | An agreement about what software is expected to do. Its wording helps a developer and a customer test whether they asked for and built the same thing. |
| Grammar; production | A grammar describes allowable structure. A production gives a way to form one named structure from smaller pieces. |
| Terminal; nonterminal | A terminal is a literal symbol or token at the level described. A nonterminal names a category that another rule can expand. Typography is a clue, not a universal convention. |
| Base case; recursive alternative | One rule can finish without referring back to itself; another builds a larger structure from an instance already formed. Compare an identifier start with the addition of another continuation. |
| Syntax; semantics | Syntax asks whether the parts fit an allowed arrangement. Semantics asks what that arrangement means; being well formed does not guarantee that a program does what was intended. |
| BNF; ABNF | Backus–Naur Form and Augmented BNF describe grammars. In the RFC notation a slash separates alternatives, a leading asterisk allows repetition, and `%x` introduces hexadecimal numeric values. |
| Identifier; translation unit | An identifier is a name formed according to the language’s rules. A C translation unit is a top-level unit presented for translation after preprocessing, not necessarily an entire application. |
| Code point; scalar value | A code point is a numbered position in Unicode. A scalar value is a code point other than a surrogate (`U+D800`–`U+DFFF`); UTF-8 encodes scalar values. |
| Nibble; octet | A nibble is four bits, exactly one hexadecimal digit. An octet is eight bits, exactly two hex digits. Leading zeros can make this grouping visible without changing the value. |
| UTF-8; ASCII | UTF-8 represents a scalar value in one to four octets. ASCII is a seven-bit code; UTF-8 uses the same one-octet values for its entire range. |
| Prefix; payload; continuation | A prefix marks the octet’s role. Payload bits carry pieces of the original number. A continuation begins with `10` and carries six payload bits. |
| Shift; mask; bitwise OR | A right shift brings higher bits down. An AND mask keeps selected positions and clears others. OR combines the prepared group with fixed prefix bits. |

## Historical appendix · Numbered screen code, grammar and scratch work

These listings preserve what was readable. A line number before source text is a reference, not a character to enter. The editor, the RFC, and the C draft use different numbering; each listing identifies its source.

### `utf8.py`: from input text to bytes and back to the window

Assembled visual reconstruction of the editor buffer titled `C:\com\joesta\Numerical\src\utf8.py`. Line numbers are the editor gutter, **not Python to type**. These overlapping views show a scroll through the same named buffer; they do not establish a saved revision, a new edit, or a particular launch command. All frame times here are requested seeks. The missing-suffix markers are editorial, not executable code.

**Imports and encoder (0:03:05–0:03:25; revisited at 1:31:50–1:32:25).** The six imports provide the Kivy widgets and application base. The full encoder is legible at `t00205.png` and `t05545.png`; `t05510.png` confirms the return and blank line before the decoder. The highlighted occurrences of `encodedOctets` are editor selections. Indentation reproduces the visible columns, not the underlying tab characters.

```text
  1 | from kivy.app import App
  2 | from kivy.uix.boxlayout import BoxLayout
  3 | from kivy.uix.textinput import TextInput
  4 | from kivy.uix.button import Button
  5 | from kivy.uix.label import Label
  6 | from kivy.uix.scrollview import ScrollView
  7 |
  8 | def encode(text):
  9 |     encodedOctets = []
 10 |     for char in text:
 11 |         codePoint = ord(char)
 12 |         if codePoint <= 0x7F:
 13 |             encodedOctets.append(codePoint)
 14 |         elif codePoint <= 0x7FF:
 15 |             b1 = 0xC0 | (codePoint >> 6)
 16 |             b2 = 0x80 | (codePoint & 0x3F)
 17 |             encodedOctets.extend([b1, b2])
 18 |         elif codePoint <= 0xFFFF:
 19 |             b1 = 0xE0 | (codePoint >> 12)
 20 |             b2 = 0x80 | ((codePoint >> 6) & 0x3F)
 21 |             b3 = 0x80 | (codePoint & 0x3F)
 22 |             encodedOctets.extend([b1, b2, b3])
 23 |         elif codePoint <= 0x10FFFF:
 24 |             b1 = 0xF0 | (codePoint >> 18)
 25 |             b2 = 0x80 | ((codePoint >> 12) & 0x3F)
 26 |             b3 = 0x80 | ((codePoint >> 6) & 0x3F)
 27 |             b4 = 0x80 | (codePoint & 0x3F)
 28 |             encodedOctets.extend([b1, b2, b3, b4])
 29 |     return bytes(encodedOctets)
 30 |
```

Lines 1–6 load UI components. Lines 8–13 set up the conversion and accept the one-byte case. Lines 14–28 build larger groups in transmission order: form a prefixed lead, form continuation bytes, then extend the existing list. Line 29 returns bytes after all characters, not after the first pass. There is no invocation or print beneath that return in this view.

**Decoder (0:02:56–0:03:12).** The late view (`t05520.png`, 1:32:00) confirms only the opening lines. `t00177.png` supplies lines 31–49 and most lines through 59; `t00183.png` supplies 46–64, including longer prefixes for 50 and 58 previously obscured by the webcam. Their ends still run beyond the editor viewport. `t00176.png`, `t00184.png` and `t00192.png` corroborate the opening; `t00182.png` corroborates the exit. Lines 36–44 handle one- and two-byte candidates; 45–59 handle longer candidates; 60–64 show what is skipped and what is returned. The length guards protect indexing, not sequence validity.

```text
 31 | def decode(octetStream):
 32 |     chars = []
 33 |     i = 0
 34 |     while i < len(octetStream):
 35 |         b = octetStream[i]
 36 |         if (b & 0x80) == 0:
 37 |             codePoint = b
 38 |             i += 1
 39 |         elif (b & 0xE0) == 0xC0:
 40 |             if i + 1 >= len(octetStream):
 41 |                 break
 42 |             b2 = octetStream[i+1]
 43 |             codePoint = ((b & 0x1F) << 6) | (b2 & 0x3F)
 44 |             i += 2
 45 |         elif (b & 0xF0) == 0xE0:
 46 |             if i + 2 >= len(octetStream):
 47 |                 break
 48 |             b2 = octetStream[i+1]
 49 |             b3 = octetStream[i+2]
 50 |             codePoint = ((b & 0x0F) << 12) | ((b2 & 0x3F) << 6) | (b3 &[remainder not visible]
 51 |             i += 3
 52 |         elif (b & 0xF8) == 0xF0:
 53 |             if i + 3 >= len(octetStream):
 54 |                 break
 55 |             b2 = octetStream[i+1]
 56 |             b3 = octetStream[i+2]
 57 |             b4 = octetStream[i+3]
 58 |             codePoint = ((b & 0x07) << 18) | ((b2 & 0x3F) << 12) | ((b3[remainder not visible]
 59 |             i += 4
 60 |         else:
 61 |             i += 1
 62 |             continue
 63 |         chars.append(chr(codePoint))
 64 |     return "".join(chars)
 65 |
```

**Kivy construction (0:02:58–0:03:03).** Sources: `t00182.png` lines 66–89, `t00178.png` lines 78–105, `t00181.png` lines 94–122. Fractional frames `t0017865cs.png` (0:02:58.65) and `t0018065cs.png` (0:03:00.65) extend line 116 past the webcam and recover the continuation of the class after line 122. Neither shows the end of 116. Line 68 is clipped at the right edge. The input literal on line 85 is also corroborated by the displayed hex bytes at 0:02:24; the GUI alone does not render the emoji clearly.

```text
 66 | class Utf8(App):
 67 |     def build(self):
 68 |         rootLayout = BoxLayout(orientation='vertical', padding=15, spac[remainder not visible]
 69 |
 70 |         rootLayout.add_widget(Label(
 71 |             text="[b]UTF-8 Encoding & Decoding[/b]",
 72 |             markup=True,
 73 |             font_size=20,
 74 |             size_hint_y=None,
 75 |             height=40
 76 |         ))
 77 |
 78 |         rootLayout.add_widget(Label(
 79 |             text="Enter text (try emojis, accents, or symbols):",
 80 |             size_hint_y=None,
 81 |             height=25
 82 |         ))
 83 |
 84 |         self.messageText = TextInput(
 85 |             text="Hello, 🌍! Café 🚀",
 86 |             multiline=False,
 87 |             size_hint_y=None,
 88 |             height=45
 89 |         )
 90 |         self.messageText.bind(text=self.messageChange)
 91 |         rootLayout.add_widget(self.messageText)
 92 |
 93 |         self.statisticLabel = Label(
 94 |             text="",
 95 |             markup=True,
 96 |             size_hint_y=None,
 97 |             height=30
 98 |         )
 99 |         rootLayout.add_widget(self.statisticLabel)
100 |
101 |         processButton = Button(
102 |             text="Process Full UTF-8 Breakdown",
103 |             size_hint_y=None,
104 |             height=50,
105 |             background_color=(0.1, 0.5, 0.8, 1)
106 |         )
107 |         processButton.bind(on_press=self.process)
108 |         rootLayout.add_widget(processButton)
109 |
110 |         self.resultLabel = Label(
111 |             text="Results will appear here...",
112 |             halign='left',
113 |             valign='top',
114 |             markup=True
115 |         )
116 |         self.resultLabel.bind(size=self.resultLabel.setter('text_size')[remainder not visible]
117 |
118 |         scroll = ScrollView(size_hint=(1, 1))
119 |         scroll.add_widget(self.resultLabel)
120 |         rootLayout.add_widget(scroll)
121 |
122 |         self.updateStats(self.messageText.text)
123 |
124 |         return rootLayout
125 |
```

**Callbacks and entry point (0:02:58.65–0:03:00.65).** The two fractional frames expose 124–133; `t00179.png` supplies 135–150 and 153–154. The base minute frame `5aVtm-oPRzQ_v2/t00180.png` shows lines 136–154 with a longer legible error-message prefix on line 151 than `t00179.png`; it still does not expose the closing markup or end of the assignment. Lines 133 and 151 below are not completed from likely Kivy syntax. The automatic callback updates counts, whereas the button callback formats four sections in one result; the visible GUI result is a normal result, not this exception path.

```text
126 |     def messageChange(self, instance, value):
127 |         self.updateStats(value)
128 |
129 |     def updateStats(self, text):
130 |         charCount = len(text)
131 |         encoded = encode(text)
132 |         octetCount = len(encoded)
133 |         self.statisticLabel.text = f"[i]Characters:[/i] [b]{charCount}[[remainder not visible]
134 |
135 |     def process(self, instance):
136 |         text = self.messageText.text
137 |         try:
138 |             encoded = encode(text)
139 |             hexDisplay = " ".join([f"0x{b:02X}" for b in encoded])
140 |             binaryDisplay = " ".join([f"{b:08b}" for b in encoded])
141 |             decoded = decode(encoded)
142 |
143 |             result = (
144 |                 f"[b]Original Text:[/b]\n{text}\n\n"
145 |                 f"[b]Hexadecimal Byte Stream:[/b]\n{hexDisplay}\n\n"
146 |                 f"[b]Binary Byte Stream:[/b]\n{binaryDisplay}\n\n"
147 |                 f"[b]Decoded Result:[/b]\n{decoded}"
148 |             )
149 |             self.resultLabel.text = result
150 |         except Exception as e:
151 |             self.resultLabel.text = f"[color=ff0000]Error: {str(e)}[/co[remainder not visible]
152 |
153 | if __name__ == '__main__':
154 |     Utf8().run()
```

The literal `[` immediately before the editorial marker on line 133 is visible. `\n` in lines 144–147 denotes a Python newline escape, not two printed characters. The parenthesized strings are adjacent; their text is combined before the label is changed. On direct execution the guard at 153–154 creates the application object and calls its `run` method; the frames do not show an invocation to transcribe.

### N3998 Annex A: tokens and keywords (0:14:00; 0:18:22)

**Observed PDF notation, not C to compile.** `t00840.png`, then `t01102.png` for the entire unobscured keyword grid. Printed page 499 (viewer 516 of 793); the PDF outline identifies Annex A as *informative*. The following transcriptions use spaces to represent indentation and columns; ordinary code-block type does not reproduce the source's italic nonterminals or bold literals. Section numbers are the document's, not invented line numbers.

```text
A.1   Notation
The notation is described in 6.1.
A.2   Lexical grammar
A.2.1 Lexical elements
(6.4.1) token:
    keyword
    identifier
    constant
    string-literal
    punctuator

(6.4.1) preprocessing-token:
    header-name
    identifier
    pp-number
    character-literal
    string-literal
    punctuator
    each universal character name that cannot be one of the above
    each non-white-space character that cannot be one of the above
```

```text
A.2.2 Keywords
(6.4.2) keyword: one of
alignas   do      int           struct        while
alignof   double  long          switch        _Atomic
auto      else    nullptr       thread_local  _BitInt
bool      enum    register      true          _Complex
break     extern  restrict      typedef       _Countof
case      false   return        typeof        _Decimal128
char      float   short         typeof_unqual _Decimal32
const     for     signed        union         _Decimal64
constexpr goto    sizeof        unsigned      _Generic
continue  if      static        void          _Noreturn
default   inline  static_assert volatile
```

The token rule offers alternatives; *keyword* is just one of the five. The keyword grid supplies literal alternatives for that category, not a required sequence of words. Its spelling is tied to the working draft visible on the cover at 0:13:00, not retroactively to C23.

### N3998: the recursive identifier rule and its terminal characters (0:18:22–0:23:35)

**Observed PDF notation.** `t01102.png` and `t01140.png` show Annex A.2.3. The two lines under `identifier:` are distinct alternatives; the space between category names in the second is notation for a sequence, not a space in the C name.

```text
A.2.3 Identifiers
(6.4.3.1) identifier:
    identifier-start
    identifier identifier-continue

(6.4.3.1) identifier-start:
    nondigit
    XID_Start character
    universal character name of class XID_Start

(6.4.3.1) identifier-continue:
    digit
    nondigit
    XID_Continue character
    universal character name of class XID_Continue
```

**Separate location after PDF Find.** At 0:23:35 (`t01415.png`), the viewer is on printed page 54 (71 of 793), at 140%, with `XID_Start character` in the find field and `1 of 3 matches`. In addition to the start/continue alternatives, this page shows the actual characters and a constraint:

```text
nondigit: one of
    _ a b c d e f g h i j k l m
      n o p q r s t u v w x y z
      A B C D E F G H I J K L M
      N O P Q R S T U V W X Y Z

digit: one of
    0 1 2 3 4 5 6 7 8 9

Constraints
An identifier shall conform to Normalization Form C as specified in ISO/IEC 10646.
```

The following *Semantics* paragraph discusses an implementation-defined character and the XID_Start property; portions are obscured, so it is not supplied as a continuous quotation. Unlike a literal `a` in the nondigit table, the phrase `XID_Start character` names a class whose meaning the reader must look up.

### C working draft: the grammar as displayed

**Historical screen record.** These are printed productions, not program source, editor edits, commands or output. The visible URL was [open-std.org/JTC1/SC22/WG14/www/docs/n3998.pdf](https://open-std.org/JTC1/SC22/WG14/www/docs/n3998.pdf), and the page header identifies *ISO/IEC 9899:202y (en) — N3998 working draft*. The PDF has section numbers, not editor line numbers. Left-hand line numbers below are editorial; do not type them. Indentation distinguishes alternatives under a rule. In the PDF, italic text denotes grammar categories and bold text denotes literal tokens; the character-property descriptions are in roman type. In the plain-text listings, `[opt]` represents the draft's subscript *opt* (optional), not literal square brackets. Spaces separate symbols in a production; they do not prescribe spaces between characters in an identifier.

**Later checks of the earlier keyword and identifier listings.** At 0:34:00 the A.2.2 keyword table again shows `_Countof`; at 0:35:50 and 0:36:00 the A.2.3 summary spans a page boundary. These are the same productions transcribed above. At 0:24:00 the body text also discusses case sensitivity, reservation of some names, normalization and the implementation-defined status of the dollar sign. Selection and search highlights do not change a production.

#### 2. A.2.4: spelling a universal character name

At 0:37:00 (`t02220.png`) the subsequent section was visible even though the class was verbally moving on to the other end of Annex A. The backslashes, lowercase and uppercase letters, and braces below are part of the printed alternatives; *hex-quad* and *simple-hexadecimal-digit-sequence* are category names. This is notation shown in the C draft, not the **`U+HHHH`** convention printed in the UTF-8 RFC.

```text
01  (6.4.4) universal-character-name:
02      \u hex-quad
03      \U hex-quad hex-quad
04      \u{ simple-hexadecimal-digit-sequence }
05      \U{ simple-hexadecimal-digit-sequence }
06  (6.4.4) hex-quad:
07      hexadecimal-digit hexadecimal-digit hexadecimal-digit hexadecimal-digit
08  (6.4.4) simple-hexadecimal-digit-sequence:
09      hexadecimal-digit
10      simple-hexadecimal-digit-sequence hexadecimal-digit
```

Line 7 fixes the width of a quad. Lines 9–10 instead give a base choice of one hexadecimal digit and permit appending another. Thus lines 4–5 use delimiters around a variable-length sequence. The notation is not evidence that every number one could spell is an allowable identifier character.

#### 3. A.4: the preprocessor has grammar, too

At 0:38:00 (`t02280.png`; printed p. 513) the page shows these directive rules while the instructor explains preprocessing. The two initial alternatives have their heading off-screen. The last portion of the long macro rule is behind the webcam. The visibly short label **`has-c-attribute-express:`** is retained, not normalized to another word.

```text
01      __has_include ( header-name )
02      __has_include ( header-name-tokens )
03  has-embed-expression:
04      __has_embed ( header-name embed-parameter-sequence[opt] )
05      __has_embed ( header-name-tokens pp-balanced-token-sequence[opt] )
06  has-c-attribute-express:
07      __has_c_attribute ( pp-tokens )
08  (6.10.3) include-directive:
09      # include pp-tokens new-line
10  (6.10.4.1) embed-directive:
11      # embed pp-tokens new-line
12  (6.10.4.1) embed-parameter-sequence:
13      pp-parameter
14      embed-parameter-sequence pp-parameter
15  (6.10.5.1) define-directive:
16      # define identifier replacement-list new-line
17      # define identifier lparen macro-parameter-list ) replacement-list new-[remainder not visible]
18  (6.10.5.1) undef-directive:
19      # undef identifier new-line
```

The words *pp-tokens* stand for preprocessing tokens, not the literal letters one would place after a directive. Compare lines 12–14 with the identifier rule: one parameter forms a sequence; an existing sequence can take another. Lines 15–19 distinguish a named replacement from undefining a name. No macro expansion or output was demonstrated here.

#### 4. A.3.4: a translation unit is a nonempty sequence

The page that follows in the instructor's navigation is *earlier* in the PDF: printed p. 511. At 0:39:00 (`t02340.png`) a selection and annotation control obscure part of the recursive alternative. The same line is independently legible without the control at 0:39:40 (`t02380.png`), the source for the listing below. At 0:40:00 (`t02400.png`) part of the second alternative is selected; at 0:41:00 (`t02460.png`) the selection is gone. Those are selection states of a reference page, not edits. The camera inset starts at the right edge of *function-body* on line 8; no text beyond that boundary is asserted.

```text
01  (6.9.1) translation-unit:
02      external-declaration
03      translation-unit external-declaration
04  (6.9.1) external-declaration:
05      function-definition
06      declaration
07  (6.9.2) function-definition:
08      attribute-specifier-sequence[opt] declaration-specifiers declarator function-body[remainder not visible]
09  (6.9.2) function-body:
10      compound-statement
```

Line 2 gets the construction started; line 3 appends exactly one more external declaration. Following lines 4–6 explains what is allowed in either position. Lines 7–10 then narrow just the function-definition branch; the function body is a compound statement, not another top-level external declaration.

**Adjacent screen-only syntax.** Directly above A.3.4 at 0:39:00 (`t02340.png`), the end of a production (heading outside the crop) and the jump-statement alternatives make the contrast with top-level items concrete. They are preserved as this draft displays them; the instructor did not run or teach each loop and jump in this interval.

```text
01      while ( expression ) secondary-block
02      do secondary-block while ( expression ) ;
03      for ( expression[opt] ; expression[opt] ; expression[opt] ) secondary-block
04      for ( declaration expression[opt] ; expression[opt] ) secondary-block
05  (6.8.7.1) jump-statement:
06      goto identifier ;
07      continue identifier[opt] ;
08      break identifier[opt] ;
09      return expression[opt] ;
```

Notice the role of the punctuation. Line 4's declaration occupies the first part of the loop header; the printed production does not place an additional literal semicolon immediately after the word *declaration*. Line 9 permits a return statement with or without an expression in the syntax; that alone does not determine which form is appropriate for a particular function.

### Historical scratch: RFC table, code-point digits and ASCII boundaries

These are numbered **Sublime Text scratchpad states**, not runnable code or terminal output. The gutter numbers and the following `│` are not part of the text. The active tab is identified by its first line (`Char. number range … UTF-8 octet sequence`); `utf8.py` is a different, inactive tab. The frames establish the visible text, not a saved filename, continuous typing history or execution. Times refer to requested frame targets. The automatic caption track has short uncovered intervals, including about 0:57:43–0:57:57 during the color detour and 1:07:36–1:07:48 during the first-row conversion. Those gaps establish neither speech nor silence. The listings below report discrete sampled states, not inferred keystrokes during the gaps.

#### 1. Reference pasted above the calculations | 0:50:40, `t03040.png`

The first block assigns a byte pattern to each range of character numbers. The block starting at line 10 expresses byte-sequence validity as grammar rules. In the RFC browser view at 0:50:00 (`t03000.png`), the page footer/header splits the grammar after `UTF8-2`; those page labels are not in the editor version. Line 1 starts farther left than the data rows, as displayed.

```text
 1│Char. number range  |        UTF-8 octet sequence
 2│      (hexadecimal)    |              (binary)
 3│   --------------------+---------------------------------------------
 4│   0000 0000-0000 007F | 0xxxxxxx
 5│   0000 0080-0000 07FF | 110xxxxx 10xxxxxx
 6│   0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx
 7│   0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
 8│
 9│
10│   UTF8-octets = *( UTF8-char )
11│   UTF8-char   = UTF8-1 / UTF8-2 / UTF8-3 / UTF8-4
12│   UTF8-1      = %x00-7F
13│   UTF8-2      = %xC2-DF UTF8-tail
14│   UTF8-3      = %xE0 %xA0-BF UTF8-tail / %xE1-EC 2( UTF8-tail ) /
15│                 %xED %x80-9F UTF8-tail / %xEE-EF 2( UTF8-tail )
16│   UTF8-4      = %xF0 %x90-BF 2( UTF8-tail ) / %xF1-F3 3( UTF8-tail ) /
17│                 %xF4 %x80-8F 2( UTF8-tail )
18│   UTF8-tail   = %x80-BF
19│
```

Read lines 4–7 from the left column to the right. Lines 10–11 describe a sequence of encoded characters and the alternatives for one character. Lines 12–18 restrict individual bytes; for instance, line 13 requires a lead byte from `C2` to `DF` followed by one byte matching line 18. The constraints in the grammar are more specific than merely counting octets in the picture. This material is already visible here; its presence does not establish that every alternative was explained at this point.

#### 2. The cross: a correct label above incorrect bits | 0:58:35–1:02:00

At 0:58:35 (`t03515.png`) the caret is on blank line 21; at 0:59:00 (`t03540.png`) line 21 reads `U+271E` and line 22 is blank. At 1:00:00 (`t03600.png`) line 22 is mid-entry:

```text
21│U+271E
22│0010 0111 0001
```

**Observed at 1:00:30, `t03630.png`:** line 24 uses two groups of eight; line 26 at this instant still repeats the groups of four.

```text
21│U+271E
22│0010 0111 0001 1100
23│
24│00100111 00011100
25│
26│0010 0111 0001 1100
27│
```

**Changed line at 1:01:00, `t03660.png`:** line 22 is selected and lines 22 and 24 retain their prior text. The third version now has no spaces:

```text
26│0010011100011100
```

By 1:02:00 (`t03720.png`) line 26 is blank. These displays all retained `1100` for the last nibble. The checked conversion of the *label* ends in `1110`; that repair is **not** in the historical listings. No screenshot here shows UTF-8 bytes for the cross.

#### 3. The emoji: five nibbles regrouped | 1:02:00–1:05:00

At 1:02:00 (`t03720.png`) the new example consists of `U+1F618` on line 28 with line 29 blank. At 1:03:00 (`t03780.png`) the first three nibbles are present:

```text
28│U+1F618
29│0001 1111 0110
```

**Observed at 1:03:50, `t03830.png`:** lines 29 and 30 are complete, and line 31 is blank. **At 1:04:00, `t03840.png`,** line 31 has a second copy of the nibble grouping; the following block is that later frame only:

```text
28│U+1F618
29│0001 1111 0110 0001 1000
30│0001 11110110 00011000
31│0001 1111 0110 0001 1000
32│
```

**Changed line at 1:05:00, `t03900.png`:**

```text
31│00011111011000011000
```

The three completed lines now represent the same number with different separators. On line 30 the left group is only four bits; the other two groups are eight bits apiece. Nothing on the screen encodes the emoji by emitting two and a half bytes.

#### 4. First-row endpoint, next value, and second-row endpoint | 1:08:00–1:14:20

At 1:08:00 (`t04080.png`), lines 34–36 introduce `007F`; line 36 has no decimal annotation yet. At 1:08:10 and 1:09:00 (`t04090.png`, `t04140.png`), line 37 is present but blank:

```text
34│007F
35│0000 0000 0111 1111
36│0111 1111
```

By 1:10:00 (`t04200.png`) the last two lines directly compare adjacent numbers:

```text
36│0111 1111  127
37│1000 0000  128
```

At 1:11:00 (`t04260.png`) the selected `0xxxxxxx` is back on line 4, **not** a newly calculated result below line 37. At 1:13:00 (`t04380.png`) the selected `7F` is the end of that first row. The next row begins at `0080` and ends at `07FF`. At 1:14:00 (`t04440.png`) its upper endpoint is being expanded; note the lowercase `ff` on line 39:

```text
39│07ff
40│0000 0111 11
```

**Longest observed lower pane, 1:14:20, `t04460.png`, lines 19–40.** This is one later frame with the completed line 40. It is not a composite with the earlier nonblank version of line 26 or the earlier grouped version of line 31.

```text
19│
20│
21│U+271E
22│0010 0111 0001 1100
23│
24│00100111 00011100
25│
26│
27│
28│U+1F618
29│0001 1111 0110 0001 1000
30│0001 11110110 00011000
31│00011111011000011000
32│
33│
34│007F
35│0000 0000 0111 1111
36│0111 1111  127
37│1000 0000  128
38│
39│07ff
40│0000 0111 1111 1111
```

Lines 34–37 compare 127 with 128; lines 39–40 express a different upper bound, 2047. The latter decimal result is supported by the discussion around 1:14:40 and by independent arithmetic; it is **not** printed next to line 40 in this frame. The caret and mouse pointer overlap the third nibble; they are interface marks, not added data characters. No terminal run accompanies any of these listings.

### Historical worksheet: the UTF-8 range table and RFC scratchpad

**Source:** Sublime Text, plain-text buffer whose title begins `Char. number range` and includes `UTF-8 octet sequence`; an RFC 3629 browser page is visible behind it at `www.rfc-editor.org/info/rfc3629/`. These are observed worksheet states, not a Python source file or program output. Numbers before `│` reproduce editor gutter numbers; do not type them. Spacing represents visible alignment, not a determination of tab versus space for each column. The times are requested frame targets. Separate observations do not establish every intervening keystroke, save, or execution.

At 1:15:00 (`t04500.png`) lines 21–40 match the preceding completed scratch listing, including the uncorrected `271C` bit pattern beneath the `U+271E` label. At 1:16:00 (`t04560.png`) the earlier table and RFC grammar (lines 1–18) are again visible in full. The changed selections and later edits are recorded below.

The selection moves through the same table: both bytes of line 5 at 1:16:00, the six `x`s in the tail at 1:16:25, and the fixed `10` at 1:17:00. At 1:19:00, `0800` is selected; at 1:20:00 it is the longer substring `0800-0000 FFFF`, **not** just the number `FFFF`. At 1:22:00 the third `1` in `1110` is selected; at 1:23:00 the four `1`s in `11110` are selected. The class reasons about these shapes without showing a decoder run.

**1. Alternate endpoint drafts — 1:20:20 (`t04820.png`), then 1:21:00 (`t04860.png`), then 1:28:00 (`t05280.png`).** These are **three separate views** of the same line numbers. First the selected range fragment and a partial expansion are present. Then only the upper endpoint remains, with the last group of ones still being written. A later frame confirms the completed expansion. The partially typed forms are not alternative encodings.

```text
41 │0800-0000 FFFF
42 │0000 1000 0000
```

```text
41 │0000 FFFF
42 │0000 0000 0000 0000 1111 1111 1111 11
```

```text
41 │0000 FFFF
42 │0000 0000 0000 0000 1111 1111 1111 1111
```

**2. A transient ABNF edit — 1:26:00 (`t05160.png`), compared with 1:27:00 (`t05220.png`).** The earlier 1:25:00 frame still has the percentage sign. The 1:26:00 caret is immediately before the `x`; the `%` is absent in that editor state. At 1:27:00 `%` is again present and line 18 is selected. The instructor's distinction was between `0x` in other contexts and the RFC's `%x`; the incomplete editor line is not a third valid spelling of the ABNF.

```text
12 │   UTF8-1      = x00-7F
```

```text
12 │   UTF8-1      = %x00-7F
```

**3. Two rules copied together, followed by four endpoint columns — 1:28:00 to 1:30:00.** This is a manual comparison of *lead range* and *tail range*. The following is the longest visible lower block at 1:30:00 (`t05400.png`); lines 34–40 match the preceding 1:15:00 state. The blank beneath the missing half of `BF` is not an unreadable crop: the caret is immediately after `1011` (line 49, column 39).

```text
41 │0000 FFFF
42 │0000 0000 0000 0000 1111 1111 1111 1111
43 │
44 │
45 │   UTF8-2      = %xC2-DF UTF8-tail
46 │   UTF8-tail   = %x80-BF
47 │
48 │C2          DF         80         BF
49 │1100 0010   1101 1111  1000 0000  1011
```

**Distinct earlier states of lines 48–49, not back-filled from 1:30:00:**

- **1:28:00 (`t05280.png`):** lines 45–46 already contain both rules; line 48 is blank, and there is no line 49 shown.
- **1:28:25 (`t05305.png`):** first nibble only, caret on line 49, column 6:

```text
48 │C2
49 │1100
```

- **1:29:00 (`t05340.png`):** the second heading is present, but the binary beneath it has not appeared; the caret is in its column:

```text
48 │C2          DF
49 │1100 0010
```

At **1:30:45 (`t05445.png`)** the top table is back, with the first `1` of line 5 selected. At **1:31:00 (`t05460.png`)** the selection spans `110x`, not the literal `1100`; the spoken nibble `1100` is one possible filling of the `x`. A completed `BF` binary cell is not visible in these later top-of-buffer views. Guide verification gives `BF = 1011 1111`; it does not establish the instructor's later worksheet contents.

### Historical scratchpad: two-byte mask worksheet (1:36:00–1:49:00)

The right pane is plain text, not a Python file, calculator output or saved test fixture. It shares the screen with the encoder on the left. At 1:34:35 the range table has the five `x` positions of the first byte selected; by 1:36:00 the `110` prefix is selected instead. A later scroll at 1:46:00 (`t06360.png`) briefly revisits the earlier notes on U+271E and U+1F618; it is not a change of the eleven-bit input. The following completed scratch is from `t06480.png` (1:48:00), corroborated by `t06415.png` (1:46:55), `t06420.png` (1:47:00) and `t06540.png` (1:49:00). Column spacing is represented in character cells; `and` and `or` label operations in the handwritten layout rather than execute Python Boolean operators.

```text
45 |    UTF8-2      = %xC2-DF UTF8-tail
46 |    UTF8-tail   = %x80-BF
47 |
48 | C2          DF         80         BF
49 | 1100 0010   1101 1111  1000 0000  1011 1111
50 |
51 |
52 | 00000101 00101010
53 |        0000010100
54 |          11000000 or
55 |          --------
56 |          11010100
57 |
58 | 00000101 00101010
59 | 00000000 00111111
60 | ----------------- and
61 |          00101010
62 |          10000000
63 |          --------- or
64 |          10101010
65 |
66 | 11010100 10101010
67 | 110xxxxx 10xxxxxx
68 |
```

Lines 45–49 retain the allowable hex ranges and their four endpoints. Lines 52–56 form the lead. Notice the ten-character, zero-padded shifted row **`0000010100`** as actually typed on line 53; the extra leading zeros do not change its value of 20. Lines 58–64 start again from the original value and form the continuation. Lines 66–67 put the two calculated bytes above their template. Neither the original nor the resulting bytes were displayed here by a `print()` call.

Distinct scratch states, in order (individual lines are snapshots, **not** a second contiguous listing):

| Requested target | Gutter | Text / action visible at that point |
|---|---:|---|
| 1:37:00 (`t05820.png`) | 52 | `000010100101010` without a space; the last eight positions are selected during the count. |
| 1:37:30 (`t05850.png`) | 52 | `00000101 00101010` after adding the leading zero and separating the octets. |
| 1:38:00 (`t05880.png`) | 53 | A copy of `00000101 00101010` is offset beneath line 52; its last six bits are selected to be dropped, not deleted from the original. |
| 1:39:00 (`t05940.png`) | 53–54 | `0000010100` above `11000000`; the low ends align. |
| 1:39:35 (`t05975.png`) | 56 | `110` is the first part of the lead under the OR row. |
| 1:40:00 (`t06000.png`) | 56 | `1101011` is a seven-digit work in progress, not the completed lead byte. |
| 1:40:03 (`t06003.png`) | 56 | `11010110` is the completed **incorrect scratch**. With the OR mask's low bits all zero, the shifted input's lower bits must pass through unchanged. |
| 1:40:50 (`t06050.png`) | 56 | `11010100` is the corrected first octet. |
| 1:41:00 (`t06060.png`) | 58 | `00000101 00101010` reappears unchanged below the first result. |
| 1:42:00 (`t06120.png`) | 59 | `000000000011 1111` during regrouping of the mask; the display space is not a binary digit. |
| 1:42:35 (`t06155.png`) | 59–61 | Regrouped as `00000000 00111111`; `and` on the separator, then an empty result row. |
| 1:43:00 (`t06180.png`) | 61 | `001` so far, not an octet. |
| 1:43:15 (`t06195.png`) | 61 | `00101011` briefly has the wrong final bit. Zero AND one must be zero. |
| 1:44:00–1:44:05 (`t06240.png`, `t06245.png`) | 59–61 | Corrected `00101010`; the six low mask bits are initially selected to show why the input pattern passes. |
| 1:45:00 (`t06300.png`) | 62–64 | `10000000` and an `or` separator, with a blank result row. |
| 1:46:05 (`t06365.png`) | 64 | `10101010` is now the completed continuation. |
| 1:46:55–1:47:00 (`t06415.png`, `t06420.png`) | 66–67 | `11010100 10101010` over `110xxxxx 10xxxxxx`; first the lead payload, then a portion of the tail is highlighted. |

The final row is consistent with a separate execution: U+052A produced `D4 AA`, not `D6 AA` or `D4 AB`. The wrong scratch rows were neither exceptions nor alternative encodings emitted by the class program. At 1:49:33 the view is OBS over Discord, with a `Stop Recording` control; it does not show another computation or establish the exact stop-button click.

## Micro-Learning Insights

- **Start with the question the app cannot answer.** Seeing bytes appear after pressing a button confirms a result, but not how it was produced. Here the missing link was a table in RFC 3629 and the Python that fills its bit positions.
- **Read a large standard by following one rule.** The C draft became useful when the class stopped trying to read hundreds of pages and traced *identifier* to something a programmer can actually type. A base case makes a self-referential rule usable.
- **Read an agreement, not a tone of voice.** Before treating *shall* as weaker than *must*, look up the document’s requirement conventions. In RFC 2119 they mean the same thing.
- **Ask what the digits describe.** `1F618` is a character number written in hex; `F0 9F 98 98` is the UTF-8 encoding of that number. Both answers can be correct because they answer different questions.
- **Keep four places per hex digit while converting.** Write `2` as `0010` in `271E`. Discarding leading zeros inside a group shifts the positions of the following digits.
- **Separate the count from the last label.** Seven free bits offer 128 choices; the last is 127 because the first is zero. Apply the same reasoning to eleven free bits before looking up the upper endpoint.
- **Count the x’s, not all the ink.** `110xxxxx 10xxxxxx` takes sixteen transmitted bits but has only eleven places for the character number. The other five bits tell a reader how to interpret the bytes.
- **Check a hand trace by undoing it.** Strip `110` from `11010100` and `10` from `10101010`; put `10100` beside `101010`. The recovered groups reproduce the original payload without depending on a font or on the printed glyph.
- **A successful example is not a validator.** The class encoder reproduces the hand example. A separate surrogate test still distinguishes its behavior from strict UTF-8: correct packing and checking which values are allowed are different jobs.
