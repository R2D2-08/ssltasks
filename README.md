# ssltasks
**Documentation for the SSL admins' induction tasks:**
## Task 1:
Configured a Standard B1 (1 vcpu, 1 GiB memory) in Azure. The operating system I chose was Linux (Ubuntu 22.04). The IPv4 public IP assigned for this instance is 74.224.124.103.

### To set up unattended upgrades: 
- After ensuring that i have the ```unattended-upgrades``` package installed on my machine. I modified ```/etc/apt/apt.conf.d/20auto-upgrades``` to enable automatic upgrades.
  
## Task 2:
Most of the changes made were in the sshd_config file. This is where the ssh daemon's configuration lives in.
- Disabling root login, password-based authentication and enabling public key auth is all done in the above specified file (```/etc/sshd/sshd_config```).

### Setting up fail2ban:
- After cloning fail2ban, to set up our "jail" we edit the fail2ban configuration file (```/etc/fail2ban/jail.local```)
- This is where we enable the jail under ```[sshd]```.
 
## Task 3: 
- Configured UFW to deny all incoming traffic except for SSH, HTTP, HTTTPS and SSH on port 2222.
- To deny incoming traffic, allow outgoing traffic, set up my own firewall: ```sudo ufw default deny incoming; sudo ufw default allow outgoing; sudo ufw allow http/tcp https/tcp ssh/tcp 2222/tcp```
  
### Testing if port 2222 is open to accepting SSH requests:
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
Created the users and change the permissions as required.
- Write a backup script to backup the exam_* users' directories evrey day. Use a cron job for the regularly timed backups.

## Task 5:
To Configure nginx as a reverse proxy that redirects http/https requests from the public internet to dedicated servers/processes.
- This involved writing a configuration file in ```/etc/nginx/sites-available``` and making a link for the same file in ```/etc/nginx/sites-enabled```.
- This file specifies the redirection logic. So, whenever a http/https request is made to the server (dependent on what port the server is open to listen on), the request is forwarded to the dedicated process based on the nginx configuration present in ```/etc/nginx/sites-enabled```.
- Defined a strict CSP in the configuration file to mitigate XSS attacks. 

## Task 6:
- When running the security script ```sudo mysql_secure_installation``` after installing mariadb, an option allows you to disable remote login with a simple yes or no, so I used that to disable remote login.
- The backups for the database are stored using a cron job that executes a script regularly. The backups are stored in ```/var/backups/db```
  
## Task 7:
- Firstly, I installed wireguard on both the client and server (I used my personal laptop as the client).
- Generated the public keys in both the client and server.
- Then for the server, I wrote the VPN configuration in ```/etc/wireguard/wg0.conf```.
- To allow traffic to be tunnelled from the client to the server, they must be aware of each other, hence, I added the public keys of clients under the [Peer] header and the public key of the server in the client's configuration file.
- For the client(s) write the configuration in the same directory (```client.conf```).  There I specified the public IP address of the server along with the server's wireguard public key as mentioned above.

## Task 8:
- Once docker is set up, after installation, the Dockerfile is used to build a container that will be proxied http traffic from the server which inturn will be forwarded to the localhost, which is hosting the portfolio (I'm not entirely sure if what I just stated is entirely true in terms of the terminology). 

## Task 9:

