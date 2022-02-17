# Projet Cloud
Alexandre LOU & Jean-Hanna SALEH

## Objectifs

Nous souhaitons profiter de ce projet pour réaliser le déploiement d'une application web développée dans le cadre d'un autre projet. L'objectif est donc d'automatiser le déploiement d'un serveur Node back-end et d'une base de donnée MySQL sur l'infrastructure décrite dans le sujet.

## Création d'une image

Pour déployer le serveur node dans un container, nous créons une image contenant le code de notre application. De cette façon, le conteneur disposera des ressources nécessaires pour lancer le serveur back-end.

La création de cette image se fait grâce au fichier `Dockerfile` suivant :

```Dockerfile
FROM node:17-alpine3.14 As development
WORKDIR /usr/src/app
COPY package*.json ./
RUN yarn install
COPY . .
EXPOSE 80:3000
RUN npm run build
CMD ["npm", "run", "start:cloud"]
```

L'image est construite dans le répertoire contenant le code de notre application, ce qui nous permet de le copier.

Nous utilisons la commande suivante afin de build et ajouter un tag a cette image.

```
docker build . -t enssapp/nestjs
```
> Une fois que l'image est crée, elle est mise à disposition sur docker hub pour être utilisée plus tard lors de la mise en place de la stack dans Openstack.

## Infrastructure

Pour ce qui est de la partie infrastructure de ce projet, nous nous appuyons sur les templates heat d'Openstack afin d'automatiser la création des ressources qui serviront au déploiement.

> Certain paramètres sont renseignés dans un fichier d'environnement qui vient compléter le template.

Ce fichier contient une description des ressources physiques à mettre en place, mais également d'autres indications comme les user data et les configurations des machines virtuelles. 

## Docker-compose

Sur cette infrastructure, il faut maintenant organiser nos containers et rendre les différents composants de la web app opérationnels.

Pour décrire cette organisation, nous allons nous appuyer sur `docker-compose` :

```yaml
version: '2'
services:
    db:
        image: mysql
        container_name: db
        command: --default-authentication-plugin=mysql_native_password
        volumes:
            - ~/.docker/mysql:/var/lib/mysql
        ports:
            - 80:3306
        environment:
            - MYSQL_DATABASE=enssapp
            - MYSQL_ROOT_PASSWORD=12rR8tY45
            - MYSQL_USER=user
            - MYSQL_PASSWORD=12rR8tY45
```
```yaml
# docker-compose Node.js
version: '2'
services:
    nestjs:
        image: enssapp/nestjs:latest
        container_name: nest
        ports:
            - 80:3000
```

Des composants de configuration (`OS::Heat::SoftwareConfig`) nous permettent d'écrire ces fichiers docker-compose à l'intérieur des VM. Ces fichiers seront exploités par ces VM une fois via docker (également présent sur ces machines).


## Requêtes vers le Back-end

**Attention: OpenStack requiert une dizaine de minute afin d'instancier les conteneurs.**

De plus, **{@ip flottante}** désigne l'adresse ip flottante de la machine virtuelle 1 **08_projet_VM1**.

Cette variable correspond à **148.60.225.93** pour le dernier déploiement.

Voici quelques exemples de requêtes :

Obtenir un "Hello World!"
```
curl {@ip flottante}/api -UseBasicParsing
```

Obtenir la liste des emplois du temps existants :
```
curl {@ip flottante}/api/schedule/all -UseBasicParsing
```

Obtenir l'emplois du temps des INFO3 en parcours IA court :
```
curl {@ip flottante}/api/schedule?timetable=INFO3_IA -UseBasicParsing
```