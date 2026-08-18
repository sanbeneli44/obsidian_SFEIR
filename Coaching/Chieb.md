# 27/01/2026

6 ans d'expérience avec Sfeir ???

Toujours intéressé par le dev web, l'architecture, sécurité

Phrasé trop rapide

* Pack techno
Bonne description du produit, de l'équipe de la mission
On a développé
Nous avons utilisé
Manque version techno

keycloak => mission
devops => mission (à la fin de la présentation)

Achevé en mars 2023 ???

* SNCF
Que fait l'application
réduction dette technique: montée de version java, angular

Les infos des versions sont notifiés

Création des dashboards de monitoring avec Datadog

Travailler sur AWS, certification

* Filière Crédit agricole
Bonne description de la mission

Dernière version de java ?

Keycloak => mission
Participation des pipelines / CICD sur cluster kubernetes sur on prmomise

Architecture Hexa



C'est presque tout

* Question
VuesJs: Je suis orienté plus back que front, je suis capable d'assurer une tache création des dashboards
Petit flou, je galère coté CSS mais à l'aise coté Typescript

Build des composants: avec maven
différence entre mvn install et deploy: builder en local / deploy pousser sur l'artifactory

Différence entre PUT et PATCH: permet de faire une update sur une partie de l'objet / Put permet de modifier tout l'objet

Qualité du code: Nous avons principalement 3 steps
code review
sonar
scan CVE

Outils permettant de corriger les CVE ? manuellement
Renovate ? Je ne connais pas

Propagation / isolation readonly = true
propagation (je note)

Stratégie de test: 
Partie délicate dans le projet / occuper du temps sur le sprint

Autre stratégie de test => 
à coté

Tests cucumber basé sur gherkin

TDD: Difficile mais pas mis en place

Architecture hexagonale: Domaine est protégé et ne dépend pas des FW, utilisation des adapteurs pour la couche domaine

DDD: Diviser en différents micro services, chaque micro service est orienté domaine métier

ADR: Ne connait pas

restTemplate vers webclient ? Deprecated avec des failles de sécurité 
Retry native 

Kafka, kafka stream / rabbit MQ: Utilisation de Kafka
Projet perso sur Rabbit: Modèle PULL / Push

Queue / topic: topic: offset coté kafka 

Schéma avro ? oui permet de unifier le type de message entre le consumer / producer => contrat d'interface
Inconvénient: rigide

@Service et @Component: @Component bean Java / @Component bean de type métier

Les concepts principaux de Spring: injection de dépendance
IOC AOP / singleton et factory

Inejction de dépendance, utilisation de @Autowired
Injection par constructeur c'est mieux => au niveau des tests, instanciation des tests
@Autowired, le singleton est mal géré

Docker: Commande pour supprimer des volumes plus utilisées

Git: récupérer une commande à partir d'une autre branche

Keycloak: tu l'as mis en place sur tes projets 

IAM et Keycloak: Bonne réponse 

Comment sécuriser les API rest ?
Avec Spring security, les dépendances Auth et sécurity
Règle avec les rôles

Création code retour: 200 ou 202

Fonctionnalités à mettre en place rapidement sans faire les tests:
Cela dépend du contexte du projet. Est ce qu'on accepte de faire les tests ou est ce qu'on peut mettre en production sans test.

C'est moins grave de livrer en retard que d'accepter des régressions

Délégation sécurité à spring security: Au niveau des codes, quels sont les rôles ?