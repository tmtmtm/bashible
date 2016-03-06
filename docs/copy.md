##### copy SOURCE_PATH [MODE] [OWNER]

Copy a file from source to the current directory. Optionally set permissions and/or ownership.

If the source path is relative, refers to the directory of the bashible script.

```bash
@ Copying some files to homedir
  - cd /home/foobar
  - copy "skel/.bashrc" 750 foobar:foobar
  - copy "skel/.bash_logout" 750 foobar:foobar
```

