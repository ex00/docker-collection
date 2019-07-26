# Flink

## Flink with Mesos
### Build image
```bash
pwd
#>> /docker-collection/flink

docker build -t flink:mesos .
# or
FLINK_VERSION=1.8.1
MESOS_VERSION=1.6.0
docker build -t flink:$FLINK_VERSION-mesos-$MESOS_VERSION -t flink:mesos \ 
	--build-arg FLINK_IMAGE_VERSION=$FLINK_VERSION-scala_2.11  \  ## check existing tags on https://hub.docker.com/_/flink?tab=tags
	--build-arg MESOS_VERSION=$MESOS_VERSION \
	 .
```

### Run Flink as mesos framework 
```bash
docker run --rm --name=Flink --network=host flink:mesos /opt/flink/bin/mesos-appmaster.sh  \
         -Dmesos.master=localhost:5050 \ ## Addres your mesos cluster
         -Dmesos.resourcemanager.tasks.container.image.name=flink:mesos \
         -Dmesos.resourcemanager.tasks.container.type=docker \
         -Dmesos.resourcemanager.tasks.taskmanager-cmd=\"/opt/flink/bin/mesos-taskmanager.sh\" \
         -Dmesos.resourcemanager.tasks.container.docker.force-pull-image=false \
         -Djobmanager.heap.mb=615 \
         -Djobmanager.rpc.port=6123  \
         -Drest.port=8081 \
         -Dmesos.resourcemanager.tasks.cpus=1.0 \
         -Dmesos.resourcemanager.tasks.mem=1024 \
         -Dtaskmanager.heap.mb=720 \
         -Dtaskmanager.numberOfTaskSlots=1 \
         -Dparallelism.default=1 || cat /opt/flink/log/*.log
```
#### Flink framework in mesos as marathon application
```bash
pwd
#>> /docker-collection/flink
# run mesos cluster
docker run --rm --privileged -d --net=host --name mesos  mesos/mesos-mini

# need access to flink:mesos image inside meosos cluster.
# copy docker file to mesos container
docker cp Dockerfile mesos:/root
# build image inside the mesos container
docker exec mesos /bin/bash -c "cd /root/ && docker build -t flink:mesos ."
....
# create mathathon flink application master job
cat > flink-app-master.json << "END"
{
  "id": "flink-app-master",
  "cmd": "/opt/flink/bin/mesos-appmaster.sh  \\\n
         -Dmesos.master=localhost:5050 \\\n
         -Dmesos.resourcemanager.tasks.container.image.name=flink:mesos \\\n
         -Dmesos.resourcemanager.tasks.container.type=docker \\\n
         -Dmesos.resourcemanager.tasks.taskmanager-cmd=\"/opt/flink/bin/mesos-taskmanager.sh\" \\\n
         -Dmesos.resourcemanager.tasks.container.docker.force-pull-image=false \\\n
         -Djobmanager.heap.mb=615 \\\n
         -Djobmanager.rpc.port=$PORT0  \\\n
         -Drest.port=$PORT1 \\\n
         -Dmesos.resourcemanager.tasks.cpus=1.0 \\\n   
         -Dmesos.resourcemanager.tasks.mem=1024 \\\n
         -Dtaskmanager.heap.mb=720 \\\n
         -Dtaskmanager.numberOfTaskSlots=1 \\\n
         -Dparallelism.default=1 || cat /opt/flink/log/*.log ",
  "cpus": 1,
  "mem": 512.0,
  "instances": 1,
  "networks": [
    {
      "mode": "host"
    }
  ],
  "portDefinitions": [
    {
      "port": 10000,
      "name": "rpc",
      "protocol": "tcp"
    },
    {
      "port": 10001,
      "protocol": "tcp",
      "name": "rest"
    }
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "forcePullImage": false,
      "image": "flink:mesos",
      "parameters": [],
      "privileged": false
    },
    "volumes": []
  }
}
END
curl -X POST http://localhost:8080/v2/apps -d @flink-app-master.json -H "Content-type: application/json"
```
