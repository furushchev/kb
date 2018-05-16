# Docker

## Change the location of docker images / data

Docker images are located under `/var/lib/docker` on Ubuntu by default.
To change the directory, it is required to pass `-g` option to docker daemon process.
To do it, edit `DOCKER_OPTS` environment variables in `/etc/default/docker`.

```diff
- DOCKER_OPTS=
+ DOCKER_OPTS="-g /path/to/docker-image"
```

And then restart docker daemon process:

```bash
# Ubuntu 14.04
sudo restart docker
```

```bash
# Ubuntu 16.04
sudo systemctl stop docker.service
sudo systemctl start docker.service
```

## Launch Private Registry Server

### Create private registry

Instead of using Docker official registry, we can also create and run our own docker registory that docker images are stored and managed.
The private registry can also be run as one docker image.

- Images stored in private registry in the host machine: `/media/docker/registry`
- Port of docker registry server: `5000`
- Port of docker registry frontend: `8080`

Before run a registry server, set the appropriate hostname to your server.

To run registry server, run the commands below:

```bash
mkdir -p /media/docker/registry
chmod -R 0777 /media/docker/registry
docker run -d -p 5000:5000 -v /media/docker/registry:/var/lib/registry registry
```

And then also run the frontend for visualize images stored in the registry server.

```bash
docker run -d -e ENV_DOCKER_REGISTRY_HOST=<hostname> -e ENV_DOCKER_REGISTRY_PORT=5000 -p 8080:80 konradkleine/docker-registry-frontend:v2
```

And you can see the frontend.

![docker](https://user-images.githubusercontent.com/1901008/40100258-479c803a-591e-11e8-84f4-31a2cf35b33c.png)

### Push own image to created private registry

Docker determines the destination of pushed image by its tag as followings: `<host>:<port>/<image>:<version>`.
If `<host>:<port>/` is abbreviated, docker pushes its image to the official docker hub.
So, all we need to do is to tag images.

```bash
docker pull osrf/ros:kinetic-desktop
docker tag osrf/ros:kinetic-desktop <host>:5000/osrf/ros:kinetic-desktop
docker push <host>:5000/osrf/ros:kinetic-desktop
```

This pushes images to the registry server located on `<host>:5000`.
