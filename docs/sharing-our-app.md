
# Sharing Our App (Partager notre App)

Maintenant que nous avons créé une image, partageons-la ! Pour partager des images Docker, vous devez utiliser un registre Docker. Le registre par défaut est Docker Hub et c'est de là que proviennent toutes les images que nous avons utilisées.

## Creer un Repo
Pour pousser une image, nous devons d’abord créer un dépôt sur Docker Hub.

1. Allez sur [Docker Hub](https://hub.docker.com/) et connectez-vous si nécessaire.
2. Cliquer sur le button **Create Repository**
3. Pour le nom du repository, utiliser docker-lab-101. Assurez-vous que la visibilité est publique
4. Cliquer sur le button **Create**

Si vous regardez sur le côté droit de la page, vous verrez une section nommée Commandes Docker. Cela donne un exemple de commande que vous devrez exécuter pour envoyer des données vers ce dépôt.

[Exemple image de commande push](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git/images/docs/docker-hub-push-cmd.png "Commande push exemple")

## Pousser notre image
1. De retour dans votre environnement, essayez d'exécuter la commande. Vous devriez obtenir une erreur qui ressemble à ceci :
```bash
$ docker push yaboigui/fmnt-docker-lab-101
The push refers to repository [docker.io/yaboigui/fmnt-docker-lab-101]
An image does not exist locally with the tag : yaboigui/fmnt-docker-lab-101
```
Pourquoi cela a-t-il échoué ? La commande push recherchait une image nommée yaboigui/fmnt-docker-lab-101, mais n'en a pas trouvé. Si vous exécutez ```docker image ls```, vous n'en verrez pas non plus.

Pour résoudre ce problème, nous devons **« étiqueter »** notre image, ce qui signifie en fait lui donner un autre nom.

2. Connectez-vous au Docker Hub à l'aide de la commande ```docker login -u votre-docker-username```

3. Utilisez la commande ```docker tag``` pour donner un nouveau nom à l'image fmnt-docker-lab-101. Assurez-vous de remplacer YOUR-USER-NAME par votre identifiant Docker
```bash 
docker tag fmnt-docker-lab-101 YOUR-USER-NAME/fmnt-docker-lab-101
```
4. Essayez maintenant à nouveau votre commande push.
```bash
docker push YOUR-USER-NAME/fmnt-docker-lab-101
```
## Executons notre image sur une autre instance
Maintenant que notre image a été créée et poussée dans un registre, essayons d’exécuter notre application sur une instance qui n’a jamais vu ce conteneur !

1. Si vous etes sur le AWS creer une nouvelle instance EC2 ou si vous etes en local connectez vous sur un autre compte utilisateur
2. Dans votre nouvelle instance, démarrez votre application fraîchement lancée.
```bash
docker run -dp 3000:3000 yaboigui/fmnt-docker-lab-101
```
Vous devriez voir l'image se dérouler vers le bas et éventuellement démarrer

3. Ouvrez un navigateur et tapez : ``` https://public-ip:3000``` et vous verrez votre site apparaître.
Bingo !!

# Recap
Dans cette section, nous avons appris à partager nos images en les poussant vers un registre. Nous sommes ensuite passés à une toute nouvelle instance et avons pu exécuter l'image fraîchement poussée. C'est assez courant dans les pipelines CI, où le pipeline crée l'image et la pousse vers un registre, puis l'environnement de production peut utiliser la dernière version de l'image.

Maintenant que nous avons compris cela, revenons à ce que nous avons remarqué à la fin de la dernière section. Pour rappel, nous avons remarqué que lorsque nous avons redémarré l'application, nous avons perdu tous les participants sur la liste. Ce n'est évidemment pas une expérience utilisateur optimale, alors apprenons comment conserver les données après plusieurs redémarrages !  

#### Section suivante : [Persisting Our DB](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git/docs/persisting-our-db.md)