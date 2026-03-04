# Claude Job Hunter

AI-powered job search toolkit using [Claude Code](https://docs.anthropic.com/en/docs/claude-code) slash commands. Three skills that cover the full job search lifecycle:

- **`/should-i-apply`** — Evaluate WHETHER you should apply, with a scorecard, sentiment-driven SWOT, and deep interview prep
- **`/interview-feedback`** — Debrief after each interview round with signal analysis, performance assessment, and next-round strategy
- **`/player-card`** — Generate a password-protected Cloudflare Worker site that showcases you TO a company (deployed, shareable)

All commands use live web research to produce outputs specific to you and the target company — no generic templates.

## Quick Start

### 1. Clone and configure

```bash
git clone https://github.com/deeso/claude-job-hunter.git ~/claude-job-hunter
```

### 2. Fill in your candidate profile

Edit `profile/candidate-profile.md` with your information. This is the core data source both commands use. The more honest and specific you are (especially the "Honest Considerations" section), the better the outputs.

### 3. Install the slash commands

Symlink or copy the commands into your Claude Code commands directory:

```bash
# Create Claude commands directory if it doesn't exist
mkdir -p ~/.claude/commands

# Symlink the commands (recommended — stays in sync with repo updates)
ln -sf ~/claude-job-hunter/commands/player-card.md ~/.claude/commands/player-card.md
ln -sf ~/claude-job-hunter/commands/should-i-apply.md ~/.claude/commands/should-i-apply.md
ln -sf ~/claude-job-hunter/commands/interview-feedback.md ~/.claude/commands/interview-feedback.md
```

### 4. Use them

Open Claude Code and run:

```
# Before applying — should I bother?
/should-i-apply "~/resume.pdf" "Anthropic" "Security Engineer" "https://job-url.com" "~/evidence/"

# After each interview round — how did it go?
/interview-feedback "Anthropic" "Security Engineer" "Jane Smith" "VP of Engineering"

# Ready to send something impressive — build the card
/player-card "Anthropic" "Security Engineer" "https://job-url.com"
```

## Commands

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
│   └── interview-feedback.md        # /interview-feedback command
├── profile/
│   └── candidate-profile.md         # Your profile (fill this in)
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

1. **`/should-i-apply`** — Pre-application: research, SWOT analysis, interview prep
2. **`/interview-feedback`** — During process: debrief each round, read signals, prep for next
3. **`/player-card`** — Outreach: deploy a personalized showcase site

Each command follows a multi-phase workflow with user confirmation between phases. Outputs accumulate in the same `evaluations/[company]-[role]/` directory, building a complete picture over time.

## Tips

- **Be honest in your candidate profile.** The "Honest Considerations" section makes SWOT analysis genuinely useful. Generic strengths produce generic outputs.
- **Use the evidence directory.** The more context you give (cover letters, project docs, publications), the stronger the evidence mapping in scorecards and interview prep.
- **Review between phases.** Both commands pause for confirmation. Add context the AI might have missed.
- **Debrief every round.** Run `/interview-feedback` after each interview while details are fresh. The running log across rounds reveals patterns.
- **Full lifecycle.** `/should-i-apply` → `/interview-feedback` (per round) → `/player-card` if you want to send something impressive.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- For `/player-card` deployment: Cloudflare account, Wrangler CLI, custom domain, Resend API key
- For `/should-i-apply` and `/interview-feedback`: no infrastructure needed — outputs are local markdown files
