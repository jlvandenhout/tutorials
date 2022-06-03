# Hornet setup using Docker and Traefik
In this tutorial we will set up an [IOTA Hornet node](https://wiki.iota.org/hornet/welcome) that connects to [the mainnet](https://wiki.iota.org/learn/networks/iota-1.5-chrysalis), using Docker to manage our services and Traefik as [a reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy) to control access to our node and route requests to the correct endpoints.

## Prerequisites
- A running server with [Ubuntu 22.04 LTS](https://releases.ubuntu.com/) or above.
- [Docker engine](https://docs.docker.com/engine/install/ubuntu/) installed on your server.

## Maintaining your node
### Create a database backup
```
HORNET_BACKUP_DIR=./backup/$(date -u +%Y-%m-%dT%H:%M:%S)
mkdir -p $HORNET_BACKUP_DIR
docker compose stop hornet
cp -R hornet/mainnetdb $HORNET_BACKUP_DIR
cp -R hornet/snapshots $HORNET_BACKUP_DIR
docker compose start hornet
```

### Update your node
```
sudo docker compose pull && sudo docker compose up --remove-orphans -d
```