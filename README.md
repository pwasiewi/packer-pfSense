## pfSense packer vagrant box
pfSense vagrant box file creation VirtualBox with Packer

## Download ISO manually
As packer is not able to download gzipped ISO files directly, you have to download it manually and extract it to e.g. 

wget http://mirror.transip.net/pfsense/downloads/pfSense-CE-2.4.2-RELEASE-amd64.iso.gz -O /mnt/tmp/IMAGES/pfSense-CE-2.4.2-RELEASE-amd64.iso.gz && cd /mnt/tmp/IMAGES && gzip -d pfSense-CE-2.4.2-RELEASE-amd64.iso.gz 

Then customize the pfsense-network.json file so packer could find the ISO file locally.

https://www.pfsense.org/download/

## Create vagrant box
To build images, simply run.

```
git clone https://github.com/pwasiewi/packer-pfSense
cd packer-pfSense
export VAGRANT_CLOUD_TOKEN=the token string taken from Vagrant https://app.vagrantup.com/settings/security
#change 42n4 in a template json to your vagrant account name!!
#It uploads the box version based on version number from template.json e.g. "version": "1.8"
packer build -only=virtualbox-iso pfsense-2.4.2-network.json
```

All packages can be taken from http://pkg.freebsd.org/freebsd:11:x86:64/latest/All/

Pfsense is based on freebsd 11.1 (2.4.2/3 - freebsd 11 release2)

## Run Vagrant box (you can omit previous points and use my 42n4/pfsense box)

```
#pull box from https://app.vagrantup.com (pfsensebeta for testing new releases)
vagrant box add 42n4/pfsense
or
vagrant box add 42n4/pfsensebeta
#get Vagrantfile to an empty directory:
wget https://raw.githubusercontent.com/pwasiewi/packer-pfSense/master/Vagrantfile.2routers
ln -sfn Vagrantfile.2routers Vagrantfile (for 42n4/pfsense)
or 
ln -sfn Vagrantfile.2routers.new Vagrantfile (for 42n4/pfsensebeta)
#NIC config in Vagrantfile, change to your settings: I have created boxes in 192.168.0.0/24 network
vagrant destroy -f; vagrant up
```

### In M$Windows: https://www.sitepoint.com/getting-started-vagrant-windows/ - you use putty after converting with puttygen a vagrant openssh key to a putty key

![Screen](https://github.com/pwasiewi/packer-proxmox/blob/master/picture/win10connectionwithvagranthost.png)

## Test ssh
```
ssh -vvv -C -A -X vagrant@127.0.0.1 -p2222
ssh -vvv fake@getfetch.com

```


## Packer Debug

```shell
# Debug for bash etc..
export PACKER_LOG="DEBUG"

# unDebug for bash etc..
export PACKER_LOG=""
```


## config.xml
---

* CPU: `2` Core
* Memory: `2048` MB
* DISK Size: `8192` GB
* ssh enable (LAN Network)
* Time Zone: `Europe/Warsaw`
* Add package
  * sudo
  * virtualbox-ose-additions-noX11 with all dependencies


### OVA information
---

|             |                                              |
|:-           |:-                                            |
| Product     | pfSense                                      |
| Product URL | https://www.pfsense.org                      |
| Version     | 2.4.2                                        |
| Vendor      | @pwasiewi (@FoxBoxsnet fork)                 |
| vendor URL  | https://github.com/pwasiewi                  |
| Repository  | https://github.com/pwasiewi/packer-pfSense   |


### NIC
---

| json    | NET  | NIC | MODE    |
|:-       |:-    |:-   |:-       |
| network | WAN  | em1 | bridged |
| network | LAN  | em2 | intnet  |
| network |      | em3 | intnet  |
| network | NAT  | em0 | nat     |


### adduser
---
|           |         |
|:-         |:-       |
| username  | vagrant |
| password  | vagrant |
| groupname | admins  |

LICENCE APACHE
@taken from https://github.com/FoxBoxsnet/packer-pfSense and changed a little
