FROM ubuntu:latest
MAINTAINER "Tamas K Stenczel <stenczelt@gmail.com>"

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
    && rm -rf /var/lib/apt/lists/*

# MKL installation
RUN apt update && apt install -y apt-transport-https gnupg && \
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB && \
    apt-key add GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB && \
    sh -c 'echo deb https://apt.repos.intel.com/oneapi all main > /etc/apt/sources.list.d/intel-oneapi.list' && \
    apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y intel-oneapi-mkl-devel && \
    echo "/opt/intel/oneapi/mkl/latest/lib/intel64" >> /etc/ld.so.conf.d/intel.conf && \
    ldconfig && \
    echo "source /opt/intel/oneapi/setvars.sh intel64" >> /etc/bash.bashrc

# setvars.sh environment variables
RUN env > default_env_vars; \
    . /opt/intel/oneapi/setvars.sh; \
    env > env_vars; \
    diff default_env_vars env_vars \
    | grep ">" | sed  s/..// \
    | sort \
    | sed 's/^/export /' | sed "s/=/='/" | sed "s/$/'/" \
    >> /root/.oneapi_env_vars; \
    # clean up
    rm *env_vars; \
    rm -rf /var/lib/apt/lists/*

# install SLATE
RUN git clone --recursive https://bitbucket.org/icl/slate && \
    cd slate && mkdir build && cd build && \
    cmake -Dblas=mkl -Dbuild_tests=no .. && \
    cmake --build . && \
    cmake --install . && \
    cd ../../ && \
    rm -rf slate

# DEV use: oh-my-zsh instead of bash
# Uses "robbyrussell" theme (original Oh My Zsh theme), with no plugins
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.1/zsh-in-docker.sh)" -- \
    -t robbyrussell \
ENTRYPOINT ["/usr/bin/zsh", "-c", "source /root/.oneapi_env_vars && \"$@\"", "/usr/bin/zsh"]
WORKDIR /work

# python executable
RUN echo "alias python=python3" >> /root/.zshrc
