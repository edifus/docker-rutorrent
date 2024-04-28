# [edifus/rutorrent](https://github.com/edifus/docker-rutorrent)

DEPRECIATED: No longer updated

[Rutorrent](https://github.com/Novik/ruTorrent) is a popular rtorrent client with a webui for ease of use.

[![rutorrent](https://raw.githubusercontent.com/linuxserver/docker-templates/master/linuxserver.io/img/rutorrent.jpg)](https://github.com/Novik/ruTorrent)

## Supported Architectures

| Architecture | Tag |
| :----: | --- |
| x86-64 | amd64-latest |


## Usage

Here are some example snippets to help you get started creating a container.

### docker-compose (recommended)

Compatible with docker-compose v2 schemas.

```yaml
---
version: "2.1"
services:
  rutorrent:
    image: edifus/rutorrent
    container_name: rutorrent
    environment:
      - PUID=1000
      - PGID=1000
      - DOCKER_MODS=edifus/mods:rutorrent-autodl-irssi #optional
    volumes:
      - /path/to/rutorrent/config:/config
      - /path/to/rutorrent/downloads:/downloads
    ports:
      - 80:80
      - 5000:5000
      - 51413:51413
      - 6881:6881/udp
    restart: unless-stopped
```

### docker cli

```
docker run -d \
  --name=rutorrent \
  -e PUID=1000 \
  -e PGID=1000 \
  -e DOCKER_MODS=edifus/mods:rutorrent-autodl-irssi `#optional` \
  -p 80:80 \
  -p 5000:5000 \
  -p 51413:51413 \
  -p 6881:6881/udp \
  -v /path/to/rutorrent/config:/config \
  -v /path/to/rutorrent/downloads:/downloads \
  --restart unless-stopped \
  edifus/rutorrent
```


## Parameters

Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 80` | ruTorrent Web UI |
| `-p 5000` | scgi port |
| `-p 51413` | Bit-torrent port |
| `-p 6881/udp` | Bit-torrent port |
| `-e PUID=1000` | for UserID - see below for explanation |
| `-e PGID=1000` | for GroupID - see below for explanation |
| `-e DOCKER_MODS=user/repo:tag` | enable optional mods |
| `-v /config` | where ruTorrent should store it's config files |
| `-v /downloads` | path to your downloads folder |


## Environment variables from files (Docker secrets)

You can set any environment variable from a file by using a special prepend `FILE__`.

As an example:

```
-e FILE__PASSWORD=/run/secrets/mysecretpassword
```

Will set the environment variable `PASSWORD` based on the contents of the `/run/secrets/mysecretpassword` file.


## Umask for running applications

This image provides the ability to override the default umask settings for services started within the containers using the optional `-e UMASK=022` setting.
Keep in mind umask is not chmod, it masks permissions based on it's value. Please read up [here](https://en.wikipedia.org/wiki/Umask) for more information.


## User / Group Identifiers

When using volumes (`-v` flags) permissions issues can arise between the host OS and the container, we avoid this issue by allowing you to specify the user `PUID` and group `PGID`.

Ensure any volume directories on the host are owned by the same user you specify and any permissions issues will vanish like magic.

In this instance `PUID=1000` and `PGID=1000`, to find yours use `id user` as below:

```
  $ id username
    uid=1000(dockeruser) gid=1000(dockergroup) groups=1000(dockergroup)
```


## Application Setup

Webui can be found at `<your-ip>:80` , configuration files for rtorrent are in /config/rtorrent, php in config/php and for the webui in /config/rutorrent/settings.

`Settings, changed by the user through the "Settings" panel in ruTorrent, are valid until rtorrent restart. After which all settings will be set according to the rtorrent config file (/config/rtorrent/rtorrent.rc),this is a limitation of the actual apps themselves.`

** Important note for unraid users or those running services such as a webserver on port 80, change port 80 assignment **

`** It should also be noted that this container when run will create subfolders ,completed, incoming and watched in the /downloads volume.**`

** The Port Assignments and configuration folder structure has been changed from the previous ubuntu based versions of this container and we recommend a clean install **

Umask can be set in the /config/rtorrent/rtorrent.rc file by changing value in `system.umask.set`

If you are seeing this error `Caught internal_error: 'DhtRouter::get_tracker did not actually insert tracker.'.` , a possible fix is to disable dht in `/config/rtorrent/rtorrent.rc` by changing the following values.

```shell
dht.mode.set = disable
protocol.pex.set = no
```

If after updating you see an error about connecting to rtorrent in the webui,
remove or comment out these lines in /config/rtorrent/rtorrent.rc ,whatever value is set, yes or no.
Just setting them to no will still cause the error..

```
trackers.use_udp.set = yes
protocol.pex.set = no
```
To add plugins, create a `plugins` folder in your /config directory and add your plugin folders. Ensure proper user and group ownership is set once you add the directories.
To add themes, create a themes folder in your /config directory and add your theme folders. Ensure proper user and group ownership is set once you add the directories.


## Docker Mods
* [autodl-irssi](https://github.com/edifus/docker-mods/tree/rutorrent-autodl-irssi)
  * Adds [irssi](https://irssi.org/), [autodl-irssi](https://github.com/autodl-community/autodl-irssi) and [autodl-irssi ruTorrent plug-in](https://github.com/autodl-community/autodl-rutorrent)
* [quickbox-theme](https://github.com/edifus/docker-mods/tree/rutorrent-quickbox-theme)
  * Adds club-QuickBox theme


## Support Info
* Shell access whilst the container is running: `docker exec -it rutorrent /bin/bash`
* To monitor the logs of the container in realtime: `docker logs -f rutorrent`


## Updating Info

Below are the instructions for updating containers:

### Via Docker Compose
* Update all images: `docker-compose pull`
  * or update a single image: `docker-compose pull rutorrent`
* Let compose update all containers as necessary: `docker-compose up -d`
  * or update a single container: `docker-compose up -d rutorrent`
* You can also remove the old dangling images: `docker image prune`

### Via Docker Run
* Update the image: `docker pull ghcr.io/edifus/rutorrent`
* Stop the running container: `docker stop rutorrent`
* Delete the container: `docker rm rutorrent`
* Recreate a new container with the same docker run parameters as instructed above (if mapped correctly to a host folder, your `/config` folder and settings will be preserved)
* You can also remove the old dangling images: `docker image prune`

### Via Watchtower auto-updater (only use if you don't remember the original parameters)
* Pull the latest image at its tag and replace it with the same env variables in one run:
  ```
  docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower --run-once rutorrent
  ```
* You can also remove the old dangling images: `docker image prune`

### Image Update Notifications - Diun (Docker Image Update Notifier)
* Recommended to use [Diun](https://crazymax.dev/diun/) for update notifications. Other tools that automatically update containers unattended are not recommended.


## Building locally

If you want to make local modifications to these images for development purposes or just to customize the logic:
```
git clone https://github.com/edifus/docker-rutorrent.git
cd docker-rutorrent
docker build  --no-cache --pull -t edifus/rutorrent:test .
```


## Versions

* **25.04.21:** - Forked from linuxserver.io - continue mod support
* **25.03.21:** - Deprecated. Please migrate to [crazy-max's image](https://github.com/crazy-max/docker-rtorrent-rutorrent).
* **02.06.20:** - Rebasing to alpine 3.12.
* **29.02.20:** - Update readme for pex/dht.
* **28.06.19:** - Rebasing to alpine 3.10.
* **20.05.19:** - Shift to building from official releases instead of commits.
* **13.05.19:** - Add libffi and openssl.
* **07.05.19:** - Add cloudscraper pip package.
* **11.04.19:** - Fix warnings in webui by adding python3, procps and pip packages.
* **23.03.19:** - Switching to new Base images, shift to arm32v7 tag.
* **22.02.19:** - Rebasing to alpine 3.9.
* **03.11.18:** - Add pipeline and multi arch logic to repo.
* **27.08.18:** - Add bind tools package.
* **22.08.18:** - Rebase to alpine 3.8.
* **08.12.17:** - Rebase to alpine 3.7, add sox package.
* **28.10.17:** - Mediainfo moved from testing to community repo.
* **09.10.17:** - Use repo version of mediainfo to shorten build time.
* **28.05.17:** - Fix permissions on secondary temp folder of nginx.
* **26.05.17:** - Rebase to alpine 3.6.
* **03.05.17:** - Fix log permissions.
* **18.03.17:** - Note in readme about disabling dht in some circumstances.
* **24.02.17:** - Patch a source file to quash rss https bug.
* **29.01.17:** - Rebase to alpine 3.5.
* **20.11.16:** - Add php7-mbstring package, bump mediainfo to 0.7.90.
* **14.10.16:** - Add version layer information.
* **04.10.16:** - Remove redundant sessions folder.
* **30.09.16:** - Fix umask.
* **21.09.16:** - Bump mediainfo, reorg dockerfile, add full wget package.
* **09.09.16:** - Add layer badges to README.
* **28.08.16:** - Add badges to README, bump mediainfo version to 0.7.87.
* **07.08.16:** - Perms fix on nginx tmp folder, also exposed php.ini for editing by use in /config/php.
* **26.07.16:** - Rebase to alpine.
* **08.03.16:** - Initial Release.

