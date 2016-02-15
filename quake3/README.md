# Quake3 Server on Alpine Linux

This image provides a simple quake3 dedicated server. It was originally based on [Arch Linux](https://archlinux.org) but moved to [Apline Linux](https://alpinelinux.org/). This change reduced the image size from around 900mb to 32mb.

## Quickstart
To run a simple baseq3 dedicated server do:

``` bash
export PAK0=/path/to/pak0.pk3
docker run -it --rm -v ${PAK0}:/pak0.pk3 \
-p 0.0.0.0:27960:27960/udp jberrenberg/quake3
```

To run the server in the background replace the `-it --rm` with a nice name `--name quake -d`

``` bash
export PAK0=/path/to/pak0.pk3
docker run --name quake -d -v ${PAK0}:/pak0.pk3 \
-p 0.0.0.0:27960:27960/udp jberrenberg/quake3
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

Since we will add additional maps and configurations to our server, we will create a data container for these files. Use following command to create a empty busybox container named quake3-data

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

### Troubleshoot
#### SELinux

When running docker on centos with selinux in permissive mode, you might encounter some problems. Since it bad practice just to disable SELinux or run the docker container with `--privileged` we will also cover the steps how to do it the right way. This is a little out of scope but it can't harm to cover these steps here.

The error:
```bash
Loading vm file vm/qagame.qvm...
File "vm/qagame.qvm" found at "/home/ioq3srv/ioquake3/osp"
----- Server Shutdown (Server fatal crashed: VM_CompileX86: mprotect failed) -----
---------------------------
VM_CompileX86: mprotect failed
```

First we will have a look at the audit logs

```bash
sudo grep -e ioq  /var/log/audit/audit.log |grep avc |tail -n 1
type=AVC msg=audit(3333333333.222:111111): avc:  denied  { execute } for pid=23092 comm="ioq3ded.x86_64" path=20000000000000000000000000011111111111 dev="tmpfs" ino=1111111 scontext=system_u:system_r:svirt_lxc_net_t:s0:3333,5555 tcontext=system_u:object_r:tmpfs_t:s0 tclass=file
```

Now we will use this line to create a selinux module

```bash
sudo grep -e ioq  /var/log/audit/audit.log |grep avc |tail -n 1 | audit2allow -M quake3
```

This will create a `quake3.pp` and `quake3.te` file. Using `semodule -i quake3.pp` we can load this custom module. To check if the loading was successful use `semodule -l |grep quake3`. Now selinux should no longer interfere with running the quake3 docker container.

