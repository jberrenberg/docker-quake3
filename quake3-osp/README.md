# quake3-osp

## Quickstart
To run a simple osp dedicated server do:

``` bash
export PAK0=/path/to/pak0.pk3
docker run -it --rm -v ${PAK0}:/pak0.pk3 -p 0.0.0.0:27960:27960/udp jberrenberg/quake3-osp
```
