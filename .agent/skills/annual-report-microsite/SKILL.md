---
name: annual-report-microsite
description: Reads any Annual Report PDF, discovers its structure dynamically, applies universal editorial reasoning to identify homepage sections, and generates a document wireframe with reasoning for team review.
---

# Annual Report → Microsite Skill

## Purpose

This skill replaces the manual process of a team reading through a client's Annual Report PDF and deciding what goes on the homepage microsite. It works for **any company, any industry, any report structure**.

It does not assume page numbers. It does not assume chapter names. It learns the structure from the PDF itself first, then applies universal editorial rules.

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
- The year's **theme/tagline** — usually 4–12 words, often on the cover visual or inside cover spread. This is NOT the company name. Example: *"Accelerating Momentum, Embracing Opportunities"* — this is the year's brand story.
- The **company's one-line identity** — what they do and their scale claim
- Any **"Year in Numbers"** spread — a page with 5–10 large numbers and labels

### 2c. Map Every Chapter to Its Category

For every chapter/section found in the TOC, classify it into one of these universal categories:

| Category | What to look for | Homepage? |
|---|---|---|
| **Company identity** | About us, at a glance, who we are, corporate overview | ✅ Always |
| **Year highlights** | Year in review, performance highlights, FY summary | ✅ Always |
| **Leadership voice** | Chairman/MD/CEO/CFO letters, messages, Q&A | ✅ Always |
| **Value Creation Model** | Value creation model, business model, capitals model, input-output model, six capitals | ✅ If present |
| **Investment story** | Investment case, why invest, value proposition | ✅ Always |
| **Strategy** | Strategic priorities, pillars, focus areas, roadmap | ✅ Always |
| **Financial KPIs** | Key performance indicators, financial highlights | ✅ Always |
| **Operational KPIs** | Production, capacity, volumes, efficiency | ✅ Always |
| **Products/Segments** | Product lines, business segments, divisions | ✅ If multi-product |
| **ESG/Sustainability** | Environment, social, governance, CSR | ✅ Always |
| **Awards/Recognition** | Rankings, certifications, accolades | ✅ Always |
| **Human stories** | Case studies, employee stories, community impact | ✅ If present |
| **Operational detail** | Plant-wise, unit-wise, facility-by-facility data | ❌ Internal page |
| **Risk management** | Risk registers, mitigation strategies | ❌ Internal page |
| **Statutory/legal** | Directors' report, board committees, compliance | ❌ Internal page |
| **Financial statements** | Balance sheet, P&L, cash flow, notes | ❌ Download only |

> **Key rule:** A section is homepage-worthy if it answers one of these three questions for a first-time reader:
> 1. *Who is this company and how big are they?*
> 2. *How did they perform this year?*
> 3. *What are they building toward?*

---

## Step 3 — Read the Narrative (The Step That Cannot Be Skipped)

The TOC tells you the skeleton. The narrative reading tells you *what the company wants to say* — which is what your team actually does when they read through the report.

For each homepage-eligible chapter, read the **first 2–3 pages** of that chapter. Specifically extract:

### What to look for and why:

**Cover / Year Highlights chapter:**
- The year tagline → Hero headline
- The 5–8 numbers the company chose to feature prominently → KPI strip
- *Why it matters:* These are the company's own editorial choices. If they chose to lead with sustainability rank over revenue, that tells you their brand priority.

**Leadership Voices / Messages:**
- Extract EVERY top management message present in the report (Chairman, MD, CEO, CFO, etc).
- Opening operational/strategic statement → Teaser card quote (verbatim)
- **Sequence rule:** Order them strictly by designation hierarchy (e.g., Chairman → MD → CEO → CFO).
- *Why it matters:* Leadership sets the tone. Every leader featured in the report must be featured on the homepage in their proper rank.

**Investment Case / Why Invest section (if present):**
- Extract all bullet points or reason tiles verbatim — this content is already written for the homepage
- *Why it matters:* If this section exists, the company wrote it specifically for investor audiences. Use it almost as-is.

**Strategic Priorities / Pillars:**
- Extract each priority name and its one-line description
- Count: if there are more than 7, only show the first 5–6 on the homepage; link to full strategy page
- *Why it matters:* Forward-looking content balances the backward-looking KPIs.

**ESG / Sustainability chapter (first 2-3 pages only):**
- Split this into 3 distinct sections: **1. Environment, 2. Social, 3. Governance**.
- For each of the 3 sections, extract an **introduction paragraph** verbatim.
- If present, extract any **numbers & key highlights** mentioned in the chapter to accompany that section's intro.
- *Why it matters:* ESG is now a primary lens for investors, regulators, and media. Splitting into E, S, and G provides structured clarity.

**Value Creation Model / Business Model chapter (if present):**
- Extract the model's core flow — what goes IN (inputs/capitals) and what comes OUT (outputs/outcomes for stakeholders).
- **Isolation Rule:** Only extract Value Creation content from the specific Value Creation chapter. Do not pull strategy content into this section.
- *In the wireframe:* Describe it as an interactive flow diagram with a text snippet for each capital/pillar.

**Strategy / Strategic Priorities chapter:**
- Extract each priority name and its one-line verbatim description.
- **Isolation Rule:** Only extract Strategy content from the Strategy chapter. Do not mix it with Value Creation or Operations.

**Financial Highlights:**
- **Always showcase:** Revenue, EBITDA, PAT, and Dividend Declared.
- If (and only if) these specific metrics are unavailable, show the primary financial numbers provided.

**Awards / Recognition chapter:**
- List every award and its awarding body
- Filter: keep only **international, national, or industry-body** awards on homepage; regional/community awards go on internal pages
- *Why it matters:* Third-party validation builds trust with first-time readers.

**Design System Extraction (Global throughout report):**
- **Colors:** Identify the primary brand color, secondary accents, and background colors used prominently. Pay special attention to **gradients** (e.g., "Navy to Teal linear gradient used on section backgrounds").
- **Typography:** Identify the font stack used. Extract specific styling rules for:
  - **Headings (H1/H2):** Font family, weight (e.g., Bold/Black), capitalization (e.g., All-Caps), and primary color used.
  - **Body Copy:** Font family, weight (e.g., Regular/Light), and color.
  - **Highlights/Callouts:** Font styling used for pull quotes or large KPI numbers (e.g., "Italics Serif for quotes", "Oversized bold sans-serif for numbers").
- *Why it matters:* The wireframe needs to give the UI designer the exact visual rules established by the print designers building the PDF.

**Graphs and charts throughout the report:**
- While reading each chapter, note every chart, graph, or data visualisation that appears
- For each one, record: chart type (bar, line, pie, donut, area), what it measures, how many data points, and the time range (e.g., 5-year trend)
- Flag the top 4–6 charts that are most impactful for a homepage audience — prioritise: revenue trend, production trend, cost trend, ESG metric trend
- *Why it matters:* Clients want interactive charts in their digital report — static PDF graphs become animated, interactive web charts. The wireframe must specify which data series to chart and in what format.
- *In the wireframe:* For each chart, specify — Chart type | X-axis | Y-axis | Data series | Years covered | Interaction (hover tooltip, filter by year)

---

## Step 4 — Write the Reasoning

Before writing the wireframe, document your reasoning. This is your team's audit trail.

For every section chosen, write:

```
SECTION: [Name]
SOURCE: PDF pages [X–Y], chapter "[exact chapter title from TOC]"
CHOSEN BECAUSE: [1–2 sentence editorial reason using Who/How/What framework]
CONTENT USED: [Specific numbers, quotes, bullet points extracted]
EXCLUDED: [What from this chapter was left out and why]
```

For every section rejected, write:

```
REJECTED: [Chapter name]
SOURCE: PDF pages [X–Y]
REASON: [Why it stays off the homepage]
GOES TO: [Which internal page it belongs on]
```

---

## Step 5 — Generate the Document Wireframe

Produce a **structured markdown document** — a specification for designers to work from. Not HTML, not a prototype, not a mockup. A document your team can read, mark up, and approve.

### Statutory Content Rule (CRITICAL)
**Annual Reports are statutory/legal documents. You MUST extract text verbatim.** 
- Do NOT rewrite, summarise, paraphrase, or invent text.
- If a section needs a short blurb, copy the exact opening sentence from the PDF.
- If an exact snippet isn't available, state `[Requires client to provide [X] word snippet here]` instead of making one up.

### Content Snippet Requirement
Every single card, tile, or pillar on the homepage (Leadership, Capitals, Products, ESG, Reasons to Invest) **must** include a supporting text snippet extracted directly from the PDF. Do not just output headlines.

### Universal homepage section order (with flexibility):

The top sections have a specific flow rule: KPIs and About Company can shuffle positions depending on what the company emphasizes. Leadership must always be in the top 4.

```
0.  DESIGN SYSTEM       → Brand Colors (incl. gradients) + Typography (Headings, Body, Highlights)
    -----------------------------------------------------
1.  NAVIGATION          → Logo | Links | CTA
2.  HERO                → Tagline (Verbatim from PDF cover)
    -- Shuffle Section 3 & 4 based on report emphasis: --
3.  COMPANY AT A GLANCE → Who we are + scale facts (Verbatim)
4.  KPI DASHBOARD / HIGHLIGHTS → Financial + Operational figures (Must use real PDF numbers only)
    -----------------------------------------------------
5.  LEADERSHIP VOICES   → ALL top management messages ordered by designation hierarchy (Verbatim snippets)
6.  BOARD OF DIRECTORS  → Images, names, and "Read More" CTA only (no bios)
7.  GEOGRAPHICAL FOOTPRINT → Operations map / presence (Split from At a Glance)
8.  VALUE CREATION MODEL→ Interactive flow diagram (Sourced ONLY from Value Creation chapter)
9.  INVESTMENT CASE     → Reason tiles + verbatim snippets (if section exists in report)
10. STRATEGY            → Strategic priority tiles + verbatim snippets (Sourced ONLY from Strategy chapter)
11. INTERACTIVE CHARTS  → 4-6 trend charts (Must use real data points from PDF, never dummy data)
12. PRODUCTS/SEGMENTS   → Cards per product/segment + verbatim snippets
13. ESG / SUSTAINABILITY → STRICT 3-part split: Environment, Social, Governance. Intro para + numbers & key highlights for each.
14. AWARDS              → Award grid (Real names and awarding bodies)
15. HUMAN STORIES       → Case study / feature cards + verbatim snippets (if present)
16. FOOTER              → Navigation + download
```

> **Adapting for different companies:**
> - If the company is **single-product** (e.g., a bank), skip Section 8 or replace with business verticals/geographies
> - If the company has **no Investment Case chapter**, skip Section 5 and expand Strategy section
> - If the report is **small** (under 100 pages), some sections may not exist — skip gracefully
> - If the company leads with **people/CSR** (common for consumer or healthcare firms), promote Stories to Section 4

### Format for each section in the wireframe:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SECTION [N]: [Section Name]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LAYOUT:       [Full width / 2-col / 3-col grid / 4-tile / etc.]
SOURCE:       PDF Pages [X–Y] — "[exact chapter name]"
PRIORITY:     [Hero / High / Medium]

WHY THIS SECTION:
[1–2 sentence editorial reasoning]

CONTENT:
[Actual extracted text — verbatim quotes, KPI numbers, section titles]

DESIGN NOTES:
[Colour treatment, imagery direction, interaction hints]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Output Files

| File | What it is |
|---|---|
| `wireframe.md` | Document wireframe — layout + real content per section |
| `section_reasoning.md` | Full reasoning log — every section chosen or rejected with justification |

---

## Prompt Template (Copy and Use)

> **"Read the annual report PDF at [PATH]. Using the Annual Report Microsite Skill:**
> 1. Extract all text from the PDF page by page
> 2. Find the Table of Contents — map every chapter to its page range
> 3. Classify every chapter using the universal homepage categories
> 4. Read the narrative content from every homepage-eligible chapter: cover, highlights, leadership messages, investment case (if present), strategic priorities, ESG opener, awards
> 5. While reading, dynamically extract the global **Design System** (Brand colors, gradients, and font styling for headings/body/highlights).
> 6. Document your section reasoning — why each section was chosen or rejected
> 7. Generate `wireframe.md` — starting with the Design System block, followed by the full document wireframe with real extracted content and design notes"

---

## Universal Editorial Rules (Apply to Any Client)

| Rule | Detail |
|---|---|
| **STATUTORY RULE: Verbatim ONLY** | Never rewrite, paraphrase, or invent text. Use headings for headings and copy for copy from the PDF as is. |
| **Strict Section Isolation** | Use content ONLY from the start to the end of a section's specific chapter. Do not pull content from other chapters. |
| **Snippets required everywhere** | A headline is never enough. Every card/tile needs 1-2 lines of supporting text. |
| **Real data ONLY** | Every number, KPI, and chart data point MUST be found in the PDF. Do not estimate or extrapolate. |
| **Fixed Financial Metrics** | Always showcase Revenue, EBITDA, PAT, and Dividend Declared (unless truly absent). |
| **Leadership Hierarchy Top 4 Position** | All top management messages must be placed in the top 4 sections, ordered strictly by designation sequence. |
| **Board of Directors** | Always follows Leadership. Only includes images, names, and a Read More CTA. |
| **3-Part ESG Split** | ESG must be 3 distinct sections (E, S, G). Each needs an intro para + numbers & key highlights. |
| **Shuffle KPIs and About** | Based on the report's tone, place either KPIs or Company at a Glance directly below the Hero. |
| **Geographical Footprint is standalone** | Always separate the geographic map/presence from the 'At a Glance' text section. |
| **Value Creation vs Strategy** | These are different chapters. Never mix their content. |
| **Respect the company's own emphasis** | If the cover tagline is about sustainability, the hero should reflect that — even if revenue grew more |
| **Awards need the awarding body** | "We won an award" is meaningless. The body's name is the credibility |
| **Value Creation Model always goes on homepage if present** | It is the one diagram that tells the whole company story. If the PDF has it, it goes on the homepage — never omit it |
| **Every chart must specify its data in the wireframe** | Don't just say "add a chart here" — specify: chart type, X-axis, Y-axis, data series, years, and hover interaction |
| **Prioritise trend charts over single-year charts** | A 5-year revenue trend is more valuable on a homepage than a single year's number in a bar |
| **Statutory chapters never go on homepage** | Directors' Report, Audit Report, remuneration disclosures — always internal or download |
| **Images are placeholders in the wireframe** | Describe the image direction (aerial shot, people at work, product close-up) — don't leave it blank |

---

## Quality Check Before Handing to Designers

Before delivering `wireframe.md` to the team, verify:

- [ ] Is every sentence verbatim from the PDF? (Did the AI hallucinate or rewrite anything?)
- [ ] Is there a "DESIGN SYSTEM" block at the top containing Colors (with gradients) and Typography (Headings, Body, Highlights)?
- [ ] Are chapters strictly isolated? (No Strategy content in Value Creation?)
- [ ] Are ALL leadership messages included and sequenced by designation?
- [ ] Is the Board of Directors section present below Leadership?
- [ ] Does Financial Highlights explicitly include Revenue, EBITDA, PAT, and Dividend?
- [ ] Is ESG strictly split into 3 sections (E, S, G) with intro paras and numbers/highlights?
- [ ] Are KPIs or About Company positioned directly below the Hero?
- [ ] Does every single card (Capitals, Strategy, Products) have a text snippet?
- [ ] Are all chart data points sourced from actual PDF numbers, not estimated?
