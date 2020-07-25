# vim resize split size

vim에서 split을 사용하다보면 split size를 조정해야 할 일이 생기고는 한다.

이럴 때 자주 쓰이는 명령어들을 정리해둔다. (항상 검색해서 다시 사용하곤 했음)


### :resize 명령어

기본적인 명령어는 `:resize`이다 (`:res`)

`:resize 60`과 같이 숫자를 적으면 해당 숫자로 위아래 크기를 조정해준다. `+5`, `-5`같이 사용할 수도 있다.

수직 split (vertial split)을 변경하려면 `:vertical resize`를 사용하면 된다.

```
# 현재 split 수평 size를 60으로
:resize 60

# 현재 split size에 +, -5
:resize +5
:resize -5

# 현재 split 수직 size를 60으로
:vertical resize 60
```

resize는 자주 사용하는 만큼 단축키도 제공한다. `CTRL-W`

```
# 모든 split을 동일하개
<CTRL-W>=

# 현재 split의 수직 size를 +, -
<CTRL-W>+
<CTRL-W>-
# 명령어 이전에 숫자를 입력하면 해당 숫자만큼 +, -
10<CTRL-W>+

# 현재 split의 수직 size를 최대로
# 수직은 언더바 (_), 수평은 pipe (|)로 꽤나 직관적인 명령어이다.
<CTRL-W>_

# 현재 split의 수평 size를 최대로
<CTRL-W>|
```

> `:help resize`를 한 번 읽어보면 큰 도움이 된다.


