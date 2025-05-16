1-1 Pourquoi vaut-il mieux utiliser -e pour les variables d'environnement ?

Car cela évite de stocker des informations sensibles (comme les mots de passe) en dur dans l'image, ce qui est une mauvaise pratique de sécurité.

---

1-2 Pourquoi a-t-on besoin d’un volume pour Postgres ?

Pour éviter de perdre les données à chaque suppression du conteneur. Le volume permet de stocker les données de manière persistante sur l’hôte.

---

1-3 Dockerfile et commandes du conteneur Postgres :

Dockerfile :
```
FROM postgres:17.2-alpine
ENV POSTGRES_DB=db \
    POSTGRES_USER=usr \
    POSTGRES_PASSWORD=pwd
```

Commandes :
```
docker build -t tp-database ./database
docker run --name postgres-db --network app-network -v tp_db-data:/var/lib/postgresql/data -d tp-database
```

---

1-4 Pourquoi un multistage build ?

Pour séparer la phase de build et d’exécution. On évite de garder Maven et le JDK dans l’image finale, ce qui la rend plus légère.

Étape 1 : compile le code avec Maven  
Étape 2 : copie le jar et exécute avec uniquement un JRE

---

1-5 Pourquoi utiliser un reverse proxy ?

Pour centraliser les requêtes, exposer un seul point d’entrée, masquer la complexité interne, gérer les ports, la sécurité, ou servir une interface web plus tard.

---

1-6 Pourquoi docker-compose est important ?

Parce qu’il permet de tout configurer, lancer, arrêter et reconstruire facilement. Il automatise la gestion de plusieurs conteneurs comme dans un projet réel.

---

1-7 Commandes Docker Compose importantes :

```
docker compose build
docker compose up -d
docker compose down -v
docker compose ps
docker compose logs -f
```

---

1-8 Mon fichier docker-compose :

```yaml
services:
  database:
    build: ./database
    container_name: postgres-db
    environment:
      POSTGRES_DB: db
      POSTGRES_USER: usr
      POSTGRES_PASSWORD: pwd
    networks:
      - app-network
    volumes:
      - db-data:/var/lib/postgresql/data

  backend:
    build: ./backend-simple-api
    container_name: springboot-api
    depends_on:
      - database
    networks:
      - app-network
    ports:
      - "8080:8080"

  httpd:
    build: ./http
    container_name: apache-reverse-proxy
    ports:
      - "8088:80"
    depends_on:
      - backend
    networks:
      - app-network

networks:
  app-network:

volumes:
  db-data:
```

---

1-9 Commandes utilisées pour publier les images :

```
docker login
docker tag tp-database simiamine/tp-database:1.0
docker push simiamine/tp-database:1.0

docker tag tp-backend simiamine/tp-backend:1.0
docker push simiamine/tp-backend:1.0

docker tag tp-httpd simiamine/tp-httpd:1.0
docker push simiamine/tp-httpd:1.0
```

---

1-10 Pourquoi publier les images en ligne (Docker Hub) ?

Pour pouvoir les réutiliser sur d’autres machines, partager avec les autres membres du projet, ou les intégrer facilement dans un pipeline CI/CD.