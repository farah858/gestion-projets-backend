# Gestion des projets - Backend Spring Boot

Projet DevOps : intégration continue et déploiement continu avec Jenkins et Docker.

## Pipeline Jenkins (Jenkinsfile)

Git → Jenkins → Build & Test Maven → Docker Build → Docker Push → Déploiement MySQL → Déploiement backend-app → Vérification avec logs

| Étape | Commande |
|---|---|
| Build & Test Maven | `mvn clean package` |
| Docker Build | `docker build -t backend-app:latest .` |
| Docker Push | `docker login`, `docker tag backend-app:latest farrrraaaahhh/backend-app:latest`, `docker push farrrraaaahhh/backend-app:latest` |
| Déploiement MySQL | `docker run -d --name mysql --network devops-net -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test_db mysql:8.0` |
| Déploiement backend-app | `docker rm -f backend-app`, `docker pull farrrraaaahhh/backend-app:latest`, `docker run -d --name backend-app --network devops-net -p 8089:8080 ... farrrraaaahhh/backend-app:latest` |
| Vérification | `docker ps`, `docker logs backend-app` |

L'application est accessible sur http://localhost:8089 une fois déployée.
