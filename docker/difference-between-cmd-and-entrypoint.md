# difference between cmd and entrypoint


컨테이너에서 실행되는 모든 명령어는 두 파트로 나뉜다: `command`와 `arguments`

Dockerfile에서는 이 두 가지로 정의된다

- `ENTRYPOINT`: container가 시작되면 실행
- `CMD`: `ENTRYPOINT`에 전달되는 arguemtns

`CMD`를 사용하여 이미지가 실행될 때 execute 시킬 수 있지만 올바른 방법은 `ENTRYPOINT`를 사용하고 기본 인자를 수정할 필요가 있을 때에만 `CMD`를 사용하는 것이다.


출처
- https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact
