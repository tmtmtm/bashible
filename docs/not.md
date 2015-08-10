##### not COMMAND [ARG1] [ARG2] ...

Runs a following command and negates it's exit status.

```bash
@ Basic checks
  - not empty echo $1
  - not empty cat /etc/passwd
 
@ Installing cron.d files unless already
  - skip_if not test -d /etc/cron.d/
  - cp /shared/cron.d/* /etc/cron.d
```

You can also use `when` with an exclamation mark or `unless` instead,

```bash
@ Doing cleanup
  - when '! test -d /etc/cron.d' skip
  - unless 'test -d /etc/cron.d' skip
```