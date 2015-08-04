##### set_var NAME COMMAND ARG1 ARG2 ... #####

Runs a command and stores it's output into an environment variable.

```bash
@ Setting some variables
  - set_var DOMAIN nonempty cat /etc/domain.txt
```

You can use chaining and put `nonempty` to fail if the command's output is empty.

##### See also

[export_var](export_var.md)

