FROM centos:centos6
MAINTAINER gpolaert (@gpolaert)


RUN yum update -y
ADD http://archive.cloudera.com/cm5/redhat/6/x86_64/cm/cloudera-manager.repo /etc/yum.repos.d/


RUN yum install -y cloudera-manager-daemons oracle-j2sdk1.7
RUN yum install -y cloudera-manager-agent

RUN mkdir -p /cmf/{parcels,lib,log}

CMD cmf-agent --master $CMF_SERVER --parcel_dir /cmf/parcels --lib_dir /cmf/lib --logdir /cmf/log
