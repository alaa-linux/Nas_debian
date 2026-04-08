# 📦 README – Serveur NAS Debian avec RAID 5 + Services Réseau

## 📌 Description du projet

Ce projet consiste à déployer un serveur NAS (Network Attached Storage) sous Debian, permettant le stockage, le partage et la sécurisation des données sur un réseau local.

L’architecture mise en place inclut :

- un stockage sécurisé avec RAID 5
- un partage de fichiers avec Samba
- un accès sécurisé via SFTP
- une interface cloud avec Nextcloud
- une administration web avec Webmin
- une sauvegarde automatique avec rsync + cron

👉 L’objectif est de créer un serveur complet, multi-utilisateurs, sécurisé et automatisé.

---

## 🎯 Objectifs

- Mettre en place un NAS sous Debian sans interface graphique
- Créer un stockage redondant (RAID 5)
- Permettre l’accès multi-utilisateurs
- Partager les fichiers sur le réseau (Samba)
- Sécuriser les accès (SFTP)
- Mettre en place un cloud privé (Nextcloud)
- Automatiser les sauvegardes (rsync + cron)
- Superviser via Webmin
- Tester et documenter toute l’infrastructure

---

## 🏗️ Architecture globale

Clients (Windows / Linux / Mobile)
        ↓
   Samba / SFTP / Nextcloud
        ↓
   Serveur Debian NAS
        ↓
   RAID 5 (mdadm)
        ↓
   Disques physiques

---

## 💾 Mise en place du RAID 5

Le RAID 5 permet :

- la tolérance de panne (1 disque)
- une répartition des données
- une optimisation de stockage

### Installation

sudo apt update
sudo apt install mdadm

### Création du RAID

sudo mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/sdb /dev/sdc /dev/sdd

### Vérification

cat /proc/mdstat

### Formatage

sudo mkfs.ext4 /dev/md0

### Montage

sudo mkdir /home/alaa/raid5
sudo mount /dev/md0 /home/alaa/raid5

### Persistance

sudo mdadm --detail --scan >> /etc/mdadm/mdadm.conf

👉 Ce RAID constitue le cœur du stockage du NAS

---

## 📂 Partage avec Samba

### Installation

sudo apt install samba

### Configuration

[alaaraid5]
path = /home/alaa/raid5
public = no
writable = yes

👉 On voit le partage [alaaraid5] pointant vers le RAID

### Permissions

sudo chmod -R 775 /home/alaa/raid5
sudo chown -R alaa:alaa /home/alaa/raid5

### Redémarrage

sudo service smbd restart

### Test client Linux

smbclient //192.168.1.71/alaaraid5 -U alaa

👉 Résultat : accès au stockage RAID via réseau

---

## 🔐 Accès sécurisé avec SFTP

### Installation

sudo apt install openssh-server

### Configuration

sudo nano /etc/ssh/sshd_config

Ajouter :

Subsystem sftp internal-sftp

Match User sftp_user
 ChrootDirectory /home/alaa/raid5
 ForceCommand internal-sftp
 AllowTcpForwarding no

👉 L’utilisateur est bloqué dans le RAID (sécurité maximale)

---

## ☁️ Mise en place de Nextcloud

### Installation stack web

sudo apt install apache2 mariadb-server php

### Création base de données

CREATE DATABASE nextcloud;
CREATE USER 'alaacloud'@'localhost' IDENTIFIED BY 'alaa';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'alaacloud'@'localhost';

### Installation Nextcloud

wget https://download.nextcloud.com/server/releases/nextcloud.tar.bz2
tar -xvjf nextcloud.tar.bz2
sudo mv nextcloud /var/www/

### Configuration stockage RAID

'datadirectory' => '/home/alaa/raid5/',

👉 Nextcloud permet :
- accès web
- cloud privé
- gestion fichiers

---

## 🔄 Sauvegarde automatique avec rsync

### Synchronisation

rsync -avz --exclude=lost+found /home/alaa/raid5/ alaa@192.168.1.159:/home/alaa/raid5backup

### Automatisation avec cron

* * * * * rsync -avz --exclude=lost+found /home/alaa/raid5/ alaa@192.168.1.159:/home/alaa/raid5backup

👉 Sauvegarde toutes les minutes

---

## 🔑 SSH sans mot de passe

ssh-keygen -t rsa
ssh-copy-id alaa@192.168.1.159

👉 Permet à rsync de fonctionner sans interaction

---

## 🖥️ Administration avec Webmin

### Installation

wget http://prdownloads.sourceforge.net/webadmin/webmin.deb
sudo dpkg -i webmin.deb

### Accès

https://192.168.1.71:10000

👉 Permet :
- gestion utilisateurs
- monitoring
- configuration services

---

## 👥 Gestion des utilisateurs

Chaque utilisateur possède :

- un dossier personnel
- un accès Samba
- un accès SFTP
- un accès Nextcloud

👉 Multi-session = plusieurs utilisateurs simultanés

---

## 🧪 Tests réalisés

- RAID actif ✅
- Montage RAID ✅
- Samba accessible ✅
- SFTP sécurisé ✅
- Nextcloud fonctionnel ✅
- Sauvegarde rsync ✅
- Cron automatique ✅

---

## 🔐 Sécurité mise en place

- SFTP (chroot)
- Permissions Linux
- RAID (tolérance panne)
- SSH sécurisé
- Isolation utilisateurs
- accès Samba restreint

---

## 📈 Améliorations possibles

- TLS / HTTPS pour Nextcloud
- Fail2ban
- Firewall (ufw)
- LDAP / Active Directory
- Dockerisation
- Monitoring Prometheus/Grafana

---

## 📌 Compétences développées

- Administration Linux
- Stockage RAID
- Réseaux
- Sécurité
- Automatisation
- Cloud privé
- Gestion utilisateurs

---

## 👨‍💻 Auteur

Alaa Kaid Ahmed
