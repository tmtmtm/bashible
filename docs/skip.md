##### skip MESSAGE

Skips following tasks until a next tasks block.

```bash
@ Installing nginx
 - when 'test -f /usr/local/bin/nginx' skip "local nginx is already installed, skipping"
 - yum install nginx

@ Installing php
 - yum install php
```

##### See also

[finish](finish.md)
[finish_if](finish_if.md)
[halt](halt.md)
[halt_if](halt_if.md)
[skip_if](skip_if.md)
