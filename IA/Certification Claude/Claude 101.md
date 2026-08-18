
# Premiers pas avec Claude.ai

Porte d'entrée de l'utilisation des IA, les chats comme claude.api

Un bon prompt:
* Définir son rôle et ses objectifs
* Quel action va faire Claude (Claude va analyser, écrire ...) ?
* Spécifier les règles (style, ton ... à utiliser)

Il y a la possibilité d'uploader des fichiers

Claude peut générer toute sorte de fichiers comme pdf, csv, docs ...

## Configuration

**Seaarch menu** On peut donner à Claude des accès à des outils externes (recherche internet, drive, gmail ...). On peut ajouter des connecteurs, des plugins ou relier avec des skills

**Configuration model**: on a la possibilité de choisir le model que Claude va utiliser

**Extended**: A utiliser pour des tâches complexes. Ce mode augmente le temps de réponse et n'est pas nécessaire pour les questions basiques et simples

# Projet

## Nouveau projet

Création d'un nouveau projet en lui donnant un nom et une description contenant l'ojectif à atteindre

on peut épingler le projet (star) pour un accès rapide

Ensuite, on va choisir la visibilité du projet (accès restreint à certaines personnes ou à tout le monde)

## Configuration du comportement

Dans "Set project instructions", on va spécifier une liste d'instructions pour aider le chat à répondre avec mes besoins.

Par exemple, on va lui indiquer le rôle que va jouer l'IA et lui indiquer ce qu'on attend de lui.

Toutes ces instructions seront appliquées à chaque conversation du projet

## Ajouter bases de connaissances

On peut ajouter des fichiers au projet. On peut uploader des pdf, csv, text ... ou connecter à google drive

Claude utilisera ses fichiers dans chaque conversation associée au projet.

Dans le cas d'un dépassement de la capacité max du projet. Cklaude basculera en mode rag pour augmenter les capacités du projet

## Partage du projet

On indique qui peut récupérer le projet et dans quel mode (lecteur / rédacteur / createur: peut modifier les droits du projet). Un email sera envoyé pour indiquer qu'un projet a été partagé avec nous.

# Artifacts

Les artefacts sont des contenus interractifs que Claude va générer en dehors de la fenêtre de conversation.

Claude va créer un artefact quand
* Les informations de la conversation suffisent (plus de 15 lignes)
* Une information qu'on souhaite réutiliser, modifier ...
* Contenu qui se suffit à lui même sans avoir les infos de la conversation
* Contenu qu'on souhaiterait utiliser ultérieurement

Claude peut créer différents types d'artefact:
* Document
* Extrait de code
* HTML
* images SVG
* Mermaid diagramme
* Composant React
La création de documents, Excel, ppt, pdf fonctionne différement. Claude les créera pas avec un artefact mais sous la forme d'un téléchargement

## Création

Ajouter la description que ce que l'on souhaite avoir comme rendu.

Si Claude ne crée pas un artifact, on peut lui demander explicitement: 'Create this as an artifact" oou "Show me this is an artifact'

Claire générera un artifact dans une fenêtre à coté de la fenêtre
![[Screenshot 2026-08-05 at 11.49.14.png]]

Une fois, l'artefact créé, on peut
* basculer sur le visuel ou le code généré
* copier l'artefact
* télécharger l'artefact

## partager et publier

En plus de copier et de télécharger, on peut partager l'artefact à notre orgnisation ou le publier à tous ceux qui ont le lien

## Tips

* Bien spécifier quel artefact est ce qu'on veut
* Décrire pour quel usage sera utilisé cet artefact
* Itération, ne pas hésiter à améliorer le rendu en discutant avec Claude
* Demandes un artefact quand c'est nécessaire
# Skills

Les skills sont des instructions, des scripts ou des ressources que Claude peut charger dynamiquement

Il y a 2 catégories de types de skill
* **Skills d'Anthropic**: créée et maintenue par Anthropic permettant de créer des fichiers pfd, excel ...
* **skill personnalisée**: créée par moi ou par mon organisation
Attention à bien faire attention aux skills utilisées pour des questions de sécurité

## Création

On peut créer une skill avec le chat de conversation de Claude en lui indiquant qu'on faire à créer une skill permettant de faire une action précise.

## Skills vs project

Les projets apportent les connaissances dont Claude a besoin pour répondre, les skills définissent la manière de traiter une tâche

# Connection your tools

Les connecteurs transforment Claude d'assistant IA en collaborateur en lui permettant de lire, d'écrire, de faire des actions sur les outils que j'utilise au quotidien. 

A chaque conversation, Claude ne part pas de 0

Ces connecteurs sont liés au MCP offrant des outils à Claude

Il y a deux types de connecteur:
* **Connecteur Web**: service cloud et applications (gmail, slack ...)
* **Extension de bureau**: outils local

## trouver et connecter des outils

Pour les conencteurs web, on peut connecter des outils, en cliquant sur le bouton + et ensuiste sur add connector. 
Une liste de connecteur fournie par Claude apparaît

Pour les extensions de bureau, il faut installer Claude desktop app et ouvrir la configuration et sélectionnner extension. Il faut parcourir l'extension disponible sur notre ordinateur et l'installer

Une fois le connecteur installé, Claude répondra à nos requêtes en se connectant systématiquement aux outils

## Sécurités et permissions

En donnat accès à des outils à Claude, il faut faire attention à 
* **les accès**: Ne donner à Claude que les droits dont il a besoin
* **Claude voit ce que vous voyez**: Claude n'a accès qu'aux données dont vous avez accès.
* **Modification à tout moment**: on peut déconnecter un outils de Claude. On peut construire nos propres connecteurs. Ne prendre que des connecteurs de source fiable

# Recherche d'entreprise

Permet de rechercher des informations dans les outils et les datas de l'entreprise

Pour faire ce genre de recherche, il faut qu'un admin configure l'organisation et ensuite les membres de l'entreprise pourront se connecter avec leur compte

Coté sécurité, les conversations sont privées et Claude aura les mêmes accès aux données que celles des utilisateurs.

# Recherche en profondeur


La recherche est une fonctionnalité avancée qui transforme Claude d'un assitant conversationnel en un enquêteur méthodique

Quand on active la recherche, Claude ne répond pas seulement à la question mais il explore plusieurs sources et synthétises les informations

On utilisera la recherche pour faire des analyses/comparaisons entre plusieurs sources

En activant la recherche, Claude va faire des recherches sur différentes sources. Avant de faire la recherche, il va analyser la question et identifier les informations importantes.

Claude va faire plusieurs recherches et synthétisera la résultat. Claude fournira les sources de ces recherches.

## tips

Comme une recherche prendra plusieurs minutes plutôt que quelques secondes, il faut bien rédiger la requête
* être précis sur les objectifs à atteindre
* Préciser les sections ou les structures recherchées
* Inclure les contraintes
* Demander à Claude de corriger le prompt


