# Lessons

- When executing Orca orchestration or CLI commands (`orca`, `orca-ide`, or `$ORCA_CLI_COMMAND`), run with host permissions (unsandboxed / `BypassSandbox: true`) because Orca Desktop's runtime metadata, IPC UNIX sockets, and database reside in `~/.config/orca` outside the workspace directory.

- When starting feature implementation or committing changes in this repository, verify the current branch first and create a `feature/<slug>` branch from `develop` (or `hotfix/<slug>` from `main`) before making edits or commits, never developing or committing directly on permanent branches (`main`, `develop`).

