FROM nvidia/cuda:9.2-devel-ubuntu16.04
#ref:https://sarus.readthedocs.io/en/stable/cookbook/pyfr/pyfr.html
#https://github.com/sisl/deep_flow_control
LABEL com.pyfr.version="1.7.6"
LABEL com.python.version="3.5"

# Install system dependencies
# Metis is a library for mesh partitioning:
# http://glaros.dtc.umn.edu/gkhome/metis/metis/overview
RUN apt-get update && apt-get install -y   \
        unzip                       \
        wget                        \
        build-essential             \
        gfortran-5                  \
        strace                      \
        realpath                    \
        libopenblas-dev             \
        liblapack-dev               \
        python3-dev                 \
        python3-setuptools          \
        python3-pip                 \
        libhdf5-dev                 \
        libmetis-dev                \
        --no-install-recommends  && \
    rm -rf /var/lib/apt/lists/*

# Install MPICH 3.1.4
RUN wget -q http://www.mpich.org/static/downloads/3.1.4/mpich-3.1.4.tar.gz && \
    tar xvf mpich-3.1.4.tar.gz                       && \
    cd mpich-3.1.4                                   && \
    ./configure --disable-fortran --prefix=/usr      && \
    make -j$(nproc)                                  && \
    make install                                     && \
    cd ..                                            && \
    rm -rf mpich-3.1.4.tar.gz mpich-3.1.4            && \
    ldconfig

# Create new user
RUN useradd docker
WORKDIR /home/docker

# Install Python dependencies
RUN pip3 install numpy>=1.8         
RUN pip3 install pytools>=2016.2.1  
RUN pip3 install mako>=1.0.0        
RUN pip3 install appdirs>=1.4.0     
RUN pip3 install mpi4py>=2.0     
RUN pip3 install pycuda>=2015.1     
RUN pip3 install h5py>=2.6.0     
RUN pip3 install tensorflow
RUN    wget -q -O GiMMiK-2.1.tar.gz    \
        https://github.com/vincentlab/GiMMiK/archive/v2.1.tar.gz && \
    tar -xvzf GiMMiK-2.1.tar.gz  && \
    cd GiMMiK-2.1                && \
    python3 setup.py install     && \
    cd ..                        && \
    rm -rf GiMMiK-2.1.tar.gz GiMMiK-2.1
# Set base directory for pyCUDA cache
ENV XDG_CACHE_HOME /tmp


# Install PyFR and https://github.com/sisl/deep_flow_control
RUN cd /opt && \
    git clone https://github.com/PyFR/PyFR.git  && \
    git clone https://github.com/sisl/deep_flow_control.git && \
    cd PyFR && \
    git checkout v1.7.6 && \
    cp -rf ../deep_flow_control/mpc_files/controller.patch . && \
    git apply controller.patch && \
    python3 setup.py install     && \
    cd ..                        
RUN 

CMD ["pyfr --help"]
