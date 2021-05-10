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

## 7. Seeing the world as the shell sees it

expansion: 무언가 입력했을 때 shell에서 실제로는 다른 확장된 일을 해줌

```
root@e78e6ae9e14a:~# echo *
ls.txt
```

위처럼 `*`를 프린트하지 않고 wildcard가 모든 파일명을 반환함.

### pathname expansion

wildcard로 동작하는 매커니즘을 `pathname expansion`이라고 부른다.

```
root@e78e6ae9e14a:~# ls
Desktop  Documents  Music  Pictures  Public  Templates  Videos  ls.txt
root@e78e6ae9e14a:~# echo D*
Desktop Documents
root@e78e6ae9e14a:~# echo *s
Documents Pictures Templates Videos
root@e78e6ae9e14a:~# echo [[:upper:]]*
Desktop Documents Music Pictures Public Templates Videos
root@e78e6ae9e14a:~# echo /usr/*/share
/usr/local/share
```

숨긴 파일 (`.`으로 시작되는 파일)을 보는 법

```
# 이렇게 하면 ., .. 같은 경로가 나옴
root@e78e6ae9e14a:~# echo .*
. .. .bashrc .profile

root@e78e6ae9e14a:~# ls -d .* | less

# pathname expansion으로는 이렇게 표현 가능
root@e78e6ae9e14a:~# echo .[!.]*
.bashrc .profile

root@e78e6ae9e14a:~# ls -A
.bashrc  .profile  Desktop  Documents  Music  Pictures  Public  Templates  Videos  ls.txt
```

### Tilde expansion

`cd ~`로 수없이 사용해봤을 명령어. home 디렉토리의 경로로 expand됨


### Arithmetic expansion

`$((expression))` 문법으로 산술 가능

```
root@e78e6ae9e14a:~# echo $((2 + 2))
4
```

### Brace expansion

이상한 expansion중 하나. 괄호에 들어있는 패턴으로 여러 텍스트 스트링을 만들어줌.

```
root@e78e6ae9e14a:~# echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back
```

comma-seperated list나 range of integer를 받는다.

```
root@e78e6ae9e14a:~# echo Number_{1..5}
Number_1 Number_2 Number_3 Number_4 Number_5

# zero padding도 가능
root@e78e6ae9e14a:~# echo {1..5}
1 2 3 4 5
root@e78e6ae9e14a:~# echo {01..5}
01 02 03 04 05
root@e78e6ae9e14a:~# echo {001..5}
001 002 003 004 005

# 알파벳도 가능
root@e78e6ae9e14a:~# echo {Z..A}
Z Y X W V U T S R Q P O N M L K J I H G F E D C B A

# nested도 가능
root@e78e6ae9e14a:~# echo a{A{1,2},B{3,4}}b
aA1b aA2b aB3b aB4b
```

어디에 쓰일까? 많은 리스트의 파일이나 디렉토리를 만들 때 쓰면 좋음.

```
root@e78e6ae9e14a:~/Photos# mkdir {2007..2009}-{01..12}
root@e78e6ae9e14a:~/Photos# ls
2007-01  2007-04  2007-07  2007-10  2008-01  2008-04  2008-07  2008-10  2009-01  2009-04  2009-07  2009-10
2007-02  2007-05  2007-08  2007-11  2008-02  2008-05  2008-08  2008-11  2009-02  2009-05  2009-08  2009-11
2007-03  2007-06  2007-09  2007-12  2008-03  2008-06  2008-09  2008-12  2009-03  2009-06  2009-09  2009-12
```


### Parameter Expansion

(나중에도 다룰) variables를 사용해 작은 데이터를 저장하고 이름을 부여할 수 있다.

```
root@e78e6ae9e14a:~# echo $PWD
/root

# list 확인 (env 명령어로도 확인 가능)
root@e78e6ae9e14a:~# printenv | less
```

### Command substitution

명령어의 output을 expansion으로 사용할 수 있게 해줌.

```
root@e78e6ae9e14a:~# echo $(ls)
Desktop Documents Music Photos Pictures Public Templates Videos ls.txt

root@e78e6ae9e14a:~# ls -l $(which cp)
-rwxr-xr-x 1 root root 153976 Sep  5  2019 /usr/bin/cp
```

단순히 싱글 커맨드가 아닌 pipeline에서도 사용이 가능하다.

```
root@e78e6ae9e14a:~# file $(ls -d /usr/bin/* | grep zip)
/usr/bin/bunzip2:      ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=00c09d800d549d58e3b7c4f6170446cc69bf14a5, for GNU/Linux 3.2.0, stripped
/usr/bin/bzip2:        ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=00c09d800d549d58e3b7c4f6170446cc69bf14a5, for GNU/Linux 3.2.0, stripped
/usr/bin/bzip2recover: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=aa9666f913c3a67eab9d62585a9b37e46d0c7cb9, for GNU/Linux 3.2.0, stripped
/usr/bin/gunzip:       POSIX shell script, ASCII text executable
/usr/bin/gzip:         ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=445b2aa3186ca2fc7b96fa7c42c348a1662769c1, for GNU/Linux 3.2.0, stripped
```

bash에서는 backquote도 지원한다.

```
root@e78e6ae9e14a:~# ls -l `which cp`
-rwxr-xr-x 1 root root 153976 Sep  5  2019 /usr/bin/cp
```

### Quoting

expansion때문에 표현이 안되는 것들도 있음

```
root@e78e6ae9e14a:~# echo this is a     test
this is a test
root@e78e6ae9e14a:~# echo The total is $100.00
The total is 00.00
```

원하지 않는 expansion을 피하기 위해서 quotinig mechanism을 사용 가능

가장 흔한건 쌍따옴표 (`"`)

```
# 띄어쓰기가 들어간 파일
root@e78e6ae9e14a:~# ls -l two words.txt
ls: cannot access 'two': No such file or directory
ls: cannot access 'words.txt': No such file or directory
root@e78e6ae9e14a:~# ls -l "two words.txt"
-rw-r--r-- 1 root root 0 Nov 26 13:23 'two words.txt'


root@e78e6ae9e14a:~# echo $PWD $((2+2)) $(gcal)
/root 4 November 2020 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25<26>27 28 29 30
root@e78e6ae9e14a:~# echo "$PWD $((2+2)) $(gcal)"
/root 4
    November 2020
 Su Mo Tu We Th Fr Sa
  1  2  3  4  5  6  7
  8  9 10 11 12 13 14
 15 16 17 18 19 20 21
 22 23 24 25<26>27 28
 29 30

root@e78e6ae9e14a:~# echo this is a    test
this is a test
root@e78e6ae9e14a:~# echo "this is a    test"
this is a    test
```

모든 expansion을 쓰지 않게 하고 싶으면 일반 따옴표 (`'`)를 사용하면 됨

```
# 모든 expansion 사용됨
root@e78e6ae9e14a:~# echo text ~/*.txt {a,b} $(echo foo) $((2+2)) $PWD
text /root/ls.txt /root/two words.txt a b foo 4 /root

# $ expansion만 사용됨
root@e78e6ae9e14a:~# echo "text ~/*.txt {a,b} $(echo foo) $((2+2)) $PWD"
text ~/*.txt {a,b} foo 4 /root

# 모든 expansion 무시
root@e78e6ae9e14a:~# echo 'text ~/*.txt {a,b} $(echo foo) $((2+2)) $PWD'
text ~/*.txt {a,b} $(echo foo) $((2+2)) $PWD
```

escaping도 사용 가능 (`\`)

```
root@e78e6ae9e14a:~# echo "The balance of the $PWD is : \$5.00"
The balance of the /root is : $5.00

# 백슬래쉬를 쓰고 싶으면 `\\` 사용
root@e78e6ae9e14a:~# echo "\\"
\
```

escape 문자를 사용하면 `control code`라고 불리는 몇몇 특별한 문자도 표현 가능하다.

- `\a`: Bell (an alert that causes the computer to beep)
- `\b`: backspace
- `\n`: newline
- `\r`: carrage return
- `\t`: tab

```
root@e78e6ae9e14a:~# echo -e "test \t test"
test     test
root@e78e6ae9e14a:~# echo -e "test \b test"
test test
root@e78e6ae9e14a:~# echo -e "123\b45"
1245
root@e78e6ae9e14a:~# echo -e "test \ntest"
test
test
```

## 08. Advanced keyboard tricks

### Cursor movement

> 나는 개인적으로 vi mode로 shell을 사용한다.

```
set -o vi

bindkey -v
```

- `CTRL-A`: 커서를 줄의 앞으로
- `CTRL-E`: 커서를 줄 뒤로
- `CTRL-F`: Move cursor forward one character; same as the right arrow key. 
- `CTRL-B`: Move cursor backward one character; same as the left arrow key. 

그 외 다양한 명령어들이 있지만 굳이 정리는 안해놓을 예정


### Using history

```
root@e78e6ae9e14a:~# history | less
root@e78e6ae9e14a:~# history | grep /usr/bin
   10  ls /usr/bin | tee ls.txt | grep zip
  105  file $(ls -d /usr/bin/* | grep zip)
  213  history | grep /usr/bin
  
# history expansion 사용 가능
root@e78e6ae9e14a:~# !10
ls /usr/bin | tee ls.txt | grep zip
bunzip2
bzip2
bzip2recover
gunzip
gzip
zipdetails
```

`CTRL-R`을 사용해서 incremental search를 사용할 수 있다. (대부분의 사람들은 zsh 쉘에서 peco나 zsh-autocompletion을 사용할듯 하지만.. 기본 기능을 알아두면 편할때가 많다)

- `CTRL-R`: 증분검색 시작
- `ENTER` (`CTRL-J`): 해당 명령어 확인
- `CTRL-R`: 다음 검색으로 넘어가기

history expansion

- `!!`: 이전 명령어 실행
- `!number`: 해당 번호의 history 실행
- `!string`: 해당 string으로 시작되는 가장 최근 명령어 실행 (startswith)
- `!?string`: 해당 string을 가지고 있는 가장 최근 명령어 실행 (contains)

## 09. Permission

user account는 `/etc/passwd`에, group은 `/etc/group`에 정의되어 있고 유저나 그룹이 생성되면 `etc/shadow`가 수정된다.

```
root@e78e6ae9e14a:~# > foo.txt
root@e78e6ae9e14a:~# ls -l foo.txt
-rw-r--r-- 1 root root 0 Nov 28 16:02 foo.txt
```

앞의 10 문자는 file attribute를 나타낸다. 첫번째 문자는 파일타입.

- `-`: regular file
- `d`: directory
- `l`: symbolic link. 이 경우 나머지 값은 `rwxrwxrwx` (dummy 값이다)
- `c`: terminal이나 `/dev/null`같은 character special file
- `b`: block special file (HDD, DVD 드라이브 등)

나머지 9자리는 file mode. owner, group, world(전역)의 read, write, execute 권한을 나타냄.

파일인지 디렉토리인지에 따라 조금씩 내용이 달라짐.

- r
  - file: open, read
  - directory: 파일 list 가능 (execute가 셋업되어 있어야됨)
- w
  - file: written or truncated; 다만 파일의 제거나 이름 변경은 directory 속성을 따름
  - directory: 디렉토리 하위의 파일의 생성, 제거, 이름변경 (execute가 셋업되어 있어야됨)
- w
  - file: 실행가능
  - directory: 해당 디렉토리에 접근 가능 (ex. `cd directory`)


파일 모드 변경은 `chmod` 명령어로 가능. (owner나 superuser만 가능하다)

두가지 방법을 지원하는데

- Octal number representation
- Symbolic representation


### Octal

> octal (8진법), hexadecimal (16진법)이 많이 사용되는 이유는 컴퓨터가 binairy(2진법)으로 작동하기 때문. hex color처럼 16진법이 더 많이 사용되지만 8진법이 3 bit binary를 표현하기에 좋기 때문에 8진법이 사용됨


| ocatl | binary | file mode |
| 0     | 000    | ---       |
| 1     | 001    | --x       |
| 2     | 010    | -w-       |
| 3     | 011    | -wx       |
| 4     | 100    | r--       |
| 5     | 101    | r-x       |
| 6     | 110    | rw-       |
| 7     | 111    | rwx       |


```
root@e78e6ae9e14a:~# ls -l foo.txt
-rw-r--r-- 1 root root 0 Nov 28 16:02 foo.txt
root@e78e6ae9e14a:~# chmod 600 foo.txt
root@e78e6ae9e14a:~# ls -l foo.txt
-rw------- 1 root root 0 Nov 28 16:02 foo.txt
```

주로 사용되는 항목들은 다음과 같음

- 7 (rwx)
- 6 (rx-)
- 5 (r-x)
- 4 (r--)
- 0 (---)


### Symbolic

symbolic notation은 세가지 파트로 나뉜다.

- 변경이 어디에 적용될 것인지: u (user), g(group), o(others=world), a(all)
- Which operation will be performed: + (추가), -(제거), =(해당 항목만 추가 후 나머지 모두 제거)
- What permission will be set: r, w, x

다음은 몇몇 예시

- `u+x`: user(u) 에게 execute (x) 권한 추가 (+)
- `+x`: `a+x`와 동일. 모두에게 execute 권한 추가
- `go=rw`: group, other(world)에게 r, w 권한 추가. 나머지 권한 (execute)이 있었다면 제거
- `u+x,go=rw`


### umask

umask는 default permission을 조정해줌.

```
root@e78e6ae9e14a:~# umask
0002
root@e78e6ae9e14a:~# > foo.txt
root@e78e6ae9e14a:~# ls -l foo.txt
-rw-rw-r-- 1 root root 0 Nov 29 10:17 foo.txt
```

보통 `0002, 0022`가 default value.

```
root@e78e6ae9e14a:~# rm foo.txt
root@e78e6ae9e14a:~# umask 0000
root@e78e6ae9e14a:~# > foo.txt
root@e78e6ae9e14a:~# ls -l foo.txt
-rw-rw-rw- 1 root root 0 Nov 29 10:17 foo.txt
```

mask를 `0000`로 변경하면 모두 `rw`로 변경된 것을 볼 수 있다.

`1`로 셋팅되면 mask가 없어지는 것. 아래 표를 보면 world의 기본 모드는 `rw-`인데 0002의 마스크의 2의 2진법이 `010`이기 때문에 `w`가 꺼진다.

| original file mode | --- rw- rw- rw- |
| Mask               | 000 000 000 010 |
| result             | --- rw- rw- r-- |


`0022`일 경우에는 world와 마찬가지로 group의 `w`모드도 꺼진다.

| original file mode | --- rw- rw- rw- |
| Mask               | 000 000 010 010 |
| result             | --- rw- r-- r-- |

대부분의 경우 umask를 변경할 일은 없고, 특별히 보안을 신경써야 할 때 주로 변경한다.


위의 mask는 3자리수면 충분한데 4자리인 이유는 잘 쓰이지 않는 특수 권한이 사용되기 때문.

- `setuid bit`(4000): 실행 가능한 파일에 적용되면 실제로 파일을 실행하는 권한이 프로그램의 owner로 변경된다. 보통 superuser 권한의 프로그램에 사용되어 일반 유저도 해당 프로그램을 superuser 권한으로 실행시킬 수 있게 해줌. 보안 이슈가 생길 수 있어 쵲대한 적게 적용하는게 좋음
- `setgid bit`(2000): directory에 적용되면 파일을 만든 group이 아니라 directory의 그룹이 사용된다.
- `sticky bit`: 오래된 unix의 잔재. directory에 사용되면 directory의 owner나 superuser가 제외하고는 해당 디렉토리의 파일을 지우거나 이름 변경을 못하게 함. 주로 `/tmp`같은 shared directory에 적용되어 있음.

각각을 지정하는 방법은 다음과 같음

```
# setuid
chmod u+s program
# 이렇게 execute에 s가 표시됨
-rwsr-xr-x

# setgid
chmod g+s dir
# 이렇게 directroy execute에 s가 표시됨
drwxrwsr-x

# sticky
chmod +t dir
# world execute에 t가 표시됨
drwxrwxrwt
```

### Changing id

주로 세가지 방법으로 유저 변경.

- logout 하고 다른 유저로 login
- `su` 명령어
- `sudo` 명령어

#### `su`: Substitute User

새로운 유저로 shell을 시작함.

`-l` option은 특정 유저의 login shell이 열림. 이는 곧 새로운 유저의 env가 로드되고 새로운 유저의 home 디렉토리로 변경되는 것. (보통 이렇게 사용함)

> `-`가 `-l`의 축약어이기 때문에 대부분 `su -`로 사용한다

user가 지정되지 않으면 superuser로 로그인되는데, 그래서 `su -`가 superuser login임. (`su -l root`와 같은말)

`-c` option으로 특정 명령어만 실행시킬 수도 있다.

```
$ su -c 'less /etc/shadow'
Password:
```

#### `sudo`: Execute a command as another user

`sudo`는 `su`와 비슷하지만 더 중요한 기능들을 포함하고 있음

- 관리자가 일반 유저들이 `sudo`로 사용할 수 있는 명령어를 컨트롤 가능하다.
- `sudo` 명령어를 사용할 때 superuser의 비밀번호가 필요 없음 (사용중인 해당 유저의 password만 있으면됨)
- `sudo`는 `su`와 다르게 새로운 shell을 시작하지 않고, 다른 유저의 환경얼 불러오지도 않는다.

### chown: change file owner and group

```
chown [owner][:[group]] file...
```

## 10. Process

모던 os 는 보통 multitasking이다: 빠르게 프로그램을 스위칭하면서 한 가지보다 더 많은 일을 하고 있게 환상을 만드는 것

커널은 이를 `process`를 통해 관리: 프로세스는 리눅스가 CPU에서 다른 프로그램을 해당 순서까지 기다리게 관리해줌.

시스템이 시작되면 커널은 `init`이라는 프로그램을 실행시키는 프로세스를 시작함. (`/etc`에 있는 몇몇의 init script들을 실행)

대부분의 서비스들은 `daemon program` (유저 인터페이스 없이 백그라운드에서 실행되는 프로그램)

다른 프로그램을 실행시킬 수 있는 프로그램 (`parent process`가 `child process`를 생성함)

커널은 각각의 프로세스들이 정렬되게 정보를 유지함. ex) 각각의 프로세스는 process ID (PID)라고 불리는 번호를 순서대로 부여받는다. (`init`이 항상 PID 1)

### Viewing process

ps를 보는 가장 흔한 방법은 `ps` 명령어.

```
[ec2-user@ip-xxx.xx.xx.xx ~]$ ps
  PID TTY          TIME CMD
 3357 pts/0    00:00:00 bash
 3426 pts/0    00:00:00 ps
```

default로 ps는 현재 터미널 세션에 관련된 프로세스들만 보여준다.

`TTY`는 "teletype"의 줄임말로 process의 controlling terminal을 나타낸다. `TIME`은 해당 프로세스가 사용한 CPU time을 나타냄.

옵션을 사용하면 시스템이 뭘 하고있는지 더 자세히 볼 수 있다.

`x` option은 터미널과 관계 없이 모든 프로세스를 보여주라는 명령어이다. TTY에 `?`는 controlling terminal이 없다는 뜻. 이 명령어로 우리가 사용중인 모든 프로세스를 보여준다.

시스템이 많은 프로세스를 실행하고 있기 때문에 `ps`는 긴 리스트를 반환한다.

또한 `STAT`이라는 새로운 열이 생기는데, 이는 `state`의 줄임말이다

- `R`: Running
- `S`: Sleeping
- `D`: Uninterruptible sleep (disk drive처럼 I/O를 기다리는중)
- `T`: Stopped
- `Z`: zombie 프로세스. 이미 종료되었지만 parent로부터 정리되지 않은 child process 등
- `<`: 우선순위가 높은 프로세스. 프로세스에 중요도를 줘서 더 많은 CPU time을 할당할 수 있다. 이 속성을 `niceness`라고 부름
- `N`: 우선순위가 낮은 프로세스

또 자주 쓰이는 옵션은 `aux` 옵션이다.

```
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0 191708  3172 ?        Ss   Oct13   2:17 /usr/lib/systemd/systemd --switched-root --system --dese
root         2  0.0  0.0      0     0 ?        S    Oct13   0:01 [kthreadd]
root         4  0.0  0.0      0     0 ?        S<   Oct13   0:00 [kworker/0:0H]
root         5  0.0  0.0      0     0 ?        S    Oct13   0:01 [kworker/u96:0]
root         6  0.0  0.0      0     0 ?        S    Oct13  17:41 [ksoftirqd/0]
root         7  0.0  0.0      0     0 ?        S    Oct13   0:07 [migration/0]
root         8  0.0  0.0      0     0 ?        S    Oct13   0:00 [rcu_bh]
root         9  0.0  0.0      0     0 ?        S    Oct13  46:49 [rcu_sched]
...
```

이 옵션은 모든 유저의 process를 보여준다. (BSD 스타일)

### view process dynamically with top

`top` 명령어는 기계의 현재 상태를 더 동적으로 보여준다.

(기본으로) 매 초마다 상태를 업데이트해준다.

> `top` 명령어의 유래는 주로 시스템의 "top" 프로세스를 보기 위해 사용되는 것으로부터 유래

크게 두가지로 나뉨
- system summary
- table of processes
```
top - 18:43:04 up 23:09,  0 users,  load average: 0.08, 0.12, 0.15
Tasks:   2 total,   1 running,   1 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.2 us,  4.7 sy,  0.0 ni, 94.1 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   1990.8 total,     84.1 free,    848.0 used,   1058.7 buff/cache
MiB Swap:   1024.0 total,    998.9 free,     25.1 used.   1047.8 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
      1 root      20   0    4112   3428   2984 S   0.0   0.2   0:00.03 bash
      9 root      20   0    6120   3232   2748 R   0.0   0.2   0:00.00 top
...
```

system summary는 아주 많은 정보를 보여준다.

- top; 프로그램 이름
- 18:43:04; 현재 시간
- up 23:09; `uptime`이라고 불림. boot되고 지금까지의 시간
- users; 현재 로그인 된 유저 수
- load average; 실행되기를 기다리는 프로세스의 수. 첫번째 값은 평균 60초, 두번째는 5분, 세번째는 15분. 1.0 척도이다.
- Tasks; process stat에 따른 프로세스 수 요약
- CPU(s);
  - 1.2 us; CPU의 1.2%가 user process (커널 밖에서 실행되는)
  - 4.7 sy; CPU의 4.7%가 시스템 (커널) 프로세스
  - 0.0 ni; CPU의 0.0%가 낮은 우선순위의 프로세스 (nice)
  - 94.1 id; CPU의 94.1%가 놀고있음 (idle)
  - 0.0 wa; CPU의 0.0퍼센트가 I/O를 기다리고 있음


`top` 프로그램은 몇몇 키보드 명령어를 받을 수 있음. help text인 `h`와 종료하는 `q`가 가장 많이 쓰일듯.

```
Help for Interactive Commands - procps-ng version 3.3.10
Window 1:Def: Cumulative mode Off.  System: Delay 3.0 secs; Secure mode Off.

  Z,B,E,e   Global: 'Z' colors; 'B' bold; 'E'/'e' summary/task memory scale
  l,t,m     Toggle Summary: 'l' load avg; 't' task/cpu stats; 'm' memory info
  0,1,2,3,I Toggle: '0' zeros; '1/2/3' cpus or numa node views; 'I' Irix mode
  f,F,X     Fields: 'f'/'F' add/remove/order/sort; 'X' increase fixed-width

  L,&,<,> . Locate: 'L'/'&' find/again; Move sort column: '<'/'>' left/right
  R,H,V,J . Toggle: 'R' Sort; 'H' Threads; 'V' Forest view; 'J' Num justify
  c,i,S,j . Toggle: 'c' Cmd name/line; 'i' Idle; 'S' Time; 'j' Str justify
  x,y     . Toggle highlights: 'x' sort field; 'y' running tasks
  z,b     . Toggle: 'z' color/mono; 'b' bold/reverse (only if 'x' or 'y')
  u,U,o,O . Filter by: 'u'/'U' effective/any user; 'o'/'O' other criteria
  n,#,^O  . Set: 'n'/'#' max tasks displayed; Show: Ctrl+'O' other filter(s)
  C,...   . Toggle scroll coordinates msg for: up,down,left,right,home,end

  k,r       Manipulate tasks: 'k' kill; 'r' renice
  d or s    Set update interval
  W,Y       Write configuration file 'W'; Inspect other output 'Y'
  q         Quit
          ( commands shown with '.' require a visible task display window )
Press 'h' or '?' for help with Windows,
Type 'q' or <Esc> to continue
```

> mac 의 top은 조금 다르다. `?`를 입력하거나 `man top`으로 문서를 볼 수 있다.

### Controlling process

대부분의 터미널에서 실행되고 있는 프로그램들은 `CTRL-C`로 interrupt 가능하다. 이는 프로그램에게 종료할 것을 공손하게 요청하는 것.

반면 process를 background에서 실행할수도 있다. 

```
[sckim@localhost ~]$ vi &
[1] 29500
[sckim@localhost ~]$
```

ps 명령어로 떠 있는 것을 볼 수 있다.

```
[sckim@localhost ~]$ ps
  PID TTY          TIME CMD
29001 pts/0    00:00:00 bash
29500 pts/0    00:00:00 vim
29501 pts/0    00:00:00 ps
```

`jobs` 명령어로 현재 터미널에서 실행중인 job들을 볼 수도 있다.

```
[1]+  Stopped                 vim
[sckim@localhost ~]$ jobs
[1]+  Stopped                 vim
```

이를 다시 foreground로 불러오려면 `fg` 명령어를 사용하면 된다.

```
[sckim@localhost ~]$ fg %1
```

vim 창이 켜지는 것을 볼 수 있다.


잠시 프로그램을 멈출 수도 있다. 보통 foreground를 background로 보내는 것. `CTRL-Z`로 background로 보낼 수 있다.

### SIGNALS

`kill` 명령어는 이름처럼 프로세스를 제거하는 명령어.

하지만 실제로 프로세스를 죽이는게 아니라 프로세스에 `signals`를 보낸다. 시그널은 os가 프로그램과 커뮤니케이션 하는 방식 중 하나이다.

위에서 썼던 `CTRL-C`, `CTRL-Z` 명령어도 사실은 각각 `INT`(interrupt), `TSTP`(Termianl stop) signal을 프로세스에 보낸 것.

kill 명령어는 option으로 시그널을 선택해서 보낼 수 있다.

```
kill -signal PID...
```

주로 사용되는 kill signal 종류는 다음과 같다.

> 맨날 의미도 잘 모르고 `kill -9`만 썼던 나 자신을 반성..

- 1 (`HUP`): Hang up. 모뎀이나 전화에 물려서 remote computer에 연결되었던 시절의 유산. daemon 프로그램이 reinitialization 할 때 쓰인다 (ex. nginx)
- 2 (`INT`): Interrupt. `CTRL-C`와 같은 효과. 보통 프로그램을 종료시킨다.
- 9 (`KILL`): 특별한 시그널. 타겟 프로그램에게 신호를 보내지 않고 커널이 즉각적으로 프로그램을 종료. 이렇게 종료하면 clean up 할 기회가 없기 때문에 다른 종료 시그널이 실패했을 때 마지막 방법으로 쓰는것이 좋다.
- 18 (`TERM`): Terminate. kill 명령어에 옵션을 주지 않으면 default로 보내지는 시그널이다.
- 19 (`STOP`): Stop. 종료하지 않고 프로세스를 정지시킨다. `-9` (KILL) 시그널과 마찬가지로 타겟 프로세스에 보내지지 않기 때문에 무시되지 않는다.
- 20 (`TSTP`): Terminal stop. `CTRL-Z`와 같은 효과. STOP과 비슷한 효과지만 프로그램에게 보내지기 때문에 프로그램이 무시하기로 선택할 수 있음.


```
root@6d1b96b09793:/# vi &
[1] 611
root@6d1b96b09793:/# kill -1 611

[1]+  Stopped                 vi
```

`killall vi`처럼 해당 프로그램을 전부 kill 시킬수도 있다.

### 다양한 명령어들

- reboot
- shutdown
- pstree: tree-like 패턴으로 관계된 프로세스를 보여줌
- vmstat: 시스템 리소스를 보여줌. `vmstat -5`처럼 5초마다 업데이트도 가능

## 11. The environment

shell은 세션의 정보를 `environment`로 유지한다.

이는 크게 두가지로 나눌 수 있는데, `environment variable`과 `shell variable`

`bash`에 의해서 만들어진 데이터를 `shell variable`이라고 부르고 나머지 전부는 `environment variable`이다.

이와 더불어 몇몇 정보는 `aliases`와 `shell functions`에도 저장한다. shell function은 책의 나중에 다룰 예정 (shell scripting)

env를 보는 방법은 `printenv`, `set`이 있다.

`printenv`는 environment variable을 모두 보여주고 `set` 명령어는 shell과 env 변수를 모두 보여준다.

### 어떻게 environment가 세팅되는지

로그인시, `bash` 프로그램은 `startup files`라고 불리는 몇몇 설정 스크립트를 읽는다.

보통 shell 세션은 두가지 방법으로 시작되는데,

- login shell session: prompt에 username, password를 입력하고 로그인하는 방식. 보통 virtual console (ssh) 세션을 시작하면 이렇게 시작된다. (대부분의 서버)
- non-login shell session: 보통 gui에서 터미널을 열면 이렇게 시작된다. (대부분의 pc)

시작되는 방법에 따라 읽는 startup file이 다르다.

로그인 세션은 다음 파일들을 읽는다
- `/etc/profile`: 모든 유저에게 적용되는 global configuration script
- `~/.bash_profile`: 유저의 개인적인 startup file. global configuration script를 덮어씌울때 사용된다.
- `~/.bash_login`: ~`/.bash_profile`이 없으면 읽으려고 시도
- `~/.profile`: ~/.bash_profile, ~/.bash_login이 둘 다 없으면 읽기 시도. 몇몇 debian 기반 배포판에서 기본

non-login 세션은 다음 파일을 읽는다.
- `/etc/bash.bashrc`: global configuration script
- `~/.bashrc`: 유저의 개인적인 startup file.

`~/.bashrc`가 대부분 읽히기 때문에 유저의 관점에서는 아마 가장 중요한 startup file일 듯. non-login shell은 이를 기본으로 읽고, login shell 도 대부분의 startup file 안에서 ~/.bashrc를 읽는다.

`~/.bash_profile`을 열어보자.


```
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/.local/bin:$HOME/bin

export PATH
```


위에 `~/.bashrc` 파일이 있으면 읽는 코드가 있기 때문에 대부분의 서버에서 login shell에서도 `~/.bashrc`가 적용되는 것.

그 다음은 `PATH`. 명령줄에서 실행 파일을 실행시키면 shell이 명령어를 찾아내는 방식.

shell은 명령어를 입력 받으면 `PATH` 변수에 있는 디렉토리 리스트에서 해당 명령어를 찾는다.

> `PATH` 변수는 대부분의 경우 `/etc/profile` startup file에서 다음 코드로 설정된다.

```
PATH=$PATH:$HOME/bin
```

이는 parameter expansion을 사용한 구문으로 아래 예시를 따라해보면 이해가 쉽다.

```
root@d44964688ad2:/# foo="this is some "
root@d44964688ad2:/# echo $foo
this is some
root@d44964688ad2:/# foo=$foo"text."
root@d44964688ad2:/# echo $foo
this is some text.
```

이 테크닉을 사용하여 variable의 내용의 끝에 새로운 텍스트를 추가할 수 있다.

위에 `$PATH`에 `:$HOME/bin`이 추가되어 홈 디렉토리의 `~/bin` 안의 실행파일이 실행되는 것.


> 어떤 파일을 수정해야할까?

> 서버에서는 ~/.bash_profile을, 로컬에서는 ~/.bashrc (주로 ~/.zshrc)를 수정하기 일쑤였는데, 책에서는 `PATH`를 추가하거나 새로운 환경변수를 정의할 때에는 ~/.bash_profile을, 나머지 다른 것들은 .bashrc에 추가하라고 한다.


## 13. Customizing the prompt

가장 기본적인 prompt는 다음처럼 생겼다.

```
[ec2-user@ip-xxx.xx.xx.xx ~]$
```

prompt는 전에 살펴봤던 것처럼 `PS1`이라는 환경변수로 정의되어 있다. (Prompt String 1의 줄임말)

```
[ec2-user@ip-xxx.xx.xx.xx ~]$ echo $PS1
[\u@\h \W]\$
```
이 backslashed-escaped special character들은 bash가 prompt string에서 특별하게 사용한다.

- \a: ASCII bell. computer beep 소리를 낸다 (?)
- \d: current date (Mon May 26)
- \h: hostname
- \H: full hostname
- \j: 현재 shell 세션에서 실행중인 job 개수
- \l: 현재 터미널 기기의 이름
- \n: newline (줄바꿈)
- \r: carrage return
- \s: shell 프로그램 이름
- \t: 현재 시간 (24시간 기준) HH:mm:ss 포멧
- \T: 12시간 기준 현재시간
- \@: 21시간 기준 + AM/PM 포멧
- \A: 24시간 기준 현재시간 HH:mm 포멧
- \u: 현재 유저명
- \v: shell 버전명
- \w: 현재 working directory
- \W: 현재 working directory의 마지막 경로
- \!: 현재 명령어의 히스토리 넘버
- \#: 현재 쉘 세션에서 입력되었던 명령어 수
- `\$`: `$`캐릭터를 표시. superuser 권한일 떄에는 #
- \[: non-printing 캐릭터를 나타내는 시그널 시작
- \]: non-printing 캐릭터를 나타내는 시그널 시작


### Prompt 디자인 변경해보기

```
[ec2-user@ip-xxx.xx.xx.xx ~]$ ps1_old="$PS1"
[ec2-user@ip-xxx.xx.xx.xx ~]$ echo $ps1_old
[\u@\h \W]\$

# 나중이 이렇게 복구할 수 있다.
[ec2-user@ip-172-31-5-10 ~]$ PS1="$ps1_old"
```

일단 모두 제거해보자.

```
[ec2-user@ip-172-31-5-10 ~]$ PS1=

```

아무것도 표시되지 않는 것을 볼 수 있다.  이제 달러 사인($)만 줘보자.

```
[ec2-user@ip-172-31-5-10 ~]$ PS1="\$ "
$
```

이제 적어도 어디에 있는지 정도는 볼 수 있다.

시간과 hostname을 표시해보자.

```
$ PS1="\A \h \$ "
15:25 ip-xxx-xx-xx-xx $
```

기존 방식과 거의 흡사하게 만들어보자.

```
15:26 ip-172-31-5-10 $ PS1="<\u@\h \W>\$ "
<ec2-user@ip-172-31-5-10 ~>$ ls
```

### 색깔 변경

캐릭터 색깔은 ANSI escape code로 터미널 에뮬레이터에 보내진다.

```
# red prompt
<ec2-user@ip-172-31-5-10 ~>$ PS1="\[\033[0;31m\]<\u@\h \W>\$ "

# 기존 색상으로 돌아가라는 신호
<ec2-user@ip-172-31-5-10 ~>$ PS1="\[\033[0;31m\]<\u@\h \W>\$\[\033[0m\] "

# red background color
<ec2-user@ip-172-31-5-10 ~>$ PS1="\[\033[0;41m\]<\u@\h \W>\$\[\033[0m\] "
```

ANSI 는 `\033[X;XXm` 형태로 이루어진다.

세미콜론으로 나누어진 첫번째 숫자는 특별한 뜻을 갖고, 숫자는 30부터 시작된다.

- 0이 기본, 1은 bold, 4는 italic, 5는 밑줄 등이다.
- 각 숫자 번호는 [ANSI escape code 위키피디아](https://en.wikipedia.org/wiki/ANSI_escape_code)에서도 볼 수 있다.
- 0;30 은 기본 블랙 -> `\033[0;30m` 이 그래서 검정색이다.

- `\033[0;30m`: black
- `\033[0;31m`: red
- `\033[0;32m`: green

백그라운드 컬러는 40으로 시작한다.

- `\033[0;40m`: black
- `\033[0;41m`: red
- `\033[0;42m`: green

### 커서 이동

escape code로 커서의 위치도 조정할 수 있다. 보통 시계나 특별한 정보를 스크린의 위치에 고정할 때 쓰인다.

구체적인 sequence는 따로 정리하지 않겠다.

다음은 스크린의 맨 위에 빨간 시계를 고정시키는 prompt.

```
PS1="\[\033[s\033[0;0H\033[0;41m\033[K\033[1;33m\t\033[0m\033[u\]<\u@\h \W>\$ "
```

> 이걸 활용하면 zsh를 조금 더 깔끔하게 만들 수 있을듯.

> 한번 더 보고 실제로 많은 정보를 표시해주는 shell을 만들어보자.


###  prompt 저장

`.bashrc` 파일에 두줄을 추가하면 된다.

```
PS1="\[\033[s\033[0;0H\033[0;41m\033[K\033[1;33m\t\033[0m\033[u\]<\u@\h \W>\$ "

export PS1
```

## 14. Package management

package management는 시스템에서 소프트웨어를 설치하고 관리하는 시스템.

요즘엔 대부분의 사람들이 각 linux distributor의 패키지들을 설치한다.

> 이는 소스코드를 받아서 컴파일하는 초기의 리눅스에 반대되는 것이지만, 소스코드를 받아서 쓰는 방식도 여전히 널리 사용되는 방식이다. 이는 이후 챕터에서 다룰예정

### Packaging syswtem

일반적으로 다른 배포판은 다른 패키징 시스템을 갖는다.

대부분의 배포판은 크게 두가지로 나뉜다

- Debian: .deb (Debian, Ubuntu, Linux Mint, Raspbian)
- Red hat: .rpm (Fedora, CentOS, Red hat Enterprise Linux, OpenSUSE)

### 패키지 시스템이 작동하는법

디스크 등으로 설치되는 윈도우와 다르게 모든 리눅스 시스템의 소프트웨어는 인터넷에서 찾을 수 있다.

대부분은 `package file`의 형태로 distribution vendor에 의해서 제공된다.

소프트웨어 패키징의 기본 유닛은 `package file`이다. 이는 소프트웨어 패키지를 구성하는 파일의 콜렉션.

배포판은 소프트웨어 개발 라이프사이클에 따라 몇몇 다른 레파지토리를 유지한다. 보통은 testing, development 등...


### High and Low level package tool

package management system은 대개는 두가지 타입의 툴로 구성된다.

- Low level tools: 패키지 파일을 설치하거나 제거
- High level tools: 메타데이터를 찾거나 디펜던시 해결

| distribution            | Low-level | High-level             |
| Debian                  | dpkg      | apt-get, apt, aptitute |
| Redhat (Fedora, Centos) | Low-level | yum, dnf               |


> 그래서 서버에 따라서 어떤 서버는 yum을, 어떤 서버는 apt-get을 사용하는 것

### 흔한 패키지 관리 명령어들

패키지 검색

```
# debian
apt-get update
apt-cache search search_string

# redhat
yum search search_string
```


패키지 설치 및 제거

```
# debian
apt-get update
apt-cache install package_name

## 파일로 설치
dpkg -i package_file

## 제거
apt-get remove package_name


# redhat
yum insatll package_name

## 파일로 설치
rpm -i package_file
## 제거
yum erase package_name
```

레파지토리에서 패키지 업데이트

```
# debian
apt-get update
apt-get upgrade
# upgrade from file
dpkg -i package_file

# redhat
yum update
# upgrade from file
rpm -U package_file
```

설치된 패키지 리스팅 및 정보 확인

```
# debian
dpkg -l
## 해당 패키지가 설치되었는지 확인
dpkg -s package_name
## 설치된 패키지 정보 확인
apt-cache show package_name

# redhat
rpm -qa
## 해당 패키지가 설치되었는지 확인
rpm -q package_name
## 설치된 패키지 정보 확인
yum info package_name
```

해당 파일이 어떤 패키지로 설치되었는지 확인


```
# debian
dpkg -S file_name

# redhat
rpm -qf file_name
```

ex. vim이 어떻게 설치되었는지

```
rpm -qf $(which vim)
```

## 17. Searching for files

### locate

database를 바탕으로 주어진 substring과 맞는 파일을 탐색.

> `updatedb`라는 프로그램이 정기적으로 cron job으로 파일 데이터베이스를 업데이트한다.

### find

`locate`가 파일을 이름으로만 찾았다면 `find` 프로그램은 특정 디렉토리에서 다양한 옵션과 특성으로 파일을 찾을 수 있다.

```
find ~

# wordcount
find ~ | wc -l  
```

#### Options (Test)
find에는 다양한 옵션 (test라고 부름)이 존재한다.

대표적인 옵션은 `-type`.

```
# directory만 찾기
find ~ -type d | wc -l

# file 찾기
find ~ -type f | wc -l

# size option
find ~ -type f -name "*.JPG" -size +1M | wc -l
```

그 외 옵션들은 man page에서 확인 가능하다.

#### Operators

`-or` `and`, `not`, `()` (그룹)과 같은 operator도 사용 가능하다.

operator를 사용하지 않고 옵션을 연달아서 쓰면 `-and`가 생략된것

#### actions

`-delete`, `-ls`, `-print`와 같은 미리 정의된 액션이 있음.

```
find ~ -type f -name '*.bak' -delete
```

> `-delete` 옵션을 쓰기 전에는 `-print`로 확인하는 것이 좋음

#### User-defined action

위에서 미리 정의된 액션 이외에도 `-exec` 를 사용하여 원하는 액션을 수행시킬 수 있다

```
-exec command '{}' ';'
```

`-delete`와 같은 액션은 다음과 같다.

```
-exec rm '{}' ';'
```

> 중괄호와 세미콜론은 쉘에서 특수하게 처리됨으로 quoted나 escaped 되어야한다.

`exec` 대신 `-ok`를 사용하면 interactive하게 사용 (명령어 실행 전에 prompt로 띄워줌)

```
find ~ -type f -name 'foo*' -ok ls -l '{}' ';'
< ls ... /home/me/bin/foo > ? y
-rwxr-xr-x 1 me   me 224 2007-10-29 18:44 /home/me/bin/foo
< ls ... /home/me/foo.txt > ? y
-rw-r--r-- 1 me   me   0 2016-09-19 12:53 /home/me/foo.txt
```

#### Improving efficiency

위에서는 파일 두개가 찾아졌기 때문에 `ls -l` 명령어가 두 번 실행된다.

하지만 `ls -l file1 file2` 처럼 파일 두 개를 한 번의 명령어에서 사용할 수 있는데, 이런 경우 다음과 같이 사용 가능.

```
find ~ -type f -name 'foo*' -exec ls -l '{}' +
```

#### xargs

`xargs`는 standard input으로 intput을 받고, 이를 특정 커맨드의 argument로 넘겨준다.

이를 find와 사용하면 아주 좋음

```
find ~ -type f -name 'foo*' -print | xargs ls -l
```

#### 실제 사용 예시

```
# 테스트용 파일 생성
root@3c28bc830cb6:~# mkdir -p playground/dir-{001..100}
root@3c28bc830cb6:~# touch playground/dir-{001..100}/file-{A..Z}

# file-A 파일 개수 확인
root@3c28bc830cb6:~# find playground -type f -name 'file-A' | wc -l
100
```

권한을 일괄 확인 후 수정해보자. 파일인 경우 600, 디렉토리인 경우 700이 아니면 수정하는 명령어.

```
# permission이 600, 700이 아닌 개수 확인
root@3c28bc830cb6:~# find playground \( -type f -not -perm 600 \) -or \( -type d -not -perm 700 \) | wc -l
2701

# 해당 파일을 600, 700으로 수정
root@3c28bc830cb6:~# find playground \( -type f -not -perm 600 -exec chmod 600 '{}' ';' \) -or \( -type d -not
-perm 700 -exec chmod 700 '{}' ';' \)

# 다시 개수 확인
root@3c28bc830cb6:~# find playground \( -type f -not -perm 600 \) -or \( -type d -not -perm 700 \) | wc -l
0
```

> 괄호 사이에는 띄어쓰기가 필수

## 18. Archiving and backup

- gzip, bzip2, tar, zip, rsync

### Compressing files

압축은 데이터의 과잉(redundancy)를 줄이는 것.
- lossless: 데이터를 원본 그대로 압축하는것
- lossy: 데이터를 제거하여 압축하는것 (JPEG, MP3 등)

### GZIP

```
root@3c28bc830cb6:~# ls -l foo.txt
-rw-r--r-- 1 root root 3680 Dec 12 03:37 foo.txt
root@3c28bc830cb6:~# gzip foo.txt
root@3c28bc830cb6:~# ls -l foo.*
-rw-r--r-- 1 root root 821 Dec 12 03:37 foo.txt.gz
root@3c28bc830cb6:~# ls -l foo.*^C
root@3c28bc830cb6:~# gunzip foo.txt.gz
root@3c28bc830cb6:~# ls -l foo.txt
-rw-r--r-- 1 root root 3680 Dec 12 03:37 foo.txt
```

다양한 옵션이 있음
- `-d`: --decompress, --uncompress
- `-f`: --force
- `-l`: --list
- `-r`: --recursive
- `-t`: --test. 압축 파일의 integrity를 테스트
- `-v`: --vervose

```
# 압축 파일 테스트
$ gzip -tv foo.txt.gz
foo.txt.gz:    OK
```

압축 파일의 내용을 보려면 여러가지 명령어로 가능

```
# standard output
$ gunzip -c foo.txt | less

# gunzip -c와 동일한 zcat도 있음
$ zcat foo.txt.gz | less

# zcat | less와 동일한 zless도 있음
$ zless too.txt.gz
```

bzip도 리눅스에서는 많이 쓰인다.

```
root@3c28bc830cb6:~# ls -l /etc > foo.txt
root@3c28bc830cb6:~# ls -l foo.txt
-rw-r--r-- 1 root root 3680 Dec 12 03:47 foo.txt
root@3c28bc830cb6:~# bzip2 foo.txt
root@3c28bc830cb6:~# ls -l foo.txt.bz2
-rw-r--r-- 1 root root 869 Dec 12 03:47 foo.txt.bz2
root@3c28bc830cb6:~# bunzip2 foo.txt.bz2
```

### Archiving files

압축과 많이 쓰이는 방식은 archiving: 많은 파일을 모아서 하나의 큰 파일로 번들링하는 프로세스

#### tar

예전에 백업 테이프를 만들 때 쓰이는 `tape archive`의 줄임말인 `tar`가 많이 쓰인다.

일반 아카이빙인 `.tar`와 압축된 아카이브인 `.tgz` extension이 쓰인다.

```
tar mode[options] pathname...
```

모드는 4가지가 있다.

- `c`: Create an archive. 아카이브 생성
- `x`: Extract an archive. 아카이브 추출
- `r`: append to the end of an archive. 아카이브의 맨 끝에 추가
- `t`: list the contents of an archive. 아카이브 리스팅

`tar`는 옵션 사용법이 조금 특이하다. (모드 뒤에 연달아 붙이는 방식)

```
$ mkdir -p playground/dir-{001..100}
$ touch playground/dir-{001..100}/file-{A..Z}

# 전체 디렉토리를 아카이빙
$ tar cf playground.tar playground
```

`c` 모드 (생성 모드)에 `f`옵션 (이름 부여)을 주고 `playground.tar`를 만들었다.

```
# 해당 파일 리스팅
$ tar tf playground.tar

# verbose option (상세내용) 리스팅
$ tar tvf playground.tar
```

이제 `x` option으로 extract 해보자.

```
$ tar xf playground.tar
$ ls
playground
```

> tar의 기본 pathname은 절대 경로가 아니라 상대경로를 사용한다.

tar는 보통 아카이브 할 파일을 찾기 위해 `find`와 함께 사용된다.

```
# 기존 playground.tar에 file-A 파일을 찾아서 append
$ find playground -name 'file-A' -exec tar rf playground.tar '{}' '+'
```

뿐만 아니라 standard input, output으로도 사용 가능하다

`file-A`를 찾아서 아카이빙, 압축하여 `playground.tgz`로 만드는 명령어

```
$ find playground -name 'file-A' | tar cf - --files-from=- | gzip > playground.tgz
```

`-`는 standard input이나 output을 지칭한다. (대부분의 프로그램에서도 그럼)

위의 명령어를 더 쉽게 사용하기 위해 tar는 `z` (gzip)와 `j`(bzip2) 옵션을 제공한다.

```
$ find playground -name 'file-A' | tar czf playground.tgz -T -
```

> `-T` 옵션은 `--files-from`과 동일하다. 위 예시에서는 standard input으로 들어온 내용인 `-`를 사용

또 자주 사용되는 방식은 네트워크간에 파일을 주고받을 때.

```
# remote-sys 서버에 접속하여 Documents 디렉토리를 아카이빙한 뒤 현재 로컬 클라어인트에 다시 추출
$ ssh remote-sys 'tar cf - Documents' | tar xf -
```

## 19. Regular expression

```
^\(?[0-9][0-9][0-9]\)? [0-9][0-9][0-9]-[0-9][0-9][0-9][0-9]$
```

```
[[:upper:]][[:upper:][:lower:] ]*\.
```

```
^([[:alpha:]]+ ?)+$
```

```
^\(?[0-9]{3}\)? [0-9]{3}-[0-9]{4}$
```


```
# 미국 전화번호 : (xxx) xxx-xxxx
$ for i in {1..100}; do echo "(${RANDOM:0:3}) ${RANDOM:0:3}-${RANDOM:0:4}" >> phonelist.txt; done

# 한국버전: 010-xxxx-xxxx
$ for i in {1..100}; do echo "010-${RANDOM:0:4}-${RANDOM:0:4}" >> phonelist_kor.txt; done
```

```
$ grep -Ev "^\([0-9]{3}\) [0-9]{3}-[0-9]{4}$" phonelist.txt

# 한국버전
$ grep -Ev "^010-[0-9]{4}-[0-9]{4}$" phonelist_kor.txt
```

```
$ touch "invalid test file"
$ find . -regex '.*[^-_./0-9a-zA-Z].*'
```


```
locate --regex 'bin/(bz|gz|zip)'
```

```
cd /usr/share/man/man1
zgrep -El 'regex|regular expression' *gz
```


## 20. Text Processing

- cat
- sort
- uniq
- cut, paste, join
- comm, diff, patch
- tr sed, aspell

### sort

다양한 옵션이 있음
- `-f`: ignore case
- `-n`: numeric sort
- `-r`: reverse
- `-k`: key. 아래에서 더 자세히 설명
- `-o`: output file
- `-t`: field seporator. default는 space나 tab

```
du -s /usr/share/* | sort -nr | head
```

**key를 사용하는 법**

sort는 tabular data 형태로 작동한다.

아래 예시는 /usr/bin 디렉토리의 파일을 크기순으로 정렬 후 10개만 보여줌.

각각의 열은 필드처럼 작동하여 이 필드를 key로 사용할 수 있다.

아래 예제에서는 5번째 field인 파일 크기를 key로 사용하여 이를 numeric sort 하였음.

```
ls -l /usr/bin | sort -nrk 5 | head
-rwxr-xr-x    1 root root    113973984 Dec  9 05:05 kubelet
-rwxr-xr-x    1 root root    102131240 Sep 17 02:03 dockerd
-rwxr-xr-x    1 root root     84977456 Sep 17 02:04 docker
-rwxr-xr-x    1 root root     66751208 Jan  8  2019 ffmpeg-10bit
-rwxr-xr-x    1 root root     53221576 Sep 10 03:33 containerd
-rwxr-xr-x    1 root root     40228064 Dec  9 05:05 kubectl
-rwxr-xr-x    1 root root     39219040 Dec  9 05:05 kubeadm
-rwxr-xr-x    1 root root     26958888 Sep 10 03:33 ctr
-rwxr-xr-x    1 root root     22021168 Jul  9  2019 crictl
-rwxr-xr-x    1 root root     12893816 Sep 10 03:33 runc
```


예시 파일을 사용하여 또 다른 key의 예제를 확인해보자.

아래는 linux 배포판의 버전과 날짜를 적은 파일.

```
SUSE      10.2        12/07/2006
Fedora    10          11/25/2008
SUSE      11.0        06/19/2008
Ubuntu    8.04        04/24/2008
Fedora    8           11/08/2007
SUSE      10.3        10/04/2007
Ubuntu    6.10        10/26/2006
Fedora    7           05/31/2007
Ubuntu    7.10        10/18/2007
Ubuntu    7.04        04/19/2007
SUSE      10.1        05/11/2006
Fedora    6           10/24/2006
Fedora    9           05/13/2008
Ubuntu    6.06        06/01/2006
Ubuntu    8.10        10/30/2008
Fedora    5           03/20/2006
```

이를 `distros.txt`로 저장하고 sort로 분류해보자.

```
$ sort distros.txt
Fedora  10      11/25/2008
Fedora  5       03/20/2006
Fedora  6       10/24/2006
Fedora  7       05/31/2007
Fedora  8       11/08/2007
Fedora  9       05/13/2008
SUSE    10.1    05/11/2006
SUSE    10.2    12/07/2006
SUSE    10.3    10/04/2007
SUSE    11.0    06/19/2008
Ubuntu  6.06    06/01/2006
Ubuntu  6.10    10/26/2006
Ubuntu  7.04    04/19/2007
Ubuntu  7.10    10/18/2007
Ubuntu  8.04    04/24/2008
Ubuntu  8.10    10/30/2008
```

어느 정도 잘 작동하는것 같지만 10이 작동하지 않는다. 버전을 number로 인식하지 않았기 때문.

이를 해결하기 위해서 multiple key를 사용할 수 있다. 첫 번째 열인 배포판은 문자로, 두 번째 열인 버전은 숫자로 정렬하면 됨.

```
$ sort --key=1,1 --key=2n distros.txt
Fedora  5       03/20/2006
Fedora  6       10/24/2006
Fedora  7       05/31/2007
Fedora  8       11/08/2007
Fedora  9       05/13/2008
Fedora  10      11/25/2008
SUSE    10.1    05/11/2006
SUSE    10.2    12/07/2006
SUSE    10.3    10/04/2007
SUSE    11.0    06/19/2008
Ubuntu  6.06    06/01/2006
Ubuntu  6.10    10/26/2006
Ubuntu  7.04    04/19/2007
Ubuntu  7.10    10/18/2007
Ubuntu  8.04    04/24/2008
Ubuntu  8.10    10/30/2008
```

`1,1`은 첫번째 sort를 1열에서 시작해서 1열에서 끝내라는 의미. 두번째 key인 2n은 두 번째 열부터 numberic sort를 적용하라는 의미이다.

주로 사용하는 `YYYY-MM-DD` 형태가 아니기 때문에 날짜 정렬도 되지 않는다. 이 또한 key를 사용하여 정렬이 가능하다.

```
$ sort --key=3.7nbr --key=3.1nbr --key=3.4nbr distros.txt
Fedora  10      11/25/2008
Ubuntu  8.10    10/30/2008
SUSE    11.0    06/19/2008
Fedora  9       05/13/2008
Ubuntu  8.04    04/24/2008
Fedora  8       11/08/2007
Ubuntu  7.10    10/18/2007
SUSE    10.3    10/04/2007
Fedora  7       05/31/2007
Ubuntu  7.04    04/19/2007
SUSE    10.2    12/07/2006
Ubuntu  6.10    10/26/2006
Fedora  6       10/24/2006
Ubuntu  6.06    06/01/2006
SUSE    10.1    05/11/2006
Fedora  5       03/20/2006
```

`3.7`은 3번째 열 (날짜 열)의 연도가 시작하는 7번째 캐릭터에서부터 정렬하라는 의미.

`n`은 numeric sort, `r`은 reverse, `b`는 leading space를 무시하는 옵션이다 (숫자의 위치가 띄어쓰기로 인해서 행마다 위치가 달라질 수 있기 때문에 space를 제외하고 sort해야 정확하다)

`-t` 옵션으로 seperator를 지정할 수도 있다.

```
# /etc/passwd를 :로 구분하여 7번째 행인 shell name으로 분류
$ sort -t ':' -k 7 /etc/passwd | head
```

### sed

sed는 `streaming editor`의 줄임말이다.

> 여기저기서 sed가 자주 쓰이는것을 보았는데 항상 의미를 알기 어려웠는데 이번 기회에 잘 배워두면 사용할 일이 많을 듯 하다.


```
# input에서 front를 찾아서 back으로 변환한 후 stdout
$ echo "front" | sed 's/front/back/'
back

# address를 명시적으로 지정할수도 있다. 1줄밖에 없기 때문에 1로 지정
$ echo "front" | sed '1s/front/back/'
back

# 2로 지정하면 back으로 substitute 되지 않아 front가 print된다.
$ echo "front" | sed '2s/front/back/'
front
```


다양한 주소 옵션이 있다
- n: 숫자를 그냥 입력하면 해당 줄만 적용
- `$`: 마지막 줄
- `|regex|`: POSIX regex도 적용할 수 있다.
- `n,m`: n부터 m까지. (아래 예시에서 1,5)
- `n,+m`: n부터 m개 줄
- `n!`: n 줄을 제외한 나머지


sed는 기본적으로 모든 줄을 print 하기 때문에 filter를 하려면 `-n` (not auto print) 옵션을 주어야한다.

```
# n,m: 위에서 만든 distros.txt의 1~5 줄만 print
$ sed -n '1,5p' distros.txt

# regex: distros.txt에서 SUSE만 찾아서 print
$ sed -n '/SUSE/p' distros.txt
SUSE    10.2    12/07/2006
SUSE    11.0    06/19/2008
SUSE    10.3    10/04/2007
SUSE    10.1    05/11/2006

# !로 SUSE가 아닌것들만 찾아서 print
$ sed -n '/SUSE/!p' distros.txt
Fedora  10      11/25/2008
Ubuntu  8.04    04/24/2008
Fedora  8       11/08/2007
Ubuntu  6.10    10/26/2006
Fedora  7       05/31/2007
Ubuntu  7.10    10/18/2007
Ubuntu  7.04    04/19/2007
Fedora  6       10/24/2006
Fedora  9       05/13/2008
Ubuntu  6.06    06/01/2006
Ubuntu  8.10    10/30/2008
Fedora  5       03/20/2006
```

print(`p`) 커맨드 말고도 다양한 커맨드가 있다.

- `a`: append text after current line
- `d`: delete line
- `i`: insert text in front of the current line
- `p`: print current line. (기본으로 모든 줄을 프린트하기 때문에 특정 줄만 print 하려면 `-n` 옵션 사용)
- `s/regexp/replacement/`: regexp를 replacement로 replace.

위의 `s` 명령어를 사용해서 distros.txt의 날짜 양식을 `YYYY/MM/DD`에서 `YYYY-MM-DD`로 변경해보자.

```
$ sed 's/\([0-9]\{2\}\)\/\([0-9]\{2\}\)\/\([0-9]\{4\}\)$/\3-\1-\2/' distros.txt
SUSE    10.2    2006-12-07
Fedora  10      2008-11-25
SUSE    11.0    2008-06-19
Ubuntu  8.04    2008-04-24
Fedora  8       2007-11-08
SUSE    10.3    2007-10-04
Ubuntu  6.10    2006-10-26
Fedora  7       2007-05-31
Ubuntu  7.10    2007-10-18
Ubuntu  7.04    2007-04-19
SUSE    10.1    2006-05-11
Fedora  6       2006-10-24
Fedora  9       2008-05-13
Ubuntu  6.06    2006-06-01
Ubuntu  8.10    2008-10-30
Fedora  5       2006-03-20
```

복잡해 보이지만 escaping을 위해 사용한 역슬래쉬를 제외하면 아래와 같은 단순한 정규표현식이다.

```
([0-9]{2})/([0-9]{2})/([0-9]{4})$
```

replacement에는 BRE라는 back reference를 사용하였다. 위의 정규표현식의 그룹 (괄호로 묶인)을 각각 `\1`, `\2`, `\3`으로 사용 가능하다.

연도(`\3`), 월(`\1`), 일(`\2`)을 대쉬(-)로 연결

```
\3-\1-\2
```

이 명령어들을 sed script로 만들어서 `-f` 옵션으로 사용할 수도 있다.

```
# sed script to produce Linux distributions report

1 i\
\
Linux Distributions Report\

s/\([0-9]\{2\}\)\/\([0-9]\{2\}\)\/\([0-9]\{4\}\)$/\3-\1-\2/
y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/
```

첫번째 줄은 주석.

세번째부터 다섯번째 줄은 텍스트를 입력하는 스크립트. `1 i\`는 첫번째 줄에 insert하라는 의미이고 `\`를 사용하여 escape carriage return (줄변환)

7번째 줄은 사용할 명령어.

8번째 줄은 소문자를 대문자로 변환하는 transliteration이다. (tr 명령어와 다르게 POSIX나 `[a-z]`와 같은 형태를 지원하지 않는다.)


```
$ sed -f distros.sed distros.txt

Linux Distributions Report

SUSE    10.2    2006-12-07
Fedora  10      2008-11-25
SUSE    11.0    2008-06-19
Ubuntu  8.04    2008-04-24
Fedora  8       2007-11-08
SUSE    10.3    2007-10-04
Ubuntu  6.10    2006-10-26
Fedora  7       2007-05-31
Ubuntu  7.10    2007-10-18
Ubuntu  7.04    2007-04-19
SUSE    10.1    2006-05-11
Fedora  6       2006-10-24
Fedora  9       2008-05-13
Ubuntu  6.06    2006-06-01
Ubuntu  8.10    2008-10-30
Fedora  5       2006-03-20
```

## 23. compiling program

What is Compiling?

> Compiling is the process of translating source code into the native language of the computer’s processor.

- compiler
- linker


### Compiling a C program

시작 전에 컴일러, 링커, `make` 명령어 등의 툴이 필요하다.

```
$ which gcc
/usr/bin/gcc
```

소스 코드는 `diction`이라 불리는 GNU Project의 프로그램을 컴파일 해보자.

```
$ mkdir src
$ cd src
$ ftp ftp.gnu.org

Connected to ftp.gnu.org.
220 GNU FTP server ready.
Name (ftp.gnu.org:seulchankim): anonymous
230-NOTICE (Updated October 13 2017):
...
230 Login successful.
ftp> cd gnu/diction
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 3003     65534       68940 Aug 28  1998 diction-0.7.tar.gz
-rw-r--r--    1 3003     65534       90957 Mar 04  2002 diction-1.02.tar.gz
-rw-r--r--    1 3003     65534      141062 Sep 17  2007 diction-1.11.tar.gz
-rw-r--r--    1 3003     65534         189 Sep 17  2007 diction-1.11.tar.gz.sig
226 Directory send OK.
ftp> get diction-1.11.tar.gz
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for diction-1.11.tar.gz (141062 bytes).
WARNING! 554 bare linefeeds received in ASCII mode
File may not have transferred correctly.
226 Transfer complete.
141062 bytes received in 0.725 seconds (190 kbytes/s)
ftp> bye
```


> 만약 Mac os에 ftp가 설치되지 않았으면 다음 명령어로 설치
```
brew install inetutils
```

ftp를 사용하지 않고 httpㄴ로 다운받을 수도 있다.

```
$ wget https://ftp.gnu.org/gnu/diction/diction-1.11.tar.gz
```

이제 받은 파일의 압축을 풀어주자.

```
$ tar xzf diction-1.11.tar.gz
$ ls
diction-1.11        diction-1.11.tar.gz

$ cd diction-1.11
$ ls
COPYING         NEWS            config.h.in     configure.in    diction.1.in    diction.spec    en              getopt.c        getopt_int.h    misc.h          sentence.c      style.c
INSTALL         README          config.sub      de              diction.c       diction.spec.in en_GB           getopt.h        install-sh      nl              sentence.h      test
Makefile.in     config.guess    configure       de.po           diction.pot     diction.texi.in en_GB.po        getopt1.c       misc.c          nl.po           style.1.in
```

`.c` 와 `.h` 파일을 볼 수 있다.

`.c` 파일은 C 프로그래밍 언어로 작성된 파일.

```
$ less diction.c

#include "config.h"

#include <sys/types.h>
#include <assert.h>
#include <ctype.h>
#include <errno.h>
#include <limits.h>
#include <locale.h>
#ifdef HAVE_GETTEXT
#include <libintl.h>
#define _(String) gettext(String)
#else
#define _(String) String
#endif
#include <regex.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#include "getopt.h"
...
```

`.h` 파일은 `header files`로도 알려져 있는데, 소스코드나 라이브러리에 포함된 routines에 대한 정보를 담고 있다.

`include "getopt.h"`가 실행되기 전에 있는 header file들은 현재 소스트리 바깥의 프로그램을 참조한다.

`/usr/include` 등의 디렉토리를 살펴보면 확인할 수 있다.

```
#include <regex.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
```

### 프로그램 빌드하기

대부분의 프로그램의 빌드는 간단한 두 명령어 조합으로 이뤄진다.

```
./configure
make
```

`.configure`는 build 환경을 분석하는 역할을 한다.

대부분의 소스 코드는 portable하게 디자인 된다. 이는 한가지 이상의 unix system에서 빌드될 것을 상정하고 만들어지는 것.

`configure`는 필요한 외부 툴과 컴포넌트들이 설치되었는지 확인해주는 역할을 한다.

```
$ ./configure
checking build system type... i386-apple-darwin19.6.0
checking host system type... i386-apple-darwin19.6.0
checking for gcc... gcc
checking for C compiler default output file name... a.out
checking whether the C compiler works... yes
checking whether we are cross compiling... no
checking for suffix of executables...
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking for a BSD-compatible install... /usr/bin/install -c
checking for strerror... yes
checking for library containing regcomp... none required
checking for broken realloc... no
checking for msgfmt... yes
checking how to run the C preprocessor... gcc -E
checking for grep that handles long lines and -e... /usr/bin/grep
checking for egrep... /usr/bin/grep -E
```

여기서 중요한것은 에러메세지가 나타나지 않는 것이다.

configure 프로그램은 몇몇 새로운 파일을 만들어주는데 가장 중요한 것은 `makefile`이다.

makefile은 `make` 명령어가 어떻게 프로그램을 빌드할지 가이드해주는 configuration 파일이다.

이 파일도 일반적인 테스트 파일이기 때문에 확인해볼 수 있다.

```
$ less Makefile
```

makefile의 첫 부분은 파일의 후반부에서 쓰이는 변수들을 정의한다.

```
CC=             gcc
```


C 컴파일러를 gcc로 정의하고, 이후의 파일들에서 이를 `$(CC)` 형태로 참조해서 사용한다.

대부분의 makefile은 `target`을 정의하는 줄들로 구성된다. (우리의 경우에는 `diction` 파일과 이의 디펜던시들)

```
diction.o:      diction.c config.h getopt.h misc.h sentence.h
getopt.o:       getopt.c getopt.h getopt_int.h
getopt1.o:      getopt1.c getopt.h getopt_int.h
misc.o:         misc.c config.h misc.h
sentence.o:     sentence.c config.h misc.h sentence.h
style.o:        style.c config.h getopt.h misc.h sentence.h
```


이제 실제 `make` 명령어를 실행해보자.

```
$ make
```

명령어가 완료되면 위의 target 파일들이 모두 생긴 것을 볼 수 있다.

한 번 더 실행하면 다음 메세지가 나온다.

```
$ make
make: Nothing to be done for `all'.
```

현재의 `target`을 바탕으로 `make`는 해야할 일을 결정하기 때문.

한 파일을 제거하고 다시 make 해보자.

```
$ rm getopt.o
$ make
```

make가 사라진 모듈 때문에 다시 빌드하고 링크되는 것을 볼 수 있다.

위 기능 덕분에 make 명령어는 target을 최신 상태로 유지할 수 있다.


### Install the program

잘 만들어진 소스 코드는 `install`이라고 불리는 `make` target을 제공하기도 한다.

이 타겟은 시스템 디렉토리에 최종 결과물을 설치해준다. (보통 `/usr/local/bin`)

```
$ sudo make install
$ which diction
/usr/local/bin/diction
$ man diction
```


## 24. Writing your first script

지금까지는 command line의 tools들을 사용하였다. 이는 많은 컴퓨팅 문제를 해결해주긴 하지만, 이들만으로 해결하기 힘든 문제들도 있다.

이 다양한 툴들을 사용하여 자신만의 프로그램을 작성한다면, shell에서 더 복잡한 태스크들도 실행시킬 수 있는데 이를 `shell scripts`라고 부른다.

### shell script란?

간단하게, 쉘 스크립트는 "여러 명령어들을 담은 파일"이다. 쉘은 이 파일을 읽고 명령어를 실행시켜 준다.


### 쉘 스크립트는 어떻게 동작할까

쉘 스크립트를 만들고 실행하기 위해서, 세가지 단계를 해야한다.

1. Write a script: shell scipt는 text file이다. syntax highlighting이 지원되는 에디터에서 쓰면 더 좋다.
2. Make the script executable: 시스템은 기본적으로 텍스트 파일을 프로그램으로 취급하기 않기 때문에 execution 권한을 주어야 한다.
3. Put the script somewhere the shell can find it: 쉘은 특정 pathname을 지정하지 않았다면 executable file을 찾기 위해 특정 디렉토리를 자동으로 찾는다. 편리하게 사용하기 위해 만든 스크립트를 해당 디렉토리에 넣어준다. 

#### script file format

전통적인 "Helo World"를 출력하는 간단한 스크립트를 만들어 보자.

```
#!/bin/bash

# This is our first script.

echo 'Hello World!'
```

가장 아랫줄은 익숙한 `echo` 명령어이다. 두번째 줄도 많은 configuration 파일에서 본 주석이다.

첫줄은 조금 미스테리하다. 이는 `#`으로 시작하기 때문에 주석처럼 보이지만, 뭔가 목적이 있어 보인다.

`#!` 캐릭터는 `shebang`이라는 특수한 존재이다. shebang은 이 스크립트를 실행시킬 때 사용할 interpreter의 이름을 커널에게 알려준다. 모든 쉘스크립트는 이를 첫줄에 가지고 있어야 한다.

#### Executable permission

이제 위 스크립트를 실행시켜보자.

실행 가능하게 만들기 위해서는, `chmod` 명령어를 사용하면 된다.

```
$ ls -l helloworld
-rw-r--r--  1 me  me  0  4  4 14:58 helloworld
$ chmod 755 helloworld
$ ls -l helloworld
-rwxr-xr-x  1 me  me  0  4  4 14:58 helloworld
```

보통 두가지 권한을 사용한다. 755는 모두가 사용할 수 있게, 700은 owner만 사용할 수 있게 해 준다.

#### Script file location

permission을 세팅했기 때문에 파일을 실행시킬 수 있다.

```
$ ./helloworld

Hello World!
```

하지만 이를 실행시키려면 명시적인 경로 (`./`)가 필요하다. 이를 지정하지 않으면 에러가 난다.

```
$ helloworld
bash: command not found: helloworld
```

이는 프로그램의 문제가 아닌 경로의 문제이다. 이전에 다뤘던 `PATH` 환경변수에서 본 것 처럼, 시스템은 특정한 디렉토리들에서 executable program을 찾는다.

`/bin` 디렉토리가 시스템이 기본으로 찾는 디렉토리이기 때문에 여기에 들어있는 `ls` 명령어를 그냥 실행할 수 있는 것.

이 디렉토리들의 목록은 `PATH`라는 환경변수가 가지고 있다.

```
$ echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/username/.local/bin:/home/username/bin
```

마지막 디렉토리가 `/home/username/bin`인 것을 보자. 대부분의 리눅스 배포판은 `PATH` 변수에 유저의 home directory 안의 `bin` 디렉토리를 가지고 있다. 이 디렉토리를 만들고 위에서 만든 파일을 옮겨보자.

```
$ mkdir ~/bin
$ mv helloworld ~/bin
$ helloworld
Hello World!
```

만약 만든 `~/bin` 디렉토리가 PATH 변수에 등록되어 있지 않으면 이를 추가해주자. `.bashrc` 파일에 아래 명령어를 넣고 새 터미널 세션을 열어서 진행하면 된다. (아니면 shell이 .basrh  파일을 읽게 해도 된다.)

```
export PATH=~/bin:"$PATH"
```

#### Good location for script

`~/bin`은 개인적으로 사용할 스크립트를 두기 좋은 디렉토리이다. 하지만 모든 시스템에서 사용하는 프로그램은 전통적으로 `/usr/local/bin`에 둔다.

시스템 관리자가 사용할 스크립트들은 보통 `/usr/local/sbin`에 둔다.

대부분의 경우, 로컬에서 지원하는 프로그램들은 `/bin/`이나 `usr/bin`에 두지 않고 `/usr/local` 하위에 둔다. `/bin/`, `/usr/bin/`은 리눅스 디스트리뷰터들이 제공하고 관리하는 파일을 위한 디렉토리이다.


### Formatting tricks

좋은 스크립트를 작성하는 목표 중 하나는 쉽게 관리하고 유지보수하는 것이다. 이를 위해 몇몇 팁을 소개.

#### Long option names

많은 명령어들은 짧고 긴 옵션 이름을 둘 다 제공한다. `ls` 명령어를 예를 들어보면

```
# short option
$ ls -al

# long option
$ ls --all --directory
```

타이핑을 줄이는 것도 중요하지만, 스크립트를 쓸 때에는 가독성을 위해 long option을 사용하는게 좋다.

#### Indentation and continuation

긴 명령어의 경우 여러 줄로 나눠주는 것이 좋다. 긴 find 명령어 예시를 보자

```
$ find playground \( -type f -not -perm 0600 -exec chmod 0600 '{}' ';' \) -or \( -type d -not -perm 0700 -exec chmod 0700 '{}' ';' \)
```

이는 한눈에 알아보기 어렵다. 아래 처럼 indentation을 함께 쓰면 더 읽기가 쉬워진다.

```
find playground \
     \( \
          -type f \
          -not -perm 0600 \
          -exec chmod 0600 '{}' ';' \
     \) \
     -or \
     \( \
          -type d \
          -not -perm 0700 \
          -exec chmod 0700 '{}' ';' \
     \)
```

백슬래쉬 (`\`)를 사용한 줄바꿈과 indentation을 써서 더 읽기 좋아진 것을 볼 수 있다.

## 25. Starting a project

해당 챕터에서는 실제 프로그램을 만들어봄. 

report generator: system과 status에 대한 다양한 통계를 HTML 포맷으로 생성해주는 프로그램


### First stage: minimul document

```
<html>
      <head>
            <title>Page Title</title>
      </head>
      <body>
            Page body.
      </body>
</html>
```

간단한 html을 만들어보자. (`foo.html`) 이 파일의 경로를 입력하면 firefox나 chrome 등의 브라우저에서 열 수 있다. (`file://hoem/username/<path_to>/foo.html`)

프로그램의 첫번째 스테이지는 standard output으로 이 HTML 파일을 보내게 하는 것이다. `~/bin/sys_info_page`라는 파일을 만들고 수정해보자.

```
#!/bin/bash

# Program to output a system information page

echo "<html>"
echo "     <head>"
echo "          <title>Page Title</title>"
echo "     </head>"
echo "     <body>"
echo "          Page body."
echo "     </body>"
echo "</html>"
```


이를 실행할 수 있게 만들고 실행해보자.

```
$ chmod 755 sys_info_page
$ sys_info_page
<html>
     <head>
          <title>Page Title</title>
     </head>
     <body>
          Page body.
     </body>
</html>
```

이를 html 파일로 만들어서 실행시켜보자.

```
$ sys_info_page > sys_info_page.html
$ firefox sys_info_page.html
```

> mac OS X의 경우에는 `open` 커맨드를 사용해서 열 수 있다. `open -a "Google Chrome" sys_info_page.html`


프로그램을 만들 때에는 간단한고 명료하게 만드는 것이 좋다.

지금 프로그램은 잘 작동하지만 더 심플하게 만들 수 있다.

여러 `echo` 명령어를 하나로 만들어서 더 간단하게 해보자.

```
#!/bin/bash

# Program to output a system information page

echo "<html>
     <head>
          <title>Page Title</title>
     </head>
     <body>
          Page body.
     </body>
</html>"
```

quoted string은 newline을 포함할 수 있기 때문에 여러 줄을 넣을 수 있다. 이는 cli에서도 마찬가지로 작동한다.

```
$ echo "<html>
dquote> <head>
dquote>     <title>test title</title>
dquote> </head>
dquote> </html>"

<html>
<head>
    <title>test title</title>
</head>
</html>
```

### Second stage: Adding a little data

현재 우리의 프로그램은 간단한 document를 생성해준다. 이 리포트에 데이터를 넣어보자.

이를 위해서 일단 각 데이터의 제목을 추가해준다.


```
#!/bin/bash

# Program to output a system information page

echo "<html>
        <head>
            <title>System Information Report</title>
        </head>
        <body>
            <h1>System Information Report</h1>
        </body>
</html>"
```

#### Variables and constants

위에서 `System Information Report` 텍스트가 반복되고 있다. 이를 변수로 젖아하면 이후에 스크립트를 관리하고 유지보수하는데 훨씬 수월해 질 것이다.

```
#!/bin/bash

# Program to output a system information page

title="System Information Report"

echo "<html>
        <head>
            <title>$title</title>
        </head>
        <body>
            <h1>$title</h1>
        </body>
</html>"
```

그럼 shell에서는 어떻게 변수가 만들어질까?

shell에서는 다른 프로그래밍 언어들과 다르게 변수를 만나면 즉시 이를 생성한다. 다른 프로그래밍 언어에서는 변수가 사용되기 전에 명시적으로 정의되어야 하는것과는 다르다.

이 때문에 몇몇 문제가 발생할 수 있다. 아래 예시를 살펴보자.

```
$ foo="yes"
$ echo $foo
yes
$ echo $fool

```

`foo`라는 변수에 "yes"라는 값을 할당했기 때문에 이후 `echo $foo`는 잘 작동한다. 하지만 이후에 `echo $fool`은 에러가 나지 않고 아무 값도 반환하지 않는다.

그렇기 때문에 shell에서 변수를 사용할 떄에는 스펠링이나 오타에 주의하여야 한다.


variable 이름은 몇몇 규칙이 있다.
- alphanumeric + underscore
- 첫글자는 문자열이나 underscore

variable은 이름에서도 알 수 있듯이 변하는 값을 의미한다. 하지만 위에서 우리가 정의한 `title`은 constant로 사용된다.

constant는 variable과 비슷하게 이름과 value를 가진다. 하지만 constant는 변하지 않는다.

shell에서는 variable과 constant의 구분이 없이 사용되기 때문에 constant는 UPPER case (대문자)로, variable은 lower case (소문자)로 사용하는 것이 컨벤션이다. 이를 적용해서 수정해보자.

```
#!/bin/bash

# Program to output a system information page

TITLE="System Information Report For $HOSTNAME"

echo "<html>
        <head>
            <title>$TITLE</title>
        </head>
        <body>
            <h1>$TITLE</h1>
        </body>
</html>"
```

> shell에서는 `-r` flag와 함께 `declare` 구문을 사용하여 immutable constant를 생성하는 문법을 제공하기는 한다. 하지만 이는 거의 사용되지 않는다. `declare -r TITLE="Page Title"`

#### Assigning values to variables and constants

지금까지는 아래와 같은 방식으로 변수를 정의했다.

```
variable=value
```

다른 프로그래밍 언어들과는 다르게 shell은 할당된 데이터타입은 신경쓰지 않는다.

> `-i` 옵션과 함께 사용되어 integer type으로 강제할 수 있지만 `-r` 옵션처럼 거의 쓰이지 않는다.

변수에 whitespace나 equal sign 등을 허용하지 않기 때문이 이를 넣기 위해서는 string expand를 사용해서 변수를 선언해주어야 한다.

```
a=z 
b="a string"          # Embedded spaces must be within quotes
c="a string and $b"
d="$(ls -l foo.xtx)"  # command의 결과
e=$((5 * 7))          # 계산식
f="\t\ta tring\n"     # tabs, newline은 반드시 quote 안에서 escaped 되어야한다.
```

한 줄에서 여러 변수를 선언하는것도 가능.

```
$ a=5 b=string c="c string"
$ echo $a $b $c
5 string c string
```

expansion동안에는 변수명은 `{}`로 감싸주는게 좋다. 이는 모호함을 줄여주는 역할을 한다.

다음 예시는 filename 변수를 사용하여 파일명을 변경하는 예시이다. curly brace가 없으면 원하는대로 작동하지 않는것을 볼 수 있다.

```
$ filename="myfile"
$ touch "$filename"
$ ls
myfile

$ mv "$filename" "$filename1"
mv: rename myfile to : No such file or directory

$ mv "$filename" "${filename}1"
$ ls
myfile1
```

`{}`를 추가하여 파일명 뒤에 1을 붙일 수 있는 것을 볼 수 있다.

이를 활용하여 report에 현재 시간을 표시해보자.

```
#!/bin/bash

# Program to output a system information page


TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME="$(date +"%x %r %Z")"
TIMESTAMP="Generated $CURRENT_TIME, by $USER"

echo "<html>
        <head>
            <title>$TITLE</title>
        </head>
        <body>
            <h1>$TITLE</h1>
            <p>$TIMESTAMP</p>
        </body>
</html>"
```

> date 뒤에 나온 flag는 각각 `%x`: `YYYY/MM/DD` format, `%r`: `HH:MM:SS PM` format, `%Z`: display timezone을 의미한다.

> 그래서 `YYYY/MM/DD HH:MM:SS PM KST` 형태가 나온다.

> 더 자세한 정보는 `man date`를 참고하자.


### Here documents

지금까지는 echo 명령어만을 사용하여 text를 outputting하였다.

`here document`, 또는 `here script`를 통한 다른 방법을 알아보자.

here document: 스크립트의 text를 명령어의 standard input으로 주는 추가적인 I/O redirection form이다.

아래와 같이 작동한다.
- command: std input을 줄 명령어
- text: command에 std input으로 들어가는 텍스트
- token: 대개 embedded text의 시작과 끝을 나타내줌

```
command << token
text
token
```

html을 echo로 stdout으로 보내는 기존 스크립트를 here document로 수정해보자.

```
#!/bin/bash

# Program to output a system information page


TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME="$(date +"%x %r %Z")"
TIMESTAMP="Generated $CURRENT_TIME, by $USER"

cat << _EOF_
<html>
    <head>
        <title>$TITLE</title>
    </head>
    <body>
        <h1>$TITLE</h1>
        <p>$TIMESTAMP</p>
    </body>
</html>
_EOF_
```

`echo` 대신에 `cat` 명령어를 사용하였다.

컨벤션으로 사용되는 `_EOF_` (End of file) 는 embedded text의 끝을 나타내준다.

그렇다면 echo 대신 here document를 사용하는 이점은 무엇일까?

echo와는 다르게, here document에서는 single/double quotation이 특수 처리되지 않는다.


```
$ foo="some text"
$ echo $foo
some text
$ echo "$foo"
some text
$ echo "$foo
dquote> "
some text
```

echo의 예시를 보면 따옴표가 변수를 감싸는걸로 해석되어 없어진 것을 볼 수 있다.

```
$ cat << _EOF_
heredoc> $foo
heredoc> "$foo"
heredoc> '$foo'
heredoc> \$foo
heredoc> _EOF_
some text
"some text"
'some text'
$foo
```

반면 here document에서는 shell이 따옴표에 특별한 처리를 하지 않고 작은따옴표, 큰따옴표 모두 일반적인 문자열로 해석된다.

이는 here document를 통해서 embed quote를 자유롭게 쓸 수 있다는 말이 된다.


뿐만 아니라 here document는 어떤 명령어의 standard input으로 사용할 수 있다.

다음은 `ftp` 프로그램에 접속하여 파일을 가져오는 예시 스크립트이다.

```
#! /bin/bash

# Script to retrieve a file via FTP

FTP_SERVER=ftp.nl.debian.org
FTP_PATH=/debian/dists/stretch/main/installer-amd64/current/images/cdrom
REMOTE_FILE=debian-cd_info.tar.gz

ftp -n << _EOF_
open $FTP_SERVER
user anonymous me@linuxbox
cd $FTP_PATH
hash
get $REMOTE_FILE
bye
_EOF_
ls -l "$REMOTE_FILE"
```

해당 명령어를 실행시키면 ftp 커맨드가 순차적으로 실행되면서 파일을 다운로드하는것을 볼 수 있다.


redirection operator를 `<<`를 `<<-`로 변경하면 shell은 leading tab character를 무시하기 때문에 더 가독성이 좋게 작성이 가능하다. (whitespace는 무시하지 않음)

```
#! /bin/bash

# Script to retrieve a file via FTP

FTP_SERVER=ftp.nl.debian.org
FTP_PATH=/debian/dists/stretch/main/installer-amd64/current/images/cdrom
REMOTE_FILE=debian-cd_info.tar.gz

ftp -n <<- _EOF_
    open $FTP_SERVER
    user anonymous me@linuxbox
    cd $FTP_PATH
    hash
    get $REMOTE_FILE
    bye
_EOF_
ls -l "$REMOTE_FILE"
```


이제 간단한 작업을 하는 스크립트를 자주 만들어 보면서 shell script 작성에 익숙해지는 연습을 해야겠다.


## 26. Top-down design

프로그램이 점점 커지고 복잡해지면, 디자인하고 유지보수하기가 점점더 어려워짐.

top-down design: 상위의 단계부터 정의하고 디테일한 단계로 내려오는 방식을 얘기함
- 크고 복잡한 작업을 작고 간단한 작업으로 쪼갤 수 있음
- 쉘스크립트를 포함한 많은 프로그램을 디자인할 때 쓰이는 방법

### Shell functions

이전 블로그 포스트에서 만들었던 report program은 다음 단계로 쪼갤 수 있따.

1. Open page.
2. Open page header.
3. Set page title.
4. Close page header.
5. Open page body.
6. Output page heading.
7. Output timestamp.
8. Close page body.
9. Close page

다음 부분에서는 7-8 단계에 내용을 추가할텐데, 다음과 같은 내용을 추가해보자.
- system uptime and load
- disk space
- home space

```
#!/bin/bash

# Program to output a system information page


TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME="$(date +"%x %r %Z")"
TIMESTAMP="Generated $CURRENT_TIME, by $USER"

cat << _EOF_
<html>
    <head>
        <title>$TITLE</title>
    </head>
    <body>
        <h1>$TITLE</h1>
        <p>$TIMESTAMP</p>
        $(report_uptime)
        $(report_disk_space)
        $(report_home_space)
    </body>
</html>
_EOF_
```

위의 코드에서 볼 수 있듯이 명령어를 넣어주고 두가지 방법으로 함수를 작성할 수 있다.
- 분리된 script를 만들고 `PATH` 디렉토리에 추가하덙,
- `shell function`이라는 방식으로 프로그램 안에 스크립트를 embed 할 수 있다.

```
# formal form
function name {
      commands
      return
}

# simpler form
name () {
      commands
      return
}
```

간단한 shell function을 작성해보자.

```
#!/bin/bash

# shell function demo

function step2 {
    echo "Step 2"
    return
}

# main program starts here

echo "Step 1"
step2
echo "Step 3"
```

```
$ ./shell_function_example
Step 1
Step 2
Step 3
```

### Local variables

지금까지는 global variable만 사용되었는데,
다른 많은 프로그래밍 언어들처럼 shell script도 함수에서 local variable이 사용된다.

```
#!/bin/bash

# local-vars: script to demonstrate local variables

foo=0  # global variable

func_1 () {
    local foo
    foo=1
    echo "func_1: foo = $foo"
}

func_2() {
    local foo
    foo=2
    echo "func_2: foo = $foo"
}

echo "global:  foo = $foo"
func_1
echo "global:  foo = $foo"
func_2
echo "global:  foo = $foo"
```

실제로 실행하면 예상했던대로 동작하는걸 볼 수 있다

```
$ local_variable_example
global:  foo = 0
func_1: foo = 1
global:  foo = 0
func_2: foo = 2
global:  foo = 0
```

### keep scripts running

이제 실제로 세 함수를 작성해보자.

```
#!/bin/bash

# Program to output a system information page
report_uptime() {
    cat <<- _EOF_
           <h2>System Uptime</h2>
           <pre>$(uptime)</pre>
_EOF_
    return
}

report_disk_space () {
      cat <<- _EOF_
            <h2>Disk Space Utilization</h2>
            <pre>$(df -h)</pre>
_EOF_
      return
}

report_home_space () {
      cat <<- _EOF_
            <h2>Home Space Utilization</h2>
            <pre>$(du -sh /home/*)</pre>
_EOF_
      return
}


TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME="$(date +"%x %r %Z")"
TIMESTAMP="Generated $CURRENT_TIME, by $USER"

cat << _EOF_
<html>
    <head>
        <title>$TITLE</title>
    </head>
    <body>
        <h1>$TITLE</h1>
        <p>$TIMESTAMP</p>
        $(report_uptime)
        $(report_disk_space)
        $(report_home_space)
    </body>
</html>
_EOF_
```


shell function은 `~/.bashrc` 파일 등에서도 사용될 수 있다.

이는 alias 대신에 조금 복잡하거나 더 verbose한 경우 유용하게 사용할 수 있다.

```
ds () {
  echo "Disk Space Utilization forr $HOSTNAME"
  df -h
}
```


## 27. Flow control: branching with if

위에서 만든 스크립트에서 user의 권한에 따라 스크립트의 내용을 변경해야 한다고 생각해보자. programming 용어로 `branch`가 필요하다.

### If statement

pseudo code로 아래와 같은 내용을 생각

```
X = 5
If X = 5 then:
Say "X equals 5."
Otherwise:
Say "X is not equal to 5"
```


shell에서는 이를 if/else문을 이용해 나타낼 수 있음.

```
x=5

if [ "$x" -eq 5 ]; then
    echo "x equals 5."
else
    echo "x does not equal 5."
fi
```

이는 command line에서도 동일하게 사용할 수 있다.

```
$ x=5
$ if [ "$x" -eq 5 ]; then echo "5"; else echo "not 5";
else> fi
5

$ x=4
$ if [ "$x" -eq 5 ]; then echo "5"; else echo "not 5";
fi
not 5
```

이를 정리하면 다음과 같은 syntax (대괄호 안에는 optional)

```
if commands; then
    commands
[elif commands; then
    commands...]
[else
    commands]
fi
```


### Exit status

shell script를 포함한 명령어들은 종료될 때 `exit status`라는 value를 내뱉음.

이 값은 0~255의 integer이고, 실행한 명령어가 성공했는지 실패했는지를 나타냄.

```
$ ls -d /usr/bin
/usr/bin

$ echo $?
0

$ ls -d /bin/usr
ls: /bin/usr: No such file or directory

$ echo $?
2
```

명령어마다 error status의 값이 조금씩 다른데 대부분 1. man page에 "Exit Status"에 관한 부분이 대개 포함되어 있다. 대부분 0이 성공을 나타냄.

다음은 `man ls`의 exit status에 대한 설명 부분
```
   Exit status:
       0      if OK,

       1      if minor problems (e.g., cannot access subdirectory),

       2      if serious trouble (e.g., cannot access command-line argument).
```

shell은 exit status만을 반환하는 아주 심플한 두개의 빌트인 명령어를 제공. `true`, `false`

```
[sckim@master ~]$ true
[sckim@master ~]$ echo $?
0
[sckim@master ~]$ false
[sckim@master ~]$ echo $?
1
```

이를 if statuement에서 활용할 수 있다.

### Using Test

if와 가장 많이 사용되는 명령어 중 하나는 `test`. `test` 명령어는 다양한 비교나 확인에 쓰임.

아래와 같은 형태로 사용되거나,

```
test expression
```

더 축약된 형태로도 사용된다.

```
[ expression ]
```

expression이 true인지, false인지에 따라 exit status를 각각 0이나 1을 반환한다.

`[` 캐릭터가 실제로 `/usr/bin`에 있는 명령어인게 흥미로움.

### File Expressions

파일의 상태와 관련된 다양한 file expression들이 있음. (어떤게 있는지만 보고 필요할 때 찾아보는게 좋을듯. [gun manual](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)에서 확인할 수 있다)

```
-a file
True if file exists.

-b file
True if file exists and is a block special file.

-c file
True if file exists and is a character special file.

-d file
True if file exists and is a directory.

-e file
True if file exists.

-f file
True if file exists and is a regular file.

-g file
True if file exists and its set-group-id bit is set.

-h file
True if file exists and is a symbolic link.

-k file
True if file exists and its "sticky" bit is set.

-p file
True if file exists and is a named pipe (FIFO).

-r file
True if file exists and is readable.

-s file
True if file exists and has a size greater than zero.

-t fd
True if file descriptor fd is open and refers to a terminal.

-u file
True if file exists and its set-user-id bit is set.

-w file
True if file exists and is writable.

-x file
True if file exists and is executable.

-G file
True if file exists and is owned by the effective group id.

-L file
True if file exists and is a symbolic link.

-N file
True if file exists and has been modified since it was last read.

-O file
True if file exists and is owned by the effective user id.

-S file
True if file exists and is a socket.

file1 -ef file2
True if file1 and file2 refer to the same device and inode numbers.

file1 -nt file2
True if file1 is newer (according to modification date) than file2, or if file1 exists and file2 does not.

file1 -ot file2
True if file1 is older than file2, or if file2 exists and file1 does not.

-o optname
True if the shell option optname is enabled. The list of options appears in the description of the -o option to the set builtin (see The Set Builtin).

-v varname
True if the shell variable varname is set (has been assigned a value).

-R varname
True if the shell variable varname is set and is a name reference.

-z string
True if the length of string is zero.
```

아래는 파일의 상태를 print해주는 간단한 스크립트 예시.

```
# test-file: evaluate the statue of a file

FILE=~/.bashrc

if [ -e "$FILE" ]; then
    if [ -f "$FILE" ]; then
        echo "$FILE is a regular file."
    fi
    if [ -d "$FILE" ]; then
        echo "$FILE is a directory"
    fi
    if [ -r "$FILE" ]; then
        echo "$FILE is readable"
    fi
    if [ -w "$FILE" ]; then
        echo "$FILE is writable"
    fi
    if [ -x "$FILE" ]; then
        echo "$FILE is executable"
    fi
else
    echo "$FILE does not exist"
    exit 1   # exit with fail status code
fi

exit
```

위 스크립트에는 두가지 살펴볼만한게 있음
- `$FILE` 변수가 quote 안에서 사용된 점
  - 굳이 필요 없지만, quote 안에서 사용되지 않으면 empty value일 경우 에러가 남.
  - quote 안에 넣음으로서 빈 값도 string 처리 가능
- 마지막의 `exit` 명령어
  - `exit` 명령어는 script의 exit status를 변수로 받음 (optional)
  - argument가 없으면 마지막 실행된 명령어의 exit status를 기본으로 사용
  - 이런식으로 `exit` 명령어를 사용함으로써 스크립트가 없는 파일일 경우 실패한 exit status를 가질 수 있게 해줌
  - 대부분의 스크립트에서 exit은 이렇게 사용됨

이와 비슷하게 shell function도 `return` command에 integer argument를 포함시켜 exit stauts를 반환할 수 있다.

아래는 위의 스크립트를 함수로 생성한것.

```
test_file () {
    FILE=~/.bashrc

    if [ -e "$FILE" ]; then
        if [ -f "$FILE" ]; then
            echo "$FILE is a regular file."
        fi
        if [ -d "$FILE" ]; then
            echo "$FILE is a directory"
        fi
        if [ -r "$FILE" ]; then
            echo "$FILE is readable"
        fi
        if [ -w "$FILE" ]; then
            echo "$FILE is writable"
        fi
        if [ -x "$FILE" ]; then
            echo "$FILE is executable"
        fi
    else
        echo "$FILE does not exist"
        return 1   # return with  fail status code
    fi
}

test_file

exit
```

### String Expression

아래는 string을 평가하는 expression의 리스트
```
string
string is not null

-n string
True if the length of string is non-zero.

-z string
True if the length of string is zero

string1 == string2
string1 = string2
True if the strings are equal. When used with the [[ command, this performs pattern matching as described above (see Conditional Constructs).

‘=’ should be used with the test command for POSIX conformance.

string1 != string2
True if the strings are not equal.

string1 < string2
True if string1 sorts before string2 lexicographically.

string1 > string2
True if string1 sorts after string2 lexicographically.

arg1 OP arg2
OP is one of ‘-eq’, ‘-ne’, ‘-lt’, ‘-le’, ‘-gt’, or ‘-ge’. These arithmetic binary operators return true if arg1 is equal to, not equal to, less than, less than or equal to, greater than, or greater than or equal to arg2, respectively. Arg1 and arg2 may be positive or negative integers. When used with the [[ command, Arg1 and Arg2 are evaluated as arithmetic expressions (see Shell Arithmetic).
```

> `>`와 `<` expression은 `test`와 함께 사용될 때 반드시 쿼팅되거나 backslash로 escaped되어야됨. 그렇지 않으면 redirection operator로 interpreted됨.

아래는 string expression의 예시.

```
#!/bin/bash

# test-string: evaluate the value of a string

ANSWER=maybe

# -z string
# True if the length of string is zero
if [ -z "$ANSWER" ]; then
    echo "There is no answer." >&2
    exit 1
fi

if [ "$ANSWER" = "yes" ]; then
    echo "The answer is YES."
elif [ "$ANSWER" = "no" ]; then
    echo "The answer is NO."
elif [ "$ANSWER" = "maybe" ]; then
    echo "The answer is MAYBE."
else
    echo "The answer is UNKNOWN."
fi
```

### Integer Expression

```
integer1 -eq integer2
integer1 is equal to integer2.

integer1 -ne integer2
integer1 is not equal to integer2.

integer1 -le integer2
integer1 is less than or equal to integer2.

integer1 -lt integer2
integer1 is less than integer2.

integer1 -ge integer2
integer1 is greater than or equal to integer2.

integer1 -gt integer2
integer1 is greater than integer2.
```

아래는 간단한 integer expression 예시

```
#!/bin/bash

# test-integer: evaluate the value of an integer

INT=-5

if [ -z "$INT" ]; then
    echo "INT is empty." >&2
    exit 1
fi

if [ "$INT" -eq 0 ]; then
    echo "INT is zero"
else
    if [ "$INT" -lt 0 ]; then
        echo "INT is negative"
    else
        echo "INT is positive"
    fi
    if [ $((INT % 2)) -eq 0 ]; then
        echo "INT is even"
    else
        echo "INT is odd"
    fi
fi
```

### A more modern version of test

modern version의 bash는 `test`에 대해 강화된 대체를 실현시켜줌(?)

아래와 같이 사용된다.

```
[[ expression ]]
```

`[[ ]]` 명령어는 `test`와 비슷하지만 새로운 string expression을 제공한다.

```
string1 =~ regex
```
이는 string1이 정규표현식에 매칭되면 true를 반환. 이는 data validation 등 다양한 태스크에서 많은 기능을 제공해줌.

앞의 integer expression에서는 `INT`값에 string이 들어오면 실패했었는데, `[[ ]]`에서 `=~` operator를 쓰면 이 문제를 해결할 수 있다.

```
#!/bin/bash

# test-ingeter-enhenced: evaluate the value of an integer

INT=-5

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    if [ "$INT" -eq 0 ]; then
        echo "INT is zero"
    else
        if [ "$INT" -lt 0 ]; then
            echo "INT is negative"
        else
            echo "INT is positive"
        fi
        if [ $((INT % 2)) -eq 0 ]; then
            echo "INT is even"
        else
            echo "INT is odd"
        fi
    fi
else
    echo "INT is not an integer." >&2
    exit 1
fi
```

정규표현식을 사용해서 `INT`의 값을 `-` (minus sign)을 옵셔널로 받고 한개 이상의 numeral을 포함시킨 스트링으로 제한시킬 수 있다. 이는 empty value의 가능성도 제거해준다.

`[[  ]]`의 또 다른 기능은 `==` operator. 이는 pathname expansion과 동일한 역할을 한다.

```
[me@linuxbox ~]$ FILE=foo.bar
[me@linuxbox ~]$ if [[ $FILE == foo.* ]]; then
> echo "$FILE matches pattern 'foo.*'"
> fi
foo.bar matches pattern 'foo.*'
```

### (( )) - designed for integers

bash는 `[[ ]]`와 더불어 `(( ))` compound command도 제공해준다. 이는 이후 챕터에서 다룰 arithmetic evaluation을 제공한다.

arithmetic truth test를 실행시킬 때 사용하는데, 이는 arithmetic evaluation이 non-zero일때 true를 반환함.

```
$ if ((1)); then echo "true"; fi
true
$ if ((0)); then echo "true"; fi
```

이를 사용해서 이전에 만든 integer script를 더 간단하게 만들 수 있음.

```
#!/bin/bash

# test-ingeter-enhenced: evaluate the value of an integer

INT=-5

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    if ((INT == 0)); then
        echo "INT is zero"
    else
        if (( INT < 0 )); then
            echo "INT is negative"
        else
            echo "INT is positive"
        fi
        if (( (( INT%2 )) == 0 )); then
            echo "INT is even"
        else
            echo "INT is odd"
        fi
    fi
else
    echo "INT is not an integer." >&2
    exit 1
fi
```

### Combining expressions

더 복잡한 evaluation을 위해 expression들을 합치는것도 가능. AND, OR, NOT의 logical operator를 통해서 합칠 수 있다. `test`와 `[[ ]]`는 다른 형태의 operator를 사용함.

```
| operator | test | [[ ]] and (( )) |
| AND      | -a   | &&              |
| OR       | -o   | "||"            |
| NOT      | !    | !               |
```

아래는 AND operator를 사용하여 integer가 특정 범위 안에 있는지를 판단하는 스크립트 예시.

```
#!/bin/bash

# test-ingeter-and: determine if and integer is within a range of values

MIN_VAL=1
MAX_VAL=100

INT=50

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    if [[ "$INT" -ge "$MIN_VAL" && "$INT" -le "$MAX_VAL" ]]; then
        echo "$INT is within $MIN_VAL to $MAX_VAL"
    else
        echo "$INT is out of range"
    fi
else
    echo "INT is not an integer." >&2
    exit 1
fi
```

`[[  ]]`을 썼기 때문에 `&&` operator를 사용했는데, test command를 사용하면 아래와 같이 쓸 수 있다.

```
    if [ "$INT" -ge "$MIN_VAL" -a "$INT" -le "$MAX_VAL" ]; then
```

`!` negation operator는 expression의 결과를 반대로 만들어줌. 아래는 특정 범위를 벗어난 integer인지 확인하는 script

```
#!/bin/bash

# test-ingeter-and: determine if and integer is within a range of values

MIN_VAL=1
MAX_VAL=100

INT=50

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    if [[ ! ("$INT" -ge "$MIN_VAL" && "$INT" -le "$MAX_VAL") ]]; then
        echo "$INT is outside $MIN_VAL to $MAX_VAL"
    else
        echo "$INT is in range"
    fi
else
    echo "INT is not an integer." >&2
    exit 1
fi
```

grouping을 위해 괄호를 사용하였는데, 이게 없으면 negation이 처음 나오는 expression에 적용되기 때문.

test에서 사용하려면 escape를 해줘야된다.

```
    if [ \( "$INT" -ge "$MIN_VAL" && "$INT" -le "$MAX_VAL" \) ]; then
```

`test`와 `[[ ]]`는 거의 같은 일을 하는데 뭐가 더 선호될까?

`test`는 전통적인 반면 (system startup에 주로 사용되는 POSIX specification for standard shell) `[[  ]]`는 bash와 다른 모던 shell들에 특화되어있다.

### Control operator: another way to branch

bash는 branching에 사용할 수 있는 두가지 operator를 제공해줌: `&&`(AND)와 `||`(OR)는 `[[ ]]`안에서 사용되는 것처럼 쓸 수 있다.

```
command1 && command2
command1 || command2
```

이게 어떻게 작동하는지 이해하는게 중요함.

`&&` operator가 사용되면 command1이 실행되었을 때 성공했을 경우에만 command2가 실행된다.

`||`operator는 command1이 실행되었을 때 실패했을 경우에만 command2가 실행된다.

그래서 아래와 같은 명령어 가능.

```
$ mkdir temp && cd temp
```

`temp`를 만들고 이게 성공했을 경우에만 작동함.

아래와 같이 `||`를 사용한 명령어도 가능하다.

```
$ [[ -d temp ]] || mkdir temp
```

temp 디렉토리가 있는 경우에는 `[[ -d temp ]]` 명령어가 true를 반환하고 mkdir 명령어는 실행되지 않는다. 이런 형태의 구조는 스크립트에서 아래와 같이 에러 핸들링을 하는데 많이 사용된다.

스트립트가 `temp` 디렉토리가 필요하고, 디렉토리가 없을 때 스크립트를 종료시키려면 이렇게 사용하면 된다.

```
[ -d temp ] || exit 1
```

### Summing up

처음에 챕터를 시작할 때 이전에 만든 `sys_info_page` 스크립트가 user가 모든 home 디렉토리를 읽을 수 있는 권한이 있는지에 따라 명령어를 다르게 부여하려고 했었다.

`if`를 사용해서 `report_home_space` function을 만들고 이를 사용할 수 있음.

```
report_home_space() {
    if [[ "$(id -u)" -eq 0 ]]; then
        cat <<- _EOF
            <h2>Home spae utilization (All user)</h2>
            <pre>$(du -s /home/*)</pre>
        _EOF
    else
        cat <<- _EOF
            <h2>Home spae utilization ($USER)</h2>
            <pre>$(du -sh $HOME)</pre>
        _EOF
    fi
    return
}
```

superuser는 id가 0이기 때문에 이 경우 모든 home directory를 확인할 수 있다.


