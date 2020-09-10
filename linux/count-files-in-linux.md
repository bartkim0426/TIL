# count files in linux

`wc -l`를 사용하여 디렉토리의 파일의 개수를 셀 수 있음.

```
# 현재 디렉토리 내부의 파일 개수
ls | wc -k

# find 한 파일의 개수 (서브 디렉토리안의 파일을 포함한 현재 디렉토리 안의 모든 파일 개수)
find . -type f | wc -k
```


> 구체적인 사용법은 `man wc`
