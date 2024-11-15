﻿# ACIT-2420-Assignment-1

**Creating a Remote server with Digital Ocean and Arch Linux**
## Introduction
In this tutorial, We will provide the steps on how to set up a remote server, known as a **droplet**, on **DigitalOcean** using **Arch Linux**. Using a remote server allows users to manage resources and perform specific tasks without the need of physical hardware. Arch Linux is known for its simplicity and customability, making it a good choice for users who want to build a system specific to their needs. By the end of this tutorial, you'll gain the basic skills and knowledge in order to deploy and manage your own server.

## Table of Contents:
- Create SSH keys
- Add to DigitalOcean account
- Download and add a custom Arch Linux image
- Create a Droplet running Arch Linux
- Connect to the droplet from a local machine using SSH
- Use a cloud-init configuration file to automate initial setup tasks
- Conclusion

**Before we get started users will need:**

-  A DigitalOcean account 

- A terminal application 

## Step 1: **Creating an SSH key on a local machine.**

**What are SSH keys?**

In simple terms, SSH (Secure Shell) keys resemble usernames and passwords, granting access and deciding who controls what. They are used to authenticate and establish an encrypted communication channel between a client and remote machine over the internet. Using an SSH key instead of a password reduces the risk of brute-force attacks on a server.

**Public Key:** is available for anyone to use

**Private Key:** is kept secret by the owner

**Create an SSH key pair:**

**1.** Open windows power shell, type this command:

    ssh-keygen -t ed25519 -f C:\Users\your-user-name\.ssh\do-key -C "youremail@email.com"

 - Replace your-user-name with your personal username
 - Replace "youremail@email.com" with your personal email
 - Replace do-key with a name for your key
<br />
<br />

 - **-t:** type, this type is used for encryption
 - **-f:** filename, specifies the filename and location
 - **-C:** comment, attaches a comment to a key 
<br />
<br />

**2.** You will be prompted to enter a passphrase. 

    This is optional but adds an extra layer of security. If you don’t want to set one, press Enter.

Your Terminal should look like this:

![Screenshot] <img src = "./Assets/my_key.png">

<br />
<br />

After you have created your key, you want to copy the contents in order to add it to your digital ocean account. 

**3.** We can do this by running this command in powershell: 

    Get-Content C:\Users\your-user-name\.ssh\do-key.pub | Set-Clipboard

 - Replace your-user-name with your personal username
 - Replace do-key with the name of your key

 - this will automatically copy the contents of the file to your clipboard
<br />
<br />

## Step 2: **Add SSH keys to your digital Ocean Account**

**Why Add SSH Keys?**

SSSH keys are used by DigitalOcean in order to access your droplet without a password. When you add the public key you just made to DigitalOcean, the server will trust your machine when connecting over SSH.

**Adding your SSH Key:**

**1.** In the DigitalOcean dashboard, Select Settings on the left, Then Select Security

**2.** Then you want to click the "Add SSH Key" button, and paste the contents you copied in the steps above in the "SSH Key content" box. Give your key a name, it can be anything

![Screenshot] <img src = "./Assets/new_key.png">

**3.** DigitalOcean will associate your SSH key with any droplets you create, it will automatically be copied to your droplet.


## Step 3: **Downloading a Custom Arch Linux Image and Creating a Droplet**

**Why a Custom Arch Linux Image?**

DigitalOcean offers images for many popular operating systems, but unfortunately they do not provide an image for Arch Linux. However, they provide an option to use your own custom image.

**Adding the Arch Linux Image to your Digital ocean account:**

**1.** Go to the link provided, and click the first Images button.

![Screenshot] <img src = "./Assets/press_image.png">

    https://gitlab.archlinux.org/archlinux/arch-boxes/-/packages/1545 

**2.** Under assets, there is options to download many different images. We want to download the image that has "cloudimg". and ".qcow2"

![Screenshot] <img src = "./Assets/arch_linux.png">

**3.** After download has been completed, open your digital ocean dashboard and select "+ New Project" on the left.

**4.** Fill out the required fields, you can name your project anything and add any description. Press create project on the bottom.

![Screenshot] <img src = "./Assets/create_new_project.png">

**5.** Next, you want to select the green "create" button on the top right, and choose **Droplet.** 

It should take you to this page:

![Screenshot] <img src = "./Assets/see_this_pg.png">

<br />
<br />

**Creating your Droplet:**

**1.** When choosing the region, we want to make sure this option is selected:

    "San Francisco, Datacenter 3, SFO3" 

![Screenshot] <img src = "./Assets/regions.png">

**2.** Under "choose an image" Select Custom Images, Add Image. 

**3.** Select the blue Upload Image button, and add the Arch Linux Image we downloaded in the first two steps.

![Screenshot] <img src = "./Assets/choose.png">

**4.** Next, Select the "Basic" Size

![Screenshot] <img src = "./Assets/basic.png">

**5.** Under CPU options, select Regular, and $6/mo

![Screenshot] <img src = "./Assets/cpu.png">

**6.** Under Choose Authentication Method, Select SSH key, And press "New SSH Key"

**7.** Add the key you added to your account

**8.** You can change the hostname to anything you would like it to be. 
- I would reccomend naming it something short.

**9.** leave the rest as is, and press "create Droplet."

<br />
<br />

## Step 5: **Connect to the droplet from a local machine using SSH**

**Why Use SSH?**

It is a Secure way to log into a remote server over a network 

**1.** Open your terminal and run the following command:

    ssh -i .ssh/do-key arch@your-droplets-ip-address

- Replace do-key with the name of your key
- Replace your-droplets-ip-address with the IP address from your droplet

- **-i:** identity file, path to private key file
- **arch:** your username

**2.** You'll be asked to verify the server's authenticity. type "Yes"


## Step 6: **Use a cloud-init configuration file to automate initial setup tasks**
**What is cloud-init?**

Cloud-init is the leading method for initializing cloud instances across multiple distributions. It is widely supported by all major public cloud providers, as well as provisioning systems for private cloud setups and bare-metal installations.

**why do we use cloud-init?**

We use cloud-init in order to setup a server with some initial configuration

**1.** Open your terminal and run the following command to install veovim:

        sudo pacman -S neovim

It should look like this:

 ![Screenshot] <img src = "./Assets/sudo_pacman.png">

**2.** To create a cloud-init configure file, run this command next:

    nvim cloud-init.yml

**3.** Next, add the configuration below to the file

    #cloud config
    users:
        name: user-name #change me
        primary_group: group-name #change me
        groups: wheel
        shell: /bin/bash
        sudo: ['ALL=(ALL) NOPASSWD:ALL']
        ssh-authorized-keys:
       ssh-ed25519 ...

    packages:
        ripgrep
        rsync
        neovim
        fd
        less
        man-db
        bash-completion
        tmux

    disable_root: true


It should look like this:

![Screenshot] <img src = "./Assets/cloud_config.png">

**4.** To exit and save, press the **Esc** button on your keyboard, then enter 

    :wq

![Screenshot] <img src = "./Assets/done.png">

## Conclusion
**Congratulations!** You’ve successfully created a remote server using DigitalOcean, installed Arch Linux, and connected to it using SSH. You now have set up a remote environment, and you're using secure SSH authentication.

## References

Canonical Group. (n.d.). Introduction to cloud-init¶. introduction to cloud init. https://docs.cloud-init.io/en/latest/explanation/introduction.html 

Cloud infrastructure for developers. DigitalOcean. (n.d.). https://www.digitalocean.com/?utm_campaign=&utm_adgroup=&_keyword=digital+ocean&_device=c&_adposition=&utm_content=&utm_medium=&utm_source=google&gad_source=1&gclid=Cj0KCQjwr9m3BhDHARIsANut04b-AtKh8E12c0enKbWrol1bx8IkXwV8acfO_6cgzmuKvAXqPmYpDyAaAmP8EALw_wcB 

Introduction to cloud-init¶. arch linux. (n.d.). https://cloudinit.readthedocs.io/en/latest/explanation/introduction.html 

McNinch, N. (n.d.). 2420-notes/week-three.md · main · cit_2420 / 2420-notes-F24 · GITLAB. GitLab. https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-three.md 

