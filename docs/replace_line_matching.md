##### replace_line_matching REGEXP STRING PATH

Replace complete line with a string (on all lines where a matching regexp has been found).

```bash
@ Editing sshd_config
  - replace_line_matching 'domain' 'domain=example.com' /etc/default/domain.cfg
```
