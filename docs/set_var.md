##### set_var NAME COMMAND [ARG1] [ARG2] ...

Runs a command and stores it's output in an environment variable.

```bash
@ Setting some variables
  - set_var DOMAIN nonempty cat /etc/domain.txt
```

You can use chaining and [nonempty](nonempty.md) to check, whether the command's output is not empty.

##### See also

[export_var](export_var.md)  

