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

The Keycloak version in the `master` branch does not always match the most
recent version of the official Keycloak image in Docker Hub. For example,
`12.0.1` vs `12.0.4`. Switch to the `latest` branch as the `Dockerfile` in that
branch should match the version and Dockerfile in Docker Hub.

```sh
git branch latest
```

## Build the image using Docker

The Docker commands below have been run on the Raspberry Pi itself as Docker
will build the image for the platform that it is being run on, i.e. arm64 in
this case. You can also use the new Docker Buildx that provides the means to
build images for multiple architectures, see the [Leverage multi-CPU
architecture support] Docker docs for more info. This was one approach I took
initially when building this on my Mac laptop, I'll add some notes about this
later.

```sh
docker build -t sleighzy/keycloak:12.0.4-arm64 .
```

Run the container to ensure that it starts up as expected and that it runs to
completion and is accessible.

```sh
docker run --rm sleighzy/keycloak:12.0.4-arm64
```

Tag the image as latest and push both tags to Docker Hub. Profit.

```sh
docker tag sleighzy/keycloak:12.0.4-arm64 sleighzy/keycloak:latest
docker push sleighzy/keycloak:12.0.4-arm64
docker push sleighzy/keycloak:latest
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
[sleighzy/keycloak]: https://hub.docker.com/repository/docker/sleighzy/keycloak
