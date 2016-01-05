### Aim
The primary goal is to deploy the cloudera manager server, ready-to-use. This server can be used with the `cmf-db` image or a custom database.
Passwords are automaticaly generated during the `cmf-db` build. So, in order to use this with the `cmf-db` image, a `volumes-from` option must be use.

### Quickstart
```
# linked to the cmf-db image
doccer run -dt --name cmf-db cmf-db
docker run -dt --link cmf-db:cmf-db --volumes-from=cmf-db cmf-server
```
