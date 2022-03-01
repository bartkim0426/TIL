# unity essentials

unity에서 제공하는 [Unity Essentials](https://learn.unity.com/mission/real-time-creation-essentials?pathwayId=5f7bcab4edbc2a0023e9c38f) 교육을 들으면서 정리한 내용


## Unity Plans

최근 12개월 동안 $100k 수익이 발생하지 않았으면 무료. (저정도 잘 되면 라이센스가 안아까울듯)


## Install unity hub

아래 스토어에서 Unity Hub를 다운받아 설치.

[unity store](https://store.unity.com/#plans-individual)

unity hub를 실행시키면 단계에 따라 unity id를 만들고 미니게임을 선택하면 해당 미니게임으로 unity project를 열 수 있다.

자세한 내용은 [tutorial](https://learn.unity.com/tutorial/install-unity-and-the-hub?uv=2019.4&pathwayId=5f7bcab4edbc2a0023e9c38f&missionId=5f77cc6bedbc2a4a1dbddc46&projectId=5fa1b1a1edbc2a001f612a3d#5fa1bde7edbc2a002191ef25) 참고

## Unity editor

기본 인터페이스에는 5가지 영역이 이씀

- Scene view and Game view
- Hierarchy window
- Project window
- Inspector window
- Toolbar

원하는대로 layout을 바꿀 수 있음. 프리미어나 포토샵을 보는거같음.

### Using Scenes

Unity 에디터의 프로젝트는 `Scene`으로 구성됨.

scene을 구성하는 엄격한 규칙은 없다고 함.

### Navigate Scene

Unity 에디터에서 작업시에 씬 뷰를 navigate 하는게 매우 중요.

드론 카메라를 조작하는것처럼 모든 각도/거리에서 볼 수 있음.
- Pan: toolbar에서 Hand tool을 선택한 이후 클릭/드래그 해서 이동 가능
- Zoom: 씬 뷰를 Option이나 right click한 후 드래그
- Orbit: Option, left-click한 후 드래그, 2D 모드에서는 적용 안됨.
- Focus (Frame select): GameObject가 선택되었으면 F키로 포커스 가능.

#### Flythrough mode
많은 게임에서 그렇듯이 first person 주변을 날아다니면서 navigate도 가능함.

- Click and hold the right mouse button.
- Use WASD to move the view left/right/forward/backward.
- Use Q and E to move the view up and down.
- Select and hold Shift to move faster.


> navigate때문에 트랙패드로 불편해서 못할듯.. 마우스가 필수로 필요하지 않을까 싶음 ㅠㅠ

### Package

패키지 관리자를 통해서 패키지를 관리함.

```
Window > Package Manager
```

