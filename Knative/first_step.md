🎯 Objectif du tutoriel

Nous allons :

✅ Installer Knative sur un cluster Kubernetes

✅ Déployer une application serverless avec Knative Serving

✅ Tester le scaling automatique (scale-to-zero)

✅ Utiliser Knative Eventing pour gérer des événements
________________________________________
📌 1. Prérequis

🔹 Un cluster Kubernetes (v1.24+) (Minikube, KIND, GKE, EKS...)

🔹 kubectl et helm installés

🔹 Un Ingress Controller (Istio ou Kourier)

🔹 Docker pour builder l’image

Si tu n’as pas encore de cluster Kubernetes, voici un moyen rapide avec KIND :
```
kind create cluster --name knative
```
________________________________________
📌 2. Installer Knative

1️⃣ Installer Kourier (ingress pour Knative)
```
kubectl apply -f https://github.com/knative/net-kourier/releases/latest/download/kourier.yaml
kubectl patch configmap/config-network \
  --namespace knative-serving \
  --type merge \
  --patch '{"data":{"ingress-class":"kourier.ingress.networking.knative.dev"}}'
  ```
2️⃣ Installer Knative Serving
```
kubectl apply -f https://github.com/knative/serving/releases/latest/download/serving-crds.yaml
kubectl apply -f https://github.com/knative/serving/releases/latest/download/serving-core.yaml
```
3️⃣ Vérifier l’installation
```
kubectl get pods -n knative-serving
```

✅ Si tous les pods sont en Running, Knative Serving est prêt !
________________________________________
📌 3. Déployer une application serverless avec Knative

Nous allons créer un service hello-world qui répondra "Hello Knative!".

1️⃣ Écrire le fichier hello-world.yaml
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
2️⃣ Déployer l’application
```
kubectl apply -f hello-world.yaml
```
3️⃣ Vérifier que le service est actif
```
kubectl get ksvc
```
✅ Tu devrais voir ton service avec une URL !

4️⃣ Tester l’application
```
curl http://hello-world.default.127.0.0.1.sslip.io
```
📌 Si tout fonctionne, tu verras Hello Knative! affiché. 🎉
________________________________________
📌 4. Tester l’Auto-Scaling (scale-to-zero)

Knative peut réduire automatiquement le nombre de pods à zéro quand ils ne sont pas utilisés.

🔹 Vérifions les pods actifs :
```
kubectl get pods
```
Tu verras un pod hello-world-xxxx-xxx en Running.

🔹 Attends 2-3 minutes sans envoyer de requêtes...

Puis, vérifie à nouveau :
```
kubectl get pods
```
✅ Le pod a disparu ! (Knative a scalé à 0)

🔹 Refais une requête avec curl :
```
curl http://hello-world.default.127.0.0.1.sslip.io
```
✅ Knative va automatiquement redémarrer le pod pour gérer la requête !
________________________________________
📌 5. Knative Eventing : Déclencher un service via un événement Knative Eventing permet d’exécuter des applications en réponse à des événements (Kafka, HTTP, etc.).

1️⃣ Installer Knative Eventing
```
kubectl apply -f https://github.com/knative/eventing/releases/latest/download/eventing-crds.yaml
kubectl apply -f https://github.com/knative/eventing/releases/latest/download/eventing-core.yaml
```
2️⃣ Créer un Broker pour les événements
```
kubectl apply -f - <<EOF
apiVersion: eventing.knative.dev/v1
kind: Broker
metadata:
  name: default
EOF
```
3️⃣ Créer un Trigger pour écouter un événement
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
      name: hello-world
```
Déploiement :
```
kubectl apply -f trigger.yaml
```
4️⃣ Envoyer un événement pour tester
```
kubectl -n default get broker
kubectl -n default get ksvc
```

```
curl -v "http://broker-ingress.knative-eventing.svc.cluster.local/default" \
  -X POST \
  -H "Ce-Id: 1234" \
  -H "Ce-Specversion: 1.0" \
  -H "Ce-Type: dev.knative.example.event" \
  -H "Ce-Source: my-test" \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello from Eventing!"}'
```
✅ Knative déclenche hello-world en réponse à cet événement ! 🎉
________________________________________
📌 6. Supprimer les ressources

Pour nettoyer l’environnement :
```
kubectl delete -f hello-world.yaml
kubectl delete -f trigger.yaml
kubectl delete -f broker.yaml
```
________________________________________
🎯 Conclusion

✔ Knative permet d’exécuter des applications serverless sur Kubernetes

✔ L’auto-scaling gère le traffic de manière dynamique (scale-to-zero)

✔ Avec Eventing, on peut connecter des services de manière asynchrone

✔ Alternative open-source à AWS Lambda et Google Cloud Run


