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

# Disable Prompt During Packages Installation
ARG DEBIAN_FRONTEND=noninteractive

ENV ENABLE_INIT_DAEMON false
ENV INIT_DAEMON_BASE_URI http://identifier/init-daemon
ENV INIT_DAEMON_STEP spark_master_init

# JAVA_HOME is already set in base image
ENV SPARK_HOME="/usr/local/spark"
ENV PATH=$SPARK_HOME/bin/:$SPARK_HOME/sbin/:$PATH
# ENV SPARK_DIST_CLASSPATH=$(hadoop classpath)

ENV BASE_URL=https://archive.apache.org/dist/spark/
ENV SPARK_VERSION=3.2.1
# Not used
#ENV HADOOP_VERSION=3.3

COPY wait-for-step.sh /
COPY execute-step.sh /
COPY finish-step.sh /

# && wget --no-verbose -O ${BASE_URL}/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends \
    wget \
    gcc \
    python3 \
    python3-dev \
    python3-pip 

RUN wget ${BASE_URL}/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-without-hadoop.tgz \
    && tar -xvzf spark-${SPARK_VERSION}-bin-without-hadoop.tgz \
    && mv spark-${SPARK_VERSION}-bin-without-hadoop /usr/local/spark \
    && rm spark-${SPARK_VERSION}-bin-without-hadoop.tgz \
    && pip3 install jupyterlab findspark \
    && cd / \
    && chmod +x *.sh  \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /var/cache/apt/archives \
    && apt clean

# install jupyter lab, and findspark (for easily using spark in jupyter)

#Give permission to execute scripts
# RUN chmod +x /wait-for-step.sh && chmod +x /execute-step.sh && chmod +x /finish-step.sh

# Fix the value of PYTHONHASHSEED
# Note: this is needed when you use Python 3.3 or greater
ENV PYTHONHASHSEED 1


# Spark files can also be run as this, if you prefer this to jupyter
#spark-submit <<<./your-spark-file.py>>> --master yarn --deploy-mode cluster