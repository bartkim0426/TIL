# create shell script to remove all pyenv versions

### Purpose

Recenlty I figured out that I have over 50 pyenv versions. I want to delete almost all of them, but I can't find pyenv commands for uninstall multiple versions at once.

So I created simple shell script to uninstall multiple pyenv versions.


### Process

I thought it can be done with 2 steps.

1. Write all deleted version to simple text file.
2. Read this version line by line and execute `pyenv uninstall <version>`

### Create text file

It can be good to include this function to script, but I decided not to because it's hard to choose which versions will be deleted.

I can make cli interface, but it could be much more ambiguous than edit text file that contains ones to be deleted.

```
pyenv versions --bare --skip-aliases > pyenv-deleted-versions.txt
```

File will be like below.

```
2.7
3.6.0
3.6.1
3.6.7
3.6.8
3.6.8/envs/wants_to_delete
3.7.1
3.7.3
3.8.0
3.9.0
3.9.0/envs/wants_to_delete_2
3.9.0/envs/not_wants_to_delete
```

Open the file and remain versions that only want to delete. You don't have to care about alias ones since if you delete specific version, alias will be gone too.

You can also delete specific python version.

I want to delete
- python 3.6.0, 3.6.1, 3.6.7, 3.7.1, 3.8.0
- 3.6.8/envs/wants_to_delete
- 3.9.0/envs/wants_to_delete_2

So the file will be only contains specific versions want to delete. like below.

```
3.6.0
3.6.1
3.6.7
3.6.8/envs/wants_to_delete
3.7.1
3.7.3
3.8.0
3.9.0/envs/not_wants_to_delete
```

Then, let's create simple script.

### 
