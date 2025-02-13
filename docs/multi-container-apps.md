
# Multi-Container Apps
Jusqu'à présent, nous avons travaillé avec des applications à conteneur unique. Mais nous souhaitons maintenant ajouter MySQL à la pile d'applications. La question suivante se pose souvent : « Où MySQL s'exécutera-t-il ? L'installer dans le même conteneur ou l'exécuter séparément ? » En général, chaque conteneur doit faire une chose et le faire bien. Plusieurs raisons :

* Il y a de fortes chances que vous deviez mettre à l'échelle les API et les interfaces frontales différemment des bases de données.

* Des conteneurs distincts vous permettent de gérer les versions et de mettre à jour les versions de manière isolée.

* Bien que vous puissiez utiliser un conteneur pour la base de données localement, vous souhaiterez peut-être utiliser un service géré pour la base de données en production. Vous ne souhaitez alors pas expédier votre moteur de base de données avec votre application.

* L'exécution de plusieurs processus nécessitera un gestionnaire de processus (le conteneur ne démarre qu'un seul processus), ce qui ajoute de la complexité au démarrage/à l'arrêt du conteneur.

Et il y a d'autres raisons. Nous allons donc mettre à jour notre application pour qu'elle fonctionne comme ceci :

[Schema Application et MySQL](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git/images/docs/app-mysql.png "Schema Application et MySQL")


## Mise en réseau de conteneurs
N'oubliez pas que les conteneurs, par défaut, fonctionnent de manière isolée et ne savent rien des autres processus ou conteneurs sur la même machine. Alors, comment permettre à un conteneur de communiquer avec un autre ? La réponse est la mise en réseau. Vous n'avez pas besoin d'être un ingénieur réseau (hourra !). Souvenez-vous simplement de cette règle...

> Si deux conteneurs sont sur le même réseau, ils peuvent communiquer entre eux. S'ils ne le sont pas, ils ne le peuvent pas.

## Démarrage de MySQL
Il existe deux façons de mettre un conteneur sur un réseau : 1) l'attribuer au démarrage ou 2) connecter un conteneur existant. Pour l'instant, nous allons d'abord créer le réseau et attacher le conteneur MySQL au démarrage.

1. Creer le reseau
```bash 
docker network create net-participant-app-mysql
```
2. Démarrez un conteneur MySQL et connectez-le au réseau. Nous allons également définir quelques variables d'environnement que la base de données utilisera pour initialiser la base de données (voir la section « Variables d'environnement » dans la liste MySQL Docker Hub).
```bash
docker run -d \
    --network net-participant-app-mysql --network-alias appmysql \
    -v participant-app-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=fmntapp \
    -e MYSQL_DATABASE=fmnt_participant_db \
    mysql:5.7
```
3. Pour confirmer que la base de données est opérationnelle, connectez-vous à la base de données et vérifiez qu'elle se connecte.
```bash
docker exec -it <mysql-container-id> mysql -p
```
Lorsque l'invite de mot de passe apparaît, saisissez ```fmntapp```. Dans le shell MySQL, répertoriez les bases de données et vérifiez que vous voyez la base de données ```fmnt_participant_db```.
```bash 
mysql> SHOW DATABASES;
```
Vous devriez voir un résultat qui ressemble à ceci :
```bash 
+--------------------+
| Database           |
+--------------------+
| fmnt_participant_db|
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```
Hourra ! Nous avons notre base de données des participants et elle est prête à être utilisée !

## Connexion de notre application avec MySQL
Notre application prend en charge la définition de quelques variables d'environnement pour spécifier les paramètres de connexion MySQL. Il s'agit de :
- ```MYSQL_HOST``` - le nom d'hôte du serveur MySQL en cours d'exécution
- ```MYSQL_USER``` - le nom d'utilisateur à utiliser pour la connexion
- ```MYSQL_PASSWORD``` - le mot de passe à utiliser pour la connexion
- ```MYSQL_DB``` - la base de données à utiliser une fois connectée

Avec tout cela expliqué, commençons notre conteneur prêt pour le développement !

1. Nous spécifierons chacune des variables d’environnement ci-dessus et connecterons le conteneur à notre réseau d’applications.
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

2. Si nous regardons les journaux du conteneur (docker logs <container-id>), nous devrions voir un message indiquant qu'il utilise la base de données mysql.
```bash
# Previous log messages omitted
$ nodemon src/index.js
[nodemon] 1.19.2
[nodemon] to restart at any time, enter `rs`
[nodemon] watching dir(s): *.*
[nodemon] starting `node src/index.js`
Connected to mysql db at host mysql
Listening on port 3000
```

3. Ouvrez l’application dans votre navigateur et ajoutez quelques éléments à votre liste de participant.
4. Connectez-vous à la base de données ```mysql``` et prouvez que les éléments sont en cours d'écriture dans la base de données. N'oubliez pas que le mot de passe est ```fmntapp```.
```bash
docker exec -it <mysql-container-id> mysql -p fmnt_participant_db
```
Et dans le shell mysql, exécutez ce qui suit :
```bash
mysql> select * from todo_items;
<<faire une sortie ici>>
```
Bien sûr, votre table sera différente car elle contient vos participants. Mais vous devriez les voir rangés là !

# Recap 
À ce stade, nous avons une application qui stocke maintenant ses données dans une base de données externe exécutée dans un conteneur séparé. Nous avons appris un peu sur la mise en réseau des conteneurs et vu comment la découverte de services peut être effectuée à l'aide de DNS.

Mais il y a de fortes chances que vous commenciez à vous sentir un peu dépassé par tout ce que vous devez faire pour démarrer cette application. Nous devons créer un réseau, démarrer des conteneurs, spécifier toutes les variables d'environnement, exposer les ports, et plus encore ! C'est beaucoup de choses à retenir et cela rend certainement les choses plus difficiles à transmettre à quelqu'un d'autre.

Dans la section suivante, nous parlerons de Docker Compose. Avec Docker Compose, nous pouvons partager nos piles d'applications de manière beaucoup plus simple et permettre aux autres de les lancer avec une seule (et simple) commande !
#### Section suivante : [Using Docker Compose](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git/docs/using-docker-compose.md)