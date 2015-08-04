##### may_fail COMMAND ARG1 ARG2 ...

Normally if a command returns nonzero exitcode, execution of the script is stopped.
Sometimes a failing task doesn't matter.

```bash
@ Cleaning up
  - may_fail rmdir /home
  - may_fail rmdir /var
```

