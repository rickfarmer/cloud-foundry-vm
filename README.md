Cloud Foundry VM
================

The [Cloud Foundry][1] VM is a simple way to spin up a complete development instance quickly in a self-contained VM using [the tao of Vagrant][2].  No need to install the complete toolchain and configure your local environment with the layers of tooling needed to run Cloud Foundry (CF).  Instead the Cloud Foundry VM automatically provisions and configures a VM containing everything you need to run Cloud Foundry using [Pivotal CF][3] (PCF), the enterprise version of Cloud Foundry.

The strategy is to:
1. Support the Pivotal CF minimum of 48Gb RAM with 16 vCPUs clustered over virtualized ESXi host instances via Vagrant. Preferably 64Gb RAM with 24 vCPUs on at least two ESXi hosts (to enable vMotion for HA).  
2. Automatically configure the challenging parts of Cloud Foundry — network and datastore.  In particular, share these resources among the virtualized ESXi hosts.
3. What?!?  We are turning a laptop sized system into a full-fledged vSphere and Pivotal CF environment… not possible!  Maybe not… but
	- “CPU core over-subscription is not something we worry about. vSphere 5.5′s Virtual CPU limit is 32 Virtual CPUs per core, which means that a 4-core Mac Pro could support as many as 128 Virtual CPUs. Cloud Foundry’s Engineering Team’s servers are often over-subscribed by a factor of more than 20:1 (i.e. as many as 240 cores allocated, but only 12 physical cores available).”  **Please see the excellent [Brian Cunnie IaaS articles][4] for more information.**
		- RAM is the hard limit.  Cloud Foundry breathes RAM.  At a minimum we want to start with a physical machine that has 16Gb of physical RAM, take 12Gb of that per ESXi host instance and spin-up 4 of them for a total of 48Gb of virtualized, over-subscribed RAM made available for CF  

**Special thanks go to [Doug MacEachern][5] at [VMware][6] for putting together an awesome set of tooling in the form of his example [ESXi Packer template][7] and [ESXi Vagrant plug-in][8] projects.**

# Requirements

_Currently, the instructions for using the Cloud Foundry VM is Mac OS X centric.  If running on a Windows or Linux machine please consider contributing the information back to this repository.  Thanks._

## Hardware

- **Bare** minimum (i.e. you may not be able to install the Elastic Runtime for instance)
	- 2 cores
	- 16 Gb RAM
	- 1Tb
	- Gigabit Ethernet
- **Better** minimum
	- 6 cores or more
	 - 64Gb RAM or greater
	- 1Tb Disk or greater
	- Gigabit Ethernet or greater

## Software

- [VMware Fusion 6.x][9] or greater
- [Vagrant][10]
- [Vagrant VMware Fusion plugin][11]

_Note: The embedded ESXi box contains the publicly available trial version which comes with a 60 day evaluation license — **you will want to get a full license at some point.  VMware generously offers a [free ESXi license][12] for a single machine install for development and evaluation**_

# Consider the Environment aka “What are we building?”

We are building a single VM with the following specifications (which we ultimately clone and cluster together).  

- The vSphere ESXi 5.5u1 hypervisor
	- 12Gb RAM (recommend 48Gb or greater)
	- 4 vCPUs (recommend 12 vCPUs or greater)
	- 750Gb datastore (recommend 1TB or more)
	- 10.9.8.1/24 network 
- vCenter
- Pivotal CF (Cloud Foundry)

_Note: The base VM can be “tuned” if you have more resources available on your machine to get it up to the recommended specs via the `Vagrantfile`)_


# tl;dr - Install and Run on Mac OS X

1. Be sure you have installed, 
	- [VMware Fusion 6.x][13]
	- [Vagrant][14]
	- [Vagrant VMware Fusion plugin][15] (paid plugin)

`$ vagrant plugin license vagrant-vmware-fusion license.lic`
 
- The Vagrant ESXi plugin, e.g.

`$ gem build vagrant-esxi.gemspec`	
`$ vagrant plugin install vagrant-esxi`

2. Clone the repo to your local machine

`$ git clone https://github.com/rickfarmer/cloud-foundry-vm.git`

3. From the `cloud-foundry-vm` directory, launch the EXSi VM using Vagrant

`$ cd cloud-foundry-vm`
`$ vagrant up`

or, if you have **challenges** (_cough_ er, network issues) use,

`$ VAGRANT_LOG=info vagrant up`

4. Repeat and cluster…  **coming soon**


# Getting started with Cloud Foundry

## Install the the Cloud Foundry CLI ([Homebrew][16] makes it easy)

1. Install Homebrew

`$ ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"`

2. Install the Cloud Foundry CLI

`$ brew update`
`$ brew tap homebrew/binary`
`$ brew install caskroom/cask/brew-cask`
`$ brew cask install cloudfoundry-cli`

[1]:	http://cloudfoundry.org/ "Cloud Foundry"
[2]:	http://mitchellh.com/the-tao-of-vagrant "The Tao of Vagrant"
[3]:	http://www.pivotal.io/platform-as-a-service/pivotal-cf "Pivotal CF"
[4]:	http://pivotallabs.com/worlds-smallest-iaas-part-3-paas/?tag=cloudfoundry#cpu_cores "World's Smallest IaaS"
[5]:	https://github.com/dougm "Doug MacEachern"
[6]:	http://www.vmware.com "VMware"
[7]:	https://github.com/dougm/packer-esxi "ESXi Packer template"
[8]:	https://github.com/dougm/vagrant-esxi "ESXi Vagrant plug-in"
[9]:	http://www.vmware.com/products/fusion "VMware Fusion"
[10]:	http://docs.vagrantup.com/v2/installation/
[11]:	http://www.vagrantup.com/vmware
[12]:	https://my.vmware.com/web/vmware/evalcenter?p=free-esxi5&lp=default "Free ESXi License"
[13]:	http://www.vmware.com/products/fusion "VMware Fusion"
[14]:	http://docs.vagrantup.com/v2/installation/
[15]:	http://www.vagrantup.com/vmware
[16]:	http://brew.sh "Homebrew"