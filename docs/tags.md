##### tags TAG1 TAG2 ...

Reacts on a special environment variable TAGS.

You can match these tags and skip actions.

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

Now execute the script with no TAGS,

 ```bash
  bashible script.ble
 ```

=> all three blocks will be executed

If you execute the script with TAGS set,

 ```bash
  TAGS="compile install" bashible script.ble
 ```

=> again all three blocks will happen (if no tags are specified - as in the third block - it will run by default)

If you execute bashible with another TAGS like this,

 ```bash
  TAGS="compile" bashible script.ble
 ```

=> only the first and the third block will be executed.

