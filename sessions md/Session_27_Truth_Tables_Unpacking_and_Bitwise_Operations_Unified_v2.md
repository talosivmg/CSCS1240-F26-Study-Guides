# CSCS1240F26 · Session 27 | From floating-point fields to bitwise operations

**Worked teaching companion** · [JoesTA’s class recording](https://www.youtube.com/watch?v=vQWCT5ioRFg) · Unified v2

How could a computer separate the sign of a number from the bits that describe its size? The instructor starts with that question, then sets floating-point decoding aside to build the tools it would take. The turning point is a small Python program: the same AND that compares a single pair of bits can keep selected positions of a much larger number. To read the program, the class has to settle two other questions: what exactly does one trip through a `for` loop pick up, and what is the difference between a digit and the value of its place?

**Study route.** Predict the last row of the three truth tables before checking it. In the debugging section, cover the output and decide whether each loop will print a whole tuple, print its members, or stop before the body. At the closing bit display, read down a single column before trying to read across a whole number. The knowledge check and definitions precede the numbered screen record; the final Insights page can be detached.

**Source and scope.** The recording title, course, session and video ID match the request. The English auto-caption track runs from the opening to the sign-off. The visual record consists of 100 full-screen captures: every whole-minute target from 0:00:00 through 1:02:00, plus 37 event or closing views. Times identify requested frames, not exact edit or save times. An editor view and old terminal output can coexist; the guide labels separately what was visible, what was said, and what was independently reproduced. Indented listings with editor line numbers are historical evidence, not code to paste. The recording motivates floating-point field extraction; it does **not** finish a floating-point decoder.

## Where the challenges stood (0:00:06)

Two students were working on challenge **`3000`**. The instructor said the posted sequence reached **`3030`**, naming **`3010`** and **`3020`** along the way. From memory, the instructor described these as challenges on the course’s **`Item`** doubly linked list: the emphasis was on iterators and generators, rather than separate exercises for every operation, such as counting items or converting to and from a list. That was an oral overview, not a list of grader requirements.

When a student asked whether nine challenges remained, the answer was not a fixed total: the instructor expected to add challenges based on what the class actually covered, at most about three per remaining week. The older October 26 progress note in Discord’s **`#general`** said “3 code challenges per week” and “6 weeks left --> 18 more challenges.” That projection used the weeks remaining *then*; it was not a new promise of eighteen in this session.

**Key items to scan**

- **`3000`, `3010`, `3020`, `3030`** — students’ starting point in the discussion, the next two named challenges, and the reported endpoint that day.
- **`Item`** — the course’s doubly linked list; iterators and generators were among the practice topics the instructor recalled.
- **`#general`** — held the earlier pacing note. Distinguish the rate (about three per week) from a projected total.

## How can a fixed number of bits hold a fraction? (0:02:34)

Suppose all you know is how to count whole numbers in base two. How would you store a fractional part? How would you describe something larger than 2⁶⁴ or something extremely small? Astronomical and quantum scales motivated the instructor’s question. Floating-point representation separates two jobs: the significant digits tell you the shape of the number; an exponent tells you the scale at which to read them. That buys range without making every possible number exactly representable.

### Read the standard’s table before counting the bits (0:04:04)

The instructor looked up IEEE 754, the floating-point arithmetic standard from the Institute of Electrical and Electronics Engineers. IEEE 802 was mentioned as a familiar *networking* standards family, not the floating-point specification. The search for **`ieee754`** also showed Wikipedia’s *IEEE 754*, an *IEEE-754 Floating Point Converter* at h-schmidt.net, and the IEEE Standards Association result *IEEE 754-2019*. The resource actually opened was the University of Minnesota-hosted [*IEEE Standard for Floating-Point Arithmetic*](https://www-users.cse.umn.edu/~vinals/tspot_files/phys4041/2020/IEEE%20Standard%20754-2019.pdf). The title page says “Approved 13 June 2019”; the instructor identified it as the revision of the 2008 edition and cautioned that the standard was not generally freely available. The suggested reading route was §2, “Definitions, abbreviations, and acronyms,” for terminology, then §3, “Floating-point formats,” for the layouts.

At 0:06:00, Table 3.2, “Parameters defining basic format floating-point numbers,” compares five formats. Read down **`binary64`**: **`p`** is 53 *base-two digits of precision*, and **`emax`** is +1023, the largest normal exponent. Neither number tells you directly how many physical bits the exponent or fraction occupies. In the decimal columns, the precision is counted in base-ten digits instead.

| Parameter on screen | binary32 | binary64 | binary128 | decimal64 | decimal128 |
|---|---:|---:|---:|---:|---:|
| p, digits | 24 | 53 | 113 | 16 | 34 |
| emax | +127 | +1023 | +16383 | +384 | +6144 |

**Key items to scan**

- **`ieee754`** — the search that led to the 2019 standard. The converter appeared in results; no converter calculation was demonstrated.
- **`binary64`** — the class’s chosen 64-bit binary format; Table 3.2 also includes two other binary widths and two decimal formats.
- **`p`** — significant digits in the format’s own base; 53 binary digits for binary64, not 53 stored fraction bits.
- **`emax`** — maximum normal exponent, +1023 for binary64; not a bit count or the largest number the format represents.

### Find the missing bit: sign, scale, and significant digits (0:06:44)

At 0:06:45, Figure 3.1, “Binary interchange floating-point format,” divides a word into three fields. Read from the most significant end: the first bit gives the sign; the next field encodes the exponent; the remainder stores the *trailing* digits of the significand. The instructor also used the older term *mantissa*. The word selected on screen at 0:08:00 is **significand**.

| Figure 3.1 field | In binary64 | How to read it |
|---|---:|---|
| S, sign | 1 stored bit | 0 for positive, 1 for negative in a normal nonzero value. |
| E, biased exponent | 11 stored bits | For a normal value, subtract the bias (1023) from the stored integer to recover the power of two. The diagram writes the encoding the other way: E = e + bias. |
| T, trailing significand field | 52 stored bits | The figure says *t = p − 1*. For a normal value, the significand begins with a known 1, so only the bits after the binary point need storing. |

The instructor wondered aloud whether the count was off by one. The distinction is **storage versus precision**: 1 + 11 + 52 = 64 stored bits, but the implied leading 1 plus the 52 trailing bits gives 53 bits of precision for a normal value. The page beneath the diagram shows why “normal” matters: an all-zero exponent field is reserved for signed zeros and subnormals; an all-one exponent field is reserved for infinities and NaNs (not-a-number values). Don’t supply an implicit leading 1 to every bit pattern. [Supplemental field interpretation: Oracle, *Numerical Computation Guide*, “Double Format.”](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_math.html)

**Independently verified illustration, not a classroom run:** In binary64, **`1.5`** has sign 0, stored exponent **`01111111111`** (1023), and a trailing field beginning with 1 followed by 51 zeros. Subtracting the bias gives exponent 0. The trailing 1 is in the halves place, so the significand is one plus one-half; scaling by 2⁰ leaves 1.5. The leading one before the binary point was never stored in the trailing field.

**Key items to scan**

- **S / E / T** — sign / biased exponent / trailing significand, in screen order. Separate the fields before interpreting the bits.
- **Significand** — the significant digits; for a normal binary64 value, the first 1 is understood.
- **Storage versus precision** — 52 digits are stored in T; 53 are available to describe a normal significand.
- **`1.5`** and **`01111111111`** — in this added example the stored exponent is 1023, so the actual exponent is zero, not 1023.

## Reading the first truth table (0:08:18)

A 64-bit layout is useful only if we can take the word apart. To introduce the bit operations that will do that work, the instructor located the Numerical project rather than the KivySteps window. Sublime Text showed **`binOp.py`**; PowerShell showed **`git log`** with entries including “IEEE754 64-bit floating point format” and “Binary operations in Python (CLI).” Those were history entries, not commands to switch versions. The command **`py .\src\binOp.py`**, issued from the Numerical directory, was followed by three tables. Start with the one headed **`& and`**.

Read the *output* first. The class returns to the five lines that produced it in “One pair in, one AND row out.”

**Observed output, first table (0:09:40):**

```text
A   B  |  & and
-----------
0   0  |  0
0   1  |  0
1   0  |  0
1   1  |  1
```

The horizontal rule separates labels above from values below. The vertical bar separates inputs on the left from the result on the right. To read a row, stay on that row: the second row says that when A is 0 and B is 1, the result is 0. Only the last row has two 1s, and only that row produces 1. In Python the ampersand performs integer bitwise AND; the vertical bar inside quotes here only *prints a separator*. The other headers, **`| or`** and **`^ xor`**, introduce different operations over the same input pairs. A place-value grid labeled **`data`**, **`mask`**, and **`not`** is visible below; its calculation comes later.

**Key items to scan**

- **`binOp.py`** — the program in Sublime Text; **`py .\src\binOp.py`** is the PowerShell command whose output contains the tables.
- **`git log`** — shows project history, not a new execution of any of the listed revisions.
- **`q = a & b`** / **`& and`** — compute the integer result for one pair; the table prints 1 only for (1, 1).
- **`:<4`** and **`:<3`** — left-align a field to at least four or three characters. This makes the columns readable without changing the values.
- **`'|'`** — a quoted character printed as a divider; **`| or`** names a different *operation* in the next table, and **`^ xor`** labels the third.
- **`data`**, **`mask`**, **`not`** — row labels in the ten-place display below the three one-bit tables.

## Two states, not two magic symbols (0:10:49)

Why use 0 and 1 at all? The instructor’s cue was “twoness”: what is essential to **binary** is having exactly two distinguishable values. The names do not even have to be single characters. The class recalled using *Tom* and *Jerry* in Essentials; the instructor did not say which had been preferred. Pick one value to speak about consistently; with only two possibilities, “not that one” identifies the other. A **preferred value** is a choice of convention, not an inherent property of a glyph.

A computer does not recognize the printed shapes 0 and 1 as numbers in a human sense. The hardware deals with voltage and current. Imagine a light: the physical difference is that it is on or off. We may choose to label “on” as 1 and “off” as 0. Once that choice is fixed, the AND table has a short verbal rule: the preferred result appears when A *and* B are both preferred. In the next table the instructor pointed out that three of the four outputs are preferred. Reading the actual OR rows explains why: either input may be 1, and the case where both are 1 also counts.

**Key items to scan**

- **Binary** — needs two distinguishable values, not specifically the characters 0 and 1; *Tom/Jerry* and *on/off* illustrate the distinction.
- **Preferred value** — the one you choose to name when stating the rule; the displayed tables use 1.
- **AND versus inclusive OR** — both prefer (1, 1); OR also prefers (0, 1) and (1, 0).

### Why four rows? Two coins and two places (0:13:41)

With one coin as A and another as B, each first-coin outcome allows two second-coin outcomes. “Heads then tails” and “tails then heads” are different because the two input positions are different. Two choices in each position give four ordered pairs. Counting in binary lists precisely those pairs: **`00`**, **`01`**, **`10`**, **`11`**. The left place counts twos and the right place counts ones: **`10`** therefore means one two and no ones, not decimal ten. This gives a check on a truth table: even if several rows give the same answer, every input combination still needs a row.

To revisit number systems, the instructor went to Discord’s **`#session`** index. At 0:15:00 it shows [09/14 “numbers”](https://youtu.be/Kku2qmwU-Uk) and [09/17 “functions and number systems”](https://youtu.be/Mn4XZ43JmJ4). These are links displayed in class, not additional sources for this lesson. The key question was not “where is the point?” but “how many digit symbols are available?” A **base** of two gives two digit symbols; a base of ten gives ten, conventionally 0 through 9. The positional counting and arithmetic rules carry over; the place weights and the point at which you must carry follow from the base. *Decimal* names base ten, whether or not a number has a fractional part.

**Key items to scan**

- **`00`**, **`01`**, **`10`**, **`11`** — all two-position binary inputs in counting order; switching the positions can give a different input even if a particular operator returns the same answer.
- **`#session`** — the course’s index for locating the earlier number-system lessons by topic and date.
- **Base** — number of available digit symbols; it determines how place values grow. Decimal means base ten, not “contains a fraction.”

## Four input pairs: their values, their positions, and their count (0:16:00–0:18:32)

With only **`0`** and **`1`** available, what comes after one? **`10`**: the ones digit resets and the next place advances. The class now uses that carry to enumerate inputs without skipping or repeating any.

Now read the first two columns of the terminal truth table as a two-place binary numeral. The right-hand input is the ones place; the input to its left is the twos place. The guide-added notes show the calculation behind each row the instructor reads aloud (0:17:00–0:17:27).

| Inputs A B | Twos place (2¹) | Ones place (2⁰) | Value / zero-based index | Position in the list |
|---|---:|---:|---:|---|
| 0 0 | 0 × 2 | 0 × 1 | 0 | First |
| 0 1 | 0 × 2 | 1 × 1 | 1 | Second |
| 1 0 | 1 × 2 | 0 × 1 | 2 | Third |
| 1 1 | 1 × 2 | 1 × 1 | 3 | Fourth |

**Result:** The last index is three, but there are four pairs. A *cardinal* answers “how many?” An *ordinal* identifies a position, such as “fourth.” An index is the label chosen for a position; here the first position has index zero. The instructor contrasts starting a schoolroom count at one with allowing for “nothing before one,” a useful intuition for zero-based indexing. Neither convention makes the first row disappear. Also, a row's index is **not** its truth-table output: row `10` has index two, but the box can return only zero or one.

**Key items to scan**

- **Decimal** — base ten; not a claim that a numeral has a fractional part.
- **`0`, `1`, `10`** — binary has two digit symbols; `10` represents two, not ten.
- **Twos and ones places** — in `11`, the left digit contributes two and the right contributes one.
- **Cardinal, ordinal, zero-based index** — four rows in total; the fourth row has index three.

## Which “or”? Read the row where both inputs are true (0:18:32–0:21:20)

Hold input A at zero and change B from zero to one. The OR output changes from zero to one. Now hold A at one: either value of B gives one. The instructor calls one the *preferred value* here. To describe the three rows giving that value, say exactly what is true in each: **NOT A and B** for `01`; **A and NOT B** for `10`; **A and B** for `11`. The word *or* connects the three alternatives: any one matching row suffices. The remaining row, `00`, matches none.

**Observed results, rearranged for comparison.** The PowerShell output displays three separate tables; this table places their results side by side without changing their order or values (0:18:40). The “why” column is guide-added.

| A | B | AND | Inclusive OR | Exclusive OR (XOR) | Why OR and XOR agree or differ |
|---:|---:|---:|---:|---:|---|
| 0 | 0 | 0 | 0 | 0 | Neither input is true. |
| 0 | 1 | 0 | 1 | 1 | Only B is true. |
| 1 | 0 | 0 | 1 | 1 | Only A is true. |
| 1 | 1 | 1 | 1 | 0 | Both are true. OR includes this row; XOR excludes it. |

When asked what “one or the other” means, a student understands it to exclude both. That is a valid reading of ordinary English, not the rule for logical inclusive OR. You can settle the ambiguity with one question: *What should happen if both are true?* If the result is one, use inclusive OR; if the intended rule is exactly one true input, the `11` row must give zero.

**Annotated teaching version — guide-added comments.** In the visible **`binOp.py`** editor (0:17:00), each operator gets its own loop over the same ordered pairs. This shorter adaptation computes all three columns on one pass. The displayed operators act on the integer bits zero and one; the words in the terminal headers label them.

```python
for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:  # Visit each pair once, in the screen's order.
    both = a & b       # 1 only on the last pair, where both inputs are 1.
    either = a | b     # 1 on the last three pairs, including (1, 1).
    different = a ^ b  # 1 only on the middle two pairs, where the bits differ.
    print(a, b, both, either, different)  # Display the inputs and the three results for this row.
```

**Independently verified output of this adaptation** (not the classroom print layout):

```text
0 0 0 0 0
0 1 0 1 1
1 0 0 1 1
1 1 1 1 0
```

**Key items to scan**

- **NOT A and B; A and NOT B; A and B** — the three distinct ways to match an inclusive-OR row with output one.
- **`binOp.py`** — the screen version prints separate tables, using the same four pairs for each.
- **AND (`&`)** — requires both inputs; only `11` gives one.
- **Inclusive OR (`|`)** — at least one input; only `00` gives zero.
- **Exclusive OR / XOR (`^`)** — exactly one input; equal inputs give zero, different inputs give one.

## One input, one output: finish the table before naming the box (0:21:36–0:26:50)

The instructor notices a missing case: every table so far takes **two** inputs. In the new editor sketch, **`A`** is the *only input* and **`B`** is the *output*. Do not carry over the old column meanings. A vertical bar separates input from output; a dashed line separates the headings from the data. Neither mark asks Python to calculate anything. With one input bit, only two rows need filling: what comes out for zero, and what comes out for one?

| Observed editor state; approximate target | What is on the page | What the change means |
|---|---|---|
| 0:23:00 | `0` and `1` under A; both B cells blank | The possible inputs are known before the behavior of the box is chosen. |
| 0:24:00 and 0:25:00 | Two tables both read `0 → 0`, `1 → 1` | Duplicating the entries hasn't made a different function yet. |
| 0:26:00 | Four B columns, read top to bottom: `01`, `00`, `11`, `10` | The same two input rows now describe four distinct behaviors. These are pairs of output entries, **not** two-digit inputs to a unary box. |

When the instructor fixes the first row at `0 → 0` and asks about the second, students propose both zero and one. Neither answer is inherently wrong: `1 → 1` gives one rule, and `1 → 0` gives a different rule. A student then asks whether both outputs could be one; another suggests one followed by zero. Together these exhaust the cases. “Both outputs” means entries for **two different input rows**; it does not mean a single input produces both values at once.

A question about computer wiring exposes the difference between a mark on a page and what it represents. The instructor describes current versus no current, or relatively higher and lower current or voltage. The machine need not contain written digits or the words “true” and “false.” Those labels let us reason about two distinguishable states without first describing the hardware.

**Key items to scan**

- **`A`** — the unary input. Its possible values, zero and one, determine the two rows.
- **`B`** — the unary output. Select one value *per row* to specify a rule.
- **`01`, `00`, `11`, `10`** — paired output entries, in the order the four sketches appear; read each pair from top to bottom.
- **Electrical states** — current or voltage distinctions can represent a bit; the symbol is not the physical signal itself.

## Four black boxes, named by what they do (0:26:50–0:30:03)

Put a zero into a sealed device and get zero back. Is it a repeater? Not enough evidence: the device that *always* gives zero passes that test too. Put one in next. If one comes back, the device copies the input; if zero comes back, it is the constant-zero device. This is the point of a **black box**: something inside determines the result, but its internal construction is not needed to specify its behavior. The instructor suggests an electrical circuit, a mechanical device, or an optical device; different implementations can have exactly the same input–output table.

**Worked comparison.** These are the four handwritten tables as a single comparison. The screen labels at 0:29:40 identify them; the class supplies additional spoken names.

| Screen label | With input 0 | With input 1 | Behavior the name identifies |
|---|---:|---:|---|
| `echo` | 0 | 1 | Copy the input. A student proposes “echo”; the instructor adds *repeater* and *buffer*. |
| `zero` | 0 | 0 | A zero generator. Changing the input does not change the output. |
| `one` | 1 | 1 | A one generator. The output stays one, even on input zero. |
| `opposite, not, invert` | 1 | 0 | Flip the input. “Inverse” is another name used in speech. |

**Result:** Each box defines a *function*: give it an allowed input and its rule tells you one output. The instructor singles out NOT as the most useful of these four for building the logic to come. The generators ignore their inputs and the echo passes its input through; NOT makes a new signal that represents the opposite condition. That is an argument about usefulness **in this construction**, not a claim that buffers or constants never have a use.

**Key items to scan**

- **Black box** — identify the input–output rule without needing to open the device; both possible inputs matter here.
- **Echo / repeater / buffer** — returns the same bit, not always the same *constant*.
- **Zero generator; one generator** — return zero or one respectively, regardless of input.
- **NOT / opposite / invert / inverse** — returns the other bit: `0 → 1`, `1 → 0`.
- **Function** — the complete input–output behavior, independent of the mechanism used to realize it.

## From individual boxes to combinations; the two Discord prompts (0:30:09–0:32:39)

Take the output of one box and use it as an input to another: this is *composition*. For example, the earlier phrase “not A and B” already used one result inside a second operation: first reverse A, then combine that result with B. For the class's `01` row, reversing A gives one, and combining it with B (also one) gives one. This checks one condition on one row; it is not a construction of XOR.

The instructor's claim is that **NOT, AND, and OR** suffice to build more complicated Boolean behavior. XOR is useful, but usefulness does not mean it must be a separate primitive. The instructor invokes the scale and speed of a machine—even one running an AI model—to make the distinction between a small set of operations and what many such operations can do together. No circuit implementing XOR is demonstrated here. The automatic captions vary between “NAND and NOR” at 0:30:58 and “NANDs, ANDs, and ORs” at 0:32:26; the earlier statement naming NOT, AND, and OR is clear. No formal list of permitted gates for an assignment is shown.

| Historical class prompt | What the recording supplies | What was left to students |
|---|---|---|
| Count the functionally distinct boxes with two binary inputs and one binary output (0:31:08). | The class answers **16**. At 0:32:06 someone notices “two to the fourth power”; the terminal's place-weight display includes `2^4` above `16`. | Justify the count. The four input **rows** are not themselves sixteen different inputs. |
| Construct exclusive OR from simpler operations (0:32:20). | The earlier truth table gives the behavior to match, notably zero on `11`. | Propose a construction and post about it in Discord. No completed construction or submission is shown. |

The first post for each prompt was invited for “bonus points”; the instructor also joked that the points don't matter. Treat this as a historical invitation, not a verified grading policy. The instructor then turns from boxes to how operations appear in Python.

**Key items to scan**

- **Composition** — feed one result into another operation; the NOT of A can serve as an input to AND.
- **NOT, AND, OR** — the three building blocks named in the instructor's general claim.
- **16 functions versus four input rows** — the first Discord prompt asks *why* the function count is sixteen, not how many pairs fit in a single table.
- **XOR construction** — the second prompt asks for a design that matches the target truth table, not another name for its output.

## From a printed truth table back to its Python code (0:32:40–0:40:40)

The terminal already has the answer: with two one-bit inputs, only the last AND row produces 1. The instructor turns the question around: *What Python produced those columns and those four results?* There are two jobs here. Formatting tells Python **where to display** a value; a bitwise operation determines **which value to display**. A student's question about a colon opens the first job.

### Why are there three spaces after A? (0:33:10–0:36:08)

**Annotated teaching version — guide-added comment; header from the editor at 0:33:00:**

```python
# Start with two newlines, then put the four headings in adjacent fields.
print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")
```

**Observed heading** (PowerShell, 0:32:45; blank lines before the heading omitted here):

```text
A   B  |  & and
```

The **`f`** before the opening quote makes this a formatted string. Inside it, ordinary text is copied, but **`\n`** stands for one newline, not the two printed characters backslash and n. There are *two* of them before the heading. Each **`{...}`** encloses a replacement field. The part before its colon supplies a value; here **`'A'`** is a quoted string, not a variable that has to be defined. The **`:`** introduces instructions for displaying that value. The quotes, braces and colon don't appear in the result.

Read **`{'A':<4}`** as “put A against the left edge of a field at least four characters wide.” A takes one position, so three spaces follow it. B takes one position in a field of three, leaving two spaces. The vertical bar is another *quoted character* in its own three-position field. The fields touch: there are no extra separator spaces typed between the closing and opening braces. The class asks about the other direction: **`>`** puts unused space *before* the value instead.

**Independent spacing check — guide-added brackets mark the field edges:**

```python
print(f"[{'A':<4}][{'A':>4}][{'& and':<4}]")  # Compare left, right and over-width fields.
```

**Verified stdout:**

```text
[A   ][   A][& and]
```

Notice that the five-character label **`'& and'`** survives even though it was given width four. Width is a minimum, not a cut-off. This explains both the advantage and the limit of the technique: changing from one digit to two need not shift the following column if the value still fits; exceeding the reserved width can shift it. That is easier to maintain than moving hand-typed spaces back and forth.

**Key items to scan**

- **`f`**, **`{...}`** — mark the string as formatted and enclose each value to insert.
- **`\n`** — a newline; the header begins with two before A. The enclosing print adds the newline *after* the heading.
- **`'A'`**, **`:`**, **`{'A':<4}`** — supply the literal A, start its format specification, and put three spaces on its right.
- **`{'B':<3}`** — B followed by two spaces. **`{'|':<3}`** — a visible divider followed by two spaces; it does not compute OR here.
- **`>`** — right alignment; a one-character value in a four-character field has three spaces before it.
- **`'& and'`** — a five-character printed label, not the AND calculation. A minimum width of four does not shorten it.

### Why does multiplication draw a rule? (0:36:08–0:38:14)

The next line draws the rule through the first result position, not the whole width of the longer heading. The class recognizes **`*`** as multiplication; the instructor asks what that can mean when one side is text. The answer depends on the types of the operands.

**Annotated teaching version — first statement from class; second is a guide-added contrast:**

```python
print("-" * 11)  # A one-character string repeated eleven times, then printed once.
print(2 * 11)    # Two integers instead: compute and print their product.
```

**Verified stdout:**

```text
-----------
22
```

The first expression produces *one new string*, not eleven calls to print. Its meaning is an example of **operator overloading**: the same notation has behavior defined for the types involved. The instructor connects this with a previous class example: their custom item class supported indexing with square brackets because they defined what indexing should do for those objects. Python knows how its built-in types behave; it doesn't infer what selecting a position in a new custom structure is meant to return. No new indexing implementation is shown here.

**Key items to scan**

- **`*`**, **`"-" * 11`** — a string and an integer produce eleven hyphens; two integers produce a numeric product instead.
- **Operator overloading** — one operator spelling, type-specific behavior. Indexing with **`[]`** on the earlier custom class is the instructor's comparison.

### One pair in, one AND row out (0:38:14–0:40:40)

The four possible inputs are written down in order. A student correctly predicts that the code puts the two values into variables, ANDs them, and prints the result each time. The remaining question is what those two names actually *are*. Follow the whole first table: the heading and rule print once, while only the indented statements repeat.

**Annotated teaching version — complete first table, guide-added comments:**

```python
# Print the blank lines and the heading once, before any input pairs are read.
print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")
print("-" * 11)  # Print the rule once, not between rows.
# Read one pair at a time; bind its first integer to a and its second to b.
for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
    q = a & b  # Compute a fresh result each pass; only 1 & 1 gives 1.
    # Print current integers, not the letters a, b and q. The pipe stays literal.
    print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
```

**Classroom command observed in PowerShell at 0:32:45:** `py .\src\binOp.py`  
**Observed result — first table only** (leading blank lines and invisible trailing padding not shown):

```text
A   B  |  & and
-----------
0   0  |  0
0   1  |  0
1   0  |  0
1   1  |  1
```

**Independent run:** Executing the excerpt reproduced the heading, rule and four rows from the five-statement excerpt. Its stdout starts with two newlines and has three padding spaces after each one-digit result. This separate check did not run the instructor's full project.

**`for a, b in`** doesn't start two independent loops. The outer square brackets hold a list of four items, and each item is an ordered two-element tuple. The loop obtains the *next pair*; then the comma-separated targets unpack that pair by position. On the second pass, for example, **`(0, 1)`** gives **`a`** the integer 0 and **`b`** the integer 1. The integer operator **`&`** keeps a result bit only when both input bits are 1. Thus **`q`** is rebound to 0 on this pass; the input tuple has not changed. The final colon on the loop line starts its indented body. It does not specify a field width as the colon inside an f-string did.

**Worked trace — first table; results checked independently:**

| Pass | Pair obtained | Names after unpacking | Value assigned to q | What the row shows |
|---|---|---|---|---|
| 1 | `(0, 0)` | `a=0`, `b=0` | `0 & 0` → `0` | Neither bit is 1. |
| 2 | `(0, 1)` | `a=0`, `b=1` | `0 & 1` → `0` | A 1 on the right alone is insufficient. |
| 3 | `(1, 0)` | `a=1`, `b=0` | `1 & 0` → `0` | A 1 on the left alone is insufficient. |
| 4 | `(1, 1)` | `a=1`, `b=1` | `1 & 1` → `1` | Both bits are set. |
| Next request | No more pairs | No new assignment | No recalculation | The loop ends; no fifth row prints. |

The classroom distinction at 0:39:18 matters: lower-case **`a`** and **`b`** are *identifiers* (names chosen by the programmer) used as variables, not the iterator that supplies the pairs. The instructor says they will check whether there is a more specialized name; no answer to that follow-up is reported later in this recording. For reading the code, *assignment targets* describes their job. The list holds pairs already packed together; each pass unpacks one. That distinction between receiving one whole pair and receiving its two components sets up the next edit.

**Key items to scan**

- **`for a, b in`** — take one tuple from the list and assign its two elements to two names; the final colon opens the repeated block.
- **`(0, 1)`**, **`a`**, **`b`** — on pass two, first target 0, second target 1. Uppercase A and B in the heading are only labels.
- **`&`**, **`q`** — calculate integer bitwise AND and store the result for this row. The letter q is never itself printed.
- **`{q:<4}`** — display the current value left-aligned, just as the other row fields are aligned. Formatting does not alter the integers.
- **Identifier vs. iterator** — the names receive the current values; iteration is the process that supplies successive pairs, then stops.

## What does one trip through the truth-table loop assign? (0:40:40–0:48:59)

### Two errors, two different stages (0:40:40–0:42:04)

The truth-table code normally takes two inputs at a time. The instructor removed the second name from the first loop, leaving **`for a in ...`**. Would the assignment work? The first run of **`py .\src\binOp.py`** didn’t answer that question. Python rejected a line much farther down the file, before executing even the first print. The AND, OR and XOR tables already in the terminal were older output.

Observed at 0:41:00, after the command:

```text
  File "C:\com\joesta\Numerical\src\binOp.py", line 62
    A | B echo
          ^^^^
SyntaxError: invalid syntax
```

**`A | B echo`** is not a valid Python statement. The caret marks **`echo`**, not the loop. The instructor said to make Python ignore that text. At 0:41:08 an opening triple-double-quote delimiter appears above the scratch table. Inside a multiline string the table is text, not code; the matching closing edit is not visible at this point. Reaching line 62 while *parsing* the file does not mean that lines 1–61 ran. An isolated check shows the distinction:

Annotated teaching version — guide-added comments; short parser test, not the classroom file:

```python
print("started")  # Would print only if the file could be parsed.
A | B echo        # Invalid syntax; even the earlier print never runs.
```

Independently verified: no output; **`SyntaxError`**. On the next classroom run the heading and divider *did* print. The changed line 3 had accepted the first pair, but the body still required a name that it no longer assigned.

Annotated teaching version — guide-added comments; opening excerpt of **`binOp.py`** (0:41:38–0:42:00):

```python
print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")  # Original AND column labels.
print("-" * 11)                                   # Print eleven dashes before the loop.
for a in [(0, 0), (0, 1), (1, 0), (1, 1)]:        # First pass assigns the whole (0, 0) to a.
    q = a & b                                     # b has no value; q is never assigned.
    print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")           # Not reached; no data row prints.
```

Observed (0:41:38; full line-4 diagnostic also visible in 0:42:55 scrollback). Initial blank lines omitted here and in later output excerpts:

```text
A   B  |  & and
-----------
Traceback (most recent call last):
  File "C:\com\joesta\Numerical\src\binOp.py", line 4, in <module>
    q = a & b
            ^
NameError: name 'b' is not defined
```

Python must look up both operands before it can perform **`a & b`**. This is a **`NameError`** for missing **`b`**, not evidence that a tuple cannot be assigned to **`a`**. The failing line moved from 62 to 4 because a different stage of the program was now reachable.

**Key items to scan**

- **`py .\src\binOp.py`** — the PowerShell command starts the Python file afresh; earlier terminal rows remain visible.
- **`A | B echo`**, **`echo`**, **`SyntaxError`** — invalid source at line 62; no statement of that run executes.
- **`binOp.py`**, **`for a in ...`**, **`a`** — line 3 assigns an entire pair to one name; the assignment itself succeeds.
- **`b`**, **`a & b`**, **`NameError`** — line 4 needs a second operand that this version never defined; no result is assigned to `q`.

### Test the first-member prediction (0:42:04–0:44:00)

A student predicted that printing **`a`** would show the first number in each pair. To find out, the instructor replaced the calculation with **`print(a)`** and commented out both the AND and its formatted row. The old heading still said AND; the body now displayed the data it received, not a truth value.

Annotated teaching versions — guide-added comments. These are **two separate runs** of the first loop; their unchanged heading and divider are omitted. Each output block shows only this loop’s rows.

```python
for a in [(0, 0), (0, 1), (1, 0), (1, 1)]:  # Take one item of the outer list at a time.
    print(a)                                 # One name holds the whole tuple, not its first number.
```

**Observed output at 0:42:55:**

```text
(0, 0)
(0, 1)
(1, 0)
(1, 1)
```

```python
for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:  # Split each current pair between a and b.
    print(a)                                    # Only show the first number; b still receives the second.
```

Observed in the 0:44:00 terminal, consistent with the **two-name** editor at 0:43:32, not with the three-name edit already visible at 0:44:00:

```text
0
0
1
1
```

The student’s prediction fits the *second* loop. On its second pass, the outer list supplies one object, **`(0, 1)`**. With one target, that object is the value of `a`. With **`for a, b in ...`**, Python takes the pair apart, assigning `0` to `a` and `1` to `b` before entering the body. That second step is *unpacking*. Neither loop edits the list or the tuples; the names take on new values on each pass.

**Key items to scan**

- **`a`**, **`print(a)`** — same print statement, different output because the loop header changed what `a` receives.
- **`(0, 1)`** — the second outer item: one tuple for one name, two component values for two names.
- **`for a, b in ...`** — unpack first, then run the body; four pairs yield four first members: `0`, `0`, `1`, `1`.

### Change the number of values, then the number of names (0:43:48–0:46:23)

A student’s question about a third number was initially understood as a request to add a third *name*. Those are different experiments. A tuple can certainly contain more than two values. What matters for these comma-separated targets is whether the **current item** supplies exactly one value per name. The check happens anew on each pass, before the body gets to print anything.

Annotated teaching versions — guide-added comments; each snippet is a fresh run of the affected loop. First, the 0:44:00 editor briefly shows the extra name with the **original pairs**:

```python
for a, b, c in [(0, 0), (0, 1), (1, 0), (1, 1)]:  # The first pair cannot fill three names.
    print(a)                                       # Never reached, even though only a would print.
```

Independent result: no data rows; `ValueError: not enough values to unpack (expected 3, got 2)`. The four single digits on screen at 0:44:00 are from the preceding two-name experiment, not this run.

The student clarified: leave two names, but add values to the data. The first two tuples gained `8` and `7`; the last two stayed pairs.

```python
for a, b in [(0, 0, 8), (0, 1, 7), (1, 0), (1, 1)]:  # Three values arrive for two names on pass 1.
    print(a)                                          # Never reached; nothing is silently discarded.
```

Observed at 0:44:31, immediately after the heading and divider, with **no data rows**: `ValueError: too many values to unpack (expected 2)`. The traceback quotes the loop header, not the print statement. The body above is a teaching reconstruction; its text cannot affect this first-pass failure.

Adding the third target **`c`** fixes the first two assignments. Notice a small but revealing difference between what the loop assigns and what its body displays. At 0:44:56 the editor was partway through changing the print:

```python
for a, b, c in [(0, 0, 8), (0, 1, 7), (1, 0), (1, 1)]:  # Three names still receive three values.
    print(a, b ,)                                        # Only two arguments: print 0 0, then 0 1.
```

Independent check of this editor state (not a demonstrated run): it prints `0 0` and `0 1`, then raises `ValueError: not enough values to unpack (expected 3, got 2)`. The trailing comma in a function call does not add a third argument. The completed 0:45:00 version prints all three:

```python
for a, b, c in [(0, 0, 8), (0, 1, 7), (1, 0), (1, 1)]:  # First two items fit; third is short.
    print(a, b, c)                                       # Print three separate values with spaces.
```

Observed at 0:45:16 (traceback location omitted here):

```text
0 0 8
0 1 7
ValueError: not enough values to unpack (expected 3, got 2)
```

| Pass | Current item | What reaches the body? |
|---|---|---|
| 1 | `(0, 0, 8)` | `a=0`, `b=0`, `c=8`; prints `0 0 8`. |
| 2 | `(0, 1, 7)` | `a=0`, `b=1`, `c=7`; prints `0 1 7`. |
| 3 | `(1, 0)` | Two values cannot fill three names. No third row prints. |
| 4 | `(1, 1)` | Never fetched; the third assignment already stopped the run. |

The two rows do not disappear when a later row fails. Nor does the loop inspect the entire list in advance. The repair is to put a third value into **each** remaining pair; the class used `6` and `23`.

```python
for a, b, c in [(0, 0, 8), (0, 1, 7), (1, 0, 6), (1, 1, 23)]:  # Now all four items fit.
    print(a, b, c)                                             # Show all three members, even 23.
```

The complete loop header and body are visible at 0:45:29; the following four rows were observed at 0:45:31 and 0:45:35 and independently verified:

```text
0 0 8
0 1 7
1 0 6
1 1 23
```

One final test answered the question “Would two names just print the first two?” At 0:46:00 there was briefly a trailing comma in the **target**, not in the print call:

```python
for a, b, in [(0, 0, 8), (0, 1, 7), (1, 0), (1, 1)]:  # Still only two names; no unnamed third slot.
    print(a)                                          # The first triple fails before even this one print.
```

Independent check: no rows and a too-many-values error. By 0:46:16 the comma after `b` was gone; the header was again the two-name version tested at 0:44:31. Its classroom rerun failed on the same first triple. Printing fewer values is not the same as accepting more values at assignment.

**Key items to scan**

- **`c`** — a third destination for a third member; adding a name without a value makes a pair too short.
- **`ValueError: too many values to unpack (expected 2)`** — classroom wording; `(0, 0, 8)` is too long for `a, b`.
- **`print(a, b ,)`** — intermediate edit: two arguments, not three; no classroom execution established for that edit.
- **`print(a, b, c)`** — displays values already assigned; the two good rows survive the third row’s failure.
- **`ValueError: not enough values to unpack (expected 3, got 2)`** — `(1, 0)` fails before the third print; the fourth item is never attempted.
- **`(1, 0, 6)`**, **`(1, 1, 23)`** — filling both short tuples lets the body print four times.
- **`print(a)`** — changing the print cannot repair a mismatch that happens before it executes.

### Does a list unpack differently from a tuple? (0:46:29–0:48:59)

The instructor changed the first inner item to the list **`[0, 0, 8]`** and left the second as the tuple **`(0, 1, 7)`**. The *outer* container was still a list of four items. This time the loop asked for three names but printed only the first. The zeros under the AND heading are evidence of two successful assignments, not of two AND calculations.

Annotated teaching version — guide-added comments. First-loop source visible at 0:48:00; two rows and the start of its traceback visible at 0:46:57–0:47:43:

```python
for a, b, c in [[0, 0, 8], (0, 1, 7), (1, 0), (1, 1)]:  # One list, then three tuples.
    print(a)                                            # Zero from the list, then zero from the tuple.
```

```text
0
0
```

The next item, `(1, 0)`, still has no third member. The instructor identified it as the failure; the screenshot cuts off the end of this traceback. Independently verified for the exact loop above: `ValueError: not enough values to unpack (expected 3, got 2)`. There is no fourth iteration. What mattered was whether each object could supply three values, not whether its punctuation was round or square.

At 0:47:11 a student narrowed the question to two names and just the three-number list. Here is that *hypothetical* made runnable, keeping one inner item inside the outer list:

```python
for a, b in [[0, 0, 8]]:  # The outer list supplies one inner list containing three numbers.
    print(a, b)           # No row: there are only two destinations for those three numbers.
```

Independent result: a too-many-values **`ValueError`**, just as for the triple of numbers in parentheses. The instructor grouped lists, tuples and sets as examples of *iterables*—objects from which Python can request successive values. Only the list and tuple contrast was demonstrated here. By 0:48:50 the editor had returned to the earlier one-name loop and active AND calculation; the two zeros still in the terminal are not a run of that restored code. With the assignment question settled, the class returned to why the truth-table program had been written.

**Key items to scan**

- **`[0, 0, 8]`** — inner list: fills all three names and contributes the **first** printed `0`.
- **`(0, 1, 7)`** — inner tuple: also fills all three names and contributes the **second** printed `0`.
- **`(1, 0)`** — first item that cannot fill three names; the body does not print `1`.
- **`[[0, 0, 8]]`**, **`ValueError`** — one inner iterable is still too long for two names. Count the values in the item being assigned, not the number of items in the outer container.

## 0:49:00 — Restore the two inputs and read the result column

Where did the three zeros in the first AND row come from? The first two were **inputs**: the loop took them from the pair **`(0, 0)`**. The third was a **result**: **`q = a & b`** compared the bits and produced zero. The distinction matters because the demonstration had temporarily changed the loop to **`for a in ...`**. That gives the *whole pair* to one name; it does not give its two integers to two names. The instructor noticed the missing **`b`** and restored two targets. The older unpacking tracebacks still visible at 0:49:00 are not evidence that this intermediate one-target line ran.

Annotated intermediate excerpt — selected editor text at 0:49:00; guide-added comments, not a recorded run:

```python
for a in [(0, 0), (0, 1), (1, 0), (1, 1)]:  # First pass: a is the pair (0, 0), not the integer 0.
    q = a & b                                # This loop has supplied neither two integer operands nor a new b.
```

Annotated teaching version — restored AND block visible at 0:50:00; comments added:

```python
print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")  # Label input, separator and result columns.
print("-" * 11)                                      # Print a divider, not a calculation.
for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:      # Unpack one pair for this row.
    q = a & b                                        # Both bits must be 1 for q to be 1.
    print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")             # Display the inputs and the computed q.
```

The next two tables use the same four pairs and the same layout. The one character *between the operands* changes the operation. The pipe in a quoted field is only a printed separator; the unquoted pipe in **`q = a | b`** computes OR. The unquoted caret in **`q = a ^ b`** computes exclusive OR. The following excerpts belong to separate loops (0:50:35–0:51:00); the table below aligns their output for comparison.

```python
q = a | b  # OR loop: either bit being 1 suffices, including when both are 1.
```

```python
q = a ^ b  # XOR loop: exactly one bit must be 1; two 1s give 0.
```

At 0:53:00 the command **`py .\src\binOp.py`** and all three complete tables are visible together. The output columns are unchanged from the earlier OR/XOR comparison: reading downward, AND gives `0001`, OR `0111`, XOR `0110`. The important repair was restoring the two operands, not changing a truth rule.

**Key items to scan**

- **Inputs** — **`(0, 0)`** supplies two zeros, not the third zero printed in the first AND row.
- **`for a in ...`** — one name receives a whole pair; the repaired loop also binds **`b`** by unpacking.
- **Result** — **`q = a & b`** assigns the bitwise AND before the row is printed.
- **`q = a | b`** — the pipe between integers computes OR; a quoted pipe prints a character.
- **`q = a ^ b`** — the caret computes XOR, so the final row differs from OR.
- **`py .\src\binOp.py`** — the visible PowerShell command runs the script; the return of the prompt below the rows marks completion.

## 0:51:15 — Print one integer as a row of ten bits

The truth tables used only zero and one, but Python's bitwise operators work across *all* corresponding positions of two integers. To see those positions separately, the instructor showed **`displayRow`**. Give it the integer 1000 and the highest position 9: it prints positions 9 through 0, left to right. It does not change 1000 or return a string of bits. The work happens when the function is called, not when its definition is read.

Annotated teaching version — visible at 0:51:45 and 0:52:00; comments added:

```python
def displayRow(val, msb, label=""):       # val is the integer; an omitted label means empty text.
    for i in range(msb, -1, -1):          # With msb = 9, visit 9, 8, …, 0; stop before -1.
        bit = (val >> i) & 1              # Move position i to the right edge, then keep only that bit.
        print(f"{bit:<6}", end='')        # Print 0 or 1 left-aligned in a field six characters wide; stay on this row.
    print(label)                          # Once, after all bits: print the label and finish the line.

msb = 9                                  # Highest bit index, not a count of nine columns.
n = 1000                                 # Data integer, written in decimal.
mask = 0x0f0                             # Hexadecimal 240: its 128, 64, 32 and 16 positions are set.
```

Why are both operations needed in **`(val >> i) & 1`**? A right shift by four moves the original 16s bit to the units position, but the higher bits move with it: 1000 shifted right four is 62, not a single bit. AND with 1 discards everything except that last bit. Since 62 is even, the bit is zero. At the next position, 1000 shifted right three is 125, which ends in a one bit. Each pass starts again with the original 1000. Here “binary operator” means *takes two operands*: the integer to shift and a nonnegative integer shift count. You do not need to write the first integer using binary digits. A shift is legal even on a one-bit value: one shifted right three is zero; it simply makes a less informative demonstration.

Guide trace, independently executed with the class values. Read the final column top to bottom to get the observed *data* row.

| Position i | 1000 shifted right by i | After AND with 1 |
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

**Key items to scan**

- **`displayRow`** — prints one bit per column and then the row label; no explicit return value.
- **`val`** — unchanged source integer for every pass; shifting it in an expression does not reassign it.
- **`msb`** — 9 names the most significant *displayed* bit; index 0 is the least significant.
- **`range(msb, -1, -1)`** — count down through zero; the stop value is excluded.
- **`(val >> i) & 1`** — position the chosen bit at the units place, then isolate it with a one-bit mask.
- **`end=''`** — successive bit prints stay on one line. **`print(label)`** finishes that line even if the label is empty.
- **`mask = 0x0f0`** — a separate four-position mask for the later operations, not the single 1 used to extract a bit.

### 0:52:00–0:53:35 — Read the headings, then read down each bit column

The first heading gives names such as **`2^9`**; the second gives their decimal weights, starting at 512. Although a caret is visible in the first row, the program isn't calculating with it: it is ordinary text inside a string. The arithmetic in the second loop is **`2**i`**, Python exponentiation. Six-character columns line up the power, its weight, and all the data bits beneath it.

Annotated teaching version — visible heading code; comments added:

```python
print()                            # Blank line before the multibit table.
for i in range(msb, -1, -1):       # Go from the 512s place to the ones place.
    print(f"2^{i:<4}", end='')     # Print literal '2^', then the index in a four-character field.
print()                            # Finish the row of power labels.

for i in range(msb, -1, -1):       # Start at 9 again, in the same column order.
    print(f"{2**i:<6}", end='')    # Compute and display 512, 256, …, 1, one weight per column.
print()                            # Finish the row of weights.
```

Classroom terminal, 0:53:00 (spacing condensed; row order, labels and bits preserved):

```text
2^9 2^8 2^7 2^6 2^5 2^4 2^3 2^2 2^1 2^0
512 256 128  64  32  16   8   4   2   1
  1   1   1   1   1   0   1   0   0   0   data
  0   0   1   1   1   1   0   0   0   0   mask
  0   0   0   0   0   1   0   1   1   1   not
  0   0   1   1   1   0   0   0   0   0   and
  1   1   1   1   1   1   1   0   0   0   or
  1   1   0   0   0   1   1   0   0   0   xor
  1   1   0   1   0   0   0   0   0   0   left shift 3
  0   0   0   1   1   1   1   1   0   1   right shift 3
```

**Annotated teaching version — guide-added comments.** At 0:23:00 the fourteen driver statements below were already visible above the handwritten unary table. Together with the visible helper and class values, these exact statements independently reproduce all eight rows shown in the later completed run. The decimal interpretations are guide calculations, not printed classroom values. Notice that **`result`** is reassigned before each call; it is not a running total.

```python
displayRow(n, msb, "data")              # Print 1000 across ten positions; then finish the line with data.
displayRow(mask, msb, "mask")           # Print 240; its four ones mark positions 7 through 4.
result = ~n                             # Flip every bit; the full integer is -1001, but its low ten bits encode 23.
displayRow(result, msb, "not")          # Display that result and its label; n and mask have not changed.
result = n & mask                       # Keep selected data bits: 128 + 64 + 32 gives 224.
displayRow(result, msb, "and")          # Display that result and its label; n and mask have not changed.
result = n | mask                       # Set the four selected bits; setting the 16s bit makes 1016.
displayRow(result, msb, "or")           # Display that result and its label; n and mask have not changed.
result = n ^ mask                       # Toggle the four selected bits: the full integer becomes 792.
displayRow(result, msb, "xor")          # Display that result and its label; n and mask have not changed.
result = n<<3                           # Shift left three positions: 8000; the ten visible bits encode 832.
displayRow(result, msb, "left shift 3") # Display that result and its label; n and mask have not changed.
result = n>>3                           # Shift right three positions: 125.
displayRow(result, msb, "right shift 3")# Display that result and its label; n and mask have not changed.
```

To predict a masked result, read *down one column* using the earlier one-bit truth table. At weight 128, data and mask both have 1: AND and OR give 1, XOR gives 0. At weight 16, data has 0 and mask has 1: AND gives 0, OR and XOR give 1. Now read *across* the row: add the weights over its ones to recover the displayed nonnegative value. The width is a display choice, not a limit on Python integers. Higher bits of 8000 lie outside the left edge; the negative complement is not a positive integer merely because its ten displayed bits sum to 23.

**Key items to scan**

- **`2^9`** — a printed power label; **`2**i`** computes the actual column weight in Python.
- **`result`** — reused name for each new operation. The earlier source shows the intermediate assignment and a separate call to print it.
- **`~n`** — flips the data bits; its full integer value and its low ten bits have different interpretations.
- **`n & mask`** — retain selected data bits; **`n | mask`** — set selected bits; **`n ^ mask`** — toggle selected bits.
- **`n << 3`** — shift left three places; output can extend above displayed position 9.
- **`n >> 3`** — shift right three places; with the positive integer 1000 the result is 125.

## 0:53:35–1:00:35 — How can “100” be four, sixty-four, or one hundred?

The output labels its fours place with a decimal 4. The instructor asked for the *weight of that place written in binary*, not its decimal translation. To separate those questions, the class switched to decimal 532 (0:54:14): **the digit** in the hundreds place is 5, **the place weight** is 100, and **the contribution** of that digit is 500. The hundreds place keeps its weight even in a number with a zero in that position.

A student suggested **`001`**, then pointed out that with only a twos place and a ones place, the greatest value is three. That second observation is exactly right: **`11`** in binary is one two plus one one. The missing distinction was between the *number of possible digits at each position* and the *number of positions you can write*. Binary permits only the digits 0 and 1, not just two positions. Add a third position on the **left**: **`100`** in binary is one four, no twos, no ones. A leading zero does not move the lone one in `001` out of the units place.

The editor highlights `100` at 0:55:40 and 0:56:00. Just above it is an earlier handwritten rule, `A | B opposite, not, invert`, with rows `0 | 1` and `1 | 0`: every input bit has exactly one opposite. Those marks and the selected numeral are editor notes, not an instruction to execute `100` or output from a conversion program. By 0:57:00 the terminal highlights the three rightmost power headings. The leftmost of those three is the *base squared* position, not necessarily the decimal hundreds position. Here's the same written pattern in the three bases discussed; decimal values are independently checked.

| Written pattern and base | Three place weights in decimal, left to right | Value in decimal |
|---|---|---:|
| `100` in binary (base 2) | 4, 2, 1 | 1 × 4 + 0 × 2 + 0 × 1 = 4 |
| `100` in decimal (base 10) | 100, 10, 1 | 1 × 100 + 0 × 10 + 0 × 1 = 100 |
| `100` in octal (base 8) | 64, 8, 1 | 1 × 64 + 0 × 8 + 0 × 1 = 64 |

The class's resolution at 0:59:18–1:00:30: start with exponent zero at the right, increase it by one at each step left, and use **the same base in every column**. The exponents stay 0, 1, 2, 3, … when you switch from binary to octal; the multiplier changes from two to eight. Saying “the 64s place” for octal already translates its weight into decimal. Written in octal itself, that weight is `100`.

**Key items to scan**

- **The digit** — 5 is the symbol occupying the hundreds place of 532.
- **The place weight** — 100 is the weight of that position in decimal, regardless of the occupying digit.
- **The contribution** — 5 hundreds contribute 500 toward 532.
- **`001`** — one in binary, because its only 1 is at the right; **`11`** is three, the largest two-bit value.
- **`100`** — one base-squared unit: four in binary, sixty-four in octal, one hundred in decimal.
- **The same base in every column** — add more positions as needed; do not change multipliers midway through a numeral.

### 1:00:37–1:01:58 — What was new in Python?

Place weights were familiar to some students from Essentials and earlier problem solving. For someone joining near the end, the instructor identified the new application: integer operations in Python. **`&`**, **`|`** and **`^`** are not three spellings of logical “or” or “and.” Each applies its own truth-table rule independently at every corresponding bit position. A reliable way back into the lesson is to predict the `(1, 1)` row, then use exactly that rule on the 128s column of the data and mask.

**Key items to scan**

- **`&`** — both bits set: `(1, 1)` keeps the 128s bit.
- **`|`** — at least one bit set: `(1, 1)` also keeps it.
- **`^`** — exactly one bit set: `(1, 1)` clears it.

## Syntax to remember

| Pattern | Read it as |
|---|---|
| `f"{'A':<4}"` | Put the literal `A` at the left of a field at least four characters wide. The colon starts the format specification; it is not printed. |
| `"-" * 11` | Repeat one string eleven times to make a rule. Here the asterisk means repetition, not numerical multiplication. |
| `for a in rows:` | Bind the next **whole** row to `a`, regardless of how many items that row contains. |
| `for a, b in rows:` | Get one row, unpack exactly two items into the targets, then enter the loop body. A mismatch interrupts this sequence. |
| `a & b` | On integers, keep a 1 only where both operands have a 1. |
| <code>a &#124; b</code> | On integers, keep a 1 wherever either operand has a 1, including both. A vertical bar inside quoted table text is just a printed character. |
| `a ^ b` | On integers, keep a 1 where the operands differ. In Python, exponentiation is `**`, not `^`. |
| `(value >> position) & 1` | Shift a chosen bit to the ones place, then discard every bit except that one. This expression gives an integer; it does not print anything by itself. |

## Knowledge check

1. The binary64 table says **53** digits of precision, and the diagram shows one sign bit and eleven exponent bits. Why isn’t this a 65-bit format?
2. A black box gives 0 for input `00` and 1 for inputs `01` and `10`. Which input do you still need to distinguish OR from XOR? Why can four possible inputs describe sixteen different functions rather than just four?
3. Predict what `for a in [(0, 1)]: print(a)` displays. What changes if the target is `a, b` and the body is `print(a, b)`? Did Python change the tuple in either case?
4. A three-target loop receives `(0, 0, 8)`, `(0, 1, 7)`, then `(1, 0)`, in that order. Its body prints the three variables. Which lines appear, and where does the error occur?
5. In the ten-column display, the data is 1000 and the mask is 240. At the 16s place the data bit is 0 and the mask bit is 1. Predict the AND, OR and XOR bits at that place. Why does displaying only ten positions matter for the left-shift row?
6. A classmate says, “There are only two digits in binary, so there can’t be a fours place.” Use `100` in base two and base eight to show where the reasoning goes wrong.

### Answer key

1. The 53 counts **precision**, not 53 separately stored trailing bits. A normal binary64 number has an implicit leading 1 and 52 stored trailing bits. The stored fields therefore occupy **1 + 11 + 52 = 64** bits. The normal-number qualification matters; subnormal numbers do not have that leading 1.
2. Test `11`. Both inputs are preferred, so inclusive OR returns 1 and XOR returns 0. The four input rows are fixed. A different function can choose 0 or 1 independently for each output row: two choices made four times gives sixteen possible output columns.
3. The first loop prints `(0, 1)` with the tuple’s punctuation. The second binds `a` to 0 and `b` to 1; `print(a, b)` displays `0 1`. Unpacking binds names; it does not dismantle or modify the tuple.
4. `0 0 8` and `0 1 7` print. The third item has no third member. Unpacking raises `ValueError` **before** the body runs for that row. The earlier printed lines do not disappear, and a later row is not reached in that run.
5. AND gives **0** (not both); OR gives **1** (at least one); XOR gives **1** (different). Left-shifting 1000 by three produces the integer 8000. A display of only positions nine through zero omits higher set bits; that is a limit of the display, not an overflow of the Python integer.
6. Two is the number of available **symbols**, not the number of **positions**. Moving left from the ones place multiplies the weight by the base each time. The third place therefore weighs four in binary and sixty-four in octal. The written pattern `100` puts one copy of that weight in the total.

## Library of definitions

| Term | Meaning in this session |
|---|---|
| Bit; bit position | A bit has two possible values. Its position determines the weight it contributes to an integer: the 1 at the right of `001` contributes one, whereas the 1 in `100` contributes four in binary. |
| Base (radix); digit; place weight | The base specifies the digit alphabet size for ordinary positional notation. A digit counts copies of a weight; positions from right to left have weights given by successive powers of the base. |
| Count, order and index | A cardinal number says **how many**; an ordinal says **which in order**. An index labels a position according to a convention. Starting at index 0 doesn’t reduce the number of rows. |
| Floating-point format | A specified way to encode a sign, significant digits and scale. For normal binary numbers, the scale is a power of two. This is different from storing an ordinary integer as bits with fixed place weights. |
| Significand; precision; trailing field | The significand holds the significant digits (sometimes called the mantissa in class). Precision counts those digits, including an implicit leading digit when applicable; the trailing field stores only the trailing part. |
| Biased exponent | A stored field encoding a scale exponent using an offset. A stored exponent field is not simply the signed exponent written into the bits. |
| Truth table; preferred value | A truth table describes every allowed input combination and its output. Choosing 1 as the preferred value lets you describe a function by naming precisely the rows where it returns 1. |
| Unary; binary operator | Unary describes an operation on one operand; binary here means **two operands**. Neither is a restriction on the number of bits in each operand. |
| Black box; buffer; inverter | A black box is characterized by observable input and output without assuming its construction. A buffer echoes its input; an inverter gives the other bit. |
| Bitwise operation; mask | A bitwise operation acts on matching bit positions. A mask is a pattern chosen to select or affect particular positions; AND with a 1 preserves the corresponding data bit, AND with a 0 clears it. |
| Field width; alignment | For printed text, width sets a minimum space for the value and alignment controls where padding goes. This **format field** is not a fixed-width bit field in IEEE 754. |
| Iterable; iterator; loop target | An iterable supplies values for iteration. An iterator tracks what to supply next. The loop target receives each item (or its unpacked members); it need not itself be an iterator. |
| Unpacking; arity | Unpacking assigns members of one iterable to targets. Arity here means how many items the row must supply. With three ordinary targets, two or four items will not do. |
| Shift; low-order bits | An integer shift moves bit positions by a specified count. Low-order bits occupy the smallest-weight positions; inspecting just those bits does not change the original integer. |

## Historical screen record

Numbers at the left of source listings are editor gutter numbers, not characters to type. Incomplete or assembled views are labeled; a visible program is not by itself a record of an execution.

### 1. `binOp.py`: the three truth tables and the start of the bit display (0:03:00)

**Observed, partial editor listing.** Sublime Text shows **`C:\com\joesta\Numerical\src\binOp.py`** in `00-03-00_minute.png`. The corresponding lines are also visible at `00-04-00_minute.png`. Numbers at left reproduce the editor gutter; do not type them. Line 30 is blank; the text of line 31 is cut off vertically, so the listing stops before it. This is not a complete file.

```text
 1  print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")
 2  print("-" * 11)
 3  for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
 4      q = a & b
 5      print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
 6
 7  print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'| or':<4}")
 8  print("-" * 11)
 9  for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
10      q = a | b
11      print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
12
13  print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'^ xor':<4}")
14  print("-" * 11)
15  for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
16      q = a ^ b
17      print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
18
19
20
21  def displayRow(val, msb, label=""):
22      for i in range(msb, -1, -1):
23          bit = (val >> i) & 1
24          print(f"{bit:<6}", end='')
25      print(label)
26
27  msb = 9
28  n = 1000
29  mask = 0x0f0
30
```

Lines 1–17 send the same four ordered pairs through three different operations. Reading downward, their result columns are 0, 0, 0, 1 (AND); 0, 1, 1, 1 (inclusive OR); 0, 1, 1, 0 (XOR). The three columns are visible in PowerShell at `00-09-40_event.png`, following **`py .\src\binOp.py`**; they are not output of **`git log`**, which remains above them in scrollback.

Lines 21–25 define a helper rather than immediately printing a row: given a value and a top bit position, it visits each position down to zero, moves that bit into the rightmost place, isolates it with 1, and prints the bits on the same line before adding a label. Lines 27–29 set up position 9, data value 1000, and the hexadecimal mask. See the later worked bit-display example for the calls and their effects; the first 30 lines alone do not issue those calls.

**Later checks on the opening listing.** The same executable lines 1–17 were visible at 0:17:00 and 0:18:00, and lines 1–25 at 0:33:00. The 0:18:40 and 0:32:45 terminal views show the same three result columns. Multiple observations of matching lines are not proof of a new launch or a saved change.

### 2. The one-input table was drawn in the editor, not executed (0:23:00–0:30:00)

**Observed manual notation in the `binOp.py` buffer.** At `00-23-00_minute.png`, both output cells are empty. At `00-25-00_minute.png`, there are two completed copies with the same output. These are two separate states, not one piece of runnable Python. Cursor, selection shading and invisible trailing whitespace are not transcribed.

**0:23:00 — question before the class answers:**

```text
56  A | B
57  ------
58  0 |
59  1 |
```

**0:25:00 — two identical tables, before the second is changed:**

```text
56  A | B
57  ------
58  0 | 0
59  1 | 1
60
61  A | B
62  ------
63  0 | 0
64  1 | 1
```

**0:29:40 — four named behaviors, as seen in one original** (`00-29-40_event.png`). At 0:26:00 all four output patterns already appear, but the headers still say only `A | B`. At 0:29:00 the first three headers have names; the fourth does not. Those earlier states are not evidence that the three names or the final label were present all along.

```text
56  A | B echo
57  ------
58  0 | 0
59  1 | 1
60
61  A | B zero
62  ------
63  0 | 0
64  1 | 0
65
66  A | B one
67  ------
68  0 | 1
69  1 | 1
70
71
72  A | B opposite, not, invert
73  ------
74  0 | 1
75  1 | 0
```

Read each sketch vertically to identify the rule: lines 58–59 copy; 63–64 force zero; 68–69 force one; 74–75 reverse the bit. The screen says `invert`; “inverse” is an additional spoken description, not part of the typed label. When the terminal covers this buffer at 0:31:00–0:32:00, its highlighted XOR table still has **two** input columns; it is not the output of these **one**-input notes.

**Key items to scan**

- **Lines 56–59** — the first two output cells start blank and later become the echo rule.
- **Line 64** — changing the second copy's last output from one to zero makes it a distinct function.
- **Lines 66–69 and 72–75** — a fixed one is different from an inversion; input one separates those two boxes.

### 3. `binOp.py`: spaced-out editor state before the debugging edits (0:34:00–0:40:00)

**Observed alternate layout:** `00-34-00_minute.png` shows the same first five lines, now followed by blank lines 6–12 rather than only line 6. OR starts at line 13, XOR at line 19. Compare `00-36-00_minute.png` and `00-40-00_minute.png` for the lower blocks. Those blank source lines change the gutter numbers, not the printed spacing: runtime spacing still comes from the string escapes and fields. This state is not evidence of a separate execution.

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
```

At 0:35:00 the terminal selection covers the spaces between A and B; at 0:35:25 it covers the pipe field. At 0:38:00 the editor highlights the `11` in the rule. At 0:40:00 the selection is the list on line 3, including its square brackets but not the colon. These are changes in what is being pointed out, not four different algorithms.

### 4. The unpacking investigation in `src\binOp.py`

The numbers before the vertical rules reproduce the editor gutter; do **not** type them. Each time identifies a requested capture target, not an exact keystroke or save time. Short listings preserve just the changed lines of a particular state. A PowerShell window sometimes covers the left part of the editor; source quoted by a traceback is identified as terminal evidence, not silently filled into an obscured editor line.

#### 4.1. One target with the original calculation still active (0:40:55)

Observed editor, lines 1–23; lines 1–5 are transcribed below. Lines 6–23 match the numbered layout in record 3, including its blank rows. The calculation at line 4 still uses two names, but line 3 supplies only one. The later OR and XOR blocks each have their own two-name loop; defining `b` down there cannot help at line 4. The preexisting AND table visible to the left is not output from the displayed edit.

```text
 1 | print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")
 2 | print("-" * 11)
 3 | for a in [(0, 0), (0, 1), (1, 0), (1, 1)]:
 4 |     q = a & b
 5 |     print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
```

The initial run failed during parsing. The offending source line, **from the 0:41:00 and 0:41:16 terminal diagnostic**, is:

```text
62 | A | B echo
```

At 0:41:08 the editor shows three double quotes (`"""`) on line 61, with `A | B echo` now at line 63. The opening delimiter makes the following lines string text rather than Python expressions when paired with a closing delimiter. The 0:41:25 editor still shows it. Neither view shows the matching closing delimiter for this revision.

The recorded command is `py .\src\binOp.py` from `(Numerical) PS C:\com\joesta\Numerical>`. The diagnostic identifies `C:\com\joesta\Numerical\src\binOp.py`, line 62, underlines `echo`, and says `SyntaxError: invalid syntax`. The instructor said to have Python ignore it; the opening delimiter is visible, but the exact closing edit and keystroke sequence are not. At 0:41:38 the same opening five editor lines sit beside a **later** traceback: the heading and divider appeared, then `q = a & b` failed with `NameError: name 'b' is not defined`. The full line-4 location is independently legible in the 0:42:55 scrollback.

#### 4.2. Probe one whole item, then unpack it (0:42:55–0:44:00)

Observed editor at 0:43:00; original comments reproduced. Line 4 is the new probe. Two old body lines are still present, but both are commented, not executed. The OR block now begins on line 14, the XOR block on line 20; the extra line in the opening loop shifted the later line numbers.

```text
 1 | print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")
 2 | print("-" * 11)
 3 | for a in [(0, 0), (0, 1), (1, 0), (1, 1)]:
 4 |     print(a)
 5 |     #q = a & b
 6 |     #print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
```

The 0:42:55 terminal shows `(0, 0)`, `(0, 1)`, `(1, 0)`, `(1, 1)`, one per line, followed by the separate OR and XOR tables. In the 0:43:32 editor, the print and commented lines were unchanged; only the targets had changed:

```text
 3 | for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
 4 |     print(a)
```

The four single digits visible at 0:44:00 are `0`, `0`, `1`, `1` on separate lines. The **editor** at that target already shows a third name with the same four pairs, a distinct state prompted by the initial interpretation of the question:

```text
 3 | for a, b, c in [(0, 0), (0, 1), (1, 0), (1, 1)]:
 4 |     print(a)
```

There is no observed run of this three-name/four-pair edit. A separate check fails at the first pair; it cannot be the source of the displayed digits.

#### 4.3. Two triples and two pairs: which item fails? (0:44:31–0:45:16)

At 0:44:31 the terminal quotes the offending source at line 3. This is the two-name version with the first two items lengthened, **not** the three-name version from 0:44:00:

```text
 3 | for a, b in [(0, 0, 8), (0, 1, 7), (1, 0), (1, 1)]:
```

After the column heading and divider, this run prints no data rows. The actual error is `ValueError: too many values to unpack (expected 2)`. Its body is not quoted by the traceback. The visible editor at 0:44:56 is a newer, three-name state; the error beside it is not from that state:

```text
 3 | for a, b, c in [(0, 0, 8), (0, 1, 7), (1, 0), (1, 1)]:
 4 |     print(a, b ,)
 5 |     #q = a & b
 6 |     #print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
```

There is a space before the last comma in that in-progress print. At 0:45:00 the same three-name input has this completed replacement for line 4:

```text
 4 |     print(a, b, c)
```

The 0:45:16 terminal separates two invocations of `py .\src\binOp.py`. The upper ends with the earlier **too-many** error. The lower prints `0 0 8` and `0 1 7`, then reports `ValueError: not enough values to unpack (expected 3, got 2)` at line 3. There is no `1 0` output: the body is never entered for that short item.

#### 4.4. Four triples work; returning to two names fails (0:45:35–0:46:16)

At 0:45:35 the terminal covers the beginning of editor line 3. Only this right-hand portion of the edited loop line is transcribed (left side obscured):

```text
 3 | [beginning not visible]1, 7), (1, 0, 6), (1, 1, 23)]:
```

The terminal shows four rows under the unchanged AND label. They are the **three members**, not AND results:

```text
0 0 8
0 1 7
1 0 6
1 1 23
```

The complete left edge is independently recoverable from `00-45-29_event.png`, immediately before the output: 

```text
 3 | for a, b, c in [(0, 0, 8), (0, 1, 7), (1, 0, 6), (1, 1, 23)]:
 4 |     print(a, b, c)
```

The four rows already appear at 0:45:31. This earlier frame supplies the source; it does not make the hidden portion of the 0:45:35 image visible. The subsequent OR rows have results `0, 1, 1, 1`, and the XOR rows `0, 1, 1, 0`; those are separate, still active, two-input computations.

At 0:46:00 the terminal still shows those successful four rows, but the editor has reverted to the two-triples/two-pairs input, now with this transient spelling:

```text
 3 | for a, b, in [(0, 0, 8), (0, 1, 7), (1, 0), (1, 1)]:
 4 |     print(a)
```

At 0:46:16 the extra comma has gone. This version and the matching traceback confirm the renewed two-target test:

```text
 3 | for a, b in [(0, 0, 8), (0, 1, 7), (1, 0), (1, 1)]:
 4 |     print(a)
```

No data row precedes this `ValueError: too many values to unpack (expected 2)`; its full text also remains visible above the next invocation at 0:46:57. The two spellings both name two targets, but the stills do not show a separate run of the extra-comma edit.

#### 4.5. A list first, a tuple second; then a different editor again (0:46:57–0:48:50)

Observed editor excerpt at 0:48:00. Compare the first inner brackets to the parentheses of the second item. The full loop header is independently visible in the 0:46:57, 0:47:00 and 0:47:43 tracebacks; the latter has the first `0` selected. The printed values before failure are **two individual zeros**:

```text
 3 | for a, b, c in [[0, 0, 8], (0, 1, 7), (1, 0), (1, 1)]:
 4 |     print(a)
 5 |     #q = a & b
 6 |     #print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
```

```text
0
0
Traceback (most recent call last):
  File "C:\com\joesta\Numerical\src\binOp.py", line 3, in <module>
    for a, b, c in [[0, 0, 8], (0, 1, 7), (1, 0), (1, 1)]:
```

The screenshot’s terminal ends at the quoted source line. The final exception line is not visible for **this** run; the class identifies a two-value item where three were required, and the independent fixture confirms the exception. At 0:48:50 the opening lines have returned to the first listing: one target, active `q = a & b`, active formatted print, no diagnostic print. The terminal still contains the two zeros from the mixed-container run; no fresh execution of the restored editor is established.

### 5.1. `binOp.py`: a partly restored AND loop (0:49:00)

Historical editor excerpt, `00-49-00_minute.png`. The five selected lines already use the bitwise AND expression, but line 3 still takes only one name. Read the gutter as an editor reference, **not characters to type**. Indentation following the gutter belongs to the source.

```text
 1 | print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'& and':<4}")
 2 | print("-" * 11)
 3 | for a in [(0, 0), (0, 1), (1, 0), (1, 1)]:
 4 |     q = a & b
 5 |     print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
```

Lines 1–2 print the headings; lines 3–5 intend to calculate and print one result for each pair. Line 3 alone does not bind a separate second integer for line 4. The neighboring terminal retains older unpacking tracebacks with *different* loop targets and input lengths. Neither traceback is proof of a run of the selected five lines.

### 5.2. `binOp.py`: repaired AND beside OR and XOR (0:50:00)

Historical editor listing, `00-50-00_minute.png`, visible lines 1–26. The repaired line 3 now matches the two-target loops in the two lower tables. Reading only the unquoted operator on lines 4, 16, or 22 tells you which truth rule is actually being executed.

The complete numbered layout is already printed in record 3. Comparison of the two originals shows the same executable lines 1–23, with the seven blank lines at 6–12. At 0:50:00, lines 24–26 are also visibly blank. The decisive repair after record 5.1 is the second name on line 3:

```text
 3 | for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
 4 |     q = a & b
```

Lines 5, 17, and 23 display each result; the quoted bar in all three print statements is a visual separator. The result columns visible to the left of the editor are respectively `0 0 0 1`, `0 1 1 1` and `0 1 1 0`, top to bottom. The full unobstructed command and rows are visible at 0:53:00, not in the portion of the terminal behind this editor.

### 5.3. `binOp.py`: subsequent layout and the ten-bit display (0:50:35–0:52:00)

Assembled visual reconstruction with compatible overlapping gutters: `00-50-35_event.png` shows lines 7–32, `00-51-00_minute.png` shows 16–41, and `00-51-45_event.png` and `00-52-00_minute.png` show 22–47. The shared rows agree; unsampled changes outside these views cannot be excluded. Here XOR starts at line **21**, not 19 as in record 5.2. Lines 1–6 of this version are outside the view, so they have not been copied in from record 5.2.

Lines 7–12 are blank in this view; the transcription resumes at the first statement.

```text
13 | print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'| or':<4}")
14 | print("-" * 11)
15 | for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
16 |     q = a | b
17 |     print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
18 |
19 |
20 |
21 | print(f"\n\n{'A':<4}{'B':<3}{'|':<3}{'^ xor':<4}")
22 | print("-" * 11)
23 | for a, b in [(0, 0), (0, 1), (1, 0), (1, 1)]:
24 |     q = a ^ b
25 |     print(f"{a:<4}{b:<3}{'|':<3}{q:<4}")
26 |
27 |
28 |
29 | def displayRow(val, msb, label=""):
30 |     for i in range(msb, -1, -1):
31 |         bit = (val >> i) & 1
32 |         print(f"{bit:<6}", end='')
33 |     print(label)
34 |
35 | msb = 9
36 | n = 1000
37 | mask = 0x0f0
38 |
39 | print()
40 | for i in range(msb, -1, -1):
41 |     print(f"2^{i:<4}", end='')
42 | print()
43 |
44 | for i in range(msb, -1, -1):
45 |     print(f"{2**i:<6}", end='')
46 | print()
47 |
```

- **13–25:** OR and XOR retain the same input pairs and output formatting. Compare the expression on line 24 with the literal caret on line 41: only the former performs XOR.
- **29–33:** each successive right shift and one-bit mask selects one column for printing. Line 33 is inside the function but outside the loop, so the label appears only once.
- **35–37:** the view is ten bits wide (positions 9–0), with data 1000 and hexadecimal mask `0x0f0`.
- **39–46:** the first loop writes the exponents as labels; the second computes the weights. Each standalone `print()` ends a line or makes a blank one.

The completed PowerShell run at `00-53-00_minute.png` reads `(Numerical) PS C:\com\joesta\Numerical> py .\src\binOp.py`. After the truth tables and all eight multibit rows, the prompt returns. Lines 48–75 are not visible in these editor views. The later frames do not establish those exact intervening lines or delimiters. The corresponding driver **is** independently visible in an earlier layout, reproduced below with its own gutter; do not infer that it occupied the same lines in the later file.

#### Earlier driver, recovered at 0:23:00

The same original that shows the unfinished unary table also shows the code above it. This is an observed excerpt, not an inferred continuation of the 0:52:00 editor. On lines 42–53, each assignment calculates one integer and the next statement displays that integer. An independent run with the recovered helper and setup produced the eight observed rows; it does not establish the identity of every line of the later saved file.

```text
40 | displayRow(n, msb, "data")
41 | displayRow(mask, msb, "mask")
42 | result = ~n
43 | displayRow(result, msb, "not")
44 | result = n & mask
45 | displayRow(result, msb, "and")
46 | result = n | mask
47 | displayRow(result, msb, "or")
48 | result = n ^ mask
49 | displayRow(result, msb, "xor")
50 | result = n<<3
51 | displayRow(result, msb, "left shift 3")
52 | result = n>>3
53 | displayRow(result, msb, "right shift 3")
```

### 5.4. `binOp.py`: a numeral selected inside scratch text (0:55:40–0:56:00)

Historical editor data, not a Python calculation or terminal run. `00-55-40_event.png` and `00-56-00_minute.png` show the same lower excerpt. Line 81 introduces an inverse mapping: zero maps to one, one to zero. Line 86 is selected while the class asks what `100` means in different bases. The header for lines 77–78 and the other multiline-string boundary are not visible in this view; line 90 does show three double quotes.

```text
76 | ------
77 | 0 | 1
78 | 1 | 1
79 |
80 |
81 | A | B opposite, not, invert
82 | ------
83 | 0 | 1
84 | 1 | 0
85 |
86 | 100
87 |
88 |
89 |
90 | """
91 |
92 |
```

The terminal diagrams at 0:54:00 and 0:55:00 highlight `2^2`; at 0:57:00 the selection spans `2^2`, `2^1`, `2^0`. These are selections within existing output, not executions converting between bases. On the closing recording-control screens (1:01:59–1:02:00) no further program output is established.

## Micro-Learning Insights

*Removable appendix · short cues for reconstructing the lesson from memory.*

- **Read the figure when a bit count is off by one.** IEEE 754 Figure 3.1 labels the trailing field `t = p − 1`. For a normal binary64 value, 53 digits of precision do not require 53 trailing storage bits. (0:06:45)
- **Rename the bits and test whether the explanation survives.** Tom and Jerry, a lamp off and on, and 0 and 1 can all stand for two alternatives. The rule must specify which alternative is preferred; the symbol does not do that by itself. (0:11:00–0:13:00)
- **Put a finger on the `11` row.** It is the fastest way to distinguish inclusive OR from exclusive OR when the other three rows already agree. (0:19:00–0:21:00)
- **Count output choices, not boxes you can name.** Two binary inputs give four rows. Writing one output bit beside each row is enough to specify one of sixteen behaviors, whether or not you know its name. (0:30:00–0:32:00)
- **Separate layout characters from operators.** In the printed OR header, a pipe divides the columns and a second pipe names the operator. Only the unquoted operator between integer operands computes OR. (0:32:45; 0:50:00)
- **Use width rather than hand-spaced columns.** In `{'A':<4}`, the single letter takes one of four spaces; the remaining padding is on its right. Field width is a minimum, not a promise to truncate a longer value. (0:33:00–0:36:00)
- **Ask “one item from which container?”** One iteration of a list of pairs supplies a whole pair. A one-name target keeps it together; a two-name target unpacks its members. (0:42:00–0:43:00)
- **Look immediately above the exception.** Two printed triples before “expected 3, got 2” tell you the first two assignments succeeded. The third row is the first place to inspect. (0:45:35)
- **Read the mask vertically.** In the 16s column, data 0 and mask 1 produce AND 0, OR 1 and XOR 1. You can validate a busy ten-bit display one column at a time. (0:53:35)
- **Say the base out loud.** “One-zero-zero in base eight” is unambiguous. “One hundred” invites the listener to assume base ten before you have explained the third position. (0:55:00–0:59:00)
