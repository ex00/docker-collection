# Flink

## Flink with Mesos
for build
```bash
cd docker-collection/flink
docker build -t flink:mesos .
```
### Run FLink framework in mesos mini
```bash
docker run --rm --privileged -d --net=host --name mesos  mesos/mesos-mini
docker cp Dockerfile mesos:/root
docker exec mesos cd /root/ && docker build -t flink:mesos .
cat <<EOT >> flink-app-master.json
{
  "id": "flink-app-master",
  "cmd": "/opt/flink/bin/mesos-appmaster.sh \
   -Dmesos.master=localhost:5050 \
   -Dmesos.resourcemanager.tasks.container.image.name=flink:mesos \
   -Dmesos.resourcemanager.tasks.container.type=docker \
   -Dmesos.resourcemanager.tasks.taskmanager-cmd=\"/opt/flink/bin/mesos-taskmanager.sh\" \
   -Dmesos.resourcemanager.tasks.container.docker.force-pull-image=false \
   -Djobmanager.heap.mb=615 \
   -Djobmanager.rpc.port=6063 \
   -Drest.port=8081 \
   -Dmesos.resourcemanager.tasks.cpus=1.0 \
   -Dmesos.resourcemanager.tasks.mem=1024 \
   -Dtaskmanager.heap.mb=720 \
   -Dtaskmanager.numberOfTaskSlots=1 \
   -Dparallelism.default=1 || cat /opt/flink/log/*.log ",
  "cpus": 1,
  "mem": 512.0,
  "instances": 1,
  "networks": [
    {
      "mode": "host"
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
EOT
curl -X POST http://localhost:8080/v2/apps -d @flink-app-master.json -H "Content-type: application/json"
```
