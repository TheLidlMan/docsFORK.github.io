---
title: DOH (DNS over HTTPS) DNS Resolving (Cloudflared)
date: 2022-06-13 12:00:00 +0100 
categories: [The Ultimate Pi-hole Project, 3b. DOH DNS Resolving]
tags: [pi, selfhosting, pihole, privacy, isp, tracking, cloudflared]     # TAG names should always be lowercase
---

## Why?

DNS-Over-HTTPS is a protocol for performing DNS lookups via the same protocol you use to browse the web securely: HTTPS.

With standard DNS, requests are sent in plain-text, with no method to detect tampering or misbehavior. This means that not only can a malicious actor look at all the DNS requests you are making (and therefore what websites you are visiting), they can also tamper with the response and redirect your device to resources in their control (such as a fake login page for internet banking).

DNS-Over-HTTPS prevents this by using standard HTTPS requests to retrieve DNS information. This means that the connection from the device to the DNS server is secure and can not easily be snooped, monitored, tampered with or blocked. It is worth noting, however, that the upstream DNS-Over-HTTPS provider will still have this ability. For this reason I will not be using Cloudflares `1.1.1.1` service due to there lack of privacy, we will be using `9.9.9.9`.

## How?


We are going to be using cloudflared to run the local DOH upstream client for our `Pi-Hole`.

Firstly we are going to check that all the packages are upto date on the Pi after installation, so we will run this in the terminal.

```bash
sudo apt update && sudo apt upgrade -y
```

Now we are ready to install `cloudflared`, we need to run the following command

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64 
sudo cp ./cloudflared-linux-arm64 /usr/local/bin/cloudflared 
sudo chmod +x /usr/local/bin/cloudflared cloudflared -v
```

Now we are going to make the folder and config file that cloudflared will use

```bash
sudo mkdir /etc/cloudflared/ 
#Followed by 
sudo nano /etc/cloudflared/config.yml
```

Now once the text editor `nano` is open you can paste the following in

```yaml
proxy-dns: true
proxy-dns-port: 5053
proxy-dns-upstream:
  - https://dns.quad9.net/dns-query
```

Now to save this you use `CTRL` + `x` then select yes with `y` then followed by `Enter` to save to that file name and it should bring you back to the terminal.

Now make sure clouldflared runs at startup

```bash
sudo cloudflared service install --legacy
```

Now we are going to check the status of cloudflared by starting then checking the health of it.

```bash
sudo systemctl start cloudflared
```

Now wait about 5-10 second and then run this command

```bash
sudo systemctl status cloudflared
```

You should get something like this back

```terminal
● cloudflared.service - cloudflared DNS over HTTPS proxy
     Loaded: loaded (/etc/systemd/system/cloudflared.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-05-30 12:33:43 BST; 2 weeks 0 days ago
   Main PID: 812 (cloudflared)
      Tasks: 12 (limit: 3720)
        CPU: 1h 28.364s
     CGroup: /system.slice/cloudflared.service
             └─812 /usr/local/bin/cloudflared proxy-dns --port 5053 --upstream https://dns9.quad9.net/dns-query --upstream https://dns.quad9.net/dns-query
```

We have now completed the setup BUT wait because we need to set it up within `Pi-Hole` so that it can use it. So navigate to your pihole admin page `192.168.xx.xx/admin` Then go to the setting on the sidebar, then select DNS from the top menu bar and in the `Custom 1 (IPv4)` section your should type in

```
127.0.0.1#5053
```

Now click the `Save` button at the bottom of this page and you will now have private HTTPS DNS queries on your network.
