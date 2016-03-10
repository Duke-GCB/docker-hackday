# Data Staging

The goal of this target is to produce practices and documentation for accessing data from network shares in docker containers, without hard-coding.

We suppose this can be made possible with docker volumes, and plugins such as [docker-volume-netshare](https://github.com/gondor/docker-volume-netshare)

General sketch

1. Install volume plugin
2. Create a volume from CIFS (authenticated as service account)
3. Create a volume from NFS (unknown authentication)
4. Run a container connecting the volume and accesses the files
5. Run a container as a non-root user and sort out any issues with file ownership and permissions
6. Kerberize?!


## Steps

### Installing the volume plugin

    sudo apt-get install -y nfs-common
    wget https://dl.bintray.com//content/pacesys/docker/docker-volume-netshare_0.11_amd64.deb?direct
    sudo dpkg -i docker-volume-netshare_0.11_amd64.deb



### Editing configuration

- `/etc/default/docker-volume-netshare`

Has mount options for NFS in config file, including CIFS user and password options

### Starting docker-volume-netshare

https://github.com/gondor/docker-volume-netshare#docker-version-190

docker-volume-netshare is a process that runs in the background. As of docker 1.9 and later, it is run with no arguments.

Then when you create a volume (`docker volume create` or `docker run -v `), you specify the host address and credentials

- `sudo docker-volume-netshare cifs` - leave it running in one window
