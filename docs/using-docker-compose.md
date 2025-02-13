## Using Docker Compose (Utilisation de Docker Compose)
Docker Compose est un outil qui a été développé pour aider à définir et partager des applications multi-conteneurs. Avec Compose, nous pouvons créer un fichier YAML pour définir les services et avec une seule commande, nous pouvons tout lancer ou tout détruire.

Le gros avantage de l'utilisation de Compose est que vous pouvez définir votre pile d'applications dans un fichier, la conserver à la racine de votre dépôt de projet (il est désormais contrôlé par version) et permettre facilement à quelqu'un d'autre de contribuer à votre projet. Quelqu'un n'aurait qu'à cloner votre dépôt et démarrer l'application Compose. 

Alors, comment commencer ?

## Installation de Docker Compose
Si vous avez installé Docker Desktop pour Windows ou Mac, Docker Compose est inclus dans l'installation.  Si vous utilisez une machine Linux avec Docker Engine installer dessus, alors vous avez agalement Docker Compose dessus.
```bash
docker compose version
```

## Creation de notre fichier Compose
1. À la racine du projet d'application, créez un fichier nommé ```docker-compose.yml```.
2. Dans le fichier Compose, nous commencerons par définir la version du schéma. Dans la plupart des cas, il est préférable d'utiliser la dernière version prise en charge. Vous pouvez consulter la référence du fichier Compose pour connaître les versions de schéma actuelles et la matrice de compatibilité.
```bash 
version: "3.7"
*pas neccessaire pour les dernieres version de docker compose*
```
3. Ensuite, nous définirons la liste des services (ou conteneurs) que nous souhaitons exécuter dans le cadre de notre application.
```bash
version: "3.7"
services:
```
Et maintenant, nous allons commencer à migrer un service à la fois dans le fichier de composition.

### Definir le service de notre application 
Pour rappel, c'était la commande que nous utilisions pour définir notre conteneur d'application.
```bash
docker run -dp 3000:3000 \
  -w /app -v $PWD:/app \
  --network net-participant-app-mysql \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=fmntapp \
  -e MYSQL_DB=fmnt_participant_db \
  node:10-alpine \
  sh -c "yarn install && yarn run dev"
```

1. Tout d'abord, définissons l'entrée de service et l'image du conteneur. Nous pouvons choisir n'importe quel nom pour le service. Le nom deviendra automatiquement un alias réseau, ce qui sera utile lors de la définition de notre service MySQL.
```bash
version: "3.7"

services:
  app:
    image:node:10-alpine
```
2. En règle générale, vous verrez la commande à proximité de la définition de l'image, même si aucune exigence de commande n'est imposée. Alors, allons-y et déplaçons cela dans notre fichier.
```bash
version: "3.7"

services:
  app:
    image:node:10-alpine
    command: sh -c "yarn install && yarn run dev"
```
3. Migrons la partie -p 3000:3000 de la commande en définissant les ports pour le service.
```bash
version: "3.7"

services:
  app:
    image:node:10-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
```

4. Ensuite, nous allons migrer à la fois le répertoire de travail ```(-w /app)``` et le mappage de volumes ```(-v $PWD:/app)``` en utilisant les définitions ```working_dir``` et ```volumes```. Les volumes ont également une syntaxe courte et longue.
```bash
version: "3.7"

services:
  app:
    image:node:10-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
```
5. Enfin, nous devons migrer les définitions de variables d’environnement à l’aide de la clé d’environnement.
```bash
version: "3.7"

services:
  app:
    image:node:10-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: fmntapp
      MYSQL_DB: fmnt_participant_db
```

### Definir le service de MySQL
Il est maintenant temps de définir le service MySQL. La commande que nous avons utilisée pour ce conteneur était la suivante :
```bash
docker run -d \
    --network net-participant-app-mysql --network-alias mysql \
    -v participant-app-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=fmntapp \
    -e MYSQL_DATABASE=fmnt_participant_db \
    mysql:5.7
```
1. Nous allons d'abord définir le nouveau service et le nommer ```mysql``` afin qu'il obtienne automatiquement l'alias réseau. Nous allons ensuite spécifier l'image à utiliser également.

```bash
version: "3.7"

services:
  app:
    # The app service definition
  mysql:
    image: mysql:5.7
```
2. Ensuite, nous allons définir le mappage de volume. Lorsque nous avons exécuté le conteneur avec ```docker run```, le volume nommé a été créé automatiquement. Cependant, cela ne se produit pas lors de l'exécution avec Compose. Nous devons définir le volume dans la section ```volumes```: de niveau supérieur, puis spécifier le point de montage dans la configuration du service. En fournissant simplement le nom du volume, les options par défaut sont utilisées. 
```bash
version: "3.7"

services:
  app:
    # The app service definition
  mysql:
    image: mysql:5.7
    volumes:
      - participant-app-mysql-data:/var/lib/mysql


volumes:
  participant-app-mysql-data:
```
3. Enfin, il ne nous reste plus qu’à spécifier les variables d’environnement.
```bash
version: "3.7"

services:
  app:
    # The app service definition
  mysql:
    image: mysql:5.7
    volumes:
      - participant-app-mysql-data:/var/lib/mysql
    environment: 
      MYSQL_ROOT_PASSWORD: fmntapp
      MYSQL_DATABASE: fmnt_participant_db

volumes:
  participant-app-mysql-data:
```

À ce stade, notre docker-compose.yml complet devrait ressembler à ceci :

```bash
version: "3.7"

services:
  app:
    image:node:10-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: fmntapp
      MYSQL_DB: fmnt_participant_db
  
  mysql:
    image: mysql:5.7
    volumes:
      - participant-app-mysql-data:/var/lib/mysql
    environment: 
      MYSQL_ROOT_PASSWORD: fmntapp
      MYSQL_DATABASE: fmnt_participant_db

volumes:
  participant-app-mysql-data:
```
### Exécution de notre stack d'applications
Maintenant que nous avons notre fichier docker-compose.yml, nous pouvons le démarrer !

1. Assurez-vous qu'aucune autre copie de l'application/db n'est en cours d'exécution en premier (```docker ps``` et ```docker rm -f <ids>```).
2. Démarrez la stack d'applications à l'aide de la commande ```docker-compose up```. Nous ajouterons l'indicateur ```-d``` pour tout exécuter en arrière-plan.
```bash
docker compose up -d
```

Lorsque nous exécutons ceci, nous devrions voir un résultat comme celui-ci :
```bash
Creating network "app_default" with the default driver
Creating volume "app_todo-mysql-data" with default driver
Creating app_app_1   ... done
Creating app_mysql_1 ... done
```

Vous remarquerez que le volume a été créé ainsi qu'un réseau ! Par défaut, Docker Compose crée automatiquement un réseau spécifiquement pour la stack d'applications (c'est pourquoi nous n'en avons pas défini un dans le fichier de composition).

3. Examinons les journaux à l'aide de la commande ```docker-compose logs -f```. Vous verrez les journaux de chacun des services entrelacés dans un seul flux. Cela est incroyablement utile lorsque vous souhaitez surveiller les problèmes liés au timing. L'indicateur ```-f``` « suit » le journal, vous donnera donc une sortie en direct au fur et à mesure de sa génération.
```bash
mysql_1  | 2019-10-03T03:07:16.083639Z 0 [Note] mysqld: ready for connections.
mysql_1  | Version: '5.7.27'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
app_1    | Connected to mysql db at host mysql
app_1    | Listening on port 3000
```

Le nom du service est affiché au début de la ligne (souvent en couleur) pour aider à distinguer les messages. Si vous souhaitez afficher les journaux d'un service spécifique, vous pouvez ajouter le nom du service à la fin de la commande logs (par exemple, ```docker-compose logs -f app```).

4. À ce stade, vous devriez pouvoir ouvrir votre application et la voir s'exécuter. Et hé ! Nous n'avons plus qu'une seule commande !

#### Tout démolir (Tearing it All Down)
Lorsque vous êtes prêt à tout démonter, exécutez simplement ```docker-compose down```. Les conteneurs s'arrêteront et le réseau sera supprimé.

Une fois démonté, vous pouvez passer à un autre projet, exécuter ```docker-compose up``` et être prêt à contribuer à ce projet ! Il n'y a vraiment rien de plus simple que cela !

# Recap 
Dans cette section, nous avons découvert Docker Compose et comment il simplifie considérablement la définition et le partage d'applications multiservices. Nous avons créé un fichier Compose en traduisant les commandes que nous utilisions dans le format de composition approprié.

À ce stade, nous commençons à conclure le Lab. Cependant, nous souhaitons aborder quelques bonnes pratiques concernant la création d'images, car il existe un gros problème avec le Dockerfile que nous avons utilisé. Alors, jetons un œil !

Section suivante [Image Building Best Practices](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101/blob/main/docs/best-practices.md)