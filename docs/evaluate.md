##### evaluate STRING

When you need complex bash constructions (for instance pipes), use the evaluate.

In the following example, users starting with the letter "g" will be loaded into a variable USERS (and it will also check whether it has loaded something):

```bash
@ Loading some variables
 - set_var USERS not empty evaluate 'cat /etc/passwd | egrep -e ^g'
```

