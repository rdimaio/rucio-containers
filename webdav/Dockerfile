FROM httpd:latest

# These variables are inherited from the httpd:alpine image:
# ENV HTTPD_PREFIX /usr/local/apache2
# WORKDIR "$HTTPD_PREFIX"

# Copy in our configuration files.
COPY conf/ conf/

RUN set -ex; \
    # Download current `mod_want_digest.c` file (and store into root dir)
    cd /;\
    apt-get update; \
    apt-get install -y curl; \
    curl -LJ https://github.com/wetzel-desy/mod_want_digest/tarball/main -o wetzel-desy-tarball; \
    tar -xf wetzel-desy-tarball; \
    FILENAME=$(find . -name mod_want_digest.c); \
    mv $FILENAME .; \
    rm -rf wetzel-desy-*; \
    cd $HTTPD_PREFIX;\
    \
    # Create empty default DocumentRoot.
    mkdir -p "/var/www/html"; \
    # Create directories for Dav data and lock database.
    mkdir -p "/var/lib/dav/data"; \
    touch "/var/lib/dav/DavLock"; \
    chown -R www-data:www-data "/var/lib/dav"; \
    \
    # Enable DAV modules.
    for i in dav dav_fs; do \
        sed -i -e "/^#LoadModule ${i}_module.*/s/^#//" "conf/httpd.conf"; \
    done; \
    \
    # Make sure authentication modules are enabled.
    for i in authn_core authn_file authz_core authz_user auth_basic auth_digest; do \
        sed -i -e "/^#LoadModule ${i}_module.*/s/^#//" "conf/httpd.conf"; \
    done; \
    \
    # Make sure other modules are enabled.
    for i in alias headers mime setenvif; do \
        sed -i -e "/^#LoadModule ${i}_module.*/s/^#//" "conf/httpd.conf"; \
    done; \
    \
    # Run httpd as "www-data" (instead of "daemon").
    for i in User Group; do \
        sed -i -e "s|^$i .*|$i www-data|" "conf/httpd.conf"; \
    done; \
    \
    # Include enabled configs and sites.
    printf '%s\n' "Include conf/conf-enabled/*.conf" \
        >> "conf/httpd.conf"; \
    printf '%s\n' "Include conf/sites-enabled/*.conf" \
        >> "conf/httpd.conf"; \
    \
    # Write output to file and to std(err|out)???
    sed -i -e 's/\/proc\/self\/fd\/2/"|\$tee -a \/proc\/self\/fd\/2 \/usr\/local\/apache2\/logs\/apacheerrors.log"/g' conf/httpd.conf; \
    sed -i -e 's/LogLevel warn/LogLevel debug/g' conf/httpd.conf; \
    touch logs/apacheerrors.log; \
    \
    # Enable dav and default site.
    mkdir -p "conf/conf-enabled"; \
    mkdir -p "conf/sites-enabled"; \
    ln -s ../conf-available/dav.conf "conf/conf-enabled"; \
    ln -s ../sites-available/default.conf "conf/sites-enabled"; \
    # Install openssl if we need to generate a self-signed certificate.
    # apk add --no-cache openssl
    apt-get install openssl; \
    \
    # install mod_want_digest
    mv /mod_want_digest.c mod_want_digest.c; \
    apt-get install -y libtool libaprutil1-dev libapr1-dev zlib1g-dev build-essential; \
    ln -s /usr/lib/x86_64-linux-gnu/libz.so /usr/lib/x86_64-linux-gnu/libzlib.so; \
    apxs -c -lzlib mod_want_digest.c; \
    apxs -ia mod_want_digest.la; \
    \
    # enable SSL
    ln -s "../sites-available/default-ssl.conf" "conf/sites-enabled"; \
    sed -i -e "/^#LoadModule ssl_module.*/s/^#//" "conf/httpd.conf"


COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
EXPOSE 80/tcp 443/tcp
ENTRYPOINT [ "docker-entrypoint.sh" ]
CMD [ "httpd-foreground" ]