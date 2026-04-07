# Skills Claude Code par Ismax

La plus grosse bibliotheque francophone de skills Claude Code. Open source.

---

## C'est quoi un skill ?

Un skill, c'est un fichier d'instructions qui donne de nouvelles capacites a Claude Code. Tu tapes une commande, Claude change de comportement. `/sentinel` et il devient un red teamer qui attaque ton travail sous 20 angles. `/ralph` et il entre dans une boucle autonome jusqu'a ce que le resultat soit propre. `/karen` et il verifie si c'est vraiment fait, pas juste "fait".

En gros : un skill = un expert specialise que tu appelles en une commande.

Pas de code a ecrire. Pas de plugin a installer. Pas de configuration. Tu copies, Claude fait le reste.

## Pourquoi cette bibliotheque

Claude Code est puissant de base. Mais il fait ce que tu lui demandes, exactement comme tu le demandes. Si ton prompt est vague, le resultat est vague. Si tu oublies de lui dire de verifier son travail, il ne verifie pas.

Les skills corrigent ca. Chaque skill encode un processus complet : les etapes, les criteres de qualite, les cas limites, les red flags a detecter. Du coup tu obtiens un resultat de niveau expert sans ecrire un prompt de 200 lignes a chaque fois.

Aujourd'hui il y a 10 skills. L'objectif c'est d'en avoir 100+. Chaque skill est teste en conditions reelles avant d'etre publie ici.

## Pour qui

- Tu utilises Claude Code (ou tu veux t'y mettre)
- Tu veux des resultats concrets : du contenu qui tient, du code qui marche, des decisions challengees
- Tu preferes installer un truc qui marche plutot que passer 3 heures a bricoler un prompt

Pas besoin d'etre dev. Tous les skills s'installent avec un copier-coller.

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

## Credits

7 skills sur 10 sont des traductions integrales de projets open source. 3 sont des creations originales.

Details complets, auteurs originaux et sources dans [CREDITS.md](CREDITS.md).

---

## Pour aller plus loin

Cette bibliotheque c'est un bout de mon workflow IA. Si tu veux voir comment j'utilise ces skills en vrai, comment je cree du contenu, et les systemes que je construis au quotidien :

→ **[Ma newsletter](https://redigeretvendreavecia.substack.com)** — 2 emails par semaine. Tutoriels IA en français, pas de blabla
→ **[Mon Instagram](https://instagram.com/ismaelalaoui_)** — workflows IA en carrousels, tout est actionnable

---

## Licence

MIT. Utilise, modifie, partage.
