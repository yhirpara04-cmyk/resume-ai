# Resume Optimizer — Powered by Claude Code

Drop this file into any empty folder. Say **"initialize"** once.
Claude builds the entire system. You never touch setup again.

**Workflow after setup:**
1. Fill in `resume/master-resume.md` once with your full career history
2. Paste a job description into `jobs/job-description.md`
3. Say **"optimize"** — get a tailored `.md` + `.docx` with a before/after ATS score

**Requires:** Python + `pip install python-docx`

---

## COMMAND: initialize

Trigger words: "initialize", "init", "setup", "start"

Run all steps automatically without asking permission.

### Step 1 — Create folders
```
resume/   jobs/   output/   log/   scripts/
```

### Step 2 — Pull `scripts/create_docx.py` from GitHub

**Windows (PowerShell):**
```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/yhirpara04-cmyk/resume-ai/main/scripts/create_docx.py" -OutFile "scripts/create_docx.py"
```
**Mac / Linux:**
```bash
curl -o scripts/create_docx.py "https://raw.githubusercontent.com/yhirpara04-cmyk/resume-ai/main/scripts/create_docx.py"
```
If the download fails, tell the user and stop. They may need to check internet access or run `pip install python-docx`.

### Step 3 — Pull templates from GitHub

**Windows (PowerShell):**
```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/yhirpara04-cmyk/resume-ai/main/templates/master-resume.md" -OutFile "resume/master-resume.md"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/yhirpara04-cmyk/resume-ai/main/templates/job-description.md" -OutFile "jobs/job-description.md"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/yhirpara04-cmyk/resume-ai/main/templates/applications.md" -OutFile "log/applications.md"
```
**Mac / Linux:**
```bash
curl -o resume/master-resume.md "https://raw.githubusercontent.com/yhirpara04-cmyk/resume-ai/main/templates/master-resume.md"
curl -o jobs/job-description.md "https://raw.githubusercontent.com/yhirpara04-cmyk/resume-ai/main/templates/job-description.md"
curl -o log/applications.md "https://raw.githubusercontent.com/yhirpara04-cmyk/resume-ai/main/templates/applications.md"
```
If any download fails, tell the user and stop.

### Step 6 — Confirm
Say exactly:
```
Resume Optimizer is ready.

  resume/master-resume.md    ← Fill this in once (your full career history)
  jobs/job-description.md    ← Paste a job description here before each run
  output/                    ← Tailored .md + .docx saved here automatically
  log/applications.md        ← Track every application
  scripts/create_docx.py     ← Auto-runs after every optimize (do not edit)

PRE-REQUISITE: pip install python-docx  (run once if not already installed)

NEXT STEP: Open resume/master-resume.md and replace all placeholders with your
real information. Include every job, skill, and project — don't filter yet.

When done, say: "resume ready"
```

---

## COMMAND: resume ready

Trigger words: "resume ready", "done with resume", "resume done", "filled resume"

Read `resume/master-resume.md`.

If it still contains `[Your Full Name]` or looks like an unfilled template:
```
It looks like the resume still has placeholder text. Fill in all bracketed
fields with your real information, then say "resume ready" again.
```

If it has real content:
```
Master resume loaded.

NEXT STEP: Open jobs/job-description.md, delete the placeholder line, and paste
the full job posting — title, company name, all requirements, responsibilities,
and preferred skills. The more you paste, the better the keyword match.

When done, say: "optimize"
```

---

## COMMAND: optimize

Trigger words: "optimize", "run", "go", "tailor", "job ready"

Execute all four phases in full. Do not skip any phase.

---

### Phase 0 — Baseline ATS Score (internal, never show to user)

Read both `jobs/job-description.md` and `resume/master-resume.md`.

Score the master resume using the six-category ATS model below. Save all scores and
keyword lists — you will use them in Phase 4 to compute the improvement.

**Six-category ATS model (internal):**

| Category                   | Weight |
|----------------------------|--------|
| Keywords & Skills Match    | 40%    |
| Relevant Experience        | 25%    |
| Education & Certifications | 12%    |
| Job Title Alignment        | 12%    |
| Resume Structure/Parsing   | 6%     |
| Location/Work Authorization| 5%     |

Scoring rules:
- **Keywords (40 pts):** Extract REQUIRED keywords (Qualifications/Must have/You will need)
  and PREFERRED (Nice to have/Bonus). Score:
  `((required_matched/required_total × 0.7) + (preferred_matched/preferred_total × 0.3)) × 40`
  If no preferred section, score on required only × 40.
- **Experience (25 pts):** Assess years, domain match, role-type overlap × 25.
- **Education (12 pts):** Degree/cert match × 12. If JD doesn't specify → use 90%.
- **Title (12 pts):** Exact match = 100%, one word off = 80%, level mismatch = 60%,
  different function = 30%. Multiply × 12.
- **Structure (6 pts):** Tailored output is always ATS-safe → use 90% (5.4 pts).
  Deduct if master resume has tables, columns, or inconsistent dates.
- **Location (5 pts):** Location/remote match × 5. If JD doesn't specify → use 95%.

**Rule: If a category cannot be assessed from the JD, assume it matches the resume.**

Baseline score = sum of all six categories, rounded to nearest whole number.

---

### Phase 1 — Parse the Job Description

Extract and hold in context:
- Company name and exact job title (character-for-character)
- All required skills, tools, technologies
- All preferred skills
- Key action verbs (e.g. "drive", "scale", "own", "partner")
- Industry-specific terms, acronyms, methodologies
- Soft skills (e.g. "high-ownership", "collaborative", "detail-oriented")

---

### Phase 2 — Audit the Master Resume

Read `resume/master-resume.md` in full.
For each JD keyword: classify as Present (exact) / Present (rewordable) / Missing.
Note which bullets can be reworded to incorporate JD language before making edits.

---

### Phase 3 — Tailor the Resume

Apply every rule. No exceptions.

**Rule 1 — Keyword Mirroring (Highest Impact)**
Use the JD's exact phrases — ATS does string matching, not semantic matching.
- JD says "cross-functional collaboration" → resume must say exactly that.
- Applies to every skill, tool, methodology, and key phrase in the JD.

**Rule 2 — Title Alignment**
Summary's first sentence must contain the exact job title from the JD.

**Rule 3 — Skills Reordering**
Move JD-required skills to the top of the Skills section.

**Rule 4 — Bullet Reformatting**
Formula: `[JD action verb] [what] [using/via what] [quantified result]`
- Lead with a JD verb wherever natural.
- NEVER remove a metric — only reframe it with JD language.
- Past tense throughout (including current roles). 1–2 lines per bullet max.

**Rule 5 — Acronym Coverage**
First use: write both forms — "ML (Machine Learning)".

**Rule 6 — Summary Rewrite**
Must: (1) open with exact job title, (2) include 3+ JD keywords naturally,
(3) close with one specific quantified value statement.

**Rule 7 — Section Headers**
Only these exact headers: Summary · Skills · Experience · Education · Certifications · Projects

**Rule 8 — Date Format**
"Month YYYY" throughout. No exceptions.

**Rule 9 — Aggressive Tailoring (Target: ≥ 80% AFTER score)**
Push keyword inclusion as far as the resume defensibly supports:
- If existing experience implies a skill, use the JD's exact language to describe it.
- If a concept is adjacent and a reasonable professional would agree it applies, include it.
- Reframe existing bullets liberally using JD vocabulary — same work, JD's words.
- Surface every achievement, metric, and relevant project from master-resume.md — leave nothing useful on the table.
- Do NOT fabricate metrics, credentials, job titles, or companies.
- Do NOT claim specific tools entirely absent from the master resume.
- Missing required skills that cannot be inferred → flag under GAPS only.
- If projected AFTER score is below 80%, do another pass — find more keywords to mirror, more bullets to reframe, more skills to surface before writing the output.

---

### Phase 4 — Write Output + ATS Report

**Write the tailored resume to:**
`output/[CompanyName]-[ExactJobTitle]-[YYYY-MM-DD].md`
(Hyphens, no spaces. Example: `output/Stripe-SeniorProductManager-2026-06-11.md`)

File format:
- Section order: Summary → Skills → Experience → Education → Certifications → Projects
- `##` for sections, `###` for job titles, `**bold**` for company and school names
- No tables, no columns, no emoji, no graphics
- Preserve the candidate's voice

**Auto-generate the Word document — run immediately after writing the .md:**
```
python scripts/create_docx.py output/[CompanyName]-[ExactJobTitle]-[YYYY-MM-DD].md
```
If this fails (python-docx not installed): tell the user to run `pip install python-docx` then retry.

**Compute the optimized score:**
Re-run all six ATS scoring categories against the tailored output file.

**IMPORTANT — Report display rules:**
- NEVER show the scoring model, category weights, or per-category breakdown.
- NEVER show calculations. Show only the final BEFORE and AFTER scores.

**Print this report in the chat (do not write to file):**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ATS OPTIMIZATION REPORT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Target:         [Job Title] — [Company Name]
  Output (.md):   output/[filename].md
  Output (.docx): output/[filename].docx
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  BEFORE  (original resume):   ~[XX]%
  AFTER   (tailored resume):   ~[YY]%
  IMPROVEMENT:                 +[ZZ] points
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  KEYWORDS ADDED:
    + [keyword]
    ...

  ALREADY PRESENT:
    ✓ [keyword]
    ...

  GAPS (required by JD, not in your resume):
    ⚠  [skill] — add to master resume if you have this experience
    ...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Bullets rewritten:    [N]
  Sections modified:    [list]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Open output/[filename].docx → export as PDF → submit.
  Word → Save as PDF  |  Google Docs → Download → PDF
  Canva / Adobe / Figma PDFs are NOT ATS-safe.

  To log this application → say "log it"
  To optimize for another job → paste new JD and say "optimize"
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**After printing the report:** check `log/applications.md`.
If it has no prior data rows (this is the user's first optimized resume), append this
message below the report — do not skip it:

```
🎉 Your first resume is ready!

If this tool saved you time, consider subscribing to the channel
that built it — new AI productivity tools dropped regularly:

  → [Subscribe to TokenByte on YouTube](https://www.youtube.com/@TokenByte)
```

---

## COMMAND: log it

Append a row to `log/applications.md`:
`[today's date] | [company] | [role] | output/[filename].md | Applied | `

Confirm: "Logged. Your master resume is unchanged — ready for the next application."

---

## COMMAND: review

Trigger words: "review", "check the doc", "read the output"

**For `.md` files:** Read directly with the Read tool.
**For `.pdf` files:** Read directly with the Read tool (natively supported).
**For `.docx` files:** Run:
```python
from docx import Document
doc = Document('output/[filename].docx')
for p in doc.paragraphs:
    if p.text.strip(): print(p.text)
```

After reading, report: missing sections, blank bullets, truncated content.
Required sections: Summary, Skills, Experience, Education, Certifications, Projects.

---

## ATS Reference

- ~75% of resumes are rejected before a human reads them
- ATS scores by keyword frequency and placement against the JD
- ATS cannot parse: tables, columns, text boxes, graphics, headers/footers, designed PDFs
- Safe export: Word → Save as PDF  |  Google Docs → Download → PDF
- Keyword stuffing (keywords in isolation) is detected and penalized — use in context
- Under 5 years experience = 1 page max; 5+ years = 2 pages acceptable
- Inconsistent date formats confuse parsers — "Month YYYY" everywhere

---

## Changelog
| Date       | Change |
|------------|--------|
| 2026-06-11 | Initial version |
| 2026-06-13 | Auto .docx generation; 6-category ATS scoring; aggressive tailoring; scores-only report |
| 2026-06-13 | initialize now pulls all templates from GitHub; 80%+ AFTER score target added |
