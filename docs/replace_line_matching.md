##### replace_line_matching REGEXP STRING PATH

Replaces complete line(s) with a string in a file where a regexp has been found.

```bash
@ Editing sshd_config
  - replace_line_matching 'domain' 'domain=example.com' /etc/default/domain.cfg
```

##### See also

[replace_matching](replace_matching.md)  
[remove_line_matching](remove_line_matching.md)  
