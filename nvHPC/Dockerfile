FROM jameshclrk/parmetis:latest

MAINTAINER James Clark <james.clark@stfc.ac.uk>

ARG HDF5_VERSION=1.8.20
ARG CGNS_VERSION=3.3.1
ARG HDF5_INSTALL_DIR="/opt/hdf5"
ARG CGNS_INSTALL_DIR="/opt/cgns"

RUN set -x \
    && curl -fSL "https://support.hdfgroup.org/ftp/HDF5/current18/src/hdf5-$HDF5_VERSION.tar.gz" -o hdf5.tar.gz \
	&& mkdir -p /usr/src/hdf5 \
	&& tar -xf hdf5.tar.gz -C /usr/src/hdf5 --strip-components=1 \
	&& rm hdf5.tar.gz* \
	&& cd /usr/src/hdf5 \
	&& CC=mpicc ./configure --prefix=${HDF5_INSTALL_DIR} --enable-parallel \
	&& make -j"$(nproc)" \
	&& make install \
	&& rm -rf /usr/src/hdf5

RUN set -x \
	&& curl -fSL "https://github.com/CGNS/CGNS/archive/v$CGNS_VERSION.tar.gz" -o cgns.tar.gz \
	&& mkdir -p /usr/src/cgns \
	&& tar -xf cgns.tar.gz -C /usr/src/cgns --strip-components=1 \
	&& rm cgns.tar.gz* \
	&& cd /usr/src/cgns \
	&& mkdir build && cd build \
	&& PATH="${HDF5_INSTALL_DIR}/bin:$PATH" cmake -DCGNS_ENABLE_HDF5=ON -DCMAKE_INSTALL_PREFIX:PATH=${CGNS_INSTALL_DIR} -DHDF5_NEED_MPI=ON .. \
	&& make -j"$(nproc)" \
	&& make install \
	&& rm -rf /usr/src/cgns

ENV LD_LIBRARY_PATH="${CGNS_INSTALL_DIR}/lib:${HDF5_INSTALL_DIR}/lib:${LD_LIBRARY_PATH}" PATH="${CGNS_INSTALL_DIR}/bin:${HDF5_INSTALL_DIR}/bin:$PATH"
ENV HDF5_DIR="${HDF5_INSTALL_DIR}"
ENV CGNS_DIR="${CGNS_INSTALL_DIR}"
