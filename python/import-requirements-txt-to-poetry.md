# import requirements txt to poetry

```
$ poetry init
$ for item in $(cat requirements.txt); do   poetry add "${item}"; done

# Install poetry -D
$ foe item in $(cat local.txt); do   poetry add "${item}" -D; done
```
