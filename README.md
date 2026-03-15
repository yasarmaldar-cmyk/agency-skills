# Agency Skills — Antigravity

Skills for the team to use with Antigravity AI. Each skill automates a repeatable agency task.

---

## Skills Available

| Skill | What it does |
|---|---|
| `annual-report-microsite` | Reads any Annual Report PDF, identifies homepage sections, and generates a document wireframe with editorial reasoning |

---

## One-Time Setup (Do This Once)

**1. Clone the repo**
```bash
git clone https://github.com/yasarmaldar-cmyk/agency-skills.git
```

**2. Install the skill into your project**

For each client project, copy the `.agent` folder into that project's folder:
```bash
cp -r agency-skills/.agent "/path/to/your/client-project/"
```

That's it. The skill is now available whenever you open that project in Antigravity.

---

## How to Use: Annual Report → Microsite Wireframe

### What you need
- The client's Annual Report PDF (any company, any year)
- Antigravity open with the client project folder as your workspace

### The prompt to use

Copy and paste this into Antigravity, replacing `[PATH]` with the actual PDF path:

```
Using the annual-report-microsite skill, read the Annual Report PDF at [PATH].

1. Extract all text from the PDF page by page
2. Find the Table of Contents — map every chapter to its page range
3. Classify every chapter using the universal homepage categories
4. Read the narrative content from every homepage-eligible chapter:
   cover, highlights, leadership messages, value creation model (if present),
   investment case (if present), strategy, KPI section, ESG chapter opener, and awards.
5. While reading, dynamically extract the global Design System (Colors, Gradients, Typography).
6. Apply all Universal Editorial Rules strictly:
   - Extract content verbatim (No summarising)
   - Do not cross-pollinate chapter content (e.g. Keep Value Creation and Strategy separate)
   - Split ESG into 3 distinct sections (E, S, G)
   - Sequence all top leadership by designation
   - Always include Revenue, EBITDA, PAT, and Dividend Declared
7. Note all charts/graphs — flag the top 4–6 for interactive web charts using real data points.
8. Document your section reasoning — why each was chosen or rejected.
9. Generate wireframe.md — starting with the Design System, followed by the full document wireframe with real content, chart specs, and design notes.
```

### What you get

| Output file | What it contains |
|---|---|
| `wireframe.md` | Full homepage document wireframe — layout, real content, chart specs, design notes |
| `section_reasoning.md` | Audit log — every section chosen or rejected with editorial reasoning |

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
