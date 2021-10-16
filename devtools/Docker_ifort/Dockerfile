# Adapted from Intel's oneapi-hpckit docker image
# SPDX-License-Identifier: BSD-3-Clause

FROM ubuntu:18.04 as build

RUN mkdir -p /opt/build && mkdir -p /opt/dist

RUN apt-get update && \
  DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    ca-certificates curl

# install cmake
RUN cd /opt/build && \
    curl -LO https://github.com/Kitware/CMake/releases/download/v3.20.2/cmake-3.20.2-linux-x86_64.sh && \
    mkdir -p /opt/dist//usr/local && \
    /bin/bash cmake-3.20.2-linux-x86_64.sh --prefix=/opt/dist//usr/local --skip-license

FROM ubuntu:18.04

COPY --from=build /opt/dist /

ARG DEBIAN_FRONTEND=noninteractive
ARG APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=1

# install OS tools
RUN apt-get update -y && \
    apt-get install -y --no-install-recommends -o=Dpkg::Use-Pty=0 \
    build-essential \
    pkg-config \
    ca-certificates \
    gnupg \
    libarchive13 \
    wget

COPY third-party-programs.txt /

# add apt repo public key
ARG url=https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2023.PUB
RUN wget $url
RUN file=$(basename "$url") && \
    apt-key add "$file" && \
    rm "$file"

# configure the repository
ARG repo=https://apt.repos.intel.com/oneapi
RUN echo "deb $repo all main" > /etc/apt/sources.list.d/oneAPI.list

# disable cert check
ARG disable_cert_check=""
RUN if [ "$disable_cert_check" ] ; then echo "Acquire::https::Verify-Peer \"false\";\nAcquire::https::Verify-Host \"false\";" > /etc/apt/apt.conf.d/99-disable-cert-check ; fi

# install Intel(R) oneAPI Packages
RUN apt-get update -y && \
apt-get install -y --no-install-recommends -o=Dpkg::Use-Pty=0 \
    intel-oneapi-compiler-dpcpp-cpp \
    intel-oneapi-mkl-devel \
    intel-oneapi-compiler-dpcpp-cpp-and-cpp-classic \
    intel-oneapi-compiler-fortran \
    intel-oneapi-mpi-devel \
    --

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

ENTRYPOINT ["bash", "-c", "source /root/.oneapi_env_vars && \"$@\"", "bash"]
