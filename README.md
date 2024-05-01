# MVP Kafka on Kubernetes (K8s) in KRaft mode (Zookeperless)

## Mindmap diagram:
The architecture diagram below represents the resources we will deploy to get a general idea of how our Kafka cluster will look in Kubernetes.

![Kafka on K8s](docs/kraft.png)

## Run kind cluster:
- install kind to your local machine [quick start](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- create a kind cluster with the following command:
    ```bash
    kind create cluster --name kafka-cluster --config=kind-config.yaml 
    ``` 
- get context for the new cluster
    ```bash
    kind get kubeconfig --name kafka-cluster
    ```
- install ingress controller [kind ingress](https://kind.sigs.k8s.io/docs/user/ingress)
- for deleting the cluster run the following command:
    ```bash
    kind delete cluster --name kafka-cluster
    ```
### Optional: Install Metrics Server
Install the latest Metrics Server release by applying the components.yaml from the official GitHub repository:
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Once installed, you'll need to locate the Metrics Server deployment to patch it. This can typically be found in the kube-system namespace. Use the following command to patch it:

```bash
kubectl patch deployment metrics-server -n kube-system --type='json' -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--kubelet-insecure-tls"}]'
```
Please be aware that using `--kubelet-insecure-tls` means the Metrics Server will not verify the TLS certificates presented by the Kubelets. This can lead to insecure operation and is not recommended for production environments. It is usually used when you have self-signed certs or a non-standard CA and have not set up the appropriate CA chain for the Metrics Server to trust the Kubelets' certificates.

### Install Apache Kafka:
- [Bitnami Kafka Zookeeper](bitnami-zookeeper/README.md) for testing migration to KRaft mode;
- [Bitnami Kafka KRaft](bitnami-kraft/README.md);
- [Strimzi Kafka Kraft](strimzi/README.md);
- [Kafka migration with MirrorMaker2](kafka-migration/README.md).