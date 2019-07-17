# syncthing-relay-discovery-logging

## This is a fork
This repo is forked from [seancrites/syncthing-relay-discovery-logging](https://github.com/seancrites/syncthing-relay-discovery-logging), which is a fork of [t4skforce/syncthing-relay-discovery](https://github.com/t4skforce/syncthing-relay-discovery). I made some small changes to suit my own need.

This repo contains two dockerfiles: `Dockerfile.private` and `Dockerfile.public`. If you want to host a Syncthing discovery and relay server for private use (just for yourself or for a selected number of users), use the former one. If you want to host a public instance to be added to the 'official' Syncthing server list, use `Dockerfile.public`.

On Docker Hub, the following tags are available:
* `:private`: For hosting a private server.
* `:public`: For hosting a public server.
* `:latest`: Same as `:private`.

The remainder of this README has been left unchanged, except for the badges.

# Readme
Docker Container for the global relay server for the [http://syncthing.net/](http://syncthing.net/) project. I build the container because ther is no official one. This build is listening on the gihub project of the relay server and gets updated whenever there is a code change. [relaysrv GitHub repo](https://github.com/syncthing/relaysrv) and [dicosrv GitHub repo](https://github.com/syncthing/discosrv). The container is intendet for people who like to roll their own private syncthing "cloud".

The files for this container can be found at my [GitHub repo](https://github.com/seancrites/syncthing-relay-discovery-logging)

[![](https://images.microbadger.com/badges/image/seancrites/syncthing-relay-discovery-logging.svg)](http://microbadger.com/images/seancrites/syncthing-relay-discovery-logging "Get your own image badge on microbadger.com") [![](https://img.shields.io/docker/automated/seancrites/syncthing-relay-discovery-logging.svg)](https://cloud.docker.com/repository/docker/seancrites/syncthing-relay-discovery-logging) [![](https://img.shields.io/docker/build/seancrites/syncthing-relay-discovery-logging.svg)](https://cloud.docker.com/repository/docker/seancrites/syncthing-relay-discovery-logging) [![](https://images.microbadger.com/badges/version/seancrites/syncthing-relay-discovery-logging.svg)](http://microbadger.com/images/seancrites/syncthing-relay-discovery-logging "Get your own version badge on microbadger.com") [![](https://img.shields.io/docker/pulls/seancrites/syncthing-relay-discovery-logging.svg)](https://cloud.docker.com/repository/docker/seancrites/syncthing-relay-discovery-logging) [![](https://img.shields.io/docker/stars/seancrites/syncthing-relay-discovery-logging.svg)](https://cloud.docker.com/repository/docker/seancrites/syncthing-relay-discovery-logging) [![](https://img.shields.io/github/last-commit/seancrites/syncthing-relay-discovery-logging.svg)](https://github.com/seancrites/syncthing-relay-discovery-logging) [![](https://img.shields.io/maintenance/yes/2019.svg)](https://github.com/seancrites/syncthing-relay-discovery-logging) [![](https://img.shields.io/github/issues-raw/seancrites/syncthing-relay-discovery-logging.svg)](https://github.com/seancrites/syncthing-relay-discovery-logging/issues) [![](https://img.shields.io/github/issues-pr-raw/seancrites/syncthing-relay-discovery-logging.svg)](https://github.com/seancrites/syncthing-relay-discovery-logging/pulls)


# About the Container

This build is based on t4skforce's [syncthing-relay-discovery](https://github.com/t4skforce/syncthing-relay-discovery) docker image and installs the latests successful build of the syncthing relay server, discovery server and enables logging of both.

# How to use this image

`docker run --name syncthing-relay -d -p 22067:22067 --restart=always seancrites/syncthing-relay-discovery-logging:latest`

This will store the certificates and all of the data in `/home/syncthing/`. You will probably want to make at least the certificate folder a persistent volume (recommended):

`docker run --name syncthing-relay -d -p 22067:22067 -v /your/home:/home/syncthing/certs --restart=always seancrites/syncthing-relay-discovery-logging:latest`

If you already have certificates generated and want to use them and protect the folder from being changed by the docker images use the following command:

`docker run --name syncthing-relay -d -p 22067:22067 -v /your/home:/home/syncthing/certs:ro --restart=always seancrites/syncthing-relay-discovery-logging:latest`

Creating cert directory and setting permissions (docker process is required to have access):
```bash
mkdir -p /your/home/certs
chown -R 1000:1000 /your/home/certs
```

# Container Configuration

There are several configuarion options available. The options are configurable via environment variables (docker default):

Example enabling debug mode:
```bash
export RELAY_OPTS='-debug'
export DISCO_OPTS='-debug'
docker run --name syncthing-relay-discovery -d -p 22067:22067 -p 22026:22026 --restart=always seancrites/syncthing-relay-discovery-logging:latest
```

or

```bash
docker run --name syncthing-relay-discovery -d -p 22067:22067 -p 22026:22026 -e RELAY_OPTS='-debug' -e DISCO_OPTS='-debug' --restart=always seancrites/syncthing-relay-discovery-logging:latest
```

## Options

* DEBUG: enable debugging (true/false) / default:true

### Syncthing-Relay Server

* RATE_GLOBAL: global maximum speed for transfer / default:10000000 = 10mbps
* RATE_SESSION: maximum speed for transfer per session / default:5000 = 500kbps
* TIMEOUT_MSG: change message timeout / default: 1m45s
* TIMEOUT_NET: change net timeout / default: 3m30s
* PING_INT: change ping timeout / default: 1m15s
* PROVIDED_BY: change provided by string / default:"syncthing-relay"
* RELAY_PORT: port the relay server listens on / default:22067
* STATUS_PORT: disable by default to enable it add `-d 22070:22070` to you `docker run` command  / default:22070
* POOLS: leave empty for private relay use "https://relays.syncthing.net/endpoint" for public relay / default: ""
* RELAY_OPTS: to provide addition options not configurable via env variables above / default: ""
  - example: `-e RELAY_OPTS='-ext-address=:443'`

### Syncthing-Discovery Server
* DISCO_PORT: port the discovery server listens on / default:22026
* DISCO_OPTS: to provide addition options not configurable via env variables above / default: ""
  - example: `-e RELAY_OPTS='-metrics-listen=:19201'`

Have a look at the current doc [GitHub - relaysrv](https://github.com/syncthing/relaysrv/blob/master/README.md) [GitHub - discosrv](https://github.com/syncthing/discosrv/blob/master/README.md)
