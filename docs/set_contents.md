##### set_contents CONTENTS PATH

DEPRECATED, see write_to instead

Write contents into a file (and does nothing if it has been already found in there).

```bash
@ Writing hostname
  - set_contents 'centos-local' /etc/default/hostname
```

##### See also

[set_contents_safe](set_contents_safe.md)  
[write_to](write_to.md)  
