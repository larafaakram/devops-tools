Voici un exemple de configuration KEDA pour **Kafka**. 🚀  

📌 **Cas d'usage** :  
- On a une application qui consomme des messages depuis un **topic Kafka**.  
- KEDA va automatiquement **scaler les pods** en fonction du **nombre de messages en attente** dans le topic.  
- Si le topic est vide, **les pods seront réduits à zéro** pour économiser les ressources.  

---

### 📜 **Déploiement de l'application worker**  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kafka-consumer
spec:
  replicas: 1  # Géré dynamiquement par KEDA
  selector:
    matchLabels:
      app: kafka-consumer
  template:
    metadata:
      labels:
        app: kafka-consumer
    spec:
      containers:
        - name: consumer
          image: my-kafka-consumer:latest
          env:
            - name: KAFKA_BROKER
              value: "my-cluster-kafka-bootstrap.kafka.svc:9092"
            - name: KAFKA_TOPIC
              value: "my-topic"
            - name: KAFKA_GROUP
              value: "my-consumer-group"
```

---

### 📜 **ScaledObject (Configuration KEDA pour Kafka)**  
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: kafka-scaledobject
spec:
  scaleTargetRef:
    name: kafka-consumer  # Le Deployment à scaler
  minReplicaCount: 0  # Peut descendre à zéro
  maxReplicaCount: 10  # Maximum de pods
  triggers:
    - type: kafka
      metadata:
        bootstrapServers: "my-cluster-kafka-bootstrap.kafka.svc:9092"
        topic: "my-topic"
        consumerGroup: "my-consumer-group"
        lagThreshold: "10"  # Si le lag > 10 messages, on scale up
```

---

### 🎯 **Explication** :
- **`ScaledObject`** : Définit comment KEDA va autoscaler le `kafka-consumer`.
- **Trigger `kafka`** : Surveille le **lag** des messages Kafka pour un **groupe de consommateurs**.
- **Scale à zéro** si aucun message en attente et jusqu'à **10 pods max** si la file est longue.

---

### 🔧 **Pré-requis** :
- Kafka doit être déployé sur ton cluster Kubernetes (ou accessible en externe).
- Le `consumerGroup` utilisé doit être bien configuré pour permettre à KEDA de mesurer le lag.
- KEDA doit être installé sur ton cluster Kubernetes.  

---
