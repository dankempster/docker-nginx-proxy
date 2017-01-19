# Docker Nginx Reverse Proxy

Using [jwilder/docker-gen](https://hub.docker.com/r/jwilder/docker-gen/), based on the examples in [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy), this project provides an automated Nginx Reverse Proxy for your containers.

See [Automated Nginx Reverse Proxy for Docker](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/) for why you might want to use this.

## Usage

To run it (with [docker-composer](https://docs.docker.com/compose/))
```bash
 $ docker-compose up -d
```

Now start any containers you want proxied with an env var VIRTUAL_HOST=subdomain.youdomain.com

```bash
$ docker run -e VIRTUAL_HOST=foo.example.com  ...
```

Or set the environment in docker-compose.yml config

```yaml
services:
    my_web_app:
        ...
        environment:
            - VIRTUAL_HOST=bar.example.com
        ...
```

The containers being proxied must expose the port to be proxied, either by using the EXPOSE directive in their Dockerfile or by using the --expose flag to docker run or docker create.

Provided your DNS is setup to forward foo.bar.com to the a host running nginx-proxy, the request will be routed to a container with the VIRTUAL_HOST env var set.

## Multiple Ports

If your container exposes multiple ports, docker-nginx-proxy will default to the service running on port 80. If you need to specify a different port, you can set a VIRTUAL_PORT env var to select a different one. If your container only exposes one port and it has a VIRTUAL_HOST env var set, that port will be selected.

## Multiple Hosts

If you need to support multiple virtual hosts for a container, you can separate each entry with commas. For example, foo.bar.com,baz.bar.com,bar.com and each host will be setup the same.

## Wild dcard Hosts

You can also use wildcards at the beginning and the end of host name, like *.bar.com or foo.bar.*. Or even a regular expression, which can be very useful in conjunction with a wildcard DNS service like xip.io, using ~^foo\.bar\..*\.xip\.io will match foo.bar.127.0.0.1.xip.io, foo.bar.10.0.2.2.xip.io and all other given IPs. More information about this topic can be found in the nginx documentation about server_names.