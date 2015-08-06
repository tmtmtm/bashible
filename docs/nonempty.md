##### nonempty COMMAND [ARG1] [ARG2] ...

Runs the command and returns false if it's output is empty. Also returns false if the command has failed.

```bash
@ Setting some variables and checking whether they contain something
  - set_var DOMAIN nonempty cat /etc/myapp/domain.txt
  - set_var FOO nonempty echo $BAR
```

Q: Why there's no function `empty` instead? It might be used as `not empty`?
A: Because if you mis-type 'empty' (e.g. empyt), the `not` function wouldn't fail. Therefore nonempty is more safe.

##### See also

[evaluates](evaluate.md)  
[set_var](set_var.md)  
