# Build Profile — Generate Your Candidate Profile from Evidence

Automatically builds a comprehensive `candidate-profile.md` from your resume, web presence, publications, patents, and any supporting evidence you provide. This replaces manually filling in the template — the agent extracts, researches, and synthesizes everything, then asks you to validate and add the human context that only you know.

## Inputs

**Usage:** `/build-profile "resume-path" "evidence-dir" "linkedin-url" "github-usernames"`

Parse `$ARGUMENTS` as up to four quoted strings:
- **First quoted string** (required) — Path to resume file (PDF or markdown)
- **Second quoted string** (optional) — Path to directory containing supporting evidence
- **Third quoted string** (optional) — LinkedIn profile URL
- **Fourth quoted string** (optional) — GitHub username(s), comma-separated (e.g., `"deeso"` or `"deeso,deeso-sec"`)

If resume path is missing, ask. Everything else can be gathered during the process.

**Evidence directory:** If provided, scan and read all files (PDFs, markdown, text, etc.):
- Cover letters (reveal how you position yourself)
- Portfolio or project descriptions
- Publications and papers
- LinkedIn PDF export
- Personal website content
- Recommendation letters
- Conference talk abstracts or slides
- Blog posts
- Certifications
- Performance reviews (if willing to share — these are gold for impact metrics)

**GitHub repos:** If GitHub username(s) provided, the agent uses `gh` CLI to deeply analyze repos, contributions, and activity. If not provided, the agent will search for GitHub profiles during web research and ask.

## Setup

This command expects a `claude-job-hunter` repo cloned locally. Find it by checking (in order):
1. `$CLAUDE_JOB_HUNTER_DIR` environment variable
2. `./claude-job-hunter/` relative to cwd
3. `~/claude-job-hunter/`

If not found, ask the user for the path.

## Output

Create or overwrite:
```
[claude-job-hunter]/profile/candidate-profile.md
```

If a profile already exists, read it first and ask the candidate whether to:
- Start fresh (overwrite)
- Update and enrich the existing profile

## Workflow

### Phase 1: Ingest All Supplied Materials

#### 1a. Read the Resume

Read the resume file. Extract everything:
- Contact information
- Job titles, companies, dates, durations
- Bullet points and achievements
- Skills listed
- Education
- Certifications
- Formatting and emphasis choices (what the candidate chose to highlight tells you something)

#### 1b. Read Evidence Directory

If provided, list and read every file. For each, extract:
- **Cover letters:** How the candidate describes themselves, what they emphasize, language and tone
- **Publications/papers:** Titles, venues, co-authors, topics, awards
- **Project docs:** Technical depth, tools used, scale, outcomes
- **LinkedIn export:** Recommendations received, endorsements, activity, volunteer work, education details
- **Blog posts:** Topics they care about, technical depth, writing style
- **Conference talks:** What they present on, audiences they address
- **Certifications:** Issuing bodies, dates, relevance
- **Recommendation letters:** What others say about them (themes, patterns)
- **Performance reviews:** Impact metrics, feedback themes, growth areas

#### 1c. Analyze GitHub Repos

If GitHub username(s) were provided (4th arg) or found in the resume/evidence, do a deep analysis using `gh` CLI and WebFetch.

**For each GitHub username, run:**
```
gh api users/[USERNAME] --jq '{name, bio, company, location, public_repos, followers}'
gh api users/[USERNAME]/repos --paginate --jq '.[] | {name, description, language, stargazers_count, forks_count, updated_at, topics, homepage}' | sort by stars
gh api users/[USERNAME]/events --per-page=100 --jq '.[].type' | sort | uniq -c | sort -rn
```

**For each notable repo (starred, pinned, or substantial):**
```
gh api repos/[USERNAME]/[REPO] --jq '{description, language, stargazers_count, forks_count, topics, license, created_at, pushed_at}'
gh api repos/[USERNAME]/[REPO]/languages
gh api repos/[USERNAME]/[REPO]/contributors --per-page=5 --jq '.[].login'
```

Also read the repo README if it exists (via WebFetch on the GitHub URL) to understand what was built, why, and how.

**Extract from GitHub analysis:**
- **Technical signal:** Primary languages, frameworks, and tools actually used in code (not just listed on resume)
- **Builder signal:** Original projects vs. forks, frequency of commits, recency of activity
- **Scope signal:** Solo projects vs. collaborations, project complexity, documentation quality
- **Domain signal:** What problem domains do the repos cover? Security tooling, data pipelines, web apps, research tools, etc.
- **Open source signal:** Contributions to other projects (PRs, issues), community engagement
- **Craft signal:** Code quality indicators — tests, CI/CD, documentation, architecture patterns
- **Notable repos:** Projects worth highlighting in the profile (starred, unique, technically impressive, or relevant to target roles)

**Categorize repos:**
1. **Showcase repos** — impressive, well-documented, demonstrate real skills (highlight in profile)
2. **Evidence repos** — support resume claims (e.g., "I built security tooling" backed by actual security tools)
3. **Learning/exploration repos** — show curiosity and growth but not production-grade
4. **Forks and contributions** — show open source engagement

If the candidate has multiple GitHub accounts (e.g., personal + work), analyze each and note the distinction.

#### 1d. Catalog What You Have

Present a summary of all materials ingested:
```
Materials found:
- Resume: [filename] — [X pages, extracted N roles, N skills]
- Evidence directory: [N files]
  - [filename]: [type — cover letter / publication / etc.] — [brief summary]
  - ...
- GitHub: [username(s)]
  - [N] public repos, [N] stars total, primary languages: [list]
  - Showcase repos: [list of notable repos with brief descriptions]
  - Recent activity: [last commit date, activity level]
- LinkedIn URL: [if provided]
```

**Output:** Confirm materials list. Ask if anything is missing before proceeding.

---

### Phase 2: Deep Web Research

Research the candidate's public presence to fill gaps and add depth beyond what's in the resume.

#### 2a. Professional Identity

Search for:
- [ ] LinkedIn profile (if URL provided, fetch it; otherwise search by name + company)
- [ ] GitHub profile — repos, contribution activity, languages, stars, open source involvement
- [ ] Personal website or blog
- [ ] Twitter/X presence — topics, engagement, professional tone
- [ ] Company bios or team pages from current/past employers
- [ ] Conference speaker pages or talk recordings

#### 2b. Publications & Patents

Search for:
- [ ] Google Scholar profile — papers, citations, h-index, co-authors
- [ ] Specific paper titles from resume on Google Scholar, Semantic Scholar, DBLP, or arXiv
- [ ] USPTO patent search by name — full patent details, claims, assignees
- [ ] Google Patents for patent abstracts and filing dates
- [ ] ResearchGate or Academia.edu profile
- [ ] Thesis or dissertation (if PhD/MS mentioned)

For each publication found:
- Full title and venue
- Co-authors
- Citation count
- Abstract summary (what the paper actually contributes)
- Awards or recognition
- Relevance to industry work

For each patent found:
- Patent number and title
- Filing and grant dates
- Assignee (which company)
- Brief description of what it protects
- Claims summary

#### 2c. Open Source & Technical Footprint

If GitHub was not analyzed in Phase 1c (no username provided), search for it now:
- [ ] GitHub profile search by name + company or email
- [ ] If found, run the full Phase 1c analysis

Regardless:
- [ ] npm, PyPI, crates.io, or other package registry packages authored (search by name and GitHub username)
- [ ] Stack Overflow profile and top answers (if findable)
- [ ] Technical blog posts (personal or company engineering blog)
- [ ] Open source contributions to major projects (search GitHub for PRs by username)
- [ ] DevPost, Kaggle, or other competition profiles

#### 2d. Professional Reputation

- [ ] News mentions or press quotes
- [ ] Podcast appearances
- [ ] Industry awards or recognition
- [ ] Meetup or conference organizer roles
- [ ] Advisory or board positions
- [ ] Teaching or mentoring roles (adjunct faculty, bootcamp instructor, etc.)

#### Suggested Searches
1. `"[FULL NAME]" [CURRENT COMPANY]`
2. `"[FULL NAME]" security OR engineering OR [DOMAIN]`
3. `"[FULL NAME]" site:linkedin.com`
4. `"[FULL NAME]" site:github.com`
5. `"[FULL NAME]" site:scholar.google.com`
6. `"[FULL NAME]" patent site:patents.google.com`
7. `"[FULL NAME]" site:youtube.com OR site:speakerdeck.com`
8. `"[FULL NAME]" [UNIVERSITY] thesis OR dissertation`
9. `"[FULL NAME]" conference OR talk OR keynote`
10. `invenor:"[FULL NAME]" site:patents.google.com` (patent inventor search)

**Output:** Present web research findings. Ask the candidate:
- Is anything found inaccurate or outdated?
- Anything they'd prefer NOT included in their profile?
- Any public presence they know of that wasn't found?

---

### Phase 3: Candidate Interview

Now ask the candidate the things that only they know. This is the most important phase — the human context that makes the profile real.

Ask in conversational batches (3-4 questions at a time):

#### 3a. Career Narrative

1. **Your headline** — In one sentence, how do you describe what you do? Not your title, but your value. What's the first thing you want someone to know?
2. **Career arc** — What's the thread that connects your career moves? Was it intentional or did it evolve?
3. **Why each move?** — For each job transition, what pulled you toward the next role? (Not just what you did, but why you moved)
4. **Short tenures** — If any roles were under 18 months, what's the honest context? (Layoff, bad fit, reorg, opportunity — all valid, but the story matters for SWOT)

#### 3b. Impact & Evidence

5. **Your biggest impact** — What's the single most impressive thing you've done professionally? Tell the story — situation, what you did, what happened.
6. **Revenue connection** — Has your work ever directly enabled revenue, saved money, or prevented loss? Quantify if possible.
7. **Scale markers** — What's the biggest team you've led? Biggest budget? Biggest system you've owned? Largest customer base affected by your work?
8. **Things you've built from zero** — Programs, teams, systems, products that didn't exist before you. These are your strongest differentiators.

#### 3c. Skills & Depth

9. **Top 3 technical skills** — Not a laundry list. The three things you're genuinely deep in, where you could teach others.
10. **Tools and technologies** — What do you actually use day-to-day vs. what's on the resume because you touched it once?
11. **Leadership style** — How do you describe how you lead? What do your reports say about working for you?
12. **Cross-functional work** — How do you work with people outside your function? Engineering, product, legal, sales?

#### 3d. The Honest Section

This is what makes the profile genuinely useful. Every candidate has these — acknowledging them makes SWOT analysis and interview prep real instead of generic.

13. **What are you bad at?** — Not "I work too hard." Real weaknesses. Things that have caused friction or problems.
14. **What drains you?** — Types of work, environments, or management styles that make you want to leave.
15. **What energizes you?** — When you're at your best, what does the work look like?
16. **Deal-breakers** — Things about a company or role that would make you walk away, no matter the comp.
17. **What would a critical reference say?** — If someone who didn't love working with you gave feedback, what would they say?
18. **Tenure risks** — What patterns have led to short stays in the past? What conditions need to be true for you to stay 3+ years?

#### 3e. Career Goals & Priorities

19. **Where are you headed?** — What role do you want in 3-5 years? Not just title — what does the work look like?
20. **What are you optimizing for right now?** — Growth, compensation, impact, stability, learning, title, team building?
21. **Comp context** — What's your current comp range? (This is used privately for comp research, not published. If they don't want to share, that's fine.)

**Output:** Confirm you have everything. Ask if they want to add anything else.

---

### Phase 4: Synthesize the Profile

Now build the `candidate-profile.md` following the template structure. Every section must be populated with real content from the research and interview.

#### A. Contact
- Pull from resume, LinkedIn, GitHub findings

#### B. Headline
- Use the candidate's own words from Q1, refined for punch
- Should be specific to their value prop, not a generic title

#### C. Summary Stats
- Extract the most impressive quantified metrics
- Choose stats that span technical, leadership, and impact
- Every stat must be backed by evidence from the resume or interview

#### D. Key Impact Metrics
- 5-7 metrics, each with context for WHY it matters
- Prioritize: revenue impact > cost savings > scale markers > speed metrics
- Each should be defensible in an interview (the candidate can tell the full story)

#### E. Core Competencies
- Organized by domain, not a flat skills list
- Each skill has specific tools, technologies, and scale indicators
- Distinguish between "deep expertise" and "working knowledge"
- Include leadership and business skills alongside technical
- Cross-reference with GitHub repos: if they claim Python expertise, which repos demonstrate it?

#### F. Career History
- For each role: title, dates, company, one-line context
- Key achievements with metrics (drawn from resume + interview)
- Technologies and scale
- Any relevant context (short tenure explanations, company stage)
- Most recent roles get more detail; early career can be brief

#### G. Patents
- Full title, patent number, filing/grant dates
- Brief description of what it protects (from patent research)

#### H. Publications
- Full title, venue, co-authors
- Awards or recognition
- Brief description of the contribution
- Citation count if significant

#### I. GitHub & Open Source
- GitHub username(s) with profile links
- Showcase repos: 3-5 most impressive or relevant projects, each with:
  - Repo name and link
  - What it does (one line)
  - Languages/technologies
  - Stars/forks (if notable)
  - Why it matters (what it demonstrates about the candidate)
- Open source contributions to external projects (if any)
- Overall GitHub signal summary: activity level, languages, domain focus
- Package registry packages authored (npm, PyPI, etc.)

#### J. Certifications
- Name, issuing body, date if relevant

#### K. Honest Considerations
- This is the most important section for downstream skills
- Draw from Phase 3d answers
- Frame each consideration with the real context AND how the candidate thinks about it
- These get used in SWOT analysis, interview prep, and offer negotiation
- 5-7 items covering: overqualification risks, tenure patterns, autonomy needs, culture requirements, growth expectations, communication style, comp expectations

#### L. Resume PDFs
- Reference the resume file path provided

---

### Phase 5: Review & Finalize

Present the complete profile to the candidate section by section.

For each section, ask:
- Is this accurate?
- Is anything missing?
- Is anything you want to remove or soften?
- Is anything overstated or understated?

**Pay special attention to:**
- Impact metrics — are they defensible? Could they say more here?
- Honest considerations — are these real enough? Too harsh? Not honest enough?
- Career narrative — does the thread make sense?
- Skills — anything listed they wouldn't want to be tested on in an interview?

After incorporating feedback, write the final `candidate-profile.md`.

**Output:** Confirm the profile is saved. Remind the candidate that this profile feeds into all other skills:
- `/should-i-apply` uses it for SWOT and requirements mapping
- `/mock-interview` uses it for answer frameworks and story preparation
- `/comp-research` uses it for leveling and current comp context
- `/offer-negotiation` uses it for leverage assessment
- `/player-card` uses it for the entire card content

---

## Update Mode

If a profile already exists and the candidate wants to update it:

1. Read the existing profile
2. Ask what has changed:
   - New role or promotion?
   - New achievements or metrics?
   - New skills or certifications?
   - Changed career goals or priorities?
   - Updated honest considerations?
3. Re-run web research to catch new publications, patents, or public mentions
4. Merge updates into existing profile rather than starting from scratch
5. Present diffs to the candidate for approval

---

## Quality Checklist

Before finishing, verify:
- [ ] Every section of the template is populated (no placeholders or "[fill in]" remaining)
- [ ] All impact metrics are quantified with specific numbers
- [ ] Career history has no gaps — every role from resume is accounted for
- [ ] Short tenures have honest context explanations
- [ ] Patents have real patent numbers verified from USPTO/Google Patents
- [ ] Publications have real venues and titles verified from Google Scholar
- [ ] Honest Considerations section has 5-7 substantive items (not fluffy weaknesses)
- [ ] Skills listed are things the candidate would be comfortable being tested on
- [ ] Headline is specific and compelling (not generic)
- [ ] Web research findings are incorporated (publications, talks, etc.)
- [ ] GitHub repos are analyzed and showcase repos are included with links
- [ ] Technical skills claims are cross-referenced with GitHub evidence where possible
- [ ] Nothing is included that the candidate asked to exclude
- [ ] Profile reads as authentic, not inflated — the tone matches how the candidate actually speaks
