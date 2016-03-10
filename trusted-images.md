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

[Docker Trusted Registry]: https://docs.docker.com/docker-trusted-registry/overview/
[Docker Trusted Registry, Technical Brief]: https://www.docker.com/sites/default/files/Docker%20Trusted%20Registry.pdf
[AppArmor profiles]: https://docs.docker.com/engine/security/apparmor/
[Seccomp profiles]: https://docs.docker.com/engine/security/seccomp/
