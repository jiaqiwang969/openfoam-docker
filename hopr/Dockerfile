FROM unifem/fenics-desktop:latest
LABEL maintainer "Xiangmin Jiao <xmjiao@gmail.com>"

USER root
WORKDIR /tmp

# Install system packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        automake autogen autoconf libtool \
        libhdf5-100 \
        libhdf5-dev \
        libhdf5-openmpi-100 \
        libhdf5-openmpi-dev \
        hdf5-tools \
        libnetcdf-dev netcdf-bin \
        libmetis5 libmetis-dev \
        \
        tk-dev \
        libglu1-mesa-dev \
        libxmu-dev && \
    apt-get clean && \
    \
    pip3 install -U \
        cython \
        nose && \
    \
    mkdir -p /usr/lib/hdf5-openmpi && \
    ln -s -f /usr/include/hdf5/openmpi /usr/lib/hdf5-openmpi/include && \
    ln -s -f /usr/lib/x86_64-linux-gnu/hdf5/openmpi /usr/lib/hdf5-openmpi/lib && \
    \
    mkdir -p /usr/lib/hdf5-serial && \
    ln -s -f /usr/include/hdf5/serial /usr/lib/hdf5-serial/include && \
    ln -s -f /usr/lib/x86_64-linux-gnu/hdf5/serial /usr/lib/hdf5-serial/lib && \
    \
    rm -rf /var/lib/apt/lists/* /tmp/*

# Install CGNS from source with parallel enabled
RUN cd /tmp && \
    git clone --depth=1 -b master https://github.com/CGNS/CGNS.git && \
    cd CGNS/src && \
    export CC="mpicc" && \
    export LIBS="-Wl,--no-as-needed -ldl -lz -lsz -lpthread" && \
    ./configure --enable-64bit --with-zlib --with-hdf5=/usr/lib/hdf5-openmpi \
        --enable-parallel --enable-cgnstools --enable-lfs --enable-shared && \
    sed -i 's/TKINCS =/TKINCS = -I\/usr\/include\/tcl/' cgnstools/make.defs && \
    make -j2 && make install && \
    rm -rf /tmp/CGNS

# Install pyCGNS from source
RUN cd /tmp && \
    git clone --depth=1 -b master https://github.com/unifem/pyCGNS.git && \
    cd pyCGNS && \
    python3 setup.py build \
        --includes=/usr/include/hdf5/openmpi:/usr/include/openmpi \
        --libraries=/usr/lib/x86_64-linux-gnu/hdf5/openmpi && \
    python3 setup.py install && \
    rm -rf /tmp/pyCGNS

# Install dopr from source
RUN cd /opt && \
    git clone --depth=1 -b master https://github.com/flexi-framework/hopr.git && \
    cd hopr && \
    mkdir build && \
    cd build && \
    cmake .. -DLIBS_BUILD_HDF5=OFF && \
    make -j2 && make install

