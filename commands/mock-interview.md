# Mock Interview — Practice Interview Loops with AI Feedback

Simulate a realistic interview loop for a target company and role. The agent builds a panel of likely interviewers, each with researched questions tailored to the company's tech stack, values, and interview style. You answer in writing, and the agent provides critical and constructive feedback on content, communication style, and areas to practice.

## Inputs

**Usage:** `/mock-interview "company" "role" "round-type"`

Parse `$ARGUMENTS` as up to three quoted strings:
- **First quoted string** (required) — Company name (e.g., `"Anthropic"`)
- **Second quoted string** (required) — Role title (e.g., `"Security Engineer"`)
- **Third quoted string** (optional) — Specific round to practice (see Round Types below)

If company and role are missing, ask. If round type is omitted, run the full loop.

**Round Types:**
- `"phone-screen"` — recruiter or hiring manager screen
- `"technical"` — hands-on technical questions, system design, coding
- `"behavioral"` — leadership principles, culture fit, STAR-format questions
- `"hiring-manager"` — role scope, team fit, management style alignment
- `"cross-functional"` — working with other teams, communication, influence
- `"executive"` — VP/C-level strategic conversation
- `"full-loop"` (default) — all rounds in sequence

## Setup

This command expects a `claude-job-hunter` repo cloned locally. Find it by checking (in order):
1. `$CLAUDE_JOB_HUNTER_DIR` environment variable
2. `./claude-job-hunter/` relative to cwd
3. `~/claude-job-hunter/`

If not found, ask the user for the path.

## Existing Context

Before building the mock, check for existing evaluation materials:
- `[claude-job-hunter]/evaluations/[company]-[role-slug]/INTERVIEW_PREP.md` — predicted panel and questions
- `[claude-job-hunter]/evaluations/[company]-[role-slug]/COMPANY_INTEL.md` — company research
- `[claude-job-hunter]/evaluations/[company]-[role-slug]/SCORECARD.md` — fit analysis, SWOT
- `[claude-job-hunter]/evaluations/[company]-[role-slug]/INTERVIEW_LOG.md` — prior real interviews
- `[claude-job-hunter]/profile/candidate-profile.md` — candidate background

If `INTERVIEW_PREP.md` exists, use its predicted panel and questions as the foundation — but expand and add depth. If `INTERVIEW_LOG.md` exists with prior rounds, use it to avoid repeating questions already asked in real interviews and to focus on identified weak areas.

## Output

Create or update:
```
[claude-job-hunter]/evaluations/[company]-[role-slug]/
└── MOCK_INTERVIEW.md    # Full mock session with questions, answers, and feedback
```

## Workflow

### Phase 1: Research & Panel Construction

#### 1a. Company Interview Style Research

Use WebSearch and WebFetch to understand how this company interviews:

- [ ] Glassdoor interview reviews for this role (reported process, difficulty, sample questions)
- [ ] Blind posts about interview experience at this company
- [ ] Company blog posts or talks about their hiring philosophy
- [ ] Known interview frameworks (e.g., Amazon's Leadership Principles, Google's Googleyness)
- [ ] Interview difficulty level (Glassdoor rating)
- [ ] Typical number of rounds and format
- [ ] Tech stack and tools candidates are tested on
- [ ] Company values or leadership principles used as evaluation criteria

#### Suggested Searches
1. `"[COMPANY]" "[ROLE]" interview questions glassdoor`
2. `"[COMPANY]" interview process experience`
3. `"[COMPANY]" hiring philosophy engineering`
4. `"[COMPANY]" interview questions site:blind`
5. `"[COMPANY]" leadership principles values`
6. `"[COMPANY]" tech stack engineering blog`
7. `"[COMPANY]" "[ROLE]" interview site:reddit.com`
8. `"[COMPANY]" system design interview`

#### 1b. Build the Interview Panel

Create a realistic panel of 4-6 interviewers. For each:

| Interviewer | Title | Round Type | Duration | Focus Areas |
|-------------|-------|-----------|----------|-------------|
| [Name/Persona] | [Title] | [Type] | [Minutes] | [What they evaluate] |

**Base interviewers on real research:**
- If specific interviewers are identified from LinkedIn or `INTERVIEW_PREP.md`, use their real backgrounds
- If not, create realistic personas based on the company's org structure and typical panel composition
- Each interviewer should have a distinct personality and evaluation focus

For each interviewer, define:
- **Background:** Where they came from, what they've built, years at the company
- **Interview style:** Direct, conversational, Socratic, stress-test, collaborative
- **What they care about:** The specific signals they're evaluating
- **Difficulty level:** Easy-going, moderate, tough, adversarial
- **Known preferences:** Based on their background (e.g., a former Amazon person might lean on Leadership Principles)

#### 1c. Build Question Sets

For each interviewer, create 4-6 questions that they would realistically ask. Questions must be:
- **Researched:** Based on Glassdoor reports, company values, tech stack, and interview style
- **Specific:** Tailored to this company's domain, not generic ("Tell me about yourself" is only appropriate for a phone screen opener)
- **Leveled:** Appropriate difficulty for the target role level
- **Diverse:** Mix of question types within each round

**Question categories to draw from:**

**Technical:**
- System design relevant to the company's product (e.g., "Design a rate-limiting system" for an API company)
- Debugging scenarios using the company's tech stack
- Architecture trade-offs relevant to their scale
- Security-specific scenarios (if security role)
- Code review or implementation questions
- On-call and incident response scenarios

**Behavioral (STAR format):**
- Mapped to company values/leadership principles
- Conflict resolution in technical contexts
- Failure and learning stories
- Cross-functional influence without authority
- Prioritization under pressure
- Dealing with ambiguity

**Culture & Values:**
- Questions that probe alignment with specific company values
- "How do you think about [principle]?" style questions
- Ethical dilemmas relevant to the company's domain
- Work style and collaboration preferences

**Role-Specific:**
- "First 90 days" planning questions
- Stakeholder management scenarios
- Resource allocation and prioritization
- Scaling a function or team
- Handling disagreement with leadership

For each question, prepare (internally, not shown to candidate yet):
- **What good looks like:** Specific elements of a strong answer
- **What great looks like:** Elements that differentiate from good
- **Common pitfalls:** Typical weak responses
- **Follow-up probes:** Questions to dig deeper based on the answer
- **Evaluation criteria:** What signals this question is designed to test

**Output:** Present the panel overview and round structure. Ask the candidate:
- Which rounds they want to practice (or all)
- Any specific areas they feel weak in and want to focus on
- Any rounds they've already completed in real interviews (skip or adjust)
- How tough they want the feedback (supportive, balanced, or brutally honest)

---

### Phase 2: Run the Mock Interview

Execute each round one at a time. For each round:

#### 2a. Set the Scene

Before the first question, briefly set context:

```
--- ROUND [N]: [Type] with [Interviewer Name], [Title] ---
Duration: [X] minutes
Style: [description of this interviewer's approach]
They're evaluating: [what they care about]

[Brief scene-setting: "You've just joined a video call. After brief introductions,
they get started..."]
```

#### 2b. Ask Questions One at a Time

Present ONE question at a time. Wait for the candidate's written response before proceeding.

For each question:
1. Present the question as the interviewer would ask it (in their voice/style)
2. If it's a multi-part or follow-up scenario, reveal parts progressively
3. Wait for the candidate's written answer
4. Based on the answer, ask 1-2 realistic follow-up probes (as the interviewer would)
5. Wait for follow-up responses
6. Move to the next question

**Important interaction rules:**
- Stay in character as the interviewer during the question flow
- Ask follow-ups that a real interviewer would ask based on the specific answer given
- If the candidate's answer is vague, probe for specifics (as a real interviewer would)
- If the candidate goes deep on something interesting, follow that thread
- Don't break character to give feedback mid-round — save all feedback for Phase 3
- Between questions, a brief transition is fine: "[Interviewer] nods and moves on..."

#### 2c. End of Round

After all questions in a round:
```
--- END OF ROUND [N] ---
[Interviewer] thanks you for your time.
```

Then proceed to Phase 3 feedback for this round before starting the next round.

---

### Phase 3: Round Feedback

After each round, provide detailed feedback. This is where the value is.

#### A. Question-by-Question Review

For each question and answer:

**Question:** [the question]

**Your Answer Summary:** [brief summary of what they said]

**Content Assessment:** [Strong / Adequate / Needs Work]
- What worked well in your answer
- What was missing or could be stronger
- Specific evidence or examples you could have included from your experience (reference candidate profile)

**Communication Assessment:**
- **Structure:** Was the answer organized? Did it follow a clear framework (STAR, situation→action→result)?
- **Conciseness:** Right length, too rambling, or too terse? What to cut, what to expand?
- **Specificity:** Did you use concrete numbers, metrics, and specifics? Or was it abstract?
- **Confidence signals:** Did the language convey authority and ownership? Watch for hedging ("I think maybe...", "we sort of...") vs. confidence ("I built...", "I decided...")
- **Storytelling:** Did the answer tell a compelling narrative or just list facts?

**Stronger Answer Framework:**
Provide a rewritten version or outline showing what a top-tier answer looks like, using the candidate's actual experience from their profile. Not a script to memorize — a structural model.

**Follow-Up Performance:**
- How well did they handle the probing questions?
- Did they stay composed or get flustered?
- Did they go deeper with useful detail or repeat themselves?

#### B. Round Summary

**Overall round performance:** [Strong Pass / Pass / Borderline / Likely No Hire]

**Strongest moments:** Top 2-3 answers or moments that would impress this interviewer

**Weakest moments:** Top 2-3 areas where the interviewer would have concerns

**Interviewer's likely assessment:** Based on what this type of interviewer typically looks for, how would they score you? What would they write in their feedback?

**Communication patterns observed:**
- Recurring strengths (e.g., "consistently uses metrics to quantify impact")
- Recurring weaknesses (e.g., "tends to describe team work as 'we' — use more 'I' to show individual contribution")
- Style adjustments for this interviewer type

---

### Phase 4: Loop Summary (after all rounds)

After all rounds are complete, provide an overall assessment.

#### A. Overall Verdict

**Hire recommendation:** [Strong Hire / Hire / Lean Hire / Lean No Hire / No Hire]

**Reasoning:** What the panel as a whole would conclude based on across-round signals.

#### B. Skills Heat Map

| Skill Area | Assessment | Evidence From Mock | Priority to Improve |
|-----------|------------|-------------------|-------------------|
| Technical depth | Strong/Adequate/Weak | [which questions showed this] | High/Medium/Low |
| System design | Strong/Adequate/Weak | ... | ... |
| Behavioral/STAR | Strong/Adequate/Weak | ... | ... |
| Communication clarity | Strong/Adequate/Weak | ... | ... |
| Culture fit articulation | Strong/Adequate/Weak | ... | ... |
| Leadership signals | Strong/Adequate/Weak | ... | ... |
| Handling ambiguity | Strong/Adequate/Weak | ... | ... |
| Technical communication | Strong/Adequate/Weak | ... | ... |

#### C. Communication Style Report

**Overall communication assessment:**
- Default answer length: too short / about right / too long
- Structure tendency: well-organized / somewhat organized / stream of consciousness
- Specificity level: concrete with metrics / mostly specific / tends toward vague
- Confidence level: projects authority / neutral / undermines with hedging language
- Storytelling ability: compelling narratives / factual but dry / disorganized

**Top 3 communication habits to build:**
1. [Specific habit with example from the mock]
2. ...
3. ...

**Top 3 communication habits to break:**
1. [Specific habit with example from the mock and how to fix it]
2. ...
3. ...

**Word and phrase audit:**
- Filler words or phrases used frequently (count occurrences)
- Hedging language that undercuts confidence
- Strong phrases that worked well — use these more

#### D. Practice Plan

Based on the mock results, provide a prioritized practice plan:

**High Priority (practice before real interviews):**
1. [Skill/topic] — Why it matters, how to practice, what good looks like
2. ...

**Medium Priority (would improve your performance):**
1. ...

**Low Priority (nice to have):**
1. ...

**Specific stories to prepare:**
For each weak area, identify 2-3 stories from the candidate's experience (via candidate profile) that they should have ready. Provide the story outline:
- Situation (1-2 sentences)
- Action YOU took (2-3 sentences, specific and individual)
- Result (quantified outcome)
- Learning (what you'd do differently or what it taught you)

**Recommended practice schedule:**
- Which rounds to re-run (use `/mock-interview "[company]" "[role]" "[round-type]"`)
- External resources for technical practice (if applicable)
- Topics to research more deeply

---

### Phase 5: Write Output

Generate `MOCK_INTERVIEW.md`:

```markdown
# Mock Interview: [Role] @ [Company]
**Date:** [date]
**Rounds practiced:** [list]
**Feedback level:** [supportive/balanced/brutal]
**Overall verdict:** [Strong Hire → No Hire]

## Panel
| Interviewer | Title | Round | Focus |
|---|---|---|---|

## Round 1: [Type] — [Interviewer]
### Q1: [Question]
**Your answer:** [summary]
**Assessment:** [Strong/Adequate/Needs Work]
**Content feedback:** ...
**Communication feedback:** ...
**Stronger answer framework:** ...

(repeat for each question)

### Round 1 Summary
- Performance: [verdict]
- Strongest moments: ...
- Weakest moments: ...
- Interviewer's likely assessment: ...

(repeat for each round)

## Loop Summary
### Overall Verdict: [verdict]
### Skills Heat Map
(table)

### Communication Style Report
(assessment + habits to build/break + word audit)

### Practice Plan
(prioritized with stories to prepare)
```

If `MOCK_INTERVIEW.md` already exists from a prior session, append the new session as a dated section and add a **Progress Tracking** section at the top comparing performance across sessions.

**Output:** Present the loop summary to the candidate. Ask if they want to:
- Re-run any specific round
- Practice specific question types
- Get more detailed feedback on any area
- Adjust difficulty level up or down

---

## Re-Run Mode

When the candidate re-runs a specific round (either via the round-type argument or by request):
- Use DIFFERENT questions from the same category (don't repeat the same questions)
- If `MOCK_INTERVIEW.md` exists with prior attempts, reference their previous weak areas and target those
- In feedback, compare against prior performance: "Last time you struggled with X — this time you [improved/still need work]"
- Track improvement over time in the Progress Tracking section

---

## Quality Checklist

Before finishing, verify:
- [ ] Questions are researched and specific to this company (not generic interview questions)
- [ ] Questions reflect the company's known interview style, values, and tech stack
- [ ] Follow-up probes are realistic and based on the candidate's actual answers
- [ ] Feedback distinguishes between content quality and communication quality
- [ ] "Stronger answer" frameworks use the candidate's real experience, not hypotheticals
- [ ] Communication feedback is specific with examples (not "be more concise" but "in Q3, your answer was 200 words when 80 would land harder — here's the tighter version")
- [ ] Practice plan is prioritized and actionable
- [ ] Stories to prepare reference specific entries from the candidate profile
- [ ] Each interviewer has a distinct personality and evaluation focus
- [ ] Difficulty matches the target company's known interview rigor
- [ ] No feedback during the mock itself — stay in character until the round ends
