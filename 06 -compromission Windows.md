# 🚨 Incident Response Lab — Windows Compromise (LSASS Dump & Lateral Movement)

## 🎯 Résumé rapide

- Compte compromis : svc_backup (logon type 3)
- Exécution suspecte : cmd.exe → whoami
- Credential dumping : rundll32 + comsvcs.dll (LSASS)
- Mouvement latéral : accès Administrator
- Statut : Compromission confirmée

---

## ⚡ Impact

- Vol d’identifiants
- Escalade privilèges
- Propagation possible dans le domaine

---

## 🛡️ Actions

- Isolation machine
- Désactivation comptes
- Blocage IP
- Escalade SOC N2




 # Début de l'analyse commplète 
## 📌 Contexte
Un serveur Windows critique (FILE-SRV01) déclenche une alerte suite à une activité anormale sur un compte de service. L’objectif est d’analyser les événements afin de déterminer s’il s’agit d’une compromission.

---

## 🔍 Détection initiale

- Event ID : 4624 (Logon réussi)
- Logon Type : 3 (Network)
- Compte : svc_backup
- IP source : 10.10.14.23

Connexion réseau suspecte pour un compte de service.

---

## 📊 Analyse

### 1. Connexion suspecte
```powershell
EventCode=4624
Account_Name=svc_backup
Logon_Type=3
Source_Network_Address=10.10.14.23
``` 

Utilisation probable de credentials compromis.

---

### 2. Reconnaissance système
```powershell
EventCode=4688
New_Process_Name=C:\Windows\System32\cmd.exe
Creator_Process_Name=C:\Windows\System32\services.exe
CommandLine=cmd.exe /c whoami
```

Vérification des privilèges.

---

### 3. Credential Dumping (LSASS)
```powershell
EventCode=4688
New_Process_Name=C:\Windows\System32\rundll32.exe
CommandLine=rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump lsass.exe C:\ProgramData\lsass.dmp full
```

Dump LSASS pour extraction des identifiants (MITRE T1003).

---

### 4. Création du fichier dump
File created: C:\ProgramData\lsass.dmp  

Extraction potentielle de mots de passe.  

---

### 5. Mouvement latéral  
```powershell
EventCode=4624
Account_Name=Administrator
Logon_Type=3
Source_Network_Address=FILE-SRV01
```

Utilisation des credentials pour accès administrateur.

---

## ⏱️ Timeline

1. Connexion suspecte via svc_backup depuis 10.10.14.23
2. Exécution de whoami (reconnaissance)
3. Dump LSASS via rundll32
4. Création du fichier lsass.dmp
5. Connexion en Administrator (mouvement latéral)

---

## ⚠️ Conclusion

- Type d’attaque : Credential Dumping + Lateral Movement
- Accès initial : Compte compromis (svc_backup)
- Escalade : Administrator
- Statut : Compromission confirmée

Risque élevé de propagation dans le domaine.

---

## 🛡️ Actions SOC

- Isoler FILE-SRV01
- Désactiver les comptes compromis
- Supprimer lsass.dmp
- Rechercher d’autres connexions suspectes
- Bloquer l’IP 10.10.14.23
- Escalade SOC N2

---

## 🔎 Requêtes Splunk

index=windows EventCode=4624 Account_Name=svc_backup

index=windows EventCode=4688 CommandLine="*rundll32*"

index=windows "lsass.dmp"

