# Localization Pipeline

## Supported Languages

| Language | Code | Text | VO | Priority | Notes |
|----------|------|------|----|----------|-------|
| English | en | Yes | Yes | Primary | Source language |
| Japanese | ja | Yes | Yes | High | Key market |
| French | fr | Yes | Yes | High | EFIGS standard |
| German | de | Yes | Yes | High | EFIGS standard |
| Spanish (Spain) | es | Yes | Yes | High | EFIGS standard |
| Portuguese (Brazil) | pt-BR | Yes | No | Medium | Growing market |
| Korean | ko | Yes | No | Medium | Strong indie market |
| Simplified Chinese | zh-CN | Yes | No | Medium | Largest PC market |
| Russian | ru | Yes | No | Medium | Active community |
| Polish | pl | Yes | No | Low | Strong gaming culture |

## String Management

### String Key System

```
All player-facing text uses a key-based system:
  Key: "ui.hud.hp_label"
  English: "Health"
  Japanese: "体力"

Key naming convention:
  {category}.{subcategory}.{identifier}

Categories:
  ui.*          — HUD, menus, buttons
  narrator.*    — Narrator VO lines
  lore.*        — Lore text, item descriptions
  tutorial.*    — Tutorial messages
  system.*      — System messages, errors
  boon.*        — Boon names and descriptions
  enemy.*       — Enemy names
  zone.*        — Zone names and descriptions
  achievement.* — Trophy/achievement text
```

### String Table Format

```json
{
    "locale": "en",
    "version": "1.0.0",
    "strings": {
        "ui.hud.hp_label": "Health",
        "ui.hud.infection_label": "Infection",
        "ui.menu.play": "Play",
        "ui.menu.settings": "Settings",
        "narrator.zone1.first_entry": "You step into Sporehaven, where the air is thick with potential.",
        "boon.spore_shield.name": "Spore Shield",
        "boon.spore_shield.desc": "Gain a shield that absorbs {0} damage when entering a new room."
    }
}
```

## Word Count Estimate

| Category | String Count | Avg Words/String | Total Words |
|----------|-------------|-----------------|-------------|
| UI text | 300 | 3 | 900 |
| Menu text | 150 | 5 | 750 |
| Tutorial | 80 | 20 | 1,600 |
| Narrator lines | 406 | 15 | 6,090 |
| Lore text | 200 | 30 | 6,000 |
| Boon names + descriptions | 120 | 15 | 1,800 |
| Enemy names + descriptions | 60 | 10 | 600 |
| Zone descriptions | 40 | 25 | 1,000 |
| System messages | 80 | 8 | 640 |
| Achievements | 30 | 10 | 300 |
| **Total** | **~1,466** | — | **~17,680** |

Per language translation cost (at $0.12/word): ~$2,120
Total for 9 languages (excluding English): ~$19,080
With LQA and context review: ~$30,000

## Localization Workflow

```
1. Developer adds string with key in code/prefab
2. String auto-exported to master string table (en.json)
3. Weekly: new/changed strings flagged for translation
4. Strings sent to TMS (Crowdin / Lokalise)
5. Translator receives string + context (screenshot, max length)
6. Translation completed + peer reviewed
7. Strings imported back to Unity
8. In-context QA: verify text fits UI, no truncation
9. Bug reports filed for display issues
10. Fix and re-verify
```

## Text Expansion Factors

| Language | Expansion vs English | Max UI Width Impact |
|----------|---------------------|-------------------|
| English | 1.0x (baseline) | — |
| French | 1.2-1.3x | +30% width |
| German | 1.3-1.5x | +50% width (compound words) |
| Spanish | 1.2-1.3x | +30% width |
| Portuguese | 1.2-1.3x | +30% width |
| Japanese | 0.5-0.7x (char count) | Variable (wider chars) |
| Korean | 0.8-1.0x | Similar width |
| Chinese | 0.5-0.7x (char count) | Variable (wider chars) |
| Russian | 1.1-1.3x | +30% width (Cyrillic) |
| Polish | 1.2-1.4x | +40% width |

## Font Requirements

| Script | Font | Characters | Size (MB) |
|--------|------|-----------|-----------|
| Latin (EN, FR, DE, ES, PT, PL) | Custom pixel font | ASCII + extended Latin | 0.5 |
| Cyrillic (RU) | Custom pixel font | Cyrillic block | 0.3 |
| Japanese | Noto Sans JP | Hiragana, Katakana, ~3000 Kanji | 4.0 |
| Korean | Noto Sans KR | Hangul Syllables | 3.0 |
| Simplified Chinese | Noto Sans SC | ~6000 common CJK | 6.0 |
| **Total font data** | — | — | **~13.8 MB** |

TextMeshPro SDF fonts generated at 36px with 2px padding.

## UI Localization Testing

| Test | Method | Pass Criteria |
|------|--------|--------------|
| Text overflow | Cycle all languages, screenshot all UI | No text clipped or overlapping |
| Font rendering | Visual inspection per script | All characters render correctly |
| RTL support | N/A (no RTL languages) | — |
| Variable substitution | Test all `{0}`, `{1}` placeholders | Correct values displayed |
| Pluralization | Test singular/plural forms | Grammatically correct |
| Date/time format | Region-appropriate formatting | Matches locale convention |
| Number format | Comma/period separators | Matches locale |

## VO Localization

| Language | Lines | Est. Sessions | Delivery |
|----------|-------|-------------|----------|
| English | 406 | 8-10 sessions | M14 |
| Japanese | 406 | 8-10 sessions | M19 |
| French | 406 | 8-10 sessions | M19 |
| German | 406 | 8-10 sessions | M19 |
| Spanish | 406 | 8-10 sessions | M19 |

### VO Recording Requirements

| Parameter | Value |
|-----------|-------|
| Format | 48kHz/24-bit WAV |
| Naming | `{language}/{line_id}.wav` |
| Delivery | Via shared drive, per-session batches |
| Direction | English reference recordings provided |
| Lip sync | Not required (2D game, no facial animation) |
| Timing | Match English line length +/- 20% |

## TMS (Translation Management System)

| Feature | Requirement |
|---------|------------|
| Platform | Crowdin or Lokalise |
| Integration | GitHub sync (auto-push/pull string files) |
| Context | Screenshot upload per string |
| Glossary | Shared game-specific terminology |
| Translation memory | Leverage previous translations |
| Review workflow | Translate → Review → Approve pipeline |
| API access | For CI/CD integration |
| Cost | ~$300/month (team plan) |
