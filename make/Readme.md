Automatiser des tâches DevOps avec make

📌 Makefile utilisé pour Docker et Kubernetes :

```
build:
	docker build -t myapp .

run:
	docker run -d -p 8080:8080 myapp

deploy:
	kubectl apply -f deployment.yaml
```

Exécution :

```
make build   # Construit l'image Docker
make run     # Lance le conteneur
make deploy  # Déploie sur Kubernetes
```

💡 Explication : make est utilisé ici comme outil DevOps pour gérer les builds et le déploiement.

📌 Conclusion

make est un outil puissant pour automatiser les builds, tests, déploiements et autres tâches répétitives.

Il repose sur un Makefile qui définit les règles à suivre.

Très utilisé en développement logiciel, DevOps, CI/CD, et automatisation.