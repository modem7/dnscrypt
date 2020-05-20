# DNSCrypt-Proxy

A DNS server container which utilises several UK and European DNS over HTTPS resolution services (cloudflare, quad9-dnscrypt-ip4-nofilter-pri, quad9-dnscrypt-ip4-nofilter-alt, quad9-doh-ip4-filter-alt, quad9-doh-ip4-filter-pri, doh-crypto-sx, dnscrypt.uk-ipv4, cs-uk, v.dnscrypt.uk-ipv4) by utilizing DNSCrypt Proxy (https://github.com/jedisct1/dnscrypt-proxy, https://dnscrypt.info/).

In this config, tcp and udp port 53 must be free on the host:

```bash
docker run -dt --dns 127.0.0.1 -p 53:53/udp -p 53:53/tcp --name dnscrypt-proxy --restart unless-stopped modem7/dnscrypt-proxy
```

If you need it to listen on an alternate port:

```bash
docker run -dt --dns 127.0.0.1 -p 5353:53/udp -p 5353:53/tcp --name dnscrypt-proxy --restart unless-stopped modem7/dnscrypt-proxy
```

## Docker-compose
```bash
version: "2.4"

services:

 #DNSCrypt-Proxy - Non-caching, Non-logging, DNSSEC DNS Resolver
  dnscrypt-proxy:
    image: modem7/dnscrypt-proxy
    hostname: DNSCrypt
    container_name: Dnscrypt-proxy
    volumes:
      - DNSCrypt:/etc/dnscrypt-proxy/
      - /etc/localtime:/etc/localtime
    dns:
      - 127.0.0.1
    ports:
      - "53:53"
      - "5353:5353"
    environment:
      TZ: 'Europe/London'
    networks:
      docker-macvlan:
        ipv4_address: '192.168.0.3'
    restart: always
    mem_limit: 100m
    mem_reservation: 30m

volumes:
  DNSCrypt:
```
---------------

## Modifications
If you want to modify the server list being used or other parameters you can clone the repo, modify the configuration files, build your own image, and run from that build.

Clone Repo:
git clone https://github.com/modem7/Docker.git

Modify DNSCrypt-Proxy config:
dnscrypt/dnscrypt-proxy.toml

Modify servers to meet your needs, adjust other params if desired.  For more detail around those settings, see: https://github.com/jedisct1/dnscrypt-proxy/wiki/Configuration-Sources

Modify dnscrypt-proxy/Dockerfile if you want to adjust the monitor

Run the build:
```bash
docker build -f dnscrypt-proxy/Dockerfile dnscrypt-proxy/ -t dnscrypt-proxy-build
```

Run a container from the build:
```bash
docker run -dt --dns 127.0.0.1 -p 53:53/udp -p 53:53/tcp --name dnscrypt-proxy --restart unless-stopped dnscrypt-proxy-build
```

## Troubleshooting
If you run into issues after updating, remove the container and volume and recreate to get the latest config