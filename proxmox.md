# Déploiement de 3 noeuds

Téléchargement de l'ISO de proxmox VE: https://proxmox.com/en/downloads
![proxmox](img/proxmox/1.png)

Création d'une VM pour le premier noeud:
![proxmox](img/proxmox/2.png)

- 16 Go de RAM
- Deux disques de 20Go
- Cocher Virtualize Intel VT-x/EPT or AMD-V/RVI (Rubrique Processors)
- Réseau NAT
![proxmox](img/proxmox/3.png)
![proxmox](img/proxmox/4.png)
![proxmox](img/proxmox/5.png)
![proxmox](img/proxmox/6.png)
![proxmox](img/proxmox/7.png)
![proxmox](img/proxmox/8.png)
![proxmox](img/proxmox/9.png)
![proxmox](img/proxmox/10.png)
![proxmox](img/proxmox/11.png)

- Installation graphique:
![proxmox](img/proxmox/12.png)
![proxmox](img/proxmox/13.png)
![proxmox](img/proxmox/14.png)
![proxmox](img/proxmox/15.png)
![proxmox](img/proxmox/16.png)

- Hostname/IP différents pour chaque noeud:
![proxmox](img/proxmox/17.png)
![proxmox](img/proxmox/18.png)
![proxmox](img/proxmox/19.png)

- Captures d'écran pour le noeud2 (pour info):
![proxmox](img/proxmox/21.png)
![proxmox](img/proxmox/22.png)

- Se connecter en ssh sur chaque noeud:
![proxmox](img/proxmox/23.png)

- Completer /etc/hosts sur chaque noeud:
  Chaque noeud doit connaitre les adresses IP des 3 noeuds.
  Attention: Il manque le noeud3 ci dessous.
![proxmox](img/proxmox/24.png)
![proxmox](img/proxmox/25.png)

- Essayer de ping les noeuds entre eux (via leur ip et via leur nom):
![proxmox](img/proxmox/26.png)

# Configuration du cluster

- Interface web de gestion de l'un des noeuds:
  Rubrique Datacenter > Options > HTTP proxy: renseigner le proxy de l'IUT
![proxmox](img/proxmox/27.png)

- Sur l'un des 3 noeuds uniquement: Datacenter > Cluster: Bouton "Create Cluster":
![proxmox](img/proxmox/29.png)
![proxmox](img/proxmox/30.png)

- Sur ce même noeud: Bouton "Cluster Join Information":
  (copier le bloc de données)
![proxmox](img/proxmox/31.png)

- Sur les deux autres noeuds: Bouton "Join Cluster":
  Coller le bloc de données, et saisir le mot de passe root
![proxmox](img/proxmox/32.png)

- Voici ce qui doit s'afficher lorsque le cluster comporte 2 noeuds:
![proxmox](img/proxmox/33.png)

- S'assurer que le cluster comporte bien 3 noeuds

# Mise en place d'un système de stockage distribué: Ceph

- Opérations à effectuer sur chaque noeud (console ssh root):
```
export http_proxy=http://cache.univ-st-etienne.fr:3128
pveceph install --repository no-subscription
```

- Sur un seul noeud:
```
pveceph init --network 192.168.220.0/24
```

- Sur chaque noeud: (note: /dev/sdb représente le second disque de 20Go)
```
pveceph mon create
pveceph mgr create
pveceph osd create /dev/sdb
```

- Sur un seul noeud: (Vérification)
```
ceph -s
```

- Sur un seul noeud:
```
ceph osd pool create pool_stockage_ceph 64
ceph osd pool set pool_stockage_ceph size 2
rbd pool init pool_stockage_ceph
pvesm add rbd ceph-rbd --pool pool_stockage_ceph --content images,rootdir
```

- Sur chaque noeud:
```
systemctl enable pve-ha-lrm pve-ha-crm --now
```

# Création d'une VM HA sur l'un des noeuds
- Téléchargement d'une image iso debian netinst

- Interface web de gestion de l'un des 3 noeuds:
  - Upload de cette image dans la rubrique "ISO Images"
  - Bouton "Create VM" (en haut)
  - Sur le premier écran: Cocher "HA"
  - Disk: Sélectionner le pool de stockage ceph: ceph-rbd
  - Installer la distribution debian sur cette VM

- Stopper brutalement le noeud qui héberge cette VM

