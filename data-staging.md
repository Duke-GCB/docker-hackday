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

docker-volume-netshare is a process that runs in the background. According to the documentation, with docker 1.9 and later, it is run with only one argument (cifs or nfs). And the credentials are passed at time of volume creation.

However, this did not work in our testing, the username and password were required here.

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

#### NetRC files

1. Create a .netrc file in your home directory, e

        //.netrc
        machine oit-nas-nb12pub.oit.duke.edu
            username  dcl9
            password  **********

2. Then when running `docker-volume-netshare cifs`, you need not include the username and password

#### kerberos

Tried using kerberos security, which is supported by CIFS. However it seems the storage isn't configured or we don't know what realm to use...

sudo apt-get install krb-user krb5-config

    Realm: ACPUB.DUKE.EDU
    Servers: kaserv1.acpub.duke.edu kaserv2.acpub.duke.edu kaserv3.acpub.duke.edu
    Admin: kaserv1.acpub.duke.edu

Trying to obtain a ticket and mount with kerberized, not having much luck

    $ sudo kinit dcl9@ACPUB.DUKE.EDU
    $ sudo kinit dcl9@WIN.DUKE.EDU
    $ kinit dcl9@WIN.DUKE.EDU
    $ kinit dcl9@ACPUB.DUKE.EDU

Obtains a ticket and puts it in /tmp, but all attempts at trying mount with sec=krb5 fail with `Required key not available`

    $ sudo mount -t cifs -o sec=krb5,user=dcl9 //oit-nas-nb12pub.oit.duke.edu/scratch/docker-hackday /mnt/krbmount --verbose
    mount.cifs kernel mount options: ip=152.3.96.9,unc=\\oit-nas-nb12pub.oit.duke.edu\scratch,sec=krb5,user=dcl9,prefixpath=docker-hackday,pass=********
    mount error(126): Required key not available
    Refer to the mount.cifs(8) manual page (e.g. man mount.cifs)

Tried debugging with smbclient, but it can't seem to authenticate no matter what. Though it did provide some suggestive error messages

    smbclient -k //oit-nas-nb12pub.oit.duke.edu/scratch/docker-hackday -v
    ads_krb5_mk_req: smb_krb5_get_credentials failed for cifs/oit-nas-nb12pub.oit.duke.edu@OIT.DUKE.EDU (Server not found in Kerberos database)
    cli_session_setup_kerberos: spnego_gen_krb5_negTokenInit failed: Server not found in Kerberos database
    session setup failed: NT_STATUS_UNSUCCESSFUL

#### Domain joining

#### Next steps

- ~~cleanup credential storage~~
- install docker-volume-netshare as a service
- NFS
- kerberos
- permissions issues
- automate configuration and installation - [ansible](https://github.com/Duke-GCB/gcb-ansible/tree/docker-volumes)

