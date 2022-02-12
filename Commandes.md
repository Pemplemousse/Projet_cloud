# Docker

Créer image 
```
docker build . -t [tag]
```

Créer conteneur
```
docker run -d -p [host:cont] --name [cont_name] [repo/img:tag]
```

# SSH

Connexion SSH
```
ssh -i [keypair] debian@[floating_ip]
```

Reset cache keypair
```
ssh-keygen -R [floating_ip]
```

# Notes 

A l'heure actuelle : time out (28) quand connexion sur port 3000 via `curl 148.60.225.96:3000`.
Pour tout autre port : (7) connection refused. 

Paramètrage du conteneur pour exposition des ports : `-p 80:3000`

Bloquage de la communication par un des deux serveurs : soupçon de problème à cause de la base de donnée.
Nécessité de tester avec un end point indépendant pour vérifier l'hypothèse.
