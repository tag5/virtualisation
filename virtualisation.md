# 1. Commandes Unix à connaitre
- pwd
- ls
- cd
- rm
- mkdir
- rmdir
- rm -rf
- mv
- cp
- touch
- cat
- whoami
- adduser
- usermod -aG [groupe] [utilisateur]
- su
- sudo
- chmod
- ip addr / ifconfig
- gcc
- apt-get update
- apt-get install [paquet]
- systemctl

# 2. Introduction virtualisation

![virtualisation](img/virtualisation.png)

# 3. VMWare workstation (hyperviseur de type 2)

## Téléchargement iso Ubuntu
![vmware](img/vmware_workstation_1.png)
![vmware](img/vmware_workstation_2.png)

## Démarrer VMWare Workstation
![vmware](img/vmware_workstation_3.png)

![vmware](img/vmware_workstation_4.png)

![vmware](img/vmware_workstation_5.png)

![vmware](img/vmware_workstation_6.png)

## Créer une nouvelle machine virtuelle
![vmware](img/vmware_workstation_7.png)

Sélectionner l'iso téléchargée:

![vmware](img/vmware_workstation_8.png)

Etape inutile:

![vmware](img/vmware_workstation_9.png)

![vmware](img/vmware_workstation_10.png)

![vmware](img/vmware_workstation_11.png)

![vmware](img/vmware_workstation_12.png)

![vmware](img/vmware_workstation_13.png)

![vmware](img/vmware_workstation_14.png)

![vmware](img/vmware_workstation_15.png)

![vmware](img/vmware_workstation_16.png)

## Premier démarrage de la machine virtuelle:
![vmware](img/vmware_workstation_17.png)

## Réseau
![vmware](img/vmware_workstation_18.png)

## Démarrer un terminal
![vmware](img/vmware_workstation_19.png)

## Mise en place serveur ssh
![vmware](img/vmware_workstation_20.png)

```
export http_proxy=http://cache.univ-st-etienne.fr:3128
sudo -E apt-get update
sudo -E apt-get install openssh-server
```

## Connexion ssh depuis la machine hote
![vmware](img/vmware_workstation_21.png)


# 4. Introduction à Docker

## Recap

### Installation de docker
```
sudo apt-get install docker.io
sudo usermod -aG docker [utilisateur]
```

### Commandes principales

```
docker pull [nomimage]
docker images

docker run [nomimage]
  -d
  -v /cheminhote:/chemincontainer
  -p 8080:80     (porthote:portcontainer)
  -it
  [commande]

docker exec -it [idcontainer] [commande]

docker ps
docker ps -a
docker stop [idcontainer]
docker rm [idcontainer]

docker cp [fichierhote] [idcontainer]:[fichiercontainer]

docker logs [idcontainer]
```

### Creation d'une image docker

Dockerfile:
```
FROM ...
COPY ... ...
CMD ["..."]
```

```
docker build -t [nomimage] .
docker build -t [nomimage]:1.0.0 .
docker tag [nomimage]:3.0.0 [nomimage]:latest
```

### docker compose
Voir projet TODO

## Rappel pour les manipulations à l'IUT
- Il n'est malheureusement pas possible d'utiliser docker sur vos PC.
- Connectez vous sur le site https://labs.play-with-docker.com/
- Une machine virtuelle est à votre disposition en cliquant sur "Add new instance".
- Vous pouvez utiliser docker sur cette machine virtuelle.

![playwithdocker](img/playwithdocker.png)

## Exercice 1

- Déployez un container docker sur la machine virtuelle:
  - Image: nginx (Serveur web)
  - Mode détaché.
  - Redirection du port tcp 80 de la VM hôte vers le port tcp 80 du container.
  - Le contenu du dossier /usr/share/nginx/html/ du container devra être "lié" au dossier /root/volume1/ de la machine virtuelle hôte.

- Créez une page index.html dans /root/volume1/ sur votre machine virtuelle hôte.
- Accédez à cette page depuis votre navigateur web.

![archi](img/archi.png)

Rappel: Une seconde redirection de port est requise au niveau de la machine virtuelle pour que le port 80 soit accessible depuis l'extérieur.

![redirection](img/redirection.png)

<details>
  <summary>💡 Voir une solution</summary>

- Créez un dossier volume1.
- Créez un fichier volume1/index.html
```sh
docker run -d -v ./volume1:/usr/share/nginx/html/ -p 80:80 nginx
```
</details>

## Exercice 2
- Vous avez réussi à mettre en place un serveur web et à héberger une page html simple.
- On souhaite désormais héberger une version html du jeu vidéo tetris.
- Le code source est disponible à cette adresse: https://github.com/bsord/tetris
- L’objectif est de pouvoir jouer à tetris depuis votre navigateur.
- Construisez votre propre image Docker prête à l’emploi, qui intègre le serveur web nginx, ainsi que ce jeu vidéo.

<details>
  <summary>💡 Voir une solution</summary>

```sh
git clone https://github.com/bsord/tetris
docker run -d -p 80:80 -v ./tetris:/usr/share/nginx/html/ nginx
```
</details>

## Exercice 3
- Ecrivez un programme en C qui affiche un message "Hello", suivi d’un numéro de version.
- Créez une image Docker minimaliste qui intègre le binaire de ce programme.
- Lorsque l’on voudra créer et lancer un container basé sur cette image, le programme devra être exécuté automatiquement.
- Ecrivez successivement différentes versions de ce programme en changeant simplement le numéro. Imaginez qu’il s’agit en réalité d’un programme plus complexe et que chaque nouvelle version est le fruit de plusieurs semaines de travail.
- Créez plusieurs images Docker qui embarquent les différentes versions de ce programme. (Utilisez les tags). Faites en sorte que le tag "latest" désigne systématiquement la dernière version disponible.

<details>
  <summary>💡 Voir une solution</summary>

- test.c:
  ```c
  #include <stdio.h>

  int main() {
    printf("Hello\n");
  }
  ```

- Dockerfile:
  ```Dockerfile
  FROM alpine
  COPY a.out /a.out
  CMD ["/a.out"]
  ```

- Compilation et construction d'une image:
  ```sh
  gcc test.c
  docker build -t monimage:1.0.0 .
  docker tag monimage:1.0.0 monimage:latest
  ```

- Version 2:
  Modifier test.c
  ```sh
  gcc test.c
  docker build -t monimage:2.0.0 .
  docker tag monimage:2.0.0 monimage:latest
  ``` 

- Test:
  ```sh
  docker run monimage:1.0.0
  docker run monimage:2.0.0
  docker run monimage
  ``` 
</details>

## Exercice 4
- Cette image Docker est dépourvue de documentation: https://hub.docker.com/r/julien237/imagetestgeii
- Analysez là et décrivez ce qu’elle contient.

```
docker run -it julien237/imagetestgeii:latest
```

<details>
  <summary>💡 Voir une solution</summary>

- Depuis une autre console, obtenez un shell sur ce container.
- Inspectez les fichiers qui se trouvent à la racine...
</details>

## Exercice 5
- Déployez un container docker qui héberge une base de données postgresql.
- Créez une base de données comportant une simple table: clients (champs id_client et nom)
- Créez un programme (dans le language de votre choix) qui permet de consulter la liste des clients et de créer un nouveau client. (Ce programme peut être une application console ou une application web) 
- Embarquez ce programme dans une image Docker.
- Trouvez une solution pour déployer les deux containers de la manière la plus simple possible (base de données et programme) 

<details>
  <summary>💡 Voir une solution</summary>

- Documentez vous sur `docker compose`
- Inspirez vous du projet https://github.com/tag5/todo/
</details>

# 5. VMWare ESXi (hyperviseur de type 1)

## Mise en route

- https://labs.hol.vmware.com/HOL/catalog
- Rechercher le laboratoire "virtualization 101"

![esxi](img/esxi1.png)

![esxi](img/esxi2.png)

![esxi](img/esxi3.png)

![esxi](img/esxi4.png)

![esxi](img/esxi5.png)

## Création d'une VM

![esxi](img/esxi6.png)

![esxi](img/esxi7.png)

![esxi](img/esxi8.png)

![esxi](img/esxi9.png)

![esxi](img/esxi10.png)

![esxi](img/esxi11.png)

![esxi](img/esxi12.png)

![esxi](img/esxi13.png)

![esxi](img/esxi14.png)

![esxi](img/esxi15.png)

![esxi](img/esxi16.png)

![esxi](img/esxi17.png)

![esxi](img/esxi18.png)

![esxi](img/esxi19.png)

![esxi](img/esxi20.png)

![esxi](img/esxi21.png)

![esxi](img/esxi22.png)

![esxi](img/esxi23.png)

![esxi](img/esxi24.png)

![esxi](img/esxi25.png)

![esxi](img/esxi26.png)

![esxi](img/esxi27.png)

## vSphere Availability (HA)

- Suppression des VMs inutiles
- Créer une VM: vmtest
- Associer cette VM à l'hôte esx-01a

![esxi](img/esxi28.png)

![esxi](img/esxi29.png)

![esxi](img/esxi30.png)

![esxi](img/esxi31.png)

![esxi](img/esxi32.png)

![esxi](img/esxi33.png)

![esxi](img/esxi34.png)

![esxi](img/esxi35.png)

- Arret de l'hôte qui héberge la VM:

![esxi](img/esxi36.png)

- La VM est de nouveau disponible sur le second hôte:

![esxi](img/esxi37.png)

- Le fichier est toujours présent:

![esxi](img/esxi38.png)
