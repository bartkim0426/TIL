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
[ec2-user@ip-172-31-5-10 ~]$ ps
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
