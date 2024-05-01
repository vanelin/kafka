# Install Apache Kafka Zookeeper on Kubernetes for testing migrgtation to KRaft mode:
```bash
helm upgrade --install kafka oci://registry-1.docker.io/bitnamicharts/kafka \
    --version 22.1.5 \
    --create-namespace \
    --namespace kafka \
    -f values-zookeeper.yaml
```

## Create a Kafka topic:
```bash
# exec to the kafka pod
kubectl exec -it pods/kafka-0 -n kafka -- /bin/bash


kafka-topics.sh --bootstrap-server localhost:9092 --topic test --create --partitions 8
kafka-topics.sh --bootstrap-server localhost:9092 --topic test --describe

kafka-topics.sh --bootstrap-server localhost:9092 --topic test2 --create --partitions 24
kafka-topics.sh --bootstrap-server localhost:9092 --topic test2 --describe

kafka-topics.sh --bootstrap-server localhost:9092 --list

# PRODUCER:
kafka-console-producer.sh \
  --broker-list kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092,kafka-2.kafka-headless.kafka.svc.cluster.local:9092 \
  --topic test

# we can read data using any broker
# CONSUMER:
kafka-console-consumer.sh \
  --bootstrap-server kafka.kafka.svc.cluster.local:9092 \
  --topic test \
  --from-beginning

# generate 1MB of random data
kafka-topics.sh --bootstrap-server localhost:9092 --topic random_topic --create --partitions 8
base64 /dev/urandom | head -c 10000 | egrep -ao "\w" | tr -d '\n' > /tmp/file10KB.txt

kafka-producer-perf-test.sh --topic random_topic --num-records 10000 --throughput 10 --payload-file /tmp/file10KB.txt --producer-props acks=1 bootstrap.servers=kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092,kafka-2.kafka-headless.kafka.svc.cluster.local:9092 --payload-delimiter A

# Get number of messages in a topic
kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list kafka-headless.kafka.svc.cluster.local:9092 --topic random_topic --time -1
# Get the total number of messages in a topic
kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list kafka-headless.kafka.svc.cluster.local:9092 --topic random_topic --time -1 | awk -F  ":" '{sum += $3} END {print sum}'

# start a consumer 
kafka-console-consumer.sh  --bootstrap-server kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092,kafka-2.kafka-headless.kafka.svc.cluster.local:9092 --topic random_topic
# start consuming messages from the topic `random_topic` on partition `0`, starting from offset `1`.
kafka-console-consumer.sh --bootstrap-server kafka.kafka.svc.cluster.local:9092 --topic random_topic --partition 0 --offset 1 --property print.offset=true

```