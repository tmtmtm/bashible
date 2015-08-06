##### skip [MESSAGE]

Skips all following tasks until a next block.

```bash
@ Installing nginx
 - when 'test -f /usr/local/bin/nginx' skip "local nginx is already installed, skipping"
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
[skip_if](skip_if.md)  
