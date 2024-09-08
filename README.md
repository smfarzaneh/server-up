# server-up
bare minimum to setup your linux server from scratch

## Virtual Private Server
My choice was **Oracle Cloud Free Tier**. It provides free virtual private servers (VPS) that are enough for test and development or even small-to-mid traffic websites. 

**Note**: If you choose ARM architecture, you can get 4 vCPUs + 24 GB RAM for free (as of Sep 2024). 

### Login
Use SSH To log into the server.  
```bash
ssh -i my-private-ssh.key ubuntu@000.000.000.000
```
Replace `my-private-ssh.key` with the correct path and `000.000.000.000` with the right IP address. 
The default user account created by oracle is `ubuntu`. 
No need to create a new user if you're not using the `root` user. 

## Update Linux
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

## Essential software
Make sure you have the following software installed:
```bash
sudo apt install -y curl git
```

## set up an HTTP server
I'm using Nginx as my http server. First install Nginx 
```bash
sudo apt install nginx
```
Then start the HTTP server
```bash
sudo systemctl start nginx
```
Check the status of the server
```bash
sudo systemctl status nginx
```
To check if the server is working use a browser on the client side and enter the IP address of your server.

**Note**: You would need to allow your HTTP web server in the firewall list as well. 
```bash
sudo ufw allow "Nginx Full"
```

### Oracle Ingress Rules
Nginx listens to port `80` by default. Make sure to release at least the `80` port so that incoming traffic can access Nginx. 
To do that 
- Go to `Virtual cloud network`
- click on your `subnet` 
- click on `Default security list`
- `Add ingress rules`
    * Source CIDR: `0.0.0.0/0`
    * IP Protocol: `TCP`
    * Description: `nginx`


### Ubuntu IP table
In addition to Oracle security list and the `ufw` you would need to update Ubuntu's own firewall to allow incoming connections on port `80`
```bash
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
sudo netfilter-persistent save
```
Note: This took me a lot of time to figure out. ChatGPT was bullshitting a lot. 
Check out this [StackOverflow](https://stackoverflow.com/questions/62326988/cant-access-oracle-cloud-always-free-compute-http-port) and this [Oracle documentation](https://docs.oracle.com/en-us/iaas/developer-tutorials/tutorials/apache-on-ubuntu/01oci-ubuntu-apache-summary.htm). 

## Database
Install PostgreSQL
```bash
sudo apt install postgresql postgresql-contrib
```
Check version and service 
```bash
psql --version
sudo systemctl status postgresql
```

## Security 
Server security is a vast topic but I'm using two methods to make the server more secure. 

### Brute-force attack prevention
Install `Fail2Ban` to prevent brute-force attack
```bash
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### HTTPS protocol 
Before doing so, you would need to have a domain name and have properly set it up with Nginx. 
Only then you can use `certbot` to get SSL certificates. 
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx
```

## Monitoring 
Use `htop` for monitoring the processes. 

