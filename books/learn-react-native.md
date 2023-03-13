# 리액트 네이티브를 다루는 기술


## 01. 리액트 네이티브 첫걸음


### requirements

설치를 위해 꽤 많은 것들이 필요함

자세한 내용은 [리액트 네이티브 문서](https://reactnative.dev/docs/next/environment-setup) 참조

- node, watchman
- npx (npm) or yarn
- ruby (2.7.5)
- xcode, cocoapods (iOS)
- JDK, Android studio (Android)

### init project

```
npx react-native init LearnReactNative
```

각종 troubleshooting

node version
- 최신 버전으로 설치해주는게 맘편함

ruby version 안맞는 이슈
- rvm을 사용해서 2.7.5 설치 필요

```
rvm install 2.7.5
rvm --default use 2.7.5
```

### running android simulator

- android studio에서 Device Manager -> device 생성
- 만든 프로젝트 내에서 `yarn android` 실행


### running iOS simulator

intel chip 맥북의 경우 별다른 준비 없이 실행시키면 됨

- `yarn ios`

