##### quiet COMMAND [ARG1] [ARG2] ...

Runs a command with all output redirected to /dev/null.

```bash
@ Installing nginx unless already
  - when 'quiet grep centos /etc/hosts' skip
  - yum_install nginx
```
