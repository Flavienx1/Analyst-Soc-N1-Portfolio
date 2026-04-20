# 🛡️ Lab SOC — Reconstitution d’une Kill Chain complète 

## 📌 Objectif
Reconstituer une kill chain complète à partir de logs (Windows, réseau, web).

---

## 🧠 Scénario

Un utilisateur a été compromis suite à un phishing.  
L’attaquant a exécuté un malware, mis en place une persistance, communiqué avec un serveur C2 et lancé un crypto miner.

---

## 🔗 Kill Chain

1. Reconnaissance  
2. Accès initial  
3. Exécution  
4. Persistance  
5. Command & Control (C2)  
6. Impact  

---

## 🔍 Étape 1 — Reconnaissance

### Log Web (Apache)
```
203.0.113.45 - - [12/May/2021:09:10:21 +0000] "GET /admin HTTP/1.1" 404 512
203.0.113.45 - - [12/May/2021:09:10:23 +0000] "GET /login HTTP/1.1" 200 1024
203.0.113.45 - - [12/May/2021:09:10:25 +0000] "GET /wp-login.php HTTP/1.1" 200 2048
203.0.113.45 - - [12/May/2021:09:10:27 +0000] "GET /phpmyadmin HTTP/1.1" 404 512
```

### Analyse
- Scan de ressources sensibles
- Comportement d’énumération

---

## 🚪 Étape 2 — Accès initial (Phishing)

### Log HTTP
```
10.0.0.15 - - [12/May/2021:09:15:02 +0000] "GET /invoice.html HTTP/1.1" 200 1543 "-" "Mozilla/5.0"
10.0.0.15 - - [12/May/2021:09:15:05 +0000] "GET /update.dat HTTP/1.1" 200 53248 "-" "Mozilla/5.0"
```

### Analyse
- Accès à une page “invoice”
- Téléchargement d’un fichier suspect

---

## ⚙️ Étape 3 — Exécution

### Log Windows (Event ID 4688)
```
New Process Created:
Creator Process Name: C:\Program Files\Internet Explorer\iexplore.exe
New Process Name: C:\Users\user\AppData\Local\Temp\update.exe
Command Line: update.exe
```

### Analyse
- Processus lancé depuis navigateur
- Exécution d’un fichier téléchargé

---

## 🔁 Étape 4 — Persistance

### Log Windows (Event ID 4688)
```
New Process Created:
New Process Name: C:\Windows\System32\schtasks.exe
Command Line: schtasks /create /sc minute /mo 30 /tn "Updater" /tr "C:\Users\user\AppData\Local\Temp\update.exe"
```

### Analyse
- Création d’une tâche planifiée
- Persistance confirmée

---

## 🌐 Étape 5 — Command & Control (C2)

### Log Réseau
```
10.0.0.15 -> 198.51.100.25:4444 TCP
10.0.0.15 -> 198.51.100.25:4444 TCP
10.0.0.15 -> 198.51.100.25:4444 TCP
```

### Log HTTP
```
10.0.0.15 - - [12/May/2021:09:20:10 +0000] "POST /checkin HTTP/1.1" 200 42
```

### Analyse
- Communication répétée vers IP externe
- Port non standard → suspect
- Beaconing C2

---

## 💥 Étape 6 — Impact (Crypto Miner)

### Log Windows (Event ID 4688)
```
New Process Created:
New Process Name: C:\Users\user\AppData\Roaming\miner.exe
Command Line: miner.exe -o pool.minexmr.com:3333 -u wallet123
```

### Log Réseau
```
10.0.0.15 -> pool.minexmr.com:3333 TCP
```

### Analyse
- Exécution d’un crypto miner
- Connexion à un pool de minage

---

## 🧠 Timeline

| Temps | Événement |
|------|----------|
| 09:10 | Scan web (reconnaissance) |
| 09:15 | Phishing + téléchargement |
| 09:16 | Exécution du malware |
| 09:17 | Persistance |
| 09:20 | Communication C2 |
| 09:22 | Lancement du miner |

---

## 🚨 IOC

- IP attaquant : 203.0.113.45  
- IP C2 : 198.51.100.25  
- Domaine : pool.minexmr.com  
- Fichier : update.exe / miner.exe  

---

## 🛡️ Actions recommandées

- Bloquer les IP malveillantes  
- Supprimer la tâche planifiée  
- Isoler le poste  
- Scanner le système  
- Réinitialiser les identifiants  

---

## 📊 Conclusion

Attaque complète reconstruite :

Phishing → Exécution → Persistance → C2 → Impact  

Compromission confirmée avec activité malveillante active.

---

## 🧪 Compétences

- Analyse de logs Windows  
- Analyse réseau  
- Reconstruction d’attaque  
- Méthodologie SOC  

---

