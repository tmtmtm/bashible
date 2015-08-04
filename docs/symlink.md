##### symlink SRC DEST

Creates a symbolic link from SRC to DEST unless already.

(warn: it's not so clever as the "ln -s", so you have to use 
absolute path also in the DEST)

```bash
@ Symlinking some files
  - symlink '/usr/local/bin/redis' '/usr/bin/redis'
  - symlink '/usr/local/bin/nginx' '/usr/bin/nginx'
```
