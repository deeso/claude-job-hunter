# Job Search — Search ATS Boards & Auto-Evaluate

Search Ashby and Greenhouse public job board APIs for matching roles, filter across multiple dimensions, and auto-evaluate approved matches using the same pipeline as `/should-i-apply`.

## Inputs

**Usage:** `/job-search "company1, company2" "role-keywords" "profile-name"`

Parse `$ARGUMENTS` as up to three quoted strings:
- **First quoted string** (optional) — Comma-separated list of company names or board tokens to search
- **Second quoted string** (optional) — Comma-separated role keyword filters (e.g., `"security, platform, infrastructure"`)
- **Third quoted string** (optional) — Profile name (e.g., `"default"`, `"security"`). See Profile Resolution below.

If no arguments are provided, search all companies in the watchlist file. If no watchlist exists, create one from the template and walk the user through adding their first companies before proceeding (do NOT ask for companies as a one-off prompt — the watchlist is the persistent source of truth).

## Setup

Resolve paths by checking (in order):
1. Read `~/.claude/.claude-job-hunter.conf` for `WORK_DIR`, `REPO_DIR`, and component paths
2. Fall back: `$CLAUDE_JOB_HUNTER_DIR` → `./claude-job-hunter/` → `~/claude-job-hunter/`

Required paths:
- **Profile:** `[PROFILE_DIR]/[resolved-profile-name].md`
- **Watchlist:** `[WORK_DIR]/WATCHLIST.md`
- **Search log:** `[WORK_DIR]/JOB_SEARCH_LOG.md`
- **Evaluations dir:** `[EVALUATIONS_DIR]/`
- **Resources dir:** `[RESOURCES_DIR]/` (research checklists)
- **Watchlist template:** `[REPO_DIR]/resources/watchlist-template.md`

If the config exists, use its paths. Otherwise, use `[claude-job-hunter]/` as both repo and working directory.

If nothing is found, ask the user to run `/setup` first or provide the path.

## Profile Resolution

Determine which profile to use:

1. **Explicit argument:** If a profile name was passed (3rd arg), use `[PROFILE_DIR]/[profile-name].md` (append `.md` if not present)
2. **Config default:** Read `ACTIVE_PROFILE` from `~/.claude/.claude-job-hunter.conf` — if set, use `[PROFILE_DIR]/[ACTIVE_PROFILE].md`
3. **Auto-detect:** Scan `[PROFILE_DIR]/` for `.md` files other than `candidate-profile.md`:
   - **One found** → use it automatically
   - **Multiple found** → list each with its first heading line, ask the user to pick
   - **None found** → tell the user: "No profile found. Run `/build-profile` first."

The resolved profile path replaces all references to `candidate-profile.md` below.

## Workflow

Execute these phases in order. Present findings to the user after each phase for confirmation before proceeding.

---

### Phase 1: Setup & Configuration

1. Resolve all paths using the standard method above
2. Read the candidate profile at `[PROFILE_DIR]/[resolved-profile-name].md`
   - If no profile exists, tell the user to run `/build-profile` first
3. Read the watchlist at `[WORK_DIR]/WATCHLIST.md`
   - If no watchlist exists, copy the template from `[REPO_DIR]/resources/watchlist-template.md` to `[WORK_DIR]/WATCHLIST.md`
   - Walk the user through adding at least one company to the watchlist (name, optional board token and platform)
   - Write their entries into the watchlist file, then continue
4. Read the search log at `[WORK_DIR]/JOB_SEARCH_LOG.md` (if it exists) for deduplication
5. Build the search plan by merging:
   - Companies from arguments (first quoted string, comma-separated) — if provided
   - Companies from watchlist `## Companies` section
   - Deduplicate company list
   - If the merged list is still empty, tell the user: "No companies to search. Add companies to your watchlist at `[WORK_DIR]/WATCHLIST.md` or pass them as the first argument." Then stop.
6. Build filter set by merging:
   - Role keywords from arguments (second quoted string, comma-separated)
   - Global filters from watchlist `## Search Filters` section
   - Per-company filter overrides from watchlist (if a company entry has its own `Filters:` line)
7. Present the search plan to the user:

```
Job Search Plan

Companies to search: [list]
Role keywords: [list]
Department keywords: [list]
Description keywords: [list]
Work modality: [list or "any"]
Location: [list or "any"]
Employment type: [list or "any"]

Previously searched: [count from log] jobs already in log
```

**Confirm with user before proceeding.**

---

### Phase 2: ATS Platform Discovery

For each company in the search plan, determine whether they use Ashby or Greenhouse and find the board token.

**Resolution order for each company:**

1. **Check watchlist first** — if the company entry has `Platform: ashby` or `Platform: greenhouse` AND a `Board token:` value, use those directly. Skip discovery for this company.

2. **Try Ashby API** — attempt to fetch:
   ```
   GET https://api.ashbyhq.com/posting-api/job-board/{token}?includeCompensation=true
   ```
   Try these token variants (in order):
   - Company name as-is, lowercased (e.g., `anthropic`)
   - Company name hyphenated (e.g., `palo-alto-networks`)
   - Company name without spaces (e.g., `paloaltonetworks`)
   - Common abbreviations if obvious (e.g., `pan` for Palo Alto Networks)

   A successful response (HTTP 200 with job data) confirms Ashby.

3. **Try Greenhouse API** — attempt to fetch:
   ```
   GET https://boards-api.greenhouse.io/v1/boards/{token}/jobs?content=true
   ```
   Try the same token variants as above.

   A successful response (HTTP 200 with job data) confirms Greenhouse.

4. **WebSearch fallback** — if neither API responds:
   - Search for `"{company name}" careers site` or `"{company name}" jobs board`
   - Look for URLs containing `jobs.ashbyhq.com/{token}`, `boards.greenhouse.io/{token}`, or `jobs.lever.co/{token}`
   - Extract the board token from the URL
   - Confirm by hitting the appropriate API with the extracted token

5. **Give up gracefully** — if no ATS board is found after all attempts:
   - Report: `"⚠ {company}: No Ashby or Greenhouse board found. Skipping."`
   - Suggest the user check the careers page manually and add the token to their watchlist

**Output:** Present discovery results:

```
ATS Discovery Results

✓ Anthropic — Ashby (token: anthropic)
✓ Cloudflare — Greenhouse (token: cloudflare)
✗ Acme Corp — No board found (skipping)

Proceeding with [N] companies. Continue?
```

**Confirm with user before proceeding.**

---

### Phase 3: Fetch & Filter Jobs

For each confirmed company/platform, fetch all jobs and apply filters.

#### 3a. Fetch Jobs

**Ashby:** `GET https://api.ashbyhq.com/posting-api/job-board/{token}?includeCompensation=true`

Response structure (extract from `jobs` array):
```json
{
  "title": "Security Engineer",
  "location": "San Francisco, CA",
  "department": "Engineering",
  "team": "Security",
  "employmentType": "FullTime",
  "workplaceType": "Remote",  // or "Hybrid", "OnSite"
  "compensation": { ... },
  "descriptionPlain": "...",
  "jobUrl": "https://jobs.ashbyhq.com/...",
  "secondaryLocations": [...]
}
```

**Greenhouse:** `GET https://boards-api.greenhouse.io/v1/boards/{token}/jobs?content=true`

Response structure (extract from `jobs` array):
```json
{
  "title": "Security Engineer",
  "location": { "name": "San Francisco, CA" },
  "departments": [{ "name": "Engineering" }],
  "content": "<p>Job description HTML...</p>",
  "absolute_url": "https://boards.greenhouse.io/...",
  "metadata": [...]
}
```

#### 3b. Normalize to Common Schema

Convert both API responses to a unified format for filtering:

| Field | Ashby Source | Greenhouse Source |
|---|---|---|
| `title` | `title` | `title` |
| `location` | `location` | `location.name` |
| `secondaryLocations` | `secondaryLocations` | — |
| `department` | `department` + `team` | `departments[].name` |
| `workplaceType` | `workplaceType` | Infer from `location.name` (look for "Remote", "Hybrid") |
| `employmentType` | `employmentType` | Infer from `metadata` or title |
| `description` | `descriptionPlain` | Strip HTML from `content` |
| `compensation` | `compensation` | Infer from `content` if present |
| `jobUrl` | `jobUrl` | `absolute_url` |
| `company` | (from search plan) | (from search plan) |
| `platform` | `ashby` | `greenhouse` |

For Greenhouse `workplaceType` inference:
- If location contains "Remote" → `Remote`
- If location contains "Hybrid" → `Hybrid`
- Otherwise → `OnSite`

#### 3c. Apply Filters

Filters are applied as **AND across dimensions, OR within each dimension**:

- **Role keywords** → case-insensitive substring match on `title`. If multiple keywords, match ANY.
- **Department keywords** → case-insensitive match on `department`. If multiple keywords, match ANY.
- **Description keywords** → case-insensitive match on plain text `description`. If multiple keywords, match ANY.
- **Work modality** → exact match on `workplaceType` (`Remote`, `Hybrid`, `OnSite`). If multiple values, match ANY.
- **Location** → case-insensitive substring match on `location` + `secondaryLocations`. If multiple locations, match ANY.
- **Employment type** → normalized match on `employmentType`. Map variants: `FullTime`/`Full-time`/`Full Time` → `FullTime`, etc. If multiple types, match ANY.

If a filter dimension is empty/unset, skip it (match all).

Example: Role keywords = `["security", "platform"]` AND Work modality = `["Remote"]` means:
- Title must contain "security" OR "platform"
- AND workplaceType must be "Remote"
- AND all other unset filters are ignored

#### 3d. Deduplicate Against Search Log

- Read previous entries from `JOB_SEARCH_LOG.md`
- Use `jobUrl` as the dedup key
- Mark each match as:
  - **NEW** — not seen in any previous search run
  - **PREVIOUSLY_SEEN** — URL exists in a prior run (include the date and status from that run)

#### 3e. Present Results

Display filtered results as a numbered table:

```
Job Search Results — [date]

[N] matches found across [M] companies ([X] new, [Y] previously seen)

 #  | Status | Company     | Title                  | Location        | Type   | Department
----|--------|-------------|------------------------|-----------------|--------|----------
 1  | NEW    | Anthropic   | Security Engineer      | SF (Remote OK)  | Full   | Security
 2  | NEW    | Anthropic   | Platform Engineer      | NYC             | Full   | Platform
 3  | SEEN   | Cloudflare  | Security Architect     | Remote          | Full   | Security
 4  | NEW    | Cloudflare  | Staff Security Eng     | Austin          | Full   | Engineering

Filtered out: [N] jobs didn't match filters
```

If no matches found, report and offer to adjust filters.

**Confirm with user before proceeding.**

---

### Phase 4: User Confirmation

Let the user select which matches to evaluate:

```
Which matches would you like to evaluate?

Enter numbers (e.g., "1, 2, 4"), "all new", or "none":
```

Options:
- **Specific numbers** — evaluate those matches
- **"all new"** — evaluate all NEW matches (skip PREVIOUSLY_SEEN)
- **"all"** — evaluate everything including previously seen
- **"none"** — skip evaluation, just log results

Before confirming selections, offer: `"Want to see the full JD for any match? Enter a number to preview."`

If the user requests a JD preview:
- Display the full description text for that match
- Then re-ask for evaluation selections

---

### Phase 5: Auto-Evaluate Pipeline

For each approved match, run the `/should-i-apply` evaluation pipeline. This produces the same output structure but adapts to the ATS context (JD already fetched, company research can be shared).

#### 5a. Group by Company

Group selected matches by company. This allows sharing `COMPANY_INTEL.md` across multiple roles at the same company.

#### 5b. For Each Company Group

**Company research (once per company):**
- Check if `[EVALUATIONS_DIR]/[company]-*/COMPANY_INTEL.md` already exists from a previous evaluation
  - If yes and it's less than 30 days old, reuse it
  - If yes but older, do a lighter refresh (check for recent news only)
  - If no, run full company research using both research checklists:
    - `[RESOURCES_DIR]/research-checklist.md`
    - `[RESOURCES_DIR]/interview-research-checklist.md`
- Save to `[EVALUATIONS_DIR]/[company]-[first-role-slug]/COMPANY_INTEL.md`

**For each role at this company:**

1. Create output directory: `[EVALUATIONS_DIR]/[company]-[role-slug]/`
2. Read the candidate profile
3. Use the job description already fetched from the API (Phase 3) — no need to re-fetch
4. Run the evaluation pipeline (same phases as `/should-i-apply` Phase 4 and Phase 5):

   **Generate SCORECARD.md:**
   - Overall verdict: Strong Yes / Yes / Maybe / Probably Not / No
   - Match score with justification
   - Sentiment-driven SWOT (strengths, weaknesses, opportunities, threats)
   - Requirements scorecard table
   - Company card

   **Generate INTERVIEW_PREP.md:**
   - Likely interview panel
   - Questions they'll ask (with answer frameworks from candidate experience)
   - Questions to ask them (with good/bad answer signals)
   - Conversation points to weave in
   - Red flags to watch for
   - Culture & values alignment map

   **Copy or symlink COMPANY_INTEL.md** if shared with another role at same company

   **Generate README.md:**
   - Company, role, evaluation date
   - Verdict summary
   - File descriptions
   - Source: `/job-search` auto-evaluation (with JD URL)

5. Present a brief verdict summary after each evaluation:

```
Evaluation: [Company] — [Role]
Verdict: [Strong Yes / Yes / Maybe / Probably Not / No] ([score]%)
Top strength: [one line]
Top risk: [one line]
Output: [path to evaluation directory]
```

6. **Confirm between evaluations:**

```
Continue to next evaluation? (yes / skip remaining / stop)
```

- **yes** — proceed to next match
- **skip remaining** — stop evaluating but still log all matches
- **stop** — stop entirely, log what's done so far

---

### Phase 6: Update Search Log

After all evaluations (or skips) are complete, update `[WORK_DIR]/JOB_SEARCH_LOG.md`.

#### 6a. Create or Update Log

If the file doesn't exist, create it with a header:

```markdown
# Job Search Log

Automated search history from `/job-search`. Each run is prepended below.

---
```

#### 6b. Prepend New Run Section

Prepend (not append) the new run section so the most recent search is always at the top:

```markdown
## Search Run — [YYYY-MM-DD HH:MM]

**Search parameters:**
- Companies: [list]
- Role keywords: [list]
- Filters: [summary of active filters]

**Results:** [N] jobs scanned, [M] matches, [E] evaluated, [S] skipped

| # | Company | Title | URL | Status | Verdict | Score |
|---|---------|-------|-----|--------|---------|-------|
| 1 | Anthropic | Security Engineer | [url] | EVALUATED | Yes | 78% |
| 2 | Anthropic | Platform Engineer | [url] | SKIPPED | — | — |
| 3 | Cloudflare | Security Architect | [url] | PREVIOUSLY_SEEN | — | — |

---
```

Status values:
- **EVALUATED** — full evaluation produced (include verdict and score)
- **SKIPPED** — matched filters but user chose not to evaluate
- **PREVIOUSLY_SEEN** — appeared in a prior search run (include date first seen)
- **FILTERED_OUT** — did not match filters (only include count, not individual entries)

#### 6c. Detect Closed Listings

Compare the current API results against previous log entries for the same company:
- If a job URL from a previous run is no longer in the API response, mark it as **CLOSED** in the log
- Add a note: `"[CLOSED as of YYYY-MM-DD] — no longer on {company} job board"`
- Do NOT delete old entries — just annotate them

#### 6d. Offer Watchlist Updates

If any companies were discovered via WebSearch (not from the watchlist), offer to add them:

```
Discovered ATS boards not in your watchlist:
  - Anthropic → Ashby (token: anthropic)
  - Cloudflare → Greenhouse (token: cloudflare)

Add these to your watchlist? (yes/no)
```

If yes, append the company entries to `[WORK_DIR]/WATCHLIST.md` using the watchlist format.

---

### Phase 7: Summary Report

Present a final summary:

```
Job Search Complete — [date]

Companies searched:  [N]
  Successful:        [N] ([list])
  No board found:    [N] ([list])

Jobs scanned:        [N]
Matches found:       [N] (after filters)
  New:               [N]
  Previously seen:   [N]

Evaluations:         [N]
  Strong Yes:        [N]
  Yes:               [N]
  Maybe:             [N]
  Probably Not:      [N]
  No:                [N]

Skipped:             [N]

Top matches:
  1. [Company] — [Role] — [verdict] ([score]%) → [eval dir path]
  2. ...

Next steps:
  - /mock-interview "[company]" "[role]" — practice for your top match
  - /comp-research "[company]" "[role]" — check compensation before applying
  - /player-card "[company]" "[role]" — build a showcase site to send with your application
  - Edit WATCHLIST.md to refine filters for next search
  - Re-run /job-search to check for new postings

Search log: [path to JOB_SEARCH_LOG.md]
```

---

## Quality Checklist

Before finishing, verify:
- [ ] All API calls use the correct endpoints and handle errors gracefully
- [ ] Filters are applied correctly (AND across dimensions, OR within each)
- [ ] Deduplication against the search log works (jobUrl is the key)
- [ ] Evaluations produce the same quality output as `/should-i-apply`
- [ ] COMPANY_INTEL.md is reused across roles at the same company (not re-researched)
- [ ] Search log is prepended (newest first) with all matches recorded
- [ ] Closed listings are detected and annotated
- [ ] Watchlist updates are offered for newly discovered boards
- [ ] User has confirmation points between phases (no runaway automation)
- [ ] All research has source URLs
- [ ] SWOT items are specific to the candidate x company (not generic)
