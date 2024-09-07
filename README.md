# server-up
bare minimum to setup your linux server from scratch

## Virtual Private Server
My choice was **Oracle Cloud Free Tier**. It provides free virtual private servers (VPS) that are enough for test and development or even small-to-mid traffic websites. 

- If you choose ARM architecture, you can get 4 vCPUs + 24 GB RAM for free (as of Sep 2024). 

Use SSH To log into the server.  
```bash
ssh -i my-private-ssh.key ubuntu@000.000.000.000
```
Replace `my-private-ssh.key` with the correct path and `000.000.000.000` with the right IP address. 
The default user account created by oracle is `ubuntu`. 
No need to create a new user if you're not using the `root` user. 

## Update the server
Update the package list
```bash
sudo apt update
```
See which ones need to be upgraded
```bash
apt list --upgradable
```
Upgrade them all
```bash
sudo apt upgrade -y
```

<!-- ## Create a new user
Create a new user with the `-m` options for a home directory
```bash
sudo useradd -m myname
```
Give sudo privileges to the user
```bash
sudo usermod -aG sudo myname
``` -->

## Firewall
Uncomplicated Firewall (UFW) provides a simple way to restrict traffic and protect your server. 
Make sure to allow OpenSSH, otherwise you would lose your connection! 
```bash 
sudo ufw allow 'OpenSSH'
sudo ufw enable
sudo ufw status
```
**Note**: You would need to allow your HTTP web server in the firewall list as well. 
Say you install `Nginx`, then 
```bash
sudo ufw allow `Nginx Full`
```

## Essential software
Make sure you have the following software installed:
```bash
sudo apt install -y curl git
```


