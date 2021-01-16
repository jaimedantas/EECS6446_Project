# Requirements

To move along this tutorial and set up the required tools and applications, you will
need access to a cloud computing infrastructure or a set of Virtual Machines (VMs).
Make sure that you have ssh access to those resources and that you are comfortable
using the `bash` to interact with the server before you continue this tutorial.

The easiest way to interact with your virtual machine is using Visual Studio Code 
Remote Development via SSH. To use this feature, you need to install the
[Visual Studio Code](https://code.visualstudio.com/) and the [Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh)
extension. You can follow the VSCode tutorials to connect to the `master VM` to follow with the
tutorials. The VMs used in this tutorial need to be a debian-based distribution and would
preferably be `Ubuntu 18.04` or `Ubuntu 20.04` with the standard `bash`.

For this tutorial,
imagine the provided ssh private and public key is stored in `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`
and thus will automatically be used for ssh communications (in case you had the keys stored in a
different location, you need to use the `-i` option for all ssh commands). Also, assume the ip of
the provided VMs are `10.1.1.1` (master) and `10.1.1.2` (worker). We will be using these assumptions
throughout the tutorial.

**Table of Contents**
- TOC
{:toc}

## SSH Access

To ssh onto the master or worker, use the default `ubuntu` user:

```sh
# ssh onto the master
$ ssh ubuntu@10.1.1.1
# ssh onto the slave
$ ssh ubuntu@10.1.1.2
# ssh onto master with another ssh key
$ ssh ubuntu@10.1.1.1 -i ~/.ssh/my_other_ssh_key
```

## Initial Setup

First, we need to make sure to update all packages installed on all VMs:

```sh
# update the package list and upgrade installed packages on all machines
(master and worker) $ sudo apt-get update && sudo apt-get upgrade -qy
```

In case you will be using more than one VM for your cluster, make sure that there
is network connectivity between your VMs by checking their `ping` status.

```sh
# ssh onto the master and check connectivity with the workers
(master) $ ping 10.1.1.2
```

You should see an output like this:

```console
PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=1.00 ms
64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=0.416 ms
64 bytes from 10.1.1.2: icmp_seq=3 ttl=64 time=0.480 ms
64 bytes from 10.1.1.2: icmp_seq=4 ttl=64 time=0.471 ms
64 bytes from 10.1.1.2: icmp_seq=5 ttl=64 time=0.349 ms
64 bytes from 10.1.1.2: icmp_seq=6 ttl=64 time=0.348 ms
64 bytes from 10.1.1.2: icmp_seq=7 ttl=64 time=0.346 ms
64 bytes from 10.1.1.2: icmp_seq=8 ttl=64 time=0.362 ms
```

## Firewall Configurations

In case you needed to do firewall configurations yourself, open the following ports:

- `TCP` port `6443` for Kubernetes API
- `UDP` port `8472` for Flannel VXLAN
- `TCP` port `10250` for kubelet
- `TCP` port `80` for the application
- `TCP` port `9090` for prometheus
- `TCP` port `8091` for locust
- `TCP` port `3000` for grafana

## Anaconda Installation

In this project, we will be using Python for interacting with our cluster. Using other
programming languages is also possible, but might need additional setup from you. You
can install [Anaconda](https://docs.conda.io/en/latest/) to act as the environment manager on your cluster.

```sh
# download miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh
# install miniconda using non-interactive mode
bash ~/miniconda.sh -b -p $HOME/miniconda
# add bash hook for conda
echo 'eval "$(~/miniconda/bin/conda shell.bash hook)"' >> ~/.bashrc && source ~/.bashrc
```
