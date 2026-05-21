---
name: annual-report-microsite
description: Reads any Annual Report PDF, discovers its structure dynamically, applies universal editorial reasoning to identify homepage sections, and generates a snippet-length document wireframe with reasoning for team review.
---

# Annual Report → Microsite Skill

## Purpose

This skill replaces the manual process of a team reading through a client's Annual Report PDF and deciding what goes on the homepage microsite. It works for **any company, any industry, any report structure**.

It does not assume page numbers. It does not assume chapter names. It learns the structure from the PDF itself first, then applies universal editorial rules.

---

## Mental Model — The Homepage Is an Index, Not a Destination

Before extracting anything, hold this idea: **the homepage is a teaser, not the content itself.** Every section on the homepage has only two jobs:

1. Communicate the single strongest idea from that chapter in **one verbatim sentence**.
2. Earn the click to a dedicated internal page where the full chapter lives.

If a section feels like it's *delivering* the content rather than *previewing* it, it's too long. Cut. The depth always belongs on the linked internal page — never on the homepage itself.

---

## Step 1 — Extract the Entire PDF

Run this on **any** PDF path provided:

```python
from pdfminer.high_level import extract_pages
from pdfminer.layout import LTTextBox

pdf_path = "<PDF_PATH_FROM_USER>"

page_texts = {}
for page_layout in extract_pages(pdf_path):
    pg = page_layout.pageid
    texts = []
    for element in page_layout:
        if isinstance(element, LTTextBox):
            t = element.get_text().strip()
            if t and len(t) > 5:
                texts.append(t)
    page_texts[pg] = texts
```

---

## Step 2 — Discover the Report Structure (Never Assume It)

Different reports have different chapter names, different section orders, different page counts. Your job is to find the structure first.

### 2a. Find the Table of Contents

Scan pages 1–15. The TOC page is identified by ANY of these patterns:
- A page containing a numbered list where each line has a section name + page number
- Lines matching patterns like: `22   Message from the Chairperson`  or  `52/ 54-91 | STRATEGIC PRIORITIES`
- A page with the words: "Contents", "In the report", "Index", "What's Inside"
- Multiple short lines that are all-caps section titles clustered together

Once found, extract every chapter/section name and its page range. This is your **content map**.

### 2b. Identify the Report's Cover Narrative

Read pages 1–3. Look for:
- The year's **theme/tagline** — usually 4–12 words, often on the cover visual or inside cover spread. This is NOT the company name. Example: *"Leadership in Enterprise — Infrastructure to Intelligence"* — this is the year's brand story.
- The **company's one-line identity** — what they do and their scale claim
- Any **"Year in Numbers"** spread — a page with 5–10 large numbers and labels

### 2c. Detect the Reporting Framework (Critical)

Look at how the report itself structures its non-financial story — this decides how sustainability content is laid out on the homepage. There are two recognised patterns:

- **Pattern A — ESG-led:** The TOC has a chapter literally called "ESG" or "Sustainability Report" and the content is organised into Environment / Social / Governance.
- **Pattern B — Capitals-led (Integrated Reporting <IR> Framework):** The TOC names individual capitals — Financial, Manufactured, Human, Intellectual, Social (or Social & Relationship), Natural. Governance lives separately under the Corporate Governance Report.

**Decision test:** does the TOC say "ESG" / "Sustainability," or does it say "Social Capital" / "Natural Capital" / "Six Capitals"? Whichever vocabulary the report uses, the homepage uses verbatim. Never invent a chapter division the report itself doesn't make.

### 2d. Map Every Chapter to Its Category

For every chapter/section found in the TOC, classify it into one of these universal categories:

| Category | What to look for | Homepage? |
|---|---|---|
| **Company identity** | About us, at a glance, who we are, corporate overview | ✅ Always |
| **Annual theme narrative** | A thematic chapter built around the year's tagline (e.g., AI, "Take the Lead," "Next Thirty") | ✅ If present |
| **Year highlights** | Year in review, performance highlights, FY summary | ✅ Always |
| **Quarterly highlights** | Q1/Q2/Q3/Q4 milestones, events, signings | ✅ If present |
| **Leadership voice** | Chairman/MD/CEO/CFO/VC letters, messages, Q&A | ✅ Always |
| **Value Creation Model** | Value creation model, business model, capitals model, input-output model, six capitals | ✅ If present |
| **Individual Capital chapter** | Financial Capital, Human Capital, Social Capital, Natural Capital, etc. as standalone chapters | ✅ If report uses Pattern B |
| **Investment story** | Investment case, why invest, value proposition | ✅ If present |
| **Strategy** | Strategic priorities, pillars, focus areas, roadmap | ✅ Always |
| **Financial KPIs** | Key performance indicators, financial highlights | ✅ Always |
| **Operational KPIs** | Production, capacity, volumes, efficiency | ✅ Always |
| **Products/Segments** | Product lines, business segments, therapeutic areas, divisions | ✅ If multi-product |
| **ESG/Sustainability** | Environment, social, governance, CSR | ✅ If report uses Pattern A |
| **Customer / Case stories** | Customer stories, case studies, client engagements | ✅ If present |
| **Awards/Recognition** | Rankings, certifications, accolades | ✅ Always |
| **Operational detail** | Plant-wise, unit-wise, facility-by-facility data | ❌ Internal page |
| **Risk management** | Risk registers, mitigation strategies | ❌ Internal page |
| **Statutory/legal** | Directors' report, board committees, compliance | ❌ Internal page |
| **Financial statements** | Balance sheet, P&L, cash flow, notes | ❌ Download only |

> **Key rule:** A section is homepage-worthy if it answers one of these three questions for a first-time reader:
> 1. *Who is this company and how big are they?*
> 2. *How did they perform this year?*
> 3. *What are they building toward?*

---

## Step 3 — Read the Narrative and Pull the Sharpest Sentence

The TOC tells you the skeleton. The narrative reading tells you *what the company wants to say* — which is what your team actually does when they read through the report.

For each homepage-eligible chapter, read the **first 2–3 pages to understand the chapter**, then extract a **single verbatim sentence** that captures its central idea. Do not extract paragraphs. Do not stitch sentences together — that produces invented content. If no single sentence does the job, extract the strongest verbatim phrase and append `[Requires client to provide bridging sentence]`.

### What to look for and why:

**Cover / Year Highlights chapter:**
- The year tagline → Hero headline (verbatim, ≤12 words)
- The 5–8 numbers the company chose to feature prominently → KPI strip
- *Why it matters:* These are the company's own editorial choices. If they chose to lead with sustainability rank over revenue, that tells you their brand priority.

**Annual Theme chapter (if present):**
- The single sentence that frames the year's narrative thesis
- The 3–5 sub-ideas / stages the theme breaks into (e.g., TCS's "Enterprise AI Opportunity → Full-Stack AI → AI-led Transformation → AI with Purpose")
- *Why it matters:* The theme chapter is the editorial spine of the entire report. The homepage needs a dedicated section that walks the visitor through the theme's sub-ideas — a typographic anchor, staged scroll, or sequential reveal pattern.

**Quarterly Highlights (if present):**
- For each quarter, one event/announcement + one supporting sentence verbatim
- *Why it matters:* Quarterly storytelling gives the year a rhythm. Treat as a stacked-card or carousel pattern on the homepage.

**Leadership Voices / Messages:**
- Extract EVERY top management message present in the report (Chairman, Vice Chairman, MD, CEO, CFO, etc).
- The single sharpest verbatim sentence from each letter → pull quote (≤50 words)
- **Sequence rule:** Order them strictly by designation hierarchy (Chairman → Vice Chairman → MD → CEO → CFO). No section may separate them.
- *Why it matters:* Leadership sets the tone. Every leader featured in the report must appear consecutively on the homepage in proper rank.

**Investment Case / Why Invest section (if present):**
- For each reason tile, extract one verbatim line (≤30 words)
- Skip this section entirely if the report has no Investment Case chapter — do not invent one
- *Why it matters:* When this section exists, the company wrote it specifically for investor audiences. Use it almost as-is.

**Strategic Priorities / Pillars:**
- Extract each priority name + one verbatim sentence describing it (≤30 words per tile)
- If more than 7 priorities, show only the first 5–6; link to full strategy page
- *Why it matters:* Forward-looking content balances the backward-looking KPIs.

**Sustainability — Pattern A (ESG-led report):**
- Split into 3 distinct sections: **Environment, Social, Governance**
- For each: one intro sentence verbatim + 2–3 headline numbers
- *Why it matters:* When the report uses ESG vocabulary, mirror it.

**Sustainability — Pattern B (Capitals-led / Integrated Reporting report):**
- Treat each capital named in the report as its own homepage section (Social Capital, Natural Capital, plus Financial/Human/Intellectual/Manufactured as relevant)
- For each: one intro sentence verbatim + 2–3 headline numbers
- Do NOT force an E/S/G split — the report didn't use that structure
- Governance content stays under Corporate Governance and does NOT get duplicated on the homepage
- *Why it matters:* Faithful to the report's own framework. Inventing a chapter division the report doesn't make violates the verbatim rule.

**Value Creation Model / Business Model chapter (if present):**
- One verbatim sentence describing what the model does
- Note the chapter's core flow — what goes IN (inputs/capitals) and what comes OUT (outputs/outcomes)
- **Isolation Rule:** Only extract Value Creation content from the Value Creation chapter. Do not pull strategy or capital-detail content into this section.
- *In the wireframe:* Describe it as an interactive flow diagram (often a horizontal-scroll SVG) with a 1-sentence snippet per capital/pillar.

**Strategy / Strategic Priorities chapter:**
- Per priority: name + one verbatim sentence (≤30 words)
- **Isolation Rule:** Only extract Strategy content from the Strategy chapter. Do not mix it with Value Creation or Operations.

**Customer / Case Stories (if present):**
- Per story: client name + industry + one verbatim snippet (≤25 words)
- Curate to 4–6 strongest; link the rest to an internal stories page
- *Why it matters:* Case stories carry the proof of strategy. When a report features them prominently, the homepage should too — place mid-page, not at the bottom.

**Financial Highlights:**
- **Always showcase:** Revenue, EBITDA, PAT, and Dividend Declared.
- If (and only if) these specific metrics are unavailable, show the primary financial numbers provided.

**Awards / Recognition chapter:**
- List every award + awarding body (≤15 words per entry)
- Filter: keep only **international, national, or industry-body** awards on homepage; regional/community awards go on internal pages
- *Why it matters:* Third-party validation builds trust with first-time readers.

**Design System Extraction (Global throughout report):**
- **Colors:** Identify the primary brand color, secondary accents, and background colors used prominently. Pay special attention to **gradients** (e.g., "Navy to Teal linear gradient used on section backgrounds").
- **Typography:** Identify the font stack used. Extract specific styling rules for:
  - **Headings (H1/H2):** Font family, weight, capitalization, primary color
  - **Body Copy:** Font family, weight, color
  - **Highlights/Callouts:** Pull quotes, large KPI numerals (e.g., "Italics Serif for quotes", "Oversized bold sans-serif for numbers")
- **Hero Treatment:** Note how the report's cover handles the headline — typographic only, photographic, illustrated, or numeral-led. This guides the digital hero's direction (video, typographic, animated-SVG numeral, masthead).
- **Motion language:** Note any sense of motion the report implies (timeline, sequential reveal, staged narrative). The digital version translates this into scroll-triggered animation, parallax, stacked decks, or fanned-arc carousels.
- *Why it matters:* The wireframe needs to give the UI designer the exact visual and motion rules established by the print designers.

**Graphs and charts throughout the report:**
- While reading each chapter, note every chart, graph, or data visualisation that appears
- For each: chart type (bar, line, pie, donut, area), what it measures, how many data points, time range
- Flag the top 4–6 charts most impactful for a homepage audience — prioritise: revenue trend, production trend, ESG/capital metric trend
- *Why it matters:* Static PDF graphs become animated, interactive web charts. Specify which data series to chart and in what format.
- *In the wireframe:* For each chart, specify — Chart type | X-axis | Y-axis | Data series | Years covered | Interaction. Charts are usually embedded into Performance/KPI/Capital sections, not a dedicated standalone block.

---

## Step 4 — Write the Reasoning

Before writing the wireframe, document your reasoning. This is your team's audit trail.

For every section chosen, write:

```
SECTION: [Name]
SOURCE: PDF pages [X–Y], chapter "[exact chapter title from TOC]"
CHOSEN BECAUSE: [1–2 sentence editorial reason using Who/How/What framework]
CONTENT USED: [Specific numbers, the single sentence extracted]
EXCLUDED: [What from this chapter was left out — and which internal page it goes on]
```

For every section rejected, write:

```
REJECTED: [Chapter name]
SOURCE: PDF pages [X–Y]
REASON: [Why it stays off the homepage]
GOES TO: [Which internal page it belongs on]
```

---

## Step 5 — Draft the Document Wireframe

Produce a **structured markdown document** — a specification for designers to work from. Not HTML, not a prototype, not a mockup. A document your team can read, mark up, and approve.

### Statutory Content Rule (CRITICAL)
**Annual Reports are statutory/legal documents. You MUST extract text verbatim.**
- Do NOT rewrite, summarise, paraphrase, or invent text.
- If a section needs a short blurb, copy the exact sharpest sentence from the PDF.
- If an exact snippet isn't available, state `[Requires client to provide [X] word snippet here]` instead of making one up.

### Hard Word Budgets (Non-Negotiable)

Every surface on the homepage has a cap. Enforce them.

| Surface | Max words | What to put there |
|---|---|---|
| Hero tagline | 12 | Year theme, verbatim from PDF cover |
| Hero subtitle | 15 | Supporting line, verbatim |
| About blurb | 60 | One sentence on identity + one on scale |
| KPI value | 1 number + 1 unit | No prose |
| KPI label | 3 words | "Revenue," "Operating Margin," etc. |
| Leadership pull quote | 50 | The single sharpest verbatim sentence from the letter |
| Section intro (Strategy / Capital / ESG / Investment Case / Theme) | 50 | One verbatim sentence stating the chapter's thesis |
| Card / tile body (per pillar, capital, story, segment, theme stage) | 30 | One verbatim sentence |
| Award entry | 15 | Award name + awarding body |
| Customer story snippet | 25 | The single line that captures the engagement |
| Quarterly highlight body | 30 | One verbatim sentence per quarter |

### Mandatory "Read More" Exits

**Every homepage section must specify the internal page it links to** in the wireframe (e.g., `LINKS TO: chairman.html — full letter`). If no internal page exists for that content, flag it as `[Internal page required]`. The homepage snippet stays at snippet length regardless.

### Universal homepage section order (with flexibility):

The top sections have a specific flow rule: KPIs and About Company can shuffle positions depending on what the company emphasizes. Leadership must always be in the top 4. Annual Theme (if present) sits between Hero and About.

```
0.   DESIGN SYSTEM         → Colors (incl. gradients) + Typography + Hero treatment + Motion language
0.5  NAVIGATION ARCHITECTURE → Top-level groupings + which internal pages exist
     -----------------------------------------------------
1.   NAVIGATION            → Logo | Links | TOC overlay or mega-dropdown
2.   HERO                  → Tagline (Verbatim from PDF cover)
2.5  ANNUAL THEME          → If the report has a thematic chapter — staged narrative of the year's idea
     -- Shuffle Section 3 & 4 based on report emphasis: --
3.   COMPANY AT A GLANCE   → Who we are + scale facts (Verbatim)
4.   KPI DASHBOARD / HIGHLIGHTS → Financial + Operational figures (Real PDF numbers only)
     -----------------------------------------------------
5.   LEADERSHIP VOICES     → ALL top management messages ordered by designation hierarchy (Verbatim snippets)
6.   BOARD OF DIRECTORS    → If present in report — images, names, "Read More" CTA only (no bios)
7.   GEOGRAPHICAL FOOTPRINT → If present as a distinct chapter — operations map / presence
8.   VALUE CREATION MODEL  → Interactive flow diagram (Sourced ONLY from Value Creation chapter)
9.   CUSTOMER / CASE STORIES → If present — 4–6 strongest stories with verbatim snippets
10.  YEAR IN REVIEW / QUARTERLY HIGHLIGHTS → If present — stacked deck or carousel of Q1–Q4 milestones
11.  INVESTMENT CASE       → If present in report — reason tiles + verbatim snippets
12.  STRATEGY              → Strategic priority tiles + verbatim snippets (Sourced ONLY from Strategy chapter)
13.  PRODUCTS/SEGMENTS     → Cards per product/segment + verbatim snippets
14.  SUSTAINABILITY        → Pattern A: 3-part E/S/G split  OR  Pattern B: per-capital sections (Social, Natural, etc.)
15.  AWARDS                → Award grid (Real names and awarding bodies)
16.  FOOTER                → Navigation + downloads (full PDF, financial statements)
```

> **Adapting for different companies:**
> - If the company is **single-product** (e.g., a bank), replace Section 13 with business verticals or geographies
> - If the company has **no Investment Case chapter**, skip Section 11 — do not invent one
> - If the company has **no Board section** in its report flow, skip Section 6 — do not invent one
> - If the report is **small** (under 100 pages), some sections may not exist — skip gracefully
> - If the company leads with **people/CSR** (common for consumer or healthcare firms), promote Customer Stories higher
> - If the report uses **Pattern B** (Capitals-led), Section 14 expands into multiple individual capital sections (Social Capital, Natural Capital, etc. each as standalone scroll sections)

### Format for each section in the wireframe:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SECTION [N]: [Section Name]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LAYOUT:       [Full width / 2-col / 3-col grid / staged scroll / arc carousel / etc.]
SOURCE:       PDF Pages [X–Y] — "[exact chapter name]"
LINKS TO:     [Internal page that holds the full chapter content]
PRIORITY:     [Hero / High / Medium]

WHY THIS SECTION:
[1–2 sentence editorial reasoning]

CONTENT (snippet only — see word budgets):
[The single verbatim sentence / KPI numbers / section title]

DESIGN NOTES:
[Colour treatment, imagery direction, motion/interaction primitive]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Step 6 — The Cut

Before exporting `wireframe.md`, re-read every CONTENT block you've drafted and apply the word budgets in Step 5. For any block over budget:

1. Keep only the single sharpest verbatim sentence.
2. Move the rest into a note under the linked internal page (or flag for client).
3. Re-confirm the section terminates in a `LINKS TO:` line.

The homepage wireframe should fit on roughly **3–4 printed A4 pages of markdown**. If it's longer, you haven't cut enough. The homepage is an index; depth lives on internal pages.

---

## Output Files

| File | What it is |
|---|---|
| `wireframe.md` | Document wireframe — layout + snippet-length content per section |
| `section_reasoning.md` | Full reasoning log — every section chosen or rejected with justification |

---

## Prompt Template (Copy and Use)

> **"Read the annual report PDF at [PATH]. Using the Annual Report Microsite Skill:**
> 1. Extract all text from the PDF page by page
> 2. Find the Table of Contents — map every chapter to its page range
> 3. Detect the reporting framework — ESG-led (Pattern A) or Capitals-led (Pattern B)
> 4. Classify every chapter using the universal homepage categories
> 5. Read the narrative content from every homepage-eligible chapter and extract the single sharpest verbatim sentence per section (do NOT pull paragraphs)
> 6. While reading, dynamically extract the global **Design System** (Colors, gradients, typography, hero treatment, motion language)
> 7. Document your section reasoning — why each section was chosen or rejected, and which internal page receives the rest of the content
> 8. Apply Step 6 — The Cut — enforce word budgets and `LINKS TO:` exits on every section
> 9. Generate `wireframe.md` — starting with the Design System + Navigation Architecture blocks, followed by the snippet-length wireframe"

---

## Universal Editorial Rules (Apply to Any Client)

| Rule | Detail |
|---|---|
| **STATUTORY RULE: Verbatim ONLY** | Never rewrite, paraphrase, or invent text. Headings stay as headings; copy stays as copy from the PDF. |
| **Homepage = Index, not Destination** | Every section is a teaser. One verbatim sentence per surface. Depth lives on internal pages. |
| **Single Sentence per Snippet** | Never stitch sentences. Pick the sharpest one verbatim. If none works, flag `[Requires client snippet]`. |
| **Word Budgets are Hard Caps** | The Step 5 budgets are non-negotiable. Step 6 (The Cut) enforces them. |
| **Every Section has a `LINKS TO:`** | If the internal page doesn't exist, flag `[Internal page required]`. Snippet stays snippet. |
| **Strict Section Isolation** | Use content ONLY from the start to the end of a section's specific chapter. Do not pull content from other chapters. |
| **Real data ONLY** | Every number, KPI, and chart data point MUST be found in the PDF. Do not estimate or extrapolate. |
| **Fixed Financial Metrics** | Always showcase Revenue, EBITDA, PAT, and Dividend Declared (unless truly absent). |
| **Leadership Hierarchy + Consecutive** | All top management messages must be placed in the top 4 sections, ordered strictly by designation sequence. No section may separate them. |
| **Board of Directors — include only if present** | If the report has a Board chapter / spread, add it after Leadership with images, names, and a Read More CTA. If absent, do not invent. |
| **Sustainability mirrors the report's framework** | Pattern A (ESG-led) → 3-part E/S/G split. Pattern B (Capitals-led) → per-capital sections (Social, Natural, etc.). Never force a structure the report doesn't use. |
| **Shuffle KPIs and About** | Based on the report's tone, place either KPIs or Company at a Glance directly below the Hero (or Annual Theme if present). |
| **Annual Theme gets its own section** | If the report has a thematic chapter built around the year's tagline, give it a dedicated staged-narrative section between Hero and About. |
| **Customer Stories promoted when prominent** | If the report features case stories prominently, mid-page placement, not bottom. |
| **Value Creation vs Strategy** | These are different chapters. Never mix their content. |
| **Investment Case — only if present** | Do not invent an Investment Case if the report doesn't have one. |
| **Geographical Footprint — only if standalone in report** | If footprint is just a line inside About, leave it there. Promote to standalone only when the report dedicates a chapter to it. |
| **Respect the company's own emphasis** | If the cover tagline is about sustainability, the hero should reflect that — even if revenue grew more. |
| **Awards need the awarding body** | "We won an award" is meaningless. The body's name is the credibility. |
| **Value Creation Model always goes on homepage if present** | It is the one diagram that tells the whole company story. Never omit it when it exists. |
| **Every chart must specify its data in the wireframe** | Specify: chart type, X-axis, Y-axis, data series, years, hover interaction. |
| **Prioritise trend charts over single-year charts** | A 5-year revenue trend is more valuable than a single year's number in a bar. |
| **Statutory chapters never go on homepage** | Directors' Report, Audit Report, remuneration disclosures — always internal or download. |
| **Images are placeholders in the wireframe** | Describe the image direction (aerial shot, people at work, product close-up) — don't leave it blank. |

---

## Quality Check Before Handing to Designers

Before delivering `wireframe.md` to the team, verify:

**Snippet discipline**
- [ ] Does every section fit its word budget from the Step 5 table?
- [ ] Is there exactly **one** verbatim sentence per card body — not a paragraph?
- [ ] Does every section terminate in a `LINKS TO:` line pointing to a named internal page?
- [ ] Total `wireframe.md` length ≤ 4 A4 pages of markdown?

**Verbatim integrity**
- [ ] Is every sentence verbatim from the PDF? (Did the AI hallucinate or rewrite anything?)
- [ ] Are chapters strictly isolated? (No Strategy content in Value Creation?)
- [ ] Are all chart data points sourced from actual PDF numbers, not estimated?

**Design System block**
- [ ] Is there a "DESIGN SYSTEM" block at the top containing Colors (with gradients), Typography (Headings, Body, Highlights), Hero treatment, and Motion language?
- [ ] Is there a "NAVIGATION ARCHITECTURE" block listing top-level groupings and which internal pages exist?

**Structural rules**
- [ ] Are ALL leadership messages included, consecutive, and sequenced by designation?
- [ ] If the report has an Annual Theme chapter, is it placed between Hero and About?
- [ ] If the report uses Pattern B (Capitals-led), are individual capitals broken out as standalone sections rather than forced into an E/S/G split?
- [ ] If the report uses Pattern A (ESG-led), is ESG strictly split into 3 sections (E, S, G) with intro snippet + numbers each?
- [ ] Does Financial Highlights explicitly include Revenue, EBITDA, PAT, and Dividend?
- [ ] Are KPIs or About Company positioned directly below the Hero (or Annual Theme)?
- [ ] Are Customer Stories placed mid-page when the report features them prominently?
- [ ] Are Board / Investment Case / Geographical Footprint sections included **only** when they exist as chapters in the report (not invented)?
