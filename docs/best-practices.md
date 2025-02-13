# Image Building Best Practices (Meilleurs pratiques de construction d'image)

### Image Layering
Saviez-vous que vous pouvez voir ce qui constitue une image ? En utilisant la commande ```docker image history```, vous pouvez voir la commande qui a été utilisée pour créer chaque calque d'une image.

1. Utilisez la commande ```docker image history``` pour voir les calques de l’image lab-docker-101 que vous avez créée plus tôt dans le Lab.
```bash
docker image history
```
Vous devriez obtenir un résultat qui ressemble à ceci (les dates/identifiants peuvent être différents)

<< Affiche image de sortie ici >>

Chacune des lignes représente une couche de l'image. L'affichage ici montre la base en bas et la couche la plus récente en haut. Grâce à cela, vous pouvez également voir rapidement la taille de chaque couche, ce qui vous aide à diagnostiquer les images de grande taille.

2. Vous remarquerez que plusieurs lignes sont tronquées. Si vous ajoutez l'option ```--no-trunc```, vous obtiendrez la sortie complète (oui... c'est drôle de voir comment vous utilisez une option tronquée pour obtenir une sortie non tronquée, hein ?)

```bash 
docker image history --no-trunc lab-docker-101
```

## Layer Caching
Maintenant que vous avez vu la superposition en action, il y a une leçon importante à apprendre pour aider à augmenter les temps de création de vos images de conteneur.

> Une fois qu'une couche change, toutes les couches en aval doivent également être recréées

Regardons une fois de plus le ```Dockerfile``` que nous utilisions...

```bash 
FROM node:10-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "/app/src/index.js"]
```

En revenant à la sortie de l'historique de l'image, nous voyons que chaque commande dans le Dockerfile devient une nouvelle couche dans l'image. Vous vous souvenez peut-être que lorsque nous avons apporté une modification à l'image, les dépendances de yarn ont dû être réinstallées. Existe-t-il un moyen de résoudre ce problème ? Cela n'a pas beaucoup de sens de livrer les mêmes dépendances à chaque fois que nous construisons, n'est-ce pas ?

1. Mettez à jour le Dockerfile pour copier d'abord le package.json, installez les dépendances, puis copiez tout le reste.
```bash
FROM node:10-alpine
WORKDIR /app
COPY package.json yarn.lock ./
RUN yarn install --production
COPY . .
CMD ["node", "/app/src/index.js"]
```

2. Construire une nouvelle image en utilisant ```docker build```
```bash
docker build -t docker-101 .
```

Vous devriez voir un résultat comme celui-ci...
```bash
Sending build context to Docker daemon  219.1kB
Step 1/6 : FROM node:10-alpine
---> b95baba1cfdb
Step 2/6 : WORKDIR /app
---> Using cache
---> 9577ae713121
Step 3/6 : COPY package* yarn.lock ./
---> bd5306f49fc8
Step 4/6 : RUN yarn install --production
---> Running in d53a06c9e4c2
yarn install v1.17.3
[1/4] Resolving packages...
[2/4] Fetching packages...
info fsevents@1.2.9: The platform "linux" is incompatible with this module.
info "fsevents@1.2.9" is an optional dependency and failed compatibility check. Excluding it from installation.
[3/4] Linking dependencies...
[4/4] Building fresh packages...
Done in 10.89s.
Removing intermediate container d53a06c9e4c2
---> 4e68fbc2d704
Step 5/6 : COPY . .
---> a239a11f68d8
Step 6/6 : CMD ["node", "/app/src/index.js"]
---> Running in 49999f68df8f
Removing intermediate container 49999f68df8f
---> e709c03bc597
Successfully built e709c03bc597
Successfully tagged docker-101:latest
```

Vous verrez que toutes les couches ont été reconstruites. C'est parfait puisque nous avons pas mal modifié le Dockerfile.

3. Maintenant, apportez une modification au fichier src/______ (par exemple, modifiez le ___ pour qu'il indique ____)

3. Créez maintenant l'image Docker à l'aide de ```docker build``` à nouveau. Cette fois, votre résultat devrait être un peu différent.
```bash
Sending build context to Docker daemon  219.1kB
Step 1/6 : FROM node:10-alpine
---> b95baba1cfdb
Step 2/6 : WORKDIR /app
---> Using cache
---> 9577ae713121
Step 3/6 : COPY package* yarn.lock ./
---> Using cache
---> bd5306f49fc8
Step 4/6 : RUN yarn install --production
---> Using cache
---> 4e68fbc2d704
Step 5/6 : COPY . .
---> cccde25a3d9a
Step 6/6 : CMD ["node","/app/src/index.js"]
---> Running in 2be75662c150
Removing intermediate container 2be75662c150
---> 458e5c6f080c
Successfully built 458e5c6f080c
Successfully tagged docker-101:latest
```

Tout d'abord, vous devriez remarquer que la construction a été BEAUCOUP plus rapide ! Et vous verrez que les étapes 1 à 4 utilisent toutes le cache. Alors, hourra ! Nous utilisons le cache de construction. Le transfert et l'extraction de cette image et ses mises à jour seront également beaucoup plus rapides. Hourra !

# Recap 
En comprenant un peu comment les images sont structurées, nous pouvons créer des images plus rapidement et livrer moins de modifications.

#### Et ensuite ? [Fin](https://github.com/GuilavoguiPierre12345/fmnt-lab-docker-101.git/docs/end.md)