##### replace_matching REGEXP STRING PATH

Replaces matching regexps with a string in a file.

```bash
@ Editing sshd_config
  - replace_matching 'enabled=0' 'enabled=1' /etc/default/foo.cfg
```
