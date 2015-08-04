##### call PATH ARG1 ARG2 ...

Call another bashile script. The script will run in a new process, therefore it won't affect the caller.

The working directory of the called script is set to the same directory as the script resides in.

Call doesn't run again if already (use `force_call` to bypass the check).

```bash
@ Prerequisities
  - call "./system-base.ble"
  - call "./redis.ble"

@ Creating virtual host
  - call "./create-virtual-host.ble" example.com
```

##### See also

[force_call](force_call.md)
[delayed_call](delayed_call.md)
