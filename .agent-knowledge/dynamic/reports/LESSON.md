# Lessons

- When executing Orca orchestration or CLI commands (`orca`, `orca-ide`, or `$ORCA_CLI_COMMAND`), run with host permissions (unsandboxed / `BypassSandbox: true`) because Orca Desktop's runtime metadata, IPC UNIX sockets, and database reside in `~/.config/orca` outside the workspace directory.

- When starting feature implementation or committing changes in this repository, verify the current branch first and create a `feature/<slug>` branch from `develop` (or `hotfix/<slug>` from `main`) before making edits or commits, never developing or committing directly on permanent branches (`main`, `develop`).

- When completing a task, implementing a feature, or making a substantial adjustment, promptly document and update the numbered task report located in `.agent-knowledge/dynamic/reports/tasks/`—following the [`TASKS-TEMPLATE.md`](http://TASKS-TEMPLATE.md) template—as an integral part of the definition of done.
