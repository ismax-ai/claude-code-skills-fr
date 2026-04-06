# Skills Claude Code par Ismax

10 skills. Prêts à copier. En français.

---

## Le problème

Tu écris un post. Tu relis. Tu publies.

Et le premier commentaire pointe la faille que tu n'avais pas vue. Ou pire, le silence. Personne ne réagit parce que le texte sonnait robot sans que tu t'en rendes compte.

En fait le vrai problème c'est pas ta rédaction. C'est que tu relis avec les mêmes yeux qui ont écrit. Tu ne peux pas être ton propre critique (et c'est normal).

## La solution

10 skills Claude Code qui font le boulot à ta place. Red team, superviseur qualité, avocat du diable, boucle infinie, prompt engineer. Chaque skill = un expert que tu appelles en une commande.

Pas besoin de coder. Pas de terminal. Juste un copier-coller.

---

## Les 10 skills

| # | Skill | Commande | En une phrase |
|---|-------|----------|---------------|
| 1 | **Superpowers** | `/superpowers` | Découpe ta tâche en sous-agents parallèles. Tout avance en même temps |
| 2 | **Créateur de Skills** | `/create-skill` | Tu décris ce que tu veux, il génère le skill complet |
| 3 | **Prompt Engineer** | `/prompt-engineer` | Boîte à outils pour construire, tester et améliorer tes prompts |
| 4 | **Agent Auto-Améliorant** | `/self-improve` | Il produit, il critique, il corrige. Tout seul |
| 5 | **Gouvernance Prompts** | `/prompt-governance` | Audit qualité de tes prompts. Cohérence et versioning |
| 6 | **L'Avocat du Diable** | `/devils-advocate` | Steel-man, pre-mortem, questionnement socratique sur tes idées |
| 7 | **Sentinel** | `/sentinel` | Red Team complet : 20 angles d'attaque, score sur 10 |
| 8 | **Karen** | `/karen` | Vérifie si c'est VRAIMENT fait. Pas juste "fait" |
| 9 | **Ralph** | `/ralph` | Boucle plan → build → check. Tourne jusqu'au propre |
| 10 | **L'Aigle Superviseur** | `/eagle-supervisor` | 7 passes qualité séquentielles. Un FAIL, rien ne sort |

---

## Installation

Copie ce texte dans Claude Code. Il s'occupe du reste.

### Tout installer d'un coup

```
Va sur https://github.com/ismax-ai/claude-code-skills-fr,
lis les fichiers dans le dossier skills/,
et installe-les tous comme commandes Claude Code.
```

### Installer un skill précis

```
Va sur https://github.com/ismax-ai/claude-code-skills-fr,
lis le fichier skills/sentinel.md,
et installe-le comme commande Claude Code.
```

Remplace `sentinel.md` par le nom du skill voulu.

Pas de terminal. Pas de ligne de commande. Pas de dossier à créer. Claude fait tout.

> Pas encore Claude Code ? Installe-le depuis [claude.ai/code](https://claude.ai/code). C'est la version de Claude qui travaille directement sur ton ordi.

---

## Les 3 familles

### ✦ Critique et qualité

Les skills qui attaquent ton travail avant que ton audience le fasse.

**Sentinel** lance 3 batteries d'attaques. 9 techniques CIA (pre-mortem, hypothèses non vérifiées, auto-critique structurée). 6 chapeaux de Bono (faits purs, émotions, critique, créativité). 5 checks OWASP adaptés au contenu. Score sur 10, verdict clair : PASS, AMEND ou FAIL.

**L'Aigle Superviseur** survole tout depuis le ciel. 7 passes séquentielles : format, structure, langue, ton, audience, promesse centrale, cohérence narrative. Chaque passe ne regarde qu'une seule chose. Un seul FAIL et rien ne sort. Bon, c'est exigeant. Mais c'est le but.

**L'Avocat du Diable** prend ton idée. Il la steel-man d'abord (la renforce au maximum). Puis il l'attaque. Pre-mortem, inversion, questionnement socratique. 7 objections max, classées par sévérité. Si l'idée survit, elle est solide.

**Karen** ne s'intéresse pas à ce que tu dis avoir fait. Elle regarde ce qui est VRAIMENT fait. Code qui compile mais ne tourne pas ? Feature "terminée" mais jamais testée ? Karen trouve le trou. C'est son truc.

### ✦ Création et production

Les skills qui produisent à ta place.

**Superpowers** prend un objectif complexe. Il le découpe en sous-tâches, lance des agents en parallèle, et orchestre le tout. Tu donnes la direction, il fait avancer.

**Créateur de Skills** génère un skill Claude Code complet à partir de ta description en français. Le fichier `.md` sort prêt à installer. Du coup tu peux créer tes propres skills sans te prendre la tête avec le format.

**Prompt Engineer** c'est ta boîte à outils. Construire un prompt de zéro, analyser un prompt existant, l'améliorer étape par étape. Du brouillon au prompt qui tourne en prod.

**Agent Auto-Améliorant** produit un résultat. L'analyse. Le critique. Le corrige. Puis recommence. Sans que tu interviennes. La boucle tourne jusqu'à ce que le résultat soit bon.

### ✦ Gouvernance et boucles

Les skills qui maintiennent l'ordre dans ton système.

**Gouvernance Prompts** audite tes prompts. Qualité, cohérence, versioning. Quand t'as 50 prompts qui partent dans tous les sens, ce skill remet de l'ordre.

**Ralph** c'est la boucle infinie. Planifier, construire, vérifier. Si le résultat ne passe pas le check, il recommence. Encore. Encore. Jusqu'à ce que ce soit propre. (Spoiler : il peut tourner longtemps.)

---

## Workflow recommandé

### Pour du contenu

```
1. Tu écris
2. /sentinel          → trouve les failles
3. Tu corriges
4. /eagle-supervisor  → vérifie que tout tient
5. Tu publies
```

Sentinel attaque. L'aigle vérifie que les corrections tiennent.

### Pour du code

```
1. Tu codes
2. /karen             → vérifie que c'est fait pour de vrai
3. /sentinel code     → red team technique
4. Tu corriges
5. Tu livres
```

### Pour une décision

```
1. Tu poses ta décision
2. /devils-advocate   → challenge les hypothèses
3. /sentinel decision → red team complet
4. Tu décides en connaissance de cause
```

---

## Exemple concret

Tu écris :
> "L'IA va remplacer 80% des jobs d'ici 2030."

Tu lances `/sentinel`. Il répond :

```
🔴 RED TEAM — CONTENT

CIA SATs : 5/9 passed
  ❌ Key Assumptions Check : "80%", source ? Aucune étude sérieuse ne donne ce chiffre
  ❌ Pre-mortem : premier commentaire garanti = "source ?"
  ❌ Devil's Advocacy : des secteurs entiers sans IA dominent encore

SCORE : 4/10
VERDICT : AMEND
→ Sourcer le chiffre (McKinsey, OCDE)
→ Nuancer "remplacer" par un verbe vérifiable
```

En 10 secondes, tu sais que ton post va se faire descendre. Avant de le publier.

---

## Crédits

3 skills sont des forks de projets open source. Traduits en français et améliorés.

| Skill | Auteur original | Source | Ce qu'on a ajouté |
|-------|----------------|--------|-------------------|
| L'Avocat du Diable | [notmanas](https://github.com/notmanas/claude-code-skills) | Devil's Advocate | Traduction FR, frameworks CIA, pre-mortem |
| Karen | [darcyegb](https://github.com/darcyegb/ClaudeCodeAgents) | Karen | Traduction FR, severity ratings, bullshit detection |
| Ralph | [Geoffrey Huntley](https://ghuntley.com/ralph/) | Ralph | Traduction FR, structure plan d'implémentation |

Les 7 autres sont des créations originales.

Détails complets dans [CREDITS.md](CREDITS.md).

---

## Pour aller plus loin

Ces 10 skills sont un bout de mon workflow IA. Si tu veux voir comment je les utilise en vrai, comment je crée du contenu, et les systèmes que je construis au quotidien :

→ **[Ma newsletter](https://redigeretvendreavecia.substack.com)** — 2 emails par semaine. Tutoriels IA en français, pas de blabla
→ **[Mon Instagram](https://instagram.com/ismaelalaoui_)** — workflows IA en carrousels, tout est actionnable

---

## Licence

MIT. Utilise, modifie, partage.
