##### evaluate STRING

This is the way how to write complex bash constructions (including pipes).

In the example, we load users starting with the letter "g" (and also check that we loaded something):

```bash
@ Loading some variables
 - set_var USERS nonempty evaluate 'cat /etc/passwd | egrep -e ^g'
```

