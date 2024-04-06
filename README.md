# seedbox
Documentation on creating a Seedbox + Media Server using a vps &amp; object cloud storage.  
The total cost comes out to around $10-$15 a month for around 1TB storage but it will scale with more storage.
I'm personally paying about $2 a month because I'm only paying for the vpn; I'm getting the server and storage for free!

## Requirements
#### Software:
- Ubuntu 22.04 LTS x86\_64
- Torrenting:
  - QBittorent
  - Radarr
- Streaming:
  - Kodi -- (This is a client side application)
  - Jellyfin
- Networking
  - Nginx
  - CloudFlare
    - Domain Name Registrar
    - SSL Certificate
  - Wireguard
#### External Services:
- Virtual Private Server
  - I'm using a Microsoft Azure B1s instance
  - any non shit-tier server will work, just get something moderately cheap (~$10/month)
  - If you use Oracle or AWS you can get a free vps for like a year or something, I'm getting free Azure credits from Github Education Pack
  - Digital Ocean, Linode, and many other providers provide a couple free months.
- Virtual Private Network
  - I am using Windscribe
  - there are a lot of free vpn providers but I'm paying $2 a month, thats basically free
- Cloud Storage
  - I was going to use backblaze which is an object cloud storage (like aws s3), it is cheap and basically has free egress :D
  - I believe this will typically end up being the most expensive part. However, usually institutions like schools, universities, or your work, may provide you with free personal storage.
  - My school gives me 5 terabytes of free microsoft cloud storage :D
  - Make sure you encrypt it if someone else is providing it to you, ill go over this later


## Steps To Follow
note: the steps must be followed in a specific order, otherwise starting the VPN may drop your SSH session.  
1. Set up NGINX & Firewall

2. Setup VPN and Firewall
3. Setup torrenting software and SSL certificate
4. mount cloud storage

## Set up NGINX & Firewall
Most cloud compute providers offer a firewall service. Using it will take the load of the firewall off your server. Otherwise I reccomend using UFW.
Firstly, go ahead and enable inbound connections on ports 22,80,443,8080 and 8096. We will remove 80 later for security reasons but we need it to get set up. Also, to reduce bot-spam on your server, use an ip whitelist for inbound ssh connections.

Next:
```
sudo apt install nginx
```
navigate to `http://your_server_ip/` and you should see the Nginx welcome page.  
make a directory where you want to serve files from. I did `/home/ubuntu/media`.
then create a file `/etc/nginx/conf.d` with contents:
```
http {
  server {
    listen 80
    location / {
      root /home/ubuntu/media;
    }
  }
}
```



At this point, you should set up your vpn, I personally set up wireguard, as OpenVPN is for boomers.
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

