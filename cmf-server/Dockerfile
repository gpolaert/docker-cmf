FROM centos:7.1.1503

# be my friend ;)
MAINTAINER gpolaert (@gpolaert)

# repos
RUN echo 7.1.1503 > /etc/yum/vars/releasever
ADD http://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo /etc/yum.repos.d/

# need to used systemd instead of fakesystemd (@see http://bit.ly/1UgISI5)
RUN yum clean all && yum swap -y fakesystemd systemd

# cloudera packages
RUN yum install -y cloudera-manager-daemons oracle-j2sdk1.7
RUN yum install -y cloudera-manager-server 

# env vars
ENV CMF_JAVA_OPTS -Xmx2G -XX:MaxPermSize=256m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp
ENV CMF_JDBC_DRIVER_JAR /cmf/config-db/:/usr/share/java/mysql-connector-java.jar:/usr/share/java/oracle-connector-java.jar
ENV JAVA_HOME /usr/java/jdk1.7.0_67-cloudera/

EXPOSE 7180 7182

CMD cmf-server
