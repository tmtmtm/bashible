##### not COMMAND ARG1 ARG2 ...

Runs a following command and negate it's exit status.

Warning: the function is dangerous; if a non existent or mis-typed command follows,
it will always result into true!

```bash
@ Installing cron.d files unless already
  - skip_if not test -d /etc/cron.d/
  - cp /shared/cron.d/* /etc/cron.d
```

Instead, you can use `when` with an exclamation mark or `unless`,
or even exclamation mark as the test's argument:

```bash
@ Doing cleanup
  - when '! test -d /etc/cron.d' skip
  - unless 'test -d /etc/cron.d' skip
  - when 'test ! -d /etc/cron.d' skip
```