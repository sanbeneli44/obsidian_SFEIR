Une skill est un fichier markdown apprenant à Claude comment faire/répondre selon mes critères.

Ces préconisations seront utilisées automatiquement par Claude

Les skills sont des instructions, des scripts et des ressources. Ces skills sont présents dans le fichier SKILL.md dans le répertoire .claude/skills/

La description du skill indique à Claude les conditions d'utilisation du skil (est ce qu'il doit l'utiliser ou non)

Claude code a différentes façon de configurer son comportement. Les skills sont automatiques et spécifiques.

Le fichier Claude.md est chargé à chaque conversation. Les skills ne sont utilisées que si la description correspond à la situation. Seuls le nom et la description sont chargés, Claude charge le reste du skill que s'il en a besoin

# Création d'une première skill

Il faut créer un répertoire avec le nom de la skill en dessous du répertoire .claude/skills

Une skill contient
* **Nom**: contenant le nom de la skill (max 64 caractères)
* **Description**: indiquant quand la skill doit être utilisée (max 1024 caractères)
* **actions**: après les deuxième "----", c'est la liste des actions de la skill 

>\----
name: pr-description
description: utiliser lors de l'écriture, la modification d'une pull request ou lorsque l'utilisateur demande de synthétiser les changements d'une pull request
\----
Faire les actions suivantes ... 


Les skills sont chargées au démarrage de la session claude.
Claude va scanner 4 répertoires:
* **Entreprise paths**: manageg-settings.json
* **Personnal path**: ~/.claude/skills
* **Project's Claude skills**: project/.claude/skills
* **Installed plugins**: project/.claude-plugin/plugin.json
* **

Lorsque deux skills ont le même nom, la priorité est définie avec la liste ci dessus

Toutes les actions CRUD d'une skill nécéssitent un redémarrage de Claude

Si Claude trouve une skill qui match avec le contexte de la conversation, il demandera s'il doit charger tout le contenu de la skill

# Configuration et skills dans plusieurs fichiers

Le site agentsskills.io permet de récupérer les normes.

En plus du nom et de la description qui sont obligatoires, on peut ajouter d'autres métadonnées comme par exemple: allowed-tools, model ...

**allowed-tools**: Cette fonctionnalité permet d'indiquer quel outils peut être utilisé par Claude lorsque la skill est active.
**model**: indique quel model de claude sera utilisé pour la skill

## description

Une bonne description répond à deux questions:
* Que doit faire la skill ?
* Quand Claude doit utiliser la skill ?

## skill dans plusieurs fichiers

Dans le cas, où la skill est trop grosse, il est préférable de la séparer en plusieurs fichiers.

Il faut mettre dans le fichier skill.md, les informations essentielles, ensuite il faut faire reference aux autres fichiers markdown en indiquant dans quel contexte est ce qu'ils doivent être chargés

Le fichier skill.md ne doit pas dépasser 500 lignes

## Regroupement de scripts utilitaires

Les scripts dans notre skill peuvent être exécutés par Claude sans qu'il lise et mette dans le contexte de la conversation, le contenu du script

# Skills vs other Claude code features

## CLAUDE.md

Le contenu de CLAUDE.md est chargé à chaque conversation. 

**CLAUDE.md** doit être utilisé pour
* les standarts de développement qui doivent toujours être appliqués
* Les contraintes
* préférence de framework et de coding style

## Sous agent

Les informations des sous-agents sont chargées dans un contexwte différent du contexte de la conversation. Les instructions sont traités indépendament et retourneront les résultats.

**Subagents** sont utilisés pour
* déléguer une tâche indépendament du contexte de la conversation
* Avoir besoin de plusieurs accès à des outils dans la conversation 
* Isoler le traitement délégué du traitement de la conversation

## Hooks

Les Hooks se déclenchent sur des événements, par exemple utilisation d'un linter lorsque Claude sauvegarde un fichier. Les hooks sont déclenchés par Claude et pas par une requête utilisateur.

**Hooks** sont utilisés pour:
* Opérations faites sur chaque fichier
* validation avant l'appel d'outils spécifiques par Claude
* automatisation d'action suite à des actions de Claude
## Skills

Les skills sont chargés à la demande dans le contexte de la conversation lors qu'une requête utilisateur

**Skills** doit être utilisée pour
* tâche spécifique
* informations utilisées ponctuellement
* procédures détaillées qui alourdiraient inutilement la conversation

## MCP

Les MCP fournissent des outils externes

# Sharing skills

Certaines skills peuvent être partagées à l'équipe
Il y a plusieurs méthodes pour partager les skills:
* **git**: commiter le fichier skill.md dans le repo
* **plugin**: peut être partagé à plusieurs projets
* **déploiement entreprise**: l'entreprise peut partager ses skills à tous les collaborateurs via le fichier managed-settings.json
* **déléguer aux subagents**: Les sous agents n'ont pas accès à nos skills, quand on délègue une tâche à un sous-agent (nouveau contexte). Il faut donc configurer certains sous-agents pour avoir accès à une liste de skill. Les skills que le sous-agent pourra utiliser seront définies dans les métadonnées du sous-agent. Ces skills seront alors chargées au démarrage du sous-agent et non à la demande comme dans la conversation principale.

# Dépannage des skills

Quand une skill ne fonctionne pas, le problème vient d'une de ses catégories, le skill:
* **ne se déclenche pas**: vérification de la description, 
* **ne se charge pas**: vérification du nom skill.md (minuscule), qu'il est bien dans un sous-répertoire du répertoire skill
* **a des conflits**: vérification que les deux descriptions ne sont pas trop similaires. Vérification des priorités et renommé les fichiers s'il y a un conflit.
* **échoue lors de l'exécution**: Vérfication des dépendances, des permissions et des chemins
* **Plugin manquant**: Vider le cache et réinstallation du plugin
## Agent skill verifier

Installation agent skill verifier dépend de l'OS, recommandation d'utiliser UV

## Debug

Lancement de claude en Debug ete regarder les messages qui mentionnent le nom de la skill

> claude --debut




