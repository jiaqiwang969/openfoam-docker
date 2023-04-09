FROM ubuntu:20.04

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y apt-utils git curl cmake g++ gfortran python3 python3-numpy python3-scipy python3-pip swig clang-tidy emacs-nox valgrind texlive texlive-latex-recommended texlive-latex-extra texinfo libreoffice fonts-cmu libpetsc-complex-dev libslepc-complex3.12-dev python3-dev libopenblas-dev libfltk1.3-dev libfreetype6-dev libgl1-mesa-dev libxi-dev libxmu-dev mesa-common-dev tcl-dev tk-dev libhdf5-dev libcgns-dev libxft-dev libxinerama-dev libxcursor-dev libxfixes-dev libocct-foundation-dev libocct-data-exchange-dev libocct-ocaf-dev libopenmpi-dev libboost-dev cargo clang gcovr && apt-get clean

RUN git clone https://github.com/flexi-framework/hopr.git

RUN mkdir build

RUN cd build

RUN cmake ..

RUN make install

