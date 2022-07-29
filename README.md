# Ubuntu Server 20.04 - cloud-init

This repo is for deploying a precreated proxmox cloud-init template (ubuntu @ vmid:9000).
I'm using this one e.g. for caching nexus repositories needed by air-gap environments. 

The file *`vars.tf`* is for defining basic variables.


## Terraforming:
You need a Proxmox api-token and saved it's values in *`token.export`*

On first use your have to init this repo via  
( File *`.terraform.lock.hcl`* doesn't exist )  
`terraform init`

In your current shell context you have to set API relevant variables.  
`source token.export`

Steps to deploy instances:  

create an execution plan
`terraform plan`  

apply the execution plan  
`terraform apply`  
you have to type *`yes`* to confirm the execution

if u're absolutely shure what u're doing, u're able to apply all steps in one  
`terraform apply -auto-approve`

You want to reject this whole deployment? U're able to do this with  
`terraform apply -destroy`

## How to create your own Ubuntu Desktop cloudimage

This description is made for Proxmox instances. Feel free to adopt to another virtualization ;-)  
First, create a new instance and make an standard Installation from an official ISO.
You have to define a temporary user for accessing the system during your setup session.

We don't need the cd/dvd device. So, remove it now.
By using nexus repositories, modify *`/etc/apt/sources.list`* with your flavour, first 

Install required services ...  
`apt install -y openssh-server cloud-init qemu-guest-agent`

Enable "Guest Agent" in your instance options as virtio device

Now, reboot the instance and log in via ssh.
Change into the root-context via `sudo su`

Run guest agent as persistent service  
`systemctl enable --now quemu-guest-agent.service`

In *`/etc/ssh/sshd_config`* modify following entries
```
PasswordAuthentication no
PermitRootLogin no
```

Remove the homedir of your temporary user. Why? Userdel doesn't work because of existing user-PIDs of your temporary user.  
Remove user relevant entries in `/etc/passwd` `/etc/shadow` and ALL associations in `/etc/group`.

Now, cutoff/stop (not shutdown) the instance.  

Last, add a "CloudInit Drive" in the same storage where your systemdrive resides.

Convert the instance as template and ... don't forget to create a backup :-)  
Finished!

Feel free to use this cloud-init template to fire up cloned instances as much as u like :-)

Hint: Never define a smaler OS-disk than the original. Proxmox is only be able to increase the Size!