# Uninstall — Remove Claude Job Hunter Skills

Remove all Claude Job Hunter slash commands from `~/.claude/commands/` and clean up the configuration file. Does NOT delete your personal data (profile, evaluations, cards) — only removes the skill symlinks and config.

## Inputs

**Usage:** `/uninstall`

No arguments. The command will confirm before removing anything.

## Workflow

### Step 1: Locate the repo

Find the `claude-job-hunter` repo by checking (in order):
1. The directory this command file lives in (resolve symlinks to find the repo root — go up one level from `commands/`)
2. Read `~/.claude/.claude-job-hunter.conf` for `REPO_DIR`
3. `$CLAUDE_JOB_HUNTER_DIR` environment variable
4. `./claude-job-hunter/` relative to cwd
5. `~/claude-job-hunter/`

### Step 2: Inventory what's installed

Check for installed components:

**Symlinked commands** — look for these in `~/.claude/commands/`:
- `build-profile.md`
- `should-i-apply.md`
- `mock-interview.md`
- `interview-feedback.md`
- `comp-research.md`
- `offer-negotiation.md`
- `player-card.md`
- `setup.md`
- `uninstall.md`

Only list files that are symlinks pointing into the repo's `commands/` directory. Do NOT remove files that aren't symlinks or that point elsewhere — those belong to other tools.

**Config file:**
- `~/.claude/.claude-job-hunter.conf`

**Working directory** (from config if it exists):
- Read `WORK_DIR` from config to show where personal data lives

### Step 3: Show what will be removed and confirm

Present the inventory:

```
Claude Job Hunter — Uninstall

The following will be removed:

  Slash commands (symlinks in ~/.claude/commands/):
    /build-profile
    /should-i-apply
    /mock-interview
    /interview-feedback
    /comp-research
    /offer-negotiation
    /player-card
    /setup
    /uninstall

  Config file:
    ~/.claude/.claude-job-hunter.conf

The following will NOT be removed:
  Repo:         [repo path]
  Working dir:  [work-dir path]  (your profile, evaluations, and cards are safe)

Proceed? (yes/no)
```

**Wait for the user to confirm before removing anything.** If they say no, stop.

### Step 4: Remove symlinks

For each command listed above, remove the symlink ONLY if it points into the repo's `commands/` directory:

```bash
# For each command, verify it's a symlink to the repo before removing
# Example check: readlink ~/.claude/commands/build-profile.md
# Only rm if the target is inside [repo]/commands/
```

Do NOT use `rm -rf` or wildcards. Remove each file individually after verifying it's a repo symlink.

### Step 5: Remove config

```bash
rm ~/.claude/.claude-job-hunter.conf
```

If `~/.claude/.claude-job-hunter.conf` is itself a symlink, also remove the original config file it points to (the one in the working directory).

### Step 6: Report results

```
Claude Job Hunter — Uninstalled

Removed:
  ✓ 9 slash commands from ~/.claude/commands/
  ✓ Config file ~/.claude/.claude-job-hunter.conf

Still present (not removed):
  Repo:         [repo path]  (delete manually if you want: rm -rf [repo path])
  Working dir:  [work-dir path]  (your personal data — delete manually if desired)

To reinstall later:
  ln -sf [repo]/commands/setup.md ~/.claude/commands/setup.md
  Then run /setup
```
