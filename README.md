# Context global du Projet

Ce projet a été réalisé dans le cadre du TP final en devops et mlops encadré par le professeur: Samuel ROHAUT.

Ce present projet a été realisé par:

- **Sow Mamadou Saidou**
- **Diallo Abdoulaye**

## Description du projet

Le projet consiste à la mise en place d'une application/interface graphique permettant, à partir d'une radiographie pulmonaire, de detecter la présence de la pneumonie ou pas.
Cette detection se fait grâce à un modèle de machine learning entraîné avec la librairie MLFLOW et avec plus de 10000 images de radiographie pulmonaire.

Le projet final est constitué de deux (2) applications séparées:

1. **Application Web**
   Une application web réalisée avec le framework Flask proposant une interface graphique, constituée d'un champs input file qui prend une image en entrée et d'un bouton permettant de soumettre l'image choisie. La soumission de l'image fait appel à une requête asynchrone (en javascript) qui appel la route **`/predict`**. Dans le backend, l'appel du route enclenche une requête vers le serveur mlflow (route: **`/invocations`**) avec pour paramètre un tableau de donnée representant l'image. Cette requête retourne une réponse avec une probabilité.

2. **Serveur MLFLOW**
   Le modèle MLFLOW a été entrainé en local pour éviter les entrainements en ligne qui nécessite beaucoup de ressource. L'application déployée contient donc les modèles déjà preenregistrés et près à l'usage.

## Architecture du projet

Ce projet utilise les technologies suivantes:

- Flask
- Mlflow
- Terraform
- Ansible

### Configuration AWS

La configuration des instances EC2 est définie dans le fichier **`main.tf`** (situé à la racine du projet).
Il existe deux (2) instances de EC2:

- **Instance frontend**
  Qui héberge l'application web fait avec Flask. Cette instance ouvre un port 80 qui permet d'ouvrir la page de l'application directement sur un navigateur, ainsi qu'un port 22 pour une connexion SSH.

- **Instance mlflow**
  Qui héberge l'api MLFLOW. Cette instance ouvre un port 8000 qui permet de faire des appels au modèle préenregistré, ainsi qu'un port 22 pour une connexion SSH.
  Cette instance est de type t3.medium permettant d'avoir un serveur avec 4GB de RAM. Ce volume a été choisi car le modèle qui répose sur la librairie TensorFlow consomme une bonne partie de la memoire.

### Configuration Ansible

La configuration instances à travers ansible se trouve dans deux (2) fichiers distincts: le fichier **`app.playbook.yml`** qui définit la configuration de l'application web et le fichier **`mlflow.playbook.yml`** qui définit la configuration de l'API MLFLOW.
Donc on pourrait le voir dans le fichier **`app.playbook.yml`**, l'application web utilise un serveur NGINX qui fait un proxy l'application Flask.
On pourrait aussi voir que l'une des tâche consiste à enregistrer l'adresse du serveur de l'API de mlflow dans les variables d'environnement de l'application web permettant ainsi une communication entre les deux(2) instances.

## Instructions de déploiement

Liste des tâches pour déployer le projet:

1. Récupérer le projet sur git (git clone)
2. Ajout deux (2) fichiers dans le dossier **`.aws`**

- Un fichier nommé **`_credentials`**: auquel vous ajoutez vos accès AWS. **(Attention: la première ligne doit contenir ceci: [awslearnerlab])**
- Un fichier nommé **`key.pem`**: auquel vous ajoutez votre clé privée à AWS.

3. Désarchiver le fichier zippé nommée "mlruns.zip" dans le dossier mlflow. Ce fichier a été zippé car très lourd.
4. Lancer la commande ci-dessus à la racine du projet vous permettant déployer le projet sur AWS

```bash
./deploy.sh
```

## Instructions d'utilisation

Une fois le déploiement terminé, rechercher les ansible qui les tâches suivantes:

- **FrontEnd Application** qui indique l'adresse pour accéder l'application web
- **Mlflow app address** qui indique l'adresse de l'api MLFLOW.

Ouvrez l'adresse de l'application de web sur un navigateur.
