## Got permission denied issue in linux

https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue


linux에서 root 권한이 아닌 상태로 docker를 실행하면 권한 문제가 발생할 수 있다.

```
[linux@localhost ]$ docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/json: dial unix /var/run/docker.sock: connect: permi
ssion denied
```

이런 경우 docker group에 해당 유저를 추가해주어야한다.


1. 보통은 docker group이 생겼을테지만, 만약 없으면 생성해준다.

```
$ sudo groupadd docker
```

2. docker group에 해당 유저를 추가

```
$ sudo usermod -aG docker $USER
```

3. 로그아웃 후 다시 로그인하거나 다음 명령어를 실행시켜야 적용이 된다.

```
$ newgrp docker
```

