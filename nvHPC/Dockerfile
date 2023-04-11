FROM debian:bullseye as devel

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update -y && \
    apt install -y wget git libssl-dev build-essential gfortran cmake


# BLAS and Lapack
RUN wget https://github.com/Reference-LAPACK/lapack/archive/v3.9.0.tar.gz --directory-prefix=/tmp/ &&\
    cd /tmp && tar -xvzf v3.9.0.tar.gz && mkdir -p /tmp/lapack-3.9.0/build && \
    cd /tmp/lapack-3.9.0/build && \
    /usr/local/bin/cmake -DCMAKE_INSTALL_PREFIX=/apps/hopr /tmp/lapack-3.9.0 &&  \
    make && make install &&\
    rm -rf /tmp/lapack-3.9.0

ENV LD_LIBRARY_PATH=/apps/hopr/lib:$LD_LIBRARY_PATH \
    PATH=/apps/hopr/bin:$PATH

#HOPR
RUN git clone https://github.com/FluidNumerics/hopr.git /tmp/hopr && \
    mkdir /tmp/hopr/build && \
    cd /tmp/hopr/build && \
    /usr/local/bin/cmake -DCMAKE_INSTALL_PREFIX=/apps/hopr /tmp/hopr &&\
    make && make install &&\
    rm -rf /tmp/hopr

FROM debian:bullseye

COPY --from=devel /apps/ /apps/
