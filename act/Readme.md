### **Act : Un outil pour exécuter des GitHub Actions en local** 🚀  

**Act** est un outil en ligne de commande qui permet d'exécuter des workflows GitHub Actions **en local** avant de les pousser sur GitHub. Il est utile pour **tester** et **déboguer** tes pipelines CI/CD sans avoir à faire un commit et attendre l'exécution sur GitHub.  

---

### **📌 Pourquoi utiliser Act ?**
1. **Tester rapidement tes GitHub Actions en local**  
   → Plus besoin de faire un commit + push pour voir si ton workflow fonctionne.  

2. **Gagner du temps et économiser des ressources**  
   → Évite d'utiliser inutilement les minutes gratuites des GitHub Actions.  

3. **Déboguer facilement tes pipelines**  
   → Act permet de voir directement les erreurs et logs localement.  

4. **Travailler hors ligne**  
   → Utile si tu es dans un environnement sans connexion internet stable.  

---

### **📌 Comment utiliser Act ?**
#### **1️⃣ Installation**
```bash
# Sur Linux/macOS (via Homebrew)
brew install nektos/tap/act

# Sur Windows (via Scoop)
scoop install act
```
  
#### **2️⃣ Exécuter un workflow GitHub Actions en local**
Dans un dépôt contenant un fichier **`.github/workflows/ci.yml`**, lance :
```bash
act
```
Cela exécutera le workflow comme si GitHub l'avait déclenché.  

#### **3️⃣ Simuler un événement spécifique**
Exécuter un workflow en simulant un **push** :
```bash
act push
```
Ou un **pull request** :
```bash
act pull_request
```

#### **4️⃣ Voir tous les jobs disponibles**
```bash
act -l
```

---

### **📌 Exemple d'utilisation avec un projet Python**
Si ton dépôt contient un fichier `.github/workflows/python-ci.yml` comme ceci :
```yaml
name: Python CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run tests
        run: pytest
```
Tu peux exécuter ce workflow en local avec :
```bash
act push
```

---

### **📌 Conclusion**
Si tu travailles avec **GitHub Actions**, **Act** est un excellent outil pour **tester** et **déboguer** tes workflows **en local** avant de les pousser. C'est un **gros gain de temps** pour les développeurs DevOps et Backend ! 🚀🔥  

Tu veux l'utiliser pour un projet précis ? 😊