### AWS Cloud Development Kit CDK for IaaS

https://cloudbytes.dev/aws-academy/install-and-configure-aws-cloud-development-kit-aws-cdk

nvm current
nvm list
nvm use 22.15.0
npm install -g aws-cdk




```bash
--platform=manylinux_2_17_x86_64 --only-binary=:all:
```

est utilisée avec `pip` (le gestionnaire de paquets Python) pour **forcer l'installation de paquets binaires compatibles avec une plateforme précise**, en évitant les compilations locales.

---

### ⚙️ Elle est souvent utilisée avec la commande `pip download` ou `pip install`. Par exemple :

```bash
pip install --platform=manylinux_2_17_x86_64 --only-binary=:all: <nom_du_paquet> --target ./build_dir
```

---

### 📌 À quoi ça sert précisément ?

#### 1. **`--platform=manylinux_2_17_x86_64`**

* Tu dis à `pip` : *"Je veux installer des paquets **compilés pour cette plateforme** (Linux x86\_64, conforme à manylinux 2.17)"*.
* Cela permet de préparer des paquets pour un système Linux **même si tu es sur macOS ou Windows**.
* Utile pour le **cross-building** ou pour préparer des déploiements Docker, Lambda, etc.

#### 2. **`--only-binary=:all:`**

* Tu dis à `pip` : *"Je veux **uniquement des fichiers binaires (.whl)** — pas de compilation à partir du code source".*
* Cela évite de devoir compiler des paquets C/C++ (comme NumPy, lxml...) localement.

> ➕ Gain de temps, évite les erreurs de compilation, surtout dans des environnements sans `gcc`, `make`, etc.

---

### 🧠 Cas d’usage courant :

* Construire une couche **AWS Lambda** avec `pip install --platform` et `--only-binary`
* Créer un **package zip portable** pour un environnement Linux (depuis un poste Windows/macOS)
* Préparer une **image Docker** sans avoir à installer les outils de build (build-essential...)

---

### 🚫 Sans ces options :

* `pip` essayera de compiler les paquets s'il ne trouve pas de wheel compatible avec ta machine.
* Tu risques d’avoir des erreurs du type `unable to build wheel` si les dépendances systèmes sont absentes.

---

### 🔁 Exemple concret :

```bash
pip install --platform=manylinux_2_17_x86_64 \
            --only-binary=:all: \
            --target lambda_layer \
            requests numpy
```

Cela va télécharger les `.whl` précompilés de `requests` et `numpy` pour Linux x86\_64, et les placer dans `lambda_layer/`.



Super ! Je vais te montrer **comment adapter ce script** selon ton **environnement local**. Choisis ton cas ci-dessous, je te guide ensuite précisément :

---

### 💻 Quel est ton environnement actuel ?

#### 🔹 1. **Windows (sans WSL ni Docker)**

→ Utilisation de **Git Bash** ou **CMD/PowerShell**, avec `pip` natif.

#### 🔹 2. **Windows avec WSL (Ubuntu)**

→ Tu travailles **comme sous Linux**, tout fonctionne comme un shell bash.

#### 🔹 3. **macOS ou Linux natif**

→ Aucun souci, tu peux exécuter le script tel quel avec bash.

#### 🔹 4. **Via Docker pour cross-build (recommandé)**

→ Solution propre et universelle, même sur Windows. Utilise un container officiel pour simuler Linux (`manylinux`).

---

### 🧱 Recommandé : méthode Docker universelle (fonctionne sur **tous les OS**)

```bash
# Crée un dossier pour tes dépendances
mkdir -p layer_build

# Lancer un conteneur manylinux pour construire les packages
docker run --rm -v "$PWD/layer_build:/var/task" \
  -w /var/task \
  lambci/lambda:build-python3.9 \
  pip install requests numpy --only-binary=:all: --target python

# Créer l'archive ZIP
cd layer_build
zip -r ../lambda_layer.zip python
cd ..
```

🟢 Avantages :

* 100 % compatible AWS Lambda
* Pas besoin de configurer manylinux localement
* Pas de conflits entre plateformes

---
