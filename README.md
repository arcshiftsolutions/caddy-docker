[![caddy/caddy on DockerHub][dockerhub-image]][dockerhub-url]
[![Docker Build][gh-actions-image]][gh-actions-url]

# [caddy/caddy](https://hub.docker.com/r/caddy/caddy)

## Supported tags and respective `Dockerfile` links

- [`v2.0.0-beta.15`, `v2.0.0-beta.15-alpine`, `alpine`, `latest`](https://github.com/caddyserver/caddy-docker/blob/f8db28380a31026b30d4fb361364ce88ec9abed1/alpine/Dockerfile)
- [`v2.0.0-beta.15-slim`, `scratch`](https://github.com/caddyserver/caddy-docker/blob/f8db28380a31026b30d4fb361364ce88ec9abed1/scratch/Dockerfile)

## Quick reference

- **Where to get help**:  
	[the Caddy Community forum](https://caddy.community)

- **Where to file issues**:  
	[https://github.com/caddyserver/caddy-docker](https://github.com/caddyserver/caddy-docker/issues) for issues with this image, or [https://github.com/caddyserver/caddy](https://github.com/caddyserver/caddy/issues) for issues with Caddy itself.

- **Supported architectures**: ([more info](https://github.com/docker-library/official-images#architectures-other-than-amd64))  
	[`amd64`](https://hub.docker.com/r/caddy/caddy/) (more to come!)

---

<a href="https://caddyserver.com/"><img alt="Caddy Logo" src="https://caddyserver.com/resources/images/caddy-wordmark.svg" width="30%"/></a>

[Caddy 2](https://caddyserver.com/) is a powerful, enterprise-ready, open source web server with automatic HTTPS written in Go.

## How to use this image

#### ⚠️ A note about persisted data

Caddy requires write access to two locations: a [data directory](https://caddyserver.com/docs/conventions#data-directory), and a [configuration directory](https://caddyserver.com/docs/conventions#configuration-directory). While it's not necessary to persist the files stored in the configuration directory, it can be convenient. However, it's very important to persist the data directory.

From the docs:

> The data directory must not be treated like a cache. Its contents are not ephemeral or merely for the sake of performance. Caddy stores TLS certificates, private keys, OCSP staples, and other necessary information to the data directory. It should not be purged without an understanding of the implications.

This image provides for two mount-points for volumes: `/data` and `/config`.

In the examples below, a [named volume](https://docs.docker.com/storage/volumes/) `caddy_data` is mounted to `/data`, so that data will be persisted.

Note that named volumes are persisted across container restarts and terminations, so if you move to a new image version, the same data and config directories can be re-used.

### Basic Usage

The default config file simply serves files from `/usr/share/caddy`, so if you want to serve `index.html` from the current working directory:

```console
$ echo "hello world" > index.html
$ docker run -d -p 80:80 \
    -v $(pwd)/index.html:/usr/share/caddy/index.html \
    -v caddy_data:/data \
    caddy/caddy
...
$ curl http://localhost/
hello world
```

To override the default [`Caddyfile`](https://github.com/caddyserver/dist/blob/master/config/Caddyfile), you can mount a new one at `/etc/caddy/Caddyfile`:

```console
$ docker run -d -p 80:80 \
    -v $(pwd)/Caddyfile:/etc/caddy/Caddyfile \
    -v caddy_data:/data \
    caddy/caddy
```

### Automatic TLS with the Caddy image

The default `Caddyfile` only listens to port `80`, and does not set up automatic TLS. However, if you have a domain name for your site, and its A/AAAA DNS records are properly pointed to this machine's public IP, then you can use this command to simply serve a site over HTTPS:

```console
$ docker run -d -p 80:80 -p 443:443 \
    -v /site:/usr/share/caddy \
    -v caddy_data:/data \
    -v caddy_config:/config \
    caddy/caddy caddy file-server --domain example.com
```

The key here is that Caddy is able to listen to ports `80` and `443`, both required for the ACME HTTP challenge.

See [Caddy's docs](https://caddyserver.com/docs/automatic-https) for more information on automatic HTTP support!

### Building your own Caddy-based image

Most users deploying production sites will not want to rely on mounting files into a container, but will instead base their own images on `caddy/caddy`:

```Dockerfile
# note: never use the :latest tag in a production site
FROM caddy/caddy:v2.0.0

COPY Caddyfile /etc/config/Caddyfile
COPY site /site
```

## License

View [license information](https://github.com/caddyserver/caddy/LICENSE.txt) for the software contained in this image.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.

[gh-actions-image]: https://github.com/caddyserver/caddy-docker/workflows/Docker%20Build/badge.svg?branch=master
[gh-actions-url]: https://github.com/caddyserver/caddy-docker/actions?workflow=Docker%20Build&branch=master

[dockerhub-image]: https://img.shields.io/badge/docker-ready-blue.svg
[dockerhub-url]: https://hub.docker.com/r/caddy/caddy
