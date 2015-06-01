# BASHIBLE

Bashible is a deployment/automation tool inspired by Ansible playbooks, but written in Bash and simplified. You can consider it as a bash script on steroids.

## Why?

Tools like Puppet, Chef or Ansible are sometimes too heavy for simple things. If you are in a hurry, there's no time to write multiple lines of code to achieve just a copy of a single file. On the other hand, without any tools, shell scripts tend to become difficult to read and usually they are also full of unhandled failures.

## Example

An example blebook:

```bash
@ Adding user foo and his public key
  - may_fail useradd foo
  - add_line "$PUBLIC_KEY" /home/foo/.ssh/authorized_keys

@ Running bundle install
  - cd /var/www
  - as foo bundle install

@ Install nginx unless already
  - already_if test -x /usr/sbin/nginx
  - apt-get install nginx
  - rsync -av /adm/config/webserver/ /

@ Start nginx and ensure it is running
  - service nginx start
  - timeout 20 wait_for 10.0.3.188:80 up
```

The very same in Bash:

```bash
basedir=`dirname $(readlink -f "$0")`

cd "$basedir" || { echo "can't chdir"; exit 1; }
echo "Adding user foo and his public key"
useradd foo
if ! grep "$PUBLIC_KEY" /home/foo/.ssh/authorized_keys; then
   echo "$PUBLIC_KEY" >> /home/foo/.ssh/authorized_keys || { echo "can't edit file"; exit 1; }
fi

cd "$basedir" || { echo "can't chdir"; exit 1; }
echo "Running bundle install"
cd /var/www || { echo "can't chdir"; exit 1; }
sudo -u foo bundle install || { echo "can't bundle install"; exit 1; }

cd "$basedir" || { echo "can't chdir"; exit 1; }
if [ ! -x /usr/sbin/nginx ]; then
  echo "Installing nginx unless already"
  apt-get install nginx || { echo "apt get failed"; exit 1; }
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


## How does it work?

  - The only dependency of bashible is the Bash.
  - Bashible blebook is a bash script. Even "-" and "@" are bash functions.
  - In a blebook, there are blocks starting with "@" and tasks starting with "-".
  - Blocks have two purposes:
       - skipping tasks that already have been done
       - the current working directory of each block starts in the same directory as the blebook resides, unless you explicitly specify elsewhere
  - If a task fails, the complete run of the blebook fails.
  - You can use environment variables to modify the run of a blebook (skip tasks, etc.)
  - You can call another blebook(s) from a blebook (like dependencies), moreover bashible doesn't call the same blebook twice if it is called in an inherited blebook, too.
  - Bashible implements helper functions to simplify common tasks (e.g. adding lines to files, commenting lines in files, etc.)
  - You can use your helpers by sourcing functions or changing PATH to use commands from a directory.
  - A change of environment variables in an inherrited blebook doesn't affect it's parent.
  - Finally, you can use a bash code if you need something special :-)


### What BASHIBLE does not (and won't do)?

Re-implement The World. 

For instance, there are beautiful tools to run bashible scripts remotely: ssh, rsync, or pdsh. Therefore bashible doesn't either re-implement the remote access or maintain a database of hosts.

Bashible is not perfect. For instance, there is a helper "user_add" for adding users. It adds an user and skips it if the user has been added already. But the helper doesn't modify the user later when you decide to change it's group or id. Maybe even the user_add helper is too much and will be removed in the future. So, the bashible is very suitable for new installations on fresh machines, but not for later updating (Chef or Puppet would better take care of the system state; installed packages's versions, updates, etc.)

Bashible doesn't run on all platforms. It would be great to have a helper "install_package" which will internally call yum on Centos, apt-get on Debian - but - why? In the end, the bashible script could also be 1MB big... Fortunatelly there is a possibility to extend it by sourcing your own functions.

### TODO

Write a description of each core function and each helper. Create a set of working examples. Create a typical use case example. Write FAQ. Stabilize functions. Better sudo implementation (if possible? e.g. use chpst instead - if found, etc.); extract env (PATH and _IMPORTED in the top of the script instead of using /usr/bin/env - use _PRESERVE_ENV and "preserve" instead of "export")
 => Release version 1.0.

### Suggestions and patches are welcome!

The project is very young, but is already used for automatic installation of an application on a remote machine. 

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
      Will be fixed in the future (see TODO).

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
- eval 'foo bar baz'
```

##### How to extend bashible with my own functions (helpers)?
```bash
import /etc/my/functions.sh
```
note: using "source" is not sufficient; sourced functions are not populated into child processes (when another blebooks are called from the parent one), also are not usable after sudo which usually cleans environment variables (so even if functions have been exported).

TODO

### Description of core functions

TODO
