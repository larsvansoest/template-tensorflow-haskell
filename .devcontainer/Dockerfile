# Tensorflow Haskell (2.3.0)
# See https://github.com/tensorflow/haskell/blob/c66c912c327810796b6282ae77c33e336c1dadea/docker/gpu/Dockerfile
FROM tensorflow/tensorflow:2.3.0 as tensorflow-haskell-2.3.0
LABEL maintainer="TensorFlow authors <tensorflow-haskell@googlegroups.com>"

RUN apt-get update

RUN apt-get install -y \
        # Required by snappy-frames dependency.
        libsnappy-dev \
        # Avoids /usr/bin/ld: cannot find -ltinfo
        libncurses5-dev \
        # Makes stack viable in the container
        libgmp-dev \
        zlib1g-dev \
        # For fetching dependencies
        curl \
        unzip \
        # Required for locales configuration.
        locales \
        # Required for tcp connections by stack (See: https://github.com/tensorflow/haskell/issues/182)
        netbase

# Our MNIST demo program outputs Unicode characters.
RUN dpkg-reconfigure locales && \
    locale-gen en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8

# Installs protoc and the libraries.
RUN \
    curl -O -L https://github.com/google/protobuf/releases/download/v3.13.0/protoc-3.13.0-linux-x86_64.zip && \
    unzip -d /usr/local protoc-3.13.0-linux-x86_64.zip bin/protoc && \
    chmod 755 /usr/local/bin/protoc && \
    curl -O https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow-cpu-linux-x86_64-2.3.0.tar.gz && \
    tar zxf libtensorflow-cpu-linux-x86_64-2.3.0.tar.gz -C /usr/local && \
    ldconfig

ENV LANG en_US.UTF-8

# devcontainer
FROM tensorflow-haskell-2.3.0

# Install git and enable ssh signing on Ubuntu 18.04.
ARG SSH_VERSION=9.0
RUN \
    # Upgrade openssh to support -Y option which git requires
    apt-get install -y build-essential zlib1g-dev libssl-dev \
    && mkdir /var/lib/sshd \
    && chmod -R 700 /var/lib/sshd/ \
    && chown -R root:sys /var/lib/sshd/ \
    && curl -O https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-${SSH_VERSION}p1.tar.gz \
    && tar -xzf openssh-${SSH_VERSION}p1.tar.gz \
    && cd openssh-${SSH_VERSION}p1/ \
    && ./configure \
    && make \
    && make install \
    # latest stable git version support ssh signing.
    && apt-get install -y software-properties-common \
    && add-apt-repository ppa:git-core/ppa \
    && apt-get update \
    && apt-get install -y git

# create the vscode user
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME

# ghcup must be installed by non-root/non-admin user
USER $USERNAME

ARG GHC_VERSION=8.10.6
ARG HLS_VERSION=1.7.0.0
# install ghcup, automatically installs cabal and stack, hls is installed through the haskell vscode extension
RUN export BOOTSTRAP_HASKELL_INSTALL_STACK=True \
    export BOOTSTRAP_HASKELL_GHC_VERSION=${GHC_VERSION} \
    && export BOOTSTRAP_HASKELL_NONINTERACTIVE=True \
    && curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh

# add ghcup, cabal and stack to path
ENV PATH="/home/${USERNAME}/.ghcup/bin:${PATH}"

# install Haskell Language Server 1.7.0.0 for GHC 8.10.6
RUN ghcup compile hls -v ${HLS_VERSION} --ghc ${GHC_VERSION}

# set c enconding to utf-8 to support ghc output
ENV LC_ALL="C.UTF-8"

# set shell to bash to use auto completion (e.g. arrow up for last command)
ENV SHELL="/bin/bash"
