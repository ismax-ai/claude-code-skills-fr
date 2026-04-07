---
name: ralph
description: "La Boucle Infinie Ralph. Workflow autonome en 3 phases (specs, planification, build) avec boucle bash externe et contexte frais a chaque iteration. Playbook complet traduit integralement en francais."
---

> Concept original : [Geoffrey Huntley](https://ghuntley.com/ralph/). Playbook : [ClaytonFarr/ralph-playbook](https://github.com/ClaytonFarr/ralph-playbook). Traduit integralement en francais.

# Le Playbook Ralph

En decembre 2025, la petite tete de [Ralph](https://ghuntley.com/ralph/), puissante mais volontairement simpliste, a emerge au sommet de la plupart des timelines liees a l'IA.

J'essaie de suivre les insights brillants que [@GeoffreyHuntley](https://x.com/GeoffreyHuntley) partage, mais je ne peux pas dire que Ralph m'avait vraiment parle cet ete. Maintenant, tout le buzz recent rend le sujet impossible a ignorer.

Les vues d'ensemble de [@mattpocockuk](https://x.com/mattpocockuk/status/2008200878633931247) et [@ryancarson](https://x.com/ryancarson/status/2008548371712135632) ont beaucoup aide, jusqu'a ce que Geoff arrive et [dise 'nah'](https://x.com/GeoffreyHuntley/status/2008731415312236984).

## Quelle est la facon optimale de faire du Ralph ?

Beaucoup de gens semblent obtenir de bons resultats avec differentes variantes, mais je voulais lire entre les lignes aussi attentivement que possible de la part de celui qui non seulement a capture cette approche mais a aussi passe le plus de temps a la mettre en pratique.

Alors j'ai creuse pour vraiment _RTFM_ sur les [videos recentes](https://www.youtube.com/watch?v=O2bBWDoxO4s) et le [post original](https://ghuntley.com/ralph/) de Geoff pour demeler par moi-meme ce qui fonctionne le mieux.

Ci-dessous le resultat : un Playbook Ralph (probablement alimente par du TOC) qui organise les details divers pour mettre tout ca en pratique sans, esperons-le, le castrer dans le processus.

> Creuser tout ca m'a aussi fait penser a des [ameliorations supplementaires](#ameliorations) potentiellement precieuses a l'approche de base qui visent a rester alignees avec les principes qui font si bien fonctionner Ralph.

J'espere que ca vous aide. -- [@ClaytonFarr](https://x.com/ClaytonFarr)

---

## Table des matieres

- [Workflow](#workflow)
- [Principes cles](#principes-cles)
- [Mecanique de la boucle](#mecanique-de-la-boucle)
- [Licence](#licence)
- [Fichiers](#fichiers)
- [Ameliorations](#ameliorations)

---

## Workflow

Une image vaut mille tweets et une video d'une heure. La [vue d'ensemble de Geoff ici](https://ghuntley.com/ralph/) (inscription a sa newsletter pour voir l'article complet) a vraiment aide a clarifier les details du workflow pour passer de 1) idee a 2) specs individuelles alignees JTBD a 3) plan d'implementation complet a 4) boucles de travail Ralph.

### Trois Phases, Deux Prompts, Une Boucle

Ce diagramme m'a clarifie que Ralph n'est pas juste "une boucle qui code". C'est un entonnoir avec 3 Phases, 2 Prompts et 1 Boucle.

#### Phase 1. Definir les exigences (conversation LLM)

- Discuter des idees de projet, identifier les Jobs to Be Done (JTBD)
- Decouper chaque JTBD en sujet(s) de preoccupation (topic of concern)
- Utiliser des subagents pour charger des infos depuis des URLs dans le contexte
- Le LLM comprend le sujet de preoccupation JTBD : un subagent ecrit `specs/FICHIER.md` pour chaque sujet

#### Phase 2 / 3. Lancer la boucle Ralph (deux modes, changer `PROMPT.md` selon le besoin)

Meme mecanisme de boucle, prompts differents pour des objectifs differents :

| Mode           | Quand l'utiliser                                    | Focus du prompt                                                      |
| -------------- | --------------------------------------------------- | -------------------------------------------------------------------- |
| _PLANIFICATION_ | Pas de plan, ou plan obsolete/incorrect            | Generer/mettre a jour `IMPLEMENTATION_PLAN.md` uniquement            |
| _CONSTRUCTION_  | Le plan existe                                      | Implementer depuis le plan, commit, mettre a jour le plan en effet secondaire |

_Differences de prompt par mode :_

- Le prompt 'PLANIFICATION' fait une analyse des ecarts (specs vs code) et produit une liste TODO priorisee, pas d'implementation, pas de commits.
- Le prompt 'CONSTRUCTION' suppose que le plan existe, choisit des taches dedans, implemente, lance les tests (backpressure), commit.

_Pourquoi utiliser la boucle pour les deux modes ?_

- La CONSTRUCTION le necessite : inheremment iteratif (beaucoup de taches x contexte frais = isolation)
- La PLANIFICATION l'utilise par coherence : meme modele d'execution, bien que souvent completee en 1-2 iterations
- Flexibilite : si le plan a besoin de raffinement, la boucle permet plusieurs passes lisant sa propre sortie
- Simplicite : un seul mecanisme pour tout ; E/S fichiers propres ; arret/redemarrage facile

_Contexte charge a chaque iteration :_ `PROMPT.md` + `AGENTS.md`

_Cycle de vie de la boucle en mode PLANIFICATION :_

1. Les subagents etudient `specs/*` et le `/src` existant
2. Comparent les specs au code (analyse des ecarts)
3. Creent/mettent a jour `IMPLEMENTATION_PLAN.md` avec les taches priorisees
4. Pas d'implementation

_Cycle de vie de la boucle en mode CONSTRUCTION :_

1. _Orienter_ -- les subagents etudient `specs/*` (exigences)
2. _Lire le plan_ -- etudier `IMPLEMENTATION_PLAN.md`
3. _Selectionner_ -- choisir la tache la plus importante
4. _Investiguer_ -- les subagents etudient le `/src` pertinent ("ne pas supposer que ce n'est pas implemente")
5. _Implementer_ -- N subagents pour les operations fichiers
6. _Valider_ -- 1 subagent pour build/tests (backpressure)
7. _Mettre a jour `IMPLEMENTATION_PLAN.md`_ -- marquer la tache terminee, noter les decouvertes/bugs
8. _Mettre a jour `AGENTS.md`_ -- si apprentissages operationnels
9. _Commit_
10. _Fin de boucle_ -> contexte efface -> iteration suivante demarre a neuf

#### Concepts

| Terme                          | Definition                                                             |
| ------------------------------ | ---------------------------------------------------------------------- |
| _Job to be Done (JTBD)_        | Besoin ou resultat attendu de haut niveau                              |
| _Sujet de preoccupation_       | Un aspect/composant distinct au sein d'un JTBD                         |
| _Spec_                         | Document d'exigences pour un sujet de preoccupation (`specs/FICHIER.md`) |
| _Tache_                        | Unite de travail derivee de la comparaison specs vs code               |

_Relations :_

- 1 JTBD -> plusieurs sujets de preoccupation
- 1 sujet de preoccupation -> 1 spec
- 1 spec -> plusieurs taches (les specs sont plus larges que les taches)

_Exemple :_

- JTBD : "Aider les designers a creer des moodboards"
- Sujets : collecte d'images, extraction de couleurs, mise en page, partage
- Chaque sujet -> un fichier spec
- Chaque spec -> beaucoup de taches dans le plan d'implementation

_Test de portee du sujet : "Une phrase sans 'Et'"_

- Pouvez-vous decrire le sujet de preoccupation en une phrase sans conjoindre des capacites non liees ?
  - OK : "Le systeme d'extraction de couleurs analyse les images pour identifier les couleurs dominantes"
  - PAS OK : "Le systeme utilisateur gere l'authentification, les profils et la facturation" -> 3 sujets
- Si vous avez besoin de "et" pour decrire ce que ca fait, c'est probablement plusieurs sujets

---

## Principes cles

### Le contexte est _TOUT_

- Quand 200K+ tokens annonces = ~176K reellement utilisables
- Et 40-60% d'utilisation du contexte pour la "zone intelligente"
- Taches serrees + 1 tache par boucle = _100% d'utilisation du contexte en zone intelligente_

Ca informe et pilote tout le reste :

- _Utiliser l'agent principal/contexte comme ordonnanceur_
  - Ne pas allouer du travail couteux au contexte principal ; deleguer aux subagents a chaque fois que possible
- _Utiliser les subagents comme extension memoire_
  - Chaque subagent dispose de ~156kb qui est ramasse par le garbage collector
  - Distribuer pour eviter de polluer le contexte principal
- _La simplicite et la brievete gagnent_
  - S'applique au nombre de parties du systeme, a la config de la boucle et au contenu
  - Des entrees verbeuses degradent le determinisme
- _Preferer le Markdown au JSON_
  - Pour definir et suivre le travail, pour une meilleure efficacite en tokens

### Piloter Ralph : Patterns + Backpressure

Creer les bons signaux et portes pour piloter le succes de Ralph est **critique**. Vous pouvez piloter depuis deux directions :

- _Piloter en amont_
  - Assurer un setup deterministe :
    - Allouer les ~5 000 premiers tokens aux specs
    - Le contexte de chaque boucle est alloue avec les memes fichiers pour que le modele parte d'un etat connu (`PROMPT.md` + `AGENTS.md`)
  - Votre code existant influence ce qui est utilise et genere
  - Si Ralph genere de mauvais patterns, ajoutez/mettez a jour des utilitaires et patterns de code existants pour le guider vers les bons
- _Piloter en aval_
  - Creer de la backpressure via les tests, typechecks, lints, builds, etc. qui vont rejeter le travail invalide/inacceptable
  - Le prompt dit "lancer les tests" de facon generique. `AGENTS.md` specifie les commandes reelles pour rendre la backpressure specifique au projet
  - La backpressure peut aller au-dela de la validation du code : certains criteres d'acceptation resistent aux verifications programmatiques -- qualite creative, esthetique, ressenti UX. Les tests LLM-comme-juge peuvent fournir de la backpressure pour des criteres subjectifs avec un pass/fail binaire. ([Reflexions plus detaillees ci-dessous](#backpressure-non-deterministe) sur comment approcher ca avec Ralph.)
- _Rappeler a Ralph de creer/utiliser la backpressure_
  - Rappeler a Ralph d'utiliser la backpressure lors de l'implementation : "Important : quand tu rediges de la documentation, capture le pourquoi -- tests et importance de l'implementation."

### Laisser Ralph faire du Ralph

L'efficacite de Ralph vient de combien vous lui faites confiance pour faire la bonne chose (eventuellement) et favorisez sa capacite a le faire.

- _Laisser Ralph faire du Ralph_
  - S'appuyer sur la capacite du LLM a s'auto-identifier, s'auto-corriger et s'auto-ameliorer
  - S'applique au plan d'implementation, a la definition et priorisation des taches
  - Consistance eventuelle atteinte par l'iteration
- _Utiliser des protections_
  - Pour operer de maniere autonome, Ralph necessite `--dangerously-skip-permissions` -- demander une approbation a chaque appel d'outil casserait la boucle. Ca contourne entierement le systeme de permissions de Claude -- donc une sandbox devient votre seule frontiere de securite.
  - Philosophie : "La question n'est pas si ca va etre compromis, mais quand. Et quel est le rayon de l'explosion ?"
  - Tourner sans sandbox expose vos identifiants, cookies navigateur, cles SSH et tokens d'acces sur votre machine
  - Tourner dans des environnements isoles avec un acces minimum viable :
    - Uniquement les cles API et cles de deploiement necessaires a la tache
    - Pas d'acces aux donnees privees au-dela des besoins
    - Restreindre la connectivite reseau quand possible
  - Options : sandboxes Docker (local), Fly Sprites/E2B/etc. (remote/production) -- [notes supplementaires](#environnements-sandbox)
  - Sorties de secours supplementaires : Ctrl+C arrete la boucle ; `git reset --hard` annule les changements non commites ; regenerer le plan si la trajectoire part mal

### Se mettre a l'exterieur de la boucle

Pour tirer le maximum de Ralph, il faut se mettre hors de son chemin. Ralph devrait faire _tout_ le travail, y compris decider quelle tache planifiee implementer ensuite et comment l'implementer. Votre job est maintenant de vous asseoir SUR la boucle, pas DEDANS -- d'ingenierer le setup et l'environnement qui permettront a Ralph de reussir.

_Observer et corriger le cap_ -- surtout au debut, asseyez-vous et regardez. Quels patterns emergent ? Ou Ralph se trompe-t-il ? Quels signes lui faut-il ? Les prompts avec lesquels vous commencez ne seront pas ceux avec lesquels vous finirez -- ils evoluent a travers les patterns d'echec observes.

_Accorder comme une guitare_ -- au lieu de tout prescrire d'emblee, observer et ajuster de facon reactive. Quand Ralph echoue d'une certaine facon, ajoutez un signe pour l'aider la prochaine fois.

Mais les signes ne sont pas juste du texte de prompt. Ce sont _n'importe quoi_ que Ralph peut decouvrir :

- Garde-fous du prompt -- instructions explicites comme "ne pas supposer que ce n'est pas implemente"
- `AGENTS.md` -- apprentissages operationnels sur comment builder/tester
- Utilitaires dans votre codebase -- quand vous ajoutez un pattern, Ralph le decouvre et le suit
- Autres entrees pertinentes et decouvrables...

> **Conseil :**
>
> 1. essayer de commencer avec _rien_ dans `AGENTS.md` (fichier vide ; pas de _bonnes pratiques_, etc.)
> 2. tester ponctuellement les actions desirees, trouver les erreurs ([exemple de walkthrough de Geoff](https://x.com/ClaytonFarr/status/2010780371542241508))
> 3. observer les boucles initiales, voir ou des lacunes apparaissent
> 4. ajuster le comportement _uniquement si necessaire_, via des mises a jour AGENTS et/ou des patterns de code (utilitaires partages, etc.)

Et rappelez-vous, _le plan est jetable :_

- S'il est faux, jetez-le et recommencez
- Le cout de regeneration est une boucle de Planification ; peu couteux par rapport a Ralph qui tourne en rond
- Regenerer quand :
  - Ralph part dans la mauvaise direction (implemente les mauvaises choses, duplique le travail)
  - Le plan semble obsolete ou ne correspond plus a l'etat actuel
  - Trop d'encombrement d'elements completes
  - Vous avez fait des changements significatifs aux specs
  - Vous etes confus sur ce qui est reellement fait

---

## Mecanique de la boucle

### I. Selection de tache

`loop.sh` agit en effet comme une 'boucle externe' ou chaque boucle = une seule tache (dans des sessions separees). Quand la tache est completee, `loop.sh` lance une session fraiche pour selectionner la tache suivante, si des taches restantes sont disponibles.

La forme minimale initiale du script `loop.sh` de Geoff :

```bash
while :; do cat PROMPT.md | claude ; done
```

_Note :_ La meme approche peut etre utilisee avec d'autres CLIs ; par ex. `amp`, `codex`, `opencode`, etc.

_Qu'est-ce qui controle la continuation des taches ?_

Le mecanisme de continuation est elegamment simple :

1. _La boucle bash tourne_ -> alimente `PROMPT.md` a claude
2. _PROMPT.md instruit_ -> "Etudie IMPLEMENTATION_PLAN.md et choisis la chose la plus importante..."
3. _L'agent complete une tache_ -> met a jour IMPLEMENTATION_PLAN.md sur disque, commit, sort
4. _La boucle bash redemarre immediatement_ -> fenetre de contexte fraiche
5. _L'agent lit le plan mis a jour_ -> choisit la prochaine chose la plus importante...

_Insight cle :_ Le fichier IMPLEMENTATION_PLAN.md persiste sur disque entre les iterations et agit comme etat partage entre des executions de boucle autrement isolees. Chaque iteration charge de facon deterministe les memes fichiers (`PROMPT.md` + `AGENTS.md` + `specs/*`) et lit l'etat courant depuis le disque.

_Pas besoin d'orchestration sophistiquee_ -- juste une boucle bash simpliste qui continue de relancer l'agent, et l'agent determine quoi faire ensuite en lisant le fichier plan a chaque fois.

### II. Execution de tache

Chaque tache est promptee pour continuer son travail contre la backpressure (tests, etc.) jusqu'a ce qu'elle passe -- creant une pseudo 'boucle' interne (dans une seule session).

Cette boucle interne est juste de l'auto-correction interne / du raisonnement iteratif au sein d'une seule longue reponse du modele, alimentee par les prompts de backpressure, l'utilisation d'outils et les subagents. Ce n'est pas une boucle au sens programmatique.

Une seule execution de tache n'a pas de limite technique dure. Le controle repose sur :

- _Discipline de portee_ -- PROMPT.md instruit "une tache" et "commit quand les tests passent"
- _Backpressure_ -- les echecs de tests/build forcent l'agent a corriger les problemes avant de commiter
- _Completion naturelle_ -- l'agent sort apres un commit reussi

_Ralph peut tourner en rond, ignorer les instructions, ou partir dans de mauvaises directions_ -- c'est attendu et fait partie du processus d'accordage. Quand Ralph "vous teste" en echouant de facons specifiques, vous ajoutez des garde-fous au prompt ou ajustez les mecanismes de backpressure. Le non-determinisme est gerable par l'observation et l'iteration.

### Exemple de `loop.sh` ameliore

Enveloppe la boucle de base avec selection de mode (plan/build), avec max-iterations pour le nombre maximum de taches a completer, et git push apres chaque iteration.

_Cette amelioration utilise deux fichiers prompt sauvegardes :_

- `PROMPT_plan.md` -- Mode planification (analyse des ecarts, genere/met a jour le plan)
- `PROMPT_build.md` -- Mode construction (implemente depuis le plan)

```bash
#!/bin/bash
# Usage : ./loop.sh [plan|build] [max_iterations]
# Exemples :
#   ./loop.sh              # Mode build, taches illimitees
#   ./loop.sh 20           # Mode build, max 20 taches
#   ./loop.sh build 20     # Mode build, max 20 taches
#   ./loop.sh plan         # Mode plan, taches illimitees
#   ./loop.sh plan 5       # Mode plan, max 5 taches

# Analyser les arguments
if [ "$1" = "plan" ]; then
    # Mode planification
    MODE="plan"
    PROMPT_FILE="PROMPT_plan.md"
    MAX_ITERATIONS=${2:-0}
elif [ "$1" = "build" ]; then
    # Mode construction explicite (avec max iterations optionnel)
    MODE="build"
    PROMPT_FILE="PROMPT_build.md"
    MAX_ITERATIONS=${2:-0}
elif [[ "$1" =~ ^[0-9]+$ ]]; then
    # Mode build avec max taches (nombre seul)
    MODE="build"
    PROMPT_FILE="PROMPT_build.md"
    MAX_ITERATIONS=$1
else
    # Mode build, illimite (pas d'arguments ou entree invalide)
    MODE="build"
    PROMPT_FILE="PROMPT_build.md"
    MAX_ITERATIONS=0
fi

ITERATION=0
CURRENT_BRANCH=$(git branch --show-current)

echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "Mode:    $MODE"
echo "Prompt:  $PROMPT_FILE"
echo "Branche: $CURRENT_BRANCH"
[ $MAX_ITERATIONS -gt 0 ] && echo "Max:     $MAX_ITERATIONS iterations (nombre de taches)"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Verifier que le fichier prompt existe
if [ ! -f "$PROMPT_FILE" ]; then
    echo "Erreur : $PROMPT_FILE non trouve"
    exit 1
fi

while true; do
    if [ $MAX_ITERATIONS -gt 0 ] && [ $ITERATION -ge $MAX_ITERATIONS ]; then
        echo "Nombre max d'iterations (nombre de taches) atteint : $MAX_ITERATIONS"
        break
    fi

    # Lancer une iteration Ralph avec le prompt selectionne
    # -p : Mode headless (non-interactif, lit depuis stdin)
    # --dangerously-skip-permissions : Auto-approuver tous les appels d'outils (mode YOLO)
    # --output-format=stream-json : Sortie structuree pour logging/monitoring
    # --model opus : L'agent principal utilise Opus pour le raisonnement complexe (selection de tache, priorisation)
    #               Peut utiliser 'sonnet' en mode build pour la vitesse si le plan est clair et les taches bien definies
    # --verbose : Logging d'execution detaille
    cat "$PROMPT_FILE" | claude -p \
        --dangerously-skip-permissions \
        --output-format=stream-json \
        --model opus \
        --verbose

    # Pousser les changements apres chaque iteration
    git push origin "$CURRENT_BRANCH" || {
        echo "Echec du push. Creation de la branche remote..."
        git push -u origin "$CURRENT_BRANCH"
    }

    ITERATION=$((ITERATION + 1))
    echo -e "\n\n======================== BOUCLE $ITERATION ========================\n"
done
```

_Selection de mode :_

- Pas de mot-cle -> Utilise `PROMPT_build.md` pour la construction (implementation)
- Mot-cle `plan` -> Utilise `PROMPT_plan.md` pour la planification (analyse des ecarts, generation du plan)

_Max-iterations :_

- Limite la _boucle de selection de tache_ (nombre de taches tentees ; PAS les appels d'outils au sein d'une seule tache)
- Chaque iteration = une fenetre de contexte fraiche = une tache de IMPLEMENTATION_PLAN.md = un commit
- `./loop.sh` tourne en illimite (arret manuel avec Ctrl+C)
- `./loop.sh 20` tourne max 20 iterations puis s'arrete

_Flags CLI de Claude :_

- `-p` (mode headless) : Active l'operation non-interactive, lit le prompt depuis stdin
- `--dangerously-skip-permissions` : Contourne tous les prompts de permission pour des runs entierement automatises
- `--output-format=stream-json` : Produit du JSON structure pour logging/monitoring/visualisation
- `--model opus` : L'agent principal utilise Opus pour la selection de tache, priorisation et coordination (peut utiliser `sonnet` pour la vitesse si les taches sont claires)
- `--verbose` : Fournit un logging d'execution detaille

### Variante avec sortie streamee

Une alternative `loop_streamed.sh` qui pipe la sortie JSON brute de Claude a travers `parse_stream.js` pour un affichage terminal lisible et colore montrant les appels d'outils, resultats et statistiques d'execution.

_Differences par rapport au `loop.sh` de base :_

- Passe le prompt comme argument (`-p "$FULL_PROMPT"`) au lieu d'un pipe stdin
- Ajoute `--include-partial-messages` pour le streaming en temps reel
- Pipe la sortie a travers `parse_stream.js` (Node.js, aucune dependance)
- Ajoute "Execute the instructions above." au contenu du prompt

_Fichiers :_ `loop_streamed.sh` et `parse_stream.js`

```bash
#!/bin/bash
set -o pipefail
# Usage : ./loop_streamed.sh [plan|build] [max_iterations]
# Exemples :
#   ./loop_streamed.sh              # Mode build, illimite
#   ./loop_streamed.sh 20           # Mode build, max 20
#   ./loop_streamed.sh build 20     # Mode build, max 20
#   ./loop_streamed.sh plan         # Mode plan, illimite
#   ./loop_streamed.sh plan 5       # Mode plan, max 5

# Analyser les arguments
if [ "$1" = "plan" ]; then
    MODE="plan"
    PROMPT_FILE="PROMPT_plan.md"
    MAX_ITERATIONS=${2:-0}
elif [ "$1" = "build" ]; then
    MODE="build"
    PROMPT_FILE="PROMPT_build.md"
    MAX_ITERATIONS=${2:-0}
elif [[ "$1" =~ ^[0-9]+$ ]]; then
    MODE="build"
    PROMPT_FILE="PROMPT_build.md"
    MAX_ITERATIONS=$1
else
    MODE="build"
    PROMPT_FILE="PROMPT_build.md"
    MAX_ITERATIONS=0
fi

ITERATION=0
CURRENT_BRANCH=$(git branch --show-current)

echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "Mode:    $MODE"
echo "Prompt:  $PROMPT_FILE"
echo "Branche: $CURRENT_BRANCH"
[ $MAX_ITERATIONS -gt 0 ] && echo "Max:     $MAX_ITERATIONS iterations"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Verifier que le fichier prompt existe
if [ ! -f "$PROMPT_FILE" ]; then
    echo "Erreur : $PROMPT_FILE non trouve"
    exit 1
fi

while true; do
    if [ $MAX_ITERATIONS -gt 0 ] && [ $ITERATION -ge $MAX_ITERATIONS ]; then
        echo "Nombre max d'iterations atteint : $MAX_ITERATIONS"
        break
    fi

    # Lancer une iteration Ralph avec le prompt selectionne
    # -p : Mode headless (non-interactif, imprime la sortie et quitte)
    # --dangerously-skip-permissions : Auto-approuver tous les appels d'outils (mode YOLO)
    # --model opus : L'agent principal utilise Opus pour le raisonnement complexe
    # --verbose : Logging d'execution detaille
    # --output-format stream-json : Sortie structuree pipee a parse_stream.js
    # --include-partial-messages : Streamer les resultats partiels pour un feedback en direct

    FULL_PROMPT="$(cat "$PROMPT_FILE")

Execute the instructions above."

    echo "En cours..."
    echo ""

    # Streamer le JSON avec messages partiels, parser pour une sortie lisible
    SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
    claude -p "$FULL_PROMPT" \
        --dangerously-skip-permissions \
        --model opus \
        --verbose \
        --output-format stream-json \
        --include-partial-messages | node "$SCRIPT_DIR/parse_stream.js"

    echo ""
    echo "Iteration Claude terminee"

    # Pousser les changements apres chaque iteration
    git push origin "$CURRENT_BRANCH" || {
        echo "Echec du push. Creation de la branche remote..."
        git push -u origin "$CURRENT_BRANCH"
    }

    ITERATION=$((ITERATION + 1))
    echo -e "\n\n======================== BOUCLE $ITERATION ========================\n"
done
```

#### `parse_stream.js` -- Parseur de sortie stream

```javascript
#!/usr/bin/env node
// Parser la sortie stream-json de Claude pour un affichage lisible
// Usage : claude ... --output-format stream-json | node parse_stream.js

const readline = require('readline');

// Codes couleur ANSI
const colors = {
  reset: '\x1b[0m',
  dim: '\x1b[2m',
  green: '\x1b[32m',
  yellow: '\x1b[33m',
  blue: '\x1b[34m',
  magenta: '\x1b[35m',
  cyan: '\x1b[36m',
  red: '\x1b[31m',
  gray: '\x1b[90m',
};

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
  terminal: false
});

// Suivre l'outil courant et son input
let currentToolIndex = null;
let currentToolName = null;
let toolInputBuffer = '';
let lastToolName = null;

// Suivre l'etat du message
let messageCount = 0;
let toolUseCount = 0;

// Formater les details de l'outil selon le nom et l'input
function formatToolDetails(name, input) {
  try {
    const params = JSON.parse(input);
    switch (name) {
      case 'Bash':
        return params.command ? `$ ${params.command}` : null;
      case 'Task':
        const desc = params.description || '';
        const type = params.subagent_type || '';
        return type ? `${type}(${desc})` : desc;
      case 'Read':
        return params.file_path ? `${params.file_path}` : null;
      case 'Write':
        return params.file_path ? `${params.file_path}` : null;
      case 'Edit':
        return params.file_path ? `${params.file_path}` : null;
      case 'Glob':
        return params.pattern ? `${params.pattern}` : null;
      case 'Grep':
        return params.pattern ? `"${params.pattern}"` : null;
      case 'WebFetch':
        return params.url ? `${params.url}` : null;
      case 'WebSearch':
        return params.query ? `"${params.query}"` : null;
      case 'TodoWrite':
      case 'TaskCreate':
        return params.todos ? `${params.todos.length} taches` : null;
      default:
        const keys = Object.keys(params);
        if (keys.length > 0) {
          const key = keys[0];
          const val = params[key];
          if (typeof val === 'string' && val.length < 80) {
            return `${key}: ${val}`;
          }
        }
        return null;
    }
  } catch (e) {
    return null;
  }
}

// Formater le resultat d'outil pour l'affichage
function formatToolResult(content) {
  if (!content) return null;

  let text = '';

  // Gerer un tableau de blocs de contenu
  if (Array.isArray(content)) {
    for (const block of content) {
      if (block.type === 'text') {
        text += block.text;
      }
    }
  } else if (typeof content === 'string') {
    text = content;
  } else if (content.text) {
    text = content.text;
  }

  if (!text) return null;

  // Nettoyer et tronquer
  const lines = text.split('\n').filter(l => l.trim());
  const maxLines = 5;
  const maxLineLength = 120;

  let result = [];
  for (let i = 0; i < Math.min(lines.length, maxLines); i++) {
    let line = lines[i];
    if (line.length > maxLineLength) {
      line = line.substring(0, maxLineLength - 3) + '...';
    }
    result.push(line);
  }

  if (lines.length > maxLines) {
    result.push(`${colors.dim}... +${lines.length - maxLines} lignes de plus${colors.reset}`);
  }

  return result.join('\n');
}

rl.on('line', (line) => {
  try {
    const data = JSON.parse(line);

    // ===== EVENEMENTS STREAM =====
    if (data.type === 'stream_event') {
      const event = data.event;

      if (event?.type === 'content_block_delta') {
        const delta = event.delta;
        if (delta?.text) {
          process.stdout.write(delta.text);
        }
        if (delta?.partial_json !== undefined) {
          toolInputBuffer += delta.partial_json;
        }

      } else if (event?.type === 'content_block_start') {
        const block = event.content_block;
        if (block?.type === 'tool_use') {
          currentToolIndex = event.index;
          currentToolName = block.name;
          lastToolName = block.name;
          toolInputBuffer = '';
          toolUseCount++;
          console.log(`\n${colors.cyan}  ${block.name}${colors.reset}`);
        }

      } else if (event?.type === 'content_block_stop') {
        if (currentToolName && toolInputBuffer) {
          const details = formatToolDetails(currentToolName, toolInputBuffer);
          if (details) {
            const lines = details.split('\n');
            const maxLines = 3;
            const displayLines = lines.slice(0, maxLines);
            displayLines.forEach(l => {
              const truncated = l.length > 100 ? l.substring(0, 97) + '...' : l;
              console.log(`${colors.dim}   ${truncated}${colors.reset}`);
            });
            if (lines.length > maxLines) {
              console.log(`${colors.dim}   ... +${lines.length - maxLines} lignes de plus${colors.reset}`);
            }
          }
        }
        currentToolName = null;
        currentToolIndex = null;
        toolInputBuffer = '';

      } else if (event?.type === 'message_start') {
        messageCount++;
      } else if (event?.type === 'message_stop') {
        // Message complet
      }

    // ===== RESULTATS D'OUTILS =====
    } else if (data.type === 'tool_result') {
      const result = data.result || data.content;
      const toolName = data.tool_name || lastToolName || 'tool';
      const isError = data.is_error || false;

      if (isError) {
        console.log(`${colors.red}   Erreur :${colors.reset}`);
      } else {
        console.log(`${colors.green}   Resultat :${colors.reset}`);
      }

      const formatted = formatToolResult(result);
      if (formatted) {
        formatted.split('\n').forEach(line => {
          console.log(`${colors.gray}     ${line}${colors.reset}`);
        });
      }

    // ===== MESSAGES UTILISATEUR =====
    } else if (data.type === 'user') {
      const content = data.message?.content;
      if (Array.isArray(content)) {
        for (const block of content) {
          if (block.type === 'tool_result') {
            const isError = block.is_error || false;
            if (isError) {
              console.log(`${colors.red}   Erreur :${colors.reset}`);
            } else {
              console.log(`${colors.green}   Resultat :${colors.reset}`);
            }
            const formatted = formatToolResult(block.content);
            if (formatted) {
              formatted.split('\n').forEach(line => {
                console.log(`${colors.gray}     ${line}${colors.reset}`);
              });
            }
          }
        }
      }

    // ===== MESSAGES ASSISTANT =====
    } else if (data.type === 'assistant') {
      const message = data.message;
      if (message?.subagent) {
        console.log(`\n${colors.magenta}  [${message.subagent.type || 'subagent'}] ${message.subagent.status || ''}${colors.reset}`);
      }

    // ===== ERREURS =====
    } else if (data.type === 'error') {
      const error = data.error || data;
      console.log(`\n${colors.red} Erreur : ${error.message || JSON.stringify(error)}${colors.reset}`);

    // ===== MESSAGES SYSTEME =====
    } else if (data.type === 'system') {
      if (data.message) {
        console.log(`${colors.yellow} ${data.message}${colors.reset}`);
      }

    // ===== RESULTAT FINAL =====
    } else if (data.type === 'result') {
      const duration = Math.floor((data.duration_ms || 0) / 1000);
      const minutes = Math.floor(duration / 60);
      const seconds = duration % 60;
      const timeStr = minutes > 0 ? `${minutes}m ${seconds}s` : `${seconds}s`;

      const cost = data.total_cost_usd || data.cost_usd || 0;
      const inputTokens = data.total_input_tokens
        || data.input_tokens
        || data.usage?.input_tokens
        || data.session_input_tokens
        || 0;
      const outputTokens = data.total_output_tokens
        || data.output_tokens
        || data.usage?.output_tokens
        || data.session_output_tokens
        || 0;

      console.log('\n\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
      console.log(`${colors.green} Termine${colors.reset} en ${timeStr} | Cout : $${cost.toFixed(4)} | Tokens : ${inputTokens.toLocaleString()} ${outputTokens.toLocaleString()} | Outils : ${toolUseCount}`);
    }
  } catch (e) {
    if (line.trim()) process.stderr.write(line + '\n');
  }
});
```

-- contribue par [@terry-xyz](https://github.com/terry-xyz) et [@blackrosesxyz](https://x.com/blackrosesxyz)

## Licence

Ce repository est disponible sous la [Licence MIT](LICENSE).

Les captures d'ecran tierces et images provenant de sources externes sont exclues sauf mention explicite contraire. Voir [NOTICE](NOTICE) pour les details.

---

## Fichiers

```
racine-du-projet/
├── loop.sh                         # Script de boucle Ralph
├── PROMPT_build.md                 # Instructions mode construction
├── PROMPT_plan.md                  # Instructions mode planification
├── AGENTS.md                       # Guide operationnel charge a chaque iteration
├── IMPLEMENTATION_PLAN.md          # Liste de taches priorisee (generee/mise a jour par Ralph)
├── specs/                          # Specs d'exigences (une par sujet JTBD)
│   ├── [sujet-jtbd-a].md
│   └── [sujet-jtbd-b].md
├── src/                            # Code source de l'application
└── src/lib/                        # Utilitaires & composants partages
```

### `loop.sh`

Le script de boucle principal qui orchestre les iterations Ralph.

Voir la section [Mecanique de la boucle](#mecanique-de-la-boucle) pour des exemples d'implementation detailles et les options de configuration.

_Setup :_ Rendre le script executable avant la premiere utilisation :

```bash
chmod +x loop.sh
```

_Fonction principale :_ Alimente continuellement le fichier prompt a Claude, gere les limites d'iterations et pousse les changements apres chaque completion de tache.

### PROMPTS

Le jeu d'instructions pour chaque iteration de boucle. Basculer entre les versions PLANIFICATION et CONSTRUCTION selon le besoin.

_Structure du prompt :_

| Section                | Objectif                                                       |
| ---------------------- | -------------------------------------------------------------- |
| _Phase 0_ (0a, 0b, 0c) | Orienter : etudier specs, emplacement source, plan courant    |
| _Phase 1-4_            | Instructions principales : tache, validation, commit           |
| _Numerotation 999..._  | Garde-fous/invariants (nombre plus eleve = plus critique)      |

_Patterns de langage cles_ (formulations specifiques de Geoff) :

- "study" (pas "read" ou "look at") -- "etudier"
- "don't assume not implemented" (critique, le talon d'Achille) -- "ne pas supposer que ce n'est pas implemente"
- "using parallel subagents" / "up to N subagents" -- "en utilisant des subagents paralleles" / "jusqu'a N subagents"
- "only 1 subagent for build/tests" (controle de backpressure) -- "seulement 1 subagent pour build/tests"
- "Think extra hard" (maintenant "Ultrathink") -- "Reflechir tres fort"
- "capture the why" -- "capturer le pourquoi"
- "keep it up to date" -- "garder a jour"
- "if functionality is missing then it's your job to add it" -- "si la fonctionnalite manque c'est ton job de l'ajouter"
- "resolve them or document them" -- "les resoudre ou les documenter"

#### Convention de numerotation 999...

Les garde-fous dans les prompts utilisent une numerotation en series croissantes de 9 (99999, 999999, 9999999, etc.). Ce n'est **pas** un systeme de priorite conventionnel. C'est une convention de Geoff ou :

- Les numeros ne sont pas des priorites mais des **invariants** -- des regles qui doivent toujours etre respectees
- Le nombre croissant de 9 cree une separation visuelle claire entre les instructions principales et les garde-fous
- Plus le numero est long, plus la regle est "fondamentale" dans la philosophie (les derniers ajouts ont les plus longs numeros)
- Ca rend les garde-fous faciles a ajouter sans reordonner les instructions existantes

#### Template `PROMPT_plan.md`

_Notes :_

- Mettre a jour le placeholder [objectif-specifique-du-projet] ci-dessous.
- Les noms de subagents actuels presument l'utilisation de Claude.

```
0a. Study `specs/*` with up to 250 parallel Sonnet subagents to learn the application specifications.
0b. Study @IMPLEMENTATION_PLAN.md (if present) to understand the plan so far.
0c. Study `src/lib/*` with up to 250 parallel Sonnet subagents to understand shared utilities & components.
0d. For reference, the application source code is in `src/*`.

1. Study @IMPLEMENTATION_PLAN.md (if present; it may be incorrect) and use up to 500 Sonnet subagents to study existing source code in `src/*` and compare it against `specs/*`. Use an Opus subagent to analyze findings, prioritize tasks, and create/update @IMPLEMENTATION_PLAN.md as a bullet point list sorted in priority of items yet to be implemented. Ultrathink. Consider searching for TODO, minimal implementations, placeholders, skipped/flaky tests, and inconsistent patterns. Study @IMPLEMENTATION_PLAN.md to determine starting point for research and keep it up to date with items considered complete/incomplete using subagents.

IMPORTANT: Plan only. Do NOT implement anything. Do NOT assume functionality is missing; confirm with code search first. Treat `src/lib` as the project's standard library for shared utilities and components. Prefer consolidated, idiomatic implementations there over ad-hoc copies.

ULTIMATE GOAL: We want to achieve [project-specific goal]. Consider missing elements and plan accordingly. If an element is missing, search first to confirm it doesn't exist, then if needed author the specification at specs/FILENAME.md. If you create a new element then document the plan to implement it in @IMPLEMENTATION_PLAN.md using a subagent.
```

#### Template `PROMPT_build.md`

_Note :_ Les noms de subagents actuels presument l'utilisation de Claude.

```
0a. Study `specs/*` with up to 500 parallel Sonnet subagents to learn the application specifications.
0b. Study @IMPLEMENTATION_PLAN.md.
0c. For reference, the application source code is in `src/*`.

1. Your task is to implement functionality per the specifications using parallel subagents. Follow @IMPLEMENTATION_PLAN.md and choose the most important item to address. Before making changes, search the codebase (don't assume not implemented) using Sonnet subagents. You may use up to 500 parallel Sonnet subagents for searches/reads and only 1 Sonnet subagent for build/tests. Use Opus subagents when complex reasoning is needed (debugging, architectural decisions).
2. After implementing functionality or resolving problems, run the tests for that unit of code that was improved. If functionality is missing then it's your job to add it as per the application specifications. Ultrathink.
3. When you discover issues, immediately update @IMPLEMENTATION_PLAN.md with your findings using a subagent. When resolved, update and remove the item.
4. When the tests pass, update @IMPLEMENTATION_PLAN.md, then `git add -A` then `git commit` with a message describing the changes. After the commit, `git push`.

99999. Important: When authoring documentation, capture the why — tests and implementation importance.
999999. Important: Single sources of truth, no migrations/adapters. If tests unrelated to your work fail, resolve them as part of the increment.
9999999. As soon as there are no build or test errors create a git tag. If there are no git tags start at 0.0.0 and increment patch by 1 for example 0.0.1  if 0.0.0 does not exist.
99999999. You may add extra logging if required to debug issues.
999999999. Keep @IMPLEMENTATION_PLAN.md current with learnings using a subagent — future work depends on this to avoid duplicating efforts. Update especially after finishing your turn.
9999999999. When you learn something new about how to run the application, update @AGENTS.md using a subagent but keep it brief. For example if you run commands multiple times before learning the correct command then that file should be updated.
99999999999. For any bugs you notice, resolve them or document them in @IMPLEMENTATION_PLAN.md using a subagent even if it is unrelated to the current piece of work.
999999999999. Implement functionality completely. Placeholders and stubs waste efforts and time redoing the same work.
9999999999999. When @IMPLEMENTATION_PLAN.md becomes large periodically clean out the items that are completed from the file using a subagent.
99999999999999. If you find inconsistencies in the specs/* then use an Opus 4.6 subagent with 'ultrathink' requested to update the specs.
999999999999999. IMPORTANT: Keep @AGENTS.md operational only — status updates and progress notes belong in `IMPLEMENTATION_PLAN.md`. A bloated AGENTS.md pollutes every future loop's context.
```

### `AGENTS.md`

Le "coeur de la boucle", unique et canonique -- un guide operationnel concis "comment tourner/builder".

- PAS un changelog ni un journal de progression
- Decrit comment builder/tourner le projet
- Capture les apprentissages operationnels qui ameliorent la boucle
- Garder bref (~60 lignes)

Les statuts, la progression et la planification appartiennent a `IMPLEMENTATION_PLAN.md`, pas ici.

_Loopback / Auto-evaluation immediate :_

AGENTS.md devrait contenir les commandes specifiques au projet qui permettent le loopback -- la capacite de Ralph a evaluer immediatement son travail dans la meme boucle. Ca inclut :

- Commandes de build
- Commandes de test (ciblees et suite complete)
- Commandes de typecheck/lint
- Tout autre outil de validation

Le prompt CONSTRUCTION dit "lancer les tests" de facon generique ; AGENTS.md specifie les commandes reelles. C'est comme ca que la backpressure est cablee par projet.

#### Exemple

```
## Build & Run

Regles succinctes pour comment BUILDER le projet :

## Validation

Lancer apres implementation pour un feedback immediat :

- Tests : `[commande de test]`
- Typecheck : `[commande de typecheck]`
- Lint : `[commande de lint]`

## Notes operationnelles

Apprentissages succincts sur comment TOURNER le projet :

...

### Patterns de la codebase

...
```

### `IMPLEMENTATION_PLAN.md`

Liste priorisee a puces de taches derivees de l'analyse des ecarts (specs vs code) -- generee par Ralph.

- _Cree_ via le mode PLANIFICATION
- _Mis a jour_ pendant le mode CONSTRUCTION (marquer comme complete, ajouter des decouvertes, noter des bugs)
- _Peut etre regenere_ -- Geoff : "J'ai supprime la liste TODO plusieurs fois" -> basculer en mode PLANIFICATION
- _Auto-correcteur_ -- le mode CONSTRUCTION peut meme creer de nouvelles specs si manquantes

La circularite est intentionnelle : consistance eventuelle par l'iteration.

_Pas de template pre-specifie_ -- laisser Ralph/LLM dicter et gerer le format qui fonctionne le mieux pour lui.

### `specs/*`

Un fichier markdown par sujet de preoccupation. Ce sont la source de verite pour ce qui doit etre construit.

- Crees pendant la phase Exigences (conversation humain + LLM)
- Consommes par les modes PLANIFICATION et CONSTRUCTION
- Peuvent etre mis a jour si des inconsistances sont decouvertes (rare, utiliser un subagent)

_Pas de template pre-specifie_ -- laisser Ralph/LLM dicter et gerer le format qui fonctionne le mieux pour lui.

### `src/` et `src/lib/`

Code source de l'application et utilitaires/composants partages.

References dans les templates `PROMPT.md` pour les etapes d'orientation.

---

## Ameliorations

Je suis encore en train de determiner la valeur/viabilite de celles-ci, mais les opportunites semblent prometteuses :

- [AskUserQuestionTool de Claude pour la planification](#utiliser-askuserquestiontool-de-claude-pour-la-planification) -- utiliser l'outil d'interview integre de Claude pour clarifier systematiquement les JTBD, cas limites et criteres d'acceptation pour les specs.
- [Backpressure pilotee par les criteres d'acceptation](#backpressure-pilotee-par-les-criteres-dacceptation) -- Deriver les exigences de test pendant la planification a partir des criteres d'acceptation. Empeche la "triche" -- impossible de pretendre avoir fini sans les tests appropries qui passent.
- [Backpressure non-deterministe](#backpressure-non-deterministe) -- Utiliser LLM-comme-juge pour des tests sur des taches subjectives (ton, esthetique, UX). Reviews binaires pass/fail qui iterent jusqu'au pass.
- [Branches de travail compatibles Ralph](#branches-de-travail-compatibles-ralph) -- Demander a Ralph de "filtrer sur la feature X" a l'execution n'est pas fiable. A la place, creer un plan scope par branche en amont.
- [JTBD -> Story Map -> Release SLC](#jtbd--story-map--release-slc) -- Pousser la puissance de "Laisser Ralph faire du Ralph" pour connecter l'audience et les activites des JTBD a des releases Simple/Lovable/Complete.
- [Audit des specs](#audit-des-specs) -- Mode dedie pour generer/maintenir les specs avec des regles qualite : resultats comportementaux uniquement, cadrage des sujets, nommage coherent.
- [Reverse engineering de projets brownfield en specs](#reverse-engineering-de-projets-brownfield-en-specs) -- Amener les codebases brownfield dans le workflow Ralph en effectuant le reverse engineering du code existant en specs avant de planifier du nouveau travail.

---

### Utiliser AskUserQuestionTool de Claude pour la planification

Pendant la Phase 1 (Definir les exigences), utiliser l'outil integre `AskUserQuestionTool` de Claude pour explorer systematiquement les JTBD, sujets de preoccupation, cas limites et criteres d'acceptation via une interview structuree avant d'ecrire les specs.

_Quand l'utiliser :_ Exigences initiales minimales/vagues, besoin de clarifier les contraintes, ou plusieurs approches valides existent.

_Invoquer :_ "Interviewe-moi en utilisant AskUserQuestion pour comprendre [JTBD/sujet/criteres d'acceptation/...]"

Claude posera des questions ciblees pour clarifier les exigences et assurer l'alignement avant de produire les fichiers `specs/*.md`.

_Flow :_

1. Commencer avec les informations connues ->
2. _Claude interviewe via AskUserQuestion_ ->
3. Iterer jusqu'a clarte ->
4. Claude ecrit les specs avec criteres d'acceptation ->
5. Passer a la planification/construction

Aucun changement de code ou de prompt necessaire -- ca ameliore simplement la Phase 1 en utilisant les capacites existantes de Claude Code.

_Inspiration_ -- [Post X de Thariq](https://x.com/trq212/status/2005315275026260309)

---

### Backpressure pilotee par les criteres d'acceptation

Le Ralph de Geoff connecte _implicitement_ specs -> implementation -> tests par l'iteration emergente. Cette amelioration rendrait cette connexion _explicite_ en derivant les exigences de test pendant la planification, creant une ligne directe de "a quoi ressemble le succes" a "qu'est-ce qui le verifie".

Cette amelioration connecte les criteres d'acceptation (dans les specs) directement aux exigences de test (dans le plan d'implementation), ameliorant la qualite de la backpressure en :

- _Empechant la "triche"_ -- impossible de pretendre avoir fini sans les tests requis derives des criteres d'acceptation
- _Activant un workflow TDD_ -- exigences de test connues avant le debut de l'implementation
- _Ameliorant la convergence_ -- signal de completion clair (les tests requis passent) vs ambigu ("ca semble fait ?")
- _Maintenant le determinisme_ -- exigences de test dans le plan (etat connu) pas emergentes (probabilistiques)

#### Compatibilite avec la philosophie de base

| Principe                 | Maintenu ? | Comment                                                          |
| ------------------------ | ---------- | ---------------------------------------------------------------- |
| Operation monolithique   | Oui        | Un agent, une tache, une boucle a la fois                        |
| Backpressure critique    | Oui        | Les tests sont le mecanisme, juste derives explicitement maintenant |
| Efficacite du contexte   | Oui        | La planification decide des tests une fois vs la construction qui redecouvre |
| Setup deterministe       | Oui        | Exigences de test dans le plan (etat connu) pas emergentes       |
| Laisser Ralph Ralph      | Oui        | Ralph choisit toujours la priorite et l'approche d'implementation |
| Plan jetable             | Oui        | Mauvaises exigences de test ? Regenerer le plan                  |
| "Capturer le pourquoi"   | Oui        | Intention du test documentee dans le plan avant l'implementation |
| Pas de triche            | Oui        | Les tests requis empechent les implementations placeholder       |

#### L'equilibre de la prescriptivite

La distinction critique :

_Criteres d'acceptation_ (dans les specs) = Resultats comportementaux, resultats observables, a quoi ressemble le succes

- OK : "Extrait 5-10 couleurs dominantes de n'importe quelle image uploadee"
- OK : "Traite les images <5MB en <100ms"
- OK : "Gere les cas limites : niveaux de gris, couleur unique, fonds transparents"

_Exigences de test_ (dans le plan d'implementation) = Points de verification derives des criteres d'acceptation

- OK : "Tests requis : Extraire 5-10 couleurs, Performance <100ms, Gerer le cas limite niveaux de gris"

_Approche d'implementation_ (a la discretion de Ralph) = Decisions techniques sur comment y arriver

- PAS OK : "Utiliser le clustering K-means avec 3 iterations et conversion d'espace colorimetrique LAB"

La cle : _Specifier QUOI verifier (resultats), pas COMMENT implementer (approche)_

Ca maintient le principe "Laisser Ralph Ralph" -- Ralph decide des details d'implementation tout en ayant des signaux de succes clairs.

#### Architecture : connexion en trois phases

```
Phase 1 : Definition des exigences
    specs/*.md + Criteres d'acceptation
    |
Phase 2 : Planification (derive les exigences de test)
    IMPLEMENTATION_PLAN.md + Tests requis
    |
Phase 3 : Construction (implemente avec tests)
    Implementation + Tests -> Backpressure
```

#### Phase 1 : Definition des exigences

Pendant la conversation humain + LLM qui produit les specs :

- Discuter des JTBD et decouper en sujets de preoccupation
- Utiliser des subagents pour charger du contexte externe si necessaire
- _Discuter et definir les criteres d'acceptation_ -- quels resultats observables et verifiables indiquent le succes
- Garder les criteres comportementaux (resultats), pas implementation (comment construire)
- Le LLM ecrit les specs incluant les criteres d'acceptation de la facon la plus logique pour la spec
- Les criteres d'acceptation deviennent la base pour deriver les exigences de test en phase de planification

#### Phase 2 : Amelioration du mode Planification

Modifier l'instruction 1 de `PROMPT_plan.md` pour inclure la derivation de tests. Ajouter apres la premiere phrase :

```markdown
For each task in the plan, derive required tests from acceptance criteria in specs - what specific outcomes need verification (behavior, performance, edge cases). Tests verify WHAT works, not HOW it's implemented. Include as part of task definition.
```

#### Phase 3 : Amelioration du mode Construction

Modifier les instructions de `PROMPT_build.md` :

_Instruction 1 :_ Ajouter apres "choose the most important item to address" :

```markdown
Tasks include required tests - implement tests as part of task scope.
```

_Instruction 2 :_ Remplacer "run the tests for that unit of code" par :

```markdown
run all required tests specified in the task definition. All required tests must exist and pass before the task is considered complete.
```

_Ajouter un nouveau garde-fou_ (dans la sequence des 9) :

```markdown
999. Required tests derived from acceptance criteria must exist and pass before committing. Tests are part of implementation scope, not optional. Test-driven development approach: tests can be written first or alongside implementation.
```

---

### Backpressure non-deterministe

Certains criteres d'acceptation resistent a la validation programmatique :

- _Qualite creative_ -- ton de l'ecriture, flux narratif, engagement
- _Jugements esthetiques_ -- harmonie visuelle, equilibre du design, coherence de marque
- _Qualite UX_ -- navigation intuitive, hierarchie d'information claire
- _Pertinence du contenu_ -- messages contextuels, adequation a l'audience

Ceux-ci necessitent un jugement humain mais ont besoin de backpressure pour satisfaire les criteres d'acceptation pendant la boucle de construction.

_Solution :_ Ajouter des tests LLM-comme-juge comme backpressure avec pass/fail binaire.

Les reviews LLM sont non-deterministes (le meme artefact peut recevoir des jugements differents d'un run a l'autre). Ca s'aligne avec la philosophie Ralph : "deterministiquement mauvais dans un monde non-deterministe." La boucle fournit la consistance eventuelle par l'iteration -- les reviews tournent jusqu'au pass, acceptant la variance naturelle.

#### Ce qui doit etre cree (premiere etape)

Creer deux fichiers dans `src/lib/` :

```
src/lib/
  llm-review.ts          # Fixture de base, une seule fonction, API propre
  llm-review.test.ts     # Exemples de reference montrant le pattern (Ralph apprend de ceux-ci)
```

##### `llm-review.ts` -- API binaire pass/fail que Ralph decouvre :

```typescript
interface ReviewResult {
  pass: boolean;
  feedback?: string; // Present uniquement quand pass=false
}

function createReview(config: {
  criteria: string; // Quoi evaluer (comportemental, observable)
  artifact: string; // Contenu texte OU chemin vers screenshot
  intelligence?: "fast" | "smart"; // Optionnel, 'fast' par defaut
}): Promise<ReviewResult>;
```

_Support multimodal :_ Les deux niveaux d'intelligence utiliseraient un modele multimodal (texte + vision). La detection du type d'artefact est automatique :

- Evaluation texte : `artifact: "Votre contenu ici"` -> Route comme input texte
- Evaluation vision : `artifact: "./tmp/screenshot.png"` -> Route comme input vision (detecte les extensions .png, .jpg, .jpeg)

_Niveaux d'intelligence_ (qualite du jugement, pas type de capacite) :

- `fast` (par defaut) : Modeles rapides et economiques pour des evaluations directes
  - Exemple : Gemini 3.0 Flash (multimodal, rapide, pas cher)
- `smart` : Modeles de meilleure qualite pour un jugement esthetique/creatif nuance
  - Exemple : GPT 5.1 (multimodal, meilleur jugement, cout plus eleve)

L'implementation de la fixture selectionne les modeles appropries. (Les exemples sont des options actuelles, pas des exigences.)

##### `llm-review.test.ts` -- Montre a Ralph comment l'utiliser (exemples texte et vision) :

```typescript
import { createReview } from "@/lib/llm-review";

// Exemple 1 : Evaluation de texte
test("ton du message de bienvenue", async () => {
  const message = generateWelcomeMessage();
  const result = await createReview({
    criteria:
      "Le message utilise un ton chaleureux et conversationnel adapte aux professionnels du design tout en transmettant clairement la proposition de valeur",
    artifact: message, // Contenu texte
  });
  expect(result.pass).toBe(true);
});

// Exemple 2 : Evaluation vision (chemin screenshot)
test("hierarchie visuelle du dashboard", async () => {
  await page.screenshot({ path: "./tmp/dashboard.png" });
  const result = await createReview({
    criteria:
      "La mise en page demontre une hierarchie visuelle claire avec une action primaire evidente",
    artifact: "./tmp/dashboard.png", // Chemin screenshot
  });
  expect(result.pass).toBe(true);
});

// Exemple 3 : Intelligence smart pour un jugement complexe
test("coherence visuelle de marque", async () => {
  await page.screenshot({ path: "./tmp/homepage.png" });
  const result = await createReview({
    criteria:
      "Le design visuel maintient une identite de marque professionnelle adaptee aux services financiers tout en evitant la sterilite corporate",
    artifact: "./tmp/homepage.png",
    intelligence: "smart", // Jugement esthetique complexe
  });
  expect(result.pass).toBe(true);
});
```

_Ralph apprend de ces exemples :_ Tant le texte que les screenshots fonctionnent comme artefacts. Choisir selon ce qui doit etre evalue. La fixture gere le reste en interne.

_Extensibilite future :_ Le design actuel utilise un seul `artifact: string` pour la simplicite. Peut s'etendre a `artifact: string | string[]` si des patterns clairs emergent necessitant plusieurs artefacts (comparaisons avant/apres, coherence entre elements, evaluation multi-perspective). Les screenshots composites ou le texte concatene pourraient gerer la plupart des besoins multi-elements.

#### Integration avec le workflow Ralph

_Phase de planification_ -- Mettre a jour `PROMPT_plan.md` :

Apres :

```
...Study @IMPLEMENTATION_PLAN.md to determine starting point for research and keep it up to date with items considered complete/incomplete using subagents.
```

Inserer ceci :

```
When deriving test requirements from acceptance criteria, identify whether verification requires programmatic validation (measurable, inspectable) or human-like judgment (perceptual quality, tone, aesthetics). Both types are equally valid backpressure mechanisms. For subjective criteria that resist programmatic validation, explore src/lib for non-deterministic evaluation patterns.
```

_Phase de construction_ -- Mettre a jour `PROMPT_build.md` :

Ajouter un nouveau garde-fou (dans la sequence des 9) :

```markdown
9999. Create tests to verify implementation meets acceptance criteria and include both conventional tests (behavior, performance, correctness) and perceptual quality tests (for subjective criteria, see src/lib patterns).
```

_Decouverte, pas documentation :_ Ralph apprend les patterns de review LLM depuis les exemples de `llm-review.test.ts` pendant l'exploration de `src/lib` (Phase 0c). Pas de mises a jour AGENTS.md necessaires -- les exemples de code SONT la documentation.

#### Compatibilite avec la philosophie de base

| Principe                | Maintenu ?   | Comment                                                                                                                                                       |
| ----------------------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Backpressure critique   | Oui          | Etend la backpressure a l'acceptation non-programmatique                                                                                                       |
| Setup deterministe      | Partiel      | Criteres dans le plan (deterministe), evaluation non-deterministe mais converge par iteration. Compromis intentionnel pour la qualite subjective.              |
| Efficacite du contexte  | Oui          | Fixture reutilisee via `src/lib`, petites definitions de test                                                                                                  |
| Laisser Ralph Ralph     | Oui          | Ralph decouvre le pattern, choisit quand l'utiliser, ecrit les criteres                                                                                        |
| Plan jetable            | Oui          | Exigences de review font partie du plan, regenerer si mauvaises                                                                                                |
| La simplicite gagne     | Oui          | Une seule fonction, resultat binaire, pas de complexite de scoring                                                                                             |
| Ajouter des signes      | Oui          | Ajouts legers au prompt, apprentissage par exploration du code                                                                                                 |

---

### Branches de travail compatibles Ralph

_Le principe critique :_ Le Ralph de Geoff travaille depuis un plan unique et jetable ou Ralph choisit "le plus important". Pour utiliser des branches avec Ralph tout en maintenant ce pattern, vous devez scoper au moment de la creation du plan, pas a la selection de tache.

_Pourquoi c'est important :_

- PAS OK _Mauvaise approche_ : Creer un plan complet, puis demander a Ralph de "filtrer" les taches a l'execution -> pas fiable (70-80%), viole le determinisme
- OK _Bonne approche_ : Creer un plan scope en amont pour chaque branche de travail -> deterministe, simple, maintient "le plan est jetable"

_Solution :_ Ajouter un mode `plan-work` pour creer un IMPLEMENTATION_PLAN.md scope au travail sur la branche courante. L'utilisateur cree une branche de travail, puis lance `plan-work` avec une description en langage naturel du focus du travail. Le LLM utilise cette description pour scoper le plan. Apres la planification, Ralph construit depuis ce plan deja scope sans aucun filtrage semantique -- il choisit juste "le plus important" comme toujours.

_Terminologie :_ "Work" (travail) est intentionnellement large -- ca peut decrire des features, des sujets de preoccupation, des efforts de refactoring, des changements d'infrastructure, des corrections de bugs, ou tout ensemble coherent de changements lies. La description de travail que vous passez a `plan-work` est en langage naturel pour le LLM -- ca peut etre de la prose, pas contrainte par les regles de nommage de branches git.

#### Principes de design

- Chaque session Ralph opere de facon monolithique sur UN corps de travail par branche
- L'utilisateur cree les branches manuellement -- controle total sur les conventions de nommage et la strategie (par ex. worktrees)
- Descriptions de travail en langage naturel -- passer de la prose au LLM, pas contraint par le nommage git
- Scoping a la creation du plan (deterministe) pas a la selection de tache (probabiliste)
- Un plan par branche -- un IMPLEMENTATION_PLAN.md par branche
- Le plan reste jetable -- regenerer le plan scope quand mauvais/obsolete pour une branche
- Pas de changement dynamique de branche au sein d'une session de boucle
- Maintient la simplicite et le determinisme
- Optionnel -- le workflow branche principale fonctionne toujours
- Pas de filtrage semantique au moment du build -- Ralph choisit juste "le plus important"

#### Workflow

_1. Planification complete (sur la branche principale)_

```bash
./loop.sh plan
# Generer le IMPLEMENTATION_PLAN.md complet pour tout le projet
```

_2. Creer une branche de travail_

L'utilisateur execute :

```bash
git checkout -b ralph/user-auth-oauth
# Creer une branche avec la convention de nommage que vous preferez
# Suggestion : prefixe ralph/* pour les branches de travail
```

_3. Planification scopee (sur la branche de travail)_

```bash
./loop.sh plan-work "systeme d'authentification utilisateur avec OAuth et gestion de session"
# Passer une description en langage naturel -- le LLM l'utilise pour scoper le plan
# Cree un IMPLEMENTATION_PLAN.md focalise avec uniquement les taches pour ce travail
```

_4. Construire depuis le plan (sur la branche de travail)_

```bash
./loop.sh
# Ralph construit depuis le plan scope (pas de filtrage necessaire)
# Choisit la tache la plus importante du plan deja scope
```

_5. Creation de PR (quand le travail est termine)_

L'utilisateur execute :

```bash
gh pr create --base main --head ralph/user-auth-oauth --fill
```

#### Script de boucle avec scope de travail

Etend le script de boucle ameliore de base pour ajouter le support des branches de travail avec planification scopee :

```bash
#!/bin/bash
set -euo pipefail

# Usage :
#   ./loop.sh [plan|build] [max_iterations]  # Plan/build sur la branche courante
#   ./loop.sh plan-work "description du travail"  # Creer un plan scope sur la branche courante
# Exemples :
#   ./loop.sh                               # Mode build, illimite
#   ./loop.sh 20                            # Mode build, max 20
#   ./loop.sh build 20                      # Mode build, max 20
#   ./loop.sh plan 5                        # Planification complete, max 5
#   ./loop.sh plan-work "auth utilisateur"  # Planification scopee

# Analyser les arguments
MODE="build"
PROMPT_FILE="PROMPT_build.md"

if [ "$1" = "plan" ]; then
    # Mode planification complete
    MODE="plan"
    PROMPT_FILE="PROMPT_plan.md"
    MAX_ITERATIONS=${2:-0}
elif [ "$1" = "build" ]; then
    # Mode construction explicite (avec max iterations optionnel)
    MAX_ITERATIONS=${2:-0}
elif [ "$1" = "plan-work" ]; then
    # Mode planification scopee
    if [ -z "$2" ]; then
        echo "Erreur : plan-work necessite une description du travail"
        echo "Usage : ./loop.sh plan-work \"description du travail\""
        exit 1
    fi
    MODE="plan-work"
    WORK_DESCRIPTION="$2"
    PROMPT_FILE="PROMPT_plan_work.md"
    MAX_ITERATIONS=${3:-5}  # 5 par defaut pour la planification de travail
elif [[ "$1" =~ ^[0-9]+$ ]]; then
    # Mode build avec max iterations (nombre seul)
    MAX_ITERATIONS=$1
else
    # Mode build, illimite
    MAX_ITERATIONS=0
fi

ITERATION=0
CURRENT_BRANCH=$(git branch --show-current)

# Valider la branche pour le mode plan-work
if [ "$MODE" = "plan-work" ]; then
    if [ "$CURRENT_BRANCH" = "main" ] || [ "$CURRENT_BRANCH" = "master" ]; then
        echo "Erreur : plan-work doit etre lance sur une branche de travail, pas main/master"
        echo "Creer une branche de travail d'abord : git checkout -b ralph/votre-travail"
        exit 1
    fi

    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "Mode:     plan-work"
    echo "Branche:  $CURRENT_BRANCH"
    echo "Travail:  $WORK_DESCRIPTION"
    echo "Prompt:   $PROMPT_FILE"
    echo "Plan:     Va creer un IMPLEMENTATION_PLAN.md scope"
    [ "$MAX_ITERATIONS" -gt 0 ] && echo "Max:      $MAX_ITERATIONS iterations"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

    # Avertir des changements non commites sur IMPLEMENTATION_PLAN.md
    if [ -f "IMPLEMENTATION_PLAN.md" ] && ! git diff --quiet IMPLEMENTATION_PLAN.md 2>/dev/null; then
        echo "Attention : IMPLEMENTATION_PLAN.md a des changements non commites qui seront ecrases"
        read -p "Continuer ? [o/N] " -n 1 -r
        echo
        [[ ! $REPLY =~ ^[Oo]$ ]] && exit 1
    fi

    # Exporter la description du travail pour PROMPT_plan_work.md
    export WORK_SCOPE="$WORK_DESCRIPTION"
else
    # Mode plan/build normal
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "Mode:    $MODE"
    echo "Branche: $CURRENT_BRANCH"
    echo "Prompt:  $PROMPT_FILE"
    echo "Plan:    IMPLEMENTATION_PLAN.md"
    [ "$MAX_ITERATIONS" -gt 0 ] && echo "Max:     $MAX_ITERATIONS iterations"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
fi

# Verifier que le fichier prompt existe
if [ ! -f "$PROMPT_FILE" ]; then
    echo "Erreur : $PROMPT_FILE non trouve"
    exit 1
fi

# Boucle principale
while true; do
    if [ "$MAX_ITERATIONS" -gt 0 ] && [ "$ITERATION" -ge "$MAX_ITERATIONS" ]; then
        echo "Nombre max d'iterations atteint : $MAX_ITERATIONS"

        if [ "$MODE" = "plan-work" ]; then
            echo ""
            echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
            echo "Plan scope cree : $WORK_DESCRIPTION"
            echo "Pour construire, lancer :"
            echo "  ./loop.sh 20"
            echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
        fi
        break
    fi

    # Lancer une iteration Ralph avec le prompt selectionne
    # -p : Mode headless (non-interactif, lit depuis stdin)
    # --dangerously-skip-permissions : Auto-approuver tous les appels d'outils (mode YOLO)
    # --output-format=stream-json : Sortie structuree pour logging/monitoring
    # --model opus : L'agent principal utilise Opus pour le raisonnement complexe (selection de tache, priorisation)
    #               Peut utiliser 'sonnet' pour la vitesse si le plan est clair et les taches bien definies
    # --verbose : Logging d'execution detaille

    # Pour le mode plan-work, substituer ${WORK_SCOPE} dans le prompt avant de piper
    if [ "$MODE" = "plan-work" ]; then
        envsubst < "$PROMPT_FILE" | claude -p \
            --dangerously-skip-permissions \
            --output-format=stream-json \
            --model opus \
            --verbose
    else
        cat "$PROMPT_FILE" | claude -p \
            --dangerously-skip-permissions \
            --output-format=stream-json \
            --model opus \
            --verbose
    fi

    # Pousser sur la branche courante
    CURRENT_BRANCH=$(git branch --show-current)
    git push origin "$CURRENT_BRANCH" || {
        echo "Echec du push. Creation de la branche remote..."
        git push -u origin "$CURRENT_BRANCH"
    }

    ITERATION=$((ITERATION + 1))
    echo -e "\n\n======================== BOUCLE $ITERATION ========================\n"
done
```

#### Template `PROMPT_plan_work.md`

_Note :_ Identique a `PROMPT_plan.md` mais avec des instructions de scoping et la variable d'environnement `WORK_SCOPE` substituee (automatiquement par le script de boucle).

```
0a. Study `specs/*` with up to 250 parallel Sonnet subagents to learn the application specifications.
0b. Study @IMPLEMENTATION_PLAN.md (if present) to understand the plan so far.
0c. Study `src/lib/*` with up to 250 parallel Sonnet subagents to understand shared utilities & components.
0d. For reference, the application source code is in `src/*`.

1. You are creating a SCOPED implementation plan for work: "${WORK_SCOPE}". Study @IMPLEMENTATION_PLAN.md (if present; it may be incorrect) and use up to 500 Sonnet subagents to study existing source code in `src/*` and compare it against `specs/*`. Use an Opus subagent to analyze findings, prioritize tasks, and create/update @IMPLEMENTATION_PLAN.md as a bullet point list sorted in priority of items yet to be implemented. Ultrathink. Consider searching for TODO, minimal implementations, placeholders, skipped/flaky tests, and inconsistent patterns. Study @IMPLEMENTATION_PLAN.md to determine starting point for research and keep it up to date with items considered complete/incomplete using subagents.

IMPORTANT: This is SCOPED PLANNING for "${WORK_SCOPE}" only. Create a plan containing ONLY tasks directly related to this work scope. Be conservative - if uncertain whether a task belongs to this work, exclude it. The plan can be regenerated if too narrow. Plan only. Do NOT implement anything. Do NOT assume functionality is missing; confirm with code search first. Treat `src/lib` as the project's standard library for shared utilities and components. Prefer consolidated, idiomatic implementations there over ad-hoc copies.

ULTIMATE GOAL: We want to achieve the scoped work "${WORK_SCOPE}". Consider missing elements related to this work and plan accordingly. If an element is missing, search first to confirm it doesn't exist, then if needed author the specification at specs/FILENAME.md. If you create a new element then document the plan to implement it in @IMPLEMENTATION_PLAN.md using a subagent.
```

#### Compatibilite avec la philosophie de base

| Principe                | Maintenu ? | Comment                                                                             |
| ----------------------- | ---------- | ----------------------------------------------------------------------------------- |
| Operation monolithique  | Oui        | Ralph opere toujours comme processus unique au sein de la branche                   |
| Une tache par boucle    | Oui        | Inchange                                                                            |
| Contexte frais          | Oui        | Inchange                                                                            |
| Deterministe            | Oui        | Scoping a la creation du plan (deterministe), pas a l'execution (probabiliste)      |
| Simple                  | Oui        | Amelioration optionnelle, le workflow principal fonctionne toujours                  |
| Pilote par le plan      | Oui        | Un IMPLEMENTATION_PLAN.md par branche                                               |
| Source de verite unique  | Oui        | Un plan par branche -- le plan scope remplace le plan complet sur la branche        |
| Plan jetable            | Oui        | Regenerer le plan scope n'importe quand : `./loop.sh plan-work "description"`       |
| Markdown plutot que JSON| Oui        | Toujours des plans markdown                                                         |
| Laisser Ralph Ralph     | Oui        | Ralph choisit "le plus important" du plan deja scope -- pas de filtre               |

---

### JTBD -> Story Map -> Release SLC

#### Sujets de preoccupation -> Activites

Le [workflow suggere](https://ghuntley.com/content/images/size/w2400/2025/07/The-ralph-Process.png) de Geoff aligne deja la planification avec les Jobs-to-be-Done -- decomposant les JTBD en sujets de preoccupation, qui a leur tour deviennent des specs. J'adore ca et je pense qu'il y a une opportunite de s'appuyer davantage sur les benefices produit que cette approche offre en recadrant les _sujets de preoccupation_ comme des _activites_.

Les activites sont des verbes dans un parcours ("uploader une photo", "extraire des couleurs") plutot que des capacites ("systeme d'extraction de couleurs"). Elles sont naturellement scopees par l'intention utilisateur.

> Sujets : "extraction de couleurs", "moteur de mise en page" -> oriente capacite
> Activites : "uploader une photo", "voir les couleurs extraites", "arranger la mise en page" -> oriente parcours

#### Activites -> Parcours utilisateur

Les activites -- et leurs etapes constitutives -- se sequencent naturellement en un flux utilisateur, creant une _structure de parcours_ qui rend les lacunes et dependances visibles. Une _[User Story Map](https://www.nngroup.com/articles/user-story-mapping/)_ organise les activites comme colonnes (la colonne vertebrale du parcours) avec les profondeurs de capacite comme lignes -- l'espace complet de ce qui _pourrait_ etre construit :

```
UPLOADER    ->   EXTRAIRE    ->   ARRANGER     ->   PARTAGER

basique          auto            manuel            export
en masse         palette         templates         collab
batch            themes IA       auto-layout       embed
```

#### Parcours utilisateur -> Tranches de release

Les tranches horizontales a travers la carte deviennent des candidats de release. Pas chaque activite n'a besoin de nouvelle capacite dans chaque release -- certaines cellules restent vides, et c'est bien si la tranche reste coherente :

```
                  UPLOADER    ->   EXTRAIRE    ->   ARRANGER     ->   PARTAGER

Release 1:        basique          auto                               export
                  ────────────────────────────────────────────────────────────
Release 2:                         palette          manuel
                  ────────────────────────────────────────────────────────────
Release 3:        batch            themes IA        templates         embed
```

#### Tranches de release -> Releases SLC

La story map vous donne la _structure_ pour decouper. Le concept _[Simple, Lovable, Complete (SLC)](https://longform.asmartbear.com/slc/)_ de Jason Cohen vous donne les _criteres_ de ce qui fait une bonne tranche :

- _Simple_ -- Portee etroite que vous pouvez livrer vite. Pas chaque activite, pas chaque profondeur.
- _Complete_ -- Accomplit pleinement un job dans cette portee. Pas une preview cassee.
- _Lovable_ -- Les gens veulent reellement l'utiliser. Agreeable dans ses limites.

_Pourquoi SLC plutot que MVP ?_ Les MVP optimisent pour l'apprentissage au detriment du client -- "minimum" signifie souvent casse ou frustrant. SLC inverse ca : apprendre en conditions reelles _tout en_ delivrant de la vraie valeur. Si ca reussit, vous avez de l'optionalite. Si ca echoue, vous avez quand meme bien traite les utilisateurs.

Chaque tranche peut devenir une release avec une valeur et une identite claires :

```
                  UPLOADER    ->   EXTRAIRE    ->   ARRANGER     ->   PARTAGER

Palette Picker:   basique          auto                               export
                  ────────────────────────────────────────────────────────────
Mood Board:                        palette          manuel
                  ────────────────────────────────────────────────────────────
Design Studio:    batch            themes IA        templates         embed
```

- _Palette Picker_ -- Uploader, extraire, exporter. Valeur instantanee des le premier jour.
- _Mood Board_ -- Ajoute l'arrangement. L'expression creative entre dans le parcours.
- _Design Studio_ -- Fonctionnalites pro : traitement par lots, themes IA, sortie integrable.

---

#### Operationnaliser avec Ralph

Les concepts ci-dessus -- activites, story maps, releases SLC -- sont les _outils de reflexion_. Comment les traduire dans le workflow Ralph ?

_Approche Ralph par defaut :_

1. _Definir les exigences_ : Humain + LLM definissent les sujets de preoccupation JTBD -> `specs/*.md`
2. _Creer le plan de taches_ : Le LLM analyse toutes les specs + code actuel -> `IMPLEMENTATION_PLAN.md`
3. _Construire_ : Ralph construit contre la portee complete

Ca fonctionne bien pour du travail oriente capacites (features, refactors, infrastructure). Mais ca ne produit pas naturellement des releases de produit a valeur (SLC) -- ca produit "ce que les specs decrivent".

_Approche Activites -> Release SLC :_

Pour obtenir des releases SLC, il faut ancrer les activites dans le contexte audience. L'audience definit QUI a les JTBD, ce qui informe QUELLES activites comptent et ce que "lovable" signifie.

```
Audience (qui)
    └── a des JTBD (resultats desires)
            └── accomplis par des Activites (moyens d'atteindre les resultats)
```

##### Workflow

_I. Phase exigences (2 etapes) :_

Toujours effectuee dans des conversations LLM avec l'humain, similaire a l'approche Ralph par defaut.

1. _Definir l'audience et ses JTBD_ -- POUR QUI construisons-nous et quels RESULTATS veulent-ils ?

   - Humain + LLM discutent et determinent la ou les audience(s) et leurs JTBD (resultats desires)
   - Peut contenir plusieurs audiences connectees (par ex. le "designer" cree, le "client" review)
   - Genere `AUDIENCE_JTBD.md`

2. _Definir les activites_ -- QUE font les utilisateurs pour accomplir leurs JTBD ?

   - Informe par `AUDIENCE_JTBD.md`
   - Pour chaque JTBD, identifier les activites necessaires pour l'accomplir
   - Pour chaque activite, determiner :
     - Profondeurs de capacite (basique -> avancee) -- niveaux de sophistication
     - Resultat(s) desire(s) a chaque profondeur -- a quoi ressemble le succes ?
   - Genere `specs/*.md` (une par activite)

   Les etapes discretes au sein des activites sont implicites et le LLM peut les inferer pendant la planification.

_II. Phase planification :_

Effectuee dans la boucle Ralph avec un prompt de planification _mis a jour_.

- Le LLM analyse :
  - `AUDIENCE_JTBD.md` (qui, resultats desires)
  - `specs/*` (ce qui pourrait etre construit)
  - Etat du code actuel (ce qui existe)
- Le LLM determine la prochaine tranche SLC (quelles activites, a quelles profondeurs de capacite) et planifie les taches pour cette tranche
- Le LLM genere `IMPLEMENTATION_PLAN.md`
- _L'humain verifie_ le plan avant de construire :
  - La portee represente-t-elle une release SLC coherente ?
  - Les bonnes activites sont-elles incluses aux bonnes profondeurs ?
  - Si incorrect -> relancer la boucle de planification pour regenerer le plan, en mettant optionnellement a jour les inputs ou le prompt de planification
  - Si correct -> proceder a la construction

_III. Phase construction :_

Effectuee dans la boucle Ralph avec le prompt de construction standard.

##### Prompt de planification mis a jour

Variante de `PROMPT_plan.md` qui ajoute le contexte audience et la recommandation de tranche orientee SLC.

_Notes :_

- Contrairement au template par defaut, celui-ci n'a pas de placeholder `[objectif-specifique-du-projet]` -- l'objectif est implicite : recommander la prochaine release la plus precieuse pour l'audience.
- Les noms de subagents actuels presument l'utilisation de Claude.

```
0a. Study @AUDIENCE_JTBD.md to understand who we're building for and their Jobs to Be Done.
0b. Study `specs/*` with up to 250 parallel Sonnet subagents to learn JTBD activities.
0c. Study @IMPLEMENTATION_PLAN.md (if present) to understand the plan so far.
0d. Study `src/lib/*` with up to 250 parallel Sonnet subagents to understand shared utilities & components.
0e. For reference, the application source code is in `src/*`.

1. Sequence the activities in `specs/*` into a user journey map for the audience in @AUDIENCE_JTBD.md. Consider how activities flow into each other and what dependencies exist.

2. Determine the next SLC release. Use up to 500 Sonnet subagents to compare `src/*` against `specs/*`. Use an Opus subagent to analyze findings. Ultrathink. Given what's already implemented recommend which activities (at what capability depths) form the most valuable next release. Prefer thin horizontal slices - the narrowest scope that still delivers real value. A good slice is Simple (narrow, achievable), Lovable (people want to use it), and Complete (fully accomplishes a meaningful job, not a broken preview).

3. Use an Opus subagent (ultrathink) to analyze and synthesize the findings, prioritize tasks, and create/update @IMPLEMENTATION_PLAN.md as a bullet point list sorted in priority of items yet to be implemented for the recommended SLC release. Begin plan with a summary of the recommended SLC release (what's included and why), then list prioritized tasks for that scope. Consider TODOs, placeholders, minimal implementations, skipped tests - but scoped to the release. Note discoveries outside scope as future work.

IMPORTANT: Plan only. Do NOT implement anything. Do NOT assume functionality is missing; confirm with code search first. Treat `src/lib` as the project's standard library for shared utilities and components. Prefer consolidated, idiomatic implementations there over ad-hoc copies.

ULTIMATE GOAL: We want to achieve the most valuable next release for the audience in @AUDIENCE_JTBD.md. Consider missing elements and plan accordingly. If an element is missing, search first to confirm it doesn't exist, then if needed author the specification at specs/FILENAME.md. If you create a new element then document the plan to implement it in @IMPLEMENTATION_PLAN.md using a subagent.
```

##### Notes

_Pourquoi `AUDIENCE_JTBD.md` comme artefact separe :_

- Source de verite unique -- empeche la derive entre specs
- Permet un raisonnement holistique : "De quoi cette audience a-t-elle PLUS besoin ?"
- Les JTBD captures avec l'audience (le "pourquoi" vit avec le "qui")
- Reference deux fois : pendant la creation des specs ET la planification SLC
- Garde les specs d'activite focalisees sur le QUOI, sans repeter le QUI

_Cardinalites :_

- Une audience -> plusieurs JTBD ("Designer" a "capturer l'espace", "explorer des concepts", "presenter au client")
- Un JTBD -> plusieurs activites ("capturer l'espace" inclut upload, mesures, detection de piece)
- Une activite -> peut servir plusieurs JTBD ("uploader une photo" sert a la fois "capturer" et "rassembler de l'inspiration")

---

### Audit des specs

Un mode de boucle dedie pour generer et maintenir les fichiers spec avec des regles de qualite appliquees. Assure que les specs restent focalisees sur les resultats comportementaux (pas les details d'implementation), les sujets correctement scopes (test "une phrase sans 'et'"), et les conventions de nommage coherentes.

_Quand l'utiliser :_ Apres avoir ecrit ou mis a jour des specs, lancer le mode specs pour appliquer la coherence et l'hygiene a travers tous les fichiers spec.

_Ce que ca fait :_

- Itere sur les fichiers `specs/*` existants
- Applique les regles de qualite : resultats comportementaux uniquement, pas de blocs de code, pas de details d'implementation
- Valide le scoping des sujets en utilisant le test "Une phrase sans 'Et'"
- Cree de nouveaux fichiers spec si necessaire base sur `specs/README.md`
- Applique un nommage de fichier coherent : `<int>-nom-fichier.md` (par ex. `01-range-optimization.md`)

_Usage :_ Ajouter un argument `specs` a votre script de boucle qui selectionne `PROMPT_specs.md` :

```bash
./loop.sh specs        # Mode specs, iterations illimitees
./loop.sh specs 3      # Mode specs, max 3 iterations
```

_Pour ajouter le mode specs a `loop.sh` :_ inserer une nouvelle branche `elif` dans l'analyse des arguments :

```bash
# Analyser les arguments
if [ "$1" = "plan" ]; then
    # Mode planification
    MODE="plan"
    PROMPT_FILE="PROMPT_plan.md"
    MAX_ITERATIONS=${2:-0}
elif [ "$1" = "specs" ]; then        # <- ajouter ce bloc
    # Mode specs
    MODE="specs"
    PROMPT_FILE="PROMPT_specs.md"
    MAX_ITERATIONS=${2:-0}
elif [[ "$1" =~ ^[0-9]+$ ]]; then
    # Mode build avec max iterations
    ...
```

_Pour ajouter le mode specs a `loop_streamed.sh` :_ meme changement -- ajouter le bloc `elif` a la meme position. Le reste du script (streaming, pipe `parse_stream.js`) fonctionne sans changement.

#### Template `PROMPT_specs.md`

_Notes :_

- Les specs definissent QUOI verifier (resultats), pas COMMENT implementer (approche). Les decisions d'implementation sont laissees a Ralph pendant la phase de construction.

```
0a. Study `specs/*` with up to 250 parallel Sonnet subagents to learn the application specifications.

1. Identify Jobs to Be Done (JTBD) → Break individual JTBD into topic(s) of concern → Use subagents to load info from URLs into context → LLM understands JTBD topic of concern: subagent writes specs/FILENAME.md for each topic.

## RULES (don't apply to `specs/README.md`)

999. NEVER add code blocks or suggest how a variable should be named. This will be decided by Ralph.

9999.
- Acceptance criteria (in specs) = Behavioral outcomes, observable results
for example:
✓ "Extracts 5-10 dominant colors from any uploaded image"
✓ "Processes images <5MB in <100ms"
✓ "Handles edge cases: grayscale, single-color, transparent backgrounds"
- Test requirements (in plan) = Verification points derived from acceptance criteria
for example:
✓ "Required tests: Extract 5-10 colors, Performance <100ms"
- Implementation approach (up to Ralph) = Technical decisions
example TO AVOID:
✗ "Use K-means clustering with 3 iterations"

99999. Topic Scope Test: "One Sentence Without 'And'"
Can you describe the topic of concern in one sentence without conjoining unrelated capabilities?
example to follow:
✓ "The color extraction system analyzes images to identify dominant colors"
example to avoid:
✗ "The user system handles authentication, profiles, and billing" → 3 topics
If you need "and" to describe what it does, it's probably multiple topics

99999999. The key: Specify WHAT to verify (outcomes), not HOW to implement (approach). This maintains "Let Ralph Ralph" principle - Ralph decides implementation details while having clear success signals.

99999999999. Apply all rules to all existing files with up to 100 parallel Sonnet subagents in @specs (except README.md) and create new files if determined its needed based on `specs/README.md`. The names of the files should follow this name convention: <int>-filename.md, for example 01-range-optimization.md, 02-adaptive-behavior.md etc.
```

-- contribue par [@terry-xyz](https://github.com/terry-xyz) et [@blackrosesxyz](https://x.com/blackrosesxyz)

---

### Reverse engineering de projets brownfield en specs

C'est facile de commencer a travailler avec des specs en greenfield, mais quand vous travaillez en brownfield, il faut une autre approche. C'est pourquoi vous devez faire le reverse engineering des implementations du code en specs pour commencer a utiliser le playbook Ralph.

_Quand l'utiliser :_ Vous avez herite ou rejoint une codebase sans specs. Vous voulez utiliser Ralph sur un projet qui n'a pas ete construit avec Ralph. Vous devez ajouter des features a un projet brownfield existant.

_Invoquer :_ "Reverse-engineer les specs pour [sujet/domaine] en utilisant `PROMPT_reverse_engineer_specs.md`"

_Flow :_

1. Pointer l'agent sur la codebase existante avec `PROMPT_reverse_engineer_specs.md` ->
2. L'agent investigue le code (conscient de l'implementation) ->
3. L'agent ecrit les specs decrivant le comportement reel (sans implementation) ->
4. Les specs atterrissent dans `specs/` ->
5. Repeter autant que necessaire pour toutes les specs
6. Proceder avec les phases Ralph normales (plan -> build) contre la baseline documentee

Vous pouvez utiliser un pattern d'orchestration d'agents ou le sous-agent est le reverse engineer et l'orchestrateur connait la philosophie des sujets de preoccupation :

- **Couverture complete du domaine :** Dire a l'orchestrateur d'identifier la liste des sujets du domaine, puis deleguer a des sous-agents la creation de specifications completes pour chaque sujet.
- **Couverture scopee par tache :** Fournir une tache specifique que vous allez effectuer et faire analyser la codebase par l'agent, trouver les sujets pertinents, puis creer/mettre a jour chaque spec respective.

Aucune modification des fichiers prompt existants necessaire -- c'est purement additif. Les specs generees sont au meme format que ce que Ralph consomme deja dans les phases de planification et construction.

#### Considerations

- **Structures mono-repo :** Peut necessiter de scoper le reverse engineering a des packages ou services specifiques plutot qu'au repo entier. Pointer l'agent vers le sous-dossier pertinent.
- **Generation de specs pour un domaine entier :** Generer des specs pour un domaine entier est un investissement plus important -- ca vaut le coup si votre equipe adopte Ralph comme workflow standard.
- **Developpement rapide ou petits changements :** Les petits changements de code peuvent deriver des specs generees. Decider en amont si votre equipe va relancer le reverse engineering pour garder les specs a jour, ou accepter une derive temporaire.
- **Obsolescence des specs apres refactors :** Une fois que Ralph construit de nouvelles features par-dessus les specs reverse-engineerees, les refactors majeurs peuvent invalider les specs silencieusement. Relancer le reverse engineering periodiquement sur les zones fortement modifiees.
- **Granularite des sujets :** Le prompt applique strictement "un sujet par spec". Sur une grande codebase, decider ou tracer les limites des sujets est un choix de jugement -- trop large et les specs deviennent ingereables, trop etroit et vous noyez sous les fichiers. Commencer large et decouper si necessaire.
- **Les bugs deviennent des specs :** Le prompt documente intentionnellement le comportement bugge comme le comportement defini. Les specs reverse-engineerees decrivent ce qui *est*, pas ce qui *devrait etre*. Ecrire de nouvelles specs separement pour les changements de comportement desires.
- **Cout en tokens sur les grandes codebases :** Le tracage exhaustif du code avec des sous-agents peut bruler des tokens significatifs. Scoper d'abord aux zones que vous planifiez reellement de modifier.

#### Compatibilite avec la philosophie de base

| Principe                | Maintenu ? | Comment                                                                                                     |
| ----------------------- | ---------- | ----------------------------------------------------------------------------------------------------------- |
| Setup deterministe      | Oui        | Les specs sont des artefacts ecrits (etat connu), pas du contexte ad-hoc, contient tous les defauts du code |
| Efficacite du contexte  | Partiel    | Doit etre adopte a travers toute la culture de l'equipe                                                     |
| Capturer le pourquoi    | Partiel    | Pas tout le code implemente contient le pourquoi, capture les commentaires seulement s'ils expriment l'intention |
| Laisser Ralph Ralph     | Oui        | Les sujets de preoccupation sont toujours choisis par Ralph                                                  |
| Plan jetable            | Oui        | Les specs fournissent une baseline stable ; les plans se regenerent contre la realite documentee             |
| La simplicite gagne     | Oui        | Fournit une vue d'ensemble de toutes vos specifications                                                      |

#### Template `PROMPT_reverse_engineer_specs.md`

_Notes :_

- Documente le comportement reel du code (bugs inclus) -- pas le comportement prevu
- Processus en deux phases : Phase 1 investigue avec acces complet au code, Phase 2 ecrit les specs avec zero detail d'implementation
- Un sujet par spec, applique par le test "une phrase sans 'et'"
- Les noms de subagents actuels presument l'utilisation de Claude

```
0a. Study `specs/*` with up to 250 parallel Sonnet subagents to learn existing specifications.
0b. Study `src/*` to understand the codebase. Use up to 500 parallel Sonnet subagents for reads/searches. Treat `src/lib` as the project's standard library for shared utilities and components.

1. For each topic assigned (or discovered), reverse-engineer the source code and produce a specification in `specs/`. Use Opus subagents for complex tracing. Ultrathink. Before writing a spec, search to confirm one doesn't already exist for that topic.
2. One topic per spec. Must pass the "one sentence without 'and'" test. Split if "and" joins unrelated capabilities.
3. **Two-phase process:** Phase 1 (Investigation) — trace every entry point, branch, code path to terminal. Map data flow, side effects, state mutations, error handling, concurrency, config-driven paths, implicit behavior. Phase 2 (Output) — zero implementation details. No function/class/variable names, file paths, library/framework references. A different team on a different stack must be able to reimplement from the spec alone.
4. **Document reality, not intent.** Bugs are features. Never add behaviors the code doesn't implement. Never suggest improvements. If a source comment contradicts the code, document the code's behavior and ignore the comment.
5. **Scope boundaries:** When tracing leaves the topic, stop. Document what crosses the boundary (sent/received) only. Test: "Could this change without changing my topic's outcomes?" If yes, it's across the boundary.
6. **Shared behavior:** Inline fully in every spec (self-contained). Note shared topics for cross-spec tracking. Shared behavior also gets its own canonical spec.
7. **Spec format:** Markdown in `specs/`. Each spec includes: topic statement, scope (in-scope and boundaries), data contracts, behaviors (in execution order), and state transitions. Mark notable/surprising behavior, unreachable paths, and shared cross-topic behavior inline. Capture rationale from source comments (strip implementation references). File naming: `specs/NN-kebab-case.md` (e.g., `01-session-management.md`).
8. When specs are complete and validated, `git add` all the specs updated/created then `git commit` with a message describing which specs were added/updated.

99999. **Exhaustive checklist before finalizing:** Every entry point documented. Every branch traced to terminal. Every data contract. Every side effect in execution order. Every error path (caught/propagated/ignored). Every config-driven path. Concurrency outcomes. Unreachable paths marked. Notable/surprising behavior marked. Zero implementation details in output. If any item is missing, trace again.
999999. The code is the source of truth. If specs are inconsistent with the code, update the spec using an Opus 4.6 subagent.
9999999. Single sources of truth, no duplicated specs. Update existing specs rather than creating new ones.
99999999. When you learn something new about the project, update @AGENTS.md using a subagent but keep it brief and operational only — no status updates or progress notes.
999999999. Source comments explaining why behavior must be preserved (regulatory, compatibility, intentional) — capture rationale, strip implementation references. Stale comments are not spec.
9999999999. Document all configuration-driven paths, not just the currently active one.
99999999999. If you find inconsistencies in `specs/*` then use an Opus 4.6 subagent with 'ultrathink' to update the specs.
```

-- contribue par Jake Cukjati, [@Byte0fCode](https://x.com/Byte0fCode) et [@jackstine](https://github.com/jackstine)

---

## Environnements sandbox

_Modele de securite :_ La sandbox (Docker/E2B) fournit la frontiere de securite. Dans la sandbox, Claude tourne avec les permissions completes parce que le conteneur lui-meme est isole.

_Philosophie de securite :_

> "La question n'est pas si ca va etre compromis, mais quand. Et quel est le rayon de l'explosion ?"

Tourner sur des VMs dediees ou des sandboxes Docker locales. Restreindre la connectivite reseau, fournir uniquement les credentials necessaires, et assurer aucun acces aux donnees privees au-dela de ce que la tache requiert.

### Options

#### Sprites (Fly.io)

- Environnements Linux persistants qui survivent entre les executions indefiniment
- Isolation Firecracker VM avec jusqu'a 8 vCPUs et 8GB RAM
- Checkpoint/restore rapide (~300ms creation, <1s restore)
- Auto-sleep apres 30 secondes d'inactivite
- URL HTTPS unique par Sprite pour webhooks, APIs, acces public
- Politiques reseau Layer 3 pour controle egress (whitelist de domaines ou liste LLM-friendly par defaut)
- CLI, REST API, JavaScript SDK, Go SDK (Python et Elixir bientot)
- Outils pre-installes : Claude Code, Codex CLI, Gemini CLI, Python 3.13, Node.js 22.20
- 30$ de credits gratuits pour commencer (~500 Sprites)

#### E2B

- Concu specialement pour les agents IA et workflows LLM
- Template pre-construit `anthropic-claude-code` livre avec Claude Code CLI pret
- Appels SDK en une ligne en Python ou JavaScript (v1.5.1+)
- Filesystem complet + git pour progress.txt, prd.json et operations repo
- Limites de session de 24h sur le plan Pro (1h sur Hobby)
- Acces natif a 200+ outils MCP via partenariat Docker (GitHub, Notion, Stripe, etc.)
- Compute configurable : 1-8 vCPU, 512MB-8GB RAM

#### exe.dev

- Plateforme VM persistante par David Crawshaw (ancien CTO Tailscale) et Josh Bleecher Snyder
- Creation de VM en ~2 secondes
- Stockage disque persistant (pas ephemere)
- Interface native SSH (`ssh exe.dev`)
- TLS automatique et domaines personnalises
- Agent IA Shelley inclus (web, mobile-friendly)
- Pas de SDK necessaire -- interaction purement SSH

#### Docker (local)

```bash
docker sandbox run claude                  # Basique
docker sandbox run -w ~/mon-projet claude  # Workspace personnalise
docker sandbox run claude "votre tache"    # Avec prompt
docker sandbox run claude -c               # Continuer la derniere session
```

- Credentials stockees dans le volume persistant `docker-claude-sandbox-data`
- `--dangerously-skip-permissions` active par defaut
- Image de base inclut : Node.js, Python 3, Go, Git, Docker CLI, GitHub CLI, ripgrep, jq
- Le conteneur persiste en arriere-plan ; relancer reutilise le meme conteneur
- Utilisateur non-root avec acces sudo

### Tableau comparatif

| Caracteristique  | Sprites             | E2B                 | Docker Local           |
| ---------------- | ------------------- | ------------------- | ---------------------- |
| Setup            | Facile              | Tres facile         | `docker sandbox run`   |
| Free tier        | 30$ credit          | 100$ credit         | Gratuit (votre materiel) |
| Isolation        | Firecracker microVM | Firecracker microVM | Conteneur              |
| Cold Start       | <1 seconde          | ~150ms              | Illimite               |
| Max Timeout      | Aucun (persistant)  | 24h (Pro)           | Illimite               |
| Claude CLI       | Pre-installe        | Template pre-construit | Manuel              |
| Fichiers persistants | Oui (permanent) | 24h                 | Base volume            |
| Meilleur pour    | Agents long-running | Boucles agent IA    | Dev local, prototypage |

### Recommandation

- **Pour la production/multi-tenant :** E2B -- template Claude Code zero-setup, sessions 24h, isolation vraie, 200+ outils MCP
- **Pour les agents persistants long-running :** Sprites -- pas de limites de session, snapshots transactionnels, auto-sleep, outils IA pre-installes
- **Pour le developpement local :** Docker Sandboxes -- `docker sandbox run claude`, gratuit, duree illimitee
