##### delayed_call PATH

Calls a bashible script once in the very end even if the delayed_call has been used multiple times.

In the following example, two virtual hosts will be generated, 
but nginx will be reloaded only once afterwards.

```bash
@ Creating virtual host example1.com
  - call "./create-virtual-host.ble" example1.com
  - delayed_call './restart-nginx.ble'

@ Creating virtual host example2.com
  - call "./create-virtual-host.ble" example2.com
  - delayed_call './restart-nginx.ble'
```

##### See also

[call](call.md)
[force_call](force_call.md)
