---
name: seo-tt-agent
description: SEO Technical Specification (ТЗ) generator for copywriters. Accepts keyword list (xlsx, CSV, or pasted) and niche, generates structured Excel brief with H1/H2/H3 hierarchy, keyword distribution, word counts, copywriter instructions, and quality requirements. Integrates with DataForSEO API for keyword volume validation. Outputs production-ready ТЗ files with formatting, color coding, and examples.
triggers:
  - "сделай ТЗ"
  - "generate tech spec"
  - "техзадание"
  - "create brief"
  - "generate tz"
  - "content brief"
version: 1.2.0
updated: 2026-03-13
---

# SEO TZ Generator — Technical Specification Specialist

Generates structured SEO content briefs (ТЗ / техзадание) in Excel format for copywriters.

**Workspace:** `~/.openclaw/workspace/`  
**Skill Location:** `/opt/homebrew/lib/node_modules/openclaw/skills/seo-tz-generator/`  
**Python:** `/opt/homebrew/bin/python3.11`  
**Dependencies:** `openpyxl`, `requests` (for DataForSEO API)

## Quick Start

```bash
# Generate ТЗ from keyword list (CLI)
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input /tmp/keywords.json \
  --output ~/Downloads/ТЗ_niche-slug.xlsx \
  --niche transportation \
  --language en

# Or from xlsx file (read keywords from column 1, volume from column 2)
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input keywords.xlsx \
  --output ~/Downloads/ТЗ.xlsx \
  --auto-convert-json
```

## Workflow

### Step 1: Gather Keywords

Input can be:
- **Excel file** (`.xlsx`) — Column A = keyword, Column B = volume (optional)
- **CSV** — semicolon or comma-separated
- **Pasted text** — newline-separated keywords
- **DataForSEO export** — JSON with `keyword`, `search_volume`, `cpc` fields

**Example keyword list:**

```
moving from nj to florida,150
moving company nj to florida,89
moving cost from nj to florida,76
moving companies nj to florida,65
moving from new jersey to florida,58
florida to nj moving,45
```

### Step 2: Define Niche & Domain

Required:
- **Niche:** transportation, ecommerce, legal, real estate, medical, dating, gambling, SaaS
- **Target Language:** EN (English), DE (German), RU (Russian), FR (French)
- **Word Count Target:** Optional (default: 2000-2500 words)

### Step 3: Plan H1/H2/H3 Structure

The script auto-plans based on keyword volumes:

**Rules:**
- **H1** (1 only): Highest-volume keyword → 50–100 words
- **H2 Sections** (4–6): 
  - Bold=true: Brand/transactional anchors (cannot be reordered by copywriter)
  - Bold=false: Informational sections
- **H3 Sub-sections** (2–4 per H2): 30–75 words each
- **FAQ** (separate H2 + H3s): Remaining low-volume keywords
- **Quality Requirements** (auto-added): Standard rules for formatting, links, tone

**Keyword Placement Strategy:**

```
H1: "Moving From NJ to Florida" (150 vol)
  ├─ H2: "Cost of Moving from NJ to Florida" (76 vol) [bold=false]
  │   ├─ H3: "Average Moving Costs" (LSI, no keyword req)
  │   ├─ H3: "What Affects Moving Prices" (LSI)
  │   └─ H3: "Cost Breakdown by Company" (LSI)
  ├─ H2: "Best Moving Companies NJ to Florida" (89 vol) [bold=true, cannot reorder]
  │   ├─ H3: "Local Moving Companies" (LSI)
  │   ├─ H3: "Interstate Movers" (LSI)
  │   └─ H3: "Customer Reviews" (LSI)
  ├─ H2: "How to Choose a Moving Company" (LSI, transactional) [bold=true]
  │   ├─ H3: "Licensing & Insurance" (LSI)
  │   ├─ H3: "Free Moving Quotes" (LSI)
  │   └─ H3: "Red Flags to Avoid" (LSI)
  ├─ H2: "Moving Checklist NJ → FL" (LSI) [bold=false]
  │   ├─ H3: "Before Moving" (LSI)
  │   ├─ H3: "During Moving" (LSI)
  │   └─ H3: "After Moving" (LSI)
  └─ H2: "FAQ: Moving from New Jersey to Florida" (58 vol) [bold=false]
      ├─ H3: "How long does NJ to FL move take?" (LSI)
      ├─ H3: "Is it cheaper to move from NJ to FL?" (LSI)
      ├─ H3: "Can I move from FL back to NJ?" (65 vol)
      └─ H3: "What items can't be shipped?" (LSI)
```

### Step 4: Run Generator Script

```bash
# Create temp JSON from keywords
cat > /tmp/tz_input.json <<'EOF'
{
  "niche": "transportation",
  "niche_slug": "nj-to-florida-movers",
  "language": "en",
  "title": "Moving From NJ to Florida",
  "meta_description": "Expert guide to moving from NJ to Florida. Compare costs, find licensed movers, get free quotes. Updated 2026.",
  "h1_keyword": "moving from nj to florida",
  "sections": [
    {
      "level": "h2",
      "keyword": "cost of moving from nj to florida",
      "type": "informational",
      "bold": false,
      "word_count": 350,
      "subsections": [
        {"level": "h3", "keyword": null, "type": "lsi", "word_count": 100, "instruction": "Average cost breakdown..."},
        {"level": "h3", "keyword": null, "type": "lsi", "word_count": 100, "instruction": "Factors affecting price..."},
        {"level": "h3", "keyword": null, "type": "lsi", "word_count": 150, "instruction": "Company comparison table..."}
      ]
    },
    {
      "level": "h2",
      "keyword": "best moving companies nj to florida",
      "type": "transactional",
      "bold": true,
      "word_count": 400,
      "instruction": "Top 5 movers with ratings. Must include: license, experience, customer reviews. Cannot reorder.",
      "subsections": [
        {"level": "h3", "keyword": null, "type": "lsi", "word_count": 130},
        {"level": "h3", "keyword": null, "type": "lsi", "word_count": 130},
        {"level": "h3", "keyword": null, "type": "lsi", "word_count": 140}
      ]
    }
  ],
  "faq": [
    {"question": "How long does NJ to FL move take?", "keyword": null, "type": "lsi", "word_count": 80},
    {"question": "Is it cheaper to move from NJ to FL?", "keyword": null, "type": "lsi", "word_count": 80},
    {"question": "Can I move from FL back to NJ?", "keyword": "florida to nj moving", "type": "keyword", "word_count": 80},
    {"question": "What items can't be shipped?", "keyword": null, "type": "lsi", "word_count": 80}
  ]
}
EOF

# Generate xlsx
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input /tmp/tz_input.json \
  --output ~/Downloads/ТЗ_nj-to-florida-movers.xlsx
```

### Step 5: Deliver to Copywriter

```bash
# Open in Finder (Mac)
open -R ~/Downloads/ТЗ_nj-to-florida-movers.xlsx

# Or send via email
mail -s "ТЗ: Moving NJ to Florida" copywriter@example.com \
  < ~/Downloads/ТЗ_nj-to-florida-movers.xlsx
```

## Excel Output Format

### Sheet Structure

**Sheet Name:** `nj-to-florida-movers` (niche_slug)

| Col | Name | Type | Example | Purpose |
|-----|------|------|---------|---------|
| A | **Heading Level** | H1/H2/H3 | H1 | Hierarchy |
| B | **Heading Title** | Text | "Moving From NJ to Florida" | Copywriter sees exact heading |
| C | **Keyword** | Text | "moving from nj to florida" | What to rank for |
| D | **Word Count** | Number | 80 | Target length |
| E | **Instructions** | Text | "Primary keyword, include benefits..." | Tone, structure, requirements |
| F | **Content Type** | informational / transactional / faq | transactional | Section purpose |
| G | **Bold (Don't Reorder)** | TRUE / FALSE | TRUE | If true = fixed order, copywriter can't move |
| H | **Keyword Type** | primary / secondary / lsi / brand | primary | For copywriter reference |
| I | **Examples** | Text | "E.g., Atlas Van Lines, Mayflower..." | Concrete examples |

### Color Coding

- **Header row (A1:I1):** Dark background, white text, bold
- **H1 rows:** Light gold background (`#ffd700`)
- **H2 rows (bold=true):** Light red background (`#ffcccc`) — copywriter can't reorder
- **H2 rows (bold=false):** Light yellow background (`#ffffcc`)
- **H3 rows:** White background
- **FAQ row:** Light blue background (`#cce5ff`)
- **Requirements rows:** Gray background (`#f0f0f0`)

### Auto-Added Sections

These are always included at the end:

#### 1. Basic Requirements
```
Heading Level: [blank]
Heading Title: "Basic Requirements"
Instructions: "Word count: 2000–2500 words total. Use H2-H3 hierarchy as provided. Follow the keyword placement shown above. Include internal links (3–5 per 500 words). No external links to competitors."
```

#### 2. Quality Requirements
```
Heading Level: [blank]
Heading Title: "Quality Requirements"
Instructions: "
- Tone: Professional, authoritative, conversational
- Grammar: American English, Hemingway Editor grade B+
- Uniqueness: 100% original, no AI-generated filler
- Structure: Clear intro, main points with evidence, conclusion
- Links: 3–5 internal links per 500 words (anchor text = keyword)
- Images: Placeholder [IMG: description] — we'll add later
- Updates: Latest data, real prices, current regulations
"
```

#### 3. Keyword & LSI Strategy
```
Heading Level: [blank]
Heading Title: "Keyword & LSI Strategy"
Instructions: "
Primary keyword (H1): 1–2 times in article
Secondary keywords (H2s): 1–2 times in each section
LSI keywords (related): Naturally throughout (moving company, interstate movers, relocation, etc.)
Long-tail: Use conversational variations (moving from NJ to FL, how much to move from NJ)
Avoid: Keyword stuffing, unnatural placement, exact match in every sentence
"
```

### Example Output

```
A              B                                    C                              D    E                                  F                 G       H
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
[Header row colored dark gray]
H1             Moving From NJ to Florida            moving from nj to florida      80   Catchy, benefits, includes number   informational     FALSE   primary
H2             Cost of Moving from NJ to Florida    [blank]                        350  Introduce costs, comparison...      informational     FALSE   secondary
H3             Average Moving Costs                 [blank]                        100  Price range, factors affecting...   informational     FALSE   lsi
H3             What Affects Moving Prices          [blank]                        100  Distance, season, items, service... informational     FALSE   lsi
H3             Cost Breakdown by Company           [blank]                        150  Table/comparison of top movers...   informational     FALSE   lsi
H2             Best Moving Companies NJ to FL      best moving companies...       400  Top 5, ratings, phone, license     transactional     TRUE    secondary
H3             Local Movers                        [blank]                        130  Nearby companies, reviews...        transactional     FALSE   lsi
H3             Interstate Movers                   [blank]                        130  Licensed interstate carriers...     transactional     FALSE   lsi
H3             Customer Reviews                    [blank]                        140  Ratings, testimonials, BBB...      transactional     FALSE   lsi
[... more sections ...]
[blank]        Basic Requirements                  [blank]                        [blank] Word count: 2000–2500...    [blank]          [blank] [blank]
[blank]        Quality Requirements                [blank]                        [blank] Tone: Professional...       [blank]          [blank] [blank]
[blank]        Keyword & LSI Strategy              [blank]                        [blank] Primary keyword 1–2×...    [blank]          [blank] [blank]
```

## DataForSEO Integration

### Setup

```bash
# Install DataForSEO API credentials (save once)
export DATAFORSEO_USER="borys.kutsenko97@gmail.com"
export DATAFORSEO_PASS="edf0c28d85c68298"
```

### Query Keywords

```bash
# Fetch volumes for keywords
python3 skills/seo-tz-generator/scripts/query_dataforseo.py \
  --keywords "moving from nj to florida,moving company nj,florida movers" \
  --output /tmp/keyword_volumes.json

# Output:
# {
#   "moving from nj to florida": {"volume": 150, "cpc": 2.45, "difficulty": 35},
#   "moving company nj": {"volume": 89, "cpc": 3.12, "difficulty": 42},
#   "florida movers": {"volume": 320, "cpc": 2.88, "difficulty": 38}
# }
```

### Auto-Validate & Sort

The script can auto-detect low-volume keywords:

```bash
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input keywords.xlsx \
  --validate-dataforseo \
  --min-volume 20 \
  --output ~/Downloads/ТЗ.xlsx

# Removes keywords with < 20 monthly searches
# Sorts sections by volume (highest to lowest)
```

## Usage Examples

### Example 1: NJ to Florida Movers (Full Workflow)

**Input:**
```
moving from nj to florida,150
moving company nj to florida,89
moving cost from nj to florida,76
moving companies nj to florida,65
moving from new jersey to florida,58
florida to nj moving,45
```

**Command:**
```bash
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input movers-keywords.xlsx \
  --niche transportation \
  --language en \
  --output ~/Downloads/ТЗ_movers.xlsx
```

**Output:** Excel file with:
- H1: "Moving From NJ to Florida" (150 vol)
- H2 sections: Cost, Companies, How to Choose, Checklist
- H3 subsections: 3–4 per H2
- FAQ: Remaining keywords
- Total words: ~2000–2500
- Copywriter instructions: Detailed per section

**Delivery:** Open in Finder, send to copywriter

### Example 2: Gambling Casino Reviews (High-Competition Niche)

**Input:**
```
best online casinos,2800
online casino usa,1500
top casinos 2026,890
casino bonuses,750
free spins no deposit,680
casino reviews,620
legitimate online casinos,580
us casino sites,450
best casino bonus,380
casino games,420
```

**Command:**
```bash
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input casino-keywords.xlsx \
  --niche gambling \
  --language en \
  --word-count 3000 \
  --output ~/Downloads/ТЗ_casinos.xlsx
```

**Result:**
- H1: "Best Online Casinos 2026: Top 10 Reviewed"
- 5 H2 sections (reviews, bonuses, games, etc.)
- 18 H3 subsections
- FAQ with 6 questions
- ~3000 word target
- Compliance notes: 18+, responsible gaming links

### Example 3: Multi-Language Brief (Russian + German)

**Keyword Files:**
- `gambling-keywords-ru.xlsx` (Russian keywords)
- `gambling-keywords-de.xlsx` (German keywords)

**Commands:**
```bash
# Russian
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input gambling-keywords-ru.xlsx \
  --niche gambling \
  --language ru \
  --output ~/Downloads/ТЗ_казино_ru.xlsx

# German
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input gambling-keywords-de.xlsx \
  --niche gambling \
  --language de \
  --output ~/Downloads/ТЗ_casino_de.xlsx
```

**Output:** Two separate ТЗ files, each with language-appropriate:
- Tone & examples
- Copywriter instructions
- Quality requirements (de: "Rechtschreibung nach Duden", ru: "Грамотность по ТОSOT")

## Advanced Features

### Custom Section Weighting

Control how keyword volumes affect section size:

```bash
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input keywords.json \
  --weight-strategy "proportional" \
  # proportional: H2 word count ∝ keyword volume
  # balanced: equal word count per section
  # custom: read weights from JSON
```

### Multiple H2 Per Keyword

For highly competitive keywords, split into multiple sections:

```json
{
  "keyword": "best online casinos",
  "volume": 2800,
  "split_into_h2": 3,  // Creates 3 H2s, each ~1000 words
  "split_titles": [
    "Best Online Casinos 2026",
    "Top Casinos by Game Type",
    "Casinos with Best Bonuses"
  ]
}
```

### Lock & Unlock Sections

Mark sections that copywriter CANNOT reorder:

```json
{
  "level": "h2",
  "keyword": "best moving companies nj",
  "locked": true,  // Copywriter cannot move this H2
  "instruction": "This section must come first. Compare 5 companies with ratings."
}
```

These appear with `bold=TRUE` in Excel output (red background).

## Error Handling

### Common Issues

#### 1. Keyword List Too Short (< 5 keywords)
**Problem:** Not enough variety for 2000-word article  
**Solution:**
```bash
python3 skills/seo-tz-generator/scripts/expand_keywords.py \
  --input keywords.xlsx \
  --dataforseo-related-expand \
  --min-additional 10 \
  --output expanded-keywords.xlsx

# Fetches related keywords from DataForSEO
# Expands "moving from nj to florida" → also adds "moving company nj", "florida movers", etc.
```

#### 2. Uneven Word Count Distribution
**Problem:** H2s vary 100–500 words  
**Solution:**
```bash
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input keywords.json \
  --auto-balance-word-counts \
  --target-word-count 2200
  # Redistributes so each H2 is ~300 words, H3s ~100 words
```

#### 3. DataForSEO API Timeout
**Problem:** "API request timeout"  
**Solution:**
```bash
# Retry with backoff
python3 scripts/query_dataforseo.py \
  --keywords "..." \
  --retry-count 3 \
  --backoff-seconds 30

# Or skip validation (use volume estimates)
python3 scripts/generate_tz.py \
  --input keywords.json \
  --skip-dataforseo-validation
```

### Debug Logging

```bash
python3 skills/seo-tz-generator/scripts/generate_tz.py \
  --input keywords.json \
  --output ~/Downloads/ТЗ.xlsx \
  --debug \
  --log-file ~/tz-debug.log

# Check log for detailed steps:
# - Keyword parsing
# - Volume ranking
# - Section allocation
# - Excel generation
```

## Reference Files

- **tz-format.md:** Full JSON schema, column descriptions, examples
- **keyword-strategy.md:** Best practices for niche-specific keyword distribution
- **samples/:** Pre-made ТЗ examples (dating, gambling, legal, ecommerce)

## Version History

### 1.2.0 (2026-03-13)
- Added Vegas Matt (EN) gambling template
- Multi-language support: EN, DE, RU, FR
- DataForSEO integration with auto-validation
- Custom section weighting
- Locked sections (copywriter cannot reorder)

### 1.1.5 (2026-03-11)
- Initial seo-tz-generator skill
- H1/H2/H3 hierarchy
- Keyword distribution
- Excel export with formatting
- Basic instructions

## Support

**For issues:**
1. Document error in memory
2. Tag: #seo-tt-agent-issue
3. Open GitHub issue in openclaw-skills/seo-tt-agent
4. Post in Telegram #seo-tt-agent-issues topic

**Common commands saved:**
```bash
# Quick ТЗ generation
alias make-tz='python3 ~/site-factory/skills/seo-tz-generator/scripts/generate_tz.py'
```
