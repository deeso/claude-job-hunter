# Comp Research — Market Compensation Analysis

Research and build an objective compensation picture for a target role. Combines market data, company-specific intel, peer company benchmarks, geography adjustments, and leveling analysis. Run this before you have an offer to know what fair looks like.

## Inputs

**Usage:** `/comp-research "company" "role" "level"`

Parse `$ARGUMENTS` as up to three quoted strings:
- **First quoted string** (required) — Company name (e.g., `"Anthropic"`)
- **Second quoted string** (required) — Role title (e.g., `"Security Engineer"`)
- **Third quoted string** (optional) — Expected level (e.g., `"Senior"`, `"Staff"`, `"L6"`, `"IC4"`)

If company and role are missing, ask. Level can also be determined during analysis.

## Setup

This command expects a `claude-job-hunter` repo cloned locally. Find it by checking (in order):
1. `$CLAUDE_JOB_HUNTER_DIR` environment variable
2. `./claude-job-hunter/` relative to cwd
3. `~/claude-job-hunter/`

If not found, ask the user for the path.

## Existing Context

Before starting, check for existing evaluation materials:
- Look for `[claude-job-hunter]/evaluations/[company]-[role-slug]/`
- If found, read available files: `SCORECARD.md`, `COMPANY_INTEL.md`, `INTERVIEW_LOG.md`
- Use company intel to inform comp philosophy and peer company analysis
- Use interview log for any comp signals dropped during interviews
- If no evaluation exists, that's fine — this skill works standalone

Also read:
- **Candidate profile:** `[claude-job-hunter]/profile/candidate-profile.md`

## Output

Create or update:
```
[claude-job-hunter]/evaluations/[company]-[role-slug]/
└── COMP_RESEARCH.md    # Full compensation analysis with sources
```

## Workflow

### Phase 1: Candidate Comp Context

Ask the candidate:

1. **Current compensation** (all components):
   - Base salary
   - Bonus (target % and actual recent payouts)
   - Equity (type, vesting schedule, current value, remaining unvested)
   - Signing bonus received at current job
   - Other: retirement match, deferred comp, stipends
2. **Geographic details:**
   - Where do you live?
   - Are you willing to relocate? Where?
   - Remote, hybrid, or onsite preference?
   - Is the target role remote, hybrid, or onsite?
3. **Comp priorities** — rank what matters most:
   - Base salary (cash flow, stability)
   - Equity (upside potential, belief in company)
   - Signing bonus (bridge unvested equity)
   - Title/level (career progression signal)
   - Benefits (healthcare, retirement, PTO)
   - Other (learning budget, flexibility, sabbatical, etc.)
4. **Minimum acceptable comp:**
   - What's the floor — below this, you walk away?
   - Is that a hard floor or "it depends"?
5. **Current market activity:**
   - Are you actively interviewing elsewhere?
   - Any other offers on the table or expected?
   - How urgently do you need to move?

**Output:** Confirm understanding of candidate's comp profile before proceeding.

---

### Phase 2: Leveling Analysis

Determine the candidate's likely level at this company.

#### 2a. Company Leveling Research

Use WebSearch and WebFetch to find:
- [ ] Company's leveling framework (if public — many companies publish career ladders)
- [ ] levels.fyi data for this company (level names, comp bands per level)
- [ ] Job posting level indicators (years of experience, scope of role, "senior" vs "staff" vs "principal")
- [ ] LinkedIn profiles of people in similar roles at this company (title patterns)
- [ ] Glassdoor/Blind posts about leveling at this company
- [ ] H1B/PERM salary data (LCA filings are public — search for company + role)

#### 2b. Level Mapping

Map the candidate's experience to the company's framework:

| Signal | Candidate | Level Implication |
|--------|-----------|-------------------|
| Years of experience | X years | → likely [level] |
| Scope of past roles | individual/team/org | → likely [level] |
| Current title | [title] | → maps to [level] |
| Technical depth | [assessment] | → likely [level] |
| Leadership experience | [assessment] | → likely [level] |

**Predicted level:** [level] with confidence (high/medium/low)
**Level risk:** Could they try to down-level? Why? (common tactic — watch for it)

#### Suggested Searches
1. `"[COMPANY]" levels career ladder framework`
2. `"[COMPANY]" "[ROLE]" site:levels.fyi`
3. `"[COMPANY]" "[ROLE]" salary site:glassdoor.com`
4. `"[COMPANY]" "[ROLE]" LCA H1B salary`
5. `"[COMPANY]" leveling site:blind`
6. `"[COMPANY]" "[ROLE]" site:linkedin.com` (check titles of current employees)

**Output:** Present leveling analysis. Confirm with candidate.

---

### Phase 3: Market Comp Research

Build a comprehensive picture of what this role pays.

#### 3a. Target Company Comp Data

Search for:
- [ ] levels.fyi entries for this company and level
- [ ] Glassdoor salary reports for this role at this company
- [ ] H1B/LCA/PERM filings (public data — prevailing wage and actual wage)
- [ ] Job posting comp ranges (many states require posting salary ranges)
- [ ] Blind salary sharing threads for this company
- [ ] Company compensation philosophy (if stated — e.g., "we pay at 75th percentile")
- [ ] Equity structure: options vs RSUs, vesting schedule, refresh grants, cliff
- [ ] Recent fundraising or IPO status (affects equity value and comp budgets)
- [ ] Benefits package highlights from careers page or Glassdoor

#### 3b. Peer Company Benchmarks

Identify 5-8 peer companies — companies that compete for the same talent:
- Direct competitors (same product space)
- Similar stage companies (same funding round, employee count)
- Companies hiring for the same skill set
- Companies in the same geography (if location-adjusted)

For each peer, search for:
- [ ] levels.fyi data for equivalent role and level
- [ ] Known comp ranges from job postings
- [ ] Equity type and stage (pre-IPO options vs public RSUs)

Build a comparison table:

| Company | Stage | Base Range | Equity Range | Total Comp Range | Source |
|---------|-------|-----------|-------------|-----------------|--------|
| [target] | ... | ... | ... | ... | ... |
| [peer 1] | ... | ... | ... | ... | ... |
| ... | ... | ... | ... | ... | ... |

#### 3c. Geography Adjustment

- [ ] Cost of living comparison: candidate's location vs. company's base location
- [ ] Does the company adjust pay by geography? (many do for remote roles)
- [ ] What's the typical remote discount for this company? (search Blind/Glassdoor)
- [ ] If relocating: cost of living delta and its impact on net comp
- [ ] State income tax implications (e.g., CA vs TX vs WA)

#### 3d. Market Conditions

- [ ] Current hiring market for this role: hot, normal, or cold?
- [ ] Are companies in this space doing layoffs or aggressive hiring?
- [ ] How long has this role been open? (longer = more desperate = more leverage)
- [ ] How many similar roles is this company hiring for? (volume = less negotiation room)
- [ ] Macro conditions: interest rates, tech sector health, VC funding climate

#### Suggested Searches
1. `"[COMPANY]" "[ROLE]" "[LEVEL]" compensation site:levels.fyi`
2. `"[COMPANY]" "[ROLE]" salary site:glassdoor.com`
3. `"[COMPANY]" "[ROLE]" LCA H1B site:h1bdata.info`
4. `"[COMPANY]" compensation philosophy pay`
5. `"[COMPANY]" equity RSU options vesting`
6. `"[COMPANY]" benefits perks`
7. `"[COMPANY]" remote salary adjustment`
8. `"[PEER]" "[ROLE]" compensation site:levels.fyi` (for each peer)
9. `"[ROLE]" "[LEVEL]" hiring market 2026`
10. `"[COMPANY]" "[ROLE]" job posting salary range`

**Output:** Present market comp summary. Confirm with candidate.

---

### Phase 4: Comp Analysis & Bands

Synthesize all research into an actionable analysis.

#### A. Compensation Bands

Based on all data, establish three bands:

**Below Market (bottom 25th percentile)**
- Base: $X - $Y
- Equity: $X - $Y/yr
- Total: $X - $Y
- *This is what a lowball looks like — push back if offered here*

**Market Rate (25th - 75th percentile)**
- Base: $X - $Y
- Equity: $X - $Y/yr
- Total: $X - $Y
- *Fair offer — acceptable but room to negotiate*

**Above Market (75th+ percentile)**
- Base: $X - $Y
- Equity: $X - $Y/yr
- Total: $X - $Y
- *Strong offer — focus negotiation on other terms*

**Stretch Target (what to aim for)**
- Base: $X
- Equity: $X/yr
- Total: $X
- *Realistic ceiling given company stage, your leverage, and market conditions*

#### B. Component Breakdown

For each comp component, provide specific context:

**Base Salary**
- Market range for this role/level/geo: $X - $Y
- Company's likely range based on data: $X - $Y
- Candidate's current base: $X (delta: +/-$X)
- Tax implications if relocating

**Equity**
- Type at this company: options / RSUs / phantom / none
- Typical grant for this level: $X over 4 years
- Vesting schedule: cliff? monthly? quarterly?
- If options: current valuation, strike price signals, liquidation preferences
- If pre-IPO: realistic value assessment (not just paper value)
- Candidate's unvested equity at current employer: $X (needs bridging via signing bonus?)

**Signing Bonus**
- Typical range at this company/level: $X - $Y
- Justification angles: unvested equity bridge, relocation, opportunity cost
- Clawback terms to watch for

**Bonus**
- Target percentage at this level: X%
- Actual payout history (if findable): do they pay at target?
- Pro-rated first year considerations

**Benefits & Perks**
- Notable benefits that have real dollar value (healthcare, retirement match, etc.)
- Benefits gaps compared to current employer
- Hidden value: PTO policy, sabbatical, learning budget, home office stipend

#### C. Comp vs. Current Situation

| Component | Current | Market Rate | Stretch Target | Delta |
|-----------|---------|------------|----------------|-------|
| Base | $X | $X | $X | +/-$X |
| Equity/yr | $X | $X | $X | +/-$X |
| Bonus | $X | $X | $X | +/-$X |
| Signing | — | $X | $X | — |
| Benefits value | $X | $X | $X | +/-$X |
| **Total** | **$X** | **$X** | **$X** | **+/-$X** |

#### D. Comp Signals from Interviews

If `INTERVIEW_LOG.md` exists, extract any comp-relevant signals:
- Did anyone mention comp ranges, bands, or philosophy?
- Did they ask about comp expectations? What was discussed?
- Any signals about flexibility or constraints?
- Did they mention budget, headcount, or approval processes?

#### E. Company Negotiation Intelligence

Research how this company negotiates:
- [ ] Blind/Glassdoor posts about negotiation experience at this company
- [ ] Are they known for aggressive first offers or fair ones?
- [ ] Do they negotiate on base, equity, signing, or all three?
- [ ] Is there a formal comp committee or does the hiring manager have authority?
- [ ] Do they match competing offers?
- [ ] Any known "we don't negotiate" policies?
- [ ] Typical time pressure tactics (exploding offers, deadlines)

**Output:** Present full comp analysis. Ask candidate to confirm or correct any data points.

---

### Phase 5: Write Output

Generate `COMP_RESEARCH.md` with all sections:

```markdown
# Compensation Research: [Role] @ [Company]
**Date:** [date]
**Predicted Level:** [level]
**Geography:** [candidate location] → [role location/remote]

## Executive Summary
- Market total comp range: $X - $Y
- Recommended target: $X
- Minimum acceptable (candidate-set): $X

## Leveling Analysis
(from Phase 2)

## Market Data
### Target Company
(data with sources)

### Peer Company Benchmarks
(comparison table with sources)

### Geography Adjustment
(cost of living, tax implications)

### Market Conditions
(hiring climate, leverage factors)

## Compensation Bands
(below market / market / above market / stretch)

## Component Breakdown
(base, equity, signing, bonus, benefits)

## Current vs. Target Comparison
(comparison table)

## Interview Comp Signals
(if available)

## Company Negotiation Intel
(how they negotiate, what to expect)

## Sources
(all URLs and data sources used)
```

**Output:** Present summary to the candidate. Remind them that this research feeds directly into `/offer-negotiation` when an offer arrives.

---

## Quality Checklist

Before finishing, verify:
- [ ] All comp data has sources (levels.fyi links, Glassdoor, H1B data, job postings)
- [ ] Peer companies are genuinely comparable (stage, size, talent competition)
- [ ] Geography adjustment accounts for remote policy specifics, not just COL
- [ ] Equity analysis considers realistic value, not just grant size (options vs RSUs, stage)
- [ ] Leveling analysis accounts for down-leveling risk
- [ ] Bands are evidence-based, not guesses
- [ ] Current comp comparison is complete (all components, not just base)
- [ ] Market conditions are current (2026 data, not stale)
- [ ] Company negotiation intel is specific, not generic advice
- [ ] Tax implications noted where relevant (state taxes, equity tax treatment)
