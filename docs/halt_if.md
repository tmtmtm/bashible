##### halt_if COMMAND ARG1 ARG2 ...

If a command returns nonzero exitcode, stops the process immediately (including possible parent bashible scripts that called the current one).

```bash
@ Basic checks
 - halt_if test -d /tmp
```

##### See also

[finish](finish.md)
[finish_if](finish_if.md)
[halt](halt.md)
[skip](skip.md)
[skip_if](skip_if.md)
