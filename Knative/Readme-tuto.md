🎯 Objectif du tutoriel
Nous allons :
✅ Déployer un cluster Kubernetes avec Knative et Kafka
✅ Créer un service serverless avec Knative Serving
✅ Déclencher des événements avec Knative Eventing + Kafka
✅ Mettre en place un pipeline CI/CD avec ArgoCD et GitHub Actions

📌 1. Prérequis
🔹 Un cluster Kubernetes (v1.24+) (Minikube, KIND, EKS, GKE...)
🔹 kubectl, helm, docker, kafka installés
🔹 Un dépôt GitHub pour gérer le CI/CD
🔹 Un registre d’images (Docker Hub ou ECR)

📌 2. Installer Knative et Kafka sur Kubernetes
1️⃣ Installer Knative Serving et Eventing
bash
Copier
Modifier
kubectl apply -f https://github.com/knative/serving/releases/latest/download/serving-crds.yaml
kubectl apply -f https://github.com/knative/serving/releases/latest/download/serving-core.yaml
kubectl apply -f https://github.com/knative/eventing/releases/latest/download/eventing-crds.yaml
kubectl apply -f https://github.com/knative/eventing/releases/latest/download/eventing-core.yaml
2️⃣ Installer Kafka avec Helm
bash
Copier
Modifier
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-kafka bitnami/kafka
Vérifier que Kafka fonctionne :

bash
Copier
Modifier
kubectl get pods
✅ Si Kafka et Knative sont Running, nous pouvons continuer.

📌 3. Déployer un service serverless avec Knative Serving
1️⃣ Créer une application Python simple (app.py)
python
Copier
Modifier
from flask import Flask, request

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Knative + Kafka!", 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
2️⃣ Construire et publier l’image Docker
bash
Copier
Modifier
docker build -t my-dockerhub-username/knative-kafka-app .
docker push my-dockerhub-username/knative-kafka-app
3️⃣ Déployer l’application avec Knative Serving (knative-service.yaml)
yaml
Copier
Modifier
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: knative-kafka-app
spec:
  template:
    spec:
      containers:
        - image: my-dockerhub-username/knative-kafka-app
          env:
            - name: KAFKA_TOPIC
              value: "knative-topic"
Déploiement :

bash
Copier
Modifier
kubectl apply -f knative-service.yaml
Vérifier l’URL de l’application :

bash
Copier
Modifier
kubectl get ksvc
📌 Copie l’URL et teste avec curl :

bash
Copier
Modifier
curl http://knative-kafka-app.default.127.0.0.1.sslip.io
📌 4. Intégrer Kafka avec Knative Eventing
1️⃣ Installer le Knative Kafka Source
bash
Copier
Modifier
kubectl apply -f https://github.com/knative/eventing-contrib/releases/latest/download/kafka-source.yaml
2️⃣ Créer un KafkaTopic (kafka-topic.yaml)
yaml
Copier
Modifier
apiVersion: eventing.knative.dev/v1alpha1
kind: KafkaTopic
metadata:
  name: knative-topic
spec:
  numPartitions: 1
  replicationFactor: 1
bash
Copier
Modifier
kubectl apply -f kafka-topic.yaml
3️⃣ Déployer un KafkaSource pour écouter les événements
yaml
Copier
Modifier
apiVersion: sources.knative.dev/v1beta1
kind: KafkaSource
metadata:
  name: knative-kafka-source
spec:
  bootstrapServers:
    - my-kafka.default.svc.cluster.local:9092
  topics:
    - knative-topic
  sink:
    ref:
      apiVersion: serving.knative.dev/v1
      kind: Service
      name: knative-kafka-app
bash
Copier
Modifier
kubectl apply -f kafka-source.yaml
4️⃣ Tester l’envoi d’un événement Kafka
bash
Copier
Modifier
kubectl -n default get broker
kubectl -n default get ksvc

kubectl exec -it my-kafka-0 -- kafka-console-producer.sh --broker-list localhost:9092 --topic knative-topic
> {"message": "Hello Knative Eventing!"}
✅ Le message est consommé par notre application knative-kafka-app !

📌 5. Ajouter CI/CD avec ArgoCD et GitHub Actions
1️⃣ Installer ArgoCD sur Kubernetes
bash
Copier
Modifier
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443
🔹 Connexion via https://localhost:8080
🔹 Login avec kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

2️⃣ Ajouter un App pour Knative
yaml
Copier
Modifier
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: knative-app
  namespace: argocd
spec:
  destination:
    namespace: default
    server: https://kubernetes.default.svc
  source:
    repoURL: "https://github.com/my-username/knative-kafka-app.git"
    path: "manifests"
    targetRevision: main
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
bash
Copier
Modifier
kubectl apply -f knative-argocd.yaml
3️⃣ Ajouter un pipeline CI/CD avec GitHub Actions
Créer un fichier .github/workflows/deploy.yaml :

yaml
Copier
Modifier
name: CI/CD Knative

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Build Docker image
        run: |
          docker build -t my-dockerhub-username/knative-kafka-app:${{ github.sha }} .
          docker push my-dockerhub-username/knative-kafka-app:${{ github.sha }}

      - name: Update Kubernetes manifests
        run: |
          sed -i "s|image: my-dockerhub-username/knative-kafka-app.*|image: my-dockerhub-username/knative-kafka-app:${{ github.sha }}|" knative-service.yaml
          git add knative-service.yaml
          git commit -m "Update image tag to ${{ github.sha }}"
          git push origin main
✅ À chaque git push, l’image est mise à jour et déployée automatiquement avec ArgoCD !

📌 6. Conclusion
🎯 Nous avons créé une architecture serverless complète avec Knative, Kafka et ArgoCD !
✔ Déploiement automatique avec CI/CD
✔ Scalabilité automatique grâce à Knative
✔ Communication événementielle via Kafka