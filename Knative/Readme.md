🚀 Qu'est-ce que Knative ?
Knative est une plateforme open-source qui permet d'exécuter des applications serverless sur Kubernetes. Il ajoute des fonctionnalités de scalabilité automatique, de gestion d'événements et de déploiement simplifié aux applications conteneurisées.

📌 Knative permet aux développeurs d'exécuter des workloads sans se soucier de l'infrastructure, en ne consommant des ressources que lorsqu'elles sont nécessaires.

📌 1. Pourquoi utiliser Knative ?

✔ Auto-scaling avancé : monte et descend automatiquement en fonction de la charge

✔ Mode "scale to zero" : un service s’éteint s’il n’est pas utilisé

✔ Événements asynchrones : support natif des événements (Kafka, Pub/Sub, etc.)

✔ Déploiements simplifiés : gestion native du traffic avec versioning

✔ Exécution sur Kubernetes : compatible avec n'importe quel cluster Kubernetes

📌 2. Les composants de Knative
Knative est divisé en deux modules principaux :

🏗️ 1️⃣ Knative Serving (Exécution d’applications serverless)

👉 Gère le déploiement et le scaling automatique des applications

👉 Permet aux services de s'éteindre (scale-to-zero) quand ils sont inactifs

👉 Fournit un routage automatique du trafic (via Istio, Kourier, Contour, etc.)

🔹 Exemple de Knative Service en YAML :

```
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: hello-world
spec:
  template:
    spec:
      containers:
        - image: gcr.io/knative-samples/helloworld-go
          env:
            - name: TARGET
              value: "Hello Knative!"

```
📌 Déployé avec :

```
kubectl apply -f service.yaml
```

🔄 2️⃣ Knative Eventing (Gestion des événements serverless)
👉 Permet d'exécuter des applications en réponse à des événements (Kafka, RabbitMQ, CloudEvents...)
👉 Gère l'interconnexion entre les services sans dépendances directes
👉 Fonctionne avec les messages CloudEvents

🔹 Exemple d'un Trigger Knative Eventing :

```
apiVersion: eventing.knative.dev/v1
kind: Trigger
metadata:
  name: my-trigger
spec:
  broker: default
  filter:
    attributes:
      type: dev.knative.example.event
  subscriber:
    ref:
      apiVersion: serving.knative.dev/v1
      kind: Service
      name: my-service
```
📌 Déployé avec :

```
kubectl apply -f trigger.yaml
```
📌 3. Comment installer Knative sur Kubernetes ?
Knative peut être installé sur n'importe quel cluster Kubernetes. Voici un guide rapide :

📌 1️⃣ Installer Istio ou Kourier (ingress)
```
kubectl apply -f https://github.com/knative/net-istio/releases/latest/download/release.yaml
```
📌 2️⃣ Installer Knative Serving
```
kubectl apply -f https://github.com/knative/serving/releases/latest/download/serving-crds.yaml
kubectl apply -f https://github.com/knative/serving/releases/latest/download/serving-core.yaml
```
📌 3️⃣ Installer Knative Eventing
```
kubectl apply -f https://github.com/knative/eventing/releases/latest/download/eventing-crds.yaml
kubectl apply -f https://github.com/knative/eventing/releases/latest/download/eventing-core.yaml
```
📌 4. Knative vs. Autres solutions serverless

Caractéristique	        Knative	  AWS Lambda	  OpenFaaS

Basé sur Kubernetes	    ✅ Oui	❌ Non	       ✅ Oui

Auto-scaling avancé	    ✅ Oui	✅ Oui	       ✅ Oui

Support des événements	✅ Oui	✅ Oui	       ✅ Oui

Vendor Lock-in	        ❌ Non	✅ Oui (AWS)	   ❌ Non

Facilité d’installation	⚠️ Moyen ✅ Très simple	✅ Simple

📌 5. Quand utiliser Knative ?

✅ Cas d’usage recommandés :

✔ Applications serverless & microservices

✔ Déploiement dynamique avec auto-scaling

✔ Applications basées sur événements (Kafka, RabbitMQ, etc.)

✔ Besoin d’un contrôle total sur l’infrastructure (vs AWS Lambda)

❌ À éviter si :

✖ Tu veux une solution prête à l’emploi comme AWS Lambda

✖ Tu ne veux pas gérer un cluster Kubernetes

✖ Tu cherches une alternative plus simple comme OpenFaaS

🎯 Conclusion

✔ Knative est une solution serverless puissante basée sur Kubernetes

✔ Il permet le scaling automatique et l'exécution d'applications événementielles

✔ Idéal pour les entreprises et développeurs voulant une alternative à AWS Lambda