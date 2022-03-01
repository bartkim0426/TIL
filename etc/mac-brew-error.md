# mac brew error

2020년 10월부터 homebrew가 더이상 shallow clone을 생성하지 않기 때문에 오랜 기간 brew update를 진행하지 않은 경우 아래 에러가 발생할 가능성이 높다.

```
Error: homebrew-cask is a shallow clone. To `brew update` first run:

git -C /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core fetch --unshallow git -C /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask fetch --unshallow This restriction has been made on GitHub's request because updating shallow clones is an extremely expensive operation due to the tree layout and traffic of Homebrew/homebrew-core and Homebrew/homebrew-cask. We don't do this for you automatically to avoid repeatedly performing an expensive unshallow operation in CI systems (which should instead be fixed to not use shallow clones). Sorry for the inconvenience!
```

Github에서 shallow clone usage를 줄이기 위해 homebrew측에 직접적인 요청을 했다고 하고, 이후 full clone 방식으로 변경이 되면서 기존 shallow clone을 사용하던 형태를 full clone으로 변경하는 작업이 필요하다.

더 자세한 내용은 관련 discussion 링크들을 참고

- https://github.com/Homebrew/discussions/discussions/225
- https://github.com/Homebrew/discussions/discussions/226


해결은 homebrew-core, homebrew-cask 깃을 둘 다 full clone으로 변경해주는 아래 명령어들을 실행하면 된다.

```
git -C "/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core" fetch --unshallow
git -C "/usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask" fetch --unshallow
```

이후에 `brew update`를 진행하면 잘 실행되는 것을 볼 수 있다.


