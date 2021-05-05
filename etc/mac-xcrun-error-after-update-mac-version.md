# mac xcrun error after update mac version

After upgrade to Mac Big Sur, I encountered error below for specific commands including git.

```
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun
```


The problem is that Xcode Command-line Tools should be updated based on updated version of os.

So it can be solved with update Xcode

```
xcode-select --install
```
