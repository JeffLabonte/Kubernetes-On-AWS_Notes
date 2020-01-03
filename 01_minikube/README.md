# Up and Running - HelloWorld

This is an up and running from your development machine.

## Requirements

1. kubectl
2. VM Manager:
    * VirtualBox
    * KVM
    * VMWare
    * or any other I guess
3. minikube
4. Docker
    * On your dev machine

## Install kubectl

I am running Linux so I will stick to Linux install:

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
```
