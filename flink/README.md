# Flink

## Flink with Mesos
for build
```bash
cd docker-collection/flink
docker build -t flink:mesos .
```
### Run FLink framework in mesos mini
```bash
pwd
#>> /docker-collection/flink
# run mesos cluster
docker run --rm --privileged -d --net=host --name mesos  mesos/mesos-mini

# need access to flink:mesos image inside meosos cluster.
# copu docker file to mesos container
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
