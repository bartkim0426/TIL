# Override entrypoint while docker run


example Dockerfile

```
FROM ubuntu
RUN apt-get update
ENTRYPOINT [“echo”, “Hello”]
CMD [“World”]
```

### override CMD

- Override CMD by adding the desired parameter

```
$ docker run <container_namme> Hello World
```

### override entrypoint

- with `--entrypoint` flag, can override default entrypoint

`docker run --entrypoint [new_command] [container_name] [optional:value]`

```
$ docker run -it --entrypoint /bin/bash <container_name>
```
