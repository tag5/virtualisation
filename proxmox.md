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
![proxmox](img/proxmox/20.png)

- Captures d'écran pour le noeud2 (pour info)
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
![proxmox](img/proxmox/28.png)
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

