# Containerized MongoDB

* runs database init script from `mongodb/initdb.d/` (creates new user and database using environment variables)
* sets custom config file (enables auth)

## Starting container from scratch

If you want to start the container from scratch, first you need manually clean everything up left after the old stopped container: run the shell script `sudo clean-up-old-container`.

## Logging into container and database

As we enabled authentication, we will need to authenticate using credentials from the `.env` file (database `test`, user `test`, password `test`)

The user we created in our shell script is assigned a [`readWrite` role](https://docs.mongodb.com/manual/reference/built-in-roles/#mongodb-authrole-readWrite) (hence we can't create any new databases, execute `insert` commands on it, etc.). We're allowed to work solely with `test` DB.

1. Log into container:
   ```shell
   docker container exec -it mongodb mongosh # mongosh is MongoDB shell
   ```

2. Log into database
   ```shell
   # Switch to our db
   use test 

   # Authenticate as test
   db.auth('test', 'test') 

   # Now we can perform any CRUD operations
   ```

## Troubleshooting

If you get an error, check out `mongodb/data/` dir, it should contain two empty dirs: `db` and `log`. If there are none, create them manually and set 777 permissions.

`data/db/` and `data/log/` dirs need to have `777` permissions. If at some point you manually delete them, Docker will recreate the dir structure (cause it is defined in `docker-compose.yml`) but with different permissions, and this will lead to an error on container startup.

## References

* [MongoDB container with Docker Compose](https://zgadzaj.com/development/docker/docker-compose/containers/mongodb)
