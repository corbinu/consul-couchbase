Consul Couchbase
======================
This Docker container is setup to run Couchbase with Consul for service discovery and health monitoring.

It has also been optimized to run on Joyent's [Triton](https://www.joyent.com/blog/understanding-triton-containers), however it will always also support strait Docker and boot2docker.

Much thanks to [Casey Bisson](https://github.com/misterbisson) who created the original version.

## Running

It can be run on its own simply via:

```
docker run -d  -p 8400 -p 8500 -p 8600 --name consul progrium/consul -server -bootstrap -ui-dir /ui
docker run -d --name cb1 --link consul:consul -p 8091 corbinu/consul-couchbase
docker run -d --name cb2 --link consul:consul corbinu/consul-couchbase

docker exec -it cb1 couchbase-bootstrap bootstrap
```

Consul will UI will be available on port mapped to 8500
Couchbase Dashboard will be on port mapped to 8091

## Demos

More detailed demos on how to scale Consul, Couchbase, and other options please visit [corbinu/docker-demos](https://github.com/corbinu/docker-demos)


## Consul notes

[Bootstrapping](https://www.consul.io/docs/guides/bootstrapping.html), [Consul clusters](https://www.consul.io/intro/getting-started/join.html), and the details about [adding and removing nodes](https://www.consul.io/docs/guides/servers.html). The [CLI](https://www.consul.io/docs/commands/index.html) and [HTTP](https://www.consul.io/docs/agent/http.html) API are also documented.

[Check for registered instances of a named service](https://www.consul.io/docs/agent/http/catalog.html#catalog_service)

```bash
curl -v http://consul:8500/v1/catalog/service/couchbase | json -aH ServiceAddress
```

[Register an instance of a service](https://www.consul.io/docs/agent/http/catalog.html#catalog_register)

```bash
export MYIP=$(ip addr show eth0 | grep -o '[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}')
curl http://consul:8500/v1/agent/service/register -d "$(printf '{"ID": "couchbase-%s","Name": "couchbase","Address": "%s"}' $MYIP $MYIP)"
```