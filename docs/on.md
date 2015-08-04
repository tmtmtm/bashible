##### on HOST HOST ...

Returns true if the current hostname is listed.

In the example below the script fails unless executed on webserver1 or webserver2.

```bash
@ Basic checks
  - on webserver1 webserver2 
```

In the second example the task will be skipped on hosts webserver1 or webserver2.

```bash
@ Installing nginx on some machines
  - skip_if on webserver1 webserver2
  - yum install nginx
```
