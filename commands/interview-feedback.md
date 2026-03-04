# Interview Feedback — Post-Interview Debrief & Analysis

Debrief after an interview round. The agent asks structured questions to understand how the interview went, then produces an analysis with signal reads, performance assessment, preparation gaps, and next-round strategy.

## Inputs

**Usage:** `/interview-feedback "company" "role" "interviewer-name" "interviewer-title"`

Parse `$ARGUMENTS` as up to four quoted strings:
- **First quoted string** (required) — Company name (e.g., `"Anthropic"`)
- **Second quoted string** (required) — Role title (e.g., `"Security Engineer"`)
- **Third quoted string** (optional) — Interviewer's name
- **Fourth quoted string** (optional) — Interviewer's title/role (e.g., `"VP of Engineering"`)

If company and role are missing, ask. Interviewer details can also be gathered during the debrief.

## Setup

This command expects a `claude-job-hunter` repo cloned locally. Find it by checking (in order):
1. `$CLAUDE_JOB_HUNTER_DIR` environment variable
2. `./claude-job-hunter/` relative to cwd
3. `~/claude-job-hunter/`

If not found, ask the user for the path.

## Existing Context

Before starting the debrief, check for an existing evaluation:
- Look for `[claude-job-hunter]/evaluations/[company]-[role-slug]/`
- If found, read `SCORECARD.md`, `INTERVIEW_PREP.md`, and `COMPANY_INTEL.md`
- Use this to contextualize the debrief — compare what was predicted vs. what happened
- If no evaluation exists, that's fine — the debrief still works standalone

Also read:
- **Candidate profile:** `[claude-job-hunter]/profile/candidate-profile.md`

## Output

Append to or create:
```
[claude-job-hunter]/evaluations/[company]-[role-slug]/
└── INTERVIEW_LOG.md    # Running log of all interview rounds with analysis
```

Each debrief is appended as a new section in `INTERVIEW_LOG.md`, building a complete picture across rounds.

## Workflow

### Phase 1: Capture the Basics

Ask the candidate:

1. **Interview logistics**
   - What round was this? (phone screen, technical, behavioral, panel, final, etc.)
   - How long did it last?
   - Who interviewed you? (name, title — if not provided in args)
   - Was anyone else present?
   - Format: video, phone, in-person, take-home review, etc.

2. **Overall gut feeling**
   - On a scale of 1-10, how do you think it went?
   - One word to describe the vibe
   - Did you leave feeling energized or drained?

---

### Phase 2: Deep Debrief — Questions for the Candidate

Ask these in a conversational flow. Don't dump all at once — ask 3-4 at a time based on what the candidate shares, then follow up.

#### What They Asked You

3. **Questions asked** — List every question you can remember, even paraphrased
4. **Your answers** — For each question:
   - What did you say? (rough summary is fine)
   - How confident did you feel in your answer?
   - Anything you wish you'd said differently?
5. **Curveballs** — Any questions you didn't expect or weren't prepared for?
6. **Technical depth** — Did they go deep on anything? What level of detail?

#### How the Interviewer Behaved

7. **Engagement signals** — Did the interviewer seem:
   - Engaged / leaning in / asking follow-ups? On which topics?
   - Disengaged / rushing / checking time? When?
   - Taking notes? How much?
8. **Body language / tone** — Any notable reactions?
   - Nodding, smiling, "that's great" moments
   - Frowning, skepticism, pushback moments
   - Neutral / poker face throughout
9. **Selling the role** — Did the interviewer spend time selling:
   - The team, the company, the role?
   - Growth opportunities, projects, culture?
   - (Selling is a strong positive signal — interviewers sell when they like a candidate)
10. **Time allocation** — How did the interview split between:
    - Them asking you questions
    - You asking them questions
    - Them telling you about the role/company
    - Small talk / rapport building

#### What You Learned About Them

11. **What did you learn** about the role, team, or company that you didn't know before?
12. **Culture signals** — What did the interview tell you about how they work?
13. **Red flags** — Anything that gave you pause? Evasive answers, concerning statements, misaligned expectations?
14. **Green flags** — Anything that made you more excited about the opportunity?

#### Your Questions to Them

15. **Questions you asked** — What did you ask? What were their answers?
16. **Questions you didn't get to ask** — Ran out of time? Forgot?
17. **Answer quality** — Were their answers specific and genuine, or vague and rehearsed?

#### Closing & Next Steps

18. **How did it end?** — Did they:
    - Give a clear timeline for next steps?
    - Mention next rounds or who you'd meet next?
    - Say anything encouraging ("looking forward to...") or neutral?
19. **Follow-up** — Did they ask for references, work samples, or anything else?
20. **Your interest level** — After this interview, how interested are you? More, less, or the same as before?

---

### Phase 3: Analysis

After gathering the debrief, produce a structured analysis:

#### A. Signal Analysis

Read the tea leaves. Based on interviewer behavior, assess:

**Positive signals observed:**
- Selling the role/company (strong buy signal)
- Asking detailed follow-ups (genuine interest)
- Running over time (they wanted to keep talking)
- Mentioning next steps or specific people to meet
- Sharing internal context unprompted
- Laughing, rapport moments

**Negative signals observed:**
- Rushing through questions (checking a box)
- Not asking follow-ups (surface-level engagement)
- Ending early
- Vague on next steps
- Not selling the role at all
- Challenging tone without curiosity

**Ambiguous signals:**
- Poker face throughout (some interviewers are just like this)
- Standard closing language (doesn't signal either way)

**Overall read:** Likely advancing / 50-50 / Probably not → with reasoning

#### B. Performance Assessment

For each question the interviewer asked:

| Question | Your Answer Summary | Assessment | What a Stronger Answer Looks Like |
|----------|-------------------|------------|-----------------------------------|
| ... | ... | Strong / Adequate / Weak | (only if adequate or weak) |

- **Strongest moments:** Where you clearly impressed
- **Missed opportunities:** Topics where you had relevant experience but didn't bring it up
- **Recovery candidates:** Weak answers you could address in a follow-up email or next round

#### C. Preparation Gap Analysis

If an `INTERVIEW_PREP.md` exists:
- Which predicted questions actually came up? How well did prep help?
- Which predicted questions didn't come up (might in next rounds)?
- What came up that wasn't predicted? (update prep for next rounds)
- How accurate was the interviewer profile prediction?

If no prep exists:
- Based on what was asked, what pattern of questions should the candidate expect in future rounds?

#### D. Culture & Fit Update

Based on what the candidate observed:
- **SWOT updates:** Any strengths/weaknesses/opportunities/threats that should be revised?
- **Culture signals confirmed:** Things from research that the interview validated
- **Culture signals contradicted:** Things that seemed different in practice
- **New information:** Anything not in the original research
- **Updated interest level:** Given new information, should the candidate's enthusiasm change?

#### E. Next Round Strategy

- **Topics to prepare:** Based on gaps and what's likely next
- **Stories to have ready:** Specific experiences to prepare as narratives
- **Questions to ask next time:** Especially things that weren't answered or raised concerns
- **Points to reinforce:** Themes that resonated — lean into these
- **Points to course-correct:** If an answer was weak, how to proactively address it
- **Interviewer research:** If next interviewers are known, research them

#### F. Follow-Up Actions

- **Thank-you note draft** — personalized to this interviewer referencing specific conversation points (NOT generic "thank you for your time")
- **Items to send** — if the interviewer asked for references, work samples, or follow-up info
- **Timeline tracking** — when to expect to hear back, when to follow up if silence

---

### Phase 4: Write Output

Append a new section to `INTERVIEW_LOG.md` with this structure:

```markdown
---

## Round [N]: [Interview Type] — [Interviewer Name], [Title]
**Date:** [date]
**Duration:** [duration]
**Format:** [video/phone/in-person]
**Candidate gut feeling:** [X/10]

### Questions Asked & Performance
(table from Phase 3B)

### Signal Analysis
(from Phase 3A)

### Key Takeaways
- Top 3 things that went well
- Top 3 things to improve
- Overall read: [likely advancing / 50-50 / probably not]

### Culture & Fit Notes
(from Phase 3D)

### Next Round Prep
(from Phase 3E)

### Follow-Up Actions
- [ ] Send thank-you note by [date]
- [ ] Prepare [topic] for next round
- [ ] Research [next interviewer] before next round
- [ ] (other action items)

### Thank-You Note Draft
(from Phase 3F)
```

If `INTERVIEW_LOG.md` already exists with prior rounds, also add a **Cross-Round Patterns** section at the top of the file that synthesizes themes across all interviews:
- Recurring topics (they care about X)
- Consistent signals (positive or negative)
- Evolving candidate interest level
- Overall process assessment

**Output:** Present the analysis summary to the candidate. Ask if anything needs correction or addition.

---

## Quality Checklist

Before finishing, verify:
- [ ] Every question the candidate remembered is captured and assessed
- [ ] Signal analysis is grounded in specific observed behaviors, not speculation
- [ ] Performance assessment gives actionable "stronger answer" suggestions for weak spots
- [ ] Thank-you note references specific conversation points (not generic)
- [ ] Next round strategy is concrete and actionable
- [ ] If prior evaluation exists, analysis references and updates it
- [ ] Culture signals are compared against prior research if available
- [ ] Follow-up actions have specific deadlines
- [ ] Candidate's sentiment and interest level changes are noted
