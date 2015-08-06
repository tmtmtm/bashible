##### call PATH [ARG1] [ARG2] ...

Calls another bashile script. The script will run in a new process, therefore it won't affect the caller.

The working directory of the called script will be set to the same directory as the another script resides in.

The call function doesn't run twice unless you use [force_call](force_call.md) instead.

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
