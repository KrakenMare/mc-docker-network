# Minecraft Network Boilerplate
![](https://img.shields.io/badge/PROXY-WATERFALL-informational?style=flat&logo=<LOGO_NAME>&logoColor=gray&color=blue) ![](https://img.shields.io/badge/PROXY-VELOCITY-informational?style=flat&logo=<LOGO_NAME>&logoColor=gray&color=green) ![](https://img.shields.io/badge/SUPPORT-DOCKER-informational?style=flat&logo=<LOGO_NAME>&logoColor=gray&color=3d34eb) ![](https://img.shields.io/github/license/KrakenMare/mc-docker-network?label=LICENSE&style=flat-square)

## Explanation
Mostly made this as I wanted to explore the super cool docker image for minecraft, and really like how docker-compose works in this regard, when running multiple local servers.

This is minecraft network boilerplate utilizing docker and docker compose for running a standalone Bungee/Waterfall network with multiple servers using Docker images. There is an optional Velocity setup.

It utilizes a private bridged network so the spigot/purpur servers are only accessible via the proxy connection. There is an example `mariadb` Database configured. The `port`, `host`, `user`, and `password` may be configured via the the `.env` file.

## Tldr
Run minecraft network with four servers. Go. 
```
git clone https://github.com/KrakenMare/mc-docker-network network
cd network
docker-compose up -d --build
```
Connect to your machine ip:25565. 

# Configuration & First Time

This is a pretty straight forward docker compose project. It can be built and ran in a deteached mode via `docker-compose up -d --build`. 

The first time you run, the project will generate volumes using the folders, `hub`, `build` etc spefific to each server. This will mount the configuration files, and will also be populated with world data, and the configuration files created upon startup. See Folders & Volumes.

If you make changes, you can re-run that command, or restart indiviaul containers via `docker restart container-name`.


## Folders & Volumes
Each server has a folder with it's configuration file. These are used and specified as `volumes`. The data from the server will be stored in these containers. So the worlds, plugins, will all be inside this mount. Not unsimiliar from a traditional setup.

# Docker
For Docker Images, this uses the fantastic images from itzg available @ https://github.com/itzg/docker-minecraft-server.

These images are super versatile, allowing access to dozen of enviroment variables, and different server sources. They also have an image for proxies that is utilized.

For all of these images, the default volume and bind is `:/data`. Following this logic, plugins would be `:/data/plugins`.


# Servers
This boilerplate has an example of four game servers, and both a Velocity and Bungeecord/Waterfall configuration. 
![Servers](https://gyazo.com/3aef936a1674b5dbf834a80d6d48e343.png)

### Proxy
A Waterfall Proxy is running bound to the 25565 port on the host network, and is the only exposed port of this setup. We utilize a private network for the server containers, and let the proxy forward the connections. This is the default setup. There is also a `velocity.toml` example configuration file mirroring the bungeecord/waterfall setup. To enable this, you change `WATERFALL` to `VELOCITY` in the `docker-compose.yml`

#### Defaults - Both Proxies
* Listens on 0.0.0.0:25565 and is exposed on the host network.
* Support for Forge is enabled.
* Configuration for a lobby + 3 other servers.
* Uses online mode on the proxy. Mojang accounts only by default.

### Hub
 Privately listening on: `mc-hub:25565`
This server is running the latest build of purpur, and is the default server all players connect to.

### Factions
Privately listening on: `mc-factions:25565`
This server is running the latest build of purpur, and is configured with the name Factions.


### Skyblock
Privately listening on: `mc-factions:25565`
This server is running the latest build of purpur, and is configured with the name Skyblock.


### Build
Privately listening on: `mc-build:25565`
This server is running the latest build of purpur, and is configured with a whitelist. Creative Gamemode is enabled by default.

The whitelist is on by default, and a list of default names is in the `/build` directory.


### All Servers

All servers are configured to use the latest version of purpur, and install four plugins for cross compatability. This is accomplished via very awesome features built for the docker images we are using.

* All servers are on a private subnet and are accessed via the proxy. A firewall is still recommended for the `25655` and any other exposed ports.
* IP Forwarding is enabled.
* All servers are persistent across container destruction, and reboots via the use of volumes.

#### Plugins
* Installs ViaVersion, ViaRewind, Vault & ProtocolLib on every server that supports it.
* Installations are pulled from spigot.org via Plugin ID.

#### Configuration

* Each server has it's own folder with a barebones spigot.yml that has some performance tuning and enables bungeecord to support `ip forwarding`.

* There is a registered container for every server, with a defined container-name, and the port 25565 exposed, but not bound. In the velocity and bungee configurations, the container-name replaces the localhost you would normally use. So the hub is `mc-hub:25565`.

### Database

There is a MariaDB instance configured to run using the credentials and database from the `.ENV` file. This is to avoid hardcoding any sort of passwords into the repositories, and makes it easy to change. MariaDB is a MySQL compatiable replacement.

![MariaDB](https://gyazo.com/981083351937dc2dda8fbdf3de11f2c5.png)

The bitnami image is used as they are trusted, stable and well maintained. Further configuration can be done via ENVIROMENT variables the image exposes. See more [Here - Bitnami MariaDb Image](https://github.com/bitnami/bitnami-docker-mariadb)

# Shared

Folder: `common` - stores op.json and other configuration files mounted across all servers.

