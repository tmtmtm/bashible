##### skip_if COMMAND [ARG1] [ARG2] ...

Skips all tasks until a next block, but only if a following command returns a nonzero exitcode.

```bash
@ Installing nginx
 - skip_if test -f /usr/local/bin/nginx
 - rm -rf /etc/nginx
 - yum install nginx

@ Installing php
 - yum install php
```

##### See also

[fail](fail.md)  
[finish](finish.md)  
[finish_if](finish_if.md)  
[halt](halt.md)  
[halt_if](halt_if.md)  
[skip](skip.md)  
