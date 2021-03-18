# terraform version error refreshing state


Those error occured when using lower version than the version when snapshot created.

```
Error refreshing state: state snapshot was created by Terraform v0.12.29, which is newer than current v0.12.23; upgrade to Terraform v0.12.29 or greater to work with this state
```

Just update terraform version using tfenv.


```
# install tfenv if not installed
brew install tfenv

# list env
tfenv list-remove

# install specific version
tfenv install 0.12.29

# use specific version
tfenv use 0.12.29
```
