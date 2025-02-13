
# Using Bind Mounts

Dans le chapitre précédent, nous avons parlé et utilisé un volume nommé pour conserver les données dans notre base de données. Les volumes nommés sont parfaits si nous voulons simplement stocker des données, car nous n'avons pas à nous soucier de l'endroit où les données sont stockées.

Avec les montages de liaison, nous contrôlons le point de montage exact sur l'hôte. Nous pouvons l'utiliser pour conserver les données, mais il est souvent utilisé pour fournir des données supplémentaires dans des conteneurs. Lorsque nous travaillons sur une application, nous pouvons utiliser un montage de liaison pour monter notre code source dans le conteneur afin de lui permettre de voir les modifications de code, de répondre et de nous permettre de voir les modifications immédiatement.

Pour les applications basées sur Node, nodemon est un excellent outil pour surveiller les modifications de fichiers, puis redémarrer l'application. Il existe des outils équivalents dans la plupart des autres langages et frameworks.

## Démarrage d'un conteneur en mode Dev
Pour exécuter notre conteneur afin de prendre en charge un flux de travail de développement, nous procéderons comme suit :

- Montez notre code source dans le conteneur
- Installez toutes les dépendances, y compris les dépendances « dev »
- Démarrez nodemon pour surveiller les modifications du système de fichiers

So, let's do it!

1. Assurez-vous qu'aucun conteneur **lab-docker-101** n'est en cours d'exécution.
2. Exécutez la commande suivante. Nous vous expliquerons ensuite ce qui se passe :
```bash
docker run -dp 3000:3000 \
    -w /app -v $PWD:/app \
    node:10-alpine \
    sh -c "yarn install && yarn run dev"
```
* ```-dp 3000:3000``` exécutez en mode détaché (arrière-plan) et créez un mappage de port
* ```-w /app``` l'image à utiliser. Notez qu'il s'agit de l'image de base de notre application à partir du Dockerfile
* ```sh -c "yarn install && yarn run dev"``` Nous démarrons un shell en utilisant ```sh``` (alpine n'a pas de bash) et exécutons ```yarn install``` pour installer toutes les dépendances, puis exécutons ```yarn run dev```. Si nous regardons dans le ```package.json```, nous verrons que le script ```dev``` démarre ```nodemon```.
3. Vous pouvez consulter les journaux en utilisant ```docker logs -f <container-id>```. Vous saurez que vous êtes prêt à partir lorsque vous verrez ceci...
```bash
docker logs -f <container-id>
$ nodemon src/index.js
[nodemon] 1.19.2
[nodemon] to restart at any time, enter `rs`
[nodemon] watching dir(s): *.*
[nodemon] starting `node src/index.js`
Using sqlite database at /etc/todos/todo.db
Listening on port 3000
```
Lorsque vous avez terminé de regarder les journaux, quittez en appuyant sur Ctrl+C.

4. Maintenant, apportons une modification à l'application. Dans le fichier _______, modifions le bouton « Valider » pour qu'il dise simplement « Réservez une place ». Cette modification sera à la ligne ___.

5. Il vous suffit d'actualiser la page (ou de l'ouvrir) et vous devriez voir le changement reflété dans le navigateur presque immédiatement. Le redémarrage du serveur Node peut prendre quelques secondes. Si vous obtenez une erreur, essayez simplement d'actualiser après quelques secondes.

6. N'hésitez pas à effectuer les autres modifications que vous souhaitez. Lorsque vous avez terminé, arrêtez le conteneur et créez votre nouvelle image à l'aide de docker build -t lab-docker-101 .

L'utilisation de montages de liaison est très courante pour les configurations de développement local. L'avantage est que la machine de développement n'a pas besoin d'avoir tous les outils et environnements de build installés. Avec une seule commande docker run, l'environnement de développement est extrait et prêt à fonctionner. Nous parlerons de Docker Compose dans une étape ultérieure, car cela nous aidera à simplifier nos commandes (nous obtenons déjà beaucoup d'indicateurs).

# Recap 
À ce stade, nous pouvons conserver notre base de données et répondre rapidement aux besoins et aux demandes de nos investisseurs et fondateurs. Hourra ! Mais, devinez quoi ? Nous avons reçu une excellente nouvelle !

Votre projet a été sélectionné pour un développement futur !

Afin de préparer la production, nous devons migrer notre base de données de SQLite vers quelque chose qui peut évoluer un peu mieux. Pour plus de simplicité, nous conserverons une base de données relationnelle et passerons notre application à MySQL. Mais comment devons-nous exécuter MySQL ? Comment permettre aux conteneurs de communiquer entre eux ? Nous en parlerons ensuite !

#### Section suivante : [Multi-Container Apps](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101/blob/main/docs/multi-container-apps.md)