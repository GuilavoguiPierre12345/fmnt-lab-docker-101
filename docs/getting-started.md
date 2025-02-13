
## Getting Started (Commencer)
## Installation

Suivez les instructions officielles pour installer Docker sur votre système d'exploitation [docs.docker.com](https://docs.docker.com/). 

Pour cet exemple nous allons utiliser ubuntu [installation-ubuntu](https://docs.docker.com/engine/install/ubuntu/)

## Demarrer notre application
Executez cette commande dans votre terminal
```bash
docker run --name lab-101 -d -p 8080:80 yaboigui/fmnt-docker-lab-101
```
- -d : exécuter le conteneur en mode **détaché (en arrière-plan)**
- -p 8080:80 : mapper le port **8080** de l'hôte au port **80** du conteneur
- --name : le nom du container
- yaboigui/fmnt-docker-lab-101 : le nom de l'image a utiliser 


#### Notre Application
![Application interface](https://blog.codewithdan.com/wp-content/uploads/2023/06/Docker-Logo.png "Application interface")

#### Obtenir l'application sur votre environnement 
1. Faire un clone du repository github
```bash 
git clone https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git
```
2. Modifiez votre répertoire de travail actuel dans le nouveau dossier « app ».
```bash 
cd app/
```
3. Dans ce répertoire, vous devriez voir une application simple basée sur Node.
```bash
ls
```
## Création de l'image conteneur de l'application
Pour créer l'image de l'application, nous devons utiliser un Dockerfile. Un Dockerfile est simplement un script textuel d'instructions utilisé pour créer une image de conteneur.

1. Créez un fichier nommé **Dockerfile** avec le contenu suivant.
```bash
FROM node:10-alpine
WORKDIR /app
COPY . .
RUN npm install --production
CMD ["node","/app/src/index.js"]
```
2. Créez l’image du conteneur à l’aide de la commande docker build.
```bash
docker build -t lab-docker-101 .
```
## Démarrage d’un conteneur d’applications
1. Démarrez votre conteneur à l’aide de la commande docker run :
```bash
docker run -dp 3000:3000 lab-docker-101
```
2. Ouvrir un navigateur et taper ``` https://ip-public:3000 ```
3. Tester l'application en s'inscrivant a une formation

# Recap
Dans cette courte section, nous avons appris les bases de la création d'une image de conteneur et créé un Dockerfile pour le faire. Une fois que nous avons créé une image, nous avons démarré le conteneur et vu l'application en cours d'exécution !

Nous allons ensuite apporter une modification à notre application et apprendre à mettre à jour notre application en cours d'exécution avec une nouvelle image. En cours de route, nous apprendrons quelques autres commandes utiles.

[Updatting Our App](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git/docs/updatting-our-app.md)