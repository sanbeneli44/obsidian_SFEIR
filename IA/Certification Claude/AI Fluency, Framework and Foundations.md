**AI Fluency**: Ensemble de bonnes pratiques, connaissances, compréhension ...
* Correct (Effective)
* Efficace (Efficient)
* Ethique (Ethical)
* Sécuriser (Safe)

Utilisation de l'IA
* automisation
* collaboration
* Traitement indépendant de l'IA à notre place

# 4D Framework

* **Delegation**: focus sur la demande, définit une vision claire et la stratégie de l'utilisation de l'iA dans la résolution du problème
	* comprendre notre but et notre problème
	* ce que l'IA peut ou ne peut pas faire
	* décider quelle partie va être traitée par l'IA et par moi
* **Description**: communication claire avec l'IA
	* quel est le résultat attendu
	* quelle approche l'IA va utiliser pour résoudre le problème
	* comment l'IA va se comporter (le ton et le style de l'intéraction)
* **Discernment**: évaluation du résultat obtenu
	* est ce que le résultat est utile et correct ?
	* est ce que l'IA a utilisé la bonne approche
	* est ce que l'IA s'est comporté comme on l'a souhaité ?
* **Diligence**: focus sur l'intéraction responsable de l'IA
	* Assurer de l'exactitude et prendre ses responsabilités
	* Honnêteté et transparence
	* Utilisation éthique et conscience critique

# Principes de l'IA Générative ? 

**language models**: sont entrainés, peuvent prédire et génère une réponse compréhensible par tous les humains

3 pilliers
* **Algorithme**: 
	* réseau neuronal: indique comment l'IA apprend
	* transformers: (gamechanger en 2017): architecture excellant dans la compréhension des séquences de texte, faisant le lien avec les mots, les textes longs ... permettant de créer un contexte
* **Données**
	* articles et sites web
	* exemples de codes
* **Traitement**
	* GPU, TPU ...
	* clusters



# Capacités et limitations
**Capacités**: LLM peuvent en plus de leurs connaissances, faire des recherches sur internet, utiliser d'autres applications

**Limitations**: 
* Date de la fraicheur des données 
* Pas de vérifications de l'exactitude des données (Hallucination)

Chaque LLM a une limitation dans la taille de son contexte

Avec la même question, l'IA pourra répondre des réponses différentes

# Delegation

La délégation est critique pour l'exactitude et l'efficacité

Les expériences personnelles permettront de décider ce qui peut être géré par l'IA ou par l'homme

L'IA ne lit pas dans mes pensées

# Description

Ce n'est pas juste faire des prompts mais expliquer la tâche, poser une question, fournir du contexte et guide l'intéraction.

Avec une meilleure description, on transforme l'IA en assistant générique qui serait un partenaire qui devrait répondre à nos besoins

# Techniques de prompt efficaces

Communication to IA
* Ce que nous voulons
* Comment on veut qu'il le fasse
* Comment nous voulons intéragir avec l'IA

C'est comme un nouveau collègue qui aurait besoin de directions claires pour faire son travail

## Pilliers d'un prompt efficace

* **Fournir du contexte**: 
	* Expliquer ce qu'on veut
	* Pourquoi on le veut
	* Qui on est
	
* **Donner des exemples**
* **Indiquer le format/contraintes du retour attendu**
* **Décomposer une tâche complexe**: découper la tâche dans le prompt en sous tache en lui indiquant l'ordre / la priorité de chaque tâche
* **Demander à Claude de penser en premier**: indiquer à Claude qu'avant de répondre, il doit prendre en compte certains paramètres (faire une recherche approfondie, considération des différents facteurs, les potentiels contraintes et les différentes approchaes pour trouver la meilleure solution ) **Il est préférable de lui demander de réfléchir avant plutôt que de lui demander des comptes sur le résultat obtenu**
* **Définir un rôle (qui suis je ?), un style, un ton**

**TIPS**: 
* Ne pas hésiter à demander à l'IA d'améliorer mon prompt en lui indiquant le but à atteindre
* Demander plusieurs solutions
* Demander un formatage différent de la solution
* Demander son de gré de confiance sur sa réponse
* Reset la conversation au lieu d'essayer de corriger la conversation en cours

# Discernement

Le discernement est la faculté d'évaluer les réponses de l'IA, de son raisonnement ...

Cette vérification peut être faite
* **expertise**
* **Comprendre comment fonctionne le travail de l'IA et de ses défauts**

# IA responsable (Diligence)

Focus sur la partie éthique et sécuritaire

Quels sont les impacts de travailler avec une IA ?
* Comment l'IA a été créé et entrainé ?
* Quelles données sont utilisées ?
* Qui est propriétaire des données que j'ai envoyé ?
* Qui a accès à ces données ?
# Vocabulaire
**GPU**: Graphic Processing Units
**TPU**: Tensor Processing Units
**Contextes** contient les prompts, les réponses et les autres infos qu'on a partagé
