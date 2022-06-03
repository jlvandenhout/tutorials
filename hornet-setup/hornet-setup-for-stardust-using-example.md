# Hornet setup using Docker and Traefik
In this tutorial we will set up an [IOTA Hornet node](https://wiki.iota.org/hornet/welcome) that connects to the `alphanet` test network, using Docker to manage our services and Traefik as [a reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy) to control access to our node and route requests to the correct endpoints.

## Prerequisites
- A running server with [Ubuntu 22.04 LTS](https://releases.ubuntu.com/) or above.
- [Docker engine](https://docs.docker.com/engine/install/ubuntu/) installed on your server.

## Setup
# Download and unpack the Hornet docker example release
Go to the directory where you want to run the node from. Download the software archive, unpack it and delete the archive.

```
wget https://github.com/iotaledger/hornet/releases/download/v2.0.0-alpha17/HORNET-2.0.0-alpha17-docker-example.tar.gz && tar -xf HORNET-2.0.0-alpha17-docker-example.tar.gz && rm HORNET-2.0.0-alpha17-docker-example.tar.gz
```

# Configure the node
Follow the README provided in the directory to configure the node, which involves adding a `.env` file to set needed environment variables and creating credentials to log into the node dashboard. You should be able to then run the node using `docker compose up -d`, which starts your node as a daemon process. Shut it down with `docker compose down`.

Open ports 80 and 443 for the API and dashboard, 14626 for autopeering and 15600 for gossip.

```
sudo ufw allow 80,443,15600/tcp && sudo ufw allow 14626/udp
```

Optionally change the dashboard bind address in the `config.json` file to `0.0.0.0:8081` if you want to access your dashboard from a remote machine.