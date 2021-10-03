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
#        # graphviz to visualise CMake dependency  \
#        graphviz
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

ENV MKLROOT='/opt/intel/oneapi/mkl/latest'
ENV LD_LIBRARY_PATH='/opt/intel/oneapi/vpl/2021.4.0/lib:/opt/intel/oneapi/tbb/2021.3.0/env/../lib/intel64/gcc4.8:/opt/intel/oneapi/mpi/2021.3.0//libfabric/lib:/opt/intel/oneapi/mpi/2021.3.0//lib/release:/opt/intel/oneapi/mpi/2021.3.0//lib:/opt/intel/oneapi/mkl/2021.3.0/lib/intel64:/opt/intel/oneapi/itac/2021.3.0/slib:/opt/intel/oneapi/ippcp/2021.3.0/lib/intel64:/opt/intel/oneapi/ipp/2021.3.0/lib/intel64:/opt/intel/oneapi/dnnl/2021.3.0/cpu_dpcpp_gpu_dpcpp/lib:/opt/intel/oneapi/debugger/10.1.2/gdb/intel64/lib:/opt/intel/oneapi/debugger/10.1.2/libipt/intel64/lib:/opt/intel/oneapi/debugger/10.1.2/dep/lib:/opt/intel/oneapi/dal/2021.3.0/lib/intel64:/opt/intel/oneapi/compiler/2021.3.0/linux/lib:/opt/intel/oneapi/compiler/2021.3.0/linux/lib/x64:/opt/intel/oneapi/compiler/2021.3.0/linux/lib/emu:/opt/intel/oneapi/compiler/2021.3.0/linux/lib/oclfpga/host/linux64/lib:/opt/intel/oneapi/compiler/2021.3.0/linux/lib/oclfpga/linux64/lib:/opt/intel/oneapi/compiler/2021.3.0/linux/compiler/lib/intel64_lin:/opt/intel/oneapi/ccl/2021.3.0/lib/cpu_gpu_dpcpp'
ENV LIBRARY_PATH='/opt/intel/oneapi/vpl/2021.4.0/lib:/opt/intel/oneapi/tbb/2021.3.0/env/../lib/intel64/gcc4.8:/opt/intel/oneapi/mpi/2021.3.0//libfabric/lib:/opt/intel/oneapi/mpi/2021.3.0//lib/release:/opt/intel/oneapi/mpi/2021.3.0//lib:/opt/intel/oneapi/mkl/2021.3.0/lib/intel64:/opt/intel/oneapi/ippcp/2021.3.0/lib/intel64:/opt/intel/oneapi/ipp/2021.3.0/lib/intel64:/opt/intel/oneapi/dnnl/2021.3.0/cpu_dpcpp_gpu_dpcpp/lib:/opt/intel/oneapi/dal/2021.3.0/lib/intel64:/opt/intel/oneapi/compiler/2021.3.0/linux/compiler/lib/intel64_lin:/opt/intel/oneapi/compiler/2021.3.0/linux/lib:/opt/intel/oneapi/clck/2021.3.0/lib/intel64:/opt/intel/oneapi/ccl/2021.3.0/lib/cpu_gpu_dpcpp'

# install SLATE
RUN git clone --recursive https://bitbucket.org/icl/slate && \
    cd slate && mkdir build && cd build && \
    cmake -Dblas=mkl -Dblas_threaded=1 .. && \
    cmake --build . --target slate && \
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