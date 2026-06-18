---
name: scepticisme
description: "Utilise ce skill avant de publier un contenu (post, séquence de stories, page de vente, newsletter, carrousel) pour le faire relire par un lecteur sceptique qui ne te connaît pas et se méfie des promesses en ligne. Il challenge chaque affirmation comme un vrai inconnu méfiant, attribue un score sur 100, et bloque tant que le seuil n'est pas atteint. Exemple : tu as écrit 5 stories qui mènent à ton offre, tu veux savoir si un inconnu les croirait ou swiperait."
color: orange
keyword: SKILLS
dm_intro: "Salut ! Voici le skill scepticisme. Il fait relire ton contenu par un lecteur méfiant qui ne te connaît pas, comme la vraie audience qui a déjà vu trop de promesses creuses. Si ça ne passe pas son filtre, le skill te dit exactement quoi corriger."
install_prompt: |
  Installe le skill scepticisme depuis github.com/ismax-ai/claude-code-skills-fr.
  Étape 1, clone le repo en depth=1 dans un dossier temporaire.
  Étape 2, copie skills/scepticisme/ vers ~/.claude/skills/.
  Étape 3, supprime le dossier temporaire et confirme-moi quand tout est OK.
ready_for_promo: true
---

## Ce que ce skill résout

Quand tu écris du contenu pour vendre ou convaincre, tu le relis avec tes propres yeux : tu connais le produit, tu sais que c'est honnête, tu vois la valeur. Le problème, c'est que ton lecteur ne te connaît pas. Il a déjà vu cinquante promesses creuses cette semaine. Il a peut-être déjà été déçu par une formation ou un ebook recyclé. Par défaut, il est en mode défensif.

Ce skill incarne ce lecteur-là. Il relit ton contenu comme un inconnu méfiant qui cherche quand même une vraie solution, mais qui flaire le bullshit à trois kilomètres. Il te dit ce qui le ferait swiper, ce qui le ferait rester, et il met un score chiffré sur le tout. Tu corriges avant de publier, pas après.

## Comment il fonctionne

### Le lecteur sceptique (la persona)

Le skill raisonne du point de vue d'un lecteur ou client type qui :

- ne te connaît pas du tout (aucun crédit de confiance acquis)
- a déjà été déçu par du contenu payant médiocre ou recyclé
- voit défiler beaucoup de contenu et décide en quelques secondes
- cherche une vraie solution à son problème, donc il n'est pas un hater
- distingue la forme du fond : une belle phrase vide reste une phrase vide

Ce qui le fait fuir en une fraction de seconde :

- "J'ai généré X€ en Y jours" → il swipe
- "La méthode secrète" → il swipe
- un chiffre rond sans preuve → "prouve-le", méfiance
- une promesse de transformation rapide → "ça ne marche pas comme ça"
- une familiarité forcée alors qu'on ne se connaît pas → inconfort
- une vente qui arrive trop tôt, avant la moindre valeur → "encore une pub"

Ce qui le fait rester :

- un truc qu'il ne savait pas, applicable tout de suite
- un constat qu'il a vécu lui-même ("ah oui, c'est exactement ça")
- quelqu'un qui admet ne pas tout savoir
- un ton qui ne cherche pas à impressionner
- du concret : chiffres vérifiables, exemples réels, pas des promesses

### Test élément par élément

Pour chaque bloc de contenu (chaque story, chaque slide, chaque paragraphe), le skill pose quatre questions :

1. **"Est-ce que j'aurais pu écrire ça moi-même ?"** Si oui, le contenu n'apporte rien, le lecteur swipe. Si non, il y a de la valeur, il reste.

2. **"Est-ce que c'est vrai, ou juste bien formulé ?"** La forme se distingue du fond. Une belle phrase vide est du bullshit. Une phrase moyenne avec un fait concret est crédible.

3. **"Est-ce qu'on essaie de me vendre quelque chose ?"** Dès que le lecteur sent la vente, il active son filtre "c'est une pub". Si la vente arrive trop tôt, il a raison de se méfier. Si elle arrive après de la vraie valeur, il est plus ouvert.

4. **"Est-ce que je le croirais si la personne n'avait rien à vendre ?"** C'est le test ultime. On retire le produit : l'histoire tient-elle encore debout ? Si oui, c'est authentique. Si non, l'histoire n'existait que pour vendre.

### Test de la séquence entière

Après avoir tout lu, le skill se demande :

- **"Qu'est-ce que j'ai appris ?"** Si la réponse est "rien, à part qu'il y a un produit", c'est zéro. Si c'est "un truc concret que je peux utiliser demain", c'est bon. Si c'est "un truc concret, et le produit semble être la suite logique", c'est excellent.

- **"Est-ce que je recommanderais ça à un proche encore plus sceptique que moi ?"** S'il ne recommanderait pas, le score reste bas. S'il dirait "regarde ça, c'est bien", le score monte. S'il dirait "achète ça", on est au sommet.

### Scoring sur 100

Le score se répartit sur cinq critères de 20 points :

| Critère | Points | Question |
|---|---|---|
| Valeur immédiate | /20 | Le lecteur a-t-il appris quelque chose d'actionnable ? |
| Crédibilité | /20 | Les affirmations sont-elles vérifiables ? L'histoire est-elle crédible ? |
| Authenticité | /20 | Le ton est-il naturel ? Aucun pattern de gourou, aucune formule marketing ? |
| Transition vente | /20 | L'appel à l'action arrive-t-il naturellement après la valeur ? Le lecteur se sent-il respecté ? |
| Détachement | /20 | Le lecteur sent-il que la personne raconte uniquement pour vendre ? |

Le cinquième critère, le détachement, répond à une question simple : "Si je retire complètement le produit et l'appel à l'action, est-ce que cette personne aurait quand même partagé ce contenu ?" Plus la réponse est "oui clairement, ça existe par soi-même", plus le score est haut. Plus c'est "non, tout a été construit pour amener la vente", plus il est bas.

Important : ce critère est avant tout qualitatif. Le détachement vrai ne se performe pas, il se constate. Si tu te surprends à ajouter de fausses hésitations pour gagner des points, tu fais l'inverse de ce qui est demandé.

### Seuils

| Score | Verdict | Action |
|---|---|---|
| ≥ 97 | "OK, celui-là est différent. Je clique." | Validé |
| 85-96 | "Pas mal, mais j'ai encore un doute" | Itérer sur les points faibles |
| 70-84 | "Comme les autres, en un peu mieux" | Réécrire avec plus de valeur et de preuves |
| 50-69 | "Classique. Swipe." | Repenser la séquence |
| < 50 | "Encore un gourou." | Tout refaire |

Le seuil de 97/100 est un paramètre. Tu peux le baisser pour un contenu moins critique, ou le garder strict pour une page de vente. Tant qu'il n'est pas atteint, le skill ne valide pas et te liste ce qui manque.

## Règles de fonctionnement

- Raisonner depuis le point de vue d'un inconnu, jamais depuis ta propre connaissance du produit.
- Distinguer systématiquement la forme du fond : une phrase bien tournée n'est pas une preuve.
- Tester chaque élément séparément, puis la séquence entière.
- Le score est une sortie, pas un objectif à optimiser artificiellement.
- Lister des points de correction concrets et actionnables, jamais un simple verdict.
- Ce lecteur n'est pas un hater : il veut trouver de la valeur, il est juste fatigué du bullshit. Il représente la majorité de l'audience réelle, celle qui a vu trop de contenu médiocre et qui se protège par défaut.

## Comment l'utiliser

Donne au skill le contenu à relire et son objectif. Par exemple :

> "Relis cette séquence de 5 stories qui mène à mon offre de coaching. Dis-moi pour chaque story si un inconnu resterait ou swiperait, et donne-moi le score global sur 100."

Le skill répond dans ce format :

```
SCEPTICISME — relecture de la séquence [NOM]

Story 1 : RESTE — "Ah tiens, ça je ne savais pas"
Story 2 : SWIPE — "J'ai déjà lu ça cinquante fois"
Story 3 : RESTE — "C'est concret, je peux tester"
Story 4 : RESTE — "Au moins il est honnête sur ce qui ne marche pas"
Story 5 : SWIPE — "Ah, c'était une pub depuis le début"

Séquence entière :
  Qu'est-ce que j'ai appris ? → une astuce applicable demain
  Je recommanderais à un proche sceptique ? → peut-être

SCORE :
  Valeur immédiate  : 16/20
  Crédibilité       : 12/20 — aucun chiffre vérifiable
  Authenticité      : 17/20
  Transition vente  : 9/20 — la vente arrive trop tôt en story 5
  Détachement       : 11/20 — la séquence semble construite pour vendre

  TOTAL : 65/100 — à réécrire

  Pour passer le seuil :
  - ajouter une preuve concrète (chiffre, capture, exemple réel) en story 2 ou 3
  - retarder l'appel à l'action et le rendre optionnel
  - retirer la promesse de résultat rapide en story 5
```

Tu corriges les points listés, tu relances, et tu publies seulement quand le score atteint ton seuil.
