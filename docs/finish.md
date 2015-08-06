##### finish [MESSAGE]

Stops the execution of the current bashible script, but parent scripts will continue (if called from a parent).

```bash
@ Basic checks
 - when 'test -f /usr/local/bin/nginx' finish "already installed"
```

##### See also

[finish_if](finish_if.md)
[halt](halt.md)
[halt_if](halt_if.md)
[skip](skip.md)
[skip_if](skip_if.md)
