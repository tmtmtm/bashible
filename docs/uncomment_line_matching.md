##### uncomment_line_matching REGEXP FILE

Removes '#' from the beginning of matching line(s) in the file.

```bash
@ Editing sshd_config
  - uncomment_line_matching 'UseDNS' /etc/ssh/sshd_config
```

