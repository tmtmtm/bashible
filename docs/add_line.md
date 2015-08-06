##### add_line LINE PATH

Add a line into a file unless the line has been aleady found somewhere in the file.

```bash
@ Editing sshd_config
  - add_line 'UseDNS no;' /etc/ssh/sshd_config
```
