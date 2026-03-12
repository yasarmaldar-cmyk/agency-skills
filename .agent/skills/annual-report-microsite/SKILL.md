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

**Chairman / MD / Chairperson letter:**
- Opening sentence → Leadership card teaser quote
- What theme does the letter emphasise? (Growth? Resilience? Sustainability? Innovation?) → Tone signal for the entire microsite
- *Why it matters:* The Chairperson sets the emotional frame of the report.

**CEO / MD / Managing Director message or Q&A:**
- Opening operational statement → CEO card quote
- What 2–3 achievements does the CEO choose to highlight first? → These are homepage-worthy KPIs
- *Why it matters:* The CEO's priorities reveal what the company most wants stakeholders to remember.

**CFO / Finance Director statement:**
- The headline financial achievement they lead with → CFO card teaser
- *Why it matters:* Signals which financial metric is most significant this year.

**Investment Case / Why Invest section (if present):**
- Extract all bullet points or reason tiles verbatim — this content is already written for the homepage
- *Why it matters:* If this section exists, the company wrote it specifically for investor audiences. Use it almost as-is.

**Strategic Priorities / Pillars:**
- Extract each priority name and its one-line description
- Count: if there are more than 7, only show the first 5–6 on the homepage; link to full strategy page
- *Why it matters:* Forward-looking content balances the backward-looking KPIs.

**ESG / Sustainability chapter opener (first 2 pages only):**
- What sustainability framework does the company use? (Net Zero, Zero Harm, GRI, TCFD, etc.)
- What are their 3–4 ESG pillars? (Environment / Social / Governance are common, but vary)
- What is the single most impressive ESG achievement this year? → Featured stat
- *Why it matters:* ESG is now a primary lens for investors, regulators, and media.

**Value Creation Model / Business Model chapter (if present):**
- Look for chapter titles like: "Value Creation Model", "Our Business Model", "Six Capitals", "How We Create Value", "Input-Output Model"
- Extract the model's core flow — what goes IN (inputs/capitals) and what comes OUT (outputs/outcomes for stakeholders)
- Identify how many "capitals" or "pillars" the model uses — this determines the layout (typically 4–6 columns or a circular/flow diagram)
- Note the stakeholder groups the model addresses (shareholders, employees, communities, environment, etc.)
- *Why it matters:* The Value Creation Model is the single diagram that explains the whole company — it shows how resources turn into value. It replaces a generic "about us" paragraph with a structured, visual story. Clients love it on the homepage because it differentiates their microsite from a simple PDF summary.
- *In the wireframe:* Describe it as an interactive flow diagram — left side = inputs, centre = business activities, right side = outputs. Each node is clickable to its detail page.

**Awards / Recognition chapter:**
- List every award and its awarding body
- Filter: keep only **international, national, or industry-body** awards on homepage; regional/community awards go on internal pages
- *Why it matters:* Third-party validation builds trust with first-time readers.

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

### Universal homepage section order:

```
1.  NAVIGATION          → Logo | Links | CTA
2.  HERO                → Tagline + KPI strip (animated counters)
3.  COMPANY AT A GLANCE → Identity + scale facts + operations map
4.  VALUE CREATION MODEL→ Interactive flow diagram (if present in PDF)
5.  LEADERSHIP VOICES   → Chairman + CEO + CFO cards (teasers)
6.  INVESTMENT CASE     → Reason tiles (if section exists in report)
7.  STRATEGY            → Strategic priority tiles
8.  KPI DASHBOARD       → Financial + Operational + ESG columns + INTERACTIVE CHARTS
9.  PRODUCTS/SEGMENTS   → Cards per product/segment (if multi-product company)
10. ESG / SUSTAINABILITY → Pillar cards with headline stat each
11. AWARDS              → Award grid
12. HUMAN STORIES       → Case study / feature cards (if present)
13. FOOTER              → Navigation + download
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
> 4. Read the narrative from every homepage-eligible chapter (first 2–3 pages each): cover, highlights, leadership messages, investment case (if present), strategic priorities, ESG opener, awards
> 5. Document your section reasoning — why each section was chosen or rejected
> 6. Generate `wireframe.md` — the full document wireframe with real extracted content and design notes"

---

## Universal Editorial Rules (Apply to Any Client)

| Rule | Detail |
|---|---|
| **Don't lead with financials** | Unless the company is a financial services firm, finance comes after identity and leadership |
| **Always include leadership voice** | Even if there's only one leader message |
| **Respect the company's own emphasis** | If the cover tagline is about sustainability, the hero should reflect that — even if revenue grew more |
| **Numbers need context** | Never show a raw number without a label and YoY change (or a superlative: "record", "first time", "#1") |
| **Awards need the awarding body** | "We won an award" is meaningless. The body's name is the credibility |
| **ESG always gets its own section** | Even for companies that don't lead with ESG — investors expect it |
| **Value Creation Model always goes on homepage if present** | It is the one diagram that tells the whole company story. If the PDF has it, it goes on the homepage — never omit it |
| **Every chart must specify its data in the wireframe** | Don't just say "add a chart here" — specify: chart type, X-axis, Y-axis, data series, years, and hover interaction |
| **Charts should use data from the PDF, not estimated** | Extract actual numbers from the KPI/financial sections to populate chart specs |
| **Prioritise trend charts over single-year charts** | A 5-year revenue trend is more valuable on a homepage than a single year's number in a bar |
| **Statutory chapters never go on homepage** | Directors' Report, Audit Report, remuneration disclosures — always internal or download |
| **Images are placeholders in the wireframe** | Describe the image direction (aerial shot, people at work, product close-up) — don't leave it blank |

---

## Quality Check Before Handing to Designers

Before delivering `wireframe.md` to the team, verify:

- [ ] Does the hero use the company's actual year tagline (not a generic headline)?
- [ ] Are all KPI numbers real and sourced from the PDF (not estimated)?
- [ ] Is there at least one leadership quote that is verbatim from the PDF?
- [ ] Does the reasoning log explain every excluded section?
- [ ] Are any awards included that are regional/community-level? (Remove them)
- [ ] Does the wireframe adapt for this company's specific type? (Single product? Financial company? Conglomerate?)
- [ ] Is the section order logical for this company's primary audience? (Investors first? Community first?)
- [ ] If a Value Creation Model exists in the PDF, is it included in the wireframe with its flow structure described?
- [ ] Does every chart in the wireframe specify: chart type, axes, data series, years, and hover interaction?
- [ ] Are chart data points sourced from actual PDF numbers, not estimated?
