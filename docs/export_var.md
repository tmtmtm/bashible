##### export_var NAME COMMAND ARG1 ARG2 ... #####

Runs a command and stores it's output into an environment variable. Then exports the variable and also preserves it if
another command is run via sudo (which normally cleans-up the environment).

```bash
@ Setting some variables
  - export_var DOMAIN nonempty cat /etc/domain.txt
```

You can use chaining and put `nonempty` to fail if the command's output is empty.

##### See also

[set_var](set_var.md)
[as](as.md)

