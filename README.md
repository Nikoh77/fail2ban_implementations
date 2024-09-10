# FAIL2BAN implementations
Just my fail2ban filters library to use with or without a webserver proxy, and some software/applications:
* [**Shinobi DVS**](https://shinobi.video/)
* [**Foundry VTT**](https://foundryvtt.com/)

## Prerequisites:
fail2ban at least version 1.0.2

## Installation:
# On application server:
- After fail2ban installation:\
- copy one or more filters from mine here to fail2ban filters directory (usually `/etc/fail2ban/filter.d`)\
- do `cp jail.conf jail.local` to prevent future fail2ban updates from changing your configuration.\
- ONLY with a proxy, copy `ssh-nftables.conf` from here to fail2ban actions directory (usually `/etc/fail2ban/action.d`)\

Add/append below section to jail.local on the fail2ban root dir (replacing \*\*\*2replace\*\*\*) with your data:
```
[***JailName***]
enabled = true
logpath = ***path2log***
port = http,https
filter = ***filterFileName***
bantime  = 1h
banaction = nftables[remote-host=***user***@***proxyIP***, type=multiport, blocktype=drop]
```
ONLY with a proxy the line above become:\
`banaction = nftables-multiport.conf[remote-host=***user***@***proxyIP***, type=multiport, blocktype=drop]`

Below a jail example:
```
[shinobi]
enabled = true
logpath = /root/.pm2/logs/camera-out.log
port = http,https
filter = shinobi_DVS
bantime  = 1h
banaction = ssh-nftables[remote-host=fail2ban@192.168.1.110, type=multiport, blocktype=drop]
```
# That's all, if you have a proxy server continue with the instructions below
# On the proxy you must:
1. create a user with name as you want (in my case fail2ban)\
2. give rights to user to execute nftables (nft bin) as root without asking password; since nftables uses several libraries and commands, it will not be enough to assign the setuid to the nft command to run it as root, you will need to add the user to the sudoers file:
do:\
`sudo visudo`\
Look for the area where per-user privileges are added (where is not very important but it is always better to keep things in order)
Add:\
`***user*** ALL=(ALL) NOPASSWD: ALL`\
# On fail2ban PC to proxy PC:
After creating the user, as indicated in above point one, you need to enable ssh connection with key (without asking password) by root user:\
- `sudo su`\
- `ssh-keygen -t rsa`\
- `ssh-copy-id ***user***@proxyIP`\
- Authenticate and you will get a confirmation message that a key has been copied, then try connecting with:\
`ssh ***user***@proxyIP uname -a`
you should get the requested data back\


# Contacts:
For any questions, you can find me on the official Shinobi Discord server (if I don't answer, please ping me).
