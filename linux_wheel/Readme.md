Un **Linux wheel** (ou plus précisément un **Python wheel binaire pour Linux**) est un **fichier précompilé** contenant une bibliothèque Python prête à être installée, **sans avoir à la recompiler sur ta machine**.

---

### 🧱 Définition : **Wheel (.whl)**

Un fichier **`.whl`** est un **paquet binaire** standard pour Python, défini par la **PEP 427**.

* Il contient **le code déjà compilé** (souvent en C/C++) pour une plateforme spécifique.
* Il est plus rapide à installer qu’un paquet source (`.tar.gz`, `.zip`), car il n’a **pas besoin de compilation locale**.

---

### 🐧 Et un **Linux wheel**, alors ?

> C’est un fichier `.whl` **compilé spécifiquement pour Linux**, selon certaines conventions de compatibilité comme **`manylinux`**.

---

### 📦 Exemple de nom de wheel Linux :

```bash
numpy-1.26.2-cp39-cp39-manylinux_2_17_x86_64.whl
```

Décomposons-le :

| Élément                 | Signification                        |
| ----------------------- | ------------------------------------ |
| `numpy-1.26.2`          | Nom et version du paquet             |
| `cp39`                  | CPython 3.9 (interpréteur cible)     |
| `manylinux_2_17_x86_64` | Compatible avec Linux (glibc ≥ 2.17) |

---

### 🔄 Pourquoi `manylinux` ?

* Les distributions Linux sont nombreuses (Ubuntu, CentOS, Alpine…).
* `manylinux` est une **spécification** définie par Python pour garantir qu'un wheel compilé **fonctionne sur la majorité des distributions Linux**.
* AWS Lambda, par exemple, exige des wheels **compatible `manylinux`**.

> ✅ Un wheel manylinux est un "binaire portable Linux".

---

### 🛠️ À quoi ça sert concrètement ?

* **Installation rapide** : pas de compilation (gain de temps + évite les erreurs)
* **Déploiement AWS Lambda** : tu dois fournir des dépendances déjà compilées pour Linux (manylinux)
* **CI/CD** : on utilise souvent des wheels manylinux pour packager et distribuer des libs Python compilées (comme NumPy, pandas, etc.)

---

### 🔍 Où trouver ces wheels ?

* Sur [https://pypi.org](https://pypi.org) — PyPI héberge les wheels pour chaque plateforme si disponibles
* Tu peux aussi les construire toi-même avec Docker et `auditwheel`

---

Souhaites-tu que je t'explique **comment créer ton propre wheel Linux manylinux** pour ton projet Python ?

Parfait ! Voici **comment créer ton propre Linux wheel compatible manylinux**, que tu pourras ensuite **distribuer** ou **déployer dans AWS Lambda, Docker, etc.**

---

## 🧱 Objectif :

Créer un `.whl` binaire **portable pour Linux** à partir de ton projet Python, compatible `manylinux`.

---

## ✅ Prérequis

* Ton projet Python doit contenir :

  * Un fichier `setup.py` (ou `pyproject.toml`)
  * Ton code dans un dossier (ex : `mon_module/`)

Exemple `setup.py` simple :

```python
from setuptools import setup

setup(
    name="mon_module",
    version="0.1",
    packages=["mon_module"],
)
```

---

## ⚙️ Étapes pour créer un wheel manylinux

### ✅ 1. Installer Docker (si pas déjà fait)

Télécharge Docker ici : [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

---

### ✅ 2. Structure de base de ton projet :

```
mon_projet/
├── mon_module/
│   └── __init__.py
├── setup.py
```

---

### ✅ 3. Lancer la construction dans un container manylinux

Utilise le conteneur officiel `quay.io/pypa/manylinux_2_28_x86_64` (recommandé pour Python ≥ 3.8) :

```bash
docker run --rm -v $(pwd):/io quay.io/pypa/manylinux_2_28_x86_64 \
  /bin/bash -c "
    cd /io &&
    pip install build &&
    python3 -m build --wheel &&
    auditwheel repair dist/*.whl --plat manylinux_2_28_x86_64 -w dist/
  "
```

📂 Résultat :

* Un fichier `.whl` corrigé avec `auditwheel` sera généré dans le dossier `dist/`.
* Ce fichier sera **installable sur toutes les distribs Linux supportant manylinux**, y compris **AWS Lambda**.

---

## 🧪 Tester l'installation du wheel

Tu peux ensuite tester localement :

```bash
pip install dist/mon_module-0.1-*.whl
```

---

## 📦 Astuce pour un module natif (avec code C/C++) :

Si ton module contient du C ou Cython, il sera compilé à l'intérieur du container.

Tu peux ajouter :

```python
from setuptools import Extension

setup(
    ...
    ext_modules=[
        Extension("mon_module.cmoncode", sources=["mon_module/cmoncode.c"])
    ]
)
```

---

Souhaites-tu que je te fournisse un **exemple complet de projet** avec un module natif C, ou veux-tu transformer un **projet Python existant** en wheel manylinux ?
