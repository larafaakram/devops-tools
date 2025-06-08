Un **CMDB** (Configuration Management Database), ou en français **base de données de gestion de configuration**, est un composant central dans les pratiques **ITSM** (IT Service Management), notamment dans les frameworks comme **ITIL**.

---

## 🧠 **Définition simple**

> Une **CMDB** est une base de données qui contient des informations sur tous les **actifs informatiques (IT assets)** d'une organisation et leurs **relations**.

---

## 📦 **Que contient une CMDB ?**

Elle stocke des objets appelés **CI (Configuration Items)**, comme :

* Machines physiques et virtuelles
* Applications
* Réseaux, routeurs, firewalls
* Instances cloud (EC2, RDS, etc.)
* Services (DNS, mail, web)
* Dépendances entre composants
* Utilisateurs ou groupes

---

## 🔗 **Exemples de relations dans une CMDB**

Un exemple :

* L’application `App_X` dépend du serveur `VM42`
* Ce serveur est hébergé sur `vSphere` ou `AWS`
* `VM42` est surveillée par `Prometheus`
* Le service `DNS` est utilisé par `App_X`

---

## 🛠️ **À quoi sert une CMDB ?**

| Utilisation                                      | Description                                      |
| ------------------------------------------------ | ------------------------------------------------ |
| 🔍 **Visualiser l’infrastructure**               | Voir tous les composants et leurs dépendances    |
| 🧩 **Diagnostiquer plus vite**                   | Identifier ce qui est affecté lors d’un incident |
| 📋 **Gestion du changement (Change Management)** | Comprendre l’impact d’un changement              |
| 🔐 **Audit & conformité**                        | Savoir qui a quoi, où, et pourquoi               |
| 🧾 **Inventaire centralisé**                     | Automatiser la documentation de l’environnement  |

---

## 🌐 **Exemples d’outils CMDB**

* **ServiceNow CMDB** (le plus connu en entreprise)
* **iTop** (open source)
* **Rudder**
* **Device42**
* **GLPI + FusionInventory**
* **BMC Atrium**

Certains outils modernes comme **Ansible AWX**, **Rundeck**, ou **Terraform Cloud** peuvent **interagir avec des CMDBs** pour obtenir ou stocker des infos.

---

## 🔄 **CMDB vs Cloud Providers**

* Un **cloud provider** (AWS, Azure, etc.) fournit l’infrastructure.
* Un **CMDB** garde une trace de **ce que vous avez dans le cloud**, de **comment c’est configuré**, et **qui y accède**.
