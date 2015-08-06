##### finish_if COMMAND [ARG1] [ARG2] ...

Stops the execution of the current bashible script if an execute command returns a nonzero exitcode; a parent script will continue (if called from parent).

```bash
@ Basic checks
 - finish_if test -f /usr/local/bin/nginx
```

##### See also

[finish](finish.md)
[halt](halt.md)
[halt_if](halt_if.md)
[skip](skip.md)
[skip_if](skip_if.md)
