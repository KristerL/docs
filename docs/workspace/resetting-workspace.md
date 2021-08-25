---
title: Resetting the Workspace
id: resetting-workspace
---

### Resetting a Workspace

To reset a workspace to its initial state, use the `bit reset` command.

```bash
bit init --reset
```

Delete all Bit files and directories, including Bit configuration, tracking and model data. Useful when you need to re-start and use Bit from scratch.

```bash
bit --init reset-hard
```