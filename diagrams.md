# https://diagrams.helpful.dev/s/s:vMRoNJqV
graph TD
  subgraph "kube-cluster"
    subgraph "kafka namespace"
      kafka-svc[Kafka Service]
      
      subgraph "node-0"
        kafka-0[kafka-controller-0 Pod]
      end
      
      subgraph "node-1"
        kafka-1[kafka-controller-1 Pod]
      end

      subgraph "node-2"
        kafka-2[kafka-controller-2 Pod]
      end

      subgraph "pvc"
        pvc-0[PVC data-kafka-controller-0]
        pvc-1[PVC data-kafka-controller-1]
        pvc-2[PVC data-kafka-controller-2]
        pvc-3[PVC logs-kafka-controller-0]
        pvc-4[PVC logs-kafka-controller-1]
        pvc-5[PVC logs-kafka-controller-2]
      end
      
      kafka-0 --- pvc-0
      kafka-0 --- pvc-3
      kafka-1 --- pvc-1
      kafka-1 --- pvc-4
      kafka-2 --- pvc-2
      kafka-2 --- pvc-5
      
      kafka-svc ---|"kafka-controller-0.kafka-controller-headless.kafka.svc.cluster.local:9092"| kafka-0
      kafka-svc ---|"kafka-controller-1.kafka-controller-headless.kafka.svc.cluster.local:9092"| kafka-1
      kafka-svc ---|"kafka-controller-2.kafka-controller-headless.kafka.svc.cluster.local:9092"| kafka-2
    end
  end

  classDef k8s fill:#326ce5,stroke:#fff,stroke-width:4px;
  classDef namespace fill:#ffcc00,stroke:#fff,stroke-width:4px;
  classDef service fill:#ff9900,stroke:#fff,stroke-width:4px;
  classDef worker fill:#00cc99,stroke:#fff,stroke-width:4px;
  classDef pod fill:#66ccff,stroke:#fff,stroke-width:4px;
  classDef pvc fill:#ff6666,stroke:#fff,stroke-width:4px;
  classDef pv fill:#cc99ff,stroke:#fff,stroke-width:4px;

  class kafka-svc service;
  class kafka-0,kafka-1,kafka-2 pod;
  class pvc-0,pvc-1,pvc-2 pvc;