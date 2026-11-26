# From Recorded Lectures to a Verified AI Teaching-Companion Production System

## The complete storyboard of my Python study-guide optimization journey

**A historical record and narrative sourcebook for a future illustrated PDF**  
**Course:** CSCS1240F26 — object-oriented Python  
**Project:** Hermes / ChatGPT Work source-grounded worked teaching companions  
**Milestone represented:** From the $20 Plus-plan allowance bottleneck and November 23 Pro upgrade to production of Sessions 25, 26, and 29 using Hermes Unified v2  
**Record compiled and expanded:** November 2026, from conversation history, actual available Markdown instruction files and guides, and user-reported run metrics  
**Purpose:** Preserve the decisions, experiments, failures, improvements, file versions, usage figures, results, and lessons learned in enough detail to serve as an archival narrative and source for Gemini's PDF design and reasoning workflow.

> **Read this first — evidence and metric integrity.** This is a retrospective reconstruction, not a laboratory report. The recorded *quality scores* (such as 94/100 and 96/100) are ChatGPT's **subjective document-review grades**, not independently measured factual accuracy percentages. A two-point improvement from 94 to 96 is **+2 percentage points on a 100-point rubric** and **approximately +2.13% relative to 94**, but because Sessions 25 and 26 are **different recordings**, it is **not proof that the model became 2.13% more accurate**. The study guides and actual instruction Markdown files can substantiate wording, size, and organization; the user's reports substantiate pages, recording source verification and meter readings. Do not reframe a grade as a measured accuracy rate, or weekly-meter changes as exact token counts or dollar costs. The early $20 Plus-period exhaustion, approximate Hermes credits and $100 Pro upgrade are user-reported history; the newer Pro-meter readings are separate later observations, not directly comparable limits.

---

# PART I — THE PERSONAL REASON FOR BUILDING THE SYSTEM

## Chapter 1 — The actual problem was not taking notes; it was understanding a recorded class

I returned to college as an adult learner and began studying object-oriented programming in Python. The course was taught through recordings that could span nearly an hour or more than three hours. The content was not a neat sequence of slides. The instructor worked in programming editors, terminal windows, Discord, Git repositories, and class discussion. Code could be visible without being read aloud. An instructor might spend seconds on a crucial distinction and much longer on a tangential discussion. The editor could show an unsaved program, while PowerShell still displayed the output of a *different Git revision*.

My learning need was more demanding than a transcription. I needed to understand and later **reproduce** the code. I particularly wanted to remember syntax and connect each statement to a value, action, outcome or exception. I wanted the relationship among the instructor's words, the code on screen and the terminal output explained concretely. I did not want an oversimplified fifth-grade summary, a shallow glossary or a 15-page condensation of a three-hour lesson simply because an AI decided that short was better.

The goal gradually became a **worked teaching companion**: a teaching resource that could stand beside the recording, explain everything meaningful, preserve technical terminology, and give me ways to scan the critical syntax when studying later.

### My non-negotiable educational needs

1. **Source-grounded completeness.** Preserve meaningful instruction, demonstrations, errors, questions, brief methodological insights and corrections. Remove irrelevant chatter, not short insights.
2. **Concrete-first teaching.** Describe what the program does using the *actual values from class*—apple, banana, grape, 1000, 0x0f0, 34.67—before introducing abstract terminology.
3. **Strong Python syntax memory support.** Every substantial teaching example should have inline or adjacent Python `#` comments that explain what important statements create, change, return, display or raise.
4. **Execution traces.** Show intermediate states, not only final answers. A good table reveals how a variable, iterator, file, list or object changes after each step.
5. **“Key items to scan.”** After explanation, provide concise definitions of the exact functions, operators, variables, commands, errors and syntax used, with concrete results.
6. **Source fidelity.** Preserve the original screen code separately. Do not silently repair a mistake and then claim it is the teacher's code. Label reconstructed or supplemental examples.
7. **Usable deliverables.** Produce an editable Markdown guide *and* a polished, searchable, print-ready PDF.
8. **Repeatable workflow.** The next session should not require rebuilding the instructions, manually restating the quality standard or repeatedly troubleshooting screenshot retrieval.

### The first pivotal realization

> An AI can summarize the words from a lecture and still miss the lesson. To create a useful teaching companion, it must reconstruct *the relationship between the spoken explanation, code on the screen, program state and results* without inventing events that cannot be verified.

**Storyboard visual:** A split-screen lecture: instructor code on one side, terminal and conversation on the other; a learner surrounded by transcripts, screenshots and incomplete notes; then a transformation into one annotated, navigable guide. Caption: **“From transcription to understanding.”**

---

# PART II — EARLY WORKFLOW EXPERIMENTS

## Chapter 2 — The first reusable prompts solved only part of the problem

The early workflow had two separate instruction components that existed in the project:

- `addon study guide enhanment.txt` — a kickoff/add-on instruction designed to start production and reinforce the preferred teaching presentation.
- `Experimental_Study_Guide_Reusable_prompt.txt` — a longer reusable workflow describing transcript acquisition, screenshots, evidence interpretation, source completeness and guide creation.

The original kickoff explicitly instructed the agent to ask for the YouTube URL and course/session number, then use the experimental reusable prompt. This was a useful step because the instruction text began to work across recordings rather than depending on one manually selected lesson.

But a two-file instruction hierarchy made authority and maintenance harder. When prompts were edited, the agent could potentially follow outdated rules, repeat sections of guidance, or treat a sample as though it were a requirement. I wanted one instruction source that would remain stable while every session changed dynamically.

### Important technical lessons from the earlier study-guide work

**The screenshot is a primary source, not decoration.** The course frequently displayed an editor with code on the right and output or terminal on the left. A single clear frame could contain 80–95% of what the instructor was discussing at that point, but could still omit something cropped, hidden or changed between frames. The visual source and transcript needed to be merged rather than ranked as substitutes for each other.

**One frame is not a movie.** A screenshot at 10:00 and another at 11:00 do not prove what keys were pressed, whether a file was saved, what code ran or what occurred between them. Evidence must remain bounded to what was visible or actually spoken.

**Capture coverage should be driven by recording duration, not arbitrary limits.** In earlier troubleshooting, one working version captured frames around three-minute intervals. A later rule change appeared to produce only about 15 images and steered the agent toward the browser player, although the desired workflow was direct source retrieval. The exact source of those earlier failures was not conclusively established here, but the corrective direction was clear: direct permitted capture, recording-wide coverage, no fixed 15-image cap, plus event frames where needed.

**A short insight can be essential.** A one- or two-second comment about why an operation is safe, why a variable is rebinding rather than changing an object, or why a method name is conventional might be more useful than several minutes of general class conversation. A good filter removes chatter and retains methodology.

### The initial teaching architecture: three layers

1. **Core lesson concept:** what the instructor is actually teaching.
2. **Subtle reasoning and methodology:** the quick why/how insights, edge cases, debugging choices and distinctions the instructor uses.
3. **Syntax and worked expansion:** accurate Python spelling, complete locally relevant examples, comments, predictable outputs and trace tables when the transcript does not spell out every step.

The third layer requires care. Expanding a concept for learning is allowed; attributing the expansion to the instructor is not.

**Storyboard visual:** Two original prompt documents feeding an agent; an inconsistent capture path; a corrective loop introducing transcript + time-aligned one-minute screenshots + event frames; then three interlocking teaching layers.

---

# PART II-A — THE ORIGINAL $20 PLUS BOTTLENECK AND WHY USAGE BECAME PART OF THE DESIGN

## Chapter 2A — November 7–23: the study-guide experiments outgrew the Plus allowance

This is an essential part of the origin story, not a footnote to the model comparison. **Before the November 23–24 production transition, I was using the $20-per-month ChatGPT Plus plan.** I was trying to turn recorded programming lectures into source-grounded study guides using Hermes and GPT model access through OpenAuth. A richer guide demanded more than a short question-and-answer response: transcript analysis, source screenshots, interpreting the professor's editor and terminal, writing detailed explanations, preparing the Markdown and PDF, and sometimes revising the output after inspection. The work was educationally valuable, but the account's usage limits interrupted it.

The important breakthrough was realizing that **model capability and operating sustainability are separate constraints**. An excellent teaching companion is not a repeatable production process if one or two sessions can stop the rest of the evening's work. The system needed to maximize instructional usefulness *per unit of available model usage* without simply shortening the guides until they stopped being useful.

### Reconstructing the pre-production chronology

| Time frame | What I was doing | What it taught me |
|---|---|---|
| **November 7** | Formalizing the Session 11 reusable prompt, making session identification dynamic across a planned series of approximately 40 recordings and keeping one Markdown guide per session. | A useful guide needs explicit educational requirements and must not hard-code one lesson's topics. |
| **November 8–15** | Troubleshooting Session 11, comparing guide versions, refining syntax-focused annotations and the way the agent combines lecture text with screenshot evidence. | Better quality requires retrieval, reasoning, code commentary and careful source limits—not simply more summary text. |
| **November 15** | Organizing the source workspace under `C:\Projects\StudyProfile\CSCS1240F26\` and finished guides under `C:\Projects\GPT Study Guides\Sessions\Session <number>\Finished Guides\`. Evaluating Hermes's study-guide output against alternate versions and committing to approximately one image per minute as a source-capture goal. | Production needs stable directories and evidence that can be reused across guide versions. |
| **November 15–23** | Enlarging the prompt and requesting increasingly comprehensive guides through Hermes; studying the tradeoffs among models, screenshots, code comments and historical evidence. | Depth improves the teaching material, but repeated long experiments consume scarce interactive allowances and credits. |
| **November 23** | Explicitly reporting that a session had exhausted the shorter five-hour allowance window and that the *weekly* allowance was also at zero. | The Plus-plan limit was now blocking the study schedule, not merely slowing an isolated answer. |
| **Around November 23, reported in the November 24 discussion** | Upgrading from the **$20 Plus plan to the $100 Pro plan** so I could sustain comparisons and produce more than one guide in an evening. | The upgrade bought operational headroom; it did not automatically solve wordiness, completeness, or source fidelity. |
| **November 24 onward** | Comparing Terra/SOL/Astra, refining Unified v2, and measuring production sessions systematically. | Model selection plus a stable instruction contract mattered as much as the higher allowance. |

These are **conversation-derived historical markers**, not a minute-by-minute account ledger. Several activities spanned multiple days. Do not infer that the upgrade happened at an exact hour or that every experiment named above occurred on a single specified day.

### What the exhausted allowance actually looked like

During the November 23 conversation, I reported that the Plus plan's **five-hour meter had fallen from roughly 60% remaining to zero**, and that the **weekly allowance was also exhausted (0% remaining)**. I had managed only a small number of substantial study-guide jobs before progress was blocked, and I described needing to wait roughly five hours for the shorter-window allowance to recover. This was especially frustrating because classes were approaching their end: approximately three weeks remained, and I wanted to process a backlog of recordings rather than spend an entire evening waiting to continue.

These observations should be presented as **my reported account-meter experience**, not a published specification of Plus limits. A remaining-percentage display describes a particular user's account at a particular time. It does not certify a universal allowance for all users or imply that the content of one guide alone consumed every displayed point.

### The credit drawdown was a second warning signal

I also reported an approximate Hermes credit sequence:

| Reported point in the experimentation | Approximate credits | Arithmetic against the preceding marker |
|---|---:|---:|
| Earlier balance | **2,500** | Starting reference, not a confirmed billing-cycle allocation |
| Intermediate balance | **2,300** | **−200 credits**, approximately **8%** of 2,500 |
| Later balance | **1,700** | **−600 credits** from 2,300, approximately **26.1%** of that intermediate balance |
| Total observed span | **2,500 → 1,700** | **−800 credits**, approximately **32%** of the 2,500 reference |

These are **approximate user-reported balances**, not individual-session invoices. The point at which each credit change occurred is not fully attributed to a single task or model. The percentage arithmetic describes the *reported balance change*; it is **not the same as weekly ChatGPT allowance usage**, and credits should not be converted to dollars or tokens without actual billing records.

The earlier operating picture was therefore two-dimensional: the allowance meter could prevent continued model work, while the credits consumed in the experiments made repeated trial-and-error costly in another way. Both encouraged a shift from improvising a new prompt for every guide to **building, testing and freezing a reusable production standard**.

### The upgrade decision: greater capacity, but still a reason to optimize

Around November 23, I described upgrading to the **$100 Pro tier** to gain continuity and access to stronger model/reasoning choices. I was at approximately Session 24 of a roughly 40-session course sequence and hoped to complete **several recordings in one evening**, rather than wait through cooldown periods. In the prior discussion I described an ambitious target of roughly six sessions that night and finishing the remaining work soon after; this was a **plan and motivation**, not a verified batch of completed guides.

Crucially, the upgrade was **not** a declaration that every long, verbose answer was acceptable. After moving to Pro, the Session 24 Astra High experiment itself reportedly moved the visible weekly meter by approximately **eight percentage points**, while the more compressed ChatGPT Work comparison reportedly moved it by about **one point**. That contrast sharpened the question: **how do I obtain Astra's comprehensive understanding without paying for repetitive narration or losing time to repeated manual repairs?**

That question leads directly to the model bake-off and Unified v2's controlled-repetition rules. The model comparison was about educational strengths; the language refinement was about delivering those strengths efficiently, consistently and without corrupting source evidence.

### The important before-and-after comparison—and what cannot be compared

| Earlier $20 Plus period | Later $100 Pro / Unified v2 production |
|---|---|
| Reported five-hour meter around **60% → 0%** and weekly allowance **→ 0%** during extended experimentation | Three individually recorded production runs, **83% → 82% → 78% → 74% weekly remaining** |
| Approximate Hermes credits **2,500 → 2,300 → 1,700** across the experimental period | Specific session, recording duration, caption count, screenshot count, pages and document-review grade recorded after each job |
| Repeated prompt changes and comparisons to find the desired educational depth | Stable authoritative **`Hermes_Unified_Study_Guide_Instructions_v2.md`** and a small reusable kickoff |
| Waiting for usage availability interrupted planned guide production | Sessions 25, 26 and 29 completed sequentially as reported, without claiming that usage limits could never recur |
| Success measured largely as *getting any sufficiently thorough guide finished* | Success also measured by **source completeness, commented code, verified outputs, Markdown/PDF integrity and consistent quality** |

**Do not calculate a “percent efficiency improvement” by dividing Plus exhaustion by Pro meter usage.** The plans, limits, model routes, tasks and timing are not directly comparable. The faithful conclusion is qualitative but meaningful: on the $20 plan the sustained workflow became operationally blocked; after the upgrade *and* process refinement, three measured production jobs yielded **195 PDF pages from about 7 hours 50 minutes of recordings** with **nine displayed percentage points** of weekly Pro allowance movement. The quality-review scores were **94/100, 96/100 and 96/100**, but those are subjective grades on *different source lessons*, not quantified changes in factual accuracy.

### Why this chapter changes the entire story

There were **two parallel optimization problems**:

1. **Learning quality.** The guide must capture useful teaching, preserve the actual classroom evidence and explain Python at the line level.
2. **Production continuity.** The same workflow must be affordable in the user's available allowance, resilient to long recordings, and repeatable across a backlog of sessions.

The first problem led me to combine Astra's completeness, Terra's scanability and SOL's helpful inline comments. The second led me to reduce prompt crosstalk, reuse verified evidence, avoid redundant rewrites, separate long historical listings from the main lesson, and track the remaining allowance after each production run.

> **The personal turning point:** I did not upgrade merely to buy more AI responses. I upgraded because the study-guide workflow had become useful enough that losing the ability to run it was itself a problem. Then I made the workflow more disciplined, so the additional capacity would produce repeatable learning materials rather than more expensive experimentation.

**Storyboard visuals:** (1) A two-gauge dashboard for the Plus period—**five-hour allowance 60% → 0%** and **weekly 0% remaining**—explicitly labeled *reported, plan-specific meter readings*. (2) A credit staircase **2,500 → 2,300 → 1,700**, with **−800 / −32% relative to the approximate opening balance**, clearly separated from account allowance. (3) A timeline showing the **$20 Plus → $100 Pro** upgrade around November 23 as the *capacity* milestone, followed by Unified v2 as the *process* milestone. (4) A split “Two constraints” diagram: instructional quality versus sustained production. Avoid inventing a precise upgrade timestamp, cost-per-guide, tokens consumed, or a universal Plus plan limit.

---

# PART III — THE MODEL BAKE-OFF

## Chapter 3 — Session 24 became the testing ground

After the $20 Plus-plan bottleneck and the upgrade to a $100 Pro account, I could resume more extensive experiments without immediately hitting the same reported usage interruption. Instead of assuming that the newest or most powerful-sounding model was automatically the best teacher, I compared different models on the same Session 24 lecture material. The model experiments explicitly involved:

- **Terra XHigh**
- **SOL 6.2 High**
- **Astra High**

Three available comparison outputs are named:

- `CSCS1240F26_Session_24_Worked_Teaching_Companion_Terra_XHigh.md`
- `CSCS1240F26_Session_24_Worked_Teaching_Companion SOL62_High.md`
- `CSCS1240F26_Session_24_Worked_Teaching_Companion_Astra_High.md`

### What the three models taught me

| Model | Most useful strength observed | Limitation observed | What I borrowed for the instruction standard |
|---|---|---|---|
| **Terra XHigh** | Easy-to-understand definitions and especially scannable **“Key items to scan”** | Less thorough inline explanation of each behavior-changing code statement in the evaluated outputs | Quick-reference entries must be brief, independent, tied to actual lesson syntax and actual values |
| **SOL 6.2 High** | Learner-friendly Python `#` comments that say what the code *does* | Less exhaustive overall lesson and historical coverage than Astra in this comparison | Line-level comments must be concrete, close to the relevant statement and easy for a beginner to understand |
| **Astra High** | Comprehensive integration of transcript, visible source code, output, screenshots, full explanations, tables and historical reconstruction | Can become wordy and require more of the visible weekly allowance | Retain full coverage and source reconstruction, then edit for clarity and remove redundant narration |

The model comparison was not a controlled benchmark yielding a measured percentage for each model. It was a **qualitative evaluation of pedagogical strengths**. The infographic's “model bake-off” describes these strengths—not a statistically valid accuracy ranking.

### The outcome

**Astra supplied completeness. Terra supplied scanability. SOL supplied learner-friendly code commentary.** The correct design decision was not necessarily to choose one output style untouched, but to encode the best features of all three into a model-independent Markdown instruction standard, then use Astra as the production engine.

### Available file-level size comparison

The counts below were calculated from available Markdown files. They describe *output size*, not accuracy.

| Session 24 model file | Approximate whitespace-delimited words | Markdown lines | Note |
|---|---:|---:|---|
| Terra XHigh | 10,326 | 889 | Plain-language, more compact comparator |
| SOL 6.2 High | 10,307 | 790 | Commentary-focused comparator |
| Astra High | 15,763 | 1,262 | Most extensive of these three initial outputs |

**Important:** These original model-specific outputs and the later *Unified* v1/v2 outputs are different artifacts. Their lengths must not be conflated.

**Storyboard visual:** Three AI-model cards with differentiated strengths, followed by three colored arrows joining into a single “completeness + explanation + scanability” standard. Do not label one as “X% objectively accurate” because those percentages were not measured.

---

# PART IV — INSTRUCTIONS BECOME THE PRODUCT

## Chapter 4 — Unified v1: one authoritative Markdown specification

**File:** `Hermes_Unified_Study_Guide_Instructions_v1.md`  
**Version:** 1.0  
**Available file size:** about 4,177 whitespace-delimited words, 248 lines.

Unified v1 merged the two older instruction files and incorporated the lessons from Session 24 model comparison. The introduction states that it is the authoritative instruction set for making an editable Markdown companion and print-ready PDF and replaces the need to read the two previous files together.

The major conceptual advance was separating four kinds of requirements that used to blur together:

1. **Source acquisition and identification:** correct course, session, video and transcript; confirm the recording actually matches the request.
2. **Evidence integration:** align captions and frames without inventing actions, missing characters or terminal results.
3. **Teaching quality:** actual values, annotated examples, stepwise reasoning and scan-friendly definitions.
4. **Deliverable integrity:** preserve source and previous versions, generate Markdown and PDF, check content and formatting.

Unified v1 was a substantial consolidation. Its learning objective was already correct, but early full-scale output demonstrated a weakness: a guide can be *complete yet unnecessarily exhausting to read*. Source forensics and repeated explanations competed with the flow of learning.

### Artifact in the comparative sequence

`Session_24_Worked_Teaching_Companion_Unified_v1.md`  
Available: approximately 24,209 words, 2,284 Markdown lines.

The size confirmed that the process could preserve substantial evidence and instructional material. But the next optimization targeted **instructional density**, not the number of pages.

## Chapter 5 — Unified v2: controlled repetition without sacrificing coverage

**Production instruction file:** `Hermes_Unified_Study_Guide_Instructions_v2.md`  
**Version:** 2.0  
**Available file size:** about 4,882 words, 267 lines.

Version 2.0 explicitly describes itself as a **“clarity and controlled-repetition refinement”** of the consolidated instruction set.

This was the key production turning point. I did **not** ask Hermes to arbitrarily cut the word count or halve the pages. I asked it to stop repeating explanations that had already been fully taught while retaining genuinely new examples, state changes, errors and insights.

### The nine controlled-repetition rules that materially changed the guide

Unified v2 includes a dedicated **Section 9A, “Instructional clarity and controlled repetition.”** Its principles are:

1. **Concrete action first; formal term second.** Describe what apple, banana, the position counter or the iterator does before introducing jargon.
2. **One central idea per paragraph.** Use short, connected explanations rather than long chains of abstract operations.
3. **Explain a distinct mechanism fully once.** Later appearances of unchanged code should focus on changed inputs, new errors or new insight instead of restating the entire mechanism.
4. **Do not sandwich code between duplicate paraphrases.** Give setup before the code; let strong `#` comments explain lines; focus on causal insights after the code.
5. **Never remove teaching artifacts for compactness.** Retain annotated code, output, traces, definitions, questions and useful scan lists.
6. **Keep deep source forensics out of the main reading path.** Explain relevant uncertainty briefly, and place long reconstruction and revision histories in an appendix.
7. **Give tables a purpose.** Trace tables should show change and causal differences, not repeat another table's rows for decoration.
8. **Edit for readability *after* auditing coverage.** Removing repetition is safe only if the distinct teaching facts remain intact.
9. **Handle clipped source literally.** Display only legible characters and mark missing suffixes; do not complete a cropped line and then claim it was observed.

### The two-layer code design

Unified v2 protects a critical distinction:

- **Annotated teaching version** — supported executable teaching code with guide-added `#` comments, practical examples, return/output labels and nearby reasoning.
- **Faithful historical source version** — what appeared in the instructor's editor, including quirks, syntax mistakes, incomplete expressions, line numbers and uncertain portions, normally in a numbered appendix.

These complementary layers solve two different problems: **learn the code** and **preserve the classroom record**.

### Why the appendix matters

The instructor often checks out multiple Git revisions within one lesson. A historical appendix can trace those versions and preserve complete visible code without forcing the learner to detour through every image and terminal state before understanding the main idea. This produces an instructional path followed by a source audit path.

### Session 24 Unified v2 comparison artifact

`Session_24_Worked_Teaching_Companion_Unified_v2.md`  
Available: approximately **24,600 words**, **2,364 lines**, **31 “Key items to scan” sections** and **91 fenced code/output blocks** (fences counted as pairs; not all blocks are Python). These counts use the actual available Markdown and may differ slightly from earlier approximate conversational figures.

**Qualitative outcome:** The Hermes v2 result excelled at preserving detailed technical instruction and historical code. It became the preferred **comprehensive master-guide** format.

**Storyboard visual:** Before/after spread. Left: a densely repeated explanation. Right: a concise setup, precisely commented Python, one execution table and a short “Key items to scan,” with a large historical appendix tucked behind the learning path. Caption: **“Do not teach less; repeat less.”**

---

# PART V — THE CHATGPT WORK COUNTER-EXPERIMENT

## Chapter 6 — The same Session 24 with a different execution environment

I wanted to see whether another agent could create a better study experience with the same evidence and instructions. **ChatGPT Work** became the counter-experiment to the local **Hermes** workflow.

The first major comparison was Hermes Unified v2 against a ChatGPT Work interpretation of Unified v2.

### Verified available artifacts

- **Hermes v2:** `Session_24_Worked_Teaching_Companion_Unified_v2.md`
- **ChatGPT Work v2:** `Session_24_Worked_Teaching_Companion_Unified_v2_GPT.md`

### What changed

The Work version was far shorter. It was easier to skim and somewhat more direct, but its reduced length raised the question of whether substantial teaching and historical code had been lost.

| Session 24 output | Approximate words from available file | Lines | Distinguishing concern |
|---|---:|---:|---|
| Hermes Unified v2 | 24,600 | 2,364 | Very comprehensive; substantial depth and historical reconstruction |
| ChatGPT Work Unified v2 | 7,763 | 777 | Readable and compact, but less instructional and historical coverage |

An earlier comparative review recorded **approximately 95/100 for Hermes v2 and 87/100 for ChatGPT Work v2**. These were **subjective evaluations of two outputs on the same session**, not objective source-coverage measurements and not laboratory estimates of model correctness.

**Arithmetic of the earlier review:** 95 − 87 = **8 rubric points**; relative to 87, this is approximately **9.20% higher reviewer score**. This is an *apples-to-apples session comparison* in the sense that both addressed Session 24, but the workflow, output size and reviewer criteria were not blinded or independently calibrated. It is evidence of a preference in this evaluation, not proof that a model is 9.20% more factually accurate.

### Usage tradeoff noted during the trial

This comparison belongs **after the earlier Plus allowance exhaustion and the Pro upgrade**, not to the same meter or billing window. The two periods show why I began recording usage rather than relying only on subjective impressions.


The experimental Session 24 Hermes Astra High run reportedly moved the visible weekly allowance by about **8 percentage points**, while the comparable ChatGPT Work v2 run reportedly moved it by about **1 point**. These are rough rounded meter differences, not exact tokens, time or money; they may be influenced by tool work, prompts, context, output and other account activity. A short Work output was not necessarily a better bargain if completeness was lost.

This led to a more useful decision than simply switching models: keep **Hermes + Unified v2** as the master workflow, and develop a **more concise supplementary style** in ChatGPT Work rather than making it replace the master.

## Chapter 7 — ChatGPT Work v3: the clearer companion

**Instruction file:** `ChatGPT_Work_Unified_Study_Guide_Instructions_v3.md`  
**Available file size:** about 6,794 words, 367 lines.  
**Guide example:** `Session_24_Worked_Teaching_Companion_ChatGPT_Work_Unified_v3.md`  
**Available guide size:** about 11,259 words, 1,047 lines.

Work v3 retained the foundation of Unified v2 but added a Work-specific execution contract addressing over-compression, historical reconstruction and instructional depth.

In the Session 24 trial, this version was more direct and example-driven than the large Hermes master. It emphasized clear conceptual explanation and a practical standalone demonstration or lab. That was useful as a complementary study resource: the master keeps the breadth and historical record, while a focused edition can help revisit the key code.

### The issue discovered in Work v3

Two screenshots of the guide showed a stylistic inconsistency: teaching sections provided strong same-line `#` comments, but the historical code appendix included long, faithful listings without comparable embedded guidance. The absence of comments in a historical *verbatim* listing is actually appropriate for source integrity. The problem was that the learner still needed a line-by-line explained **paired version**.

This produced another refinement.

## Chapter 8 — ChatGPT Work v3.1: universal code-comment coverage without falsifying history

**Instruction file:** `ChatGPT_Work_Unified_Study_Guide_Instructions_v3_1.md`  
**Available file size:** about 7,801 words, 400 lines.

The v3.1 revision introduced a global comment contract. It instructs Work to audit teaching Python across all sections, appendices, examples, labs and syntax references. Every meaningful statement should be explained by a same-line or immediately adjacent `#` comment when the code is being offered for instruction.

The critical provenance rule remains:

> **Never insert AI-generated comments into a historical listing and then label that edited listing as the exact classroom source.** Keep the verbatim code unchanged and pair it with a clearly labeled **“Annotated teaching version — guide-added # comments.”**

This approach gives the learner both source truth and the desired commentary without sacrificing either. Version 3.1 was a revised **instruction specification**, not a separately scored production guide in the evidence reviewed here. Do **not** assign v3.1 a numerical grade without an evaluated resulting guide.

### The current division of labor

| Workflow | Authority | Role |
|---|---|---|
| **Hermes + Unified v2 + Astra High** | `Hermes_Unified_Study_Guide_Instructions_v2.md` | **Production master:** maximum source-grounded teaching, historical record, PDF and Markdown |
| **ChatGPT Work v3 / v3.1** | Corresponding `ChatGPT_Work_Unified_Study_Guide_Instructions_*.md` | **Supplemental edition:** shorter, direct reinforcement with strong commented code |

This is not a simple linear replacement of v2 with v3.1. They are **parallel tracks serving different study purposes**.

**Storyboard visual:** A fork in the road. One lane produces a large authoritative library volume labeled “Hermes Unified v2 — comprehensive master.” The other produces a slim annotated workbook labeled “ChatGPT Work v3.1 — focused companion.” The lanes complement rather than cancel each other.

---

# PART VI — THE PRODUCTION KICKOFF BECOMES REUSABLE

## Chapter 9 — From hard-coded Session 24 to a benign kickoff

An experimental kickoff initially contained a specific YouTube recording URL, course identifier, session 24, source folder and output folder. That was acceptable for repeating one test, but it risked reusing the previous session accidentally.

The production request changed the interaction:

1. Read `C:\Projects\StudyProfile\Hermes_Unified_Study_Guide_Instructions_v2.md` in full.
2. Ask for **YouTube URL** and **session number** unless both were already supplied in the current request.
3. Establish the course identity from reliable evidence; never borrow it merely from a previous session.
4. Run the complete existing Unified v2 process.
5. Use the session-specific source and final output directories.
6. Produce, validate and report the Markdown and PDF.

I explicitly asked that the kickoff **not duplicate** the rule file. A review of the 267-line v2 instructions found that most of the longer kickoff's detailed source, annotation, quality-control and output requirements were already defined there. Therefore, the kickoff was shortened to an *invocation contract*, while the Markdown file remained the authoritative *execution standard*.

### The resulting two-file relationship

**Permanent production instructions** (how to do the work):

`C:\Projects\StudyProfile\Hermes_Unified_Study_Guide_Instructions_v2.md`

**Reusable kickoff** (how to start and which session to process):

> Read the authoritative v2 instructions, ask for the new recording and session if missing, execute the complete v2 workflow, preserve previous outputs and report verified results.

### Dynamic path rules

**Evidence/source directory:**

`C:\Projects\StudyProfile\<CourseIdentifier>\<SessionNumber>\`

For this course, an example is:

`C:\Projects\StudyProfile\CSCS1240F26\25\`

**Finished deliverables:**

`C:\Projects\GPT Study Guides\Sessions\Session <SessionNumber>\Finished Guides\`

The instruction standard also requires separate `sources` and `snapshots` evidence organization, validation before reuse, a recorded source manifest, and preservation of older guides. Output names use `_Unified_v2` to distinguish production results from prior Sol, Terra, Astra and Unified experiments.

### Session 25: a practical test of session isolation

At the start of Session 25, Hermes read the submitted YouTube page and mentioned a separate recommended Session 26 video. I was initially concerned it had chosen the wrong lesson. The visible evidence showed otherwise: Hermes identified the requested **Session 25** title and requested captions from the correct video ID, `eU49LGG63rM`. The remark about Session 26 was likely from YouTube's related content. This incident reinforced an operational rule: **the supplied URL and verified video identity determine the source; recommendations and next-video links do not.**

**Storyboard visual:** A production kickoff textbox becomes a reusable slot: “Video URL + Session number.” The permanent Markdown rule file stays locked in the background. An arrow points to dynamically named `Session 25`, `Session 26`, and `Session 29` folders.

---

# PART VII — PRODUCTION RESULTS, EVIDENCE AND QUALITY SCORES

## Chapter 10 — What “production” means in this project

A production run isn't just a large AI response. It has to deliver a *verified, reusable, archival artifact*:

- Identify the exact source recording.
- Retrieve and validate the full transcript, including beginning and ending.
- Capture one frame at every whole-minute target, plus relevant event/closing frames.
- Align classroom language with source images.
- Distinguish visible code states from live terminal executions.
- Generate complete teaching sections with actual values, annotated code and execution traces.
- Add “Key items to scan” and practice/definition materials.
- Preserve historical screen code without fabricating clipped text or unseen operations.
- Create an editable Markdown guide and a print-ready PDF.
- Compare PDF text against Markdown and visually check the PDF.
- Preserve source materials and report gaps.

The first three completed and graded production examples in this record are Sessions **25, 26 and 29**, all using **Hermes Unified v2 with Astra High**.

## Chapter 11 — Session 25: the first full production test

**Recording:** `https://www.youtube.com/watch?v=eU49LGG63rM`  
**Course/session:** CSCS1240F26 / 25  
**Guide:** `CSCS1240F26_Session_25_Counting_and_Indexing_a_Doubly_Linked_List_Unified_v2.md`  
**Reported video duration:** 54 minutes 27 seconds  
**Reported English caption cues:** 1,138  
**Reported captures:** 77 = 55 whole-minute targets + 21 event targets + a near-closing frame  
**PDF:** 24 pages (reported)  
**Available Markdown:** about 11,424 words and 839 lines  
**Review grade:** **94/100 (subjective document-quality rubric)**  
**Weekly allowance:** **83% → 82%**, a **1-percentage-point displayed change**.

### Why the guide was shorter than expected

The nearly one-hour recording yielded 24 pages. At first I worried that Hermes was summarizing too aggressively. The actual Markdown was more focused than the Session 24 experiments: much of the lesson was about counting, indexing, integers, punctuation, data types and exception behavior rather than repeatedly generating huge code revisions. The short length alone was not evidence of missing content.

### Core topics covered

- **`count()`** on a doubly linked list: zero items for an empty placeholder; one for the first occupied node; increment after following each next link.
- **`at(n)`**: find an item by **zero-based position**; index zero needs no link traversal, while the third item is reached by two moves.
- **List size and binary capacity:** eight bits represent 256 distinct patterns, so unsigned values numbered from zero run through 255, not 256. Python integers can expand in representation, subject to practical limits.
- **Operators and collections:** `color[1]`, `2 + 3`, grouping versus tuple construction, list versus tuple mutability, sets and dictionaries.
- **Exceptions and program flow:** one `try` block encompassing multiple calls means an earlier exception prevents later calls in that same block from running.
- **Git history and source evidence:** a terminal result from the count revision must not be attributed to a later index revision simply because the old output is still visible.

### A signature teaching distinction

```python
# Illustrative teaching comparison, not verbatim source:
count = 1  # Counting ITEMS: apple already counts as one occupied item.
count = 0  # Counting MOVES from the head for an index: apple is at position zero.
```

That contrast demonstrated the value of concrete-first instruction. A counter is not inherently right or wrong starting at zero or one; the question is **what it measures**.

### Review outcome

The initial review graded the guide **94/100**, praising completeness relative to its narrower content, annotated Python, state tables and error-control explanations. The review recommended no production rerun. This grade reflects the document's observed teaching quality, **not an independent verification of 94% transcript coverage**.

## Chapter 12 — Session 26: the stress test for source scale and deep reconstruction

**Recording:** `https://www.youtube.com/watch?v=fLQcJno8F-w`  
**Course/session:** CSCS1240F26 / 26  
**Guide:** `CSCS1240F26_Session_26_Linked_List_Protocols_File_IO_and_Generators_Unified_v2.md`  
**Reported duration:** **3:31:59**  
**Reported English caption cues:** **4,715**  
**Reported verified captures:** **244 = 212 whole-minute + 32 event/closing frames**  
**PDF:** **118 pages** (reported)  
**Available Markdown:** approximately **50,572 words, 5,291 lines**, 56 “Key items to scan” sections  
**Reported PDF QA:** all 118 pages rendered and visually reviewed; Markdown/PDF text parity checked; **100 bookmarks**; removable Micro-Learning appendix starts on page 118  
**Review grade:** **96/100 (subjective document-quality rubric)**  
**Weekly allowance:** **82% → 78%**, a **4-percentage-point displayed change**.

### Why this was a major technical milestone

The recording was more than three and a half hours. Hermes initially had a video download with the last roughly seven minutes missing, then **resumed/recovered the missing material** so the guide was not built from a knowingly incomplete recording. It verified every minute target through the closing material and added events where code or results changed between minute marks.

### Topics addressed in the finished guide

1. **Python protocols on a custom linked list:** `__iter__`, `__next__`, `__len__`, `__getitem__`, `__setitem__`.
2. **Node versus value:** a search may return the actual `Item` node, its stored string, a Boolean, or `None`; those are different contracts with different caller consequences.
3. **Git:** commits, detached HEAD, `git restore`, branch discussion, merge conflicts, comparing edits and the distinction between the working file and terminal scrollback.
4. **Mutation and references:** create, append, prepend, insert, extract, clear; distinguish removing an object's references from rebinding a name.
5. **Converting data representations:** `toList()`, `fromList()`, `toFile()`, `fromFile()`; file writing doesn't necessarily print; file reading may append to an already populated chain.
6. **Name shadowing:** naming a variable `list` can hide the built-in constructor; later assigning a function to the same name changes what operations are valid.
7. **File modes:** `"w"`, `"a"`, `"x"` and `"wb"` as distinct strings; overwrite versus append, exclusive creation, and the incompatibility of writing text to a binary stream.
8. **Exceptions and tracebacks:** why a valid open mode can still fail on write; why a missing colon is a parse error; why `mode == "a"` is a comparison rather than an assignment.
9. **Generators:** `yield`, `StopIteration`, paused execution and two independent generator objects traversing the same fruit at different positions.

### An especially important debugging reconstruction

The instructor's file-mode guard contained a defect:

```python
elif hope == "a":  # The caller requests append mode.
    mode == "a"    # BUG: this compares rather than assigning; mode is unbound here.
```

Hermes explained the consequence rather than silently repairing the professor's source. A correct new teaching version can show `mode = "a"`, but the historical source must remain unchanged.

### A concrete file-state trace

Writing apple, banana and grape to a text file produces three lines on disk. Reading the file back into the same three-item chain adds three more nodes in memory. Switching from overwrite (`"w"`) to append (`"a"`) can make the file persistently grow across runs. The guide keeps **file contents, in-memory contents and printed console output** distinct.

### The generator insight

Two generator objects can traverse the same linked list independently. Calls interleaved unevenly produce:

```text
apple
apple
banana
grape
banana
```

The second reader is one value behind the first. Trace tables show why.

### Review outcome

Hermes Unified v2 Session 26 received **96/100** from ChatGPT's document-review rubric. The guide excelled at extensive historical reconstruction, detailed code comments and honest treatment of source evidence. The PDF checks and 100-bookmark count were **reported by Hermes**, not recertified in this storyboard.

## Chapter 13 — Session 29: three subjects, a shorter guide, similar visible usage

**Recording:** `https://www.youtube.com/watch?v=NRfvyLTFC_I`  
**Course/session:** CSCS1240F26 / 29  
**Guide:** `CSCS1240F26_Session_29_Kivy_Interfaces_Bitwise_Operations_and_Bounding_Pi_Unified_v2.md`  
**Reported duration:** **3:24:07.867**  
**Reported caption cues:** **4,563**  
**Reported captures:** **233 = 205 whole-minute + 27 event + 1 closing**  
**PDF:** **53 pages** (reported)  
**Available Markdown:** approximately **25,252 words**, **2,054 lines**, **33 “Key items to scan” sections**  
**Reported code QA:** all **32 annotated Python blocks** exercised with their stated setups; Kivy checked using **stand-in widgets**, not a live GUI launch  
**Reported PDF QA:** all 53 pages visually reviewed  
**Review grade:** **96/100 (subjective document-quality rubric)**  
**Weekly allowance:** **78% → 74%**, a **4-percentage-point displayed change**.

### Why this lesson's guide differed in length

This three-hour-plus lesson moved among an application interface, a numerical/bitwise exercise and conceptual geometry. Although its recording time resembled Session 26, it involved a different number of code revision states and a substantial spoken mathematical explanation. Its 53 pages were shorter than Session 26's 118, but the shorter guide was not automatically evidence of lost content.

### Segment A — Kivy GUI construction and callback behavior

The instructor moved from an empty Kivy `App` with `build()`, to `BoxLayout`, a `Label`, `TextInput`, `AnchorLayout`, a grouped `Button`, and finally an `on_press` callback. The guide traces why the widgets must be added to their parent containers, why size and position hints are parent-relative, and why a button caption such as `2x` is not itself a calculation.

The callback needs an actual application behavior. The text input holds strings, even when its characters look numeric. For the classroom example, **`"34.67"`** is converted to the number 34.67, multiplied by 2 and written back as **`"69.34"`**. The next press uses the current text rather than the original value.

One of the most valuable corrections involved **`except ValueError: pass`**. If the conversion raises `ValueError` and the handler executes `pass`, the original text may remain in the field without a traceback. This is explained by Python exception handling; the guide refuses to claim that Kivy swallowed an *unhandled* exception merely because the GUI did not show an error.

### Segment B — binary, hex masks and shifts

This part covers truth tables for AND, OR and XOR; bit weights; converting decimal 1000 into **`1111101000`**; hex mask `0x0f0` (decimal 240); bitwise `~`, `&`, `|`, `^`; left and right shifts; the expression **`(val >> i) & 1`** that isolates one selected bit; byte order and an interpreter-specific example using `ctypes.sizeof`.

A crucial source correction concerned the shift result:

```python
n = 1000          # The integer, not a fixed-width ten-bit storage cell.
left = n << 3     # 8000 as the full Python integer.
print(left)       # 8000.
```

The professor's display function showed **only the lowest ten bit positions**. These ten bits of 8000, interpreted alone as an unsigned value, equal 832. That does **not** mean Python lost the upper bits. The display window was limited; the integer result was 8000.

### Segment C — bounding π with polygons

The instructor discussed circumference, diameter, Archimedes and the idea of polygons surrounding a circle, but did not demonstrably run a `piCalc.py` program during the closing segment. Hermes labeled its mathematical expansion as independently checked reasoning derived from the spoken explanation.

For a radius-1 circle, the inscribed regular hexagon's perimeter is **6**; the circumscribed regular hexagon's perimeter is **12/√3**. These lengths bound the circle's **circumference**. Dividing both perimeter bounds by the **diameter 2** gives the dimensionless π bound:

**3 < π < 2√3 ≈ 3.4641.**

The important insight was to distinguish a **perimeter or circumference bound** from an actual bound on π.

### Evidence limits observed

Session 29's original captures were **854 × 480**, lower resolution than the 1920 × 1080 frames reported in Sessions 25 and 26. Unreadable characters were explicitly marked. Kivy execution was tested with stand-in widgets and the observed GUI behavior was attributed to the recording, not a live local launch. The closing polygon construction was labeled spoken reasoning plus independently checked calculations.

### Review outcome

Session 29 received **96/100** for strong cross-topic teaching, numerically accurate bitwise calculations, careful source classifications and useful code annotations. Optional improvements included simple diagrams of parent-child Kivy layouts and inscribed/circumscribed polygons. No expensive rerun was justified.

---

# PART VIII — ALL METRICS, SCORES, PERCENTAGES AND LIMITATIONS

## Chapter 14 — The three-run production scorecard

**Historical context:** This production scorecard was created *after* the earlier $20 Plus exhaustion and the upgrade around November 23 to the reported $100 Pro plan. It measures changes on the later Pro meter only; it must not be presented as a direct percentage-cost comparison between subscription plans.

| Metric | Session 25 | Session 26 | Session 29 |
|---|---:|---:|---:|
| Model | Astra High | Astra High | Astra High |
| Markdown standard | Hermes Unified v2 | Hermes Unified v2 | Hermes Unified v2 |
| Recording duration | 0:54:27 | 3:31:59 | 3:24:08 (rounded) |
| Auto-caption cues | 1,138 | 4,715 | 4,563 |
| Verified capture count | 77 | 244 | 233 |
| PDF pages, reported | 24 | 118 | 53 |
| Markdown words, approximately | 11,424 | 50,572 | 25,252 |
| Subjective document-review grade | 94/100 | 96/100 | 96/100 |
| Weekly allowance before | 83% | 82% | 78% |
| Weekly allowance after | 82% | 78% | 74% |
| **Displayed meter change** | **1 percentage point** | **4 percentage points** | **4 percentage points** |

### Combined production totals

- **Recording time:** **7 hours, 50 minutes, 34 seconds** (rounded Session 29 to 3:24:08).
- **Reported caption cues:** **10,416**.
- **Reported frames:** **554**.
- **Reported PDF pages:** **195**.
- **Approximate Markdown words:** **87,248**.
- **Visible weekly allowance movement:** **83% → 74%, a total of 9 percentage points**.
- **Average document-quality grade:** **95.333…/100**, conventionally displayed as **95.3/100**.

### What “increased accuracy” actually means in this story

The infographic uses a chart labeled “Accuracy Trend” with **94 → 96 → 96**. These scores originated from **ChatGPT's subjective document reviews**, not an automated factual-error or transcript-coverage benchmark.

**Calculation from Session 25 to Session 26:**

- Absolute difference: **96 − 94 = 2 rubric points**.
- Relative score difference: **(96 − 94) ÷ 94 × 100 = 2.127659…%**, or about **2.13% relative**.
- This **does not establish an actual 2.13% increase in factual accuracy**, because the two classes differed in length, content and source complexity.

**Calculation from Session 26 to Session 29:**

- Absolute difference: **96 − 96 = 0 rubric points**.
- Relative score difference: **0% on the recorded grade**.
- The defensible observation is **stable high reviewer-rated quality on different lesson topics**.

**Historical Session 24 same-session comparison:**

An earlier subjective assessment estimated **Hermes Unified v2 ≈95/100**, versus **ChatGPT Work Unified v2 ≈87/100** for Session 24.

- Absolute difference: **8 rubric points**.
- Relative difference compared with 87: **8 ÷ 87 × 100 ≈ 9.20% higher reviewer score**.
- Both addressed the same underlying lecture, making this a more direct workflow comparison; it remains a subjective, non-blinded evaluation, not a scientific measure of factual accuracy.

**No numerical score was reliably established here for every original Terra, SOL and Astra standalone output or a production result using Work v3.1.** Any bar chart assigning those numeric values would be an invention.

### How to interpret the weekly usage metric

**Different era, different meter:** On November 23, before this production series, the user reported the $20 Plus plan's five-hour meter falling from about 60% remaining to zero and the weekly allowance reaching zero. The later production points below are on the Pro account and should not be overlaid as if the quotas were identical. Similarly, the approximate credit balance changes **2,500 → 2,300 → 1,700** are a separate measure. See Chapter 2A and Appendix B4.

| Observation | Supported conclusion | Unsupported leap |
|---|---|---|
| Session 25: 83% → 82% | Visible allowance declined by 1 percentage point during the interval | Exactly one percent of all allowed tokens was consumed by the study guide alone |
| Session 26: 82% → 78% | Visible allowance declined by 4 points | The job used exactly four times the compute or money of Session 25 |
| Session 29: 78% → 74% | Another 4-point decline | A 53-page output inherently costs exactly the same as a 118-page output |
| Three runs total: 83% → 74% | Nine-point displayed allowance decline across these reported runs | A stable per-minute price or guaranteed forecast for future runs |

The user-visible allowance meter is rounded, and model/tool/context/verification activity may contribute to changes. These figures are excellent **operational markers** but are not audited token counts or provider invoices.

### Why the 118-page and 53-page runs could have similar meter changes

Sessions 26 and 29 both required processing more than three hours of recording, thousands of caption cues and more than 200 screen captures. The volume and complexity of source verification may contribute substantially to usage independently of finished-page count. The observed equal four-point meter changes are **consistent with that explanation**, but do not prove it as a causal formula.

**Storyboard visual:** Put a third, historically earlier panel ahead of the two below: the **Plus-plan bottleneck** with a five-hour window ending at zero, a separately labeled weekly zero, and approximate credit balances. Show a **vertical change-of-plan divider** before the later Pro run. Two parallel panels, not one misleading graph. Panel A: **“Subjective study-guide quality /100”** showing 94, 96, 96. Panel B: **“Weekly allowance remaining”** with the step path **83 → 82 → 78 → 74**. Put the caution **“Grades are expert-style reviews, not measured accuracy rates”** directly under Panel A.

---

# PART IX — WHAT MAKES THE NEW WORKFLOW TRUSTWORTHY

## Chapter 15 — Evidence has different levels of certainty

| Category | Meaning | Example |
|---|---|---|
| **Observed screen state** | A legible code/error/UI state in a specific captured frame | The editor clearly displays `__len__` or a runtime error |
| **Transcript-supported account** | An instructor statement, question, answer or proposal | The instructor discusses changing a button behavior |
| **Source-based reconstruction** | A bounded combination of compatible visible screen regions, labeled with uncertainty | Assembling a method from two overlapping screenshots |
| **Annotated teaching code** | Guide-added `#` comments and supported setup that explain observed concepts | A fully commented linked-list lookup |
| **Supplemental example** | New instructional code that is not attributed to the professor | A two-generator fixture or safe file-mode experiment |
| **Expected output** | Predicted behavior from code; not proven classroom execution | A reasoned exception or expected print result |
| **Independently verified output** | A separate test, distinct from a classroom run | A fixture computing the shift or generator result |
| **Historical classroom output** | Terminal output actually associated with a supported run | Fruit values from the correct Git revision |

The mature system does not merely label anything visually near an error message as the cause of that error. It distinguishes saved source, unsaved editor buffers, successful and failed checkouts, terminal scrollback, and actual command execution.

An AI that tells a seamless story about classroom code can be **misleading** when the story combines incompatible revisions. The reliable workflow prefers an explicit gap to a plausible invented code line.

## Chapter 16 — Known limits and safeguards

- One-minute sampling plus event frames is still not a continuous video log of keystrokes.
- Auto-captions can mistranscribe code identifiers, punctuation and specialized language.
- A cropped line remains uncertain until another legible source shows the missing portion.
- A safe standalone Python fixture is **not** proof that the instructor's entire project executed that code.
- Stand-in Kivy widgets cannot certify full graphical behavior.
- A source-completeness claim in an agent report is not independently recertified here without the original full transcript and image evidence.
- A PDF inspection claim cannot be independently certified from Markdown alone; the actual PDF must be inspected to verify the claim.
- Review grades and weekly-meter changes should not be confused with measured model accuracy or precise expenses.
- Some teaching examples are explicitly fragments and require an enclosing class or other setup before they can be pasted into VS Code as runnable programs.

These constraints are part of the success story. The goal became **honest, source-grounded teaching**, not confident reconstruction beyond the evidence.

---

# PART X — AUTOMATION AS A FUTURE CHAPTER

## Chapter 17 — From single-session kickoff to a possible scheduled queue

After the successful production runs I asked whether Hermes could accept a list of ten sessions and process them one at a time without a new manual kickoff for each video. The design under discussion was:

1. Read the permanent Unified v2 Markdown instructions.
2. Select the next pending URL and session number.
3. Execute source acquisition and the entire guide workflow.
4. Verify the Markdown/PDF output.
5. Record completion and move to the next pending session.

I then considered using a **cron job** to trigger this rather than constructing an elaborate queue controller. At this milestone, **the cron/queue arrangement was proposed, not demonstrated as an installed, successful unattended system**. This must not be described as a completed achievement in the eventual PDF.

The key separation for future automation is **quality standard (Unified v2)** versus **work assignment (URL and session)** versus **scheduler and status (when and whether a job should run)**.

**Storyboard visual:** The existing three completed guides form a solid pathway; a dotted arrow points to a future ten-item scheduled batch with a label **“Next phase — proposed.”**

---

# PART XI — MY FINAL OPERATING PHILOSOPHY

## Chapter 18 — The lessons learned about models, language and artifacts

The greatest insight was that **better model output does not come only from choosing a model**. Quality also depends on a carefully designed instruction file, reliable source retrieval, the teaching method, deliberate error correction and production quality control.

**Model choice:** Astra High emerged as my preferred production engine for source-grounded completeness. Terra XHigh demonstrated the importance of scannable terms and plain definitions. SOL 6.2 High helped define the style of useful, adjacent Python `#` comments.

**Markdown language:** The instruction specification evolved from two overlapping prompt files into Unified v1 and then v2. The pivotal v2 edit was **controlled repetition**: teach the mechanism fully without restating it needlessly, and put long historical evidence in a separate appendix.

**Teaching design:** Concrete values, code comments, trace tables and quick-reference sections work together. A comment alone cannot replace the explanation; a table alone cannot replace the causal story; a glossary alone cannot teach the behavior.

**Parallel models:** ChatGPT Work v3 and v3.1 are not simply later replacements for Hermes v2. They serve an alternate **concise companion** role. Version 3.1's universal comment requirement improves study usefulness without rewriting the historical source.

**Evidence discipline:** Accurate teaching requires distinguishing what was said, seen, inferred, independently tested and actually executed in class.

**Production:** A successful job means complete evidence preparation, valid Markdown, a matching readable PDF, appropriate quality gates, honest limitations and preserved original work.

**Cost consciousness:** The earlier $20 Plus five-hour/weekly allowance exhaustion and approximate Hermes credit drawdown showed why the workflow needed operational headroom. The $100 Pro upgrade around November 23 increased available continuity, and later weekly-meter markers let me compare the visible cost of experiments and production runs, but should not be treated as exact accounting or as directly comparable to Plus quotas. More output is not the sole measure of quality, and less output is not necessarily better value.

## Chapter 19 — The November 2026 milestone

By this point in the project, I had:

0. **Moved beyond the $20 Plus bottleneck:** reported an exhausted five-hour and weekly allowance during November 23 experimentation, observed an approximate 2,500 → 1,700 Hermes credit drawdown, and upgraded to the reported $100 Pro plan to sustain the work.

1. Moved beyond a transcript-summary workflow toward a **full teaching companion** designed for my syntax and comprehension needs.
2. Compared **Terra XHigh**, **SOL 6.2 High** and **Astra High** and identified strengths to retain in the instructions.
3. Created a unified, authoritative Markdown specification by consolidating the two older instructional files.
4. Revised v1 into **Hermes Unified v2**, enforcing comprehensive coverage alongside controlled repetition and strong historical-source discipline.
5. Run comparative Session 24 experiments with Hermes and ChatGPT Work, exposing a meaningful tradeoff between volume, coverage and concise reading style.
6. Developed **ChatGPT Work v3** and **v3.1** to support a more focused, comprehensively commented supplemental teaching edition.
7. Made the kickoff reusable, requesting a new video URL and session and resolving dynamic directories without reusing old hard-coded session details.
8. Completed three documented Astra High / Hermes Unified v2 production guides: **Session 25 (24 pages, 94/100), Session 26 (118 pages, 96/100) and Session 29 (53 pages, 96/100)**.
9. Reported a combined **195 finished PDF pages**, roughly **87,248 Markdown words**, **10,416 caption cues** and **554 captures**, with a weekly-meter change from **83% to 74%**.
10. Identified a feasible next-stage direction: a **scheduled queue of recordings**, not yet documented as an unattended production deployment.

### The story in a sentence

> **I stopped asking AI to summarize my lectures and taught an AI-driven workflow how to become a careful, source-grounded Python teaching assistant—then refined its language, compared models, verified its evidence, measured usage and put it into repeatable production.**

### Closing reflection

What matters most isn't a 118-page PDF or a score of 96. It is learning to articulate the exact kind of teaching I need, test whether a model and an instruction file meet those needs, and revise the process without losing the source record. I now have a repeatable way to turn recordings into learning materials that help me understand and reproduce the code.

**From a transcript to an understanding. From a prompt to a process. From an experiment to production.**

---

# APPENDIX A — THE EXACT FILES AND PROJECT STRUCTURE

## A1. Instruction file lineage

| Exact filename | Role | Status |
|---|---|---|
| `addon study guide enhanment.txt` | Early kickoff/add-on | Historical predecessor |
| `Experimental_Study_Guide_Reusable_prompt.txt` | Detailed reusable transcript/screenshots/guide workflow | Historical predecessor |
| `Hermes_Unified_Study_Guide_Instructions_v1.md` | First authoritative consolidated version | Earlier unified experiment |
| `Hermes_Unified_Study_Guide_Instructions_v2.md` | Clarified, controlled-repetition instruction standard | **Current production authority** |
| `ChatGPT_Work_Unified_Study_Guide_Instructions_v3.md` | Work-specific alternative with explicit execution contract | Supplemental experimental track |
| `ChatGPT_Work_Unified_Study_Guide_Instructions_v3_1.md` | Global annotated-code quality requirements with preserved source fidelity | Supplemental instruction upgrade |

The instruction files were available to inspect for this storyboard. The narrative does not assert that every version was used for an equally long production history.

## A2. Session 24 model comparison and experiment artifacts

- `CSCS1240F26_Session_24_Worked_Teaching_Companion_Terra_XHigh.md`
- `CSCS1240F26_Session_24_Worked_Teaching_Companion SOL62_High.md`
- `CSCS1240F26_Session_24_Worked_Teaching_Companion_Astra_High.md`
- `Session_24_Worked_Teaching_Companion_Unified_v1.md`
- `Session_24_Worked_Teaching_Companion_Unified_v2.md`
- `Session_24_Worked_Teaching_Companion_Unified_v2_GPT.md`
- `Session_24_Worked_Teaching_Companion_ChatGPT_Work_Unified_v3.md`

**Important:** `...Unified_v2.md` is the Hermes comprehensive result; `...Unified_v2_GPT.md` is the ChatGPT Work counterpart. They are not interchangeable.

## A3. Production artifacts

- `CSCS1240F26_Session_25_Counting_and_Indexing_a_Doubly_Linked_List_Unified_v2.md`
- `CSCS1240F26_Session_26_Linked_List_Protocols_File_IO_and_Generators_Unified_v2.md`
- `CSCS1240F26_Session_29_Kivy_Interfaces_Bitwise_Operations_and_Bounding_Pi_Unified_v2.md`

## A4. User-side Windows paths

**Source root:** `C:\Projects\StudyProfile\`  
**Course/session evidence:** `C:\Projects\StudyProfile\CSCS1240F26\<SessionNumber>\`  
**Production rule file:** `C:\Projects\StudyProfile\Hermes_Unified_Study_Guide_Instructions_v2.md`  
**Final Markdown and PDF:** `C:\Projects\GPT Study Guides\Sessions\Session <SessionNumber>\Finished Guides\`

These historical project paths are user-provided. This storyboard does not claim to have inspected the present contents of the Windows folders.

---

# APPENDIX B — REPRODUCIBLE METRIC ARITHMETIC

## B1. Durations

| Session | Seconds | Duration |
|---|---:|---|
| 25 | 54 × 60 + 27 = 3,267 | 0:54:27 |
| 26 | 3 × 3,600 + 31 × 60 + 59 = 12,719 | 3:31:59 |
| 29 | 3 × 3,600 + 24 × 60 + 8 = 12,248 | ~3:24:08 |
| **Total** | **28,234** | **7:50:34** |

## B2. Reviewer grade calculations

```text
(94 + 96 + 96) / 3 = 95.333333... /100
96 - 94 = 2 points
(96 - 94) / 94 * 100 = 2.127659574...% relative change in reviewer grade
96 - 96 = 0 points

Earlier same-session experimental comparison:
95 - 87 = 8 points
(95 - 87) / 87 * 100 = 9.195402298...% relative reviewer-score difference
```

## B3. Evidence and output totals

```text
Caption cues: 1,138 + 4,715 + 4,563 = 10,416
Captured frames: 77 + 244 + 233 = 554
PDF pages: 24 + 118 + 53 = 195
Markdown word counts: 11,424 + 50,572 + 25,252 = 87,248
Weekly allowance: 83% - 74% = 9 percentage points
```

These are totals from *three production sessions*, not counts of unique conceptual lessons. The counts of captions, screenshots and PDF quality checks are as reported by Hermes; Markdown word counts are file-derived approximations.

## B4. Early Plus period: reported allowance and Hermes credit arithmetic

```text
Reported before the Pro upgrade, around November 23:
  Plus five-hour meter: ~60% remaining → 0% remaining
  Plus weekly meter: reached 0% remaining
  Approximate credit balances: 2,500 → 2,300 → 1,700

Balance-change arithmetic:
  2,500 - 2,300 = 200 credits = 8.0% of 2,500
  2,300 - 1,700 = 600 credits ≈ 26.09% of 2,300
  2,500 - 1,700 = 800 credits = 32.0% of 2,500

Reported subscription change:
  $20 Plus → $100 Pro around November 23, 2026
  $100 / $20 = 5× nominal subscription price
  This does NOT establish 5× tokens, 5× usage allowance, or 5× productivity.
```

**Limitations:** The credit amounts are approximate balance snapshots, not a transaction history; individual model calls and sessions cannot be reconstructed from them. The five-hour meter's approximately 60% beginning was a reported marker, not a controlled pre/post measurement of one guide. The weekly zero and later Pro 83→74 reading occur under different entitlements and should not be plotted as a continuous, same-scale consumption line. The subscription figures describe reported plan prices at the time, not a present-day plan catalog.

---

# APPENDIX C — GEMINI PDF DESIGN BRIEF

This Markdown should become a **detailed illustrated book or case-study report**, not a one-page summary of the infographic.

| PDF section | Suggested illustration | Message to preserve |
|---|---|---|
| Personal starting point | Recorded video, a source screenshot, transcript and learner notes | Need understanding, not summary |
| Early prompts | Two documents, evidence capture workflow | Reliable retrieval and grounding |
| **Plus-limit bottleneck, November 7–23** | Two exhausted usage meters and approximate credit balance staircase | Why sustainable production became an explicit objective |
| **Pro upgrade around November 23** | Distinct subscription-capacity timeline milestone | More operational headroom did not replace process optimization |
| Three-model bake-off | Terra/SOL/Astra cards | Complementary strengths, not invented accuracy percentages |
| Unified v1 | Two predecessor documents merging | Single authority |
| Unified v2 | Dense explanation → annotated teaching code, trace table and appendix | Full substance, less repetition |
| ChatGPT Work v2 | Master-versus-condensed Session 24 comparison | Coverage-versus-concision tradeoff |
| Work v3 and v3.1 | Paired historical source and guide-commented code | Clear code without falsifying history |
| Production kickoff | URL/session slots → dynamic paths | No hard-coded prior recording |
| Session 25 | Apple/banana/grape nodes and counters | First production success |
| Session 26 | Git version timeline, file state, two paused generators | Long-source comprehensive verification |
| Session 29 | Kivy parent-child tree, ten-bit shift window, two hexagons around a circle | Corrective reasoning across subjects |
| Measured production | Separate grade and allowance charts | No confusion between review grade and usage |
| Future | Dashed queue/cron extension | Planned, not claimed complete |
| Closing | Shelf of three guides and concluding quote | From transcript to process to production |

### PDF fidelity rules

1. Retain exact model names and distinction between **Hermes** and **ChatGPT Work**.
2. Preserve exact Markdown filenames, notably `_v3_1.md` for the Work comment-contract upgrade.
3. Separate the **Session 24 same-lecture experimental comparison** from **Sessions 25, 26 and 29 production grades**.
4. Label grades **“Subjective document-quality review /100”**, not “verified factual accuracy percent.”
5. If showing +2 points / +2.13%, explain it compares **different session documents**, so does not prove causal improvement in model accuracy.
6. Show later Pro meter movement **83 → 82 → 78 → 74** as **percentage points of remaining weekly allowance**. Separately show the earlier Plus five-hour meter **~60% → 0%**, its weekly allowance **0%**, and approximate credit balances **2,500 → 2,300 → 1,700**. Never connect these as a continuous same-plan trend.
7. Use the verified aggregate recording duration **7:50:34**, not an erroneous eight-hour estimate.
8. Do not invent grades for all individual model runs or for Work v3.1 when none was recorded here.
9. Treat source and PDF checks as reported unless the original sources/PDFs are independently examined.
10. Treat cron/queue automation as **proposed**.
11. Add helpful technical visuals without removing concrete Python syntax or example values.
12. Preserve the first-person educational voice and the final quote; the point is an enduring story of progress.
13. Include the reported **$20 Plus → $100 Pro** transition around November 23, 2026. Avoid invented exact dates, session-by-session credit costs or the false inference that price ratios equal usage ratios.

### Ready-to-paste Gemini instruction

> Create a premium, comprehensive, book-length PDF titled **“From Recorded Lectures to a Verified AI Teaching-Companion Production System”** using the supplied Markdown storyboard as the authoritative historical record. Preserve the full chronology from the early prompts, **the November 7–23 $20 Plus-plan usage-limit bottleneck and approximate Hermes credit drawdown, the reported $100 Pro upgrade around November 23**, through the Terra/SOL/Astra model comparison, Hermes Unified v1/v2, ChatGPT Work v2/v3/v3.1, reusable kickoff and production Sessions 25/26/29. Do not collapse it to the original short infographic. Build a polished editorial report with a table of contents, numbered chapters, page numbers, consistent code formatting, informative diagrams, session case studies, verified file-name inventory and metric appendix. Distinguish review grades from objectively measured accuracy; separate historical same-session comparison from later cross-session production scores. Include the later Pro weekly meter progression 83% → 82% → 78% → 74% and aggregate duration 7:50:34. In a distinct earlier panel show the reported Plus five-hour allowance ~60% → 0%, weekly exhaustion at 0%, and approximate credit balances 2,500 → 2,300 → 1,700; do not put the two plans on one numerical allowance scale. Do not invent missing experiments, pages, source records or unattended automation achievements. Treat agent-reported source/PDF checks as reports, not independent certification. Use a respectful, detailed first-person narrative, clear visual hierarchy, readable tables, and high-quality bookmarked PDF output. The finished book should memorialize the development of a precise source-grounded AI learning workflow and explain the reasons behind every major version change.

---

## A note to future me

The achievement is not merely that an AI generated 118 pages in a single session. The story includes the period when the $20 Plus allowance ran out while I was still learning how to produce those guides—and the decision to upgrade, refine the instructions and measure production instead of accepting that interruption as normal. It is that I learned to specify what a good Python teaching companion must do, compared models instead of trusting the first answer, corrected the prompt language, preserved evidence for verification, and built a process that can be used for the next lecture without beginning again.

**From a transcript to an understanding. From a prompt to a process. From an experiment to production.**
