# Deployment & Maintenance Guide

Complete guide for deploying and maintaining the OpenClaw Skills repository.

## Initial Setup

### 1. Prerequisites

- Git 2.34+
- Python 3.8+
- GitHub account with push access
- Telegram bot token (for feedback integration)
- OpenClaw 2.1+ installed

### 2. Clone Repository

```bash
# HTTPS (recommended for CI/CD)
git clone https://github.com/aigrizzly97-dot/openclaw-skills.git
cd openclaw-skills

# Or SSH (for local development)
git clone git@github.com:aigrizzly97-dot/openclaw-skills.git
cd openclaw-skills
```

### 3. Configure Environment

```bash
# Copy example configuration
cp .env.example .env

# Edit with your credentials
nano .env

# Verify configuration
source .env
echo "GitHub Token: ${GITHUB_TOKEN:0:10}..."
echo "Git User: $GIT_USER_NAME <$GIT_USER_EMAIL>"
```

### 4. Configure Git

```bash
# Set global configuration
git config --global user.email "$GIT_USER_EMAIL"
git config --global user.name "$GIT_USER_NAME"

# Verify
git config --list | grep user.
```

### 5. Authenticate with GitHub

```bash
# Using GitHub CLI (recommended)
gh auth login
# Select: GitHub.com, HTTPS, Y for storing password, Y for authentication

# Or configure git credential manager
git config --global credential.helper osxkeychain  # macOS
git config --global credential.helper manager-core  # Windows
```

### 6. Test Repository Access

```bash
# Pull latest changes
git pull origin main

# Check remote
git remote -v

# List branches
git branch -a
```

## Validation

### 1. Validate SKILL.md Files

```bash
# Check format
python3 << 'EOF'
import yaml
import re
from pathlib import Path

errors = []

for skill_file in Path('skills').glob('*/SKILL.md'):
    with open(skill_file) as f:
        content = f.read()
        
    # Check frontmatter
    match = re.match(r'^---\n(.*?)\n---\n', content, re.DOTALL)
    if not match:
        errors.append(f"{skill_file}: Missing frontmatter")
        continue
    
    # Validate YAML
    try:
        frontmatter = yaml.safe_load(match.group(1))
        required = ['name', 'description', 'version', 'updated']
        for field in required:
            if field not in frontmatter:
                errors.append(f"{skill_file}: Missing '{field}'")
    except yaml.YAMLError as e:
        errors.append(f"{skill_file}: Invalid YAML - {e}")

if errors:
    print("❌ Validation Errors:")
    for error in errors:
        print(f"  - {error}")
    exit(1)
else:
    print("✅ All SKILL.md files are valid")
EOF
```

### 2. Run GitHub Actions Locally

```bash
# Install act (GitHub Actions local runner)
# macOS: brew install act
# Linux: curl https://raw.githubusercontent.com/nektos/act/master/install.sh | bash

# Run validation workflow
act -j validate-skills -l

# Run all workflows
act -l
```

## Deployment Checklist

Before deploying to production:

- [ ] All SKILL.md files validated
- [ ] `.env` file configured with all secrets
- [ ] Git credentials verified
- [ ] GitHub Actions workflows enabled
- [ ] Cron jobs registered in OpenClaw
- [ ] Telegram bot token verified
- [ ] DataForSEO credentials (if using SEO agent)
- [ ] SFTP credentials (if using gambling agent)

## Making Changes

### 1. Update a Skill File

```bash
# Create feature branch
git checkout -b update/gambling-agent-v2.2

# Edit the skill
nano skills/gambling-agent/SKILL.md

# Update version and date
# Change: version: 2.1.0 → 2.2.0
# Change: updated: 2026-03-13 → 2026-03-14

# Validate
python3 << 'EOF'
import yaml
with open('skills/gambling-agent/SKILL.md') as f:
    content = f.read()
    # Quick version check
    for line in content.split('\n')[:10]:
        if 'version:' in line or 'updated:' in line:
            print(line)
EOF

# Commit changes
git add skills/gambling-agent/SKILL.md
git commit -m "Update gambling-agent to v2.2.0

- Add Dutch language support
- Improve performance for large CSV files
- Fix: Handle files >100MB correctly"

# Push to GitHub
git push origin update/gambling-agent-v2.2

# Create Pull Request via GitHub web UI or:
# gh pr create --title "Update gambling-agent v2.2.0" --body "See commit message"
```

### 2. Merge Changes

```bash
# Pull latest main
git checkout main
git pull origin main

# Merge feature branch
git merge update/gambling-agent-v2.2

# Push to main
git push origin main
```

### 3. Verify Deployment

```bash
# Check GitHub Actions status
gh run list --workflow=skill-validate.yml

# View workflow logs
gh run view <run-id>

# Verify commit
git log --oneline -3
```

## Backup & Recovery

### 1. Create Backup

```bash
# Clone mirror repository
git clone --mirror https://github.com/aigrizzly97-dot/openclaw-skills.git openclaw-skills.git

# Backup environment variables
cp .env .env.backup

# Create tarball
tar -czf openclaw-skills-backup-$(date +%Y%m%d).tar.gz \
  .git .env.backup skills/ docs/
```

### 2. Restore from Backup

```bash
# Extract backup
tar -xzf openclaw-skills-backup-20260313.tar.gz

# Restore environment
cp .env.backup .env

# Verify
git log --oneline -3
git status
```

## Troubleshooting

### Git Authentication Fails

```bash
# Test connection
ssh -T git@github.com
# Output: "Hi aigrizzly97-dot! You've successfully authenticated..."

# Or test HTTPS
git ls-remote https://github.com/aigrizzly97-dot/openclaw-skills.git

# Clear credentials (if needed)
git config --global --unset credential.helper
# Re-authenticate
git push origin main
```

### Merge Conflicts

```bash
# View conflicts
git status

# Edit conflicting files
nano skills/gambling-agent/SKILL.md

# After resolving conflicts
git add skills/gambling-agent/SKILL.md
git commit -m "Resolve merge conflict in gambling-agent SKILL.md"
git push origin main
```

### Cron Job Not Running

```bash
# Check OpenClaw status
openclaw gateway status

# Check cron job
openclaw cron status daily-skill-sync

# View logs
openclaw cron logs daily-skill-sync

# Restart OpenClaw
openclaw gateway restart

# Re-enable job
openclaw cron enable daily-skill-sync
```

### GitHub Actions Not Triggering

```bash
# Check repository settings
# Settings → Actions → Allow all actions and reusable workflows

# Verify workflow syntax
gh workflow list

# Manually trigger workflow
gh workflow run daily-commit.yml

# View run details
gh run list --workflow=daily-commit.yml
```

## Maintenance Schedule

### Daily (Automated)
- Git commits via daily-commit.yml
- Telegram feedback polling via cron

### Weekly
- Skill validation audit
- Repository statistics report
- Update documentation if needed

### Monthly
- Review feedback trends
- Update agent capabilities
- Audit GitHub token expiration

### Quarterly
- Major version updates
- Performance optimization
- Security audit

## Security Best Practices

### 1. Token Management

```bash
# Store tokens in environment variables only
export GITHUB_TOKEN=ghp_xxx

# Never commit tokens
echo ".env" >> .gitignore
git rm --cached .env 2>/dev/null
git commit -m "Remove .env from tracking"

# Rotate tokens periodically
# GitHub: Settings → Developer settings → Personal access tokens
```

### 2. Commit Signing

```bash
# Generate GPG key
gpg --gen-key

# Configure git to sign commits
git config --global user.signingkey YOUR_KEY_ID
git config --global commit.gpgsign true

# Verify signature
git log --show-signature
```

### 3. Branch Protection

In GitHub repository settings:
- Require pull request reviews (1 minimum)
- Require status checks (skill-validate, weekly-report)
- Require branches to be up to date
- Require conversation resolution

## Performance Optimization

### 1. Reduce Workflow Runtime

```yaml
# In .github/workflows/
- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}
    restore-keys: |
      ${{ runner.os }}-pip-
```

### 2. Optimize Cron Intervals

```bash
# Balance between:
# - Feedback responsiveness (shorter = more responsive)
# - System load (longer = less load)

# Current settings:
# daily-skill-sync: 0 2 * * *     (once/day at 2am)
# feedback-listener: */15 * * * *  (every 15 minutes)
# weekly-audit: 0 8 * * 1         (once/week Monday 8am)
```

## Monitoring

### 1. GitHub Insights

```bash
# Repository statistics
gh repo view --web

# Actions usage
gh run list --limit 30

# Issue/PR activity
gh issue list
gh pr list
```

### 2. OpenClaw Logs

```bash
# View recent logs
openclaw log tail -n 100

# Search for errors
openclaw log grep "ERROR"

# Export logs
openclaw log export --format csv --since "7 days ago"
```

## Documentation

- **README.md** - Repository overview
- **FEEDBACK_SYSTEM.md** - Feedback collection details
- **GITHUB-SETUP.md** - Initial setup guide
- **CRON_JOBS.md** - Cron job configuration
- **DEPLOYMENT.md** - This file

## Getting Help

### Resource Locations

- GitHub Issues: Report bugs and feature requests
- Telegram Topics: Submit feedback
- Documentation: Check each skill's SKILL.md

### Common Questions

**Q: How do I update a skill?**
A: Edit the SKILL.md file, update version/timestamp, commit and push.

**Q: When does feedback sync?**
A: Every 15 minutes via cron job, or manually via GitHub Actions.

**Q: Can I disable workflows?**
A: Yes, in GitHub repo settings → Actions → Disable for this repository.

---

**Last Updated:** 2026-03-13  
**Version:** 1.0.0  
**Maintainer:** OpenClaw Team
