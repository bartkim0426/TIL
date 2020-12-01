#  gcloud use multiple configuration account


```
# see all configurations
$ gcloud config configurations list

# create configurations
$ gcloud config configurations create <config_name>

# activate
$ gcloud config configurations activate <config_name>

# set values
$ gcloud config set project <my_project>
$ gcloud config set compute/region <region_name>
$ gcloud config set compute/zone <region_name>
$ gcloud config set account <my-account@example.com>

# login
$ gcloud auth login

# see region list
$ gcloud compute zones list

# see current config
$ gcloud config list
```

> 만약 결제 설정이 안되어있다면 gcloud console -> 결제 에서 결제 계정 설정을 해주어야 한다. (핸드폰 인증 및 카드 등록)
