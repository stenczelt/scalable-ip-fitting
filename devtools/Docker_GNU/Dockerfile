FROM ubuntu:latest
MAINTAINER "Tamas K Stenczel <stenczelt@gmail.com>"

# Time Zone data
ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Europe/London

# general installations
RUN apt-get -q update && \
    apt-get -qy install --no-install-recommends \
        # general
        make \
        gfortran g++ \
        libopenmpi-dev \
        # cmake and tools
        cmake vim git wget ca-certificates \
        # CBLAS and LapackE
        liblapacke-dev libatlas-base-dev \
        # ScaLapack \
        libscalapack-openmpi-dev \
    && rm -rf /var/lib/apt/lists/*

## install SLATE
RUN git clone --recursive https://bitbucket.org/icl/slate && \
    cd slate && mkdir build && cd build && \
    cmake -Dbuild_tests=no .. && \
    cmake --build . && \
    cmake --install . && \
    cd ../../ && \
    rm -rf slate

# DEV use: oh-my-zsh instead of bash
# Uses "robbyrussell" theme (original Oh My Zsh theme), with no plugins
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.1/zsh-in-docker.sh)" -- \
    -t robbyrussell
ENTRYPOINT "/usr/bin/zsh"
WORKDIR /work

# python executable
RUN echo "alias python=python3" >> /root/.zshrc