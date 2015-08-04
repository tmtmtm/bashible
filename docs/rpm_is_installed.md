##### rpm_is_installed NAME

Returns true if a RPM is installed. Does a quick check (but not always perfect).

```bash
@ Installing nginx
  - when 'rpm_is_installed nginx' fail 'nginx RPM is already installed'
```
