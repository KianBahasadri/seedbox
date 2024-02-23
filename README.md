# seedbox
simple documentation on creating seedbox with vps &amp; object cloud storage.  
I personally only use this infrastructure to seed ISO files for various linux distributions.  
My total cost comes out to around $10-$15 a month for around 1TB storage but it will scale with more storage.

## Ingredients
- access to a vps 
  - I'm using the cheapest tier of DigitalOcean, but really anything works, just get something cheap with fast internet speed
  - If you use Oracle or AWS you can get a free vps for like a year or something, I'm paying $5 a month for mine
- access to a vpn
  - my provider is windscribe and my client is wireguard
  - there are a lot of free vpn providers but I'm paying $2 a month, thats basically free
- access to some kind of cloud storage
  - I'm using backblaze which is an object cloud storage (like aws s3), it is cheap and basically has free egress :D
  - I believe this is one thing you cant (easily) get for free. However, usually institutions like schools, universities, or your work, may provide you with free personal storage.
  - For example my school gives me 4 terabytes of free microsoft cloud storage :D (make sure you encrypt it if its not your own account, ill go over this later)

Basically what I'm saying is that you can definitely set up your shit a lot cheaper than my shit, but like cmon its $15 who cares.  
Think of it this way, with the money you save on windows licences by downloading various linux distributions (see above section) you can pay for the cloud services :3


## Steps (Overview)
1. VPS Considerations
2. Setup VPN and Firewall
3. Setup torrenting software
4. mount cloud storage


## VPS Considerations
I reccommend the following security considerations:
- use a stable release distro like ubuntu or even fedora, I'm using the newest ubuntu LTS version
- use keys to ssh, not passwords
- make sure every user has a moderately strong password (i.e. not "admin" or "password")
  - you can set the same password for the root user and any sudo user (of course). But you can set a weaker password on non sudo users.
- make sure software is up to date (apt update and upgrade)
  - I'm pretty sure you can automate this but I dont lol
  - I'm also pretty sure that you can reduce the risk of out-of-date software by uninstalling bloatware, or skip that and install debian which has no pre-installed bloatware
Non-security factors:
- test the internet speed, if its not gigabit, and you would like gigabit, then probably switch providers
  - mine gets around like 500 up and 400 down. More than enough to get the ball rolling

## Setup VPN and Firewall
(when dealing with any linux firewall, its honestly easier to switch to the root user. Thus, its nice to do this first in case you bork the system and want to start over)
in my OPINION, the easiest firewall to use on linux is ufw. Your distribution may or may not already have it running.
For managing ufw you can either use ufw itself or systemctl, I suggest use ufw, I feel like its better, idk, gut feeling.
To see if its running, you can do either 
```systemctl status ufw```
or
```ufw status```

and if stopped, use
```ufw enable```
or
```systemctl startufw```
to enable it.

to see the current ufw status run:
```ufw status verbose```
You can run that command again afterwards to see the differences.

Now run the following commands:
```
ufw allow ssh
ufw default deny outgoing
ufw default deny incoming
```
This blocks every connection on the server except for port 22.  
If you haven't installed your vpn software yet, use
