# Bonjour 😊 

Quand tu lances une instance EC2, tu peux utiliser un **user data script** (aussi appelé **"cloud-init"** dans le monde Linux) pour exécuter des commandes automatiquement **au premier démarrage**. Mais… comment savoir si ce script a bien été exécuté ? Voici plusieurs manières de vérifier ça dans une EC2 Linux :

---

### 🔍 1. **Consulter les logs cloud-init sur l'EC2**

Connecte-toi à ton instance EC2 via SSH, puis exécute :

```bash
sudo cat /var/log/cloud-init-output.log
```

Ce fichier contient :
- La **sortie standard** du script `user-data`
- Les éventuelles erreurs rencontrées pendant l’exécution

Tu peux aussi regarder ce fichier :

```bash
sudo cat /var/log/cloud-init.log
```

Il est plus verbeux et montre les différentes étapes de `cloud-init`.

---

### 📁 2. **Regarder si ton script est bien passé**
Si tu veux savoir si ton script a simplement été lu/traité :

```bash
sudo cat /var/lib/cloud/instance/user-data.txt
```

Il te montre le contenu du script qui a été exécuté. Tu peux comparer avec ce que tu avais mis dans la console.

---

### ✅ 3. **Vérifier les effets**
Parfois, le script s’exécute bien mais tu ne vois rien car il ne fait rien de "visuel". Quelques astuces :
- Mets des logs dans ton script, exemple :

```bash
#!/bin/bash
echo "Hello from user-data script" >> /var/log/user-data.log
```

- Ensuite, une fois connecté à ta machine :

```bash
cat /var/log/user-data.log
```

---

### 🛠️ 4. **Relancer manuellement pour tester**
Tu peux tester ton script manuellement (attention, certains éléments ne se comportent pas pareil que lors du boot initial) :

```bash
sudo bash /var/lib/cloud/instance/scripts/part-001
```

---

### 💡 Bonus : Tester user-data sans relancer une instance
Tu peux :
- Créer une AMI à partir d’une instance
- Lancer une nouvelle instance avec un `user-data`
- Ou utiliser un outil comme **Cloud-Init EC2 local** ou **EC2 Instance Connect + script temporaire**
