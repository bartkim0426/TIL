# vim terraform autocomplete with coc vim

### Install coc.vim

```
Plug 'neoclide/coc.nvim', {'branch': 'release'}
```


### Install node

To run coc (language server), have to install node.js

```
curl -sL install-node.now.sh/lts | bash
```

### Install terraform-ls

I'm using [terraform-ls](https://github.com/hashicorp/terraform-ls). You can use [terraform-lsp]() if you want.

```
brew install hashicorp/tap/terraform-ls
```

### Add languageserver config to CocConfig

```
:CocConfig
```

```
{
	"languageserver": {
		"terraform": {
			"command": "terraform-ls",
			"args": ["serve"],
			"filetypes": [
				"terraform",
				"tf"
			],
			"initializationOptions": {},
			"settings": {}
		}
}
```
