FROM postgres:9.3

# be my friend ;)
MAINTAINER gpolaert (@gpolaert)

# secure postgres account 
#ENV POSTGRES_PASSWORD $(openssl rand -base64 18)

# generate db.properties for cmf
RUN mkdir -p /etc/cloudera-scm-server && \
    echo com.cloudera.cmf.db.host=cmf-db > /etc/cloudera-scm-server/db.properties && \
    echo com.cloudera.cmf.db.name=cmf >> /etc/cloudera-scm-server/db.properties && \
    echo com.cloudera.cmf.db.user=cmf >> /etc/cloudera-scm-server/db.properties && \
    echo com.cloudera.cmf.db.type=postgresql >> /etc/cloudera-scm-server/db.properties

# cmf specific
RUN PASSWD=$(openssl rand -base64 18) && \
    echo "CREATE ROLE cmf LOGIN PASSWORD '$PASSWD';" > /docker-entrypoint-initdb.d/0.cmf-create-database.sql && \
    echo com.cloudera.cmf.db.password=$PASSWD >> /etc/cloudera-scm-server/db.properties
ADD cmf-prepare-database.sql /docker-entrypoint-initdb.d/1.cmf-prepare-database.sql
ADD log4j.properties /etc/cloudera-scm-server/log4j.properties

# share config files
VOLUME ["/etc/cloudera-scm-server"]



