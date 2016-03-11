# Trusted Images

## Use-case

### Goal

Enable computing system users and maintainers to ensure that only
trusted images are run as containers.

### Motivation

Although users generally have already full root privileges on
dedicated cloud VMs (whether hosted on-premise or by an IaaS
provider), this is generally not the case for shared compute systems,
such as shared high-performance computing (HPC) clusters. Hence, while
on dedicated VMs potentially rogue Docker containers don't increase a
user's privileges to compromise the integrity of the system to the
point of irrecoverability, this is generally not the case, and
strongly undesirable on shared HPC. Yet, many of the general use-cases
for the use of Docker in scientific computing, such as containerized
software dependencies and reproducible computational workflows, are
equally applicable to shared HPC as they are to dedicated compute VMs.

How, therefore, can system administrators responsible for the
integrity of a compute system ensure that users can only run trusted
Docker images. In addition, signed containers allow stronger
provenance tracking for what computational workflows were being used
in which environment.

## Results

**TL;DR:** This isn't currently possible in Docker, and the efforts
that are underway or have reached maturity suggest that it won't be at
least for a while, and possibly never. The support for trusted images
that exists seems designed for the end-user to _enable_ use of trusted
images, not for system administrators to _enforce_ it or to protect
system integrity, as each feature can be trivially disabled or
circumvented by the Docker user.

That said, trusted images could still be useful for us for stronger
provenance on scientific workflow components.

### Trusted registry

One way to enable trusted images is through a trusted image
registry. The trusted registry would provide control over who can
push, and who can pull which images.

As part of their Containers-as-a-service infrastructure, Docker
provides a [Docker Trusted Registry] service (also see
[Docker Trusted Registry, Technical Brief]). The service can be
deployed on premise or in the cloud. However, the service requires a
paid subscription and use of the commercially supported Docker Engine
version with as-of-yet still to be fully published pricing. Even if
one ran such a service, it would not alter or restrict the ability of
the Docker client to pull an image from _any_ repository, making use
of the trusted one opt-in.

### Signed images

Similar to code-signing as used for Java libraries, cryptographically
signed Docker images would provide a way for trusting images (by
accepting the signing key, similar to accepting ssh host keys in ssh
connections), and for validating that an image was signed by a trusted
party. This protects the end-user from possibly tampered with
images. It also enables strong provenance, by providing strong
evidence that an image used in a workflow is indeed from the purported
source, and that when pulled again, it is indeed the same as was used
previously.

Since August 2015, [Docker supports "Content Trust"]. This works by
signing _image tags_, not the images themselves. Content trust can be
enabled for the Docker client by setting the environment variable
`DOCKER_CONTENT_TRUST`, like so:

```sh
$ export DOCKER_CONTENT_TRUST=1
```

With this environment set, the Docker commands `push`, `build`,
`create`, `pull`, and `run` require (or push or create) signed images
with signatures that are accepted as trusted (like one would accept an
ssh host key when connecting for the first time). More precisely, they
require images with a _signed tag_. Not all tags of a Docker image
repository need to be signed, and a tag (such as _latest_) may be
signed at one point and published unsigned at another, providing the
publisher of an image to signal reliability or other attributes as a
convention by choosing which tags are and which are not signed.

Signed tags could thus convey considerable benefits to the end user by
enabling strong provenance. However, any attempt to enforce content
trust mode can be trivially circumvented by the end-user. The Docker
client accepts a command-line argument (`--disable-content-trust`)
that disables it, and pulling an image by specifying its full SHA256
hash always succeeds regardless of whether content trust is enabled or
not.

### Security profiles

Linux supports several technologies for secure computing mode
profiles. Specifically, there is support for [AppArmor profiles], and
for [Seccomp profiles].

Rather than establishing (and requiring) trust for images, this
restricts the operations that a container can do on the host. For
example, containers can be denied access to certain parts of the
host's filesystem. System administrators could therefore provide a
default policy that tightens up the access to the host that containers
can at most gain.

However, the Docker client allows overriding the default policy with a
different one of the user's choosing (option `--security-opt`), and
thus a default restrictive policy can be trivially disabled. Also,
this method does not yield any of the end-user benefits of trusted
images, such as strong provenance.

## Further resources and background

* Docker's content trust mode needs an additional software component,
  named "[Notary]", released by Docker as open source. Notary itself
  extends the [The Update Framework] (TUF), an open source system for
  securely distributing software updates to systems. TUF in turn is
  based on work funded by NSF, discussed in a [2010 CCS paper].
* Once a user is privileged to create and run Docker containers on a
  machine, it is rather impossible to force them to use *only* signed
  images, unless Docker provided a means of restricting them to a
  selected set of registries that only provide signed images. Such a
  means was provided by Redhat ([Docker in RHEL 7.1]), but the
  extension was [refused by the upstream Docker maintainers]. Instead,
  the maintainers wished to use [their own proposal], which was closed
  for re-examination in January 2016.

## Acknowledgements

* Victor J. Orlikowski for the write-up that surfaced most of the
  further resources.

[Docker Trusted Registry]: https://docs.docker.com/docker-trusted-registry/overview/
[Docker Trusted Registry, Technical Brief]: https://www.docker.com/sites/default/files/Docker%20Trusted%20Registry.pdf
[AppArmor profiles]: https://docs.docker.com/engine/security/apparmor/
[Seccomp profiles]: https://docs.docker.com/engine/security/seccomp/
[Docker supports "Content Trust"]: https://docs.docker.com/engine/security/trust/content_trust/
[Notary]: https://github.com/docker/notary
[The Update Framework]: https://theupdateframework.github.io
[2010 CCS paper]: http://freehaven.net/~arma/tuf-ccs2010.pdf
[Docker in RHEL 7.1]: http://rhelblog.redhat.com/2015/04/15/understanding-the-changes-to-docker-search-and-docker-pull-in-red-hat-enterprise-linux-7-1/
[refused by the upstream Docker maintainers]: https://github.com/docker/docker/pull/13450
[their own proposal]: https://github.com/docker/distribution/pull/303
