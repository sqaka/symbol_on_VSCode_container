ARG  UBUNTU_VERSION=18.04
FROM ubuntu:$UBUNTU_VERSION
USER root
SHELL ["/bin/bash", "-l", "-c"]

LABEL authors="hoatle <hoatle@teracy.com>"

# add more arguments from CI to the image so that `$ env` should reveal more info
ARG CI_BUILD_ID
ARG CI_BUILD_REF
ARG CI_REGISTRY_IMAGE
ARG CI_BUILD_TIME

ENV CI_BUILD_ID=$CI_BUILD_ID CI_BUILD_REF=$CI_BUILD_REF CI_REGISTRY_IMAGE=$CI_REGISTRY_IMAGE \
    CI_BUILD_TIME=$CI_BUILD_TIME

ARG ARCH="amd64"
ARG DOCKER_CHANNEL="stable"
ARG DOCKER_VERSION="latest"
ARG DOCKER_COMPOSE_VERSION="1.24.1"

ADD ./.devcontainer/install.sh ./install.sh 
RUN chmod +x ./install.sh
RUN ./install.sh

RUN apt-get update && \
    apt-get -y install --no-install-recommends apt-utils dialog 2>&1 && \
    apt-get -y install git sudo vim iproute2 procps lsb-release \
                       autoconf automake libtool libssl1.0-dev

RUN apt-get -y install locales && \
    localedef -f UTF-8 -i ja_JP ja_JP.UTF-8

ARG USERNAME=vscode
ARG USER_UID=2222
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME && \
    useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME && \
    echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME && \
    chmod 0440 /etc/sudoers.d/$USERNAME

RUN apt-get update && \
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash - && \
    apt-get install -y nodejs && \
    npm install -g npm@latest

RUN npm install --global symbol-cli@0.20.2 && \
    npm install -g symbol-bootstrap@0.2.0 && \
    npm install symbol-sdk@0.20.7 rxjs && \
    npm install --global typescript ts-node

COPY ./.devcontainer/docker-entrypoint.sh /usr/local/bin/

# https://github.com/docker-library/docker/pull/166
#   dockerd-entrypoint.sh uses DOCKER_TLS_CERTDIR for auto-generating TLS certificates
#   docker-entrypoint.sh uses DOCKER_TLS_CERTDIR for auto-setting DOCKER_TLS_VERIFY and DOCKER_CERT_PATH
# (For this to work, at least the "client" subdirectory of this path needs to be shared between the client and server containers via a volume, "docker cp", or other means of data sharing.)
ENV DOCKER_TLS_CERTDIR=/certs

RUN mkdir /certs /certs/client && chmod 1777 /certs /certs/client

ENTRYPOINT ["docker-entrypoint.sh"]
CMD [ "node", "server.js" ]
