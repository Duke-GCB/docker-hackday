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

