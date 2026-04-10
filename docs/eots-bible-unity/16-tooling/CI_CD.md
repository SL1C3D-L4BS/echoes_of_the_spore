# CI/CD Pipeline

## GitHub Actions Workflow

```yaml
name: EotS CI/CD
on:
  push: { branches: [main, develop] }
  pull_request: { branches: [main] }

jobs:
  rust-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: dtolnay/rust-toolchain@stable
      - run: cd rust && cargo test --all
      - run: cd rust && cargo clippy -- -D warnings

  unity-edit-tests:
    runs-on: ubuntu-latest
    needs: rust-tests
    steps:
      - uses: actions/checkout@v4
      - uses: game-ci/unity-test-runner@v4
        with:
          testMode: EditMode
          projectPath: eots
          githubToken: ${{ secrets.GITHUB_TOKEN }}

  unity-play-tests:
    runs-on: ubuntu-latest
    needs: unity-edit-tests
    steps:
      - uses: game-ci/unity-test-runner@v4
        with:
          testMode: PlayMode
          projectPath: eots

  determinism-check:
    runs-on: ubuntu-latest
    needs: [rust-tests, unity-edit-tests]
    steps:
      - uses: actions/checkout@v4
      - run: cd rust && cargo test determinism
      - uses: game-ci/unity-test-runner@v4
        with:
          testMode: EditMode
          customParameters: '-testFilter Determinism'
          projectPath: eots

  build-windows:
    runs-on: ubuntu-latest
    needs: [unity-play-tests, determinism-check]
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: game-ci/unity-builder@v4
        with:
          targetPlatform: StandaloneWindows64
          projectPath: eots

  build-linux:
    runs-on: ubuntu-latest
    needs: [unity-play-tests, determinism-check]
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: game-ci/unity-builder@v4
        with:
          targetPlatform: StandaloneLinux64
          projectPath: eots
```

## Branch Strategy

| Branch | Purpose | CI | Merge To |
|--------|---------|-----|----------|
| `main` | Release-ready | Full pipeline + builds | — |
| `develop` | Integration | Tests only | main (PR) |
| `feature/*` | Feature work | Tests on PR | develop |
| `hotfix/*` | Critical fixes | Full pipeline | main + develop |

## Build Artifacts

| Platform | Format | Destination |
|----------|--------|-------------|
| Windows | .exe + data | Steam (SteamPipe) |
| Linux | .x86_64 | Steam |
| PS5 | .pkg | Sony DevNet |
| Xbox | .msixvc | Xbox Partner Center |
| Switch | .nsp | Nintendo Dev Portal |

## Deployment Pipeline

```
PR merged to main
  → CI runs all tests + determinism check
  → Build artifacts for all platforms
  → Upload to Steam (staging branch)
  → QA team plays staging build (24h)
  → Promote staging → default (live)
```
