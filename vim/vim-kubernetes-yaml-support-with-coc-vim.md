# vim kubernetes yaml support with coc vim

### Install coc.vim

```
Plug 'neoclide/coc.nvim', {'branch': 'release'}
```


### Install node

To run coc (language server), have to install node.js

```
curl -sL install-node.now.sh/lts | bash
```

### Add coc-yaml extension

```
:CocInstall coc-yaml
```


### Add configurations

```
:CocConfig
```

Add schemas for kubernetes

```
{
  "yaml.schemas": {
      "kubernetes": "/*.yaml"
  }
}
```


