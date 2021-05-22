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

6. Add DNS server on devices, or directly in your router configuration

7. Enjoy AD- and trackerfree browsing