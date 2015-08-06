##### tags TAG [TAG2] ...

If there is a special environment variable TAGS given, you can match tags and skip actions.

Example:

```bash
@ Compiling redis
  - tags download compile
  - ./configure
  - make

@ Installing redis
  - tags install
  - make install

@ Starting redis service
  - service start redis
```

A) When you execute the script with no TAGS set, all three blocks will be executed.

 ```bash
  bashible script.ble
 ```

B) When you execute the script with TAGS compile and install, again all three blocks will be executed
(if no tags are specified - as in the third block - it will run by default)

 ```bash
  TAGS="compile install" bashible script.ble
 ```


C) If you execute the script with TAGS set to compile only,
then only the first and the third block will be executed.

 ```bash
  TAGS="compile" bashible script.ble
 ```
