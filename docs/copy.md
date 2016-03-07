##### copy SOURCE_PATH [MODE] [OWNER]

Copy a file from source to the destination. Optionally sets permissions and/or ownership.

(it's the same as 'cp' plus chmod/chown)

```bash
@ Copying some files to homedir
  - cd /home/foobar
  - copy "$_BASE_DIR/skel/.bashrc" . 750 foobar:foobar
  - copy "$_BASE_DIR/skel/.bash_logout" . 750 foobar:foobar
```
