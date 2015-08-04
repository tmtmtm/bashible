##### skip_if COMMAND ARG1 ARG2 ...

If a command returns nonzero exitcode, it skips following tasks until a next tasks block.

```bash
@ Installing nginx
 - skip_if test -f /usr/local/bin/nginx
 - yum install nginx

@ Installing php
 - yum install php
```

##### See also

[finish](finish.md)
[finish_if](finish_if.md)
[halt](halt.md)
[halt_if](halt_if.md)
[skip](skip.md)
