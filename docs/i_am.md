##### i_am NAME [NAME2] ...

Returns true if the current user ($USER) is among listed users.

In the following example, only the user root is allowed:

```bash
@ Basic checks
  - i_am root
```
