### **🔍 Kubeaudit : Outil d'Audit de Sécurité Kubernetes**  

**Kubeaudit** est un outil open-source développé par **Shopify** qui permet d’analyser la **sécurité des configurations Kubernetes**. Il aide à identifier et corriger les **mauvaises pratiques** de sécurité sur les **déploiements Kubernetes**.  

---

## **📌 Fonctionnalités Principales**
🔹 **Audit Automatisé** des configurations des pods et des workloads  
🔹 **Détection des erreurs courantes** de sécurité (exécution en tant que root, manque de limites de ressources, etc.)  
🔹 **Recommandations et correctifs automatiques**  
🔹 **S'intègre facilement** dans un pipeline CI/CD  

---

## **📌 Problèmes Détectés par Kubeaudit**
✅ **Pods en tant que root** : Vérifie si un conteneur tourne en mode `root`  
✅ **Manque de limites de ressources** : Vérifie l'absence de `limits` et `requests` CPU/mémoire  
✅ **Capabilities dangereuses** : Détecte des permissions excessives (`NET_RAW`, `SYS_ADMIN`, etc.)  
✅ **Volumes hostPath** : Vérifie si un pod accède au système de fichiers de l'hôte  
✅ **RBAC mal configuré** : Vérifie les permissions trop larges accordées aux services  

---

## **📌 Installation et Utilisation**
### **1️⃣ Installer Kubeaudit**
```bash
brew install kubeaudit  # macOS
go install github.com/Shopify/kubeaudit@latest  # via Go
```
  
### **2️⃣ Exécuter un audit sur un cluster Kubernetes**
```bash
kubeaudit all -kubeconfig ~/.kube/config
```

### **3️⃣ Exécuter un audit sur un fichier de configuration**
```bash
kubeaudit all -f deployment.yaml
```

### **4️⃣ Appliquer des correctifs automatiquement**
```bash
kubeaudit autofix -f deployment.yaml
```

---

## **📌 Pourquoi Utiliser Kubeaudit ?**
✔ **Automatisation des audits de sécurité** dans un pipeline CI/CD  
✔ **Gain de temps** pour identifier et corriger les erreurs de configuration  
✔ **Meilleures pratiques de sécurité Kubernetes** appliquées automatiquement  
