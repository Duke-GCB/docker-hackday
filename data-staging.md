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


