# Sorting by hand: what must the machine remember?

**CSCS1240F26 · Session 19 · Worked teaching companion**

Given a column of numbers, a person can point straight at `0`. A program needs instructions for what to read, what to remember, and when it has seen enough. The class builds those instructions in a plain-text editor, then changes the rules: what if two entries are equal? What if removing one changes the indexes? Can we put entries in order without a second list? By the end, the difference between moving a number to the front and swapping it with its neighbor explains why bubble sort works.

**Recording and evidence.** JoesTA, [CSCS1240F26 19 — play from the beginning](https://www.youtube.com/watch?v=CikvDyntx9U).[1] Time ranges below are elapsed time from the opening, not from the supplied link’s playback offset. The saved English auto-captions contain 1,297 cues (first start `0:00:00.800`; last end `1:02:35.040`). The capture manifest records a video duration of `1:02:37.483`. All 63 whole-minute targets, eight additional event frames and a closing frame were present; every PNG decoded and matched the archived copy. These are requested seeks, not measured frame times. Short intervals lack captions, including the final 2.44 seconds; they do not establish silence.

**How to use the guide.** Follow §§1–7 for the lesson. Use the knowledge check before consulting the answer key. Appendix A preserves the editor's numbers and notation, not a Python program. Python blocks in the teaching sections are **guide-created, independently tested examples**, not the instructor’s source, a challenge submission, or evidence of a classroom run.

## 1 · What does “sort these numbers” actually ask for?

**0:00:00–0:13:59.** A question about the code challenges opens the lesson. Earlier tasks dealt with getting data into or out of a program: standard input, standard output, standard error, files and command-line arguments. Challenge 2060 changed the question from *where does data go?* to *what decisions does the program make about it?* The instructor reported two attempts and two passes at the time, but said the checks were incomplete. Calling a ready-made sort might produce the right output without demonstrating the intended reasoning. The instructor also discussed slow progress, break-week catch-up, and releasing more challenges; neither a final challenge count nor a grading decision was announced. These are historical comments, not current course policy.[1]

On screen in the course Discord’s **`#general`** channel, the actual question asks whether anything is available after 2060. Earlier messages mention building item lists from command-line arguments, standard input or a file, writing items back out, and adding a prepend method. A student also proposes checking prices daily using watched items and changing price/date records. Those messages illustrate the prior I/O and collection work; neither the tracker nor its code was demonstrated here.

The instructor connects sorting to organizing information for search, using Google as a motivation, then sets aside the large-scale problem. Start with the actual ten-entry scratch column, here written across the page in **reading order**:

```text
344.5   353.3   23   536   2535   34   54   0   0.2   53
```

“Sort” does not specify a direction, or even a comparison rule. The class assumes **ascending numeric order**: `0` must precede `0.2`. In descending order it would follow it. A student first looked for negatives; another strategy used digit count and aligned columns. Both reveal how much a human gets from seeing the whole screen. A short spelling need not denote the smallest value, and a decimal point is not a digit. The instructor’s “every character matters” is a way to break the task down, not a claim that modern processors literally fetch only one bit at a time.

Before comparing two values, work out how the program gets **one** value. Read the characters `3`, `4`, `4`, `.`, `5`, then the line ending. The radix point separates the whole and fractional parts; the line boundary tells us when that numeral ends. A text file gives the program a string, not the number it depicts. At `0:05:00`, line 2 reads `353.`; a minute later it reads `353.3`. The first spelling is unusual, not necessarily invalid: Python accepts `float("353.")` as `353.0`. Adding the last `3` changes the value, not just its appearance. The instructor also mentions a letter entry rejected while composing the example; no general parser or challenge validation rule was shown.

**Annotated teaching version — guide-added example and comments**

```python
from io import StringIO                   # A small text stream instead of a course file.
source = StringIO("344.5\n53\n")           # Two actual lesson values, one per line.
a = source.readline()                     # Reads '344.5\n'; the next call starts after it.
b = source.readline()                     # Reads '53\n', not a list of numbers.
print(a < b)                              # Text: '3' precedes '5', so True.
print(float(a) < float(b))                # Numbers: 344.5 is not below 53, so False.
```

**Independent output**

```text
True
False
```

This distinction matters to the sorting rule: the very same lines give opposite answers. In Python, **`readline()`** returns one line as a string for a text stream; **`readlines()`** or **`list(source)`** collects the remaining lines. That clarifies the spoken shorthand about getting a list. Conversion is a separate decision.[4]

**Key items to scan**

- **`#general`** — the question about challenges after 2060 prompts the lesson; the price watcher is a prior proposal.
- **2060** — the sorting challenge motivated reasoning about comparisons; a passing check did not prove that reasoning had been implemented.
- **Ascending numeric order** — least to greatest by value, not by spelling, width, or original position.
- **`readline()`** — reads one text line; **`readlines()`** and **`list(source)`** collect lines, not converted numbers.
- **`float(a)`** — turns the text for `344.5` into a number before comparison.

## 2 · From seeing the minimum to remembering it

**0:14:00–0:19:59.** The instructor switches to a scratch tab and tries to show the class one number at a time. Pressing Enter out of habit spoils the intended one-at-a-time presentation; afterward even the instructor cannot confidently name the least value. The point is not the answer to that fleeting sequence. It is the student’s remedy: remember the first number, then replace it only when the next one is smaller. A **variable** makes that selective memory explicit. No need to remember each losing candidate.

Back in the ten-entry column, `344.5` is a sound starting value **because it is the only value seen so far**, not because it is especially small. Using an arbitrary `0` instead would fail on a list of strictly positive numbers: it might report a minimum that never occurred.

**Annotated teaching version — one pass only**

```python
values = [344.5, 353.3, 23, 536, 2535, 34, 54, 0, 0.2, 53]
least_at = 0                              # Start at the first entry; the list is nonempty.
for i in range(1, len(values)):           # Visit the other nine positions in order.
    if values[i] < values[least_at]:      # Compare this number with the best so far.
        least_at = i                      # Save its position; the list itself is unchanged.
print(least_at, values[least_at])         # Display position 7 and the value there: 0.
```

**Independent output:** `7 0`. The spoken reasoning first keeps just the value; the example also saves its position to prepare for removal. This is not an editor line number.

| Python index | Number read | Smaller than the remembered number? | Remembered value afterward |
|---:|---:|---|---:|
| 0 | 344.5 | First entry; no comparison | 344.5 |
| 1 | 353.3 | No | 344.5 |
| 2 | 23 | Yes | 23 |
| 3–6 | 536, 2535, 34, 54 | No at each position | 23 |
| 7 | 0 | Yes | 0 |
| 8–9 | 0.2, 53 | No at either position | 0 |

After each comparison the remembered value is the least **of the part already inspected**. This claim, called a *loop invariant*, explains why the last remembered value is the minimum of the whole sequence. It does not mean the sequence has been sorted: `344.5` is still first. When a student asks whether the goal is just to return a value, the instructor clarifies: order the entire list. The instructor tentatively recalls a restriction on functions for the challenge; no assignment specification was shown to settle that rule.

**Key items to scan**

- **Loop invariant** — after each visit, the remembered entry is the smallest seen so far.
- **First entry** — initializes the search from data that really exists. Empty input has no first entry.
- **`values[i] < values[least_at]`** — only a strictly smaller entry changes the remembered position.
- **`least_at`** — `0 → 2 → 7`; the corresponding remembered values are `344.5 → 23 → 0`.
- **One pass** — identifies one winner, not a complete ordering.

## 3 · Two regions, two 536s, and what “remove” means

**0:19:00–0:30:59.** The class starts a second region below the working column. Write `0` there as the first sorted value. Now imagine scanning the upper region again without changing it. The same `0` would win every time. To make progress, each pass must transfer **one occurrence**: add the winner to the result and remove it from the remaining work. A momentary screen with `0` in both regions can illustrate copying; it is not evidence that the input contained two zeros.

The instructor adds a second `536` and asks whether this is still a valid input. It is. A **list** keeps both occurrences at different positions. A **set** of those numbers would retain the value only once; that changes the question from sorting observations to identifying distinct values. Consider prices in a store: for a mean or median across items, a price attached to two different items deserves two observations. For a list of price points offered, once per distinct price might be exactly right. The class chooses the former rule: keep the original number of entries and all their frequencies. Students propose remembering the last value or removing every match for the *different* deduplication task.

Once `536` is the smallest remaining value, move just the first winning occurrence. The other remains and will win another pass. A strict comparison leaves the first tie as winner; changing **`<`** to **`<=`** lets a later equal occurrence take its place. In a column of bare numbers the difference is invisible in the printed result. If two equal prices belong to different items, the order of those records can reveal it. Choosing the first equal minimum and leaving the remainder in its original relative order preserves ties in this particular transfer method.

Why remember a position as well as a value? A position identifies the exact occurrence. The instructor calls the zero on editor **line 8** “index 8” while treating the gutter as a position marker. In the original Python sequence the zero is at **index 7**. Delete that slot and *later* indexes decrease by one; earlier ones do not change. Erase only its text in an editor and you can instead leave an empty row. Assigning Python’s **`None`** is a useful analogy for a retained slot, not what a blank line in Sublime Text literally contains.

**Annotated teaching version — replacement versus deletion**

```python
work = [54, 0, 536, 0.2]                 # Small fixture keeping the neighbors of zero.
work[1] = None                           # Replace zero. There are still four slots.
print(work, len(work))                   # 536 is still at index 2.
work = [54, 0, 536, 0.2]                 # Reset: a NEW four-entry list, not another edit.
picked = work.pop(1)                     # Return 0; remove its slot; later indexes shift.
print(picked, work, len(work))           # 536 is now at index 1; three slots remain.
```

**Independent output**

```text
[54, None, 536, 0.2] 4
0 [54, 536, 0.2] 3
```

A Python list can also **`remove(value)`**, which deletes the *first equal* occurrence; it does not force the caller to supply an index. **`pop(index)`** deletes the chosen occurrence and returns it. The discussion of positions explains what changes inside an ordered sequence, not a restriction that Python only allows deletion by index.[2]

**Key items to scan**

- **Two regions** — the remaining entries are candidates; the lower region contains winners in the order found.
- **Two `536`s** — preserve both; a set would discard one before the sort.
- **`<` / `<=`** — keep the first minimum in a tie / replace it with the later equal occurrence.
- **`None`** — occupies a slot. **`pop(1)`** removes a slot and returns what occupied it.
- **`remove(536)`** — deletes the first equal value; it is not “remove index 536.”

## 4 · Why the representation changes the cost

**0:26:00–0:37:59.** At `0:27:15`, a second editor shows the eleven working values without the numbered gutter. The separate result zero is visible behind it in Sublime Text, not in the foreground copy. Take the line numbers away: can you still find the minimum? Yes: visit each value and compare it with the best seen. To delete or reposition a particular occurrence, however, something has to locate it. That need not be a numbered index: in the course’s doubly linked `Item` example, one can follow links forward or backward. A student points out that Python often hides these details; the instructor compares this with being able to program without thinking about object identity. Abstraction hides bookkeeping from the caller, not the need to do it somewhere.

The instructor models a **classic array** as a single block of neighboring slots, each the same width. Suppose each occupies **23 units**, the class’s deliberately arbitrary choice. With base address `B`, index 0 starts at `B`, index 1 at `B + 23`, and index 2 at `B + 46`. The **third element** is at index 2, *not* at index 3. For index `i`, the address is `B + i × 23`. You can calculate a distant slot without inspecting the intervening entries. If widths varied, the multiplication alone could not say where the third one began.

| Arrangement | What reaching the third entry entails | What growth may cost |
|---|---|---|
| Fixed-width contiguous array | Skip two slot widths from the base | The next memory location may already be occupied. A larger block and a copy may be needed. |
| Linked nodes in the course example | Start at the first node, follow its next link, then follow the next one | Nodes need not be contiguous; links let the structure change, but finding a distant node takes traversal. |
| Growable array of slots | Compute an index as in the fixed-width case | Reserve spare capacity; when it runs out, allocate and copy rather than assuming adjoining memory is free. |

The instructor proposes reserving **ten extra spaces** rather than reallocating for each new value. The trade is unused space now for fewer later copies; ten is an illustration, not a Python setting. For a nearly fixed collection, a classic array’s direct access is attractive. For frequent growth or shrinkage, ask what has to move, how often, and whether the algorithm needs random access or only the next item.

**Supplemental Python clarification.** The course’s linked `Item` is **not** the implementation of CPython’s built-in **`list`**. CPython stores a contiguous, variable-length array of references; the referred-to objects need not be adjacent or the same size. Deleting from the middle shifts later *references*, not necessarily the number objects themselves.[3] The classroom base-address arithmetic models array slots; it is not an instruction to add 23 to a Python object’s identity.

**Key items to scan**

- **`B + i × 23`** — address in the hypothetical fixed-width array; index 2 is 46 units past the base.
- **Linked traversal** — two links to reach the third node from the first; the editor’s visible line numbers are unnecessary.
- **Capacity versus length** — reserved slots may exceed the number of live entries; that spare space can postpone copying.
- **CPython `list`** — contiguous references, not a chain of the course’s `Item` nodes.[3]

## 5 · Finish the minimum-transfer sort, including the last item

**0:37:00–0:43:59.** With the storage question settled, the instructor returns to the upper column. Reset the remembered minimum to the **first remaining entry** on every pass. After removing zero, the next scan starts at `344.5`, improves to `23`, then improves again to `0.2`. Append `0.2` after `0` and remove only that occurrence. At `0:39:00` its new position is editor line 9, or Python index 8. The auto-captions near this point say “2”; the screen and the spoken correction identify **zero point two**.

The easiest stopping question is not “Does my new length equal the original length?” unless you saved the original count. Here the working list is shrinking; **no remaining entries** means there is nothing left to transfer. With exactly one left, that entry is automatically the minimum. There is no second position to inspect. Trying to read index 1 in `[2535]` raises **`IndexError`**, not a special end-of-list number.

| Pass or phase | Value moved to result | Why this step matters |
|---|---|---|
| 1–2 | `0`, then `0.2` | Start a fresh search after each removal; a previous winner is no longer a candidate. |
| 3–6 | `23`, `34`, `53`, `55` | The later scratch state uses `55`. The earlier ten-number setup used `54`; the class changes the data. |
| 7–8 | `344.5`, `353.3` | Decimal values are compared numerically, not alphabetically. |
| 9 | First `536` | Candidates `536, 2535, 536`: the last equals, but is not less than, the first. |
| 10 | Second `536` | Remaining candidates `2535, 536`: now the second occurrence wins. |
| 11 | `2535` | One item, zero comparisons with neighbors. Transfer it, leaving the working list empty. |

**Guide-derived trace**, using the later `55` state consistently and preserving the two `536`s. It is not a continuous sequence of eleven observed program executions. The lower editor region at `0:43:25` reads `0, 0.2, 23, 34, 53, 55, 344.5, 353.3, 536, 536, 2535`. A separate partially edited upper line is **not** another result entry.

**Annotated teaching version — isolate the last three transfers.** This small fixture tests the tricky end of the reasoning, not the code challenge’s file interface.

```python
work = [536, 2535, 536]                  # Reset to the last three candidates.
result = []                              # New result for this suffix only.
while work:                              # No first entry is read if work is empty.
    best = 0                             # First remaining entry is the provisional winner.
    for i in range(1, len(work)):         # A singleton has no other positions to inspect.
        if work[i] < work[best]:          # Equal 536 does not replace the first one.
            best = i                     # Save the winning position in this pass.
    result.append(work.pop(best))         # Remove once, return that value, append it once.
    print(result, work)                  # Show completed transfers and remaining candidates.
```

**Independent output**

```text
[536] [2535, 536]
[536, 536] [2535]
[536, 536, 2535] []
```

**Key items to scan**

- **Fresh `best = 0`** — starts in the current, shorter working list; an index saved on an earlier pass can now mean something else.
- **`result.append(work.pop(best))`** — `pop` returns the value, `append` changes the result list, and neither action prints it.[2]
- **Empty versus singleton** — an empty working list starts no pass; one element starts a pass but needs no comparison.


## 6 · Count the work, not the milliseconds

**0:43:25–0:51:59.** The completed column answers “Does it sort?” The instructor restores working data and asks a different question: “What happens if I give this method one more entry?” **Space** means storage such as a second list; **time** means how much work is required, rather than the clock time on one particular machine. Here **N** is the number of data items, not the number of instructions. Big O describes an upper bound on how work grows with the input; in informal discussion it often names the characteristic growth rate.

Consider the eleven-value version. Finding its minimum takes ten comparisons *after* accepting the first entry as a candidate. On the next pass only ten entries remain, requiring nine comparisons. The last singleton needs none. The count is `10 + 9 + … + 1 + 0 = 55`, or `N(N − 1)/2` for this comparison rule. Adding a twelfth entry brings the count to `66`: eleven more comparisons in total, not just one. The list shrinks, but repeated scans still give quadratic growth. These are **verified counts of numeric comparisons only**, not timings or counts of removals, shifts or appends.

| Question | On already ascending input | What the result does and does not establish |
|---|---|---|
| Is this list ordered? | A single scan can verify all neighboring pairs; ten checks for eleven entries. | Linear work, O(N). No need to build a sorted copy just to answer yes or no. |
| What does the minimum-transfer method actually do? | It still searches for each winner and transfers it; 55 comparisons for eleven entries. | O(N²) comparisons even if the answer looks obvious. The second list grows to N entries. |
| Could a different method stop early? | A full adjacent-comparison pass with **no swaps** certifies order. | One pass suffices in this case. A swap on a pass means the no-swap test has not yet certified completion. |

The class considers reversed order and then a nearly sorted list with one item out of place. A student suggests insertion; the instructor recognizes that the proposed “done after one pass” has slipped into thinking about an **optimization**, a change to the method. A flag can remember whether a move occurred, but its meaning depends on the algorithm: a minimum-transfer sort must remove even the winning first entry; a no-removal test cannot certify order for that method. Nor does a single out-of-place value promise exactly two passes for every possible procedure. The important question is *which completed pass proves what?*

**Key items to scan**

- **N** — count of entries; **O(N)** — work bounded linearly in that count, such as checking every neighboring pair once.
- **O(N²)** — here, repeated searches through progressively shorter lists; their counts add rather than multiply by a fixed pass length.
- **O(log N)** — logarithmic growth, mentioned for contrast. The instructor did not demonstrate a logarithmic sorting algorithm; the editor shorthand is transcribed in Appendix A.
- **No-swap flag** — meaningful only after a full pass checking all relevant neighboring pairs.
- **Extra storage** — the result list grows as the source shrinks; avoiding a second collection is a separate question from reducing comparisons.

## 7 · Move to the front, insert, or swap a neighbor?

**0:51:00–1:02:35.** The class now asks whether it can reuse the working list instead of building a second result. A mutable list can be changed in place, but permission to change it is not a strategy. `344.5` followed by `353.3` is already in ascending order *as a pair*. One caption says `354.3`; the screen reads `353.3`. When `23` arrives, it is smaller than `353.3`. A student proposes moving it to index zero. That works for this prefix because `23` is also smaller than `344.5`; it does **not** follow simply from winning the comparison against its neighbor.

Try the same shortcut when the next offending value is `34`. The rest of the prefix can include `23`. Moving `34` straight to the front gives `34, 23, …`, which is out of order. At `0:55:00` the front reads `23, 34, …`. The original prefix is restored at `0:56:00`; `34, 23, …` appears at `0:57:00` in a separate trial. If you insert `34` into an ordered prefix, its proper place is **after `23`** and before the larger entries. If you swap two neighbors, by contrast, you are making only a local claim about that pair. A variable remembering where to restart is useful only when you can explain why positions you skip are finished.

| Decision | What it preserves | What it does **not** prove |
|---|---|---|
| Move the first smaller-than-previous value to the front | You can keep all entries if you remove and reinsert one occurrence. | That the moved value is the least of everything before it. |
| Pick the minimum of the **entire remaining region** | No still-unselected entry belongs before that winner. | That a local dip such as `34` was that minimum. |
| Compare adjacent entries; swap only if the left is larger | The two entries are now in the correct relative order. | That either entry is already in its final position after that single comparison. |

**0:58:00 onward: change the unit of work.** Rather than moving `23` past the whole prefix, exchange just `353.3` and `23`. The first three values now read `344.5, 23, 353.3`: this is progress, not a sorted list. Continue across neighboring pairs. When `2535` meets `34` and then `55`, it can move one slot to the right each time. The `0:59:34` frame shows the local sequence `536, 34, 55, 2535` within the column. The intermediate trio `34, 2535, 55` is reconstructed from the speech and the two endpoints. No sampled frame shows that intermediate exchange or a completed sort.

**Worked continuation — two adjacent comparisons, guide-added comments.** The fixture isolates the later neighbors rather than claiming to run the classroom editor.

```python
row = [2535, 34, 55]                    # Only the three neighbors being studied.
for left in range(len(row) - 1):         # Left indexes 0 and 1; each has a right neighbor.
    right = left + 1                    # Neighbor immediately to the right.
    if row[left] > row[right]:          # Ascending order: swap only an inverted pair.
        saved = row[left]               # Preserve 2535 before overwriting its slot.
        row[left] = row[right]          # Move 34 (then 55) into the left slot.
        row[right] = saved              # Put the preserved 2535 back on the right.
    print(row)                          # Display the same list after each comparison.
```

**Independent output**

```text
[34, 2535, 55]
[34, 55, 2535]
```

For a left-to-right pass in ascending order, the greater of the two neighbors goes right. At the next comparison the larger value carried forward competes with the next entry. By the end of a full pass the greatest remaining value has reached the far right; smaller values can “bubble” toward the front over later passes. That is why the instructor calls this **bubble sort**. Reversing the comparison sign without reversing the intended ordering changes what gets pushed where. The instructor catches a less-than/greater-than mix-up during the live reasoning.

**The closing student question.** How do you move a value without accidentally duplicating one and losing another? The instructor uses `55` and `34` as a mental exercise: if you take `55` out to move `34`, you are still *remembering* `55`. A temporary variable does just that. Overwriting the first slot and then reading that overwritten slot back cannot recover its old occupant. The same temporary variable can be reused for each swap. A student realizes the already remembered least value may be enough for the operation they are coding. That works **if** it is the value that needs to survive the overwrite; remembering a value is not, by itself, remembering its location. The closing “It worked” is a spoken report, not a visible program, grader result or traceback.

**Key items to scan**

- **`34, 23`** — counterexample to moving every newly smaller value straight to the front.
- **`row[left] > row[right]`** — a larger left neighbor is an inversion for ascending order. Equal values need not swap.
- **`saved`** — holds the displaced value until the second assignment; prevents overwriting both slots with the same value.
- **One full forward bubble pass** — the largest remaining value moves to the right end; a single neighbor swap has a weaker guarantee.

## Syntax and decisions to remember

| Pattern | Meaning in this lesson |
|---|---|
| `range(1, len(work))` | First item already supplies a candidate. Visit the rest; a singleton produces no positions here. |
| `work[i] < work[best]` | Compare numeric values, not indexes. Keep the earlier equal minimum. |
| `work.pop(best)` | Remove exactly the winning slot and return its value. |
| `result.append(value)` | Add one occurrence at the end of the sorted region. Changes the list; does not return the changed list.[2] |
| `while work:` | Start another pass only while a candidate exists. |
| `row[left] > row[left + 1]` | Test for an inverted adjacent pair in an ascending sort. Ensure the right neighbor exists. |
| `values.sort()` versus `sorted(values)` | The list method changes that list in place; the built-in function produces a new sorted list. Neither explains the manual comparisons requested in class.[2] |

## Knowledge check

1. If the first item is `344.5`, why initialize the minimum to it rather than to `0`? When does that initialization need a guard?
2. In `[536, 2535, 536]`, which position wins the first pass with `<`? What changes with `<=`? Which property of the numeric result stays the same?
3. The editor shows `0` on line 8. After deleting its slot, what happens to the entry formerly on line 9? How would replacing the slot with `None` differ?
4. For slots 23 units wide, how far from the base does the third entry start? Why can’t you use that rule to reach the third node of a linked list?
5. A student claims an already sorted eleven-item input makes the two-list minimum-transfer algorithm finish in ten comparisons. Which *other* task does that count describe?
6. A prefix starts `23, 344.5, 353.3`; a later `34` is smaller than its predecessor. Why not put it at index zero? In a swap of `[55, 34]`, what must be kept before overwriting the first slot?

### Answer key

1. The first entry really occurs and is the least of the one value seen. Starting at zero would report a nonexistent minimum on positive-only input. Check for empty input before accessing index zero.
2. Strict `<` keeps index 0 because the `536` at index 2 only ties it; `<=` changes the saved position to 2. Removing one per pass keeps both occurrences in the eventual numeric result either way.
3. Editor line 8 corresponds to Python index 7 in this column. On deletion, the next entry moves one slot earlier. On replacement it stays at its old index, and the length is unchanged.
4. Two intervening slot widths: `2 × 23 = 46` units. Linked nodes need not occupy predictable adjacent addresses; follow two next links instead.
5. Ten adjacent comparisons can *verify order* in eleven entries. Repeated minimum selection still compares `10 + 9 + … + 0 = 55` times; it does not acquire an early exit merely because the data happens to be sorted.
6. `23` belongs before `34`; being less than one predecessor does not establish being less than the entire prefix. In the swap, preserve `55` first. Otherwise writing `34` over it loses the value needed in the second slot.

## Library of definitions

| Term | Definition anchored to the demonstration |
|---|---|
| Algorithm | A repeatable set of decisions: read a number, compare it, remember a winner, and know when to stop. |
| Ascending / descending | Smaller first / larger first under an agreed comparison rule. A text comparison can disagree with a numeric one. |
| Radix point | The `.` in `344.5`, separating whole from fractional places in this base-ten numeral. |
| Sequence / index | Values visited in order / a position naming an occurrence, such as index 7 for zero in the first column. |
| Conditional / invariant | A test chooses whether to update; the fact that the remembered entry is the smallest seen stays true after either choice. |
| Duplicate / multiplicity | Two entries can have equal values. Multiplicity counts occurrences; `536` has multiplicity two in the later example. |
| Mutable | An existing list can change contents, positions or length. Rebinding a name to a newly constructed list is different. |
| Array / base address | Equal-width slots in contiguous storage / the start of the first slot in the classroom model. |
| Linked list / traversal | Nodes connected by references / following those references rather than calculating a slot address. |
| Capacity | How many slots are allocated; length counts how many currently hold entries. |
| Edge case | A boundary that can break a plausible rule: all equal, no values, or one value with no next neighbor. |
| Time and space complexity | How work and storage requirements grow with input size, not a stopwatch time or exact memory reading. |
| In place / temporary variable | Change the existing collection rather than building another result list / keep one displaced value safe while overwriting a slot. |
| Adjacent inversion / bubble sort | Neighbors out of the desired order / a method that fixes such pairs over successive passes. |


## Sources


[1] https://www.youtube.com/watch?v=CikvDyntx9U&t=3577s — CSCS1240F26 19 — JoesTA

[2] https://docs.python.org/3/tutorial/datastructures.html — Python tutorial: Data Structures

[3] https://docs.python.org/3/faq/design.html — Python Design FAQ: How are lists implemented in CPython?

[4] https://docs.python.org/3/tutorial/inputoutput.html — Python tutorial: Methods of File Objects

## Appendix A · Numbered historical screen record

**Sublime Text, Plain Text.** The tab labeled `344.5` contains scratch data; no saved program filename, command or output stream is established. These are transcriptions of discrete editor states after a second visual pass, not an executable file. Read **down one timestamp column**. The left column is the one-based editor gutter, not source to type and not a Python index. A centered dot (·) represents a visible blank line; a dash (—) means no line with that number is shown in that state. Do not combine columns into a single run.

### A1 · Why the instructor asks about line 2

At `0:05:00`, line 2 ends with the decimal point in `353.`. At `0:06:00` it reads `353.3`. All the other nonblank entries in the first column below agree between those two frames. This minor edit changes the numeric value; the running-minimum walkthrough uses the later spelling. At `0:15:00` the separate one-at-a-time display shows just `6` on line 1; the following minute frames show an empty tab. They do not reveal a complete former sequence or its minimum.

| Editor line | `0:05:00` | `0:06:00` | `0:20:00` | `0:22:00` |
|---:|---:|---:|---:|---:|
| 1 | `344.5` | `344.5` | `344.5` | `344.5` |
| 2 | `353.` | `353.3` | `353.3` | `353.3` |
| 3 | `23` | `23` | `23` | `23` |
| 4 | `536` | `536` | `536` | `536` |
| 5 | `2535` | `2535` | `2535` | `2535` |
| 6 | `34` | `34` | `34` | `34` |
| 7 | `54` | `54` | `54` | `54` |
| 8 | `0` | `0` | `0` | `0` |
| 9 | `0.2` | `0.2` | `0.2` | `536` |
| 10 | `53` | `53` | `53` | `0.2` |
| 11 | · | · | · | `53` |
| 12 | — | — | · | · |
| 13 | — | — | `0` | · |
| 14 | — | — | — | `0` |

At `0:20:00`, the upper zero is on line 8; line 13 is the lower result, not an eleventh input. By `0:22:00` a duplicate `536` occupies the upper line 9, shifting the lower zero to line 14. Selecting identical text in an editor can highlight both appearances; it does not implement comparison or deletion.

### A2 · Empty the slot or close the gap?

At `0:27:15` these are the numbered rows of Sublime Text behind the unnumbered second editor. The highlighted work region stops at line 11; the separate result is below it. Compare line 8 **and the line number of `0.2`** across the first three columns. A blank leaves later rows where they were. Removing the row pulls them up. At `0:38:00` the upper zero is visible again; do not read `0:31:00` through `0:39:00` as an irreversible series of list mutations. By `0:39:00`, `55` replaces the earlier `54` in the visible work.

| Editor line | `0:27:15` | `0:30:00` | `0:31:00` | `0:39:00` |
|---:|---:|---:|---:|---:|
| 1 | `344.5` | `344.5` | `344.5` | `344.5` |
| 2 | `353.3` | `353.3` | `353.3` | `353.3` |
| 3 | `23` | `23` | `23` | `23` |
| 4 | `536` | `536` | `536` | `536` |
| 5 | `2535` | `2535` | `2535` | `2535` |
| 6 | `34` | `34` | `34` | `34` |
| 7 | `54` | `54` | `54` | `55` |
| 8 | `0` | · | `536` | `536` |
| 9 | `536` | `536` | `0.2` | `0.2` |
| 10 | `0.2` | `0.2` | `53` | `53` |
| 11 | `53` | `53` | · | · |
| 12 | · | · | · | · |
| 13 | · | · | `0` | `0` |
| 14 | `0` | `0` | — | — |

### A3 · Shrinking work, growing result

Read from the top until the blank separator to see what still needs sorting. At `0:40:49`, for instance, five unsorted numbers remain and the lower block already contains six winners. At `0:42:00`, the upper region is the three-item tie test worked in §5. At `0:43:25`, the top row literally says `25` during an edit; the `2535` in the lower region is fully legible. Do not silently turn the upper `25` into a new input or complete it to `2535`.

| Editor line | `0:40:00` | `0:40:49` | `0:42:00` | `0:43:25` |
|---:|---:|---:|---:|---:|
| 1 | `344.5` | `344.5` | `536` | `25` |
| 2 | `353.3` | `353.3` | `2535` | · |
| 3 | `23` | `536` | `536` | · |
| 4 | `536` | `2535` | · | `0` |
| 5 | `2535` | `536` | · | `0.2` |
| 6 | `34` | · | `0` | `23` |
| 7 | `55` | · | `0.2` | `34` |
| 8 | `536` | `0` | `23` | `53` |
| 9 | `53` | `0.2` | `34` | `55` |
| 10 | · | `23` | `53` | `344.5` |
| 11 | · | `34` | `55` | `353.3` |
| 12 | `0` | `53` | `344.5` | `536` |
| 13 | `0.2` | `55` | `353.3` | `536` |
| 14 | — | · | — | `2535` |

### A4 · Restored working data and competing moves

By `0:44:00` the earlier working layout is back, with just `5` visible on line 7 at that instant. At `0:45:00` it reads `55`; the instructor has written growth-rate notation under the data. Later, line 20 reads `O(LOG)` without the ` N`. Both are reproduced as plain-text notation, including the caret in `O(N^2)`; neither is Python code. Read the first three rows at `0:53:00`, `0:57:00` and `0:58:15` to distinguish sending values to the front from swapping neighboring values. Compare rows 5–7 of the last two columns to see the effect of adjacent exchanges without pretending the stills capture every edit.

| Editor line | `0:45:00` | `0:53:00` | `0:57:00` | `0:58:15` | `0:59:34` |
|---:|---:|---:|---:|---:|---:|
| 1 | `344.5` | `23` | `34` | `344.5` | `344.5` |
| 2 | `353.3` | `344.5` | `23` | `23` | `23` |
| 3 | `23` | `353.3` | `344.5` | `353.3` | `353.3` |
| 4 | `536` | `536` | `353.3` | `536` | `536` |
| 5 | `2535` | `2535` | `536` | `2535` | `34` |
| 6 | `34` | `34` | `2535` | `34` | `55` |
| 7 | `55` | `55` | `55` | `55` | `2535` |
| 8 | `536` | `536` | `536` | `536` | `536` |
| 9 | `0.2` | `0.2` | `0.2` | `0.2` | `0.2` |
| 10 | `53` | `53` | `53` | `53` | `53` |
| 11 | · | · | · | · | · |
| 12 | · | · | · | · | · |
| 13 | `0` | `0` | `0` | `0` | `0` |
| 14 | · | · | · | · | · |
| 15 | · | · | · | · | · |
| 16 | · | · | · | · | · |
| 17 | · | · | · | · | · |
| 18 | `O(N)` | `O(N)` | `O(N)` | `O(N)` | `O(N)` |
| 19 | `O(N^2)` | `O(N^2)` | `O(N^2)` | `O(N^2)` | `O(N^2)` |
| 20 | `O(LOG N)` | `O(LOG N)` | `O(LOG N)` | `O(LOG)` | `O(LOG)` |

The `1:00:18` editor still shows the `0:59:34` arrangement while the class discusses temporary storage. The recording returns to OBS/Discord near the end. None of these views is a submitted challenge, a completed bubble-sort run, or the student's code.

## Micro-Learning Insights · Removable appendix

- **Start with a real candidate.** `344.5` is a valid first minimum even though it won’t win. “Smallest seen” is already true before the first comparison. (§2)
- **Ask what “smaller” compares.** For `344.5` and `53`, comparing the strings and comparing the numbers give opposite answers. Decide what one input line represents first. (§1)
- **A value is not an occurrence.** At `0:42:00`, `536` is on two rows. Remembering the winning position distinguishes them; comparing with `<` keeps the first tie. (§§3, 5)
- **Read the whitespace as evidence.** Find `0.2` before and after deleting the line holding zero. If `0.2` has not moved up, an empty slot may have been left instead. (Appendix A2)
- **Ask what “next” costs.** In fixed-width storage, index 2 is two widths past the base. In a linked structure, “third” is two next-link operations from the first node. (§4)
- **Test the last pass on paper.** With `[2535]`, choose index 0, compare nothing, move 2535, stop. A rule that always reads a second item fails this test. (§5)
- **A right answer is not a cost measurement.** Sorting and checking an already sorted sequence can display the same numbers while doing very different amounts of work. (§6)
- **Demand proof before skipping a position.** `34` beats its predecessor but not `23`. Only a properly established prefix or suffix justifies skipping it. (§7)
- **Name what must survive the overwrite.** Before changing `[55, 34]`, point to the old `55` and say where it will live until you put it back. That is the purpose of the temporary variable. (§7)
