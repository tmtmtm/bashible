##### toplevel

Returns true if the current bashible script has been called as the top-level script,
otherwise false if it is called as a dependency (sub-script) from a parent.

In the following example, the script will halt if it is called directly,

```bash
@ Basic checks
  - not toplevel
```

