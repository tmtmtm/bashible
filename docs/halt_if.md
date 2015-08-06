##### halt_if COMMAND [ARG1] [ARG2] ...

Stops the process immediately, including possible parents, but only if a command returns a nonzero exitcode.

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
