# Install Strimzi Operator on Kubernetes
```bash
helm repo add strimzi https://strimzi.io/charts
helm repo update
helm search repo strimzi --versions

helm upgrade --install strimzi-kafka-operator strimzi/strimzi-kafka-operator \
    --version 0.40.0 \
    --create-namespace \
    --namespace kafka-v3
```
Run Kafka in the KRaft Mode with Strimzi Operator:
```bash
kubectl apply -f strimzi/values-strimzi.yaml -n kafka-v3
```

# Install Mirror Maker2:

```bash
kubectl apply -f strimzi/mirror-maker.yaml -n kafka-v3
```

### Commands to interact with Kafka:
```bash
# List topics
/opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
/opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic random_topic --describe

# get offsets
/opt/kafka/bin/kafka-get-offsets.sh --bootstrap-server localhost:9092 --topic random_topic
/opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic random_topic --partition 0 --offset 1 --property print.offset=true

# start a consumer 
/opt/kafka/bin/kafka-console-consumer.sh  --bootstrap-server dev-cluster-kafka-bootstrap.kafka-v3.svc.cluster.local:9092 --topic random_topic

# consume messages with the console consumer
/opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server dev-cluster-kafka-bootstrap.kafka-v3.svc.cluster.local:9092 --topic random_topic --from-beginning

# Get number of messages in a topic
/opt/kafka/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list kafka-headless.kafka.svc.cluster.local:9092 --topic random_topic --time -1
# Get the total number of messages in a topic
/opt/kafka/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list kafka-headless.kafka.svc.cluster.local:9092 --topic random_topic --time -1 | awk -F  ":" '{sum += $3} END {print sum}'
```