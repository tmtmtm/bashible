##### replace_matching REGEXP STRING PATH

Replace matching regexps in a file with a string.

```bash
@ Editing sshd_config
  - replace_matching 'enabled=0' 'enabled=1' /etc/default/foo.cfg
```
