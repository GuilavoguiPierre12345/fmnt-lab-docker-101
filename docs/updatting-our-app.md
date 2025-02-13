
## Updatting Our App (Mise à jour de notre application)

En guise de petite demande de fonctionnalité, l'équipe FMNT nous a demandé de modifier le « Liste de participation vide » lorsque nous n'avons aucun élément de liste. Ils aimeraient le faire passer à ce qui suit :

> *Vous n'avez pas encore de participant sur la liste*

C'est assez simple, non ? Faisons le changement.

1. Dans le fichier ```~/app/src/path```, a la ligne ```line number``` faites ce changement suivant :
```bash 
changement place
```
2. Construisons notre version mise à jour de l’image, en utilisant la même commande que nous avons utilisée auparavant.
```bash
docker build -t fmnt-lab-docker-101 .
```
3. Commençons un nouveau conteneur en utilisant le code mis à jour.
```bash
docker run -dp 3000:3000 fmnt-lab-docker-101
```
Oh oh ! Vous avez probablement vu une erreur comme celle-ci (les identifiants seront différents) :
```bash
docker: Error response from daemon: driver failed programming external connectivity on endpoint laughing_burnell 
(bb242b2ca4d67eba76e79474fb36bb5125708ebdabd7f45c8eaf16caaabde9dd): Bind for 0.0.0.0:3000 failed: port is already allocated.
```
## Remplacement de notre ancien conteneur
1. Obtenez l'ID du conteneur en utilisant la commande ```docker ps```.
```bash
docker ps
```
2. Utilisez la commande ```docker stop``` pour arrêter le conteneur.
```bash
docker stop <container-id>
```
3. Une fois le conteneur arrêté, vous pouvez le supprimer en utilisant la commande ```docker rm```
```bash 
docker rm <container-id>
```
***Raccourcis de 2 et 3 :*** ``` docker rm -f <container-id>``` pour arrêter et supprimer en même temps

4. Maintenant, démarrez votre application mise à jour.
```bash
docker run -dp 3000:3000 lab-docker-101
```
5. Ouvrez l’application et vous devriez voir votre texte d’aide mis à jour !

## Racap 
Bien que nous ayons pu créer une mise à jour, vous avez peut-être remarqué deux choses :

Tous les éléments existants de notre liste de tâches ont disparu ! Ce n'est pas une très bonne application ! Nous en parlerons sous peu.
Un si petit changement impliquait de nombreuses étapes. Dans une prochaine section, nous verrons comment voir les mises à jour de code sans avoir besoin de reconstruire et de démarrer un nouveau conteneur à chaque fois que nous effectuons une modification.
Avant de parler de la persistance, nous verrons rapidement comment partager ces images avec d'autres.

#### Section suivante : [Sharing our App](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git/docs/sharing-our-app.md)