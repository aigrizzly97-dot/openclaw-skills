---
name: anchor-agent
description: Generates SEO anchor text plans for multi-domain deployments. Fetches keyword data from DataForSEO API, generates unique anchor variations per domain (100% unique across all domains), exports production-ready Excel files for navigation menu planning. Supports language-specific anchors (DE, EN, RU), brand variations, and tie-in with gambling/dating site generation. Used for 10+ domain deployments with custom anchor strategy per domain.
triggers:
  - "generate anchors"
  - "anchor plan"
  - "link anchors"
  - "создай анкоры"
  - "anchor text"
  - "navigation menu"
version: 1.1.0
updated: 2026-03-13
---

# Anchor Agent — SEO Anchor Text Specialist

Generates keyword-based anchor text plans for multi-domain SEO deployments. Creates 100% unique anchor variations per domain for navigation menus, internal linking, and backlink strategy.

**Workspace:** `~/.openclaw/workspace/site-factory/`  
**Python:** `/opt/homebrew/bin/python3.11`  
**Dependencies:** `openpyxl`, `requests` (DataForSEO API)  
**DataForSEO Login:** `borys.kutsenko97@gmail.com / edf0c28d85c68298`

## Quick Start

```bash
# Generate anchor plan for single domain
python3 scripts/generate_anchors.py \
  --primary-keyword roshtein \
  --secondary-keywords casino,slots,bonus \
  --domain beat-the-brand.de \
  --language de \
  --count 14 \
  --output roshtein-anchor-plan.xlsx

# Generate for 10 domains (each gets unique anchors)
python3 scripts/generate_anchors.py \
  --primary-keyword roshtein \
  --domains beat-the-brand.de,ice-football.de,trailschnittchen.de \
  --batch-mode \
  --language de \
  --count 14 \
  --output roshtein-anchor-plan-batch.xlsx
```

## Workflow

### Step 1: Define Keywords

Required inputs:
- **Primary keyword:** Main brand/topic (e.g., "roshtein", "best online casinos")
- **Secondary keywords:** Related searches (e.g., "casino", "slots", "bonus")
- **Niche:** gambling, dating, transportation, ecommerce, etc.
- **Language:** DE (German), EN (English), RU (Russian)
- **Target markets:** For language variants

### Step 2: Fetch Keyword Data (DataForSEO)

```bash
python3 scripts/fetch_keywords.py \
  --keyword roshtein \
  --language de \
  --region germany \
  --output /tmp/roshtein-keywords.json

# Returns:
# {
#   "keyword": "roshtein",
#   "volume": 150,
#   "difficulty": 45,
#   "cpc": 2.34,
#   "related_keywords": [
#     {"keyword": "roshtein casino", "volume": 89},
#     {"keyword": "roshtein stream", "volume": 67},
#     {"keyword": "roshtein slots", "volume": 45}
#   ]
# }
```

### Step 3: Generate Anchor Variations

Strategy:
1. **Exact match:** Keyword as-is
2. **Phrase match:** Keyword + related word
3. **Brand match:** Stripped keyword or nickname
4. **Generic:** Related terms without exact keyword
5. **LSI:** Semantic variation (synonyms, long-tail)

```bash
python3 scripts/generate_anchors.py \
  --keyword roshtein \
  --secondary-keywords casino,slots,stream,bonus \
  --variation-count 14 \
  --variation-types exact,phrase,brand,generic,lsi \
  --language de \
  --output /tmp/anchors.json
```

**Example output:**
```json
{
  "primary_keyword": "roshtein",
  "language": "de",
  "anchors": [
    {
      "anchor": "Roshtein",
      "type": "brand",
      "target": "Home or brand landing page",
      "order": 1
    },
    {
      "anchor": "Roshtein Casino",
      "type": "exact",
      "target": "/reviews/roshtein-casino",
      "order": 2
    },
    {
      "anchor": "Roshtein Streamer",
      "type": "phrase",
      "target": "/articles/roshtein-streamer",
      "order": 3
    },
    {
      "anchor": "Casino mit Roshtein",
      "type": "phrase",
      "target": "/guides/casinos",
      "order": 4
    },
    {
      "anchor": "Die besten Roshtein Slot-Spiele",
      "type": "long-tail",
      "target": "/slots",
      "order": 5
    }
  ]
}
```

### Step 4: Ensure Uniqueness Across Domains

**Critical for multi-domain deployments:** Each domain gets 100% unique anchors.

```bash
# Generate for multiple domains
python3 scripts/generate_anchors.py \
  --keyword roshtein \
  --domains beat-the-brand.de,ice-football.de,trailschnittchen.de \
  --count 14 \
  --uniqueness-mode full \
  --output roshtein-anchor-plan-all-domains.xlsx

# Output Excel file with sheets per domain:
# Sheet 1: beat-the-brand.de (14 anchors)
# Sheet 2: ice-football.de (14 different anchors)
# Sheet 3: trailschnittchen.de (14 different anchors)
# ...
```

### Step 5: Export to Excel

```bash
python3 scripts/export_anchors_to_xlsx.py \
  --input /tmp/anchors.json \
  --domains beat-the-brand.de,ice-football.de \
  --output roshtein-anchor-plan.xlsx \
  --format production
```

**Output columns:**
- Domain
- Order (1–14)
- Anchor Text
- Type (Brand / Exact / Phrase / Generic / LSI)
- Target Page/Section
- Search Volume (from DataForSEO)
- Difficulty
- Notes

### Step 6: Apply to Navigation Menu

```html
<!-- Example: beat-the-brand.de navigation -->
<nav class="sidebar">
  <ul>
    <li><a href="/">Roshtein</a></li>
    <li><a href="/casino">Roshtein Casino</a></li>
    <li><a href="/stream">Roshtein Stream</a></li>
    <li><a href="/slots">Slot Spiele</a></li>
    <li><a href="/bonus">Casino Bonus</a></li>
    <!-- ... 9 more from Excel -->
  </ul>
</nav>

<!-- ice-football.de gets completely different anchors: -->
<nav class="sidebar">
  <ul>
    <li><a href="/">Top Casino Portal</a></li>
    <li><a href="/casino">Online Casino Deutschlands</a></li>
    <li><a href="/stream">Live Casino Stream</a></li>
    <!-- ... -->
  </ul>
</nav>
```

## Anchor Categories & Best Practices

### 1. Brand Anchors (10–15%)
**Purpose:** Build brand recognition  
**Examples (Roshtein niche):**
- Roshtein
- roshtein.com
- Der Roshtein Casino Streamer

**Rules:**
- Use exact brand name
- 1–2 per domain
- Always link to home or main landing page

### 2. Exact Match (20–30%)
**Purpose:** Signal keyword relevance  
**Examples:**
- roshtein casino
- roshtein slots
- roshtein stream

**Rules:**
- Use keyword exactly
- Link to relevant page section
- Spread across navigation items
- Don't overuse (max 3–4 per domain)

### 3. Phrase Match (20–30%)
**Purpose:** Variation, context  
**Examples:**
- casino mit roshtein
- beste roshtein casino
- roshtein slots zum spielen

**Rules:**
- Add supporting word before/after keyword
- Natural language, not forced
- Link to related section
- 3–5 per domain

### 4. Generic / Category (20–30%)
**Purpose:** Diversify anchor profile  
**Examples:**
- Online Casino
- Casino Spiele
- Slot Maschinen

**Rules:**
- No keyword in anchor
- Still relevant to page
- 3–5 per domain
- Different per domain to avoid footprint

### 5. LSI / Semantic (10–15%)
**Purpose:** Natural variation, semantic relevance  
**Examples (Roshtein):**
- Streaming Casino Plattform
- Große Casino Auswahl
- Die besten Glücksspiele online

**Rules:**
- Synonyms, related concepts
- Natural language
- Supports semantic SEO
- 1–3 per domain

### Distribution Example (14 anchors for one domain)

| Order | Anchor | Type | % | Target |
|-------|--------|------|---|--------|
| 1 | Roshtein | Brand | 7% | Home |
| 2 | Roshtein Casino | Exact | 7% | /reviews |
| 3 | Roshtein Slots | Exact | 7% | /slots |
| 4 | Roshtein Stream | Exact | 7% | /stream |
| 5 | Online Casino Deutschland | Generic | 7% | /casinos |
| 6 | Beste Casino Spiele | Generic | 7% | /games |
| 7 | Casino mit Bonus | Generic | 7% | /promotions |
| 8 | Die besten Roshtein Casinos | Phrase | 7% | /top-casinos |
| 9 | Roshtein Casino Streamer Guide | Phrase | 7% | /guide |
| 10 | Casino Angebote für Spieler | Phrase | 7% | /offers |
| 11 | Sicheres Online Glücksspiel | LSI | 7% | /safety |
| 12 | Premium Casino Auswahl | LSI | 7% | /premium |
| 13 | Spielautomaten kostenlos testen | LSI | 7% | /free-play |
| 14 | Top-bewertete Spielplattformen | LSI | 7% | /reviews |

## Language-Specific Rules

### German (DE) Anchors

**Guidelines:**
- Capitalize nouns: "Online Casino" not "online casino"
- Use compound words: "Casino-Streaming", "Slot-Spiele"
- Gender-neutral when possible: "Spieler" (player), not "Spielerin"
- Regional variants: "Glücksspiel" (Germany), not "Spiel" alone

**Examples:**
- Bestes Online Casino Deutschland
- Die größten Slot-Spiele 2026
- Casino-Streaming-Portal
- Sicheres Spielen online

### English (EN) Anchors

**Guidelines:**
- Title case for headings: "Best Online Casinos", not "best online casinos"
- Avoid hyphens unless compound: "Best Casinos", not "Best-Casinos"
- Use contractions naturally: "We've tested", not "We have tested"
- Numbers in text: "Top 10", "2026"

**Examples:**
- Best Online Casinos
- Top Roshtein Slots
- Casino Streaming Guide
- Safe Gambling Tips

### Russian (RU) Anchors

**Guidelines:**
- Use Cyrillic, proper case
- Avoid Latin characters mixed with Cyrillic
- Gender agreement matters: "лучший казино" (m), "лучшая игра" (f)
- Common suffixes: -ный, -ский, -ение

**Examples:**
- Лучшие онлайн казино
- Топ слоты Roshtein
- Безопасная игра онлайн
- Рейтинг казино 2026

## Multi-Domain Uniqueness Strategy

### Problem
Using same anchors on all 10 domains looks like a footprint:
```
Domain 1: "Best Online Casinos" → Section 1
Domain 2: "Best Online Casinos" → Section 1
Domain 3: "Best Online Casinos" → Section 1
... (all 10 identical)
```

Google sees this as artificial linking pattern. **Solution: Complete diversity.**

### Solution: Domain-Specific Anchors

Each domain gets 14 unique anchors, nothing repeated:

**Domain 1 (beat-the-brand.de):**
1. Roshtein
2. Roshtein Casino
3. Slot Spiele
4. Online Casinos
5. Casino Bonus
6. beste roshtein casino
7. roshtein livestream
8. casino mit freispielen
9. deutsches online casino
10. top slot games
11. casino angebote
12. roshtein casino test
13. online spielautomaten
14. sicheres glücksspiel

**Domain 2 (ice-football.de):** (completely different)
1. Top Casino Portal
2. Online Spielplattform
3. Casino Games 2026
4. Streaming Casinos
5. Neue Casino Bonuse
6. die besten casinos deutschland
7. live casino spielen
8. casino adrenalin
9. sichere spielbanken online
10. virtuelle spielautomaten
11. glücksspiel mit lizenz
12. casino-streaming guide
13. beste casino-apps
14. spielbank online bewertungen

**Domain 3 (trailschnittchen.de):** (unique again)
1. Casino Streamer
2. Beliebte Casinos
3. Spiele mit Gewinn
4. Echtgeld Casinos
5. Casino Freispiele
... (14 completely different)

### Generation Command

```bash
python3 scripts/generate_anchors.py \
  --primary-keyword roshtein \
  --domains beat-the-brand.de,ice-football.de,trailschnittchen.de,... \
  --batch-mode \
  --uniqueness full \
  --language de \
  --count 14 \
  --output roshtein-10-domain-anchor-plan.xlsx

# Result: Excel with 10 sheets (one per domain)
# Each sheet has 14 UNIQUE anchors
```

### Verify Uniqueness

```bash
python3 scripts/validate_anchor_uniqueness.py \
  --input roshtein-10-domain-anchor-plan.xlsx \
  --output anchor-validation-report.txt

# Report shows:
# ✅ Domain 1: 14/14 unique globally
# ✅ Domain 2: 14/14 unique globally
# ✅ Domain 3: 14/14 unique globally
# ...
# Total unique anchors: 140 (no duplicates)
```

## Workflow Examples

### Example 1: Roshtein Casino (German) — 10 Domains

**Input:**
```
Primary: roshtein
Secondary: casino, slots, stream, bonus
Language: DE
Domains: 10 (from domains.md)
Count per domain: 14
```

**Command:**
```bash
python3 scripts/generate_anchors.py \
  --keyword-file roshtein-keywords.json \
  --domains-file domains.md \
  --language de \
  --batch-unique \
  --output roshtein-anchor-plan.xlsx
```

**Output:** Excel file
- 10 sheets (one per domain)
- 14 anchors per sheet
- 140 total unique anchors
- All keyword variations covered
- Ready to paste into HTML navigation

**Time:** ~5 minutes (includes DataForSEO API calls)

### Example 2: Vegas Matt Casino (English)

**Input:**
```
Primary: vegas matt
Secondary: casino, slots, streaming, free play
Language: EN
Domains: 1 (vegasmatt.casino)
Count: 14
```

**Command:**
```bash
python3 scripts/generate_anchors.py \
  --keyword "vegas matt" \
  --secondary-keywords casino,slots,streaming \
  --language en \
  --count 14 \
  --output vegasmatt-anchors.xlsx
```

**Output Anchors:**
1. Vegas Matt
2. Vegas Matt Casino
3. Vegas Matt Slots
4. Vegas Matt Streaming
5. Best Vegas Casinos
6. Online Casinos USA
7. Casino Slots Games
8. Free Casino Play
9. Live Casino Streaming
10. Vegas Casino Reviews
11. Top Slot Games 2026
12. Safe Online Gambling
13. Casino Bonus Offers
14. Live Dealer Games

### Example 3: Multiple Niches in One Plan

Generate anchors for gambling + dating sites (different anchor styles):

```bash
python3 scripts/generate_anchors.py \
  --multi-niche \
  --niche1 gambling --keyword1 "roshtein casino" \
  --niche2 dating --keyword2 "best dating apps" \
  --language en \
  --output multi-niche-anchors.xlsx

# Output has 2 sheets:
# Sheet 1: Gambling anchors (casino, slots, bonus)
# Sheet 2: Dating anchors (dating apps, singles, matches)
```

## Error Handling

### Common Issues

#### 1. DataForSEO API Rate Limit
**Problem:** "429 Too Many Requests"  
**Solution:**
```bash
python3 scripts/generate_anchors.py \
  --keyword roshtein \
  --dataseo-request-delay 2 \  # 2 sec between requests
  --output anchors.xlsx

# Or use cached data
python3 scripts/generate_anchors.py \
  --keyword roshtein \
  --use-cache /tmp/keyword-cache.json \
  --output anchors.xlsx
```

#### 2. Duplicate Anchors Across Domains
**Problem:** "Full uniqueness mode failed — 3 duplicates found"  
**Solution:**
```bash
# Increase complexity/variation
python3 scripts/generate_anchors.py \
  --domains ... \
  --uniqueness-mode full \
  --min-variation 3 \  # More variations per anchor
  --output anchors.xlsx

# Or auto-resolve duplicates
python3 scripts/resolve_anchor_duplicates.py \
  --input anchors.xlsx \
  --output anchors-resolved.xlsx \
  --method shuffle
```

#### 3. Language Inconsistency
**Problem:** Mixed German + English in output  
**Solution:**
```bash
python3 scripts/validate_anchor_language.py \
  --input anchors.xlsx \
  --language de \
  --output language-validation-report.txt

# Shows which anchors are not in German
# Manually fix or re-generate with --strict-language-mode
```

## Integration with Site Generation

**Gambling Agent** can read anchor plan automatically:

```bash
# Generate site with anchors
python3 scripts/generate_site.py \
  --niche gambling \
  --slug roshtein-site \
  --anchor-file roshtein-anchor-plan.xlsx \
  --domain beat-the-brand.de \
  --auto-apply-anchors

# Script will:
# 1. Read anchor-plan.xlsx
# 2. Extract anchors for beat-the-brand.de sheet
# 3. Inject anchors into HTML navigation menu
# 4. Generate internal link structure
```

## Reference

- **anchor-rules.md:** Complete guide to anchor text SEO best practices
- **dataforseo-guide.md:** How to use DataForSEO API directly
- **examples/:** Completed anchor plans (roshtein-en, casino-de, dating-mixed)

## Version History

### 1.1.0 (2026-03-13)
- Added 10-domain batch processing
- Full uniqueness mode (no anchor duplication)
- Multi-language support (DE, EN, RU)
- DataForSEO API integration with caching
- Validation tools for uniqueness + language

### 1.0.0 (2026-03-11)
- Initial anchor-agent skill
- Single domain anchor generation
- Basic variation types
- Excel export

## Support

**For issues:**
1. Check validation reports
2. Document in memory
3. Tag: #anchor-agent-issue
4. Open GitHub issue in openclaw-skills/anchor-agent
5. Post in Telegram #anchor-agent-issues topic

**Saved commands:**
```bash
# Quick anchor generation
alias gen-anchors='python3 ~/site-factory/scripts/generate_anchors.py'

# Validate existing plan
alias check-anchors='python3 ~/site-factory/scripts/validate_anchor_uniqueness.py'
```
