# ansible-semaphore production image
#
# Uses glibc alpine so we have access to glibc which is needed for golang
# and when deploying in openshift
FROM stanchan/glibc-go-nodejs-npm-yarn:1.12.7_10.16.1 as builder

RUN apk add --no-cache git ansible mariadb-client curl openssh-client libc-dev tini && \
    curl -sL https://taskfile.dev/install.sh | sh && \
    rm -rf /var/cache/apk/*

COPY ./ /go/src/github.com/stanchan/semaphore
WORKDIR /go/src/github.com/stanchan/semaphore

RUN deployment/docker/prod/bin/install

FROM stanchan/glibc-nodejs-npm-yarn:10.16.3

# RUN apk add --no-cache -U libc-dev go nodejs && \
#   ./deployment/docker/prod/bin/install && \
#   apk del libc-dev go nodejs && \
#   rm -rf /go/* && \
#   rm -rf /var/cache/apk/*

RUN apk add --no-cache ca-certificates git ansible mariadb-client curl openssh-client tzdata tini && \
    python3 -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --no-cache-dir --upgrade pip setuptools wheel cffi && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    adduser -D -u 1001 -G root semaphore && \
    mkdir -p /tmp/semaphore && \
    mkdir -p /etc/semaphore && \
    chown -R semaphore:0 /tmp/semaphore && \
    chown -R semaphore:0 /etc/semaphore && \
    curl -sL https://taskfile.dev/install.sh | sh && \
    rm -rf /var/cache/apk/*

COPY --from=builder bin/semaphore /usr/local/bin/semaphore
RUN chown 1001:0 /usr/local/bin/semaphore && \
    chmod +x /usr/local/bin/semaphore
COPY deloyment/docker/common/semaphore-wrapper /usr/local/bin/semaphore-wrapper

RUN chown semaphore:0 /usr/local/bin/semaphore-wrapper

WORKDIR /home/semaphore
USER 1001

ENTRYPOINT ["/sbin/tini", "--"]
CMD ["/usr/local/bin/semaphore-wrapper", "/usr/local/bin/semaphore", "--config", "/etc/semaphore/config.json"]
