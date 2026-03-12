---
name: gambling-agent
description: Specialist for generating casino/gambling affiliate websites. Creates landing pages, reviews, bonus pages, and slot game showcases with DALL-E 3 image generation, SFTP deployment to ISPManager hosting, DataForSEO keyword integration, and multilingual SEO optimization (EN/DE). Batch process 10+ domains with unique content and design variations.
triggers:
  - "gambling site"
  - "casino landing"
  - "casino review"
  - "создай казино сайт"
  - "гемблинг лендинг"
  - "casino affiliate"
version: 2.1.0
updated: 2026-03-13
---

# Gambling Agent — Casino Affiliate Site Specialist

Creates production-ready casino/gambling affiliate websites with image generation, deployment automation, and SEO optimization.

**Workspace:** `~/.openclaw/workspace/site-factory/`  
**Python:** `/opt/homebrew/bin/python3.11`  
**Node:** v22.22.1 (for image manipulation)

## Quick Start

```bash
# Ensure dependencies
pip3 install -q openpyxl pillow requests

# Generate single casino site
python3 scripts/generate_site.py \
  --niche gambling \
  --slug "casino-review-2026" \
  --language en \
  --theme roshtein-v3

# Deploy to FTP
python3 scripts/deploy_ftp.py \
  --domain beat-the-brand.de \
  --local output/gambling/casino-review-2026 \
  --remote /var/www/beat-the-brand.de/data/www/

# Validate before deploy
python3 scripts/validator.py \
  --file output/gambling/casino-review-2026/index.html \
  --niche gambling
```

## Production Environment

### Hosting Infrastructure

**ISPManager Server:** `198.37.112.93:1500`  
**Root SSH:** Port 22 (may be blocked externally — contact Vlad for admin access)  
**Databases:** SQL + file hosting

### Domain Mapping (10 Live Domains)

| Port | Folder | Domain | Status | Notes |
|------|--------|--------|--------|-------|
| 8765 | roshtein-clone | beat-the-brand.de | ✅ Live | Primary, all SEO edits tested |
| 8766 | roshtein-v2 | ice-football.de | ⏳ SFTP Ready | Shell access requested |
| 8767 | roshtein-v3 | trailschnittchen.de | ⏳ SFTP Ready | Shell access requested |
| 8768 | roshtein-v4 | startupmediaforum.de | ⏳ SFTP Ready | Shell access requested |
| 8769 | roshtein-v5 | woodenspaces.de | ⏳ SFTP Ready | Shell access requested |
| 8770 | roshtein-v6 | literaturpforte.de | ⏳ SFTP Ready | Shell access requested |
| 8771 | roshtein-v7 | zum-loewen-mainz.de | ⏳ SFTP Ready | Shell access requested |
| 8772 | roshtein-v8 | renault-stadelbauer-villingen.de | ⏳ SFTP Ready | Shell access requested |
| 8773 | roshtein-v9 | joinvolunteerio.de | ⏳ SFTP Ready | Shell access requested |
| 8774 | roshtein-v10 | hiltrud-lotze.de | ⏳ SFTP Ready | Shell access requested |

**SFTP Credentials Pattern:**
```
Host: 198.37.112.93
User: {domain}@198.37.112.93  (e.g., ice-football.de@198.37.112.93)
Pass: {domain name}  (e.g., ice-football.de)
Port: 22 (SFTP)
Path: /var/www/{domain}/data/www/
```

**Beat-the-brand.de (Primary):**
```
User: beat-the-brand.de
Pass: beat-the-brand.de
Path: /var/www/beat-the-brand.de/data/www/
```

### Local Development

```bash
# Serve site locally on port 8765
cd site-factory/output/gambling/roshtein-clone
python3 -m http.server 8765

# Open browser
open http://localhost:8765
```

### Tunneling (for preview before deploy)

**Serveo.net** (recommended, works with ISP restrictions):
```bash
ssh -R 80:localhost:8765 serveo.net
# Returns: https://[random].serveo.net
```

**Important:** Serveo gives random subdomains on free tier. For persistent URLs, use Cloudflare Tunnel.

**Cloudflare Tunnel (not working via ISP firewall currently):**
```bash
# Setup once
cloudflare tunnel create {name}
cloudflare tunnel route dns {name} {domain}

# Run tunnel
cloudflared tunnel run {name}
```

Cloudflare config saved in OpenClaw:
- Account ID: `f04024e552b883844fd6dbf723a777d9`
- Token: `env.CLOUDFLARE_TUNNEL_TOKEN` (read-only, limited permissions)

## Image Generation Workflow

### DALL-E 3 Setup

**API Key:** Required in `env.OPENAI_API_KEY`  
**Cost:** ~$0.04 per 1024×1024 image  
**Quality:** Excellent for casino/gambling graphics

### Generate Slot Images

```bash
python3 scripts/generate_images.py \
  --slots sweet-bonanza,gates-of-olympus,money-train-4 \
  --count 3 \
  --size 1024x1024 \
  --output output/gambling/{project}/images/slots/
```

**Slot Names to Use:**
- Sweet Bonanza
- Gates of Olympus
- Money Train 4
- Dead Canary
- Jammin' Jars 2
- Razor Shark
- Fruit Party
- Wanted Dead or a Wild
- Wild Swarm
- Stacked Wilds
- Plinko
- Dragon's Gold
- Fortune Tiger
- Crash Games

**Image Naming Convention:**
```
Format: {slot-name-kebab-case}.jpg
Example: sweet-bonanza.jpg, gates-of-olympus.jpg

Alt Text: {lowercase, space-separated} slot
Example: alt="sweet bonanza slot"
```

### Casino Logo Generation

```bash
python3 scripts/generate_images.py \
  --type casino-logos \
  --casinos stake,roobet,xv-casino \
  --output output/gambling/{project}/images/casinos/
```

**Standard Casinos:**
- Stake
- Roobet
- XV Casino
- N1Bet
- Holyluck
- BetMGM
- Caesars
- DraftKings

**Naming:** `{casino-name-lowercase}-logo.jpg`  
**Alt Text:** `alt="{casino name} logo"`

### Image Compression

```bash
# Compress from original DALL-E 3 to 400x400 JPEG q75
python3 scripts/compress_images.py \
  --input output/gambling/{project}/images/ \
  --size 400x400 \
  --quality 75 \
  --output output/gambling/{project}/images/compressed/
```

**Results:** 301MB → 7MB (42x reduction) with acceptable visual quality

## HTML Structure & Templates

### Page Template Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Best Online Casinos 2026 | Roshtein Casino Guide</title>
  <meta name="description" content="Top 10 online casinos reviewed...">
  <meta property="og:title" content="...">
  <meta property="og:image" content="..." width="1200" height="630">
  <style>/* inline CSS */</style>
</head>
<body>
  <!-- All assets inline (no external requests) -->
  
  <header>...</header>
  <section id="hero">...</section>
  <section id="trust-bar">...</section>
  <section id="top-casinos">...</section>
  <section id="slots">...</section>
  <section id="faq">...</section>
  <footer>...</footer>
  
  <script>/* inline JS */</script>
</body>
</html>
```

### Required Sections

#### 1. Header
- Logo
- Navigation anchors to page sections (#hero, #deals, #slots, #stream, #about)
- 18+ badge/disclaimer

#### 2. Hero Section
- H1: Primary keyword (e.g., "Roshtein Casino Streamer Guide 2026")
- Subheading: Value proposition
- CTA button: "View Top Casinos" or "Claim Bonus"
- Background image or gradient

#### 3. Trust Bar
```html
<div class="trust-bar">
  <div class="trust-item">🔒 SSL Secured</div>
  <div class="trust-item">✅ Licensed</div>
  <div class="trust-item">💰 Fast Payouts</div>
  <div class="trust-item">🎮 500+ Games</div>
</div>
```

#### 4. Casino Showcase Table
```html
<table class="casino-table">
  <thead>
    <tr>
      <th>Rank</th>
      <th>Casino</th>
      <th>Bonus</th>
      <th>Free Spins</th>
      <th>Rating</th>
      <th>Action</th>
    </tr>
  </thead>
  <tbody>
    <tr class="top-pick">
      <td><span class="rank">🥇 1</span></td>
      <td><strong>Stake Casino</strong><br><small>Since 2017 • Curacao Licensed</small></td>
      <td class="bonus">100% up to $500</td>
      <td>200 FS</td>
      <td><span class="stars">★★★★★</span> 4.9</td>
      <td><a href="#" class="btn-cta">Get Bonus</a></td>
    </tr>
    <!-- min 5 rows -->
  </tbody>
</table>
```

#### 5. Slots Carousel
```html
<section id="slots" class="slots-carousel">
  <h2>Featured Slot Games</h2>
  <div class="carousel">
    <div class="slot-card">
      <img src="images/slots/sweet-bonanza.jpg" alt="sweet bonanza slot">
      <h3>Sweet Bonanza</h3>
      <p class="rtp">RTP: 96.48%</p>
      <a href="#">Play Now</a>
    </div>
    <!-- 8-16 slots total -->
  </div>
</section>
```

#### 6. FAQ Section
```html
<section id="faq" class="faq">
  <h2>Frequently Asked Questions</h2>
  <!-- min 5 questions, schema.org FAQPage markup -->
  <div itemscope itemtype="https://schema.org/FAQPage">
    <div itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
      <h3 itemprop="name">Is online gambling legal?</h3>
      <div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer">
        <p itemprop="text">Yes, in most jurisdictions...</p>
      </div>
    </div>
  </div>
</section>
```

#### 7. Footer
```html
<footer>
  <div class="disclaimer">
    ⚠️ <strong>18+</strong> | Gambling can be addictive. Play responsibly.
  </div>
  <a href="https://www.begambleaware.org/">🔗 BeGambleAware</a>
  <small>Last updated: March 2026 | Affiliate disclosure</small>
</footer>
```

## Design System

### Color Scheme

**Dark Market (Germany):**
- Primary bg: `#0a0a0a` or `#1a1a2e`
- Accent (gold): `#ffd700` or `#f4a261`
- Neon: `#00ff88` (green) or `#ff6b6b` (red)
- Text: `#ffffff`

**Light Alternative:**
- Primary bg: `#ffffff`
- Primary accent: `#7c5ce0` (purple)
- Secondary accent: `#00ff88` (green)
- Text: `#1a1a2e`

### Typography

- **Headings:** Montserrat or Roboto (font-weight: 700)
- **Body:** Open Sans or Lato (font-weight: 400)
- **Mono:** Monaco or Inconsolata (code)

### Layout System

- **Sidebar width:** 220px (collapses on mobile < 820px)
- **Container max:** 1200px
- **Section padding:** 20px (desktop), 16px (mobile)
- **Grid gap:** 16px
- **Border radius:** 8px (cards), 0 (strict)

### Responsive Breakpoints

```css
/* Desktop: 1100px+ */
/* Tablet: 820px - 1100px */
/* Mobile: < 820px */
```

## SEO Optimization

### Required Meta Tags

```html
<title>Best Online Casinos 2026 | Top Reviews & Bonuses</title>
<!-- 50-60 chars, include keyword -->

<meta name="description" content="Expert reviews of top online casinos. Compare bonuses, games, and ratings. Find your best casino site today. Updated March 2026.">
<!-- 120-160 chars, natural language, CTA -->

<meta property="og:title" content="Best Online Casinos 2026">
<meta property="og:description" content="...">
<meta property="og:image" content="https://domain.com/images/og-image.jpg" width="1200" height="630">
<meta property="og:type" content="website">
<meta property="og:url" content="https://domain.com/">
```

### Schema.org Markup

```html
<!-- WebSite schema -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "Best Online Casinos",
  "url": "https://domain.com",
  "description": "..."
}
</script>

<!-- Review schema (for each casino) -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Review",
  "itemReviewed": {
    "@type": "LocalBusiness",
    "name": "Stake Casino",
    "url": "https://stake.com"
  },
  "reviewRating": {
    "@type": "Rating",
    "ratingValue": "4.9",
    "bestRating": "5",
    "worstRating": "1"
  },
  "author": {
    "@type": "Person",
    "name": "Casino Expert"
  },
  "reviewBody": "..."
}
</script>

<!-- FAQPage schema -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Is online gambling legal?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "..."
      }
    }
  ]
}
</script>
```

### Internal Linking

- 3-5 internal links per 500 words
- Anchor text: exact keyword or LSI variant
- Link to related page sections and other gambling posts

### Keyword Strategy

**Primary:** High-volume, commercial (100+ searches/month)  
**Secondary:** Transactional, brand anchors (Stake, Roobet, etc.)  
**Tertiary:** Informational, long-tail (how to, guide, tips)

## Deployment Workflow

### Pre-Deploy Checklist

```bash
# 1. Validate HTML
python3 scripts/validator.py --file output/gambling/project/index.html --niche gambling

# 2. Check file list
ls -la output/gambling/project/

# 3. Test responsive (desktop, tablet, mobile)
# Open browser dev tools, test 3 breakpoints

# 4. Verify all links work
grep -o 'href="[^"]*"' output/gambling/project/index.html | sort -u

# 5. Confirm images compressed
du -sh output/gambling/project/images/
```

### SFTP Deploy Steps

```bash
# Using lftp (recommended for batch operations)
lftp -u beat-the-brand.de,beat-the-brand.de sftp://198.37.112.93 <<EOF
cd /var/www/beat-the-brand.de/data/www/
mirror -R -n output/gambling/roshtein-clone/ .
quit
EOF

# Or using scp (for single file)
scp -P 22 output/gambling/roshtein-clone/index.html \
  beat-the-brand.de@198.37.112.93:/var/www/beat-the-brand.de/data/www/

# Or using Python script
python3 scripts/deploy_ftp.py \
  --domain beat-the-brand.de \
  --local output/gambling/roshtein-clone \
  --remote /var/www/beat-the-brand.de/data/www/ \
  --action upload
```

### Post-Deploy Verification

```bash
# Check live site
curl -I https://beat-the-brand.de/

# Verify sitemap.xml accessible
curl https://beat-the-brand.de/sitemap.xml

# Check robots.txt
curl https://beat-the-brand.de/robots.txt

# Monitor logs
ssh beat-the-brand.de@198.37.112.93 'tail -f /var/www/beat-the-brand.de/logs/access.log'
```

## Anchor Planning Integration

Use with **anchor-agent** for keyword targeting:

```bash
# Generate anchor plan from keywords
python3 ../seo-tz-generator/scripts/generate_anchors.py \
  --keywords roshtein,casino,slots,bonus \
  --count 14 \
  --variation-type german \
  --output anchors-roshtein.xlsx
```

**Anchor Categories:**
- Exact match: "roshtein casino"
- Phrase match: "casino roshtein"
- Brand: "roshtein", "roshtein.com"
- Generic: "best casino", "online casino"
- LSI: "casino streamer", "twitch casino"

## Error Handling & Recovery

### Common Issues

#### 1. SFTP Connection Timeout
**Problem:** "Connection timeout at /var/www/"  
**Solution:**
```bash
# Verify server firewall
ssh root@198.37.112.93 'ufw status'

# Check if SFTP service running
ssh root@198.37.112.93 'systemctl status ssh'

# Contact Vlad if firewall blocks external IP
# Workaround: Deploy from server directly
ssh beat-the-brand.de@198.37.112.93 'mkdir -p /var/www/beat-the-brand.de/data/www/'
```

#### 2. Image Generation Quota Exceeded
**Problem:** "DALL-E 3 rate limit: 429"  
**Solution:**
```bash
# Wait 60 seconds, then retry
sleep 60
python3 scripts/generate_images.py --retry-count 3

# Use pre-generated images instead
cp examples/slots/*.jpg output/gambling/project/images/slots/

# Reduce batch size
python3 scripts/generate_images.py --batch-size 2
```

#### 3. HTML Validation Score < 80%
**Problem:** 73/100 validation score  
**Solution:**
```bash
# View detailed errors
python3 scripts/validator.py --file output/gambling/project/index.html --verbose

# Common fixes:
# - Missing alt text on img tags
# - H1 count > 1
# - Missing meta description
# - Responsive viewport meta tag
```

#### 4. ISPManager Root Password Not Working
**Problem:** "blocklogon" error, multiple failed attempts  
**Solution:**
```bash
# Contact Vlad for reset
# Temporary workaround: use SFTP user account only

# For admin tasks, request shell access:
# ISPManager → Domains → {domain} → Shell access: Yes
```

### Debugging Commands

```bash
# Check HTML syntax
python3 -m html.parser output/gambling/project/index.html

# Count images
find output/gambling/project -name "*.jpg" -o -name "*.png" | wc -l

# Check file sizes
du -sh output/gambling/project/*

# Verify git status (if committed)
cd output/gambling && git status
```

## Workflow Examples

### Example 1: Generate Roshtein Landing (Germany)

```bash
python3 scripts/generate_site.py \
  --niche gambling \
  --slug roshtein-de-2026 \
  --language de \
  --theme dark-purple \
  --casinos stake,roobet,xv \
  --slots sweet-bonanza,gates-of-olympus,money-train-4 \
  --anchor-file roshtein-anchor-plan.xlsx

# Result: output/gambling/roshtein-de-2026/index.html
# Time: ~5 min (image generation optional)
```

### Example 2: Batch Generate 10 Domains

```bash
for domain in beat-the-brand.de ice-football.de trailschnittchen.de; do
  echo "Generating $domain..."
  python3 scripts/generate_site.py \
    --slug ${domain%.*} \
    --language de \
    --unique-images \
    --deploy-ready
done

# Result: 10 unique sites in output/gambling/*/
```

### Example 3: Deploy All 10 Sites

```bash
# Create deployment batch file
cat > deploy-batch.txt <<EOF
beat-the-brand.de
ice-football.de
trailschnittchen.de
startupmediaforum.de
woodenspaces.de
literaturpforte.de
zum-loewen-mainz.de
renault-stadelbauer-villingen.de
joinvolunteerio.de
hiltrud-lotze.de
EOF

# Deploy each
while read domain; do
  python3 scripts/deploy_ftp.py \
    --domain $domain \
    --local output/gambling/${domain%.*} \
    --remote /var/www/$domain/data/www/ \
    --batch-confirm
done < deploy-batch.txt
```

## Version History

### 2.1.0 (2026-03-13)
- Added Vegas Matt Casino template (EN market)
- Documented 10-domain production setup
- Added image compression pipeline (300MB → 7MB)
- Fixed responsive breakpoints for mobile (< 820px)
- Added SFTP deployment patterns for ISPManager

### 2.0.5 (2026-03-12)
- Fixed CSS spacing system (Clean Spacing + Precision Fix)
- Added hero-cards 16px margin
- Sidebar navigation refactored (anchor links only)
- SEO text section H2 → h2 (no H1 duplicate)

### 2.0.0 (2026-03-11)
- Initial gambling agent skill
- Roshtein casino template
- DALL-E 3 image generation
- Domain mapping (8 domains)

## References

- Design patterns: `references/gambling-patterns.md`
- Checklist: `site-factory/checklists/gambling.md`
- Domain mapping: `domains.md`
- Anchor plans: `roshtein-anchor-plan.xlsx`

## Support

**Issues/Feedback:**
1. Document error in `memory/YYYY-MM-DD.md`
2. Tag issue: #gambling-agent-issue
3. Open GitHub issue in openclaw-skills repo
4. Post in Telegram #gambling-agent-issues topic

**For urgent problems:** Contact Vlad (ISPManager admin)
