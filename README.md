This is a failed attempt at getting my head around the structure for docker-compose for multiple environments.

The goals:

* Keep the environment-specific docker-compose configurations as small as possible (maximize reuse of configuration). (I started experimenting with using `.env` files.)
* Allow (non-swarm) servers to keep sensitive information outside of SCM, in a designated directory. (My thought was to use a [`./var`](./var) directory, which _is_ in SCM, but which has its contents ignored.)

From the `localdev` directory, things behave as expected:

```
$ cd docker-compose/localdev/
$ docker-compose up --build -d
WARNING: The Docker Engine you're using is running in swarm mode.

Compose does not use swarm mode to deploy services to multiple nodes in a swarm. All containers will be scheduled on the current node.

To deploy your application across the swarm, use `docker stack deploy`.

Building solr
Step 1/2 : FROM alpine
 ---> 7328f6f8b418
Step 2/2 : ENTRYPOINT tail -f /dev/null
 ---> Using cache
 ---> 94a7672e2ac0
Successfully built 94a7672e2ac0
Successfully tagged local/solr-mariadb:6.6.0_localdev
Recreating solr_localdev ...
Recreating solr_localdev ... done
```

From the `test1` directory (which emulates how it would be run on the server), my attempts at factoring the environment-specific `docker-compose.yml` and/or the use of the remote `.env` file causes problems.

```
$ cd docker-compose/localdev/
$ docker-compose up --build -d
WARNING: The Docker Engine you're using is running in swarm mode.

Compose does not use swarm mode to deploy services to multiple nodes in a swarm. All containers will be scheduled on the current node.

To deploy your application across the swarm, use `docker stack deploy`.

Building solr
Step 1/2 : FROM alpine
 ---> 7328f6f8b418
Step 2/2 : ENTRYPOINT tail -f /dev/null
 ---> Using cache
 ---> 94a7672e2ac0
Successfully built 94a7672e2ac0
Successfully tagged local/solr-mariadb:6.6.0_localdev
Recreating solr_localdev ...
Recreating solr_localdev ... done
ICF2008571:localdev jjackson$ cd ..
ICF2008571:docker-compose jjackson$ cd test1/
ICF2008571:test1 jjackson$ docker-compose up --build -d
WARNING: The DB_HOST variable is not set. Defaulting to a blank string.
WARNING: The DB_SCHEMA variable is not set. Defaulting to a blank string.
WARNING: The DB_USER variable is not set. Defaulting to a blank string.
WARNING: The DB_PASSWORD variable is not set. Defaulting to a blank string.
WARNING: The SOLR_CONTAINER_SUFFIX variable is not set. Defaulting to a blank string.
WARNING: The SOLR_PUBLISH_PORT variable is not set. Defaulting to a blank string.
ERROR: The Compose file './../common/docker-compose.yml' is invalid because:
services.solr.ports is invalid: Invalid port ":8983", should be [[remote_ip:]remote_port[-remote_port]:]port[/protocol]
```