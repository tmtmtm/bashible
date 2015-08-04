##### empty COMMAND ARG1 ARG2 ...

Runs the command and if it's output is empty, returns true.

In the example, domain.txt file is loaded into a variable DOMAIN but if it fails (the file is not present, not readable or is empty),
the process fails.

```bash
@ Loading some variables
 - set_var DOMAIN not empty cat /etc/myapp/domain.txt
```

##### See also

[evaluates](evaluate.md)
[not](not.md)
[set_var](set_var.md)

