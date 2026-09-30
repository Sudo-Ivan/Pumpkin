# Hard fork notes

This tree is a **hard fork**. There is no intent to sync PR-style with the original upstream.

## Original source

| Field | Value |
| --- | --- |
| Upstream (original) | https://github.com/Pumpkin-MC/Pumpkin |
| Ivan's GitHub fork (clone origin) | https://github.com/Sudo-Ivan/Pumpkin (`git@github.com:Sudo-Ivan/Pumpkin.git`) |
| Fork date (local) | 2026-09-29 (America/Chicago) |
| Cloned commit | `361c34c4d` (`feat(plugin): Branch a new v0.2 plugin API from the v0.1 API (#3766)`) |
| Local path | `/run/media/user1/projects/ported-games/Pumpkin` |

## Remotes after hard-fork

```
origin  git@github.com:Sudo-Ivan/Pumpkin.git (fetch)
origin  git@github.com:Sudo-Ivan/Pumpkin.git (push)
```

- No `upstream` remote was present after clone; none was added.
- `origin` left pointing at `Sudo-Ivan/Pumpkin`.
- Git history left intact (file edits only; no force-push).

## Removed / changed

### Community / template noise

| Path | Why |
| --- | --- |
| `CODE_OF_CONDUCT.md` | Upstream Contributor Covenant; not wanted in this hard fork. |
| `CONTRIBUTING.md` | Upstream contribution theater / Discord-oriented fluff. |
| `SECURITY.md` | Upstream-only contact (`lilalexmed@proton.me`); not applicable here. |
| `.github/FUNDING.yml` | Upstream donate link spam. |
| `.github/PULL_REQUEST_TEMPLATE.md` | Upstream PR theater. |
| `.github/ISSUE_TEMPLATE/` (`bug_report.yml`, `feature_request.yml`) | Upstream issue forms (assignees are upstream maintainers). |
| `.github/workflows/reviewers.yml` | Auto-requests upstream GitHub usernames as reviewers. |

**Kept:** build/CI workflows (`rust.yml`, `docker.yml`, `nix.yml`, `release.yml`, `typos.yml`, `sync-wit.yml`), `dependabot.yml`, LICENSE, useful docs.

**Kept on purpose (not community COC):** Minecraft Java protocol packets `crates/pumpkin-protocol/src/java/client/config/code_of_conduct.rs` and `.../server/config/accept_code_of_conduct.rs` — these are game protocol types, not the repo CoC.

### Telemetry / phone-home

| Path | Change | Why |
| --- | --- | --- |
| `crates/pumpkin-config/src/telemetry.rs` | Default `enabled = false`; default `endpoint = ""` (was `https://market.pumpkinmc.org/api/v1/rest/telemetry/heartbeat`). Unit tests updated. | Stop anonymous usage heartbeats by default. |
| `crates/pumpkin/src/telemetry.rs` | `start_telemetry_with_config` made an unconditional no-op (never spawns heartbeat/shutdown HTTP tasks). Crypto helpers + unit tests retained so the crate still builds. | Hard-disable server phone-home even if config re-enables it. |

Local crash reports (`crates/pumpkin/src/crash.rs`) write to disk only; no upload path found.

**Not stripped (library APIs for plugins, not auto server telemetry):** `crates/pumpkin-plugin-utils` marketplace helpers (`check_license_online`, `check_for_updates` → `https://market.pumpkinmc.org`). Plugins that call those still can; the server itself no longer phones home.

### Docs / banner cleanup

| Path | Change |
| --- | --- |
| `README.md` | Removed Discord/CI badge spam, Contributions/Communication/Funding sections; pointed at this `FORK.md`. |
| `AGENTS.md` | Dropped `CONTRIBUTING.md` reference; noted hard fork. |
| `crates/pumpkin/src/main.rs` | Startup banner no longer prints upstream Discord / donate / issues links. |

## How to build / run

From README / project layout (Rust Minecraft server):

```bash
cd /run/media/user1/projects/ported-games/Pumpkin
cargo build --release
# or for a quick compile check:
cargo check
cargo run --release
```

Also see `Dockerfile`, `docker-compose.yml`, and Nix files (`flake.nix`, `default.nix`, `shell.nix`) if you prefer those entrypoints. Upstream quick-start docs: https://docs.pumpkinmc.org/#quick-start (reference only).

## Build status

`cargo check` (stable 1.98.1 via `~/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/bin`) succeeded on 2026-09-29 after these edits (`Finished dev profile in ~3m 50s`). Full `cargo build --release` not run here (same compile graph; expect longer). Note: system `/usr/bin/cargo` rustup proxy can mis-detect argv0 in some agent shells — use the toolchain `bin/cargo` path above if that happens.
