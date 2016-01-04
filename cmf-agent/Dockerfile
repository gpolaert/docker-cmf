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
RUN yum install -y cloudera-manager-agent

# env vars
ENV CMF_CONF_DIR /cmf/config
ENV JAVA_HOME /usr/java/jdk1.7.0_67-cloudera/

# mount/volume points
RUN mkdir -p /cmf/{config,lib,log,parcels} && \
    ln -s /cmf/parcels /opt/cloudera/parcels 

# ports used
EXPOSE 9000

# container volume 
VOLUME ["/cmf/parcles", "/cmf/lib", "/cmf/log"]

# default command
ENTRYPOINT ["cmf-agent", "--parcel_dir", "/cmf/parcels", "--lib_dir", "/cmf/lib", "--logdir", "/cmf/log", "--logfile", "/cmf/log/cloudera-scm-agent/cloudera-scm-agent.log"]

# override some args
CMD ["--master", "cmf-master:7182"] 
