# Building a Kivy interface, reading bits, and bounding π

**CSCS1240F26 · Session 29 · Worked teaching companion**

Start with the window’s `2x` button. Is its job to repeat whatever the user typed or to double a number? Both designs make sense, but they require different operations on the same field. The class builds up to that choice one widget at a time. It then asks a related question about numerals: when you see `1000`, which *value* do the characters stand for, and how do you isolate one of its bits? The final discussion replaces an unattainable exact drawing of a circle with two measurable polygons. Across the three examples, the useful habit is the same: identify the representation, predict what an operation changes, and check the result.

**Where to look in the recording**

| If you want to… | Start here |
|---|---|
| Follow the project setup, first Kivy window, layout, label and color choices | [0:00:00 · Opening and GUI setup](https://www.youtube.com/watch?v=NRfvyLTFC_I&t=0s) |
| Understand hint text, layout allocation, anchoring, and the button callback | [0:47:00 · Input and parent layouts](https://www.youtube.com/watch?v=NRfvyLTFC_I&t=2820s) |
| Rebuild the truth tables and display decimal 1000 in binary | [1:37:00 · Switch to the numerical project](https://www.youtube.com/watch?v=NRfvyLTFC_I&t=5820s) |
| Map hex digits to bits, trace masks and shifts, and distinguish byte order from bit order | [2:16:00 · Hex mapping](https://www.youtube.com/watch?v=NRfvyLTFC_I&t=8160s) |
| Follow the move from finite machine representations to an approximation of π | [2:54:00 · Closing numerical discussion](https://www.youtube.com/watch?v=NRfvyLTFC_I&t=10440s) |

**Source and limits.** [1] [CSCS1240F26 29, JoesTA](https://www.youtube.com/watch?v=NRfvyLTFC_I). The 4,563 English auto-caption cues cover the greeting through the sign-off (0:00:01.600–3:24:06.920); video duration is 3:24:07.867. Evidence includes all 205 whole-minute frames, 27 event frames and one closing frame. Times are elapsed **hours:minutes:seconds**; frame times are seek targets. The 854 × 480 images do not resolve every character or show every edit. **Observed** means visible in a particular frame, **reported** means supported by speech, **expected** explains what a stated version should do, and **independently verified** means a separate test—not a recovered classroom run. Guide-added comments are teaching notes. Numbered historical listings near the end are a separate record, not code to paste with the gutter attached.

## The first Kivy window: from a sprint idea to a text field

### 0:00:00–0:04:06 · What would count as a good sprint measurement?

The opening Discord thread turns a student's idea into a design question: if you want to measure a runner's sprint, how much error can you tolerate? A GPS receiver that locates you to within a few meters might be useful on a long route but give a poor estimate of a short sprint. The written thread asks how to calculate that error. It also suggests Doppler radar or counting steps with an accelerometer calibrated to the runner's stride. Those measure different things: a count of steps is not yet a distance. They are proposals, not hardware built or tested in class.

The 0:01:00 screen offers two levels of study: start with an Arduino board, shields and the Arduino IDE, or investigate the compilers, buses and registers beneath the convenient interface. That choice foreshadows the GUI lesson: use a framework to get a result, then look down one level when you need to understand it. The class was invited to respond in the thread. A separate Discord video recommendation shows the thumbnail “Now is the time to learn to code”; the class does not watch it here.

Uneven audio in earlier recordings provides a second measurement example. Moving the microphone helps a quiet person in the room; a remote participant coming through the computer's audio can instead be too loud. Turning up everything amplifies both. The class raises audio leveling in OBS and the logarithmic decibel scale; no leveling setting is demonstrated. **Technical correction:** the suggestion that 3 dB means ten times louder confuses intensity with perceived loudness. A 3 dB increase is about twice the intensity; 10 dB is ten times the intensity, not ten times the perceived loudness. See [NIDCD, *How Is Sound Measured?*](https://www.nidcd.nih.gov/health/how-sound-measured).

**Key items to scan**

- Sprint thread — compare the sensor's error with the size of the distance you want to measure; calibrate a stride before treating step count as distance.
- Arduino IDE versus buses and registers — a working high-level interface leaves lower layers available for a deeper investigation.
- OBS and decibels (dB) — mixing inputs with different levels is not the same as raising the master volume; decibels express a logarithmic ratio.

### 0:04:07–0:10:57 · How little Python will open an application window?

The instructor had hoped that Flet would let one unchanged Python codebase behave the same on desktop, mobile and the web. The browser and HTTP introduce differences that mattered to him; this is his reason for changing tools, not a verdict that Flet cannot target the web. Flutter was another possibility, but he wanted Python rather than Dart. He returns to Kivy, a Python framework he had used before, to experiment with a graphical user interface (GUI): instead of controlling the application solely through a terminal, the user interacts with a window and the things inside it. The practical starting advice is to activate a project-specific virtual environment and use **`pip install kivy`** there. The classroom terminal already has **`(KivySteps)`** in its prompt; installation is advice, not a new successful install shown on screen.

Rather than build everything at once, he steps through Git revisions of **`src/steps.py`**. At 0:05:00 the terminal shows **`git checkout master~8`** followed by **`git log --oneline`** and the commit message **`Steps: empty window`**. An older diff above those commands mentions sizing; it is scrollback, not part of the empty-window program.

**Annotated teaching version — guide-added comments** (editor, 0:10:05):

```python
from kivy.app import App       # Bring Kivy's application class into this file.

class KivySteps(App):          # Define a new kind of App; inherit its run method.
    def build(self):           # Kivy asks this particular app to supply its interface.
        pass                   # Do nothing; reaching the end returns None, not a widget.

KivySteps().run()              # Create one app instance, then call run on that same instance.
```

The parentheses have two jobs. In the class header, **`(App)`** names the base class; it is not a call that creates a window. In **`KivySteps()`**, the call creates an instance. No variable is needed to name that instance before calling its inherited method. Defining **`build`** in the subclass overrides the inherited method: when the framework asks this app to build its interface, this body runs. Python does not call it merely because it encounters its definition. Predict before running: does doing nothing inside the method make invalid Python, produce no interface content, or prevent a window from being created at all?

**Key items to scan**

- Kivy GUI — use Python for a graphical interface on desktop and mobile; the instructor does not demonstrate web deployment.
- **`pip install kivy`**; **`(KivySteps)`** — install in the activated environment; the prompt identifies the classroom environment.
- **`src/steps.py`** — the same file path is used across different revisions.
- **`git checkout master~8`**; **`git log --oneline`** — select the earlier snapshot, then inspect its short history; **`Steps: empty window`** identifies the starting state.
- **`App`**, **`(App)`** — the imported base class and its use in the subclass header; inheritance supplies behavior the student did not write.
- **`build`**, **`self`**, **`pass`** — the app supplies its own startup method; the instance is available as its first parameter, but this body supplies no root widget.
- **`KivySteps()`**, **`.run()`** — construct the app, then start it. The framework calls the build method during startup.

### 0:10:58–0:16:16 · Read the window and the terminal together

**Observed classroom invocation** at 0:11:00 (Windows PowerShell):

```powershell
py .\src\steps.py
```

**Observed console text, selected messages in order** (0:11:00–0:13:00; logger alignment omitted):

```text
Successfully imported "kivy_deps.angle" 0.4.0
Successfully imported "kivy_deps.glew" 0.3.1
Successfully imported "kivy_deps.sdl2" 0.8.0
No window is created. Terminating application run.
Start application main loop
Provider: sdl2
Using the "OpenGL" graphics system
GLEW initialization succeeded
Backend used <glew>
```

The console identifies Kivy 2.3.1 and Python 3.12.7. The “No window is created” line is labeled critical, but the subsequent log reports a main loop and window provider, and an empty black window is visible, unambiguously in front at 0:14:38. This is neither a Python traceback nor proof that the process exited at that line. The instructor moves, resizes, minimizes and restores the window. The operating system supplies those familiar window controls; the app did not draw any controls of its own. The classroom result is specific to this setup, not a guarantee that a rootless app behaves identically on every platform.

| Step | What executes or changes | Evidence or result |
|---|---|---|
| 1 | Python loads the import and defines the subclass. | No label or layout has been constructed. |
| 2 | The last line constructs the app and calls its inherited run method. | The console logs framework startup; there is no print statement in the example. |
| 3 | Startup invokes the override; it falls through without a return statement. | The expected Python return value is **`None`**. The classroom window contains no lesson widgets. |
| 4 | The GUI event loop continues to handle window actions. | The app remains open without a student-written loop. In the next revision the instructor points out that the shell prompt returns when the window closes (0:20:16–0:20:34). |

What is under this short program? In this run **`sdl2`** is the window provider and the graphics stack reports OpenGL, a GLEW backend and an Intel Iris Xe renderer. The instructor contrasts the amount of code needed for a window at the Kivy, SDL and direct graphics-API levels. He also mentions OpenGL ES for embedded/mobile systems. **Technical clarification:** [OpenGL](https://khronos.org/opengl/wiki/OpenGL) is an API specification implemented by drivers, often using a GPU; it is not itself firmware on a graphics card. [SDL (Simple DirectMedia Layer)](https://libsdl.org/) provides cross-platform access to windows, input and graphics facilities; the classroom log does not show SDL emulating a missing GPU, nor does it identify this desktop context as OpenGL ES. A learner separately reports an installation timeout on a Chromebook; the instructor repeats the install command and offers to troubleshoot later. Neither the cause nor a successful repair is established.

**Key items to scan**

- **`py .\src\steps.py`** — the observed launcher command; the log is Kivy's, not output from a student print statement.
- **`None`** — what this empty build method implicitly returns. Absence of a root widget is not the same claim as absence of an OS window.
- **`sdl2`** — the selected window provider; GLEW and OpenGL are identified separately in the graphics messages.
- Event loop — keeps the process alive to handle input and window changes; closing the running window gives the terminal its prompt back.

### 0:16:17–0:20:45 · Give the window a container before adding content

The next revision replaces the empty build body with a **`BoxLayout`**. Think of a column waiting for items: it already knows where its edges are and how much space to leave between future children, even when it has nothing to display. A horizontal layout would arrange items along the other axis. The instructor mentions float and grid layouts as other choices, not as widgets used in this revision. Letting a layout recompute positions when the window changes size avoids choosing a new set of absolute pixel coordinates for every device.

**Annotated teaching version — guide-added comments** (0:18:00; constructor wrapped for reading):

```python
from kivy.app import App                 # Reuse the framework's application class.
from kivy.uix.boxlayout import BoxLayout # Import the container this revision adds.

class KivySteps(App):
    def build(self):                     # Called at startup, as before.
        rootLayout = BoxLayout(          # A NEW, empty container for this run.
            orientation='vertical',      # Stack children in a column instead of a row.
            padding=20,                  # Reserve an inset around the children.
            spacing=15)                  # Leave a gap between adjacent children.
        return rootLayout                # Give Kivy the container, not a printed description.

KivySteps().run()                        # Start a new instance for this revision.
```

**Classroom result, instructor's account:** another empty window. This time a root *was* returned. The unchanged appearance is no evidence that the code did nothing: it created a container but attached no visible child to it. With no pair of children, there is nowhere for **`spacing=15`** to appear. The prompt not returning until the window closes gives a second, independent clue that the program is still running.

**Key items to scan**

- **`BoxLayout`** — a widget whose job is to arrange other widgets; an empty one need not draw anything noticeable.
- **`rootLayout`** — a local name for the newly created container, not an automatically supplied Kivy variable.
- **`orientation='vertical'`** — arrange multiple children as a column; **`padding=20`** is the inset at the edges, **`spacing=15`** the gap between neighbors.
- **`return rootLayout`** — installs the object as the app's proposed root by handing it back to Kivy during startup.

### 0:20:46–0:30:32 · A label becomes visible only after it has a place in the layout

The next checkpoint adds one thing the learner can see: the words “Kivy Steps.” A **`Label`** is a widget for displaying text. Importing the class makes the name available. Constructing the widget sets up its appearance. Attaching it to the root is a separate operation. Without the corresponding import the new constructor name is not available in this file; without attachment the local label is not part of the displayed tree. Neither omission is a demonstrated error here.

**Annotated teaching version — guide-added comments** (0:21:00–0:24:00; changed lines and their placement):

```python
from kivy.uix.label import Label         # Additional import, alongside App and BoxLayout.

# Inside build, after creating this run's rootLayout:
        titleLab = Label(               # Create one NEW label; titleLab names it locally.
            text="Kivy Steps",          # Exact text displayed; not printed to PowerShell.
            font_size='24sp',           # Request 24 scalable font units, not 24 raw pixels.
            bold=True,                 # Request bold text; True is a Boolean, not a string.
            color=(0.5, 0.5, 1, 1)      # Half red, half green, full blue; fully opaque.
        )
        rootLayout.add_widget(titleLab) # Put that same label in the existing container.

        return rootLayout               # Return the parent with its child attached.
```

The words at the left of the equals signs in the constructor are keyword arguments: change a named property without specifying every optional setting. The class asks whether a font size is measured in inches, millimeters or the points familiar from a word processor. Fixed pixel counts give different physical sizes on screens of different densities. In Kivy **`sp`** (scale-independent pixels) accounts for display density and font scaling; it is not a point or a physical inch. [Kivy's *Metrics* reference](https://kivy.org/doc/stable/api-kivy.metrics.html) distinguishes the units. The instructor's preference for camelCase explains **`titleLab`**; **`add_widget`** must keep the spelling of the library method, regardless of that preference.

**Key items to scan**

- **`Label`**, **`titleLab`** — the class and the local name of the single object displaying “Kivy Steps.”
- **`font_size='24sp'`**, **`sp`** — a font size in density- and font-scale-aware units; **`bold=True`** requests bold rendering.
- **`rootLayout.add_widget(titleLab)`**, **`add_widget`** — attach the label to the already-created container using the framework's exact method name.

#### 0:24:00–0:30:32 · Read a color in two representations

The label's **`color=(0.5, 0.5, 1, 1)`** supplies one argument whose value is a tuple of four numbers, not four separate arguments to the label. Count three separating commas inside the parentheses. A tuple cannot be changed by assigning to one of its positions; the instructor later changes the expression in the source for a subsequent run. Read it in **RGBA** order: red, green, blue, then alpha (opacity). A channel value of 0 means none of that channel, not necessarily a black pixel. Blue is at its maximum; adding red and green to that blue makes a lighter, less saturated blue. A student predicts a deeper indigo by thinking about mixing colors. Testing the prediction will reveal why the kind of mixing matters.

At 0:27:00 a *comment* after the tuple reads **`#fe90a8`**. It illustrates hexadecimal notation. It neither sets the label's color nor denotes the same color as its tuple. Split the six digits into three pairs before converting them. In base 16, digits 0–9 and a–f cover sixteen possibilities; one hex digit occupies four bits, two digits eight bits (one octet). Here is the split, **independently verified with Python**; no conversion output was printed in class:

| Channel | Two hex digits | Eight bits | Decimal out of 255 |
|---|---|---|---:|
| Red | fe | 11111110 | 254 |
| Green | 90 | 10010000 | 144 |
| Blue | a8 | 10101000 | 168 |

With one octet per color channel there are 24 bits of RGB; appending an eight-bit alpha field gives a 32-bit RGBA representation. That adds opacity, not more shades per existing eight-bit channel. The instructor's broader point about normalization is that “all the blue available” can be written as 1 without hard-coding a storage maximum like 255. To convert this *particular* hex example to normalized RGB, divide each decoded channel by 255; the alpha would have to be supplied separately. The executable tuple stays in place while the temporary hex comment is added and removed.

**Observed versus reported:** at 0:30:00 the editor still shows the original tuple and the call that attaches the widget. Around 0:30:27–0:30:44 the instructor reports seeing the mostly-blue title. The next sampled rendered window shows the *subsequent* pure-blue comparison, not a frame to use as proof of the first shade.

**Key items to scan**

- **`color=(0.5, 0.5, 1, 1)`**, **RGBA** — one four-item tuple: equal half-strength red and green, full blue and full opacity.
- **`#fe90a8`** — a temporary Python comment illustrating three hex pairs, not a second executable color assignment.
- Octet and normalization — two hex digits represent an eight-bit channel; division by 255 expresses that channel on a 0–1 scale.

### 0:30:33–0:43:07 · Why a screen mixes colors differently from paint

Instead of arguing about a name for the shade, the instructor varies only the three color channels of the same label definition. These are separate configurations/runs, not four successive assignments to one live label. Alpha stays at 1; transparency was suggested for another experiment but not tested here.

**Annotated teaching version — guide-added comments.** These are alternative argument lines inside the label constructor, not a block to paste together. The pure-blue line is reconstructed from the instructor's stated channel settings; the other three tuples are legible in editor frames.

```python
color=(0.5, 0.5, 1, 1)  # Original: blue plus equal amounts of red and green.
color=(0, 0, 1, 1)      # Comparison: remove red and green, keep maximum blue.
color=(1, 1, 1, 1)      # All three channels at maximum: display white.
color=(0.5, 0.5, 0.5, 1)  # All three at the same lower level: display gray.
```

| Experiment | What changed? | What the class could compare |
|---|---|---|
| Original label | Full blue with some red and green. | Instructor reports “mostly blue,” not pure blue. |
| Pure blue | Both other channels drop to zero; blue itself did not get stronger. | At 0:31:00 the black window contains blue “Kivy Steps.” |
| Full RGB | Red and green rise to join blue at maximum. | White text on black is visible at 0:32:00–0:39:00; all-ones code is visible at 0:40:00. |
| Equal lower RGB | All three are set to 0.5; alpha is unchanged. | Gray text is visible beside the matching editor state at 0:42:00. Equal channels approaching zero tend to black; black was discussed, not another observed run. |

Why did “add all the colors” lead one student to predict a muddy gray? In art class, the eye receives the light a painted surface *reflects*. Incident light strikes a red apple; the surface absorbs some wavelengths more than others. The student explicitly reverses an initial account: we see the reflected light, not the absorbed light. Pigments change what remains of the illumination, hence **subtractive color**. Cyan, magenta and yellow printer inks, often supplemented by black (CMYK), are an everyday example. The result depends on the light falling on the surface as well as on the pigment.

A monitor, by contrast, supplies the light. Red, green and blue contributions add; no paint removes wavelengths from a lamp's light. Those are the **primaries** of this system, not the one universal list of colors every device must start with. The colors a particular system can make form its **gamut**. A classmate's objection that white “bounces back” describes a lit surface; the instructor had switched to **additive color** on the display. **Technical clarification:** the white this monitor produces is a perceptual match; its RGB emitters need not produce every visible wavelength. [Color Addition, The Physics Classroom](https://www.physicsclassroom.com/tutorial/light-waves-and-colors/physics-of-color/color-addition) explains this distinction.

The class ties both cases back to the eye: visible light occupies part of the electromagnetic spectrum, with infrared below visible red in frequency and ultraviolet above visible violet. Rods are especially useful in dim light; cones with different, overlapping spectral sensitivities contribute to ordinary color vision. A participant's suggestion that red–green color deficiency always means having only two cone types is too broad; several forms affect color discrimination in different ways ([National Eye Institute, *Types of Color Vision Deficiency*](https://www.nei.nih.gov/learn-about-eye-health/eye-conditions-and-diseases/color-blindness/types-color-vision-deficiency)). The important question for the GUI is simpler: is the eye receiving light emitted by the display, or light left after absorption by a surface?

Finally, distinguish the space given to the label from the space occupied by its letters. The gray title appears in the middle of the window. The only child takes the available layout area inside the padding, and the text appears centered within that child. Changing from a row to a column is not visually informative with only one child; there are no neighbors for spacing to separate. Background color on the label could make its boundary easier to see, but that change was only proposed.

**Key items to scan**

- **`(0, 0, 1, 1)`** — opaque pure blue; adding red and green can lighten the result without increasing its blue channel.
- **`(1, 1, 1, 1)`** versus **`(0.5, 0.5, 0.5, 1)`** — the same balance of RGB at different levels produces white versus gray on this display.
- **Primaries**, **gamut** — component colors chosen for a system and the range they can reproduce; available colors are device-dependent.
- **Subtractive color** — a surface absorbs part of the incoming light; the eye receives what is reflected. **Additive color** — the display combines emitted light.
- Alpha — the fourth channel; 1 is fully opaque. Gray text in this experiment is not transparent white text.
- Label bounds versus text bounds — centered letters do not imply the widget is only the size of those letters.

### 0:43:08–0:47:59 · Use Git to see exactly what the next interface adds

The temporary color edits now matter: switching revisions would overwrite **`src/steps.py`**. Git refuses rather than discard them. The 0:44:00 console shows why the first attempt to clear the way did not work. **`git restore --staged`** changes the index (the staging area); it does not undo the edits in the working file. Closing the editor also doesn't undo them. **`git stash`** preserves the work separately and clears the way for the desired checkout. These are historical project actions, not instructions to discard a learner's own edits.

| Action in PowerShell | Observed result or meaning |
|---|---|
| **`git checkout master~5`** | Rejected: local changes to **`src/steps.py`** would be overwritten; the output ends with **`Aborting`**. |
| **`git restore --staged .\src\steps.py`**; checkout retried | The checkout is still rejected. Restoring the index alone leaves working-tree changes; see [Git's restore reference](https://git-scm.com/docs/git-restore). |
| **`git stash`** | Git reports saving the working directory and index state. This is not a failed checkout and not a new permanent commit on the current branch. |
| Checkout retried after the stash | A later terminal view reports **`HEAD is now at 2ec4f8a Steps: Text input widget`**. The editor once again contains the earlier blue-tinted label definition, not the gray experiment. |
| **`git diff HEAD~ -- .\src\steps.py`** | Compare this working file with the parent of the current commit. With no relevant local edits it reveals what the checked-out revision added relative to its parent; it does not generally mean “compare two named commits.” |

In the displayed unified diff, **`a/src/steps.py`** is the older side and **`b/src/steps.py`** the newer side. Read a hunk header such as **`@@ -15,6 +17,18 @@`** as “six old lines starting at 15; eighteen new lines starting at 17,” *not* “lines 15 through 6.” Unmarked context helps locate the change. A leading plus on a content line is an addition; a minus would mark a removed line. The plus sign is Git's notation, not something to type into the Python file.

The new lines construct a **`TextInput`**, not a second label. The existing label remains a child. The new field provides a hint when empty and has its own background, foreground and caret colors. The source makes the background's blue component higher than red or green. The guide annotations below describe the actual arguments, not the loose wording of the captions.

**Annotated teaching version — guide-added comments** (diff at 0:46:00–0:47:00; insertion after the title label):

```python
from kivy.uix.textinput import TextInput  # Additional import: the editable field class.

# Inside build, after rootLayout.add_widget(titleLab):
        someTxt = TextInput(              # Create a NEW editable widget, not another title.
            hint_text="Type here",        # Prompt while empty; not pre-entered user text.
            multiline=False,             # Keep the input to one line of text.
            background_normal="",        # Use no normal-state background image.
            background_active="",        # Use no active-state background image.
            background_color=(0.1, 0.1, 0.25, 1), # Opaque dark background, blue largest.
            foreground_color=(1, 1, 1, 1),        # White entered text.
            cursor_color=(1, 1, 1, 1),            # White insertion caret.
        )
        rootLayout.add_widget(someTxt)    # Attach the new field as a second child.
```

**Expected from the code:** there will now be a title and an editable field, both laid out by the same parent. Single-line input limits the lines of text; it does not by itself request a one-line-high rectangle. At 0:47:50–0:47:56 the instructor announces the run. Compare that prediction with the window that follows.

**Key items to scan**

- **`git checkout master~5`**, **`Aborting`** — the first switch is refused to protect the locally edited **`src/steps.py`**.
- **`git restore --staged`**, **`git stash`** — unstaging did not erase the working-file changes; stashing preserved them outside the working tree.
- **`git diff HEAD~ -- .\src\steps.py`**, **`a/src/steps.py`**, **`b/src/steps.py`** — compare the named path to its parent-revision version and read the old/new sides separately.
- **`@@ -15,6 +17,18 @@`** — a hunk's two start/count pairs; plus signs on the lines below mark additions.
- **`TextInput`**, **`someTxt`** — the new class and the local name for the field it creates. **`hint_text="Type here"`** is a placeholder, not actual input.
- **`multiline=False`** — restricts text entry, not layout height; **`rootLayout.add_widget(someTxt)`** attaches the field to the same parent as the label.
- **`background_normal`**, **`background_active`** — empty strings remove the named state images; **`background_color`**, **`foreground_color`** and **`cursor_color`** set three different visual properties.

## Building the interface: layout, events and the 2x button

### What can you edit? What determines which widget comes first? (0:48:00–0:53:26)

The window shows “Kivy Steps” in the upper half and a dark blue typing area below. “Type here” looks like text, but try moving the insertion point through those letters. You cannot: **`hint_text`** belongs to the widget, not to the user's entry. As soon as you type, the gray hint disappears. Now the arrow keys move through actual characters; Shift+arrow selects them, Delete removes them, and the mouse can place the cursor or select a range. The instructor also discusses cut, copy, and paste. A login field that suggests a username without making you erase the word “username” uses the same idea. That editing logic belongs to **`TextInput`**; the application didn't implement a keyboard editor.

Annotated teaching version — guide-added comments; excerpt from the input setup at 0:49:00. The title has already been attached to the vertical root.

```python
someTxt = TextInput(                        # Create the field; it has no entered text yet.
    hint_text="Type here",                  # Display when empty; not available for selection.
    multiline=False,                        # Do not insert another line on Enter.
    background_normal="",                   # Do not use the normal background image.
    background_active="",                   # Do not use the active background image either.
    background_color=(0.1, 0.1, 0.25, 1),   # Red, green, blue, alpha: dim blue, fully opaque.
    foreground_color=(1, 1, 1, 1),           # Actual typed characters are bright white.
    cursor_color=(1, 1, 1, 1),               # Make the insertion cursor white as well.
)
rootLayout.add_widget(someTxt)              # The field now occupies the second slot.
```

**`BoxLayout`** arranges its children, not every object that happens to have been constructed. With the original two unconstrained children, both share the vertical space. The instructor leaves the constructors in their original order but moves the line that adds the label. The rerun shows the blue input above the title (0:53:15). The inset around the blue rectangle makes the outer padding visible; spacing separates siblings. A horizontal arrangement would run side by side instead; it is a comparison, not this run.

Annotated teaching version — guide-added comments; changed insertion order at 0:53:00, after both constructors:

```python
rootLayout.add_widget(someTxt)    # First in this vertical stack: typing area on top.
rootLayout.add_widget(titleLab)   # Same label object as before, now below the field.
```

**Key items to scan**

- **`TextInput`** — supplies the editable field and ordinary selection/editing behavior.
- **`hint_text`** — gray “Type here” is a prompt, not selectable input.
- **`background_color=(0.1, 0.1, 0.25, 1)`** — the blue channel is strongest; the last component sets opacity.
- **`foreground_color`** and **`cursor_color`** — actual text and insertion point are separately styled.
- **`BoxLayout`** — with vertical orientation, first-added child above the second here; padding is outside the children, spacing is between them.
- **`add_widget`** — attaching a child decides its place; constructing it earlier does not reserve the top slot.

### Compare one layout change at a time (0:53:38–1:01:37)

Trying to move to **`master~4`** encounters an obstacle unrelated to Kivy: a saved change to **`src/steps.py`** would be overwritten. Closing an editor tab without saving a *further* change is not the same as undoing a change already on disk. Git reports “Aborting.” The terminal at 0:55:00 shows **`git stash`**, then a successful checkout. The instructor asks whether the second stash erased the first: stashes behave like a stack; a later push does not overwrite the earlier entry, and popping can recover the latest. This was a demonstration of preserving experiments while visiting historical revisions, not an instruction to discard work.

The instructor uses **`git diff HEAD~ -- .\src\steps.py`** to compare the current file to the parent of the checked-out commit. In the clean checked-out examples this exposes the revision change: the minus line is the prior form, the plus lines the new form. A comma needed to add another keyword and an incidental blank-line edit can occupy diff space without being the point. Find the constructor to which the changed keyword actually belongs.

Annotated teaching version — guide-added comments; changed keywords in separate revisions (constructor excerpts, not successive updates to a live widget):

```python
# master~4: abridged constructor; title styling omitted here.
titleLab = Label(text="Kivy Steps", size_hint_y=None)  # Opt the label out of proportional height.
```

Result at 0:57:00: the title has a smaller band at the top and the blue input takes most of the window. This is not a rule that setting the hint to None automatically measures the letters: it opts this widget out of proportional height allocation. Its own height still matters.

```python
# master~3: abridged field constructor; the label still opts out of vertical sharing.
someTxt = TextInput(
    hint_text="Type here", multiline=False,  # Same prompt and single-line entry.
    height="40dp",                         # Set a concrete, density-independent height.
    size_hint_y=None,                      # Do not stretch the input to share the remaining height.
)
```

Result at 0:59:00: the blue field is a narrow strip at the bottom; the title appears just above it, **not** vertically centered in the enormous black region. Neither child is now asking to expand in the vertical direction. Do not infer the label's rectangle fills all black pixels just because the backgrounds blend. **`dp`** describes a density-independent measurement, not a fraction of the current window.

Now the instructor presses Enter. At first it looks as if one line is the default, but the instructor corrects that explanation: the code explicitly set **`multiline=False`**. A username usually wants that restriction; an editing area may not. The instructor reports changing the setting and rerunning, entering another line and scrolling within the same short field (1:00:46–1:01:16). The exact temporary edit is not unobscured in these captures; scroll-bar widgets are mentioned as a future option, not installed here.

Annotated teaching comparison — guide-added comments; second line expresses the demonstrated choice, not a literal transcription of the temporary buffer:

```python
single = TextInput(multiline=False)  # Enter does not add a newline to this single-line field.
editor = TextInput(multiline=True)   # A different field can hold several lines without becoming taller.
```

**Key items to scan**

- **`master~4`** — fourth first-parent ancestor of the branch tip, not “four more” from whichever commit is open.
- **`src/steps.py`** — the working file Git refused to overwrite.
- **`git stash`** — preserves the experiment and clears the way for checkout; earlier stashes remain available.
- **`git diff HEAD~ -- .\src\steps.py`** — inspect changes against the immediately preceding commit for this path; **`--`** separates revisions from paths.
- **`size_hint_y=None`** — opt out of vertical size sharing, first on the label and then also on the input.
- **`height="40dp"`** / **`dp`** — set the input's height independently of the window's remaining height.
- **`multiline=False`** — Enter doesn't add another line because of an explicit field setting, not because the field is blue or short.

### Anchor the field inside the right rectangle (1:01:37–1:11:44)

The next revision introduces **`AnchorLayout`**. Instead of asking the root to stack a title immediately above a small input, leave the title in the root and give the rest of the root to a *container*. Position the field within that container. Thinking in parent–child rectangles lets you specify a design that survives a window resize without choosing absolute screen coordinates. The instructor suggests adding visible borders when black backgrounds hide those rectangles.

Annotated teaching version — guide-added comments; center/center version, 1:04:00–1:07:00; full anchor declaration also visible in the 1:05:00 diff:

```python
from kivy.uix.anchorlayout import AnchorLayout  # Bring in the container that positions a child.
# The vertical root already contains titleLab, and the 40dp-high someTxt already exists.
contentLayout = AnchorLayout(anchor_x="center", anchor_y="center", padding=10)  # Inner rectangle.
contentLayout.add_widget(someTxt)          # The field is a child of the inner rectangle, not the root.
rootLayout.add_widget(contentLayout)       # Give that entire rectangle the root's next slot.
```

Result: the label remains at the top. The blue strip sits halfway down the *remaining region*, not necessarily halfway down the whole window. Asked to predict a top anchor, a student expects the title might be displaced below the input. The field does rise, but only to the top of its own parent, still **below** the title (1:08:00–1:08:20). Next, changing horizontal anchoring barely changes the visible field. It already uses the available width. There is nowhere left for the field to slide horizontally; this setting concerns the rectangle, not the justification of the letters inside it.

Annotated teaching version — guide-added comments; abridged constructors isolating the two arguments seen in distinct edits, not three containers in the class app:

```python
# Read each line as a separate alternative for the same constructor, not successive live updates.
contentLayout = AnchorLayout(anchor_x="center", anchor_y="top")  # Top of the inner area, below titleLab.
contentLayout = AnchorLayout(anchor_x="left", anchor_y="top")    # Align the field, not its characters.
contentLayout = AnchorLayout(anchor_x="right", anchor_y="top")   # A full-width field has no room to move sideways.
```

**Key items to scan**

- **`AnchorLayout`** — positions its child relative to its own bounds; it does not rearrange siblings outside it.
- **`contentLayout.add_widget(someTxt)`** — establishes the field's immediate parent and therefore its frame of reference.
- **`rootLayout.add_widget(contentLayout)`** — places that parent below the existing title in the outer column.
- **`anchor_y="top"`** — top of the inner area, not top of the window.
- **`anchor_x`** — left, center, and right positioning of a full-width field can look identical; not text alignment.

### Give the button a place before giving it a job (1:11:44–1:23:48)

The instructor initially looks at the diff before making the next checkout, notices the mistake, and then examines the revision described as “Button grouped with TextInput.” It also gives the title a concrete 40dp height. The question is no longer just “Where does this field go?” It is “How do the field and button stay together while the window changes size?”

Annotated teaching version — guide-added comments; unbound button revision, 1:13:00–1:23:00:

```python
titleLab = Label(text="Kivy Steps", size_hint_y=None, height="40dp")  # Abridged: constrain the title height too.
doubleBut = Button(                         # A new button; creating it does not specify what to double.
    text="2x",                             # Visible caption; Python does not execute this text.
    background_color=(0.2, 0.6, 1, 1),     # Give the button a blue tint.
    bold=True,                             # Use a bold caption.
    height="40dp", size_hint_y=None,        # Keep its concrete height in the vertical group.
    size_hint_x=0.25,                      # Use one quarter of the group's available width.
    pos_hint={"center_x": 0.5, "center_y": 0.5},  # Request midpoint alignment; the layout decides which axes apply.
)
```

The braces here enclose a Python **`dict`**, a dictionary. The two string **keys** name two distinct coordinates; each colon pairs its key with a numeric **value**, and the comma separates entries. Look up the value *by the key*, not the other way round. The instructor connects this notation to JSON and mentions hashing but does not teach a hashing algorithm. The button is a quarter of the available width; centering that quarter leaves three eighths on either side. Resizing preserves the proportion. Distinguish horizontal centering of the narrow button in its vertical group from vertical positioning of the *whole group* by its outer anchor.

Annotated teaching version — guide-added comments; construction abridged to show the hierarchy. The source uses a vertical group with vertical size hint disabled; constructor details not material to this diagram are omitted here.

```python
groupLayout = BoxLayout(size_hint_y=None, orientation='vertical')  # Separate column for the controls.
groupLayout.add_widget(someTxt)             # Wide text field first, above the button.
groupLayout.add_widget(doubleBut)           # Narrow button second, below the field.
contentLayout = AnchorLayout(anchor_x="center", anchor_y="center", padding=10)  # Outer positioning container.
contentLayout.add_widget(groupLayout)       # Center both controls together, not two independently anchored children.
rootLayout.add_widget(contentLayout)        # The title remains a separate child above this region.
```

Result: a title, a text field, and a centered blue **`2x`** button. Clicking or dragging over the label ordinarily makes no visible change here. Keystrokes and mouse drags in the input change the text or selection. Pressing the button does give visual feedback, as the class notices at 1:22:30–1:23:36, but the field's content does not double. A student suggests duplicating the text each time; that is a plausible design, not behavior inherent in the caption. An **event** is the press (or a keystroke, click, or drag); an **event handler** is the code the app supplies to say what the press *means*. On this checkout the button has native press feedback but no app-specific handler.

**Key items to scan**

- **`dict`** / **keys** / **value** — in the position hint, the key `"center_x"` names the coordinate whose value is `0.5`.
- **`size_hint_x=0.25`** — changes the button's width; it is not the distance from the left edge.
- **`pos_hint`** — requests a position from the parent layout; it does not position text inside the button.
- **`groupLayout`** — vertical box containing the field followed by the button; **`contentLayout`** anchors that group as a unit.
- **`2x`** — a button caption, not a calculation. Press feedback alone doesn't prove the field changed.
- **Event** / **event handler** — the press is an occurrence; the app must supply code if it should have a domain-specific effect.

### Bind the press and share the right object (1:23:48–1:29:46)

The terminal shows a switch to **`master`** at 1:24:00. The new version relabels the empty field “Enter a number.” More importantly, the widget created during **`build`** must be available to a different method that runs *later*. The former local **`someTxt`** was a name available inside **`build`**. Writing **`self.someTxt`** attaches the input to this application instance; the callback can now retrieve the very same field. The button itself is still locally named **`doubleBut`**; giving the app a field attribute does not turn every local name into an attribute.

Annotated teaching version — guide-added comments; excerpts, with unchanged constructor styling omitted:

```python
self.someTxt = TextInput(hint_text="Enter a number", multiline=False)  # Store the field on this app object.
# After creating doubleBut in build, register what to do on a press:
doubleBut.bind(on_press=self.on_button_click)  # Pass the bound method itself; do not run it now.
groupLayout.add_widget(self.someTxt)          # Display the very input the callback will later read.
groupLayout.add_widget(doubleBut)             # Display the button that dispatches the press.
```

**`self.on_button_click`** without parentheses tells Kivy *which callable to invoke*. Calling it at registration would attempt the work at startup, before a user press, rather than hand over the method. When Kivy dispatches **`on_press`**, the method already has **`self`**, the app; its **`instance`** parameter receives the widget that fired the event, here the button. The handler doesn't need that parameter to find the text field: it follows **`self.someTxt.text`** instead. This is an attribute lookup on the widget, not conversion of the widget object itself.

**Key items to scan**

- **`master`** — branch now containing the application-specific behavior; the earlier button-only checkout is not this version.
- **`build`** / **`someTxt`** — the original field name was local to layout construction.
- **`self.someTxt`** — an attribute retaining the editable widget on this app instance.
- **`doubleBut`** — local name for the button object; it can be bound and attached to a parent without becoming `self.doubleBut`.
- **`on_press`** / **`self.on_button_click`** — event name and method to call later; no call parentheses when registering it.
- **`self`** / **`instance`** — app owning the bound method versus button dispatching the press.
- **`self.someTxt.text`** — the current contents of the field; assigning this property changes the interface.

### What “2x” actually doubles, and what happens on bad input (1:27:00–1:35:43)

The user's entry is a string, even if it looks like a number. For the spoken example “34.67,” the instructor wants the number 69.34, **not** the repeated characters “34.6734.67.” The method at 1:27:00 and 1:29:46 tries a conversion before doing the multiplication. Its first conversion stands alone; don't accidentally remove it when reading the historical code. It tests whether conversion succeeds but neither stores the float nor changes the field.

Annotated teaching version — guide-added comments; callback before the later experiment:

```python
def on_button_click(self, instance):                       # A press arrives; instance is the button.
    try:                                                   # Guard conversions of the user's current text.
        float(self.someTxt.text)                           # Try '34.67' -> 34.67; discard the result.
        self.someTxt.text = str(float(self.someTxt.text) * 2)  # Read again, double, turn back into text.
    except ValueError:                                     # First float conversion failed: skip the assignment above.
        self.someTxt.text = ""                             # Clear the same field; its hint can reappear.
```

The right side of the assignment finishes before the text property gets a new value. No line prints the number. The user sees it because the existing input's text changes; on the next press this new string becomes the input. A valid float input takes the success path. Non-numeric or empty text raises **`ValueError`** at the *first* conversion; this is caught, not returned. Clearing an already empty box looks like doing nothing.

| Input before press | Inside the handler | Interface after press |
|---|---|---|
| `"34.67"` | Conversion succeeds; `34.67 * 2`, then `str(...)` | `"69.34"` |
| `"69.34"` | The next press reads the new contents; `69.34 * 2` | `"138.68"` |
| `"abc"` (teaching fixture) | Conversion raises; jump directly to the clearing assignment | Empty string; no number is displayed |
| `""` | Conversion also raises; clearing changes nothing visible | Still empty; “Enter a number” is a hint, not the value |

Evidence: 34.67 is the instructor's spoken starting input. At 1:33:00 the displayed result is `1136066.56`. A separate Python fixture using the callback arithmetic reproduced the first two rows and that displayed value after 15 doublings from 34.67; the stills do **not** count the instructor's clicks. The nonnumeric example was independently tested, not a transcription of classroom typing. Asked whether the number can double forever, the class reasons that a computer has finitely many bits. More precisely for this float operation, it cannot generate indefinitely larger *finite* numbers. In the independent fixture the text eventually became `inf` and stayed `inf` on the next press; the class did not demonstrate that endpoint here.

Finally the instructor proposes replacing the clearing action with **`pass`** and commenting out the assignment. The later frame at 1:35:00 shows the nonnumeric entry `dbnjt` and a lit button; the running window obscures the handler, so the exact edited listing and save/run sequence are not confirmed. The instructor wonders whether Kivy swallowed an exception because no console traceback appeared. In the proposed Python code the handler itself already accounts for that: **`except ValueError:`** catches the failure, and **`pass`** neither clears the box nor raises again. This does *not* establish how Kivy treats an unhandled exception.

Annotated teaching version — guide-added comments; reconstruction of the spoken comparison, not a transcription of an unobscured editor state:

```python
try:
    float(self.someTxt.text)                               # Example: 'abc' raises here.
    self.someTxt.text = str(float(self.someTxt.text) * 2)  # Not reached for 'abc'.
except ValueError:
    pass                                                   # Handle the error without changing the field.
    # self.someTxt.text = ""                               # Previous clearing action is inactive.
```

Independently verified Python comparison: `"abc"` stayed `"abc"` with the no-action handler, versus `""` with the clearing handler. Neither printed a traceback. Around 1:35:20 the instructor closes the basic Kivy introduction and offers to examine the API or more details on request.

**Key items to scan**

- **`float(self.someTxt.text)`** — convert the text, not the widget; this first, stand-alone conversion discards the number but can raise.
- **`str(float(self.someTxt.text) * 2)`** — convert again, double numerically, then create a string ready to display.
- **`ValueError`** — an empty string and nonnumeric letters both fail float conversion.
- **`except ValueError:`** — transfers control to the local handler; no uncaught exception has been established.
- **`self.someTxt.text = ""`** — changes the existing field to the empty string instead of returning or printing a message.
- **`pass`** — do nothing after catching; independent test preserved the invalid entry rather than clearing it.

## Truth tables and binary place value

### From Kivy buttons to truth tables (1:37:01–1:41:07)

The Kivy example now has a job: let someone design a black box by choosing its outputs. The instructor leaves the Kivy steps project, changes to the numerical project, and activates its environment. At 1:38:00 the PowerShell history shows these inputs, each entered separately:

```powershell
deactivate
cd ..\Numerical\
.\venv\scripts\activate
```

The prompt changes from **`(KivySteps)`** to **`(Numerical)`**. Changing the working directory picks the project; activating its virtual environment picks that project’s Python environment. The roadmap names **`binOp.py`**, a carryover from the preceding class. The instructor revisits the program and opens a work-in-progress graphical truth-table app alongside it. A student suggests CircuitVerse, used in Essentials. The instructor elects to keep working with their own tool; a CircuitVerse simulation is not demonstrated here.

At 1:39:00 the *Interactive Truth Table & Expression Generator* has two inputs and one output. The fields advertise 1–6 inputs and 1–4 outputs; those are interface labels, not ranges tested in class. Read a row from left to right: **`A`** and **`B`** describe an input case, and **`Y1`** says what comes out. The instructor calls the output Y, sometimes Q; those are labels, not operators. The rows count up in binary: 00, 01, 10, 11. The initial output is 0 on all four rows. A red output button is 0; clicking changes its value, and a green button shows 1. The instructor attributes this to a programmed click handler, not an inherent meaning of a Kivy button’s color. Dragging over the table was not selecting its text; adding separator lines was a suggestion, not a completed edit.

**Key items to scan**

- **`deactivate`**, **`cd ..\Numerical\`**, **`.\venv\scripts\activate`** — leave the old environment, switch folders, then activate the new one; **`(KivySteps)`** and **`(Numerical)`** mark different active environments.
- **`binOp.py`** — prints reference tables; it is not the graphical table generator.
- **`A`**, **`B`**, **`Y1`** — two input columns and one editable result per input pair. “Generate Table” builds the grid for the chosen counts; “Extract Expressions” is another visible control, not a result demonstrated here.

### Recognize the function by its rows (1:41:08–1:47:54)

The instructor asks what Python “function” the class is seeing. A student’s correction matters: **`&`** is an *operator*. With integer operands it tests matching bit positions, not whether two entire objects count as true. Python’s **`and`** in an **`if`** condition instead tests truthiness. For example, both 2 and 1 are nonzero, but their binary patterns, 10 and 01, have no set bit in common.

**Annotated teaching version — guide-added comments; independently run contrast:**

```python
print(2 & 1)        # 10₂ AND 01₂ has no shared 1: print 0.
print(2 and 1)      # 2 is truthy, so Python returns the second operand; print 1.
```

Independent output:

```text
0
1
```

With **one input**, there are only two rows to decide. The editor at 1:41:00 preserves four possible black boxes in a quoted text block. These are examples to read, not four calls that run. From top row to bottom row (input 0, then 1), echo gives 0,1; zero gives 0,0; one gives 1,1; opposite/not/invert gives 1,0. At 1:43:59 the instructor describes generating a one-input table and changing an output button. The input-count field and the already-generated grid are distinct: the 1:44:00 still shows a field containing 1 but a two-input grid. The unary truth values are supported by the editor and classroom discussion, not by claiming that this particular still shows two unary rows. The instructor names **`~`** for Python’s bitwise NOT. It flips integer bits; it is not a Python function returning 0 or 1 for every integer input.

With **two inputs**, set the four output buttons however you like. The trick question at 1:45:27 is not answered by naming a familiar gate. At 1:46:00 only row 01 lights up: B is greater than A. A student recognizes a one-bit magnitude comparison from work on a multi-bit comparator. Then the class identifies AND, inclusive OR, and exclusive OR by changing which cases count. The deciding question for the two ORs is: what happens when *both* inputs are 1? At 1:47:05 the both-1 button is still green: this is inclusive OR. The class goes on to describe excluding that case, not to call every box with a 1 somewhere a constant-one box.

**Annotated teaching version — guide-added comments.** The screen uses three separate loops over the same four pairs. Here is an independently run comparison using the same order; the added comparison column tests the student’s answer.

```python
for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:  # Unpack one row at a time.
    greater = int(b > a)                        # Only 0,1 makes B larger.
    both = a & b                                # A 1 survives only at 1,1.
    either = a | b                              # Include 1,1 as well as 0,1 and 1,0.
    different = a ^ b                           # Exclude 1,1: the inputs must differ.
    print(a, b, greater, both, either, different)  # Print one row, in this order.
```

Independent output (columns: A, B, B>A, AND, OR, XOR):

```text
0 0 0 0 0 0
0 1 1 0 1 1
1 0 0 0 1 1
1 1 0 1 1 0
```

The classroom version gives each operator its own table. Here are the AND lines in the editor at 1:42:00, annotated with guide-added comments. Each pass replaces **`q`** with the result for the current pair.

```python
print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")  # Two line breaks, then column labels.
print("-" * 11)                                    # Repeat one dash eleven times.
for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:       # Take the next input pair.
    q = a & b                                      # Only the final pair sets q to 1.
    print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")             # Print inputs, separator, result.
```

Independent output (initial blank lines and trailing field padding omitted):

```text
A   B  |  & and
-----------
0   0  |  0
0   1  |  0
1   0  |  0
1   1  |  1
```

The two other classroom loops keep the row layout. They change the last header and the expression assigned to **`q`**. For example, a vertical bar *inside quotes* separates printed columns; the same character *between two integers* computes inclusive OR. The comparison above lets you see precisely which rows each edit changes.

**Key items to scan**

- **`&`** — AND bit by bit. **`and`** — a different operation used for whole-condition truth testing, such as an **`if`** guard.
- **`~`** — integer bitwise NOT; the one-bit *table* “opposite, not, invert” describes just one output position.
- **`b > a`** — for the four one-bit pairs, true only when A=0 and B=1.
- **`|`** — inclusive OR; 1 OR 1 is 1. **`^`** — exclusive OR; 1 XOR 1 is 0.
- **`q`** — current result in the classroom loops. Compare its four outputs *in the same row order* before naming a box.

### Build the place-value ruler (1:47:56–1:54:17)

Which lines print the first row at the bottom of the terminal? A student suggests lines 37–44. The instructor narrows it to 38–39: the first loop prints *names* for positions; the second loop, at 42–43, computes their *weights*. In this version **`msb = 9`** sets the highest position to display, not the number of columns. Position 0 counts too. **`range(msb, -1, -1)`** includes its start, steps down by one and excludes its stop. There is nothing about base ten that prevents Python iterating over negative numbers. This range stops at 0 because its exclusive stop is −1. In the instructor’s terms the interval is closed at the start and open at the stop.

**Annotated teaching version — guide-added comments; loop text from the screen:**

```python
msb = 9                             # First exponent; the last will be 0.
print()                             # Separate the ruler from the truth tables above.
for i in range(msb, -1, -1):        # Visit 9, 8, …, 1, 0; never visit -1.
    print(f"2^{i:<4}", end='')      # Literal 2^ plus left-aligned exponent; stay on the line.
print()                             # Finish this heading row.
for i in range(msb, -1, -1):        # Start again at 9, not where the last loop ended.
    print(f"{2**i:<6}", end='')     # Evaluate the power; print it in a six-character field.
print()                             # Finish the weight row.
```

Observed terminal headings (1:49:00), reproduced by an independent run:

```text
2^9   2^8   2^7   2^6   2^5   2^4   2^3   2^2   2^1   2^0
512   256   128   64    32    16    8     4     2     1
```

The instructor leaves the different field widths as an experiment. Count the characters in the first column: **`2^`** is two literal characters and **`:<4`** gives the exponent four positions, so the next heading starts six characters later. There is no literal prefix on the numeric row; **`:<6`** supplies its whole field. The dimensions measure printed characters, not bits. The caret inside the literal heading is not evaluated at all; **`2**i`** is Python’s exponentiation expression.

**Key items to scan**

- **`msb = 9`** — fixes the leftmost displayed exponent; positions 9 through 0 make ten columns.
- **`range(msb, -1, -1)`** — start, exclusive stop, step; the first −1 sets the boundary and the second sets the direction.
- **`2^`**, **`:<4`**, **`:<6`** — two literal heading characters plus four positions align with each six-position numeric field.
- **`2**i`** — compute the weight, from 512 to 1. **`end=''`** keeps successive fields on one line; an empty **`print()`** ends it.

### Extract a bit without changing the input (1:54:17–1:56:49)

The next row is labeled **`data`**. Where does its value come from? **`displayRow(n, msb, "data")`** passes the already-defined 1000, the index 9 and a text label. Inside the function those values are called **`val`**, **`msb`** and **`label`**. First compute the integer you get by shifting the input to the right by the current index; the sought bit is now the rightmost one. AND with 1 to discard every other bit. Do not shift last iteration’s answer: every pass starts from the same original 1000.

**Annotated teaching version — guide-added comments; classroom function and call, independently run:**

```python
def displayRow(val, msb, label=""):       # A missing label defaults to empty text.
    for i in range(msb, -1, -1):          # Try each position from left to right.
        bit = (val >> i) & 1              # Move position i to the ones place; keep only it.
        print(f"{bit:<6}", end='')        # Print one digit in its matching column.
    print(label)                          # After all the bits, print the label and newline.

msb = 9                                   # Show ten bit positions.
n = 1000                                  # Plain integer literal: decimal one thousand.
displayRow(n, msb, "data")                # Print the row; it does not return a bit string.
```

Observed row (1:54:20); independent run matches:

```text
1     1     1     1     1     0     1     0     0     0     data
```

| Position i | 1000 shifted right by i | Rightmost bit printed |
|---:|---:|---:|
| 9 | 1 | 1 |
| 8 | 3 | 1 |
| 7 | 7 | 1 |
| 6 | 15 | 1 |
| 5 | 31 | 1 |
| 4 | 62 | 0 |
| 3 | 125 | 1 |
| 2 | 250 | 0 |
| 1 | 500 | 0 |
| 0 | 1000 | 0 |

The transition at position 4 is a useful check: 62 is not zero, but its last binary digit *is* zero. Testing whether the shifted integer is truthy would print the wrong bit. The loop prints ten digits; after that, its unindented-with-respect-to-the-loop label prints once. Nothing reassigns the input **`val`**.

**Key items to scan**

- **`displayRow(n, msb, "data")`** — call with 1000, 9, and **`data`**; displays one labeled row.
- **`val`**, **`msb`**, **`label`** — value examined, highest bit index examined, text printed at the right.
- **`(val >> i) & 1`** — shift first, isolate the last bit second; position 4 gives 62 then 0. The shift does not update the caller’s **`n`**.

### Find the highest place instead of guessing it (1:56:49–2:11:56)

Why start at position 9 at all? The instructor backs away from 1000 and starts with decimal 10. The question is not “what does the digit 9 tell me?” but “how far must I double the weights to cover this particular number?” The instructor suggests the procedure could make a coding challenge; that is not an assigned specification here.

Start at the right with one unit. For a positional base *b*, the rightmost weight is *b*⁰ = 1. Each step left multiplies the weight by *b*. Both “10” and “two” can describe the second binary place, provided you say which notation you are using. The editor puts powers, binary spellings and decimal weights on separate lines. Counting input *combinations* in a truth table is related, but it is not necessary to bring the whole table into this place-value calculation.

| Place from the right | Binary weight, written in binary | Same binary weight in decimal | Decimal-system weight | Octal-system weight in decimal |
|---|---|---:|---:|---:|
| First | 1 | 1 | 1 | 1 |
| Second | 10 | 2 | 10 | 8 |
| Third | 100 | 4 | 100 | 64 |

The instructor’s “there is no two in binary” is about a *digit*, not a quantity. The quantity two is written 10₂. Likewise 10000₂ is a legitimate pattern; it means sixteen, not “ten thousand.” A student notices the decimal 4 disappearing while the columns are being spaced. At 2:05:00 the repaired chart shows these five decimal weights: 16, 8, 4, 2, 1.

For decimal 10, the first weight too large is 16. Starting there, choose zero sixteens, one eight (2 left), zero fours, one two (0 left), and zero ones. **`01010`** and **`1010`** express the same unsigned quantity. The leftmost zero was optional; the final zero is not. The general stopping test, for a positive integer, is the first power of two *strictly greater* than the target. Equality still counts: eight fits in eight.

Now put 1000 back. The weights double through 256, 512 and 1024. Since 512 fits and 1024 does not, position 9 is the highest occupied one. Working left to right, write a bit for **every** remaining position. If its weight fits, write 1 and subtract; otherwise write 0 and carry the same remainder forward. This is a different algorithm from the shift-and-mask display, so its agreement with the terminal makes a useful check.

| Position | Weight | Remainder before | Digit | Remainder after |
|---:|---:|---:|---:|---:|
| 9 | 512 | 1000 | 1 | 488 |
| 8 | 256 | 488 | 1 | 232 |
| 7 | 128 | 232 | 1 | 104 |
| 6 | 64 | 104 | 1 | 40 |
| 5 | 32 | 40 | 1 | 8 |
| 4 | 16 | 8 | 0 | 8 |
| 3 | 8 | 8 | 1 | 0 |
| 2 | 4 | 0 | 0 | 0 |
| 1 | 2 | 0 | 0 | 0 |
| 0 | 1 | 0 | 0 | 0 |

At 2:11:00 the editor’s completed row and the terminal’s selected data read **`1111101000`**. Independently add only the selected weights: 512 + 256 + 128 + 64 + 32 + 8 = 1000. Don’t stop writing at the last 1. Removing the final three zeros would move all the other digits three places to the right and give 125 instead of 1000.

**Key items to scan**

- Place weight — start at 1; moving left multiplies by the base. The third place is worth 4 in binary, 100 in decimal and 64 in octal, expressed here as decimal quantities.
- **`01010`**, **`1010`** — both represent decimal 10 in binary. Dropping a leading zero preserves the value; removing a required placeholder does not.
- 512 ≤ 1000 < 1024 — proves the highest needed index is 9. The bit count is ten, not nine.
- **`1111101000`** — binary 1000; a zero at the 16 place leaves the remainder 8 unchanged, and the final three zeros keep the 1 for eight in its proper position.

### Read a hexadecimal mask four bits at a time (2:12:31–2:17:56)

The next call passes **`mask = 0x0f0`** instead of **`n`**. The **`0x`** prefix tells Python to read the following digits as hexadecimal, base 16. In ordinary decimal writing, 15 needs two symbols; in hex the *single* digit **`f`** carries that value. The screen uses lowercase letters; the instructor says A through F. Moving the webcam and spacing the editor rows are for readability; an editor completion/stray letter corrected during the discussion did not establish an extra hex digit.

Why is hex useful here? Four bits have exactly sixteen possible patterns. The four place weights are 8, 4, 2 and 1, so a hex digit can stand for a whole group without changing anything about the bits. At 2:16:00 the editor shows the hex and decimal alphabets; at 2:17:00 the binary row has reached 1001. The remaining correspondences below are the class’s spoken count completed and independently checked, not a claim that all sixteen groups already appear in that still.

| Hex | Decimal | Four bits | Hex | Decimal | Four bits |
|---|---:|---|---|---:|---|
| 0 | 0 | 0000 | 8 | 8 | 1000 |
| 1 | 1 | 0001 | 9 | 9 | 1001 |
| 2 | 2 | 0010 | a | 10 | 1010 |
| 3 | 3 | 0011 | b | 11 | 1011 |
| 4 | 4 | 0100 | c | 12 | 1100 |
| 5 | 5 | 0101 | d | 13 | 1101 |
| 6 | 6 | 0110 | e | 14 | 1110 |
| 7 | 7 | 0111 | f | 15 | 1111 |

For the mask, keep all four places per hex digit until the groups are lined up: 0 → 0000, f → 1111, 0 → 0000. Written with every group, that is 0000 1111 0000. The program displays only positions 9 through 0, so it shows **`0011110000`**, with two leading zeros rather than four. No nonzero bit has disappeared. As an independent decimal check, the rightmost hex digit contributes zero ones and the digit immediately to its left contributes fifteen sixteens: 15 × 16 = 240.

**Annotated teaching version — guide-added comments; use the preceding function definition:**

```python
mask = 0x0f0                    # Integer 240, entered with hex digits rather than decimal.
displayRow(mask, 9, "mask")     # Keep the ten display positions; put mask at the right.
```

Observed mask row (2:16:00), also independently reproduced:

```text
0     0     1     1     1     1     0     0     0     0     mask
```

**Key items to scan**

- **`mask = 0x0f0`**, **`0x`** — the prefix specifies base 16; the three remaining digits describe the mask, not the display width.
- **`f`** — decimal 15, binary 1111. For comparison, d is 1101 because 13 = 8 + 4 + 1.
- **`0011110000`** — ten-bit display of 240. **`n`** is still 1000; passing a different integer to the same function does not overwrite it.

## Bitwise operations and what the display leaves out

### Why the mask is written in hex (2:18:00–2:24:51)

What number is the mask **`0x0f0`**? The prefix says to read the following digits in base sixteen. One hex digit can stand for exactly four binary places: four switches have sixteen possible settings, exactly enough for the symbols 0 through 9 and a through f. The instructor's editor chart at 2:19:00 lines up each hex symbol with its decimal value and its four-bit spelling. It gives a short route from a mask you can type to a pattern you can recognize.

First the class takes the longer route through place value. The rule is familiar from decimal: keep the exponents and change the base. The rightmost place still counts ones. Moving left now multiplies the previous weight by sixteen, not ten. The instructor initially says 64 for sixteen squared, then corrects it to 256; the class checks 4,096 and corrects a spoken stumble to 65,536. A student catches another problem: the final 1 has slipped to the next editor line. Aligning the columns matters as much as doing the multiplication.

| Place, left to right | Sixteen to the fourth | Sixteen cubed | Sixteen squared | Sixteen to the first | Sixteen to the zero |
|---|---:|---:|---:|---:|---:|
| Decimal weight | 65,536 | 4,096 | 256 | 16 | 1 |
| Digits of the padded mask | 0 | 0 | 0 | f | 0 |
| Contribution | 0 | 0 | 0 | 15 × 16 = 240 | 0 × 1 = 0 |

The zero on the **right** matters: it puts the f in the sixteens place. Zeros on the left just pad the representation. Now take the short route: f becomes **`1111`**, the following 0 becomes **`0000`**, giving eight bits **`11110000`**. In the program's ten columns the same integer is **`0011110000`**. This is why a programmer might write a mask in hex rather than decimal: the four adjacent ones are easier to recognize than they are in the spelling 240. The place-value work is editor scratch, not a Python calculation that printed 240.

**Key items to scan**

- **`0x0f0`** — the source spelling for 240; the leading zero after the prefix does not change the value.
- **`1111`** and **`0000`** — the four-bit spellings of f and 0. Keep the zero digit's *four* positions when joining groups.
- **`11110000`** and **`0011110000`** — the same mask, padded to eight and ten positions respectively; bits 7 through 4 are on.

### Whose bits get inverted? (2:24:55–2:28:55)

The program has two inputs: **`n`** holds 1000, the data, and **`mask`** holds 240. Only one is involved in **`~n`**. The tilde flips every bit of the data, and the assignment puts that new integer into **`result`**. It does not change either input. The terminal's row labeled “not” is a view of positions 9 through 0, not the decimal value of the whole result.

Annotated teaching version — guide-added comments; the final two print calls are a separate check of what the ten-place view includes.

```python
n = 1000                   # Data: the ten low bits are 1111101000.
mask = 0x0f0               # A separate integer: 0011110000 in ten columns.
result = ~n                # Complement the data only; n still refers to 1000.
print(result)              # Full Python integer, not the ten-column row.
print(f"{result & 1023:010b}")  # Keep just ten low bits and show all ten.
```

Independently verified output:

```text
-1001
0000010111
```

A student, recalling network-address masks, asks why the 16's column is lit in both the mask and the NOT row. Follow that *same column* in the source row: the data has 0 there, so its complement has 1. The mask's 1 is irrelevant. This question exposes two different mistakes: comparing with the wrong input, and reading the positive value of the visible low bits (23) as the result of complementing the whole Python integer (−1001). The instructor proposes a clickable display to make comparisons easier; none is demonstrated here.

**Key items to scan**

- **`n`** — the data, 1000, remains available unchanged for every following calculation.
- **`mask`** — 240, shown for comparison but not used by NOT.
- **`~n`** — unary bitwise NOT: one input, each bit inverted; the result is −1001.
- **`result`** — a name rebound to each answer, not a cumulative change to the original data.

### AND, inclusive OR, exclusive OR: read one column at a time (2:28:55–2:33:32)

Now pair each data bit with the mask bit **in the same place**. Unlike arithmetic addition, these operations have no carry into a neighbor. With **`n & mask`**, a mask 1 lets the corresponding data bit through; a mask 0 forces that result bit to 0. Here the ones that pass through have weights 128, 64 and 32. The mask also admits the 16's position, but the data there is 0. Those three surviving weights add to 224.

With **`n | mask`**, *either* 1 is sufficient, including two ones. During the walkthrough a student notices the pointer hovering at exclusive OR while the class is reading inclusive OR. A mixed pair gives 1 for both, so reading the wrong row can go unnoticed until a pair of ones. The instructor separates result rows by adding **`\n`** to selected labels, sets aside the non-Python scratch work, and reruns the program. The data, mask and calculations stay the same. With **`n ^ mask`**, exactly one of the pair must be 1; two ones now give 0. Equivalently, ones in this mask tell XOR which four data bits to toggle.

Annotated teaching version — guide-added comments; example prints the full results as decimals so the column trace can be checked.

```python
n = 1000                     # Same data on each line, not the previous answer.
mask = 0x0f0                 # Select the positions with weights 128, 64, 32, 16.
result = n & mask            # Keep only the data ones where the mask is one.
print(result)                # 224: 128 + 64 + 32.
result = n | mask            # Set all four mask positions; only the 16 bit is newly set.
print(result)                # 1016: 1000 + 16.
result = n ^ mask            # Turn off 128, 64, 32 and turn on 16.
print(result)                # 792: bits 9, 8, 4 and 3 are on.
```

Observed terminal bit rows (2:30:00 compact; 2:35:00 spaced). Decimal results at right were independently verified, not printed beside the classroom rows.

| Row | 512 | 256 | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | Decimal |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| data | 1 | 1 | 1 | 1 | 1 | 0 | 1 | 0 | 0 | 0 | 1000 |
| mask | 0 | 0 | 1 | 1 | 1 | 1 | 0 | 0 | 0 | 0 | 240 |
| and | 0 | 0 | 1 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | 224 |
| or | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 0 | 0 | 0 | 1016 |
| xor | 1 | 1 | 0 | 0 | 0 | 1 | 1 | 0 | 0 | 0 | 792 |

To check any new pair of integers by hand, line up the units places first. Use the 128's column here to distinguish the two ORs, the 16's column to see a 1 supplied by the mask alone, and the 1's column to see what happens when neither input has a 1.

Here is the spacing change in isolation (simplified teaching example, guide-added comments). The class helper ends each row with a label print, so changing the *label* suffices:

```python
print("and\n")               # Embedded newline ends the label; print adds another.
print("or")                  # The next label appears after a blank line.
```

Independently verified output, including the empty line:

```text
and

or
```

**Key items to scan**

- **`n & mask`** — keep common ones; returns 224.
- **`n | mask`** — keep any ones; returns 1016, including positions already set in both inputs.
- **`\n`** — adds a line break inside selected labels; improves row tracking, not the arithmetic.
- **`n ^ mask`** — keep differing bits; returns 792, not the inclusive-OR result.

### Shift the data, then ask what fits in the display (2:33:38–2:37:10)

Three is a demonstration choice, not another mask. For **`n << 3`**, the class starts from the least significant end. The original bit at position 0 goes to 3; the original 1 at position 3 goes to 6. Positions 2, 1 and 0 are filled with zeros. For **`n >> 3`**, the class starts at the other end: the 1 in position 9 moves to position 6; the next four high ones follow it down. The former bit at position 3 reaches the ones place. The three original low zeros drop off the right. Both operations start from 1000.

Annotated teaching version — guide-added comments; the low-ten-bit mask models a ten-switch register with an ordinary Python integer.

```python
n = 1000                       # Start both shifts from 1111101000.
left = n << 3                  # Move every original bit to a weight eight times larger.
right = n >> 3                 # Move the original bits right, not the bits of left.
print(left, right)             # Full integers: 8000 and 125.
print(f"{left & 1023:010b}")    # Show positions 9..0 of 8000; omit positions above 9.
print(f"{right & 1023:010b}")   # 125 fits; pad the high positions with zeros.
```

Independently verified output:

```text
8000 125
1101000000
0001111101
```

| Follow one position | Left three places | Right three places |
|---|---|---|
| Original 1 at bit 9 (512) | Bit 12; outside the ten printed places | Bit 6 (64), visible |
| Original 1 at bit 3 (8) | Bit 6 (64), visible | Bit 0 (1), visible |
| New or discarded positions | Three new low zero bits | Three old low bits discarded; high places show zero for this positive input |

The instructor describes bits falling “into the abyss” beyond the available storage. That is a useful fixed-width picture: the displayed left-shift row represents 832 if read as ten unsigned bits. But ordinary Python integer left shift still produced 8000; the display routine did not print its high bits. A **rotate** is a different operation on a fixed-width pattern: a bit leaving one end reappears at the other. No rotation is performed by either shift here.

**Key items to scan**

- **`n << 3`** — 8000 as a Python integer; the displayed ten bits are 1101000000.
- **`n >> 3`** — 125 from the original 1000; the displayed ten bits are 0001111101.
- **Rotate** — wrap a bit around a chosen width instead of dropping it; mentioned as a contrast.

### How the display reads exactly one bit (2:37:24–2:42:06)

Why does **`displayRow`** need both a shift *and* an AND? Imagine the second pass through its loop. Shifting 1000 right eight places gives 3, binary 11. The right-hand 1 is the bit the instructor wants to print; the other 1 belongs to a more significant place already displayed. The expression **`(val >> i) & 1`** zeros everything except the new ones place. It works even when the desired bit is zero and there are many ones above it.

Annotated teaching version — guide-added comments; source helper and one classroom call, used here on their own.

```python
def displayRow(val, msb, label=""):     # Value, highest bit index, optional row name.
    for i in range(msb, -1, -1):        # 9, 8, ..., 0; stop before -1.
        bit = (val >> i) & 1            # Move original bit i to position 0; keep only it.
        print(f"{bit:<6}", end='')      # Left-align in six spaces; do not end the row yet.
    print(label)                        # Append the row name and end the line once.

displayRow(1000, 9, "data")             # Ten columns from the same unchanged integer.
```

Observed row content (2:38:00); the separate run of the helper gives the same line:

```text
1     1     1     1     1     0     1     0     0     0     data
```

| Loop pass: bit index | 9 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1000 shifted right by that many | 1 | 3 | 7 | 15 | 31 | 62 | 125 | 250 | 500 | 1000 |
| After AND with 1: bit printed | 1 | 1 | 1 | 1 | 1 | 0 | 1 | 0 | 0 | 0 |

At index 4, 62 is not zero, but its low bit *is* zero. Testing whether the whole shifted integer is nonzero would therefore print the wrong bit. The loop starts afresh from the original value each time; it never assigns the shift back to that value. The heading loops apply the same countdown to print first the power labels, then their numeric weights, so every printed bit has a named column.

Annotated excerpt — guide-added comments. Compare the caret inside the first string (ink on the screen) with the exponentiation expression inside the second string's braces (a calculation).

```python
msb = 9                                  # Ten positions, starting at index 9.
for i in range(msb, -1, -1):             # Walk from the 512's place to the 1's place.
    print(f"2^{i:<4}", end='')           # Literal 2^ prefix plus the index in a four-space field.
print()                                  # End the symbolic heading before printing the weights.
for i in range(msb, -1, -1):             # Reset i to 9 and walk the same positions again.
    print(f"{2**i:<6}", end='')          # Compute the weight and align it across six spaces.
print()                                  # Finish this row before displaying data.
```

Observed headings (2:35:00), also independently reproduced; trailing padding omitted:

```text
2^9   2^8   2^7   2^6   2^5   2^4   2^3   2^2   2^1   2^0
512   256   128   64    32    16    8     4     2     1
```

**Key items to scan**

- **`displayRow`** — prints the selected positions from left to right, then the row name; it does not return a new integer to the caller.
- **`range(msb, -1, -1)`** — include position 0; the exclusive stop is −1.
- **`(val >> i) & 1`** — shift the bit into place, then discard all other positions; the only possible results are 0 and 1.
- **`{bit:<6}`** and **`end=''`** — align each digit with its heading and keep the digits on one line.
- **`2**i`** — computes the weight in the second heading; the caret in the first heading is literal text.

### Byte order is not the base (2:42:06–2:45:29)

The diagram assumes the most significant printed bit is on the left. Does storage have to put the most significant part first too? The class recalls Jonathan Swift's *Gulliver's Travels*: people disagree over which end of an egg to open. The names **big-endian** and **little-endian** preserve that “which end first?” question. Specify the unit. The instructor talks about bit sequences and sending data; Python's **`sys.byteorder`** specifically reports the native order of *bytes* in a multibyte value. It does not reverse the digits printed inside a byte or establish the order a network protocol uses. Supplemental reference: [Python’s definition of native byte order](https://docs.python.org/3/library/sys.html#sys.byteorder).

Annotated teaching version — guide-added comments; the first print is from the classroom script. The last two are a safe, separately verified example using 1000, not a memory read.

```python
import sys                                   # The same module the class recalls using for argv.
print(f"System byte order is {sys.byteorder} endian.")  # Insert this interpreter's native order.
n = 1000                                     # Hex 03e8: one high byte 03, one low byte e8.
print(n.to_bytes(2, 'little').hex(' '))       # Ask for least-significant byte first.
print(n.to_bytes(2, 'big').hex(' '))          # Ask for most-significant byte first.
```

Observed first line from the classroom run (2:44:00): `System byte order is little endian.` The separate example printed `e8 03` and `03 e8` for the last two lines. Neither spelling changes the number 1000. Picking an explicit order is what lets a reader interpret the bytes consistently.

**Key items to scan**

- **Big-endian** — the higher-weight byte comes first: 03 then e8 in this example.
- **Little-endian** — the lower-weight byte comes first: e8 then 03.
- **`sys.byteorder`** — the native byte-order string, `little` on the classroom system; the alternative is `big`.

### What the “16” in the second line measures (2:44:07–2:50:59)

A student asks whether 16 means the base. The instructor's response is a debugging method: look at the expression that generated the message. The script does not convert a number into hex. It adds **two sizes in bytes**. At 2:44:00, **`py .\src\byteOrder.py`** has printed both a byte-order string and a line about a header. Those lines answer different questions.

Annotated teaching version — guide-added comments; split the size expression into named intermediates to show what was counted. This only asks about sizes of types; it does not inspect any object's storage.

```python
import ctypes                                 # C-compatible type descriptions for this interpreter.
count_bytes = ctypes.sizeof(ctypes.c_ssize_t)  # Width of the signed size type, here 8 bytes.
ptr_bytes = ctypes.sizeof(ctypes.c_void_p)     # Width of a pointer type, here 8 bytes.
pyObjectHeaderSize = count_bytes + ptr_bytes   # Two assumed fields: 8 + 8, not a whole object.
print(f"The PyObject header size on this system is {pyObjectHeaderSize}")
```

Observed classroom line: `The PyObject header size on this system is 16`. A separate run also found widths of 8 and 8. The classroom script labels the sum a **`PyObject`** header size; it calculates a model, not a universal measurement. Supplemental clarification: in the usual CPython model these two roles are a reference count and a reference to the object's type, not a stored copy of the object's own address. The [Python C API’s description of common object structures](https://docs.python.org/3/c-api/structures.html#c.PyObject) distinguishes builds; the size of these two fields is not the size of all the data in a particular list or string.

Why qualify it? Python is a language; the interpreter running a Python program is itself a program. The class discusses different implementations, including one written in Java. Saying “I'm running Linux” identifies an operating system, not the implementation or its exact object layout. It is reasonable to expect CPython on many machines, but that is not a check. The instructor introduces the C interface in that implementation-dependent context, not as a requirement of Python assignments.

The editor then acquires three lines. Here **`a`** successively names three different objects; it is not an integer cell that morphs into a string and then into a list. The class describes an ID or a place in memory to explain the reference. At the language level, the value one works with is the object, not a portable numeric address. Its type helps determine what data it carries and how that data is organized; the string length or the number of list entries can also affect storage.

Annotated teaching version — guide-added comments; observed assignments, not a recorded printout.

```python
a = 3          # Name the integer 3.
a = "hello"    # Rebind the same name to a string, without changing the integer.
a = [23]       # Create a new list containing one integer, 23; rebind a to that list.
```

Expected effect: the final value of **`a`** is the one-element list **`[23]`**. None of these assignments prints anything. The earlier two terminal lines should not be treated as a run of this later edit.

**Key items to scan**

- **`py .\src\byteOrder.py`** — the invocation with the two observed output lines, not the later rebinding edit.
- **`ctypes.sizeof`** — reports the byte width of each supplied C type, not a count of hex digits.
- **`ctypes.c_ssize_t`** and **`ctypes.c_void_p`** — signed-size and pointer types whose widths the source adds.
- **`PyObject`** — the CPython object-header concept modeled by the sum; 16 is the reported byte count on this system, not a promise for all builds.
- **`a`** — same name, successively referring to an integer, a string and a list.
- **`[23]`** — one list element, not a list of length 23 and not a printed output.

## From machine precision to a geometric bound on π

### 2:51:00–2:54:44 | Changing what a name refers to is not changing an object's identity

The three assignments just shown leave **`a`** referring to a list containing 23. The instructor now distinguishes that change from changing an object's identity. Identity stays the same during its lifetime; **`id()`** reports an identity token. In CPython the token corresponds to an address, but Python programs need not treat it as one. When the instructor first says “mutable” and then corrects it to “immutable” at 2:52:36, distinguish this stable identity from both rebinding a name and changing the contents of a mutable object. The instructor contrasts C pointers, which can be advanced through storage, with C++ references, which cannot be reseated to refer to another object. That does not make the referred-to object immutable. Bypassing the interpreter's protections can corrupt the running program; nothing in the assignments requires doing so.

The instructor also corrects “64-bit operating system” to **64-bit Python program** (2:53:10). The editor uses **`sys.byteorder`** to report byte order and **`ctypes.sizeof()`** to ask about C type widths in the running interpreter. The class models a basic CPython header as a signed size field plus a type-pointer field. The calculation counts bytes of *types*; it does not inspect a particular object's contents. This keeps the interpreter example separate from the portable behavior of the assignments above.

The earlier two output lines remain visible in terminal scrollback at 2:55:00; they do not establish a rerun after adding the assignments. The first line describes byte ordering within a multibyte value; it is not a measurement of pointer width. The second reports the example's sum, not the total size of an integer, string or list, nor a promise about every CPython version or build. A separate size-only check returned 8 bytes for each field and the same sum on the guide's interpreter. Using plain Python normally keeps machine widths out of application logic. Once code depends on a C layout, an assumption that worked on one build can be wrong on another.

**Key items to scan**

- **`byteOrder.py`** — the byte-order and header-size example, not a π calculator.
- **`a`** — one name rebound from **`3`** to **`"hello"`** to **`[23]`**; no list element is changed or printed.
- **Identity**; **`id()`** — stable for the object's lifetime, not a portable invitation to use its address.
- **64-bit Python program** — the instructor's corrected unit of comparison is the running interpreter, not just its host OS.
- **`sys.byteorder`** — reports **`little`** in the displayed run. Little-endian means least-significant byte first in storage.
- **`ctypes.sizeof()`** — returns a type's size in bytes; **`pyObjectHeaderSize`** is the sum for this illustration.

### 2:54:49–2:59:20 | How many digits of π are known, and how many do you need?

A student notices a document about π and asks why it belongs in this project. The class starts with **3.14** and guesses how many digits have been computed. At 2:56:10 the instructor reads “314 trillion.” That is a number reported *during class*, not a verified current record, and computing digits is a different feat from memorizing them. More useful for designing a computation is a different question: how many digits could change the decision you have to make?

Imagine aiming a probe at Jupiter. A small error in direction can mean a large miss after a long flight. Guessing “five or six decimals” doesn't establish an error budget; neither does insisting on a trillion. In Slim PDF Reader at 2:59:00, a passage identifies Marc Rayman, JPL's former Chief Engineer for Mission Operations and Science. For JPL's highest-accuracy interplanetary-navigation calculations, it gives **3.141592653589793**. The instructor counts the places after the point: 15 decimal places, 16 significant digits including the leading 3. The passage starts a Voyager 1 distance example; it does not show those digits being calculated or a trajectory being simulated.

Guide example, independently calculated — hold the diameter at 100 metres and change only the approximation to π. The added example illustrates error scale, not a spacecraft calculation.

| Approximation used | Circumference, diameter × approximation | What changes? |
|---|---:|---|
| 3.14 | 314.0000 m | A convenient first estimate. |
| 3.141592653589793 | About 314.1593 m | About 0.1593 m more around this circle. |

For a circumference, the error from using 3.14 scales with the diameter. Whether that matters depends on the permitted error in metres. The number of *available* digits alone cannot answer that question.

**Key items to scan**

- **3.14** — a rounded value, not the definition of π and not automatically accurate enough for every task.
- **3.141592653589793** — the value printed in the classroom JPL reading; count 15 places *after* the decimal point.
- **314 trillion** — an in-class report about computation, not a navigation requirement, memorization total, or current-record claim.
- **Voyager 1 / Jupiter** — Voyager appears in the document; Jupiter is the instructor's hypothetical destination.

### 2:59:32–3:07:21 | Before calculators: what quantity are we trying to find?

The next question reverses the first: where did even three correct digits come from? A student who searched for the history of π asks why the number exists. The answer isn't the Greek letter or its decimal expansion. Wrap a string once around a circle and straighten it; its length is the **circumference**. Compare that with the **diameter**, the straight distance across the circle through the center. Scale up the whole circle and both lengths increase by the same factor. The quotient **π = C/d** stays the same. An irrational number has no exact ratio of two integers; the student recalls the decimal consequence: its digits neither terminate nor eventually repeat. There is no contradiction: two *lengths* can have a ratio that isn't a ratio of integers.

The class distinguishes the old quantity from the later popularity of its symbol. A student mentions 1737 in connection with wider use of the name or symbol, not the discovery of the ratio. The conversation mentions Babylonian, Indian and Chinese mathematical traditions, and surveying, navigation and taxes as reasons people needed practical numerical methods. Archimedes enters for a particular advance: using geometry to put the ratio **between two bounds**, not inventing all earlier circle measurement. The student's fractional endpoints are indistinct in the captions; the reliable idea is an inner and an outer bound. At 3:04:48–3:05:00 the class Discord shows a student-shared [Exploratorium history-of-π link](https://www.exploratorium.edu/pi/history-of-pi); the resource was shared, not shown being read aloud in full.

Why this discussion in a programming class? The instructor wants students to develop intuition for numerical methods before calculus. There's a difference between asking for more digits and representing them: a fixed-width floating-point number has only so many bits for one value. More free RAM does not give that same value extra bits. Extra software can represent one number using more storage, but that changes the representation. The instructor plans to return to float encoding and its limits, then to square roots and a first-principles π calculation. In **`Roadmap.md`**, the π reading is highlighted at 3:06:00, **`squareRoot.py`** at 3:07:00, and **`piCalc.py`** at 3:08:00. Selecting a filename *in a text document* is not opening or executing that program.

**Key items to scan**

- **Circumference** — the length around; **diameter** — the length through the center, twice the radius.
- **π = C/d** — dimensionless ratio, the same for any circle in plane geometry.
- **Between two bounds** — estimate something curved with lengths you can calculate on both sides; no calculus needed to get started.
- **`Roadmap.md`** — planning notes for a numerical project, not a transcript of successful runs.
- **`squareRoot.py`** — square-root calculation was deferred. **`piCalc.py`** — first-principles π calculation was previewed, not run.

### 3:07:21–3:17:43 | Which polygons make useful bounds?

Work in the plane: a circle is not a sphere, and the figure to fit around it is a **polygon**, a closed shape made of straight sides. A student guesses four sides are needed. Three suffice: a triangle closes, while two straight segments cannot enclose an ordinary planar polygon. The class works up through a quadrilateral (square as one example), pentagon, hexagon, septagon, octagon, nonagon and decagon. The earlier number-base discussion provides a memory cue for *hex*, six; a hexagon has six sides, not sixteen.

Use *regular* polygons here: all sides and angles are equal. “Inscribed” and “circumscribed” are easy to reverse, so ask *which part touches the circle?* At 3:11:31–3:12:47 the students reason about the contact points, not just which outline looks more circular.

| Same fixed circle | Which parts touch? | Why it bounds the circumference |
|---|---|---|
| Inscribed regular polygon | Its vertices lie on the circle. Each straight side is a chord joining two neighboring vertices. | The chord cuts inside its arc. Adding the chords gives the shorter perimeter. |
| Circumscribed regular polygon | Each side is tangent to the circle at its midpoint. Its vertices are outside. | The outer perimeter encloses the circle and is longer; it supplies the upper bound. |

A student favors a square for its manageable sides and an octagon for looking rounder. But the best *starting* shape need not look roundest. Which has side lengths we can already relate to the circle's radius? The class recalls right, isosceles and equilateral triangles, the triangle angle sum of 180°, and the hypotenuse opposite a right angle. Angles, not sides, are measured in degrees. The instructor brings up the **3:4:5** side ratio as an example of familiar geometry, not the proportions of the triangle the class will actually use. The class also connects Babylonian base 60, a 360° turn, and the 60° angles of an equilateral triangle. That last connection points to a hexagon. The instructor considers finding or making a drawing app at 3:11:58, but asks the class to picture the construction instead. When a student flags the approaching next class at 3:17:12, the instructor defers a fuller treatment and gives the geometric starting point before they leave.

**Key items to scan**

- **Polygon** — a triangle qualifies; a sphere is not the planar figure being bounded.
- **Inscribed** — corners on the circle, sides inside; inner perimeter is too short.
- **Circumscribed** — sides touch at their midpoints for this centered regular shape; outer perimeter is too long.
- **3:4:5** — a right-triangle side ratio recalled in discussion, not the ratio for half an equilateral triangle.
- **360° / 60°** — six equal turns around the center, one per side of a regular hexagon.

### 3:17:43–3:22:36 | From six triangles to an interval containing π

Picture a regular hexagon inside the circle. Draw a radius from the center to each of its six vertices. Consecutive radii enclose **60°**. Since both radii have the same length, the other angles in that triangle are equal; they must each be 60° as well. This makes each of the six triangles equilateral. The surprise is that the straight side of the hexagon is exactly one radius long. We don't need to know π to find that side.

Now draw a line from the center to the midpoint of one side. It bisects the equilateral triangle into two right triangles. Each has a radius as its hypotenuse and half the original side opposite its 30° angle. At 3:20:07 a student proposes that *both* legs are half. Only the base was halved: the perpendicular dropped from the center is a different length. The instructor moves on without deriving it. Here is the missing comparison with a concrete radius.

Guide reconstruction from the spoken geometry; values independently calculated for radius 1, diameter 2. No such drawing, numeric table or output is visible on the classroom screen.

| Step | Length or operation | What it gives you |
|---|---|---|
| 1. Inside: count the sides | 6 sides × radius 1 = 6 | Inner perimeter 6. Divide by diameter 2 for the lower bound 3. |
| 2. Halve one center triangle | Hypotenuse 1; half-side 1/2; perpendicular √(1 − 1/4) = √3/2 ≈ 0.8660 | A 30°–60°–90° triangle. Its perpendicular is **not** 1/2. |
| 3. Outside: draw the radius to a tangency point | The radius of length 1 now serves as the perpendicular leg. The outer half-side divided by this radius equals (1/2)/(√3/2) = 1/√3 | The outer and inner half-triangles have the same angles. Use their side ratio, not the inner side length. |
| 4. Reassemble the outside | Outer side 2/√3; six sides give 12/√3 ≈ 6.9282 | Divide by the same diameter 2: the upper bound is 6/√3 ≈ 3.4641. |

Thus **3 < π < 2√3**. The exact outer bound is 2√3; its printed decimal is only a rounded approximation. The perimeters themselves bracket the *circumference*. Dividing both by the diameter is the step that turns a length comparison into a statement about π. This is the qualification behind the student's “where they meet is pi” and the instructor's “ish” at 3:22:09.

What if we add a seventh side, an eighth, and so on, as the instructor proposes at 3:21:05? Keep the same circle and redistribute the vertices evenly each time. Each individual side becomes shorter, but there are more of them. Inside, the *whole* perimeter grows toward the circumference; outside, the whole perimeter shrinks toward it. No finite straight-sided polygon becomes the circle.

Independent numerical check of the class's proposed progression (rounded to five decimals, not historical output):

| Regular polygon | Inner perimeter ÷ diameter | Outer perimeter ÷ diameter | Gap between estimates |
|---|---:|---:|---:|
| 6 sides | 3.00000 | 3.46410 | 0.46410 |
| 7 sides | 3.03719 | 3.37102 | 0.33384 |
| 8 sides | 3.06147 | 3.31371 | 0.25224 |
| 9 sides | 3.07818 | 3.27573 | 0.19755 |
| 10 sides | 3.09017 | 3.24920 | 0.15903 |

For checking only, those columns were calculated with modern trigonometry: for *n* sides, **n sin(π/n)** and **n tan(π/n)**. The angle π/n is half one central angle, in radians. These formulas use a known π, so the table verifies the *trend*, not a way of discovering π from scratch. The hexagon argument above does not need π as an input. Stop when the two bounds are close enough for the required accuracy; it is not necessary to carry the construction to infinity.

**Key items to scan**

- **60°** — angle at the center between adjacent vertices; produces six equilateral triangles.
- **1, 1/2, √3/2** — hypotenuse, short leg and long leg after bisecting one triangle in the radius-1 example.
- **3 < π < 2√3** — the inner and outer hexagon perimeters divided by the diameter; a bound, not an equality.
- **n sin(π/n)** and **n tan(π/n)** — modern independent checks for the inner and outer ratios, not formulas shown or used in class.
- **Stopping rule** — use a tolerance in the quantity you care about; narrower bounds give a reason to trust more digits.

### 3:22:36–3:24:07 | Two different reasons to stop

An engineering tolerance tells you when more digits aren't *needed*. The number representation can also impose a point beyond which more digits can't be *stored*. The instructor closes with **ULP**, “unit in the last place”: around a given floating-point value, how big a step is it to the neighboring representable value? If a refinement is smaller than the available spacing, recording another iteration in the same precision may give the same stored value. That plateau alone is not a mathematical proof that the bounds coincide.

Independent example, not a classroom run: in the guide's Python, the next representable float **above** 3.141592653589793 displayed as 3.1415926535897936; the gap was approximately 4.44 × 10⁻¹⁶. More memory does not change the spacing of that float format. The roadmap names **`doc/IEEE Standard 754-2019.pdf`** and **`ulp.py`**. Floating-point encoding and how to calculate square roots remain future work here. At 3:23:50–3:24:03 the instructor signs off and tells viewers to take a break; the closing capture shows OBS's recording control, not another mathematical result or a new assignment.

**Key items to scan**

- **ULP** — spacing at the scale of the stored number, not a universal fixed decimal error.
- **`doc/IEEE Standard 754-2019.pdf`** — the floating-point standard listed in the roadmap, not a standard derived in the lesson.
- **`ulp.py`** — a roadmap entry, not evidence of an executed ULP demonstration.

## Syntax to remember

| Pattern | Meaning in this session |
|---|---|
| `class KivySteps(App):` | Define a subclass. The parentheses here name a base class; they do **not** launch the app. |
| `KivySteps().run()` | Construct one app object, then enter its inherited run method. |
| `rootLayout.add_widget(titleLab)` | Attach the existing label to the widget tree. Constructing a widget and displaying it are distinct steps. |
| `size_hint_y=None` | Stop asking for a proportional share on this axis. It does not by itself request a height fitted to the text. |
| `doubleBut.bind(on_press=self.on_button_click)` | Give Kivy the bound method to call later. Adding `()` here would call it during setup instead. |
| `except ValueError:` | Select the response to a failed conversion; `pass` in that handler does **not** re-raise the error. |
| `git diff HEAD~ -- .\src\steps.py` | Compare the working file to the preceding commit. The `--` separates the revision from the file path. |
| `range(msb, -1, -1)` | Count down to position zero. The stopping value, −1, is excluded. |
| `2**i` versus `2^i` | In executable Python the first means exponentiation; the second means XOR. A printed `2^9` is just a label. |
| `(val >> i) & 1` | Move the wanted bit to the units position, then keep only that position. |
| `0x0f0` | A hexadecimal mask: the `f` selects positions 7 through 4. Its spelling does not specify a storage width. |
| `end=''` | Print the next item on the same line so successive calls can build one display row. |
| `sys.byteorder` | Ask the running interpreter about the native byte order, rather than inferring it from a printed binary number. |

## Knowledge check

1. A label and an input have both been constructed. What determines which one appears first in the box? If a button is anchored at the top, the top of *what*?
2. The field shows `Type here` before the user types. Does that prove the callback will receive those nine characters? Trace one click with `34.67`, then one with `hello`. How would changing the exception handler to `pass` change the second result?
3. A student wants to read bit 3 of the decimal integer 1000. Why is shifting right by three positions not enough? Which single bit will the student obtain?
4. Is the `f` in `0x0f0` a variable? Explain the mask without converting the data to decimal. Which four original bit positions survive an AND?
5. Two systems disagree about which byte of a multi-byte value is stored at the lowest address. Could they nevertheless agree on the integer and its printed binary representation?
6. An inscribed polygon and a circumscribed polygon give two estimates. Which is the upper bound, what quantity do the perimeters bound, and what must you divide by to bound π?

### Answer key

1. Constructing widgets makes objects; adding them to a particular parent places them in its layout. The order of additions and the box orientation matter, not simply the order of the constructor calls. An anchor acts within the rectangle *its own parent allocated it*. Its top need not be the top of the application window.
2. No. A hint is a prompt displayed when the actual text is empty. For `34.67`, conversion to a float succeeds, multiplication gives `69.34`, and conversion back to a string supplies replacement **field text**, not console output. `float("hello")` instead raises `ValueError` before a replacement value has been computed. The clearing handler sets the field to `""`. A handler that just executes `pass` still catches the exception but leaves the old text alone.
3. `1000 >> 3` is `125`, binary `1111101`: the original bit 3 is now at the right, but several higher bits remain. `125 & 1` keeps only that last bit, giving `1`. The two steps answer different questions: *where is the bit now?* and *which bits am I keeping?*
4. `f` is a base-16 digit representing four one-bits. The low eight mask bits are `1111 0000`; the higher positions are zero here. AND with this mask preserves positions 7, 6, 5 and 4, counting from bit 0 on the right. It clears every other position even if the data had a one there.
5. Yes. **Byte order** describes how successive bytes occupy memory addresses. It doesn't redefine the numerical weights of the bits or reverse a printed numeral. Inspecting byte order and computing a numerical shift answer different questions.
6. The outer polygon has the larger perimeter; the inner polygon's perimeter is below the circle's circumference. Divide both by the same diameter to bound circumference divided by diameter, π. More sides narrow the interval; agreement to the required precision is a usable stopping condition, not proof that a finite polygon has become a circle.

## Library of definitions

| Term | Meaning in this session |
|---|---|
| Application, widget, root | The application manages the GUI run. A widget is one interface component, such as the editable field. The root returned from `build()` is the top of the widget tree the app displays. |
| Inheritance | A subclass uses behavior supplied by its base class and can provide a different implementation of a method such as `build()`. |
| Layout, parent, child | A layout allocates rectangles for the widgets added to it. A nested layout is both a child receiving space and a parent dividing that space. |
| Hint text | Placeholder shown when the input is empty; not a default numerical value. |
| Size hint vs. position hint | One describes how a child should use available size; the other suggests placement. The parent layout determines which hints it honors. |
| RGBA, alpha | Three channels specify amounts of red, green and blue light; the fourth controls opacity. A darker opaque color isn't the same setting as a transparent bright color. |
| Callback, binding | Binding connects an event to a function or method. A callback is the callable invoked when the event happens, rather than when the interface is constructed. |
| Local name vs. instance attribute | A local name inside `build()` is not automatically visible to a different method. An attribute of `self` can let both methods reach the same text widget. |
| Exception handler | Code selected when a matching exception is raised. A handler may replace a value, do nothing, or take some other action; catching is not the same as correcting the input. |
| Truth table | A row for each input combination, showing the resulting output. One row is a test case; all rows together distinguish operations such as inclusive OR and XOR. |
| Base, place value, most significant bit | The base determines the multiplier from one position to the next. The most significant bit is the highest-weight bit in the chosen representation or field. |
| Bit, nibble, byte | One binary digit; four bits (one hex digit); eight bits, respectively. Leading zeros can show a field boundary without changing the value. |
| Mask, shift | A mask specifies selected bit positions; a shift moves bits to different positions. Applying a mask does not edit the original integer unless the resulting value is assigned somewhere. |
| Endianness | The order of the bytes of a multi-byte value in storage. Little-endian puts the least significant byte at the lowest address. |
| Object identity vs. value | Identity distinguishes one live object from another; value is what the object represents. Memory layout and header size are implementation details, not universal Python constants. |
| Inscribed vs. circumscribed regular polygon | In the first, vertices lie on the circle; in the second, sides are tangent to the circle. These give lower and upper perimeter bounds. |
| ULP | A **unit in the last place**: the spacing associated with the least significant retained digit at a given floating-point scale. More algorithmic steps do not guarantee more representable precision. |

## Historical screen-code appendix

The following listings preserve distinct classroom states. Gutter numbers are not commands or source to type. An unavailable suffix is marked in place. Later output is attached to an earlier version only where the evidence supports that connection.

### Screen code · The Kivy application grows from one class to two child widgets

The file is **`src/steps.py`**, shown in Sublime Text. Numbers at the left reproduce the editor gutter (or, in the last listing, continue it from a verified diff overlap); they are **not** Python to type. Frame times are requested capture targets. Visual soft wraps are joined on their original logical line. Blank numbered rows are visible blanks, not missing code. All comments in the main teaching sections are guide-added; the temporary hex comment below is an exception actually visible in the editor.

#### Empty window · 0:05:00–0:16:16

The terminal at 0:05:00 shows the switch to the empty-window revision. The earlier diff still above it compares a different state; for example, green **`size_hint_y=None,`** in that scrollback is *not* a line in the empty-window file.

```text
git checkout master~8
Previous HEAD position was 53d55a7 Steps: size_hint
HEAD is now at 5283048 Steps: empty window
git log --oneline
5283048 (HEAD) Steps: empty window
```

**Observed listing:** `00-10-05.png`; also `00-07-00.png` and `00-08-00.png`. Lines 1–5 define the subclass and deliberately empty startup method. Only line 7 constructs the instance and calls the inherited run method.

```text
 1  from kivy.app import App
 2
 3  class KivySteps(App):
 4      def build(self):
 5          pass
 6
 7  KivySteps().run()
 8
```

**Run evidence:** `00-11-00.png` shows **`py .\src\steps.py`**, startup messages and a critical message; the 0:12:00–0:14:00 views expose the graphics messages below it. `00-14-38.png` shows the empty black client area in front. Do not silently remove the critical line just because a window ultimately appeared. Selected console messages, retaining the text rather than column padding:

```text
[CRITICAL] [Application] No window is created. Terminating application run.
[INFO] [Base] Start application main loop
[INFO] [Window] Provider: sdl2
[INFO] [GL] Using the "OpenGL" graphics system
[INFO] [GL] GLEW initialization succeeded
[INFO] [GL] Backend used <glew>
[INFO] [GL] OpenGL renderer <b'Intel(R) Iris(R) Xe Graphics'>
```

#### Root layout · 0:16:17–0:20:45

**Observed listing:** `00-18-00.png`; corroborated by `00-19-00.png`. The editor wraps the last argument of line 7 onto an unnumbered display row. This method returns a real layout on line 8; it still has no children. The second blank appearance is the instructor's report, not a second copy of the first run's console output.

```text
 1  from kivy.app import App
 2  from kivy.uix.boxlayout import BoxLayout
 3
 4
 5  class KivySteps(App):
 6      def build(self):
 7          rootLayout = BoxLayout(orientation='vertical', padding=20, spacing=15)
 8          return rootLayout
 9
10  KivySteps().run()
11
```

#### Title label · 0:20:46–0:43:07

**Observed listing:** `00-22-00.png` with the selected text cross-checked against `00-24-00.png`. Lines 10–15 construct a label, line 16 attaches it to the layout, and line 18 returns the layout rather than the label. Notice the double quotes around the title, single quotes around the size, and lack of a trailing comma after the color tuple in this revision.

```text
 1  from kivy.app import App
 2  from kivy.uix.boxlayout import BoxLayout
 3  from kivy.uix.label import Label
 4
 5
 6  class KivySteps(App):
 7      def build(self):
 8          rootLayout = BoxLayout(orientation='vertical', padding=20, spacing=15)
 9
10          titleLab = Label(
11              text="Kivy Steps",
12              font_size='24sp',
13              bold=True,
14              color=(0.5, 0.5, 1, 1)
15          )
16          rootLayout.add_widget(titleLab)
17
18          return rootLayout
19
20  KivySteps().run()
21
```

**Observed alternate line 14, shown separately by editor state:**

| Source frame | Historical line (leading indentation not shown in table) | Significance |
|---|---|---|
| `00-27-00.png`, `00-27-05.png` | **`color=(0.5, 0.5, 1, 1)  #fe90a8`** | A six-digit color example written as a Python comment. No alternate color is executed by this edit. |
| `00-28-00.png`, `00-30-00.png` | **`color=(0.5, 0.5, 1, 1)`** | Comment gone; original label tuple retained. |
| `00-40-00.png` | **`color=(1, 1, 1, 1)`** | White editor variant. The white title is independently visible in the 0:32:00–0:39:00 window frames. |
| `00-41-00.png`, `00-42-00.png` | **`color=(0.5, 0.5, 0.5, 1)`** | Gray variant; the latter frame shows the gray title and this editor state together. |

The pure-blue result between the first and white variants is visible in `00-31-00.png`. The instructor describes the input as no red, no green and full blue; the foreground application obscures the relevant source line. That is enough to explain the experiment, not to reconstruct its exact Python numeric spelling in a historical listing.

#### Text input checkpoint · 0:43:08–0:47:59

The 0:44:00 terminal contains three aborted checkout attempts, one intervening restore of the staged path, then a successful stash. The repeated failure text reads:

```text
error: Your local changes to the following files would be overwritten by checkout:
        src/steps.py
Please commit your changes or stash them before you switch branches.
Aborting
```

The terminal wraps the stash message on screen; it reads as one line: **`Saved working directory and index state WIP on (no branch): fc1e98b Steps: Label widget`**. The later 0:46:00 view says **`HEAD is now at 2ec4f8a Steps: Text input widget`** and shows **`git diff HEAD~ -- .\src\steps.py`**. Its diff headers include **`@@ -1,6 +1,8 @@`** and **`@@ -15,6 +17,18 @@ class KivySteps(App):`**. They establish where the import and later widget block were added; the displayed quit/scroll state does not establish the contents of lines below the hunk.

**Assembled visual reconstruction, readable prefix:** `00-46-35.png` supplies editor lines 1–24; the same-revision diff in `00-46-00.png` and `00-47-00.png` overlaps the label attachment, the new constructor, its options and the next attachment. The hunk's new-file start and the editor numbers agree in their overlap. Line 10 runs into the edge of the editor; this view alone does not recover its suffix. The earlier *different revision* shows a complete layout call but is not substituted for this one. The matching 0:49:00 lower editor view agrees on lines 16–29 and independently supplies the footer on lines 30–34. This composite records compatible visible lines, not proof of an unseen save.

```text
 1  from kivy.app import App
 2  from kivy.uix.boxlayout import BoxLayout
 3  from kivy.uix.label import Label
 4  from kivy.uix.textinput import TextInput
 5
 6
 7
 8  class KivySteps(App):
 9      def build(self):
10          rootLayout = BoxLayout(orientation='vertical', padding=20, spacing[remainder not visible]
11
12          titleLab = Label(
13              text="Kivy Steps",
14              font_size='24sp',
15              bold=True,
16              color=(0.5, 0.5, 1, 1)
17          )
18          rootLayout.add_widget(titleLab)
19
20          someTxt = TextInput(
21              hint_text="Type here",
22              multiline=False,
23              background_normal="",
24              background_active="",
25              background_color=(0.1, 0.1, 0.25, 1),
26              foreground_color=(1, 1, 1, 1),
27              cursor_color=(1, 1, 1, 1),
28          )
29          rootLayout.add_widget(someTxt)
30
31
32          return rootLayout
33
34  KivySteps().run()
```

Lines 12–18 restore the prior checkpoint's blue-tinted label. Lines 20–28 create a different widget, with an empty-field prompt, single-line behavior and three separate color settings. Line 29 makes that new object a sibling of the label. These green additions in terminal scrollback describe source differences; they are not program output from a window run.

### Screen code: swapping insertion order

The baseline above uses the original add order. The next local edit is a separate state.

**Local reordered edit, 0:53:00.** Lines 8–17 still construct the label. What changes is not the constructor order but the placement of the add calls. The same field constructor now begins one line earlier. The corresponding 0:53:15 interface has the field above the title.

```text
18 | 
19 |         someTxt = TextInput(
20 |             hint_text="Type here",
21 |             multiline=False,
22 |             background_normal="",
23 |             background_active="",
24 |             background_color=(0.1, 0.1, 0.25, 1),
25 |             foreground_color=(1, 1, 1, 1),
26 |             cursor_color=(1, 1, 1, 1),
27 |         )
28 |         rootLayout.add_widget(someTxt)
29 |         rootLayout.add_widget(titleLab)
```

### Screen code: two different size-hint revisions

Historical `src/steps.py`, label-size revision at 0:56:00–0:57:00. The comma on the color line now permits a further keyword. Line 17 belongs to the label; the input still has no explicit height. The blank line before its closing parenthesis is actually visible.

```text
12 |         titleLab = Label(
13 |             text="Kivy Steps",
14 |             font_size='24sp',
15 |             bold=True,
16 |             color=(0.5, 0.5, 1, 1),
17 |             size_hint_y=None,
18 |         )
19 |         rootLayout.add_widget(titleLab)
20 | 
21 |         someTxt = TextInput(
22 |             hint_text="Type here",
23 |             multiline=False,
24 |             background_normal="",
25 |             background_active="",
26 |             background_color=(0.1, 0.1, 0.25, 1),
27 |             foreground_color=(1, 1, 1, 1),
28 |             cursor_color=(1, 1, 1, 1),
29 | 
30 |         )
31 |         rootLayout.add_widget(someTxt)
```

**Next revision, 0:58:00 diff and 0:59:00 editor.** Lines 12–20 retain the label form just shown. The input keyword list is now longer; the blank line there is gone. In the accompanying window, both the title and the narrow field sit near the bottom.

```text
21 |         someTxt = TextInput(
22 |             hint_text="Type here",
23 |             multiline=False,
24 |             background_normal="",
25 |             background_active="",
26 |             background_color=(0.1, 0.1, 0.25, 1),
27 |             foreground_color=(1, 1, 1, 1),
28 |             cursor_color=(1, 1, 1, 1),
29 |             height="40dp",
30 |             size_hint_y=None,
31 |         )
32 |         rootLayout.add_widget(someTxt)
```

**Temporary multiline experiment, 1:00:46–1:01:16.** The instructor reports enabling newlines; 1:00:56 and 1:01:00 show changing text in the short field, but the exact enabling edit is behind the running window. It is not transcribed as a historical `True` assignment.

### Screen code: anchored input and alternative placements

Assembled visual reconstruction, `src/steps.py`, center/center version. The 1:05:00 terminal diff shows the new import and the complete wrapped anchor construction, including `padding=10`. Gutter 10–32 comes from the 1:06:00 editor; 22–42 from 1:04:00. The field constructor and new parent-child edges agree in the overlapping views. The import hunk anchors the first imports to the start of the file, but unseen intervening rows are not filled by borrowing a different checkout.

```text
 1 | from kivy.app import App
 2 | from kivy.uix.boxlayout import BoxLayout
 3 | from kivy.uix.anchorlayout import AnchorLayout
 4 | from kivy.uix.label import Label
 5 | from kivy.uix.textinput import TextInput
```

Rows 6–9 are not supplied by these views.

```text
10 |     def build(self):
11 |         rootLayout = BoxLayout(orientation='vertical', padding=20, spacing[remainder not visible]
12 | 
13 |         titleLab = Label(
14 |             text="Kivy Steps",
15 |             font_size='24sp',
16 |             bold=True,
17 |             color=(0.5, 0.5, 1, 1),
18 |             size_hint_y=None,
19 |         )
20 |         rootLayout.add_widget(titleLab)
21 | 
22 |         someTxt = TextInput(
23 |             hint_text="Type here",
24 |             multiline=False,
25 |             background_normal="",
26 |             background_active="",
27 |             background_color=(0.1, 0.1, 0.25, 1),
28 |             foreground_color=(1, 1, 1, 1),
29 |             cursor_color=(1, 1, 1, 1),
30 |             height="40dp",
31 |             size_hint_y=None,
32 |         )
33 | 
34 |         contentLayout = AnchorLayout(anchor_x="center", anchor_y="center", padding=10)
35 |         contentLayout.add_widget(someTxt)
36 | 
37 |         rootLayout.add_widget(contentLayout)
38 | 
39 | 
40 |         return rootLayout
41 | 
42 | KivySteps().run()
```

**Separate temporary edits to line 34.** 1:08:00/1:08:20 displays the center/top variant; 1:09:00 exposes left/top behind the app; 1:11:00 displays right/top. A top-anchored field still appears below the title. Suffixes from the committed center/center diff cannot certify that the experiment retained the same suffix. At 1:09:00 the running window also obscures the beginning of the line.

```text
34 |         contentLayout = AnchorLayout(anchor_x="center", anchor_y="top", padd[remainder not visible]
```

```text
34 | [beginning not visible]AnchorLayout(anchor_x="left", anchor_y="top", paddin[remainder not visible]
```

```text
34 |         contentLayout = AnchorLayout(anchor_x="right", anchor_y="top", pa[remainder not visible]
```

### Screen code: grouped controls, no application binding yet

Historical `src/steps.py`, button-only checkout. Assembled from 1:13:00 upper editor, 1:14:55/1:15:00 lower editor, and 1:22:00–1:23:00 lower lines with the app in front. The field retains `"Type here"`. The label now has its own explicit height. The 1:13:00 diff supplies the visible beginnings of the partly covered container lines; its wrap also exposes the group-constructor suffix `=20, spacing=15)` but not the complete keyword immediately before `=20`. The 1:13:00 diff shows the unchanged anchor declaration wrapping to `g=10)`, but not every intervening character; the clipped line below is not silently completed. Gutter 57 and the regular editor baselines anchor the covered lower line numbers.

```text
10 | class KivySteps(App):
11 |     def build(self):
12 |         rootLayout = BoxLayout(orientation='vertical', padding=20, spacing[remainder not visible]
13 | 
14 |         titleLab = Label(
15 |             text="Kivy Steps",
16 |             font_size='24sp',
17 |             bold=True,
18 |             color=(0.5, 0.5, 1, 1),
19 |             size_hint_y=None,
20 |             height="40dp",
21 |         )
22 |         rootLayout.add_widget(titleLab)
23 | 
24 |         someTxt = TextInput(
25 |             hint_text="Type here",
26 |             multiline=False,
27 |             background_normal="",
28 |             background_active="",
29 |             background_color=(0.1, 0.1, 0.25, 1),
30 |             foreground_color=(1, 1, 1, 1),
31 |             cursor_color=(1, 1, 1, 1),
32 |             height="40dp",
33 |             size_hint_y=None,
34 |         )
35 | 
36 |         doubleBut = Button(
37 |             text="2x",
38 |             background_color=(0.2, 0.6, 1, 1),
39 |             bold=True,
40 |             height="40dp",
41 |             size_hint_y=None,
42 |             size_hint_x=0.25,
43 |             pos_hint={"center_x": 0.5, "center_y": 0.5},
44 |         )
45 | 
46 |         groupLayout = BoxLayout(size_hint_y=None, orientation='vertical',[remainder not visible]
47 |         groupLayout.add_widget(someTxt)
48 |         groupLayout.add_widget(doubleBut)
49 | 
50 | 
51 |         contentLayout = AnchorLayout(anchor_x="center", anchor_y="center",[remainder not visible]
52 |         contentLayout.add_widget(groupLayout)
53 | 
54 |         rootLayout.add_widget(contentLayout)
55 | 
56 | 
57 |         return rootLayout
```

Lines 36–44 describe the button. Lines 46–48 place the two controls in the inner box; 51–54 attach that box through the anchor to the root. There is no binding between the end of the button constructor and the group definition. Button press feedback is not a numeric operation in this version.

### Screen code: bound button and conversion on master

Historical `src/steps.py`, visual reconstruction from 1:28:00 (upper editor), 1:25:00–1:26:00 (button and group), and 1:27:00/1:29:46 (overlap at 49 and 52–57, handler and launch). These are compatible views, not proof of a particular save or terminal invocation. Here the app owns the input; the grouped button remains locally named `doubleBut`.

```text
10 | class KivySteps(App):
11 |     def build(self):
12 |         rootLayout = BoxLayout(orientation='vertical', padding=20, spacing[remainder not visible]
13 | 
14 |         titleLab = Label(
15 |             text="Kivy Steps",
16 |             font_size='24sp',
17 |             bold=True,
18 |             color=(0.5, 0.5, 1, 1),
19 |             size_hint_y=None,
20 |             height="40dp",
21 |         )
22 |         rootLayout.add_widget(titleLab)
23 | 
24 |         self.someTxt = TextInput(
25 |             hint_text="Enter a number",
26 |             multiline=False,
27 |             background_normal="",
28 |             background_active="",
29 |             background_color=(0.1, 0.1, 0.25, 1),
30 |             foreground_color=(1, 1, 1, 1),
```

The lower input properties on rows 31–33 are not fully legible in this master upper-editor view; the same numbered rows in the earlier button-only checkout are not substituted.

```text
34 |         )
35 | 
36 |         doubleBut = Button(
37 |             text="2x",
38 |             background_color=(0.2, 0.6, 1, 1),
39 |             bold=True,
40 |             height="40dp",
41 |             size_hint_y=None,
42 |             size_hint_x=0.25,
43 |             pos_hint={"center_x": 0.5, "center_y": 0.5},
44 |         )
45 |         doubleBut.bind(on_press=self.on_button_click)
46 | 
47 |         groupLayout = BoxLayout(size_hint_y=None, orientation='vertical',[remainder not visible]
48 |         groupLayout.add_widget(self.someTxt)
49 |         groupLayout.add_widget(doubleBut)
50 | 
51 | 
52 |         contentLayout = AnchorLayout(anchor_x="center", anchor_y="center",[remainder not visible]
53 |         contentLayout.add_widget(groupLayout)
54 | 
55 |         rootLayout.add_widget(contentLayout)
56 | 
57 |         return rootLayout
58 | 
59 |     def on_button_click(self, instance):
60 |         try:
61 |             float(self.someTxt.text)
62 |             self.someTxt.text = str(float(self.someTxt.text) * 2)
63 |         except ValueError:
64 |             self.someTxt.text = ""
65 | 
66 | KivySteps().run()
```

Line 45 registers a method reference, not its result. Line 61 can raise even though its result is discarded; 62 computes a string and writes it to the widget; 63–64 clear invalid input. In the foreground app, 1:32:00/1:32:13 show the new hint, 1:33:00 shows `1136066.56`, and 1:34:00 shows the hint again. **Later experiment:** at 1:34:54 and 1:35:00 the handler body is behind the GUI. The brighter button and nonnumeric entry at 1:35:00 are visible. Replacing the clear with `pass` and commenting out the old line is the *spoken edit*; there is no readable numbered replacement listing here.

### Revision and console landmarks

Observed actions and status text, not commands to run. `HEAD~` asks for the parent of the current checkout; `master~N` is relative to the branch tip. A prompt with typed text and no subsequent output is distinguished from a completed action.

| Capture | Visible evidence | What it establishes |
|---|---|---|
| 0:54:00 | `git checkout master~4` appears twice; `src/steps.py`, “Please commit your changes or stash them before you switch branches.” and “Aborting” | Neither attempt reached the requested revision. |
| 0:55:00 | `git stash`; `git checkout master~4`; `Previous HEAD position was 2ec4f8a Steps: Text input widget`; `HEAD is now at 53d55a7 Steps: size_hint` | The stash is saved and a subsequent checkout succeeds. |
| 0:58:00 | `git checkout master~3`; `HEAD is now at 2561f7f Steps: another size_hint`; `git diff HEAD~ -- .\src\steps.py` | Now inspecting the change to the input, not the label-only revision. |
| 1:00:00 | `py .\src\steps.py` followed by startup output including Kivy v2.3.1 and Python v3.12.7 | An invocation, not a statement that every displayed edit was used by this run. |
| 1:02:00 | `git stash`; `git checkout master~2`; `HEAD is now at 079e9f2 Steps: AnchorLayout` | The single-input experiment gives way to the anchored revision. |
| 1:12:00 | `git stash`; `git diff HEAD~ -- .\src\steps.py` still shows the `AnchorLayout` diff | Running a diff did not check out the button revision. |
| 1:24:00 | `git checkout master`; `Previous HEAD position was 48b5a99 Steps: Button grouped with TextInput`; `Switched to branch 'master'` | Confirms the previous checkout and branch switch. `py .\src\steps.py` is visible at the prompt here without an outcome yet. |

### Historical screen code: `binOp.py` before the handwritten conversions

**Assembled visual reconstruction.** The filename in Sublime Text is **`C:\com\joesta\Numerical\src\binOp.py`**. Gutter numbers below are editor line numbers, **not characters to type**. The quoted block at lines 61–85 is part of the listing, not terminal output. Numbered blank rows preserve the visible gaps. The sampled views agree where they overlap, but do not establish continuous editing history or the contents of an on-disk file. All times are requested frame targets.

Lines 1–25 are visible at 1:42:00; the right end of line 21 is independently visible at 1:43:00, which also gives lines 24–34. Lines 24–47 are visible at 1:55:00 and 33–57 at 1:49:00. Lines 39–63 are visible at 1:47:05; lines 61–85 at 1:41:00, and the blank tail at 1:48:00. These sources all precede the use of the buffer as a calculation sheet.

```text
 1  print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")
 2  print("-" * 11)
 3  for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
 4      q = a & b
 5      print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
 6
 7
 8
 9
10
11
12
13  print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'| or':<4}")
14  print("-" * 11)
15  for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
16      q = a | b
17      print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
18
19  print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'^ xor':<4}")
20  print("-" * 11)
21  for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
22      q = a ^ b
23      print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
24
25
26
27  def displayRow(val, msb, label=""):
28      for i in range(msb, -1, -1):
29          bit = (val >> i) & 1
30          print(f"{bit:<6}", end='')
31      print(label)
32
33  msb = 9
34  n = 1000
35  mask = 0x0f0
36
37  print()
38  for i in range(msb, -1, -1):
39      print(f"2^{i:<4}", end='')
40  print()
41
42  for i in range(msb, -1, -1):
43      print(f"{2**i:<6}", end='')
44  print()
45
46  displayRow(n, msb, "data")
47  displayRow(mask, msb, "mask")
48  result = ~n
49  displayRow(result, msb, "not")
50  result = n & mask
51  displayRow(result, msb, "and")
52  result = n | mask
53  displayRow(result, msb, "or")
54  result = n ^ mask
55  displayRow(result, msb, "xor")
56  result = n<<3
57  displayRow(result, msb, "left shift 3")
58  result = n>>3
59  displayRow(result, msb, "right shift 3")
60
61  """
62
63  A | B echo
64  ------
65  0 | 0
66  1 | 1
67
68  A | B zero
69  ------
70  0 | 0
71  1 | 0
72
73  A | B one
74  ------
75  0 | 1
76  1 | 1
77
78
79  A | B opposite, not, invert
80  ------
81  0 | 1
82  1 | 0
83
84
85  """
86
87
```

Lines 1–23 print the four possible input pairs three times; line 4, 16 or 22 supplies the result for each respective table. Lines 27–31 define a display routine but do not run it yet. Lines 33–44 choose the numbers and print the two headings. Lines 46–59 call the routine for the original numbers and then each new result; every calculation starts from **`n`**, not the previous **`result`**. Lines 61–85 are a triple-quoted string expression: the file run does not print its handwritten unary examples.

**Terminal evidence.** The instructor reports running the program at 1:47:44; the invocation text is not visible in the 1:48:00/1:49:00 output views. At 1:49:00 the scrollback includes the last two AND rows, the OR and XOR tables, and the complete labeled ten-column display. Below are the observed bit rows with inter-column padding removed, then the *separately calculated* integer that was passed to the display. These two things can differ: an output window onto ten bit positions does not report an integer’s sign or its higher positions.

| Printed label | Ten displayed bits (9 through 0) | Independent integer check |
|---|---|---:|
| data | 1111101000 | 1000 |
| mask | 0011110000 | 240 |
| not | 0000010111 | −1001 |
| and | 0011100000 | 224 |
| or | 1111111000 | 1016 |
| xor | 1100011000 | 792 |
| left shift 3 | 1101000000 | 8000 |
| right shift 3 | 0001111101 | 125 |

### Historical scratchpad: manual conversion states in the same editor

These are *notes as typed*, not additional executable statements demonstrated to produce the earlier terminal output. The blank space between columns below is editorially aligned for reading; tokens and gutter numbers are preserved. Do not insert these tables into the preceding program. At 1:57:00 the editor shows **`1000`** alone on line 26; **`displayRow`** now begins on 29 rather than 27. At 1:58:00, 1:58:38 and 1:59:00 that scratch literal is **`10`**, while the real assignment below remains **`n = 1000`**.

**2:04:00 — work in progress.** The header already runs from 2^4 to 2^0. The lower rows are still being edited; the decimal 4 is missing. Read the rows independently, not as a completed alignment.

```text
26  10
27  2^4   2^3   2^2   2^1   2^0
28  1000  100   10    1
29  8     2     1
```

**2:05:00 — repaired decimal-10 chart.** Line 28 spells out the weights *in binary*; line 29 gives the respective decimal values. Neither line is the binary expansion of the target: that expansion is the spoken 1010 (or 01010).

```text
26  10
27  2^4    2^3    2^2    2^1    2^0
28  10000  1000   100    10     1
29  16     8      4      2      1
```

**2:08:41 — intermediate 1000 chart.** The first three bit choices are 1. Line 35 literally joins two numbers as **`232104`** during editing; it is not an arithmetic result. At 2:09:00 the scratch line reads 104 alone, agreeing with the spoken subtraction of 128 from 232.

```text
31  1000
32  2^9   2^8   2^7
33  512   256   128
34  1     1     1
35       232104
```

**2:11:00 — completed 1000 chart.** At 2:10:49 the headings and weights were already present but the three low-order zero digits had not yet been entered in the editor. Here line 34 has all ten digits. The separate 0 on line 35 is the remainder, not an eleventh digit of the answer.

```text
31  1000
32  2^9  2^8  2^7  2^6  2^5  2^4  2^3  2^2  2^1  2^0
33  512  256  128  64   32   16   8    4    2    1
34  1    1    1    1    1    0    1    0    0    0
35       0
```

**2:17:00 — hex chart in progress.** At 2:15:00 only the familiar 0–9 symbols had been entered. At 2:16:00 lines 48 and 49 contained the hex-to-decimal correspondence; here line 50 has ten four-bit groups. The three rows are transcribed in sequence, not realigned to imply that a partially spaced column above a given group labels that group. No new Python run is established by this screen.

```text
44  msb = 9
45  n = 1000
46  mask = 0x0f0
47
48  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
49  0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15
50  0000  0001  0010  0011  0100 0101  0110  0111  1000  1001
```

### `binOp.py`: the mask worked out in the editor (2:18:00–2:24:51)

Gutter numbers are editor line numbers, **not text to type**. For the scratch diagrams, horizontal spacing is normalized to preserve the column relationships; entries and case are retained. These are manual notes in a Python editor, not executable Python. In particular, the caret in a place heading means “raised to” in the diagram, not Python exponentiation.

At 2:18:00 the hex-symbol row is being spaced out. At 2:19:00 the hex symbols, decimal correspondences, and four-bit patterns line up. The weights are still being edited at 2:20:00. The 1 sits alone on line 51 at 2:21:00 and 2:21:13; it is back in the last column of line 50 at 2:22:00. The following excerpts preserve the difference, rather than making the initial mistake look like the final table.

```text
2:20:00 — intermediate scratch
48  16^4    16^3    16^2    16^1    16^0
49  10000   1000    100     10      1
50  16      8       64      16      1

2:21:13 — corrected larger weights; units value on the wrong line
50  65536       4096       256    16
51      1
```

At 2:23:00 line 52 stops at `f*16`. At 2:24:00 the expansion and decimal result are in place and the three lookup rows have moved down. Lines 55–57 are blank in this state; a binary answer for line 55 should not be inferred from the spoken explanation.

```text
48  16^4      16^3      16^2   16^1  16^0
49  10000     1000      100    10    1
50  65536     4096      256    16    1
51  0         0         0      f     0
52                             f*16  + 0*1
53                             15*16  + 0
54                             240
55
56
57
58  0    1    2    3    4    5    6    7    8    9    a    b    c    d    e    f
59  0    1    2    3    4    5    6    7    8    9    10   11   12   13   14   15
60  0000 0001 0010 0011 0100 0101 0110 0111 1000 1001 1010 1011 1100 1101 1110 1111
```

Line 49 writes each place using digits of the working base; line 50 gives its weight in decimal. In lines 51–54 only the sixteens position contributes. Lines 58–60 give the direct route: read down from f to 15 to 1111, then down from 0 to 0000.

### `binOp.py`: compact driver and later spacing edit (2:25:00–2:37:00)

Assembled visual excerpt from the **compact-label** state: top of the heading loop at 2:25:00; the complete driver tail at 2:30:15. Lines 63–65 are numbered by alignment immediately above the visible gutter 66; the webcam covers those three gutter numbers but not their code. The overlap agrees; this is not a claim that the entire buffer was saved or runnable while unquoted scratch text was being edited above it.

```text
63  print()
64  for i in range(msb, -1, -1):
65      print(f"2^{i:<4}", end='')
66  print()
67
68  for i in range(msb, -1, -1):
69      print(f"{2**i:<6}", end='')
70  print()
71
72  displayRow(n, msb, "data")
73  displayRow(mask, msb, "mask")
74  result = ~n
75  displayRow(result, msb, "not")
76  result = n & mask
77  displayRow(result, msb, "and")
78  result = n | mask
79  displayRow(result, msb, "or")
80  result = n ^ mask
81  displayRow(result, msb, "xor")
82  result = n<<3
83  displayRow(result, msb, "left shift 3")
84  result = n>>3
85  displayRow(result, msb, "right shift 3")
86
87  """
```

Lines 63–70 make two headers, one symbolic and one numeric. Lines 72–73 establish the reference rows; every assignment after them starts from `n`, and each call labels only the ten displayed bits of the latest `result`. Earlier terminal rows are visible while the editor is being changed. At 2:27:00 the selected row is `not`; at 2:28:00 the selected digit is its 1 in the 16's place. At 2:30:00 the data and mask are highlighted together.

Spaced-label revision, observed at 2:33:00. The three input/complement labels **do not** acquire a newline; the five labels from AND onward do. The terminal at 2:32:00 shows `py .\src\binOp.py` **below** older compact output; the later terminal at 2:35:00 shows the spaced table. The openings/closings around every scratch region cannot all be read in one state. A triple-quoted region following the driver is visible starting on line 87, with `A | B echo` on line 89.

```text
72  displayRow(n, msb, "data")
73  displayRow(mask, msb, "mask")
74  result = ~n
75  displayRow(result, msb, "not")
76  result = n & mask
77  displayRow(result, msb, "and\n")
78  result = n | mask
79  displayRow(result, msb, "or\n")
80  result = n ^ mask
81  displayRow(result, msb, "xor\n")
82  result = n<<3
83  displayRow(result, msb, "left shift 3\n")
84  result = n>>3
85  displayRow(result, msb, "right shift 3\n")
86
87  """
88
89  A | B echo
90  ------
```

Observed table contents at 2:35:00, with horizontal padding shortened. The blank separation begins *after* the AND row, since the newline is in the label and the final print also ends the line. The row labeled `left shift 3` is not a printout of the decimal result.

```text
2^9  2^8  2^7  2^6  2^5  2^4  2^3  2^2  2^1  2^0
512  256  128  64   32   16   8    4    2    1
1    1    1    1    1    0    1    0    0    0    data
0    0    1    1    1    1    0    0    0    0    mask
0    0    0    0    0    1    0    1    1    1    not
0    0    1    1    1    0    0    0    0    0    and

1    1    1    1    1    1    1    0    0    0    or

1    1    0    0    0    1    1    0    0    0    xor

1    1    0    1    0    0    0    0    0    0    left shift 3

0    0    0    1    1    1    1    1    0    1    right shift 3
```

### `binOp.py`: return to the row helper (2:37:24–2:42:00)

Observed single-frame excerpt at 2:41:17, including the **later** delimiter on line 47. The unquoted scratch in the earlier 2:24:00 frame instead had an empty line 47. The isolated 0 on line 35 is retained as scratch and should not be appended to the ten-bit value on line 34. Horizontal spacing in the scratch rows is normalized.

```text
28  10000  1000  100   10   1
29  16     8     4     2    1
30
31  1000
32  2^9   2^8   2^7   2^6   2^5   2^4   2^3   2^2   2^1   2^0
33  512   256   128   64    32    16    8     4     2     1
34  1     1     1     1     1     0     1     0     0     0
35       0
36  """
37
38  def displayRow(val, msb, label=""):
39      for i in range(msb, -1, -1):
40          bit = (val >> i) & 1
41          print(f"{bit:<6}", end='')
42      print(label)
43
44  msb = 9
45  n = 1000
46  mask = 0x0f0
47  """
48  16^4      16^3      16^2   16^1  16^0
49  10000     1000      100    10    1
50  65536     4096      256    16    1
51  0         0         0      f     0
```

Lines 38–42 print one bit at a time; the default empty label is visible even though this driver supplies labels. The inner print stays on the same line; the dedented label print runs once after the loop. Lines 44–46 give the index of the highest displayed position and the two source integers. The event shows `& 1` selected, not a change from masking to some other operator. The matching terminal data row and the partially obscured earlier editor at 2:38:00 illustrate what the discussion is tracing; no new invocation of the helper is visible in that frame.

### `byteOrder.py`: one observed run, then an editor extension (2:44:00–2:50:00)

At 2:44:00 the terminal shows the following command and two output lines. The editor behind the terminal still shows `binOp.py`; the code for **the other file**, `byteOrder.py`, becomes legible in later editor frames.

```text
py .\src\byteOrder.py
System byte order is little endian.
The PyObject header size on this system is 16
```

At 2:45:00 a second command is visible, `.\src\byteOrder.py`, with a following prompt but no new pair of output lines between them. Do not count the old lines twice. At 2:46:07 a “References to sizeof” tooltip covers part of the last print; that line below comes from the unobscured 2:47:00 frame, **not** from filling in text hidden by the tooltip.

```text
 1  import sys
 2  print(f"System byte order is {sys.byteorder} endian.")
 3
 4  import ctypes
 5  pyObjectHeaderSize = ctypes.sizeof(ctypes.c_ssize_t) + ctypes.sizeof(ctypes.c_void_p)
 6  print(f"The PyObject header size on this system is {pyObjectHeaderSize}")
 7
```

Lines 1–2 print the native byte-order string; the period after `endian` is part of the message. Lines 4–6 add two type widths and print the sum with no final period. This expression queries sizes, not memory contents. It does not determine the actual layout of every possible interpreter build.

At 2:49:00 and 2:50:00 the same six lines remain visible and the buffer has an added tail. This later editor state does not establish a rerun. The illustration reuses one name with three different types:

```text
 7
 8  a = 3
 9  a = "hello"
10  a = [23]
11
```

The last pair of brackets encloses a single item. Nothing in these lines asks for output or displays an address.

### Historical screen record | End of `byteOrder.py` and the numerical roadmap

**1. Same extended editor state, 2:51:00–2:54:00.** The complete ten-line buffer confirms the preceding listing and its rebinding tail. `02-53-00.png` exposes the sum on line 5 without the selection. The modified-buffer indicator is not proof of a new run.

**Command and scrollback, 2:55:00 (`02-55-00.png`).** The terminal is now in front of the roadmap; an older bit table sits *above* the invocation. The table still has columns from `2^9` through `2^0` and rows labeled `data`, `mask`, `not`, `and`, `or`, `xor`, `left shift 3`, `right shift 3`. These are not π results and are not output of the byte-order command. The following run and its two displayed lines are legible:

```text
(Numerical) PS C:\com\joesta\Numerical> py .\src\byteOrder.py
System byte order is little endian.
The PyObject header size on this system is 16
```

A later prompt shows `.\src\byteOrder.py`, then a further prompt shows `py .\src\byteOrder.py` at the bottom without a subsequent result in that frame. The earlier two output lines support the size and byte-order account, not a newly observed execution of all ten lines in the modified editor buffer.

**2. From file list to a specific calculation requirement, 2:55:48–3:05:00.** Firefox's profile chooser precedes a partially typed search (`how m` at 2:56:00). At 2:57:00, File Explorer covers most of the results; the exposed memorization snippet is not the instructor's computed-digit claim. At 2:58:00 the selected entry in the Numerical `doc` folder begins `How Many Decimals of Pi Do We Really ...`; the full filename is visible separately in the 3:06:00 roadmap below. Other entries include `Accuracy of Mathematical Functions.pdf`, `IEEE Standard 754-2019.pdf` and `The Works of Archimedes.marc`. Their presence in Explorer is not evidence they were opened during this discussion.

The 2:59:00 original (`02-59-00.png`) shows page 2 in Slim PDF Reader. The document identifies Marc Rayman and links [his NASA profile](https://solarsystem.nasa.gov/people/2761/dr-marc-rayman/). Rayman recalls first asking this question as a sixth grader. The relevant printed passage reads: “For JPL's highest accuracy calculations, which are for interplanetary navigation, we use 3.141592653589793.” Its first example names [Voyager 1](http://www.jpl.nasa.gov/missions/voyager-1/) and says “As of this writing” before giving about 16 billion miles; that is a historical passage, not the spacecraft's distance today. Those two URLs are links *within the displayed PDF*, not the URL of the article itself. At 3:00:00–3:04:00 the selected document is visible again in Explorer; no polygon construction is pictured. At 3:04:48 and 3:05:00 a distinct resource appears in Discord `#general`: a [student-shared Exploratorium history-of-π link](https://www.exploratorium.edu/pi/history-of-pi) with a text-highlight fragment. The older “Now is the time to learn to code” thumbnail above it is a different post.

**3. `Roadmap.md`, local planning text, not Python code.** Source: `03-06-00.png`, with changed selections in `03-07-00.png` and `03-08-00.png`; the listing remains visible in the 3:23:00 minute capture. Below is the visible segment, **not the entire document**. The webcam obscures the first five gutter numbers but not the entry text; those five numbers are supplied by counting back from visible line 6. Remaining numbers follow the editor gutter. No continuation below line 25 is inferred.

```text
 1 | kivySteps
 2 | binOp.py
 3 | truthTableWIP.py
 4 | byteOrder.py
 5 | jStruct.py
 6 | floatBytes.py
 7 | doc/IEEE Standard 754-2019.pdf
 8 | ieee754App.py
 9 | ulp.py
10 | doc/Accuracy of Mathematical Functions.pdf
11 | doc/How Many Decimals of Pi Do We Really Need.pdf
12 | squareRoot.py
13 | piCalc.py
14 | < see below
15 | poly2.py
16 | ellipseScale.py
17 | expScale.py
18 | expSlopeAtOne.py
19 | expSlope.py
20 | poly.py
21 |
22 |
23 |
24 | Start simply with an independent variable "a" and a dependent variable "b".
25 | We can think of b as a function of a: b = f(a).
```

The first group lists earlier tools and notes on floating point. The selection moves from within the π-document path on line 11 to the square-root filename on line 12, then to the π filename on line 13. That sequence fits the instructor's proposal: understand representation, calculate a square root, then approach π geometrically. The last two lines frame a separate modeling idea: choose an input `a` and describe how an output `b` depends on it; no particular function or numerical result is given here. Lines 15–20 name files, not functions demonstrated in this closing discussion. At 3:17:05 the same roadmap is on screen, and at 3:19:00 the instructor gestures while explaining the bisection. No legible polygon drawing, handwritten lengths, square-root algorithm, `piCalc.py` source, or corresponding run is visible in these captures. The worked polygons in the teaching section reconstruct the spoken reasoning.

**Closing screen, 3:24:00–3:24:07.** Discord is in front at 3:24:00. The final target shows OBS with `Display Capture` selected, its recursive desktop preview, and the blue `Stop Recording` control. This shows the recording interface before the end; the still does not show a completed stop-button click.

## Micro-Learning Insights · removable appendix

### Designing and debugging the interface

- **Ask what error the application can tolerate.** At 0:01:00, a few metres of GPS uncertainty means something different on a sprint than on a long route. Start with the measurement the runner needs, not the sensor you have.
- **A blank window can be a successful intermediate test.** Compare 0:14:38 with the empty layout that follows: a running event loop and a real root container need not have visible children.
- **Keep one color channel experiment interpretable.** At 0:40:00–0:42:00 the alpha stays opaque. Equal RGB channels at lower intensity produce gray without making the text transparent. The earlier `#fe90a8` after the Python expression is a *comment*, not a property change.
- **Read the target of a Git command.** At 0:44:00–0:46:00 `--staged` changes the index. An edited working file can still block checkout; an “Aborting” message is not a successful revision change.
- **Ask “the top of what?”** At 1:08:00 the anchored field moves to the top of the space *below* the title. Always locate the immediate parent before deciding a positioning rule failed.
- **Separate feedback from function.** The `2x` button can light up before it has been bound to a callback. A widget's pressed appearance proves neither that the handler ran nor that a value changed.
- **An empty field has a history.** At 1:34:00 the clearing handler catches a failed conversion and deliberately assigns the empty string. The same blank appearance before and after a press doesn't prove that nothing ran.

### Reading and testing numerical programs

- **Find the row that distinguishes two hypotheses.** OR and XOR agree on `01` and `10`. Test `11`: inclusive OR gives 1; XOR gives 0. One convenient row is more diagnostic than several nondiscriminating ones.
- **Write every place, even when you subtract nothing.** In the manual conversion of 1000, the remaining 8 cannot cover 16. Write zero in the 16s place; don't slide the 8 into the wrong column.
- **Group by four, then consider the display width.** Hex `f0` gives `1111 0000`. The ten-column display adds two leading zeros; it doesn't change the mask or make it a twelve-bit operation.
- **Name the operands before comparing rows.** `~n` reads only `n`. A matching bit in the mask row is coincidence, not evidence that NOT used the mask.
- **Separate shifting from isolation.** At `i=8`, shifting 1000 gives 3. The output is one bit because the code also performs `& 1`. A nonzero shifted integer is not necessarily a one in the selected column.
- **Ask what the printer omits.** The left shift produces 8000, yet the classroom helper prints only positions 9 through 0. Change the display window before concluding high bits were lost from the integer.
- **Read a unit from its producing expression.** The `16` printed by `byteOrder.py` is a sum of two type widths **in bytes**, not a number base, a byte order, or the size of every Python object.

### Estimation and precision

- **Distinguish a record from a requirement.** The π-digit discussion asks what has been computed. The Marc Rayman reading at 2:59:00 asks how much precision a navigation calculation actually uses.
- **Halve the base, not both legs.** Bisecting an equilateral center triangle leaves one radius as the hypotenuse. For radius 1 the half-base is ½ and the altitude is √3/2. This distinction is what makes the hexagon bound work.
- **Check the units before naming the result.** Two polygon perimeters squeeze the *circumference*. Divide both by the same diameter before calling them bounds on π.
- **Know why you stopped.** A justified interval narrow enough for the application is evidence of accuracy. Two successive floating-point results that print alike can instead signal a display or precision limit.
