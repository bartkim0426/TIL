# deploy django in elastic beanstalk

## 0. prerequisites

- django application prepare to deploy
- awscli command
- aws account and profile (`~/.aws/credentials`)

## 1. setup config

Create config file in root directory

`.ebextensions/django.config`

```
container_commands:
  01_set_bne:
    command: "sudo ln -f -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime"
  02_migrate:
    command: "$PYTHONPATH/python manage.py migrate"
    leader_only: true
  03_collectstatic:
    command: "$PYTHONPATH/python manage.py collectstatic --noinput --verbosity=0 --clear"
option_settings:
  aws:elasticbeanstalk:container:python:
    WSGIPath: path_for/wsgi.py
  "aws:elasticbeanstalk:container:python:staticfiles":
    "/static/": "static/"
```

- change WSGIPath to path for wsgi.py file
- add/remove container command to execute before deploy


## 2. eb init with application

Init elastic beanstalk.

EB is deployed based on the root directory (reference to `.elasticbeanstalk` directory)

```
$ eb init --profile <profile> --interactive
If you want to set aws profile name, use `--profile`. Also, if want to use ssh inside eb instance, use `-interactive` option and set or create ssh key.

> If you create new key, your pem key will be automatically download in `~/.ssh` directory.

```
$ eb init --profile <aws_profile> --interactive
```

## 3. eb create environment

You can use multiple environment for a single application. i.e. staging, production.

First, we'll make staging environment.

```
$ eb create staging    # you can use any environment name instead of staging
# it will takes around 5 mins

$ eb status
Environment details for: staging
  Application name: <application_name>
  ...
  CNAME: staging-xxxx.elasticbeanstalk.com
  ...
```

Add CNAME to `ALLOWED_HOSTS` in django settings

```
ALLOWED_HOSTS = ['staging-xxxx.elasticbeanstalk.com']
```

After environments are created, you can use `eb list` and `eb use` to set environment for deploy.

## 4. (Optional) Set env for DJANGO_SETTINGS_MODULE

We'll use staging django config, so set it for environment.

```
eb setenv DJANGO_SETTINGS_MODULE=project.settings.staging  # path for your staging settings
eb prinenv DJANGO_SETTINGS_MODULE  # check env is set
```

## 5. Deploy

```
eb deploy
```

## 6. (Optional) EB ssh

You can connect ec2 instance with ssh if you enabled ssh.

```
eb ssh
```

Inside container, you can access your code with commands below.

Source code is in `/opt/python/current/app/`.

```
source /opt/python/run/venv/bin/activate
source /opt/python/current/env
cd /opt/python/current/app
python manage.py <commands>
```



