# Laravel Homestead in Ubuntu Desktop 16.04 or Higher

Laravel strives to make the entire PHP development experience delightful, including your local development environment. Vagrant provides a simple, elegant way to manage and provision Virtual Machines.

Laravel Homestead is an official, pre-packaged Vagrant box that provides you a wonderful development environment without requiring you to install PHP, a web server, and any other server software on your local machine. No more worrying about messing up your operating system! Vagrant boxes are completely disposable. If something goes wrong, you can destroy and re-create the box in minutes!

## Requirements

- [VirtualBox](https://www.virtualbox.org/), [VMWare](https://my.vmware.com/) or [Parallels](https://www.parallels.com/)
- [Vagrant](https://www.vagrantup.com/)

### Installing The Homestead Vagrant Box

Once VirtualBox / VMware and Vagrant have been installed, you should add the  laravel/homestead box to your Vagrant installation using the following command in your terminal. It will take a few minutes to download the box, depending on your Internet connection speed:

```console
$ vagrant box add laravel/homestead
```

### Installing Homestead

You may install Homestead by simply cloning the repository. Consider cloning the repository into a Homestead folder within your "home" directory, as the Homestead box will serve as the host to all of your Laravel projects:

```console
$ cd
$ git clone https://github.com/laravel/homestead.git Homestead
$ cd Homestead
```

Once you have cloned the Homestead repository, run the bash init.sh command from the Homestead directory to create the Homestead.yaml configuration file. The Homestead.yaml file will be placed in the Homestead directory:

```console
$ bash init.sh
```

> **Note:** To change init.sh:

```console
$ sudo nano init.sh
```

```shell
#!/usr/bin/env bash

homesteadRoot=~/.homestead

mkdir -p "$homesteadRoot"

cp -i src/stubs/Homestead.yaml "$homesteadRoot/Homestead.yaml"
cp -i src/stubs/after.sh "$homesteadRoot/after.sh"
cp -i src/stubs/aliases "$homesteadRoot/aliases"

echo "Homestead initialized!"
```

### Configuring Homestead

The folders property of the Homestead.yaml file lists all of the folders you wish to share with your Homestead environment. As files within these folders are changed, they will be kept in sync between your local machine and the Homestead environment. You may configure as many shared folders as necessary:

```yaml
name: homestead-<project>
ip: "192.168.10.10"
memory: 2048
cpus: 1
provider: virtualbox

authorize: ~/.ssh/id_rsa.pub

keys:
    - ~/.ssh/id_rsa

folders:
    - map: ~/path/to/project/<project>
      to: /path/to/project/<project>

sites:
    - map: <project-domain>
      to: /path/to/project/<project>/public

databases:
    - homestead

# blackfire:
#     - id: foo
#       token: bar
#       client-id: foo
#       client-token: bar

# ports:
#     - send: 50000
#       to: 5000
#     - send: 7777
#       to: 777
#       protocol: udp
```

### The Hosts File

You must add the "domains" for your Nginx sites to the hosts file on your machine. The hosts file will redirect requests for your Homestead sites into your Homestead machine. On Linux, this file is located at /etc/hosts. The lines you add to this file will look like the following:
```
192.168.10.10  <project-domain>
```

### Launching The Vagrant Box

Once you have edited the Homestead.yaml to your liking, run these command from your Homestead directory:
```console
$ vagrant up
```

Vagrant will boot the virtual machine and automatically configure your shared folders and Nginx sites.

For more informaton about vagrant commands, please refer [https://www.vagrantup.com/docs/index.html](https://www.vagrantup.com/docs/index.html).

### Stopping The Vagrant Box

```console
$ vagrant halt
```

### Creating Project Using Vagrant SSH

```console
$ vagrant ssh
$ cd /path/to/project
```

#### Create via Laravel Installer:

```console
$ laravel new <project>
```

#### Create via Composer:

```console
$ composer create-project --prefer-dist laravel/laravel <project>
```

> **Note**: That when you encounter this error when running 'vagrant up' command:

> The home directory you specified is not accessible. The home directory that Vagrant uses must be both readable and writable.

> You specified: `/home/<user>/.vagrant.d`

> All you have to do is change the owner of the .vagrant.d:

```console
$ sudo chown -R <user> /home/<user>/.vagrant.d
```

### Redirect http to https

Copy `ssl.sh` into `/path/to/Homestead/scripts/site-types`

Run Homestead

```console
$ cd /path/to/Homestead
$ vagrant up
```

Enter Homestead via SSH

```console
$ vagrant ssh
```

Copy `ca.homestead.homestead.crt` from laravel/homestead box into /path/to/project

```console
$ sudo cp /etc/nginx/ssl/ca.homestead.homestead.crt /home/vagrant/path/to/project/<project>/ca.homestead.homestead.crt
```

Import `ca.homestead.homestead.crt` into browser (Mozilla, Chrome, Safari and etc.)
Edit Homestead.yaml

```
...
sites:
    - map: ~/path/to/project/<project>
      to: /path/to/project/<project>
      type: "ssl"
...
```

### Add extra provisioning

Modify `~/Homestead/after.sh`.
```bash
echo "Restart services..."
sudo service nginx restart
```