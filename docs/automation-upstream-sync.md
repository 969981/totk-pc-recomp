# Upstream synchronization and releases

This repository tracks [valentderah/totk-pc-recomp](https://github.com/valentderah/totk-pc-recomp) on its `main` branch.

## Automation

`.github/workflows/upstream-sync-release.yml` runs:

- Every 30 minutes (best-effort GitHub Actions schedule), on manual dispatch, and when its workflow file is changed.
- It fetches upstream `main`. When new commits are not already in this repository's `main`, it **merges** upstream instead of force-resetting the branch, preserving local workflows and docs.
- Before pushing any merge, it runs `python3 tools/check_content.py --staged`.
- It publishes a **prerelease source snapshot** named `sync-<12-character upstream commit SHA>`, but only once per upstream revision. GitHub supplies the source archives automatically. A source snapshot is **not** a runnable game or prebuilt installer.
- It checks the **latest official upstream release**. If that release has not been mirrored here, it fetches its upstream Git tag, downloads the upstream release assets, adds SHA-256 hashes and republishes those **unchanged upstream binaries** under the same version tag. These installers are upstream-built, **not rebuilt by this fork**.

No Nintendo game files, generated game code, or console keys are committed or distributed. The upstream installers require the end user to supply their own legitimate game files.

The release build tools in this project require locally extracted game data to prepare generated C and runtime libraries. Therefore a public hosted runner cannot independently create the game's executable or assert that a source-only change produced working installers. Only official upstream tagged releases provide installation binaries in this automation.

## Troubleshooting

The workflow needs GitHub Actions enabled and a `GITHUB_TOKEN` able to write repository contents (`permissions: contents: write`).

- See [Actions](../../actions/workflows/upstream-sync-release.yml) for job logs and manual triggering.
- See [Releases](../../releases) for official installer mirrors (e.g. `v0.3.4`) and source-only sync snapshots (`sync-*`).
- When an upstream merge conflicts with a local change, the job fails without force-pushing or overwriting local history. Resolve that conflict and rerun.
- If an upstream release has no downloadable assets, the mirror step fails rather than presenting a fake installer.
- Scheduled runs are not guaranteed to run at exactly the scheduled minute.
