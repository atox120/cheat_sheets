# Raspberry Pi

How to set up a Raspberry pi as a remote linux server.

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

3. Optional - deleting a user

If we wish to delete a user, it an be done with the following command:

```bash
sudo deluser --remove-home -f username --remove-home
```
Again, the *username* here hsould be substituted with the account that one wishes to delete. The --remove-home flag is an optional flag which removes the home directory of that given user. Note make sure you have have sudo permissions by running

```bash
sudo su
```

Similarly, you may have issues with processes running in that users account. YOu can halt these processes with

```bash
sudo killall -u username
```

The instructions for the above were taken from this [link](https://linuxize.com/post/how-to-add-and-delete-users-on-ubuntu-20-04/)







