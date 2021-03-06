# © Copyright IBM Corporation 2017, 2020.
# LICENSE: Apache License, Version 2.0 (http://www.apache.org/licenses/LICENSE-2.0)

########## Dockerfile for Sysdig version 0.26.7 #########
#
# This Dockerfile builds a basic installation of Sysdig.
#
# Sysdig is open source, system-level exploration: capture system state and activity from a running Linux instance, then save, filter and analyze.
# Sysdig is scriptable in Lua and includes a command line interface and a powerful interactive UI, csysdig, that runs in your terminal.
#
# To build this image, from the directory containing this Dockerfile
# (assuming that the file is named Dockerfile):
# docker build -t <image_name> .
#
# To run sysdig in a Docker container use below command
#  docker run -it --privileged -v /var/run/docker.sock:/host/var/run/docker.sock -v /dev:/host/dev -v /proc:/host/proc:ro -v
#   /boot:/host/boot:ro -v /lib/modules:/host/lib/modules:ro -v /usr:/host/usr:ro --name <container_name> <image_name>
# 
# Note: Please make sure you have kernel version 4.15.0 to build this Dockerfile.
#
# The official website
# https://www.sysdig.org/
##################################################################################

# Base Image
FROM s390x/ubuntu:18.04

ARG SYSDIG_VER=0.26.7

LABEL maintainer="LoZ Open Source Ecosystem (https://www.ibm.com/community/z/usergroups/opensource)"
LABEL RUN="docker run -i -t --privileged -v /var/run/docker.sock:/host/var/run/docker.sock -v /dev:/host/dev -v /proc:/host/proc:ro -v /boot:/host/boot:ro -v /lib/modules:/host/lib/modules:ro -v /usr:/host/usr:ro --name NAME IMAGE"

ENV SOURCE_ROOT=/tmp/source

WORKDIR $SOURCE_ROOT

# Install dependencies
RUN apt-get update \
        && apt-get install -y \
                automake \
                cmake \
                gcc \
                g++ \
                libelf-dev \
                linux-headers-$(uname -r) \
                lua5.1 \
                lua5.1-dev \
                patch \
                tar \
                git \
                curl \
                wget \
# Download source code
        && cd $SOURCE_ROOT \
        && git clone https://github.com/draios/sysdig.git \
        && cd sysdig      \
        && git checkout ${SYSDIG_VER}  \
        && mkdir build \
# Configure Sysdig
        && cd build \
        && cmake .. -DSYSDIG_VERSION=${SYSDIG_VER} \
# Build and Install Sysdig
        && make \
        && make install \
# Copy important content from build
        && mkdir -p /opt/sysdig \
        && cp $SOURCE_ROOT/sysdig/build/driver/sysdig-probe.ko /opt/sysdig \
        && cp -r $SOURCE_ROOT/sysdig/build/userspace/sysdig /opt/sysdig \
# Clean up cache data and remove dependencies which are not required
        &&      apt-get -y remove \
                cmake \
                gcc \
                g++ \
                linux-headers-$(uname -r) \
                lua5.1 \
                lua5.1-dev \
                patch \
                git  \
                wget \
        &&      apt-get autoremove -y\
        &&      apt autoremove -y \
        &&      rm -rf $SOURCE_ROOT \
        &&      apt-get clean \
        &&      rm -rf /var/lib/apt/lists/*

WORKDIR /opt/sysdig

CMD ["bash"]
# End of Dockerfile
