# Golang testing image with some tools already installed
FROM stanchan/go-nodejs-npm-yarn:1.12.7_10.16.1

LABEL name="semaphore" \
      version="3.0.0" \
      os="alpine" \
      osversion="3.10" \
      architecture="amd64" \
      vcs-url="https://github.com/stanchan/semaphore"

ENV SEMAPHORE_VERSION="development" SEMAPHORE_ARCH="linux_amd64" \
    SEMAPHORE_CONFIG_PATH="${SEMAPHORE_CONFIG_PATH:-/etc/semaphore}" \
    APP_ROOT="/go/src/github.com/stanchan/semaphore/"

# hadolint ignore=DL3013
RUN apk add --no-cache git mariadb-client python3 py3-openssl openssl ca-certificates curl curl-dev openssh-client tini bash rsync && \
    apk add --no-cache --virtual build-dependencies python3-dev libffi-dev openssl-dev build-base && \
    pip install --upgrade pip cffi && \
    pip install ansible && \
    rm -rf /var/cache/apk/*    && \
    adduser -D -u 1002 -g 0 semaphore && \
    mkdir -p /go/src/github.com/stanchan/semaphore && \
    mkdir -p /tmp/semaphore && \
    mkdir -p /etc/semaphore && \
    chown -R semaphore:0 /go && \
    chown -R semaphore:0 /tmp/semaphore && \
    chown -R semaphore:0 /etc/semaphore && \
    ssh-keygen -t rsa -q -f "/root/.ssh/id_rsa" -N "" && \
    ssh-keyscan -H github.com > /root/.ssh/known_hosts && \
    curl -sL https://taskfile.dev/install.sh | sh

# Copy in app source
WORKDIR ${APP_ROOT}
COPY . ${APP_ROOT}
RUN deployment/docker/ci/bin/install

USER semaphore
EXPOSE 3000
ENTRYPOINT ["/usr/local/bin/semaphore-wrapper"]
CMD ["./bin/semaphore", "--config", "/etc/semaphore/config.json"]