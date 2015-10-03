##### var_empty NAME

Return true if the variable is empty or not set.

```bash
@ Setting DATABASES by arguments or defaults from /etc/databases unless any arguments given
  - set_var DATABASES echo $@
  - when 'var_empty DATABASES' set_var DATABASES ls -1 /etc/databases
```

##### See also

[set_var](set_var.md)  
[export_var](export_var.md)  
