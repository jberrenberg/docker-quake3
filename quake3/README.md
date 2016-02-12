# Quake3 Dedicated Server on Alpine Linux

This image provides a simple quake3 dedicated server. It was originally based
on [Arch Linux](https://archlinux.org) but moved to [Apline
Linux](https://alpinelinux.org/). This change reduced the image size from
around 900mb to 32mb.

## Quickstart
To run a simple baseq3 dedicated server do:

``` bash
export PAK0=/path/to/pak0.pk3
docker run -it --rm -v ${PAK0}:/pak0.pk3 -p 0.0.0.0:27960:27960/udp jberrenberg/quake3
```

To run the server in the background replace the `-it --rm` with a nice name `--name quake -d`

``` bash
export PAK0=/path/to/pak0.pk3
docker run --name quake -d -v ${PAK0}:/pak0.pk3 -p 0.0.0.0:27960:27960/udp jberrenberg/quake3
```

Now to stop the server again use `docker stop`

```bash
docker stop quake3
```

and to remove it use `rm`

```bash
docker rm quake3
```

## Real Setup

Since we will add additional maps and configurations to our server, we will
create a data container for these files. Use following command to create a
empty busybox container named quake3-data

```bash
docker run -v /home/ioq3srv/.q3a/baseq3 --name quake3-data busybox
```

Now we can copy a configuration into this container

```bash
export PAK0=/path/to/pak0.pk3
docker cp my-server.cfg quake3-data:/home/ioq3srv/.q3a/baseq3/my-server.cfg
```

or mount it and download maps from the web

```bash
docker run --rm --it --volumes-from quake3-data alpine bash
cd /home/ioq3srv/.q3a/baseq3/
wget http://exmaple.com/some_q3map.zip
unzip some_q3map.zip
```

We can also cp the pak0 into the data container, then we no longer need to add
it on start.

```bash
export PAK0=/path/to/pak0.pk3
docker cp ${PAK0} quake3-data:/home/ioq3srv/.q3a/baseq3/
```

to run the quake server with the custom configuration use

```bash
docker run \
-it \
--rm \
-v ${PAK0}:/pak0.pk3 \
-p 0.0.0.0:27960:27960/udp \
--volumes-from quake3-data \
jberrenberg/quake3 +exec my-server.cfg
```

### Autostart via systemd

To run the quake3 server as a system service via systemd drop this unit file
into `/etc/systemd/system/` called `quake3.service`

```bash
[Unit]
Description=quake3 server
After=docker.service
Requires=docker.service

[Service]
Restart=always
RestartSec=60
ExecStartPre=/usr/bin/docker pull jberrenberg/quake3:latest
ExecStart=/bin/bash -c "/usr/bin/docker start quake3 || /usr/bin/docker \
        run --rm \
        --name quake3 \
        --volumes-from quake3-data \
		-p 0.0.0.0:27960:27960/udp \
        jberrenberg/quake3:latest"
ExecStop=/bin/bash -c '/usr/bin/docker stop quake3'

[Install]
WantedBy=multi-user.target
```

Before we can start the quake3 sever we will reload systemd
```bash
systemctl daemon-reload
```
start the service
```bash
systemctl start quake3
```
enable autostart on boot
```bash
systemctl enable quake3
```

