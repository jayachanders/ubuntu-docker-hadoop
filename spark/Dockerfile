FROM jayachander/hadoop-base:1.0.0-hadoop3.3.2-java8
#FROM ubuntu:20.04

LABEL maintainer="Jayachander Surbiryala <jayachander.it@gmail.com>"

# Disable Prompt During Packages Installation
ARG DEBIAN_FRONTEND=noninteractive

ENV ENABLE_INIT_DAEMON false
ENV INIT_DAEMON_BASE_URI http://identifier/init-daemon
ENV INIT_DAEMON_STEP spark_master_init

# JAVA_HOME is already set in base image
#ARG TARGETARCH
#ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-$TARGETARCH

ENV SPARK_HOME="/usr/local/spark"
ENV PATH=$SPARK_HOME/bin/:$SPARK_HOME/sbin/:$PATH
# ENV SPARK_DIST_CLASSPATH=$(hadoop classpath)

ENV BASE_URL=https://archive.apache.org/dist/spark/
ENV SPARK_VERSION=3.2.1
# Not used
#ENV HADOOP_VERSION=3.3.2

COPY wait-for-step.sh /
COPY execute-step.sh /
COPY finish-step.sh /

# && wget --no-verbose -O ${BASE_URL}/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends \
#    openjdk-8-jdk \
    wget \
    gcc \
    python3 \
    python3-dev \
    python3-pip \
    python3-virtualenv 

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

# Define working directory
WORKDIR /

# Define default command
CMD ["sleep", "infinity"]

# Spark files can also be run as this, if you prefer this to jupyter
#spark-submit <<<./your-spark-file.py>>> --master yarn --deploy-mode cluster