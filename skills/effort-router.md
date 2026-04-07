---
description: "Routage automatique d'effort : adapte la profondeur de reflexion (low/medium/high/max) a chaque tache pour eliminer la sur-reflexion sur les taches simples et la sous-reflexion sur les taches critiques"
---

# /effort-router — Le Regulateur

> Skill original par [Ismax](https://github.com/ismax-ai).

## Ce que ce skill resout

Depuis Opus 4.6, Claude utilise l'extended thinking : une phase de reflexion interne avant chaque reponse. Cette reflexion consomme des tokens. Par defaut, l'effort est regle sur `medium` pour toutes les taches, quelle que soit leur complexite.

Probleme : renommer un fichier et concevoir une architecture distribuee consomment le meme budget de reflexion. Sur les taches simples, Claude gaspille des tokens en reflexion inutile. Sur les taches critiques, il ne reflechit pas assez.

Ce skill adapte le comportement de Claude (profondeur d'analyse, nombre d'alternatives considerees, detail de la reponse) en fonction de la complexite reelle de chaque tache.

## Comment fonctionne l'extended thinking dans Claude

L'extended thinking est une phase de raisonnement interne que Claude execute avant de generer sa reponse. Cette phase :
- Consomme des tokens (entre 500 et 50 000+ selon le niveau)
- N'est pas visible dans la reponse finale
- Est controlee par le parametre `/effort` (low, medium, high, max)

La difference de consommation entre `max` et `low` peut atteindre 10x. En pratique, utiliser `medium` pour tout revient a payer le prix moyen pour des taches qui n'en ont pas besoin.

Ce skill ne modifie pas le parametre `/effort` (c'est un reglage systeme). Il adapte le COMPORTEMENT de Claude : longueur de reponse, profondeur d'analyse, nombre d'alternatives evaluees, detail des justifications.

## Les 4 niveaux

### LOW — Execution directe

Taches ou la reponse est evidente. Pas de deliberation necessaire.

| Signal | Exemples |
|--------|----------|
| Renommer, deplacer, copier | "Renomme ce fichier en kebab-case" |
| Lister, compter, scanner | "Liste tous les .py dans src/" |
| Formater, indenter, trier | "Trie ces imports par ordre alphabetique" |
| Repondre factuellement | "C'est quoi la syntaxe de map() en JS ?" |
| Supprimer du code mort | "Supprime les imports inutilises" |
| Chercher un fichier/pattern | "Trouve ou est defini UserService" |
| Generer du boilerplate standard | "Cree un Dockerfile basique pour ce projet Node" |

**Comportement LOW** : reponse directe, pas d'alternatives, pas de considerations, pas d'avertissements superflus.

### MEDIUM — Travail courant

Taches qui demandent de la reflexion dans un scope limite. Un seul fichier, un seul composant, resultat previsible.

| Signal | Exemples |
|--------|----------|
| Modifier du code existant | "Ajoute un champ email a ce formulaire" |
| Corriger un bug simple | "Ce bouton ne redirige pas, corrige" |
| Ecrire un test unitaire | "Ecris les tests pour cette fonction" |
| Refactorer localement | "Extrais cette logique dans une fonction" |
| Expliquer du code | "Explique-moi ce que fait ce middleware" |
| Ecrire de la documentation | "Documente cette API" |

**Comportement MEDIUM** : considerer 1-2 approches, mentionner les implications directes. Pas d'analyse exhaustive.

### HIGH — Analyse approfondie

Taches complexes. Multi-fichiers, effets de bord, decisions a prendre. Une erreur a des consequences.

| Signal | Exemples |
|--------|----------|
| Debug multi-fichiers | "L'auth casse apres le deploy, trouve pourquoi" |
| Concevoir une feature | "Ajoute un systeme de notifications real-time" |
| Refactorer un module entier | "Refactore le module de paiement" |
| Ecrire un plan technique | "Planifie la migration de REST a GraphQL" |
| Review de code | "Review cette PR, je veux pas de surprises" |
| Optimiser les performances | "La page met 4s a charger, optimise" |

**Comportement HIGH** : analyser en profondeur, considerer 3+ approches, anticiper les effets de bord, verifier les implications.

### MAX — Mode expert

Reserve aux problemes ou une erreur coute cher. Decisions irreversibles, production, securite.

| Signal | Exemples |
|--------|----------|
| Decision architecturale | "Monolith ou microservices ?" |
| Securite / audit | "Verifie les failles d'injection" |
| Bug non reproduisible | "Ca crash en prod 1 fois sur 10" |
| Migration de donnees critique | "Migre la DB, zero perte, zero downtime" |
| Design systeme a grande echelle | "Systeme de file d'attente pour 10K req/s" |

**Comportement MAX** : investigation exhaustive, tous les angles consideres, chaque affirmation justifiee.

---

## Arbre de decision

```
1. Action mecanique sans ambiguite ?
   (renommer, lister, copier, formater, factuel)
   OUI → LOW
   NON ↓

2. Scope = 1 fichier/composant, resultat previsible ?
   OUI → MEDIUM
   NON ↓

3. Signal critique detecte ? (voir Signaux de surclassement)
   OUI + consequences irreversibles → MAX
   OUI + scope limite → HIGH
   NON → MEDIUM

4. Doute sur le niveau ?
   → Monter d'un cran. Sur-reflechir coute des tokens. Sous-reflechir coute des erreurs.
```

### Signaux de surclassement (prioritaires)

Ces signaux l'emportent TOUJOURS sur les signaux de declassement.

| Signal detecte | Niveau minimum |
|----------------|----------------|
| "production", "prod", "deploy" | HIGH |
| "securite", "injection", "auth", "faille" | HIGH (souvent MAX) |
| "migration", "sans perte", "zero downtime" | MAX |
| "architecture", "systeme", "design system" | HIGH |
| "critique", "irreversible" | HIGH |
| "decision", "choix strategique" | MAX |
| Suppression de donnees en prod | MAX |

### Signaux de declassement (sauf si surclassement present)

| Signal detecte | Niveau maximum |
|----------------|----------------|
| "juste", "vite fait", "rapidement" | MEDIUM |
| "montre-moi", "affiche", "liste" | LOW |
| "renomme", "deplace", "supprime" (hors prod) | LOW |
| "c'est quoi", "comment on fait" (factuel simple) | LOW |

**Regle** : si surclassement ET declassement dans le meme message, le surclassement gagne. "Supprime juste les users inactifs en prod" = MAX, pas LOW.

---

## Cas limites

| Tache | Classification | Raison |
|-------|---------------|--------|
| "Ecris un Dockerfile pour ce projet" | **MEDIUM** | Choix d'image, multi-stage, securite des layers. Si "basique" → LOW. |
| "Explique-moi pourquoi X ne marche pas" | **MEDIUM** | Diagnostic sans fix, reflexion necessaire. |
| "Supprime les imports inutilises" dans un fichier de 200 imports | **MEDIUM** | Volume = complexite. Effets de bord possibles sur des re-exports. |
| "Corrige ce bug en prod" | **HIGH** | "Prod" surclasse tout. |
| "Fais un README" | **LOW** | Documentation standard. |
| "Fais un README pour ce projet open-source" | **MEDIUM** | Decisions de communication et structure. |
| "Refactore tout le module auth" | **HIGH** | Multi-fichiers, securite impliquee, effets de bord. |

---

## Correction de route

Le classement automatique n'est pas parfait. L'utilisateur peut corriger en cours de session.

### Monter (sous-estimation detectee)

| L'utilisateur dit | Action |
|-------------------|--------|
| "va plus loin", "creuse plus" | Monter d'un niveau, re-analyser |
| "c'est plus complexe que ca" | Monter d'un niveau |
| "t'as pas considere X" | Monter d'un niveau, integrer X |
| "mode max" / "reflechis a fond" | MAX |

### Descendre (sur-estimation detectee)

| L'utilisateur dit | Action |
|-------------------|--------|
| "pas besoin d'autant de detail" | Descendre d'un niveau |
| "fais-le juste", "execute" | LOW |
| "trop long", "plus court" | Descendre d'un niveau |

Apres une correction, ajuster le calibrage pour les messages suivants de la meme session. Si l'utilisateur dit "creuse plus" sur un type de tache, classer les taches similaires un cran au-dessus pour le reste de la session.

---

## Affichage

Une ligne en debut de reponse, uniquement quand le niveau change.

```
⚡ LOW — execution directe
```

```
⚙️ MEDIUM — reflexion standard
```

```
🔥 HIGH — analyse approfondie
```

```
🧠 MAX — reflexion maximale
```

Si le niveau est le meme qu'au message precedent, ne rien afficher.

---

## Override utilisateur

L'utilisateur peut forcer un niveau a tout moment. Son override a toujours priorite.

- "Fais ca en mode max" → MAX
- "Vite fait" → LOW
- `/effort high` → override pour ce message

---

## Diagnostic de session

Si l'utilisateur tape `/effort-router` sans tache :

```
⚙️ EFFORT ROUTER — Diagnostic
═══════════════════════════════════════

Messages traites : [N]
Distribution :
  ⚡ LOW    : [X] ([%]%)
  ⚙️ MEDIUM : [X] ([%]%)
  🔥 HIGH   : [X] ([%]%)
  🧠 MAX    : [X] ([%]%)

Corrections utilisateur : [N]
  ↑ Montees : [X]
  ↓ Descentes : [X]

Calibrage : [stable | tendance haute | tendance basse]
═══════════════════════════════════════
```

---

## Ce que ce skill ne fait pas

- **Ne change pas le modele.** Haiku/Sonnet/Opus = quel cerveau. Effort = combien de reflexion. Pour le model routing, voir `/model`.
- **Ne compresse pas le contexte.** C'est le job du compactage et de token-optimizer.
- **Ne modifie pas le parametre systeme `/effort`.** Il adapte le comportement de Claude (profondeur, detail, alternatives). L'utilisateur peut aussi taper `/effort [niveau]` directement.

---

## Gains mesurables

| Situation | Sans routing | Avec routing | Economie |
|-----------|-------------|-------------|----------|
| Renommer 5 fichiers | 5x MEDIUM (~2500 tokens thinking) | 5x LOW (~500 tokens thinking) | ~80% |
| Session mixte (70% simple, 30% complexe) | Tout en MEDIUM | LOW/MEDIUM/HIGH adaptes | ~40% |
| Debug prod critique | MEDIUM (sous-reflexion) | MAX (analyse complete) | Moins de tokens gaspilles en allers-retours |

Le gain principal n'est pas que l'economie de tokens : c'est aussi la qualite. Les taches critiques recoivent plus de reflexion, les taches simples sont executees sans latence inutile.

---

## Integration avec les autres techniques d'optimisation

| Technique | Ce qu'elle optimise | Skill |
|-----------|-------------------|-------|
| **Effort routing** (ce skill) | Profondeur de reflexion par tache | /effort-router |
| **Model routing** | Choix du modele par tache | Frontmatter `model:` |
| **Token optimizer** | Nettoyage des tokens fantomes | /token-optimizer |
| **Compactage** | Compression du contexte | autoCompact |
| **Cache prompt** | Reutilisation des instructions systeme | Protection cache |
