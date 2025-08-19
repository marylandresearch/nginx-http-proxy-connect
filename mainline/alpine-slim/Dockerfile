ARG NGINX_VERSION=1.29.0

FROM nginxinc/nginx-unprivileged:${NGINX_VERSION}-alpine-slim AS builder

ARG NGINX_VERSION

# See https://github.com/chobits/ngx_http_proxy_connect_module
ARG HTTP_PROXY_CONNECT_PATCH=proxy_connect_rewrite_102101.patch
ARG HTTP_PROXY_CONNECT_VERSION=0.0.7

# See https://github.com/nginx/docker-nginx-unprivileged/blob/main/mainline/alpine-slim/Dockerfile
ENV NGINX_VERSION=${NGINX_VERSION}
ENV PKG_RELEASE=1

USER root

# Build nginx with nginx_http_proxy_connect_module
RUN set -x \
    && nginxPackages=" \
        nginx=${NGINX_VERSION}-r${PKG_RELEASE} \
    " \
    && apk add --no-cache --virtual .build-deps \
        gcc \
        libc-dev \
        make \
        openssl-dev \
        pcre2-dev \
        zlib-dev \
        linux-headers \
        bash \
        alpine-sdk \
        findutils \
        curl \
    && CONFIGURE_ARGS=$(nginx -V 2>&1 | grep 'configure arguments' | sed 's/^configure arguments: //') \
	&& curl -f -L -O https://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz \
    && tar -xzvf nginx-${NGINX_VERSION}.tar.gz \
    && curl -f -L -o ngx_http_proxy_connect_module.tar.gz https://github.com/chobits/ngx_http_proxy_connect_module/archive/v${HTTP_PROXY_CONNECT_VERSION}.tar.gz \
    && tar -xzvf ngx_http_proxy_connect_module.tar.gz \
    && cd nginx-${NGINX_VERSION} \
    && patch -p1 < ../ngx_http_proxy_connect_module-${HTTP_PROXY_CONNECT_VERSION}/patch/${HTTP_PROXY_CONNECT_PATCH} \
    && eval ./configure ${CONFIGURE_ARGS} --add-module=../ngx_http_proxy_connect_module-${HTTP_PROXY_CONNECT_VERSION} \
    && make \
    && make install

FROM nginxinc/nginx-unprivileged:${NGINX_VERSION}-alpine-slim

# Copy nginx binary with custom module into final nginx image
COPY --from=builder /usr/sbin/nginx /usr/sbin/

# Enable the http proxy connect; overwrite to suite your needs. (Run docker build with context as project root)
COPY nginx /etc/nginx/
ENV NGINX_ENTRYPOINT_LOCAL_RESOLVERS=true
