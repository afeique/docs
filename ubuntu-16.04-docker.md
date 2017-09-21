**Created**: 17 July 2017

# Installation of Docker in Ubuntu 16.04 LTS

These instructions also apply to other distros which use Ubuntu 16.04 for base packages e.g. [Linux Mint](https://www.linuxmint.com/) 18.1.

## Docker CE Installation

### Uninstall old versions

Older versions of Docker were called `docker` or `docker-engine`. If these are installed, uninstall them:

    $ sudo apt-get purge docker docker-engine docker.io

### Add Repository

See:

* [Ubuntu 16.04 guide on DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04).

First, add the GPG key for the official Docker repository to the system:

    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

Add the Docker repository to `apt` sources.

    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable"
    
If you are on a different version of Ubuntu, you can use `$(lsb_release)` to get the specific release name:

    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

Next, update the package database with the Docker packages from the newly added repo:

    $ sudo apt-get update

Make sure to install from the Docker repo instead of the default Ubuntu 16.04 repo:

    $ apt-cache policy docker-ce
    docker-ce:
        Installed: (none)
        Candidate: 17.03.1~ce-0~ubuntu-xenial
        Version table:
             17.03.1~ce-0~ubuntu-xenial 500
                    500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
             17.03.0~ce-0~ubuntu-xenial 500
                    500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages

Notice that docker-ce is not installed, but the candidate for installation is from the Docker repository for Ubuntu 16.04. The docker-ce version number might be different.

Finally, install Docker:

    $ sudo apt-get install -y docker-ce

## Alternative: `docker-engine` Installation

This approach may be out-of-date according to the [official Docker documentation](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/), but it still works.

Begin by updating `apt` package information and setting `apt` up to
pull content from Docker's own repository:

    $ sudo apt-get update
    $ sudo apt-get install -y apt-transport-https ca-certificates

Add the new GPG key.

    $ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D

Open `/etc/apt/sources.list.d/docker.list`.

If the file doesn’t exist, create it.

Remove any existing entries.

Add an entry for Ubuntu Xenial (16.04 LTS):

    deb https://apt.dockerproject.org/repo ubuntu-xenial main

Save and close the `/etc/apt/sources.list.d/docker.list` file.
Update the `apt` package index.

    $ sudo apt-get update

Purge the old repo if it exists.

    $ sudo apt-get purge lxc-docker

Verify that `apt` is pulling from the right repository.

    $ apt-cache policy docker-engine

This should return something like:

    $ apt-cache policy docker-engine
    docker-engine:
        Installed: (none)
        Candidate: 17.05.0~ce-0~ubuntu-xenial
        Version table:
             17.05.0~ce-0~ubuntu-xenial 500
                    500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
             17.04.0~ce-0~ubuntu-xenial 500
                    500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
        
             ...

Finally, install Docker:

    $ sudo apt-get install -y docker-engine


# Using Docker

Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it's running:

    $ sudo systemctl status docker

The output should be similar to the following, showing that the service is active and running:

    Output
    ● docker.service - Docker Application Container Engine
       Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
       Active: active (running) since Sun 2016-05-01 06:53:52 CDT; 1 weeks 3 days ago
         Docs: https://docs.docker.com
     Main PID: 749 (docker)

## Commands

As of Docker 1.11.1, the complete list of available subcommands includes:

    attach    Attach to a running container
    build     Build an image from a Dockerfile
    commit    Create a new image from a container's changes
    cp        Copy files/folders between a container and the local filesystem
    create    Create a new container
    diff      Inspect changes on a container's filesystem
    events    Get real time events from the server
    exec      Run a command in a running container
    export    Export a container's filesystem as a tar archive
    history   Show the history of an image
    images    List images
    import    Import the contents from a tarball to create a filesystem image
    info      Display system-wide information
    inspect   Return low-level information on a container or image
    kill      Kill a running container
    load      Load an image from a tar archive or STDIN
    login     Log in to a Docker registry
    logout    Log out from a Docker registry
    logs      Fetch the logs of a container
    network   Manage Docker networks
    pause     Pause all processes within a container
    port      List port mappings or a specific mapping for the CONTAINER
    ps        List containers
    pull      Pull an image or a repository from a registry
    push      Push an image or a repository to a registry
    rename    Rename a container
    restart   Restart a container
    rm        Remove one or more containers
    rmi       Remove one or more images
    run       Run a command in a new container
    save      Save one or more images to a tar archive
    search    Search the Docker Hub for images
    start     Start one or more stopped containers
    stats     Display a live stream of container(s) resource usage statistics
    stop      Stop a running container
    tag       Tag an image into a repository
    top       Display the running processes of a container
    unpause   Unpause all processes within a container
    update    Update configuration of one or more containers
    version   Show the Docker version information
    volume    Manage Docker volumes
    wait      Block until a container stops, then print its exit code


To view the switches available to a specific command, type:

    $ sudo docker <subcommand> --help

To view system-wide information about Docker, use:

    $ sudo docker info

## Hello World

To download the `hello-world` image from Docker Hub, type:

    $ sudo docker run hello-world

The output, which should include the following, should indicate that Docker in working correctly:

    Hello from Docker.
    This message shows that your installation appears to be working correctly.
    ...

## Searching Images

You can search for images available on Docker Hub by using the docker command with the search subcommand. For example, to search for the Ubuntu image, type:

    $ sudo docker search ubuntu

In the OFFICIAL column, OK indicates an image built and supported by the company behind the project. Once you've identified the image that you would like to use, you can download it to your computer using the pull subcommand, like so:

    $ sudo docker pull ubuntu

## Pulling Images

### Ubuntu

Docker has an official [Ubuntu image](https://hub.docker.com/_/ubuntu/).

To download this image:

    $ sudo docker pull ubuntu

After an image has been downloaded, you may then run a container using the downloaded image with the run subcommand. If an image has not been downloaded when docker is executed with the run subcommand, the Docker client will first download the image, then run a container using it:

    $ sudo docker run ubuntu

### Jenkins

Docker has an official [Jenkins image](https://hub.docker.com/_/jenkins/).

To download this image:

    $ sudo docker pull jenkins

## Running Containers Using Images

### Background

Docker images represent read-only filesystems. They do not maintain state and are only the **starting points** for docker containers. 

A Docker container is invoked using `docker run`. This container houses **at least** one image, but usually runs multiple layered images. Images are layered using a **union filesystem**.

The Docker container additionally creates a read-write layer that serves as a buffer for any changes to the underlying union-filesystem. Whenever the user makes a change, the read-write layer is affected, but the underlying images are **not updated**. Instead, docker shows us the changes or delta at the read-write layer without changing the underlying layered images.

More information on how Docker containers work:

* [About Images, Containers, and Storage Drivers](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/)
* [DigitalOcean: Working with Docker Containers](https://www.digitalocean.com/community/tutorials/working-with-docker-containers)

Once an image is installed, you can run it using:

    $ sudo docker run <image-name>

For example, `<image-name>` can be `ubuntu` or `jenkins` if you ran `docker pull ubuntu` or `docker pull jenkins`.

You can specify `-i` and `-t` which, according to the [official Documentation](https://docs.docker.com/engine/reference/run/#foreground) have the following effects:

    -t              : Allocate a pseudo-tty
    --sig-proxy=true: Proxy all received signals to the process (non-TTY mode only)
    -i              : Keep STDIN open even if not attached

Thus, running:

    $ sudo docker run -it <image-name>

will return a root TTY to the Docker container that was created:
    
    root@d9b100f2f636:/#

**Important:** Note the container id in the command prompt. In the above example, it is `d9b100f2f636`.

Now you may run any command inside the container. For example, let's update the package database inside the container. No need to prefix any command with sudo, because you're operating inside the container with root privileges:

    # apt-get update

## Managing Images

To see the images that have been downloaded to your computer, type:

    $ sudo docker images

The output should look similar to the following:

    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    jenkins             latest              0b4d4d677a26        7 days ago          681MB
    ubuntu              latest              d355ed3537e9        9 days ago          119MB
    hello-world         latest              1815c82652c0        2 weeks ago         1.84kB

