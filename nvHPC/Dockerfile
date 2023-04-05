# 
# HPC Base image
# Contents:
#   FFTW version 3.3.8
#   HDF5 version 1.10.6
#   Mellanox OFED version 5.0-2.1.8.0
#   NVIDIA HPC SDK version 20.7
#   OpenMPI version 4.0.4
#   Python 2 and 3 (upstream)
# 

FROM ubuntu:20.04 AS devel

# Python
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        python \
        python3 && \
    rm -rf /var/lib/apt/lists/*

# NVIDIA HPC SDK version 20.7
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        ca-certificates \
        gnupg \
        wget \
        gcc \ 
        g++ \ 
        gfortran \
        libnuma1 \
        libatomic1 && \
    rm -rf /var/lib/apt/lists/*
RUN apt-get update -y   
RUN wget https://developer.download.nvidia.com/hpc-sdk/20.7/nvhpc-20-7_20.7_amd64.deb \
     https://developer.download.nvidia.com/hpc-sdk/20.7/nvhpc-20-7-cuda-multi_20.7_amd64.deb
RUN apt-get install ./nvhpc-20-7_20.7_amd64.deb ./nvhpc-20-7-cuda-multi_20.7_amd64.deb
ENV CPATH=/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/comm_libs/nvshmem/include:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/comm_libs/nccl/include:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/compilers/extras/qd/include/qd:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/math_libs/include:$CPATH \
    LD_LIBRARY_PATH=/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/comm_libs/nvshmem/lib:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/comm_libs/nccl/lib:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/math_libs/lib64:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/compilers/lib:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/cuda/lib64:$LD_LIBRARY_PATH \
    MANPATH=/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/compilers/man:$MANPATH \
    PATH=/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/comm_libs/nvshmem/bin:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/comm_libs/nccl/bin:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/profilers/bin:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/compilers/bin:/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/cuda/bin:$PATH

# Mellanox OFED version 5.0-2.1.8.0
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        ca-certificates \
        gnupg \
        wget && \
    rm -rf /var/lib/apt/lists/*
RUN wget -qO - https://www.mellanox.com/downloads/ofed/RPM-GPG-KEY-Mellanox | apt-key add - && \
    mkdir -p /etc/apt/sources.list.d && wget -q -nc --no-check-certificate -P /etc/apt/sources.list.d https://linux.mellanox.com/public/repo/mlnx_ofed/5.0-2.1.8.0/ubuntu20.04/mellanox_mlnx_ofed.list && \
    apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        ibverbs-providers \
        ibverbs-utils \
        libibmad-dev \
        libibmad5 \
        libibumad-dev \
        libibumad3 \
        libibverbs-dev \
        libibverbs1 \
        librdmacm-dev \
        librdmacm1 && \
    rm -rf /var/lib/apt/lists/*

# OpenMPI version 4.0.4
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        bzip2 \
        file \
        hwloc \
        libnuma-dev \
        make \
        openssh-client \
        perl \
        tar \
        wget && \
    rm -rf /var/lib/apt/lists/*
RUN mkdir -p /var/tmp && wget -q -nc --no-check-certificate -P /var/tmp https://www.open-mpi.org/software/ompi/v4.0/downloads/openmpi-4.0.4.tar.bz2 && \
    mkdir -p /var/tmp && tar -x -f /var/tmp/openmpi-4.0.4.tar.bz2 -C /var/tmp -j && \
    cd /var/tmp/openmpi-4.0.4 &&  CC=nvc CFLAGS=-O1 CXX=nvc++ F77=nvfortran F90=nvfortran FC=nvfortran FCFLAGS='-fpic -DPIC' ./configure --prefix=/usr/local/openmpi --disable-getpwuid --enable-orterun-prefix-by-default --with-cuda=/opt/nvidia/hpc_sdk/Linux_x86_64/20.7/cuda --with-verbs && \
    make -j$(nproc) && \
    make -j$(nproc) install && \
    rm -rf /var/tmp/openmpi-4.0.4 /var/tmp/openmpi-4.0.4.tar.bz2
ENV LD_LIBRARY_PATH=/usr/local/openmpi/lib:$LD_LIBRARY_PATH \
    PATH=/usr/local/openmpi/bin:$PATH

# FFTW version 3.3.8
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        file \
        make \
        wget && \
    rm -rf /var/lib/apt/lists/*
RUN mkdir -p /var/tmp && wget -q -nc --no-check-certificate -P /var/tmp ftp://ftp.fftw.org/pub/fftw/fftw-3.3.8.tar.gz && \
    mkdir -p /var/tmp && tar -x -f /var/tmp/fftw-3.3.8.tar.gz -C /var/tmp -z && \
    cd /var/tmp/fftw-3.3.8 &&  CC=nvc CXX=nvc++ F77=nvfortran F90=nvfortran FC=nvfortran ./configure --prefix=/usr/local/fftw --enable-mpi --enable-openmp --enable-shared --enable-sse2 --enable-threads && \
    make -j$(nproc) && \
    make -j$(nproc) install && \
    rm -rf /var/tmp/fftw-3.3.8 /var/tmp/fftw-3.3.8.tar.gz
ENV LD_LIBRARY_PATH=/usr/local/fftw/lib:$LD_LIBRARY_PATH

# HDF5 version 1.10.6
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        bzip2 \
        file \
        make \
        wget \
        zlib1g-dev && \
    rm -rf /var/lib/apt/lists/*
RUN mkdir -p /var/tmp && wget -q -nc --no-check-certificate -P /var/tmp http://www.hdfgroup.org/ftp/HDF5/releases/hdf5-1.10/hdf5-1.10.6/src/hdf5-1.10.6.tar.bz2 && \
    mkdir -p /var/tmp && tar -x -f /var/tmp/hdf5-1.10.6.tar.bz2 -C /var/tmp -j && \
    cd /var/tmp/hdf5-1.10.6 &&  CC=nvc CXX=nvc++ F77=nvfortran F90=nvfortran FC=nvfortran FCFLAGS='-fpic -DPIC' ./configure --prefix=/usr/local/hdf5 --enable-cxx --enable-fortran && \
    make -j$(nproc) && \
    make -j$(nproc) install && \
    rm -rf /var/tmp/hdf5-1.10.6 /var/tmp/hdf5-1.10.6.tar.bz2
ENV CPATH=/usr/local/hdf5/include:$CPATH \
    HDF5_DIR=/usr/local/hdf5 \
    LD_LIBRARY_PATH=/usr/local/hdf5/lib:$LD_LIBRARY_PATH \
    LIBRARY_PATH=/usr/local/hdf5/lib:$LIBRARY_PATH \
    PATH=/usr/local/hdf5/bin:$PATH

ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility \
    NVIDIA_REQUIRE_CUDA="cuda>=10.1 brand=tesla,driver>=384,driver<385 brand=tesla,driver>=396,driver<397 brand=tesla,driver>=410,driver<411" \
    NVIDIA_VISIBLE_DEVICES=all

ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility \
    NVIDIA_REQUIRE_CUDA="cuda>=10.1 brand=tesla,driver>=384,driver<385 brand=tesla,driver>=396,driver<397 brand=tesla,driver>=410,driver<411" \
    NVIDIA_VISIBLE_DEVICES=all
