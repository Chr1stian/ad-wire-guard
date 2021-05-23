# ad-wire-guard
Setup instructions for Adguard Home and WireGuard


# Adguard Home
1. Configure docker container  
**Need docker and docker-compose**  
Create docker-compose.yaml file in /home/user/docker folder. Replace path to match your system and user, check with `pwd` command.  
```docker-compose.yaml
version: "2.1"
services:
  adguard-home:
    image: adguard/adguardhome
    container_name: adguard-home
    restart: unless-stopped
    network_mode: host
    dns:
      - 127.0.0.1
      - 1.1.1.1
    ports:
      - '53:53/tcp'
      - '53:53/udp'
      - '67:67/udp'
      #- '68:68/tcp'
      #- '68:68/udp'
      - '80:80/tcp'
      - '443:443/tcp'
      - '853:853/tcp'
      - '3000:3000/tcp'
      - '99:99/tcp'
    volumes:
      - /home/preference/adguard/work:/opt/adguardhome/work
      - /home/preference/adguard/conf:/opt/adguardhome/conf
```
2. Start container  
```zsh
docker-compose up -d
```

3. Open browser at server IP:3000

4. (Disable systemd-resolved if its using port 53)
https://github.com/AdguardTeam/AdGuardHome/wiki/Docker#-running-on-a-system-with-resolved-daemon

5. Add DNS filterlist  
E.g. [http://dbl.oisd.nl](http://dbl.oisd.nl)
(I reccomend using only one list, as to avoid conflicts)

6. Set server static IP

7. Add DNS server on devices, or directly in your router configuration

8. Enjoy AD- and trackerfree browsing

# WireGuard
1. Create or add service to previous docker-compose.yaml.  
```docker-compose.yaml
version: "2.1"
services:
  wireguard:
    image: ghcr.io/linuxserver/wireguard
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Oslo
      #- SERVERURL=wireguard.domain.com #optional
      #- SERVERPORT=51820 #optional
      - PEERS=1 #optional
      - PEERDNS=auto #optional
      - INTERNAL_SUBNET=10.13.13.0 #optional
      - ALLOWEDIPS=0.0.0.0/0 #optional
    volumes:
      - /path/to/appdata/config:/config
      - /lib/modules:/lib/modules
    ports:
      - 51820:51820/udp
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped
```

2. Start container  
```zsh
docker-compose up -d
```

3. Port forward SERVERPORT to server IP in router

4. Setup VPN network if router requires it  
E.g. 10.13.13.0/24

5. Download WireGuard app on clients

6. Create and scan QR-code
```zsh
apt install qrencode
qrencode -t ansiutf8 < wireguard/config/peer1/peer1.conf
```

7. Browse encrypted, secure and ad-free while away