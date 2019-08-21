# Golang testing image with some tools already installed
FROM stanchan/go-nodejs-npm-yarn:1.12.7_10.16.1

ENV SEMAPHORE_VERSION="development" SEMAPHORE_ARCH="linux_amd64" \
    SEMAPHORE_CONFIG_PATH="${SEMAPHORE_CONFIG_PATH:-/etc/semaphore}" \
    APP_ROOT="/go/src/github.com/stanchan/semaphore/"

RUN apk add --no-cache git mariadb-client python3 py3-openssl openssl ca-certificates curl curl-dev openssh-client tini bash rsync && \
    apk add --no-cache --virtual .build-dependencies gcc python3-dev libffi-dev openssl-dev build-base && \
    python3 -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --no-cache-dir --upgrade pip setuptools wheel cffi && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    pip install ansible && \
    adduser -D -u 1002 -g 0 semaphore && \
    mkdir -p /go/src/github.com/stanchan/semaphore && \
    mkdir -p /tmp/semaphore && \
    mkdir -p /etc/semaphore && \
    mkdir -p /.cache && \
    chown -R semaphore:0 /go && \
    chown -R semaphore:0 /tmp/semaphore && \
    chown -R semaphore:0 /etc/semaphore && \
    chown -R semaphore:0 /.cache && \
    ssh-keygen -t rsa -q -f "/root/.ssh/id_rsa" -N "" && \
    ssh-keyscan -H github.com > /root/.ssh/known_hosts && \
    curl -sL https://taskfile.dev/install.sh | sh && \
    apk del .build-dependencies && \
    rm -rf /var/cache/apk/*

# Copy in app source
WORKDIR ${APP_ROOT}
COPY . ${APP_ROOT}
RUN deployment/docker/dev/bin/install

USER 1002
EXPOSE 3000

CMD ["task", "watch"]