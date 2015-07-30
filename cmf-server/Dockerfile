FROM centos:centos6
MAINTAINER gpolaert (@gpolaert)

RUN yum update -y
ADD http://archive.cloudera.com/cm5/redhat/6/x86_64/cm/cloudera-manager.repo /etc/yum.repos.d/


RUN yum install -y cloudera-manager-daemons oracle-j2sdk1.7
RUN yum install -y cloudera-manager-server 

CMD cmf-server
