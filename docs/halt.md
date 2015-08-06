##### halt [MESSAGE]

Stops the process immediately, including possible parents that have called the current script.

```bash
@ Basic checks
 - when 'test -d /tmp' halt "there is no /tmp dir"
```

##### See also

[fail](fail.md)  
[finish](finish.md)  
[finish_if](finish_if.md)  
[halt_if](halt_if.md)  
[skip](skip.md)  
[skip_if](skip_if.md)  
