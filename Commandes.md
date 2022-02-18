# Docker

Créer image 
```
docker build . -t [tag]
```

Créer conteneur
```
docker run -d -p [host:cont] --name [cont_name] [repo/img:tag]

docker run -d -p 80:3000 --name nest enssapp/nestjs:latest
```

# SSH

Connexion SSH
```
ssh -i [keypair] user@[floating_ip]
```
> mdp : toto

Reset cache keypair
```
ssh-keygen -R [floating_ip]
```
