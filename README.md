# BASHIBLE

Bashible is a deployment/automation tool written in Bash.

## Why?

Tools like Puppet, Chef or Ansible are sometimes too heavy to do simple things. On the other hand, plain shell scripts tend 
to be difficult to read and full of unhandled failures. Bashible stays somewhere in the middle: a bashible script is just a bash script,
but better structured with additional features.

features:

  - skipping tasks which have already been done
  - a failed command halts the process
  - dependencies (calling a script from a script)
  - you can use environment variables and script arguments as usual
  - running tasks via sudo plus preserving defined environment variables
  - delayed script calling (do an action only once at the end)
  - auto-chdir (the working directory is always predictable)
  - safe setting variables (checking for empty values)
  - other helper functions (files editing, etc.)

  At the moment it is used on Centos Linux only. May not work on other platforms (sed, netstat, etc. may behave differently).

  Suggestions and patches are welcome! :-)

## Example

A bashible script:

```bash

@ Prerequisities
  - on host3 host4
  - i_am root
  - call ./system_base.ble

@ Setting up some variables
  - set_var fqdn not empty hostname
  - set_var domain2 not empty evaluate "echo $fqdn | awk -F . '{ print \$(NF-1) \".\" \$NF }' "

@ Adding user webuser and his public key
  - may_fail useradd webuser
  - add_line "$PUBLIC_KEY" /home/webuser/.ssh/authorized_keys

@ Running bundle install
  - cd /var/www
  - as webuser bundle install

@ Install nginx unless already
  - skip_if test -x /usr/sbin/nginx
  - yum_install nginx
  - rsync -av /adm/config/webserver/ /

@ Start nginx and ensure it is running
  - service nginx start
  - timeout 20 wait_for_tcp 10.0.3.188:80 up
```

`@` represents a block of tasks, `-` represents a task. Both are bash functions with names `@` and `-` (their behaviour is explained below).
You would then run the script by executing `bashible script.ble`.


About the same in Bash:

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
fqdn=`hostname`
if [ -z "$fqdn" ]; then
  echo "fqdn is empty"; exit 1
fi
domain2=`echo $fqdn | awk -F . '{ print \$(NF-1) \".\" \$NF }' "`
if [ -z "$domain2" ]; then
  echo "domain2 is empty"; exit 1
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


## Install & usage

```bash
 cd /usr/local/bin
 wget https://raw.githubusercontent.com/mig1984/bashible/master/bashible
 chmod 755 bashible
```

 then

```bash
 ./bashible your-script.ble
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
