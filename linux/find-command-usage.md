# find command usage

find 명령어는 linux에서 아주 유용하게 쓰이는 명령어이다.

하지만 나는 실제로 cli에서 잘 사용하지는 않고 대신 `vim`에서 자주 사용하게 된다.

```
vimgrep /<pattern>/g `find . -name "*.md"`
```

## file만 검색

```
find . -type f
```

## 파일명 검색

```
find . -name myfile.md
```

## wildcard 검색

```
find . -name "myfile_*.md"
```
