### **🚀 Intégration de Kubeaudit dans un Pipeline CI/CD (GitLab & Jenkins)**  

L'intégration de **Kubeaudit** dans un **pipeline CI/CD** permet d'auditer les configurations Kubernetes **automatiquement** à chaque déploiement et d’empêcher les configurations non sécurisées.  

---

## **1️⃣ Intégration avec GitLab CI/CD**  

### **📌 Étapes**  
1. **Ajouter Kubeaudit à l'image Docker utilisée dans le pipeline**  
2. **Exécuter un audit de sécurité sur les fichiers YAML Kubernetes**  
3. **Générer un rapport et échouer le pipeline en cas d'erreur critique**  

### **📜 Exemple `.gitlab-ci.yml` avec Kubeaudit**
```yaml
stages:
  - security_check

kubeaudit:
  image: shopify/kubeaudit:latest  # Utilisation de l'image officielle
  stage: security_check
  script:
    - echo "🔍 Exécution de Kubeaudit sur les manifests YAML..."
    - kubeaudit all -f k8s/deployment.yaml || exit 1  # Arrête le pipeline si erreur
  only:
    - main  # S'exécute uniquement sur la branche principale
```

👉 **Explication** :  
✅ **Analyse le fichier `k8s/deployment.yaml`** avant déploiement  
✅ **Arrête le pipeline (`exit 1`) si des erreurs sont détectées**  
✅ **Facilement intégrable avec d'autres outils comme kube-score ou trivy**  

---

## **2️⃣ Intégration avec Jenkins**
### **📌 Étapes**
1. **Installer Kubeaudit sur le serveur Jenkins**
2. **Créer un job Jenkins pour exécuter Kubeaudit**
3. **Empêcher les configurations non sécurisées de passer en production**

### **📜 Exemple de Pipeline Jenkins avec Kubeaudit**
```groovy
pipeline {
    agent any
    stages {
        stage('Security Audit') {
            steps {
                sh 'kubeaudit all -f k8s/deployment.yaml || exit 1'
            }
        }
    }
}
```
👉 **Explication** :  
✅ **Le pipeline exécute Kubeaudit** sur `k8s/deployment.yaml`  
✅ **En cas d’erreur critique, le déploiement échoue**  

---

## **📌 Résumé et Avantages**
🔹 **Empêche les mauvaises pratiques de sécurité** d’atteindre l’environnement de prod  
🔹 **Facile à intégrer dans GitLab CI/CD et Jenkins**  
🔹 **Détecte les permissions excessives, les pods root et autres failles**  

---