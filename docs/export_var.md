##### export_var NAME COMMAND [ARG1] [ARG2] ... #####

Runs a command and stores it's output in an environment variable. Then exports the variable. 

Moreover, when you run a command via sudo using [as](as.md), all exported variables will be copied to the sudoed command.
It's important, because the sudo usually clears environment variables.

```bash
@ Setting some variables
  - export_var DOMAIN not empty cat /etc/domain.txt
```

##### See also

[as](as.md)  
[empty](empty.md)  
[not](not.md)  
[set_var](set_var.md)  
[var_empty](var_empty.md)  
