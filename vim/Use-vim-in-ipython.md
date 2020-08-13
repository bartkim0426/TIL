# Use vim in ipython

ipython에서는 다양한 단축키를 제공하지만 기본 shell 단축기이기 때문에 따로 단축키를 알아야 하는 불편함이 있다.

ipython 내에서도 vi mode를 지원하는 명령어가 있다.

```
ipython --TerminalInteractiveShell.editing_mode=vi
```

매번 이렇게 쓰기 귀찮기 때문에 ipython config에 넣고 써도 된다. profile이 있다면 그걸 쓰고 없다면 새로 만들자

```
ipython profile create <my_profile>

cd ~/.ipython/<my_profile>/
vi ipython_config.py
```

아래 내용을 추가해주자. (다른 설정도 넣을 수 있다)

```
c = get_config()
c.TerminalInteractiveShell.editing_mode = 'vi'
```

이제 ipython 명령어를 실행시키면 edit-mode가 vi로 열리는 것을 볼 수 있다.

기본적으로 insert mode이고 (`[ins]`) esc를 누르면 normal/visual 모드 (`[nav]`)로 변경된다. 아주 편리하다!

```
Python 3.7.3 (default, Mar 27 2019, 16:54:48)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.7.0 -- An enhanced Interactive Python. Type '?' for help.

[ins] In [1]:

[nav] In [2]:
```

profile을 만들기 귀찮다면 `alias`로 설정해 두어도 된다 (...) `~/.bashrc` (`~/.zshrc`)에 다음을 추가해주자

```
alias ipython="ipython --TerminalInteractiveShell.editing_mode=vi"
```
