# BASHIBLE

Bashible is a deployment/automation tool written in Bash, inspired by Ansible. 

Bashible blebook is a better readable bash script.

## Why?

Tools like Puppet, Chef or Ansible are sometimes too heavy to do simple things. If you are in a hurry, there's no time to write multiple lines of code to achieve a copy of a single file. On the other hand, without any tools, shell scripts tend to become difficult to read and usually they are also full of unhandled failures.

## Example

An example blebook:

```bash
@ Adding user webuser and his public key
  - may_fail useradd webuser
  - add_line "$PUBLIC_KEY" /home/webuser/.ssh/authorized_keys

@ Running bundle install
  - cd /var/www
  - as webuser bundle install

@ Install nginx unless already
  - already_if test -x /usr/sbin/nginx
  - yum_install nginx
  - rsync -av /adm/config/webserver/ /

@ Start nginx and ensure it is running
  - service nginx start
  - timeout 20 wait_for_tcp 10.0.3.188:80 up
```

The very same in Bash:

```bash
basedir=`dirname $(readlink -f "$0")`

cd "$basedir" || { echo "can't chdir"; exit 1; }
echo "Adding user webuser and his public key"
useradd webuser
if ! grep "$PUBLIC_KEY" /home/webuser/.ssh/authorized_keys; then
   echo "$PUBLIC_KEY" >> /home/webuser/.ssh/authorized_keys || { echo "can't edit file"; exit 1; }
fi

cd "$basedir" || { echo "can't chdir"; exit 1; }
echo "Running bundle install"
cd /var/www || { echo "can't chdir"; exit 1; }
sudo -u webuser bundle install || { echo "can't bundle install"; exit 1; }

cd "$basedir" || { echo "can't chdir"; exit 1; }
if [ ! -x /usr/sbin/nginx ]; then
  echo "Installing nginx unless already"
  yum install -y nginx || { echo "yum failed"; exit 1; }
  echo "Installing default vhosts.d contents unless already"
  rsync -av /adm/config/webserver/ / || { echo "rsync failed"; exit 1; }
fi

echo "Start nginx and ensure it is running"
service start nginx
timeout 20 bash -c '
  while ! netstat -ltn | grep LISTEN | grep 10.0.3.188:80; do
    sleep 1; 
  done
'
```

`bashible blebook.ble` would then run the blebook, but it is just an example. Really working examples will come later.

## Install & usage

```bash
 cd /usr/local/bin
 wget https://raw.githubusercontent.com/mig1984/bashible/master/bashible
 chmod 755 bashible
```

 then

```bash
 bashible your-blebook.ble
```

## How does it work?

  - Bashible blebook is a better readable bash script.
  - There are blocks **"@ A comment..."** and tasks **"- command ..."**
  - Blocks have two purposes:
     - **skipping tasks** that already have been done
     - the **working directory** of each block starts in the same directory as the blebook resides, unless you explicitly specify elsewhere
  - If a **task fails** => the complete run of the **blebook fails**.
  - You can use **environment variables** to modify the run of a blebook (skip tasks, etc.)
  - You can **call** another blebook(s) from a blebook (like dependencies), moreover bashible doesn't call the same blebook twice (unless forced).
  - Bashible implements **helper functions** to simplify common tasks (e.g. adding lines to files, commenting lines in files, etc.)
  - You can use your **own helpers** (importing functions).
  - Finally, you can use a bash code if you need something special.

  At the moment it is used on Linux (Centos) only. May not work on other platforms (sed, netstat, etc. may behave differently elsewhere).

  Suggestions and patches are welcome! :-)


### Description of core functions

##### already

a) If used before tasks started, stops execution of the blebook (exits the process like "stop").

```bash
when 'var_is_empty ENVIRONMENT' already
```

b) If used inside a tasks block, skips following tasks up to the next block.

```bash
@ Installing nginx unless already
  - when 'test -x /usr/sbin/nginx' already
  - yum install nginx
```

note: does the same as the "skip", but outputs a different message

##### already_if COMMAND ...

Runs the command and if it results in true, calls "already"

a) before task blocks started

```bash
already_if var_is_empty ENVIRONMENT
```

b) inside a tasks block

```bash
@ Installing nginx unless already
  - already_if test -x /usr/sbin/nginx
  - yum install nginx
```


##### as USER COMMAND ...

Run the command as the user. Sudo in non-login shell mode is used (the current working dir should be preserved).

notice: only some environment variables are copied to the sudoed command (e.g. PATH), other depends on your sudoers config (the sudo may clear the environment).

```bash
@ Installing dependencies of rails app
  - cd /home/rails
  - as www-data bundle install
```

##### base_dir PATH

Change a base working directory. On each tasks block the working directory is (re)set to the base directory.
Unless set, defaults to the same directory as the blebook resides in.

The reset_base_dir resets it to the default.

```bash
base_dir "/home/user1"

@ Doing something in /home/user1
- foo
- bar

...

reset_base_dir
```

##### call PATH [force]

Call another blebook. It will run in a new process, therefore it won't affect the caller anyhow.
The working directory of the called blebook is set to the same directory as the blebook file resides in.
The "call" is used also for the top blebook.

Unless you specify "force", the same blebook won't run again if already run in a parent.

```bash
call "./another-blebook.ble"
```


##### fail MESSAGE

Interrupts execution with a message displayed in red on stdout.

```bash
- when 'var_is_empty SERVERNAME' fail "variable SERVERNAME must be set"
```

##### info MESSAGE

Prints info message to stdout.

```bash
info "foobar"
```

##### import PATH

Use "import" instead of "source". It does the same, but remembers imported files. When a command is run using sudo, all files will be re-sourced
(otherwise functions won't be reachable in the child process).

```bash
import '/usr/local/share/mylib.sh'
```

##### only_on HOST HOST ...

a) If used before task blocks, stops execution of the blebook unless it is not run on one of specified hosts (only_on).

```bash
only_on web123
```

b) If used inside a task block, skips following tasks unless it is not run on one of specified hosts (only_on).

```bash
@ Installing nginx
  - only_on web123 web125 web126
  - ...
```

The "not_on" does the opposite.

##### not COMMAND ...

Runs the command and negate it's exit status.

```bash
@ Doing cleanup
  - already_if not test -f '/tmp/foobar'
```

(or use "when" or "unless" with an exclamation mark)

##### not_on HOST HOST ...

see the "only_on" above (does the opposite)

##### may_fail COMMAND ...

Normally if a command fails, execution of a blebook is stopped.
Sometimes tasks may fail and it doesn't matter.

```bash
@ Cleaning up
  - may_fail rmdir /home
  - may_fail rmdir /var
```

##### quiet COMMAND ...

Runs the command with output directed to /dev/null.

```bash
- when 'quiet grep centos /etc/hosts' already
```

##### reset_base_dir

see the "base_dir" above

##### skip

a) if used before all task blocks started, stops execution of the blebook

```bash
when 'var_is_empty ENVIRONMENT' skip
```

b) skips following tasks up to a next block

```bash
when 'var_is_empty ENVIRONMENT' skip
```

note: does the same like the "already", but outputs a different message

##### skip_if COMMAND ...

Runs the command and if it results in true, calls "skip"

a) before task blocks started

```bash
skip_if var_is_empty ENVIRONMENT
```

b) inside a tasks block

```bash
@ Installing nginx unless already
  - skip_if test -x /usr/sbin/nginx
  - yum install nginx
```

##### stop

Stops execution of the blebook with a message (exits the process).

```bash
when 'var_is_empty ENVIRONMENT' stop
```

##### stop_if COMMAND ...

Runs the command and if it results in true, calls "stop".

```bash
stop_if var_is_empty ENVIRONMENT
stop_if not test -d /etc/nginx
```


##### tag TAG

If a blebook is run with TAGS environment variable set (tag words separated by a comma),

a) before task blocks started, stops execution of the blebook unless TAG is found among specified tags.

```bash
tag install deploy
```

b) inside a task block, skips following tasks unless TAG is found among specified tags.

```bash
@ Installing nginx
  - tag install
  - ...
```

##### unless 'EVALUATED STRING' COMMAND ...

see the "when" below (does the opposite)

##### warn MESSAGE

Prints warn message to stdout.

```bash
info "foobar"
```

##### when 'EVALUATED STRING' COMMAND ...

Runs the command if the evaluated string returns true ("when") or false ("unless").

```bash
when 'var_is_empty ENVIRONMENT' fail 'ENVIRONMENT variable is not set!'
unless '! var_is_empty ENVIRONMENT' fail 'ENVIRONMENT variable is not set!'

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

##### is_rpm_installed NAME

Returns true if the rpm is installed.

```bash
when '! is_rpm_installed nginx' fail 'nginx RPM is not installed'
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

##### replace_matching REGEXP STRING FILE

Replace matching regexps in the file with the string.

```bash
replace_matching 'enabled=0' 'enabled=1' /etc/default/foo.cfg
```

##### set_contents CONTENTS FILE

If the file is empty, write the contents. If the contents is already there, does nothing. Fails, if contents is there but differs.

```bash
set_contents 'centos-local' /etc/default/hostname
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

##### var_is_empty VARNAME

Results true if the env variable VARNAME is empty.

```bash
when 'var_is_empty ENVIRONMENT' fail 'ENVIRONMENT variable must be set'
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
  - export NGINX_INSTALLED=1

@ Configuring nginx if freshly installed
  - skip_if 'var_is_empty NGINX_INSTALLED'
  - cp -a /defaults/nginx/* /etc/nginx/
```
