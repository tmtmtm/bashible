##### import PATH

Whenever you want to use "source", use "import" instead. The import does the same, moreover remembers imported files.
It is good when running commands via sudo - [as](as.md) - all imported files will be re-sourced first, otherwise functions won't be reachable in the child process.

```bash
import './my_functions.sh'

@ Creating virtual host
  - call './create-virtual-host.ble' example.com
```

##### See also

[as](as.md)
