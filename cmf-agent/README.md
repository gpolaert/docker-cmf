## The Cloudera Manager Agent (cmf-agent)
![Cloudera logo](http://www.cloudera.com/content/dam/www/static/images/logos/cloudera-logo.png)
### Aim
This image is a part of the **docker-cmf** images. The original aim is to deploy a Hadoop cluster with the Cloudera Manager.
In this repo, we have made the choice of running all hadoop services as sub-processes of the cloudera agent.

### Quickstart
see @Running section to see all examples.

**Info:** The agent hostname need to respect the RFC 952 in order to works the cloudera server.

```
# Quick and dirty example
docker run -dt --hostname cmf-agent-$(hostname) -p 9000:9000 cmf-agent --master master_fqdn:7180
```

#### Design
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

##### Volumes
There is a unique mount point `/cmf`. This path contains 3 directories:
- `/cmf/parcels/`: Location of Parcel repository root directory.
- `/cmf/log/`: Directory where to store sub-process log files. 
- `/cmf/lib/`: Agent's persistent state directory.
 
##### Logs
The stdout logs every events of the agent' stdout like the default behavior.
Hadoop sub-processes log by default in `/cmf/log/<service-name>/` like the agent does without docker.


##### Environment vars
- `SCM_DEBUG`: INFO (default) or DEBUG, set the Agent's log level.
- `CMF_CONF_DIR`: `/cmf/config`, use to override the Agent's properties.

### How to use this image
#### Building

#### Running
##### Standalone (local containers)
```
# local mode (linked with cmf-server)
docker_hostname=cmf-agent-$(hostname)
docker run \
  --name cmf-agent \ 
  --hostname $docker_hostname \
  --link cmf-server:cmf-server \
  -dt cmf-agent --master cmf-server:7182
```

##### Network/Swarm
```
# network mode
docker_hostname=cmf-agent-overlay
docker network create -d overlay network-overlay
docker run \
  --name cmf-agent \
  --hostname $hostname \
  --network=network-overlay \
  -dt cmf-agent --master cmf-server:7182
```
##### Network stacked
```

```
