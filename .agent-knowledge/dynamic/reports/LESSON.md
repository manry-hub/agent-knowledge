# Lessons

- When executing Orca orchestration or CLI commands (`orca`, `orca-ide`, or `$ORCA_CLI_COMMAND`), run with host permissions (unsandboxed / `BypassSandbox: true`) because Orca Desktop's runtime metadata, IPC UNIX sockets, and database reside in `~/.config/orca` outside the workspace directory.
- When completing a task, implementing a feature, or making a substantial adjustment, promptly document and update the numbered task report located in `.agent-knowledge/dynamic/reports/tasks/`—following the [`TASKS-TEMPLATE.md`](http://TASKS-TEMPLATE.md) template—as an integral part of the definition of done.
