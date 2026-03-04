# Should I Apply? — Candidate Evaluation Report

Evaluate whether you should apply to a company/role. Produces a comprehensive markdown report with a scorecard, sentiment-driven SWOT analysis, and deep interview prep including culture/leadership analysis.

## Inputs

**Usage:** `/should-i-apply "resume-path" "company" "role" "jd-url" "evidence-dir"`

Parse `$ARGUMENTS` as up to five quoted strings:
- **First quoted string** (required) — Path to resume file (PDF or markdown)
- **Second quoted string** (required) — Target company name (e.g., `"Anthropic"`)
- **Third quoted string** (required) — Target role title (e.g., `"Security Engineer"`)
- **Fourth quoted string** (optional) — URL to the job description posting
- **Fifth quoted string** (optional) — Path to directory containing supporting evidence

If any required argument is missing, ask the user for it. Do NOT proceed without the first three.

**Evidence directory:** If provided, scan and read all files in it (PDFs, markdown, text, etc.) for additional context — cover letters, portfolio pieces, publications, project docs, LinkedIn exports, personal statements, etc. Use this material to enrich the candidate profile and strengthen evidence mapping.

## Setup

This command expects a `claude-job-hunter` repo cloned locally. Find it by checking (in order):
1. `$CLAUDE_JOB_HUNTER_DIR` environment variable
2. `./claude-job-hunter/` relative to cwd
3. `~/claude-job-hunter/`

If not found, ask the user for the path.

## Source Materials

Read these files before starting:

- **Candidate profile:** `[claude-job-hunter]/profile/candidate-profile.md`
- **Company research checklist:** `[claude-job-hunter]/resources/research-checklist.md`
- **Interview research checklist:** `[claude-job-hunter]/resources/interview-research-checklist.md`

For examples of completed research:
- `[claude-job-hunter]/examples/example-company-research.md`
- `[claude-job-hunter]/examples/example-fit-analysis.md`

## Output Directory

```
[claude-job-hunter]/evaluations/[company]-[role-slug]/
├── SCORECARD.md          # Verdict, SWOT, requirements mapping, company card
├── INTERVIEW_PREP.md     # Panel research, Q&A, conversation points, red flags
├── COMPANY_INTEL.md      # Raw research findings with sources
└── README.md             # Summary and how to use these docs
```

Create the output directory before writing any files. Use lowercase, hyphenated slug for the role (e.g., `security-engineer`).

## Workflow

Execute these phases in order. Present findings to the user after each phase for confirmation before proceeding.

---

### Phase 1: Candidate Profile Ingestion

1. Read the resume file provided as the first argument (PDF or markdown)
2. Read the candidate profile at `[claude-job-hunter]/profile/candidate-profile.md`
3. If an evidence directory was provided (5th arg):
   - List all files in the directory
   - Read each file (PDFs, markdown, text, etc.)
   - Extract additional context: cover letters, portfolio work, publications, project descriptions, LinkedIn exports, personal statements, recommendation letters, certifications
4. Ask the user for any supplementary info not captured:
   - Current priorities and what they're optimizing for (growth, comp, impact, stability, etc.)
   - Deal-breakers and non-negotiables
   - Sentiment about career direction — what excites them, what drains them
   - Any specific concerns about this company/role

Extract and synthesize:
- Technical skills and experience levels
- Seniority and career trajectory
- Values signals and cultural preferences
- Unique differentiators from supporting evidence
- **Candidate sentiment** — what they care about, what motivates them, what they'd walk away from

**Output:** Present synthesized candidate profile summary. Confirm with user before proceeding.

---

### Phase 2: JD Analysis

If a JD URL was provided (4th arg), fetch it using WebFetch. Otherwise, ask the user to paste the JD text.

Extract:
- Required skills with experience levels
- Nice-to-have qualifications
- Role level and reporting structure
- Team size and composition hints
- Company-specific priorities and keywords
- Remote/hybrid/onsite requirements
- Compensation signals if available
- Red flags in language (unrealistic expectations, scope creep, vague ownership)

**Output:** Present extracted requirements as a structured checklist. Ask user to confirm or adjust before proceeding.

---

### Phase 3: Company & Leadership Deep Research

Use WebSearch and WebFetch. Follow both research checklists:
- `[claude-job-hunter]/resources/research-checklist.md` — company basics
- `[claude-job-hunter]/resources/interview-research-checklist.md` — leadership, culture, interviewer profiling

#### 3a. Company Basics
- Official website, mission, product, business model
- Funding stage, investors, employee count
- Recent news (last 6 months)
- Competitor landscape and market position
- Technical infrastructure and engineering culture

#### 3b. Leadership Team Research
- CEO, CTO, VP/Director over this role, hiring manager
- LinkedIn profiles, talks, blog posts, interviews
- Leadership style signals, stated values, management philosophy
- Tenure patterns — how long do leaders stay?

#### 3c. Culture & Values Analysis
- Official company values / leadership principles
- Glassdoor/Blind reviews — look for patterns, not outliers
- Engineering culture: blog posts, open source, conference talks
- Red flag signals: high turnover in role/team, negative review patterns
- Work-life balance signals, remote culture, async vs. sync

#### 3d. Hiring Team Identification
- Who would likely interview for this role (hiring manager, team leads, cross-functional)
- Their backgrounds, interests, likely focus areas

Save all findings with source URLs to `COMPANY_INTEL.md` in the output directory.

**Output:** Present research summary. Ask user to confirm or add context before proceeding.

---

### Phase 4: Scorecard & Fit Analysis

Generate `SCORECARD.md` with these sections:

#### A. Should You Apply? (Verdict)

- **Overall recommendation:** Strong Yes / Yes / Maybe / Probably Not / No
- **Match score** (percentage) with justification
- Top 3 reasons to apply
- Top 3 reasons to hesitate

#### B. Personalized SWOT Analysis (Sentiment-Driven)

This is NOT a generic SWOT. Every item must be grounded in the candidate's stated sentiment, values, and priorities crossed with what research reveals about this specific company/role.

**Strengths — "Why YOU specifically for THIS role"**
- What makes this candidate uniquely strong for this position
- Evidence from resume + supporting materials mapped to JD requirements
- Differentiators that other candidates likely lack

**Weaknesses — "Honest gaps and how to address them"**
- Skills or experience gaps relative to JD requirements
- For each gap: severity assessment and mitigation strategy (what to say in the interview)
- Gaps from supporting evidence that might surface in reference checks

**Opportunities — "What success looks like"**
- Growth potential for the candidate in this role
- Career trajectory implications (does this advance their goals?)
- What the candidate would gain: skills, network, resume value, impact
- **Success criteria based on candidate sentiment:** Define what "this worked out" looks like in 6, 12, and 24 months based on what the candidate said they care about. For example, if they value autonomy, success = "has ownership of security roadmap by month 6." If they value growth, success = "managing 2+ reports by month 18."

**Threats — "Failure risks based on your priorities"**
- Concrete failure scenarios grounded in the candidate's sentiment and values:
  - If candidate values autonomy → risk: "Company has 3 layers of approval for security tooling purchases (Glassdoor reviews mention bureaucracy)"
  - If candidate values impact → risk: "Security is a checkbox function here, not strategic (no CISO, reports to IT Director)"
  - If candidate values growth → risk: "Flat org with no management track, could plateau in 18 months"
- Culture misalignment risks — specific tensions between candidate values and company culture signals
- Role risks — scope creep, unrealistic expectations, under-resourcing
- Red flags from research that map to candidate deal-breakers
- **Failure criteria:** Define what "this didn't work out" looks like, tied to the candidate's stated priorities. What would make them start job searching again in 6-12 months?

#### C. Requirements Scorecard

| JD Requirement | Your Evidence | Score | Gap Analysis |
|---|---|---|---|
| (each requirement) | (specific experience) | (%) | (gap or leverage point) |

- Skills you have that go BEYOND the JD (leverage points)
- Skills gaps with suggested mitigation (what to say in interviews)

#### D. Company Card

- Company snapshot: stage, size, funding, product, market position
- Culture profile: values, leadership style, work patterns
- Growth trajectory: where company is headed, what that means for this role
- Compensation context: market data signals, equity considerations

**Output:** Present scorecard summary. Get user approval before proceeding.

---

### Phase 5: Interview Prep Guide

Generate `INTERVIEW_PREP.md` with these sections:

#### A. Likely Interview Panel

- Research who would interview for this role (hiring manager, team leads, cross-functional)
- For each likely interviewer: background, likely focus areas, what they value
- Signals about what they look for (from their posts, talks, interviews)

#### B. Questions They'll Ask You (with Suggested Answers)

For each question category, provide specific questions with answer frameworks drawing from the candidate's experience:

**Technical questions** — based on JD requirements + company tech stack
**Behavioral questions** — aligned with company values/leadership principles
**Culture fit questions** — based on stated values
**Scenario questions** — based on company-specific challenges

For each question:
- The question itself
- Why they're asking it (what they're evaluating)
- Suggested answer framework using candidate's specific experience
- What a strong answer looks like vs. a weak one

#### C. Questions You Should Ask Them

Role-specific probing questions, culture validation questions, leadership/management style questions, growth/trajectory questions, team dynamics questions.

For each question:
- The question to ask
- **Why to ask it** — what you're probing for
- **What good answers sound like** — signals this is a healthy environment
- **What red flags look like** — signals to hesitate or probe deeper

#### D. Conversation Points to Drop

- Specific experiences to weave in that map to company priorities
- Industry knowledge to demonstrate
- Shared values/philosophy connections with interviewers
- Technical opinions that would resonate with this team

#### E. Red Flags to Watch For

- Signals during interviews that suggest culture problems
- Evasive answers to watch for on specific topics
- Patterns from Glassdoor/reviews to probe during interviews
- Compensation/equity negotiation traps
- Role scope vs. title mismatches
- Questions they should ask but don't (tells you something)

#### F. Culture & Values Deep Dive

- Company's stated leadership principles/values (list each one)
- How hiring team demonstrates (or contradicts) these values (evidence)
- Questions to validate each principle is real vs. aspirational
- Alignment map: candidate's values vs. company's values (where they match, where they diverge)

**Output:** Present interview prep summary. Ask for any additions.

---

### Final Step: Generate README.md

Create `README.md` in the output directory with:
- Company and role being evaluated
- Date of evaluation
- Verdict summary (recommendation + match score)
- File descriptions and how to use each document
- Key caveats (research is point-in-time, verify in interviews)

---

## Quality Checklist

Before finishing, verify:
- [ ] All research has source URLs (no unsourced claims)
- [ ] SWOT items are specific to THIS candidate x THIS company (not generic)
- [ ] Failure risks and success criteria are grounded in candidate's stated sentiment
- [ ] Requirements scorecard covers every JD requirement
- [ ] Interview prep questions have answer frameworks using candidate's actual experience
- [ ] "Questions to ask" include what good/bad answers look like
- [ ] Red flags are specific and evidence-based
- [ ] No generic/templated content — everything is personalized
- [ ] Evidence directory materials are incorporated where relevant
- [ ] Company Intel has sources for every major claim
