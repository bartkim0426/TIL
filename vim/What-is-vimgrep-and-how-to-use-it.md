# What is vimgrep and how to use it

`vimgrep`은 vim의 빌트인 명령어로 vim의 regex engine을 사용한다. 다른 명령어들 (ack, git-grep)만큼 빠르지는 않지만 유용하게 사용 가능하다.

```
:vim[grep][!] /{pattern}/[g][j] {file} ...
```

패턴, 파일 두가지 인자를 받음.

## 현재 파일에서 검색

가장 쉬운 사용법은 `%` simbol로  현재 active buffer를 검색하는 것. (`:help _%`)

```
:vimgrep /\v`[^`]*`/g %
```

검색된 내용 사이의 이동은 다음으로 가능하다.

```
:cnext
:cprev
:cfirst
:clast
```

## 여러 파일에서 검색

여러 파일을 검색해보자.

일단 그냥 파일을 명시적으로 적어주는 방법이 있음.


```
:vimgrep /\v`[^`]*`/g filename1.md filename2.md filename3.md
```

wildcard를 사용할 수 있음.

```
# wildcard 사용
:vimgrep /\v`[^`]*`/g *.md

# backtick expression
:vimgrep /\v`[^`]*`/g `find . -type f`
```

`args`로 지정한 path를 `##`로 사용이 가능하다.

```
:args `find . -type f`
:vimgrep /\v`[^`]*`/g ##
:vimgrep /\v`[^`]*`/g ##
:vimgrep /\v`[^`]*`/g ##
```

## 기타

- `g` flag를 주지 않으면 가장 처음 나온 항목만 반환
- pattern을 주지 않으면 모든 characters 검색
- 더 좋은 툴을 사용하려면 `git-grep`, `ack`, `ag` 등을 사용

출처:

http://vimcasts.org/episodes/search-multiple-files-with-vimgrep/
