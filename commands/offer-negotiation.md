# Offer Negotiation — Evaluate, Strategize, Respond

You have an offer. This skill helps you decide whether to accept, negotiate, or walk away — then builds the actual negotiation strategy with scripts, counter-offer framing, and decision frameworks grounded in your priorities and leverage.

## Inputs

**Usage:** `/offer-negotiation "company" "role"`

Parse `$ARGUMENTS` as two quoted strings:
- **First quoted string** (required) — Company name (e.g., `"Anthropic"`)
- **Second quoted string** (required) — Role title (e.g., `"Security Engineer"`)

If either is missing, ask.

## Setup

This command expects a `claude-job-hunter` repo cloned locally. Find it by checking (in order):
1. `$CLAUDE_JOB_HUNTER_DIR` environment variable
2. `./claude-job-hunter/` relative to cwd
3. `~/claude-job-hunter/`

If not found, ask the user for the path.

## Existing Context

This skill is most powerful when prior skills have been run. Before starting, check for:
- `[claude-job-hunter]/evaluations/[company]-[role-slug]/COMP_RESEARCH.md` — market data and bands
- `[claude-job-hunter]/evaluations/[company]-[role-slug]/SCORECARD.md` — how much they want this role
- `[claude-job-hunter]/evaluations/[company]-[role-slug]/INTERVIEW_LOG.md` — how interviews went, signals
- `[claude-job-hunter]/evaluations/[company]-[role-slug]/COMPANY_INTEL.md` — company context
- `[claude-job-hunter]/profile/candidate-profile.md` — candidate background

Read whatever exists. If `COMP_RESEARCH.md` doesn't exist, recommend running `/comp-research` first but proceed anyway — gather essential market data inline.

## Output

Create or update:
```
[claude-job-hunter]/evaluations/[company]-[role-slug]/
└── NEGOTIATION_STRATEGY.md    # Offer analysis, strategy, scripts, decision framework
```

## Workflow

### Phase 1: Capture the Offer

Ask the candidate to share every detail of the offer. Be thorough — every component matters.

1. **Base salary:** Amount, pay frequency
2. **Equity:**
   - Type: stock options, RSUs, phantom equity, profit sharing, none
   - Grant size: number of shares or dollar value
   - Vesting schedule: cliff, total period, monthly/quarterly/annual
   - If options: strike price, current 409A/FMV, exercise window post-departure
   - If RSUs: current stock price, public or private
   - Refresh grant policy mentioned?
3. **Signing bonus:** Amount, clawback terms (how long must you stay?)
4. **Annual bonus:** Target percentage, guaranteed first year or pro-rated?
5. **Level/title:** What level did they offer? Does it match expectations?
6. **Start date:** When do they want you? Any flexibility?
7. **Benefits highlights:** Healthcare, retirement match, PTO, any standouts
8. **Other terms:** Relocation package, remote work policy, non-compete, IP assignment
9. **Deadline:** When do you need to respond? Is it an exploding offer?
10. **How was it delivered?** Verbal, email, formal letter? Who delivered it?

Also ask:
11. **Your initial reaction:** Gut feeling — excited, disappointed, mixed?
12. **Compared to expectations:** Better, worse, or about what you expected?
13. **Compared to current comp:** Better, lateral, or a step back?

**Output:** Confirm you have the full offer details. Flag anything missing.

---

### Phase 2: Emotional Check-In

Before strategy, ground in sentiment. This is often the most valuable part.

Ask the candidate honestly:

1. **Do you actually want this job?**
   - Scale of 1-10, how excited are you about the ROLE (not the comp)?
   - If the comp were perfect, would you be thrilled to start?
   - What specifically excites you? What gives you pause?

2. **What's your alternative?**
   - Are you currently employed? Happy there?
   - Other offers on the table? Expected soon?
   - How painful is it to stay in your current situation?
   - Could you keep interviewing if you decline?

3. **What would make you say yes immediately?**
   - Is there a number where you'd sign tonight?
   - Is there a non-comp term that would seal it? (title, scope, team, etc.)

4. **What would make you walk away?**
   - Is there a floor below which no amount of "great opportunity" matters?
   - Any non-negotiable terms? (remote, title, reporting structure, etc.)

5. **How do you feel about negotiating?**
   - Comfortable, anxious, experienced, dreading it?
   - Any past negotiation experiences (good or bad) that shape your approach?
   - How aggressive do you want to be?

**Synthesize the candidate's negotiation posture:**
- **Leverage level:** Strong (multiple offers, happy where they are) / Moderate (good position but want to move) / Weak (need this job, no alternatives)
- **Risk tolerance:** Will push hard / Wants to push but fears losing offer / Wants to accept and move on
- **Priority stack:** What matters most — base, equity, title, signing, other?

**Output:** Reflect the sentiment back. Make sure the candidate feels heard before jumping into strategy.

---

### Phase 3: Offer Analysis

#### A. Offer vs. Market

If `COMP_RESEARCH.md` exists, compare directly. Otherwise, do quick market research.

| Component | Offered | Below Market | Market Rate | Above Market | Assessment |
|-----------|---------|-------------|------------|-------------|------------|
| Base | $X | $X | $X-$Y | $X+ | Below/At/Above |
| Equity/yr | $X | $X | $X-$Y | $X+ | Below/At/Above |
| Signing | $X | $X | $X-$Y | $X+ | Below/At/Above |
| Bonus target | X% | X% | X-Y% | X%+ | Below/At/Above |
| **Total Y1** | **$X** | **$X** | **$X-$Y** | **$X+** | **Below/At/Above** |
| **Total Y2-4 avg** | **$X** | **$X** | **$X-$Y** | **$X+** | **Below/At/Above** |

**Year 1 vs. Year 2+ distinction:** Year 1 includes signing bonus. Years 2-4 show the steady-state comp. Both matter — don't let a big signing bonus mask a below-market base.

#### B. Offer vs. Current Comp

| Component | Current | Offered | Delta | % Change |
|-----------|---------|---------|-------|----------|
| Base | $X | $X | +/-$X | +/-X% |
| Equity/yr | $X | $X | +/-$X | +/-X% |
| Bonus | $X | $X | +/-$X | +/-X% |
| Signing (amortized Y1) | — | $X | +$X | — |
| Benefits delta | — | +/-$X | +/-$X | — |
| **Total Y1** | **$X** | **$X** | **+/-$X** | **+/-X%** |

**Unvested equity bridge:** Calculate what the candidate leaves on the table by departing (unvested equity, unvested signing bonus clawback, etc.). The signing bonus should ideally cover this.

#### C. Offer vs. Other Offers

If the candidate has other offers:

| Component | [This Company] | [Other Company 1] | [Other Company 2] |
|-----------|---------------|-------------------|-------------------|
| Base | $X | $X | $X |
| Equity/yr | $X | $X | $X |
| Total Y1 | $X | $X | $X |
| Role excitement | X/10 | X/10 | X/10 |
| Growth potential | assessment | assessment | assessment |

#### D. Leveling Check

- Does the offered level match the candidate's experience?
- Were they down-leveled? (common tactic — significantly impacts long-term comp)
- If down-leveled: what's the comp delta between this level and the correct level?
- Is it worth fighting for a higher level vs. taking the lower level with a higher comp?

#### E. Hidden Comp Factors

Things candidates often miss:
- **Equity tax treatment:** ISO vs NSO, RSU tax at vest, 83(b) election opportunity
- **Exercise window:** If options, how long after leaving to exercise? (90 days = bad, 10 years = good)
- **Acceleration on change of control:** Do options/RSUs vest on acquisition?
- **Clawback terms:** Signing bonus repayment schedule
- **Non-compete enforceability:** Does it limit future earning potential?
- **PTO policy:** Unlimited (often means less) vs. accrued (has cash value)
- **Healthcare cost delta:** Employer contribution differences can be $5-15K/year
- **401k match:** Immediate vesting or graded? Match cap?

---

### Phase 4: Negotiation Strategy

Based on analysis and candidate sentiment, build the strategy.

#### A. Verdict: Accept / Negotiate / Walk Away

**Recommendation:** [Accept as-is / Negotiate specific components / Walk away]

**Reasoning:**
- How the offer compares to market (data-driven)
- Candidate's stated priorities and leverage
- Company's likely flexibility (from negotiation intel)
- Risk/reward of negotiating vs. accepting

#### B. What to Negotiate (Priority Order)

For each component worth negotiating, provide:

**1. [Component] — Push from $X to $Y**
- **Why this is achievable:** Evidence that the company has room here
- **Justification framing:** How to frame the ask (market data, competing offers, unvested equity bridge)
- **Risk level:** Low (standard) / Medium (company might push back) / High (could irritate)
- **Fallback position:** If they can't do $Y, would you take $Z?

**2. [Component] — ...**
(repeat for each component)

**Items NOT worth negotiating** (and why):
- Components where the offer is already at or above market
- Components where the company is known to be inflexible
- Low-value items that could burn goodwill

#### C. Negotiation Scripts

Provide actual language the candidate can use. Three scenarios:

**Email/Written Counter (recommended for most situations):**
```
Subject: [Role] Offer — Excited to discuss a few details

[Name],

Thank you for the offer — I'm genuinely excited about [specific thing about the role/team].
[Reference a specific interview moment that was compelling.]

I've reviewed the package and would love to discuss a few components:

[Component 1]: Based on [justification — market data, competing offer, current comp],
I'd like to explore [specific ask]. [Why this is reasonable.]

[Component 2]: [Same structure.]

I'm committed to making this work and believe these adjustments reflect
[market rates / the value I'd bring / what I'd be leaving behind]. Happy to discuss
on a call if that's easier.

Looking forward to it,
[Name]
```

**Phone Script (if recruiter prefers verbal):**
- Opening: Express genuine excitement, reference specifics
- Transition: "I've had a chance to review the package and wanted to discuss a few things"
- Each ask: State the number, give the justification, pause (let them respond)
- Close: "I'm excited about this and want to make it work. What can you do?"

**If They Push Back:**
- "I understand there are constraints. Here's what I'm trying to solve for: [specific need]"
- "Would you be able to [alternative]? For example, [lower base but higher equity / signing bonus / accelerated review]?"
- "What is the maximum flexibility you have on [component]?"
- "I have [competing offer / unvested equity I'd leave behind]. I want to make this work but need to close that gap."

#### D. Timing & Tactics

- **When to respond:** Don't respond same day. Take 24-48 hours minimum. If they pressure, say "I want to give this the consideration it deserves."
- **Deadline management:** If exploding offer, ask for extension: "I'm very interested and want to make a thoughtful decision. Could I have until [date]?"
- **Who to negotiate with:** Recruiter (they expect it, less personal) vs. hiring manager (more authority but can affect relationship)
- **Multiple offers:** How to leverage without bluffing or burning bridges
- **Escalation:** If recruiter says no, when to ask "is there anyone else I could discuss this with?"
- **Accepting:** When the negotiation is done, accept gracefully and enthusiastically

#### E. Company-Specific Negotiation Notes

From `COMP_RESEARCH.md` company negotiation intel (or researched inline):
- Known flexibility patterns at this company
- What other candidates have reported about negotiating here
- Red flags to watch for (rescinding offers, lowball anchoring, etc.)
- Things that tend to work vs. things that don't at this company

---

### Phase 5: Decision Framework

Help the candidate make the final call.

#### A. The Spreadsheet View

| Factor | Weight (candidate-set) | This Offer | Score |
|--------|----------------------|------------|-------|
| Base salary | X% | [assessment] | X/10 |
| Equity upside | X% | [assessment] | X/10 |
| Role excitement | X% | [assessment] | X/10 |
| Growth potential | X% | [assessment] | X/10 |
| Culture fit | X% | [assessment] | X/10 |
| Work-life balance | X% | [assessment] | X/10 |
| Career trajectory | X% | [assessment] | X/10 |
| Geographic fit | X% | [assessment] | X/10 |
| **Weighted Total** | **100%** | | **X/10** |

(If multiple offers, add columns for each.)

#### B. The Gut Check

Beyond the numbers:
- "If a friend had this exact offer and asked your advice, what would you tell them?"
- "Imagine it's 6 months from now and you took this job. Are you smiling or regretting?"
- "What's the story you tell people about why you took this job?"
- "If they came back with exactly what you asked for, are you in? If you hesitate, the comp isn't the real issue."

#### C. Scenarios

**If you accept today:**
- Year 1 total comp: $X
- What you gain: [specific to candidate's priorities]
- What you give up: [other options, current role benefits]

**If you negotiate and they meet you:**
- Year 1 total comp: $X (delta: +$X)
- Probability they meet you: X% (based on company intel)

**If you negotiate and they hold firm:**
- You're back to the original offer
- Risk level: [Low — most companies don't rescind / Medium — some signals suggest pushback / High — avoid for this company]

**If you walk away:**
- What's your plan B?
- Timeline to find something comparable?
- Cost of waiting (financial, emotional, career momentum)

---

### Phase 6: Write Output

Generate `NEGOTIATION_STRATEGY.md`:

```markdown
# Offer Negotiation: [Role] @ [Company]
**Date:** [date]
**Offer Level:** [level]
**Response Deadline:** [date]

## The Offer
(complete offer details)

## Verdict: [Accept / Negotiate / Walk Away]
(recommendation with reasoning)

## Offer Analysis
### vs. Market
(comparison table)

### vs. Current Comp
(comparison table)

### vs. Other Offers
(if applicable)

### Hidden Factors
(equity tax, clawback, non-compete, benefits delta)

## Negotiation Strategy
### Priority 1: [Component]
(target, justification, risk, fallback)

### Priority 2: [Component]
...

### Items Not Worth Negotiating
...

## Scripts
### Written Counter
(ready-to-send email)

### Phone Script
(talking points)

### Pushback Responses
(if they say no)

## Timing
- Respond by: [date]
- How to ask for extension: [script]

## Decision Framework
### Weighted Scorecard
(factor table)

### Scenarios
(accept / negotiate-win / negotiate-hold / walk)

## Emotional Check
(sentiment summary and gut check questions)
```

**Output:** Walk the candidate through the strategy. Ask what resonates, what feels off. Adjust the approach based on their reaction. This is a conversation, not a report drop.

---

## Quality Checklist

Before finishing, verify:
- [ ] Every offer component is captured (base, equity, signing, bonus, benefits, clawback, non-compete)
- [ ] Offer is compared against real market data with sources (not vibes)
- [ ] Candidate's emotional state and priorities are reflected in the strategy (not just numbers)
- [ ] Negotiation scripts use specific numbers and justifications, not placeholders
- [ ] Pushback responses are prepared for each ask
- [ ] Decision framework weights reflect what the CANDIDATE cares about, not defaults
- [ ] Equity analysis considers realistic value (especially for pre-IPO options)
- [ ] Unvested equity bridge is calculated if applicable
- [ ] Year 1 vs. steady-state comp distinction is clear (signing bonus inflates Y1)
- [ ] Risk assessment is honest — when NOT to negotiate is as important as when to push
- [ ] Down-leveling risk is addressed if relevant
- [ ] Tax implications are noted where material (state taxes, equity tax treatment)
- [ ] Deadline management strategy is included
- [ ] The tone is supportive, not pushy — the candidate makes the call
