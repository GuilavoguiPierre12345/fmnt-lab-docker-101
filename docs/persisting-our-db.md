
# Persisting Our DB (Persistance de notre DB)

Au cas où vous ne l'auriez pas remarqué, notre liste des participants est effacée à chaque lancement du conteneur. Pourquoi ? Voyons comment fonctionne le conteneur.

## Le système de fichiers du conteneur
Lorsqu'un conteneur s'exécute, il utilise les différentes couches d'une image pour son système de fichiers. Chaque conteneur dispose également de son propre « espace de travail » pour créer/mettre à jour/supprimer des fichiers. Les modifications ne seront pas visibles dans un autre conteneur, même s'ils utilisent la même image.
## Voir cela en pratique
Pour voir cela en action, nous allons démarrer deux conteneurs et créer un fichier dans chacun. Vous verrez que les fichiers créés dans un conteneur ne sont pas disponibles dans un autre.

1. Démarrez un conteneur ```ubuntu``` qui créera un fichier nommé ```/data.txt``` avec un nombre aléatoire compris entre 1 et 10000.
```bash 
docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```
2. Nous pouvons voir la sortie en exécutant le conteneur. Pour ce faire, vous devez obtenir l'ID du conteneur (utilisez ```docker ps``` pour l'obtenir).
```bash
docker exec <container-id> cat /data.txt
```
Vous devriez voir un nombre aléatoire !

3. Maintenant, démarrons un autre conteneur ```ubuntu``` (la même image) et nous verrons que nous n'avons pas le même fichier.
```bash
docker run -it ubuntu ls /
```
4. Allez-y et supprimez le premier conteneur à l’aide de la commande ```docker rm -f```.

## Volumes des conteneurs
Avec l'expérience précédente, nous avons vu que chaque conteneur est en réalité en lecture seule. Bien que les conteneurs puissent créer, mettre à jour et supprimer des fichiers, ces modifications sont perdues lorsque le conteneur est supprimé et sont isolées dans ce conteneur. Avec les volumes, nous pouvons changer tout cela.

Les volumes permettent de connecter des chemins de système de fichiers spécifiques du conteneur à la machine hôte. Si un répertoire du conteneur est monté, les modifications apportées à ce répertoire sont également visibles sur la machine hôte. Si nous montons ce même répertoire lors des redémarrages du conteneur, nous verrons les mêmes fichiers.

Il existe deux principaux types de volumes. Nous utiliserons éventuellement les deux, mais nous commencerons par les volumes nommés.

### Conservation de la liste de nos participants
Par défaut, notre application stocke ses données dans une base de données SQLite dans /etc/participants/participant.db. Si vous n'êtes pas familier avec SQLite, ne vous inquiétez pas ! Il s'agit simplement d'une base de données relationnelle dans laquelle toutes les données sont stockées dans un seul fichier. Bien que ce ne soit pas la meilleure solution pour les applications à grande échelle, cela fonctionne pour les petites démonstrations. Nous parlerons plus tard de la possibilité de passer à un véritable moteur de base de données.

La base de données étant un fichier unique, si nous pouvons conserver ce fichier sur l'hôte et le rendre disponible pour le conteneur suivant, il devrait pouvoir reprendre là où le dernier s'est arrêté. En créant un volume et en l'attachant (souvent appelé « montage ») au répertoire dans lequel les données sont stockées, nous pouvons conserver les données. Au fur et à mesure que notre conteneur écrit dans le fichier participant.db, il sera conservé sur l'hôte dans le volume.

Comme mentionné, nous allons utiliser un volume nommé. Considérez un volume nommé comme un simple compartiment de données. Docker conserve l'emplacement physique sur le disque et vous n'avez besoin de mémoriser que le nom du volume. Chaque fois que vous utilisez le volume, Docker s'assurera que les données correctes sont fournies.

1. Créez un volume à l’aide de la commande ```docker volume create```.
```bash
docker volume create participant-db
```
2. Démarrez le conteneur **lab-docker-101**, mais ajoutez l'indicateur **-v** pour spécifier un montage de volume. Nous utiliserons le volume nommé et le monterons sur ```/etc/participants```, ce qui capturera tous les fichiers créés sur le chemin.

3. Une fois le conteneur démarré, ouvrez l’application et ajoutez quelques participants à votre liste.

[Liste des participants](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git/images/docs/participants-list.png "Liste des participants")

4. Supprimez le conteneur de l'application. Utilisez ```docker ps``` pour obtenir l'ID, puis ```docker rm -f <id>``` pour le supprimer.

5. Démarrez un nouveau conteneur en utilisant la même commande ci-dessus.

6. Ouvrez l'application. Vous devriez voir vos participants toujours dans votre liste !

7. N'hésitez pas à retirer le conteneur lorsque vous avez terminé de vérifier votre liste.

>Hourra ! Vous savez maintenant comment conserver les données !

### Plongée dans notre volume
De nombreuses personnes demandent souvent « Où Docker stocke-t-il réellement mes données lorsque j'utilise un volume nommé ? » Si vous voulez le savoir, vous pouvez utiliser la commande ```docker volume inspect```.
```bash
docker volume inspect participant-db
[
    {
        "CreatedAt": "2019-09-26T02:18:36Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/participant-db/_data",
        "Name": "participant-db",
        "Options": {},
        "Scope": "local"
    }
]
```
Le point de montage est l'emplacement réel sur le disque où les données sont stockées. Notez que sur la plupart des machines, vous aurez besoin d'un accès root pour accéder à ce répertoire à partir de l'hôte. Mais c'est là qu'il se trouve !

# Recap
À ce stade, nous disposons d'une application fonctionnelle qui peut survivre aux redémarrages ! Nous pouvons la montrer à nos investisseurs et espérer qu'ils pourront comprendre notre vision !

Cependant, nous avons vu plus tôt que la reconstruction des images pour chaque changement prend un certain temps. Il doit y avoir une meilleure façon d'effectuer des modifications, n'est-ce pas ? Avec les montages liés (que nous avons évoqués plus tôt), il existe une meilleure façon ! Voyons cela de plus près maintenant !


#### Section suivante : [Using Bind Mounts](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101/blob/main/docs/using-bind-mounts.md)