# Configuration d'un réseau avec serveur DNS, DHCP, FTP et SFTP

Ce projet consiste à configurer un réseau local avec deux machines virtuelles sous Debian. La **Machine 1** agit comme serveur DNS et DHCP, tandis que la **Machine 2** héberge un serveur FTP et SFTP. L'objectif est de permettre une communication sécurisée entre les machines via des services réseau standard.

---


## 🎯 Objectifs
- **Machine 1** :
  - Installer et configurer un serveur DHCP pour attribuer des adresses IP.
  - Installer et configurer un serveur DNS pour résoudre le nom de domaine `dns.ftp.com`.
- **Machine 2** :
  - Installer et configurer un serveur FTP (proFTPd) et SFTP.
  - Restreindre l'accès au serveur SFTP avec des identifiants spécifiques.
  - Utiliser `ufw` pour sécuriser les ports ouverts.
- **Communication** :
  - Tester la connexion SFTP entre les machines en utilisant le nom de domaine `dns.ftp.com`.

---

## 🖥️ Configuration des machines

### Adresses IP
- **Machine 1 (Serveur DNS et DHCP)** : `172.16.1.1`
- **Machine 2 (Serveur FTP et SFTP)** : `172.16.1.102`

### Réseau
- Les deux machines sont connectées à un réseau virtuel (VirtualBox, VMware, etc.).
- La Machine 1 a une adresse IP fixe, tandis que la Machine 2 obtient son adresse IP via DHCP.

---

## 🛠️ Installation et configuration des services

### Sur la Machine 1
1. **Serveur DHCP** :
   - Installer `isc-dhcp-server`.
   - Configurer `/etc/dhcp/dhcpd.conf` pour attribuer des adresses IP dans la plage `172.16.0.0/16`.
   - Redémarrer le service : `sudo systemctl restart isc-dhcp-server`.

2. **Serveur DNS** :
   - Installer `bind9`.
   - Créer un fichier de zone (`/etc/bind/db.dns.ftp.com`) pour `dns.ftp.com`.
   - Configurer `/etc/bind/named.conf.local` pour référencer la zone.
   - Redémarrer le service : `sudo systemctl restart bind9`.

### Sur la Machine 2
1. **Serveur FTP** :
   - Installer `proftpd`.
   - Configurer `/etc/proftpd/proftpd.conf` pour limiter les connexions.
   - Créer un utilisateur FTP : `laplateforme` avec le mot de passe `Marseille13!`.

2. **Serveur SFTP** :
   - Utiliser SSH pour permettre les connexions SFTP.
   - Changer le port SSH à `6500` dans `/etc/ssh/sshd_config`.
   - Redémarrer le service : `sudo systemctl restart ssh`.

3. **Configuration d'`ufw`** :
   - Installer `ufw` si ce n'est pas déjà fait : `sudo apt install ufw`.
   - Autoriser le port `6500` pour SFTP :
     ```bash
     sudo ufw allow 6500/tcp
     ```
   - Activer `ufw` :
     ```bash
     sudo ufw enable
     ```

---

## 🧪 Tests et vérifications

### Résolution DNS
Sur la Machine 2, vérifier que le nom de domaine `dns.ftp.com` est résolu en `172.16.1.102` :
```bash
nslookup dns.ftp.com



# 🚀 Résumé des Machines

| **Rôle**                | **Machine 1 (DHCP + DNS)**       | **Machine 2 (FTP + SSH)**         |
|-------------------------|----------------------------------|-----------------------------------|
| **Adresse IP**          | `172.16.1.1` (fixe)              | `172.16.1.102` (DHCP)               |
| **Serveur DHCP**        | ✅                                | ❌                                 |
| **Serveur DNS**         | ✅                                | ❌                                 |
| **Serveur FTP (ProFTPd)** | ❌                                | ✅                                 |
| **Serveur SSH (SFTP)**  | ❌                                | ✅ (port **6500**)                 |

---

✅ **Une fois ces configurations effectuées, tout devrait fonctionner correctement !** 🚀

