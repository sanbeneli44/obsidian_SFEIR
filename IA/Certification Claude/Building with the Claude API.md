
# Model de Claude

![[Screenshot 2026-08-06 at 11.03.06.png]]

* **OPUS**: trus intelligent
* **Sonnet**: équilibré
* **Haiku**: rapide et moins cher![[Screenshot 2026-08-06 at 11.09.59.png]]

Il est préférable de ne pas rester sur un seul model lors du développement d'un application mais d'utiliser le model adapté au besoin

# Accès à l'API Claude

Pour avoir accès à l'API Claude, il faut obtenir une clé d'api et pour cela, il faut avoir un server qui va faire le lien entre la GUI utilisée par l'utilisateur pour indiquer quelles sont les actions à effectuer et l'API d'Anthropic

Il y a plusieurs SDK de disponibles pour appeler l'API
* Python
* TypeScript
* JavaScript
* Go
* Ruby
![[Screenshot 2026-08-06 at 11.28.15.png]]
On peut aussi utiliser une requête HTTP à la place d'utiliser le SDK

Les informations à envoyer à l'API Claude sont
* clé d'api
* Model à utiliser
* Liste de message indiquant le besoin
* max tokens indiquant combien de token max doivent être utilisés

Lorsque l'API reçoit la requête, il y a plusieurs phases de traitement:
* **Tokenization**: transformation de chaque mot en au moins un token
* **Embedding**: Chaque token est converti en une suite de nombre
* **Contextualization**: Comme chaque mot peut avoir plusieurs significations, les mots l'entourant sont importants pour comprendre le sens
* **Generation**: Cette phase permet de donner un pourcentage et le model ne choisit le pourcentage le plus élevé mais c'est un mélange de probabilité de hasard. Il va commencer à générer du texte et à chaque sélection du mot/token, le model s'arrête et se demande s'il a atteint le niveau max de token. Un token sécifique indiquera que la réponse est complète
  
  Une fois ce traitement terminé, l'API enverra une réponse à notre serveur en lui indiquant
* Le texte de la réponsé généré
* information d'utilisation comme le nombre de token utilisé en entrée et en sortie
* la raison de l'arrêt du traitement: soit il a un token de fin de séquence, soit il atteint la limite du nombre de token max

# Première requête

Clé api (valable jusqu'au 5 septembre): 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

* Installation de l'environnement python - Anthropic
> pip install anthropic python-dotenv

* Ajout de la clé api, dans un fichier .env
> ANTHROPIC_API_KEY="your-api-key-here"

Ce fichier ne doit jamais être commité sous git

* Chargement du fichier d'environnement

> # Load env variables
> from dotenv import load_dotenv
> 
> load_dotenv()

* Création d'un client API
> # Create an api client
   from anthropic import Anthropic
   >
> client = Anthropic()
   model = "claude-sonnet-4-0"

* Création première requête API
> # Make a request
   message = client.messages.create(
	   model=model,
	   max_tokens=1000,
	   messages=[
		   {
		   "role": "user",
		   "content": "What is quantum computing? Answer in one sentence"
		   }
	]
)

/!\ Role peut être user (input) ou assistant (output)


Voici le résultat renvoyé par Claude, en affichant le contenu de la variable message

> Message(id='msg_011CdmYQKjE97zbzHVwS4Gzk', container=None, content=[TextBlock(citations=None, text='Quantum computing is a type of computing that uses quantum bits (qubits) to process information in multiple states simultaneously, allowing it to solve certain complex problems much faster than classical computers.', type='text')], model='claude-haiku-4-5-20251001', role='assistant', stop_details=None, stop_reason='end_turn', stop_sequence=None, type='message', usage=Usage(cache_creation=CacheCreation(ephemeral_1h_input_tokens=0, ephemeral_5m_input_tokens=0), cache_creation_input_tokens=0, cache_read_input_tokens=0, inference_geo='not_available', input_tokens=16, output_tokens=41, output_tokens_details=None, server_tool_use=None, service_tier='standard'))


Pour avoir seulement la réponse à la question, il faut récupérer le contenu de message.content[0].text
> process information in multiple states simultaneously, allowing it to solve certain complex problems much faster than classical computers.


Pour que cela fonctionne sur mon vscode, j'ai dû jouer ces commandes dans le répertoire de mon projet (terminal)

> python3 -m venv mon_env_anthropic

> source mon_env_anthropic/bin/activat

\+ de sélectionner le kernal mon_env_anthropic

# Multi-turn conversation

L'API d'anthropic et claude ne **stocke pas** les messages d'entrée ou de sortie. Il faut manuellement persister la liste des message du coté de notre serveur et fournir ces messages/contexte à chacune des requêtes suivantes

On va définir des méthodes pour enregistrer les différents messages et envoyé le contexte à Claude:

> def add_user_message(messages, text):
	user_message = {"role": "user", "content": text}
	messages.append(user_message)

> def add_assistant_message(messages, text):
	assistant_message = {"role": "assistant", "content": text}
	messages.append(assistant_message)

 > def chat(messages):
	  message = client.messages.create(
	  model=model,
	  max_tokens=1000,
	  messages=messages,
	)
	return message.content[0].text

Puis on va faire un algo pour enregistrer les questions et les réponses:

>messages = []
add_user_message(messages, "Define quantum computing in one sentence")
answer = chat(messages)
add_assistant_message(messages, answer)
add_user_message(messages, "Write another sentence")
answer = chat(messages)
add_assistant_message(messages, answer)
messages


La variable message contient tous les textes envoyés par l'utilisateur et l'assistant

# Prompt système

Pour indiquer à Claude qu'on ne souhaite pas la réponse directement à une question mais plutôt de nous donner le cheminement pour réussir à résoudre le problème. On va donner à claude des informations sous forme de prompt

Pour cela lors de l'envoie du contexte à Claude, on va ajouter un paramètre **system** lors de l'envoie à l'API Claude

> def chat(messages, system=None):
> 	params= {
> 		"model":model,
> 		"max_tokens":1000,
> 		"messages":messages,
> 		}
> 	if system:
		params["system"] = system
	message = client.messages.create(\*\*params)
	return message.content[0].text


Pour ne pas avoir la réponse mais orienter Claude pour qu'il se mette à la place d'un professeur de maths permettant de donner des indices pour répondre au problème
> messages = []
> add_user_message(messages, "How do i solve 5x+3=2 for x ?")
> system = """
> You are a patient math tutor
> Do not directly answer a student's questions.
> Guide them to a solution step by step
> """

> answer = chat(messages, system=system)
> answer


# Température

Lorsque Claude va générer sa réponse, avec pour chaque mot utilisé, analyser la probabilité du mot suivant et choisir le meilleur mot.

La température est comprise entre 0 et 1 et va influencer les probabilités du token du mot suivant

Plus la valeur de la température ira vers le 0, plus le token plus la probabilité la plus élevé sera choisie ce qui rendra la sélection du token déterministique (toujours la même réponse)
![[Screenshot 2026-08-06 at 16.53.59.png]]


Plus on augmente la température et plus la probabilité la plus forte diminue et plus les autres probabilités augmentent
![[Screenshot 2026-08-06 at 16.53.46.png]]

Plus on va vouloir des réponses précises et exact et plus la température sera basse, si au contraire on souhaite que Claude nous propose des solutions/créativité (braistorming, blague, ...) plus la température se rapprochera de 1

![[Screenshot 2026-08-06 at 16.59.33.png]]

La température sera ajoutée dans les paramètres d'envoie à Claude

> def chat(messages, system=None, temperature=1.0):
> 	params= {
> 		"model":model,
> 		"max_tokens":1000,
> 		"messages":messages,
> 		 "temperature": temperature
> 		}
> 	if system:
		params["system"] = system
	message = client.messages.create(\*\*params)
	return message.content[0].text

# Réponse streaming

Le temps de réponse de Claude peut être très long. Pour avoir une meilleure expérience utilisateur, on va utiliser une technique appelée streaming

Claude va découper la réponse en plusieurs réponses et ces réponses seront affichées au fur et à mesure jusqu'à ce que tout la réponse soit envooyée par Claude. L'utilisateur ne sera pas obligé d'attendre que Claude est entièrement construit la réponse.

Il faut ajouter dans les paramètres d'envoie, le paramètre stream=True

> message = []
> add_user_message(messages, "Write a 1 sentence desrcription of a fake database")
> stream = client.messages.create(
	model=model,
	max_tokens=1000,
	messages=messages,
	stream=True
	)
   for event in stream:
	print(event)

La réponse fera apparaître les différents messages envoyés par Claude pour répondre à la question

> **RawMessageStartEvent**(message=Message(id='msg_011CdmjF2fqTPMbxDpCtxjkm', container=None, content=[], model='claude-haiku-4-5-20251001', role='assistant', stop_details=None, stop_reason=None, stop_sequence=None, type='message', usage=Usage(cache_creation=CacheCreation(ephemeral_1h_input_tokens=0, ephemeral_5m_input_tokens=0), cache_creation_input_tokens=0, cache_read_input_tokens=0, inference_geo='not_available', input_tokens=55, output_tokens=1, output_tokens_details=None, server_tool_use=None, service_tier='standard')), type='message_start') 
> **RawContentBlockStartEvent**(content_block=TextBlock(citations=None, text='', type='text'), index=0, type='content_block_start') 
> **RawContentBlockDeltaEvent**(delta=TextDelta(text='#', type='text_delta'), index=0, type='content_block_delta') 
> **RawContentBlockDeltaEvent**(delta=TextDelta(text=' Movie Idea\nA brilliant hacker must infiltrate a high-security government facility to steal evidence of a conspiracy before the agency', type='text_delta'), index=0, type='content_block_delta') **RawContentBlockDeltaEvent**(delta=TextDelta(text=' erases it—and discovers the conspiracy goes much deeper than she imagined.\n\n# Fake', type='text_delta'), index=0, type='content_block_delta') 
> **RawContentBlockDeltaEvent**(delta=TextDelta(text=' Database Descriptions\n\n1. **MemoryVault** - A decentralized neural backup system that stores fragmented human', type='text_delta'), index=0, type='content_block_delta') 
> **RawContentBlockDeltaEvent**(delta=TextDelta(text=' memories in encrypted quantum nodes, allowing people to preserve and replay experiences from their lives.\n\n2. \*\*ChromaIndex', type='text_delta'), index=0, type='content_block_delta') 
> **RawContentBlockDeltaEvent**(delta=TextDelta(text='\*\* - A comprehensive database cataloging every color ever mixed by professional artists throughout history, complete with pigment composition', type='text_delta'), index=0, type='content_block_delta') **RawContentBlockDeltaEvent**(delta=TextDelta(text=', creation date, and the emotional context behind each shade.\n\n3. **EchoTrace** - An', type='text_delta'), index=0, type='content_block_delta') 
> **RawContentBlockDeltaEvent**(delta=TextDelta(text=' omniscient archive recording every conversation ever held in coffee shops across North America, indexed by mood', type='text_delta'), index=0, type='content_block_delta') 
> **RawContentBlockDeltaEvent**(delta=TextDelta(text=', topic, and the probability that life-changing decisions were made during each dialogue.', type='text_delta'), index=0, type='content_block_delta') 
> **RawContentBlockStopEvent**(index=0, type='content_block_stop') 
> **RawMessageDeltaEvent**(delta=Delta(container=None, stop_details=None, stop_reason='end_turn', stop_sequence=None), type='message_delta', usage=MessageDeltaUsage(cache_creation_input_tokens=0, cache_read_input_tokens=0, input_tokens=55, output_tokens=181, output_tokens_details=None, server_tool_use=None)) **RawMessageStopEvent**(type='message_stop')



![[Screenshot 2026-08-06 at 17.22.21.png]]

Pour ne pas avoir les messages mais le texte de la réponse, voici le code a implémenté:

> message = []
> add_user_message(messages, "Write a 1 sentence desrcription of a fake database")
> with client.messages.stream(
	model=model,
	max_tokens=1000,
	messages=messages
	) as stream:
		for text in stream.text_stream:
		print(text, end="")

Pour persister le message dans une base de données par exemple, on aura le code suivant

> message = []
> add_user_message(messages, "Write a 1 sentence desrcription of a fake database")
> with client.messages.stream(
	model=model,
	max_tokens=1000,
	messages=messages
	) as stream:
		for text in stream.text_stream:
		#print(text, end="")
		pass
   stream.get_final_message()

# Données structurées

Lorsque Claude retourne du code, par défaut il va ajouter des headers, des footers et des commentaires pour expliquer son raisonnement

> ```json 
> { "source": ["aws.ec2"], "detail-type": ["EC2 Instance State-change Notification"], "detail": { "state": ["running"] } } 
> ``` 
> This rule captures EC2 instance state changes when instances start running.

La plupart du temps, nous voulons le code sans les informations complémentaires de Claude



Pour ne récupérer que le code brut, nous allons utiliser deux techniques
* un message assistant prérempli: pour simuler à Claude qu'il nous a déjà envoyé ```json
> 
> { "source": ["aws.ec2"], "detail-type": ["EC2 Instance State-change Notification"], "detail": { "state": ["running"] } } 
> ``` 

Nous allons donc envoyer un message comme si c'était claude qui nous l'avait envoyé

> add_assistant_message(messages, "```json")

* une séquence de fin: pour indiquer à Claude la séquence qui indiquera que le message est terminé (sans include la séquence)

> { "source": ["aws.ec2"], "detail-type": ["EC2 Instance State-change Notification"], "detail": { "state": ["running"] } } 

Nous allons spécifier la séquence de fin en ajoutant la paramètre sequence_stop lors de l'envoi de la requête à claude


> text = chat(messages, stop_sequences=["```"])

> def chat(messages, system=None, temperature=1.0, stop_sequences=None):
	params= {-
		"model":model,
		"max_tokens":1000,
		"messages":messages,
	}

>.     if stop_sequences:
		params["stop_sequences"] = stop_sequences

>.    message = client.messages.create(**params)


# Evaluation prompt

L'évaluation du prompt nous permettra d'avoir des métriques sur l'efficacité de nos prompts.

pour évaluer un prompt, on peut
* le tester une fois
* le tester plusieurs fois
* exécuter le prompt dans un pipeline d'évaluation
  
La troisième est la meilleure des solutions pour évaluer à quel point le prompt est performant

## WorkFlow d'évaluation d'un prompt

Il y a plusieurs façons d'implémenter ce type de workflow

Nous allons écrire un prompt et définir des ensembles de données à mettre en entrée du prompt

Nous allons ensuite fournir à Claude le prompt et les données en entrée et voir comment Claude répond

Nous allons ensuite noter les réponses à chacune des questions puis en associant les entrées et les sorties de claude dans un évaluateur. L'évaluateur nous donnera une note pour chacune des réponses

Nous allons ensuite faire la moyenne des scores fournis par l'évaluateur

Nous pourrons ensuite modifier le prompt, répéter ces actions et voir l'évolution du score et estimer si nos modifications sont bonnes ou non

## Coder l'évalutation

Pour évaluer notre prompt, nous allons développer 3 fonctions:
* **run_prompt**: injecte un cas de test dans notre prompt
> def run_prompt(test_case): 
	  . """Merges the prompt and test case input, then returns the result""" 
	   prompt = f""" Please solve the following task: {test_case["task"]} """ 
	   messages = [] add_user_message(messages, prompt) 
	   output = chat(messages) return output
* **runt_test_case**: aggrège le résultat, le cas de test et le score. elle va appeler la fonction run_prompt
> def run_test_case(test_case): 
	"""Calls run_prompt, then grades the result""" 
	output = run_prompt(test_case) 
	# TODO - Grading 
	score = 10 
	return { "output": output, "test_case": test_case, "score": score }
* **run_eval**: Cette fonctionne est le point d'entrée du test et va faire une boucle sur chaque test en appelant la méthode run_test_case
> with open("dataset.json", "r") as f: 
	dataset = json.load(f) 
	results = run_eval(dataset)

## Model d'évalutation

Le résultat de l'évaluation peut être de la forme d'un nombre ou d'un booléen ... Mais la plupart du temps c'est un nombre entre 1 et 10

Il y a 3 différents types d'évaluateur:
* **code**:  Nous allons injecter le résultat à évaluer dans un bout de code selon des critères définis par le développeut. Par exemple nous assurer que la réponse n'est pas trop longue ou trop courte. Que la réponse a ou n'a pas certains mots, vérification de la syntaxe d'un json ...
* **Model**: Nous allons interroger un model via un nouvel appel API. Le prompt, lors de cet appel, va définir les critères d'évaluation
* **Humain**: Les résultats à évaluer sont communiquées à une personne humaine qui va donner une note sur l'exactitude des réponses. Ce travail demande beaucoup de travail et est fastidieux

Quelque soit le type d'évaluteur, il faut premièrement définir les critères d'évaluation
* **Format**: Est ce que la réponse contient le format attendu ?
* **Validation de la syntaxe**: Est ce que le code est correctement implémenté ?
* **Exécution de la tâche**: Est ce que le model a clairement répondu à la question ?

Pour évaluer via un model, il faut
* Envoyer la question posée
* Le résultat obtenu
* indiquer de fournir une évaluation sur les points forts, les points faibles et un résumé (sans ces informations, le model envera toujours la note de 6)
* demander de fournir un score entre 1 et 10

## Validateur de code

Le validateur de code va valider la syntaxe par rapport au langage souhaité avec un bout de code.

Pour chaque résultat, nous allons parse, charger, compiler ce dernier avec un interpréteur du langage choisi. Le résultat sera soit 0 en cas de lever d'exception, soit 10 si le résultat est effectué sans erreur

> def validate_json(text): 
> 	try: 
> 		json.loads(text.strip()) 
> 		return 10 
> 	except json.JSONDecodeError: 
> 		return 0 
> def validate_python(text): 
> 	try: 
> 		ast.parse(text.strip()) 
> 		return 10 
> 	except SyntaxError: 
> 		return 0 
> def validate_regex(text): 
> 	try: 
> 		re.compile(text.strip()) 
> 		return 10 
> 	except re.error: 
> 		return 0

# Techniques d'ingénéries des prompts

Les techniques d'ingénéries des prompts ont pour but d'améliorer son prompt avec un meilleur score moyen lors de la phase d'évaluation

## Claire et direct
La première ligne du prompt est la plus importante. Avec un verbe d'action, il faut indiquer à Claude ce qu'on souhaite 
**Claire**:
* Utiliser un langage simple
* précisez ce qu'on veut
* 

**Directe**:
* Utilisez des instructions et pas des questions
* Utiliser des verbes d'action

## Spécifique

Donnez des directives pour indiquer à Claude, les contraintes, comment arriver aux résultats souhaités ...

Il y a deux types de directives à mettre dans les prompts:
* Lister les contraintes, qualités ... que doivent avoir le résultat
* Lister les actions que Claude doit faire pour arriver au résultat

On peut mélanger ces deux types avec une liste d'attributs à respecter et ensuite une liste d'étapes que le modèle devra suivre

## Tag xml

On va structurer notre prompt avec des tags xml afin de séparer et permettre à Claude d'identifier les parties communes sans ambiguité

Le nom des balises xml n'est pas normé, on peut mettre ce qu'on veut mais surtout il faut qu'il est une signification.

On n'est pas obligé d'avoir une structure full xml mais cela peut permettre d'identifier des bouts de code, du texte à améliorer ...

# Fournir des exemples

Fournir des exemples et l'une des plus puissantes techniques d'amélioration d'un prompt

Ces exemples ne sont pas seulement là pour donner des exemples de succès et d'échec mais également pour expliquer à Claude certains cas complexes (reconnaître une phrase cacarstique) qui nécessiteraient une réflexion plus approdondie.

Donner le format attendu dans les exemples va permettre à Claude de respecter ce format et éviter des aller-retours avec un prompt corrigé pour obtenir le résultat qu'on avait en tête

# Tools

Par défaut, Claude n'a accès qu'aux données avec lesquelles il a été entrainées.

## Tools function

nous allons déclarer une tools function qui sera appelée automatiquement par Claude, lorsqu'il le jugera utile

Tips:
* Bien nommer les arguments et les descriptifs de la fonction
* validation des paramètres d'entrée et levé une exception si queleuqchose ne va pas avec la valeur. Ce message d'erreur devra être pertinent par rapport à l'erreur afin que si Claude rappelle la fonction, il puisse corriger l'erreur

## Tools schema

Pour que Claude prenne connaissance de l'outils, nous allons écrire et lui envoyer un schéma json décrivant l'outils.

Un json est allors créé pour que Claude puisse utiliser l'outils
* **Nom**: Nom de l'outils afin de l'appeler
* **Description**: Description de l'outils permettant à Claude de comprendre quand l'appeler et ce que l'outils va renvoyer
> bonne pratique: Avoir une description de 3-4 phrases
* **Input_schema**: objet représentant le schéma de l'outils (contrat d'interface) et qui décrira les arguments passés à la fonction et les contraintes associées à ces arguments

Le schéma json est une spécification de validation de données. C'est donc un ensemble de règle qui peuvent être utilisées pour valider n'importe quel type de données json (pas spécifique à l'IA)

**Astuce pour écrire le schéma json**: demander à claude décrire le schéma json à partir du code python et en lui disant de suivre les bonnes pratiques de la documentation anthropic sur l'appel des tools: https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview

Ensuite, il faut copier ce que Claude a généré et le mettre dans une variable python. Le nom de cette variable sera le nom de la function + "_schema"

La variable utilisera l'objet ToolParam d'anthropic

> from anthropic.types import ToolParam

> def get_current_datetime(date_format="%Y-%m-%d %H:%M:%S"):
	if not date_format:
		raise ValueError("date_format cannot be empty")
	return datetime.now().strftime(date_format)

> get_current_datetime_schema = ToolParam({
	"name": "get_current_datetime",
	"description": "Returns the current local date and time as a formatted string. Use this tool whenever the user asks what the current date, time, day of the week, or timestamp is, or when a task needs to reference 'now' (e.g., logging, scheduling, or computing relative dates). Do not use this tool to parse, convert, or manipulate a date/time that the user has already provided — it only returns the current moment. The output is formatted using Python's strftime directives via the optional 'date_format' parameter; if omitted, it defaults to 'YYYY-MM-DD HH:MM:SS' format. This tool reflects the server's local system clock and does not accept or return timezone information, so results may not match the user's own timezone.",
	"input_schema": {
		"type": "object",
		"properties": {
			"date_format": {
				"type": "string",
				"description": "A strftime-compatible format string controlling how the current datetime is rendered. Must be a non-empty string, since an empty value raises an error. Common directives: %Y (4-digit year), %m (2-digit month), %d (2-digit day), %H (24-hour), %M (minute), %S (second), %B (full month name), %A (full weekday name). Defaults to '%Y-%m-%d %H:%M:%S' if not provided.",
				"minLength": 1,
				"default": "%Y-%m-%d %H:%M:%S"
			}
		},
		"required": []
	},
	"input_examples": [
		{},
		{ "date_format": "%Y-%m-%d" },
		{ "date_format": "%B %d, %Y at %I:%M %p" }
	]
   })

En utilisant, un tool, Claude renverra dans sa réponse deux block:
* le bloc texte habituel qui indiquera la réponse de Claide
* ToolUseBlock indiquant que Claude aimerait utiliser un outils en spécifiant
	* nom de l'outils
	* arguments de l'outils
	  
	  
Pour rappel, Claude n'a pas de mémoire de la conversation, il faut lui rappeler à chaque requête. Il ne faut donc pas oublier de mettre les deux blocs dans l'historique des réponses

Pour autoriser, l'utilisation d'un outils, nous allons envoyé un message à Claude avec un bloc **ToolResultBlock** qui contiendra le résultat de l'exécution de l'outils

Ce bloc contiendra:
* **tool_use_id**: correlation id entre la requête et la réponse
* **type**: "tool_result"
* **content**: résultat de l'outils/fonction (String)
* **is_error**: booléen

L'échange avec Claude va contenir les étapes suivantes:
* message du user pour poser une question. Ce message contient la liste des schemas des outils disponibles
* Claude répond avec un message assistantqu'il ne peut pas répondre pas répondre sans appeler un outils spécifié dans la liste avec un bloc ToolUseBlock
* Le serveur interroge l'outils
* Un message du user est envoyé avec les deux précédents messages et un troisième message contenant un bloc ToolResultBlock contenant le résultat de l'outils

## Multi-turn conversation with tools

Une requête de l'utilisateur pourra demander à Claude de faire appels à plusieurs outils. Dès que Claude va se heurter à un problème qu'il ne pourra pas résoudre de lui même, il demandera l'utilisation d'un outils. Lorsqu'il aura la réponse, il analysera de nouveau la requête et fera une boucle jusqu'à ce qu'il aura toutes les infos pour répondre à la question.

Si Claude ne demande pas à utiliser un outils, c'est qu'il a répondu à la question avec une réponse finale.

## Raison d'arrêt de Claude

Claude enverra la raison de l'arrêt de son travail:
* **tool_use**: Claude a besoin d'un outils
* **end_turn**: Claude a répondu à la question
* **max_tokens**: Claude a atteint la limite du nombre max de token
* **stop_sequence**: Claude a rencontré une séquence de fin, paramètre lors de l'envoie du message

## Fine-Grained Tool calling

L'évènement InputJsonEvent est utilisé lorsque le streaming est activé.
Cet objet contiendra deux propriétés importantes:
* un json partiel qui sera un morceau de la réponse
* un snapshot: une somme cumulative de tous les morceaux

Pour être sûr que Claude n'envoie pas un json invalide, Claude ne va pas envoyer morceau par morceau. L'api va passer par une étape de validation qui va vérifier que le json est valide.

Claude va attendre dans la réponse un guillemet fermant dans la valeur de l'ensemble clé -valeur fur format json. Claude saura donc que le couple clé-valeur est valide. Tout le bloc clé-valeur sera envoyé à notre serveur.

Même avec du streaming, il peut y avoir des délais d'affichage car les blocs sont mis en mémoire tampon dans l'API et lorsque ces blocs sont validés, ils sont envoyés au serveur. Ce qui peut avoir comme conséquence d'avoir un gros bloc de texte qui apparaît d'un coup.

Pour éviter ce délai, il y a la fonctionnalité **Fine-Grained Tool Calling**. Cette fonctionnalité désactive l'étape de validation JSON. Claude généreré quelques blocs et les enverra immédiatement. Cette fonctionnalité ressemble à ce que l'on voit dans les chats classiques.

Il faut donc implémenter une gestion d'erreur côté serveur dans le cas où le json envoyé par Claude n'est pas valide.

## Outils éditeur de texte

Claude a accès à un outils d'éditeur de texte par défaut. Il est intégré directement à Claude. Cet outils permet à Claude d'ouvrir des fichiers, des répertoires et de lire leur contenu. Il peut remplacer du texte, il faut crééer un fichier ...

Pour rappel, si on veut utiliser un outils, il faut
* déclarer le schéma (contrat d'api)
* écrire une fonction qui implémentera les fonctionnalités de l'outils

Pour l'outils d'éditeur de texte, Claude n'a que le schéma. C'est dans ce shcéma que Claude peut savoir comment utiliser l'outils et quelles sont les fonctionnalités associées à l'outils

C'est au développeur d'écrire les fonctions d'implémentation des fonctionnalités présentes dans le schéma

Le nom du schéma de l'outils éditeur de texte va contenir une date. Cette date est différente selon la version du model utilisé (claude-3-7-sonnet vs claude-3_5_sonnet).

Grâce à ce nom de schéma, Claude peut récupérer le schéma complet de l'outils éditeur de texte.

## Outils de recherche web

Un autre outils est intégré initialement à Claude, c'est l'outils de recherche internet mais pas besoin de fournir une implémentation contrairement à l'outils d'édition des fichiers.

Il faut déclarer le schéma:

> web_search_schema = {
> 	"type": "web_search_20250305",
> 	"name": "web_search",
> 	"max_uses": 5
> }

Le paramètre max_uses va correspondre au nombre de fois où Claude va pouvoir faire une recherche. Après la première recherche et selon les résultats, Claude peut décider de faire d'autres recherches.

Claude va utiliser des objets **ServerToolUseBlock** contenant la requête que Claude va utiliser pour faire la recherche sur internet.

L'objet **WebSearchToolResultBlock** permet de stocker une liste de **WebSearchResultBloc** contenant le résultat de la recherche de Claude avec les paramètres suivants:
* **Title**: titre de la page trouvée
* **type**: "web_serach_result"
* **url**: url de la page trouvée

Ensuite Claude va répondre à la question en se basant sur le résultat de ses recherches avec une liste **citations** contenant des objets **CitationsWebSearchResultLocation**. Ces citations vont étayés ce que Claude va répondre.

Comme une recherche web faite par un utilisateur, il faut deméler les bonnes et les mauvaises informations. Pour ne rechercher que sur certains sites, on va pouvoir ajouter le paramètre **allowed_domains** et indiquer une liste de domaine. Claude ne fera des recherches que sur les sites associés à ces domaines.

# Rag et les recherches agentiques

RAG = Retrieval Augmented Generation

Pour les documents trop volumineux et pour éviter des temps de traitement trop long et de coût trop élevé, nous allons utiliser un RAG

Un RAG permet d'extraire tout le texte du document et de le diviser en plusieurs morceaux.

Lorsque l'utilisateur posra une question, Claude trouvera le morceau le plus pertinent par rapport à la demande.

## Séparation du document

La façon séparation en morceaua un impact sur la qualité de la réponse de Claude.

La division en plusieurs morceaux peut se faire grâce à 3  principales:
* **découpage par taille**: séparation en plusieurs morceaux de longueur indentique (technique la plus simple et la plus utilisée). L'inconvénient majeur c'est que des mots seront coupés  entre deux morceaux et il peut y avoir un manque de contexte entre plusieurs morceaux. Pour corriger ce problème, 
* **découpage par structure**: Division en fonction de la structure du document (titre, paragraphe, sections générales ...) et les utiliser comme lignes de division. C'est facile sur des documents markdown, c'est plus compliquer d'identifier les sections sur un fichier pdf. L'utilisation de cette stratégie dépend du type de document à ingérer.
* **découpage par sémantique**: Cette stratégier permet de découper en section/phrase et ensuite d'identifier si les morceaux suivants ont un rapport entre eux

on va utiliser une stratégie de chevauchement permettant de prendre une partie du morceau précédent et du morceau suivant (duplication de texte)

## Text embeddings

Pour faire le lien entre le découpage du RAG et la recherche demandée par l'utilisateur. Claude va utiliser la recherche sémantique

La recherche sémantique utilise des text embeddings. 

Les text embeddings sont des représentations numériques du contenu du texte. Ces text embeddings sont générés par un modèle d'embedding.

Le model d'embedding va transformer chaque phrase en une longue liste de nombres. Ces nombres peuvent varier de -1  à +1. 

Les nombres représentent un score d'une certaine qualité du texte d'entrée mais sans savoir vraiment ce que représente ces nombres.

Claude ne génère pas ces nombres maus utilise Voyage IA.

C'est une société distincte qui nécessite un compte et une clé d'API. Pour le moment c'est gratuit et facile à utiliser.  

Mettre la clé d'api dans le fichier .env
> VOYAGE_API_KEY="pa-sopdqzrFUuWeBIQSxf5wzFbqP3puV8uogMtHYHSuFSn"

## Pipeline RAG

Les étapes sont:
* segmentation du texte en morceaux de texte
* génération des embeddings: pour chacun des différents morceaux, il y a une transformation en une liste de nombre correspondant à des scores issues de Voyage IA
* étape mathématique de normalisation: léger ajustement de chaque nombre
* stockage des valeurs dans une base de données vectorielle: Base de données spécialisée dans la recherche, la comparaison de longues listes de nombres
Lorsqu'un utilisateur va poser une question
* La question va également être traité par un model Embedding et transformer la question en une liste de nombre compris entre -1 et 1
* Passage par l'étape de normalisation
* rechercher dans la bdd vectorielle avec les vecteurs de la demande et voir quel est le vecteur le plus proche

## Recherche lexicale

Une recherche lexicale qui va comparer les mots de la recherche avec les mots persistés dans la base de données et regarder le nombre d'occurence peut affiner pour avoir un meilleur résultat

On peut utiliser BM24 (Best match 25th)

Les mots avec beaucoup d'occurences sont considérés comme moins important

## Merge des deux recherches

La recherche sémantique et la recherche lexicale ont les mêmes noms de fonction dans leurs api
* add_document()
* search()

Un classe appelée Retriever va recevoir la question de l'utilisateur et va la transmettre aux deux types de recherche. Le retriever recevra les deux types de réponse et fusionnera les résultats

Le retriever va récupérer les résultats et leur rang.

# Fonctionnalités Claude

## Pensée étendue

La pensée étendue permet à Claude de réfléchir à la question avant de donner une réponse finale.

Ce qui correspond à la configuration extended du chat de Claude

Lors la pensée étendue est activée, la réponse Contiendra deux blocs:
* **Text Block**: habituel et contenant la réponse de Claude
* **Thinking Block** 

Le Thinking bloc contient les champs suivants:
* **signature**: jeton crytographique, ce jeton est utilisé pour vérifier qu'on n'a pas modifié le texte du Thinking Block lors de l'envoi d'une nouvelle requête, contenant l'historique des requêtes précédentes, à Claude
* **type**: "thinking"
* **thinking**: Raisonnement étape par étape

Un autre objet Thinking Block  contenant un type **redacted_thinking**, indiquant que la réponse a été généré par un système de sécurité interne. La raisonnement est caché et c'est le mode par défaut des IA. Cet objet contient un champ data contenant les données de la pensée étendue cryptées. En le renvoyant à Claude lors de messages suivants, Claude pourra analyser le raisonnement utilisé dans les anciennes requêtes.

Pour activer le mode étendu, nous allons ajouter à notre envoi à Claude un paramètre **thinking** contenant deux sous-paramètres:
* **type**: "enabled" si le mode extendu est activé
* **budget_tokens**: le nombre de token max à utiliser lors de l'utilisation du mode étendu. 1024 est le minimum des token à utiliser
  
Le paramètre **max_token** doit être supérieur au paramètre **thinking_budget**. La différence entre les deux paramètres sera le nombre de token utilissé par Claude pour formuler sa réponse

> def chat(
	messages,
	system=None,
	temperature=1.0,
	stop_sequences=[],
	tools=None,
	thinking=False,
	thinking_budget=1024
	):
	params = {
		"model": model,
		"max_tokens": 4000,
		"messages": messages,
		"temperature": temperature,
		"stop_sequences": stop_sequences,
	}

>	if thinking:
		params["thinking"] = {
		"type": "enabled",
		"budget_tokens": thinking_budget
	}
	...

## Inclure des images

L'utilisateur peut envoyer jusqu'à 100 images par requête
Il y a également d'autres limitations:
* poids: 5MB max
* Hauteur et Largeur: 8000 pixels max pour une seule image
* Hauteur et Largeur: 2000 pixels max pour plusieurs images

Plus l'image contiendra de pixel et plus Claude consommera de token avec la formulte suivante:
(width px x height px) / 750

Pour insérer une image, on va utiliser un nouveau type de bloc: **ImageBlock**
On peut fournir l'image en base64 ou l'url 

Pour poser une question sur une image, il ne faut pas utiliser un prompt simple mais utiliser les techniques de prompt précédement vu comme la définition des étapes de traitement
