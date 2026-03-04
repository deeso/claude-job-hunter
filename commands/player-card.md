# Player Card Generator

Generate a comprehensive, tailored candidate player card for a target company and role. This creates a password-protected Cloudflare Worker site with email alerts, embedded resume, and deep company-specific fit analysis.

## Inputs

**Usage:** `/player-card "company" "role" "url job description"`

Parse `$ARGUMENTS` as three quoted strings:
- **First quoted string** — Target company name (e.g., `"anthropic"`)
- **Second quoted string** — Target role title (e.g., `"Security Engineer"`)
- **Third quoted string** — URL to the job description posting (e.g., `"https://boards.greenhouse.io/..."`)

Example: `/player-card "anthropic" "Security Engineer" "https://boards.greenhouse.io/anthropic/jobs/12345"`

If any argument is missing, ask the user for it. Do NOT proceed without all three.

Additional defaults (ask only if user wants to override):
- **Password:** `[company][year]` (e.g., `anthropic2026`)
- **Subdomain:** `[company].yourdomain.com`
- **Resume PDF:** ask user for path if not found in project directory

## Setup

Resolve paths by checking (in order):
1. Read `~/.claude/.claude-job-hunter.conf` for `WORK_DIR`, `REPO_DIR`, and component paths
2. Fall back: `$CLAUDE_JOB_HUNTER_DIR` → `./claude-job-hunter/` → `~/claude-job-hunter/`

If the config exists, use its paths (`PROFILE_DIR`, `CARDS_DIR`, `RESOURCES_DIR`, etc.). Otherwise, use `[claude-job-hunter]/` as both repo and working directory.

If nothing is found, ask the user to run `/setup` first or provide the path.

## Source Materials

Read these files before starting:

- **Candidate profile:** `[claude-job-hunter]/profile/candidate-profile.md`
- **Card architecture:** `[claude-job-hunter]/resources/card-architecture.md`
- **Research checklist:** `[claude-job-hunter]/resources/research-checklist.md`

For examples of completed work:
- `[claude-job-hunter]/examples/example-company-research.md`
- `[claude-job-hunter]/examples/example-fit-analysis.md`

## Reference Implementation

If a reference player card exists in the repo at `[claude-job-hunter]/reference/`, read these to understand the target output format:
- `reference/src/index.html` — HTML structure, CSS design, content sections
- `reference/src/worker.js` — Cloudflare Worker with auth, email alerts, PDF serving
- `reference/build.js` — Build script embedding HTML + PDF into worker
- `reference/wrangler.toml` — Deployment config

Otherwise, use the card architecture doc as the structural guide.

## Workflow

Execute these phases in order. Present findings to the user after each phase for confirmation before proceeding.

---

### Phase 1: Job Description Analysis

Fetch the job description from the provided URL using WebFetch. If the URL fails, ask the user to paste the JD text. Extract:

- Required skills with experience levels
- Nice-to-have qualifications
- Role level and reporting structure
- Team size and composition hints
- Company-specific priorities and keywords
- Remote/hybrid/onsite requirements
- Compensation signals if available

**Output:** Present extracted requirements as a structured checklist. Ask user to confirm or adjust before proceeding.

---

### Phase 2: Company Research

Use WebSearch and WebFetch to research the target company. Follow the research checklist at `[claude-job-hunter]/resources/research-checklist.md`.

Research areas:
1. **Company Overview** — about page, mission, leadership team
2. **Product & Technology** — what they build, tech stack, API design
3. **Engineering Culture** — blog posts, tech talks, open source
4. **Security Posture** — existing team, compliance certs, security job postings
5. **Business Context** — funding, news, growth, customers, revenue model
6. **Values & Culture** — company values, reviews, culture statements
7. **Competitive Landscape** — competitors, market position

Save findings to `COMPANY_RESEARCH.md` in the output directory.

**Output:** Present a summary of key findings. Ask user to confirm or add context before proceeding.

---

### Phase 3: Fit Analysis

Cross-reference the candidate profile against company research and JD requirements:

1. **Match Score** — Calculate overall percentage (85-97% range typical). Factor in:
   - Direct experience matches (weighted heavily)
   - Transferable skills
   - Cultural alignment signals
   - Level/seniority fit
   - Domain knowledge overlap

2. **SWOT Analysis** — Tailored to THIS company:
   - **Strengths:** Why the candidate is built for THIS specific role
   - **Opportunities:** What the candidate brings beyond the JD for THIS company
   - **Weaknesses:** Honest considerations from candidate profile, tailored to context
   - **Threats:** What could go wrong in THIS specific role/company

3. **Requirements Mapping** — For each JD requirement:
   - Specific evidence from career history
   - Relevance score (percentage)
   - Brief justification

4. **Company-Specific Value Propositions** — "Why [Company] Specifically" showing deep understanding of their business and how candidate's experience maps

5. **Success Metrics** — 30-day, 90-day, 6-month indicators specific to this role

6. **Tailored Questions** — Insightful questions specific to this company (not generic interview questions)

Save full analysis to `PLAYER_CARD.md` in the output directory.

**Output:** Present analysis summary. Get user approval before generating the card.

---

### Phase 4: Card Generation

Create the output directory and generate all project files.

**Output directory:** `[claude-job-hunter]/cards/[company]-[year]/`

#### 4a. `src/index.html`

Generate a complete HTML player card following the structure and CSS design system from the card architecture doc. The card MUST include ALL sections in order:

1. Login screen with company-specific messaging
2. Header with candidate name, subtitle, match score badge, resume download, role target
3. TL;DR section — compelling narrative connecting past experience to this company's needs (NOT a list). Include the "Case in point" callout about building the player card
4. Impact Metrics — large stat numbers most relevant to this company
5. SWOT Analysis — 2x2 grid, startup-focused titles, company-specific content
6. JD Requirements Checklist — two-column layout (core + bonus/beyond)
7. Why [Company] Specifically — demonstrate genuine understanding of their business
8. I Actually Build Things — recent hands-on work + research/patents
9. Career Highlights — vertical timeline
10. Questions I'd Ask You — role + company specific questions
11. Contact — email, LinkedIn, GitHub, location, resume download CTA
12. Footer — generation date and target

**Content guidelines:**
- TL;DR must tell a story, not list facts
- SWOT items must be company-specific, not generic
- "Why [Company]" must demonstrate knowledge that could not apply to any other company
- Questions must be specific enough to show understanding of their challenges
- Use the same CSS custom properties, class names, and responsive patterns
- Keep the client-side password check matching the configured password

#### 4b. `src/worker.js`

Generate Cloudflare Worker with:
- Password auth via HTTP-only cookies (24hr expiry)
- Email alerts via Resend API using `ctx.waitUntil()` (non-blocking)
- Resume PDF serving from base64-embedded data
- `{{HTML_CONTENT}}` and `{{RESUME_PDF_BASE64}}` placeholders for build script
- BASE_PATH as `/player-card`
- Login page rendered server-side with company-specific messaging

#### 4c. `build.js`

Node.js build script (no dependencies beyond Node built-ins):
- Reads `src/index.html` and resume PDF
- Escapes HTML for embedding as template literal
- Replaces `{{HTML_CONTENT}}` and `{{RESUME_PDF_BASE64}}` placeholders
- Outputs: `dist/worker.js` (deployable) and `public/index.html` (preview)

#### 4d. `wrangler.toml`

```toml
name = "[candidate]-playercard-[company]"
main = "dist/worker.js"
compatibility_date = "2024-01-01"

routes = [
  { pattern = "[company].[domain]/player-card*", zone_name = "[domain]" }
]
```

#### 4e. `package.json`

```json
{
  "name": "[candidate]-playercard-[company]",
  "version": "1.0.0",
  "description": "Player Card - [Role] @ [Company]",
  "scripts": {
    "build": "node build.js",
    "deploy": "npm run build && wrangler deploy",
    "dev": "npm run build && wrangler dev",
    "preview": "npm run build && npx serve public"
  },
  "devDependencies": {
    "wrangler": "^4.69.0"
  }
}
```

#### 4f. `README.md`

Deployment instructions specific to this card with password, secrets setup, and Resend email alerts.

#### 4g. `.gitignore`

```
node_modules/
dist/
public/
.wrangler/
.dev.vars
*.log
.DS_Store
```

#### 4h. Resume PDF

Copy from user-specified path or ask for it.

---

### Phase 5: Build & Deploy Setup

After all files are generated:

1. Copy resume PDF to the new project directory
2. Initialize git repo: `git init`
3. Run `npm install` to install wrangler
4. Run `npm run build` to verify the build works
5. Create initial commit
6. Present deployment instructions:
   ```
   cd [output-directory]
   wrangler secret put PASSWORD
   wrangler secret put RESEND_API_KEY
   wrangler secret put ALERT_EMAIL
   npm run deploy
   ```
7. Ask if user wants to create a GitHub repo and push

---

## Quality Checklist

Before finishing, verify:
- [ ] All HTML sections populated with company-specific content
- [ ] No references to other companies leak into the card
- [ ] Match score justified by requirements mapping
- [ ] SWOT contains company-specific items, not generic statements
- [ ] "Why [Company]" demonstrates genuine company knowledge
- [ ] Questions specific to this company
- [ ] `npm run build` succeeds
- [ ] wrangler.toml has correct routing
- [ ] PLAYER_CARD.md contains full analysis
- [ ] COMPANY_RESEARCH.md contains research findings
- [ ] Resume PDF copied and referenced correctly in build.js
