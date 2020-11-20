FROM ubuntu:18.04
USER root
SHELL ["/bin/bash", "-l", "-c"]

RUN apt-get update && \
    apt-get -y install --no-install-recommends apt-utils dialog 2>&1 && \
    apt-get -y install curl gnupg git sudo vim iproute2 procps lsb-release \
                       autoconf automake libtool libssl1.0-dev

RUN apt-get -y install locales && \
    localedef -f UTF-8 -i ja_JP ja_JP.UTF-8

RUN apt-get update && \
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash - && \
    apt-get install -y nodejs && \
    npm install -g npm@latest

ARG USERNAME=vscode
ARG USER_UID=2222
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME && \
    useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME && \
    echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME && \
    chmod 0440 /etc/sudoers.d/$USERNAME

RUN npm install --global symbol-cli@0.20.2 && \
    npm install -g symbol-bootstrap@0.2.0 && \
    npm install symbol-sdk@0.20.7 rxjs && \
    npm install --global typescript ts-node

CMD [ "node", "server.js" ]