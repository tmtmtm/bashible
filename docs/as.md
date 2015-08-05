##### as USER COMMAND ...

Run a command as another user. The sudo command in a non-login shell mode is used (the current working dir should stay the same).

warning: 

Sudo usually cleans-up environment variables. Fortunately, there is a way to pass variables to the sudoed command - you have to
export them using `export_var` command. By default, only the PATH variable is copied.

```bash
@ Setting some global variables
  - export_var DOMAIN cat /etc/settings/domain.txt

@ Installing dependencies of rails app
  - cd /home/rails
  - as www-data bundle install

@ This should work
  - as www-data evaluate 'echo $DOMAIN'
```

You can also run whole child script.ble as a different user,

```bash
@ Creating virtual host
  - as www-data call './virtual-host.ble' $DOMAIN
```

##### See also

[export_var](export_var.md)
