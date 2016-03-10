# Kerberos

Install the following packages:

  - krb5-user
  - krb5-config

The kerberos servers for the ACPUB.DUKE.EDU realm are:

  - kaserv1.acpub.duke.edu
  - kaserv2.acpub.duke.edu
  - kaserv3.acpub.duke.edu

The admin server is:

  - kaserv1.acpub.duke.edu

Additionally, if anything has old, single-DES credentials, the the following
line the the libdefaults section of /etc/krb5.conf:

  allow_weak_crypto = true

You should now be able to get Kerberos tickets with `kinit`

# NFS configuration

We will be using NFSv4 with Kerberos authentication, so we do not need statd to be running, but do need gssd.

To turn off statd on Ubuntu 14.04, edit /etc/default/nfs-common and set:

  NEED_STATD=no


To turn on gssd on Ubuntu 14.04, edit /etc/default/nfs-common and set:

  NEED_GSSD=yes

