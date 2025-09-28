# git-gwts Tool Specification

## 1. Overview

The `git-gwts` tool provisions an isolated Git worktree for a target branch and mirrors sensitive local assets (such as `.env` files and `node_modules`) into the new checkout. It automates worktree creation, ensures the selected branch is safe to use, and preserves developer environment parity so a fresh checkout is immediately runnable.

## 2. Core Workflow

| Phase | Responsibility | Notes |
| :--- | :--- | :--- |
| Validation | Verify required binaries (`git`, `rsync`, `find`) exist and the command is executed inside a Git repository. | Fails fast with descriptive errors. |
| Argument Parsing | Accept `branch` (required) and optional `start-point`. | Falls back to usage help when `branch` is missing. |
| Destination Planning | Compute a sibling directory named `<repo-name>-<branch>` adjacent to the repository root. | Aborts if the destination already exists. |
| Worktree Preparation | Prune stale worktrees, switch away from the branch if currently checked out, and ensure the branch is not active elsewhere. | Prevents Git conflicts caused by multiple worktrees using the same branch. |
| Worktree Creation | Reuse existing local branches or create/tracking branches based on `start-point` or remote availability. | Fetches remotes when needed to discover possible tracking branches. |
| Asset Sync | Copy `.env` files and every `node_modules` directory into the new worktree using `rsync`. | Skips `.git` and nested `node_modules` during discovery. |
| Finalization | Emit completion message and the `cd` command to enter the new worktree. | Provides next steps for the developer. |

## 3. Branch and Worktree Management

- **Branch Availability:** If the requested branch is already checked out in any worktree, the script exits to avoid corrupting state.
- **Current Branch Safety:** When the current worktree already uses the target branch, the tool moves the current checkout to the default remote HEAD (falls back to `main`) or a detached HEAD so the new worktree can claim the branch.
- **Start Point Logic:**
  - Uses the provided `start-point` verbatim when supplied.
  - Otherwise fetches remotes, prefers `origin/<branch>` when it exists, and tracks it automatically.
  - Defaults to `HEAD` when neither of the previous options is available, effectively cloning the current commit.

## 4. Environment Asset Replication

- **`.env` Files:** Discovers every file literally named `.env`, excluding `.git` directories and `node_modules` trees from the search. Each file is copied with its directory structure preserved so nested services keep their configuration.
- **`node_modules` Trees:** Finds each `node_modules` directory (any depth), then copies contents into the new worktree. Trailing slashes during `rsync` ensure package contents land inside the destination directory.
- **Idempotence:** Both copy loops create parent directories on demand and print a short log per asset, making it easy to confirm what moved.

## 5. Usage Notes

- **Command Forms:** Can be executed as `git wta <branch> [start-point]` when installed as a Git alias, or as a standalone executable `git-gwts <branch> [start-point]`.
- **Error Handling:** Uses `set -euo pipefail` and explicit checks to stop on missing prerequisites, existing destinations, or conflicting worktree states.
- **Next Steps:** After successful completion, the script reports the new worktree path and prompts the developer to `cd` into it, where dependencies and configuration are immediately available.
