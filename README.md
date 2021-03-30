# vagrant-k8s-deploy-ubuntu

vagrant code and ansible playbooks to deploy a multi-node kubernetes cluster

## Requirements:
-  [Oracle Virtual Box](https://www.virtualbox.org/wiki/Downloads)
- [vagrant](https://www.vagrantup.com/downloads.html)

## Fixed things
- It does not require the NFS server on windows to mount the director on VM


## Howto:
- Clone this repo
```
$ git clone https://github.com/ashgupta07/vagrant-k8s-deploy-ubuntu.git
```
- Replace the value of N with the number of worker nodes(other than 1 control plane) for the cluster
```
sed -i 's/^N =.*/N = 3/' Vagrantfile
```

- Install vagrant scp plugin to copy join file
```
$vagrant plugin install vagrant-scp
```

- Deploy the cluster
```
vagrant up
```

- Install kubectl and helm on local machine

- set KUBECONFIG variable to the generated kubeconfig file in current directory.
```
$ export KUBECONFIG=<full path to current directory>/kubeconfig
```

## Manage cluster nodes
```
$ vagrant ssh k8s-master
$ vagrant ssh node-1
$ vagrant ssh node-2
....
```
## login to VMs
- Use putty to login to VMs and use private key to $HOME/.vagrant.d/insecure_private_key

```
$ ssh vagrant@192.168.50.10
```