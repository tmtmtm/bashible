#### base_dir PATH

Sets a base directory. On every block of tasks start, the current working directory is reset to this base dir.
The default base directory is the same directory as where the script resides in.

Example:

```bash

base_dir '/etc'

@ Working in /etc
  - cp passwd passwd.bak
  - cp shadow shadow.bak

@ Still working in /etc
  - cp group group.bak

reset_base_dir

@ Now working in the original directory
  - ls

```

##### See also

[reset_base_dir](reset_base_dir.md)  

