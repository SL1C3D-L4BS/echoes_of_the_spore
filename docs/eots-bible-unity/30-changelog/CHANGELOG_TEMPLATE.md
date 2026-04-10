# Changelog

All notable changes to Echoes of the Spore will be documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versioning follows [Semantic Versioning](https://semver.org/).

---

## [Unreleased]

### Added
- Project scaffolding and Unity 6.3 LTS setup
- Game bible (31 sections, 180+ documents)
- CLAUDE.md agent orchestrator with routing table
- README.md project overview
- .gitignore for Unity project

### Architecture
- Custom C# ECS design (not Unity DOTS)
- FixedPoint64 (32.32 int64) math specification
- Bridge layer pattern (ECS ↔ Unity)
- Snapshot serialization format
- Rollback netcode architecture
- Rust proc-gen FFI bridge specification
- Spatial hash grid collision broadphase

### Design
- 4 base classes with 12 Crests and 32 Echo Functions
- 12 Fused class combinations
- 8 zone specifications with bosses
- Echo Boon system (8 civilizations × 5 slots × 4 rarities)
- Community Skill Tree (5 branches × 20 depths)
- Heat system (15 Conditions, max Heat 50)
- Infection system (0-100, 6 voice DSP tiers)
- Spore-Talk VOIP DSP chain
- AI Director (Zone 8 adaptive difficulty)

---

## Version Format

```
Major.Minor.Patch[-PreRelease]

Major: Breaking changes (save incompatible)
Minor: New features (backward compatible)
Patch: Bug fixes
PreRelease: alpha.1, beta.1, rc.1
```

## Expected Version History

| Version | Phase | Target Date |
|---------|-------|-------------|
| 0.1.0-alpha.1 | ECS + FixedPoint64 working | Month 2 |
| 0.2.0-alpha.1 | Combat + 1 class playable | Month 4 |
| 0.3.0-alpha.1 | Zone 1 + Boss clearable | Month 6 |
| 0.5.0-alpha.1 | 2 zones + co-op + Fusion | Month 10 |
| 0.8.0-alpha.1 | 4 zones + 4 classes + Tree | Month 16 |
| 0.9.0-beta.1 | 8 zones + full content | Month 20 |
| 1.0.0-rc.1 | Release candidate | Month 22 |
| 1.0.0 | **LAUNCH** | Month 24 |
| 1.1.0 | Season 1 content | Month 27 |
| 1.2.0 | Season 2 + 5th class | Month 30 |
