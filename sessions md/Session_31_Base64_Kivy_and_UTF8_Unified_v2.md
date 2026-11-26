# CSCS1240F26 · Session 31 | From text to bytes and back

**Worked teaching companion · Base64, Kivy callbacks, and reading UTF-8 by its leading bits**

In the converter, typing `A` and pressing **Encode to Base64** produces `QQ==`. Typing `£` and pressing the same button produces `wqM=`. Both inputs look like one character. Why does only the first output end in two equals signs? Follow the data below the button: the app reads text, UTF-8 turns that text into bytes, and Base64 groups the bits of those **bytes**, not the characters you typed. The answer links the RFC diagram at the start of class to the handwritten bit trace and the second app near the end.

**How to use the companion.** Start with the converter as a user, then follow one button press through the interface and the encoder. Work through `A` on paper before moving on to `£`. The knowledge check asks you to explain both without looking at the code. The historical appendix preserves what could actually be read on screen; its line numbers are not instructions to type.

**Source and coverage.** [CSCS1240F26 31 — JoesTA](https://www.youtube.com/watch?v=o5G3UsVnEGs). The full returned English auto-caption track contains 1,887 cues, from the greeting at 0:00:01.520 to the farewell beginning at 1:32:58.560. The last cue nominally ends at 1:33:01.800; the independently probed video stream ends at 1:33:01.350. Caption-free intervals do not establish silence. Visual evidence comprises 94 whole-minute frames (0:00:00–1:33:00) and 46 event frames, including four targeted captures that resolved partially visible decoder and encoder lines. Time references identify cues or requested frame targets, not proof of every intervening keystroke, save, or run. The transcript and original frames, not previous guides, are the lesson evidence.

## Why send three bytes as four characters?

Suppose a sender has three bytes of data, but the receiving system can carry only plain text. Sending the bytes as they stand is risky: not every eight-bit value is a printable character. **Base64** keeps the bits in order but changes how they are grouped. It represents a complete three-byte group with four printable characters. A receiver that knows the same alphabet can reverse the process. This is why the class moved from its earlier discussion of byte order to an application that both encodes and decodes.

### Find the alphabet the sender and receiver must share (0:01:49–0:06:45)

The instructor mentioned ISOC and the IETF, then turned to the [RFC Editor](https://www.rfc-editor.org/), whose home page calls itself “The official home of RFCs.” The search field showed **`base`** as the instructor asked about Base64. An **RFC** is a Request for Comments; the site includes Internet standards as well as historical and informational material. The document chosen here, [RFC 4648](https://www.rfc-editor.org/info/rfc4648/#page-5), is marked *Standards Track*, October 2006. The instructor pointed out the header identifying an older document this one obsoletes, then passed over most of the document to study its Base64 section. It also covers Base16 and Base32: the numbers name alphabet sizes, not numbers of input bytes.

Read “Table 1: The Base 64 Alphabet” as a lookup, not a list of bytes to transmit verbatim. Six bits select one of 64 values. A value of 0 selects the letter **`A`**, not the digit zero; 52 selects **`0`**. The exact ordering matters when working backward.

| Six-bit value (decimal) | Standard Base64 output character |
|---|---|
| 0–25 | Uppercase `A` through `Z` |
| 26–51 | Lowercase `a` through `z` |
| 52–61 | Digits `0` through `9` |
| 62, 63 | `+`, `/`, respectively |
| Not an index | `=` marks padding at the end |

Why have another alphabet? A slash in a filename can be read as a path separator; the RFC also notes that some older text-search tools treat plus and slash as word breaks. Its URL- and filename-safe alphabet keeps positions 0–61, but uses **`-`** at 62 and **`_`** at 63. The symbols change, not the bit values. This is a reason to check which alphabet an application expects rather than substituting characters by eye.

Key items to scan:

- **Base64** — carries binary data as printable text; decoding reverses the representation.
- **RFC**, **`base`** — the RFC Editor search leads to the relevant specification, RFC 4648; section 4 and Table 1 give the standard Base64 mapping.
- **`A`**, **`0`** — alphabet positions 0 and 52; the printed character is not the position number.
- **`+`**, **`/`** — standard symbols for 62 and 63; **`-`**, **`_`** fill those positions in the URL- and filename-safe variant.
- **`=`** — fills an unused position in a padded final quartet. It is not an alphabet index and need not appear at all.

### Read across the byte boundaries (0:05:40–0:08:51)

Five input bytes present a puzzle: the first three fit the rule, but what about the remaining two? Start with the complete group. An *octet* is eight bits. Lay three octets end to end, keep all 24 bits, then read six at a time. Each six-bit number is an *index*: look up its printable character in the alphabet. The second index takes two bits from the first octet and four from the second. Thus an output character need not stand for a single input byte. In this guide diagram, each letter stands for one bit from its named input byte, not for a literal letter being encoded:

```text
Input octets:   aaaaaaaa bbbbbbbb cccccccc
Lookup groups:  aaaaaa   aabbbb   bbbbcc   cccccc
Output:         index 1  index 2  index 3  index 4
```

If a final group runs short, two different things happen. First, put zero **bits** on the right of the last partial six-bit index so there is a number to look up. Then fill any unused *character positions* of the four-character output with **`=`**. The receiver does not get extra zero bytes at the end of the original message. This distinction is easy to miss in the spoken shorthand about “adding bytes”; the RFC states the zero-bit rule on screen at 0:06:00.

| Bytes left in the last group | What supplies the last data character? | Final quartet |
|---|---|---|
| 3 | All 24 bits are real; no partial index | Four data characters, no padding |
| 2 | 16 real bits: two full indexes, then four bits plus two zero bits | Three data characters, then `=` |
| 1 | 8 real bits: one full index, then two bits plus four zero bits | Two data characters, then `==` |

So five bytes leave **two**, not one, after the first three. The first discussion gets this right; the later spoken recap briefly says one. Seven bytes, the other length the instructor proposed, leave **one** after two complete groups. The strings therefore end in one and two equals signs respectively.

The RFC’s ASCII-art figures make the boundary crossing visible. Read the figure caption before counting: the highlighted figure at 0:08:00 and 0:08:08 is **Base32**, not Base64. Five octets make 40 bits here. Each of its eight output characters uses five bits. As the highlight moves from the first character to the second, the second spans the first octet boundary. The same principle applies to Base64, but its groups are six bits wide. The instructor moved past further RFC examples to see what the app actually does.

Key items to scan:

- **Octet / index** — eight bits arrive together; a Base64 lookup reads six bits at a time without resetting at the octet boundary.
- **`=`** — with two bytes left use one padding character; with one byte left use two. Zero bits complete a lookup, not an input byte.
- **Five versus seven bytes** — `3 + 2` ends in `=`; `3 + 3 + 1` ends in `==`.
- **Base32** — the highlighted eight-character figure uses five bits per lookup, not the Base64 rule.

## Test the converter in both directions (0:09:00–0:12:00)

The instructor chose to test the app before reading its logic. The previous class had experimented with enlarging the Kivy interface, and the instructor reported that the version committed to the F26 demo repository and announced on Discord included a breakage. The instructor referred students to the end of the previous session for the change; no specific repair was established here. That report is separate from the deliberate invalid-input test below.

On the classroom machine, PowerShell showed the **`Numerical`** environment and **`.\src\base64App.py`**. The converter window was titled *Base64 Encoder / Decoder*. Under *Base64 Converter*, the upper field was labeled *Input Text / Base64 String:*; *Output Result:* appeared above the lower field. Both buttons read the **upper** field. Producing an encoded result below does not replace the original text above.

| Classroom action | Observed result in the lower field |
|---|---|
| Enter `I don't know` above; choose **Encode to Base64** (0:10:33). | `SSBkb24ndCBrbm93` |
| Encode the same upper field again (0:11:00). | `SSBkb24ndCBrbm93` again. The input was not changed. |
| Leave `I don't know` above; choose **Decode from Base64** (0:11:10). | `Decoding Error: Invalid Base64 character encountered: ' '` |
| Encode again; use **Copy Output to Clipboard**, paste the result into the upper field, then choose **Decode from Base64** (0:11:37). | With `SSBkb24ndCBrbm93` above, the lower field reads `I don't know`. |

You can account for every character in the first result. Split the phrase into three-byte groups, keeping both spaces and the apostrophe: `I d` → `SSBk`, `on'` → `b24n`, `t k` → `dCBr`, `now` → `bm93` (guide breakdown, independently checked). Join the four outputs without separators and you get exactly the string below the buttons. There is no partial group to pad.

In the error message, the two quotes enclose a space. The first space in the phrase is already outside this decoder’s alphabet. The decoder did not fail to reverse its own output: it had been handed the original **plain text** instead. Copying the encoded result into the input changes the question. Recovering the original phrase, spaces and apostrophe included, completes a **round trip**. In a real transfer the encoded text would travel to the other end before that final step.

Key items to scan:

- **`Numerical`**, **`.\src\base64App.py`** — classroom environment and script visible in PowerShell; the test results were displayed by the GUI, not printed by that prompt.
- **Encode to Base64** — turns the upper field’s plain text into `SSBkb24ndCBrbm93` below.
- **Decode from Base64** — expects encoded text above; a literal space in the original phrase triggers the error observed here.
- **Copy Output to Clipboard** — copies the lower result; pasting it above makes that result the next input.
- **Round trip** — encoding, then decoding the encoded result, restores the **plain text** `I don't know`.

### Why one letter produces two letters and two equals signs (0:12:01–0:15:16)

The instructor entered lowercase **`a`**. A student reasoned that the character has eight bits, a full block has 24, and the output might need four positions. The screen confirms the four positions: **`YQ==`**. But why two *letters*? The first six bits of `a` select `Y`; the last two bits still carry information. They start a second index. Filling its right side with four zero bits selects `Q`. The two remaining positions hold padding.

| Observed GUI: upper field | Observed GUI: lower field | Guide explanation of the bits (independently checked) |
|---|---|---|
| `a` (0:13:00, 0:14:00) | `YQ==` | `01100001` → `011000` (24, `Y`), `010000` (16, `Q`); two unused positions. |
| `ab` (0:14:40) | `YWI=` | Split 16 bits as `011000`, `010110`, `0010` + `00` → 24, 22, 8 → `YWI`; one unused position. |
| `abc` visible on the first line (0:14:51, 0:15:00) | `YWJjCg==` | `YWJj` represents the three letters; `Cg==` represents one further byte, a line feed. |

The last row is a useful diagnostic, not an exception to the three-byte rule. The instructor wondered aloud whether the line ending had been stripped. An independent decode of **`YWJjCg==`** gives the bytes **`b'abc\n'`**. As a control, encoding just **`b'abc'`** gives **`YWJj`**, with no padding. The result identifies the extra byte, but the stills do not establish how it got there. When padding looks wrong, examine the actual bytes before changing the encoding rule.

Annotated teaching version — guide-added comments. This independent Python check compares the two possible inputs; it does not run the classroom app.

```python
import base64                              # Use a separate Base64 implementation.
print(base64.b64encode(b'abc').decode())   # Show the encoding of just three bytes.
print(repr(base64.b64decode('YWJjCg==')))  # Make the recovered line feed visible as \n.
```

Independently verified output:

```text
YWJj
b'abc\n'
```

Key items to scan:

- **`a`**, **`YQ==`** — eight bits need two six-bit lookups; two output positions remain for padding.
- **`ab`**, **`YWI=`** — sixteen bits need three lookups; just one position remains.
- **`b'abc'`**, **`YWJj`** — the three-byte control needs no padding; this was independently checked, not the displayed result.
- **`YWJjCg==`**, **`b'abc\n'`** — the displayed result decodes to four bytes, including the line feed. Its last quartet therefore has two padding characters.

## From starting the app to seeing a window (0:15:32–0:22:15)

The converter has two different jobs. One class knows how to turn text into Base64 and back. Another puts controls on a screen and decides when to call the first class. For the second job the program uses Kivy: the instructor names the application class, box layout, label, text input, button, scroll view and clipboard as framework facilities, not things the converter author had to implement. The editor shows the application class in **`base64App.py`**.

### Who calls the first method?

At the bottom of the file, **`Base64App().run()`** creates one new application object and calls an inherited method on it. The class name **`Base64App`** was the programmer’s choice; the method name **`build`** is not arbitrary. This is where the application supplies its own version of the method Kivy looks for. The programmer does not need a second call to build the window or a loop that continually checks the buttons.

*Annotated teaching version — guide-added comments; lifecycle outline with the intervening widget construction omitted.*

```python
class Base64App(App):                         # Use Kivy's App behavior, including run().
    def build(self):                          # Kivy calls this on the app being started.
        self.title = "Base64 Encoder / Decoder"  # Set the outer window's title bar.
        rootLayout = BoxLayout(orientation="vertical", padding=15, spacing=10)  # A new column.
        # The real build adds labels, fields, rows and buttons here.
        return rootLayout                     # Give Kivy the whole assembled interface.

if __name__ == "__main__":                   # Start only when this module is run as the program.
    Base64App().run()                         # Construct, then start; don't call build() here.
```

| When | What happens | What the user gets |
|---|---|---|
| Start the file as the program | The guard is true; the new app’s inherited **`run()`** starts it. | Kivy starts the application. |
| Kivy requests its contents | **`build`** constructs the widgets, attaches them and returns the root widget. | One window displaying the assembled tree. Returning is not printing. |
| While the window is open | Kivy’s event loop dispatches events to the controls and their registered handlers. | The same window can accept many presses, edits and resizes. |
| Close the window | The loop ends in the instructor’s lifecycle account. | A closed app need not keep asking whether a button was pressed. |

**Key items to scan**

- **`base64App.py`** — file containing the GUI subclass as well as the course’s conversion code.
- **`Base64App`** and **`App`** — the course’s class and the Kivy class it inherits from.
- **`Base64App().run()`** — first construct the application; then call its inherited **`run()`** method.
- **`build`** — framework hook that supplies the root widget; the spelling matters because Kivy calls it.
- **`return rootLayout`** — return the interface object, not a result string; the event loop handles what happens next.

### The title bar is not the heading

At 0:21:21–0:21:43 the instructor corrects a pointer-level mix-up. **`self.title`** puts “Base64 Encoder / Decoder” in the window frame. “Base64 Converter” is a separate **`Label`** *inside* the black content area. A label ordinarily identifies something rather than accepting typed text or acting like a button. The second label identifies the field into which a person can enter either ordinary text or Base64.

*Annotated teaching version — guide-added comments; build fragment, compacted line breaks.*

```python
rootLayout.add_widget(                      # The outer call accepts ONE widget.
    Label(text="Base64 Converter",          # First construct that widget: the interior heading.
          font_size="20sp", bold=True,      # Make the heading larger and bold.
          size_hint_y=None, height=40)      # Use height 40 rather than a share of flexible height.
)
rootLayout.add_widget(                      # Add another child below the heading.
    Label(text="Input Text / Base64 String:",  # Describe what the entry box accepts.
          size_hint_y=None, height=25,      # Reserve a shorter, fixed-height row.
          halign="left", valign="middle")   # Requested text alignments for this label.
)
```

Read the parentheses from the inside out. The several keyword arguments configure one new label; they are not separate widgets passed to **`add_widget`**. Naming that label in an intermediate assignment and then adding it would split the same operation into two easier-to-read steps, a possibility the instructor mentions. The vertical root uses 15 of padding around its contents and 10 of spacing between children. For controls that should go *across*, the later code actually uses a horizontal box, resolving a verbal slip about the orientation.

**Observed interface (0:24:15):** title bar “Base64 Encoder / Decoder”; bold heading “Base64 Converter”; field caption “Input Text / Base64 String:”. These are three different strings.

**Key items to scan**

- **`self.title`** — names the window frame, not the first control in it.
- **`Label`** — constructs display text: one for the heading, another for the input caption.
- **`add_widget`** — attaches the single widget returned by the inner constructor to this parent.
- **`orientation="vertical"`** — makes the root a column; padding is at its edges and spacing separates its children.

## Build the controls in the order the user sees them (0:22:19–0:29:36)

### One editable field, two operations on it

The caption is not the place you type. **`self.inputText`** keeps a reference to the actual editable **`TextInput`** on this app, so a button handler can read it later. In contrast with a typical one-line password field, this converter accepts text containing line breaks. The instructor recalls increasing the font size because the original display was hard to read on the classroom TV.

*Annotated teaching version — guide-added comments; build fragment.*

```python
self.inputText = TextInput(                 # Keep the new input widget on the app.
    multiline=True,                         # Allow a line break instead of forcing one line.
    hint_text="Enter text to encode or Base64 string to decode...",  # Prompt, not input data.
    size_hint_y=0.25,                       # Give the layout a relative vertical sizing hint.
    font_size="24sp",                       # Make typed text easier to see.
    background_normal="",                   # Do not use the default normal background image.
    background_color=(0.1, 0.1, 0.1, 1),    # Dark background, fully opaque.
    foreground_color=(0.9, 0.9, 0.9, 1)     # Light text against it.
)
rootLayout.add_widget(self.inputText)        # Show this same editable widget in the column.
```

**Observed interface (0:24:15):** before entry, the dark box displays “Enter text to encode or Base64 string to decode...”. The prompt describes the two possible uses; it is not a prefilled message the encoder will convert.

**Key items to scan**

- **`self.inputText`** — app-chosen name for the field; its **`.text`** holds the user’s current contents.
- **`TextInput`** — Kivy widget that accepts the typing; **`multiline=True`** allows more than one line.
- **`hint_text`** — visible prompt while the field is empty; **`font_size="24sp"`** improves classroom legibility.
- **`size_hint_y=0.25`** — relative input-field sizing, not a promise that it takes a quarter of the entire screen.

### Nest a horizontal button row in the vertical window

A button’s label tells the *person* what to expect. It does not tell Kivy what calculation to perform. Build the button, connect its press event to a method, and put it in the layout. These are three distinct acts: creating, binding and displaying. In particular, **`on_press=self.encode`** hands Kivy the method to call *later*; adding parentheses to call the method during setup would not register it in this way.

*Annotated teaching version — guide-added comments; line wrapping compacted.*

```python
encodeLayout = BoxLayout(orientation="horizontal", spacing=10,  # Start a new row, not a new window.
                         size_hint_y=None, height=45)           # Keep this row 45 high.
encodeButton = Button(text="Encode to Base64",                 # Make the left action button.
                      background_color=(0.2, 0.6, 1.0, 1.0))   # Blue tint.
encodeButton.bind(on_press=self.encode)                        # Register the GUI encode handler.
decodeButton = Button(text="Decode from Base64",               # Make a separate right button.
                      background_color=(0.2, 0.8, 0.4, 1.0))   # Green tint.
decodeButton.bind(on_press=self.decode)                        # Register the other direction.
encodeLayout.add_widget(encodeButton)                          # First child of the row.
encodeLayout.add_widget(decodeButton)                          # Second child, next to the first.
rootLayout.add_widget(encodeLayout)                            # The column receives ONE assembled row.
```

A student asks whether the programmer is defining the size. Not every size, and not the position of each button: there *are* a 45-high row, margins, spacing and relative sizing hints in the source. The layout manager allocates the remaining space when the window changes dimensions. This saves the programmer from calculating absolute pixel positions for every screen size, orientation and platform. At 0:24:15 and 0:29:03 the converter is visible with different proportions; the blue and green controls still share one row. The instructor likens the nested containers to Russian nesting dolls.

**Key items to scan**

- **`encodeLayout`** — horizontal box inside the vertical root; its two children stay together on resize.
- **`on_press=self.encode`** — bind the blue button to the app’s encode method without calling it during construction.
- **`decodeButton.bind(on_press=self.decode)`** — connect the green button to the opposite operation.
- **`rootLayout.add_widget(encodeLayout)`** — add the entire row once; the buttons do not become two independent rows.

### Make room for a status message and a long result

The next row has a permanent caption, “Output Result:”, and room for changing feedback. **`self.statusLabel`** starts with the empty string: the widget exists even when there is nothing to read. The result itself goes in a separate widget called **`self.outputLabel`**. Despite that name it is a read-only text input, *not* a Kivy label. The program can set its contents; the user is not meant to type over them.

*Annotated teaching version — guide-added comments. Adapted output-header constructor: the argument after `size_hint_y=None` runs off screen. It is omitted here, so this fragment does not reproduce the original geometry exactly.*

```python
outputHeaderLayout = BoxLayout(orientation="horizontal", size_hint_y=None)  # Header row.
outputHeaderLayout.add_widget(Label(text="Output Result:",     # Permanent caption.
                                    halign="left", valign="middle"))
self.statusLabel = Label(text="", color=(0.3, 1.0, 0.3, 1.0),  # Create an initially blank green label.
                         halign="right", valign="middle")      # Request right/middle alignment.
outputHeaderLayout.add_widget(self.statusLabel)                # Room for feedback beside the caption.
rootLayout.add_widget(outputHeaderLayout)                      # Add both labels as one row.

outputScroll = ScrollView(size_hint_y=0.3)                      # A new viewport; it isn't the result.
self.outputLabel = TextInput(readonly=True, multiline=True,    # Result can span lines but isn't editable.
    hint_text="Result will appear here...", font_size="24sp",  # Hint before any result exists.
    background_normal="", background_color=(0.1, 0.1, 0.1, 1), # Same dark backing as input.
    foreground_color=(0.9, 0.9, 0.9, 1))                       # Light result text.
outputScroll.add_widget(self.outputLabel)                      # Put the result field inside the viewport.
rootLayout.add_widget(outputScroll)                            # Put the viewport inside the root.

copyButton = Button(text="Copy Output to Clipboard",           # Make the bottom action button.
                    size_hint_y=None, height=45,               # Keep a fixed-height row.
                    background_color=(0.9, 0.6, 0.1, 1.0))    # Amber tint.
copyButton.bind(on_press=self.copyOutput)                       # Register what the press should do.
rootLayout.add_widget(copyButton)                              # Put Copy below the result area.
return rootLayout                                              # End of build: hand the whole tree to Kivy.
```

The class tests what happens when more text exists than fits. The instructor mentions **`Ctrl+A`**, puts a long string in the upper field, and encodes again to get more content. At 0:29:03, the upper field ends in a visible line `93`; the lower field shows wrapped encoded text with a final visible line `ibTkz`. Those are *visible portions*, not the complete strings. The instructor expected to see a scrollbar, did not see the expected bar and reported scrolling through the text with the mouse wheel. Neither the stills nor that report establish which nested widget handled the wheel, or why a bar wasn’t visible. No scrollbar repair is demonstrated here.

**Key items to scan**

- **`self.statusLabel`** — blank initially; later reports copy success or that there is nothing valid to copy.
- **`self.outputLabel`** — read-only result *field*; **`readonly=True`** prevents user editing, not programmatic updates.
- **`outputScroll`** — **`ScrollView(size_hint_y=0.3)`** that contains the result field; the root contains the scroll view.
- **`Ctrl+A`** — Select All during the long-text test; distinguish the presence of scrollable content from a visible scrollbar.
- **`copyButton`** — bottom action bound to the copy handler, not another conversion button.

## Follow a press from the input field to the result (0:29:38–0:32:59)

### The app handles the event; the conversion class handles the data

Suppose a person presses the blue button. Kivy calls the previously registered **`self.encode`**. Here **`self`** means the running app; **`instance`** receives the button that sent this event. The code does not use that button as input data. It reads the *current* text field, calls the course’s separate conversion method and displays the returned string in the existing output widget. No terminal print or new window is needed.

*Annotated teaching version — guide-added comments; these are separate methods on the GUI class.*

```python
def encode(self, instance):                     # Kivy supplies the pressing button as instance.
    source = self.inputText.text                # Read the input NOW, not when bind() ran.
    try:                                        # Attempt the conversion and display update.
        encoded = Base64.encode(source)         # Pass that string to the course's encoder.
        self.outputLabel.text = encoded         # Replace the previous on-screen result.
        self.statusLabel.text = ""              # Clear old status, such as a prior copy notice.
    except Exception as e:                      # If the protected code raises an exception ...
        self.outputLabel.text = f"Encoding Error: {str(e)}"  # Show the failure in the result field.
        self.statusLabel.text = ""              # Do not leave a stale success notice visible.

def decode(self, instance):                     # A press of the green button follows this path.
    source = self.inputText.text                # Read the same upper field, NOT the last result.
    try:                                        # Try interpreting that text as Base64.
        decoded = Base64.decode(source)         # Conversion method returns decoded text on success.
        self.outputLabel.text = decoded         # Overwrite the lower field, not the input.
        self.statusLabel.text = ""              # Reset old status on success.
    except Exception as e:                      # Keep a conversion failure inside the interface.
        self.outputLabel.text = f"Decoding Error: {str(e)}"  # Include the reason supplied by e.
        self.statusLabel.text = ""              # Also clear old status on failure.
```

**Observed interface:** the long-input encode demonstration shows different text in the two fields. **Expected from source:** either handler leaves the upper field untouched; each updates the lower field and clears the status. An exception produces a message prefixed with the direction-specific error text, not a successful conversion. In the discussion the instructor is unsure whether an earlier error was an *encoding* error. Do not infer from that aside that encoding already-encoded text is inherently illegal: the blue button still treats its input as text. To reverse a result, the encoded string must become the upper field’s contents before pressing Decode.

**Key items to scan**

- **`self.encode`** — GUI callback; it reads and updates widgets. **`Base64.encode(source)`** converts the string it is given.
- **`self`** — running application object; **`instance`** — event sender, not the text to encode.
- **`source`** — local value read from **`self.inputText.text`** on this press.
- **`Base64.decode(source)`** — the course’s decoding operation, not a method of the green button.
- **`except Exception as e`** — intercept an exception; the GUI puts “Encoding Error:” or “Decoding Error:” plus its message into the result field.

### Copy the result, not the prompt or an error

The copy handler reads the result field rather than the input. The screen shows a guard that checks for nonempty data, rejects an “Encoding Error” prefix and begins a second prefix check with “D”; the rest of that test is clipped. The version below explicitly illustrates the likely intended *decoding-error* check, consistent with the two strings written by the handlers. It is an adaptation, not a recovery of that clipped text.

*Annotated teaching version — guide-added comments; second prefix supplied for illustration.*

```python
def copyOutput(self, instance):                     # A press of the bottom button calls this.
    data = self.outputLabel.text                    # Read actual result text, not its empty-field hint.
    if data and not data.startswith("Encoding Error") and not data.startswith("Decoding Error"):
        Clipboard.copy(data)                        # Copy nonempty text that passed the prefix tests.
        self.statusLabel.text = "Copied to clipboard!"  # Report success beside Output Result:.
    else:                                           # Empty text or a rejected error prefix ...
        self.statusLabel.text = "Nothing valid to copy!"  # Give feedback without copying.
```

**Expected from the visible branches:** with no result, the false branch sets “Nothing valid to copy!”; with ordinary result data that passes the checks, the true branch copies that text and sets “Copied to clipboard!”. Neither branch writes over the result field. These are code-supported consequences, not two additional observed clicks.

**Independent handler check, not a classroom run.** To isolate the GUI logic, a small in-memory test supplied pretend text fields, a clipboard that remembered its last value, and a converter set to map `A` to `QQ==` and back. It exercised the teaching handlers without launching Kivy or testing the course’s conversion algorithm. The actual sequence shows why the result and status need separate widgets:

| Action in the check | Result field after action | Status after action | Copied value after action |
|---|---|---|---|
| Copy before entering anything | Empty | Nothing valid to copy! | None |
| Encode input `A` | `QQ==` | Empty | None |
| Copy that result | `QQ==` | Copied to clipboard! | `QQ==` |
| Replace input with `QQ==`; decode | `A` | Empty | `QQ==` |
| Replace input with `?`; decoder raises the fixture’s error | `Decoding Error: test rejection` | Empty | `QQ==` |
| Press Copy with that error displayed (adapted guard) | Error remains | Nothing valid to copy! | Still `QQ==` |

The next question is where **`Base64`** comes from: there is no import for that class in the instructor’s account. The course program defines its own class with the alphabet and conversion methods so the class can study the mechanism rather than just use a ready-made module.

**Key items to scan**

- **`data = self.outputLabel.text`** — snapshot of the result to be copied; placeholder text is not data.
- **`Clipboard.copy(data)`** — sends a valid result to the clipboard; the separate status assignment is what tells the user.
- **`startswith`** — inspect a text prefix, not the provenance of the data. The second prefix is only partially visible in this source view.
- **`Base64`** — course-defined conversion class, distinct from **`Base64App`**, the Kivy window and button handler class.

## Inside the Base64 button: three bytes in, four symbols out (0:33:00–0:46:59)

### Start with the text, not its printed appearance

The **`Encode to Base64`** button supplies the text from the box to **`Base64.encode`**. The class is named **`Base64`**, not `B64`; its **`alphabet`** is an ordered lookup string. Index zero means uppercase A, not the character for the digit zero. The final two positions are plus and slash. An equals sign is not in this alphabet: the method adds it separately when the last group is short. (Editor: 0:33:00 and 0:33:08.)

The instructor uses Ctrl+A and Delete to empty the box, crediting Kivy for handling the text editing. Asking the app to encode now produces no error and no encoded text. The spoken explanation mentions **`None`**, but an empty text box supplies an empty string, not necessarily **`None`**. The test **`if not text:`** covers both. It returns an empty string; it does not return the Python value **`None`**. The screenshots later show the placeholder “Result will appear here...”, not a literal message produced by the encoder.

**Annotated teaching version — guide-added comments; opening of the class, not the entire method:**

```python
class Base64:                         # Holds the conversion methods and their shared lookup string.
    alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
    # Positions 0–25: A–Z; 26–51: a–z; 52–61: 0–9; 62: +; 63: /.

    @classmethod                      # Python supplies Base64 as cls for a call on the class.
    def encode(cls, text: str) -> str: # Takes text; the annotation describes a string result.
        if not text:                  # An empty box has nothing to convert.
            return ""                 # Return a real string of length zero; print nothing.
        octets = text.encode("utf-8") # Turn the input string into bytes before doing bit arithmetic.
        encoded = []                  # Create a fresh list of output pieces for this call.
```

The two operations named **`encode`** do different jobs. Python's **`text.encode("utf-8")`** decides which bytes represent the characters. The classroom method then expresses *those bytes* with the Base64 alphabet. The instructor deliberately borrows Python's string encoding here and postpones writing a UTF-8 encoder/decoder. An ordinary ASCII letter occupies one byte in UTF-8, but a “fancy A” need not. **Separate check:** the single character `Á` gives the two bytes `c3 81` in hexadecimal. Count the bytes after conversion, not the letters before it. (Transcript: 0:33:46–0:34:43 and 0:38:50–0:39:44.)

**Key items to scan**

- **`Encode to Base64`** — sends the current input text down the encoding path.
- **`Base64`**, **`Base64.encode`** — class and class method for converting the text; calling the method does not itself print its return value.
- **`alphabet`** — 64 positions, ending in `+/`; padding is separate.
- **`@classmethod`**, **`cls`** — let the method obtain the alphabet from the class rather than requiring a converter instance.
- **`if not text:`**, **`None`** — guard tests for a false value; blank input returns `""`, not `None`.
- **`encode`**, **`text.encode("utf-8")`** — distinguish the custom Base64 encoder from the string method that supplies its input bytes.
- **`octets`** — the resulting bytes; **`encoded`** — a new list for output pieces, not an initially empty string.

### Take at most three bytes; pack them in their original order

At 0:39:49 the instructor describes the first three letters of a keyboard sequence. The box at 0:40:00–0:43:00 reads lowercase **`qwerty`**; changing the case would change the bytes. The first outer pass takes **`b'qwe'`**, the next **`b'rty'`**. A slice stops *before* its right endpoint, just as **`range(1, 10)`** reaches 9, not 10. The instructor corrects “indices 0 through three” to 0 through 2. A slice extending past the end gives what is available. That's different from requesting a single nonexistent position: **`b'q'[0:3]`** returns `b'q'`, whereas **`b'q'[4]`** raises `IndexError` (separate check). No exception handling is needed just to take the last short slice.

**Annotated teaching version — guide-added comments; inside the method:**

```python
i = 0                                 # First unread byte is at position 0.
while i < len(octets):                # Another group exists only if at least one byte remains.
    chunk = octets[i:i+3]             # First take positions 0, 1, 2; the end at 3 is excluded.
    i += 3                            # Next start is 3; this does not change the slice already taken.
    integer = 0                       # Start a fresh packed value for THIS group, not the whole message.
    for octet in chunk:               # For b'qwe' the byte values are 113, 119, 101.
        integer = (integer << 8) + octet  # Make eight empty low bits; add the incoming byte there.
    # The length-dependent output branch runs here before the next while pass.
```

The class asks whether the unfamiliar operator is a shift, and whether shifting left “makes it bigger.” Think in places: the one in the ones place moves to the twos place, then the fours place. Shifting an integer left eight places moves its existing bits up one whole byte. Shifting *zero* still gives zero. That's why the first pass looks wasteful; on the second pass it preserves the first byte, now in bits 8–15, and puts the new byte in bits 0–7. Python evaluates the right side using the **old** value of **`integer`** before assigning the new one. The addition does not mix the bytes: the eight low positions receiving the incoming byte were cleared by the shift. (0:36:29–0:38:08; 0:40:02–0:43:35.)

**Separate execution trace on the on-screen input; hexadecimal shows the byte boundaries.** No result from encoding `qwerty` is displayed in the cited frames.

| Outer pass / inner step | Byte | Before → shifted left 8 | After adding the byte |
|---|---|---|---|
| First group, 1 | `q` = `0x71` | `0x00` → `0x00` | `0x71` |
| First group, 2 | `w` = `0x77` | `0x71` → `0x7100` | `0x7177` |
| First group, 3 | `e` = `0x65` | `0x7177` → `0x717700` | `0x717765` |
| Second group, 1–3 | `r`, `t`, `y` | Reset to `0`; do not carry `qwe` forward | `0x72` → `0x7274` → `0x727479` |
| Next outer test | No byte left | `i` is 6; length is 6 | Stop; the results list survives both groups |

The instructor compares the at-most-24-bit payload to what would fit in a 32- or 64-bit word. The useful point is that three eight-bit bytes fit without loss. **Python clarification:** a Python `int` is not limited to the machine's 32- or 64-bit word size; a separate check of `(1 << 100).bit_length()` returned `101`. Here it is the **three-byte slice**, not the system architecture, that bounds the packed value.

**Key items to scan**

- **`qwerty`**, **`b'qwe'`**, **`b'rty'`** — visible text and its two successive three-byte slices; lowercase matters.
- **`range(1, 10)`** — course analogy for an excluded stop value.
- **`b'q'[0:3]`**, **`b'q'[4]`** — shortened slice succeeds; the out-of-range single index raises `IndexError`.
- **`octets[i:i+3]`**, **`i += 3`** — take up to three bytes, then advance to the next unread position.
- **`integer`**, **`integer = 0`** — holds the packed bytes of one group; resets before the next group's inner loop.
- **`(integer << 8) + octet`** — shift old bytes to more significant positions, then place the new byte at the right.
- **Three-byte slice** — bounds each input group to 24 bits even though Python can represent larger integers.

### Use six bits as an alphabet index; choose padding from the actual byte count

With lowercase **`q`** alone in the box (0:44:00), the slice has one byte. Where do four output positions come from? Eight data bits are not an exact number of six-bit groups. The code adds four **zero bits** at the right to make twelve bits, looks up two symbols, then appends **two equals signs** to finish the four-character output. Those zero bits are part of an index calculation; the equals signs are characters in the returned *string*, not zero bits that were added to the input. A final two-byte group needs only two zero bits and one equals sign. A full three-byte group already has 24 bits, enough for four complete lookups.

To read **`cls.alphabet[(integer >> 6) & 0x3F]`**, work from the inside out. Shift the desired group to the right edge, keep only its six bits with bitwise AND, then use the resulting number as a position in the alphabet. Hexadecimal makes the mask legible: each hex digit describes four bits, so `3` means `0011`, `F` means `1111`, and **`0x3F`** means `00111111` (63). AND with that pattern preserves bits 0–5 and clears everything higher. The one-byte case therefore uses *two different directions*: **`integer <<= 4`** shifts left to align the data; **`integer >> 6`** shifts right to extract the higher group. The editor resolves the direction where the spoken description says “left.” (0:43:42–0:46:34; mask selected at 0:45:00.)

**Annotated teaching version — guide-added comments; branch inside the outer loop, return after it:**

```python
    if len(chunk) == 1:               # One byte: make two usable six-bit groups.
        integer <<= 4                 # q: append four zero bits to its eight data bits.
        encoded.append(cls.alphabet[(integer >> 6) & 0x3F])  # High six: index 28, 'c'.
        encoded.append(cls.alphabet[integer & 0x3F])         # Low six: index 16, 'Q'.
        encoded.append("==")          # One list element containing both pad characters.
    elif len(chunk) == 2:             # Two bytes: make three usable six-bit groups.
        integer <<= 2                 # Append two zero bits to complete the third group.
        encoded.append(cls.alphabet[(integer >> 12) & 0x3F]) # Highest six bits.
        encoded.append(cls.alphabet[(integer >> 6) & 0x3F])  # Middle six bits.
        encoded.append(cls.alphabet[integer & 0x3F])         # Lowest six, including the zeros.
        encoded.append("=")           # A single pad marker fills the fourth output position.
    else:                             # Exactly three bytes: all four groups are data-bearing.
        encoded.append(cls.alphabet[(integer >> 18) & 0x3F]) # First/highest six bits.
        encoded.append(cls.alphabet[(integer >> 12) & 0x3F]) # Next six bits.
        encoded.append(cls.alphabet[(integer >> 6) & 0x3F])  # Next six bits.
        encoded.append(cls.alphabet[integer & 0x3F])         # Last/lowest six bits.
return "".join(encoded)               # After ALL chunks, join the pieces without separators.
```

**Separate checks, not historical GUI output.** The middle row is a guide-created short input; the other two use text visible in the box. The result column gives strings, not a transcript of a terminal run.

| Input | Indices, high group first | Returned string |
|---|---|---|
| `q` | 28, 16; append `==` | `cQ==` |
| `qw` | 28, 23, 28; append `=` | `cXc=` |
| `qwerty` | `qwe`: 28, 23, 29, 37; `rty`: 28, 39, 17, 57 | `cXdlcnR5` |

Only the original byte count tells the method which branch to take. This is why a message that is longer than three bytes can still need padding: a message of length three plus one has a complete first group and a one-byte *last* group. The class next turns to identifying an individual input letter's bits by hand.

**Key items to scan**

- **`q`** — one byte here; its first two output symbols are lowercase `c` and uppercase `Q`.
- **Zero bits** — complete the last six-bit index; **two equals signs** — occupy the unused *character* positions for a one-byte tail.
- **`integer <<= 4`** — align eight bits into twelve; **`integer <<= 2`** — align sixteen bits into eighteen.
- **`integer >> 6`** — move the next higher six-bit group to the low end; shifts of 12 or 18 select still higher groups.
- **`0x3F`** — six low one-bits; `& 0x3F` gives a lookup index from 0 through 63.
- **`cls.alphabet[(integer >> 6) & 0x3F]`** — shift, mask, then choose the symbol at that index.
- **`encoded.append(...)`** — add a piece to the existing list; **`"".join(encoded)`** — build and return one string without extra separators.

### What the Base64 decoder checks, and what it returns

At 0:11:10 the converter rejects `I don't know`; at 0:11:37 it gives that same sentence back. The difference is **where the sentence goes**. Pressing Decode with the sentence in the upper field asks **`Base64.decode`** to treat its letters, spaces and apostrophe as Base64 symbols. The first internal space fails the alphabet test. Pressing Decode with `SSBkb24ndCBrbm93` in the upper field gives the method four groups of valid symbols instead. Their alphabet indices recover bytes; interpreting those bytes as UTF-8 recovers the sentence.

The code first uses **`strip()`** to discard whitespace at the *ends*. If nothing remains, it returns `""`. Otherwise it counts equals signs but never reads that count. It uses **`rstrip("=")`** to remove them from the *right end only*: an internal space or equals sign still has to pass the alphabet test. After lookup, the decoder groups the numeric indices in fours. Each index occupies six bits. Two indices supply twelve bits: discard the four low pad bits to recover one whole byte. Three indices supply eighteen bits: discard two to recover two bytes. Four indices supply 24 bits, exactly three bytes. The decoder collects the bytes across all groups **before** interpreting them as text.

**Annotated teaching version — runnable without Kivy.** The alphabet and decoder statements are transcribed from the editor. The overlapping recovery frames `00-32-53.000_base64-guard.png` (guard and cleanup) and `00-32-54.800_base64-unpack.png` (exception, packing and byte extraction) show the whole method. Only the right-side notes and test driver are guide additions; the unrelated imports, encoder and GUI are omitted. The complete numbered class is in `decoder-historical-final.md`.

```python
class Base64:
    alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"

    @classmethod
    def decode(cls, base64String: str) -> str:
        base64String = base64String.strip()        # Trim the ends, not the middle.
        if not base64String:
            return ""                              # Empty or whitespace-only input: no bytes to read.

        padding = base64String.count("=")          # Count every equals sign; padding is never read.
        cleanedString = base64String.rstrip("=")   # cQ== becomes cQ in cleanedString.

        values = []                                # New list of six-bit indices for this call.
        for char in cleanedString:
            if char not in cls.alphabet:           # An internal space is not an alphabet symbol.
                raise ValueError(f"Invalid Base64 character encountered: '{char}'")  # Literal quotes surround the character.
            values.append(cls.alphabet.index(char))  # c gives 28, Q gives 16.

        octetArray = bytearray()                   # One growing collection for the entire message.
        i = 0                                      # Next unread alphabet index.
        while i < len(values):
            chunk = values[i:i+4]                  # Take up to four indices, not four bytes.
            i += 4                                 # Advance even if this slice was short.
            integer = 0                            # Reset for this slice only.
            for value in chunk:                    # Read each six-bit index in order.
                integer = (integer << 6) + value   # Open six low bits, then add the next index.

            if len(chunk) == 2:
                integer >>= 4                      # cQ: drop four pad bits from 0x710.
                octetArray.append(integer & 0xFF)  # Append 0x71, the byte for q.
            elif len(chunk) == 3:
                integer >>= 2                      # wqM: drop two pad bits, leaving 0xC2A3.
                octetArray.append((integer >> 8) & 0xFF)  # Take the higher byte, C2.
                octetArray.append(integer & 0xFF)         # Then the lower byte, A3.
            elif len(chunk) == 4:
                octetArray.append((integer >> 16) & 0xFF) # First byte of the 24-bit group.
                octetArray.append((integer >> 8) & 0xFF)  # Middle byte, in stream order.
                octetArray.append(integer & 0xFF)         # Last byte; no pad bits to remove.

        return octetArray.decode("utf-8")          # Convert the completed byte sequence to text once.

for label, source in [("blank", " \n"), ("q", "  cQ==\n"), ("pound", "wqM="),
                      ("phrase", "SSBkb24ndCBrbm93")]:
    print(f"{label}: {Base64.decode(source)!r}")   # repr makes the empty result and spaces inspectable.
try:
    Base64.decode("I don't know")                  # Plain text, not the encoded result.
except ValueError as error:
    print(f"Decoding Error: {error}")              # Driver adds the same prefix as the GUI handler.
```

**Independently verified output from that block** (not a classroom terminal):

```text
blank: ''
q: 'q'
pound: '£'
phrase: "I don't know"
Decoding Error: Invalid Base64 character encountered: ' '
```

| Encoded input | Six-bit indices and packed value (separate trace) | What survives as bytes, in order |
|---|---|---|
| `cQ==` | `c` → 28, `Q` → 16. Start at 0; insert 28 to get `0x1C`, then insert 16 to get `0x710`. | `0x710 >> 4` gives `71` hex: lowercase `q`. This decoder run is independent; the classroom `q` frame at 0:44:00 shows the input, not this output. |
| `wqM=` | `48, 42, 12` → `0x30A8C`. | `>> 2` leaves `C2 A3`: **one** UTF-8 character, `£`. Class showed the *encoding* `£ → wqM=` at 1:10:05; the reverse direction here is independently verified. |
| `SSBkb24ndCBrbm93` | First `SSBk`: `18, 18, 1, 36` → `0x492064`. Reset the integer for each of the following quartets, not the byte array. | `49 20 64` = `I d`; the next groups recover `on'`, `t k`, `now`. Joining these bytes and decoding gives the phrase also seen in the GUI at 0:11:37. |

There are two distinct failure points. The classroom space error occurs **before any byte reconstruction**. Line 61 puts literal single quotes around the offending character; the GUI handler, not the method, supplies the `Decoding Error:` prefix. By contrast, `/w==` passes lookup and produces the byte `FF`, which raises **`UnicodeDecodeError`** at the final UTF-8 conversion. This converter returns text, not arbitrary binary data.

**Additional independent runs — not classroom output.** For `" TQ== "`, trimming and removing trailing equals signs leaves `TQ`. The accumulator starts at 0, adds 19 for `T`, then shifts 19 left six places and adds 16 for `Q`, reaching 1232. Discarding the low four bits gives byte 77, the letter `M`.

| Input (Python literal) | Return or exception | What the case shows |
|---|---|---|
| `" TQ== "` | `"M"` | Two symbols yield one byte. |
| `"TWE="` | `"Ma"` | Three symbols yield two bytes. |
| `"TWFu"` | `"Man"` | Four symbols yield three bytes. |
| `"w6k="` | `"é"` | Two bytes can encode one text character. |
| `"T?=="` | `ValueError: Invalid Base64 character encountered: '?'` | A nonalphabet character stops lookup. |
| `"/w=="` | `UnicodeDecodeError` | The bytes, not the symbols, fail the text check. |

**What the decoder accepts without checking.** `TQ`, `TQ==` and `TQ=====` all return `M`: the number of trailing equals signs is never checked against the group length. `T=Q` instead fails on the internal `=`. A lone `T` returns `""`, and `TWFuT` returns `Man`: the one-index tail is packed but no branch appends a byte for it. `TR==` also returns `M`; the decoder discards the low four bits without checking that they are zero. These are behaviors of this code, not instructions for writing valid Base64.

**Key items to scan**

- **`Base64.decode`** — the conversion class method; the GUI callback reads the field and displays either its returned text or an error.
- **`strip()`**, **`if not base64String:`** — trim outside whitespace, then return an empty string immediately if no text remains.
- **`count("=")`**, **`rstrip("=")`** — count equals signs without using the result, then remove every trailing equals sign. An equals sign in the middle is not removed.
- **`cls.alphabet.index(char)`**, **`ValueError`** — turn each valid symbol into an index; reject a nonalphabet symbol rather than silently skipping it.
- **`values[i:i+4]`**, **`(integer << 6) + value`** — take up to four indices; shift the existing bits left six places, then add the next index in those six positions.
- **`& 0xFF`**, **`octetArray.append(...)`** — isolate one byte at a time and keep it in transmission order across groups.
- **`octetArray.decode("utf-8")`**, **`UnicodeDecodeError`** — interpret the recovered bytes as text; valid Base64 alone cannot guarantee valid UTF-8.

## Why does one uppercase A become QQ==? (0:47:00–1:00:07)

### Find the input byte before looking up the output letter

The converter first has a lowercase **`q`** in its input box. The instructor switches examples: use uppercase **`A`**, a character whose number is easy to build by hand. The converter eventually shows **`QQ==`**. There are two different lookups to explain here: **ASCII** tells us which number represents the *input* letter, and the **Base64 alphabet** tells us which *output* letter to write for a group of six bits. Looking up the ASCII code for Q will not explain why the encoder chose Q.

At 0:48:00 the decimal table on [asciitable.com](https://www.asciitable.com/) puts A beside 65. On the [ASCII - Binary Character Table](http://sticksandstones.kstrom.com/appen.html) at 0:49:25, the same row reads **`065`** and **`01000001`**. The first is decimal with a leading zero for display. The second is the same value written in eight bit positions. Start at the right: position 0 contributes one, position 1 would contribute two, and each move left doubles the weight. Only the positions marked 1 contribute to the sum.

| Read left to right | Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 | Sum of the occupied places |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---|
| Place weight | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | |
| Uppercase A | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 64 + 1 = 65 |
| Lowercase a | 0 | 1 | 1 | 0 | 0 | 0 | 0 | 1 | 64 + 32 + 1 = 97 |

Look down the columns of the letter table. Uppercase B and C are 64 + 2 and 64 + 3. Lowercase b and c are 96 + 2 and **96 + 3**, respectively; the table resolves a repeated “plus two” in the captions. For the 26 letter pairs, the low five bits count from 1 to 26, and the 32-place bit distinguishes the cases. Don't treat a prefix alone as proof that a byte is a letter: a five-bit field has 32 possibilities, not just the 26 used for letters. Other codes, including punctuation, occur around the letter ranges.

**Key items to scan**

- **`q`** — the first visible input, not the byte used for the worked result.
- **`A`**, **ASCII** — the input character has decimal code 65; **`065`** is the table’s display of that number.
- **`01000001`** — for uppercase A, the 64-place and the 1-place are on; lowercase a also turns on the 32-place.
- **96 + 3** — lowercase c is 99, after a = 97 and b = 98.
- **Base64 alphabet**, **`QQ==`** — this alphabet supplies the two Qs in the output, not the ASCII lookup table. The equals signs mark padding.

### The character zero is not the number zero

What number does a file contain when it contains the printed digit **`'0'`**? The instructor points out that the first ASCII codes, 0 through 31, are nonprinting control characters. The printable digits start later. At 0:52:00 the [OpenReplay ASCII table](https://openreplay.com/tools/ascii-table/) highlights the character zero: decimal **`48`**, hexadecimal **`30`**, binary **`00110000`**. The two ones occupy the 32-place and the 16-place; no units are added. Keep those places and add one to get the character `'1'` (49, binary `00110001`), or two to get `'2'` (50, binary `00110010`). This is why the instructor corrects “48” to “65” when returning to uppercase A at 0:53:08–0:53:14: 48 belonged to the digit example.

**Key items to scan**

- **`'0'`** — a printable character, not the zero-valued control code.
- **`48`**, hexadecimal **`30`**, binary **`00110000`** — three representations of the same digit character code; 32 + 16 = 48.
- **`'1'`** and **`'2'`** — neighboring digit characters at 48 + 1 and 48 + 2.

### Follow A through the one-byte branch

At 0:53:00 the Base64 Converter shows **`A`** above the buttons and **`QQ==`** under *Output Result*. Follow that single byte through the earlier encoder listing. The guard **`if not text:`** does not return early: the string is nonempty. **`text.encode("utf-8")`** produces one byte here, whose integer value is 65. Asking for up to three bytes does not manufacture two more: the slice contains just that byte.

Annotated teaching version — guide-added comments. This runnable excerpt specializes the earlier encoder to its one-byte path, names the two indices so they can be inspected, and prints the joined result in place of the method’s return. It is not a second classroom implementation.

```python
text = "A"                                  # Start with the converter's actual input.
alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"  # Index 0 is A.
octets = text.encode("utf-8")               # b'A': exactly one byte with value 65.
encoded = []                                # New, empty list for the output pieces.
i = 0                                       # Start at the first byte.
chunk = octets[i:i+3]                       # Take up to three; only b'A' is available.
i += 3                                      # Next slice would start at 3, past the input.
integer = 0                                 # Reset the number for this chunk.
for octet in chunk:                         # A single iteration: octet is 65.
    integer = (integer << 8) + octet        # Make room for a byte; (0 << 8) + 65 = 65.
if len(chunk) == 1:                         # Exactly one byte, so this branch runs.
    integer <<= 4                           # 01000001 becomes 010000010000 (1040).
    high = (integer >> 6) & 0x3F            # Move the upper six bits down; retain 16.
    low = integer & 0x3F                    # No shift this time; the lower six also give 16.
    encoded.append(alphabet[high])          # Index 16 is Q; list now ['Q'].
    encoded.append(alphabet[low])           # Same index again; list now ['Q', 'Q'].
    encoded.append("==")                    # Add one string containing two padding signs.
print("".join(encoded))                     # Join the three pieces and print QQ==.
```

Independently verified output; the same text is visible in the converter, not in a classroom terminal:

```text
QQ==
```

**Key items to scan**

- **`A`**, **`if not text:`** — this nonempty input gets past the early return.
- **`text.encode("utf-8")`**, **`octets[i:i+3]`** — the first produces `b'A'`; the slice has one real byte, not three.
- **`(integer << 8) + octet`** — the code shifts **left**, despite “right” in the captions at 0:54:56. With an initial zero either direction would still yield zero before adding 65; the code establishes the direction.
- **`integer <<= 4`** — reassigns the number to 1040; the original input bytes are unchanged.
- **`encoded.append("==")`** — appends one list element, not two; the encoder returns **`QQ==`** after joining the pieces.

### Read the two masks one column at a time

Four appended zero bits turn the eight input bits into two six-bit groups: **`010000 010000`**. Why four? The first group takes six real bits. The second starts with the remaining two real bits, `01`, and needs four zeros on the right to reach six. These are zero *bits in the arithmetic*, not the literal equals signs in the output string.

The editor next lines up the right edges of two bit strings. The **mask** is **`0x3F`**: hexadecimal 3 is binary `0011` and hexadecimal F is `1111`, giving `00111111`, or 63. Its six low ones keep the six corresponding places; every higher place is cleared. With bitwise **`&`**, a bit survives only when both bits in that column are one. A one in the mask *copies* the input bit; it doesn't force a one into the answer.

| Step | Which value is being used? | Operation and result |
|---|---|---|
| 1. Position the upper group | `010000010000` (1040) | `1040 >> 6` drops the low six bits. Written in the editor’s 12 columns, the result is `000000010000`. The saved `integer` is still 1040. |
| 2. Mask the upper group | `000000010000` | AND with `000000111111` gives `000000010000` (16). `alphabet[16]` supplies the first Q. |
| 3. Mask the lower group | Start again with `010000010000`, **not** the shifted result | AND with `000000111111` gives `000000010000` (16). The 1 at the 1024-place is blocked; the 1 at the 16-place passes. This supplies the second Q. |
| 4. Finish the group | The list was `['Q', 'Q']` | Append `"=="` for the two positions with no further input. The next loop check is `3 < 1`, false; there is no second chunk. Only then does the method join `['Q', 'Q', '==']` and return `QQ==`. |

The matching indices are a property of this byte, not a rule that the two output letters must match. A useful check is to count the alphabet from zero: A occupies index 0, so Q occupies index 16. Neither masked result is the ASCII code for the character Q. The completed handwritten-style editor calculation is visible at 1:00:07; the 12-column leading zeros just make corresponding positions easier to compare.

**Key items to scan**

- **`010000 010000`** — the two six-bit groups after extending A's eight bits on the right.
- **Mask**, **`0x3F`** — decimal 63, six low ones. Right-align it with the number before comparing columns.
- **`&`** — `0 & 1` gives 0, `1 & 1` gives 1; any bit ANDed with 0 gives 0.
- **`(integer >> 6) & 0x3F`** — isolate the upper group without changing `integer`.
- **`integer & 0x3F`** — isolate the lower group from the original 1040. It independently yields 16.
- **`alphabet[16]`** — select Q, not the ASCII character whose numeric code is 16. Two Qs plus two padding signs give `QQ==`.

## From a CSV import to the bytes behind a character (1:00:30–1:15:50)

### A CSV file arrives with a mark at the beginning

A participant who receives CSV files at work asks whether the preceding Base64 exercise explains a difficult “binary decode” step in their workflow. The clue is the abbreviation **BOM**. After a brief detour through “bill of material,” the class identifies the intended meaning: **byte order mark**. The instructor places the problem one step *before* the Base64 exercise. To read comma-separated values, the importing program first needs to turn the incoming bytes into the intended text. Only if someone also wrapped those bytes in Base64 would a Base64 decoding step be needed. No workplace file or failing import was shown.

The instructor connects the mark to the earlier big-endian/little-endian discussion. For example, when a UTF-16 stream includes a BOM, initial hex bytes **`FE FF`** signal big-endian order; **`FF FE`** signal little-endian order. In UTF-8 the optional initial **`EF BB BF`** can identify the encoding, but cannot signal an order choice: UTF-8 has only one byte order. The instructor mentions generally assuming little-endian without a mark. That is not a portable rule: use the declared encoding or the file format’s agreement. If the encoding is entirely unknown, an absent mark does not identify it. For plain Unicode text known to use UTF-16 or UTF-32 but lacking an endian declaration, BOM, or higher-level convention, the Unicode default is big-endian. [Supplemental: Unicode Consortium, *UTF-8, UTF-16, UTF-32 & BOM*](https://unicode.org/faq/utf_bom.html).

Here is a small independently tested CSV-header example, **not the participant’s file**. Start with the UTF-8 signature followed by the ordinary text `name,price`. Decode it two ways before reading the first field:

| Decoder | First header as Python’s escaped representation | Consequence |
|---|---|---|
| `utf-8` | `'\ufeffname'` | The first header includes an invisible extra character. A lookup for exactly `name` will not match it. |
| `utf-8-sig` | `'name'` | The initial signature is consumed; the comma and the actual field names are unchanged. |

**Key items to scan**

- **BOM / byte order mark** — the initial signature at issue, not a bill of materials or a Base64 padding character.
- **`FE FF` / `FF FE`** — opposite byte orders of the UTF-16 mark; do not guess from the file extension.
- **`EF BB BF`** — UTF-8’s optional signature, not an instruction to reverse bytes.
- **`utf-8-sig`** — in the separate header example, reads `name` instead of leaving `\ufeff` attached to it.

### Why these details mattered at work

The participant uses Power Apps with SharePoint and connects the earlier Kivy layout example to time spent resizing or repositioning controls. This is a different kind of “size” from the number of bytes in a file: a layout manager can place controls for you, whereas an importer still needs to interpret the bytes it receives. The instructor’s point is practical: you may not work at the bit level every day, but a business workflow can still expose its consequences.

The participant had heard more about Java, HTML and SQL than Python at work; even a mentor wanted to learn Python. Now dashboards, queries, Power BI and especially Databricks training were making Python visible. The team was moving more work toward Databricks. The instructor suggests that comparable training might once have used Java, offers to compare the languages if useful, and favors Python for this learner’s immediate needs. That was advice about this workplace, not a prediction that every team will drop Java. The participant likens noticing Python everywhere to spotting the same car after buying one.

**Key items to scan**

- **Power Apps and SharePoint** — the participant’s setting for the layout and workflow questions; neither product was demonstrated here.
- **Kivy** — automatic sizing and positioning offered a concrete reason to care about the earlier app layout.
- **Power BI and Databricks** — analytics work and training where the participant was encountering Python.
- **Java comparison** — offered if relevant later, not a second implementation performed in class.

### Why one pound sign produces `wqM=`

The instructor invites questions about the Base64 shifts and suggests trying UTF-8 next, then returning to Base64. The converter still gives **`QQ==`** for one uppercase **`A`**. What if the input still looks like one character, but it is **`£`**? The instructor first tries [*Unicode – The World Standard*](https://home.unicode.org/), whose home page pairs symbols with their numbers, then finds a copyable pound sign on [SYMBL’s Latin-1 Supplement table](https://symbl.cc/en/unicode-table/#latin-1-supplement). The [*Pound £* entry](https://symbl.cc/en/00A3/) lists `Pound Sign`, **`U+00A3`**, and the HTML entity `&pound;`. The instructor copies the character, not the text of its identifier or its HTML spelling.

| Requested frame | Input field | Output field | What changed |
|---|---|---|---|
| 1:08:56 | `A` | `QQ==` | Starting result for the earlier ASCII example. |
| 1:10:00 | `£` | `QQ==` | New input, old output. Pasting alone has not updated the result. |
| 1:10:05 | `£` | `wqM=` | **Encode to Base64** has produced the result for the pound sign. |

On the scratchpad the instructor writes **`10100011`**: hex `A` contributes `1010` and `3` contributes `0011`. This gives the *number* assigned to the pound sign, decimal 163. Its high bit is set, so it cannot be an ASCII value. The spoken “first 127” is an off-by-one shorthand: ASCII uses **`U+0000`** through **`U+007F`**, or 128 values, and UTF-8 leaves every one unchanged. The spoken “byte for it” is also worth qualifying: `A3` alone is not the UTF-8 representation of `£`. Nor do all Unicode characters require multiple bytes in UTF-8: the `A` example needed only one. These claims concern the bytes supplied to the encoder, not how Python or the editor stores text internally. [Supplemental: RFC 3629, §§1, 3](https://www.rfc-editor.org/info/rfc3629/).

The converter’s first step is **`text.encode("utf-8")`**. That produces `C2 A3`, *two* bytes for one pound sign. The same two-byte branch of the Base64 algorithm now explains the change from two padding signs to one. The following trace uses the observed input and output; the intermediate calculations were independently checked, not printed by the classroom app. [Base64 alphabet and padding: RFC 4648, §4](https://www.rfc-editor.org/info/rfc4648/).

| Step | Value | Why it matters |
|---|---|---|
| Encode `£` as UTF-8 | `11000010 10100011` | These are the input bits for Base64, not the printed characters `C2 A3`. |
| Group from the left | `110000 · 101010 · 0011` | Six bits select each Base64 symbol; only four bits remain for the third one. |
| Add two zero bits on the right | `110000 · 101010 · 001100` | The resulting indices are 48, 42, 12: lowercase `w`, lowercase `q`, uppercase `M`. |
| Complete the four-character group | `wqM=` | One padding sign denotes that this final group came from two bytes. |

A useful check: if the encoder really had received only the byte `A3`, the Base64 output would have been `ow==` (independently verified), not the result on screen. To reverse the actual process, Base64-decode `wqM=` to recover bytes; then interpret those bytes as UTF-8 to recover the character.

**Key items to scan**

- **`A` → `QQ==`** — one ASCII input byte; also the stale output immediately after pasting the pound sign.
- **`£` / `U+00A3` / `10100011`** — glyph, Unicode number in hex notation, and binary spelling of that number. None by itself specifies a file’s byte encoding.
- **`U+0000` through `U+007F`** — inclusive ASCII range; each code point has the same one-byte value in UTF-8.
- **`text.encode("utf-8")`** — supplies two bytes, `C2 A3`, to Base64 for this input.
- **Encode to Base64 / `wqM=`** — updated converter output for `£`; `=` pads the final group.

### One Armenian letter, five byte layouts

The next selection is **`Ք`**, Armenian Capital Letter Keh. The instructor wants its number but reports that copying from the character site pastes the letter itself into the editor. That surprise separates three things: the *character* copied, the *glyph* the editor draws, and the *code point* written **`U+0554`**. The paste is reported in speech; the captured scratchpad at 1:13:00 does not display the pasted letter. The identity and number are supported by the [SYMBL page for Armenian Capital Letter Keh](https://symbl.cc/en/0554/).

At 1:14:00 the page’s **Encoding** tab shows different ways to send that same number. Read the `hex` column below as bytes in transmission order; the values were transcribed from the screen and independently checked. These are just the letter’s bytes, without a BOM.

| Encoding | Bytes (hex) | Bytes (decimal) | How to read it |
|---|---|---|---|
| UTF-8 | `D5 94` | `213 148` | Two bytes carry the letter’s bits plus UTF-8 prefix bits. |
| UTF-16BE | `05 54` | `5 84` | More significant byte of the 16-bit unit first. |
| UTF-16LE | `54 05` | `84 5` | Same unit, less significant byte first. |
| UTF-32BE | `00 00 05 54` | `0 0 5 84` | Four bytes, more significant end first. |
| UTF-32LE | `54 05 00 00` | `84 5 0 0` | Same four-byte unit in the opposite order. |

The site also has a column labeled `dec`. Why does the UTF-8 row show **54676**, while the letter’s Unicode number is **1364**? Reading `D5 94` as one unsigned big-endian integer gives 54676. *Decoding* it as UTF-8 instead gives 1364. Likewise, reading `54 05` in displayed order as a big-endian integer gives 21509; interpreting it according to its actual UTF-16LE label gives 1364 again. Changing the byte layout does not assign a new number to the letter.

The instructor calls UTF-8 the common encoding and recalls early Unicode as 16-bit. The precise distinction is that early Unicode had a 16-bit code space; today the highest code point is `U+10FFFF`, **not** a move to a full 32-bit code space. What can take one to four bytes is a scalar value *encoded in UTF-8*. UTF-16 uses one or two 16-bit units, and UTF-32 uses one 32-bit unit per scalar value. These are alternatives, not a series of upgrades a file undergoes. [Supplemental: Unicode Consortium, *UTF-8, UTF-16, UTF-32 & BOM*](https://unicode.org/faq/utf_bom.html).

**Key items to scan**

- **`Ք` / `U+0554`** — Armenian Capital Letter Keh, decimal **1364**, regardless of encoding.
- **Encoding** — the SYMBL tab comparing UTF-8, UTF-16 and UTF-32; `dec (bytes)` lists each byte individually.
- **54676** — decimal interpretation of the packed bytes `D5 94`, *not* the code point for Keh.
- **BE / LE** — most-significant or least-significant byte first within a multibyte unit, not a reversal of the bits inside each byte.

### How the decoder knows whether to read another byte

Suppose the reader is handed just **`11010101 10010100`**, the UTF-8 row for Keh. The instructor asks how it can find the end of the character. At 1:15:00 the page has the five trailing bits of the first byte highlighted. Compare what tells the reader *how many bytes to read* with what supplies the letter’s *value*:

| On screen | Separate prefix from data | Meaning in this example |
|---|---|---|
| `11010101` | `110` + `10101` | The lead prefix announces a two-byte sequence. Keep its five data bits. |
| `10010100` | `10` + `010100` | A continuation byte. Keep its six data bits. |
| Join only the data bits | `10101` + `010100` = `10101010100` | Decimal 1364, hex `554`: the reader recovers Keh. |

That trace is an independently checked explanation of the displayed bits, not execution of a classroom decoder. In valid UTF-8 a first byte shaped like `0xxxxxxx` is already a complete ASCII character. Valid leads beginning `110`, `1110`, or `11110` announce sequences of two, three, or four bytes, respectively; each required continuation begins `10`. Thus a high bit of 1 alone does **not** mean “start of a character,” and the next bit is not an independent “last byte” flag. The lead establishes the length; the rest must match. Not every combination of a prefix and data bits is legal. [Supplemental: RFC 3629, §§3–4](https://www.rfc-editor.org/info/rfc3629/).

A participant jokes about encryption. Neither the character encoding nor Base64 requires a secret key: the point here is to recover the same text, not keep it secret. The instructor now turns from the Base64 app to look for the UTF-8 app, as a preview of work to revisit in the next class.

**Key items to scan**

- **`11010101 10010100`** — two UTF-8 bytes, one Armenian letter; remove the prefixes before interpreting the value.
- **`0xxxxxxx`** — a complete ASCII value; no continuation follows for this character.
- **`110` / `1110` / `11110`** — leading patterns that announce a total length of two, three or four bytes in valid UTF-8.
- **`10`** — marks a continuation byte; it is not a new character or a final-byte flag.

## From a character to its bytes and back: the UTF-8 app

### One message, two lengths (1:15:55–1:17:32)

The new app opens with **`Hello, 🌍! Café 🚀`** already in its entry field. Its counter reads **`Characters: 16 | UTF-8 Bytes: 23`** before the results area is filled. Why aren't the counts equal? Most of the message takes one byte per character, including each space; the accented **`é`** takes two, and each emoji takes four. The GUI font shows small placeholder boxes for the emoji. That is a display issue, not evidence that their bytes were lost: the editor shows the characters themselves and the byte stream identifies them.

At 1:15:57 the GUI says **`Results will appear here...`**. At 1:16:16 the **`Process Full UTF-8 Breakdown`** control is still visible, now above four results: original text, hexadecimal byte stream, binary byte stream and decoded result. Hex and binary are two ways of writing the *same* bytes; the app hasn't encoded the message twice. Encoding here is a public representation of text, not encryption with a secret key.

Observed hexadecimal byte stream (line breaks added between character groups):

```text
0x48 0x65 0x6C 0x6C 0x6F 0x2C 0x20
0xF0 0x9F 0x8C 0x8D 0x21 0x20
0x43 0x61 0x66 0xC3 0xA9 0x20
0xF0 0x9F 0x9A 0x80
```

Observed binary byte stream, regrouped at the same character boundaries as the hexadecimal stream:

```text
01001000 01100101 01101100 01101100 01101111 00101100 00100000
11110000 10011111 10001100 10001101 00100001 00100000
01000011 01100001 01100110 11000011 10101001 00100000
11110000 10011111 10011010 10000000
```

| Part of original message | What the GUI's bytes represent | What the decoded text preserves |
|---|---|---|
| `Hello, ` | Seven single-byte characters, starting with `01001000` | Capitalization, comma and trailing space |
| `🌍! ` | `F0 9F 8C 8D` for the globe, then `21 20` | One globe, one exclamation mark, one space |
| `Café ` | Three ASCII letters, `C3 A9` for `é`, then `20` | The accent is part of the text, not stripped off |
| `🚀` | `F0 9F 9A 80`; the final byte displays as `10000000` | One rocket, not four separate characters |

The instructor describes a Kivy app class with a **`build`** method. The visible editor shows *how the initial display is constructed*, not a console printout. The correction in the narration matters: the GUI is class-based, but the encoding and decoding operations in this project are functions; there need not be a separate codec object to create. Compare the earlier Base64 program, where the encoder was explicitly a class method.

Annotated teaching version — guide-added comments; selected lines from the GUI construction in **`utf8.py`**, with calls condensed. This fragment belongs inside the app's build method; it is not a standalone app.

```python
rootLayout = BoxLayout(orientation='vertical', padding=15, spacing=15)  # Stack controls with space around and between them.
rootLayout.add_widget(Label(text="[b]UTF-8 Encoding & Decoding[/b]", markup=True,
                            font_size=20, size_hint_y=None, height=40))  # Add a fixed-height bold heading.
rootLayout.add_widget(Label(text="Enter text (try emojis, accents, or symbols):",
                            size_hint_y=None, height=25))  # Add the prompt above the text field.
self.messageText = TextInput(text="Hello, 🌍! Café 🚀", multiline=False,
                             size_hint_y=None, height=45)  # Create a new one-line field containing the sample.
self.messageText.bind(text=self.messageChange)  # Register the handler for later text changes; do not call it here.
rootLayout.add_widget(self.messageText)         # Put that same field into the vertical layout.
```

The next visible construction starts a statistics label with empty text and markup enabled. The running GUI *does* display the counts, but the code that fills the label and handles the breakdown button is not in this view.

**Key items to scan**

- **`Hello, 🌍! Café 🚀`** — the editor's starting string; **`Characters: 16 | UTF-8 Bytes: 23`** counts characters and encoded bytes respectively.
- **`é`** — a single character in this sample, represented by two bytes, `C3 A9`.
- **`Results will appear here...`** — initial status; **`Process Full UTF-8 Breakdown`** — the action associated with the four-part display.
- **`utf8.py`**, **`build`** — the app's layout code creates widgets; it is distinct from the functions that transform the data.
- **`BoxLayout`**, **`add_widget`** — create the vertical container, then place each control in it. **`TextInput`** is the editable field; **`bind`** connects its text changes to a handler.

### How the encoder packs the bits (1:16:35–1:17:29)

Start with `é`. The character has one code point, U+00E9, but its value 233 does not fit in the seven data bits of a one-byte UTF-8 character. The **`for char in text`** loop obtains that value with **`ord(char)`**. The first matching branch selects the width; the shifts and masks divide the value into payload groups. The encoder adds the lead prefix to the highest group and `10` to each six-bit group that follows. A four-byte character such as the globe uses the same rule with two more groups than `é`.

**Screen code with guide-added comments:** the entire standalone **`encode(text)`** function is visible at `01-16-35.200_utf8-encoder.png`. Only the last two calls below are guide fixtures. `€` and `😀` are not part of the class GUI string.

```python
def encode(text):  # Return bytes for the whole string, not one printed line per character.
    encodedOctets = []  # Start one empty list per call; keep it across iterations.
    for char in text:  # Visit one character at a time.
        codePoint = ord(char)  # Get its integer code point: 233 for é.
        if codePoint <= 0x7F:  # ASCII fits in seven bits.
            encodedOctets.append(codePoint)  # A: add the single value 0x41.
        elif codePoint <= 0x7FF:  # Up to eleven bits: use two bytes.
            b1 = 0xC0 | (codePoint >> 6)  # é: prefix 110 plus the top five bits gives C3.
            b2 = 0x80 | (codePoint & 0x3F)  # Prefix 10 plus the low six bits gives A9.
            encodedOctets.extend([b1, b2])  # Add two integers to the same list.
        elif codePoint <= 0xFFFF:  # Use three bytes in this range; this test includes surrogates.
            b1 = 0xE0 | (codePoint >> 12)  # €: prefix 1110 plus its high four bits gives E2.
            b2 = 0x80 | ((codePoint >> 6) & 0x3F)  # Middle six bits with prefix 10 give 82.
            b3 = 0x80 | (codePoint & 0x3F)  # Low six with prefix 10 give AC.
            encodedOctets.extend([b1, b2, b3])  # Extend the list in stream order.
        elif codePoint <= 0x10FFFF:  # Remaining Python code points take four bytes here.
            b1 = 0xF0 | (codePoint >> 18)  # 🌍: prefix 11110 plus its high bits gives F0.
            b2 = 0x80 | ((codePoint >> 12) & 0x3F)  # Next six payload bits with prefix 10 give 9F.
            b3 = 0x80 | ((codePoint >> 6) & 0x3F)  # Next six with prefix 10 give 8C.
            b4 = 0x80 | (codePoint & 0x3F)  # Final six with prefix 10 give 8D.
            encodedOctets.extend([b1, b2, b3, b4])  # Add all four bytes of the globe; the next character follows.
    return bytes(encodedOctets)  # After the loop, convert the accumulated integers to bytes.

print(encode("Aé€😀").hex(" "))  # Driver: hexadecimal display of all four widths.
print(encode("🌍").hex(" "))     # Driver: check the globe group from the GUI.
```

Independent fixture output, not classroom console output:

```text
41 c3 a9 e2 82 ac f0 9f 98 80
f0 9f 8c 8d
```

On the first call the list gains `41` for `A`, then `C3 A9` for `é`, then `E2 82 AC` for `€`, then `F0 9F 98 80` for `😀`. No branch returns early; the function makes one bytes object from all ten integers. With empty text the loop runs zero times and the result is empty bytes. The second call starts a new list.

**Scalar-value limitation:** use Unicode scalar values (code points other than U+D800–U+DFFF) for this encoder. It is **not a validator**: its three-byte test also accepts a surrogate. In an independent check, passing a one-character string made with `chr(0xD800)` produced `ed a0 80`; Python's strict UTF-8 encoder rejected that same string. Matching correct examples does not make those surrogate bytes valid UTF-8.

**Key items to scan**

- **`encode(text)`**, **`for char in text`**, **`ord(char)`** — visit characters and select a width using each integer code point, not its displayed shape.
- **`>>`**, **`& 0x3F`** — shift a payload group into place and retain its low six bits.
- **`|`**, **`0xC0`**, **`0xE0`**, **`0xF0`** — combine the payload with the two-, three- or four-byte lead prefix; **`0x80`** supplies the `10` continuation prefix.
- **`append`**, **`extend`** — add one integer or several integers to the same list in order; **`return bytes(encodedOctets)`** converts the finished list once. `.hex(" ")` is a separate display operation.

### How the decoder recognizes the first byte (1:17:34–1:21:53)

The standalone **`decode(octetStream)`** function faces the reverse problem: it accepts a byte stream with no separators between characters. The GUI shows text, bytes and a decoded result; the wiring that passes data between its controls and the function is not visible here. This function is neither the earlier Base64 class method nor its Kivy button callback. Start at the first unread byte. The instructor looks up uppercase **`H`** on [the ASCII table shown in class](https://www.ascii-code.com/): decimal 72, octal 110, hexadecimal 48, binary `01001000` (1:19:27). To find out whether that byte already represents a complete character, test just its highest bit. Hexadecimal **`0x80`** is binary `10000000`: the `8` stands for `1000` and the `0` for `0000`.

| Write the bytes in matching columns | What happens |
|---|---|
| `01001000` | `H` has a zero in its highest position. |
| `10000000` | The mask keeps only that position. |
| `00000000` | Bitwise AND finds no column in which both operands have a one. |

A student asks whether a one on the *top* row carries down. It does not: swapping the rows gives the same result. AND examines each column independently. No bits are added or carried. Since the masked result is zero, the decoder uses **the original byte, 72**, as the code point, not the zero returned by the mask. This is how an ASCII character can be read directly from UTF-8; ASCII characters are also Unicode characters.

For **`0xE0`**, the first hex digit is E (14, one below F), so the mask starts `111`. The decoder compares the masked byte with **`0xC0`**, which starts `110`: it is asking for a particular lead pattern, not just any set high bit. The next guard asks whether *enough* bytes remain, not whether the next byte is *valid*. For instance, `C3 41` has the right length but `41` does not start `10`. The teaching adaptation below returns `Á` for this malformed pair, whereas strict UTF-8 rejects it. Use valid sequences for character reconstruction.

**Annotated teaching adaptation, not the original function or a classroom run.** The one-, two- and three-byte cases, length guards, unmatched-byte handling, shared append and return are transcribed operations. The four-byte branch also visibly loads all three following bytes, starts calculating the code point (line 58) and then advances **`i += 4`** (59). Since the end of that expression is outside the recorded desktop, this fixture deliberately *stops* on a complete four-byte group instead of guessing it. A truncated four-byte group still follows the displayed guard. The changed function name, explicit stop, comments and print calls are guide additions.

```python
def read_visible_paths(octetStream):  # Teaching adaptation: only complete one-, two- and three-byte characters are decoded here.
    chars = []  # New list of characters for this call.
    i = 0  # Start at the first unread byte.
    while i < len(octetStream):  # Stop when no byte remains.
        b = octetStream[i]  # Bytes indexing returns an integer.
        if (b & 0x80) == 0:  # High bit clear? This is ASCII.
            codePoint = b  # Keep the original 72 for H, not the masked zero.
            i += 1  # Consume one byte.
        elif (b & 0xE0) == 0xC0:  # High bits 110? Two bytes total.
            if i + 1 >= len(octetStream):  # Is the second byte missing?
                break  # Leave the loop; keep the text already collected.
            b2 = octetStream[i+1]  # Read the next integer; no check for the 10 prefix.
            codePoint = ((b & 0x1F) << 6) | (b2 & 0x3F)  # C3 A9: (3 << 6) | 41 = 233.
            i += 2  # Consume both bytes.
        elif (b & 0xF0) == 0xE0:  # High bits 1110? Three bytes total.
            if i + 2 >= len(octetStream):  # Check that both later positions exist.
                break  # Stop on the unfinished character.
            b2 = octetStream[i+1]  # For € read 82 hex.
            b3 = octetStream[i+2]  # Then read AC hex.
            codePoint = ((b & 0x0F) << 12) | ((b2 & 0x3F) << 6) | (b3 & 0x3F)  # Join the 4-, 6- and 6-bit payloads.
            i += 3  # Advance past the entire three-byte character.
        elif (b & 0xF8) == 0xF0:  # High bits 11110? Four bytes total.
            if i + 3 >= len(octetStream):  # Check that the fourth position exists before any read.
                break  # Truncated: stop and return the prefix below.
            b2 = octetStream[i+1]  # Read the second byte.
            b3 = octetStream[i+2]  # Read the third byte.
            b4 = octetStream[i+3]  # Read the fourth byte.
            raise NotImplementedError("line 58 clipped")  # Guide-only stop, not a classroom exception.
        else:  # No lead pattern matched.
            i += 1  # Skip one unrecognized byte.
            continue  # Do not append a character for it.
        chars.append(chr(codePoint))  # All completed branches meet here, once per character.
    return "".join(chars)  # Outside the loop: return the accumulated text, even after a break.

print(read_visible_paths(bytes.fromhex("48 C3 A9")))        # H and é side by side.
print(read_visible_paths(bytes.fromhex("48 C3 A9 E2 82 AC")))  # Add € to exercise three bytes.
print(read_visible_paths(bytes.fromhex("48 FF 21 F0 9F")))  # Skip FF; stop on the incomplete four-byte group.
```

Independent fixture output:

```text
Hé
Hé€
H!
```

| Next index in `48 C3 A9 E2 82 AC` | Test and reconstruction (hex unless noted) | Character and next index |
|---|---|---|
| 0 | `48 & 80 = 00`; retain the original `48`. | `H` → 1 |
| 1 | `C3 & E0 = C0`. Shift the five-bit payload `03` left by six, then join the six-bit payload `29`: `C0` OR `29` = `E9`. | `é` → 3 |
| 3 | `E2 & F0 = E0`. From `E2 82 AC`, the shifted first two contributions are `2000` and `80`; the last is `2C`. Their OR is `20AC` (8364 decimal). | `€` → 6 |
| 6 | No byte remains; join the three collected characters. | `Hé€` |

The third call illustrates the difference between **`continue`** and **`break`**. After `H`, `FF` takes the unmatched-lead **`else`**: increment the index and continue without appending, so `21` is still read as `!`. At `F0` only one following byte remains. The guard breaks out; **`return "".join(chars)`** returns `H!`, without trying to read missing positions. These are properties of this teaching implementation, not a recommended malformed-input policy. In contrast, the *recorded GUI* displayed a decoded result for the full sample, including the positions occupied by both emoji. That display does not supply the missing source suffix or demonstrate any of these malformed-input tests.

At 1:18:00 the editor briefly switches to **`base64App.py`**: its **`text.encode("utf-8")`** supplies the bytes for Base64's earlier three-byte chunk loop. A Base64 chunk boundary is not a UTF-8 character boundary; the earlier Base64 walkthrough shows the packing step.

**Key items to scan**

- **`decode(octetStream)`** — standalone function from bytes to text; callback wiring is separate.
- **`H`**, **`0x80`** — `01001000` has a clear high bit, so the ASCII branch retains code point 72.
- **`0xE0`**, **`0xC0`** — mask the top three bits, then require `110` for the two-byte case.
- **`0x1F`**, **`0x3F`**, **`<< 6`** — retain five lead bits and six following bits, shifting to join them. The three-byte case keeps four lead bits and joins two six-bit groups.
- **`i`**, **`i += 4`** — track the next unread position; the historical four-byte branch consumes four bytes when complete.
- **`else`**, **`continue`**, **`chars.append(chr(codePoint))`** — an unmatched lead skips one byte without appending; a completed character reaches the common append.
- **`i + 3 >= len(octetStream)`**, **`break`**, **`return "".join(chars)`** — an unfinished four-byte group stops the scan; the return preserves the completed prefix.
- **`Hé`**, **`Hé€`**, **`H!`** — results of separate fixtures, not classroom console output.
- **`base64App.py`**, **`text.encode("utf-8")`** — the earlier program consumes UTF-8 bytes as input to Base64.

## Two meanings of AND, and two meanings people give “equals”

### Start with the question, then choose the operator (1:21:53–1:26:24)

A student compares this work with writing a long Excel formula using IF, AND and OR. Can a condition be expressed more clearly? The instructor first separates two different questions a program might ask. “Are these values equal **and** is the first less than a limit?” combines the outcomes of comparisons. “Which bit positions do these two integers share?” produces an *integer bit pattern*. Excel is a spoken point of comparison here; the screen shows an editor, not an Excel workbook or a tested spreadsheet formula. The suggestion about running a condition “circular” is not established as a technique.

Annotated teaching version — guide-added comments and values; runnable versions of the two headers in the scratchpad (1:24:56). The class did not supply bodies or run these examples on screen.

```python
a, b, c = 2, 3, 3                   # Guide fixture: binary a=10, b=11.
print(a == b and a < c)             # False: 2 is not 3, so Python need not test 2 < 3.
print(a & b)                        # 10 AND 11 in binary gives 10: integer 2.
if a & b:                           # The integer result is nonzero, so this body runs.
    print('shared bit')             # Displays a message, not the bit pattern itself.
b = 2                               # Change b only; a and c are still 2 and 3.
print(a == b and a < c)             # Now 2 == 2 and 2 < 3 are both true.
```

Independently verified fixture output:

```text
False
2
shared bit
True
```

Both comparisons in this example yield Boolean values. Python's **`and`** tests the left operand first and skips the right operand when the left is false; more generally it returns an operand, not necessarily a Boolean. Integer **`&`** instead applies the one-column rule from the mask demonstration. In **`if a & b:`**, the conditional tests whether that integer result is nonzero. Neither operation means “check whether the two integers are equal.”

**Key items to scan**

- **`and`** — joins conditions; in `a == b and a < c` the second comparison matters only if the first is true.
- **`&`** — intersects integer bit positions: `2 & 3` yields `2`, not `True` or `False` as a comparison result.
- **`if a & b:`** — takes the body when the integers share at least one set bit; no equality is required.

### Assignment has a direction (1:26:24–1:29:39)

The student singles out double equals as a habit that is difficult to learn after years of writing mathematical equations. If **`x = 3`** means “let the name point to the value on the right,” reversing the two sides does not express the same action. A literal like `3` is not a place where Python can store a new binding. By contrast, **`x == 3`** asks a question about values. For these integers, `3 == x` asks the same question; neither comparison changes the name. Equality here does not mean Python's object-identity test.

Annotated teaching version — guide-added comments and driver:

```python
x = 3              # Bind the name x to the integer 3. The assignment prints nothing.
print(x == 3)      # Compare by value, then print True.
print(3 == x)      # Reverse the integer comparison; still True.
```

Independent fixture output: two lines, `True` followed by `True`.

The scratchpad then deliberately shows **`3 = x`** and **`if a = b and a < c:`**. These are valuable mistakes to recognize, **not valid Python to emulate**. The instructor warns that a mistaken assignment in a test can produce a surprising branch. In the Python syntax actually shown, the consequence is earlier: the parser raises **`SyntaxError`**, so there is *no assignment, comparison or branch at all*. A separate compile check of each invalid form confirms that failure; no classroom traceback is visible. With the valid double-equals header and a body, both comparisons must be true for the body to run.

**Key items to scan**

- **`x = 3`** — assigns the right-hand value to a writable name; produces no printed output.
- **`x == 3`** — asks whether the current value is equal to 3; the result here is `True`.
- **`3 = x`** — invalid assignment target. **`if a = b and a < c:`** — invalid ordinary assignment in a Python condition. Both fail with **`SyntaxError`** before execution.

### Closing: resources and what to take into the next class (1:29:49–1:32:58)

The instructor welcomes the questions because they reveal what not to assume about a learner's background. The class informally estimates roughly ten or eleven sessions remaining; the estimate before break shifts from three to perhaps four. This exchange is not a syllabus, a confirmed calendar or an assignment deadline. The instructor had said the UTF-8 app would be revisited. A student describes learning from the videos, W3Schools, practice and a book referred to in speech as “teach yourself visually in Python,” drawing on a similar approach to SQL. The exact edition is not identified. A second, more descriptive book is mentioned but not identified; the invitation is to post the title later, not a confirmed reading assignment.

A closing Discord view (1:32:38) also shows historical links: the [Kivy account](https://github.com/kivy/), [main Kivy repository](https://github.com/kivy/kivy), and [button widget source](https://github.com/kivy/kivy/blob/master/kivy/uix/button.py). The displayed note explains the path: the repository contains the `kivy` source folder; `uix` holds interface elements; inside is `button.py`. Another dated post names [the course demo repository](https://github.com/joes-ta/F26Demo), says `binOp.py` and `base64App.py` were pushed, and warns that the committed Base64 example was left broken at the end of that earlier session. These are screen-only resource notes, not a new commit, a present-day repository check or a claim that the UTF-8 GUI failed.

**Key items to scan**

- **W3Schools, class videos and the visual Python reference** — student-described ways to revisit syntax and try examples, not prescribed textbook requirements.
- **`kivy/uix/button.py`** — follow the account → repository → source folder → interface folder → actual widget implementation.
- **`binOp.py`**, **`base64App.py`** — names in the earlier repository post; saving a historical checkpoint does not guarantee that the code worked.

The student leaves saying the discussion got them thinking. The technical question to carry forward is specific: when a familiar symbol appears, ask what goes in and what comes back. Here the same `H` can be a character, the byte `0x48`, or the input to a mask that returns zero. Knowing which value a line produces is what makes the next decoding step predictable.

## Syntax to remember | Choose by the job

| Pattern | What it does here |
|---|---|
| `bind(on_press=...)` | Give the button a callable Kivy can invoke later; calling it during setup would read the input too early. |
| `text.encode("utf-8")` / `octetArray.decode("utf-8")` | Move in opposite directions between Python text and UTF-8 bytes. Neither call by itself converts to or from Base64. |
| `strip()` / `rstrip("=")` | Remove surrounding whitespace first; remove trailing padding separately. Neither removes a space in the middle. |
| `& 0x3F` / `& 0xFF` | Keep six low bits for a Base64 alphabet index, or eight low bits for a decoded byte. |
| `and` / `&` | Combine truth tests, or intersect the set bits of two integers. `&` does not ask whether the whole operands are equal. |
| `=` / `==` | Assign to a target, or compare two values. A quoted `"="` is data, not either operation. |

## Knowledge check | Follow the data without running the app

1. The converter displays `SSBkb24ndCBrbm93` as input and `I don't know` as output. Which direction did the conversion take? Why would clicking Decode on the original sentence not test the round trip?
2. You drag the app window wider, then press Encode. Which job belongs to the layout and which belongs to the callback? Where must the callback look for the *current* input?
3. Trace `A` from UTF-8 byte to both Base64 alphabet positions. When does `=` enter the calculation?
4. A student predicts that `£` will also produce two padding characters because it is one symbol. Identify the first wrong assumption and use the classroom result to check it.
5. The UTF-8 app shows 16 characters but 23 bytes. How can a decoder recognize that the first byte, `0x48`, stands alone? Why would a logical truth test on `0x48` not tell you the same thing?
6. A colleague says a CSV is “UTF with a BOM.” What has the colleague told you, and what would you inspect before deciding how to read it? Would Base64 decoding be the first step?

### Answer key

1. **Base64 to text.** The encoded string is the input; the sentence is the recovered output. A round trip feeds the *encoder's result*, not its original input, to the decoder. The sentence contains spaces and an apostrophe that are not symbols of the standard Base64 alphabet. How a particular decoder treats invalid input is a separate question.
2. **Layout handles geometry; the callback handles the press.** A layout can redistribute the space around its child widgets without converting any text. The bound handler reads the input widget's current text, invokes the conversion in the selected direction, and updates the result. Reading once during construction would capture the initial text rather than the learner's later edit.
3. **`A` → `0x41` → `01000001`.** The one-byte branch shifts left four places, giving `010000 010000`. Both six-bit groups are 16; with `A` at index zero, index 16 is `Q`. The app appends `==` to complete the four-position output, giving `QQ==`. Neither `=` is looked up in the alphabet or stored in the input byte.
4. **Count encoded bytes, not visible symbols.** `£` is U+00A3. In UTF-8 it occupies two bytes, `C2 A3`, so the final group needs three alphabet symbols and one padding character. The observed result `wqM=` fits that prediction. Using `A3` alone would start the Base64 calculation with the wrong bytes.
5. **Test the leading bit.** The first byte is `01001000`. Bitwise `0x48 & 0x80` gives zero, so it matches the single-byte UTF-8 form `0xxxxxxx`: `H`. A truth test on `0x48` only tells you it is nonzero; it does not isolate one position. Later multibyte sequences account for the extra bytes in the displayed message.
6. **Inspect the bytes and the file's declared encoding.** BOM means *byte order mark*, not a Base64 wrapper. A leading signature can help identify the encoding and, for UTF-16 or UTF-32, the byte order. UTF-8 has no endian choice; its optional BOM acts as a signature. A `.csv` extension tells you about the intended tabular text format, not how its text was encoded. Only add a Base64 step if the data actually has that outer encoding.

## Library of definitions

| Term | Meaning in this lesson |
|---|---|
| Octet / byte | An eight-bit unit. The Base64 encoder groups up to three input bytes at a time. |
| ASCII | The seven-bit character set in which uppercase `A` has value 65. UTF-8 preserves these characters as single bytes. |
| Unicode code point | A number assigned to a character, such as U+00A3 for `£`. It is not itself the sequence of bytes in a UTF-8 file. |
| UTF-8 | A variable-length Unicode encoding. For the same text, “how many code points?” and “how many encoded bytes?” may have different answers. |
| Base64 | A binary-to-text encoding. Each six-bit group selects a printable alphabet character; encoding does not make the content secret. |
| Alphabet / index | An ordered lookup sequence and a zero-based position in it. In the standard alphabet, index 16 selects `Q`. |
| Padding | Terminal `=` markers in padded Base64. They distinguish the shortened final input groups; they do not encode extra input bytes. |
| Slice / chunk | `octets[i:i+3]` takes the available bytes starting at `i` and stopping before `i+3`. The last slice can be short. |
| Accumulator | In the encoder, the integer that combines successive input bytes before six-bit fields are selected. |
| Shift | Moving bits left creates room on the right; moving right brings a higher field down to the positions a mask will examine. |
| Bit mask | A bit pattern used to keep or test selected positions; `0x3F` preserves the low six bits under `&`. |
| Hexadecimal | Base-16 notation; one hexadecimal digit represents four bits. A leading `0x` marks a Python hexadecimal integer literal. |
| Lead / continuation byte | The first byte of a UTF-8 sequence announces its length. Following bytes in a multibyte sequence have the prefix `10`. |
| Endianness / BOM | Endianness orders bytes of a multibyte unit. A byte order mark is an initial text-encoding signature; these are related but not interchangeable terms. |
| Widget / layout | A widget is an interface object such as a button or text field. A layout is a widget that positions its children. |
| Event / callback | A button press is an event. The bound callback is the callable Kivy invokes when that event occurs. |
| Round trip | Encode a known input, then decode **that result** and compare the recovered value with the original. |
| Assignment / equality | `=` assigns a value to a target in Python; `==` compares values and produces a Boolean result. Neither is the `=` padding character inside a Base64 string. |

## Reference desk | Where to check a rule

These references clarify technical behavior; they are not independent evidence of a classroom edit or run.

- **Base64 alphabet and final groups:** [RFC 4648 §§3–4](https://www.rfc-editor.org/rfc/rfc4648#section-4). Distinguish the added zero bits used to form a six-bit index from the `=` characters printed as padding.[1]
- **UTF-8 byte structure and signature:** [RFC 3629 §§3, 6](https://www.rfc-editor.org/rfc/rfc3629#section-3). Prefix bits identify sequence forms; no endian choice is needed for UTF-8.[2]
- **Python's text encodings:** [Python `codecs` documentation](https://docs.python.org/3/library/codecs.html#encodings-and-unicode) distinguishes encoding text into bytes from decoding bytes back to text.[3]
- **Kivy's application lifecycle:** [Kivy `App` documentation](https://kivy.org/doc/stable/api-kivy.app.html) explains the `build()` override and the root widget used by the application.[4]

### Sources

[1] https://www.rfc-editor.org/info/rfc4648

[2] https://www.rfc-editor.org/info/rfc3629

[3] https://docs.python.org/3/library/codecs.html

[4] https://kivy.org/doc/stable/api-kivy.app.html

## Historical screen evidence | Code, interface states, and scratch work

Read numbered listings as records of the editor or scratchpad, not as standalone programs. Do not type the gutter numbers. The headings and frame targets identify the state; a later display cannot prove what was saved or run in an earlier window.

### Opening demonstration: shell evidence and the converter window

The strongest historical record here is the interface itself, not a complete listing of its implementation. The following line numbers are editorial and are not part of the commands. In `00-01-00_minute.png`, the instructor leaves the prior environment, enters the project directory and activates its virtual environment. The changed prompt confirms which environment was active.

```text
1  (venv) PS C:\com\joesta> deactivate
2  PS C:\com\joesta> cd .\Numerical\
3  PS C:\com\joesta\Numerical> .\venv\Scripts\activate
4  (Numerical) PS C:\com\joesta\Numerical>
```

In `00-02-00_minute.png`, the Kivy and Python messages above the script command are already in scrollback. A new prompt is visible immediately below the command. These lines establish the historical environment and script path, not that this particular command produced the later GUI results. The editor behind the shell is `base64App.py`; its top imports and an `if len(chunk) == 1:` line are visible, but the shell and later converter cover most of the implementation in these opening views.

```text
1  [INFO   ] [Base        ] Start application main loop
2  [INFO   ] [Base        ] Leaving application in progress...
3  (Numerical) PS C:\com\joesta\Numerical> .\src\base64App.py
4  (Numerical) PS C:\com\joesta\Numerical>
```

What to match to the source: `00-10-00_minute.png` shows the hints `Enter text to encode or Base64 string to decode...` and `Result will appear here...`. `00-10-33_event.png` and `00-11-10_event.png` have the same plain-text input but different lower fields; the latter reports `' '` as the invalid character. `00-11-37_event.png` has the encoded value in the upper field and the original phrase below. The three short-input tests appear in `00-13-00_minute.png`, `00-14-40_event.png` and `00-14-51_event.png`; the last is **not** a screen showing the no-padding control `YWJj`.

### `base64App.py`: the converter window and its handlers (editor lines 88–214)

**Assembled visual reconstruction.** These overlapping views of the same-named editor tab show the app subclass from its declaration to its launcher. The gutter numbers are for reference, not Python to type. The visible overlaps agree; the stills do not establish a file save or uninterrupted execution. Two right-edge omissions remain at lines 143 and 206. Source anchors below are requested frame times, not precise edit times.

**Window and input (88–121).** Frames `00-16-00_minute.png`, `00-18-00_minute.png`, `00-20-00_minute.png`, `00-22-00_minute.png`, `00-23-00_minute.png`. The end of line 92 is visible at 0:16:00 and 0:18:00 despite a webcam overlay in other views. Line 114 is recovered from 0:23:00 where the full hint is above that overlay.

```text
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
116 |             font_size="24sp",
117 |             background_normal="",
118 |             background_color=(0.1, 0.1, 0.1, 1),
119 |             foreground_color=(0.9, 0.9, 0.9, 1)
120 |         )
121 |         rootLayout.add_widget(self.inputText)
```

1. Lines 88–92 set the window title and construct the vertical container. At 0:20:00 the selection covers **`Label(...)`** at 94–100: it is one argument inside the outer call, not one widget per keyword.
2. Lines 103–121 place a caption above the persistent input field. At 0:24:15 the title bar and both labels are distinguishable; the empty input shows the hint, not a conversion result.

**Two action buttons, then the feedback row (122–158).** Frames `00-23-00_minute.png`, `00-26-00_minute.png`, `00-27-00_minute.png`, `00-30-00_minute.png`. The editor cuts off line 143 immediately after the visible `h`; its remaining argument text is not reconstructed.

```text
122 |
123 |         encodeLayout = BoxLayout(
124 |             orientation="horizontal", spacing=10, size_hint_y=None, height=45
125 |         )
126 |
127 |         encodeButton = Button(
128 |             text="Encode to Base64",
129 |             background_color=(0.2, 0.6, 1.0, 1.0)
130 |         )
131 |         encodeButton.bind(on_press=self.encode)
132 |
133 |         decodeButton = Button(
134 |             text="Decode from Base64",
135 |             background_color=(0.2, 0.8, 0.4, 1.0)
136 |         )
137 |         decodeButton.bind(on_press=self.decode)
138 |
139 |         encodeLayout.add_widget(encodeButton)
140 |         encodeLayout.add_widget(decodeButton)
141 |         rootLayout.add_widget(encodeLayout)
142 |
143 |         outputHeaderLayout = BoxLayout(orientation="horizontal", size_hint_y=None, h[remainder not visible]
144 |         outputHeaderLayout.add_widget(
145 |             Label(
146 |                 text="Output Result:",
147 |                 halign="left",
148 |                 valign="middle",
149 |             )
150 |         )
151 |         self.statusLabel = Label(
152 |             text="",
153 |             color=(0.3, 1.0, 0.3, 1.0),
154 |             halign="right",
155 |             valign="middle",
156 |         )
157 |         outputHeaderLayout.add_widget(self.statusLabel)
158 |         rootLayout.add_widget(outputHeaderLayout)
```

1. Lines 123–141 lay out and bind two distinct controls, then add their common parent to the root. In the foreground app at 0:24:15, Encode is on the left and Decode on the right.
2. Lines 143–158 put a fixed caption and an initially blank status in another row. The later callbacks keep a reference to the status label; they do not have to construct it again.

**Result viewport, copy button and return (159–183).** Frames `00-28-00_minute.png`, `00-28-12_event.png`, `00-24-00_minute.png`, `00-31-18_event.png`.

```text
159 |
160 |         outputScroll = ScrollView(size_hint_y=0.3)
161 |         self.outputLabel = TextInput(
162 |             readonly=True,
163 |             multiline=True,
164 |             hint_text="Result will appear here...",
165 |             font_size="24sp",
166 |             background_normal="",
167 |             background_color=(0.1, 0.1, 0.1, 1),
168 |             foreground_color=(0.9, 0.9, 0.9, 1)
169 |         )
170 |         outputScroll.add_widget(self.outputLabel)
171 |         rootLayout.add_widget(outputScroll)
172 |
173 |         copyButton = Button(
174 |             text="Copy Output to Clipboard",
175 |             size_hint_y=None,
176 |             height=45,
177 |             background_color=(0.9, 0.6, 0.1, 1.0)
178 |         )
179 |         copyButton.bind(on_press=self.copyOutput)
180 |         rootLayout.add_widget(copyButton)
181 |
182 |         return rootLayout
183 |
```

1. Lines 160–171 create two objects: the output text field is the child of the viewport, which is a child of the root. The result widget’s name ends in **`Label`**, but its constructor is **`TextInput`**.
2. Lines 173–182 add the bottom button and return the tree. At 0:29:03, a foreground app shows long text in both fields; discussion distinguishes scrolling with the wheel from seeing a scrollbar.

**Press handlers and launch guard (184–214).** Frames `00-24-00_minute.png`, `00-31-00_minute.png`, `00-31-18_event.png`, `00-32-00_minute.png`. The partial prefix at 206 is all that the right edge shows; the message displayed by the exception handler above it does not recover its missing suffix.

```text
184 |     def encode(self, instance):
185 |         source = self.inputText.text
186 |         try:
187 |             encoded = Base64.encode(source)
188 |             self.outputLabel.text = encoded
189 |             self.statusLabel.text = ""
190 |         except Exception as e:
191 |             self.outputLabel.text = f"Encoding Error: {str(e)}"
192 |             self.statusLabel.text = ""
193 |
194 |     def decode(self, instance):
195 |         source = self.inputText.text
196 |         try:
197 |             decoded = Base64.decode(source)
198 |             self.outputLabel.text = decoded
199 |             self.statusLabel.text = ""
200 |         except Exception as e:
201 |             self.outputLabel.text = f"Decoding Error: {str(e)}"
202 |             self.statusLabel.text = ""
203 |
204 |     def copyOutput(self, instance):
205 |         data = self.outputLabel.text
206 |         if data and not data.startswith("Encoding Error") and not data.startswith("D[remainder not visible]
207 |             Clipboard.copy(data)
208 |             self.statusLabel.text = "Copied to clipboard!"
209 |         else:
210 |             self.statusLabel.text = "Nothing valid to copy!"
211 |
212 |
213 | if __name__ == "__main__":
214 |     Base64App().run()
```

1. Lines 184–202 both read the upper field, invoke the appropriate conversion class method, and update the lower field. A caught exception replaces the result with a direction-specific error message. Either path clears the status label.
2. Lines 204–210 instead read the *lower* field. Depending on the guard, they copy its contents and show success, or leave the clipboard alone and report that nothing valid is available. These are branches in the editor, not evidence of two additional runs.
3. Lines 213–214, although written last, start the app when the file is the main program. The shell partly exposed at 0:18:00 shows Kivy v2.3.1 and Python v3.12.7 log entries and a PowerShell prompt; the command line for opening the window is not legible in these views. No shell invocation is reconstructed from that prompt.

### `base64App.py` — complete Base64 conversion class (editor lines 1–87)

The numbered listing assembles overlapping editor views: Kivy imports at lines 1–7, the conversion class at 10–85, and two blank lines before the interface class. The numbers and bars identify editor positions; they are not program text. The GUI press handlers are outside this listing.

**Frame provenance (requested seek targets, not run times):**

- `00-33-00_minute.png` supplies the imports, class, guard and start of the loop; `00-33-08_event.png` makes the end of the alphabet clear. `00-34-00_minute.png` and `00-36-00_minute.png` corroborate the slice/packing overlap. `00-44-00_minute.png` shows lines 30–40 without the webcam hiding the lookup expressions. The right end of line 42 is readable at the bottom of `00-35-00_minute.png`; lines 43–45 are readable beneath the overlay in `00-35-34_event.png`. `00-37-00_minute.png`, `00-38-00_minute.png` and `00-46-00_minute.png` show the join, method boundary and trimming statement.
- `00-15-42_event.png` overlaps the encoder/decoder boundary and shows the start of the decoder; `00-17-00_minute.png` and `00-17-10_event.png` show the byte-extraction branches and UTF-8 return during discussion of Kivy. Those frames are editor evidence, not a narrated decoder run.
- In `snapshots/recovery31_events/`, `00-32-53.000_base64-guard.png` shows lines 49–60; `00-32-54.800_base64-unpack.png` overlaps at line 58 and continues through blank lines 86–87. The second frame shows the entire exception at 61, the shift and addition at 70–71, and blank line 72. These two views establish the decoder text independently of any test result.

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
86 |
87 |
```

**Walkthrough**

1. **Lines 1–24:** Kivy supplies the interface components, but the conversion class works with its own alphabet. The encoder immediately returns an empty string for empty text; otherwise it converts the text to UTF-8 bytes, creates an output list and takes slices of up to three bytes.
2. **Lines 26–47:** A fresh integer accumulates each group of bytes. One input byte needs two alphabet symbols and `==`; two need three symbols and `=`; three need four symbols and no padding. The encoder selects high bits first and joins the output pieces only after the loop.
3. **Lines 49–62:** Strip the ends and return early if empty. The count of equals signs is assigned to `padding` but never read. Strip all trailing equals signs; reject any remaining nonalphabet character. Line 61 inserts the character itself inside literal single quotes.
4. **Lines 64–85:** Keep one byte array for the whole message. For each slice of up to four indices, reset the integer to zero, shift it six bits left and add the next index. The two-, three- and four-index branches append one, two and three bytes respectively; a lone index appends nothing. Interpret the complete byte array as UTF-8 only after the loop.

**Classroom GUI evidence:** At 0:40:00 the input is `qwerty`; at 0:44:00 it is `q`. The result box shows a placeholder, not `cXdlcnR5` or `cQ==`. At `00-11-10_event.png` the GUI reports the invalid space in `I don't know`. At `00-11-37_event.png`, the GUI shows `SSBkb24ndCBrbm93` above and `I don't know` below.

**Independent Python run (no Kivy or GUI):** The recovered class encoded `qwerty` as `cXdlcnR5` and `q` as `cQ==`; it decoded both back to their inputs. The companion `decoder-test-final.txt` records the checks against the annotated decoder, including exact exception messages. These runs verify behavior, not which editor state was running in a classroom frame.

### Sublime Text scratchpad: two ways to select six bits (0:56:00–1:00:07)

These are manual editor notes, not Python code, debugger values, or terminal output. The earlier historical listing of **`base64App.py`** contains the unchanged encoder. At 0:53:59–0:54:00 the selection is within the input annotation on line 14 (`text: str`); the class does not change the signature. In the 0:55:00 frame line 28 shifts the running number left by eight before adding the octet. At 0:56:18, line 31 has the four-place left shift selected beside the 12-digit scratch value. At 0:59:16 the editor selection is within `append` on line 32; the scratch result beside it is a **number**. The program uses that number to look up a **letter** before appending it.

**Changes in the working, in capture order.** Each entry describes a separate sampled state. In particular, a row of six zeros with the caret at its end was an unfinished line, not a reported result of zero.

| Requested frame | Visible editor state |
|---|---|
| `00-56-00_minute.png` | Scratch line 1 is `01000001`, the eight bits for uppercase A. |
| `00-56-18_event.png` | Line 1 is now `010000010000`, with four zeros on the right. The program window shows `integer <<= 4`. |
| `00-57-00_minute.png` | Line 1 still holds all twelve bits. Line 2 is blank; line 3 reads `010000`, the upper group by itself. |
| `00-57-48_event.png` | Line 3 now reads `000000010000`, the same six-bit value aligned to the right in twelve columns. Line 4 is `000000111111`, the mask. |
| `00-58-00_minute.png` | Line 5 labels the operation `AND`; line 6 contains only `000000` so far. |
| `00-59-00_minute.png` and `00-59-16_event.png` | Line 6 has the full `000000010000`. The last six digits of line 3 are selected in the first frame; the next shows the program alongside the scratchpad. |
| `01-00-00_minute.png` | The lower calculation has been set up with the **unshifted** twelve-bit operand on line 8. Line 11 contains only `000000` at this point. |
| `01-00-07_event.png` | Both result lines are complete. The listing below comes from this frame alone. |

**Final observed scratch state — editor gutter numbers at left are not text to type.** The active tab is labeled `010000010000`; the frame does not establish a saved filename. The hyphens and `AND` are typed notation.

```text
 1  010000010000
 2
 3  000000010000
 4  000000111111
 5  ------------ AND
 6  000000010000
 7
 8  010000010000
 9  000000111111
10  ------------ AND
11  000000010000
```

Lines 3–6 apply the mask *after* moving the upper six bits to the right. Lines 8–11 start from line 1 again and apply the identical mask *without shifting*. The different first operands give the same low six bits, `010000`. The actual text result appears in a different place and earlier in time: the converter’s *Output Result* field already shows `QQ==` in `00-53-00_minute.png`. The later scratch edits explain that output; they did not produce it.

### Sublime Text scratchpad: from the `A` masks to the pound sign

Historical hand-worked notation, not executable code or terminal output. Requested frame `01-11-14_event.png`; the left numbers reproduce the editor gutter and are not part of the text. The two mask examples already occupy lines 1–11. The new work is line 13: the eight bits of the pound sign’s code-point value, not its two UTF-8 bytes.

Lines 1–11 are unchanged from the preceding worksheet. The added rows are:

```text
12
13  10100011
14
```

Line 13 starts a different calculation: `1010` and `0011` spell hex `A3`. At `01-13-00_minute.png` line 13 still reads `10100011`, with lines 14 and 15 blank. The instructor’s account of pasting the Armenian character is not a basis for adding that character to this listing.

### `utf8.py`: the encoder, decoder and start of the GUI (editor lines 1–96)

Read the gutter numbers as locations in the editor; do not type the numbers or bars. These overlapping views establish the displayed code, not a saved revision or a console run. The encoder (8–29) is visible in one frame, `01-16-35.200_utf8-encoder.png`. The views at `01-17-29_event.png` and `01-17-55_event.png` cover the transition from encoder to decoder. `01-16-44.000_utf8-decode-tail.png` shows the full three-byte expression at 50, the decoder ending and the app class header at 66. Only the right-hand suffix of **line 58** is clipped in this span; the bracketed notice is editorial, not Python.

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
50 |             codePoint = ((b & 0x0F) << 12) | ((b2 & 0x3F) << 6) | (b3 & 0x3F)
51 |             i += 3
52 |         elif (b & 0xF8) == 0xF0:
53 |             if i + 3 >= len(octetStream):
54 |                 break
55 |             b2 = octetStream[i+1]
56 |             b3 = octetStream[i+2]
57 |             b4 = octetStream[i+3]
58 |             codePoint = ((b & 0x07) << 18) | ((b2 & 0x3F) << 12) | ((b3 & 0x3F) << [remainder not visible]
59 |             i += 4
60 |         else:
61 |             i += 1
62 |             continue
63 |         chars.append(chr(codePoint))
64 |     return "".join(chars)
65 |
66 | class Utf8(App):
```

1. Lines 1–6 import Kivy's app and widgets. The functions below them are at module scope: neither takes a GUI instance or class as its first argument.
2. Lines 8–29 visit each character, obtain its code point, and choose the first matching size. `append` adds one byte value, `extend` adds several in order, and the dedented return converts the whole list to bytes **after** the loop. The range checks do not exclude surrogates.
3. Lines 31–51 read one leading byte, select a width and reconstruct a code point. Line 50 is readable through its final `(b3 & 0x3F)`: `E2 82 AC` contributes `0x2000`, `0x80` and `0x2C`, yielding `€`. Each multibyte branch checks the available length, not the continuation prefixes.
4. Lines 52–64 guard the four-byte read, load three more bytes and begin reconstructing their payload. Line 59 advances by four. An unmatched lead instead advances by one and continues without reaching the append. A length guard breaks without appending the unfinished character. The common append at 63 is inside the loop but outside the branch chain; the return at 64 joins the accumulated text once, outside the loop. The displayed GUI result is separate evidence from this partial line of source.

#### Continuing in the same file: `Utf8.build` (editor lines 67–96)

`01-17-00_minute.png` continues below `class Utf8(App):`. The statistics-label constructor continues below this excerpt; it is not closed here by editorial guesswork.

```text
67 |     def build(self):
68 |         rootLayout = BoxLayout(orientation='vertical', padding=15, spacing=15)
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
```

1. Lines 67–82 build a vertical container, then add a title and prompt. Title markup is explicitly enabled; the specified heights, 40 and 25, are not fractions of the available height.
2. Lines 84–91 create the sample field, register its text-change callback and add it to the same layout. The editor glyphs and GUI hex bytes agree on the globe and rocket even though the GUI font does not draw them correctly.
3. Lines 93–96 begin a separate label, initially with empty text. The observed runtime screen (`01-16-16_event.png`) shows 16 characters and 23 UTF-8 bytes. The unseen handler is not transcribed as if it were on screen.

### Screen switch: `base64App.py`, 1:18:00

`01-18-00_minute.png` briefly returns to the earlier Base64 class. See its numbered encoder listing: lines 1–30 are the repeated viewport, not new UTF-8 code. Its `text.encode("utf-8")` supplies bytes for Base64 grouping; the earlier views show the encoder return at line 47. No new run is visible in this switch.

### Scratchpad states: bit masks, conditions and assignment (1:20:00–1:32:19)

This editor tab is a manual worksheet, not an executable Python file or a transcript of printed output. At `01-20-00_minute.png` lines 15–18 contain the H mask example without the later `AND` label on line 16. The shorter condition below is from `01-24-56_event.png`, **before** a single equals sign was substituted:

```text
20 | if a == b and a < c:
21 |
22 | if a & b:
```

The longest final visible worksheet from `01-32-19_event.png` follows. Its earlier binary rows remain visible above the subsequent examples. The line with only an insertion caret contains no statement. Selected-text highlights and carets are not characters in the listing.

```text
 1 | 010000010000
 2 |
 3 | 000000010000
 4 | 000000111111
 5 | ------------ AND
 6 | 000000010000
 7 |
 8 | 010000010000
 9 | 000000111111
10 | ------------ AND
11 | 000000010000
12 |
13 | 10100011
14 |
15 | 01001000
16 | 10000000 AND
17 | ---------
18 | 00000000
19 |
20 | if a = b and a < c:
21 |
22 |
23 | if a & b:
24 |
25 | x = 3
26 | 3 = x
27 |
```

1. Lines 3–11 apply the same six-low-bit mask twice; the different high bits in the first operands do not change the retained low-bit result. Line 13 stands separately; it is not the first byte of the `H` calculation below it.
2. Lines 15–18 isolate the high bit of `01001000`. Neither operand supplies a one in a column where the other has a one, so the worksheet result is eight zeros.
3. At 1:24:00 line 20 uses `==`; at 1:24:56 line 22 adds the distinct `&` test. At 1:27:00, line 20 has the **single** `=` and line 24 holds `x = 3`. By 1:28:00 the next line shows `3 = x`. The late extra blank line shifts the bitwise header and assignments down one gutter line; it does not supply a body or execute them.
4. In Python the double-equals version tests values; the single-equals conditional and the reversed literal assignment are syntax errors. This recording shows editing and discussion, not a resulting traceback or successful run.

## Micro-Learning Insights | A detachable review sheet

- **Read the RFC diagram across byte borders.** Draw three eight-bit boxes, then mark divisions every six bits. A Base64 index can use the end of one input byte and the beginning of the next. This is why looking up each input character separately does not work (0:07:00–0:09:00; RFC 4648 §4).
- **For a round trip, pass the result forward.** In the converter, `SSBkb24ndCBrbm93` belongs in the *input* field when decoding. Leaving `I don't know` there asks the app to solve a different problem (0:11:37).
- **For a GUI, trace two timelines.** During setup the app creates widgets and binds handlers. On a later press it reads the text that is in the field *then*. A binding should arrange a future call, not perform the conversion during construction (0:15:00–0:32:00).
- **Give a bit shift a job.** In `(integer << 8) + octet`, the shift clears space for the next whole byte. In `(integer >> 6) & 0x3F`, the shift brings a field into reach and the mask discards the rest. Both shifts change positions, for different reasons (0:37:00–1:00:00).
- **Check which alphabet you are reading.** The two `Q`s in `QQ==` are Base64 alphabet entries at index 16, not two copies of the input byte, not ASCII code 16, and not a demand to look up the ASCII bits of `Q` (0:47:00–1:00:00).
- **Let the padding diagnose your byte count.** `A → QQ==` but `£ → wqM=`. The input field shows one symbol in both cases. The differing suffixes are a clue to inspect the UTF-8 bytes *before* the Base64 step (1:09:00–1:11:00).
- **A missing glyph need not mean missing data.** In the UTF-8 app, the byte stream still distinguishes the multibyte characters even where the field draws a box. Check the actual bytes and the decoded result before blaming the encoding (1:16:16).
- **Ask what kind of answer a test produces.** `byte & 0x80` isolates one bit; `byte == 0x80` asks whether the entire value equals one particular number. Testing the truth of `byte` asks only whether the whole number is nonzero (1:17:00–1:27:00).
- **When a file opens incorrectly, name the layer.** CSV describes the fields; UTF-8 describes the text bytes; a BOM can be a signature; Base64 would be a separate outer representation. Identifying the layer narrows the fix without guessing (1:00:00–1:03:00).
