FROM ubuntu:22.04

LABEL author="Mostafa Ghadimi"
LABEL author_email="mostafa.ghadimi@yahoo.com"

ARG HADOOP_USER=hadoop \
    HADOOP_HOME=/opt/hadoop-3.3.4 \
    HADOOP_PACKAGE=hadoop-3.3.4

RUN apt update && \
    apt install -y openjdk-8-jdk wget openssh-server openssh-client && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN adduser ${HADOOP_USER}

RUN cd /opt && \
    wget https://downloads.apache.org/hadoop/common/${HADOOP_PACKAGE}/${HADOOP_PACKAGE}.tar.gz --directory-prefix /opt && \
    tar xzf ${HADOOP_PACKAGE}.tar.gz && \ 
    rm ${HADOOP_PACKAGE}.tar.gz && \
    chown -R ${HADOOP_USER}:${HADOOP_USER} ${HADOOP_HOME}

USER ${HADOOP_USER}
WORKDIR ${HADOOP_HOME}

COPY configs/* ${HADOOP_HOME}/etc/hadoop/

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/ \
    HADOOP_HOME=${HADOOP_HOME} \
    PATH=$PATH:${HADOOP_HOME}/sbin:${HADOOP_HOME}/bin \
    HADOOP_OPTS="-Djava.library.path=${HADOOP_HOME}/lib/native" \
    HDFS_NAMENODE_USER=${HADOOP_USER} \
    HDFS_DATANODE_USER=${HADOOP_USER} \
    HDFS_SECONDARYNAMENODE_USER=${HADOOP_USER} \
    YARN_RESOURCEMANAGER_USER=${HADOOP_USER} \
    YARN_NODEMANAGER_USER=${HADOOP_USER}

RUN ssh-keygen -t rsa -q -f "/home/${HADOOP_USER}/.ssh/id_rsa" -N "" && \
    cat /home/${HADOOP_USER}/.ssh/id_rsa.pub >> /home/${HADOOP_USER}/.ssh/authorized_keys && \
    chmod 640 /home/${HADOOP_USER}/.ssh/authorized_keys && \
    echo export JAVA_HOME=${JAVA_HOME} >> ${HADOOP_HOME}/etc/hadoop/hadoop-env.sh && \
    sed -i "s|\${HADOOP_HOME}|${HADOOP_HOME}|g" ${HADOOP_HOME}/etc/hadoop/hdfs-site.xml && \
    bash -c 'mkdir -pv data/hdfs/{namenode,datanode}'  # https://github.com/moby/moby/issues/13011