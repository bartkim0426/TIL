# android log with adb

android에서 발생하는 log를 확인할 일이 있어서 정리해둔다.


## install

```
brew install android-platform-tools

brew install android-file-transfer
```

## check adb

```
# find devices
adb devices

# setup log tag
adb shell setprop log.tag.GAv4 DEBUG

# monitor log
adb logcat -s GAv4
```
