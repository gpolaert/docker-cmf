## The Cloudera Manager Agent image
![Cloudera logo](http://www.cloudera.com/content/dam/www/static/images/logos/cloudera-logo.png)
### Aim
This image is a part of the **docker-cmf** images. The original aim is to deploy a Hadoop cluster with the Cloudera Manager.
In this repo, we have made the choice of running all hadoop services as sub-processes of the cloudera agent.

### Quickstart
see @Running section to see all examples.

**Info:** The agent hostname need to respect the RFC 952 in order to works the cloudera server.

```
# Quick and dirty example
docker run -dt --hostname $(hostname -f) -p 9000:9000 cmf-agent --master master_fqdn:7180
```

#### Design
All images are bases on the official CentOS-7.1 image.
**todo**

##### Entrypoint
By default, the image run the `cmf-agent` command with some default values:
- `parcel_dir` set to `/cmf/parcels`
- `logdir` set to `/cmf/log`
- `lib_dir` set to `/cmf/lib`
- `logfile` set to `/cmf/log/cloudera-scm-agent/cloudera-scm-agent.log`.

The cmf master location is provides by `CMD`, the default value is `--master cmf-master:7180`.

##### Ports

Hadoop and Agent use many ports. In standalone or network/swarm mode, you can run the image without adding a port translation.
But if you want bind the container with the host's ports, you have to add some `-p` options.

**Agent ports:**
- `-p 9000:9000`: Communication between the server and agents.

**HDFS services:**
**todo**

##### Volumes
There is a unique mount point `/cmf`. This path contains 3 directories:
- `/cmf/parcels/`: Location of Parcel repository root directory.
- `/cmf/log/`: Directory where to store sub-process log files. 
- `/cmf/lib/`: Agent's persistent state directory.

Hadoop leverages independant data mount points. If you want to use this image in *production*, you have to add your data mount points at the runtime.
Because each environment is different, no `/data/*` volumes have been declared. 

 
##### Logs
The stdout logs every events of the agent' stdout like the default behavior.
Hadoop sub-processes log by default in `/cmf/log/<service-name>/` like the agent does without docker.


##### Environment vars
There are 2 env vars set. You can override them with `-e` option.
- `SCM_DEBUG`: INFO (default) or DEBUG, set the Agent's log level.
- `CMF_CONF_DIR`: `/cmf/config`, use to override the Agent's properties.

### How to use this image
#### Building
There is not specific information about the build. You have to just read the `Dockerfile`. 
You can reuse this image in order to set static some vars.
```
FROM gpolaert/cmf-agent:5.5.1
ADD custom_agent_config.ini /cmf/config/config.ini
```

#### Running
##### Standalone (local containers)
```
# local mode (linked with cmf-server)
docker_hostname=$(hostname -f)
docker run \
  --name cmf-agent \ 
  --hostname $docker_hostname \
  --link cmf-server:cmf-server \
  -dt cmf-agent 
```

##### Network/Swarm or NetNS with volume option
```
# Hostname (prevent RFC 592)
docker_hostname=$(hostname -f)

## Network
# Overlay
docker_network=network-overlay
docker network create -d overlayd $docker_network
# Net namespace
#docker_network=net:container_name

## Volumes management (data)
cluster_name=some_name
docker_volumes=$(index=0;for path in $(find /data -type d -name "*$cluster_name");do echo " -v $path:/data/$index:rw";index=$((index+1));done)

## Persistant volumes
# log
docker_volumes=$docker_volumes -v /docker_volumes/$cluster_name-cmf-agent/log:/cmf/log:rw
# parcels (shared by all agents)
docker_volumes=$docker_volumes -v /media/nfs/docker_volumes/parcels:/cmf/parcels:rw
# lib 
docker_volumes=$docker_volumes -v /docker_volumes/$cluster_name-cmf-agent/lib:/cmf/lib:rw

docker run \
  --name $cluster_name-cmf-agent \
  --hostname $docker_hostname \
  --network=$docker_network \
  $docker_volumes \
  -dt cmf-agent --master cmf-server:7182
```



