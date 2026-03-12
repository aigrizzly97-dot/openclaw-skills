# GitHub Skills Repository Setup Guide

Initialize and configure the OpenClaw skills repository with all three agents.

## Prerequisites

- Git installed and configured
- GitHub account with repo creation permission
- SSH key added to GitHub (or use HTTPS)
- Terminal access to workspace

## Step 1: Initialize Repository

```bash
# Navigate to workspace
cd /Users/aigrizzly/.openclaw/workspace

# Initialize git (if not already done)
git init

# Add remote
git remote add origin https://github.com/aigrizzly97-dot/openclaw-skills.git

# Create initial branch
git branch -M main
```

## Step 2: Create Repository Structure

```bash
# Create directory structure
mkdir -p skills/{gambling-agent,seo-tt-agent,anchor-agent}
mkdir -p skills/{gambling-agent,seo-tt-agent,anchor-agent}/{scripts,references,examples}
mkdir -p .github/workflows
mkdir -p .github/ISSUE_TEMPLATE
mkdir -p docs
```

## Step 3: Copy SKILL.md Files

```bash
# Copy prepared SKILL.md files to correct locations
cp gambling-agent-SKILL.md skills/gambling-agent/SKILL.md
cp seo-tt-agent-SKILL.md skills/seo-tt-agent/SKILL.md
cp anchor-agent-SKILL.md skills/anchor-agent/SKILL.md

# Create main README
cat > README.md <<'EOF'
# OpenClaw Skills Repository

Specialized agents for affiliate site generation, SEO content planning, and anchor text strategy.

## Agents

### 🎰 Gambling Agent
Creates casino/gambling affiliate landing pages with DALL-E image generation, SFTP deployment, and SEO optimization.

- **SKILL.md:** `skills/gambling-agent/SKILL.md`
- **Version:** 2.1.0
- **Status:** Production (10+ domains live)

### 📝 SEO TZ Agent
Generates technical specifications (ТЗ) for copywriters. Structures keyword research into H1/H2/H3 hierarchy with word counts and copywriter instructions.

- **SKILL.md:** `skills/seo-tt-agent/SKILL.md`
- **Version:** 1.3.0
- **Status:** Production

### 🔗 Anchor Agent
Generates SEO anchor text plans for multi-domain deployments. Creates 100% unique anchors per domain for maximum SEO diversity.

- **SKILL.md:** `skills/anchor-agent/SKILL.md`
- **Version:** 1.1.0
- **Status:** Production

## Quick Start

```bash
# Generate casino landing page
python3 skills/gambling-agent/scripts/generate_site.py \
  --niche gambling --slug "roshtein-de" --language de

# Generate SEO brief
python3 skills/seo-tt-agent/scripts/generate_tz.py \
  --input keywords.xlsx --output ~/Downloads/ТЗ.xlsx

# Generate anchor plan (10 domains, unique)
python3 skills/anchor-agent/scripts/generate_anchors.py \
  --domains beat-the-brand.de,ice-football.de,... \
  --batch-unique --output anchor-plan.xlsx
```

## Documentation

- **CONTRIBUTING.md** — How to report issues & contribute
- **docs/AGENTS_OVERVIEW.md** — Detailed agent capabilities
- **docs/WORKFLOW.md** — End-to-end workflow examples
- **docs/ERROR_HANDLING.md** — Troubleshooting guide
- **docs/FEEDBACK_SYSTEM.md** — Issue tracking & continuous improvement

## Feedback & Issues

Report issues in GitHub Issues or Telegram topics:
- `#gambling-agent-issues`
- `#seo-tt-agent-issues`
- `#anchor-agent-issues`
- `#general-feedback`

## Version History

| Agent | Version | Released | Status |
|-------|---------|----------|--------|
| gambling-agent | 2.1.0 | 2026-03-13 | Live (10 domains) |
| seo-tt-agent | 1.3.0 | 2026-03-15 | Live (5+ projects) |
| anchor-agent | 1.1.0 | 2026-03-13 | Live (batch mode) |

## Deployment

All agents are production-ready and currently running:
- **ISPManager hosting:** 198.37.112.93 (10 gambling domains)
- **DataForSEO API:** Integrated for keyword research
- **DALL-E 3:** Image generation for casino sites
- **SFTP deployment:** Automated via Python scripts

## Support

For issues, improvements, or feature requests:
1. Check existing issues: https://github.com/aigrizzly97-dot/openclaw-skills/issues
2. Open new issue with template
3. Link Telegram discussion if applicable
4. Include minimal reproducible example

## License

Proprietary. For internal use only.

---

**Last updated:** 2026-03-13  
**Maintainer:** AI Grizzly Agents team  
**Repository:** https://github.com/aigrizzly97-dot/openclaw-skills
EOF
```

## Step 4: Create Contributing Guide

```bash
cat > CONTRIBUTING.md <<'EOF'
# Contributing to OpenClaw Skills

## How to Report Issues

### Via Telegram (Quick)
Post in appropriate topic with `#github-issue` tag:
- `#gambling-agent-issues` — gambling agent problems
- `#seo-tt-agent-issues` — ТЗ generation problems
- `#anchor-agent-issues` — anchor generation problems

**Format:**
```
@agent_name [issue type]
Description of problem
Error message (if applicable)
#github-issue
```

### Via GitHub (Structured)
1. Click "Issues" tab
2. Click "New Issue"
3. Select appropriate template
4. Fill out all fields
5. Submit

## Issue Templates

### Bug Report
- **Title:** Clear, reproducible issue (e.g., "Image generation timeout on batch > 20")
- **Environment:** OS, Python version, agent version
- **Steps to reproduce:** Exact commands or actions
- **Expected vs actual:** What should happen vs what happened
- **Error message:** Full error traceback if applicable
- **Workaround:** Any temporary solution

### Feature Request
- **Title:** Clear description (e.g., "Add auto-sorting by keyword volume")
- **Problem:** Why this feature is needed
- **Solution:** Suggested implementation
- **Examples:** Use cases or mock-ups
- **Priority:** Critical / High / Medium / Low

### Documentation Improvement
- **Title:** What needs clarification (e.g., "SFTP deployment steps unclear")
- **Location:** File and section that needs update
- **Issue:** Why current docs are confusing
- **Suggestion:** How to improve

## Version Bumping

When updating a skill, bump version in `SKILL.md`:

```
Old: version: 2.0.5
New: version: 2.1.0
```

**Rules:**
- **Major (X.0.0):** Breaking changes, API changes, major features
- **Minor (0.Y.0):** New features, backward compatible
- **Patch (0.0.Z):** Bug fixes, documentation updates

## Testing Before Merge

1. Test skill locally
2. Generate sample outputs
3. Run validation scripts
4. Document any known limitations
5. Include reproducible example in PR

## Merge Process

1. Create feature branch: `git checkout -b feature/description`
2. Make changes, commit with descriptive messages
3. Push: `git push origin feature/description`
4. Create Pull Request
5. Wait for review
6. Address feedback
7. Merge to main

## Commit Messages

Format:
```
[agent/type] Short description

Longer explanation if needed.

Fixes: #issue-number
Related: #related-issue-number
```

Examples:
```
[gambling/fix] Increase image generation timeout from 30s to 60s

Fixes: #89 (DALL-E timeout on batches > 20 images)

[seo-tt/feat] Auto-sort sections by search volume

New flag: --sort-by-volume (enabled by default)
Fixes: #102

[anchor/docs] Add troubleshooting guide for DataForSEO API errors

Includes common 429 errors and rate-limit workarounds
```

## Branch Strategy

```
main
├── gambling-agent (active development)
├── seo-tt-agent (active development)
├── anchor-agent (active development)
└── feedback-sync (Telegram integration)
```

**Workflow:**
1. Create feature branch from agent's branch
2. Work & test locally
3. Create PR against agent's branch
4. Merge to agent branch when approved
5. Agent branch auto-syncs to main (weekly or on-demand)

## Code Style

- Python: PEP 8 (4 spaces, max 100 chars)
- Bash: shellcheck compatible
- HTML/CSS: Inline (no external imports)
- Comments: Clear, concise English

## Documentation Requirements

Every change should include:
- Updated SKILL.md (if affects usage)
- CHANGELOG entry (if noteworthy)
- Code comments (if complex logic)
- Usage example (if new feature)

## Performance Considerations

- Image generation: Use compression (400x400 JPEG @ q75)
- API calls: Implement backoff & caching
- Large batches: Add progress indicators
- Database queries: Document optimization

## Security

- ❌ Never commit API keys, passwords, credentials
- ✅ Use environment variables for secrets
- ✅ Document credential setup in SKILL.md
- ✅ Validate user input (no shell injection)

## Questions?

1. Check existing issues & documentation
2. Ask in Telegram `#general-feedback`
3. Open discussion as GitHub issue
EOF
```

## Step 5: Create GitHub Issue Templates

```bash
cat > .github/ISSUE_TEMPLATE/bug.md <<'EOF'
---
name: Bug Report
about: Report a reproducible bug
title: "[Bug] "
labels: type:bug, status:needs-investigation
assignees: ''

---

## Agent
- [ ] gambling-agent
- [ ] seo-tt-agent
- [ ] anchor-agent

## Environment
- OS: [e.g., macOS 14.2]
- Python: [output of `python3 --version`]
- Agent version: [e.g., 2.1.0]

## Steps to Reproduce
```bash
# Exact commands to reproduce
python3 scripts/...
```

## Expected Behavior
What should happen

## Actual Behavior
What actually happened

## Error Message
```
Full error traceback or message
```

## Workaround
Any temporary solution if available

## Additional Context
Screenshots, logs, or other relevant info
EOF

cat > .github/ISSUE_TEMPLATE/feature.md <<'EOF'
---
name: Feature Request
about: Suggest an enhancement
title: "[Enhancement] "
labels: type:enhancement
assignees: ''

---

## Problem
What problem does this solve?

## Proposed Solution
How should this work?

## Examples
```bash
# Usage example
python3 scripts/... --new-flag
```

## Priority
- [ ] Critical (blocks workflow)
- [ ] High (important improvement)
- [ ] Medium (nice to have)
- [ ] Low (cosmetic)

## Additional Context
Related issues, competitor examples, etc.
EOF

cat > .github/ISSUE_TEMPLATE/feedback.md <<'EOF'
---
name: General Feedback
about: Share feedback or suggestions
title: ""
labels: type:feedback
assignees: ''

---

## Category
- [ ] Documentation unclear
- [ ] Confusing error message
- [ ] Could be faster
- [ ] Would like to see...
- [ ] Other

## Description
Your feedback here

## Context
Where did you encounter this? What were you trying to do?

## Suggested Improvement
(Optional) How could this be better?
EOF
```

## Step 6: Create GitHub Actions Workflows

```bash
cat > .github/workflows/validate-skills.yml <<'EOF'
name: Validate Skills

on:
  push:
    branches: [main, '*/fix/*', '*/feature/*']
  pull_request:
    branches: [main]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Validate SKILL.md format
        run: |
          for skill in skills/*/SKILL.md; do
            echo "Checking $skill..."
            python3 -c "
            import re
            with open('$skill') as f:
              content = f.read()
            # Check frontmatter
            assert content.startswith('---'), 'Missing YAML frontmatter'
            # Check required fields
            for field in ['name:', 'description:', 'version:', 'updated:']:
              assert field in content, f'Missing {field}'
            "
          done
      
      - name: Check referenced scripts exist
        run: |
          python3 << 'PYTHON'
          import os
          import re
          from pathlib import Path
          
          for skill_dir in Path('skills').glob('*-agent'):
            skill_file = skill_dir / 'SKILL.md'
            with open(skill_file) as f:
              content = f.read()
            
            # Find all script references
            scripts = re.findall(r'scripts/(\w+\.py)', content)
            for script in set(scripts):
              script_path = skill_dir / 'scripts' / script
              if not script_path.exists():
                raise FileNotFoundError(f'Missing script: {script_path}')
            
            print(f'✅ {skill_dir.name}: All {len(set(scripts))} scripts found')
          PYTHON
      
      - name: Validate JSON examples
        run: |
          python3 << 'PYTHON'
          import json
          from pathlib import Path
          
          for json_file in Path('skills').rglob('*.json'):
            try:
              with open(json_file) as f:
                json.load(f)
              print(f'✅ {json_file}: Valid JSON')
            except json.JSONDecodeError as e:
              raise ValueError(f'Invalid JSON in {json_file}: {e}')
          PYTHON
EOF

cat > .github/workflows/test-agents.yml <<'EOF'
name: Test Agents

on:
  push:
    branches: [main, '*/test/*']

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        agent: [gambling-agent, seo-tt-agent, anchor-agent]
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: |
          pip install -q openpyxl pillow requests
      
      - name: Test ${{ matrix.agent }}
        run: |
          echo "Testing ${{ matrix.agent }}..."
          # Add test commands here when scripts are available
          # python3 skills/${{ matrix.agent }}/scripts/validate.py
EOF
```

## Step 7: Create Documentation

```bash
# Create docs directory structure
mkdir -p docs

cat > docs/AGENTS_OVERVIEW.md <<'EOF'
# Agents Overview

## Architecture

Three specialized agents work together in the site factory pipeline:

```
User Request
    ↓
Orchestrator
    ├─→ Gambling Agent (casino sites)
    ├─→ SEO TZ Agent (content briefs)
    └─→ Anchor Agent (SEO anchors)
    ↓
Deployment
```

## Agent Capabilities Matrix

| Feature | Gambling | SEO TZ | Anchor |
|---------|----------|--------|--------|
| DALL-E image gen | ✅ | ❌ | ❌ |
| HTML generation | ✅ | ❌ | ❌ |
| Excel export | ❌ | ✅ | ✅ |
| DataForSEO API | ✅ | ✅ | ✅ |
| Multi-domain support | ✅ | ❌ | ✅ |
| SFTP deployment | ✅ | ❌ | ❌ |
| Batch processing | ✅ | ✅ | ✅ |

## Current Production Usage

- **Gambling Agent:** 10 active domains, ~140 casino pages total
- **SEO TZ Agent:** 5+ active projects, ~50 briefs generated
- **Anchor Agent:** 10-domain batch campaigns, ~1400 unique anchors

---
EOF

cat > docs/WORKFLOW.md <<'EOF'
# End-to-End Workflow Examples

## Scenario 1: Launch New Casino Site (Gambling Agent)

1. **Plan**
   - Define niche (gambling)
   - Select theme (roshtein, vegasmatt, etc.)
   - Choose domain
   - Plan content (casinos, slots, bonuses)

2. **Generate**
   ```bash
   python3 gambling-agent/scripts/generate_site.py \
     --slug roshtein-fr-2026 \
     --language fr \
     --theme roshtein-v11
   ```

3. **Generate Images**
   ```bash
   python3 gambling-agent/scripts/generate_images.py \
     --slots sweet-bonanza,gates-of-olympus \
     --count 16 \
     --output images/
   ```

4. **Validate**
   ```bash
   python3 gambling-agent/scripts/validator.py \
     --file output/index.html
   ```

5. **Deploy**
   ```bash
   python3 gambling-agent/scripts/deploy_ftp.py \
     --domain roshtein-fr.example.com \
     --local output/ \
     --remote /var/www/roshtein-fr/
   ```

6. **Monitor**
   - Check site loads
   - Verify all links work
   - Confirm images display

## Scenario 2: Create SEO Brief for Copywriter

1. **Gather Keywords**
   - Export from DataForSEO or paste list
   - ~10-20 keywords minimum

2. **Generate ТЗ**
   ```bash
   python3 seo-tt-agent/scripts/generate_tz.py \
     --input keywords.xlsx \
     --niche gambling \
     --language en \
     --output ТЗ_casino_brief.xlsx
   ```

3. **Review Structure**
   - H1 with primary keyword
   - 5 H2 sections covering keyword family
   - FAQ section with remaining keywords

4. **Send to Copywriter**
   - Include SKILL.md instructions
   - Note any compliance requirements
   - Set deadline

5. **Review Article**
   - Check keyword placement
   - Verify word counts met
   - Quality assurance

## Scenario 3: Multi-Domain Anchor Strategy (Anchor Agent)

1. **Define Keywords**
   - Primary: "roshtein" or "best online casinos"
   - Secondary: Related variations

2. **Generate Anchors**
   ```bash
   python3 anchor-agent/scripts/generate_anchors.py \
     --domains beat-the-brand.de,ice-football.de,...(10 total) \
     --keyword roshtein \
     --batch-unique \
     --language de \
     --output roshtein-10-domain-anchors.xlsx
   ```

3. **Verify Uniqueness**
   ```bash
   python3 anchor-agent/scripts/validate_anchor_uniqueness.py \
     --input roshtein-10-domain-anchors.xlsx
   ```

4. **Apply to Sites**
   - Open Excel file (one sheet per domain)
   - Copy anchors from Column A for each domain
   - Update HTML navigation menus
   - Add internal links using anchors

5. **Deploy & Monitor**
   - Deploy all 10 domains
   - Monitor rankings for targeted keywords
   - Adjust anchor strategy based on CTR

---
EOF

cat > docs/ERROR_HANDLING.md <<'EOF'
# Troubleshooting Guide

## Common Issues & Solutions

### Gambling Agent

#### DALL-E Image Timeout
**Error:** "Request timeout > 60s"
**Solution:**
- Reduce batch size (max 5 concurrent)
- Add delay between requests: `--request-delay 2`

#### SFTP Connection Failed
**Error:** "Connection timeout at server"
**Solution:**
- Check ISP firewall (may block port 22)
- Use serveo.net for preview instead
- Contact Vlad for firewall whitelist

#### HTML Validation < 80%
**Error:** "Validation score: 73/100"
**Solution:**
- Check for duplicate H1 tags
- Add alt text to all images
- Verify responsive meta viewport

### SEO TZ Agent

#### Keyword List Too Short
**Error:** "Need 5+ keywords for ТЗ"
**Solution:**
- Use DataForSEO related keywords
- Add LSI variations
- Expand with competitor keywords

#### Excel Export Fails
**Error:** "openpyxl not found"
**Solution:**
```bash
pip3 install openpyxl
```

### Anchor Agent

#### Anchor Duplication Detected
**Error:** "3 duplicate anchors found across domains"
**Solution:**
```bash
python3 scripts/generate_anchors.py \
  --domains ... \
  --uniqueness-mode full \
  --force-unique  # Retry with maximum variation
```

#### DataForSEO API 429
**Error:** "Too Many Requests"
**Solution:**
```bash
python3 scripts/generate_anchors.py \
  --keyword ... \
  --request-delay 3 \  # Increase delay
  --cache /tmp/cache.json  # Use cache
```

---
EOF
```

## Step 8: Commit & Push

```bash
# Stage all files
git add .

# Create initial commit
git commit -m "Initial commit: gambling, seo-tt, anchor agents with full documentation"

# Push to GitHub
git push -u origin main

# Create agent branches
git branch gambling-agent
git branch seo-tt-agent
git branch anchor-agent

git push origin gambling-agent seo-tt-agent anchor-agent
```

## Step 9: GitHub Configuration

1. **Go to repository settings:** https://github.com/aigrizzly97-dot/openclaw-skills/settings

2. **Enable branches protection:**
   - Go to "Branches"
   - Add rule for `main`
   - Require pull request reviews
   - Require status checks to pass

3. **Configure labels:**
   ```bash
   # Run script to create standard labels
   python3 scripts/create_github_labels.py
   ```

4. **Set up GitHub Actions:**
   - Actions should auto-detect workflows
   - Workflows run on push/PR
   - View results in "Actions" tab

## Step 10: Connect Telegram Feedback (Optional)

To auto-create GitHub issues from Telegram:

```bash
# Create Telegram bot webhook
python3 scripts/setup_telegram_webhook.py \
  --bot-token YOUR_BOT_TOKEN \
  --github-token GITHUB_TOKEN \
  --repo aigrizzly97-dot/openclaw-skills
```

See `docs/FEEDBACK_SYSTEM.md` for full details.

## Verification

```bash
# Verify repo structure
tree -L 2 -I 'node_modules|.git'

# Verify all SKILL.md files exist
find skills -name "SKILL.md" -exec echo "✅ {}" \;

# Verify GitHub Actions workflows
ls -la .github/workflows/*.yml

# Test git workflow
git log --oneline  # Should show initial commit
git branch -a      # Should show main + 3 agent branches
```

## Next Steps

1. Clone repo locally (optional): `git clone https://github.com/aigrizzly97-dot/openclaw-skills`
2. Create feature branches for improvements
3. Set up Telegram feedback sync (docs/FEEDBACK_SYSTEM.md)
4. Configure CI/CD secrets in GitHub (API keys, if needed)
5. Start using agents and reporting feedback!

## Support

For questions:
- Check `README.md` in repo
- Review `CONTRIBUTING.md`
- Post in Telegram `#general-feedback`
- Open GitHub discussion
