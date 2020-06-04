debian-ssh
==========

Simple Debian/Ubuntu Docker images with *passwordless* SSH access and a regular user
with `sudo` rights

Usage
--------------

```sh
docker build -t [IMAGE NAME] --build-arg BASE_IMAGE=[YOUR DEBIAN BASED IMAGE] .
```

Tags (and their corresponding official base images)
----



### Debian

- `latest` -> `debian:latest`
- `jessie` -> `debian:jessie`
- `wheezy` -> `debian:wheezy`
- `squeeze` -> `debian:squeeze`

### Ubuntu

- `ubuntu` -> `ubuntu:latest`
- `vivid` -> `ubuntu:vivid`
- `utopic` -> `ubuntu:utopic`
- `trusty` -> `ubuntu:trusty`
- `precise` -> `ubuntu:precise`


Using
-----

The images are built by [Docker hub](https://registry.hub.docker.com/u/krlmlr/debian-ssh/).
Each Debian release corresponds to a tag.  To run an SSH daemon in a new Debian "wheezy"
container:

    docker run -d -p 2222:22 -e SSH_KEY="$(cat ~/.ssh/id_rsa.pub)" [name]
    docker run -d -p 2222:22 -e SSH_KEY="ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDXbCpJu2UesNS9jo+ydwihdaeNcIKNNROehTNQsVC+vRBMA/EmHWTpuIYp+3/niKwF8NCcD16g7rxt0Xa+dyMvVJHfYPZU5+nvfpG0Zf0cKyjHpRoiJvfUShcJZdISaq4SASa8Zyvc7pyhFACznkATgNCgoIollGuXS2kGAKwpb0eETYzjp5QDa2WTC8rup5lViguboVpAzYJfzkpu1IwbmsdjygZkcZDxBRqwUEXPHEuKV8VjJuWZE9n6tNWj7Y5j1jp88wJGX9+UwxrF1SyRdxnlPtDn9L2hPTY6oqTYJAoZuxHC97JbTi1uENlkRwU8uWHnruiKMRI+dWtDvep+mjoRFapyJOtP3b+EFA+KHAIXetYqaxmybs0DH8C/jqHIcORuJw0Kghg3bXQgHjaIIpHiMWU8FEvqHS6ridybN9prVHgsLzOZdkp1k6D2E9z9WSfW6AcoQW6sEaTj0bY5N1XrTvcVR+v7ktQPJTcRV11r5W8USx1vdbJrTYfpNoNsEDtblazK57x106vfbDh/lKkn+Uv9Ur8kEXcNXNZ1LU5VlOkeD6r48INx5/AAVTBUcMDUe9htNI/WEDQJtx8Ab86hyYvG88KlZxo970I9UhJZxFtdLZlE6RLCVW143gz5zufKGRVYGqg6qyl4Rwa0cJxCarLg11iCH1zzhM2neQ== zoravur.s@gmail.com" [name]

This requires a public key in `~/.ssh/id_rsa.pub`.

Two users exist in the container: `root` (superuser) and `docker` (a regular user
with passwordless `sudo`). SSH access using your key will be allowed for both
`root` and `docker` users.
To connect to this container as root:

    ssh -p 2222 root@localhost

To connect to this container as regular user:

    ssh -p 2222 docker@localhost

Change `2222` to any local port number of your choice.


Enhancing
---------

Each Debian release corresponds to a Git branch, the branches differ only by
the `FROM` element in the `Dockerfile`.

To create the image `krlmlr/debian-ssh` e.g. for Debian "jessie":

    git checkout jessie
    make build

Use `make rebuild` to pull the base image and rebuild without caching.


Testing
-------

Execute `make test` to create a container and fetch all environment variables
via SSH.  This requires an `.ssh/id_rsa.pub` file in your home, it will be
passed to the container via the environment variable `SSH_KEY` and installed.
The `Makefile` is configured to run the container with the limited `docker`
account, this user is allowed to run `sudo` without requiring a password.
The SSH daemon will be always run with root access.  The `debug-*` targets
can help troubleshooting any issues you might encounter.
