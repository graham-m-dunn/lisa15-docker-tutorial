# Docker tutorial packer and Vagrant templates

## What is this

Blah blah

## Acknowledgements

The packer setup is largely based on the excellent work of Jacob Adams: [packer-qemu-templates](https://github.com/jakobadam/packer-qemu-templates)

## How to run packer

Libvirt:

```bash
packer build ubuntu14.04-dockertutorial-01.json
```

Virtualbox:

```bash
packer build vbox-ubuntu14.04-dockertutorial-01.json
```

VMWare Fusion (Mac OSX):

```bash
packer build vmware-ubuntu14.04-dockertutorial-01.json
```

As an option if you want to create a new user e.g. ubuntu use:

```bash
read -p 'Enter password: ' -s password
packer build -var 'user=ubuntu' -var "password=$password" ubuntu14.04-dockertutorial-01.json
```

## Add the box to vagrant

Packer will build a box that you can find under box/

Add it to your vagrant environment using:

```bash
vagrant box add box/dockertutorial-01.box --name "dockertutorial"
```

## Provision with vagrant

Libvirt:

Ensure you have the libvirt vagrant plugin installed (vagrant install vagrant-libvirt)

Run:

```bash
vagrant up --provider=libvirt --no-parallel
```

Virtualbox:

Run:

```bash
vagrant up --provider=virtualbox --no-parallel
```

VMWare Fusion:

Ensure you have purchased and installed the vmware fusion vagrant plugin installed.
Run:

```bash
export VAGRANT_DEFAULT_PROVIDER=vmware_fusion
vagrant up --no-parallel
```

## Build the helloweather docker image:

```bash
vagrant@dockertutorial-01:/vagrant$ ansible-playbook ansible_build_deploy/build_helloweather.yml 
```

## Run the helloweather application:

```bash
vagrant@dockertutorial-01:/vagrant$ ansible-playbook ansible_build_deploy/deploy_helloweather.yml 
```

This will deploy the helloweather app, by default, on the second vm, dockertutorial-02

## Visit the app via haproxy

Visit: <http://192.168.123.140:10000>

All helloweather containers automatically get configured as haproxy backends via consul-template.
haproxy configuration can be found under `/etc/haproxy/haproxy.cfg`

## Optional: run more helloweather app containers in different vms

You may explicitly specify the vm you wish your container to land on by overriding the variable target_shipyard_engine . For example to get the helloweather app running on the third vm you'd run:

```bash
vagrant@dockertutorial-01:/vagrant$ ansible-playbook ansible_build_deploy/deploy_helloweather.yml -e "target_shipyard_engine=dockertutorial-03"
```

## Preconfigured IP addresses:

| Vagrant VM name   |      IP Address |
| ---------------   | --------------- |
| dockertutorial-01 | 192.168.123.140 |
| dockertutorial-02 | 192.168.123.141 |
| dockertutorial-03 | 192.168.123.142 |

## Software installed:

Check scripts/packages.sh:

- ansible
- docker
- url
- emacs24-nox
- htop
- nmon
- slurm
- tcpdump
- unzip
- vim-nox

### vm specific software:

| Vagrant VM name   | Software installed        |
| ----------------- | ------------------------- |
| dockertutorial-01 | docker                    |
|                   | shipyard                  |
|                   | packer                    |
|                   | ansible                   |
|                   | docker private registry:2 |
|                   | consul server agent       |
|                   | registrator               |
|                   | consul-template           |

| Vagrant VM name   | Software installed        |
| ----------------- | ------------------------- |
| dockertutorial-02 | docker                    |
|                   | packer                    |
|                   | ansible                   |
|                   | consul server agent       |
|                   | registrator               |
|                   | consul-template           |

| Vagrant VM name   | Software installed        |
| ----------------- | ------------------------- |
| dockertutorial-03 | docker                    |
|                   | packer                    |
|                   | ansible                   |
|                   | consul server agent       |
|                   | registrator               |
|                   | consul-template           |
