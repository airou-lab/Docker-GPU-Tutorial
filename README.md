# Docker_tutorial
Docker Tutorial for creating a GPU-enabled docker container from a git project that does not have a Docker Image. 

## Downloading a base Image (Optional)

```
$ sudo docker pull nvidia/cuda:11.1.1-devel-ubuntu20.04
```

Use 'sudo' every time if you need a GPU enabled container. Without sudo the final command won't work and the container may not be accessible from root.

See the [nvidia gitlab](https://gitlab.com/nvidia/container-images/cuda/blob/master/doc/supported-tags.md) to get the right version for you.

You can also directly do the next step, but downloading the base image for your project first ensures it works before creating a new image.

## Creating a Dockerfile

Now that the image exists locally, we can make a Dockerfile to create a new Docker image, adding for example Python, Pip, wget, and mini-conda.

Create a Dockerfile in a folder in your documents.
```
$ cd ~/Documents
$ mkdir Docker
$ cd Docker
$ gedit Dockerfile
```

Once this is done, let's add things to our Dockerfile :
```
# Pulling cuda image
FROM nvidia/cuda:11.1.1-devel-ubuntu20.04

# Making sure the following command are started from the image's root
WORKDIR /

# Installing base utilities (python3, pip, wget)
RUN apt-get update \
    && apt-get install -y build-essential \
    && apt-get install -y python3-pip python3-dev \
    && apt-get install -y wget \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install miniconda
ENV CONDA_DIR /opt/conda
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda

# Put conda in path so we can use conda activate
ENV PATH=$CONDA_DIR/bin:$PATH

# Moving to /Project directory
WORKDIR /home/Project
```
The final line will create the 'Project' directory in the Docker image and the command line of the containers will start in this folder.

## Building the image

To use the Dockerfile to create an image, from the ~/Documents/Docker folder:
```
$ sudo docker build -t <image_name>:<image_tag> .
```
You now have an image from your Dockerfile.

We can make sure by running:
```
$ sudo docker image ls
```

You should see your docker image, with its tag, ID, date, and size.

### Creating a container mounted on a git repository
If this hasn't been done before, clone your git repository 
```
$ cd ~/Documents
$ git clone <YOUR_GIT_REPO>
```

To create a container mounted on this git repository (that we'll call 'Project'):
```
$ sudo docker run --name <container_name> -v /home/user/Documents/Project:/home/Project --gpus all -it <image_name>:<image_tag>
```
The '/home/user/Documents/Project' path corresponds to your git repository.

The '/home/Project' is your container working environment.

Every change made to one folder will affect the other.

The -it will put you in a shell environment once the container is running.
From there you can use `ls` and see your git repository's files and folders to make sure the container is mounted properly.

To see your containers:
```
$ sudo docker ps -a
```
-a will show every (running and stopped) containers.

Note that the conda environment may require you to exit and restart your container. You may want to try this if you get an error like: "CondaError: Run 'conda init' before 'conda activate'"

### Start and stop a container
To exit the shell environment, type `exit` or do CTRL+D.
This will automatically stop the container.

To start a container and get directly in a shell:
```
$ sudo docker start <container_name> -i
```

To stop a container:
```
$ sudo docker stop <container_name>
```
---

Congratulations, you reached the end of this tutorial !







