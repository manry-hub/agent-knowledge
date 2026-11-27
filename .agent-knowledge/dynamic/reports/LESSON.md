# Lessons

- When executing Orca orchestration or CLI commands (`orca`, `orca-ide`, or `$ORCA_CLI_COMMAND`), run with host permissions (unsandboxed / `BypassSandbox: true`) because Orca Desktop's runtime metadata, IPC UNIX sockets, and database reside in `~/.config/orca` outside the workspace directory.
