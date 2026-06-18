---
name: eli5
description: "Utilise ce skill avant de publier un contenu (post, page de vente, e-mail, légende, message) pour vérifier que chaque terme, produit ou concept que tu cites est compréhensible par quelqu'un qui ne te connaît pas. Il détecte les mots que tu emploies par habitude et que ton lecteur, lui, ne comprend pas. Exemple : tu écris « profite de mon Atelier » → le skill repère que « Atelier » n'est jamais expliqué et te propose une formulation claire (ce que c'est, ce que ça contient, sous quelle forme)."
color: green
keyword: SKILLS
dm_intro: "Voici le skill eli5. Il relit ton contenu et repère tout ce que ton lecteur ne peut pas comprendre parce que tu connais déjà la réponse. Pour chaque terme flou, il te propose une version simple, comme si tu l'expliquais à un débutant complet."
install_prompt: |
  Installe le skill eli5 depuis github.com/ismax-ai/claude-code-skills-fr.
  Étape 1, clone le repo en depth=1 dans un dossier temporaire.
  Étape 2, copie skills/eli5/ vers ~/.claude/skills/.
  Étape 3, supprime le dossier temporaire et confirme-moi quand tout est OK.
ready_for_promo: true
---

## Ce que ce skill résout

Quand tu écris sur ton activité, tu sais déjà ce que veulent dire tes mots. Tu sais ce qu'il y a derrière « mon offre », « le programme », « ma formation », « le pack ». Le problème, c'est que ton lecteur, lui, ne le sait pas. Il découvre ton contenu sans le contexte que tu as dans la tête.

C'est ce qu'on appelle la malédiction de la connaissance : plus tu maîtrises un sujet, plus il devient difficile d'imaginer ce que c'est de ne pas le maîtriser. Résultat, tu emploies des mots qui te paraissent évidents et qui laissent ton lecteur dans le flou. Et un lecteur dans le flou ne clique pas, n'achète pas, ne s'inscrit pas.

Ce skill relit ton contenu à la place d'un débutant complet. Il repère chaque terme, nom de produit ou concept que tu utilises sans l'expliquer, et il te propose une version claire que n'importe qui peut comprendre.

## Comment il fonctionne

### 1. Le test du lecteur qui ne te connaît pas

Le skill évalue ton texte du point de vue d'une personne qui te découvre pour la première fois et qui ne te fait pas spontanément confiance. Pour chaque élément que tu cites, il se pose trois questions simples :

- Est-ce qu'elle comprend de quoi tu parles, sans avoir besoin d'aller chercher ailleurs ?
- Est-ce qu'elle voit concrètement ce qu'elle obtient ?
- Est-ce qu'elle voit les informations utiles : le prix, la fréquence, le format, la durée ?

Si la réponse est non sur l'un des trois, c'est un point à corriger.

### 2. La règle des trois passages

Chaque fois qu'un produit, une offre ou un concept que tu as créé apparaît dans un contenu public, il doit être :

1. **Nommé** — on dit clairement de quoi il s'agit.
2. **Expliqué simplement** — une mini-définition que comprend un débutant, glissée juste à côté du nom.
3. **Chiffré quand c'est pertinent** — prix, fréquence, nombre, durée.

### 3. L'audit, étape par étape

Le skill suit toujours le même déroulé :

1. Il liste tous les noms propres, termes et concepts cités dans le contenu.
2. Pour chacun, il vérifie : est-il expliqué dans le texte ? L'explication est-elle compréhensible sans pré-requis ? Le lecteur voit-il l'intérêt ?
3. Là où il manque quelque chose, il ajoute une courte définition dans le fil du texte, sans alourdir.

### 4. Quand l'appliquer

C'est **indispensable** sur :

- La première fois qu'un produit ou une offre est mentionnée dans une séquence.
- Tout contenu qui peut être vu hors contexte (un post isolé, une page de vente, une légende).
- Les messages adressés à des gens qui ne te connaissent pas encore.

C'est **facultatif** sur :

- Les rappels d'un produit déjà expliqué plus haut dans le même contenu.
- Une audience qui te suit déjà et connaît tes offres.

## Règles de fonctionnement

- Ne jamais supposer que le lecteur partage ton contexte. Tu le connais, lui non.
- Expliquer le terme là où il apparaît, pas dans une note de bas de page qu'on ne lit pas.
- Garder l'explication courte. Une phrase glissée à côté du nom suffit le plus souvent.
- Mieux vaut une définition simple de trop qu'un lecteur perdu.

## Anti-patterns à corriger

- « Le pack » sans dire ce qu'il contient.
- Un nom d'offre cité sans aucune explication.
- « Mes ressources » sans en préciser le nombre ni le domaine.
- « L'article » sans en donner le titre ou le sujet.
- Un nom d'outil technique cité sans dire à quoi il sert.

## Comment l'utiliser

Tu colles ton contenu dans Claude et tu écris une instruction simple, par exemple :

```
Applique le skill eli5 sur ce texte. Repère chaque terme, produit ou concept
que mon lecteur ne pourrait pas comprendre, et propose une version claire pour
chacun.

[colle ici ton post / ta page / ton e-mail]
```

Claude te renvoie la liste des points flous et, pour chacun, une reformulation que comprend un débutant complet.

### Exemple

**Avant** (le lecteur reste dans le flou) :

> Je t'offre 1 mois d'accès à mon Atelier.

→ « Atelier », c'est quoi exactement ? « accès » à quoi, et sous quelle forme ?

**Après** (corrigé par le skill) :

> Je t'offre 1 mois d'accès à mon atelier en ligne : une visio par semaine où je réponds à tes questions en direct, plus les enregistrements pour les revoir quand tu veux.

→ Maintenant le lecteur voit ce qu'il obtient, à quelle fréquence et sous quelle forme.
