FROM ubuntu:20.04

LABEL maintainer="Jayachander Surbiryala <jayachander.it@gmail.com>"

# Disable Prompt During Packages Installation
ARG DEBIAN_FRONTEND=noninteractive

# Update and Install 
RUN apt-get update \
	&& apt-get upgrade -y \
	&& apt-get install -y --no-install-recommends \
	openjdk-8-jdk \
	net-tools \
	curl \
	netcat \
	gnupg \
	libsnappy-dev \
	# vim \
	&& rm -rf /var/lib/apt/lists/* \
	&& apt clean

RUN ARCH="$(dpkg --print-architecture)";

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-${ARCH}/

RUN curl -O https://dist.apache.org/repos/dist/release/hadoop/common/KEYS

RUN gpg --import KEYS

ENV HADOOP_VERSION=3.3.2
ENV HADOOP_URL=https://www.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz

RUN set -x \
    && curl -fSL "$HADOOP_URL" -o /tmp/hadoop.tar.gz \
    && curl -fSL "$HADOOP_URL.asc" -o /tmp/hadoop.tar.gz.asc \
    && gpg --verify /tmp/hadoop.tar.gz.asc \
    && tar -xvf /tmp/hadoop.tar.gz -C /opt/ \
    && rm /tmp/hadoop.tar.gz*

RUN ln -s /opt/hadoop-$HADOOP_VERSION/etc/hadoop /etc/hadoop

RUN mkdir /opt/hadoop-$HADOOP_VERSION/logs

RUN mkdir /hadoop-data

ENV HADOOP_HOME=/opt/hadoop-$HADOOP_VERSION
ENV HADOOP_CONF_DIR=/etc/hadoop
ENV MULTIHOMED_NETWORK=1
ENV USER=root
ENV PATH=$HADOOP_HOME/bin/:$HADOOP_HOME/sbin:$PATH

ADD entrypoint.sh /entrypoint.sh

RUN chmod a+x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

# commented and added above
#FROM jayachander/hadoop-base:1.0.0-hadoop3.3.2-java8
#
#LABEL maintainer="Jayachander Surbiryala <jayachander.it@gmail.com>"

HEALTHCHECK CMD curl -f http://localhost:8188/ || exit 1

ENV YARN_CONF_yarn_timeline___service_leveldb___timeline___store_path=/hadoop/yarn/timeline
RUN mkdir -p /hadoop/yarn/timeline
VOLUME /hadoop/yarn/timeline

ADD run.sh /run.sh
RUN chmod a+x /run.sh

EXPOSE 8188

CMD ["/run.sh"]
