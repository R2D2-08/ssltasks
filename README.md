# ssltasks
**Documentation for the SSL admins' induction tasks:**
## Task 1:
Configured a Standard B1 (1 vcpu, 1 GiB memory) in Azure. The operating system I chose was Linux (Ubuntu 22.04). The IPv4 public IP assigned for this instance is 74.224.124.103.

### To set up unattended upgrades: 
- After ensuring that i have the ```unattended-upgrades``` package installed on my machine. I modified ```/etc/apt/apt.conf.d/20auto-upgrades``` to enable automatic upgrades.
- Since the above is for security packages, I additionally added a cronjob that updates and upgrades all the system packages everyday. [References](https://www.youtube.com/watch?v=2Btkx9toufg).

## Task 2:
Most of the changes made were in the sshd_config file. This is where the ssh daemon's configuration lives in.
- Disabling root login, password-based authentication and enabling public key auth is all done in the above specified file (```/etc/sshd/sshd_config```).
- I didn't restrict the SSH access to a range of IP addresses because my router sits behind CGNAT. 

### Setting up fail2ban:
- After cloning fail2ban, to configure the "jail", I edited the fail2ban configuration file (```/etc/fail2ban/jail.local```)
- This is where I enabled the jail under ```[sshd]``` with relevant options to set the options of the jail.
 
## Task 3: 
- Configured UFW to deny all incoming traffic except for SSH, HTTP, HTTTPS and SSH on port 2222.
- To deny incoming traffic, allow outgoing traffic, set up my own firewall: ```sudo ufw default deny incoming; sudo ufw default allow outgoing; sudo ufw allow http/tcp https/tcp ssh/tcp 2222/tcp```
- Even with UFW configued to allow certain traffic, the Azure network firewall required me to add rules on the Azure portal to allow traffic through port 2222 for non-default ssh, 51820 for wireguard traffic, 80 for http traffic, 443 for https traffic. 
- I read up a bit about IP-tables, mainly from this [article](https://medium.com/skilluped/what-is-iptables-and-how-to-use-it-781818422e52).

## Task 4:
Created the users and changed the permissions as required. Created a group ```auditors``` of which, examaudit is a part of. This is done to give examaudit read access to the home directories of the users with usernames: exam_1, exam_2 & exam_3.
- To enable quotas on the space used by these users, I faced a bit of complication as described below:
    - Firstly, I edited the ```/etc/fstab``` file and added ```userquota,grpquota``` to the line with the root filesystem.
    - I then remounted the root filesystem and performed a quotacheck but I kept encountering errors. Addtionally, the person using the computer in the video I was referring to implement disk quotas hadn't encountered any issues. [source](https://www.youtube.com/watch?v=blMuxCTTnvg)
    - Some of the errors that kept popping up were concerned with how I do not have the 'quota' kernel module installed or complied on my instance. 
    - The error turned out to be in my deprecated implementation of disk quotas. I found the implementation that worked for me [here](https://askubuntu.com/questions/575967/how-do-i-set-up-user-quotas-limits-on-the-file-system).
    - I then added a 256 MB, 1024 MB and 1024 soft limit on exam_1, exam_2 and exam_3 respectively, with the corresponding hard limits being approximately 10% more than the soft limits.
- Following that, I  wrote a simple backup script to backup the exam_* users' directories evrey day and automated that process using a cronjob.

## Task 5:
To Configure nginx as a reverse proxy that redirects http/https requests from the public internet to dedicated servers/processes.
- This involved writing a configuration file in ```/etc/nginx/sites-available``` and making a link for the same file in ```/etc/nginx/sites-enabled```. Once linked and having tested the configuration, followed up by reloading nginx, I was able to configure a basic reverse-proxy that redirected to the default nginx html page.
- The configuration written in the ```/etc/nginx/sites-available``` file defines the redirection logic (this is what I have assumed it does). So, whenever a http/https request is made to the server (dependent on what port the server is open to listen on), the request is forwarded to the dedicated process based on the nginx configuration present in the config file.
- With a need to upgrade to https and a moderate CSP (a very strict one wouldn't load the static html from different directories or smtg), relevant headers were added to the same configuration file.
- To run [app1](https://do.edvinbasil.com/ssl/app) and [issslopen](https://gitlab.com/tellmeY/issslopen), I created a non-privileged user 'webapprunner' with the password 'webapprunner'.
- To print ```/server1/``` for ```https://x.ssl.airno.de/server1/```, I added a block inside the configuration file that redirects traffic from port 443 on the host with the url "/*" to port 8008 on the localhost. Similarlly, to print ```/``` for ```https://x.ssl.airno.de/server2/```, I added a block to specifically redirect traffic from port 443 on the host with the url "/server2/*" to port 8008 on the localhost.
- The same block-based redirection logic was used to redirect traffic from port 443 on the host with the url "/sslopen*" to port 3000 on the localhost for the "issslopen" app.
- Since all of these redirect only https requests to their relevant proxy 'servers', I added an additional block inside the configuration file to enforce the upgradation of all http requests to https requests.
- The token I appeneded to the list of tokens in the .env file is ```SSL-Admin:thetokenoftheadmin```. 
  - Note that, to allow https traffic I generated a self signed certificate following this [tutorial](https://stackoverflow.com/questions/10175812/how-can-i-generate-a-self-signed-ssl-certificate-using-openssl). 

## Task 6:
- When running the security script ```sudo mysql_secure_installation``` after installing mariadb, an option allows you to disable remote login with a simple yes or no, so I used that to disable remote login.
- I created a user named 'this_user' with the password 'this_user' with minimal options to interact with the ```secure_onboarding``` database.
- The backups for the database are created and stored using a simple script that is automated using a cron job that executes everyday. The backups are stored in ```/var/backups/db```.
  
## Task 7:
- Firstly, I installed wireguard on both the client and server (I used my personal laptop as the client).
- I then generated the public keys in both the client and server.
- Then for the server, I wrote the VPN configuration in ```/etc/wireguard/wg0.conf```.
- To allow traffic to be tunnelled from the client to the server, they must be aware of each other, hence, I added the public key of my laptop (the client) under the [Peer] header and the public key of the server in the client's configuration file.
- For the client(s), I wrote the configuration in the same directory in the file: (```user.conf```).  There I specified the public IP address of the server along with the server's wireguard public key as mentioned above.
- I've attached all the necessary files to configure an 'admin client' as requested for in the tasks' document.

## Task 8:
- After installing docker, enabling it using systemctl and testing it, I transferred a veryyy basic HTML only portfolio website to a volume I created.
- I then set up a systemd service by following [this tutorial](https://documentation.suse.com/smart/systems-management/html/systemd-setting-up-service/index.html). As specified in the now set-up systemd service, a new container is run everytime the container crashes or the system reboots. The container is configured in the same ```portfolio.server``` file to pull alpine and mount the previously cerated volume at the directory: ```/usr/share/nginx/html``` to serve the portfolio content.
- The hosts' nginx configuration file is modified to include an additional block that is responsible for redirecting all https traffic with the url ```/portfolio``` to port 8080 on the host that is bound to port 80 on the container.
- Since the docker volume exists on the hosts file system under the ```/var/lib/docker/volumes``` directory, I changed the access and permissions of the desired volume here.
   
## Task 9:
- Created the network ```ansible-net```. This is the network all the containers will be working inside of.
- I wrote 2 Dockerfiles, one for the control node and another for all the target nodes. Both of these pull Ubuntu and install a couple of dependencies.
- I then built the control-node image and the target-node image from these Dockerfiles. But, building this image in the server crashed it multiple times... with less than half of available RAM left to build an image whose size itself was close to 1 GB, I figured I had no option other than to simply not build and run these containers on the server. So, I built and ran them on my laptop.
- I first generate a ssh key-pair using ```ssh-keygen```. Inspecting the network, I use the local IP addresses of the target containers to copy the public key of the control container into them using ```ssh-copy-id```. I can now freely ssh in and out of the target containers.
- Next, I wrote an ```inventory.ini``` file to specify the details of the target_nodes. I also wrote a simple ```playbook.yml``` file to ping the targets, check the free disk space of the targets and print the latter & show the uptime + display it. [source](https://spacelift.io/blog/ansible-playbooks) 
