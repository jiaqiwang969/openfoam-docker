FROM centos:centos7

# build a container with lmod for singularity-hpc
# docker build -t singularity-hpc .

LABEL MAINTAINER @jiaqi

# Task 1: Preparations
RUN yum update
RUN yum -y install curl 
RUN yum -y findutils 
RUN yum -y git 
RUN yum -y gnupg2 
RUN yum -y hostname 
RUN yum -y tar 
RUN yum -y which 
RUN yum -y bzip2 
RUN yum -y xz
RUN yum -y iproute 
RUN yum -y redhat-lsb-core 
RUN yum -y make 
RUN yum -y automake 
RUN yum -y gcc 
RUN yum -y gcc-c++ 
RUN yum -y gcc-gfortran 
RUN yum -y patch 
RUN yum -y python-keyring 
RUN yum -y zlib-devel 
RUN yum -y openssl-devel 
RUN yum -y unzip 
RUN yum -y iproute
RUN yum -y python3 
RUN yum -y python3-pip 
RUN yum -y python3-setuptools 
RUN yum -y python3-devel 
RUN yum -y wget 
RUN yum -y unzip 

RUN yum -y epel-release 
RUN yum -y lua 
RUN yum -y lua-devel 
RUN yum -y lua-json 
RUN yum -y lua-lpeg 
RUN yum -y lua-posix 
RUN yum -y lua-term 
RUN yum -y lua-filesystem
RUN yum -y tcl 
RUN yum -y tcl-devel 

RUN useradd -m swmanager
RUN mkdir /opt/sw
RUN chown swmanager:swmanager /opt/sw

USER swmanager
RUN mkdir ~/build
WORKDIR ~/build
#Task 2: Install Lmod
RUN wget https://github.com/TACC/Lmod/archive/refs/tags/8.7.19.tar.gz
RUN tar -xf 8.7.19.tar.gz &&
cd Lmod-8.7.19/ &&
./configure --prefix=/opt/sw &&
make install
RUN ln -s /opt/sw/lmod/lmod/init/profile /etc/profile.d/modules.sh
RUN module avail
#Task 3: Install Spack
RUN wget https://github.com/spack/spack/releases/download/v0.19.1/spack-0.19.1.tar.gz
RUN tar -xf spack-0.19.1.tar.gz &&
cd spack-0.19.1/etc/spack &&
cp defaults/config.yaml . &&
sed -i 's/$spack/opt/spack//opt/sw/spack/g' config.yaml
RUN echo 'modules:' >> module.yaml &&
echo ' default:' >> module.yaml &&
echo ' enable:' >> module.yaml &&
echo ' - lmod' >> module.yaml &&
echo ' roots:' >> module.yaml &&
echo ' lmod: /opt/sw/modules' >> module.yaml &&
echo ' lmod:' >> module.yaml &&
echo ' hash_length: 0' >> module.yaml &&
echo ' hierarchy:' >> module.yaml &&
echo ' - mpi' >> module.yaml &&
echo ' core_compilers:' >> module.yaml &&
echo ' - gcc@8.5.0' >> module.yaml
RUN cd ~/build
RUN source ~/build/spack-0.19.1/share/spack/setup-env.sh
RUN echo '/opt/sw/modules/linux-centos8-x86_64/Core' > /opt/sw/lmod/lmod/init/.modulespath
#Task 4: Install a first package
RUN spack info tar
RUN spack spec tar
RUN spack install tar

#Task 5: Install miniconda3
ENV PATH /opt/conda/bin:${PATH}
ENV LANG C.UTF-8
RUN wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh &&
bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda &&
rm Miniconda3-latest-Linux-x86_64.sh


# If we don't run shpc through bash entrypoint, module commands not found
ENTRYPOINT ["/code//entrypoint.sh"]
