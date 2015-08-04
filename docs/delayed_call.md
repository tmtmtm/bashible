##### delayed_call PATH

If The bashible script will be called only once at the end. In this example, two virtual hosts will be generated, 
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
