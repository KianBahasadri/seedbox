# seedbox
simple documentation on creating seedbox with vps &amp; object cloud storage.  
I personally only use this infrastructure to seed ISO files for various linux distributions.  
The total cost comes out to around $10-$15 a month for around 1TB storage but it will scale with more storage.
I'm personally paying about $5 a month because I'm getting the vpn and storage for free!

## Ingredients
- access to a vps 
  - I'm using the cheapest tier of DigitalOcean, but really anything works, just get something cheap with fast internet speed
  - If you use Oracle or AWS you can get a free vps for like a year or something, I'm paying $5 a month for mine
- access to a vpn
  - my provider is windscribe and my client is wireguard
  - there are a lot of free vpn providers but I'm paying $2 a month, thats basically free
- access to some kind of cloud storage
  - I was going to use backblaze which is an object cloud storage (like aws s3), it is cheap and basically has free egress :D
  - I believe this is one thing you cant (easily) get for free. However, usually institutions like schools, universities, or your work, may provide you with free personal storage.
  - For example my school gives me 5 terabytes of free microsoft cloud storage :D (make sure you encrypt it if its not your own account, ill go over this later)

Basically what I'm saying is that you can definitely set up your shit a lot cheaper than the base rate, but like cmon its $15 who cares.  
Think of it this way, with the money you save on windows licences by downloading various linux distributions (see above section) you can pay for the cloud services :3


## Steps (Overview)
1. VPS Considerations
2. Setup VPN and Firewall
3. Setup torrenting software and SSL certificate
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
(when dealing with any linux firewall, its honestly easier to switch to the root user. Thus, its nice to do this first in case you bork the system as root and want to start over)
in my OPINION, the easiest firewall to use on linux is ufw. Your distribution may or may not already have it running.
**NOTE: ENSURE FIREWALLD IS NOT RUNNING, IT WILL FUCK EVERYTHING UP**
**NOTE: ALSO DONT START UFW BEFORE ALLOWING SSH CONNECTIONS**
For managing ufw you can either use ufw itself or systemctl, I suggest use ufw, I feel like its better, idk, gut feeling.
To see if ufw is running, use: `ufw status verbose` and if stopped, use `ufw enable` to enable it.
You can aso use the status command to see observe the changes we make to the ufw rules.
Now run the following commands:
```
ufw allow ssh
ufw default deny outgoing
ufw default deny incoming
```
This blocks every connection on the server except for port 22.  
If you haven't downloaded your vpn software yet, use `ufw disable`.

At this point, you should set up your vpn, I personally set up wireguard, as OpenVPN is for boomers. (You can use whatever you want it really doesnt matter)
I used the [Windscribe Config Generator](https://windscribe.com/getconfig/wireguard) to generate a config file.
Now you have two options, you can either whitelist the vpn interface or the vpn ip address endpoint. I went with the interface because idk. (the default wireguard interface is wg0)
```
ufw allow in on wg0
ufw allow out on wg0
```
**NOTE: START UP UFW BEFORE STARTING VPN, IT MAY BLOCK YOUR SSH CONNECTION**

Also of course start up your vpn and make sure its enabled to start on boot. if that doesnt happen by default you can use `systemctl enable <vpn_service_name>`.
If you arent sure if its enabled use `systemctl status <vpn_service_name>`.

Now you can start ufw using`ufw enable` and your system should be working properly. do `ping google.com` and if you get packets back then your probably good to go.
Note: now is a good time to run `ufw status verbose` and understand what we did to it.


## Setup torrenting software and SSL
I personally used qbittorrent. I haven't researched the other software so frankly I dont know if this is a good choice given the market for torrenting software.
That said, it does the job for me and I don't have any complaints.
Setting up the software is pretty simple, install qbittorrent-nox and then run it using
```qbittorent-nox```
(pretty straightforward there)

then you need to generate SSL certificates and set an admin password. I HIGHLY RECCOMEND that you do things in that order, because if you set an admin password before generating SSL certificates, you just exposed your admin password to the public :(  
To set up ssl certificates honestly just follow the guide here:
https://github.com/qbittorrent/qBittorrent/wiki/Linux-WebUI-setting-up-HTTPS-with-self-signed-SSL-certificates

If you're wondering whether to use self-signed certificates or certbot or something, just self-sign it man, its not a security risk if your browser caches the certificate and warns you if it changes. (I know firefox does).

Then set an admin password, you can find out where to do it in the application settings pretty easily.


## Mount Cloud Storage
Lastly you need to mount your cloud storage, you can use something like s3fs or rclone mount, I'm going to use rclone mount.
Now, something I honestly don't know shit about is what to do about VFS caching: https://rclone.org/commands/rclone_mount/#vfs-file-caching
I'm going to use FULL caching just because I'm not limited for diskspace, and it *seems* like the fastest choice. guess we'll see huh.


## Try torrenting some ISO files!
All-righty then, you should be good to go, here is the magnet link for EndeavourOS to get you started :D
magnet:?xt=urn:btih:4080177220dac4363d76922c3809a77e8bbe1e30&dn=EndeavourOS_Galileo-Neo-2024.01.25.iso&tr=udp%3A%2F%2Ftracker.openbittorrent.com%3A80&tr=udp%3A%2F%2Ftracker.torrent.eu.org%3A451%2Fannounce&tr=udp%3A%2F%2Fthetracker.org%3A80%2Fannounce&tr=udp%3A%2F%2Ftracker.dutchtracking.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce

