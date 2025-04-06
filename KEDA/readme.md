KEDA (**Kubernetes Event-Driven Autoscaling**) est un composant open-source qui permet de faire du **scaling automatique** des workloads Kubernetes en fonction d'événements externes. Il étend les capacités du **Horizontal Pod Autoscaler (HPA)** en permettant de réagir à des métriques autres que celles de base (CPU/RAM).

### 🔹 **Fonctionnalités principales** :
1. **Scaling basé sur des événements** : KEDA peut ajuster dynamiquement le nombre de pods en fonction de sources d’événements comme les files de messages, les bases de données, ou les métriques cloud.
2. **Mode "Scale-to-Zero"** : Contrairement à l’HPA qui garde toujours au moins un pod actif, KEDA peut réduire les pods à **zéro** lorsqu’aucune charge n’est présente.
3. **Plus de 50 scalers intégrés** : Supporte des services comme AWS SQS, Azure Queue Storage, Kafka, Prometheus, RabbitMQ, etc.
4. **Compatibilité avec Kubernetes HPA** : KEDA agit comme un adaptateur et peut fonctionner avec l’HPA natif.

### 🔹 **Cas d'usage** :
- **Traitement d'événements asynchrones** (ex. : jobs batch, files de messages)
- **Économie de ressources** avec le scale-to-zero
- **Autoscaling avancé** sur des métriques personnalisées

👉 **Exemple d'architecture avec KEDA** :  
Une application qui écoute une file SQS et démarre automatiquement des pods lorsqu’un certain nombre de messages est détecté.
