FROM flink:1.8-scala_2.11

ENV HADOOP_HOME=/opt/hadoop

RUN mkdir -p /opt/hadoop 

RUN wget -O /opt/hadoop/hadoop.tar.gz https://archive.apache.org/dist/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz

WORKDIR $HADOOP_HOME

RUN tar -xzvf hadoop.tar.gz &&  mv hadoop-2.7.7/* .

ENV HADOOP_CLASSPATH="/opt/hadoop/etc/hadoop:/opt/hadoop/share/hadoop/common/lib/*:/opt/hadoop/share/hadoop/common/*:/opt/hadoop/share/hadoop/hdfs:/opt/hadoop/share/hadoop/hdfs/lib/*:/opt/hadoop/share/hadoop/hdfs/*:/opt/hadoop/share/hadoop/yarn/lib/*:/opt/hadoop/share/hadoop/yarn/*:/opt/hadoop/share/hadoop/mapreduce/lib/*:/opt/hadoop/share/hadoop/mapreduce/*:/opt/hadoop/contrib/capacity-scheduler/*.jar"


ARG MESOS_VERSION=1.8.0-2.0.6

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


#RUN apt-get install -y mesos


ENV MESOS_NATIVE_JAVA_LIBRARY=/usr/local/lib/libmesos.so

WORKDIR $FLINK_HOME
