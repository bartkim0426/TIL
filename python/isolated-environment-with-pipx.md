# isolated environment with pipx

pipx creates an isolated environment for each application. (simliar to brew, npx, apt)

With pipx, you can manage and use your packages globally without other management tools like pyenv, poetry and so on.

It's useful the command like `awscli` for using globally.

## Install

```
# install with brew
brew install pipx
pipx ensurepath
```

### shell completion

Follow instructions to use tab completions

```
pipx completions

Add the appropriate command to your shell's config file
so that it is run on startup. You will likely have to restart
or re-login for the autocompletion to start working.

bash:
    eval "$(register-python-argcomplete pipx)"

zsh:
    To activate completions for zsh you need to have
    bashcompinit enabled in zsh:

    autoload -U bashcompinit
    bashcompinit

    Afterwards you can enable completion for pipx:

    eval "$(register-python-argcomplete pipx)"

tcsh:
    eval `register-python-argcomplete --shell tcsh pipx`

fish:
    register-python-argcomplete --shell fish pipx | source

```

If you're using zsh, add below in `~/.zshrc`

```
# pipx autocompletion
autoload -U bashcompinit
bashcompinit
eval "$(register-python-argcomplete pipx)"
```

If you reload zsh, you can use python package directly installed by pipx 


## Usage


```
# install
pipx install PACKAGE
# example
pipx install pycowsay

# list
pipx list

# run program
pipx run pycowsay

<  >

   \   ^__^
    \  (oo)\_______
       (__)\       )\/\
           ||----w |
           ||     ||
           
# check where pycowsay installed
which pycowsay
/Users/username/.local/bin/pycowsay
```
