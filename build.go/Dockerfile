FROM golang:1.13-buster

RUN echo "deb http://ftp.debian.org/debian buster-backports main" >> /etc/apt/sources.list.d/backports.list

# Install Docker client
ARG DOCKER_CLI_VERSION="18.03.1-ce"
ENV DOWNLOAD_URL="https://download.docker.com/linux/static/stable/x86_64/docker-$DOCKER_CLI_VERSION.tgz"
ENV RESTIC_DOWNLOAD_URL="https://wrs.nyc3.digitaloceanspaces.com/bin/restic-v0-9-4-linux-amd64.tar.gz"

RUN \
    apt-get update && apt-get install -y tzdata git bash curl libgit2-27 build-essential && \
    cp /usr/share/zoneinfo/America/Chicago /etc/localtime && \
    apt-get autoremove && apt-get autoclean

RUN mkdir -p /tmp/download \
    && curl -L $DOWNLOAD_URL | tar -xz -C /tmp/download \
    && mv /tmp/download/docker/docker /usr/local/bin/ \
    && curl -L $RESTIC_DOWNLOAD_URL | tar -xz -C /tmp/download \
    && mv /tmp/download/restic /usr/local/bin/ \
    && chmod +x /usr/local/bin/restic \
    && rm -rf /tmp/download

RUN go version

ENV CGO_ENABLED=0
ENV GOOS=linux
ENV GOARCH=amd64
ENV GOLANGCILINT=1.5

RUN \
    go get -u github.com/golang/dep/cmd/dep && \
    go get -u github.com/kardianos/govendor && \
    go get -u github.com/mattn/goveralls && \
    go get -u github.com/jteeuwen/go-bindata/... && \
    go get -u github.com/stretchr/testify && \
    go get -u github.com/vektra/mockery/.../ && \
    go get -u gopkg.in/alecthomas/gometalinter.v2 && \
    ln -s /go/bin/gometalinter.v2 /go/bin/gometalinter && \
    gometalinter --install --force

ADD https://github.com/golangci/golangci-lint/releases/download/v${GOLANGCILINT}/golangci-lint-${GOLANGCILINT}-linux-amd64.tar.gz /tmp/golangci-lint.tar.gz

RUN \
    cd /tmp && tar -zxf /tmp/golangci-lint.tar.gz && \
    mv /tmp/golangci-lint-${GOLANGCILINT}-linux-amd64/golangci-lint /go/bin/golangci-lint && \
    chmod +x /go/bin/golangci-lint


ADD coverage.sh /script/coverage.sh
ADD checkvendor.sh /script/checkvendor.sh
ADD git-rev.sh /script/git-rev.sh

RUN \
    chmod +x /script/coverage.sh /script/checkvendor.sh /script/git-rev.sh && \
    ln -s /script/checkvendor.sh $GOPATH/bin/checkvendor
