# BASHIBLE

Bashible is a deployment/automation tool written in Bash.

## Why?

Tools like Puppet, Chef or Ansible are sometimes too heavy to do simple things. On the other hand, plain shell scripts tend 
to be difficult to read and full of unhandled failures. Bashible stays somewhere in the middle: a bashible script is just a bash script,
but better structured with additional features.

features:

  - chaining of bash functions and commands
  - environment variables and script arguments can be used as usual
  - a failed command halts the process
  - skipping tasks which have already been done
  - dependencies (calling a script from a script)
  - running tasks via sudo plus preserving environment variables over the sudo
  - delayed script calling (an action is done only once at the very end)
  - auto-chdir (the working directory is always the same)
  - setting variables safe (checking whether they are not empty)
  - does not re-implement The World (if you want a remote access, use ssh or pdsh, use bash for loops, etc.)
  - helper functions (files editing, etc.)

  At the moment it is used on the Centos Linux only. May not work on other platforms (sed, netstat, etc. may behave differently).

  Suggestions are welcome! :-)

## Example

A bashible script.ble (scripts should have the .ble extension):

```bash

@ Prerequisities
  - on host3 host4
  - i_am root
  - call ./system_base.ble

@ Setting up some variables
  - set_var FQDN nonempty hostname
  - set_var DOMAIN2 nonempty evaluate "echo $FQDN | awk -F . '{ print \$(NF-1) \".\" \$NF }' "
  # expects an ip address to be passed as the first argument to the script
  - set_var MY_IP echo $1
  # expects a public key to be passed on stdout
  - set_var PUBLIC_KEY nonempty cat

@ Adding user webuser and his public key
  - may_fail useradd webuser
  - add_line "$PUBLIC_KEY" /home/webuser/.ssh/authorized_keys

@ Running bundle install
  - cd /var/www
  - as webuser bundle install

@ Install nginx unless already
  - skip_if test -x /usr/sbin/nginx
  - yum_install nginx
  - rsync -av /shared/config/webserver/ /

@ Start nginx and ensure it is running
  - service nginx start
  - timeout 20 wait_for_tcp "$MY_IP":80 up
```

You would then run the script by executing

`echo YOUR PUBLIC KEY | bashible script.ble 10.20.30.40`

By the way, `@` represents a block of tasks, `-` represents a task. Both are bash functions, named `@` and `-` (their behaviour is explained below).


About the same in plain bash:

```bash
basedir=`dirname $(readlink -f "$0")`

if [ "`hostname`" == host3 -o "`hostname`" == host5 ]; then
  exit 1
fi
if [ "$USER" != guest1 -a "$USER" != guest2 ]; then
  exit 1
fi
source './system_base.ble' || { echo 'error sourcing functions'; exit 1; }

echo "Setting up some variables"
FQDN=`hostname`
if [ -z "$FQDN" ]; then
  echo "FQDN is empty"; exit 1
fi
DOMAIN2=`echo $FQDN | awk -F . '{ print \$(NF-1) \".\" \$NF }' "`
if [ -z "$DOMAIN2" ]; then
  echo "DOMAIN2 is empty"; exit 1
fi
MY_IP=$1
if [ -z "$MY_IP" ]; then
  echo "MY_IP is empty"; exit 1
fi
PUBLIC_KEY=`cat`
if [ -z "$PUBLIC_KEY" ]; then
  echo "PUBLIC_KEY is empty"; exit 1
fi

echo "Adding user webuser and his public key"
useradd webuser
if ! grep "$PUBLIC_KEY" /home/webuser/.ssh/authorized_keys; then
   echo "$PUBLIC_KEY" >> /home/webuser/.ssh/authorized_keys || { echo "can't edit file"; exit 1; }
fi

echo "Running bundle install"
cd /var/www || { echo "can't chdir"; exit 1; }
sudo -u webuser bundle install || { echo "can't bundle install"; exit 1; }

cd "$basedir" || { echo "can't chdir"; exit 1; }
if [ ! -x /usr/sbin/nginx ]; then
  echo "Installing nginx unless already"
  yum install -y nginx || { echo "yum failed"; exit 1; }
  echo "Installing default vhosts.d contents unless already"
  rsync -av /shared/config/webserver/ / || { echo "rsync failed"; exit 1; }
fi

echo "Start nginx and ensure it is running"
service start nginx
timeout 20 bash -c '
  while ! netstat -ltn | grep LISTEN | grep "$MY_IP":80; do
    sleep 1; 
  done
'
```


## Install & usage

```bash
 wget https://raw.githubusercontent.com/mig1984/bashible/master/bashible
 chmod 755 bashible
 ./bashible your-script.ble ARG1 ARG2 ...
```

## Functions

### Core functions

[@ MESSAGE](docs/@.md)  
[- COMMAND ARG1 ARG2 ...](docs/-.md)  
[as USER COMMAND ARG1 ARG2 ...](docs/as.md)  
[base_dir PATH](docs/base_dir.md)  
[call PATH ARG1 ARG2 ...](docs/call.md)  
[delayed_call PATH](docs/delayed_call.md)  
[empty COMMAND ARG1 ARG2 ...](docs/empty.md)  
[evaluate STRING](docs/evaluate.md)  
[export_var NAME COMMAND ARG1 ARG2 ...](docs/export_var.md)  
[fail MESSAGE](docs/fail.md)  
[finish MESSAGE](docs/finish.md)  
[finish_if COMMAND ARG1 ARG2 ...](docs/finish_if.md)  
[halt MESSAGE](docs/halt.md)  
[halt_if COMMAND ARG1 ARG2 ...](docs/halt_if.md)  
[force_call PATH ARG1 ARG2 ...](docs/force_call.md)  
[i_am](docs/i_am.md)  
[i_am_child](docs/i_am_child.md)  
[import PATH](docs/import.md)  
[not COMMAND ARG1 ARG2 ...](docs/not.md)  
[on HOST HOST ...](docs/on.md)  
[may_fail COMMAND ARG1 ARG2 ...](docs/may_fail.md)  
[quiet COMMAND ARG1 ARG2 ...](docs/quiet.md)  
[reset_base_dir](docs/reset_base_dir.md)  
[set_var NAME COMMAND ARG1 ARG2 ...](docs/set_var.md)  
[skip MESSAGE](docs/skip.md)  
[skip_if COMMAND ARG1 ARG2 ...](docs/skip_if.md)  
[tags TAG TAG ...](docs/tags.md)  
[unless 'EVALUATED STRING' COMMAND ARG1 ARG2 ...](docs/unless.md)  
[when 'EVALUATED STRING' COMMAND ARG1 ARG2 ...](docs/when.md)  

### Other functions

[add_line LINE PATH](docs/add_line.md)  
[append_line LINE PATH](docs/append_line.md)  
[comment_line_matching REGEXP PATH](docs/comment_line_matching.md)  
[install_gem NAME NAME ...](docs/install_gem.md)  
[prepend_line LINE PATH](docs/prepend_line.md)  
[remove_line_matching REGEX PATH](docs/remove_line_matching.md)  
[replace_line_matching REGEXP STRING PATH](docs/replace_line_matching.md)  
[replace_matching REGEXP STRING PATH](docs/replace_matching.md)  
[rpm_is_installed NAME](docs/rpm_is_installed.md)  
[set_contents CONTENTS PATH](docs/set_contents.md)  
[set_contents_safe CONTENTS PATH](docs/set_contents_safe.md)  
[symlink SRC DEST](docs/symlink.md)  
[timeout SECS COMMAND ARG1 ARG2 ...](docs/timeout.md)  
[uncomment_line_matching REGEXP PATH](docs/uncomment_line_matching.md)  
[wait_for_tcp MATCH up|down](docs/wait_for_tcp.md)  
[yum_install NAME NAME ...](docs/yum_install.md)  
