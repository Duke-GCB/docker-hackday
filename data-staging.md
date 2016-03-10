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

### Creating a volume

Then when we create the volume there are arguments for username and password. This was an attempt to mount as dcl9 and hope for a password prompt, but it never came.

    $ docker volume create -d cifs --name oit-nas-nb12pub.oit.duke.edu/scratch/docker-hackday --opt username=dcl9
    oit-nas-nb12pub.oit.duke.edu/scratch/docker-hackday

Now I'll use the gcbservice account since I don't mind putting the password on a CLI.

This is trying to mount with guest

`DEBU[0142] Executing: mount -t cifs -o guest,rw //oit-nas-nb12pub.oit.duke.edu/scratch/docker-hackday /var/lib/docker-volumes/netshare/cifs/oit-nas-nb12pub.oit.duke.edu/scratch/docker-hackday`

Current documentation for docker-volume-netshare suggests that the credentials should be supplied when creating the volume, however this did not work. Instead it always tried to mount as `-o guest,rw`

Did not work. Instead need to provide user and pass to the docker-volume-netshare process

#### Working State!

1. Run docker-volume-netshare (in an interactive terminal)
  - `sudo docker-volume-netshare cifs --username=gcbservice --password=**** --verbose`
  - Note: special characters in the password had to be escaped with 3 backslashes. So a `$` becomes `\\\$`
  - We should probably use .netrc file for this instead, and see about running this process in background somehow
2. Create a docker volume, the name is translated from the CIFS share:
  - `docker volume create -d cifs --name oit-nas-nb12pub.oit.duke.edu/scratch/docker-hackday`
3. With the volume created, run a container
  - `docker run -it -v oit-nas-nb12pub.oit.duke.edu/scratch/docker-hackday:/docker-hackday ubuntu bash`

#### Next steps

- cleanup credential storage
- install docker-volume-netshare as a service
- NFS
- kerberos
- permissions issues
