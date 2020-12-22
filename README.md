# Keycloak arm64 Docker Image

Keycloak do not provide Docker images for `arm64` platforms, only `linux/amd64`
versions are available on Docker Hub, see [jboss/keycloak] for these. I am
running a Raspberry Pi 64bit cluster and need a `linux/arm64` image.

The [sleighzy/keycloak] images on Docker Hub have been built using the
instructions below and provide the `linux/arm64` support.

Clone the official GitHub repo [keycloak/keycloak-containers] to obtain the
assets needed to build the container.

```sh
git clone https://github.com/keycloak/keycloak-containers.git
cd keycloak-containers/server
```

In my original `11.0.2-arm64` version I found that I was unable to build it
using the default Dockerfile from their repository due to being unable to access
the `registry.access.redhat.com/ubi8-minimal` image, or another base image it
depended on. This did not appear to be the case when I built my `12.0.1-arm64`
image, and the Red Hat site [Red Hat Universal Base Image 8 Minimal] showed that
as publically available and I was able to pull and use it just fine.

I have included the `Dockerfile-11.0.2` file for historical purposes should I
need to revisit this, and because it matches that 11.0.2-arm64 tag in my Docker
Hub repo. This should **NOT** be used for further builds as the official
Keycloak `Dockerfile` seems to work fine now.

## Build the image using Docker

The Docker commands below have been run on the Raspberry Pi itself as Docker
will build the image for the platform that it is being run on, i.e. arm64 in
this case. You can also use the new Docker Buildx that provides the means to
build images for multiple architectures, see the [Leverage multi-CPU
architecture support] Docker docs for more info. This was one approach I took
initially when building this on my Mac laptop, I'll add some notes about this
later.

```sh
docker build -t sleighzy/keycloak:12.0.1-arm64 .
```

Run the container to ensure that it starts up as expected and that it runs to
completion and is accessible.

```sh
docker run --rm sleighzy/keycloak:12.0.1-arm64
```

Push to Docker Hub. Profit.

```sh
docker push sleighzy/keycloak:12.0.1-arm64
```

## Building the image with BuildKit for containerd

There is also another option for building images as well. My Raspberry Pi
cluster is running the [K3s] Kubernetes distribution which runs on [containerd],
hence Docker does not need to be installed on the Raspberry Pi. See my [Building
images with BuildKit for containerd] notes for more information on this and
using `buildctl` to build these.

[building images with buildkit for containerd]:
  https://github.com/sleighzy/raspberry-pi-k3s-homelab/blob/main/k3s.md#building-images-with-buildkit-for-containerd
[containerd]: https://containerd.io/
[jboss/keycloak]: https://hub.docker.com/r/jboss/keycloak
[k3s]: https://k3s.io/
[keycloak/keycloak-containers]: https://github.com/keycloak/keycloak-containers
[leverage multi-cpu architecture support]:
  https://docs.docker.com/docker-for-mac/multi-arch/
[red hat universal base image 8 minimal]:
  https://catalog.redhat.com/software/containers/ubi8-minimal/5c64772edd19c77a158ea216?container-tabs=overview&architecture=arm64
[sleighzy/keycloak]: https://hub.docker.com/repository/docker/sleighzy/keycloak
