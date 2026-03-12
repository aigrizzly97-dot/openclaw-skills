# OpenClaw Cron Jobs Configuration

Automated task scheduling for skill management and feedback integration.

## Overview

Cron jobs run within the OpenClaw environment and coordinate with GitHub Actions workflows. They handle:
- Daily skill synchronization
- Telegram feedback polling and parsing
- Weekly skill validation audits
- Repository maintenance

## Configuration

### Register Cron Jobs in OpenClaw

Add these entries to your OpenClaw configuration:

```yaml
# ~/.openclaw/config/cron.yaml
jobs:
  - name: daily-skill-sync
    schedule: "0 2 * * *"
    command: "openclaw skill-sync --repo https://github.com/aigrizzly97-dot/openclaw-skills"
    enabled: true
    timeout: 300
    
  - name: telegram-feedback-listener
    schedule: "*/15 * * * *"
    command: "openclaw feedback-listener --topics gambling,seo-tz,anchor"
    enabled: true
    timeout: 60
    
  - name: weekly-skill-audit
    schedule: "0 8 * * 1"
    command: "openclaw skill-audit --repo https://github.com/aigrizzly97-dot/openclaw-skills"
    enabled: true
    timeout: 600
```

## Job Descriptions

### 1. Daily Skill Sync (02:00 UTC)

**Schedule:** `0 2 * * *` (Every day at 2:00 AM UTC)

**Purpose:** Synchronize all skill changes to GitHub with timestamp

**Workflow:**
```
1. Pull latest changes from GitHub
2. Check for local SKILL.md modifications
3. Update version numbers if changed
4. Commit with timestamp
5. Push to origin/main
```

**Configuration:**
```bash
openclaw skill-sync \
  --repo https://github.com/aigrizzly97-dot/openclaw-skills \
  --branch main \
  --message "Daily skill sync: {timestamp}"
```

**Success Indicators:**
- New commit appears in repository
- All SKILL.md files are up-to-date
- No merge conflicts

**Troubleshooting:**
```bash
# Check last sync status
openclaw job status daily-skill-sync

# View recent commits
git log --oneline -10

# Manual trigger
openclaw skill-sync --force
```

---

### 2. Telegram Feedback Listener (Every 15 minutes)

**Schedule:** `*/15 * * * *` (Every 15 minutes)

**Purpose:** Poll Telegram topics for feedback and update SKILL.md files

**Topics Monitored:**
- `#gambling-agent-feedback`
- `#seo-tz-agent-feedback`
- `#anchor-agent-feedback`

**Workflow:**
```
1. Connect to Telegram API
2. Poll each feedback topic for new messages
3. Parse natural language feedback (Variant C)
4. Extract improvement suggestions
5. Create/update GitHub issues
6. Modify relevant SKILL.md sections
7. Commit changes with reference to message ID
```

**Feedback Parsing (Variant C - Natural Criticism):**

Example input:
```
"The agent should handle batch processing better. 
Also, it's slow with files over 100MB. 
Can we add support for Dutch?"
```

Parsed as:
```yaml
agent: gambling-agent
feedback_type: feature_request, performance, localization
suggestions:
  - Improve batch processing capabilities
  - Optimize for large files (>100MB)
  - Add Dutch language support
message_id: 12345
timestamp: 2026-03-13T12:00:00Z
```

**Configuration:**
```bash
openclaw feedback-listener \
  --topics gambling,seo-tz,anchor \
  --interval 15 \
  --parser natural \
  --auto-update-skill
```

**Success Indicators:**
- No errors in listener logs
- Feedback parsed correctly
- GitHub issues created for new feedback
- SKILL.md files updated

**Monitoring:**
```bash
# Check listener status
openclaw feedback-listener --status

# View recent feedback
openclaw feedback-listener --history --limit 10

# Test parser on sample text
openclaw feedback-listener --test-parse "Your feedback text here"
```

---

### 3. Weekly Skill Audit (Mondays 08:00 UTC)

**Schedule:** `0 8 * * 1` (Every Monday at 8:00 AM UTC)

**Purpose:** Validate all skills are functional and up-to-date

**Workflow:**
```
1. Check all SKILL.md files exist
2. Validate YAML frontmatter
3. Verify version numbers are semantic
4. Check updated timestamps (not older than 30 days)
5. Run skill format validation
6. Generate audit report
7. Create GitHub issue if problems found
```

**Configuration:**
```bash
openclaw skill-audit \
  --repo https://github.com/aigrizzly97-dot/openclaw-skills \
  --max-age-days 30 \
  --report-format markdown
```

**Validation Checks:**
- ✅ All required fields present (name, description, version, updated)
- ✅ Version follows semantic versioning
- ✅ Updated timestamp is recent (within 30 days)
- ✅ YAML syntax is valid
- ✅ No duplicate triggers
- ✅ SKILL.md file size reasonable (<50KB)

**Success Indicators:**
- Audit report generated
- No validation errors
- All skills marked as "Active"

**Audit Report Example:**
```markdown
# Weekly Skill Audit Report
**Date:** 2026-03-17

## Summary
- ✅ 3 skills validated
- ✅ All SKILL.md files valid
- ✅ No issues found

## Skill Status
- gambling-agent v2.1.0 - Updated 2026-03-13 - ✅ Active
- seo-tz-agent v2.0.0 - Updated 2026-03-10 - ✅ Active
- anchor-agent v1.5.0 - Updated 2026-03-08 - ⚠️ Stale (5 days)

## Recommendations
- Consider updating anchor-agent (no changes in 5 days)
```

---

## Implementation in OpenClaw

### Step 1: Create Cron Configuration File

```bash
mkdir -p ~/.openclaw/cron
cat > ~/.openclaw/cron/skills.yaml << 'EOF'
jobs:
  - name: daily-skill-sync
    schedule: "0 2 * * *"
    description: "Synchronize skills to GitHub"
    enabled: true
    command: |
      cd /tmp/openclaw-skills
      git pull origin main
      git add skills/
      git commit -m "Daily skill sync: $(date -u +'%Y-%m-%d %H:%M:%S UTC')" || true
      git push origin main
    
  - name: telegram-feedback-listener
    schedule: "*/15 * * * *"
    description: "Poll Telegram for feedback"
    enabled: true
    command: |
      openclaw feedback-listener \
        --topics gambling,seo-tz,anchor \
        --auto-update-skill \
        --commit-changes
    
  - name: weekly-skill-audit
    schedule: "0 8 * * 1"
    description: "Weekly skill validation audit"
    enabled: true
    command: |
      cd /tmp/openclaw-skills
      python3 << 'AUDIT'
      import os
      import yaml
      from datetime import datetime, timedelta
      
      audit_results = {"valid": [], "issues": []}
      
      for skill_dir in ['skills/gambling-agent', 'skills/seo-tz-agent', 'skills/anchor-agent']:
          skill_file = f"{skill_dir}/SKILL.md"
          if not os.path.exists(skill_file):
              audit_results["issues"].append(f"Missing: {skill_file}")
              continue
          
          with open(skill_file) as f:
              content = f.read()
              # Parse and validate...
              audit_results["valid"].append(os.path.basename(skill_dir))
      
      print(f"Audit Results: {len(audit_results['valid'])} valid, {len(audit_results['issues'])} issues")
      AUDIT
EOF
```

### Step 2: Load Configuration in OpenClaw

```bash
openclaw cron load ~/.openclaw/cron/skills.yaml
```

### Step 3: Verify Cron Jobs

```bash
# List all scheduled jobs
openclaw cron list

# Check specific job status
openclaw cron status daily-skill-sync

# View job logs
openclaw cron logs daily-skill-sync --limit 100
```

## Monitoring & Troubleshooting

### View Cron Logs

```bash
# Stream live logs
openclaw cron logs --follow

# View specific job logs
openclaw cron logs daily-skill-sync

# Search for errors
openclaw cron logs --grep "ERROR" --since "1 hour ago"
```

### Manual Trigger

```bash
# Run job immediately
openclaw cron run daily-skill-sync

# Run with verbose output
openclaw cron run daily-skill-sync --verbose
```

### Disable/Enable Jobs

```bash
# Temporarily disable
openclaw cron disable daily-skill-sync

# Re-enable
openclaw cron enable daily-skill-sync
```

### Environment Variables

Cron jobs inherit from:
1. System environment variables
2. `.env` file in repository
3. OpenClaw configuration

Ensure these are set:
```bash
export GITHUB_TOKEN=your_token
export TELEGRAM_TOKEN=your_token
export GIT_USER_EMAIL=agent@openclaw.local
export GIT_USER_NAME=OpenClaw Agent
```

## GitHub Actions vs Cron Jobs

**GitHub Actions (Cloud):**
- Scheduled workflows (daily-commit, weekly-report)
- PR validation (skill-validate)
- Runs on GitHub servers
- No local resource usage

**OpenClaw Cron (Local):**
- Telegram feedback polling (real-time integration)
- Local skill updates
- Quick response to feedback
- Runs on user's machine

**Coordination:**
```
Telegram Message → Cron Listener → Update SKILL.md → Daily Commit → GitHub Push
                                ↓                        ↓
                           GitHub Issue            GitHub Workflow
```

## Troubleshooting

### Cron job not running

```bash
# Check if OpenClaw daemon is running
openclaw gateway status

# Check if job is enabled
openclaw cron status daily-skill-sync

# View error logs
openclaw cron logs daily-skill-sync --errors
```

### Git authentication issues

```bash
# Verify GitHub token
echo $GITHUB_TOKEN

# Check git configuration
git config --list | grep user.

# Test GitHub access
git ls-remote https://github.com/aigrizzly97-dot/openclaw-skills.git
```

### Telegram connection issues

```bash
# Test Telegram token
curl https://api.telegram.org/bot$TELEGRAM_TOKEN/getMe

# Check topic IDs
openclaw feedback-listener --list-topics
```

## Performance Considerations

- **Daily Sync:** Runs at 02:00 UTC (low traffic time)
- **Feedback Listener:** 15-minute interval (balance between latency and resources)
- **Weekly Audit:** Monday mornings (low activity period)

Adjust schedules based on your usage patterns.

---

**Last Updated:** 2026-03-13  
**Version:** 1.0.0
