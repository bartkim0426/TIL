# celery unitest using memory broker

There're multiple ways to use celery in unittest.

- mock
- eager mode
- memory (filesystem) broker

## Mock




## Eager mode

```
CELERY_TASK_ALWAYS_EAGER = True
```


## Memory broker

```
CELERY_BROKER_URL = 'memory://localhost/'
```

