Spring Boot DevOps – Projet pédagogique
-

📌 Présentation:
- 
Ce projet est une application Spring Boot utilisée dans le cadre du cours DevOps.
L'objectif est de construire progressivement une chaîne CI/CD en intégrant différents outils DevOps, depuis la gestion du code source jusqu'au déploiement et à la supervision de l'application.
Le projet servira de support pratique pour mettre en œuvre les différentes étapes du cycle Build → Test → Analyse → Package → Publication → Déploiement → Monitoring.

🎯 Objectifs pédagogiques
-
À travers ce projet, vous allez apprendre à :
- gérer le code source avec Git / GitHub ;
- automatiser le build avec Maven ;
- mettre en place une Intégration Continue (CI) avec Jenkins ;
- automatiser l'exécution des tests ;
- analyser la qualité du code avec SonarQube ;
- construire une image Docker ;
- publier et utiliser une image Docker ;
- déployer et orchestrer l'application avec Kubernetes ;
- mettre en place la supervision avec Prometheus et Grafana ;
- construire progressivement une chaîne CI/CD complète.

## 📁 Structure du projet

Le projet suit la structure standard d'une application Spring Boot avec Maven.  
Les fichiers DevOps seront ajoutés progressivement au cours des différentes étapes du projet.

```text
spring-boot-devops/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── ...                    # Code source Java
│   │   └── resources/
│   │       └── application.properties # Configuration de l'application
│   └── test/
│       └── java/
│           └── ...                    # Tests
│
├── pom.xml                            # Configuration Maven
└── README.md                          # Documentation du projet
```
Après l'étape Jenkins :
-
```text
spring-boot-devops/
├── src/
├── pom.xml
├── Jenkinsfile
└── README.md
```
Après l'étape Docker :
-
```text
spring-boot-devops/
├── src/
├── pom.xml
├── Jenkinsfile
├── Dockerfile
└── README.md
```

## 🚀 Pipeline CD (Jenkins + Docker)

Le `Jenkinsfile` exécute les étapes suivantes :

1. **Checkout** : récupération du code depuis GitHub
2. **Build & Test Maven** : `mvn clean verify`
3. **Docker Build** : création de l'image `backend-app:latest`
4. **Docker Push** : publication dans le registre privé `localhost:5000` (authentification via les Credentials Jenkins `registry-creds`)
5. **Deploy MySQL** : lancement du conteneur `mysql` (réseau Docker `timesheet-net`)
6. **Deploy backend-app** : lancement du conteneur `backend-app`
7. **Vérification** : `docker ps` et `docker logs backend-app`

### Ports utilisés

| Service | Port hôte | Port conteneur |
|---|---|---|
| Jenkins | 9090 | - |
| Registre Docker privé | 5000 | 5000 |
| MySQL | 3306 | 3306 |
| backend-app (Spring Boot) | 8082 | 8082 |

### Commandes de vérification

```bash
docker ps
docker logs backend-app
```

L'application est accessible sur `http://localhost:8082/timesheet-devops/`.
