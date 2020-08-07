# View help text in new tab

vim `help`는 아주아주 유용한 명령어이다. 하지만 기본으로 `:help`는 수평(Horizontal) split으로 열리기 때문에 작은 모니터 ~대부분의 경우지만..~ 에서 보는 경우 잘 보기 위해 기존의 창을 닫아야 하는 경우가 발생한다.

물론 해당 창을 최대화 시킬수는 있지만 (`<Ctrl+W>_`) 처음부터 새로운 탭으로 열거나 vertical split으로 열면 더욱 편리하다.

### help text 새로운 탭으로 열기

```
:tab help {subject}
```

### help text vertial split으로 열기

```
:vert help {subect}
```

### 예시

한번씩 직접 해보기를 권한다.

```
# help의 help text open
:help help

# 해당 help text를 수평 최대화
<CTRL-W>_

# help text 종료
:q

# open help text as new tab
:tab help help

# close
:q

# open help text with vertical split
:vert help help
```
