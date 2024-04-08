# seedbox
Docker files and Documentation for creating a Seedbox + Media Server using a vps &amp; object cloud storage.  
The total cost comes out to around $10-$15 a month for around 1TB storage but it will scale with more storage.
I'm personally paying about $2 a month because I'm only paying for the vpn; I'm getting the server and storage for free!

### Software:
- Server
  - Ubuntu 22.04 LTS x86\_64
  - Docker + Docker-Compose
- Torrenting:
  - QBittorent
  - Prowlarr
  - Sonarr
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
### External Services:
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


## Limitations and Notices
1. the wireguard container does not support ipv6, it was throwing errors and it seemed too difficult to solve so i just removed ipv6 from the config
2. This error when starting the wireguard container is harmless `s6-rc: fatal: unable to take locks: Resource busy`
3. on azure, the free tier storage was a 64GiB P6 SSD. So basically you cant download anything more than about 58GiB
4. Positive: you can test the setup without mounting the remote storage, because it will just be using a local folder instead. You just have to make sure the media read and write folders are symbolic links to media_local or another directory

## Setup

You need to mount the remote storage twice, once using read-only and no VFS caching, and the other time using full VFS caching. The first one will be for streaming, and the second one will be for torrenting.
actually, sorry, on the second time, you shouldnt use full, you should have qbittorent download files locally and then move them to the storage once its done, so probably just use vfs writes mode

1. mount rclone remote bucket on the path you want
2. make a symbolic link to that path in your seedbox directory named `downloads/`
3. profit

  
  YOOOO, I ADDED A FIREFOX CONTAINER, ROUTED THROUGH WIREGUARD, AND DID DNS LEAK TEST AND SHIT, IT ALL WORKED, HOLY SHIT IT ALL WORKED FAM, LETS FUCKING GOOOOOOO

## Documentation
(Routing Docker Host And Container Traffic Through WireGuard)[https://www.linuxserver.io/blog/routing-docker-host-and-container-traffic-through-wireguard]
