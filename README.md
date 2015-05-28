# BASHIBLE

a deployment and/or automation tool inspired by Ansible playbooks, but written in Bash.

### An example playbook: install Redis on Centos from sources

```bash
@ Install prerequisities
  - yum_install make gcc tcl rsync

@ Installing redis from sources
  - already_if which redis-server
  - mkdir -p /usr/local/src
  - cd /usr/local/src
  - wget http://download.redis.io/releases/redis-3.0.1.tar.gz
  - tar xzf redis-3.0.1.tar.gz
  - cd redis-3.0.1
  - make
  - make test
  - make install

@ Copying redis files and fixing permissions
  - rsync -lrv redis_files/ /
  - chmod 755 /etc/rc.d/init.d/redis-server

@ Allowing memory overcommit
  - add_line "vm.overcommit_memory = 1" /etc/sysctl.conf
  - may_fail sysctl vm.overcommit_memory=1

@ Configuring redis service
  - already_if test -f /etc/init.d/redis-server
  - chmod u+x /etc/init.d/redis-server
  - chkconfig --add redis-server
  - chkconfig --level 345 redis-server on

@ Starting redis
  - service redis-server start
```

then you would run `bashible redis.ble`, but this is just an example and probably won't work.

Really working examples are in the project's TODO.

## Why?
Tools like Puppet, Chef or Ansible are sometimes too heavy for simple things. 
Especially when you are in a hurry, there's no time to write multiple lines 
of code just to achieve a copy of a single file. On the other hand, 
without any automation tools, shell scripts tend to be difficult to read 
and they are usually full of unhandled failures. 

## => How does it work?

  - The only dependency of bashible is the Bash.
  - Bashible runs it's playbooks locally, doesn't implement remote access (and it is a feature! :-))
  - Bashible's playbook is a bash script. Even "-" and "@" are bash functions.
  - In a playbook, there are blocks starting with "@" and tasks starting with "-".
  - Blocks have two purposes:
      - skipping tasks that already have been done, exection will continue on a next block
      - the current working directory of each block is always set to the same directory as the playbook resides in, unless you explicitly specify elsewhere
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

Bashible is not perfect. For instance, there is a helper "user_add" for adding users. It adds an user and skips it if the user has been added already. But the helper doesn't modify the user later when you decide to change it's group or id. Maybe even the user_add helper is too much and will be removed in the future.

Bashible doesn't run on all platforms. It would be great to have a helper "install_package" which will internally call yum on Centos, apt-get on Debian - but - why? In the end, the bashible script could also be 1MB big... Fortunatelly there is a possibility to extend it by sourcing your own functions.

### TODO

Write a description of each core function and each helper. Create set of working examples. Stabilize core functions. Release version 1.0.


### Suggestions and patches are welcome!

The project is very young, but is already used for automatic installation of an application on a remote machine. 

