
# installation

installation native dévloppée par anthropic: maj native an arrière plan (maj tous les jours)
/doctor: permet de voir sur quelle installation on est

# customize ton claude
/theme: theme de couleur
/color: invite de commande
/statusline: ce qu'on veut voir afficher en dessous de la barre de prompt

# /tui / focus

* / tui default: aller retour rapide
* /tui fullscreen: session qui va durer avec beaucoup d'interaction avec l'IA
* /focus: suppression des messages intermédiaires de claude (thinking ...) => uniquement prompt initial et la réponse de claude

# /rename

Permet de nommer la session, sans cela Claude générera un nom (pas frocément adapté)

Utile pour retrouver une session avec la commande /resume (liste des sessions)
Par défaut les sessions sont gardés 30jours ajustable par cleanupPeriodDay
# /powerup

un parcours intéractif intégré (tutoriel)

# accélérer saisie avec ! et @

!: exécuter un bash
@: référence à un fichier
injecte le fichier dans le contexte
auto completion
Résultat plus précis, moins de token

# Démarrer depuis la racine

aperçu golabal
structure
dépendance
fichiers de conf

/init: claude génére sa propre feuille de route
génére un CLAUDE.md

# Combinez CLAUDE.MD et mémoire automatique

Pas de fichier cluade.md de plus de 200 lignes
Maj avec les solutions techniques importantes

Mémoire automatique, qui est géré par Calude
Se construire automatiquement via vos corrections
Ne duplique pas le contenu de CLAUDE.MD

/memory: consultation et modification de la mémoire

# Maitriser la gestion du contexte

session
* Mes messages
* Réponse agent
* fichiers lus
* résultats exécutés

/compact: compresser l'historique et libérer de l'espace sans repartir de 0
/compact mais garde l'api développé et supprime les explorations

/clear: reset du contexte

# /context

Visualisation du contexte
Comprendre ce que Claude voit
Identifiant les éléments manques et inutiles

# Structurer promp efficadement
* exeplice sur le format attendu
* fournir le contexte et le pourquoi
* step by step
* inclure des exemples
* formuler en positif
* définir un critère de validation

* /voice: dicter le prompt (rester appuyer sur space)

# Commencer en mode plan

Shift + tab: aller dans les modes

Ne pas approuver le plan à la première revue

Shift + tab en mode accepte

# utiliser un modele IA adapté à votre besoin

Haiku: rapide
Sonnet: quotidien
Sonnet AM: gros fichier / longues sessions
Opus, Fab: Taches avancées

/model Haiku

# Ajuster l'effort

/effort: controler la profondeur du raisonnement du modèkle

# Echap et double escape

Raccourci clavier

echap / echapt: rewind

# Screenshot
Cmd+shift/control/4

# Utiliser /fork

Création de la session courante dans une autre session
/branch

/resume: revenir en arrière

# inspecter et maitriser les sorties

Garder la main sur l'output

# Review
/code-review skill 
/review: lancer une review d'une PR en local
/ultrareview review chez anthropic
/simplify: 3 agents en parralèlles

# /insight

Génère un rapport analysant vos sessions (après plusieurs mois)