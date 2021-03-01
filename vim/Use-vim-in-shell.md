# Use vim in terminal (bash, zsh shell)

기본으로 터미널 단축키는 emacs 단축키와 동일한 방식의다. 나는 vim을 주로 사용하기 때문에 터미널에서도 vim 단축키를 사용한다. normal 모드를 지원하기 때문에 훨씬 편하게 터미널에서 명령어를 작성할 수 있다.

```
set -o vi
```

> 쉘 시작시마다 적용시키려면 zshrc나 bashrc에 추가해주면 된다.

- normal mode, visual mode, insert mode를 모두 지원한다.
- vim과 동일하게 사용 가능
    - normal mode: esc
    - insert mode: i, a (I, A)
    - visual mode: v, V
    - 
- j, k로 명령어 히스토리 이동 (방향키)
- ciw, c$ 등 거의 모든 vim 명령어를 지원한다.
