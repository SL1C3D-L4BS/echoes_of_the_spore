# Vendor Management

## External Vendors

| Vendor Category | Purpose | Phase Engaged | Budget Allocation |
|----------------|---------|--------------|-------------------|
| Voice Acting Studio | Narrator VO recording | M9 (Alpha) | $150,000 |
| Localization | 10-language translation | M17 (Beta) | $200,000 |
| Console Cert Support | PS5/Xbox/Switch cert assistance | M19 | $50,000 |
| QA Outsource | Beta testing surge capacity | M18-M22 | $100,000 |
| Music Composer | Original score (8 zones + boss) | M4-M16 | $120,000 |
| SFX Designer | Sound effects library | M4-M16 | $60,000 |
| Marketing Agency | Trailer production, press outreach | M20-M24 | $150,000 |
| Server Hosting | AWS / GCP infrastructure | M5+ (ongoing) | $200,000/year |
| Legal | IP protection, contracts, EULA | Ongoing | $30,000 |
| **Total External** | — | — | **~$1,060,000** |

## Vendor Selection Criteria

| Criterion | Weight | Scoring (1-5) |
|-----------|--------|--------------|
| Portfolio quality / past work | 30% | Review 3+ comparable projects |
| Pricing competitiveness | 20% | Compare 3+ bids |
| Turnaround time | 15% | Meets our milestone timeline |
| Communication / responsiveness | 15% | Trial task evaluation |
| Scalability | 10% | Can ramp up if needed |
| Cultural fit | 10% | Understands game dev cadence |

## Voice Acting Studio

| Requirement | Specification |
|------------|---------------|
| Role | Mycelium Voice narrator (single primary cast) |
| Lines | ~406 lines (see NARRATOR_VOICE_DIRECTION.md) |
| Languages recorded | 5 (EN, JP, FR, DE, ES) |
| Session estimate | 8-10 recording sessions (4 hours each) |
| Turnaround | 6 weeks from script delivery |
| Delivery format | 48kHz/24-bit WAV, named per line ID |
| Revision rounds | 2 included, additional at hourly rate |
| Rights | Full buyout, perpetual, all media |

### VO Milestone Schedule

| Milestone | Deliverable | Lines |
|-----------|-------------|-------|
| M9 | Placeholder VO (internal read) | 50 key lines |
| M12 | English recording session 1 | 200 lines |
| M14 | English recording session 2 | 206 lines |
| M16 | Alpha: all English VO integrated | 406 lines |
| M19 | Localized VO (JP, FR, DE, ES) | 4 x 406 lines |

## Localization Vendor

| Requirement | Specification |
|------------|---------------|
| Languages | 10 (EN, JP, FR, DE, ES, PT-BR, KO, ZH-CN, RU, PL) |
| Word count estimate | ~80,000 words (UI + narrator + lore + tutorial) |
| Format | JSON string tables, one file per language |
| Glossary | Provided: game-specific terms (50+ entries) |
| Context | Screenshots + style guide provided per string |
| QA | In-context LQA (linguistic quality assurance) included |
| Turnaround | 4 weeks per language batch |
| Updates | Delta updates during beta (< 5,000 words) |

### Localization Pipeline

```
1. Strings tagged in Unity (string key system)
2. Export to JSON via custom editor tool
3. Send to vendor (via TMS: Crowdin or Lokalise)
4. Vendor translates + peer reviews
5. Import JSON back into Unity
6. In-context QA (text fits UI, no truncation)
7. Fix issues, re-export if needed
```

## Music Composer

| Requirement | Specification |
|------------|---------------|
| Tracks | 8 zone themes + 8 combat layers + 8 boss themes + hub + menu |
| Total duration | ~114 minutes (see AUDIO_BUDGETS.md) |
| Style | Adaptive/layered (FMOD-compatible stems) |
| Delivery format | WAV stems, 48kHz/24-bit |
| Reference tracks | Provided per zone (mood board) |
| Revision rounds | 3 per track |
| Rights | Full buyout, perpetual |
| Milestone deliveries | 2-3 tracks per month, M4-M16 |

## QA Outsource

| Requirement | Specification |
|------------|---------------|
| Scope | Functional QA during beta surge (M18-M22) |
| Team size | 8-12 testers |
| Hours | 40 hrs/week per tester |
| Platforms | PC, PS5, Xbox, Switch (4 testers per platform) |
| Bug reporting | Direct access to our Jira instance |
| Communication | Daily standup with QA lead |
| Test plans | Provided by internal QA lead |
| Ramp-up | 1 week training on game mechanics |

## Contract Templates

| Contract Type | Key Terms |
|-------------|-----------|
| Work-for-hire | IP assignment, perpetual rights, no royalties |
| NDA | 2-year term, covers all game content |
| SOW (Statement of Work) | Deliverables, timeline, payment schedule |
| Service Agreement | Ongoing services (hosting, support) |

## Payment Schedule

| Vendor Type | Payment Terms |
|------------|---------------|
| VO Studio | 30% upfront, 40% on delivery, 30% on approval |
| Localization | Per-word rate, monthly invoicing |
| Music | Per-track milestone payments |
| QA Outsource | Monthly retainer |
| Marketing | 50% upfront, 50% on campaign completion |
| Hosting | Monthly billing (usage-based) |

## Vendor Communication

| Channel | Frequency | Purpose |
|---------|-----------|---------|
| Email | As needed | Formal communication, deliverables |
| Slack (shared channel) | Daily | Quick questions, status updates |
| Video call | Weekly/bi-weekly | Status reviews, feedback |
| Project management tool | Continuous | Task tracking, asset delivery |
| Shared drive | Continuous | Asset exchange (Google Drive / Dropbox) |

## Vendor Risk Management

| Risk | Impact | Mitigation |
|------|--------|-----------|
| Vendor misses deadline | Schedule slip | Build 2-week buffer per vendor milestone |
| Quality below standard | Rework cost | Trial task before full engagement |
| Vendor goes out of business | Full replacement needed | Maintain backup vendor contacts |
| IP leak (NDA violation) | Competitive risk | Strong NDA + limited access scope |
| Cost overrun | Budget impact | Fixed-price contracts where possible |
| Communication breakdown | Delays | Dedicated vendor manager on team |
