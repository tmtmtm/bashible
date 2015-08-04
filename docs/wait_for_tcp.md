##### wait_for_tcp MATCH up|down

Waits for a listening tcp service to be up or down. Uses "netstat -ltn".

```bash
@ Stop nginx service
  - service nginx stop
  - timeout 20 wait_for_tcp 127.0.0.1:80 down
```
