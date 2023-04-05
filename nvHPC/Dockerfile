FROM nvidia/cuda:11.5.1-devel-ubuntu20.04

# Cmake
RUN apt-get update -y && \
  apt-get -qq install apt-transport-https ca-certificates gnupg software-properties-common wget && \ 
  wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | tee /etc/apt/trusted.gpg.d/kitware.gpg >/dev/null && \
  apt-add-repository 'deb https://apt.kitware.com/ubuntu/ focal main' && \
  apt-get install -qq git cmake libfftw3-dev && \
  cmake --version
