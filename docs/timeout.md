##### timeout SECS COMMAND [ARG1] [ARG2] ...

Runs a command and fails if it lasts more than SECS seconds.

```bash
@ Start nginx service
  - service nginx start
  - timeout 20 wait_for_tcp 127.0.0.1:80 up
```
