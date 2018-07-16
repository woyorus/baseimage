FROM alpine:3.7

ENV \
    TERM=xterm-color           \
    DINIT_VERSION=1.2.1        \
    TIME_ZONE=America/Chicago  \
    MYUSER=app                 \
    MYUID=1001                 \
    DOCKER_GID=999         

COPY files/scripts/nop.sh /usr/bin/nop.sh
COPY files/scripts/app.sh /usr/bin/app.sh
COPY files/init.sh /init.sh

# Install Docker client
ARG DOCKER_CLI_VERSION="18.03.1-ce"
ENV DOWNLOAD_URL="https://download.docker.com/linux/static/stable/x86_64/docker-$DOCKER_CLI_VERSION.tgz"

RUN apk --update add curl \
    && mkdir -p /tmp/download \
    && curl -L $DOWNLOAD_URL | tar -xz -C /tmp/download \
    && mv /tmp/download/docker/docker /usr/local/bin/ \
    && rm -rf /tmp/download \
    && apk del curl \
    && rm -rf /var/cache/apk/*

RUN \
    chmod +x /usr/bin/nop.sh /usr/bin/app.sh /init.sh && \
    apk add --no-cache --update su-exec tzdata curl openssl && \
    ln -s /sbin/su-exec /usr/local/bin/gosu && \
    mkdir -p /home/$MYUSER && \
    adduser -s /bin/sh -D -u $MYUID $MYUSER && chown -R $MYUSER:$MYUSER /home/$MYUSER && \
    delgroup ping && addgroup -g 998 ping && \
    addgroup -g ${DOCKER_GID} docker && addgroup ${MYUSER} docker && \
    mkdir -p /srv && chown -R $MYUSER:$MYUSER /srv && \
    cp /usr/share/zoneinfo/${TIME_ZONE} /etc/localtime && \
    echo "${TIME_ZONE}" > /etc/timezone && date && \
    wget -q https://github.com/Yelp/dumb-init/releases/download/v${DINIT_VERSION}/dumb-init_${DINIT_VERSION}_amd64 -O /sbin/dinit && \
    chmod +x /sbin/dinit && \
    rm -rf /var/cache/apk/*

ENTRYPOINT ["/init.sh"]
