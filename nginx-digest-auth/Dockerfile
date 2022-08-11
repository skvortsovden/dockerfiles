FROM nginx:alpine AS builder

# nginx-http-auth-digest version
ENV DIGEST_MODULE_VERSION 1.0.0

RUN wget "http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz" -O nginx.tar.gz && \
  wget "https://github.com/atomx/nginx-http-auth-digest/archive/refs/tags/v${DIGEST_MODULE_VERSION}.tar.gz" -O digest.tar.gz

RUN apk add --no-cache --virtual .build-deps \
  gcc \
  libc-dev \
  make \
  openssl-dev \
  pcre-dev \
  zlib-dev \
  linux-headers \
  curl \
  gnupg \
  libxslt-dev \
  gd-dev \
  geoip-dev

RUN CONFARGS=$(nginx -V 2>&1 | sed -n -e 's/^.*arguments: //p') \
  mkdir -p /usr/src && \
	tar -zxC /usr/src -f nginx.tar.gz && \
  tar -xzvf "digest.tar.gz" && \
  DIGESTDIR="$(pwd)/nginx-http-auth-digest-${DIGEST_MODULE_VERSION}" && \
  cd /usr/src/nginx-$NGINX_VERSION && \
  ./configure --with-compat $CONFARGS --add-dynamic-module=$DIGESTDIR && \
  make && make install

FROM nginx:alpine
COPY --from=builder /usr/local/nginx/modules/ngx_http_auth_digest_module.so /etc/nginx/modules/ngx_http_auth_digest_module.so
RUN rm /etc/nginx/conf.d/default.conf
EXPOSE 80
STOPSIGNAL SIGTERM
CMD ["nginx", "-g", "daemon off;"]