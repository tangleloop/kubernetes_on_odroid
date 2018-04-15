# Overview

This is a rough guide / notes for installing Kubernetes on a cluster of ODROID-HC2 systems.

The ODROID-HC2 is a ARM system intended as a network attached storage solution.  Each unit has 2GB of RAM and has a high speed SATA interface to work with almost any SATA disk you want to put in it.

Note: It is headless, so there are no monitor ports.  This means that the only console available is a serial console, and that is only available with the right adapter.  So far, I have not needed it to get my cluster running, but your mileage may vary, so you may want to get one.

To me, this seemed to be an excellent hardware platform to build a home kubernetes cluster server platform.

# My Experiences So Far

This has been a wonderful educational experience so far.  But my goal was to have a useful kubernetes cluster running at home, and whether this is possible is still a question.

## Issues

1.  The upgrade experience is not polished / stable and leaves a lot to be desired.

	At the time of this writing, the latest Kubernetes is v1.10.0.  I started with v1.9.6 and attempted an upgrade, which [was not successful](https://github.com/kubernetes/kubeadm/issues/744).  Fortunately, I didn't have anything important on it.  The process is not well documented, and the whole situation seems a bit scary at this point.  The risk of bringing down the cluster is high, so it may not be wise to use this in any sort of production situation just yet.

2.  Shared Storage is not included.

	To take advantage of the large amount of disk space that these units can provide, I intend to use glusterfs to provide shared storage across the nodes.  That is a [separate project](https://github.com/gluster/gluster-kubernetes), and I'm not sure how stable it is.

3.  Backups

	Its not clear how one should backup and restore the entire cluster.  Backups of Kubernetes itself and backups of applications running on Kubernetes are two entirely separate things, and, well, I haven't gotten that far yet.  This kind of ties in with the upgrade experience as mentioned above, which may not go smoothly.

4.  Kubernetes on ARM works, but it doesn't seem to be a first class citizen.

	Things are moving very fast in Kubernetes world, but the lack of attention on ARM shows more than I think it would on a 64 bit x86 platform.  2GB is not a lot of memory to work with, and the underlying system takes a large portion of it.

	For example, Ubuntu supports a snap based installer, but it does not appear to be compiled for or support ARM based systems.  It also recommends a minimum of 32 GB of RAM per node, which most ARM systems aren't capable of (yet).

# Primary References

## ODROID-HC2

*  [magazine.odroid.com: ODROID-HC2: 3.5" High powered storage](https://magazine.odroid.com/article/odroid-hc2-3-5-high-powered-storage/)
*  [ameridroid.com: ODROID-HC2](https://ameridroid.com/products/odroid-hc2)

## Armbian

*  [armbian.com: Odroid HC1 / HC2](https://www.armbian.com/odroid-hc1/)

Note: I have only tested with the Ubuntu server - LTS kernel, but the Debian server may work as well.

## Kubernetes

*  [kubernetes.io: Installing kubeadm](https://kubernetes.io/docs/setup/independent/install-kubeadm/)
*  [kubernetes.io: Using kubeadm to Create a Cluster](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/)

## Kuberenetes on ARM

*  [Setup Kubernetes on a Raspberry Pi Cluster easily the official way!](https://blog.hypriot.com/post/setup-kubernetes-raspberry-pi-cluster/)

*  [https://github.com/luxas/kubernetes-on-arm](https://github.com/luxas/kubernetes-on-arm)

# Hardware

My configuration includes three units, so I ordered 3 of each:

1. (3x) [ODROID-HC2](https://ameridroid.com/products/odroid-hc2) units.  Be sure to include power supplies and power plugs for your region.
2. (3x) [Sandisk Ultra 32GB Micro SDHC UHS-I Card with Adapter - 98MB/s U1 A1 - SDSQUAR-032G-GN6MA](https://www.amazon.com/gp/product/B073JWXGNT/ref=oh_aui_detailpage_o06_s01?ie=UTF8&psc=1)
3. (3x) [WD Red 4TB NAS Hard Disk Drive - 5400 RPM Class SATA 6Gb/s 64MB Cache 3.5 Inch - WD40EFRX](https://www.newegg.com/Product/Product.aspx?Item=N82E16822236599)

Note: Before using the SDHC cards that I used, you may want to check out [Raspberry Pi microSD card performance comparison - 2018](https://www.jeffgeerling.com/blog/2018/raspberry-pi-microsd-card-performance-comparison-2018).

## Hardware Considerations

1.  You will need adequate network ports on your switch and some network cables.

2.  Master Isolation: Your master node will not schedule pods for itself by default.  This is for security reasons according to kubernetes.io.  They have instructions to override this if desired.  I'm not sure yet if this will impact using it as a glusterfs storage node.

3.  You may want to purchase a USB-UART with your Odroids to allow console access.  I did not need one, but I wished I had purchased one when I was still trying out distributions.  You may find yourself stuck if you don't have one and you run into boot issues.

4.  I purchased a UPS system for my router and ODROIDs for stability sake.  I still have dreams of making this a useful home server platform.

# Why Armbian?

I used Armbian because:
*  It is Debian based, which appeared compatible with the instructions provided @ kubernetes.io
*  It appeared to be actively maintained.
*  I got it to work, so I stopped looking at alternatives.

My process was not at all scientific or well researched.  But it works, so it is what I'm using.

# Install Armbian

1.  Download an Armbian image.

	[armbian.com: Odroid HC1 / HC2](https://www.armbian.com/odroid-hc1/)

	The exact image I used was "Armbian_5.37_Odroidxu4_Ubuntu_xenial_next_4.9.71.7z" which is based on Ubuntu 16.04.4.

	At the time of this writing, Ubuntu 18.04.4 will be out soon.  I haven't tried it yet, so I'm not sure what complications it will bring.

2.  Install Armbian on your micro SD cards.

	I used [Etcher](https://etcher.io/) to burn images on my micro SD cards.  It was recommended somewhere and I found it to be extremely easy to use.
	
3.  First boot (How to find your network address and log in)

	For each node:
	
	1. Insert your micro SD card with Armbian into your ODROID.
	2. Connect a network cable
	3. Connect your power cable.

	Assuming your router has DHCP, and most do, your router will assign a network address for your ODROID.

	I was able to find the ip addresses of my nodes by using my router's admin page, which displays all active DHCP leases.  Another tool you can try is a linux command-line tool called arp-scan, available on most linux distros.

	'''sh
	sudo arp-scan --localnet
	'''

4.  Generate a unique machine-id for each node.

	On each node, log in as admin and run the following

	```sh
	$ rm /etc/machine-id
	$ reboot
	```

	During the reboot, a new unique machine-id file will be created.

5.  Get system updates. - TODO

6.  Setup /etc/hosts on each node - TODO

# Install Kubernetes

TODO

# Install Cri-Tools

TODO

# Initialize Cluster

TODO: explain the pod network

Run the following as root on the master node.

```sh
root@odroid01:~# kubeadm init --pod-network-cidr=10.244.0.0/16
[init] Using Kubernetes version: v1.10.1
[init] Using Authorization modes: [Node RBAC]
[preflight] Running pre-flight checks.
[preflight] Starting the kubelet service
[certificates] Generated ca certificate and key.
[certificates] Generated apiserver certificate and key.
[certificates] apiserver serving cert is signed for DNS names [odroid01 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.1.31]
[certificates] Generated apiserver-kubelet-client certificate and key.
[certificates] Generated etcd/ca certificate and key.
[certificates] Generated etcd/server certificate and key.
[certificates] etcd/server serving cert is signed for DNS names [localhost] and IPs [127.0.0.1]
[certificates] Generated etcd/peer certificate and key.
[certificates] etcd/peer serving cert is signed for DNS names [odroid01] and IPs [192.168.1.31]
[certificates] Generated etcd/healthcheck-client certificate and key.
[certificates] Generated apiserver-etcd-client certificate and key.
[certificates] Generated sa key and public key.
[certificates] Generated front-proxy-ca certificate and key.
[certificates] Generated front-proxy-client certificate and key.
[certificates] Valid certificates and keys now exist in "/etc/kubernetes/pki"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/admin.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/kubelet.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/controller-manager.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/scheduler.conf"
[controlplane] Wrote Static Pod manifest for component kube-apiserver to "/etc/kubernetes/manifests/kube-apiserver.yaml"
[controlplane] Wrote Static Pod manifest for component kube-controller-manager to "/etc/kubernetes/manifests/kube-controller-manager.yaml"
[controlplane] Wrote Static Pod manifest for component kube-scheduler to "/etc/kubernetes/manifests/kube-scheduler.yaml"
[etcd] Wrote Static Pod manifest for a local etcd instance to "/etc/kubernetes/manifests/etcd.yaml"
[init] Waiting for the kubelet to boot up the control plane as Static Pods from directory "/etc/kubernetes/manifests".
[init] This might take a minute or longer if the control plane images have to be pulled.
[apiclient] All control plane components are healthy after 27.504358 seconds
[uploadconfig] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[markmaster] Will mark node odroid01 as master by adding a label and a taint
[markmaster] Master odroid01 tainted and labelled with key/value: node-role.kubernetes.io/master=""
[bootstraptoken] Using token: xx1257.dmdcz94zmi6a6gnj
[bootstraptoken] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstraptoken] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstraptoken] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstraptoken] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: kube-dns
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join 192.168.1.31:6443 --token qy1257.dmdcz94zmi6a6gnj --discovery-token-ca-cert-hash sha256:b8939d6062571f9f6f4c1b3741a08ebcb9ce2ee0874fd7e2ab43741101b6d1d5
```

Run the command shown at the end of the instructions on each of the remaining nodes.

*Do not use the command line shown below.  Use the command line provided by your master node.*

```sh
root@odroid02:~# kubeadm join 192.168.1.31:6443 --token qy1257.dmdcz94zmi6a6gnj --discovery-token-ca-cert-hash sha256:b8939d6062571f9f6f4c1b3741a08ebcb9ce2ee0874fd7e2ab43741101b6d1d5
[preflight] Running pre-flight checks.
[preflight] Some fatal errors occurred:
	[ERROR CRI]: unable to check if the container runtime at "/var/run/dockershim.sock" is running: fork/exec /usr/local/bin/crictl -r /var/run/dockershim.sock info: no such file or directory
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
```

If you run into the same error that I do, you may need to ignore errors from cri-tools by appending "--ignore-preflight-errors=cri"

```sh
root@odroid02:~# kubeadm join 192.168.1.31:6443 --token qy1257.dmdcz94zmi6a6gnj --discovery-token-ca-cert-hash sha256:b8939d6062571f9f6f4c1b3741a08ebcb9ce2ee0874fd7e2ab43741101b6d1d5 --ignore-preflight-errors=cri
[preflight] Running pre-flight checks.
	[WARNING CRI]: unable to check if the container runtime at "/var/run/dockershim.sock" is running: fork/exec /usr/local/bin/crictl -r /var/run/dockershim.sock info: no such file or directory
[preflight] Starting the kubelet service
[discovery] Trying to connect to API Server "192.168.1.31:6443"
[discovery] Created cluster-info discovery client, requesting info from "https://192.168.1.31:6443"
[discovery] Requesting info from "https://192.168.1.31:6443" again to validate TLS against the pinned public key
[discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "192.168.1.31:6443"
[discovery] Successfully established connection with API Server "192.168.1.31:6443"

This node has joined the cluster:
* Certificate signing request was sent to master and a response
  was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the master to see this node join the cluster.
```

# Configure KubeCtl

TODO

# Initialize Pod Network

TODO

# Remove Kubernetes (to try a different configuration)

Note: this will destroy your currently running kubernetes cluster (and any pods which may be running)

Run the following command as root on all nodes.

```sh
kubectl reset
```
