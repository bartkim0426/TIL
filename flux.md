
## Flux


### Prerequisites 

First, create [github personal access token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) and give all repo-permissions

```
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>
```

### Install flux

```
brew install fluxcd/tap/flux
```

(optional)  load `flux` bash completion by add below to profile (`~/.bashrc` or `~/.zshrc`)

```
. <(flux completion bash)
```

### Instasll flux components
verify cluster satisfies prerequisites

```
flux check --pre
► checking prerequisites
✔ kubectl 1.19.2 >=1.18.0-0
✔ Kubernetes 1.19.6-eks-49a6c0 >=1.16.0-0
✔ prerequisites checks passed
```

Run bootstrap command

```
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=<repository_name> \
  --branch=main \
  --path=./clusters/<cluster_name> \
  --personal
```

If owner is organization, don't use `--personal` options

```
flux bootstrap github \
  --owner=<organization> \
  --repository=<repo-name> \
  --branch=<organization default branch> \
  --team=<team1-slug> \
  --team=<team2-slug> \
  --path=./clusters/my-cluster
```


### Clone git repository

```
git clone https://github.com/$GITHUB_USER/<repo-name>
cd <repo-name>
```

### Add source-code repository to Flux

```
flux create source git <source-project> \
  --url=https://github.com/<source-git-repository> \
  --branch=main \
  --interval=30s \
  --export > ./clusters/<cluster-name>/<project>-source.yaml
```

Then `<project>-source.yaml` manifest created.

### Deploy application

can deploy application via kustomize, or helmrelease.

```
flux create kustomization <project> \
  --source=<project> \
  --path="./kustomize" \
  --prune=true \
  --validation=client \
  --interval=5m \
  --export > ./clusters/<cluster-name>/<project>-kustomization.yaml
```

Then commit and push `Kustomization` manifest to repository

```
git add -A && git commit -m "Add podinfo Kustomization"
git push
```

### Watch Flux sync the application

Now 

