# Quake3 OSP Server on Alpine Linux

## Quickstart
To run a simple osp dedicated server do:

``` bash
export PAK0=/path/to/pak0.pk3
docker run -it --rm -v ${PAK0}:/pak0.pk3 -p 0.0.0.0:27960:27960/udp jberrenberg/quake3-osp
```

## Detailed Documentation

For a more detailed documentation have a look at the [quake3 base image](https://hub.docker.com/r/jberrenberg/quake3/) [README](https://github.com/jberrenberg/docker-quake3/blob/master/quake3/README.md). Almost everything described there applies here, you just have to replace quake3 with quake3-osp.


