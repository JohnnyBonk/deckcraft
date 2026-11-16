# DeckCraft — working notes for Claude

Read [AGENTS.md](AGENTS.md) first: its rules (asset policy, clean room, never crash) bind you and
win over anything here.

## Start every session
1. Read `plan/STATUS.md`, then the current milestone in `plan/execution-plan.md`.
2. Read `../../craftrules/AGENTS.md` and the standards it lists for your task.
3. Run `cargo xtask ci` before you change anything, so you know the tree is green.

## Habits
- Every user-visible action is a command with tests (`crates/engine/src/cmd/*`).
- Look at UI changes: `deckcraft --sample --control 7990` + `ui.screenshot`, or
  `deckcraft-cli render`.
- Commit after each landed arc (`M<n>.<k>: what`) and push to `origin main`.
- Keep `ROADMAP.md`, the progress docs in `docs/` (AGENTS.md §7) and `plan/STATUS.md` current.
- Don't ask the user questions; record genuinely-theirs decisions in `plan/STATUS.md`.

<!-- BEGIN: shared-storage-lifecycle -->
## Shared storage and task cleanup

On hetzner-ax42, read `~/Projects/Code/hetzner-storage-maintenance/STORAGE_POLICY.md` before installing dependencies, building, testing, rendering, or starting a temporary service. On other hosts, follow its lifecycle rules when the file/command is available.

Use `storage-cleanup run --project "$PWD" -- <project-command>` for heavy jobs, and add `--kind service` for temporary previews. Jobs from different projects run concurrently while measured CPU, memory and disk headroom allows; there is no global one-job lock. Same-project jobs stay exclusive, and waiting is fair. The wrapper waits up to an hour, then exits 75; an identical retry keeps its place in the queue. Add `--cpus`, `--mem` or `--gpu-mem` hints only for unusual jobs. `storage-cleanup status` shows headroom and the queue. Below 20 GiB free, recover space before starting. Before completion, stop your disposable processes/test stacks, preserve unfinished work, archive completed bb threads through bb, and run `storage-cleanup finish --project "$PWD" --apply --pressure`. List every intentionally retained service/worktree with its owner and expiry. Never globally kill processes, delete dirty worktrees, or prune database volumes.

This storage policy supersedes older claims of unlimited build/browser concurrency or absent resource guards on this host.
<!-- END: shared-storage-lifecycle -->
