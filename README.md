# BASHIBLE

Bashible is a deployment/automation tool inspired by Ansible playbooks, but written in Bash and simplified.

Typical use case? Installing & running a complete application on a freshly installed VM.

### Example playbook: install Redis from sources

```bash
@ Install prerequisities
  - as root yum_install make gcc tcl rsync

@ Installing redis from sources
  - already_if which redis-server
  - mkdir -p ~/redis
  - cd ~/redis
  - wget http://download.redis.io/releases/redis-3.0.1.tar.gz
  - tar xzf redis-3.0.1.tar.gz
  - cd redis-3.0.1
  - make
  - make test
  - as root make install

@ Copying redis files and fixing permissions
  - as root rsync -lrv redis_files/ /
  - as root chmod 755 /etc/rc.d/init.d/redis-server

@ Allowing memory overcommit
  - as root add_line "vm.overcommit_memory = 1" /etc/sysctl.conf
  - may_fail as root sysctl vm.overcommit_memory=1

@ Configuring redis service
  - already_if test -f /etc/init.d/redis-server
  - as root chmod u+x /etc/init.d/redis-server
  - as root chkconfig --add redis-server
  - as root chkconfig --level 345 redis-server on

@ Starting redis
  - as root service redis-server start
```

then you would run `bashible redis.ble`, but this is just an example. 

Really working examples will be packed withing the project later.

## Why?
Tools like Puppet, Chef or Ansible are sometimes too heavy for simple things. If you are in a hurry, there's no time to write multiple lines of code to achieve just a copy of a single file. On the other hand, without any tools, shell scripts tend to become difficult to read and usually they are also full of unhandled failures.

## => How does it work?

  - The only dependency of bashible is the Bash.
  - Bashible playbook is a bash script. Even "-" and "@" are bash functions.
  - In a playbook, there are blocks starting with "@" and tasks starting with "-".
  - Blocks have two purposes:
       - skipping tasks that already have been done
       - the current working directory of each block starts in the same directory as the playbook resides, unless you explicitly specify elsewhere
  - If a task fails, the complete run of the playbook fails.
  - You can use environment variables to modify the run of a playbook (skip tasks, etc.)
  - You can call another playbook(s) from a playbook (like dependencies), moreover bashible doesn't call the same playbook twice if it is called in an inherited playbook, too.
  - Bashible implements helper functions to simplify common tasks (e.g. adding lines to files, commenting lines in files, etc.)
  - You can use your helpers by sourcing functions or changing PATH to use commands from a directory.
  - A change of environment variables in an inherrited playbook doesn't affect it's parent.
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

##### How to run a playbook a different user? (sudo)
If you want to run the whole playbook as another user, then either run bashible via sudo, 
```bash
sudo -u user1 bashible playbook.ble
``` 
or inside a playbook use "as" helper, 
```bash
 - as user1 command... 
```
or call a playbook from a playbook the same way,
```bash
as user1 call './another_playbook.ble'
```

note: The "as" helper does sudo in non-login shell mode, does preserve only PATH environment variable. 
      Depends on your sudoers file, if all used environment variables will be passed to the sudoed command. The environment cleanup is, for instance, a reason why bashible re-runs itself and doesn't just export all it's functions.
      Will be fixed in the future (see TODO).

##### How to call a playbook from a playbook?

```bash
call '/another/path/to/playbook.ble'
```
The working directory of the called playbook will be set to the same directory as the playbook's resides in, but won't affect later commands in the calling playbook.

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

##### How to check if a playbook is executed under a certain user?

```bash
unless 'test $USER = user1' fail "only user1 can run this playbook"
```

##### How to use more complicated bash code as a task?
```bash
- eval 'foo bar baz'
```

##### How to extend bashible with my own functions (helpers)?
```bash
import /etc/my/functions.sh
```
note: using "source" is not sufficient; sourced functions are not populated into child processes (when another playbooks are called from the parent one), also are not usable after sudo which usually cleans environment variables (so even if functions have been exported).

TODO

### Description of core functions

TODO
