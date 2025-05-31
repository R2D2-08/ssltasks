# ssltasks
**Documentation for the SSL admins' induction tasks:**
## Task 1:
Configured a Standard B1 (1 vcpu, 1 GiB memory) in Azure. The operating system I chose was Linux (Ubuntu 22.04). The IPv4 public IP assigned for this instance is 74.224.124.103.

Commands used to update the system packages:
```bash
sudo apt update -y
sudo apt upgrade -y
```
### To set up unattended upgrades: 
- After ensuring that i have the ```unattended-upgrades``` package installed on my machine. I modified ```/etc/apt/apt.conf.d/20auto-upgrades``` to enable automatic upgrades.
  
## Task 2:
Most of the changes made were in the sshd_config file. This is where most of the ssh daemon's configuration lives in.
Disable root login by ```PermitRootLogin no```
Disable password-based authentication by ```PasswordAuthentication no```
Enable and configure public key authentication by 

### Setting up fail2ban:
- After cloning fail2ban, to set up our "jail" we edit the fail2ban configuration file (```/etc/fail2ban/jail.local```)
- This is where we enable the jail under ```[sshd]```.
 
## Task 3: 
- Configured UFW to deny all incoming traffic except for SSH, HTTP, HTTTPS and SSH on port 2222.
- sudo ufw default deny incoming, sudo ufw default allow outgoing, sudo ufw allow http/tcp https/tcp ssh/tcp 2222/tcp
  
### Testing if port 222 is open to accepting SSH requests:
```bash
ssh -vvv -i ~/.ssh/id_rsa.pem -p 2222 omar@74.224.124.103
```
- - The log would simply halt with the following log:
```bash
OpenSSH_9.9p1, OpenSSL 3.2.3 3 Sep 2024
debug1: Reading configuration data /etc/ssh/ssh_config
debug2: resolve_canonicalize: hostname 74.224.124.103 is address
debug3: expanded UserKnownHostsFile '~/.ssh/known_hosts' -> '/c/Users/Omarn/.ssh/known_hosts'
debug3: expanded UserKnownHostsFile '~/.ssh/known_hosts2' -> '/c/Users/Omarn/.ssh/known_hosts2'
debug3: channel_clear_timeouts: clearing
debug3: ssh_connect_direct: entering
debug1: Connecting to 74.224.124.103 [74.224.124.103] port 2222.
debug3: set_sock_tos: set socket 4 IP_TOS 0x48
```
- - Tried SSH-ing into the server using an open session in the server via port 2222. This worked.
```bash
ssh -i ~/id_rsa.pem -p 2222 omar@localhost
```
- - The above leads me to believe that the ISP being used by the client has blocked connections bound for a non-default port.


## Task 4:
Created the users and  
Commands used:
```bash
sudo useradd thisuser
sudo usermod -aG thisgroup thisuser
```
Cron job to backup the user directories.

## Task 5:
- Configured nginx as a reverse proxy that redirects http/https requests from the public internet to dedicated servers/processes.
- This involved writing a configuration file in ```/etc/nginx/sites-available``` and making a link for the same file in ```/etc/nginx/sites-enabled```.
- A strict CSP is written in these configuration files to mitigate XSS attacks. 

## Task 6:
- When running the security script ```sudo mysql_secure_installation``` after installing mariadb, an option allows you to disable remote login with a simple yes or no.
- The backups for the database are set up by writing a script and regularly executing it using the crontab. The backups are stored in ```/var/backups/db```
  
## Task 7:
Installed wireguard on the client and server. And generating the public keys in both the client and server, for the server, write the VPN configuration in ```/etc/wireguard/wg0.conf```. We add the public keys of clients under the [Peer] header.

## Task 8:
- Once docker is set up, after installation, the Dockerfile is used to build a container that will be proxied http traffic from the server to the container which inturn will be forwarded to the container hosting the portfolio. 
- 

## Task 9:


## Task 1:
