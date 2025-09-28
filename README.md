# Git Tools

A lightweight toolkit of Bash helpers that streamline daily Git workflows. Each script focuses on a specific developer pain point—surfacing richer repository context or spinning up isolated worktrees—without pulling in heavy dependencies.

## Getting Started

1. Clone or copy this directory somewhere on your machine.
2. Add the directory to your `PATH`, or symlink the scripts you need:
   ```bash
   ln -s "$(pwd)/git-status" /usr/local/bin/gitstatus
   ln -s "$(pwd)/git-gwts" /usr/local/bin/git-gwts
   ```
   Or create shell aliases in your profile (e.g. `~/.zshrc`) so the scripts stay portable:
   ```bash
   echo "alias gitstatus='$(pwd)/git-status'" >> ~/.zshrc
   echo "alias git-gwts='$(pwd)/git-gwts'" >> ~/.zshrc
   source ~/.zshrc  # reload your shell config
   ```

3. Make sure the prerequisites are available:
   - `bash`
   - `git`
   - `rsync` and `find` (for the worktree helper)

> Tip: run `chmod +x git-status git-gwts` if you copied the files manually.

## Tools

### `git-status`

Rich terminal status for any Git repository. Highlights remote vs. local divergence, staged/unstaged/untracked files, and recent commits with color-coded hints and emoji markers. Designed as a drop-in replacement for `git status` when you want a quick visual summary.

- Command: `gitstatus`
- Docs: `git-status-tool.spec.md`

### `git-gwts`

Automates creating a new Git worktree for a branch and copies `.env` files plus every `node_modules` directory into the fresh checkout. Prevents branch conflicts across worktrees and keeps environment-sensitive files in sync, so the new workspace is ready to run immediately.

- Command: `git-gwts <branch> [start-point]`
- Docs: `git-gwts.spec.md`

## Conventions

- Scripts follow a "receive object, return object" style for core functions to stay predictable.
- All documentation lives next to the scripts in `*.spec.md` files for quick reference.
- `.gitignore` excludes local artifacts such as `.env` files and `node_modules/` to keep the repository clean.

## Contributing

1. Update or add the relevant spec file alongside any script changes.
2. Test the script manually (or add quick smoke checks) before opening a merge request.
3. Keep the README and usage examples up to date.

Feedback or new helper ideas are welcome—drop them in as issues or PRs.
