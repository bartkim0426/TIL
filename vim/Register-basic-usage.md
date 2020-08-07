# Register basic usage

vim의 register는 아주 강력한 기능이다. 단순히 copy/paste 뿐 아니라 레지스터 자체를 수정해서 사용이 가능하기 때문이다.

하지만 나도 제대로 공부해보지 않아서 잘 사용하지 않아 이번 기회에 간단한 사용법만 정리해둔다.

vim은 yank, cut, delete하면 모두 레지스터에 저장된다.

## Unnamed register

기본으로 레지스터를 설정하지 않으면 "이름이 없는 register", 즉 `unnamed register` (`"`)에 저장된다.

> `:reg "`로 현재 unnamed register에 무엇이 있는지 확인할 수 있다.

이 경우 yank, cut, delete 한 모든 문자가 레지스터에 덮어써지게 된다.

## Register: a-z

unnaemd register 말고 직접 register를 지정할 수도 있다. (a-z)
register 지정은 쌍따움표(`"`)로 하는데 다음과 같이 사용 가능하다.

```
"{register}y
```

처음 봤을땐 읭? 싶지만 실제로 몇 번 쳐보면 아주 간단하다.

다음 예시를 직접 따라해보자.

```
hello
world
```

위 파일을 만들고 아래를 따라해보자.

```
# Move cursor to hello and copy hello with "a" register
"ayiw

# Move cursor to world and copy world with "b" register
"byiw
```

> `iw`는 In Word로 현재 커서의 단어를 지칭하는 motion이다.

이제 a, b register를 paste 하면 각각 hello, world가 paste된다.

```
"ap
hello

"bp
world
```

> 딱히 자주 쓰이지는 않지만 특별히 복사해서 여러번 붙여넣는 상황에 특정 레지스터에 저장하면 이후 덮어써지지 않기 때문에 유용하게 사용된다.

## Special register

vim에서는 미리 등록된 register가 있다. 이를 적절히 활용하면 아주 큰 도움이 될..것 같지만 사실 나도 잘 사용하지는 않는다.

주로 사용할법한 register인 `0`만 정리해본다.

### `0` register: yank한 내용을 기억

`0` register는 최근에 복사된 항목이 들어있다. delete, change 등의 항목은 들어가지 않기 때문에 마지막으로 복사한 항목을 사용할 때 유용하다.

아래 예시처럼 사용하면 되겠다.

```
1. This line is what I want to yank
2. This line should be deleted
```

우선 첫번째 줄을 복사하고, 두 번째 줄을 지운 다음에 다시 첫 번째 줄을 붙여넣는 상황을 가정해보자.

```
# move cursor to first line
yy

# move cursor to second line
dd

# paste with 0 register
"0p
```

그냥 paster를 사용했으면 unnamed register에 있는 `2. This line should be deleted`가 paste 됐겠지만 `0` 레지스터를 사용하여 의도대로 복사된다.


### Other registers

> `:help registers`에 나오는 내용. 한 번 읽어보면 도움이 된다.

1. The unnamed register ""
2. 10 numbered registers "0 to "9
3. The small delete register "-
4. 26 named registers "a to "z or "A to "Z
5. Three read-only registers ":, "., "%
6. Alternate buffer register "#
7. The expression register "=
8. The selection and drop registers "*, "+ and "~ 
9. The black hole register "_
10. Last search pattern register "/


