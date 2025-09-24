# nginx-http-proxy-connect

This project packages an Nginx server with a third-party module that allows Nginx to serve as an HTTP proxy server. It is designed as a Docker container based on the unprivileged [nginxinc/nginx-unprivileged](https://hub.docker.com/r/nginxinc/nginx-unprivileged) image.

The third-party module, [ngx_http_proxy_connect_module](https://github.com/chobits/ngx_http_proxy_connect_module), extends Nginx to support HTTP CONNECT requests, enabling it to work as an HTTP proxy.

## Features

- **HTTP Proxy Functionality:** Configured with a third-party module to handle HTTP CONNECT requests.
- **Dockerized Deployment:** Ready-to-use Docker container for rapid deployment.
- **Unprivileged, Secure Base:** Built on the secure and unprivileged [nginxinc/nginx-unprivileged](https://hub.docker.com/r/nginxinc/nginx-unprivileged) image.

## Getting Started

### Running the Container

To start up the container, run:

```bash
docker run -p 3128:3128 marylandresearch/nginx-http-proxy-connect:1.29.1-alpine-slim
```

This command maps port `3128` on your host to port `3128` in the container, where your proxy server is listening.

### Container Tags

See available tags and versions at https://hub.docker.com/r/marylandresearch/nginx-http-proxy-connect

The images are deployed to the following registries:
- [docker.io](https://hub.docker.com/r/marylandresearch/nginx-http-proxy-connect)
- [registry.gitlab.com](https://gitlab.com/marylandresearch/nginx-http-proxy-connect/container_registry)
- [ghcr.io](https://github.com/orgs/marylandresearch/packages/container/package/nginx-http-proxy-connect)


The images are deployed with the following flavors:
- alpine
- alpine-slim

This does not publish a "latest" version.

### Using the Proxy

To test the proxy, you can use a tool like `curl` with the `-x` flag. For example:

```bash
curl -x http://localhost:3128 https://example.com
```

This command directs your traffic to go through the proxy server running inside the Docker container.

## Configuration

The proxy configuration is based on the template found at `nginx/templates/proxy.conf.template` which determines DNS resolvers at runtime. If you wish to use a custom configuration, you can override the default by mounting your version into the container. For example:

```bash
docker run \
  -p 3128:3128 \
  -v /path/to/your/proxy.conf.template:/etc/nginx/templates/proxy.conf.template \
  marylandresearch/nginx-http-proxy-connect:1.29.1-alpine-slim
```

### Example Proxy Configuration

Below is an example default snippet from the configuration file that illustrates how the proxy is set up. This configuration is automatically loaded in the docker image by default.

```nginx
server {
    listen 3128;
    proxy_connect;
    resolver ${NGINX_LOCAL_RESOLVERS} valid=5s;

    location / {
        add_header "Content-Type" "text/plain" always;
        return 200 "http proxy is active!";
    }
}
```

For more details about the available options and the module, please refer to the [ngx_http_proxy_connect_module repository](https://github.com/chobits/ngx_http_proxy_connect_module).

## Base Image

This container is built on top of the [nginxinc/nginx-unprivileged](https://hub.docker.com/r/nginxinc/nginx-unprivileged) image. Visit the Docker Hub page for more details on its usage and configuration.

## License

See [LICENSE](https://github.com/marylandresearch/nginx-http-proxy-connect/blob/main/LICENSE) for details.

---

**Contributions:**
Feel free to open an issue or submit a pull request if you have suggestions, improvements, or encounter any problems.

https://github.com/marylandresearch/nginx-http-proxy-connect
