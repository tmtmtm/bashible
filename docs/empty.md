##### empty COMMAND [ARG1] [ARG2] ...

Runs the command and returns true if it's output is empty.

```bash
@ Setting some variables and checking whether they contain something
  - set_var DOMAIN not empty cat /etc/myapp/domain.txt
  - set_var FOO not empty echo $BAR
```

##### See also

[not](not.md)  
[evaluate](evaluate.md)  
[set_var](set_var.md)  
