🎯 Comment utiliser Crossplane avec ArgoCD ?
On va voir comment provisionner une base de données RDS sur AWS avec Crossplane, tout en gérant tout avec ArgoCD.

1️⃣ Installer Crossplane sur Kubernetes

Tout d'abord, installons Crossplane dans un cluster Kubernetes.

```
kubectl create namespace crossplane-system
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm install crossplane crossplane-stable/crossplane --namespace crossplane-system
```

2️⃣ Configurer un Provider Cloud (AWS)

Crossplane utilise des Providers pour interagir avec les services cloud.

🔹 Installer le provider AWS
```
apiVersion: pkg.crossplane.io/v1
kind: Provider
metadata:
  name: provider-aws
spec:
  package: "crossplane/provider-aws:v0.28.0"
```
📌 Ajoute ce YAML dans ton repo Git pour qu’ArgoCD le déploie automatiquement !

3️⃣ Ajouter des Credentials AWS

On crée une ProviderConfig pour permettre à Crossplane d’accéder à AWS.
```
apiVersion: aws.crossplane.io/v1beta1
kind: ProviderConfig
metadata:
  name: default
spec:
  credentials:
    source: Secret
    secretRef:
      namespace: crossplane-system
      name: aws-secret
      key: creds
```
🔹 Créer un secret avec les credentials AWS
```
kubectl create secret generic aws-secret -n crossplane-system  --from-file=creds=$HOME/.aws/credentials
```

4️⃣ Provisionner une base de données RDS avec Crossplane
On crée un XRD (Composite Resource Definition) pour gérer une base de données RDS.
```
apiVersion: database.aws.crossplane.io/v1alpha1
kind: RDSInstance
metadata:
  name: my-db
spec:
  forProvider:
    region: us-east-1
    dbInstanceClass: db.t3.micro
    masterUsername: admin
    masterPasswordSecretRef:
      namespace: crossplane-system
      name: db-secret
      key: password
  providerConfigRef:
    name: default
```

🔹 Créer un secret pour le mot de passe RDS
```
kubectl create secret generic db-secret -n crossplane-system --from-literal=password="SuperSecret123!"
```

5️⃣ Intégrer Crossplane avec ArgoCD

Ajoute ces manifests à ton repo GitOps et crée une Application ArgoCD :
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: crossplane-rds
  namespace: argocd
spec:
  destination:
    namespace: crossplane-system
    server: https://kubernetes.default.svc
  source:
    repoURL: "https://github.com/mon-org/mon-repo"
    targetRevision: main
    path: crossplane/rds
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```
ArgoCD va déployer et gérer la base de données automatiquement en appliquant les fichiers YAML.

🎯 Conclusion

✔ Crossplane permet de gérer l’infrastructure cloud avec des YAML Kubernetes.

✔ ArgoCD facilite l’intégration GitOps pour automatiser le provisioning.

✔ On peut provisionner des bases de données, des VPCs, et bien plus !
