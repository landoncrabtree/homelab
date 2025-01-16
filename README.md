# Landon's Homelab

This repository is a collection of assets for my homelab. Right now, it is just Docker compose files for different services.

## Services

- [LobeChat](https://github.com/lobehub/lobe-chat) -  open-source, modern-design AI chat framework with support for multiple models.
    - `casdoor` - authentication provider
    - `minio` - object storage for file embedding
    - `postgres` - database
    - `lobechat` - LobeChat application
- [Wireguard](https://www.wireguard.com/) -  fast, modern, and secure VPN tunnel.
- [Pi-hole](https://pi-hole.net/) -  network-wide ad blocker.

## Server

I am hosting these services on a single Mac mini M1 with 8GB of RAM and 256GB of storage. I am using [Docker Desktop](https://www.docker.com/products/docker-desktop/) to run the services.

## Deployment

### LobeChat

```bash
cd lobechat
cp .env.sample .env
docker compose up -d
```

Browse to http://localhost:9000 and create a new MinIO secret key. Update the `.env` file with the new secret key. Then, create a new bucket called `lobechat`. From this point, you should be good to go.

### Wireguard (Does not work - need to host on linux)

or <https://www.reddit.com/r/WireGuard/comments/vt8nqu/how_to_setup_wireguard_server_in_mac_os/>

```bash
cd wireguard
cp .env.sample .env
docker compose up -d
```

`.env` expects a bcrypt hash of the password. You can generate one with the following command:

```bash
docker run --rm -it ghcr.io/wg-easy/wg-easy wgpw 'example'
```

Browse to http://localhost:51821 and use the password as configured in the `.env` file to login. Next, you can add a new client to the Wireguard server. 

```bash
cp wireguard_data/wg0.conf /opt/homebrew/etc/wireguard/wg0.conf
sudo wg-quick up wg0
```

You should now be connected to the wireguard network. You can test this by running `ping 10.8.0.1` from the command line.

### Pi-hole

```bash
cd pihole
cp .env.sample .env
docker compose up -d
```

Configure router (usually 192.168.1.1) to use the Pi-hole as the DNS server. 
