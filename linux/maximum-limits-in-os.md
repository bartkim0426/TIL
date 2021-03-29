# Maximum limit in Operating System

계기

회사에서 개발을 하던 중 `IOError: [Errno 24] Too many open files` 에러를 만났다.

너무 많은 이미지를 한번에 오픈해서 생기는 에러였는데, 정확히 내용을 몰라 회사 내부에 물어보니 OS의 limit과 관련된 내용이였다.

전혀 알지 못하던 내용이기 때문에 이번 기회에 정리해본다.

**한국어**

linux, MacOS를 포함한 OS는 열 수 있는 파일과 프로세스의 수에 제한이 있다. 이런 제한은 시스템 과부하를 방지하기 위해 존재한다.

이를 깊이있게 들어가면 "자원을 효율적으로 관리하는 것"인 운영체제의 핵심까지 가게 되지만, 
이는 너무 방대하기 때문에 자원을 효율적으로 관리하는 방법 중의 하나로 한 번에 열 수 있는 파일과 프로세스의 수를 제한한다.

머신의 자원 제한은 hard limit, soft limit이 있다.
- hard limit: 한 번 설정하면 root 권한으로만 수정 가능
- soft limit: hard limit까지 조절 가능

## ulimit command

ulimit은 shell과 shell이 실행한 프로세스에 대해 시스템 상의 사용 제한을 해주는 명령어. `man ulimit`을 보면 자세한 내용을 확인할 수 있다

```
man ulimit

-a     All current limits are reported
-b     The maximum socket buffer size
-c     The maximum size of core files created
-d     The maximum size of a process's data segment
-e     The maximum scheduling priority ("nice")
-f     The maximum size of files written by the shell and its children
-i     The maximum number of pending signals
-l     The maximum size that may be locked into memory
-m     The maximum resident set size (many systems do not honor this limit)
-n     The maximum number of open file descriptors (most systems do not allow this value to be set)
-p     The pipe size in 512-byte blocks (this may not be set)
-q     The maximum number of bytes in POSIX message queues
-r     The maximum real-time scheduling priority
-s     The maximum stack size
-t     The maximum amount of cpu time in seconds
-u     The maximum number of processes available to a single user
-v     The maximum amount of virtual memory available to the shell and, on some systems, to its children
-x     The maximum number of file locks
-T     The maximum number of threads
```

다양한 옵션이 있는데, 사용해보자.

```
# 모든 자원의 limit을 확인
ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 125032
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 4096
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited

# hard limit 자원 확인
ulimit -aH
```

각각의 값을 보려면 위 결과에서 괄호 안의 값을 넣어주면 된다.ㅣo

```
# core file size
ulimit -c
# open files
ulimit -n
```

값을 변경하려면 각 옵션 뒤에 값을 지정하면 된다. 정수값 대신 `unlimited`를 주면 무제한 값으로 부여됨.

```
ulimit -c unlimited
```

이렇게 값을 변경하면 로그인된 user의 세션에서만 적용되고, OS 재부팅 시에는 초기값으로 설정된다.

영구적으로 값을 변경하기 위해서는 `/etc/security/limits.conf`를 수정해주면 된다.

`vi /etc/security/limits.conf`

```
...
# End of file
username      hard   stack          8096
*             soft   stack          8096
```



---

**English**

Operating Systems (Linux and MacOS included) have settings which limit the number of files and processes 


https://wilsonmar.github.io/maximum-limits/
