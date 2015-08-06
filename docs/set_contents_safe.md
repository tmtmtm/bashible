##### set_contents_safe CONTENTS PATH

If a file is empty, write a contents into it. If the contents has been already found there, does nothing. Moreover, returns false if a different contents is there.

```bash
@ Writing hostname
  - set_contents_safe 'centos-local' /etc/default/hostname
```

##### See also

[set_contents](set_contents.md)
