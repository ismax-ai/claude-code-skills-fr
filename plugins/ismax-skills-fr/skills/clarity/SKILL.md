---
name: clarity
description: "Audite la clarté d'un texte avant publication (note, post, page de vente, description produit, README, email). À utiliser quand un texte semble correct mais flou, ou quand quelqu'un dit c'est pas clair, c'est du blabla, concrètement c'est quoi. Exemple : tu écris la description d'un produit, ce skill détecte les mots vagues comme optimise ou gère et les remplace par ce que ça fait vraiment. Ne pas utiliser sur du code ou des messages de commit."
color: cyan
keyword: SKILLS
dm_intro: "Salut ! Voici le skill clarity. Il relit ton texte phrase par phrase et te dit exactement où le lecteur va décrocher, puis il te propose une version corrigée."
install_prompt: |
  Installe le skill clarity depuis github.com/ismax-ai/claude-code-skills-fr.
  Étape 1, clone le repo en depth=1 dans un dossier temporaire.
  Étape 2, copie plugins/ismax-skills-fr/skills/clarity/ vers ~/.claude/skills/.
  Étape 3, supprime le dossier temporaire et confirme-moi quand tout est OK.
ready_for_promo: true
---

## Ce que ce skill résout

Un texte peut sembler correct et rester flou. Les mots vagues forcent le lecteur à deviner ce que tu veux dire, et souvent il devine mal. "Gère les erreurs" peut vouloir dire dix choses différentes. "Optimise les performances" ne dit rien de mesurable.

Le principe est simple : si le lecteur doit relire une phrase pour la comprendre, la phrase est ratée. Chaque phrase doit se comprendre du premier coup, par quelqu'un qui ne connaît pas le sujet.

Ce skill relit ton texte phrase par phrase, repère chaque endroit où le lecteur risque de décrocher, met un score chiffré, et te propose le texte réécrit.

Il s'appuie sur des références reconnues en lisibilité : la technique Feynman (si tu ne sais pas l'expliquer à un enfant de 12 ans, tu ne l'as pas compris), les recherches du Nielsen Norman Group sur la lecture web, les règles d'écriture de George Orwell, et la théorie de la charge cognitive.

## Comment l'utiliser

Colle ton texte et demande l'audit. Par exemple :

```
/clarity sur ma description de produit : "Notre solution innovante permet
aux entreprises d'optimiser leur workflow grâce à une approche intégrée."
```

Le skill répond avec un score par dimension, la liste des phrases à problème avec leur correction, puis le texte complet réécrit.

## Comment il fonctionne

L'audit passe chaque phrase à travers 6 dimensions. Chaque vérification est binaire : la phrase passe ou elle échoue. Pas de jugement subjectif.

### Dimension 1 — Le mot concret

Les mots vagues sont le problème numéro un. Pour chaque mot porteur de sens :

- Verbe filmable : on pourrait FILMER l'action ? "supprime", "envoie", "compare" passent. "gère", "optimise", "améliore", "traite" échouent.
- Nom qui pointe : le nom désigne UN objet identifiable ? "le fichier config.json" passe. "les éléments", "le contenu" échouent.
- Pas de nom qui cache un verbe : "Claude analyse" plutôt que "Claude fait une analyse".
- Le mot le plus court qui dit la même chose.
- Un seul sens possible hors contexte.

Verbes à bannir sans complément précis : gère, optimise, améliore, traite, permet, facilite, assure, garantit, propose, offre, fournit, couvre, supporte, intègre.

Exemple : "gère les erreurs" devient "détecte l'erreur, affiche le message, relance". "optimise les performances" devient "réduit le temps de réponse de 3s à 0,5s".

### Dimension 2 — La structure de la phrase

Le cerveau lit naturellement sujet-verbe-objet. Toute déviation le force à relire.

- Qui fait quoi dans les 8 premiers mots.
- Maximum 20 mots par phrase (au-delà, la compréhension chute vite).
- Voix active : le sujet fait l'action. "Le skill détecte les failles" plutôt que "Les failles sont détectées par le skill".
- Un seul fait par phrase. Si tu as deux idées, fais deux phrases.
- L'information la plus importante en fin de phrase.

### Dimension 3 — Le signal contre le bruit

Réduire un texte de moitié peut augmenter sa lisibilité de plus de 50 %. Chaque mot inutile coûte un effort au lecteur.

- Test de suppression : si enlever le mot ne change pas le sens, enlève-le.
- Zéro mot vide : vraiment, très, assez, plutôt, effectivement, globalement.
- Zéro doublon : "gratuit" suffit, pas "gratuit et sans frais".
- Au moins un fait concret pour dix mots.
- Pas de tournure longue remplaçable par un mot : "en raison du fait que" devient "parce que", "afin de pouvoir" devient "pour", "est en mesure de" devient "peut".

Si plus de 15 % des mots sont supprimables, la phrase échoue.

### Dimension 4 — Le parcours de lecture

Les gens lisent en moyenne 28 % des mots d'une page. Si l'info clé est au milieu, presque personne ne la voit.

- Pyramide inversée : le fait le plus important dans la première phrase du bloc.
- Texte scannable : titres, listes, gras permettent de comprendre sans tout lire.
- Chaque phrase reprend un élément de la précédente pour assurer le fil.
- Un paragraphe traite un seul sujet.
- Les éléments suivent une logique (importance, chronologie, difficulté).

### Dimension 5 — Le test Feynman

L'expertise de celui qui écrit ne doit jamais devenir un présupposé pour le lecteur.

- Zéro présupposé : la phrase se comprend sans connaissance préalable.
- Chaque terme technique est défini à sa première apparition.
- Chaque idée abstraite est illustrée par un exemple concret.
- Le résultat est visualisable : "réécrit la phrase en 12 mots au lieu de 30" plutôt que "optimise le contenu".
- Test "c'est quoi concrètement ?" : si quelqu'un pose la question, la phrase répond déjà.

### Dimension 6 — La charge cognitive

Le cerveau a une capacité limitée. Chaque élément superflu la consomme, et le lecteur finit par décrocher.

- Formulation positive plutôt que double négation : "garde les phrases utiles" plutôt que "ne supprime pas les phrases qui ne sont pas inutiles".
- Pas de parenthèse de plus de 8 mots entre le sujet et le verbe.
- Listes de 5 éléments maximum, sinon grouper.
- Chaque sigle est expliqué à sa première mention.
- Rythme varié : alterne phrases courtes et phrases moyennes.

## Le scoring

Chaque dimension reçoit un pourcentage (vérifications réussies sur vérifications totales). Le score global est la moyenne des 6 dimensions.

```
AUDIT CLARTÉ — [titre du contenu]

D1 CONCRET        : __%
D2 STRUCTURE      : __%
D3 SIGNAL/BRUIT   : __%
D4 LISIBILITÉ     : __%
D5 FEYNMAN        : __%
D6 CHARGE COGN.   : __%

GLOBAL            : __%
```

Lecture du score :
- 90-100 % : cristallin, publiable tel quel.
- 75-89 % : clair, quelques corrections puis publiable.
- 60-74 % : brumeux, réécrire les phrases signalées.
- 40-59 % : opaque, réécrire le bloc entier.
- Moins de 40 % : illisible, repenser l'approche.

Seuils conseillés : 80 % pour un post ou une note courte, 85 % pour une description produit, 90 % pour une page de vente, 75 % pour un email.

## Règles de fonctionnement

- Toujours scorer avant de juger. "C'est assez clair" sans audit est une supposition.
- Les textes courts comptent le plus, ce sont les plus lus. Ne jamais les sauter.
- Technique ne veut pas dire flou. Un public technique déteste le vague autant qu'un débutant.
- Le lecteur n'a pas ton contexte. Il a la phrase, seule.
- Après l'audit, toujours proposer le texte complet réécrit, puis le rescorer pour vérifier qu'il passe le seuil.

## Exemple complet

```
AVANT :
"Notre solution innovante permet aux entreprises de toutes tailles
d'optimiser leur workflow de création de contenu grâce à une approche
intégrée qui facilite la collaboration entre les équipes."

Problèmes :
- "permet", "optimiser", "facilite" : trois verbes vagues
- "solution innovante", "approche intégrée" : noms vides
- 30+ mots en une seule phrase, quatre idées mélangées
- zéro exemple concret, zéro résultat visualisable
Score global : ~15 % (illisible)

APRÈS :
"Tu écris un post en 10 minutes au lieu de 45. Ton équipe voit le
brouillon, commente, et valide dans le même outil. Plus de va-et-vient
par email."

Score global : ~90 % (cristallin)
```

## Vérification rapide (pour une phrase isolée)

1. Qui fait quoi ? Identifiable en moins de 8 mots ?
2. C'est quoi concrètement ? Un débutant voit le résultat ?
3. Je peux couper un mot ? Chaque mot est nécessaire ?
4. Je comprends du premier coup ? Zéro relecture ?

Si une réponse est non, réécris.
