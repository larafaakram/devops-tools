📌 Objectifs du Makefile

✅ Construire et exécuter une image Docker

✅ Déployer sur Kubernetes avec Helm

✅ Appliquer une infrastructure avec Terraform

✅ Nettoyer l’environnement après usage

📝 Exemple de Makefile pour DevOps
# Définition des variables
```
IMAGE_NAME=myapp
TAG=latest
REGISTRY=docker.io/myrepo
KUBE_NAMESPACE=default
```

# Terraform
```
TF_DIR=terraform
TF_PLAN=plan.tfplan
```

# Commandes Docker
```
build:
	docker build -t $(IMAGE_NAME):$(TAG) .

push:
	docker tag $(IMAGE_NAME):$(TAG) $(REGISTRY)/$(IMAGE_NAME):$(TAG)
	docker push $(REGISTRY)/$(IMAGE_NAME):$(TAG)

run:
	docker run -d -p 8080:8080 --name $(IMAGE_NAME) $(IMAGE_NAME):$(TAG)

stop:
	docker stop $(IMAGE_NAME) && docker rm $(IMAGE_NAME)
```

# Kubernetes avec Helm
```
deploy:
	helm upgrade --install $(IMAGE_NAME) ./helm-chart --namespace $(KUBE_NAMESPACE)

delete:
	helm uninstall $(IMAGE_NAME) --namespace $(KUBE_NAMESPACE)
```

# Terraform
```
tf-init:
	cd $(TF_DIR) && terraform init

tf-plan:
	cd $(TF_DIR) && terraform plan -out=$(TF_PLAN)

tf-apply:
	cd $(TF_DIR) && terraform apply $(TF_PLAN)

tf-destroy:
	cd $(TF_DIR) && terraform destroy -auto-approve
```

# Nettoyage
```
clean:
	docker system prune -f
	rm -rf $(TF_DIR)/.terraform $(TF_DIR)/terraform.tfstate* $(TF_DIR)/$(TF_PLAN)

.PHONY: build push run stop deploy delete tf-init tf-plan tf-apply tf-destroy clean
```

🚀 Comment l’utiliser ?

1️⃣ Créer un fichier Makefile à la racine du projet

2️⃣ Exécuter les commandes depuis le terminal :
```
make build       # Construction Docker
make push        # Push vers Docker Hub
make deploy      # Déploiement Kubernetes
make tf-apply    # Application Terraform
make clean       # Nettoyage
```