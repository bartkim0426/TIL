# linux seminar about container

real linux에서 진행하는 세미나 ([컨테이너를 모르면 생겨나는 문제들](https://www.youtube.com/watch?v=Ir5c32q0kik))

docker나 container를 사용하면셔 잘 모르고 사용하다보면 문제 발생시 트러블슈팅을 잘 못하게 됨.

### 도커 port 번호 중복 문제 해결

```
$ docker run -d -p 80:80 httpd
docker: Error response from daemon: driver failed programming external connectivity on endpoint nervous_golick (760cb1579bf02397e12d490ee20bc748140c2ff99d2cf2e566424500020ba88a): Bind for 0.0.0.0:80 failed: port is already allocated.
```

이 문제를 구글링하면 그냥 80포트의 process를 죽이라는 식으로 많이 나옴

단순하게 `netstat -tupln | grep 80`으로 해서 `kill -9 xxx`로 종료하면 상황에 따라 문제가 생길 수 있음.

### 컨테이너는 프로세스다

- namespace, cgroup을 적용한 프로세스
  - cgroup: 얼마나 사용 가능한지
    - HW자원 (cpu, mem, disk, net ..)
  - namespace: 무엇을 볼 수 있는지 제한
    - application 관리 (커널 자원; proc, rootfs)

linux는 init 프로세스 (pid 1)으로부터 자식 프로세스를 만들어서 관리함.


