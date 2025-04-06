Voici un exemple de configuration YAML pour **KEDA**, qui met en place un autoscaling basé sur une file de messages **AWS SQS**.  

📌 **Cas d'usage** :  
- On a un déploiement Kubernetes qui consomme des messages depuis une queue **SQS**.  
- KEDA va automatiquement scaler les pods en fonction du **nombre de messages en attente**.  
- Si la queue est vide, **les pods seront réduits à zéro** pour économiser les ressources.

---

### 📜 **Déploiement avec KEDA et SQS Scaling**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: worker-app
spec:
  replicas: 1  # Ce sera géré dynamiquement par KEDA
  selector:
    matchLabels:
      app: worker-app
  template:
    metadata:
      labels:
        app: worker-app
    spec:
      containers:
        - name: worker
          image: my-app-image:latest
          env:
            - name: AWS_REGION
              value: "us-east-1"
            - name: QUEUE_URL
              value: "https://sqs.us-east-1.amazonaws.com/123456789012/my-queue"
```

---

### 📜 **ScaledObject (Configuration de KEDA pour le scaling)**
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: sqs-scaledobject
spec:
  scaleTargetRef:
    name: worker-app  # Le nom du Deployment à scaler
  minReplicaCount: 0  # Peut descendre à zéro
  maxReplicaCount: 10  # Maximum de pods
  triggers:
    - type: aws-sqs-queue
      metadata:
        queueURL: "https://sqs.us-east-1.amazonaws.com/123456789012/my-queue"
        awsRegion: "us-east-1"
        queueLength: "5"  # Scale up si > 5 messages en attente
      authenticationRef:
        name: keda-trigger-auth-aws-credentials
```

---

### 📜 **Authentification AWS pour KEDA**
```yaml
apiVersion: keda.sh/v1alpha1
kind: TriggerAuthentication
metadata:
  name: keda-trigger-auth-aws-credentials
spec:
  secretTargetRef:
    - parameter: awsAccessKeyID
      name: aws-secret
      key: awsAccessKeyID
    - parameter: awsSecretAccessKey
      name: aws-secret
      key: awsSecretAccessKey
```

📌 **Remarque :**  
Tu dois créer un secret Kubernetes contenant les credentials AWS :

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: aws-secret
type: Opaque
data:
  awsAccessKeyID: BASE64_ENCODED_ACCESS_KEY
  awsSecretAccessKey: BASE64_ENCODED_SECRET_KEY
```
*(Utilise `echo -n "YOUR_SECRET" | base64` pour encoder les credentials en base64.)*

---

### 🎯 **Explication** :
- **`ScaledObject`** : Définit comment KEDA va autoscaler notre **worker-app**.
- **Trigger `aws-sqs-queue`** : Surveille la file **SQS** et déclenche un scaling basé sur le **nombre de messages en attente**.
- **`TriggerAuthentication`** : Permet à KEDA d'utiliser des **credentials AWS** stockés en tant que secret Kubernetes.
- **Scale à zéro** si la queue est vide, et jusqu'à **10 pods max** si nécessaire.

---
