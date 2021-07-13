# exam-ckad
This repository contains exercises that can be used to prepare for the Certified Kubernetes Application Developer exam.

## Topics
* Core concepts
* Configuration
* Multi-container pods
* Observability
* Pod design
* Services and networking
* State persistence

## Shell settings

```bash
alias k='kubectl'
alias kns='k config set-context --current --namespace'
alias knsc='k config get-contexts'
alias kctx='k config use-context'
alias kctxc='k config get-contexts'
export dry='--dry-run=client -o yaml'
```

## ~/.vimrc
```
set expandtab
set tabstop=2
set shiftwidth=2
```
