---
name: anti-bullshit
description: "Utilise ce skill juste après avoir rédigé un contenu commercial (story, post, page de vente, séquence d'emails, caption) et AVANT de le publier, pour détecter l'oversell, les fausses promesses, le langage marketing creux et les histoires trop lisses pour être vraies. Exemple : tu viens d'écrire une séquence de stories qui finit sur un lien d'achat, tu lances ce skill, il te dit que 'va transformer ta vie' est de l'amplification non prouvée et te propose une formulation concrète à la place."
color: red
keyword: SKILLS
dm_intro: "Salut ! Voici le skill anti-bullshit. Il relit ton contenu commercial avant publi et te signale tout ce qui sonne faux, exagéré ou trop marketing, avec une reformulation concrète pour chaque problème."
install_prompt: |
  Installe le skill anti-bullshit depuis github.com/ismax-ai/claude-code-skills-fr.
  Étape 1, clone le repo en depth=1 dans un dossier temporaire.
  Étape 2, copie plugins/ismax-skills-fr/skills/anti-bullshit/ vers ~/.claude/skills/.
  Étape 3, supprime le dossier temporaire et confirme-moi quand tout est OK.
ready_for_promo: true
---

> Le bullshit, ce n'est pas le mensonge. Le menteur connaît la vérité et la cache. Le bullshiteur, lui, se moque de la vérité : il dit ce qui SONNE bien.
> Source : Harry Frankfurt, "On Bullshit" (2005).

## Ce que ce skill résout

Quand tu écris un contenu qui vend (une story, un post, une page de vente, un email), tu glisses sans t'en rendre compte vers des formules qui impressionnent mais ne prouvent rien. "Ça va transformer ta façon de bosser." "Le secret que personne ne dit." "J'étais exactement comme toi." Ces phrases passent inaperçues quand tu les écris, parce qu'elles sonnent bien. Mais ton lecteur, lui, les repère. Et chaque formule creuse grignote ta crédibilité.

Ce skill relit ton contenu commercial sur 5 couches et te signale précisément ce qui sonne faux, pourquoi, et comment le réécrire de façon concrète. Il ne te demande pas de devenir fade : il te demande de remplacer le creux par du vérifiable.

## Comment il fonctionne

Le contenu passe dans 5 couches d'analyse, de la plus mécanique à la plus humaine.

### Couche 1 — Lexicale

Scan du texte pour les mots à haute densité de bullshit, classés en 4 familles.

| Famille | Exemples | Pénalité |
|---|---|---|
| **Amplification** | transformer, révolutionner, exploser, game-changer, incroyable, extraordinaire, ultime, radical, "dingue" pour un produit | -5 pts par mot |
| **Fausse urgence** | dernière chance, ne rate pas, il ne reste que, cette offre expire (si c'est faux), places limitées (si c'est faux), maintenant ou jamais | -8 pts par mot |
| **Fausse autorité** | les experts disent, des études montrent, tout le monde sait, "c'est prouvé" sans source, scientifiquement | -5 pts par mot |
| **Histoire trop lisse** | j'étais comme toi, j'ai trouvé LA solution, tout a changé, ma vie a basculé, le secret c'est, il suffit de, tu n'as qu'à | -5 pts par mot |

Score de la couche : tu pars de 100, tu soustrais les pénalités.
- 80 et plus → propre
- 60 à 79 → à reformuler
- En dessous de 60 → trop chargé, on réécrit

### Couche 2 — Structurelle

**Le test de l'inversion.** Pour chaque phrase qui parle du produit, remplace le produit par "[un produit X]". Si la phrase a encore du sens et pourrait vendre n'importe quoi, c'est qu'elle est générique. Tu la réécris avec un détail propre à TON produit.
- Générique : "Ça va changer ta manière de travailler." (marche pour tout)
- Spécifique : "Chaque modèle a déjà le rôle, l'objectif, le ton et le format remplis." (ne marche que pour ça)

**Le faux récit héroïque.** Le schéma galère → déclic → solution → transformation est légitime quand l'histoire est vraie. Il devient du bullshit quand la galère est exagérée, le déclic trop propre ("et là j'ai compris"), ou la transformation disproportionnée ("tout a changé"). Chaque étape doit être crédible. "J'ai perdu 20 minutes" passe (c'est banal). "Ma vie a changé" bloque, sauf preuve concrète.

**L'universalisation abusive.** "On a TOUS ce problème", "tout le monde galère avec ça", "tu as forcément vécu ça" : non, pas forcément. On remplace le "tous" universel par un "si tu" conditionnel.

### Couche 3 — Factuelle

Pour chaque chiffre et chaque affirmation, tu poses la question de la source.
- Un chiffre sans source → à signaler.
- Une affirmation non vérifiable ("ça m'a changé la vie") → à prouver ou à retirer.
- Un repère de temps ("ce matin", "hier", "la semaine dernière") → doit correspondre à la réalité.

Chaque affirmation non vérifiée fait baisser le score. Le principe de fond : "je ne sais pas" vaut toujours mieux qu'une invention.

### Couche 4 — Mémoire des formulations

Le skill garde la trace des formulations déjà jugées, dans un petit fichier au format JSON :
- les patterns **rejetés** (avec la raison : pourquoi c'est du bullshit),
- les patterns **approuvés** (avec le contexte où ils ont marché).

Avant chaque analyse, il compare ton texte aux deux listes. Plus une phrase ressemble à un pattern déjà rejeté, plus elle est suspecte. Quand tu valides ou rejettes une formulation, tu l'ajoutes à la liste correspondante : le skill apprend tes préférences au fil du temps.

Exemple de fichier (`~/.claude/data/anti-bullshit-memory.json`) :

```json
{
  "rejected_patterns": [
    {
      "pattern": "va transformer ta manière de [verbe]",
      "reason": "Amplification, aucune preuve que ça transforme quoi que ce soit."
    },
    {
      "pattern": "j'étais exactement comme toi + et là tout a changé",
      "reason": "Récit héroïque forcé, trop lisse pour être vrai."
    }
  ],
  "approved_patterns": [
    {
      "pattern": "Tu copies. Tu colles. C'est prêt.",
      "context": "Description concrète d'un produit, zéro promesse."
    }
  ]
}
```

### Couche 5 — Le sceptique

La dernière couche fait relire la séquence complète par un lecteur méfiant. C'est le skill `/scepticisme` : il incarne quelqu'un qui ne te connaît pas, qui s'est déjà fait avoir par un formateur en ligne, et qui cherche quand même une vraie solution. Il note sur 4 critères :
- Valeur immédiate : a-t-il appris quelque chose ?
- Crédibilité : les affirmations tiennent-elles ?
- Authenticité : le ton est-il unique ou interchangeable ?
- Transition vers la vente : l'appel à l'action est-il naturel ou forcé ?

Si le sceptique n'est pas convaincu, on itère.

## Règles de fonctionnement

- Chaque problème signalé vient avec une reformulation concrète, jamais juste un "c'est mauvais".
- On remplace le creux par du vérifiable, on ne supprime pas la personnalité du texte.
- Un détail précis ("je gagne 15 minutes par email") bat toujours un superlatif ("c'est fluide").
- Le lecteur doit pouvoir ignorer ton appel à l'action sans se sentir manipulé : c'est une invitation, pas une injonction.
- "Je ne sais pas" reste préférable à une affirmation inventée.

## Comment le bullshit se cache (détection avancée)

Le bullshit avancé ne crie pas "ACHÈTE MAINTENANT". Il se cache dans :

1. **La fausse vulnérabilité.** "Je galère aussi" suivi aussitôt de "mais j'ai trouvé LA solution". Test : cette vulnérabilité existerait-elle si tu n'avais rien à vendre ? Si elle ne sert qu'à vendre, c'est du bullshit.
2. **Le chiffre impressionnant hors contexte.** "200 modèles" est-il beaucoup, ou 200 variantes de la même chose ? Test : le chiffre impressionne-t-il APRÈS explication, ou seulement avant ?
3. **La fausse précision.** "J'ai perdu exactement 20 minutes" : le "exactement" rend crédible un chiffre inventé. Test : enlève le détail, l'histoire tient encore ? Alors le détail était cosmétique.
4. **Le avant/après disproportionné.** "Avant je galérais, après tout est fluide." Test : le "après" est-il mesurable ? "Fluide" ne veut rien dire.
5. **L'empathie instrumentalisée.** "Je sais ce que tu ressens", dit par quelqu'un qui te vend un truc. Test : cette empathie existerait-elle sans le produit ?

## Check de la séquence entière

En plus de chaque message pris isolément, le skill regarde la séquence complète (utile pour des stories ou une suite d'emails) :

| Check | Question | Seuil |
|---|---|---|
| Escalade émotionnelle | L'émotion monte-t-elle trop vite du début à la fin ? | Max 2 crans d'escalade |
| Cohérence du personnage | Tu es la même personne au début et à la fin ? | Même ton, même registre |
| Rapport valeur / vente | Combien de messages donnent vs combien vendent ? | Au moins 60 % de valeur |
| Cohérence dans le temps | L'histoire racontée est-elle temporellement plausible ? | Pas de saut de 3 ans en 5 messages |
| Sortie sans pression | Le lecteur peut-il ignorer l'appel à l'action sereinement ? | Oui |

## Comment l'utiliser

Tu viens d'écrire une séquence de 5 stories qui se termine par "Dispo dans le lien en bio". Tu lances le skill sur le texte complet. Il te rend un rapport du type :

```
ANTI-BULLSHIT — Séquence "lancement guide"

Couche 1 (lexicale) : 72/100
  Mots signalés : "révolutionner" (story 2), "tout a changé" (story 4)

Couche 2 (structurelle) :
  Test inversion : ÉCHEC sur story 1 → "ça va changer ta vie" marche pour tout
  Récit héroïque : forcé (story 3, déclic trop propre)

Couche 3 (factuelle) : 1 chiffre sans source → "des milliers de gens"

Couche 5 (sceptique) : pas convaincu — le passage à la vente (story 5) arrive trop sec

VERDICT : à reformuler.
Corrections proposées :
  Story 1 : "ça va changer ta vie" → "tu obtiens un modèle déjà rempli, tu copies, c'est prêt"
  Story 2 : retirer "révolutionner", décrire ce que ça fait concrètement
  Story 4 : "tout a changé" → un résultat mesurable réel ou on coupe
```

Tu appliques les corrections, tu relances, et tu publies une fois que le sceptique passe.
