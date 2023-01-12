FROM ubuntu:20.04

LABEL maintainer="Jayachander Surbiryala <jayachander.it@gmail.com>"

# Disable Prompt During Packages Installation
ARG DEBIAN_FRONTEND=noninteractive

ARG TARGETARCH
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-$TARGETARCH

# Install OpenJDK 8
RUN \
  apt-get update && \
  apt-get install -y openjdk-8-jdk && \
  rm -rf /var/lib/apt/lists/*

# Install Python
RUN \
    apt-get update && \
    apt-get install -y python3 python3-dev python3-pip python3-virtualenv && \
    rm -rf /var/lib/apt/lists/*

# Install PySpark and Numpy
RUN \
    pip3 install numpy && \
    pip3 install pyspark

# Define working directory
WORKDIR /data

# Define default command
CMD ["bash"]