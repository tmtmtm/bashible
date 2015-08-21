##### template TEMPLATE_PATH RESULT_PATH

Implements a very simple Bash templating engine. You can use it for substituting variables.

Example:

Let's have a mysql.conf.tpl

```
...
## Buffer settings
key-buffer-size = $( echo $KEY_BUFFER_SIZE )
read-buffer-size = 2M
sort-buffer-size = $( echo $SORT_BUFFER_SIZE )
max-join-size = 512M
max-heap-table-size = 128M
...
```

Now use (i.e. execute) the template in a bashible script:

```bash

KEY_BUFFER_SIZE=1G
SORT_BUFFER_SIZE=128M

@ Creating mysql.conf
  - template mysql.conf.tpl /etc/mysql/mysql.conf
```

All variables must be set, otherwise the `template` function exits 1.

It works very simple: if a command in brackets produces any output on stderr,
the `template` function exits 1. (Probably there's no other way how to detect 
failing sub-commands.)

---

Moreover you can use commands in templates the same way as in bashible scripts
(just don't forget to prefix commands with '-', otherwise it won't stop the execution)

```
...
## Buffer settings
key-buffer-size = $( - not empty cat /settings/key-buffer-size.txt )
read-buffer-size = 2M
sort-buffer-size = $( - not empty cat /settings/sort-buffer-size.txt )
max-join-size = 512M
max-heap-table-size = 128M
...
```

---

It is also possible to use loops inside brackets:

```
...
## Buffer settings
$( for db in $BINLOG_DO_DB; do echo "binlog-do-db = $db"; done )
key-buffer-size = $( echo $KEY_BUFFER_SIZE )
read-buffer-size = 2M
sort-buffer-size = $( echo $SORT_BUFFER_SIZE )
max-join-size = 512M
max-heap-table-size = 128M
...
```
