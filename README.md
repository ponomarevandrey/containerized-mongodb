# Containerized MongoDB

* runs database init script from `mongodb/initdb.d/` (creates new user and database using environment variables)
* sets custom config file (enables auth)

## Starting container from scratch

If you want to start the container from scratch, first you need manually clean everything up left after the old stopped container.

```shell
docker-compose down
docker volume prune
docker image rm mongodb_mongodb

# If needed also delete the mongo image
# docker image rm mongo

# Now, manually remove the volume data. 
# It's important to delete both db data and logs, otherwise you'll get an error on `docker-compose up`:

sudo rm -rf mongodb/data/db/*
sudo rm -rf mongodb/data/log/*
```

> **NOTE:** don't remove `data` dir or its subdirs `db` and `log` cause you need to keep `777` permissions to all of them. If you delete them, Docker will recreate the dir structure (cause it is defined in `docker-compose.yml`) but with different permissions, and this will lead to an error on container startup.

## Logging into container

As we enabled authentication, we will need to authenticate using credentials from the `.env` file (database 'test', user `test`, password `test`)

The user we created in our shell script is assigned a [`readWrite` role](https://docs.mongodb.com/manual/reference/built-in-roles/#mongodb-authrole-readWrite) (hence we can't create any new databases, execute `insert` commands on it, etc.). We're allowed to work solely with `test` DB.

```shell
# Switch to our db
use test 

# Authenticate as test
db.auth('test', 'test')

# Now we can perform any CRUD operations
```

## References
* [MongoDB container with Docker Compose](https://zgadzaj.com/development/docker/docker-compose/containers/mongodb)
