## Use vim for read man page

I don't like to see man or help texts with less. So I try to find out the way to look man page with vim.

I found `vim-man` - it's quite old but still fascinating.

> https://github.com/vim-utils/vim-man


### Install with vim-plug

in your `~/.vimrc`, 

```
...

Plug 'vim-utils/vim-man'
...
```

And install plugin (`:source %`, `:PlugInstall`)

Then you can now open the man page from vim with `:Man` command.

But still it's annoying to open vim for man page, then you can use the script from the repository.


(Mac OS based commands)

Add `/usr/local/bin/viman` file and add the contents below.

```
#! /bin/sh
vim -c "Man $1 $2" -c 'silent only'
```

```
# Add file and add contents above
$ vi /usr/local/bin/viman

# Give execution permission
$ chmod +x /usr/local/bin/viman

# Then you can use viman command instead of vim
$ viman ls
```

If you want to use `viman` instead of man, add alias to your bashrc (or `~/.zshrc`)

```
alias man=viman
```
