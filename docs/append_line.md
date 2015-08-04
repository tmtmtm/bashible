##### append_line LINE PATH

Append a line to a file unless the line has been found in the end already.

```bash
@ Editing sshd_config
  - append_line 'UseDNS no;' /etc/ssh/sshd_config
```
