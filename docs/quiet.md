##### quiet COMMAND ...

Runs the command with output directed to /dev/null.

```bash
- when 'quiet grep centos /etc/hosts' already
```

##### reset_base_dir

see the "base_dir" above


##### set_var NAME [COMMAND...] #####

Store the command's output in the variable. Fails if the command fails.

```bash
set_var list ls /
```

You can use more complex command using 'evaluate' (which also fails if the evaluated string fails).

```bash
set_var list evaluate "ls / | grep usr"
```

You can make even more complex chain, for instance, there is a value stored in a file which we'd like to load into a variable. Moreover it should fail if the loaded value is empty.

```bash
set_var myuser nonempty evaluate 'cat /etc/hosts | grep myuser'
```


##### skip

a) if used before all task blocks started, skips all following tasks in the blebook (exits the process)

```bash
when 'test $ENVIRONMENT = development' skip
```

b) skips following tasks up to a next block

```bash
@ Installing application if not in development mode
  - when 'test $ENVIRONMENT = development' skip
  - yum install myapp
```

note: does the same like the "already", but outputs a different message

##### skip_if COMMAND ...

Runs the command and if it results in true, calls "skip"

a) before task blocks started

```bash
skip_if 'test $ENVIRONMENT = development'
```

b) inside a tasks block

```bash
@ Installing nginx unless already
  - skip_if test -x /usr/sbin/nginx
  - yum install nginx
```

##### stop

Stops execution of the blebook. If called from a parent blebook, it will continue normally.

```bash
when 'test $USER = root' stop
```

##### stop_all

Stops execution of the blebook and all parent blebooks.

```bash
when 'test ! -f /etc/settings.cfg' stop_all
```

##### stop_if COMMAND ...

Runs the command and if it results in true, calls "stop".

```bash
stop_if test ! -f /etc/settings.cfg
stop_if not test -d /etc/nginx
```

##### stop_all_if COMMAND ...

Runs the command and if it results in true, calls "stop_all".

```bash
stop_all_if test ! -f /etc/settings.cfg
stop_all_if not test -d /etc/nginx
```

##### tags TAG ...

There is a special environment variable TAGS. It may contain tags separated by a space.
Then you can match these tags and skip actions.

Example:

```bash
@ Compiling redis
  - tags download compile
  - ./configure
  - make

@ Installing redis
  - tags install
  - make install

@ Starting redis service
  - service start redis
```

Now if you execute the blebook with no TAGS ...

 ```bash
  bashible redis.ble
 ```

=> all three blocks will happen

If you execute the blebook with TAGS ...

 ```bash
  TAGS="compile install" bashible redis.ble
 ```

=> again all three blocks will happen (in the third, there
   are no tags specified - it will run by default)

If you execute bashible with another TAGS ...

 ```bash
  TAGS="compile" bashible redis.ble
 ```

=> only the first and the third block will happen.


Moreover, if you put "tags" inside tasks blocks as above, it will affect only those blocks.
If you put "tags" before all tasks blocks, it will skip the whole blebook unless matched.


##### export_var NAME [COMMAND...] #####

Calls set_var to store the command's output (if any) and then exports the variable. Also preserves it over sudo (which normally cleans the environment).

```bash
export_var DOMAIN foobar.com
```

for more info see 'set_var'


##### unless 'EVALUATED STRING' COMMAND ...

see the "when" below (does the opposite)


##### when 'EVALUATED STRING' COMMAND ...

Runs the command if the evaluated string returns true ("when") or false ("unless").

```bash
when 'test -z "$ENVIRONMENT"' fail 'ENVIRONMENT variable is not set!'
when 'test ! -n "$ENVIRONMENT"' fail 'ENVIRONMENT variable is not set!'
unless '! test -z "$ENVIRONMENT"' fail 'ENVIRONMENT variable is not set!'
unless 'test ! -z "$ENVIRONMENT"' fail 'ENVIRONMENT variable is not set!'

@ Installing nginx unless already
  - when 'test -f /etc/nginx/nginx.conf' already
  - unless '! test -f /etc/nginx/nginx.conf' already
```

You can write more complex expressions,

```bash
when 'test -d /etc/nginx && test -d /var/www' skip
```


### Description of other functions

##### add_line LINE FILE

Add a line into the file (path) unless the line has been found already (somewhere in the file).

```bash
add_line 'UseDNS no;' /etc/ssh/sshd_config
```

##### append_line LINE FILE

Append a line into the file (path) unless the line has been found in the end already.

```bash
append_line 'UseDNS no;' /etc/ssh/sshd_config
```

##### comment_line_matching REGEXP FILE

Prefix matching line(s) by '#' in the file.

```bash
comment_line_matching 'UseDNS' /etc/ssh/sshd_config
```

##### install_gem NAME NAME ...

Install ruby gems unless already (the check is quick).

```bash
@ Installing application prerequisities
  - install_gem celluloid cuba sequel
```

##### only_user NAME

Fails if current user is not the one defined.

```bash
only_user root
```

##### rpm_is_installed NAME

Returns true if the rpm is installed.

```bash
unless 'rpm_is_installed nginx' fail 'nginx RPM is not installed'
```

##### prepend_line LINE FILE

Prepend the file with a line unless already.

```bash
prepend_line 'UseDNS no;' /etc/ssh/sshd_config
```

##### prepend_line LINE FILE

Prepend the file with a line unless already.

```bash
prepend_line 'UseDNS no;' /etc/ssh/sshd_config
```

##### remove_line_matching REGEX FILE

Remove line(s) matching regexp from the file.

```bash
remove_line_matching 'UseDNS' /etc/ssh/sshd_config
```

##### replace_line_matching REGEXP STRING FILE

Replace complete line where the matching regexp has been found.

```bash
replace_line_matching 'domain' 'domain=example.com' /etc/default/domain.cfg
```

##### replace_matching REGEXP STRING FILE

Replace matching regexps in the file with the string.

```bash
replace_matching 'enabled=0' 'enabled=1' /etc/default/foo.cfg
```

##### set_contents CONTENTS FILE

Write contents into the file, does nothing if already.

```bash
set_contents 'centos-local' /etc/default/hostname
```

##### set_contents_safe CONTENTS FILE

If the file is empty, write the contents into it. If the contents is already there, does nothing. Fails, if a different contents has already been there.

```bash
set_contents_safe 'centos-local' /etc/default/hostname
```

##### symlink SRC DEST

Creates a symbolic link from SRC to DEST unless already.

```bash
symlink '/usr/local/bin/redis' '/usr/bin/redis'
```

##### timeout SECS COMMAND ...

Runs the command but fails if it lasts more than SECS.

```bash
@ Start nginx service
  - service nginx start
  - timeout 20 wait_for_tcp 127.0.0.1:80 up
```

##### uncomment_line_matching REGEXP FILE

Removes '#' from the beginning of matching line(s) in the file.

```bash
uncomment_line_matching 'UseDNS' /etc/ssh/sshd_config
```

##### wait_for_tcp MATCH up|down

Waits for a listening tcp service to be up or down. Uses "netstat -ltn".

```bash
@ Stop nginx service
  - service nginx stop
  - timeout 20 wait_for_tcp 127.0.0.1:80 down
```

##### yum_install NAME NAME ...

Installs the specified software unless already (the check is quick).

```bash
@ Installing prerequisities
  - yum_install rsync vsftpd nginx
```


### FAQ

##### How to run a blebook a different user? (sudo)
If you want to run the whole blebook as another user, then either run bashible via sudo, 
```bash
sudo -u user1 bashible blebook.ble
``` 
or inside a blebook use "as" helper, 
```bash
 - as user1 command... 
```
or call a blebook from a blebook the same way,
```bash
as user1 call './another_blebook.ble'
```

note: The "as" helper does sudo in non-login shell mode, does preserve only PATH environment variable. 
      Depends on your sudoers file, if all used environment variables will be passed to the sudoed command. The environment cleanup is, for instance, a reason why bashible re-runs itself and doesn't just export all it's functions.
      Will be fixed in the future (see TODO.txt).

##### How to call a blebook from a blebook?

```bash
call '/another/path/to/blebook.ble'
```
The working directory of the called blebook will be set to the same directory as the blebook's resides in, but won't affect later commands in the calling blebook.

##### How to skip tasks which have been already done?

either using an universal "when" helper,

```bash
@ Install nginx unless already
   - when 'test -f /usr/bin/nginx' already
   - yum install -y nginx
```
or the same using "already_if" helper

```bash
@ Install nginx unless already
   - already_if test -f /usr/bin/nginx
   - yum install -y nginx
```

##### How to write more complex if expressions?

```bash
@ Install nginx unless its executable and configuration file exists
   - when 'test -f /usr/bin/nginx && test -f /etc/nginx/nginx.conf' already
   - yum install -y nginx
```
(the first argument of the "when" helper is evaluated as a bash)

##### How to check if a blebook is executed under a certain user?

```bash
unless 'test $USER = user1' fail "only user1 can run this blebook"
```

##### How to use more complicated bash code as a task?
```bash
- already_if eval 'foo bar baz'
```

##### How to extend bashible with my own functions (helpers)?
```bash
import /etc/my/functions.sh
```
note: using "source" is not sufficient; sourced functions are not populated into child processes (when another blebooks are called from the parent one), also are not usable after sudo which usually cleans environment variables (so even if functions have been exported).

##### How to set a variable depending on a task beeing executed?

use "export",

```bash
@ Installing nginx unless already
  - already_if test -d /etc/nginx
  - yum install nginx
  - set_var NGINX_INSTALLED echo 1

@ Configuring nginx if freshly installed
  - skip_if test $NGINX_INSTALLED = 1
  - cp -a /defaults/nginx/* /etc/nginx/
```
