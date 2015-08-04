##### add_line LINE PATH

Add a line into the file unless the line has been found already (somewhere in the file).

```bash
@ Editing sshd_config
  - add_line 'UseDNS no;' /etc/ssh/sshd_config
```
