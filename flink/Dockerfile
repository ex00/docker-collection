ARG FLINK_IMAGE_VERSION=1.8-scala_2.11
FROM flink:$FLINK_IMAGE_VERSION as flink_base

RUN apt-get update
RUN apt-get install -y lsb-release
RUN echo "$(lsb_release -is | tr '[:upper:]' '[:lower:]') $(lsb_release -cs)" > /distro.info

# install hadoop
#ARG HADOOP_VERSION=2.7.7
#ENV HADOOP_HOME=/opt/hadoop
#ENV HADOOP_CLASSPATH="$HADOOP_HOME/etc/hadoop:$HADOOP_HOME/share/hadoop/common/lib/*:$HADOOP_HOME/share/hadoop/common/*:$HADOOP_HOME/share/hadoop/hdfs:$HADOOP_HOME/share/hadoop/hdfs/lib/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/yarn/lib/*:$HADOOP_HOME/share/hadoop/yarn/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*:$HADOOP_HOME/share/hadoop/mapreduce/*:$HADOOP_HOME/contrib/capacity-scheduler/*.jar"
#RUN mkdir -p $HADOOP_HOME 
#ADD https://archive.apache.org/dist/hadoop/common/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz $HADOOP_HOME/hadoop.tar.gz
#RUN tar -xzvf $HADOOP_HOME/hadoop.tar.gz -C $HADOOP_HOME  && mv $HADOOP_HOME/hadoop-${HADOOP_VERSION}/* $HADOOP_HOME/ && rm -rf $HADOOP_HOME/hadoop.tar.gz


FROM flink:$FLINK_IMAGE_VERSION 
COPY --from=flink_base /distro.info /

ARG MESOS_VERSION=1.6.0
ENV MESOS_NATIVE_JAVA_LIBRARY=/usr/local/lib/libmesos.so

RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv DF7D54CBE56151BF \
 && echo "deb http://repos.mesosphere.io/$(cat /distro.info) main" > /etc/apt/sources.list.d/mesosphere.list \
 && apt-get -y update

RUN apt-get -y install ca-certificates-java \
 && update-ca-certificates -f \
 && touch /usr/local/bin/systemctl && chmod +x /usr/local/bin/systemctl \
 && apt-get install -y gnupg \
 && apt-get -y install --no-install-recommends "mesos=${MESOS_VERSION}*" wget libcurl3-nss \
 && apt-get -y install libatlas3-base libopenblas-base

WORKDIR $FLINK_HOME
