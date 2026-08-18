* **claude.md**: pour les conventions
* **skill**: procédure spécifique
* **hook**: règle que code ne doit pas pouvoir ignorer
# Tools

Fonctionnalité ou module spécialisé permettant l'intéraction entre l'IA et le monde extérieur
* Accèder à des données externes
* Exécuter des actions
* Intéragir avec d'autres services
* ...

**Outils par défaut dans Claude code**

![[Screenshot 2026-07-03 at 16.51.48.png]]

Claude code a une intégration étroite avec github action

# Context

Il est important de ne donner à Calude que le context dont il a besoin pour faire sa tâche en le guidant vers les fichiers pertinents

## Claude.md

* **CLAUDE.md**: fichier projet, à commiter sur git et à partager avec les autres membres de l'équipe
* **CLAUDE.local.md**: fichier non commité et non partagé à l'équipe, instructions personnelles que Calude suivra mais que pour moi
* **~/.claude/CLAUDE.md**: fichier global, contiendra les instructions qui seront appliquées pour tous les projets exécutés en local

Un fichier avec trop d'informations nuit à la performance de Claude

Par exemple, ne jamais pousser sur un repo devrait plutôt être présent dans un hook que dans le fichier CLAUDE.md => déplacer l'application des règles dans des Hooks

Pour faciliter la maintenance du fichier, on peut utiliser des imports pour ranger des instructions dans différentes parties

>Séparation des commandes dans des fichiers regroupant différentes thématiques
 -- @.claude/conventions/code-style.md 

Cela permet de réduire la taille du fichier mais tout est chargé par Claude donc le contexte sera autant chargé

Indiquer quelles sont les règles les plus importantes qui ne doivent jamais être transgressées

# Commandes

**/init** 
* Création du fichier CLAUDE.md
* Analyse du code, des commandes pertinentes, des fichiers critiques ...

**/memory**: permet d'ouvrir les fichiers \*CLAUDE\*.md présent sur le filesystem

**@**: permet de spécifier un fichier afin de limiter le context a un fichier précis

**/plan** (planning mode) permet de faire des recherches approfondis dans le code source
* lit plus de fichiers
* création d'un plan d'implémnentation
* montre ce qu'on attend de lui
* demande l'approbation du plan

**/effort** permet de gérer la puissance de raisonnement de Claude


**/compact**: compresse toutes les demandes en une seule demande 
Ajouter des instructions après la commande permet à Claude d'indiquer comment synthétiser

**/clear** supprime le contexte en cours

**/rewind**: Chaque prompt crée un point de restauration, il est possible de revenir à un point de restauration précédent avec cette commande
Il y a différentes options de retour en arrière:
* Restaurer le code
* Restaurer la conversation
* Restaurer le code et la conversation
* permet de résumer **après** le point de contrôle
* permet de résumer **avant** le point de contrôle

**/goal** définit une condition de fin de tâche

en ajoutant clear à la commande, la condition sera supprimée

**/loop** exécute un prompt à intervale régulier, sous certaines conditions ...

**/schedule**: cron/routine qui se déclenche par un trigger ( tous les jours à 9h, à chaque requête http post, sur un évènement github ...)
routine: un prompt sauvegardé qui s'exécute dans le cloud (anthropic infra)

Une planification régulière s'exécute au maximum toutes les heures

Exemple de création d'une routine

> /schedule daily dependency audit at 9am

**/code-review**

## Ajouter des commandes personnalisées

Création de commandes personnalisées pour exécuter des taches répétitives
* Créer un sous-répertoire commands dans le répertoire .claude du projet
* créer un fichier md. le nom du fichier sera le nom de la commande par exemple audit.md => **/audit**
* décriver dans ce fichier les actions que doit exécuter Claude au format markdown

Il est possible d'ajouter des arguments au lancement de la commande personnalisée en préfixant l'argument par $, par exemple $ARGUMENT


# Worktrees

Permet d'éviter à Claude de se disputer la modification de fichiers lorsque plusieurs agents travaillent en même temps
Plusieurs arborescences de fichier sont alors créées. A la fin du traitement d'un agent, l'arborescence est automatiquement supprimé

un fichier worktreeinclude liste les fichiers devant être ignorés lors de la copie des arborescences

# Skills
Utilisation de taches répétées: checklist, action récurrente ...

Dans le répertoire .claude, ajouter un répertoire skills et un répertoire SKILL.md

Ce répertoire peut contenir d'autres fichiers: reference.md

Ce fichier devra être lié au skil (comment ?), Claude ne l'utilisera que lorsqu'il en aura besoin

Un fichier SKILL.md contient un nom, une description qui déclenche le skill et la procédure à déclencher

>name: svg-verify
description: Vérifier que le séparateur de svg ...

# Hooks

Un hook est un bout de code à exécuter 
* **PreToolUse** utiliser avant l'appel à un tool
* **PostToolUse** utiliser après l'appel réussi d'un tool
* **Stop** utiliser quand Claude vaut mettre fin à son travail
* **PreCompact / PostCompact**: utiliser avant et après la synthétisation
* **InstructionsLoaded** utiliser quand le fichier CLAUDE.md est chargé
* **SessionStart**: utiliser quand la session démarre

Retour du skil soit au format json ou avec un code
* **0**: succès
* **2**: échec et interruption 
* **1** échec mais le traitement continue

# Mode autorisation

Ce mode permet d'indiquer à Claude ce qui peut s'exécuter sans ma surveillance

Il y a 6 modes d'autorisation:
* **Manual**: permet la lecture seulement, Claude demandera des confirmations pour les autres actions
* **acceptEdits**: permet la lecture, l'écriture et l'exécution de script sans autorisation
* **plan**: lecture seulement, fais des recherches et propose des solutions sans éditer les fichiers 
* **auto**: traite toutes les actions sans confirmation utilisateur
* **dontask**: exécute seulement ce qui est autorisé par les tools (ci, pipeline ...) , les autres actions ne sont pas autorisées
* **bypassPermission**: Annule toutes les vérifications, à n'utiliser que sur un environnement local

# plugin 

* Installer un plugin
/plugin install <nom_du_plugin>
* Ajout d'un marketplace
/plugin marketplace add <nom_marketplace>

Un plugin est un ensemble de hook, configuration serveur MCP ...

Un plugin ne remplace par la configuration de Claude

Les hooks des plugins se déclencheront aux mêmes moments que mes hooks
## Création mcp

Dans un terminal, mais en dehors de la console de Claude, exécuter la commande:
claude mcp add playwright npx @playwright/mcp@latest


# Pull request

CodeReview est un service d'Anthropic qui fait une relecture des PRs
Il n'approuve pas les PRs, la décision reste au main d'un humain
# raccourci: 
* Passage en mode plan: Shift + TAB (2 fois)
* afficher le plan: Ctrl + g
* voir le raisonnement: Ctrl + o
* voir l"historique des demandes et pouvoir revenir en arrière: Echap + Echap
* interromp le traitement: Echap
* Shift + Tab
	* accept edit on
	* plan mode on
	* manuel mode on


