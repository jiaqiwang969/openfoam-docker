FROM nvidia/cuda:10.2-base

RUN apt-get update && apt-get install -y \
    bc \
    build-essential \
    glmark2 \
    htop \
    libcgal-dev \
    libcurl4-openssl-dev \
    libegl1 \
    libgl1 \
    libglfw3 \
    libglvnd0 \
    libglx0 \
    libopenmpi-dev \
    libscotch-dev \
    libx11-6 \
    libxext6 \
    openmpi-bin \
    python3-pip \
    qt5-default \
    software-properties-common \
    sudo \
    ssh \
    vim \
    wget; \
    rm -rf /var/lib/apt/lists/*

# Create a new user called foam
RUN useradd --user-group --create-home --shell /bin/bash foam ;\
    echo "foam ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Install OpenFOAM v9 including configuring for use by user=foam
# plus an extra environment variable to make OpenMPI play nice
RUN bash -c "wget -O - http://dl.openfoam.org/gpg.key | apt-key add -" && \
    add-apt-repository http://dl.openfoam.org/ubuntu

RUN apt-get update && apt-get install -y \
    openfoam9 && \
    rm -rf /var/lib/apt/lists/*

RUN echo "source /opt/openfoam9/etc/bashrc" >> /home/foam/.bashrc ;\
    echo "export OMPI_MCA_btl_vader_single_copy_mechanism=none" >> ~foam/.bashrc

RUN chown -R foam:foam /opt/openfoam9/

RUN wget -q --show-progress --progress=bar:force --timeout=5 --tries=10 -O /opt/paraview-headless.tar.gz "https://www.paraview.org/paraview-downloads/download.php?submit=Download&version=v5.10&type=binary&os=Linux&downloadFile=ParaView-5.10.0-osmesa-MPI-Linux-Python3.9-x86_64.tar.gz"

RUN tar -C /opt -xf /opt/paraview-headless.tar.gz && \
    mv /opt/ParaView-5.10.0-osmesa-MPI-Linux-Python3.9-x86_64/ /opt/paraview-headless && \
    sudo chown -R foam /opt/paraview-headless

ENV PATH /opt/paraview-headless/bin:$PATH
ENV LD_LIBRARY_PATH /opt/paraview-headless/lib:$LD_LIBRARY_PATH
ENV PYTHONPATH /home/foam/.local/lib/python3.6/site-packages
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES graphics,utility,compute
ENV QT_X11_NO_MITSHM=1

USER foam
