##### unless 'EVALUATED STRING' COMMAND [ARG1] [ARG2] ...

Does the opposite to `when`: runs a command unless an evaluated string returns true.

```bash
@ Doing some checks
  - when 'test -z "$ENVIRONMENT"' fail 'ENVIRONMENT variable is not set!'
  - when 'test ! -n "$ENVIRONMENT"' fail 'ENVIRONMENT variable is not set!'
  - unless '! test -z "$ENVIRONMENT"' fail 'ENVIRONMENT variable is not set!'
  - unless 'test ! -z "$ENVIRONMENT"' fail 'ENVIRONMENT variable is not set!'

@ Installing nginx unless already
  - when 'test -f /etc/nginx/nginx.conf' skip
  - unless '! test -f /etc/nginx/nginx.conf' skip
```

You can also use more complex constructions in the evaluated string,

```bash
when 'test -d /etc/nginx && test -d /var/www' skip
```

