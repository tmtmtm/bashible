##### timeout SECS COMMAND ...

Runs the command but fails if it lasts more than SECS.

```bash
@ Start nginx service
  - service nginx start
  - timeout 20 wait_for_tcp 127.0.0.1:80 up
```
