# GCB Informatics Docker Hackday

We are holding a Docker hack day (basically a mini hackathon) on Thursday, March 10, 2016. The idea is to hack on code, infrastructure, documentation, and use cases, not as a Docker training session, but as a chance to build something that uses Docker.

## Preparation

To prepare for the hack day, you should (at a minimum):

1. Be able to run a docker container (locally or on a host where you've installed docker)
2. Have a docker hub account https://hub.docker.com/login/
3. Be able to build a docker image from a Dockerfile https://docs.docker.com/linux/
4. Be able to push an image to docker hub https://docs.docker.com/engine/reference/commandline/push/
5. Be able to pull an existing image and run it with a simple use case (e.g. docker run apache to serve a web site)
6. Have a general understanding of docker concepts (containers vs images, volumes, networking) and how they differ from virtual machines
7. Understand advantages of containers (e.g. over VMs or bare metal) and bring ideas of use cases or areas to explore.

The tutorials at docker.com ([Mac](https://docs.docker.com/mac/), [Linux](https://docs.docker.com/linux/), [Windows](https://docs.docker.com/windows/)) will cover most of these (1-4) and get you on the path to figuring out 5-7.

Additional resources:

- The documentation at docs.docker.com is great for working knowledge and simple examples.
- If you prefer video tutorials, these are recommended too: https://www.docker.com/products/resources/video-tutorials
- [Some presentations](https://rc.duke.edu/duke-docker-day-videos/) from last year's Duke Docker Day:
 - [Chris Collins](https://rc.duke.edu/chris-collins-duke-docker-day-2/) - Great examples of using docker to administer systems without installing a bunch of extra software
 - [Mark McCahill](https://rc.duke.edu/mark-mccahill-duke-docker-day-2/) - On RStudio and other vm-manage docker images

## Resources

* We have a VM on [Research Toolkits](https://rtoolkits.web.duke.edu/projects/15), with docker 1.10.2 installed. It’s got 8 CPUs and 32GB RAM. Create your own local account.
* Also created a CIFS scratch space (100GB), named docker-hackday.

## Who

* [Dan Leehr], [Dan Somers], [Darren Boss], [John Bradley], [Hilmar Lapp] (all GCB Informatics)
* [Jack Hill] (Duke Libraries)

## Targets

* [Data staging](data-staging.md): encapsulating storage in a container so that the provisioning protocol is abstracted out. (Dan L, Jack)
* [Database Backup](database-backup.md): Container that can connect to a running database container and obtain and store a backup. (Dan S, Darren, John)
* [Trusted docker repositories, and signed images](trusted-images.md) (Hilmar)

[Dan Leehr]: http://github.com/dleehr
[Dan Somers]: http://github.com/dansomers
[Darren Boss]: http://github.com/netscruff
[John Bradley]: http://github.com/johnbradley
[Hilmar Lapp]: http://github.com/hlapp
[Jack Hill]: http://github.com/jackhill
