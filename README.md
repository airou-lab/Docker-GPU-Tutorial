# Docker_tutorial
Docker Tutorial for creating a docker container from a git that does not have a Docker Image
This tutorial shows how to create a GPU-enabled container 

## Downloading a base Image
```
$ sudo docker pull nvidia/cuda:11.1.1-devel-ubuntu20.04
```
Use 'sudo' every time if you need a GPU enabled container. Without sudo the final command won't work and the container may not be accessible from root.

See [the nvidia gitlab](https://gitlab.com/nvidia/container-images/cuda/blob/master/doc/supported-tags.md) to get the right version for you
