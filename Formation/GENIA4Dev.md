
Formation genIa 200: https://bit.ly/4axNsKc

# Module 1: Introduction à l'iA pour les développeurs

Autocompletion -> suggestion -> chat -> agents

Agent qui fait la modification de fichier (agentic)

Nouvelle unité de mesure: Token

**token** est la plus petite unité de texte qu'un LLM traite (cf conf devoxx de Didier et Guillaume Laforge): 1 mot est approximativement 1,3/1,5 token

**Fenêtre de contexte (contexte)**: nombre maximum de token qui peut être traiter en une seule intéraction, au delà des informations seront perdues
Token entrée + toekn sortie <= Fenetre de contexte totale

Les output sont beaucoup plus petits ques les inputs

Ces informations se retrouvent dans la doc

**Modèle IA** système mathématique (réseau de neurone)

* Comment évaluer les différents modèles ?
	* Key bench (SWE, LiveCodeBench, polyglot ...): Intéressant pour les LLM locaux
	* Evaluations dimensions

Différents type de modèles
* Frontier / Flagship: plus gros traitement mais consomme plus de token (claude opus)
* Balanced: équilibé (sonnet)
* Fast / Lightweight: réponse rapide mais économique (Haiku)

Les taches en mode automatique n'ont pas besoin de super puissance

## Outils pour utiliser les modèles

* Extension IDE (github copilot)
* Agent cli (claude code gemini / gemini cli): ligne de commande mais les plus récentes en terme de fonctionnalité
* IDE AI-Native (anti gravity / cursor)

## L'art du prompting

Prompting technique:
* **role**: qui je suis (dans quelle zone du knowledge graph)
* **context**: informations pour éviter les erreurs car l'IA va supposer notre contexte
* Format: Spécifier le format de la sortie
* Few-shot: donner des exemples pour que l'IA s'en inspire
* CoT: Catégoriser et de traiter séquentiellement des phases importantes (step by step)

Faire cela pour tous nos prompts, c'est relou => le fichier claude.md ca définir le rôle et le contexte.

**Lab**: pastrès intéressant à faire

## Module 2: Fondamentaux du vibe coding

Différence entre vibe coding et agentic coding

**Vibe coding**: Génération de code via un prompt et qui part en code (no review) => très rapide

* Problème de performance: pas de possibilité de détecter les problèmes performances (100 utilisateurs -> 1 000 000 utilisateurs)
* Problèmes de sécurités
* Crise de maintenabilités car pas de maitrise sur le code

Pas d'utilisation de vibe coding pour du code en production surtout s'il y a besoin de sécurité (banque, médical ...)

Vibe coding peut être utilisé pour faire des proto (hackaton, démo ...), pour des scripts ponctuels 

**Lab**: intéressant à faire

Prompt Keep it simple E? Reproductible Narrow E? L?: Système qui rafine nos prompts
>[https://www.reddit.com/r/PromptEngineering/comments/1nt7x7v/after_1000_hours_of_prompt_engineering_i_found/](https://www.reddit.com/r/PromptEngineering/comments/1nt7x7v/after_1000_hours_of_prompt_engineering_i_found/)
> https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices

La certification claude fournit des conseils sur la façon de faire des prompts

**Agentic coding**: Code développé par l'IA et Revue par un développeur

L'IA va nous faire résoudre des problèmes de plus haut niveau et d'avoir un vrai travail d'ingénieur

L'IA devient un assistant, pas un remplaçant
Continuer le pair programming avec l'IA

Utilisation de l'IA sur des choses qu'on faisait mal comme la doc, accessibilité, tests ...

# Module 3: Workflow agentic

Specify (quoi)-> Plant (comment) -> Tasks -> implement -> Validate

Chaque étape nécessite plusieurs aller-retour avec l'IA dans des fenêtre de contexte séparé et avec une revue humaine de chaque étape

SDD: impose une vision du workflow

Qu'est ce que la gestion du contexte:

Ajouter @agent.md dans claude

Dans gemini c'est de la configuration

## Cli

/model permet de changer de modèle

**rewind** restaure le contexte jusqu'à la commande dont on fait le retour arrière

**Resume**: permet de switcher d'une session à une autre

**Clear**: permet de déclarer une nouvelle conversation mais pas de clear du contexte

/memory: permet d'éditer le fichier CLAUDE.md

Il est possible d'avoir un fichier CLAUDE.md.local en plus du fichier CLAUDE.md

Rules: permet de couvrir un sujet (expérience: junior / sénior => règles différentes). Enrichir son contexte de façon empirique ou de façon cibler

**agents**: fichier markdown va avoir une responsabilité (un rôle)
* name
* description
* tools: par exemple, LLM peut écrire sur le file système 
Définir ce qu'il a droit de faire et de ne pas faire (si analyse, pas d'écriture sur les fichiers analysés)
* model (sonet, haiku, opus)
L'agent va se lancer dans un contexte séparé. La récupération de l'agent ne polluera pas le contexte

/agents permet de lister les agents dispo

## Skills

instruction de prompt réutilisable

registry récupérable sur internet
* https://skills.sh
* https://tessl.io/registry
Les skills sont les fonctionnalités les plus interopérables sur les différents modèles/providers