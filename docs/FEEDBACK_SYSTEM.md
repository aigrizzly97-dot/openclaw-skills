# Feedback System — Continuous Improvement Mechanism

System for collecting, categorizing, and acting on agent feedback from Telegram topics, GitHub issues, and memory files.

## Feedback Sources

### 1. Telegram Topics (Primary)

**Channels & Topics:**
- `AI Grizzly Agents` group (main)
  - `#gambling-agent-issues` — bugs, errors, deployment problems
  - `#gambling-agent-wins` — successful workflows, tips, improvements
  - `#seo-tt-agent-issues` — TZ generation errors, keyword problems
  - `#seo-tt-agent-wins` — working examples, use cases
  - `#anchor-agent-issues` — anchor generation, uniqueness problems
  - `#anchor-agent-wins` — successful campaigns, anchor data
  - `#general-feedback` — cross-agent issues, system improvements

### 2. GitHub Issues (Structured)

**Repository:** `https://github.com/aigrizzly97-dot/openclaw-skills`

**Issue Templates:**
- `feedback.md` — General feedback or feature request
- `error-report.md` — Reproducible error with logs
- `bug.md` — Confirmed bug with minimal reproduction case

**Labels:**
- `type:bug` — Something is broken
- `type:enhancement` — Feature request or improvement
- `type:documentation` — Docs need update
- `type:feedback` — General user feedback
- `status:needs-investigation` — Flagged from Telegram
- `status:in-progress` — Being worked on
- `status:resolved` — Fixed, waiting test
- `niche:gambling` / `niche:seo-tt` / `niche:anchor` — Scope

### 3. Memory Files (Session Logs)

**Format:** `/Users/aigrizzly/.openclaw/workspace/memory/YYYY-MM-DD.md`

**Content to capture:**
- What worked
- What failed (with error message)
- Time spent on task
- Solutions discovered
- Suggestions for improvement

**Example entry:**
```markdown
## 2026-03-13 Session: Roshtein Anchor Generation

### ✅ Success
- Generated 140 unique anchors for 10 domains
- DataForSEO integration working (20 API calls, no rate limits)
- All anchors applied to HTML navigation menus

### ⚠️ Issue Encountered
- Issue: SFTP timeout when deploying to ice-football.de
- Error: "Connection timeout at /var/www/"
- Solution: Contacted Vlad, server firewall was blocking. Resolved via ISPManager.
- Time to solve: 25 minutes

### 💡 Improvement Idea
- Suggestion: Cache SFTP connections between deploys (currently reconnecting each time)
- Would save ~2 min per 10-domain batch

### 📝 Note for Agent Developer
- Tag for GitHub: #anchor-agent-enhancement
- Priority: Medium (10% time savings on batch deploys)
```

## Feedback Processing Pipeline

### Step 1: Capture Feedback

**Daily heartbeat check (every agent session):**
1. Read latest memory file (today's date)
2. Extract issues, successes, suggestions
3. Auto-tag issues based on keywords

**Auto-tagging rules:**
- "timeout" / "connection" → `network-issue`
- "rate limit" → `api-quota`
- "validation failed" → `validation-error`
- "could be faster" / "slow" → `performance`
- "unclear instructions" → `documentation`

### Step 2: Route to GitHub

**Telegram → GitHub automation:**
1. Telegram feedback in topic tagged with `#github-issue`
2. Auto-creates GitHub issue with:
   - Topic link (Telegram message URL)
   - Original text
   - Timestamp
   - Label: `status:needs-investigation`

**Example Telegram message:**
```
@anchor_agent #github-issue
DataForSEO API timeout after 100 keywords. 
Error: "429 Too Many Requests"
Needs retry logic with backoff.
Date: 2026-03-13 14:45
```

**Auto-generated GitHub issue:**
```
Title: DataForSEO API timeout on large batches
Body:
Reported via Telegram #anchor-agent-issues
Message: [link to Telegram message]

DataForSEO API timeout after 100 keywords.
Error: "429 Too Many Requests"
Needs retry logic with backoff.

Labels: type:feedback, status:needs-investigation, niche:anchor, api-quota
```

### Step 3: Triage & Prioritize

**Weekly triage (Sunday, automated):**
1. Collect all open issues from past week
2. Count by label (bug, enhancement, documentation)
3. Calculate impact:
   - `impact = (count × severity × frequency)`
   - severity: critical (3) / major (2) / minor (1)
   - frequency: always (3) / often (2) / rare (1)

**Example calculation:**
```
Issue: "SFTP timeout"
- Count: 2 reports
- Severity: major (affects deployment, workaround exists)
- Frequency: often (happens when ISP blocks)
- Impact: 2 × 2 × 2 = 8 points

Issue: "Typo in documentation"
- Count: 1 report
- Severity: minor (doesn't affect functionality)
- Frequency: rare (one user found it)
- Impact: 1 × 1 × 1 = 1 point
```

**Priority levels:**
- **P0 (Critical):** Impact ≥ 15, breaks core workflow
- **P1 (High):** Impact 8–14, significantly affects usage
- **P2 (Medium):** Impact 3–7, nice-to-have improvements
- **P3 (Low):** Impact 1–2, documentation/cosmetic

### Step 4: Action & Track

**For each issue:**
1. Assign to skill branch (gambling-agent, seo-tt-agent, etc.)
2. Create sub-task if needed
3. Update SKILL.md with known issue + workaround (if no fix yet)
4. Set target resolution date

**Status workflow:**
```
status:needs-investigation
  ↓
status:confirmed (reproducible)
  ↓
status:in-progress (being fixed)
  ↓
status:resolved (fixed, needs test)
  ↓
status:verified (tested, closed)
```

### Step 5: Update SKILL.md

**When issue is resolved:**
1. Document fix in SKILL.md → Error Handling section
2. Add version bump (e.g., 2.1.0 → 2.1.1)
3. Update CHANGELOG.md with fix
4. Close GitHub issue with link to commit

**SKILL.md update example:**
```markdown
## Error Handling & Recovery

### Common Issues

#### NEW: DataForSEO API Timeout (v1.1.0 fix)
**Problem:** "429 Too Many Requests" after 100+ API calls
**Root cause:** No backoff between requests
**Solution (v1.1.0+):**
```bash
python3 scripts/generate_anchors.py \
  --keyword roshtein \
  --dataseo-request-delay 2 \  # NEW: 2 sec between requests
  --output anchors.xlsx
```
**Fixed in:** Commit abc123def456
**GitHub issue:** #anchor-agent-42

---
```

## Known Issues Tracking

### Format

**File:** Each skill has `KNOWN_ISSUES.md`

```markdown
# Known Issues — Anchor Agent

Last updated: 2026-03-13

## Critical (P0)

None currently.

## High (P1)

### 1. ISPManager Root Password Not Working
**Status:** Workaround available  
**First reported:** 2026-03-12  
**Frequency:** Every deployment attempt  
**Impact:** Cannot deploy to certain domains (but SFTP user account works)  
**Workaround:** Use SFTP user credentials instead of root SSH  
**Fix:** Waiting for Vlad to reset root password  
**GitHub issue:** #anchor-agent-56  

## Medium (P2)

### 1. Slow Image Generation (10+ images)
**Status:** Enhancement proposed  
**First reported:** 2026-03-11  
**Frequency:** Batch operations only  
**Impact:** 5-10 minutes for 50 images  
**Workaround:** Generate images in smaller batches (10 at a time)  
**Proposed fix:** Parallel image generation API calls  
**GitHub issue:** #gambling-agent-78  

## Low (P3)

### 1. Typo in "Slot Games" Title
**Status:** Reported, waiting fix  
**Impact:** Cosmetic only  
**GitHub issue:** #gambling-agent-12  
```

## Feedback Examples

### Example 1: Bug Report → GitHub Issue → Fix → SKILL.md Update

**Telegram message (2026-03-12):**
```
@gambling_agent problem with image generation
Getting: "DALL-E timeout: request took >60s"
When generating 30 images for beat-the-brand.de
#github-issue
```

**Auto-created GitHub issue:**
```
Title: DALL-E image generation timeout on batch operations
Labels: type:bug, status:needs-investigation, niche:gambling
Body:
Reported via Telegram
Image generation times out when batch > 20 images
Error: "DALL-E timeout: request took >60s"
Affects: beat-the-brand.de deployment (30 images planned)
```

**Investigation & Fix:**
1. Reproduce: Generate 30 images locally → confirm timeout
2. Root cause: No concurrency limit, all requests fire simultaneously
3. Fix: Add `--parallel-limit 5` flag
4. Test: Works with 50 images, ~15 min total
5. Commit: `feat: add concurrency limit for image generation`
6. Version bump: 2.0.5 → 2.0.6

**SKILL.md update:**
```markdown
### Image Generation Timeout
**Problem:** "DALL-E timeout" on batches > 20 images
**Solution (v2.0.6+):**
```bash
python3 scripts/generate_images.py \
  --slots sweet-bonanza,gates-of-olympus \
  --count 30 \
  --parallel-limit 5 \  # Max 5 concurrent requests
  --output output/gambling/project/images/
```
**Resolved in:** Commit 7a8c9d0ef1g2h3i4j5k
**GitHub issue:** #gambling-agent-89
```

### Example 2: Enhancement Request → Design → Implementation → CHANGELOG

**Telegram message (2026-03-10):**
```
@seo_tt_agent could you auto-sort sections by search volume?
Right now H2 order is random. Would be nice if highest-volume 
keyword gets first H2 spot. Would improve CTR probably.
#github-issue
```

**Auto-created GitHub issue:**
```
Title: Auto-sort sections by keyword search volume
Labels: type:enhancement, status:needs-investigation, niche:seo-tt
Priority: P2 (medium)
Body:
Feature request: Auto-sort H2 sections by keyword volume (highest first)
Rationale: Higher-volume keywords should appear earlier for better CTR
Example: If "best casinos" (2800 vol) > "casino bonuses" (750 vol)
then "best casinos" H2 should come before "casino bonuses" H2
```

**Implementation:**
1. Design: Add `--sort-by-volume` flag
2. Code: Update generate_tz.py to reorder sections
3. Test: Compare manual order vs auto-sorted
4. Version: 1.2.0 → 1.3.0

**CHANGELOG.md entry:**
```markdown
## [1.3.0] — 2026-03-15

### Added
- Auto-sort H2 sections by search volume (highest first)
- New flag: `--sort-by-volume` (default: enabled)
- New flag: `--preserve-order` (to disable auto-sort)

### Example
```bash
python3 scripts/generate_tz.py \
  --input keywords.xlsx \
  --sort-by-volume \  # NEW
  --output ~/Downloads/ТЗ.xlsx
```

### Related
- GitHub issue: #seo-tt-agent-102
- Suggested by: @user in Telegram
```

## Feedback Metrics & Reporting

### Weekly Report (Every Sunday)

**Telegram message to #general-feedback:**
```
📊 Feedback Summary — Week of 2026-03-10 to 2026-03-16

### By Status
✅ Resolved: 3 (SFTP timeout, typo fix, image concurrency)
🔄 In progress: 2 (anchor uniqueness v2, tz sorting)
📋 Queued: 5 (awaiting investigation)
❌ Known issues: 4 (ISPManager root, datacache slowness, etc.)

### By Severity
🔴 Critical (P0): 0
🟠 High (P1): 3
🟡 Medium (P2): 4
🔵 Low (P3): 2

### Top Issues This Week
1. SFTP timeout (2 reports) — RESOLVED in v2.0.7
2. DataForSEO rate limit (1 report) — Workaround documented
3. Slow batch operations (3 reports) — In progress (v1.3.0)

### Community Impact
- Issues fixed: 3
- Workarounds documented: 2
- Features added: 1
- Docs improved: 2

### Next Week Focus
- Priority: Anchor uniqueness algorithm (P1)
- Enhancement: Parallel image generation (P2)
- Docs: Add troubleshooting guide (P3)

---
GitHub repo: https://github.com/aigrizzly97-dot/openclaw-skills
Skill versions: gambling=2.1.0, seo-tt=1.3.0, anchor=1.1.0
```

## Automation Setup (GitHub Actions)

### Workflow 1: Feedback Sync (Daily)

**File:** `.github/workflows/feedback-sync.yml`

```yaml
name: Feedback Sync
on:
  schedule:
    - cron: '0 9 * * *'  # Daily 9 AM UTC

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Poll Telegram topics
        run: |
          python3 scripts/telegram_feedback_sync.py \
            --channel "AI Grizzly Agents" \
            --topics "#gambling-agent-issues,#seo-tt-agent-issues,#anchor-agent-issues" \
            --tag-github-issues \
            --min-reactions 2
      - name: Create GitHub issues
        run: |
          python3 scripts/create_github_issues.py \
            --input feedback_export.json \
            --repo aigrizzly97-dot/openclaw-skills \
            --auto-label \
            --auto-assign-priority
      - name: Update known issues
        run: |
          python3 scripts/update_known_issues.py \
            --repo aigrizzly97-dot/openclaw-skills \
            --skills gambling-agent,seo-tt-agent,anchor-agent
```

### Workflow 2: Weekly Triage

**File:** `.github/workflows/weekly-triage.yml`

```yaml
name: Weekly Triage
on:
  schedule:
    - cron: '0 10 ? * SUN'  # Sundays 10 AM UTC

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Calculate impact scores
        run: |
          python3 scripts/calculate_issue_impact.py \
            --repo aigrizzly97-dot/openclaw-skills \
            --output impact_report.json
      - name: Assign priorities
        run: |
          python3 scripts/assign_priorities.py \
            --input impact_report.json \
            --apply-labels
      - name: Generate weekly report
        run: |
          python3 scripts/generate_weekly_report.py \
            --output WEEKLY_REPORT.md
      - name: Publish to Telegram
        run: |
          python3 scripts/send_telegram_report.py \
            --report WEEKLY_REPORT.md \
            --chat "AI Grizzly Agents" \
            --topic "#general-feedback"
```

## Best Practices

1. **Capture immediately:** Document issues/wins in memory file same day
2. **Use templates:** GitHub issues follow structure (reproducible, expected vs actual)
3. **Cross-reference:** Link Telegram ↔ GitHub ↔ SKILL.md
4. **Version everything:** Each fix bumps skill version
5. **Test before closing:** Verify fix works before marking resolved
6. **Document workarounds:** Don't leave users stuck while fix is being built
7. **Celebrate wins:** Share success stories in #wins topics
8. **Weekly cadence:** Consistent triage, reporting, releases

## References

- GitHub issues: https://github.com/aigrizzly97-dot/openclaw-skills/issues
- Memory file template: `memory/TEMPLATE.md`
- Known issues: Each skill folder → `KNOWN_ISSUES.md`
- Telegram link: https://t.me/[your-group-id]/[topic-id]
