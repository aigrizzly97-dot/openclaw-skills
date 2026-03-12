# OpenClaw Skills Repository

Production-ready agent skills for OpenClaw platform with automated feedback integration and version management.

## 📁 Repository Structure

```
openclaw-skills/
├── skills/                          # Agent skill implementations
│   ├── gambling-agent/              # Casino/gambling affiliate specialist
│   │   └── SKILL.md                # Skill definition & documentation
│   ├── seo-tz-agent/               # SEO technical specification generator
│   │   └── SKILL.md
│   └── anchor-agent/               # Anchor text analysis specialist
│       └── SKILL.md
├── docs/                            # Documentation
│   ├── FEEDBACK_SYSTEM.md          # Feedback collection & processing
│   ├── GITHUB-SETUP.md             # Repository setup guide
│   ├── CRON_JOBS.md                # OpenClaw cron configuration
│   └── DEPLOYMENT.md               # Deployment procedures
├── .github/workflows/               # GitHub Actions automation
│   ├── daily-commit.yml            # Daily skill sync
│   ├── feedback-sync.yml           # Telegram feedback integration
│   ├── skill-validate.yml          # SKILL.md validation
│   └── weekly-report.yml           # Weekly update reports
└── .env.example                     # Environment configuration template
```

## 🚀 Quick Start

### 1. Clone Repository
```bash
git clone https://github.com/aigrizzly97-dot/openclaw-skills.git
cd openclaw-skills
```

### 2. Configure Environment
```bash
cp .env.example .env
# Edit .env with your GitHub token and Telegram credentials
```

### 3. Verify Skills
```bash
python3 .github/scripts/validate-skills.py
```

## 🤖 Agents

### Gambling Agent
Casino and gambling affiliate website generator with:
- Casino landing pages, reviews, and bonus guides
- DALL-E 3 slot game image generation
- DataForSEO keyword integration
- SFTP deployment to ISPManager hosting
- Multi-language SEO optimization (EN/DE)

**Triggers:** `gambling site`, `casino landing`, `casino review`  
**Version:** 2.1.0

### SEO TZ Agent
Technical specification (ТЗ) generator for copywriters:
- Keyword research via DataForSEO
- H1/H2/H3 structure generation
- Competitive analysis
- Content brief with word counts
- XLSX export for copywriters

**Triggers:** `сео тз`, `техзадание`, `content brief`  
**Version:** 2.0.0

### Anchor Agent
Anchor text analysis and optimization:
- Link anchor text detection
- SEO metrics analysis
- Anchor text diversification
- Competitor anchor analysis

**Triggers:** `anchor`, `anchor text`, `link analysis`  
**Version:** 1.5.0

## 📋 Workflows

### Daily Commit (02:00 UTC)
Automatically commits changes to skill files with timestamp. Runs daily to ensure all agent updates are versioned.

### Feedback Sync (Every 6 hours)
Syncs feedback from Telegram topics to GitHub Issues:
- Parses natural language feedback
- Creates issues with parsed suggestions
- Updates SKILL.md based on feedback patterns
- Links to original Telegram messages

### Skill Validation (On Push)
Validates SKILL.md format on every push:
- Checks required frontmatter fields
- Validates YAML syntax
- Ensures section headers exist
- Prevents invalid skill definitions

### Weekly Report (Mondays 08:00 UTC)
Generates comprehensive update report:
- Skill version status
- Repository statistics
- Recent changes summary
- Contributor information

## 🔧 Configuration

### Environment Variables
See `.env.example` for required variables:
- `GITHUB_TOKEN` - For GitHub API operations
- `TELEGRAM_TOKEN` - For Telegram bot integration
- `TELEGRAM_CHAT_ID` - Feedback channel ID
- `GIT_USER_EMAIL` - Commit author email
- `GIT_USER_NAME` - Commit author name

### Git Configuration
```bash
git config --global user.email "agent@openclaw.local"
git config --global user.name "OpenClaw Agent"
```

## 📝 Feedback System

### Telegram Integration
Each agent listens to feedback in its topic:
- **#gambling-agent-feedback** - Gambling agent improvements
- **#seo-tz-agent-feedback** - SEO TZ generator feedback
- **#anchor-agent-feedback** - Anchor analysis improvements

### Feedback Processing
Feedback is automatically:
1. Parsed for improvement suggestions
2. Created as GitHub issues
3. Used to update SKILL.md documentation
4. Committed with reference to source message

### Feedback Format (Variant C - Natural Criticism)
```
"The agent should also support Dutch language. It's slow with large CSV files too."

Parsed as:
- Feature request: Add Dutch language support
- Bug/improvement: Performance optimization for large files
```

## 🔗 Integration Points

### OpenClaw Cron Jobs
The system uses OpenClaw's built-in cron scheduling:
- Daily skill sync (coordinates with GitHub workflow)
- Telegram feedback listener (reads topics, parses feedback)
- Weekly skill audit (validates all agents are functional)

### Telegram Message Reference
Each commit includes Telegram message ID for traceability:
```
Daily skill sync: 2026-03-13 02:00:00 UTC
Related: TG message #12345 from #gambling-agent-feedback
```

## 📊 Monitoring

### Check Workflow Status
```bash
# View recent commits
git log --oneline -10

# Check skill versions
for dir in skills/*/; do
  echo "$(basename $dir):"
  grep "^version:" $dir/SKILL.md
done
```

### View GitHub Actions
Visit: https://github.com/aigrizzly97-dot/openclaw-skills/actions

## 🛠️ Maintenance

### Manual Skill Update
```bash
# Edit skill file
nano skills/gambling-agent/SKILL.md

# Update version and timestamp
# Commit changes
git add skills/
git commit -m "Update gambling-agent: Add Dutch language support"
git push origin main
```

### Validate Before Commit
```bash
python3 .github/scripts/validate-skills.py
```

## 📚 Documentation

- **[FEEDBACK_SYSTEM.md](docs/FEEDBACK_SYSTEM.md)** - Feedback collection architecture
- **[GITHUB-SETUP.md](docs/GITHUB-SETUP.md)** - Repository setup details
- **[CRON_JOBS.md](docs/CRON_JOBS.md)** - OpenClaw cron configuration
- **[DEPLOYMENT.md](docs/DEPLOYMENT.md)** - Deployment procedures

## 🔐 Security

- GitHub tokens stored in repository secrets
- Environment variables in `.env` (not committed)
- Agent credentials managed via OpenClaw secure storage
- All commits signed with verified identity

## 📈 Version Management

Skills follow semantic versioning:
- **MAJOR.MINOR.PATCH** (e.g., 2.1.0)
- Updates tracked in SKILL.md frontmatter
- Git history provides full audit trail

## 🤝 Contributing

1. Skills are managed by their respective agents
2. Manual updates: follow the validation process
3. Feedback integration: submit via Telegram topics
4. All changes require proper commit messages

## 📞 Support

For questions about specific skills, check their individual SKILL.md files.  
For feedback integration issues, see FEEDBACK_SYSTEM.md.

---

**Last Updated:** 2026-03-13  
**Repository:** https://github.com/aigrizzly97-dot/openclaw-skills
