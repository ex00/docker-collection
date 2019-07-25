FROM flink:1.8-scala_2.11

ARG MESOS_VERSION=1.8.0-2.0.6
ARG HADOOP_VERSION=2.7.7

ENV HADOOP_HOME=/opt/hadoop
ENV HADOOP_CLASSPATH="$HADOOP_HOME/etc/hadoop:$HADOOP_HOME/share/hadoop/common/lib/*:$HADOOP_HOME/share/hadoop/common/*:$HADOOP_HOME/share/hadoop/hdfs:$HADOOP_HOME/share/hadoop/hdfs/lib/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/yarn/lib/*:$HADOOP_HOME/share/hadoop/yarn/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*:$HADOOP_HOME/share/hadoop/mapreduce/*:$HADOOP_HOME/contrib/capacity-scheduler/*.jar"
ENV MESOS_NATIVE_JAVA_LIBRARY=/usr/local/lib/libmesos.so

RUN mkdir -p $HADOOP_HOME 
ADD https://archive.apache.org/dist/hadoop/common/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz $HADOOP_HOME/hadoop.tar.gz
RUN tar -xzvf $HADOOP_HOME/hadoop.tar.gz -C $HADOOP_HOME  && mv $HADOOP_HOME/hadoop-${HADOOP_VERSION}/* $HADOOP_HOME/ && rm -rf $HADOOP_HOME/hadoop.tar.gz


# TODO install lsb-release
# Setup
# apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv DF7D54CBE56151BF
#DISTRO=(lsb_release -is | tr '[:upper:]' '[:lower:]')
#CODENAME=$(lsb_release -cs)
# Add the repository
#echo "deb http://repos.mesosphere.com/${DISTRO} ${CODENAME} main" > /etc/apt/sources.list.d/mesosphere.list


RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv DF7D54CBE56151BF \
 && echo "deb http://repos.mesosphere.io/debian stretch main" > /etc/apt/sources.list.d/mesosphere.list \
 && apt-get -y update \
 && apt-get -y install ca-certificates-java \
 && update-ca-certificates -f \
 && touch /usr/local/bin/systemctl && chmod +x /usr/local/bin/systemctl \
 && apt-get install -y gnupg \
 && apt-get -y install --no-install-recommends "mesos=${MESOS_VERSION}*" wget libcurl3-nss \
 && apt-get -y install libatlas3-base libopenblas-base


WORKDIR $FLINK_HOME
