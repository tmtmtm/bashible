##### nonempty COMMAND [ARG1] [ARG2] ...

warning: deprecated; use "not empty" instead (see [empty](empty.md) )

Runs the command and returns false if it's output is empty. Also returns false if the command has failed.

```bash
@ Setting some variables and checking whether they contain something
  - set_var DOMAIN nonempty cat /etc/myapp/domain.txt
  - set_var FOO nonempty echo $BAR
```

##### See also

[empty](empty.md)  
[evaluate](evaluate.md)  
[set_var](set_var.md)  
