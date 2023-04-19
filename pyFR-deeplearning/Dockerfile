FROM centos:centos8

# build a container with lmod for singularity-hpc
# docker build -t singularity-hpc .

LABEL MAINTAINER @jiaqi

# Task 1: Preparations
RUN yum -y update && \
    yum -y install curl findutils git gnupg2 hostname tar which bzip2 xz \
    iproute redhat-lsb-core make automake gcc gcc-c++ gcc-gfortran patch \
    python-keyring zlib-devel openssl-devel unzip iproute \
    python3 python3-pip python3-setuptools python3-devel wget unzip && \
    yum -y install epel-release lua lua-devel lua-json lua-lpeg lua-posix lua-term lua-filesystem && \
    yum -y install tcl tcl-devel && \
    useradd -m swmanager && \
    mkdir /opt/sw && \
    chown swmanager:swmanager /opt/sw

USER swmanager
WORKDIR /home/swmanager/build

# Task 2: Install Lmod
RUN wget https://github.com/TACC/Lmod/archive/refs/tags/8.7.19.tar.gz && \
    tar -xf 8.7.19.tar.gz && \
    cd Lmod-8.7.19/ && \
    ./configure --prefix=/opt/sw && \
    make install && \
    echo 'modules:' >> module.yaml && \
    echo '  default:' >> module.yaml && \
    echo '    enable:' >> module.yaml && \
    echo '      - lmod' >> module.yaml && \
    echo '    roots:' >> module.yaml && \
    echo '      lmod: /opt/sw/modules' >> module.yaml && \
    echo '    lmod:' >> module.yaml && \
    echo '      hash_length: 0' >> module.yaml && \
    echo '      hierarchy:' >> module.yaml && \
    echo '        - mpi' >> module.yaml && \
    echo '      core_compilers:' >> module.yaml && \
    echo '        - gcc@8.5.0' >> module.yaml && \
    ln -s /opt/sw/lmod/lmod/init/profile /etc/profile.d/modules.sh && \
    cd ..

# Task 3: Install Spack
RUN wget https://github.com/spack/spack/releases/download/v0.19.1/spack-0.19.1.tar.gz && \
    tar -xf spack-0.19.1.tar.gz && \
    cd spack-0.19.1 && \
    cp etc/spack/defaults/config.yaml etc/spack/config.yaml && \
    sed -i 's/\$spack\/opt\/spack/\/opt\/sw\/spack/g' etc/spack/config.yaml && \
    source /home/swmanager/build/spack-0.19.1/share/spack/setup-env.sh && \
    cd ..

# Task 4: Install a first package
RUN spack install tar

# Task 5: Install miniconda3
ENV PATH /opt/conda/bin:${PATH}
ENV LANG C.UTF-8
RUN wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda && \
    rm Miniconda3-latest-Linux-x86_64.sh

WORKDIR /home/swmanager





# If we don't run shpc through bash entrypoint, module commands not found
ENTRYPOINT ["/code/entrypoint.sh"]
