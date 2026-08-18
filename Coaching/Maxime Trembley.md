
# Coaching décembre 2025
Attention aux mains, enlever les mains de sa bouche

Présentation de soi
Bien préparer sa présentation
	* présentation caravelle
	* PMU, pourquoi c'est une marche dans ta carrière
		* Java 17, Springboot, terraform
	* SNCF Connect
		* refonte conception
		* java (version)/kotlin, aws, gitlab
Faire le lien avec la mission

Le "peu" d'expérience ?

Pas d'appétence coté mobile

Envie d'aider au maximum la capacité

Patch et put: je ne connais pas

Stratégie de test: beaucoup de tests unitaires, tests d'intégration avec flux mocké, test de bout en bout.

Type de mock: wiremock pour bouchonné les appels rests, création des dataset par défaut pour une base de données en mémoire. Mockito

Pour des tests unitaires: junit jupiter, mockito
Wiremock pour les appels api

Pour compiler: maven / graddle

différence entre install et de deploy: réposne ok

Karate: outils préconfiguré de cucumber 

Docker: lancer local stack, simuler l'env aws et la base de données en local

Requête via JPA de deux tables: annotation one to many en mode lazy

JPQL: Je ne sais plus

Avantage d'utiliser JPQL: performance utiliser l'ORM

gestion des branches git: Je n'en sais rien, j'ai toujours utilisé la stack de l'équipe
Exemple de PMU (pas de mauvaise et de bonnes réponses)

Notion de ci/cd: un petit peu, j'ai appris en observant mes collègues PMU. maven dependency gitlab

Dépendance maven pas à jour: identification par sonar
Notion de dependency check (vulnérabilité) => Création d'un backlog identifié

Junit 4 -> junit 5: voir la documentation de junit vers la 4 à la 5.

Resilience4j: librairie pour mettre la resilience. Circuuit breaker pour permettre de faire souffler 

Propagation Transactional: transaction ssql mais pas spring 

Event (rabbitmq, kafka): Pas encore utilisé

Type d'architecture: n-tiers, on peut la confondre avec l'hexagonale: notion de couche faire des implémentations avec les interfaces (de ce que j'ai compris)

Avantage de l'architecture hexagonale ? Même chose

DDD: de nom seulement

BDD/TDD: de nom seulement
Principe du TDD: développement guidé par les tests. Test au fur et à mesure
Exemple bête ??? 
Manque la partie Refato

Appel d'un service par un autre service:
constructeur par injection de dépendance
Merci springboot ??

@autowired on peut l'utiliser mais je ne sais plus du tout pourquoi

@Service @Component: Je ne la connais
Projet tout seul dans mon coin

Pas hésiter à demander la réponse

Le peu que j'ai appris de kotlin, c'est à la va vite.

API First: écrire le contrat d'interface en premier puis la génération

# Coaching 5 janvier
Bonne présentation au globale avec une expertise java et spring Puis présentation de la mission caravelle
	Refonte de la structure du moteur de recherche
		=> Bien insister sur la partie étude
		Que du java 
PMU: 
	équipe 2 back et 3 front
		AWS (bien insisté sur la partie cloud)
		Mise en place de Karaté
		Génération PDF
SNCF & Tech
	Plusieurs refontes de découpages de services
	Optimisation sur les services avec resilienceForj
	Kotlin backend

Travailler sur des projets js ok du moment que ce n'est pas du front end
Kotlin migration java verds kotlin sncf connect
Quarkus: je me suis renseigné de mon coté 

Rédaction document ADR: Jamais
Plus de liberté sur les refontes.
Comment choisir les bonnes technos et les bonnes architecture:
> Une expérience pour dire que tu as argumenté pour faire passer tes précos

Clean code: des souvernirs d'école

Kafka et kafka stream: kafka non mais kafka stream (producer et consumer)
	Je ne sais pas les différences avec kafka

Technos non connues: Je vais apprendre. Je fais des petits projets perso pour le fonctionnement

Comment fais tu une revue de code ? 
* Voir les résultats du sonar
* Relirait le code
* Questionnerait l'entourage si j'ai un doute sur une question
* Hypothèse que les tests sont faits et passants

Tests unitaires sous kotlin: je ne me souviens plus
Tests unitaires puis des tests d'intégration: voir équivalent junit et mockito

Différence principale entre kotlin et java ?
Le peu de pratique que j'ai, la syntaxe
	sur les streams, java utilisent stream et fermer le stream, contrairement à kotlin pas obliger de spécifier cette notion de stream => Syntaxe plus simple
Je ne connais plus les termes exactes

Est ce que tu as des questions ?
Non

Si peut être, comment est organiser l'astreinte ?
En terme de population, combien de personnes vont utiliser l'application ?
Au lancement de l'application du PMU, une astreinte de début de déploiement. déjà eu affaire à de l'astreinte


