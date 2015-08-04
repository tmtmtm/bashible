##### import PATH

Whenever you want to use "source", use "import" instead. It does the same, moreover remembers imported files.

When a command is then run via sudo (`as`), all files will be re-sourced first (otherwise functions won't be reachable in the child process).

```bash
import './variables.sh'

@ Creating virtual host
  - call './create-virtual-host.ble' example.com
```

##### See also

[as](as.md)
