Start a k3s cluster on mac m1 chip:

```
$ brew install --cask multipass
$ multipass launch --name k3s --mem 4G --disk 40G
$ multipass shell k3s 
ubuntu@k3s:~$ curl -sfL https://get.k3s.io | sh - 
ubuntu@k3s:~$ kubectl get nodes
```

Delete:

```
$ multipass delete k3s k3s
$ multipass purge
```