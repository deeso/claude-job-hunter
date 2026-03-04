# Skill Gap — Ideal Profile Benchmark & Development Plan

Build an ideal candidate profile for a target role, benchmark your current skills against it, identify gaps, and create a time-based development plan to close them. This is NOT about whether to apply — it's about becoming the strongest candidate over time.

## Inputs

**Usage:** `/skill-gap "company" "role" "jd-url" "timeline" "profile-name"`

Parse `$ARGUMENTS` as up to five quoted strings:
- **First quoted string** (optional) — Company name or role archetype (e.g., `"Anthropic"` or `"FAANG"` or `"AI startup"`)
- **Second quoted string** (required) — Target role title (e.g., `"Staff Security Engineer"`, `"VP of Engineering"`)
- **Third quoted string** (optional) — URL to a specific job description (grounds the ideal profile in a real posting)
- **Fourth quoted string** (optional) — Timeline for the development plan (e.g., `"6 months"`, `"1 year"`, `"2 years"`). Default: `"1 year"`
- **Fifth quoted string** (optional) — Profile name (e.g., `"default"`, `"security"`). See Profile Resolution below.

If role title is missing, ask. Everything else can be inferred or defaulted.

The first argument is intentionally flexible:
- **Specific company** (`"Anthropic"`) — ideal profile is tailored to that company's expectations
- **Company category** (`"FAANG"`, `"AI startup"`, `"Series B fintech"`) — ideal profile represents the archetype
- **Omitted** — ideal profile is based on industry-wide expectations for the role

## Setup

Resolve paths by checking (in order):
1. Read `~/.claude/.claude-job-hunter.conf` for `WORK_DIR`, `REPO_DIR`, and component paths
2. Fall back: `$CLAUDE_JOB_HUNTER_DIR` → `./claude-job-hunter/` → `~/claude-job-hunter/`

If the config exists, use its paths (`PROFILE_DIR`, `EVALUATIONS_DIR`, `RESOURCES_DIR`, etc.). Otherwise, use `[claude-job-hunter]/` as both repo and working directory.

If nothing is found, ask the user to run `/setup` first or provide the path.

## Profile Resolution

Determine which profile to use:

1. **Explicit argument:** If a profile name was passed (5th arg), use `[PROFILE_DIR]/[profile-name].md` (append `.md` if not present)
2. **Config default:** Read `ACTIVE_PROFILE` from `~/.claude/.claude-job-hunter.conf` — if set, use `[PROFILE_DIR]/[ACTIVE_PROFILE].md`
3. **Auto-detect:** Scan `[PROFILE_DIR]/` for `.md` files other than `candidate-profile.md`:
   - **One found** → use it automatically
   - **Multiple found** → list each with its first heading line, ask the user to pick
   - **None found** → tell the user: "No profile found. Run `/build-profile` first."

The resolved profile path replaces all references to `candidate-profile.md` below.

## Existing Context

Before starting, check for existing materials:
- `[PROFILE_DIR]/[resolved-profile-name].md` — candidate background (required)
- `[EVALUATIONS_DIR]/[company]-[role-slug]/SCORECARD.md` — prior evaluation if available
- `[EVALUATIONS_DIR]/[company]-[role-slug]/COMPANY_INTEL.md` — company research
- `[EVALUATIONS_DIR]/[company]-[role-slug]/INTERVIEW_LOG.md` — interview feedback (gold for gap identification)
- `[EVALUATIONS_DIR]/[company]-[role-slug]/MOCK_INTERVIEW.md` — mock interview performance

If prior evaluations exist, use them to ground the analysis. Interview feedback and mock interview results are especially valuable — they reveal gaps that showed up under pressure.

## Output

Create or update:
```
[EVALUATIONS_DIR]/[company-or-archetype]-[role-slug]/
└── SKILL_GAP.md    # Ideal profile, benchmark, gap analysis, development plan
```

## Workflow

### Phase 1: Research the Ideal Candidate

Build a comprehensive picture of what the ideal candidate looks like for this role. This is NOT the user's profile — it's the benchmark they're aiming for.

#### 1a. JD Analysis (if URL provided)

Fetch the JD via WebFetch. Extract:
- Required skills with stated experience levels
- Nice-to-have qualifications
- Scope indicators (team size, budget, reporting structure)
- Technical stack and tools
- Leadership and soft skill expectations
- Level signals (years, scope, "you've done X at scale")

#### 1b. Market Research

Use WebSearch to understand what the market expects for this role:

**Job posting analysis:**
- [ ] Search for 5-10 similar JDs at comparable companies: `"{role}" "{level}" job posting {company-type}`
- [ ] Extract common requirements across postings — the intersection is the true baseline
- [ ] Note requirements that appear in 80%+ of postings vs. those in <30% (table stakes vs. differentiators)

**Industry expectations:**
- [ ] Search for `"{role}" career ladder` or `"{role}" leveling guide"` — what do companies expect at this level?
- [ ] Search for `"{role}" interview questions` — what skills are actually tested?
- [ ] Search for `"{role}" day in the life` or `"{role}" what do you actually do` — real scope vs. JD scope
- [ ] Conference talks and blog posts by people in similar roles — what do they work on?

**Compensation-level signals:**
- [ ] levels.fyi for this role/level — what tier of skills commands top-quartile comp?
- [ ] LinkedIn profiles of people in this role at target companies — what do their backgrounds look like?

#### 1c. Build the Ideal Candidate Profile

Synthesize research into a structured ideal profile:

**Technical Skills (ranked by importance):**

| Skill | Importance | Expected Depth | Evidence the Market Expects |
|-------|-----------|---------------|---------------------------|
| [skill] | Critical / Important / Nice-to-have | Expert / Proficient / Familiar | [which JDs, what % mention it] |

**Leadership & Soft Skills:**

| Skill | Importance | Expected Level | Evidence |
|-------|-----------|---------------|---------|
| [skill] | Critical / Important / Nice-to-have | [description] | [source] |

**Experience Requirements:**

| Requirement | Expected | Evidence |
|-------------|----------|---------|
| Years in role/domain | X-Y years | [market data] |
| Team leadership | X direct reports | [JD patterns] |
| Scale markers | [users, revenue, systems] | [JD patterns] |
| Industry certifications | [list] | [JD patterns] |
| Publications/talks | [expected level] | [role norms] |
| Open source / public work | [expected level] | [role norms] |

**Differentiators — what separates "good" from "outstanding":**
- The top 3-5 things that would make a hiring manager say "we have to hire this person"
- These are often NOT in the JD but emerge from interview patterns and role research

**Output:** Present the ideal candidate profile. Ask the user: "Does this match your understanding of the target role? Anything to adjust?"

---

### Phase 2: Benchmark Current Skills

Map the user's actual skills against the ideal profile.

#### 2a. Read Candidate Profile

Read the resolved profile. Extract:
- Technical skills with depth indicators
- Leadership experience and scope
- Career history with scale markers
- Certifications and education
- Publications, patents, talks
- GitHub/open source presence
- Honest considerations (self-identified weaknesses)

#### 2b. Incorporate Interview Evidence

If `INTERVIEW_LOG.md` or `MOCK_INTERVIEW.md` exists:
- Extract performance ratings per skill area
- Note questions where the candidate struggled
- Note skills that interviewers probed but found lacking
- Note areas where the candidate exceeded expectations

This is the most honest signal — self-assessment is less reliable than interview performance.

#### 2c. Build the Benchmark Scorecard

For each skill/requirement in the ideal profile, rate the candidate:

**Technical Skills Benchmark:**

| Skill | Ideal Level | Your Level | Gap | Evidence (from profile/interviews) |
|-------|------------|-----------|-----|-----------------------------------|
| [skill] | Expert | Proficient | Moderate | [specific evidence] |
| [skill] | Proficient | Expert | None (strength) | [specific evidence] |
| [skill] | Familiar | None | Significant | [no evidence found] |

Rating scale for gap:
- **None** — meets or exceeds the ideal (strength to leverage)
- **Minor** — close, needs sharpening or recent experience (< 3 months effort)
- **Moderate** — meaningful gap, addressable with focused effort (3-6 months)
- **Significant** — major gap, requires substantial investment (6-12 months)
- **Foundational** — would need to start from near-zero (12+ months or may not be realistic)

**Leadership & Soft Skills Benchmark:**

| Skill | Ideal Level | Your Level | Gap | Evidence |
|-------|------------|-----------|-----|---------|

**Experience Benchmark:**

| Requirement | Ideal | Yours | Gap | Notes |
|-------------|-------|-------|-----|-------|

**Overall Readiness Score:** [0-100%] — weighted by skill importance (Critical skills weigh 3x, Important 2x, Nice-to-have 1x)

**Output:** Present the benchmark. Ask: "Does this feel accurate? Any skills where you think you're stronger or weaker than I've assessed?"

---

### Phase 3: Gap Analysis

Categorize all gaps and prioritize them.

#### A. Gap Categories

**Strengths to Leverage (No Gap):**
Skills where you meet or exceed the ideal. These are your selling points and the foundation for your development plan — you build from strength.

**Quick Wins (Minor Gaps):**
Skills where you're close but need sharpening. These have the highest ROI for short-term effort:
- Certifications you could earn
- Technologies you know conceptually but haven't used recently
- Skills you have but can't demonstrate (need portfolio evidence)

**Development Priorities (Moderate Gaps):**
Skills requiring focused, sustained effort. These are the core of the development plan:
- Adjacent skills where your foundation is strong
- Leadership skills you haven't had the opportunity to practice
- Technical depth in areas where you have breadth

**Strategic Investments (Significant Gaps):**
Skills requiring substantial time. Be honest about whether these are worth pursuing:
- Do you WANT to develop these skills, or are they just on the JD?
- Is there a way to compensate (adjacent skills, alternative evidence)?
- Would a different target role be a better fit?

**Unrealistic Gaps (Foundational):**
Skills where the gap is so large that closing it within the timeline isn't practical. Don't sugar-coat this:
- Acknowledge the gap honestly
- Suggest alternative paths (different role level, different company type, longer timeline)
- If multiple foundational gaps exist, the target role may not be the right goal

#### B. Gap Impact Matrix

| Gap | Importance | Size | Impact | Priority |
|-----|-----------|------|--------|----------|
| [skill] | Critical | Moderate | High | P1 |
| [skill] | Important | Minor | Medium | P2 |
| [skill] | Nice-to-have | Significant | Low | P3 |

Impact = Importance × Size. This determines development plan priority.

#### C. Honest Assessment

Present a candid summary:
- **Timeline feasibility:** Can the candidate realistically close enough gaps in [timeline] to be competitive?
- **If yes:** "You're [X]% ready now. With focused effort on [top 3 gaps], you can reach [Y]% in [timeline]."
- **If borderline:** "You can close the technical gaps, but [specific area] will take longer than [timeline]. Consider [alternative]."
- **If no:** "The gap between your current profile and this role is [honest assessment]. Consider [stepping stone role] first, or extend the timeline to [realistic estimate]."

**Output:** Present gap analysis. Ask: "Does this priority order match where you want to focus? Any gaps you want to deprioritize or elevate?"

---

### Phase 4: Development Plan

Build a concrete, time-based plan to close the prioritized gaps.

#### 4a. Ask Development Preferences

Before building the plan, ask:

1. **Time budget:** How many hours per week can you dedicate to development outside of work? (e.g., 5, 10, 15, 20 hours)
2. **Learning style:** How do you learn best?
   - Building projects (hands-on)
   - Courses and structured learning
   - Reading (books, papers, docs)
   - Teaching/writing (blog posts, talks)
   - On-the-job stretch assignments
3. **Current resources:**
   - Do you have access to a lab/cloud environment for hands-on practice?
   - Education budget from current employer?
   - Conference/training budget?
4. **Constraints:**
   - Are you currently employed? (affects time and what you can do publicly)
   - Geographic/timezone constraints for meetups, conferences?
   - Budget constraints for courses, certifications, tools?

#### 4b. Build the Plan

Structure the plan in time blocks. For a 1-year timeline, use quarterly blocks. For 6 months, use monthly. For 2 years, use half-year blocks.

**For each time block:**

```
### [Time Block]: [Theme]
(e.g., "Months 1-3: Foundation Building")

**Focus areas:** [2-3 skills from the gap priority list]

**Goals by end of block:**
- [ ] [Specific, measurable goal] — closes [gap] from [current] to [target]
- [ ] [Specific, measurable goal]
- [ ] [Specific, measurable goal]

**Activities:**

| Activity | Type | Time | Resource | Closes Gap |
|----------|------|------|----------|-----------|
| [specific activity] | [project/course/cert/etc] | [X hrs/week for Y weeks] | [link or name] | [skill] |

**Milestone check:** At the end of this block, you should be able to [concrete demonstration of progress].
```

#### 4c. Activity Types and Resources

For each gap, recommend specific activities from these categories:

**Hands-on Projects:**
- Build something that demonstrates the skill (add to GitHub portfolio)
- Contribute to open source projects that use the technology
- Recreate a simplified version of what the target company builds
- Solve relevant challenges (CTFs for security, system design exercises, etc.)

**Structured Learning:**
- Online courses (specify platform, course name, duration)
- Books (specify title, which chapters matter most)
- Official documentation deep-dives
- Tutorials with hands-on labs

**Certifications:**
- Which ones matter for this role (research market value)
- Study timeline and exam prep resources
- Cost and renewal requirements

**Visibility & Evidence Building:**
- Blog posts demonstrating knowledge (doubles as learning)
- Conference talk proposals (CFPs for relevant conferences)
- Open source contributions (target projects the company uses or respects)
- Technical writing (whitepapers, case studies from past work)

**Networking & Community:**
- Relevant meetups, Slack communities, Discord servers
- Conference attendance (which ones matter for this role)
- Informational interviews with people in the target role
- Mentorship (finding mentors in the target domain)

**On-the-Job Stretch:**
- Projects to volunteer for at current job that build target skills
- Internal talks or workshops to give
- Cross-functional work that builds missing experience
- Initiatives to lead that demonstrate scope

#### 4d. Portfolio Strategy

Define what the candidate's portfolio should look like by the end of the timeline:

**GitHub:**
- [X] repos demonstrating [skills], each with:
  - Clear README explaining what and why
  - Tests and CI/CD (shows engineering maturity)
  - Documentation (shows communication skills)

**Writing:**
- [X] blog posts or articles on [topics]
- [X] technical deep-dives showing expertise

**Speaking:**
- [X] talks at [tier of conference/meetup]
- CFP submissions to [specific conferences]

**Certifications:**
- [List with expected completion dates]

#### 4e. Progress Tracking

Define how to measure progress:

**Monthly self-check:**
- Which activities did I complete?
- Which skills moved up a level?
- Am I on track with the timeline?
- What needs to be adjusted?

**Quarterly benchmark:**
- Re-run `/skill-gap` to update the benchmark scorecard
- Compare readiness score over time
- Adjust plan based on what's working and what's not

**Ready-to-apply signals:**
- When readiness score reaches [X]%, start actively applying
- When [specific milestones] are hit, you're competitive
- When portfolio has [specific artifacts], you have evidence to show

---

### Phase 5: Write Output

Generate `SKILL_GAP.md`:

```markdown
# Skill Gap Analysis: [Role] @ [Company/Archetype]
**Date:** [date]
**Timeline:** [timeline]
**Current readiness:** [X]%
**Target readiness:** [Y]% by [end date]

## Ideal Candidate Profile
### Technical Skills
(ranked table from Phase 1)

### Leadership & Soft Skills
(table)

### Experience Requirements
(table)

### Differentiators
(what separates good from outstanding)

## Your Benchmark
### Technical Skills
(gap table from Phase 2)

### Leadership & Soft Skills
(gap table)

### Experience
(gap table)

### Overall Readiness: [X]%

## Gap Analysis
### Strengths to Leverage
(your selling points)

### Quick Wins (< 3 months)
(highest ROI gaps)

### Development Priorities (3-6 months)
(core focus areas)

### Strategic Investments (6-12 months)
(long-term gaps)

### Honest Assessment
(timeline feasibility, candid summary)

## Development Plan

### [Time Block 1]: [Theme]
(goals, activities table, milestone)

### [Time Block 2]: [Theme]
...

## Portfolio Strategy
(GitHub, writing, speaking, certifications)

## Progress Tracking
(monthly self-check, quarterly benchmark, ready-to-apply signals)

## Re-Run Notes
(if this is a re-run, show progress since last assessment)
```

**Output:** Present the plan summary. Ask:
- Does the pace feel right — too aggressive or too slow?
- Any activities that don't appeal or feel impractical?
- Want to adjust the timeline or target role?

---

## Re-Run Mode

When `/skill-gap` is re-run for the same role:

1. Read the existing `SKILL_GAP.md`
2. Compare current benchmark against the previous one
3. Show progress:
   - Skills that improved (and by how much)
   - Skills that stayed the same (why? was the activity effective?)
   - New gaps that emerged (market shifted, role requirements changed)
4. Update the development plan:
   - Mark completed activities
   - Adjust timelines for items that took longer/shorter than expected
   - Add new activities for areas where progress stalled
5. Add a `## Progress Update — [date]` section at the top

---

## Quality Checklist

Before finishing, verify:
- [ ] Ideal profile is grounded in real market data (multiple JDs, levels.fyi, LinkedIn profiles), not generic
- [ ] Benchmark uses specific evidence from the candidate profile, not assumptions
- [ ] Interview/mock interview performance is incorporated if available (most honest signal)
- [ ] Gap ratings are calibrated honestly — "Significant" means significant, not "you need a little more practice"
- [ ] Honest assessment doesn't sugar-coat — if the timeline is unrealistic, say so
- [ ] Development plan has specific resources (course names, book titles, project ideas), not just categories
- [ ] Activities match the candidate's stated learning style and time budget
- [ ] Portfolio strategy produces artifacts that are directly relevant to the target role
- [ ] Progress tracking has concrete re-run signals, not vague "check in periodically"
- [ ] Time estimates for activities are realistic given the candidate's weekly time budget
- [ ] Each time block has a measurable milestone (not "understand X better" but "build X and deploy it")
- [ ] The plan accounts for the candidate's current job constraints (can't do everything publicly)
