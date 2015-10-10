##### write_to PATH COMMAND ARG1 ARG2 ...

Execute the command and write it's stdout into a file PATH.

```bash
@ Writing port of API to my_port.cfg
  - write_to /etc/my_port.cfg not empty evaluate 'cat portmap.txt' | grep api | awk "{print $1}"'
```
