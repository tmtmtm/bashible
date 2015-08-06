##### as USER COMMAND [ARG1] [ARG2] ...

Runs a command as another user. Sudo in a non-login shell mode is used, so the current working directory should stay intouched.

warning:

The sudo usually cleans-up environment variables. Fortunately, there is a way to pass variables over the sudo, but you have to 
register them using [export_var](export_var.md). By default, only PATH is copied.

```bash
@ Setting some global variables
  - export_var DOMAIN cat /etc/settings/domain.txt

@ Installing dependencies of rails app
  - cd /home/rails
  - as www-data bundle install

@ This should work
  - as www-data evaluate 'echo $DOMAIN'
```

You can also run the whole child script as a different user,

```bash
@ Creating virtual host
  - as www-data call './virtual-host.ble' $DOMAIN
```

##### See also

[export_var](export_var.md)
