# ssltasks



## Task 3:
- Configured UFW to deny all incoming traffic except for SSH, HTTP, HTTTPS and SSH on port 2222.
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


## Task 5:
- Configured and setup a reverse proxy using nginx.
