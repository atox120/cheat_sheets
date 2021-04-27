# Raspberry Pi

How to set up a Raspberry pi as a remote linux server. Disclaimer - this content I have collected from various sites on the web, I have provided references where I have done so. 

## Quicklinks

[1. Prerequisites](##1-prerequisites)
[2. OS install](##2-os-install)

## 1. Prerequisites

1.  Raspberry Pi 4 + neccesary peripherals. 
2.  Micro SD card 

Herein we use a Raspberry Pi (RPI) 4 with 8GB RAM on a 64-bit architecture. We assume you have bought the neccesary peripherals including keyboard and mouse etc. 
Furthermore, you will need a micro SD card onto which you will load your boot volumne. 

## 2. OS Install

We will load a Ubuntu 20.04 LTS Focal server distribution onto our device. The steps are as follows:

1. Download and install the raspberry pi imager from this [link](https://www.raspberrypi.org/software/)
2. Run the imager software. 
3. Choose the Ubuntu 20.04 LTS server edition. 
4. Insert the micro SD card and select this volume as your target volumne. 
5. Start the disk write process - note this will permanantly delete any existing ffiles on the sd card. 

A more complete instruction set is outlined in this [link](https://www.youtube.com/watch?v=ntaXWS8Lk34)

## 3. Setting up Ubuntu

Load the SD card into the Raspberry PI and turn it on. When you first get started, you will be prompted to enter a password for the default user 'ubuntu'. The passwod is 'ubuntu'. 

### 3.1 Setting up a user account

To set up an additional user account, follow the below instructions. 

1. Create a new user:

Here the *username* should be substtituted for the actual username you wish to use. 
```bash
sudo adduser username
```

Next you will be asked a series of questions - only the password is mandatory. If you do not wish to fill out these additional fields, leave it blank and press enter and it will move onto the next prompt untl complete. Type *Y* to confirm the details. 

2. Making a user part of the sudo group:

You can add a user to the sudo user group by performing the following command:

```bash
sudo usermod -aG sudo username
```
Again, *username* should be substituted by the username that you wish to perform the action on. 

### 3.2 Optional - deleting a user

If we wish to delete a user, it an be done with the following command:

```bash
sudo deluser --remove-home -f username
```
Again, the *username* here hsould be substituted with the account that one wishes to delete. The --remove-home flag is an optional flag which removes the home directory of that given user. Note make sure you have have sudo permissions by running

```bash
sudo su
```

Similarly, you may have issues with processes running in that users account. YOu can halt these processes with

```bash
sudo killall -u username
```

### 3.3 Optional - changing the host name
 The hostname is by default 'ubuntu'. This can be changed by modiffying the entry in the hostname file. In this example, we use vi. 
 
 ```bash
 sudo vi /etc/hostname
 ```
 
In this file you will see the current hostname. For vi, type *i* to edit and change the hostname to whatever you desire. Type *wq* to write and quit. Next you need to reboot your machine for the changes to take effect.

```bash
sudo reboot
```


The instructions for the above were taken in part from this [link](https://linuxize.com/post/how-to-add-and-delete-users-on-ubuntu-20-04/)

## 4. Setting up SSH on Ubuntu

In this section, we sett up remote access via ssh on Ubuntu. This will allow remote users to connect securely via ssh to your machine. For this we will use the utility openssh-server.

### 4.1 Install setting up openssh-server

Run the following commands to update apt and install the package. Type *y* to proceed at the prompts. 

```bash
sudo apt update
sudo apt install openssh-server
```

The service will start running automatically. You can check the status by running the command

```bash
sudo systemctl status ssh
```

Next, we need to ensure that the firewall is enabled to allow SSH traffic.

```bash
sudo ufw allow ssh
```

With this set up, you should be able to connect via ssh to your instance. On a seperate computer/instance, start up a terminal (mac and linux users only, Windows will need to use a tool like [PuTTy](https://www.putty.org/). Note in he following *username* is the username associated with the account created on your RPI, and the ip address is the IP address of your RPI. 

```bash
ssh username@reaspberry_pi_ip_addresss
```
You can find the ip address of your RPI using the following command:

```bash
ip a
```

Upon successful connection, you will be prompted for your password. 

## 5 Static IP address Assignment

If you need to assign a static IP, then follow these steps.

- 5.1 Check your ethernet interface:

Run the following command and check for a ethernet interface (if using):

```bash
ip a
```

In our case, we found an interface called *eth0*

- 5.2 Create a netplan configuration file:

Create the file ```/etc/netplan/99_config.yaml``` with the following contents:

```yml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - <your_ip_address/preffix_length>
      gateway4: <your_router_gateway>
      nameservers:
          search: [Your_Name_ServerName]
          addresses: [Your_Name_Server_IP]
```

- 5.3 Apply the settings

```bash
sudo netplan apply
```

Other Useful resources:

- Digital Ocean article on how to configure ufw to allow ssh. [link](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-20-04)
- Networking guides from Ubuntu. [link](https://ubuntu.com/server/docs/network-configuration)
- Troubleshooting Ubuntu server SSH connections 1. [link](https://askubuntu.com/questions/181723/connecting-to-ubuntu-server-via-ssh-externally) 
- Troubleshooting Ubuntu server SSH connections 2. [link](https://askubuntu.com/questions/921909/step-by-step-enable-remote-login-to-home-ubuntu-machine) 
- Checking SSH on Linux. [link](https://cplusprogrammer.wordpress.com/2016/10/17/how-to-check-if-ssh-is-running-on-linux/)
- Setting up Apache web Server [link](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-20-04)


To do:
### Adding extra security - SSH keys.
### Mapping volumes
### Install python and jupyter
### X. Install docker/docker-compose. 

Here we outline how to install docker. Docker is compatible on the ARM64 architecture and Ubuntu Focal 20.04, so we are good to go. We follow the instructions from [this](https://docs.docker.com/engine/install/ubuntu/) guide for installs from a repository, making sure to run the version for *ARM64* architectures. 

1. Update apt and install required packages to use the repository over HTTPS.

```bash
sudo apt-get update

sudo apt-get install \
 apt-transport-https \
 ca-certificates \
 curl \
 gnupg \
 lsb-release
```

2. Add dockers offficial GPG key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

3. Add the repository

Here we add the stable repository, but there are options for the nightly and test repository. If you are interested in these, check out the [link](https://docs.docker.com/engine/install/ubuntu/)

```bash
echo \
  "deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
``` 

4. Install Docker engine

Here we update our apt package index and install the latest version of the Docker Engine and containerd. It is also possible to install a specific version, in which case see the instructions on the following [link](https://docs.docker.com/engine/install/ubuntu/).

``` 
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

5. Verify the install

```bash
#Run container image 'hello-world'
docker run hello-world
#Check running containers
docker ps -a
#remove 'hello-world' container
docker rm -f hello_world
```







