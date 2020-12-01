# turn off swap memory

스왑 메모리를 확인하는 명령어

```
$ free -m

root@5f88a2f3456c:/# free -m
              total        used        free      shared  buff/cache   available
Mem:           1990         842         103           6        1044        1050
Swap:          1023          26         997
```

### Disable swap

아래 명령어로 swap을 끌 수 있다.

```
$ sudo swapoff -a
```

부팅되었을 떄를 대비해서 `fstab` 파일도 수정한다. (`vi /etc/fstab`)

```
# 이 줄을 주석처리 해준다.
# /dev/mapper/centos-swap swap swap defaults 0 0
```


