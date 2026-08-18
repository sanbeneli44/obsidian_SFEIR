
Claude code est un outils agentique qui va comprendre le code du projet, modifier des fichiers, exécuter des commandes ...

Un agent IA est un logiciel qui va intéragir avec son environnement et exécuter des actions dans un but prédéfini

Claude code peut
* Lire et comprendre le code du projet
* modifier des fichiers du projet
* exécuter des commandes dans un terminal
* faire une recherche sur internet


Pour utiliser Claude code, il faut garder à l'esprit les 3 concepts suivants:
* **la fenêtre de travail (contexte)**:; Claude va pouvoir faire des recherches dans notre code et comprendre ce qu'on veut faire
* **demander des permissions**: par défaut, Claude demandera avant d'exécuter des commandes
* **Claude peut faire des erreur**: Claude peut mal comprendre notre besoin, faire de l'over engenering, introduire des bugs

# Comment Claude code fonctionn

## boucle agentique
![[Screenshot 2026-08-05 at 17.32.50.png]]

Avec les informations de notre prompt, Claude va exécuter sa boucle agentique:
* **Gather Context**: permet de compléter le prompt, Claude intéragit avec le model ou avec des appels à des outils que Claude peut exécuter
* **Take action**: par exemple modifier un fichier ou exécuter un script
* **Verify results**: et détermine si la réponse apportée correspond à ce qui a été attendue. Si c'est le cas, le traitement s'arrêtera sinon Claude fera une boucle en allant à l'étape **Gather context**

## Context

Le contexte contient toutes les informations de la conversation (input et output), il est limité selon les models et si la taille de ce context atteint la taille maximale. Claude compactera les données, ce qui implique que c'est Claude qui va décidé ce qui va sortir du contexte, ce qui va être résumé ...

## Tools

Les tools permettent à Claude d'exécuter des tâches: recherche sur internet, lire un fichier ...

Il faut autoriser l'utilisation des tools à Claude pour qu'il puisse les utiliser. C'est claude qui déterminera quand utiliser les tools

## Permissions

Par défaut, pour chaque action, Claude demandera l'autorisation avant de modifier un fichier ou d'exécuter un script.

Avec le raccourci shift + tab, on peut switcher dans les différents modes 
* **Auto accept**: modifie les fichiers sans demander l'autorisation mais demandera pour l'exécution des commandes
* **Plan mode**: est un outils en lecture seul pour aider à la compilation d'un plan d'action avant de commencer

# Installation Claude Code

## Terminal

Il est recommandé d'utiliser la commande curl pour l'installation. Il est possible d'utiliser des gestionnaires comme Homebrew mais les mises à jour automatic ne se feront pas

Pour utiliser Claude, il faut ouvrir un terminal, aller dans le répertoire du projet et taper **claude**

On a la possibilité de s'authentifier avec notre compte entreprise ou on peut utiliser une clé d'api

Si l'entreprise a un compte entreprise, il est préférable de choisir cette option.

Claude aura accès au répertoire du projet et à ses sous-répertoires
## IDE

### Visual code

Installer l'extension **Claude Code for VS Code** par Anthropic (blue check). Après l'installation, il faudra redémarrer Visual code

## Jetbrains

Installer le plugin **Claude code** par Anthropic. Après l'installation, il faudra redémarrer intellij

Le logo Claude apparaitra et on pourra l'utiliser comme dans un temrinal

## Desktop

Il faut installer **Cloud Desktop** et s'être authentifié. Il faut ensuite aller sur l'onglet code.

La GUI est la même que le chat de Claude mais permet de travailler dans un répertoire spécifique, changer les permissions et travailler dans un environnement cloud

## Web

Accès avec l'url: claude.ai/code. L'utilisation est similaire au desktop mais limité seulement aux repositories github

# Premier prompt

## Plan mode

Le mode plan récupère notre prompt, analyse le code du projet, fait des recherches sur nos suggestions d'implémentations.

Claude posera des questions sur différents points afin de les clarifier.

Une fois termineée, Claude retournera un plan détaillé de ce qu'il compte faire pour répondre au besoin

Le mode plan est à utiliser pour des changements complexes en toute sécurité.

Après la relecture du plan, on indiquera à Claude d'exécuter son plan.

A la fin, on aura compris ce qu'a fait Claude et comment son raisonnement a été construit

# Exploration -> Plan -> Code -> Commit

## Explorer and Plan

Le moyen le plus rapide est d'utiliser le mode plan. Avec le mode plan, Claude n'éditera pas de fichiers. Il fera juste des lectures de fichiers, des recherches  pour répondre au besoin

on peut aussi demander à Claude d'explorer le code existant.

## Code

A l'acceptation du plan, on peut demander à Claude de tout jouer ou de demander à chaque exécution de chaque étape du plan

Ajouter des outils pour vérifier que Claude a bien développé ce qu'on lui a demandé et/ou inclure des tests que Claude pourra jouer à chaque étape

Claude peut aussi écrire des tests pour nous

## Commit

Une fois la solution testée et validée. Avant de pousser le code sur git, lancer un agent de relecture de code. 

Ensuite Claude générera un message de commit.avec mes préconisations

# Contexte

Le contexte est la mémoire de toute la conversation, chque fichier lu, chaque commande exécutée, chaque message envoyée par moi ... vont prendre de la place dans le contexte de la conversation

Il est important d'optimiser le contexte autant que possible

A la limite, le contexte est compacté avec les éléments que Claude a jugé important, il supprimera les appels aux outils non nécessaires et libèrera de la mémoire

On peut demander le compactage manuellement avec le commande **/compact**

Pour supprimer tout le contexte, on utilisera la commande **/clear**

Pour voir ce qu'il y a dans le contexte, on utilisera la commande **/context**

En repartant de 0, on peut utiliser le fichier claude.md pour donner des informations de contexte importantes à chaque conversation (attention à ne pas surcharger le fichier claude.md)

## Tips

* Chaque prompt doit être détaillé. Sans cela, Claude regardera plus de fichiers que  de nécessaire
* Serveur MCP charge tous les outils disponibles pour le contexte par défaut. Il serait intéressant d'éteindre certains serveurs et utiliser les skills qui seront chargés lorsque Claude en aura besoin
* Sous-agent s'exécute en parallèle et ont des contextes différents de votre agent principal

# Code review

La revue peut être faire par un sous-agent en le demandant à Claude. Claude exécutera cette commande avec un contexte vide et donc avec un oeil neuf

La commande **/commit-push-pr** commitera et poussera la PR. Des skills peuvent permettre de mettre un message de la création de cette PR dans des channels slack par exemple.

Quand Claude créée la PR, la session sera reliée à la PR automatiquement. Pour revenir dessus et traitement notamment les retours, il faudra exécuter la commande **claude --from-pr <PR_NUMBER>**

# Claude.md

Sans le fichier claude.md, à chaque conversation, Claude analysera le code du projet, pour comprendre quelles dépendances sont nécessaires et les fonctionnalités qui sont déjà implémentées

Claude lit automatiquement le fichier claude.md a chaque démarrage de conversation.

Pour créer le fichier claude.md, il faut utiliser la commande **/init**

Ce fichier peut être sauvegardé dans le repo git et partagé à toute l'équipe

## Tips

* Demander à claude de mettre une information dans le fichier Claude.md
* Si on a des documents que Claude doit connaitre, il faut utiliser @ avec le chemin du fichier markdown
* il est préférable de démarrer un projet sans Claude.md pour qu'il soit le plus compact possible et ne contienne que les informations importantes

# Sous-agents

Les sous agents sont des spécialistes pour déléguer des tâches. Chaque sous-agent s'exécute avec son propre contexte.

Le principal avantage est de ne pas polluer le contexte principal

Le sous-agent reçoit deux entrées
* un prompt spécifique provenant de notre fichier de configuration
* une description de la tâche écrite par l'agent principal basée sur ce que je lui ai demandé

Le sous-agent travaille de façon autonome. Il lira, modifiera les fichiers, utilisera des outils ...
Aucune de ces actions n'apparaitront dans le contexte princpale sauf le résultat

## Comment cela fonctionne

Pour comprendre une fonctionnalité existante, sans sous-agent, Claude lira beaucoup de fichiers, fera plusieurs recherches ... Tout sera enregistré dans le contexte principal même si on a juste besoin d'identifier un seul fichier

## Sous-agent intégré

CLaude code a plusieurs sous agents intégrés qu'on peut utiliser immédiatement

On peut demander l'utilisation d'un sous-agent avec le mot clé **subagent** (à vérifier)

D'aures sous-agent existent pour gérer les actions d'exploration, de fabrication de plan, de recherche et d'analyse du code du projet avant de présenter un plan 

On peut aussi créer nos propres sous-agents avec des prompts ou des outils

# Skills

Pour indiquer à Claude les standards de développement de l'équipe, structure des PR, les messages de commit, le formatage ... on utilisera les skills

Claude les utilisera lorsqu'il les jugera utile

Les skills sont des listes d'instruction, scripts et de ressources que les agents peuvent découvrir et utiliser 

Une skill est définie dans un fichier skill.md. La description permet à Claude de savoir quand charger et utiliser la skill

Les skills du projet peuvent être partagées car elles sont enregistrées dans le repo git du projet

Les skills seront utilisées pour effectuer des tâches spécifiques à des moments spécifiques

# MCP

Les MCP permettent de connecter à Claude code des outils externes et des données externes

Quand on posera une question, Claude saura s'il doit faire appel à ces outils

Les tools permettant à Claude d'exécuter des actions en utilisant des outils pour répondre au besoin

Il y a plus de 100 connecteurs MCP sur **claude.ai/connectors**

On peut ajouter un serveur MCP à Claude en utilisant la commande **claude mcp add --transport <nom_outils> <url_outils>**

Il y a deux types:
* **Serveur http**: accès à internet
* **serveur STDIO**: processu local
  
  la commande **/mcp** permet de voir les mcp reliés à la session de Claude code, le statut de chaque server et de pouvoir désactiver les serveurs MCP qui ne sont pas utiles

## Scope

Il y a 3 différents scopes
* **Local**: seulement disponible en local pour notre projet et pour nous (~/claude.json)
* **User**: disponible sur tous les projets (~/claude.json)
*  utilise un fichier project/.mcp.sjon qui sera sauvegardé dans notre repository git et partager à tous les membres de l'équipe

Les définitions des serveurs MCP sont ajoutés au contexte principal même si on ne les utilise pas

# Hooks

Les hooks permettent d'exécuter des commandes qui se délcnecheront systématiquement et avec le même résultat. Par exemple, formattage automatique, ajout de log, blocage d'actions dangereuses (drop table) ...

Hooks sont configurés dans le fichier settings.json.

Pour chaque hooks, on spécifie le "matcher", c'est à dire l'évènement qui va déclencher le hook ainsi que la commande a exécuté

On peut utiliser les hooks dans différents cas
* **UserPromptSubmit**: déclencher quand l'utilisateur soumet un prompt avant que Claude ne traite la demande
* **PreToolUse**: déclencher avant l'utilisation d'un outils
* **PostToolUse**: déclencher après l'utilisation d'un outils
* **Notification**: déclencher quand Claude envoie une notification, par exemple: demander la permission de faire une action
* **Stop**: déclencher quand Claude a fini de répondre

Hooks peuvent être partagés au reste de l'équipe en commitant le fichier settings.json dans le repository git

Si on a besoin d'exécuter une commande dans des cas précis qui arrivent régulièrement sans générer d'échec, il vaut mieux utiliser un hook plutôt qu'un prompt





