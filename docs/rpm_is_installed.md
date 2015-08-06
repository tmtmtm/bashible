##### rpm_is_installed NAME

Returns true if a RPM is installed. The check is dirty but quick.

warning: this feature might be moved to a separate library in the future

```bash
@ Installing nginx
  - when 'rpm_is_installed nginx' fail 'nginx RPM is already installed'
```
