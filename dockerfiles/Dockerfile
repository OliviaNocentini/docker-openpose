FROM nvidia/cuda:11.4.2-cudnn8-devel-ubuntu20.04
LABEL org.opencontainers.image.title="Openpose Docker Image"
LABEL org.opencontainers.image.description="Infrastructure for installing Openpose"
LABEL org.opencontainers.image.source="https://raw.githubusercontent.com/OliviaNocentini/docker-openpose/main/dockerfiles/Dockerfile"
LABEL org.opencontainers.image.authors="Olivia Nocentini <o.nocentini@gmail.com>"

ARG USERNAME=user
ARG USERID=1000
ARG OPENPOSE_VERSION=v1.7.0
ARG GPU_MODE=CUDA
SHELL ["/bin/bash", "-c"]

# Non-interactive installation mode
ENV DEBIAN_FRONTEND=noninteractive

# Update apt database
RUN apt update

# Set the locale
RUN apt install -y -qq apt-utils locales && rm -rf /var/lib/apt/lists/*
RUN locale-gen en_US en_US.UTF-8
RUN update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG=en_US.UTF-8

# Install essentials
COPY deps.sh .
RUN  chmod +x ./deps.sh
RUN ./deps.sh && rm ./deps.sh && rm -rf /var/lib/apt/lists/*

# install ros
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' && \
    curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add - && \
    apt update && \
    apt install -y -qq ros-noetic-desktop-full && \
    rm -rf /var/lib/apt/lists/*

# Create the user
RUN useradd --create-home -s /bin/bash --no-user-group -u $USERID $USERNAME && \
    adduser $USERNAME sudo && \
    echo "$USERNAME ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

USER $USERNAME
WORKDIR /home/$USERNAME

# install OpenPose
RUN mkdir robot-code && \
    cd robot-code && \
    git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git && \
    cd openpose && \
    git checkout ${OPENPOSE_VERSION} && \
    git submodule update --init --recursive --remote && \
    sudo bash ./scripts/ubuntu/install_deps.sh && \
    sudo pip3 install numpy opencv-python && \
    mkdir build && \
    cd build && \
    cmake -DBUILD_PYTHON=ON  -DGPU_MODE=${GPU_MODE}.. && \
    make -j`nproc` && \
    sudo make install -j`nproc` && \
    sudo rm -rf /var/lib/apt/lists/*
 
# setup enviroment variable 
RUN echo "source /opt/ros/noetic/setup.bash" >> /home/$USERNAME/.bashrc && \
    echo 'export PYTHONPATH=$PYTHONPATH:/usr/local/python' >> /home/$USERNAME/.bashrc

CMD [ "bash" ]
