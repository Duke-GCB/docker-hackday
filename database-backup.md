#  Goal

Given a database mariadb/postgres running in a docker container back it up to a volume.
We would like to put the command in cron without including the password so we can periodically backup the database.


## MariaDB
The mariadb container has environment variables for the MYSQL_* user/password etc.
So we were able to run this:
```
docker exec -i -t <name> sh -c 'mysqldump --password=$MYSQL_ROOT_PASSWORD --all-databases'
```
Using ```sh --c '...'``` uses the environment variables in the container we are exec'ing in.


## Postgres
### Backup using exec
So we were able to run this to backup stuff:
```
docker exec -i -t some-postgres pg_dumpall -U postgres
```

How to run postgres with passing in a directory.
This one is pretty heavy handed using /tmp/pgdata in container and host.
```
docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -e PGDATA=/tmp/pgdata -v /tmp/pgdata:/tmp/pgdata -d postgres
```

This one mounts /tmp/data2 on host and /var/lib/postgresql/data in container. PGDATA default in this images is /var/lib/postgresql/data.
```
docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -v /tmp/data2:/var/lib/postgresql/data -d postgres
```
### Backup to a volume
I created a Dockerfile and script to create an image that will backup a postgres database to the specified volume.
https://github.com/Duke-GCB/DockerPostgresBackup
