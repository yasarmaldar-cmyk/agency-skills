# Agency Skills — Antigravity

Skills for the team to use with Antigravity AI. Each skill automates a repeatable agency task.

---

## Skills Available

| Skill | What it does |
|---|---|
| `annual-report-microsite` | Reads any Annual Report PDF, identifies homepage sections, and generates a **snippet-length** document wireframe with editorial reasoning. Treats the homepage as an index, not a destination — every section is a teaser that links to a dedicated internal page. |

---

## Setup — Pick One Mode

You can use this skill in two ways. Mode A is recommended for ongoing client work; Mode B is faster for one-off jobs.

### Mode A — Install Locally (Recommended for Antigravity)

This registers the skill inside your project so it appears in Antigravity's skills panel and auto-attaches whenever you open that workspace.

**1. Clone the repo**
```bash
git clone https://github.com/yasarmaldar-cmyk/agency-skills.git
```

**2. Install the skill into your project**

For each client project, copy the `.agent` folder into that project's folder:
```bash
cp -r agency-skills/.agent "/path/to/your/client-project/"
```

That's it. The skill is now available whenever you open that project in Antigravity. Use the **Mode A prompt** below.

### Mode B — Fetch From Git (No Install)

Skip the clone and the copy entirely. Just open any folder containing the client PDF in Antigravity (or Claude Code) and use the **Mode B prompt** below — it tells the agent to fetch the latest skill from this repo and follow it inline.

- ✅ No download, no per-project copies
- ✅ Always uses the latest version of the skill
- ⚠️ Skill won't show up as a registered skill in Antigravity's UI — it's just instructions the agent reads during that run
- ⚠️ Requires the agent to be able to fetch from GitHub (works in Claude Code, Antigravity, and most agents)

---

## How to Use: Annual Report → Microsite Wireframe

### What you need
- The client's Annual Report PDF (any company, any year)
- Antigravity open with the client project folder as your workspace

### Mode A Prompt — Local Install

Copy and paste this into Antigravity, replacing `[PATH]` with the actual PDF path:

```
Using the annual-report-microsite skill, read the Annual Report PDF at [PATH].

1. Extract all text from the PDF page by page
2. Find the Table of Contents — map every chapter to its page range
3. Detect the reporting framework — ESG-led (Pattern A) or Capitals-led / Integrated
   Reporting (Pattern B). Mirror the report's own vocabulary on the homepage.
4. Classify every chapter using the universal homepage categories
5. Read the narrative content from every homepage-eligible chapter and extract a
   SINGLE sharpest verbatim sentence per section (do NOT pull paragraphs):
   cover tagline, annual theme (if present), highlights, leadership messages,
   value creation model (if present), customer stories (if present), quarterly
   highlights (if present), investment case (if present), strategy, KPIs,
   sustainability (per the detected framework), and awards.
6. While reading, dynamically extract the global Design System: Colors + gradients,
   Typography (headings/body/highlights), Hero treatment, and Motion language.
7. Apply all Universal Editorial Rules strictly:
   - Verbatim only — never paraphrase, summarise, or invent
   - Homepage = index, not destination — one sentence per surface
   - Enforce word budgets (Hero ≤12, leadership quote ≤50, card body ≤30, etc.)
   - Every section must terminate in a LINKS TO: line pointing to an internal page
   - Do not cross-pollinate chapter content (Value Creation ≠ Strategy ≠ Capitals)
   - Sustainability follows the report's framework — E/S/G split (Pattern A)
     OR per-capital sections (Pattern B). Never force a structure the report
     doesn't use.
   - Sequence all top leadership consecutively by designation
   - Include Board / Investment Case / Geographical Footprint sections ONLY if
     the report has them as chapters — do not invent them
   - Always include Revenue, EBITDA, PAT, and Dividend Declared
8. Note all charts/graphs — flag the top 4–6 for interactive web charts using
   real data points; embed them inside Performance/KPI/Capital sections.
9. Document your section reasoning — why each was chosen or rejected, and which
   internal page receives the rest of the content.
10. Apply Step 6 of the skill — The Cut — re-read every section and enforce the
    word budgets. Target ≤4 A4 pages of markdown total.
11. Generate wireframe.md — starting with the Design System + Navigation
    Architecture blocks, followed by the snippet-length wireframe with chart
    specs, design notes, and LINKS TO: exits on every section.
```

### Mode B Prompt — Fetch From Git (No Install)

Copy and paste this into Claude Code, Antigravity, or any agent. Replace `[PATH]` with the actual PDF path. The agent fetches the latest skill from this repo at run time, then applies it to your PDF.

```
First, fetch the latest skill instructions from:
https://raw.githubusercontent.com/yasarmaldar-cmyk/agency-skills/main/.agent/skills/annual-report-microsite/SKILL.md

Read that file in full. It is the authoritative spec — follow every step, every
universal editorial rule, every word budget, and every quality check inside it.

Then apply that skill to the Annual Report PDF at [PATH].

Produce the two output files exactly as the skill specifies:
- wireframe.md  — snippet-length homepage wireframe (~3–4 A4 pages), starting
  with the Design System + Navigation Architecture blocks, with LINKS TO:
  exits on every section
- section_reasoning.md  — full audit log of every section chosen or rejected,
  noting which internal page receives the rest of the content

Before you export, run Step 6 of the skill (The Cut) — re-read every section
and enforce the word budgets. The homepage is an index, not a destination.
```

> **If the agent can't fetch URLs in your environment**, fall back to Mode A — clone the repo locally and use the Mode A prompt instead.

### What you get

| Output file | What it contains |
|---|---|
| `wireframe.md` | Snippet-length homepage wireframe (~3–4 A4 pages) — Design System + Navigation Architecture + per-section layout, single-sentence content, chart specs, design notes, and `LINKS TO:` exits |
| `section_reasoning.md` | Audit log — every section chosen or rejected with editorial reasoning, and which internal page receives any content cut from the homepage |

### Example
```
Using the annual-report-microsite skill, read the Annual Report PDF at 
/Users/yourname/Desktop/ClientA/AnnualReport_FY2025.pdf
[...rest of prompt above]
```

---

## Getting Skill Updates

When the skill is updated, pull the latest version:
```bash
cd agency-skills
git pull
```

Then copy the updated `.agent` folder into your active client projects again.

---

## Adding a New Skill

1. Create a folder: `.agent/skills/your-skill-name/`
2. Add a `SKILL.md` file inside it
3. Commit and push:
```bash
git add .agent/skills/your-skill-name/SKILL.md
git commit -m "Add your-skill-name skill"
git push
```
