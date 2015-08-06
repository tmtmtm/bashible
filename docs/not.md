##### not COMMAND [ARG1] [ARG2] ...

Runs a following command and negates it's exit status.

Warning: this function is dangerous; if you execute a non existent command (mis-typed),
it will always results into true!

```bash
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