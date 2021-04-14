# count files in linux

## wc

wc는 `word count`의 줄임말로 사용자가 지정한 행, 단어, 문자 수를 세는 프로그램.

```
$ wc [-clmw] [file...]
```

`wc 옵션 파일명`으로 사용되며 파일 이름을 적지 않으면 standard input으로 정보를 받아들여서 계산한다.

옵션은 다음과 같음

```
-l: line
-w: word
-c: character
```

### 예시

주로 파일 안의 line을 읽을 때 사용한다.

`wc -l`를 사용하여 디렉토리의 파일의 개수를 셀 수 있음.

```
# 현재 디렉토리 내부의 파일 개수
ls | wc -k

# find 한 파일의 개수 (서브 디렉토리안의 파일을 포함한 현재 디렉토리 안의 모든 파일 개수)
find . -type f | wc -k
```

ex. `/var/log` 디렉토리에 확장자가 .log인 파일의 개수 세기

```
$ ls -l /var/log/*.log | wc -l
6
```

반대로 명령어를 사용하면 각각의 파일이 몇 줄인지를 반환하게 된다.

```
$ wc -l /var/log/*.log
      0 /var/log/alternatives.log
   1072 /var/log/auth.log
    196 /var/log/cloud-init-output.log
   1920 /var/log/cloud-init.log
    267 /var/log/dpkg.log
  10680 /var/log/kern.log
  14135 total
```


파일 개수를 세거나 하는 등 standard input, output과 사용하기 좋은 명령어이다. 항상 필요할 때에는 생각나지 않는데, 이번 기회에 정리해 두고 잘 사용해볼 예정이다.

> 구체적인 사용법은 `man wc`
