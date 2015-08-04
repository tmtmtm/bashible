##### not COMMAND ARG1 ARG2 ...

Runs a following command and negate it's exit status.

```bash
@ Installing cron.d files unless already
  - skip_if not empty ls -1 /etc/cron.d
  - cp /shared/cron.d/* /etc/cron.d
```

You can use `when` with an exclamation mark or `unless` instead,

```bash
@ Doing cleanup
  - when '! empty ls -1 /etc/cron.d' skip
  - unless 'empty ls -1 /etc/cron.d' skip
```
