# Claude Job Hunter

AI-powered job search toolkit using [Claude Code](https://docs.anthropic.com/en/docs/claude-code) slash commands. Seven skills that cover the full job search lifecycle:

- **`/build-profile`** — Generate your candidate profile from resume, web research, publications, patents, and supporting evidence
- **`/should-i-apply`** — Evaluate WHETHER you should apply, with a scorecard, sentiment-driven SWOT, and deep interview prep
- **`/mock-interview`** — Practice interview loops with researched questions, written answers, and critical feedback on content and communication
- **`/interview-feedback`** — Debrief after each real interview round with signal analysis, performance assessment, and next-round strategy
- **`/comp-research`** — Build an objective compensation picture: market data, leveling, peer benchmarks, geography adjustments
- **`/offer-negotiation`** — Evaluate an offer, build negotiation strategy with scripts, and decide whether to accept, counter, or walk
- **`/player-card`** — Generate a password-protected Cloudflare Worker site that showcases you TO a company (deployed, shareable)

All commands use live web research to produce outputs specific to you and the target company — no generic templates.

## Quick Start

### 1. Clone and configure

```bash
git clone https://github.com/deeso/claude-job-hunter.git ~/claude-job-hunter
```

### 2. Build your candidate profile

Run `/build-profile` to generate your profile automatically from your resume, GitHub, and evidence:

```
/build-profile "~/resume.pdf" "~/evidence/" "https://linkedin.com/in/your-profile" "your-github-username"
```

Or edit `profile/candidate-profile.md` manually. This is the core data source all commands use. The more honest and specific you are (especially the "Honest Considerations" section), the better the outputs.

### 3. Install the slash commands

Symlink or copy the commands into your Claude Code commands directory:

```bash
# Create Claude commands directory if it doesn't exist
mkdir -p ~/.claude/commands

# Symlink the commands (recommended — stays in sync with repo updates)
ln -sf ~/claude-job-hunter/commands/player-card.md ~/.claude/commands/player-card.md
ln -sf ~/claude-job-hunter/commands/should-i-apply.md ~/.claude/commands/should-i-apply.md
ln -sf ~/claude-job-hunter/commands/interview-feedback.md ~/.claude/commands/interview-feedback.md
ln -sf ~/claude-job-hunter/commands/comp-research.md ~/.claude/commands/comp-research.md
ln -sf ~/claude-job-hunter/commands/offer-negotiation.md ~/.claude/commands/offer-negotiation.md
ln -sf ~/claude-job-hunter/commands/mock-interview.md ~/.claude/commands/mock-interview.md
ln -sf ~/claude-job-hunter/commands/build-profile.md ~/.claude/commands/build-profile.md
```

### 4. Use them

Open Claude Code and run:

```
# First time? Build your profile from your resume + GitHub
/build-profile "~/resume.pdf" "~/evidence/" "https://linkedin.com/in/you" "your-github"

# Before applying — should I bother?
/should-i-apply "~/resume.pdf" "Anthropic" "Security Engineer" "https://job-url.com" "~/evidence/"

# Practice before the real thing
/mock-interview "Anthropic" "Security Engineer" "technical"

# After each real interview round — how did it go?
/interview-feedback "Anthropic" "Security Engineer" "Jane Smith" "VP of Engineering"

# Know your market value before the offer comes
/comp-research "Anthropic" "Security Engineer" "Staff"

# Offer in hand — what's the play?
/offer-negotiation "Anthropic" "Security Engineer"

# Ready to send something impressive — build the card
/player-card "Anthropic" "Security Engineer" "https://job-url.com"
```

## Commands

### `/build-profile`

**Purpose:** Generate your candidate profile automatically instead of filling in the template by hand. The agent extracts, researches, and synthesizes — you validate and add the human context.

**Usage:** `/build-profile "resume-path" "evidence-dir" "linkedin-url" "github-usernames"`

Arguments 2-4 are optional. Multiple GitHub usernames can be comma-separated (e.g., personal + work accounts).

**How it works:**
1. **Ingests all materials** — reads resume, evidence directory (cover letters, publications, project docs, LinkedIn export, recommendations, performance reviews), catalogs what it found
2. **GitHub deep analysis** — uses `gh` CLI to analyze repos, languages, activity, stars, contribution patterns. Categorizes repos into showcase (impressive), evidence (backs resume claims), and contributions (open source engagement). Cross-references technical skills claims with actual code.
3. **Deep web research** — searches for your public presence:
   - Google Scholar (papers, citations, h-index)
   - USPTO/Google Patents (full patent details)
   - npm/PyPI/crates.io packages authored
   - Conference talks, blog posts, press mentions
   - LinkedIn, Twitter/X, personal website
4. **Candidate interview** — asks the things only you know:
   - Career narrative and why you made each move
   - Biggest impact stories with quantified evidence
   - Top technical skills (what you're truly deep in vs. what's on the resume)
   - **The honest section** — real weaknesses, what drains you, deal-breakers, what a critical reference would say, tenure risks
   - Career goals and what you're optimizing for
5. **Synthesizes the profile** — builds `candidate-profile.md` with GitHub showcase repos, verified publications, and all evidence
6. **Review** — walks through each section for your approval

Supports **update mode**: if a profile exists, merges new information and re-runs web research to catch recent publications, patents, or mentions.

### `/should-i-apply`

**Purpose:** Decide if a role is worth pursuing before investing time in applications.

**Usage:** `/should-i-apply "resume-path" "company" "role" "jd-url" "evidence-dir"`

Arguments 4 and 5 are optional. The evidence directory can contain cover letters, portfolio pieces, publications, project docs, LinkedIn exports — anything that strengthens your profile.

**Produces:**
```
evaluations/[company]-[role-slug]/
├── SCORECARD.md       # Verdict (Strong Yes → No), SWOT, requirements mapping
├── INTERVIEW_PREP.md  # Likely panel, Q&A with answer frameworks, red flags
├── COMPANY_INTEL.md   # Sourced research on company, leadership, culture
└── README.md          # Summary and usage guide
```

**Key features:**
- Sentiment-driven SWOT: failure risks and success criteria based on YOUR priorities, not generic advice
- Interview prep with specific answer frameworks drawn from YOUR experience
- "Questions to ask them" with what good and bad answers look like
- Culture & values alignment map between you and the company

### `/mock-interview`

**Purpose:** Practice interview loops with researched questions before the real thing. Get critical feedback on both what you say and how you say it.

**Usage:** `/mock-interview "company" "role" "round-type"`

Argument 3 is optional. Round types: `"phone-screen"`, `"technical"`, `"behavioral"`, `"hiring-manager"`, `"cross-functional"`, `"executive"`, or `"full-loop"` (default).

**How it works:**
1. Researches the company's interview style (Glassdoor reviews, reported questions, values/leadership principles, tech stack)
2. Builds a panel of 4-6 realistic interviewers with distinct personalities and evaluation focuses
3. Runs each round one question at a time — you answer in writing, the agent asks follow-up probes in character
4. After each round, provides detailed feedback before moving to the next

**Feedback covers:**
- **Content assessment** — what was strong, what was missing, specific evidence from your experience you should have included
- **Communication assessment** — structure, conciseness, specificity, confidence signals, storytelling quality
- **Stronger answer frameworks** — rewritten outlines using YOUR actual experience as a structural model
- **Skills heat map** — across all rounds, where you're strong and where to focus practice
- **Communication style report** — recurring habits to build and break, word/phrase audit
- **Practice plan** — prioritized topics, stories to prepare (with STAR outlines from your profile), rounds to re-run

Supports re-run mode: practice the same round type with fresh questions, with feedback comparing against prior attempts.

### `/interview-feedback`

**Purpose:** Structured post-interview debrief that reads the signals and prepares you for the next round.

**Usage:** `/interview-feedback "company" "role" "interviewer-name" "interviewer-title"`

Arguments 3 and 4 are optional (the agent will ask during debrief).

**How it works:** The agent asks you structured questions about:
- What questions the interviewer asked and how you answered
- Interviewer behavior — engagement, body language, selling signals
- Red flags and green flags you observed
- What you learned about the role, team, and culture
- How the interview ended and what next steps were discussed

**Produces:** Appends to `INTERVIEW_LOG.md` in the evaluation directory:
- **Signal analysis** — reads interviewer behavior to assess likelihood of advancing
- **Performance assessment** — rates each answer with "what a stronger answer looks like" for weak spots
- **Preparation gap analysis** — compares against `INTERVIEW_PREP.md` predictions if available
- **Culture & fit updates** — revises SWOT based on new information from the interview
- **Next round strategy** — concrete topics, stories, and questions to prepare
- **Thank-you note draft** — personalized to the conversation, not generic

Builds a running log across rounds with cross-round pattern analysis.

### `/comp-research`

**Purpose:** Build an objective compensation picture before an offer arrives so you negotiate from data, not feelings.

**Usage:** `/comp-research "company" "role" "level"`

Argument 3 is optional (the agent determines likely level from your profile).

**Researches:**
- **Market data** — levels.fyi, Glassdoor, H1B/LCA filings, job posting salary ranges
- **Company-specific** — comp philosophy, pay bands, equity structure (options vs RSUs), benefits
- **Peer benchmarks** — 5-8 companies competing for the same talent with comp comparison table
- **Geography** — cost of living adjustments, remote pay policies, state tax implications
- **Leveling** — maps your experience to the company's framework, flags down-leveling risk

**Produces:** `COMP_RESEARCH.md` in the evaluation directory:
- Compensation bands: below market / market rate / above market / stretch target
- Component breakdown: base, equity, signing, bonus, benefits with specific ranges
- Current comp vs. market comparison table
- Company negotiation intelligence (how they negotiate, flexibility patterns)

### `/offer-negotiation`

**Purpose:** You have an offer. Decide whether to accept, negotiate, or walk — then build the actual negotiation with scripts and counter-offer language.

**Usage:** `/offer-negotiation "company" "role"`

**How it works:** The agent walks you through:
- Capturing every offer component (base, equity, signing, bonus, benefits, clawback, non-compete)
- **Emotional check-in** — do you actually want this job? What's your alternative? What makes you say yes or walk away?
- Offer analysis against market data, current comp, and other offers
- Leveling check (were you down-leveled?)
- Hidden comp factors (equity tax treatment, exercise windows, clawback terms)

**Produces:** `NEGOTIATION_STRATEGY.md` in the evaluation directory:
- **Verdict:** Accept / Negotiate / Walk Away with reasoning
- **Priority-ordered negotiation targets** with justification and fallback positions
- **Ready-to-send counter-offer email** with specific numbers and framing
- **Phone script** and **pushback responses** for each ask
- **Decision framework** — weighted scorecard reflecting YOUR priorities
- **Scenario analysis** — accept now / negotiate-win / negotiate-hold / walk away

Reads prior skills' outputs (`COMP_RESEARCH.md`, `INTERVIEW_LOG.md`, `SCORECARD.md`) to inform strategy.

### `/player-card`

**Purpose:** Generate a deployable site that showcases you to a specific company.

**Usage:** `/player-card "company" "role" "jd-url"`

**Produces:** A complete Cloudflare Worker project with:
- Password-protected single-page site with company-specific pitch
- Embedded resume PDF (no external storage)
- Email alerts when someone views your card (via Resend)
- Custom domain routing (`company.yourdomain.com/player-card`)

**Requires:** Cloudflare account, custom domain, Resend API key.

## Repository Structure

```
claude-job-hunter/
├── commands/                        # Claude Code slash commands
│   ├── player-card.md               # /player-card command
│   ├── should-i-apply.md            # /should-i-apply command
│   ├── interview-feedback.md        # /interview-feedback command
│   ├── comp-research.md             # /comp-research command
│   ├── offer-negotiation.md         # /offer-negotiation command
│   ├── mock-interview.md            # /mock-interview command
│   └── build-profile.md             # /build-profile command
├── profile/
│   └── candidate-profile.md         # Your profile (generated by /build-profile or manual)
├── resources/
│   ├── card-architecture.md         # Player card HTML/CSS/Worker reference
│   ├── research-checklist.md        # Company research prompts
│   └── interview-research-checklist.md  # Leadership & culture research
├── examples/
│   ├── openrouter-research.md       # Example company research output
│   └── openrouter-fit-analysis.md   # Example fit analysis output
├── evaluations/                     # /should-i-apply outputs go here
│   └── [company]-[role]/
└── cards/                           # /player-card outputs go here
    └── [company]-[year]/
```

## How It Works

The commands cover the full job search lifecycle:

1. **`/build-profile`** — Onboarding: build your candidate profile from resume + web research
2. **`/should-i-apply`** — Pre-application: research, SWOT analysis, interview prep
3. **`/mock-interview`** — Pre-interview: practice with researched questions, get feedback
4. **`/interview-feedback`** — During process: debrief each round, read signals, prep for next
5. **`/comp-research`** — Pre-offer: build objective comp picture from market data
6. **`/offer-negotiation`** — Offer stage: evaluate, strategize, negotiate with scripts
7. **`/player-card`** — Anytime: deploy a personalized showcase site

Each command follows a multi-phase workflow with user confirmation between phases. Outputs accumulate in the same `evaluations/[company]-[role]/` directory, building a complete picture over time.

## Tips

- **Start with `/build-profile`.** Let the agent build your profile from your resume and web presence. The candidate interview phase asks the hard questions that make everything else work.
- **Be honest in your candidate profile.** The "Honest Considerations" section makes SWOT analysis genuinely useful. Generic strengths produce generic outputs.
- **Use the evidence directory.** The more context you give (cover letters, project docs, publications), the stronger the evidence mapping in scorecards and interview prep.
- **Review between phases.** Both commands pause for confirmation. Add context the AI might have missed.
- **Practice before real interviews.** Run `/mock-interview` to identify weak spots. Re-run specific round types until your answers are tight. The communication feedback is where the real value is.
- **Debrief every round.** Run `/interview-feedback` after each real interview while details are fresh. The running log across rounds reveals patterns.
- **Run comp research early.** `/comp-research` before the offer means you negotiate from data, not gut feelings. The research feeds directly into `/offer-negotiation`.
- **Full lifecycle.** `/build-profile` → `/should-i-apply` → `/mock-interview` → `/interview-feedback` (per round) → `/comp-research` → `/offer-negotiation` → `/player-card` if you want to send something impressive.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- For `/player-card` deployment: Cloudflare account, Wrangler CLI, custom domain, Resend API key
- For all other commands: no infrastructure needed — outputs are local markdown files
