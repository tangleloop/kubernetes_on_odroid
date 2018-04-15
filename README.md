# Overview

This is a rough guide / notes for installing Kubernetes on a cluster of ODROID-HC2 systems.

The ODROID-HC2 is a ARM system intended as a network attached storage solution.  It supports 

It is headless, so there are no monitor ports.  This means that the only console available is a serial console, and that is only available with the right adapter.  So far, I have not needed it to get my cluster running, but your mileage may vary, so you may want to get one.

# My Experiences So Far

This has been a wonderful educational experience so far.  But my goal was to have a useful kubernetes cluster running at home, and whether this is possible is still a question.

## Issues

1.  The upgrade experience is not polished / stable and leaves a lot to be desired.

	At the time of this writing, the latest Kubernetes is v1.10.0.  I started with v1.9.6 and attempted an upgrade.  There were a number of issues making it complex, and the risk of bringing down the cluster is high.

2.  Shared Storage is not included.

	To take advantage of the large amount of disk space that these units can provide, I intend to use glusterfs to provide shared storage across the nodes.  That is a separate project, and I'm not sure how stable it is.

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

# Hardware

My configuration includes:

1. (3x) Odroid HC2 units with power supplies and power plugs suitable for your region. [ameridroid.com](https://ameridroid.com/products/odroid-hc2)
2. (3x) [Sandisk Ultra 32GB Micro SDHC UHS-I Card with Adapter - 98MB/s U1 A1 - SDSQUAR-032G-GN6MA](https://www.amazon.com/gp/product/B073JWXGNT/ref=oh_aui_detailpage_o06_s01?ie=UTF8&psc=1)
3. (3x) [WD Red 4TB NAS Hard Disk Drive - 5400 RPM Class SATA 6Gb/s 64MB Cache 3.5 Inch - WD40EFRX](https://www.newegg.com/Product/Product.aspx?Item=N82E16822236599)

Note: Before purchasing the SDHC cards that I did, you may want to check out [Raspberry Pi microSD card performance comparison - 2018](https://www.jeffgeerling.com/blog/2018/raspberry-pi-microsd-card-performance-comparison-2018).

## Hardware Considerations

1.  Master Isolation: Your master node will not schedule pods for itself by default.  This is for security reasons, according to kubernetes.io.  They have instructions to override this if desired.  I'm not sure yet if this will impact using it as a storage node.

2.  You may want to purchase a USB-UART with your Odroids to allow console access.  I did not need one, but I wished I had purchased one early on.  You may be stuck without one if you run into boot issues.

3.  I purchased a UPS system for my router and ODROIDs for stability sake.

# Install Armbian

I used Armbian because:
1.  It is Debian based, which seems compatible with the instructions provided @ kubernetes.io
2.  It appears to be well maintained.

Both of these statements are just speculation on my part.

1.  Download an armbian image
2.  Install armbian on your micro SD cards - TODO
3.  First boot - TODO
4.  Generating unique machine-id for each node.

	On each node, run the following

	```sh
	rm /etc/machine-id
	```

	... then reboot.

5.  Get system updates. - TODO

6.  Setup /etc/hosts on each node - TODO

# Install Kubernetes

TODO

# Install Cri-Tools

TODO

# Initialize Cluster

TODO

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
