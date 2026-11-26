# CSCS1240F26 · Session 24
## Worked Teaching Companion · Unified v2

**Central idea:** A collection holds things; an iterator remembers which thing comes next. This lesson connects that distinction to Python’s **`iter()`**, **`next()`**, and **`for`**, then uses deliberate failures to show why termination and name lookup matter.

**Recording:** [CSCS1240F26 24 — JoesTA](https://www.youtube.com/watch?v=KLTLt8TXzY8) · recorded October 26, 2026 · approximately 55:54. This is a new companion built under the supplied *Hermes Unified Study Guide Instructions, Version 2.0*, not a revision of the historical source files.

### How to use this companion

Read the teaching sections in order. They preserve the class’s movement from course progress and the existing fruit list to counting, scope, manual iteration, custom iteration, debugging, and the closing count discussion. The commented examples explain what changes; the trace tables show the changing state; **Key items to scan** lists are for later retrieval.

The historical appendix preserves substantial screen code and competing editor/run states. You do not need to reconstruct that chronology before understanding the main lesson. Its numbered gutters are evidence labels, **not Python to type**. The final Micro-Learning Insights appendix is removable without losing primary instruction, definitions, practice, or historical evidence.

### Sources, completeness, and evidence labels

The existing transcript and original capture set were reused; no video was downloaded and no images were recaptured. The readable transcript matches all **1,340** automatically generated English caption cues. It begins at **00:00:00.480**, includes the closing discussion and sign-off, and has a maximum cue end of **00:55:54.328**. The saved manifest records a media duration of **3,354.416667 seconds**. The roughly **0.089-second** tail is a timing discrepancy, not evidence of missing instruction.

All **66** original PNGs decode at **1920 × 1080**: **56** minute targets from **00:00 through 55:00**, **9** event frames, and a closing frame at **55:52**. No required minute is missing. The filenames identify requested capture times, not individually probed decoded-frame timestamps. Screenshots remain source evidence; no source images are embedded in this guide or PDF.

**Source limitations:** automatic captions can garble names and code; legible original frames govern code spelling. A complete sampled timeline is not continuous footage. Some lines are obscured or clipped, and the complete historical project is not recoverable from every screen state. Those limits are disclosed locally and detailed in the historical appendix. An editor may contain unsaved changes while the terminal still shows an older run.

- **Observed** — readable screen code, commands, or output, tied to named frames.
- **Class account** — speech or a question supported by the timestamped captions; speaker identity is not invented.
- **Source-based reconstruction** — an explicitly labeled explanation or assembly from compatible evidence, not a claim of a continuously observed run.
- **Annotated teaching version — guide-added comments** — explanatory comments or an adaptation, not an exact historical listing.
- **Expected output** — predicted behavior. **Independently verified** means a separately executed teaching fixture, never a claim that the instructor’s complete project was run here.
- **Supplemental clarification** — a technical distinction checked against official documentation or a separate fixture, not attributed to the instructor.

Original evidence is retained under **`C:\Projects\StudyProfile\CSCS1240F26\24\`**. Transcript: **`sources\KLTLt8TXzY8-20261123-054814-662999\transcript.txt`**. Frames and manifest: **`snapshots\KLTLt8TXzY8-20261123-054944-direct-1080p\`**. Frame citations below use filenames relative to that capture folder; transcript timestamps refer to the linked recording.

## 1. Course context: progress, withdrawal, and the next challenges

### Historical announcements and the withdrawal decision

At the opening, the instructor was waiting to learn whether grand-jury service would require travel to Rochester. He expected to call after about 5 p.m. the next day and post an update; his in-person availability on Wednesday was therefore uncertain. This was a contingency, not an announced cancellation. Discord was also offered as a place to ask follow-up questions about course progress and technical material.

The class was beginning week nine. The withdrawal discussion addressed a different question: whether a student would eventually receive a withdrawal mark or a letter grade. In the instructor's historical account, students could withdraw through the two-thirds point of a course. He associated the end of that week with the deadline for ordinary semester-length courses, but emphasized that shorter courses have their own two-thirds point. His wording varied between the end of the week and Friday or Saturday; this recording does not establish an exact calendar deadline.

A **W** meant withdrawal rather than an A–F course grade. The instructor said it did not enter the GPA calculation, but did count toward hours attempted and could affect academic-progress standing and financial aid. He referred students to the catalog for the attempted-hours/progress details. He advised students considering withdrawal to consult the **Enrollment Advisement Center (EAC)** and obtain the implications before acting. These are historical classroom remarks, not current verified institutional policy.

A student's question exposed an important distinction: the progress chart was not a set of grades about to be posted that weekend. Course grades would be assigned at the end of the semester. The approaching withdrawal deadline changed whether a student could still choose the official withdrawal process; it did not move final grading into that week. Remaining enrolled after that deadline meant receiving a letter grade at the end, potentially an F.

**Key items to scan**

- **Grand-jury contingency** — the instructor promised an update after learning whether he had to report; Wednesday attendance was uncertain, not definitely canceled.
- **Discord** — offered for announcements and follow-up questions.
- **Two-thirds point** — the instructor's stated withdrawal cutoff, tied to each course's duration rather than one universal date.
- **W** — historical withdrawal mark described as excluded from GPA but still relevant to attempted hours and aid.
- **Enrollment Advisement Center (EAC)** — the office students were directed to consult about financial-aid consequences.
- **Progress estimate versus final grade** — the chart described current standing; the final course grade remained an end-of-semester result.

### Reading the progress chart without treating it as a grading contract

The instructor displayed an anonymous progress snapshot from his database. He initially estimated ten or eleven challenges, then used ten as the rough comparison baseline. His screen associated progress through challenge 2070 with A, 2060 with B, 2058 with C, 2056 with D, and 2054 or below with F. He warned students at 2054 or below that they had work to do.

The discussion used rounded percentages: roughly 60% was still F, 70% reached D/passing, followed by the 80% and 90% comparisons. These were an approximate progress explanation, not a newly established exact points formula. The chart also contained people who were not ordinary currently enrolled students: the instructor and a permitted nonregistered participant complicated comparison with the official roster. He reported 17 registrations, two withdrawals, and 15 remaining students; the visible database total was 18. Do not interpret all 18 displayed records as 18 enrolled students.

He planned to resume three challenges each week, with six weeks remaining and roughly 18 additional opportunities. Major conceptual additions were more likely to become challenges than every small list convenience. Later in the discussion he named **`count`**, **`at`** (access at an index), and **`has`** as examples that might not get separate challenges, because he also wanted time for other subjects. This was a plan, not a promise that every feature shown in Git history would be assigned.

The grading mechanism itself was changing. Earlier work had been checked by running a student's program and comparing its output with the instructor's. In the discussion centered on challenge 2070, the instructor described importing the student's linked-list module into his own program. He briefly hesitated over the challenge number before settling on 2070, which is also highlighted on screen. A **driver** or **client program** is the separate program importing and exercising the module. Its **interface (API)** is the set of operations and names that program calls. In this lesson, the implementation resides in **`item.py`**.

That change matters: the submitted module must work when another program calls its interface, not only when its own demonstration prints the right text. He credited a student for helping test this new arrangement and reported one minor hiccup before it seemed to work. The assignment's complete formal contract is not supplied here.

**Key items to scan**

- **2070 / 2060 / 2058 / 2056 / 2054** — the visible progress landmarks associated with A / B / C / D / F in this historical snapshot.
- **Three challenges per week** — the instructor's intended resumed pace, with about 18 more opportunities discussed.
- **18 database records** — not interchangeable with the 15 students the instructor said remained enrolled.
- **Module / `item.py`** — the implementation unit imported by another program.
- **Driver / client program** — the separate program that exercises the imported class.
- **Interface (API)** — operations and names that the client must be able to call.
- **Module-based testing** — the instructor's program would import and use the student's linked-list implementation.
- **`count` / `at` / `has`** — tentative convenience examples, not promised exemptions; iterator/callback concepts had priority.

*Sources: transcript 00:33–10:20; logistics screen in 00-01-00-minute.png through 00-10-00-minute.png.*

## 2. Returning to the custom list: one loop, five fruit values

### Build the chain before trying to traverse it

The technical lesson resumes with a custom doubly linked list. Each **`Item`** object can hold a fruit value and links to neighboring objects. A **node** is one such linked object; the stored string, such as apple, is its value. A **reference** is how a variable refers to an object. Moving a reference to peach does not delete apple or banana from the connected chain.

The driver file, **`fruit.py`**, first constructs the chain, then prints its contents. Its setup is worth following because the variable does not finish where it began: **`fruit = fruit.last()`** makes it refer to the peach node, but the subsequent loop still begins with acai. The custom iteration methods find the first node rather than simply starting wherever the variable happens to point.

Annotated teaching version — guide-added comments; visible driver at 11:00–14:00, with the successful run observed at 14:00. Requires the corresponding classroom version of the custom module.

```python
from item import Item             # Import the class used by this classroom list.

fruit = Item("apple")             # Create a NEW chain containing one value: apple.
fruit.append("banana")            # Add banana at the end: apple -> banana.
fruit.append("peach")             # Add peach: apple -> banana -> peach.
fruit = fruit.last()               # Rebind fruit to peach; keep the same three-node chain.
fruit.insertBefore("mango")       # Insert mango before peach; fruit still refers to peach.
fruit.prepend("acai")             # Add acai at the front; do not create a separate list.

for x in fruit:                    # Begin at the chain's first node, acai; visit each value.
    print( x )                    # Print acai, apple, banana, mango, peach on separate lines.
```

Observed command and output:

```text
PS C:\com\joesta\Item> py .\test\fruit.py
acai
apple
banana
mango
peach
```

The following state trace is derived from the visible setup and iterator code, and agrees with that observed output. The arrows show forward order, not a Python built-in list literal.

| Statement completed | Connected fruit values | What `fruit` refers to |
|---|---|---|
| Construct apple | apple | apple node |
| Append banana | apple → banana | apple node |
| Append peach | apple → banana → peach | apple node |
| Assign the result of `last()` | apple → banana → peach | peach node |
| Insert mango before that node | apple → banana → mango → peach | peach node |
| Prepend acai | acai → apple → banana → mango → peach | peach node |

Appending and inserting change existing linked structure: these are **mutations**. Assignment to the result of **`last()`** is **rebinding**: the name refers to a different existing node. The insertion and prepend calls have no assignment back to the name, so their results are not used to move that name. This distinction explains why a node can be the point from which a method is called without being the first value printed.

**Key items to scan**

- **`Item`** — the custom class whose objects store values and neighbor links.
- **Node** — one object in the chain, such as the node containing peach.
- **Reference** — a connection from a name such as **`fruit`** to an existing object.
- **`fruit.py`** — the driver that builds and exercises the custom list.
- **`Item("apple")`** — creates a new one-value chain, not a reference move within an old chain.
- **`fruit.append("banana")`** — adds banana after the current last value.
- **`fruit.append("peach")`** — extends the same chain with peach.
- **`last()` / `fruit = fruit.last()`** — obtain the last node and rebind **`fruit`** to peach.
- **`fruit.insertBefore("mango")`** — puts mango immediately before peach in this example.
- **`fruit.prepend("acai")`** — adds acai at the beginning of the same connected chain.
- **`for x in fruit`** — traverses values in first-to-last order, despite **`fruit`** referring to peach.
- **`print( x )`** — displays each returned value on its own line.
- **Mutation** — changing the chain itself, as the append and insertion calls do.
- **Rebinding** — making a variable refer to a different object without itself changing the chain.

### What the iteration methods contribute

The instructor described iteration as a general computer-science idea: repeatedly visiting items, with different languages providing different support. In Python, an **iterable** is something from which a loop can obtain an iterator; an **iterator** supplies successive values and signals when it is finished. The visible classroom design makes its node objects participate in both roles. The special names **`__iter__`** and **`__next__`** are pronounced “dunder iter” and “dunder next,” referring to their double underscores.

In this implementation, the first node is called **`head`**. The extra attribute **`head._iter`** remembers which node should supply the next value. It is a saved position, often called a **cursor**. It starts at acai, moves through the chain, and eventually becomes **`None`**, meaning there is no next node left to visit. The cursor is not the fruit string: **`_object`** holds the string and **`_next`** holds the forward link.

Annotated teaching version — guide-added comments on the methods visible at 11:00–13:00. These are methods inside the existing class, not a complete standalone class definition.

```python
    def __iter__(self):                 # Called to begin traversal of this Item chain.
        head = self.first( )            # Find the first node: acai in the five-fruit example.
        head._iter = head               # RESET the saved position to acai, not to self/peach.
        return head._iter               # Return the first Item node as the iterator object.

    def __next__(self):                 # Supply one value per request, or signal completion.
        head = self.first( )            # Find the node where the saved position is stored.
        if head._iter == None:          # After peach, there is no remaining node to read.
            raise StopIteration         # Signal exhaustion; do not return a sixth fruit.
        target = head._iter._object     # Save this node's string, e.g. acai on the first call.
        head._iter = head._iter._next   # Move saved position to apple, then onward each time.
        return target                   # Return the saved string; this line does not print it.
```

The save-before-move order matters. If the next value is acai, the method first saves acai in **`target`**, then advances the cursor to apple, then returns acai. The loop receives that returned string and its separate print statement displays it. **`StopIteration`** is an exception used to indicate exhaustion; it is raised, not returned as another list value.

Source-derived execution trace for the observed five-fruit run:

| Request | Saved position before request | Value returned | Output from the loop body | Saved position afterward |
|---|---|---|---|---|
| Start iteration | Not yet initialized for this pass | First node as iterator object | None | acai |
| First value | acai | `"acai"` | `acai` | apple |
| Second value | apple | `"apple"` | `apple` | banana |
| Third value | banana | `"banana"` | `banana` | mango |
| Fourth value | mango | `"mango"` | `mango` | peach |
| Fifth value | peach | `"peach"` | `peach` | `None` |
| One more request | `None` | No value: raises `StopIteration` | No sixth line | `None`; loop ends |

The benefit identified in the recap is that the driver no longer has to maintain its own endless loop, test for the final link, and break manually. The iterable must still implement the behavior correctly; the short loop delegates the traversal rather than making it disappear. Later examples in the lesson unpack that delegation using explicit iterator requests.

The instructor's remark that implementing these methods makes the class iterable describes this particular design. Do not turn the converse into a universal claim that every Python iterable must itself define both methods: the object supplying an iterator and the iterator supplying successive values are distinct roles, even though this class combines them.

**Key items to scan**

- **Iteration** — visiting the fruit values successively rather than handling each one with a separate handwritten operation.
- **Iterable** — an object that can supply an iterator for a loop.
- **Iterator** — an object that supplies the next value and indicates exhaustion.
- **`__iter__`** — resets this chain's saved position to the first node and returns that node as iterator.
- **`__next__`** — saves one string, advances the saved position, and returns the saved string.
- **`head`** — the first node, acai in this run; it stores the shared traversal position.
- **`head._iter`** — the node whose value will be supplied on the next request.
- **Cursor** — the remembered position, moving acai → apple → banana → mango → peach → **`None`**.
- **`_object`** — the stored value, such as **`"acai"`**, rather than a neighbor link.
- **`_next`** — the link to the following node.
- **`None`** — marks the absence of another node after the last value.
- **`target`** — saves the current fruit string before the cursor moves.
- **`StopIteration`** — raised on the request after peach; no extra fruit is printed.

### The earlier callback route and the instructor's caution

The same screen shows another way to visit the chain: **`forEach`** begins at the first node and calls a supplied object's **`withItem`** method for every stored value. That supplied method is the **callback**: behavior chosen by the caller and invoked by the traversal routine. The screen does not show the callback object's class, so its printed output cannot be recovered from this fragment alone.

Annotated teaching version — guide-added comments; observed method at 11:00–13:00. With the five-fruit chain, the callback receives acai, apple, banana, mango, then peach; that is an expected call sequence, not an observed callback run in this interval.

```python
    def forEach(self, caller):           # caller must provide the withItem method used below.
        iter = self.first( )            # Start at acai for this chain; iter is a local name.
        while True:                     # Continue until the explicit last-link check breaks.
            caller.withItem(iter._object)  # Pass this fruit string to the callback.
            if iter._next == None:      # At peach, there is no following node.
                break                   # Finish after processing peach once.
            iter = iter._next           # Otherwise move to the following node, e.g. apple.
```

Here the final-node check comes after the callback. That ordering lets the last value be processed before the loop stops. The local name **`iter`** is being used for a current-node reference; the spelling alone does not prove it was obtained by calling Python's built-in iterator function.

The instructor anticipated a challenge involving callback/iterator support and mentioned that he might have overlooked an **edge case**, meaning an unusual boundary situation that could break otherwise successful code. He did not identify or demonstrate that case in this recap. He invited investigation as above-and-beyond work while telling the class to use the implementation as presented for the ordinary case. Preserve that uncertainty rather than claiming that a particular later bug was already established here. He also encouraged students to try the code: failures encountered while experimenting often make an earlier explanation meaningful, and the preceding recording or Discord could support follow-up questions.

**Key items to scan**

- **`forEach`** — walks the chain and asks the supplied caller to process each stored value.
- **`withItem`** — the callback method invoked with one fruit string at a time.
- **Callback** — caller-supplied processing invoked by traversal code; its body is not shown here.
- **`while True`** — keeps traversal running until its explicit break condition occurs.
- **`break`** — exits after the callback has processed the last node.
- **`iter`** — a local current-node variable in this method, not a call to the built-in function.
- **`iter = iter._next`** — move the local node reference to the next fruit after processing the current one.
- **Edge case** — a boundary situation the instructor suggested investigating, without identifying one here.

*Sources: transcript 10:34–14:14; 00-11-00-minute.png through 00-14-00-minute.png. Historical code and source-version details are preserved in the appendix.*

## 3. A Git detour: returning to the intended example

### Relative history depends on where you are now

The instructor intended to move on from the iterator example, but landed in a much older append demonstration. **Git** stores project revisions as **commits**. **`HEAD`** identifies the currently checked-out revision; an expression such as **`HEAD~10`** goes backward from that current starting point, not automatically from the newest version. Once the starting point has changed, another relative checkout can take the project farther back than intended.

The visible transition is specific: the iterator version was at **`ec4fdd0`**; the completed **`git checkout HEAD~10`** shown at 15:00 moved to **`fc6c333`**, whose message was “Item: addition of append( ).” The instructor verbally described going back too far. His spoken number is not a reason to rewrite the visible command. He then returned to the **`master`** branch, reviewed the one-line history, and chose the explicit count commit **`3907fbe`**. The instructor also briefly mentions **`rev-list`** as an alternative while inspecting history (15:29–15:31). No invocation, flags, or output for it are demonstrated. The detailed command-versus-buffer chronology is in the appendix.

| Workflow step | Evidence-supported result | Practical meaning |
|---|---|---|
| Iterator revision and fruit run | `ec4fdd0`; five fruit values printed | Working short-loop demonstration |
| Relative checkout | `git checkout HEAD~10` leads to `fc6c333` | Older append/manual-traversal code appears |
| Return to branch and inspect history | Log labels `1207cd3 (HEAD -> master)` | Recover a clear reference point and locate the desired feature |
| Select a specific revision | `git checkout 3907fbe`; Git reports `count` | Choose the count example without guessing another relative distance |

A direct commit checkout produces **detached HEAD**: Git is looking at a particular revision rather than following a branch name. The displayed Git advice says this permits inspection and experimental changes, and explains that a new branch can preserve new commits. In this lesson, the important distinction is between examining historical versions and treating every visible editor pane as the current saved version. The editor retained an older selected driver while the terminal already displayed the master history; that mismatch is not evidence that the old driver belonged to the newest commit.

**Key items to scan**

- **Git** — the version-control tool used to move among successive classroom implementations.
- **Commit** — a saved project revision, identified here by a short hexadecimal identifier.
- **`HEAD`** — the currently selected revision; it is the starting point for relative expressions.
- **`HEAD~10` / `git checkout HEAD~10`** — move backward relative to the current revision; the observed command selected the older append example.
- **`ec4fdd0`** — the iterator revision paired with the observed five-fruit run.
- **`fc6c333`** — the older “addition of append” revision reached by the relative checkout.
- **`master`** — the branch used to return to the recent project history.
- **`3907fbe`** — the explicit commit selected for counting.
- **`rev-list`** — mentioned history-inspection alternative, not an executed command in the sampled evidence.
- **Detached HEAD** — inspection of a specific commit rather than tracking a named branch.
- **Editor versus terminal** — a visible buffer and earlier terminal output may describe different revisions.

### What changed in the older driver

The older screen is not merely the same example with longer syntax. It uses public link attributes, returns newly appended nodes into the name, and manually walks backward and forward. This is a distinct historical design, not syntax to mix into the later underscore-attribute implementation.

Annotated teaching version — guide-added comments on the older driver visible at 15:00–17:00. Outputs below are expected from that driver and its indicated append behavior; the visible five-fruit terminal output belongs to the earlier iterator run, not to this driver.

```python
from item import Item                  # Import the older revision's class.

fruit = Item("apple")                  # Start a NEW chain at apple.
fruit = fruit.append("banana")         # Append banana and rebind fruit to the returned node.
fruit = fruit.append("peach")          # Append peach and rebind fruit to peach.

iter = fruit.prev.prev                 # Move backward from peach through banana to apple.
print( iter.object )                   # Expected output: apple.
iter = iter.next                       # Move the traversal reference to banana.
print( iter.object )                   # Expected output: banana.
iter = iter.next                       # Move to peach.
print( iter.object )                   # Expected output: peach.

iter = iter.prev                       # Reverse direction: peach -> banana.
print( iter.object )                   # Expected output: banana.
iter = iter.prev                       # Move back to apple.
print( iter.object )                   # Expected output: apple.
```

The separate traversal name moves among existing nodes; those assignments do not remove fruit from the chain. The expected printed sequence is apple, banana, peach, banana, apple. By contrast, the current iterator demonstration builds five values and lets a two-line loop request them in order. Both examples concern traversal, but their data, return conventions, attribute spellings, and output histories must remain separate.

**Key items to scan**

- **`fruit = fruit.append("banana")`** — in the older driver, retain the returned appended node as the new reference.
- **`fruit.prev.prev`** — from peach, move backward twice to apple.
- **`iter.next`** — follow the older public forward link.
- **`iter.prev`** — follow the older public backward link.
- **`iter.object`** — read the stored fruit value in this older design.
- **Manual traversal** — explicitly moving a reference and printing each value rather than letting a loop request the sequence.

*Sources: transcript 14:18–17:52; 00-15-00-minute.png through 00-19-00-minute.png. A proposed or spoken command is not treated as a completed command without supporting screen evidence.*

## 4. Counting stored items: three tests, three fresh objects

### Define the result before inventing an implementation

The instructor asked what a count method should do. A student first asked whether the class was iterable. The instructor acknowledged that iteration had just been added, then set it aside: regardless of how traversal is implemented, the purpose is to report how many items are in the list. That separates the required result from the mechanism used to obtain it.

The custom **`count()`** method returns the number of stored items. The outer **`print()`** displays that returned number. The instructor explicitly corrected the wording “return and display”: returning a value and printing it are different operations. In this example, no argument names a fruit to search for; the method counts all stored items in this course-specific class.

Annotated teaching version — guide-added comments on the count driver visible at 18:00, 18:45, and 19:00. The class implementation of counting is not visible in these frames and is not reconstructed here.

```python
from item import Item           # Import the custom class from the count revision.

fruit = Item( )                 # Create a NEW empty list: zero stored items.
print( fruit.count( ) )         # count returns 0; print displays 0.

fruit = Item( "apple" )         # Create a NEW one-item list and rebind fruit to it.
print( fruit.count( ) )         # count returns 1; print displays 1.

fruit = Item( "apple" )         # RESET again: another NEW list, containing one apple.
fruit.append("banana")         # Change this third list: apple -> banana, two items.
print( fruit.count( ) )         # count returns 2; print displays 2.
```

Observed output at 18:45 and, with the full command visible, at 19:00:

```text
PS C:\com\joesta\Item> py .\test\fruit.py
0
1
2
```

The two apple-constructor statements are both important. The second does not add an apple to the previous one-item list: it creates a different one-item list and reuses the variable name. Therefore the final test counts apple and banana, not apple, apple, and banana. Likewise, the initial empty object is not filled by the later constructor; the name has been rebound to a new object.

| Test/step | Creation or change | Current values referred to by `fruit` | Return from `count()` | Output from `print()` |
|---|---|---|---|---|
| Empty test | New empty object | No stored items | `0` | `0` |
| One-item test | New object replaces the name's previous reference | apple | `1` | `1` |
| Reset for third test | Yet another new object | apple | Not called yet | None |
| Append in third test | Modify that third object's chain | apple → banana | `2` when called | `2` |

An empty custom list may still require a Python object to represent it. The demonstrated zero means zero stored list items, not that no Python object exists. The result is established by the driver, output, and spoken explanation; it does not establish whether the unseen method uses a loop, an iterator, or a maintained counter. No one of those implementations should be attributed to this recording from this evidence alone.

**Key items to scan**

- **`count()`** — this custom method reports the number of stored list items; the three tests return 0, 1, and 2.
- **`print()`** — displays a result; it is separate from the operation that calculates and returns it.
- **`Item( )`** — creates a new empty custom-list object whose demonstrated count is 0.
- **`Item( "apple" )`** — each call creates a new one-item list, even when the same variable name is reused.
- **`fruit.append("banana")`** — adds banana only to the third test's fresh apple list, making its count 2.
- **Reset versus append** — a constructor assignment starts another object; append modifies the currently referenced chain.
- **Return versus display** — **`count`** supplies an integer; the surrounding print call writes it to the terminal.
- **Unseen implementation** — the count method's body is not supplied by the visible source in this interval.

*Sources: transcript 16:35–19:09; 00-18-00-minute.png, 00-18-45-count-run.png, and 00-19-00-minute.png.*

### Screen-only connection: inserting a node versus reusing an empty one

*Sources: 18:00 and 18:45 left editor, existing insertion/extraction helpers. These methods were visible while the count driver ran; this is not a claim that the instructor newly narrated or executed them in this interval.*

The visible **`insertBefore`** code distinguishes an empty object from a populated node. If the current object has no stored value, the method puts the supplied value into that same object and sets **`newItem = self`**. The name **`newItem`** does not by itself mean that a new object was constructed.

If the current node already contains a value, **`Item(obj)`** constructs a different node. In the fully readable first-node branch, that new node points forward to the old first node, and the old first node points backward to the new one. The middle-of-chain branch has clipped assignments, so its missing text remains in the historical appendix as unavailable.

**Annotated teaching version — source-based isolated branch, guide-added comments.** This small helper combines the readable nonempty-node construction with the complete first-node branch. Its name and signature are guide-added. It assumes **`self`** is a nonempty first node, such as apple, and **`obj`** is acai; it is not a complete replacement for **`insertBefore`**.

```python
def insert_before_first(self, obj):  # Guide helper: apple is first; insert acai before it.
    newItem = Item(obj)              # Create a NEW acai node, distinct from the apple node.
    newItem._next = self             # Make acai's forward link point to apple.
    self._prev = newItem             # Make apple's backward link point to acai.
    return newItem                  # Return the new acai NODE; do not print a fruit.
```

Expected state for that isolated first-node case: acai → apple. The original apple object still exists. A caller retaining only its old apple reference has not been rebound automatically; the returned acai reference is available if the caller chooses to save it. Independent fixture execution checks the links and that the two nodes are distinct.

**Annotated teaching version — source-based empty-node branch, guide-added helper.** The original branch writes the value and sets **`newItem`** to **`self`**; the return is the method’s visible common return. Here the helper isolates that case for study.

```python
def fill_empty_item(self, obj):  # Guide helper assumes self has no stored item yet.
    self._object = obj           # Put apple into this EXISTING empty node.
    newItem = self               # A second local name refers to that same node.
    return newItem              # Return the SAME object, now storing apple.
```

Both cases can return a node named **`newItem`**, but only the first calls the constructor. The operation, not the variable’s name, tells you whether object identity changed.

**Key items to scan**

- **`insertBefore`** — the existing helper whose visible branches distinguish empty and nonempty nodes.
- **`Item(obj)`** — creates a distinct node in the nonempty case; with acai, the new node stores acai.
- **`newItem._next = self`** — connects the new acai node forward to the existing apple node.
- **`self._prev = newItem`** — connects apple backward to acai; mutates the existing apple node.
- **`self._object = obj`** — fills the current empty node rather than constructing another node.
- **`newItem = self`** — two local names now identify one object; it does not copy the object.
- **Returned node** — the caller receives a reference, not automatic rebinding of its own variable.

### Screen-only connection: extraction clears links on the extracted object

The visible extraction tail clears **`self._prev`** and **`self._next`**, then returns **`rest`**. Earlier readable branches choose a remaining-node reference; a separate branch creates an empty **`Item`**. Some conditions and link-repair assignments are clipped, so the complete extraction algorithm cannot be recovered from this viewport.

**Annotated teaching version — source-based tail isolated in a guide-added helper.** **`rest`** is supplied to represent a reference already chosen by the unseen/earlier part of the operation. This is only the final disconnection step, not a complete extraction routine.

```python
def clear_extracted_links(self, rest):  # Guide helper: remaining reference was chosen earlier.
    self._prev = None                  # Remove this object's own backward reference.
    self._next = None                  # Remove this object's own forward reference.
    return rest                        # Return the chosen remainder; no automatic printing.
```

For a separate fixture with a banana node between apple and peach, these lines leave banana’s two own links as **`None`**. They do not by themselves reconnect apple to peach. That work must occur elsewhere in a complete extraction implementation; the guide does not supply the missing historical assignments. The driver’s name still refers to whichever object it referred to unless the driver explicitly reassigns it.

**Key items to scan**

- **`rest`** — the reference selected for the remainder by earlier extraction logic.
- **`self._prev = None`** — changes the extracted object’s backward link, not the driver’s name.
- **`self._next = None`** — changes that object’s forward link; does not itself repair its neighbors.
- **`return rest`** — hands the chosen reference back; the caller decides what to do with it.
- **Partial tail** — enough to explain disconnection, not enough to certify the complete extraction method.

## 5. From custom count to Python's length function

### The same question, a different calling form

The instructor then asked how to obtain the number of items in a built-in Python list. The list in the example is **`veg`**, containing carrot and broccoli. A student proposed length, leading to the built-in **`len()`** function.

Annotated teaching version — guide-added comments on the completed addition visible at 21:00. The 20:00 frame shows an intermediate empty print call, not the final expression.

```python
veg = ['carrot', 'broccoli']   # Create a NEW built-in list containing these two strings.
print(len(veg))               # len returns 2; the outer print displays 2.
```

Observed final output for the augmented driver, visible at 21:00:

```text
0
1
2
2
```

The first three lines still come from the preceding custom-list count tests. Only the final 2 comes from the added vegetable-list expression. Reading the nested expression proceeds inside out: evaluate the reference to the list, ask length for its number of items, and pass that integer to print. Neither counting operation is shown changing the stored values.

The instructor said he chose the name **`count`** because it seemed more intuitive to him than length. That is a naming preference for his custom interface, not a requirement imposed by Python. The **interface** is what a caller can ask for; the **implementation** is how the code stores data and carries out that request. His broader design goal was to support the operations he could perform with a built-in list while storing the data differently underneath: a linked structure can offer familiar operations without being the same internal data structure.

**Key items to scan**

- **`veg`** — the built-in list holding **`"carrot"`** and **`"broccoli"`**.
- **`len()` / `len(veg)`** — the built-in function returns 2 for that two-item list.
- **`print(len(veg))`** — passes the returned integer 2 to print; it produces the final line of the augmented driver's output.
- **`count`** — the instructor's chosen name for his custom item-total method, not Python's required name for length support.
- **Interface** — the available caller-facing operations, such as the custom count method.
- **Implementation** — the internal storage and steps; a linked representation can supply familiar list operations.

### A method name does not automatically become a built-in operation

The next question was whether naming a method **`len`** would conflict with Python's existing length function. A student expected a conflict; the instructor was not convinced and began exploring where names are defined and how methods are called. This is a question about **scope**, the context in which a name is found, as well as a question about API naming.

At the transition, he also recalled changing his neighbor-access methods from **`next`** and **`previous`** to **`after`** and **`before`**. The following part of the lesson tests what those names mean to a caller. At this stage, no successful use of the built-in length function on the custom object has been demonstrated, and no special length-support method is visible. Do not infer that simply renaming the existing count method would make the built-in function work on the custom list.

**Key items to scan**

- **`len` as a proposed method name** — a naming/scope question posed in discussion, not an implemented or tested custom length feature here.
- **Scope** — the context that determines where a name can be found and how it is accessed.
- **`next` / `previous`** — earlier neighbor-access names recalled by the instructor.
- **`after` / `before`** — replacement names whose effect on callers motivates the next discussion.
- **API naming** — names form part of how other programs call the class; changing them can require caller changes.

*Sources: transcript 19:15–21:52, with the 22:00–23:30 overlap establishing the transition; 00-20-00-minute.png and 00-21-00-minute.png.*

### Supplemental clarification: a method called len is not the length hook

The classroom question is about names: would a method called **`len`** conflict with Python’s function? It need not. But avoiding a conflict and implementing the built-in length operation are separate questions. Python’s **`len(object)`** asks the object’s type for **`__len__`**; an ordinary **`object.len()`** method has no automatic role in that protocol.

**Simplified teaching example — guide-added comments; independently verified, not classroom code.** This first example deliberately fails after its first print.

```python
class Ordinary:                # A small guide-only class, not the course Item class.
    def len(self):             # Ordinary method named len; not the special length hook.
        return 2               # Supply 2 when this ordinary method is explicitly called.

ordinary = Ordinary()          # Create a NEW instance of this simplified class.
print(ordinary.len())           # Explicitly call its method; expected output: 2.
print(len(ordinary))            # Built-in len cannot find __len__; raises TypeError.
```

**Independently verified output/error:**

```text
2
TypeError: object of type 'Ordinary' has no len()
```

Now define the actual special method. The guide’s **`Bag`** stores the lesson’s two vegetables and delegates their total to Python’s built-in list length. This is a small protocol demonstration, not a claim that the instructor implemented length support on **`Item`**.

**Simplified teaching example — guide-added comments; independently verified:**

```python
class Bag:                             # Guide-only container for the two vegetables.
    def __init__(self):                # Initialize each newly created bag.
        self.items = ['carrot', 'broccoli']  # Store a NEW two-string list on this bag.

    def __len__(self):                 # Special hook used by Python's len(bag).
        return len(self.items)         # Return 2 for the two stored vegetables.

bag = Bag()                            # Create a NEW bag containing carrot and broccoli.
print(len(bag))                        # Built-in len uses the hook; expected output: 2.
veg = ['carrot', 'broccoli']            # Separate NEW built-in list for comparison.
print(len(veg))                        # Count all stored elements; expected output: 2.
print(veg.count('carrot'))              # Count matching carrot values; expected output: 1.
```

**Independently verified output:**

```text
2
2
1
```

The course’s **`fruit.count()`** means “how many stored items in total?” Python’s built-in **`veg.count('carrot')`** means “how many values equal carrot?” Those similarly named methods have different contracts. Calling a built-in list’s **`count()`** with no argument raises **`TypeError`**; the custom no-argument example should not be copied unchanged onto a Python list.

**Key items to scan**

- **`ordinary.len()`** — calls an ordinary method and returns 2; no built-in protocol is implied.
- **`len(ordinary)`** — raises **`TypeError`** because that type has no **`__len__`** hook.
- **`__len__`** — the special method through which this **`Bag`** supports built-in length.
- **`Bag`** — a guide-only container storing carrot and broccoli; not historical classroom source.
- **`self.items`** — the particular bag’s two-element list; its length is 2.
- **`len(bag)` / `len(veg)`** — both return the total 2 in this fixture.
- **`veg.count('carrot')`** — returns 1 matching occurrence, not the total number of vegetables.
- **`fruit.count()`** — custom course method with its own no-argument total-item contract.
- **`TypeError`** — also expected if built-in list **`count()`** is called without its required value argument.

Supplemental sources: [Python’s length hook](https://docs.python.org/3/reference/datamodel.html#object.__len__) and [sequence occurrence counting](https://docs.python.org/3/builtins/stdtypes.html#sequence.count).

## 6. Names belong somewhere: `after()` is not `fruit.after()`

*Sources: recording 21:45–24:44; frames 22:00–25:00.*

The instructor had renamed the linked-list navigation methods from next/previous terminology to **`after`** and **`before`** to avoid confusion with Python’s built-in **`next()`**. Renaming a method changes the interface that other programs call: a client still using the old spelling breaks unless that spelling remains available. Importing the updated module does not automatically rewrite the client’s calls.

But using the same word in two different places need not create a collision. A method is a function accessed through a class or its objects; **`fruit.after()`** looks up **`after`** on the object referenced by **`fruit`**. Bare **`after()`** instead asks Python to resolve an unqualified name in the calling context. Defining **`after`** inside **`Item`** does not introduce a standalone **`after`** name into this driver.

**Annotated teaching version — guide-added comments.** This short reconstruction uses the visible **`Item`** interface; the successful qualified call is supported by the instructor’s account, not by a frame that shows the complete call and run together.

```python
from item import Item           # Import the class, not each method as a global name.

fruit = Item("apple")           # Create a NEW apple node/list.
fruit.append("banana")          # Mutate that list: apple -> banana.

# after()                       # If executed here: NameError; no bare name after exists.
fruit.after()                   # Return the banana node; do not print or rebind fruit.
```

At 24:00 the observed driver instead ends with active **`after()`**, and the run fails on line 16:

```text
NameError: name 'after' is not defined. Did you mean: 'aiter'?
```

The suggestion is part of Python’s error text, not the repair taught here. The instructor then reports that **`fruit.after()`** runs without an error and without displaying anything extra. That is consistent with a method returning an object whose result the driver discards. A call can do useful work or return a value without printing it.

The visible implementation explains an additional distinction. **`after`** returns the next **node object**, not the fruit string stored inside it. If there is no next node, it returns the current object rather than **`None`**. **`before`** does the analogous operation using the previous link.

**Annotated teaching version — guide-added comments; methods belong inside `Item`.**

```python
def after(self):                 # self is the node on which this method was called.
    if self._next != None:       # At apple, the next link refers to the banana node.
        return self._next       # Give back that node; do not change the caller's variable.
    return self                 # At the last node, give back that same last node.

def before(self):                # Ask for the node before the current one.
    if self._prev != None:       # At banana, the previous link refers to apple.
        return self._prev       # Give back the apple node.
    return self                 # At the first node, remain at that node.
```

This is why a class method named **`next`** would not, by itself, replace Python’s built-in **`next`**: **`fruit.next()`** and **`next(iterator)`** use different lookups. The instructor’s wording about a name being “within the scope of the class” describes this distinction; it does not mean methods are inaccessible from outside the class.

**Key items to scan**

- **`after`** — the visible class method that returns the next node, or the current node at the end.
- **`before`** — returns the previous node, or the current node at the beginning.
- **`after()`** — fails here because the driver has no standalone name **`after`**.
- **`fruit.after()`** — finds the method through **`fruit`**; a return value is not automatically displayed.
- **`next()`** — Python’s built-in iterator operation; not automatically replaced by a similarly named class method.
- **Scope/name lookup** — the place Python searches for a name; the dot in **`fruit.after()`** identifies the object to search.
- **`self`** — the particular node receiving the method call.
- **`_next` / `_prev`** — stored links to neighboring nodes; these attributes are not the built-in function **`next`**.
- **`return`** — hands a result to the caller; it does not print the result or reassign **`fruit`**.
- **`NameError`** — the unqualified name was not found; different from finding a name whose object is the wrong type.
- **`Item` node object** — the structural object returned by navigation, distinct from its stored fruit string.
- **`None`** — a missing internal neighbor link; these public navigation methods return the current node instead.
- **`fruit.next()` versus `next(iterator)`** — method lookup through an object versus a built-in next-value request.

### Screen-only connection: what an Item starts with

*Source: **`00-22-00-minute.png`**, **`item.py`** lines 1–30. The following explanation unpacks visible code; it is not an additional narrated classroom run.*

The class begins with **`_object`**, **`_next`**, **`_prev`**, and **`_iter`** set to **`None`** in the class body. These are **class attributes**: initial values available through the class. Constructing an apple node executes **`self._object = obj`**, which sets an **instance attribute** on that particular new node. It does not change every node’s stored fruit or overwrite the class’s default.

The parameter **`obj = None`** makes the argument optional. Calling **`Item()`** uses that default; calling **`Item("apple")`** supplies apple instead. The **`object()`** method is an accessor: it returns the stored value, unlike **`after()`**, which returns a node.

**Annotated teaching version — guide-added comments on a readable class prefix, with guide-added print tests.** Run this separately from the full fruit driver. It intentionally contains only the visible fields, constructor, and accessor—not append, navigation, or iteration.

```python
class Item:                      # Define this isolated prefix of the custom class.
    _object = None               # Class default: no stored value.
    _next = None                 # Class default: no following-node link.
    _prev = None                 # Class default: no preceding-node link.
    _iter = None                 # Class default: no traversal position established.

    def __init__(self, obj=None): # Item() uses None; Item("apple") supplies apple.
        self._object = obj       # Store the value on THIS newly created instance.

    def object(self):            # Accessor: ask this node for its stored value.
        return self._object     # Return None for empty, or apple for the apple node.

empty = Item()                   # NEW instance whose stored value is None.
apple = Item("apple")            # Different NEW instance whose stored value is apple.
print(empty.object())            # Expected display: None; no stored list item.
print(apple.object())            # Expected display: apple; not an Item representation.
print(Item._object)              # Expected display: None; class default is unchanged.
```

**Independently verified output of this isolated prefix fixture:**

```text
None
apple
None
```

The one-leading-underscore names are an internal-use convention, not an access barrier enforced by Python. They are also different from special double-underscore protocol names such as **`__iter__`**. This is a supplemental naming clarification, not a claim that the instructor established a privacy mechanism. See [Python’s documented underscore convention](https://docs.python.org/3/tutorial/classes.html#private-variables).

**Key items to scan**

- **Class attribute** — a value initially stored on the class, such as **`Item._object = None`**.
- **Instance attribute** — a value stored on one node; apple’s **`_object`** is apple while the empty instance’s is **`None`**.
- **`obj = None`** — default parameter value used when no argument is supplied.
- **`__init__`** — initializes the new instance; its spelling is part of Python’s construction machinery.
- **`self._object = obj`** — writes the supplied value to that instance, leaving the class default unchanged.
- **`object()`** — returns the stored fruit value rather than a neighboring node.
- **`_next` / `_prev`** — forward/backward node references, initially absent in this prefix.
- **`_iter`** — the traversal-state field, initially **`None`**; later iteration setup writes a position.
- **Leading underscore** — internal-use convention, not enforced privacy; different from **`__iter__`**.

### The visible append prefix: same node or a new one?

The beginning of **`append`** uses the same empty/nonempty distinction seen in insertion. An empty object is filled in place. For a populated chain, the code first asks for its last node, then constructs the new node. Its linking and final return are below this viewport.

**Annotated teaching version — guide-added comments; explicitly partial method.** This is not a runnable substitute for the full append operation.

```python
def append(self, obj):          # Add a stored value; only the visible prefix is reproduced.
    if self._object == None:    # Current object represents an empty list.
        self._object = obj      # Store apple in that EXISTING object, for example.
        newItem = self          # Keep a reference to the same now-filled object.
    else:                       # Existing stored value: another node must be prepared.
        last = self.last()      # Find the existing final node before connecting a new one.
        newItem = Item(obj)     # Construct a NEW node, e.g. one storing banana.
    # Link updates and the final return are outside this original viewport.
```

Do not infer a return value from this truncated block’s implicit Python behavior. It is evidence of the branch decision and object creation only; the complete historical method has further statements. Independent tests of the guide’s complete fruit fixture verify its outcomes, not the missing historical lines.

**Key items to scan**

- **`append` prefix** — visibly fills an empty node or prepares a new node for a populated list.
- **`self._object == None`** — chooses the empty representation case.
- **`newItem = self`** — preserves the existing node’s identity when filling it.
- **`last = self.last()`** — saves the existing tail reference; it does not itself append banana.
- **`newItem = Item(obj)`** — constructs the additional node; connection and return still require the missing continuation.

## 7. A list can be iterable without itself being an iterator

*Sources: 25:09–29:12; frames 26:00, 26:08, 27:00–30:00.*

The driver’s built-in Python list is **`veg = ['carrot', 'broccoli']`**. Its length is 2. The instructor tries to request its next item directly and gets a different kind of failure from the earlier missing name.

**Annotated teaching version — guide-added comments; isolated vegetable portion.**

```python
veg = ['carrot', 'broccoli']     # Create a NEW two-element Python list.
print(len(veg))                 # Return the length, then print 2.
print(next(veg))                # Fail: veg is a list, not an iterator.
```

Observed final error, at both 26:00 and the 26:08 event frame:

```text
TypeError: 'list' object is not an iterator
```

The original full driver prints **`0`**, **`1`**, **`2`**, **`2`** before reaching this error: the first three numbers belong to the preceding custom-list count tests, and the final **`2`** is **`len(veg)`**. The failure does not mean **`veg`** is undefined. Python found **`print`**, **`next`**, and **`veg`**; the object passed to **`next`** does not provide the required next-item operation. More precisely, **`next(veg)`** fails while evaluating the argument to **`print`**, so that outer print call never receives a vegetable to display.

An **iterable** can supply an iterator. An **iterator** remembers a traversal position and can supply the next value. The built-in **`iter()`** bridges the two: **`iter(veg)`** obtains a list iterator beginning before the first returned element. It does not return the string **`'carrot'`** itself.

**Annotated teaching version — guide-added comments; corrected state.**

```python
veg = ['carrot', 'broccoli']     # Start with a NEW two-item list.
viter = iter(veg)               # Create its iterator; no vegetable has been returned yet.
print(next(viter))              # Return and print carrot; remember broccoli is next.
```

Observed corrected result at 27:00: after the original driver’s numeric prefix, the terminal prints **`carrot`**. Its scrollback also preserves an intermediate failed run on line 17 with **`print(next(veg))`**: inserting **`viter = iter(veg)`** alone was not enough. The argument of **`next`** also had to change from the list to its iterator.

### Repeated requests consume the iterator’s position

The next changed state adds repeated requests. Each call advances the same iterator; it does not delete a vegetable from the underlying list.

**Annotated teaching version — guide-added comments; intentionally failing state.**

```python
veg = ['carrot', 'broccoli']     # A fresh list still contains both vegetables.
viter = iter(veg)               # A fresh iterator starts at carrot.
print(next(viter))              # Print carrot; advance past the first element.
print(next(viter))              # Print broccoli; advance past the second element.
print(next(viter))              # Raise StopIteration; there is no third value to print.
```

Observed at 30:00, and more completely in the 31:00 terminal scrollback:

```text
carrot
broccoli
Traceback (most recent call last):
  File "C:\com\joesta\Item\test\fruit.py", line 19, in <module>
    print(next(viter))
StopIteration
```

*The traceback excerpt omits Python’s caret underline. The original count/length prefix is also omitted here.*

Guide-derived execution trace, independently checked for the built-in-list behavior:

| Step | Iterator before request | Operation | Returned / printed result | Iterator afterward |
|---|---|---|---|---|
| Setup | Not yet created | `viter = iter(veg)` | Iterator object; nothing printed | Ready for carrot |
| 1 | Ready for carrot | `next(viter)` inside `print` | Returns `'carrot'`; prints `carrot` | Ready for broccoli |
| 2 | Ready for broccoli | `next(viter)` inside `print` | Returns `'broccoli'`; prints `broccoli` | Exhausted |
| 3 | Exhausted | `next(viter)` inside `print` | Raises `StopIteration`; no third value printed | Still exhausted |

The third-request traceback belongs to the later executed state; earlier unsaved edits and output are distinguished in Appendix B2.

**Key items to scan**

- **`veg = ['carrot', 'broccoli']`** — creates a Python list containing those two strings in that order.
- **`len(veg)`** — returns 2; it does not start or consume iteration.
- **Iterable** — can provide a way to traverse its values; **`veg`** qualifies.
- **Iterator** — retains the next traversal position; **`viter`** qualifies.
- **`iter(veg)`** — obtains an iterator for the list; does not return the first string.
- **`viter`** — the separate iterator object, not a replacement name for the list.
- **`next(veg)`** — raises **`TypeError`** because this list is not itself an iterator.
- **`next(viter)`** — returns carrot, then broccoli, then raises on the extra request.
- **`StopIteration`** — the exception signaling that there is no next value; not a returned third value.
- **Consumption** — advances the iterator’s saved position; the underlying list still contains carrot and broccoli.
- **Nested evaluation** — in **`print(next(viter))`**, the next-item request must succeed before its result can be printed.

## 8. Why a `for` loop stops cleanly—and why both vegetables appear again

*Sources: 29:25–31:13; frames 30:00, 30:50, 31:00, 32:00.*

The instructor adds a loop below the explicit requests. A student catches an important control-flow problem: the third explicit **`next`** still crashes before execution can reach that loop. Commenting out that call is necessary; adding a correct loop later does not repair an earlier unhandled exception.

**Annotated teaching version — guide-added comments; final vegetable demonstration, with the earlier custom count tests omitted.**

```python
veg = ['carrot', 'broccoli']     # Create the two-element list for this run.
print(len(veg))                 # Print 2.

viter = iter(veg)               # Create a separate iterator for manual requests.
print(next(viter))              # Print carrot; consume the first position.
print(next(viter))              # Print broccoli; now viter is exhausted.
#print(next(viter))             # Disabled: would stop this program before the loop.

for v in veg:                   # Iterate the LIST: obtain a fresh iterator from veg.
    print(f"v: {v}")            # Print v: carrot, then v: broccoli.

print("goes on")               # Outside the loop; print once after normal exhaustion.
```

Expected output of this isolated version:

```text
2
carrot
broccoli
v: carrot
v: broccoli
goes on
```

The full classroom run at 32:00 shows this suffix after the earlier custom count outputs **`0`**, **`1`**, **`2`**. Appendix B2 records when the loop and final print were added and actually run.

The distinction is in **`for v in veg:`**, not **`for v in viter:`**. Iterating the list obtains a fresh iterator, so both vegetables appear even though **`viter`** is exhausted. The list did not lose its contents. As a separately reasoned and independently checked contrast, a loop over the exhausted **`viter`** would print no vegetables, then continue after the loop.

A **`for`** loop arranges repeated next-item requests and treats **`StopIteration`** as normal completion. The same exhaustion signal that produces an unhandled traceback in an explicit call lets the loop finish normally. This does **not** mean a **`for`** loop catches every possible error from the code being run.

| Stage | What is traversed | Result | What follows |
|---|---|---|---|
| Manual calls | `viter`, created once | `carrot`, then `broccoli` | That iterator is exhausted |
| First loop pass | Fresh iterator obtained from `veg` | `v: carrot` | Request another item |
| Second loop pass | Same fresh iterator | `v: broccoli` | Request another item |
| Loop’s final request | Fresh iterator, now exhausted | `StopIteration` ends the loop | Execute `print("goes on")` |

**Key items to scan**

- **`#print(next(viter))`** — comments out the failing third request so execution reaches the loop.
- **`for v in veg:`** — asks the list for a new traversal; assigns each returned string to **`v`**.
- **`v`** — the current value, first carrot and then broccoli; not the whole list.
- **`f"v: {v}"`** — an f-string: literal **`v: `** followed by the value of **`v`** inside braces.
- **Indentation** — makes the labeled print part of the loop; the unindented final print runs afterward.
- **`print("goes on")`** — demonstrates execution continuing after ordinary iterator exhaustion.
- **`for v in viter:`** — contrasting expression, not the displayed classroom loop; it would have nothing left after the two manual requests.
- **Automatic termination** — **`for`** handles **`StopIteration`** as the end of its traversal, not as a program crash.

### A screen-only glimpse of the count algorithm

*Source: 32:00 editor; code visible on screen, not a newly completed narrated implementation.*

The callback and iterator methods visible again at 32:00 use the same mechanisms already explained in “Returning to the custom list.” The new evidence in that viewport is the beginning of the counting method.

The 32:00 viewport exposes only the beginning of **`count()`**. It distinguishes an empty first node from a populated first node before beginning its traversal. This visible prefix helps explain the earlier 0/1 count cases, but it is not a complete runnable implementation:

**Annotated teaching version — guide-added comments; intentionally partial historical algorithm.**

```python
def count(self):                       # Count stored items; only the visible prefix is reproduced.
    iter = self.first()                # Begin at the first node, not wherever the caller points.
    if iter._object == None:           # The first node has no stored item.
        count = 0                     # Start with zero items for that empty state.
    else:                             # A first stored item exists, such as apple.
        count = 1                     # Include that first item in the initial total.
    while True:                       # Begin walking toward the end.
        if iter._next == None:        # No following node exists.
            break                     # Leave this loop.
    # Remaining historical lines are outside this viewport; not reconstructed here.
```

Do not run this prefix as a substitute for the full count method: the off-screen continuation is needed to establish advancement, accumulation and return behavior. The historical appendix preserves the exact visible numbered lines without supplying those missing statements.

**Key items to scan**

- **`count()`** — only its starting logic is visible; the complete method is not recovered.
- **`iter = self.first()`** — local node reference starts at the first node even when the caller refers elsewhere.
- **`iter._object == None`** — distinguishes the empty-node representation from a first stored value.
- **`count = 0` / `count = 1`** — initialize the total for empty or nonempty first-node cases.
- **`while True` / `break`** — begin the end-checking loop; the advancement and return remain outside the viewport.


## 9. Returning to an older implementation with Git

*Sources: 32:16–33:52; frames 33:00, 34:00 and 34:47.*

To demonstrate the custom iterator again, the instructor returns from the **`count`** revision to the earlier Python-iterator revision. Git first refuses the switch because the edited driver would be overwritten. That refusal protects the local changes; it is not a successful checkout.

**Observed command progression — comments added for teaching, not original terminal text.** These are historical commands, not instructions to discard a learner’s current work.

```powershell
git checkout master                 # Attempt a switch; Git refuses because fruit.py changed.
git log --oneline                    # Inspect abbreviated commit IDs and their messages.
git checkout ec4fdd0                 # Attempt the iterator revision; Git refuses again.
git restore .\test\fruit.py          # Discard the local driver edits in the working file.
git checkout ec4fdd0                 # Retry; this time the checkout succeeds.
```

The log marks **`3907fbe (HEAD) count`** before the switch. The successful terminal result is:

```text
Previous HEAD position was 3907fbe count
HEAD is now at ec4fdd0 Item: python Iterator
```

**`HEAD`** identifies the checked-out revision in this demonstration. The commit IDs are repository-specific, not general commands a learner should reuse elsewhere. The first failure explicitly names **`test/fruit.py`** and suggests committing or stashing changes before switching. The instructor instead intentionally restores that one file, then retries. Restoring is not saving the experiment: here it discards the local driver edits that prevented checkout.

The visible consequence matters for interpreting the lesson. The count/vegetable driver is replaced by the five-fruit construction and loop. Later terminal scrollback can still contain results from the old driver; the screen has changed revisions, but scrollback has not been erased.

**Key items to scan**

- **`git checkout master`** — an attempted branch switch that failed in this sequence.
- **`git log --oneline`** — displays commit IDs with short descriptions so the iterator revision can be selected.
- **`3907fbe`** — the visible **`count`** revision before switching.
- **`ec4fdd0`** — the selected **`Item: python Iterator`** revision.
- **`HEAD`** — the currently checked-out revision, shown alongside **`count`** before the successful switch.
- **`git restore .\test\fruit.py`** — discards the local working-file edits in this demonstration; use only when that loss is intended.
- **`git checkout ec4fdd0`** — failed before restoration and succeeded afterward; the command text alone does not prove success.
- **Working file versus scrollback** — the file changes with checkout; previous output can remain visible and belong to another revision.

## 10. Disabling and restoring the custom iteration methods

*Sources: 31:26–32:07 and 33:48–35:15; frames 34:00–36:00 and 34:47.*

The class returns to the five-fruit driver taught earlier: apple, banana and peach are created, mango is inserted before peach, and acai is prepended. The unchanged setup and the commented implementation are in **“Returning to the custom list”**. This time the experiment changes the availability of the methods, not the fruit inputs.

The instructor disables **`__iter__`** and **`__next__`**, then runs the same loop. The observed failure at driver line 10 is:

```text
TypeError: 'Item' object is not iterable
```

No fruit is printed by that loop in this failed run: Python cannot obtain the iterator it needs to begin. Restoring the methods makes the later run at 36:00 print acai, apple, banana, mango and peach again. This tests the connection between the class’s methods and Python’s loop machinery; it is not a different five-fruit algorithm.

**Evidence limit:** the complete disabled saved source is obscured. The 35:00 screenshot contains an unsaved delimiter-removal edit, not the source proven by the earlier traceback. Appendix B5 preserves these states rather than inventing an exact runnable disabled block.

**Key items to scan**

- **`__iter__`** — the setup method Python needs for this custom class’s demonstrated iteration path.
- **`__next__`** — the next-value method supplied by the returned head object.
- **`for x in fruit:`** — unchanged caller; its ability to start depends on the class’s available protocol.
- **`TypeError: 'Item' object is not iterable`** — failure to start traversal, unlike exhaustion after valid values.
- **Restored methods** — restore the working five-fruit loop without rebuilding its teaching explanation.

## 11. Printing an iterator object is not requesting its next fruit

*Sources: 35:21–40:12, with overlap for the student’s explanation; frames 36:00–39:00 and the 38:55 event.*

After the five-fruit loop, the instructor explicitly obtains an iterator and prints it. The screen shows an **`Item`** object description, not the word **`acai`**. That is not a failed traversal: no next-item request has been made by that print.

**Annotated teaching version — guide-added comments; append to the five-fruit driver above.**

```python
#iter = fruit.first()                 # Keep this experiment disabled; iter must name the built-in.
pyIter = iter(fruit.first())           # Find acai; reset this custom iterator to the first node.
print(pyIter)                         # Display the Item object's representation, not its fruit.
print(next(pyIter))                   # Request one value: return and print acai; advance to apple.
# print(next(pyIter))                 # If added next, expected output: apple.
```

The 38:55 event and 39:00 frame show the full run ending with:

```text
acai
apple
banana
mango
peach
<item.Item object at 0x000002B8B99EE060>
acai
```

The first five lines come from the preceding **`for`** loop. The object description comes from **`print(pyIter)`**. The final **`acai`** comes from **`print(next(pyIter))`**, after **`iter(fruit.first())`** reset this implementation’s traversal. The hexadecimal address is an observed value from that run, not a stable expected output. An earlier run visible in the same scrollback has a different address.

At about 39:10–39:19, the instructor predicts that another request would show apple. The frame at 39:00 still contains only one explicit **`next`** call; the prediction is consistent with the method’s saved position, but is not a second observed output in this frame.

The class next pauses to put the object/request distinction into words, then connects it to iteration more broadly.

**Key items to scan**

- **`pyIter`** — the exact case-sensitive name used for the custom iterator reference.
- **`fruit.first()`** — returns the first node, which stores acai; not the string **`'acai'`** itself.
- **`iter(fruit.first())`** — invokes this class’s iterator setup and resets its saved position to acai.
- **`print(pyIter)`** — displays the **`item.Item`** object description; does not consume a fruit.
- **Object representation** — the displayed form **`<item.Item object at ...>`** identifies an object rather than printing its stored fruit.
- **`print(next(pyIter))`** — requests a stored value and then displays it; the first value is acai.
- **Expected next value: apple** — after acai is returned, the saved position has moved to apple.
- **`#iter = fruit.first()`** — an intentionally disabled experiment here; the name-collision consequence is developed later in the session.


## 12. Iteration means asking for the next thing, not choosing its number

*Sources: transcript 39:24–42:16, with overlap into the earlier demonstration; original frames 40:00–42:00.*

The class paused to explain what was happening rather than add more code. A student described **`pyIter`** as the thing supplied to the operation that performs the iteration, and **`next()`** as the operation that moves it along. This usefully separates the object that remembers a position from the request for another value. Printing the iterator itself does not make that request.

Other students suggested a beginning and an ongoing evolution. The product-version analogy—successive versions of an Apple phone—captured the idea of a first thing followed by another. In this program, however, the fruits do not evolve into different fruits: the program visits existing items in sequence.

The instructor's practical distinction was that iteration lets you start with the first item and reach the second, third, and fourth without supplying a numerical position each time. An **index** explicitly identifies a position; an **iterator** remembers progress so the next request can continue from where the previous request stopped. A collection must support the relevant iteration behavior; merely calling something a collection does not implement that behavior for a custom class.

**Key items to scan**

- **Iteration** — visiting successive items, such as **`acai`**, then **`apple`**, without naming each position.
- **Index** — a numerical position used explicitly, such as **`sport[0]`**.
- **Iterator** — an object that remembers the next position to visit.
- **`pyIter`** — the variable referring to the custom fruit iterator, not the fruit string it will return next.
- **`next()`** — requests one value and advances iteration; it is not the same as printing the iterator object.
- **Product-version analogy** — helps convey succession, but visiting the fruit list does not modify each fruit into its successor.

## 13. Soccer and boxing: indexing, iteration, and a naming preference

*Sources: transcript 42:32–47:23; frames 43:00–48:00. Historical versions are separated in C2.*

### One collection, two ways to get its members

The class supplied a new example: **`sport = ['soccer', 'boxing']`**. The instructor preferred the singular name **`sport`**, describing a container from which a specific sport can be selected. A student preferred “sports”; another pointed out that both perspectives made sense. The instructor explicitly treated this as style, not a rule he would impose. Python does not require a singular collection name.

**Annotated teaching version — guide-added comments.** This is the completed two-item indexing example, not the earlier moment when the editor contained an empty list.

```python
sport = ['soccer', 'boxing']  # Create a NEW built-in list with two strings.
print(sport[0])              # Read index 0; print soccer. The list is unchanged.
print(sport[1])              # Read index 1; print boxing. The list is unchanged.
```

**Observed output — sports portion of the classroom run, visible at 45:00 and in later scrollback:**

```text
soccer
boxing
```

The square brackets after the variable select positions. They are different from the square brackets in the assignment, which create a list. Reading index 0 does not “use up” soccer; indexing does not remember a next position on behalf of this code.

**Key items to scan**

- **`sport = ['soccer', 'boxing']`** — creates a list containing soccer first and boxing second.
- **`sport` versus `sports`** — instructor preference versus an alternative naming style, not a Python requirement.
- **`sport[0]`** — returns the first stored string, **`soccer`**.
- **`sport[1]`** — returns the second stored string, **`boxing`**.
- **`print(...)`** — displays the selected string; neither index read changes the list.

### The iterator is not soccer; it is the object that will supply soccer

Calling **`iter(sport)`** produces a list iterator. Assigning it to **`siter`** gives the program a name for that iterator. **`print(siter)`** displays its object representation, not either sport and not the complete list.

**Annotated teaching version — guide-added comments.** Continue from the two-item list above. The first two **`next`** calls match the 47:00 driver; the third is the later exhaustion experiment shown at 48:00.

```python
siter = iter(sport)    # Create a NEW iterator positioned before soccer.
print(siter)          # Display the iterator object; do not consume soccer.
print(next(siter))    # Return soccer, advance toward boxing, then print soccer.
print(next(siter))    # Return boxing, reach the end, then print boxing.
print(next(siter))    # Raise StopIteration: no third string exists; print is not called.
```

**Observed output — final sports portion of the successful two-request run at 47:00:**

```text
<list_iterator object at 0x0000021EF36E05B0>
soccer
boxing
```

That hexadecimal address is the one visible in this run, not a value to expect on another computer or another execution. In the later run, the third request produced **`StopIteration`** at **`fruit.py`** line 27. The fully readable traceback is visible in the 49:08 event frame as earlier scrollback, above the subsequent linked-list failure. These are separate runs, not two errors from one execution.

**Guide-derived trace of the three-request version:**

| Operation | Next position before | Returned value or raised exception | Printed by this statement | Next position after |
|---|---|---|---|---|
| `iter(sport)` | No iterator yet | A new list iterator | Nothing | Soccer |
| `print(siter)` | Soccer | No sport requested | Iterator representation | Soccer |
| First `next(siter)` inside `print` | Soccer | `'soccer'` | `soccer` | Boxing |
| Second `next(siter)` inside `print` | Boxing | `'boxing'` | `boxing` | End |
| Third `next(siter)` inside `print` | End | Raises `StopIteration` | No third sport; uncaught exception produces a traceback | End |

The inner call must finish before the outer print can use its result. On the third request there is no returned string for **`print`** to display. The instructor briefly said the next method “returned an exception”; the precise distinction is that exhaustion **raises** an exception rather than returning an exception as the next item.

**Key items to scan**

- **`iter(sport)`** — obtains a new iterator for the existing two-item list; it does not return **`soccer`**.
- **`siter`** — remembers progress through soccer and boxing.
- **`print(siter)`** — displays a **`list_iterator`** representation; it leaves soccer available.
- **`next(siter)`** — returns soccer on its first request and boxing on its second.
- **`print(next(siter))`** — first obtains the next value, then prints it; exhaustion prevents the outer call.
- **`StopIteration`** — signals that the third request has no item to return.
- **Runtime address** — varies between executions; it is not the list's first value or an index.

## 14. Returning to the fruit chain: restart, then test its stopping rule

*Sources: 39:10–39:19 prediction, 40:00–48:00 driver states, and 47:33–48:31 explanation.*

The class returns to the implementation already explained in “Returning to the custom list.” Its **`head._iter`** attribute still records the next node to visit. The new comparison is between a finished loop and explicit requests after restarting that same custom traversal.

**Annotated teaching version — guide-added comments.** Continue with the five-fruit chain; do not construct a new one. The second explicit request was predicted earlier and is now present in the later driver.

```python
for x in fruit:                   # Traverse the existing chain from acai through peach.
    print(x)                     # Print each of its five stored fruit strings.

pyIter = iter(fruit.first())      # RESET this existing head's saved position to acai.
print(pyIter)                    # Show the Item object; do not consume a fruit.
print(next(pyIter))              # Return and print acai; saved position becomes apple.
print(next(pyIter))              # Return and print apple; saved position becomes banana.
```

**Observed fruit portion of the completed 47:00 run:**

```text
acai
apple
banana
mango
peach
<item.Item object at 0x0000021EF36BE2C0>
acai
apple
```

The explicit setup after the loop reuses the head and resets its position; it does not rebuild the fruit nodes. This is a property of the custom method, not a promise that **`iter()`** resets every iterator. The full save → advance → return mechanism and six-request trace remain in the first teaching section rather than being repeated here.

**Key items to scan**

- **`head._iter`** — the saved next-node reference; no fruit links are removed by traversal.
- **`pyIter = iter(fruit.first())`** — resets this custom head’s state after the loop.
- **`iter()`** — does not universally reset iterators; the reset here comes from this class’s method.
- **Second `next(pyIter)`** — now visibly requests apple after acai; advances the saved position to banana.
- **`print(pyIter)`** — displays the object without consuming a fruit; its observed address is run-specific.
- **Same nodes, restarted traversal** — a state reset, not a new list construction.

### Remove the guard: why the sixth request fails at line 132

*Sources: transcript 48:38–50:36; frames 49:00, 49:08, 49:56, 50:00, and 51:00.*

The instructor tested the claim that the loop ends because of **`StopIteration`**: what if the custom next method does not raise it? The terminal shows the five fruit names, then a failure in **`item.py`** line 132. The loop did not fail on its first fruit, and the program did not reach the later sports code in that run.

**Annotated teaching version — source-based reconstruction, guide-added comments.** This models the guard-disabled behavior evidenced by the run. The sampled 49:00 editor shows an intermediate edit, not the complete executed disabled-guard text; C3 preserves that distinction.

```python
def __next__(self):              # Broken experiment: no exhaustion guard.
    head = self.first()         # Still finds the acai head on the sixth request.
    # The test for head._iter == None and its raise are disabled here.
    target = head._iter._object  # Sixth request: None._object raises AttributeError.
    head._iter = head._iter._next  # NOT reached on that failed sixth request.
    return target              # NOT reached: no sixth fruit is returned.
```

After peach has been returned, **`head._iter`** is **`None`**. The next call still finds the real acai node as **`head`**; it is the head's saved position—not the head itself—that is **`None`**. Python evaluates the right side of **`target = head._iter._object`** before assigning **`target`**. It reads **`head._iter`**, obtains **`None`**, and then attempts to read **`_object`** from **`None`**.

That attribute lookup fails. There is no new assignment to **`target`**, no execution of the next line's advancement, and no return from this call. The loop recognizes **`StopIteration`** as normal completion; it does not silently treat this **`AttributeError`** as the same thing.

**Observed output/error — final run at 49:08, also visible at 49:56 and 50:00:**

```text
acai
apple
banana
mango
peach
```

The traceback points from **`fruit.py`** line 10, **`for x in fruit:`**, to **`item.py`** line 132:

```text
target = head._iter._object
AttributeError: 'NoneType' object has no attribute '_object'
```

**Failure-state comparison — guide-derived:**

| Sixth request | Guard enabled | Guard disabled |
|---|---|---|
| Head node | Acai | Acai |
| Saved position | `None` | `None` |
| First decisive operation | Check `head._iter == None` | Evaluate `head._iter._object` |
| Result | Raise `StopIteration` | Raise `AttributeError` |
| Loop response | Finish normally | Propagate the error and stop this run |
| Sixth fruit printed | None | None |

The instructor restored the guard. At 51:00 both guard lines are active again, and the sports example's third manual **`next`** is commented out. Removing that extra sports request is a separate change: it avoids the sports iterator's normal exhaustion exception, whereas restoring the linked guard repairs the accidental attribute error.

**Key items to scan**

- **`head._iter == None`** — checks whether the saved position has passed peach.
- **`raise StopIteration`** — tells the loop that there are no more values.
- **`head` versus `head._iter`** — the head is still acai when the saved position becomes **`None`**.
- **`target = head._iter._object`** — fails while evaluating its right side on the unguarded sixth request.
- **`AttributeError`** — the actual accidental failure; **`None`** has no **`_object`** attribute.
- **`NoneType`** — the type of **`None`**, named in the error because the saved cursor—not the head node—is missing.
- **`#print(next(siter))`** — separately disables the third sports request after two valid sports have been returned.

## 15. A variable named `iter` hides Python's built-in function

*Sources: transcript 50:55–54:04; frames 51:28, 52:00, 52:45, 53:00, 54:00, and 55:00. C4 records the original failure and the later explanatory substitution separately.*

### Line 13 changes what line 14 means

The instructor reactivated **`iter = fruit.first()`**. That statement does not obtain an iterator by calling Python's built-in **`iter`**. It finds the acai **`Item`** instance and assigns that instance to a variable named **`iter`** in the driver module.

**Annotated teaching version — guide-added comments.** This is the failing revision, with the guard restored and the existing five-fruit chain still in place.

```python
iter = fruit.first()              # Bind the name iter to the acai Item INSTANCE.
pyIter = iter(fruit.first())      # Try to CALL that instance; this raises TypeError.
print(pyIter)                    # Not reached in this run.
```

The second line looks like the earlier working call, but its name now resolves differently. Python finds the module's **`iter`** variable before it falls back to the built-in function. That variable contains an **`Item`** object, and this class does not give its instances callable behavior.

**Guide-derived execution order for this failing run:**

| Step | Operation | Result / next state |
|---|---|---|
| 1 | Earlier `for` loop | Prints the five fruits and ends normally. |
| 2 | Evaluate `fruit.first()` on line 13 | Obtain the existing acai node. |
| 3 | Assign that result to bare `iter` | The module name now refers to that node. |
| 4 | Resolve `iter` on line 14, then evaluate its argument | Callable candidate and argument are both the acai node. |
| 5 | Attempt the call | Raise `TypeError`; no new `pyIter` value is assigned. |
| 6 | Later print and sports statements | Not reached in this run. |

**Observed output/error — latest run at 51:28:** The earlier **`for`** loop first prints acai, apple, banana, mango, and peach. Then execution stops at **`fruit.py`** line 14:

```text
pyIter = iter(fruit.first())
TypeError: 'Item' object is not callable
```

The instructor initially interpreted the capital **`I`** as a complaint that the class itself was not callable. The error instead names the **type of the instance** being called. **`Item("apple")`** is a class call that constructs an instance; attempting to call the instance returned by **`fruit.first()`** is a different operation. The successful constructor used earlier does not make every resulting object callable.

A student suggested replacing the hidden name with what it now meant. The instructor used that reasoning directly on screen:

**Annotated teaching version — explanatory substitution, guide-added comments.** The changed editor line is visible at 54:00–55:00; the visible traceback still belongs to the earlier **`iter(...)`** run, not a proven execution of this new spelling.

```python
pyIter = fruit.first()(fruit.first())
# The first fruit.first() produces the acai Item instance as the callable candidate.
# The second fruit.first() supplies an argument, also the acai node here.
# The outer call attempts to call the first Item instance: expected TypeError.
```

The substitution exposes the problem rather than repairing it. The general debugging method is to ask, “What object does this name refer to at this line?” A familiar function name does not guarantee that the program still uses the familiar function.

**Key items to scan**

- **`iter = fruit.first()`** — assigns an **`Item`** instance to a name that previously resolved to a built-in function.
- **Shadowing** — a nearer name binding hides another binding; here the module variable hides built-in **`iter`**.
- **`pyIter = iter(fruit.first())`** — now attempts to call the bound acai node and fails before assigning **`pyIter`**.
- **`TypeError: 'Item' object is not callable`** — identifies the instance's type; it does not say that constructing an **`Item`** is forbidden.
- **`Item("apple")`** — calls the class to create a new apple instance.
- **`fruit.first()(fruit.first())`** — explanatory spelling of the accidental instance call, not a successful correction.

### Name scope matters more than merely avoiding familiar words

The instructor connected this failure to the earlier concern about naming navigation operations **`next`**. He then qualified that concern: a method attached to a class is not the same as replacing a bare name in the driver module. Similarly, **`_next`** is an attribute accessed through an object, and the **`iter`** local variable visible inside **`forEach`** belongs to that method's local scope. That local name does not replace the driver module's built-in lookup in the way the module-level assignment did.

The selected source at 54:00 still displays the method names **`after`** and **`before`**; the instructor's recollection of reverting to **`next`** and **`prev`** is discussion, not evidence that this visible buffer already has those names. The historical appendix preserves the actual names on screen.

**Annotated teaching version — guide-added repair, not a demonstrated classroom run.** In a fresh run, replace the offending assignment and restore the original built-in call; do not append these lines after the broken assignment and assume the old binding disappears.

```python
current = fruit.first()       # Keep a reference to acai under a non-conflicting name.
pyIter = iter(current)        # Built-in iter is available; reset this custom traversal.
print(next(pyIter))           # Expected output: acai; the saved position moves to apple.
```

Expected output is **`acai`**, assuming the shown **`Item`** iterator methods and the five-fruit setup. The original recording does not show a completed cleanup run of this repair. Shadowing changes name lookup in the affected scope; it does not permanently overwrite Python's built-in implementation everywhere.

**Key items to scan**

- **Scope** — where a name binding is looked up; the module's **`iter`** assignment affects later bare **`iter`** uses there.
- **`forEach` local `iter`** — a traversal variable confined to that method call, not the driver's module binding.
- **`_next`** — an object attribute, not a replacement for Python's built-in **`next()`**.
- **`after` / `before`** — the actual navigation method names visible at 54:00.
- **`current = fruit.first()`** — a guide-added non-conflicting name for the same acai node.
- **Fresh run** — matters for the repair: renaming a new variable does not erase an already-existing **`iter`** binding in a continuing session.

## 16. Closing: several ways to count were proposed, not finished

*Sources: transcript 54:19–55:40; 55:00 editor frame and 55:52 closing frame.*

The instructor said the class would return to the count implementation next. Students proposed using **`iter`** and **`next`**, or using a **`for`** loop and incrementing a counter. The discussion also returned to the existing manual traversal pattern: start at the first node, follow next links, test for the end, and break when there is no successor.

All of these ideas separate the same two jobs: visit the stored items, and keep a running total. A counter would increase as items are visited and be returned afterward. A Python **`for`** loop is not unrelated magic; it uses the iteration mechanism just explored. Manual link-following explicitly performs the navigation that an iterator can hide.

These are closing proposals. No completed new **`count()`** implementation or successful new count run is shown in this closing interval. A student asked to see both approaches. The final capture returns to Discord and the earlier jury-service notice; it contains no additional count code. That notice is historical course logistics, not a new policy or a new programming demonstration.

**Proposed count trace — guide-organized reasoning, not an observed completed implementation.** A **counter** stores the running total; to **increment** it is to increase it, here by one for each stored item.

| Proposed step | Item visited | Counter afterward | Why |
|---|---|---|---|
| Initialize | None yet | 0 | No items counted before traversal. |
| First visit | acai | 1 | Include the first stored fruit. |
| Next visit | apple | 2 | Add one for apple. |
| Next visit | banana | 3 | Add one for banana. |
| Next visit | mango | 4 | Add one for mango. |
| Last visit | peach | 5 | Include peach before ending the walk. |
| Finish | No next item | Return 5 | Supply the completed total to the caller. |

For manual traversal, placing the end test before counting the final node could skip peach. The loop must count that final item and then stop without following a missing link. The empty-list case also needs an explicit design and test: a representing node with **`_object == None`** is not automatically one stored fruit. The earlier visible count prefix handles that distinction, but the closing discussion does not establish a complete new iterator-based solution for it.

**Key items to scan**

- **Counter** — running total, proposed to start at 0 and reach 5 for these five fruits.
- **Increment** — add one for the current stored fruit before advancing/stopping.
- **Empty-list boundary** — distinguish a node representing emptiness from an actual item to count.
- **`count()`** — the unfinished return topic named at closing, not a completed feature from this interval.
- **Explicit `iter` / `next` approach** — proposed way to request each item before counting it.
- **`for` plus increment** — proposed way to let Python request values while a counter records how many arrive.
- **Manual link traversal** — proposed alternative using the first node, next links, an end test, and **`break`**.
- **Return the counter** — supplies the total to the caller; returning is different from printing.


## Syntax to remember

This reference condenses operations already taught. Method names belong to the demonstrated version of the custom class; they are not promises about every class named **`Item`**.

| Pattern | Meaning in this lesson | Important distinction |
|---|---|---|
| `from item import Item` | Make the class named `Item` available from the module `item`. | The module and class are different objects, with different capitalization. |
| `fruit = Item("apple")` | Create a new custom object containing apple and make `fruit` refer to it. | Repeating the constructor starts a new object; it does not append to the old list. |
| `fruit.append("banana")` | Ask the existing custom list to add banana. | Mutation changes an existing structure. |
| `fruit = fruit.last()` | Find the last node, then rebind `fruit` to that node. | It does not create a new list or erase earlier nodes. |
| `fruit.count()` | Call the custom counting method and obtain its return value. | Calling a method and displaying its result are different actions. |
| `print(fruit.count())` | First obtain the count, then display it. | If the inner call raises an exception, the outer print does not receive a result. |
| `len(veg)` | Obtain the number of elements in the built-in vegetable list. | A custom method merely named `len` would not automatically supply Python’s length protocol. |
| `sport[0]` | Select soccer by its zero-based position. | Brackets perform indexing; parentheses perform a call. |
| `viter = iter(veg)` | Ask the iterable vegetable list for an iterator; save that object. | The iterator is not the string carrot. |
| `next(viter)` | Request one value and advance that iterator’s saved position. | After both vegetables, the next request raises `StopIteration`. |
| `for v in veg:` | Obtain an iterator and repeatedly request values for the loop body. | The loop handles normal exhaustion; it does not swallow arbitrary failures. |
| `print(f"v: {v}")` | Insert the current vegetable into the string after `v: `. | Braces inside an f-string evaluate the expression; they are not printed literally here. |
| `def __iter__(self):` | Define the method used to supply an iterator for this object. | The parameter `self` receives the object used for the method call. |
| `head._iter = head` | Store the first node as the next node to visit. | This writes an attribute on an existing head object. |
| `target = head._iter._object` | Save the current node’s stored fruit before advancing. | The saved value remains acai even after the cursor moves to apple. |
| `head._iter = head._iter._next` | Replace the saved position with the following node. | The fruit links remain in place; traversal state changes. |
| `if head._iter == None:` | Check whether the demonstrated traversal has run past the last node. | The class’s exact spelling is preserved; the guard prevents dereferencing `None`. |
| `raise StopIteration` | Signal that no next value exists. | Raising an exception is not returning an exception object. |
| `return target` | Give the saved fruit back to the caller. | It does not itself print the fruit. |
| `# ...` | Make the rest of that Python line a comment. | Removing a guard with comments can change behavior without changing the driver. |
| `iter = fruit.first()` | Bind the bare name `iter` to an `Item` instance. | Later `iter(...)` in that module no longer finds the built-in by that bare name. |

## Knowledge check

Try these without looking at the answers. Explain the cause, not just the final word.

1. Why do the count examples produce **0**, **1**, and **2** rather than continually growing one list across every constructor call? Which operation returns the number, and which displays it?
2. With **`veg = ["carrot", "broccoli"]`**, why does **`next(veg)`** fail while **`next(iter(veg))`** can return carrot?
3. Create one iterator over those vegetables and request three values. What happens on each request? Is a third value printed by **`print(next(viter))`**?
4. After consuming both values from one vegetable iterator, why can **`for v in veg:`** still visit carrot and broccoli? What changes if the loop instead uses that exhausted iterator?
5. During the custom fruit traversal, the saved position points to acai. What do **`target = head._iter._object`**, **`head._iter = head._iter._next`**, and **`return target`** accomplish, in that order?
6. Five fruits print successfully with the exhaustion guard removed. Why can the program nevertheless fail immediately afterward? Name the failing access and distinguish its exception from normal exhaustion.
7. Why can a class have a method named **`next`** without replacing the built-in **`next()`**, while the module assignment **`iter = fruit.first()`** breaks a later **`iter(fruit.first())`** call?
8. What does **`print(siter)`** display that **`print(next(siter))`** does not? Why should a guide not promise one fixed memory address?
9. What does Git’s refusal to check out a revision because of local changes protect? Why is restoring a file not a universally safe first response?
10. At the end of class, was a new count implementation completed? What approaches were discussed, and what remains unverified about formal challenge requirements?

### Answer key

1. Each **`Item(...)`** call creates a new object. The empty case has zero items; a new apple case has one; the apple-and-banana case has two after append. Rebinding **`fruit`** selects the new object rather than mutating the previous one. **`count()`** returns the integer; **`print()`** displays it.
2. The list is iterable: it can supply an iterator. It is not itself the advancing list iterator required by **`next()`**. **`iter(veg)`** obtains that iterator; the first request returns carrot. Obtaining an iterator alone does not return the first element.
3. The first request returns carrot, the second broccoli, and the third raises **`StopIteration`**. On the failing expression, **`next(viter)`** does not return a value to the outer **`print()`**, so there is no third vegetable line. An uncaught exception produces a traceback instead.
4. A built-in list supplies a new iterator for a new traversal. Consuming one iterator does not delete vegetables from the list. A loop over the same exhausted list iterator has no remaining values and ends without printing vegetables. Do not automatically transfer that independent-iterator behavior to the class’s shared-head implementation.
5. The first statement saves the string acai. The second changes the saved next position to the apple node. The third returns the already saved acai string. Saving before moving prevents the first value from being skipped; the caller can print acai while the next request is ready for apple.
6. The loop still requests another value after peach to discover whether more remain. The cursor is now **`None`**. Without the guard, **`head._iter._object`** tries to read **`_object`** from **`None`**, raising **`AttributeError`**. A correct exhaustion signal is **`StopIteration`**, which the loop handles. Five correct earlier lines do not prove correct termination.
7. **`fruit.next()`** looks up an attribute on an object; it does not bind the module’s bare name **`next`**. In contrast, assigning to **`iter`** changes what that bare name resolves to in the module. The later call attempts to call an **`Item` instance**, not the built-in function. The error naming the type does not mean that the **`Item` class constructor** is uncallable.
8. Printing the iterator shows its object representation, including its type and an address-like identity in this run. Calling **`next()`** requests the next contained value, such as soccer. Runtime representations can vary between executions; an illustrative address is not a reproducible fixed result.
9. Git avoids overwriting local tracked-file edits while changing revisions. Restoring a file can discard changes that the learner intended to keep. Inspect the diff and preserve wanted work before deliberately reproducing the instructor’s restore step. A typed command or a rejected checkout is not evidence that the revision changed.
10. No new completed count body is demonstrated at the close. The class proposes visiting the nodes and incrementing a counter, either with Python iteration or a manual linked walk, then returning the total. The oral discussion of challenges does not supply a complete formal assignment specification or justify inventing exact grader contracts.

## Library of definitions

Definitions here are anchored in the lesson. The official references below clarify Python and Git behavior where informal classroom wording could mislead.

| Term | Concrete definition and lesson connection |
|---|---|
| Object | A runtime entity, such as the custom apple node, the string `"apple"`, or a vegetable list. Distinct objects can have different roles even when one refers to another. |
| Class | The definition of a kind of object. `Item` supplies the operations available on the custom nodes. Calling the class can create an instance. |
| Instance | A particular object made from a class. The `Item` object returned by `fruit.first()` is an instance, not the `Item` class itself. |
| Constructor call | An expression such as `Item("apple")` that creates and initializes a new instance. It is not the same action as appending to an existing list. |
| Reference / binding | A way for a name or attribute to designate an object. After `fruit = fruit.last()`, the name refers to the last existing node. |
| Rebinding | Changing which object a name designates. Reusing `fruit` for a new `Item` leaves the earlier object distinct. |
| Mutation | Changing an existing object’s state, such as adding banana or updating `head._iter`. Mutation does not necessarily create a replacement collection. |
| Argument | A value supplied in a call. `"banana"` is the argument in `fruit.append("banana")`; `veg` is the argument in `iter(veg)`. |
| Parameter | A name in a function or method definition that receives an argument. `self` refers to the object used for an instance-method call. |
| Function | A callable operation such as the built-in `iter` or `len`. A bare function name is resolved through Python’s naming rules. |
| Method | An operation associated with a class/object, such as `fruit.count()` or `fruit.first()`. The object before the dot supplies the context for the call. |
| Attribute | A named part of an object, such as a node’s `_object`, `_next`, or `_iter`. An attribute may hold data or refer to a method. |
| Driver / client code | The separate program that imports and exercises an implementation, such as the fruit driver calling the `Item` class. |
| Interface / API | The operations and names exposed to callers. Changing `next` to `after` changes the interface used by existing drivers. |
| Implementation | The internal storage and statements that carry out an operation; linked nodes can provide an interface resembling a built-in list. |
| Class attribute | A value stored on the class, such as the visible `Item._object = None` default. An instance may initially read that default without owning a separate value yet. |
| Instance attribute | A value set on one object, such as apple’s `_object` after its constructor runs. It does not replace the class default for all other nodes. |
| Accessor | A method for obtaining stored information. `object()` returns the fruit value; `after()` returns a node reference. |
| F-string | A formatted string literal that inserts expressions from braces, as `f"v: {v}"` displays `v: carrot` on the first vegetable pass. |
| Counter / increment | A counter stores a running total; incrementing adds to it. The proposed fruit count begins at 0 and adds one for each of the five fruits. |
| Length hook | The special `__len__` method used by `len(object)`. It is distinct from an ordinary method merely named `len`. |
| Occurrence count | The number of matching values: built-in `veg.count('carrot')` is 1, while the total `len(veg)` is 2. |
| Detached HEAD | Git is examining a specific commit rather than following a branch name. The lesson uses that state to inspect historical implementations. |
| `NoneType` | The type of the `None` value. Its name in the attribute error identifies the missing saved cursor, not a missing head node. |
| Module | A unit of Python code with its own namespace, such as `item.py` or the fruit driver. Importing a class makes that class name available; it does not copy every method into the caller’s bare-name namespace. |
| Scope / namespace | Scope describes where a name is visible; a namespace associates names with objects. The module-level `iter` assignment and object-qualified `fruit.next` use different lookup routes. |
| Shadowing | A nearer binding hides a name that would otherwise be found. The module’s `iter` variable hides access to the built-in through the same bare spelling. |
| Callable | An object that can be used with parentheses to perform a call. A class constructor may be callable while its ordinary instances are not. |
| Collection / container | An object grouping values, such as the built-in vegetable list or the custom fruit structure. Holding several values does not by itself implement an iteration protocol. |
| Built-in list | Python’s own list type, used for carrot/broccoli and soccer/boxing. It provides indexing, length, and iterator creation. |
| Doubly linked list | A structure whose nodes link forward and backward. The lesson’s driver can move to the last node and still recover the first through methods. |
| Node | One structural object in the linked list. A node contains a fruit value and links; it is not identical to the fruit string it contains. |
| Payload / stored value | The value held in a node’s `_object`, such as acai. The iterator returns this value rather than printing the node’s object representation. |
| Head | The first node in the list. In the demonstrated implementation, it also stores the shared traversal position in `_iter`. |
| Cursor / traversal state | The remembered location of the next node to visit. Moving `_iter` from acai to apple advances the walk without deleting acai. |
| Iteration | Visiting successive values. The class connects the general idea of repeated versions or steps to a programming walk with a beginning, advancement, and an end. |
| Iterable | An object from which Python can obtain an iterator. The vegetable list is iterable even though `next(veg)` fails. |
| Iterator | An object that supplies successive values through `next()` and remembers progression. A built-in list iterator is separate from its list; the custom implementation returns the head node. |
| Iterator protocol | The expected operations that let Python request an iterator and then values, with `StopIteration` marking exhaustion. Containers and returned iterators need not be the same object. |
| Dunder method | A special method with two leading and trailing underscores, such as `__iter__` or `__next__`. Python connects these names to specific operations. |
| Index | A numeric position used with brackets. `sport[0]` selects soccer; `sport[1]` selects boxing. Iteration asks for successive values without spelling out those positions. |
| Return value | The result handed back by a call, such as `2` from counting or acai from `next()`. A caller can use that result without printing it. |
| Output / display | Text made visible by an operation such as `print()`. Seeing an object representation is not the same as receiving its next stored value. |
| Exception | A raised signal that changes normal control flow. It is not an ordinary value merely returned to the caller. |
| Exhaustion | The state in which the iterator has no more values. The program must still make a request to discover that the final value has already been consumed. |
| `StopIteration` | Python’s normal no-more-values signal for iteration. A `for` loop handles it; an uncaught manual `next()` exposes a traceback. |
| `TypeError` | An operation receives an unsuitable kind of object: for example, a list passed directly to `next`, or a noncallable `Item` instance used as a function. |
| `NameError` | A bare name cannot be found. The presence of a method named `after` does not define a bare `after()` function in the driver. |
| `AttributeError` | A requested attribute is absent. After the traversal runs out, `None` has no `_object` attribute. |
| Guard | A condition that prevents an invalid operation. The end guard checks `_iter` before reading a fruit from it. |
| Callback | An operation supplied for another routine to invoke. The visible `forEach` method calls `caller.withItem(...)` for each node’s stored value. |
| Traceback | The displayed call path for an uncaught exception. Read the failing expression and exception together rather than assuming every nearby output belongs to the current editor. |
| Commit / revision | A recorded Git project state. Switching revisions may change both the class and its driver, not just one method. |
| `HEAD` | Git’s reference to the currently checked-out state. This Git term is unrelated to the linked list’s head node. |
| Working tree | The checked-out files being edited. It can differ from the selected commit, and an editor’s unsaved buffer can differ again from the disk file. |
| Edge case | A boundary or unusual situation worth testing. The instructor’s suggestion of an unexamined case is not proof of a specific bug or a new assignment requirement. |

### Supplemental reference notes

These are technical checks, not extra classroom events.

- [Python: iteration-related types](https://docs.python.org/3/builtins/stdtypes.html#iteration-related-types) distinguishes a container’s iterator-producing method from the returned iterator’s methods. It also specifies that an iterator’s **`__iter__()`** returns that iterator itself, and that exhaustion persists. The classroom design’s resetting/shared head state therefore deserves separate testing; do not assume independent traversals.
- [Python: **`iter`**](https://docs.python.org/3/builtins/functions.html#iter) describes obtaining an iterator. Python also supports a sequence-protocol fallback, so the classroom shorthand “without these two methods nothing is iterable” should be read as an explanation of this demonstrated class, not a universal exclusion of every other protocol.
- [Python: **`next`**](https://docs.python.org/3/builtins/functions.html#next) specifies the call to **`__next__`** and the exhaustion signal. The lesson uses the one-argument form.
- [Python: name resolution](https://docs.python.org/3/reference/executionmodel.html#resolution-of-names) explains why a nearer/module binding can hide a built-in, while attribute names use an object-qualified lookup.
- [Git: **`restore`**](https://git-scm.com/docs/git-restore) explains that a plain restore of a working-tree path normally restores from the index. That can discard working-file edits; it is not a harmless refresh command.


## Historical screen-code appendix

This appendix preserves source chronology, including incomplete and erroneous editor states. Gutter numbers are not executable Python. Historical outputs and expected behaviors remain separately labeled.

### A1. Opening progress scratchpad: historical notes, not executable code

Observed editor notes in 00-01-00-minute.png through 00-10-00-minute.png. The gutter numbers below reproduce the editor's line labels; they are not part of the notes and are never Python source to type. This anonymous snapshot supported the instructor's discussion of progress, withdrawal, and remaining challenges. The letters are historical rough progress labels, not a reconstructed grading formula; blank grade positions remain blank.

```text
 1 | Grand Jury
 2 | Withdrawal
 3 | Progress
 4 |
 5 | 16 3030|1
 6 | 11 2080|1
 7 | 10 2070|1  A
 8 |  9 2060|1  B
 9 |  8 2058|1  C
10 |  7 2056|1  D
11 |  6 2054|2  F
12 |  5 2050|5  F
13 |  4 2022|2  F
14 |  3 2020|1  F
15 |  2 2005|2  F
16 |  1 2000|2  F
17 |        ---
18 |        18
19 |
20 |  17 - 2 registered
21 |
22 | 3 code challenges per week
23 | 6 weeks left --> 18 more challenges
```

The visible total and enrollment note refer to different populations. The spoken explanation identifies instructor/nonregistered participation in the database and two withdrawals from the official roster. The notes do not establish individual student identities, an exact withdrawal date, or a current policy.

### A2. The iterator-era fruit driver and its actual run

Historical observed driver: **`test\fruit.py`**, visible in 00-01-00-minute.png through 00-14-00-minute.png. Complete meaningful driver lines are available. The method implementation and terminal state at 11:00–14:00 connect this driver to the iterator example; its presence earlier during logistics does not mean it was run during those announcements.

Gutter numbers are editor labels, not source code to enter.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit.append("banana")
 5 | fruit.append("peach")
 6 | fruit = fruit.last()
 7 | fruit.insertBefore("mango")
 8 | fruit.prepend("acai")
 9 |
10 | for x in fruit:
11 |     print( x )
12 |
```

Lines 3–8 create and modify the chain. Line 6 moves the driver's reference to the last node, while the later iterator initialization finds the first node for traversal. Lines 10–11 are the short loop whose five printed values are supported by the actual terminal run.

Observed terminal excerpt, 00-14-00-minute.png; original prompt and identifier preserved:

```text
PS C:\com\joesta\Item> git checkout HEAD~11
Note: switching to 'HEAD~11'.

HEAD is now at ec4fdd0 Item: python Iterator
PS C:\com\joesta\Item> py .\test\fruit.py
acai
apple
banana
mango
peach
PS C:\com\joesta\Item>
```

The long detached-HEAD advisory between the switch notice and the final HEAD line is omitted here, not implied absent. It offered a new-branch command and a switch-back command; neither suggestion is evidence of execution.

### A3. Callback traversal and the first visible iterator implementation

Historical observed **`src\item.py`** methods, lines 114–133, visible in 00-11-00-minute.png, 00-12-00-minute.png, and 00-13-00-minute.png. The methods are fully legible in the native-resolution editor regions. This is a class-body excerpt, not a complete class file; other methods, including the implementation of **`first()`**, are outside this excerpt.

```text
114 |     def forEach(self, caller):
115 |         iter = self.first( )
116 |         while True:
117 |             caller.withItem(iter._object)
118 |             if iter._next == None:
119 |                 break
120 |             iter = iter._next
121 |
122 |     def __iter__(self):
123 |         head = self.first( )
124 |         head._iter = head
125 |         return head._iter
126 |
127 |     def __next__(self):
128 |         head = self.first( )
129 |         if head._iter == None:
130 |             raise StopIteration
131 |         target = head._iter._object
132 |         head._iter = head._iter._next
133 |         return target
134 |
```

Lines 114–120 invoke a callback before checking whether the current node is the last. Lines 122–125 reset the traversal position on the first node; lines 127–133 either return the current stored value after advancing that position, or raise exhaustion. The first node itself is returned as the iterator object; this excerpt does not construct a separate iterator class.

The 13:00 selection highlights the two special methods but does not change their visible text. The instructor's possible-edge-case remark is a tentative invitation to investigate, not an observed failure tied to a particular line. No callback implementation or callback output is visible in this interval.

The end of an extraction method is also visible above these methods. Its reliable closing lines are:

```text
109 |                 rest = self._prev
110 |         self._prev = None
111 |         self._next = None
112 |         return rest
113 |
```

The preceding long link-repair lines run out of the visible pane. The later count-revision extraction excerpt in A7 is kept separate rather than silently used to certify unseen text in this earlier revision.

### A4. Git navigation, the older append driver, and misleading scrollback

At 15:00 the screen shows a completed relative checkout and, below it, a second command still sitting at the prompt. A typed prompt line alone is not proof that the command ran again.

Observed terminal excerpt, 00-15-00-minute.png:

```text
PS C:\com\joesta\Item> git checkout HEAD~10
Previous HEAD position was ec4fdd0 Item: python Iterator
HEAD is now at fc6c333 Item: addition of append( ).
PS C:\com\joesta\Item> git checkout HEAD~10
```

The preceding five-fruit output remains in terminal scrollback. It belongs to the run at **`ec4fdd0`**, not to the older driver now visible. The spoken account says the instructor went back too far and tried a branch-relative expression; the sampled frames do not preserve every intervening command or error. Do not invent an exact failed command or traceback from that speech.

Historical observed **`test\fruit.py`** at 15:00–17:00, with the older driver selected in the editor:

```text
 1 | from item import Item
 2 |
 3 | fruit = Item("apple")
 4 | fruit = fruit.append("banana")
 5 | fruit = fruit.append("peach")
 6 |
 7 | iter = fruit.prev.prev
 8 | print( iter.object )
 9 | iter = iter.next
10 | print( iter.object )
11 | iter = iter.next
12 | print( iter.object )
13 |
14 | iter = iter.prev
15 | print( iter.object )
16 | iter = iter.prev
17 | print( iter.object )
18 |
```

The older driver rebinds the variable after appending, then traverses public links directly. It predicts apple, banana, peach, banana, apple under that older design, but no matching run of this driver is visible in the assigned interval. That predicted sequence must not be presented as the observed five-fruit run.

The top of the left class pane also shows this older spelling, with the rest of the class obscured by the terminal:

```text
 1 | class Item:
 2 |     object = None
 3 |     next = None
 4 |     prev = None
```

At 16:00 and 17:00, the terminal log already labels the current branch **`master`**, but the editor still displays the selected old driver. This demonstrates why an editor buffer and terminal history cannot automatically be treated as one synchronized code state. The prompt also contains **`git checkout master`** without a following result in those sampled views; distinguish that draft from the log's independently visible branch label.

Historical one-line log output visible at 16:00–17:00 follows. Wrapped terminal rows have been joined only where the continuation is fully visible; these are commit messages, not evidence that the corresponding features were demonstrated during this interval.

```text
1207cd3 (HEAD -> master) get/set object value at index
f23376c Construct Item from list or file.
dae3d01 toFile, fromFile
2a9805c before/after back to prev/next
843e915 generator
52fc8cc fromList( list )
7160e4c toList( )
761d45c clear( ) to empty
0ec6bc1 has(object)
91be848 at(index)
3907fbe count
ec4fdd0 Item: python Iterator
ed154df Item: forEach( ), withItem( ) callback
a5c546d Item: extract( ). Cannot extract last value, but can check return for empty
8887c71 Item: empty list edge case
ab909a4 Item: first( ), prepend( ), insertBefore( )
7630f8b Item: insertAfter( ).
e01cc6d Item: avoid returning None.
7653923 Item: private attributes with accessor methods.
6cdb33d Item: call last( ) from append( ).
4847342 Item: addition of last( ).
fc6c333 Item: addition of append( ).
2499ca0 Item: addition of constructor.
0203781 Fruit: use of prev attribute.
f70c1ad Fruit: use of second reference to fruit.
47413b4 Item: with object, next, and prev attributes. Fruit: direct assignments to object, and next attributes.
```

The transition finally becomes unambiguous at 19:00:

```text
PS C:\com\joesta\Item> git checkout 3907fbe
Note: switching to '3907fbe'.

HEAD is now at 3907fbe count
PS C:\com\joesta\Item> py .\test\fruit.py
0
1
2
PS C:\com\joesta\Item>
```

Again, the advisory text between the switch notice and the HEAD result is omitted for readability. The **`count`** commit's identifier is **`3907fbe`**; the earlier iterator revision's identifier is **`ec4fdd0`**. These literals come from the screen, not the automatic captions.

### A5. Count driver: the repeated constructors are real source lines

Historical observed **`test\fruit.py`**, visible at 18:00, 18:45, and 19:00. The count example's meaningful source is complete. Its repeated apple construction must not be collapsed into a single list that is continuously accumulating items.

```text
 1 | from item import Item
 2 |
 3 | fruit = Item( )
 4 | print( fruit.count( ) )
 5 |
 6 | fruit = Item( "apple" )
 7 | print( fruit.count( ) )
 8 |
 9 | fruit = Item( "apple" )
10 | fruit.append("banana")
11 | print( fruit.count( ) )
12 |
```

Lines 3–4 exercise an empty object; lines 6–7 exercise a fresh one-item object. Line 9 constructs yet another object before banana is appended and the third count is printed. At 18:45 the three output lines are visible in the terminal strip; at 19:00 the full invocation and output are legible together:

```text
0
1
2
```

The left editor is showing insertion/extraction code during this demonstration, not the implementation of the count method. The count body is therefore unavailable in these frames. The driver and results establish behavior, not a particular counting algorithm.

### A6. The vegetable-length addition: intermediate edit versus completed expression

The driver is extended rather than replaced. At 20:00 the new lines are:

```text
13 | veg = ['carrot', 'broccoli']
14 | print()
```

That empty print call is a visible intermediate editing state, not an observed runtime error and not the completed length example. At 21:00 the same new region reads:

```text
13 | veg = ['carrot', 'broccoli']
14 | print(len(veg))
```

The completed expression is legible in the full-frame view, while a native-resolution region confirms the terminal output below. The preceding three count tests remain in the driver, so the final terminal group is:

```text
0
1
2
2
```

The first three results belong to the earlier count tests and the last result belongs to the vegetable list. The exact invocation is obscured by the foreground editor in the 21:00 frame, so the visible output is not accompanied here by an invented newly transcribed command. The transcript at about 20:10 independently reports the length result as two.

### A7. Additional visible linked-structure code at the count revision

The left editor contains substantial code that is not the count body. This appendix preserves it because it exposes how the existing structure manipulates links, while keeping it separate from the actively discussed count interface. It does not imply a fresh demonstration or execution of these methods during the count run.

Assembled visual reconstruction of visible regions in **`src\item.py`**: 00-18-45-count-run.png and 00-20-00-minute.png supply lines 67–96; 00-18-00-minute.png supplies lines 94–116 and the method ending. These views occur around the displayed **`3907fbe`** count revision and have compatible overlapping text. They do not prove a complete saved file or supply hidden right-edge characters. The marker **`[remainder not visible]`** begins at the point where transcription stops; no missing suffix is silently supplied.

```text
 67 |             newItem = Item(obj)
 68 |             if self._next != None:
 69 |                 newItem._prev = self
 70 |                 newItem._next = self._next
 71 |                 self._next._prev = newIte[remainder not visible]
 72 |                 self._next = newItem
 73 |             else:
 74 |                 newItem._prev = self
 75 |                 self._next = newItem
 76 |         return newItem
 77 |
 78 |     def insertBefore(self, obj):
 79 |         if self._object == None:
 80 |             self._object = obj
 81 |             newItem = self
 82 |         else:
 83 |             newItem = Item(obj)
 84 |             if self._prev != None:
 85 |                 newItem._next = self
 86 |                 newItem._prev = self._pre[remainder not visible]
 87 |                 self._prev._next = newIte[remainder not visible]
 88 |                 self._prev = newItem
 89 |             else:
 90 |                 newItem._next = self
 91 |                 self._prev = newItem
 92 |         return newItem
 93 |
 94 |     def extract(self):
 95 |         rest = None
 96 |         if self._prev == None and self._n[remainder not visible]
 97 |             rest = Item( )
 98 |         else:
 99 |             if self._prev == None:
100 |                 self._next._prev = None
101 |                 rest = self._next
102 |             else:
103 |                 if self._next == None:
104 |                     self._prev._next = No[remainder not visible]
105 |                     rest = self._prev
106 |                 else:
107 |                     self._prev._next = se[remainder not visible]
108 |                     self._next._prev = se[remainder not visible]
109 |                     rest = self._prev
110 |         self._prev = None
111 |         self._next = None
112 |         return rest
113 |
114 |     def forEach(self, caller):
115 |         iter = self.first( )
116 |         while True:
```

The method header above line 67 is off-screen, so the fragment is not given an invented signature. Lines 78–92 show two different insertion situations: reuse an empty object's value slot, or construct a new node and connect it before the current node. Because some right-hand sides are cropped, the listing is a historical fragment, not a ready-to-run implementation.

Lines 94–112 show extraction cases, a result reference named **`rest`**, and the clearing of the extracted object's two neighbor references before returning that result. Some conditions and link repairs are clipped, so this record cannot independently establish the full extraction algorithm. Lines 114–116 show the beginning of callback traversal in this revision; the complete earlier callback method is preserved in A3 without asserting that unseen later lines are identical.

The repeated 21:00 left-editor view confirms portions of lines 79–108 but does not recover the clipped right-edge suffixes. Any corrected or runnable implementation would be a separately labeled reconstruction, not the historical source shown above.

### B1. Navigation methods and the beginning of `append` in `src/item.py`

**Historical/observed partial file.** Sources: **`00-22-00-minute.png`**, **`00-23-00-minute.png`**, **`00-25-00-minute.png`**, **`00-28-00-minute.png`**, and **`00-29-00-minute.png`**, in the supplied **`KLTLt8TXzY8-20261123-054944-direct-1080p`** folder. These frames show the same readable lines 1–30. The method continues below the viewport; this is not a complete **`Item`** implementation.

**Gutter numbers in all appendix listings are reference labels, not Python source to type.** Indentation is transcribed as visible logical indentation; editor display does not establish whether the underlying file used tabs or spaces.

```text
  1 class Item:
  2     _object = None
  3     _next = None
  4     _prev = None
  5     _iter = None
  6
  7     def __init__(self, obj = None):
  8         self._object = obj
  9
 10     def object(self):
 11         return self._object
 12
 13     def after(self):
 14         if self._next != None:
 15             return self._next
 16         return self
 17
 18     def before(self):
 19         if self._prev != None:
 20             return self._prev
 21         return self
 22
 23     def append(self, obj):
 24         if self._object == None:
 25             self._object = obj
 26             newItem = self
 27         else:
 28             last = self.last()
 29
 30             newItem = Item(obj)
```

**Unavailable:** lines below 30 in this viewport. No link updates or return statement are invented to complete **`append`**.

Lines 2–5 name the class-level initial values visible here; line 8 assigns an instance’s stored object during construction. Lines 13–21 implement neighbor access with a self-return fallback, so reaching an end does not return **`None`** from these public navigation methods. The visible **`append`** prefix either fills an empty node or finds the last node and constructs a new one; its remaining linking behavior is off-screen.

### B2. The count/vegetable driver and its scope/iterator revisions

**Historical/observed, longest driver state in this range.** File: **`test/fruit.py`**. Source: **`00-32-00-minute.png`** (same driver visible at 33:00). This revision belongs to the **`count`** checkout, not the later five-fruit checkout.

```text
  1 from item import Item
  2
  3 fruit = Item( )
  4 print( fruit.count( ) )
  5
  6 fruit = Item( "apple" )
  7 print( fruit.count( ) )
  8
  9 fruit = Item( "apple" )
 10 fruit.append("banana")
 11 print( fruit.count( ) )
 12
 13 veg = ['carrot', 'broccoli']
 14 print(len(veg))
 15
 16 viter = iter(veg)
 17 print(next(viter))
 18 print(next(viter))
 19 #print(next(viter))
 20
 21 for v in veg:
 22     print(f"v: {v}")
 23
 24 print("goes on")
```

Lines 3, 6, and 9 construct separate custom lists; line 10 appends to the third one. Lines 13–18 create the Python list and consume its iterator. The third manual request is disabled at line 19, while lines 21–22 start a fresh traversal of the list itself. Line 24 is outside the loop and demonstrates continued execution.

Observed complete output for the saved final state, visible at 32:00:

```text
0
1
2
2
carrot
broccoli
v: carrot
v: broccoli
goes on
```

**Earlier states must remain separate:**

- **22:00 and 23:00:** lines 1–14 only; no **`viter`** or **`for`** yet.
- **24:00:** lines 1–14, blank line 15, then the following active line:

```text
 16 after()
```

  The terminal run fails with **`NameError: name 'after' is not defined. Did you mean: 'aiter'?`** after printing **`0`**, **`1`**, **`2`**, **`2`**. The qualified repair is reported in the transcript; the supplied minute frame does not display its complete call/run pair.

- **25:00, unsaved buffer:** the scope-call line is no longer visible and line 14 is an incomplete edit. The readable source is exactly:

```text
 13 veg = ['carrot', 'broccoli']
 14 print(len(veg)
```

  This is a missing closing parenthesis in an unsaved editor state, not right-edge clipping, and not the code proven by the preceding successful terminal output.

- **26:00 and 26:08:** the complete **`print(len(veg))`** line is restored; line 16 is now:

```text
 16 print(next(veg))
```

  Observed failure: **`TypeError: 'list' object is not an iterator`** at line 16.

- **27:00 and 28:00:** corrected end of driver:

```text
 16 viter = iter(veg)
 17 print(next(viter))
```

  Terminal scrollback at 27:00 records an intermediate failed line-17 call **`print(next(veg))`**, followed by a successful run ending in **`carrot`**. Creating the iterator and changing the receiver of **`next`** are distinct edits.

- **29:00, unsaved buffer:** lines 17–19 are all active requests:

```text
 16 viter = iter(veg)
 17 print(next(viter))
 18 print(next(viter))
 19 print(next(viter))
```

  Its latest displayed completed run prints carrot and broccoli without the third-call traceback. Do not assign that completed run to the unsaved third call.

- **30:00, unsaved buffer:** the three active calls remain and lines 21–22 add the loop from the final listing. Existing terminal scrollback now shows **`StopIteration`** from the third request. A correct later loop cannot be reached until this earlier failure is removed.
- **30:50:** line 19 is commented; lines 21–22 are active; no line 24 print exists yet. The saved run ends in **`v: carrot`** and **`v: broccoli`**.
- **31:00:** **`print("goes on")`** has been added at line 24, but the tab indicates an unsaved buffer and the most recent run still ends at **`v: broccoli`**. The 32:00 frame independently establishes the later saved run including **`goes on`**.

The exact executed command visible repeatedly is:

```powershell
py .\test\fruit.py
```

The terminal’s **`C:\com\joesta\Item`** working directory is the instructor’s environment, not the source workspace used for this guide.

### B3. Callback traversal, iterator protocol, and the visible `count` prefix

**Historical/observed partial file.** File: **`src/item.py`**, source **`00-32-00-minute.png`**. The viewport supplies lines 114–143, including code shown but not explained line by line at that moment. The gap between this listing and B1 is not reconstructed.

```text
114     def forEach(self, caller):
115         iter = self.first( )
116         while True:
117             caller.withItem(iter._object)
118             if iter._next == None:
119                 break
120             iter = iter._next
121
122     def __iter__(self):
123         head = self.first( )
124         head._iter = head
125         return head._iter
126
127     def __next__(self):
128         head = self.first( )
129         if head._iter == None:
130             raise StopIteration
131         target = head._iter._object
132         head._iter = head._iter._next
133         return target
134
135     def count(self):
136         iter = self.first( )
137         if iter._object == None:
138             count = 0
139         else:
140             count = 1
141         while True:
142             if iter._next == None:
143                 break
```

**Unavailable:** lines after 143, including whatever follows this loop prefix; no unseen increment or final return is supplied as historical text.

The callback traversal gives each stored value to **`caller.withItem`**, tests whether a next link exists, and either stops or advances. The iterator methods instead remember position across separate requests and use **`StopIteration`** as their stopping signal. The visible count prefix starts at the first node, initializes zero for an empty stored value or one for a nonempty first node, and begins its end-checking loop; the source-supported historical listing stops there.

### B4. Git command history: blocked checkout, restoration, successful checkout

**Historical/observed terminal sequence.** Sources: **`00-33-00-minute.png`** and **`00-34-47-not-iterable.png`**. The first supplies the branch attempt and log; the second independently supplies the complete restoration/retry commands and result that are partly covered by the editor at 34:00. These are recovered from another original frame, not completed from obscured text.

```text
PS C:\com\joesta\Item> git checkout master
error: Your local changes to the following files would be overwritten b
y checkout:
        test/fruit.py
Please commit your changes or stash them before you switch branches.
Aborting
PS C:\com\joesta\Item> git log --oneline
3907fbe (HEAD) count
ec4fdd0 Item: python Iterator
```

**Terminal-wrap note:** the warning wraps after **`b`**; the next visual row begins **`y checkout:`** (without a substantive leading space). The display above separates the wrapped rows for readability; it is not a different error message. Only the two log entries needed to identify the revision switch are reproduced; the visible log also contains earlier linked-list milestones, not additional checkouts during this interval.

```text
PS C:\com\joesta\Item> git checkout ec4fdd0
error: Your local changes to the following files would be overwritten b
y checkout:
        test/fruit.py
Please commit your changes or stash them before you switch branches.
Aborting
PS C:\com\joesta\Item> git restore .\test\fruit.py
PS C:\com\joesta\Item> git checkout ec4fdd0
Previous HEAD position was 3907fbe count
HEAD is now at ec4fdd0 Item: python Iterator
```

The first two checkout attempts abort; they do not establish a new editor revision. After restoring the modified driver, the retry succeeds and the editor displays the older fruit example. The successful-checkout message must not be interpreted as a successful run of Python; those are different operations.

### B5. Iterator revision before, during, and after the disabling experiment

**Historical/observed source.** File: **`src/item.py`**. The 34:00 frame shows the active methods at the earlier iterator checkout with lines 114–133, matching B3 through line 133. Unlike the **`count`** revision, no **`count`** method is visible below them.

At 34:47, the foreground terminal covers the body of the protocol methods and records the failed Python run. The transcript reports that the methods were commented out. It would be unjustified to transcribe an unseen opening triple quote into that event frame as an observed character.

**Distinct unsaved intermediate edit at 35:00:** the opening delimiter has already been removed from line 122, while a trailing triple quote remains at line 135. This is the longest legible affected region in that frame:

```text
122
123     def __iter__(self):
124         head = self.first( )
125         head._iter = head
126         return head._iter
127
128     def __next__(self):
129         head = self.first( )
130         if head._iter == None:
131             raise StopIteration
132         target = head._iter._object
133         head._iter = head._iter._next
134         return target
135     """
```

The tab has an unsaved-change dot. The earlier **`TypeError: 'Item' object is not iterable`** in terminal scrollback belongs to the prior disabled-method run, not this incomplete delimiter edit. This buffer must not be silently “fixed” and then presented as the historical state.

**Active restored state at 36:00, independently rechecked at 37:00 and 38:00:**

```text
114     def forEach(self, caller):
115         iter = self.first( )
116         while True:
117             caller.withItem(iter._object)
118             if iter._next == None:
119                 break
120             iter = iter._next
121
122
123     def __iter__(self):
124         head = self.first( )
125         head._iter = head
126         return head._iter
127
128     def __next__(self):
129         head = self.first( )
130         if head._iter == None:
131             raise StopIteration
132         target = head._iter._object
133         head._iter = head._iter._next
134         return target
135
```

The restored source retains an extra blank line compared with the initial 34:00 checkout, shifting the special methods down one line. The restored methods reset a head-held cursor, preserve the current string before advancing, and raise only after the cursor reaches **`None`**. The successful five-fruit output is visible by 36:00.

### B6. Five-fruit driver: loop, iterator object, and first returned value

**Historical/observed complete visible driver.** File: **`test/fruit.py`**; sources **`00-38-55-custom-iterator-output.png`** and **`00-39-00-minute.png`**. This is the iterator-checkout driver with subsequent classroom edits, not the count/vegetable driver from B2.

```text
  1 from item import Item
  2
  3 fruit = Item("apple")
  4 fruit.append("banana")
  5 fruit.append("peach")
  6 fruit = fruit.last()
  7 fruit.insertBefore("mango")
  8 fruit.prepend("acai")
  9
 10 for x in fruit:
 11     print( x )
 12
 13 #iter = fruit.first()
 14 pyIter = iter(fruit.first())
 15 print(pyIter)
 16 print(next(pyIter))
```

Lines 3–8 build and modify one linked chain, leaving **`fruit`** at the node reached through **`last()`** rather than constructing a new list at line 6. The loop prints the whole chain in traversal order. Line 14 starts the custom traversal again; line 15 displays the iterator object, while line 16 requests and prints its first stored value.

**Distinct earlier states:**

- **34:00 and 34:47:** lines 1–11 only. At 34:47, disabled iterator support causes the line-10 failure.
- **36:00:** new unsaved line **`13 iter = fruit.first()`**; there is no line 14 iterator call yet. The completed run in scrollback shows the five-fruit loop, not execution of that newly typed line.
- **37:00:** line 13 is now **`#iter = fruit.first()`** and line 14 is **`pyIter = iter(fruit.first())`**; the tab still shows unsaved edits.
- **38:00:** the saved driver adds line 15 **`print(pyIter)`**. Its completed run ends in an object representation, not a fruit string from an explicit next call.
- **38:55 and 39:00:** line 16 has been added and executed. No second explicit **`next`** call appears in either driver frame.

Observed run after line 15 was added, recovered completely from the later 38:55 terminal scrollback:

```text
PS C:\com\joesta\Item> py .\test\fruit.py
acai
apple
banana
mango
peach
<item.Item object at 0x0000017DC87FE060>
```

Observed later run after line 16 was added:

```text
PS C:\com\joesta\Item> py .\test\fruit.py
acai
apple
banana
mango
peach
<item.Item object at 0x000002B8B99EE060>
acai
```

These addresses are legible historical output and differ between runs; neither is a fixed expected address for a learner’s execution. The five-fruit prefix comes from the loop. The trailing acai is the first explicit request after reinitializing the custom iterator.


### C1. `item.py`: the callback traversal and custom iterator methods

**Historical screen transcription.** Sources: **`00-40-00-minute.png`**, **`00-48-00-minute.png`**, **`00-51-00-minute.png`**, and **`00-52-00-minute.png`** in the original **`KLTLt8TXzY8-20261123-054944-direct-1080p`** capture folder. The same legible segment appears in these frames with the exhaustion guard enabled. **Numbers are editor gutter numbers, not Python source to type.**

The older **`forEach`** method explicitly walks next links and gives each stored object to a caller's **`withItem`** method. The two dunder methods below expose traversal to Python's iterator operations instead: **`__iter__`** establishes the starting position, and **`__next__`** supplies one stored object at a time. This is a supported segment, not a complete **`item.py`** file.

The unchanged numbered lines 114–134 are reproduced in **B5, “Active restored state.”** The later frames listed here independently support the same method text and gutter numbers; there is no new implementation to duplicate. The guard-disabled state remains separate in C3.

- Lines 115–120 retain a local traversal variable named **`iter`**, call the supplied callback, stop at the last link, or advance.
- Lines 124–126 find the first node, reset the position stored on it, and return the head **`Item`** instance.
- Lines 129–134 check the saved position before dereferencing it, preserve the current fruit, advance, and return the preserved fruit.
- The active guard above must not be silently combined with the later guard-disabled run in C3.

### C2. `fruit.py`: longest visible driver and the sports revisions

**Historical screen transcription — 48:00 state, `00-48-00-minute.png`.** Lines 1–27 are readable in the right editor pane. Earlier states are recorded separately below rather than being presented as if the third sports request had always been there.

The setup makes the five-fruit chain, loops through it, then starts explicit iteration again and requests acai and apple. The sports portion contrasts explicit indexing with a built-in list iterator. Its third sports request at line 27 is an intentional exhaustion test.

```text
 1 from item import Item
 2
 3 fruit = Item("apple")
 4 fruit.append("banana")
 5 fruit.append("peach")
 6 fruit = fruit.last()
 7 fruit.insertBefore("mango")
 8 fruit.prepend("acai")
 9
10 for x in fruit:
11     print( x )
12
13 #iter = fruit.first()
14 pyIter = iter(fruit.first())
15 print(pyIter)
16 print(next(pyIter))
17 print(next(pyIter))
18
19 sport = ['soccer', 'boxing']
20 print(sport[0])
21 print(sport[1])
22
23 siter = iter(sport)
24 print(siter)
25 print(next(siter))
26 print(next(siter))
27 print(next(siter))
28
```

- Lines 3–8 create apple, add banana and peach, rebind **`fruit`** to the last node, insert mango before that node, and prepend acai.
- Lines 10–11 print the five stored strings; lines 14–17 reset this particular custom iterator and print its representation followed by acai and apple.
- Lines 19–21 read sports by position. Lines 23–27 use one remembered position and finally request a nonexistent third value.

**Observed revision sequence:**

| Original frame | Supported change/state |
|---|---|
| `00-40-00-minute.png`–`00-42-00-minute.png` | Driver through line 17; line 13 commented; no sports code yet. |
| `00-43-00-minute.png` | Line 19 is `sport = []`, a captured intermediate edit. |
| `00-44-00-minute.png` | Line 19 becomes `sport = ['soccer', 'boxing']`. |
| `00-45-00-minute.png` | Lines 20–21 print indices 0 and 1. |
| `00-46-00-minute.png` | Lines 23–24 obtain and print `siter`; the last visible terminal command is still at the prompt with its cursor, not proof of a completed new run. |
| `00-47-00-minute.png` | Lines 25–26 request two values; line 27 is blank. |
| `00-48-00-minute.png` | Line 27 adds a third `print(next(siter))`; exhaustion traceback is partly visible behind the editor. |
| `00-51-00-minute.png` | Line 27 is `#print(next(siter))`; line 13 is still `#iter = fruit.first()`. |

**Observed completed run — latest terminal block at 47:00:**

```text
PS C:\com\joesta\Item> py .\test\fruit.py
acai
apple
banana
mango
peach
<item.Item object at 0x0000021EF36BE2C0>
acai
apple
soccer
boxing
<list_iterator object at 0x0000021EF36E05B0>
soccer
boxing
PS C:\com\joesta\Item>
```

The addresses are transcribed from that specific run and will vary on later runs. The full sports-exhaustion traceback becomes readable in the foreground terminal at 49:08; it appears above a new command and therefore belongs to the earlier run. The traceback excerpts here retain the source and error lines; caret/tilde indicator rows are omitted:

```text
Traceback (most recent call last):
  File "C:\com\joesta\Item\test\fruit.py", line 27, in <module>
    print(next(siter))
StopIteration
```

### C3. Guard experiment: intermediate editor text versus the executed failure

**Historical intermediate edit — `00-49-00-minute.png`.** The visible edit comments out line 130 but still shows **`raise StopIteration`** at its deeper indentation on line 131. Do not silently add a comment character to line 131 and call it an exact transcription.

```text
128     def __next__(self):
129         head = self.first( )
130         #if head._iter == None:
131             raise StopIteration
132         target = head._iter._object
133         head._iter = head._iter._next
134         return target
```

This is a transient editor state, not the proven text executed by the later run. Taken literally with the visible indentation, it is not the valid guard-disabled method modeled in the teaching section. At 49:08, 49:56, and 50:00, the terminal covers the relevant left-pane method lines; the sampled images do not expose the final exact way the instructor disabled both parts of the guard.

**Observed run — `00-49-08-guard-edit.png`, `00-49-56-termination-test.png`, and `00-50-00-minute.png`:**

```text
PS C:\com\joesta\Item> py .\test\fruit.py
acai
apple
banana
mango
peach
Traceback (most recent call last):
  File "C:\com\joesta\Item\test\fruit.py", line 10, in <module>
    for x in fruit:
  File "C:\com\joesta\Item\src\item.py", line 132, in __next__
    target = head._iter._object
AttributeError: 'NoneType' object has no attribute '_object'
PS C:\com\joesta\Item>
```

This run does establish that the loop obtained and printed all five fruit values before failing at line 132. Together with the instructor's explanation, it supports the guard-disabled behavior: the saved next position had reached **`None`**, and **`_object`** was requested from it. It does not establish that the exact intermediate text at 49:00 was the executed file.

**Observed restoration — `00-51-00-minute.png`:**

```text
128     def __next__(self):
129         head = self.first( )
130         if head._iter == None:
131             raise StopIteration
132         target = head._iter._object
133         head._iter = head._iter._next
134         return target
```

The right pane also has the sports third request commented out by this point, as listed in C2. These are separate repairs to separate outcomes. The terminal still contains earlier scrollback; the presence of a restored editor guard does not retroactively change which version caused an older error.

### C4. Shadowing `iter`: failed run and explanatory substitution

**Historical driver revision — `00-51-28-shadowing-traceback.png` and `00-52-00-minute.png`.** The five-fruit setup and loop at lines 1–11 remain as shown in C2. The following is the complete visible remainder of the failing driver, with line 13 active and the third sports request disabled.

```text
12
13 iter = fruit.first()
14 pyIter = iter(fruit.first())
15 print(pyIter)
16 print(next(pyIter))
17 print(next(pyIter))
18
19 sport = ['soccer', 'boxing']
20 print(sport[0])
21 print(sport[1])
22
23 siter = iter(sport)
24 print(siter)
25 print(next(siter))
26 print(next(siter))
27 #print(next(siter))
28
```

The module assignment at line 13 hides the built-in function under an **`Item`** instance. Line 14 then attempts to call that instance. Because the failure occurs at line 14, the **`print(pyIter)`** and sports lines below it do not execute in this run.

**Observed latest run — full-resolution foreground terminal at 51:28:**

```text
PS C:\com\joesta\Item> py .\test\fruit.py
acai
apple
banana
mango
peach
Traceback (most recent call last):
  File "C:\com\joesta\Item\test\fruit.py", line 14, in <module>
    pyIter = iter(fruit.first())
TypeError: 'Item' object is not callable
PS C:\com\joesta\Item>
```

At 52:45 and 53:00, an editor popup identifies a reference to **`iter`** at **`fruit.py:23`**; the popup obscures portions of nearby print statements. Their complete earlier spelling is supported by 52:00, not inferred from under the popup.

**Historical explanatory substitution — `00-54-00-minute.png` and `00-55-00-minute.png`:**

```text
13 iter = fruit.first()
14 pyIter = fruit.first()(fruit.first())
```

Lines 15–28 are unchanged from the complete failing-driver remainder earlier in C4, including the commented third sports request. These two changed-context lines are recovered from the 54:00–55:00 frames; the complete caller need not be duplicated to preserve the revision.

Line 14 makes the hidden instance call explicit; it is not a correction. The visible terminal still quotes the earlier **`pyIter = iter(fruit.first())`** call, so it is not proof of execution of the rewritten line. No completed cleanup run is established by these closing frames.

### C5. Additional readable `item.py` regions and the closing boundary

**Historical partial listing — `00-54-00-minute.png`, lines 1–30.** This view shows the stored fields, constructor, accessor, and navigation methods while the instructor discusses naming scope. It also begins **`append`**, but the rest of that method lies below the visible area.

The complete visible lines 1–30 match **B1, “Navigation methods and the beginning of append.”** Refer to that numbered listing; this later frame independently confirms that the names remain **`after`** and **`before`**. No missing continuation of **`append`** is recovered by this repeated view.

- Lines 2–5 show class-body defaults; line 8 assigns the supplied object to the new instance's **`_object`** attribute.
- Lines 13–21 return an adjacent node when available, otherwise return the same instance. The visible names are **`after`** and **`before`**, even though the instructor discusses **`next`** and **`prev`** as possible or remembered names.
- Lines 24–26 fill an existing empty item; lines 27–30 begin the nonempty-list case by finding the last node and constructing a new item. The connection and return lines are outside this view, so this excerpt must not be treated as the full append implementation.

**Historical partial tail — `00-55-00-minute.png`, immediately above the iterator segment:**

```text
108                     self._next._prev = sel[remainder not visible]
109                     rest = self._prev
110         self._prev = None
111         self._next = None
112         return rest
113
```

Line 108 is clipped at the editor's right edge. Only its readable prefix is recorded; the suffix is not completed from a guess. The enclosing method's beginning is off-screen in this frame, so this tail is not offered as a standalone algorithm. Lines 114–134 below it repeat the guarded traversal and iterator code in C1; the 55:00 frame shows no new count method.

**Closing frame — `00-55-52-closing.png`:** the screen has returned to Discord and an earlier jury-service notice. It adds closing visual coverage and corroborates that the session has left the editor; it is not a count implementation or new terminal result. The transcript's 54:19–55:30 discussion proposes explicit iterator requests, a **`for`** loop with a counter, and manual traversal as ways to finish counting later.

## Micro-Learning Insights

*Removable appendix. Every primary explanation, definition, exercise, and historical listing remains in the guide if this appendix is removed.*

### Make the next experiment answer one question

1. **Predict before running.** At 18:15–18:31, the instructor asks for a count prediction before displaying the result. Write the expected **0**, **1**, or **2**, then compare it with the run. A mismatch identifies a mistaken model of construction or append—not merely a wrong guess.

2. **Mark resets explicitly.** In the count driver, another **`fruit = Item("apple")`** creates a new object. Annotate it “start over with one apple” so you do not mentally carry values forward from an earlier test. Source: **18:00–19:04**, **`00-18-45-count-run.png`**.

3. **Separate “gave back” from “showed.”** The instructor corrects the wording at 18:25: the counting method returns; the outer print displays. Apply the same distinction to **`next(pyIter)`**. It returns acai; **`print(next(pyIter))`** makes acai visible.

4. **Read the failure precisely.** **`next(veg)`** does not fail because Python cannot find **`next`** or **`veg`**. It finds a list where it needs an iterator. The exception category and message narrow the question from “is my whole program broken?” to “what object did I supply?” Source: **25:37–26:10**, **`00-26-08-direct-next-error.png`**.

### Remember what the iterator keeps

5. **The iterator is not the first fruit.** Printing **`pyIter`** shows an object; requesting **`next(pyIter)`** gives acai. Ask “am I inspecting the thing that tracks progress, or requesting its next value?” Source: **37:12–38:40**, **`00-38-55-custom-iterator-output.png`**.

6. **Save, advance, return.** The custom method saves acai in **`target`**, moves **`head._iter`** to apple, then returns the saved acai. That order preserves the current value while preparing the next request. Source: visible **`item.py`** lines 132–134 at **48:00**.

7. **Test one request past the apparent success.** Five correct fruit lines are not the end of the test. A sixth request discovers exhaustion. Disabling the guard produces **`AttributeError`** after those five lines; a correct **`StopIteration`** lets the loop finish normally. Source: **48:38–50:25**, **`00-49-56-termination-test.png`**.

8. **Consumption is not deletion.** Consuming a built-in vegetable iterator leaves **`veg`** holding carrot and broccoli. A new loop over the list can visit both again; a loop over the exhausted iterator cannot. This distinction clarifies the 29:25–31:13 discussion; independent fixture checks confirm the two cases.

9. **Do not assume all iterators store state independently.** The classroom custom method resets an attribute on the same head object. A second **`iter(fruit)`** can therefore affect an existing traversal. This is a source-derived transfer consequence, not an identified answer to the instructor’s unnamed edge-case challenge. Compare the [official iterator contract](https://docs.python.org/3/builtins/stdtypes.html#iteration-related-types).

### Keep names, versions, and claims straight

10. **Replace a suspicious name with what it currently means.** The student’s useful move at 52:49–53:19 is to substitute **`fruit.first()`** for the newly rebound **`iter`**. The later call is then visibly trying to call an ordinary node. This explains the failure without changing the class.

11. **A dot changes the lookup question.** **`fruit.next()`** asks an object for its method; bare **`next(...)`** asks Python to resolve a name in the current environment. Renaming a method can break callers without solving any built-in collision. Source: **21:45–24:43**, revisited **53:43–54:00**; supplemental [name-resolution reference](https://docs.python.org/3/reference/executionmodel.html#resolution-of-names).

12. **Use errors as controlled experiments.** The instructor disables the iteration methods, restores them, and later removes the exhaustion guard. Each change tests a different requirement: obtaining an iterator versus ending traversal correctly. Keep those code states separate rather than remembering every traceback as “iteration failed.” Source: **34:00–35:15** and **48:38–50:25**.

13. **Check the version before interpreting output.** A rejected Git checkout leaves the previous revision in place. A restored file or unsaved editor buffer adds another state to track. Pair the actual command, disk version, and result; do not borrow a nearby terminal line merely because the filename matches. Source: **14:18–17:52** and **32:30–33:42**.

14. **Naming preferences need a reason, not a false rule.** The instructor prefers singular **`sport`** and accepts that plural **`sports`** can make sense. Neither spelling is a Python requirement. Be able to explain which name makes your collection and element expressions clearer. Source: **43:18–45:15**.

15. **An announced idea is not a finished contract.** The class previews iterator work and discusses count strategies without completing a new count implementation at the close. Keep “proposed,” “already visible,” and “implemented in this session” distinct. For formal challenge requirements, consult the actual assignment rather than expanding an oral preview. Source: **12:26–13:06**, **16:09–16:29**, **54:19–55:30**.
