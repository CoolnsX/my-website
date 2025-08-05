+++
title = "Server Setup: Media Server"
date = 2025-08-05
extra.image = "assets/starrs-jellyfin.png"
description = "Streaming Without Subscriptions"
+++

{% paragraph(class="m-8") %}
In the 90s, people started buying movies, shows, and music with their own money and wanted to preserve their collections. To protect their purchases from physical damage or data rot, many began ripping their media into digital formats. As the internet expanded in the early 2000s, people started sharing these digital files with others through various methods — one of the most popular being torrents. This caught the attention of the film and music industries, leading to a widespread crackdown on piracy. In response to the growing demand for convenient, legal access to content, Netflix launched a streaming service that offered movies and shows at affordable prices while handling licensing fees with content owners. As internet speeds improved, major media companies saw the potential and began pulling their content from Netflix to launch their own platforms. This led to a fragmented market where consumers now need multiple subscriptions just to watch their favorite shows
{% end %}

{% paragraph(class="m-8") %}
The issue doesn't end there. Streaming services control access to content, if they remove a show or movie from their servers, you lose the ability to watch it, even if it was part of your watchlist. A similar shift has occurred in gaming. In the past, buying a game meant owning a physical copy that you could keep forever. Today, digital game purchases typically give you a license to download and play the game. If the developer or store decides to remove it, you can lose access entirely, meaning you don’t truly own the game, you're just borrowing it under certain conditions. While online streaming services and digital stores have had their ups and downs, many people still choose to support their favorite shows and movies by purchasing them on physical media and creating digital backups to preserve them. However, some also share these backups with others, which continues to fuel the fire of digital piracy.
{% end %}

{% paragraph(class="m-8") %}
**Enter media automation**, a set of services and tools that help users organize and access their personal media collections from anywhere in the world, in whatever way they prefer.
{% end %}

{{ subheading(text="Radarr") }}

{% paragraph(class="m-8") %}
Radarr is Selfhost Application that is used to organise and Manage Movies, it also helps in tracking for upcoming movies and movies collection.

There are plenty of guides on Internet for setting this up using Docker, I am helping for setting up using Podman, since there are not that much guides on it.
{% end %}

{% code(file="~/.config/containers/systemd/radarr.container") %}
```ini
[Unit]
Description=Radarr Container

[Container]
Pod=starrs.pod #explained at the end.
ContainerName=radarr
Image=ghcr.io/linuxserver/radarr:latest

# Enable auto-update container
AutoUpdate=registry # for container auto-update feature, you don't need watchtower anymore
EnvironmentFile=./.env # this file should be present in same location as this config file

Volume=%h/podman/starrs/radarr:/config # resolves to ~/podman/starrs/radarr directory on host
Volume=${DATA}:/data

[Service]
# this below line is used to define all the variables above so that, all variables can be resolved
EnvironmentFile=%h/.config/containers/systemd/starrs/.env
Restart=always
TimeoutStartSec=300

[Install]
# this will restart the container on boot, you have to run "loginctl enable-linger $USER" for this to be worky.
WantedBy=default.target
```
{% end %}

{{ subheading(text="Sonarr") }}

{% paragraph(class="m-8") %}
Sonarr is used to organise and Manage TV shows and anime, it also helps in tracking for upcoming seasons of shows.
{% end %}

{% code(file="~/.config/containers/systemd/sonarr.container") %}
```ini
[Unit]
Description=Sonarr Container

[Container]
Pod=starrs.pod #explained at the end.
ContainerName=sonarr
Image=ghcr.io/linuxserver/sonarr:latest

# Enable auto-update container
AutoUpdate=registry # for container auto-update feature, you don't need watchtower anymore
EnvironmentFile=./.env # this file should be present in same location as this config file

Volume=%h/podman/starrs/sonarr:/config # resolves to ~/podman/starrs/sonarr directory on host
Volume=${DATA}:/data

[Service]
# this below line is used to define all the variables above so that, all variables can be resolved
EnvironmentFile=%h/.config/containers/systemd/starrs/.env
Restart=always
TimeoutStartSec=300

[Install]
# this will restart the container on boot, you have to run "loginctl enable-linger $USER" for this to be worky.
WantedBy=default.target
```
{% end %}

{{ subheading(text="Prowlarr") }}

{% paragraph(class="m-8") %}
Prowlarr is used by sonarr and radarr, to search for shows and movies.
{% end %}

{% code(file="~/.config/containers/systemd/prowlarr.container") %}
```ini
[Unit]
Description=Prowlarr Container

[Container]
Pod=starrs.pod #explained at the end.
ContainerName=prowlarr
Image=ghcr.io/linuxserver/prowlarr:latest

# Enable auto-update container
AutoUpdate=registry # for container auto-update feature, you don't need watchtower anymore
EnvironmentFile=./.env # this file should be present in same location as this config file

Volume=%h/podman/starrs/prowlarr:/config # resolves to ~/podman/starrs/prowlarr directory on host
Volume=${DATA}:/data

[Service]
# this below line is used to define all the variables above so that, all variables can be resolved
EnvironmentFile=%h/.config/containers/systemd/starrs/.env
Restart=always
TimeoutStartSec=300

[Install]
# this will restart the container on boot, you have to run "loginctl enable-linger $USER" for this to be worky.
WantedBy=default.target
```
{% end %}

{{ subheading(text="Bazarr") }}

{% paragraph(class="m-8") %}
Bazarr is used to manage subtitles, it checks for content in directory and also communicate to sonarr and radarr via their APIs for new content to automatically download subtitles.
{% end %}

{% code(file="~/.config/containers/systemd/bazarr.container") %}
```ini
[Unit]
Description=Bazarr Container

[Container]
Pod=starrs.pod #explained at the end.
ContainerName=bazarr
Image=ghcr.io/linuxserver/bazarr:latest

# Enable auto-update container
AutoUpdate=registry # for container auto-update feature, you don't need watchtower anymore
EnvironmentFile=./.env # this file should be present in same location as this config file

Volume=%h/podman/starrs/bazarr:/config # resolves to ~/podman/starrs/bazarr directory on host
Volume=${DATA}:/data

[Service]
# this below line is used to define all the variables above so that, all variables can be resolved
EnvironmentFile=%h/.config/containers/systemd/starrs/.env
Restart=always
TimeoutStartSec=300

[Install]
# this will restart the container on boot, you have to run "loginctl enable-linger $USER" for this to be worky.
WantedBy=default.target
```
{% end %}

{{ subheading(text="Jellyfin") }}

{% paragraph(class="m-8") %}
Jellyfin is used to stream media from anywhere and on any devices as long as it has internet access.
{% end %}

{% code(file="~/.config/containers/systemd/jellyfin.container") %}
```ini
[Unit]
Description=Jellyfin Container

[Container]
Pod=starrs.pod #explained at the end.
ContainerName=jellyfin
Image=ghcr.io/linuxserver/jellyfin:latest

# Enable auto-update container
AutoUpdate=registry # for container auto-update feature, you don't need watchtower anymore
EnvironmentFile=./.env # this file should be present in same location as this config file

Volume=%h/podman/starrs/jellyfin:/config # resolves to ~/podman/starrs/jellyfin directory on host
Volume=${DATA}/media:/data

# gpu acceleration
AddDevice=/dev/dri
GroupAdd=989

[Service]
# this below line is used to define all the variables above so that, all variables can be resolved
EnvironmentFile=%h/.config/containers/systemd/starrs/.env
Restart=always
TimeoutStartSec=300

[Install]
# this will restart the container on boot, you have to run "loginctl enable-linger $USER" for this to be worky.
WantedBy=default.target
```
{% end %}

{{ subheading(text="qBittorrent") }}

{% paragraph(class="m-8") %}
If you know torrent, you probably know this app.
{% end %}

{% code(file="~/.config/containers/systemd/qBittorrent.container") %}
```ini
[Unit]
Description=qBittorrent Container

[Container]
Pod=starrs.pod #explained at the end.
ContainerName=qbittorrent
Image=ghcr.io/linuxserver/qbittorrent:latest

# Enable auto-update container
AutoUpdate=registry # for container auto-update feature, you don't need watchtower anymore
EnvironmentFile=./.env # this file should be present in same location as this config file
Environment=WEBUI_PORT=9090
Environment=TORRENTING_PORT=6881
Environment=DOCKER_MODS=ghcr.io/vuetorrent/vuetorrent-lsio-mod:latest

Volume=%h/podman/starrs/qbittorrent:/config # resolves to ~/podman/starrs/qbittorrent directory on host
Volume=${DATA}/downloads:/downloads

[Service]
# this below line is used to define all the variables above so that, all variables can be resolved
EnvironmentFile=%h/.config/containers/systemd/starrs/.env
Restart=always
TimeoutStartSec=300

[Install]
# this will restart the container on boot, you have to run "loginctl enable-linger $USER" for this to be worky.
WantedBy=default.target
```
{% end %}

{{ subheading(text="Important") }}

{% paragraph(class="m-8") %}
Podman has this feature called Pods, which can group multiple containers and create a virtual system in which container can access to each other using localhost, instead of their name. This also helps in which port needs to be bind with host for outside access.
{% end %}

{% code(file="~/.config/containers/systemd/starrs.pod") %}
```ini
[Unit]
Description=STARRs Pod (Radarr, Sonarr, Bazarr, Prowlarr, qBittorrent, Jellyfin)

[Pod]
PodName=starrs
# you can specify which ports to bind to host
#PublishPort=7878:7878
#PublishPort=8989:8989
#PublishPort=6767:6767
#PublishPort=9696:9696
#PublishPort=9091:9091
#PublishPort=51413:51413
#PublishPort=51413:51413/udp
#PublishPort=8096:8096
#PublishPort=7359:7359/udp
#PublishPort=1900:1900/udp

# or you can make the pod network use the host network.
Network=host
```
{% end %}

{% paragraph(class="m-8") %}
And the ENV file, How can we forget that.
{% end %}

{% code(file="~/.config/containers/systemd/.env") %}
```sh
PUID=0 # set to 0 to run with podman rootless without uidmap
PGID=0 # set to 0 to run with podman rootless without uidmap
TZ=Etc/UTC
DATA=/path/to/data
```
{% end %}

{% paragraph(class="m-8") %}
The directory structure should be like this.
{% end %}

{% code() %}
```
data
├── downloads
│ ├── complete
│ │ ├── radarr
│ │ └── tv-sonarr
│ └── incomplete
├── media
│ ├── movies
│ └── tv
└── watch
```
{% end %}
