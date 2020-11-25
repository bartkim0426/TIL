# The Linux Command Line

> 다음 명령어들은 docker로 띄운 ubuntu 컨테이너 안에서 실행.

```
docker run -it ubuntu bash
unminimize
apt-get install man
```


## 5. Working with commands

`type`: 특정 명령어가 어떤 종류인지 보여주는 명령어

**명령어 종류**
- executable program
- built into the shell itself
- shell function
- alias

```
root@37ba9ce1e69f:/# type ls
ls is aliased to `ls --color=auto'
root@37ba9ce1e69f:/# type type
type is a shell builtin
root@37ba9ce1e69f:/# type cp
cp is /usr/bin/cp
```

`which`: display an executable's location


### command's documentation

`bash`는 빌트인 help 도구를 가지고 있음.

```
root@37ba9ce1e69f:/# help cd
cd: cd [-L|[-P [-e]] [-@]] [dir]
    Change the shell working directory.
    ...
```

대괄호는 optional item을 얘기함.


`--help`

많은 executable program은 `--help` 옵션을 제공한다


`man`: 프로그램의 manual page

섹션별로 인터페이스가 제공됨.

- 1: User command
- 2: Programming interfaces for kernel system calls
- 3: Programming interfaces to the C library
- 4: Special files such as device nodes and drivers
- 5: File formats
- 6: Games and amusements such as screen savers
- 7: Miscellaneous
- 8: System administration commands


위 섹션에서 필요한 부분만 찾을 수 있음

```
root@37ba9ce1e69f:/# man 5 passwd
```


`apropos`: 적절한 명령어를 보여줌

검색어로 특정 man page의 리스트를 볼 수 있다. (요새는 인터넷 검색이 활성화되어서 잘 쓰이지는 않을듯 하지만..)

```
root@37ba9ce1e69f:/# apropos partition
addpart (8)          - tell the kernel about the existence of a partition
cfdisk (8)           - display or manipulate a disk partition table
delpart (8)          - tell the kernel to forget about a partition
fdisk (8)            - manipulate disk partition table
partx (8)            - tell the kernel about the presence and numbering of on-disk partitions
resizepart (8)       - tell the kernel about the new size of a partition
sfdisk (8)           - display or manipulate a disk partition table
```


`whatis`: 한줄로 된 man page


```
root@37ba9ce1e69f:/# whatis ls
ls (1)               - list directory contents
```


`info`: GNU 프로젝트에서 제공하는 man page같은 프로그램.

- 하이퍼링크 제공
- info 내부 명령어 써야됨 (?, space, n, p, ENTER, Q 등...)


### Creating our own command with alias

`alias` 명령어를 사용하여 새로운 커맨드를 만들 수 있음.

```
root@37ba9ce1e69f:/# cd /usr; ls; cd -
bin  games  include  lib  lib32  lib64  libx32  local  sbin  share  src
/
root@37ba9ce1e69f:/# pwd
/
root@37ba9ce1e69f:/# type test
test is a shell builtin
root@37ba9ce1e69f:/# type foo
bash: type: foo: not found
root@37ba9ce1e69f:/# alias foo='cd /usr; ls; cd -'
root@37ba9ce1e69f:/# foo
bin  games  include  lib  lib32  lib64  libx32  local  sbin  share  src
/
root@37ba9ce1e69f:/# type foo
foo is aliased to `cd /usr; ls; cd -'
root@37ba9ce1e69f:/# unalias foo
root@37ba9ce1e69f:/# type foo
bash: type: foo: not found
```

## 6. Redirection

### Standard input, output, and error

대부분 프로그램은 어떤 형태로든 output을 제공함. 보통은 둘가지 타입

- 프로그램의 결과
- 에러 메세지

Unix의 "모든 것은 파일이다"라는 말처럼, 프로그램은 실제로 결과를 `standard output` (stdout)이라는 특별한 파일로 전송하고 상태 메세지를 `standard error` (stderr)로 보낸다. 기본적으로 이 아웃풋과 에러는 스크린에 연결되고 따로 디스크 파일에는 저장되지 않는다.

추가적으로, 많은 프로그램은 input을 받기 위해 `standard input` (stdin)을 받는다. (default로 키보드)

I/O redirection은 output이 어디로 가고 input이 어디로 오는지를 변경할 수 있게 해준다.

보통은 output은 스크린으로 가고, input은 키보드로부터 오지만 I/O rediretion으로 이를 변경할 수 있다.


### Redirecting standard output

standard output을 스크린 대신 다른곳으로 redirect 하려면 `>` redirection operator를 사용하면 된다.

왜 이를 사용할까? 이는 command의 output을 파일에 저장할 때 주로 쓰인다.

```
root@37ba9ce1e69f:~# ls -l /usr/bin > ls-output.txt

# 실제로 파일이 잘 만들어진 것을 볼 수 있따.
root@37ba9ce1e69f:~# ls -l ls-output.txt
-rw-r--r-- 1 root root 18543 Nov 25 12:28 ls-output.txt
```

이제 존재하지 않는 디렉토리의 명령어를 output으로 내보내보자.

```
root@37ba9ce1e69f:~# ls -l /bin/usr > ls-output.txt
ls: cannot access '/bin/usr': No such file or directory
```

왜 에러 메세지가 스크린으로 반환되지만 output 파일로 만들어지지 않을까? 그 이유는 `ls` 명령어가 에러 메세지를 standard output으로 보내지 않기 때문이다. 대신에, 대부분의 Unix 프로그램들처럼 에러 메세지를 standard error로 내보낸다.

그런데 파일을 확인해보면 내용이 사라지고 크기가 0이 된 것을 볼 수 있다.

```
root@37ba9ce1e69f:~# ls -l ls-output.txt
-rw-r--r-- 1 root root 0 Nov 25 12:31 ls-output.txt
```

그 이유는 `>` redirection operator를 사용하여 redirect를 하면 시작할 때 결과 파일은 항상 다시 써지기 때문이다. `ls` 명령어가 output을 반환하지 않았기 때문에 rewrite 된 파일이 빈 파일 그대로 남아 있는 것.

그래서 이를 트릭으로 사용해서 기존 파일을 truncate 시키거나 새 파일을 만들 때 쓰기도 한다.

```
root@37ba9ce1e69f:~# > ls-output.txt
```

기존 파일을 덮어쓰지 않고 이어서 쓰는 방법은 `>>` operator를 사용하면 됨.

```
root@37ba9ce1e69f:~# ls -l /usr/bin >> ls-output.txt
root@37ba9ce1e69f:~# ls -l ls-output.txt
-rw-r--r-- 1 root root 18825 Nov 25 12:35 ls-output.txt
root@37ba9ce1e69f:~# ls -l /usr/bin >> ls-output.txt
root@37ba9ce1e69f:~# ls -l /usr/bin >> ls-output.txt
root@37ba9ce1e69f:~# ls -l /usr/bin >> ls-output.txt
root@37ba9ce1e69f:~# ls -l ls-output.txt
-rw-r--r-- 1 root root 75300 Nov 25 12:35 ls-output.txt
```

### Redirecting standard error

에러를 redirect 하려면 `file descriptor`를 명시해야한다.

프로그램은 여러가지 지정된 숫자의 file stream output을 생성하는데, 첫 새개가 input, output, error이고 각각 file descriptor로는 0, 1, 2이다.

standard error는 file descriptor 2와 같으므로 `2>` 를 사용하여 에러를 리다이렉트 시킬 수 있다.

```
root@37ba9ce1e69f:~# ls -l /bin/usr 2> ls-error.txt
```

보통 우리는 명령어의 결과와 에러를 한 파일에 저장하고 싶어함.  두 가지 방법이 있다.

하나는 전통적인 방법. 오래된 버전의 shell에서 동작.


```
root@37ba9ce1e69f:~# ls -l /bin/usr > ls-output.txt 2>&1
```

이 방법으로 두 가지의 redirection을 실행. (output, error)


최근의 bash는 두가지의 redirection을 결합한 방법을 제공한다.

```
root@37ba9ce1e69f:~# ls -l /bin/usr &> ls-output.txt
# >>를 써서 append되게 할 수도 있음
root@37ba9ce1e69f:~# ls -l /bin/usr &>> ls-output.txt
```


가끔 모든 결과를 표현하지 않고 싶을 때가 있다. (특히 에러 메세지)

시스템은 이럴 때 사용하는 특별한 파일인 `/dev/null` (input을 받고 아무것도 하지 않는 bit bucket)을 제공
```
root@37ba9ce1e69f:~# ls -l /bin/usr 2> /dev/null
```

> `/dev/null`은 유닉스에서 널리 쓰이는 컨셉이기 때문에 많은 unix 문화에서도 사용된다고 한다. 할말하않?


### Redirecting standard input


### Pipelines

standard input을 받아서 standard output으로 보내주는 기능은 `pipelines`라는 쉘 기능으로 구현되어있다.

standard input을 받는 `less` 명령어를 통해 pipeline을 사용해보자

```
root@37ba9ce1e69f:~# ls -l /usr/bin | less
```

### Filter

pipeline은 복잡한 작업에도 자주 사용됨.

필터는 input을 받고, 이를 조금 변경한 후에 output으로 보내줌. `sort` 예시

```
root@37ba9ce1e69f:~# ls /bin /usr/bin | sort | less
```

> redirect operator (`>`)와 pipeline (`|`)의 차이

> `>`는 파일로 연결되는 명령어, `|`는 명령어의 output을 input으로 연결해주는 명령어

> silent하게 파일을 overwrite해버리기 때문에 주의해야됨


### 다른 명령어들

`uniq`: 리스트의 중복을 제거.

```
root@37ba9ce1e69f:~# ls /bin /usr/bin | sort | uniq | less

# -d option을 주면 중복된 내용만 보여줌
root@37ba9ce1e69f:~# ls /bin /usr/bin | sort | uniq -d | less 
```

`wc`: line, word, byte count

```
root@37ba9ce1e69f:~# wc ls-output.txt
 1  9 56 ls-output.txt
 
# -l option은 line만 보여줌
# pipeline으로 유용하게 사용 가능
root@37ba9ce1e69f:~# ls /bin /usr/bin | sort | uniq | wc -l
349
```

`grep`: 패턴에 매칭되는 줄

```
root@37ba9ce1e69f:~# ls /bin /usr/bin | sort | uniq | grep zip
bunzip2
bzip2
bzip2recover
gunzip
gzip
```

유용한 옵션으로는 `-i` (ignore case), `-v` (패턴과 일치하지 않는 항목만 반환) 등이 있음


`head`, `tail`

tail은 real time으로 파일을 확인할 수 있게 해줌 -> 로그파일이나 진행상황을 지켜볼 때 유용함

`-f` option으로 계속 파일을 모니터링 할 수 있음

```
# 파일이 없으면 /var/log/syslog
root@37ba9ce1e69f:~# tail -f /var/log/messages
```


`tee`: read from stdin and output to stdout and files

`tee` 프로그램은 standard input을 읽어서 stdout 과 다른 파일들에게 나눠질 수 있게 카피한다.

이는 프로세스의 중간 단계에서 pipeline의 내용을 캡쳐할 때 유용하다.

```
# ls의 명령어를 모두 ls.txt에 입력 후 zip이 포함된 애들만출력
root@e78e6ae9e14a:~# ls /usr/bin | tee ls.txt | grep zip
```
