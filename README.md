# Skills Claude Code par Ismax

Une bibliothèque francophone de skills Claude Code. 115 skills, open source.

---

## C'est quoi un skill ?

Un skill, c'est un fichier d'instructions qui donne de nouvelles capacités à Claude Code. Tu tapes une commande, Claude change de comportement. `/sentinel` et il devient un red teamer qui attaque ton travail sous 20 angles. `/ralph` et il entre dans une boucle autonome jusqu'à ce que le résultat soit propre. `/karen` et il vérifie si c'est vraiment fait, pas juste "fait".

En gros : un skill = un expert spécialisé que tu appelles en une commande.

Pas de code à écrire. Pas de configuration compliquée. Tu installes le plugin une fois, et les 115 skills sont là.

## Pourquoi cette bibliothèque

Claude Code est puissant de base. Mais il fait ce que tu lui demandes, exactement comme tu le demandes. Si ton prompt est vague, le résultat est vague. Si tu oublies de lui dire de vérifier son travail, il ne vérifie pas.

Les skills corrigent ça. Chaque skill encode un processus complet : les étapes, les critères de qualité, les cas limites, les red flags à détecter. Du coup tu obtiens un résultat de niveau expert sans écrire un prompt de 200 lignes à chaque fois.

Aujourd'hui il y a 115 skills : 15 que j'ai faits maison, et 100 que j'ai sélectionnés parmi les meilleurs projets open source de la communauté (licences MIT et Apache), traduits et adaptés en français. Chaque skill est testé en conditions réelles avant d'être publié ici.

## Pour qui

- Tu utilises Claude Code (ou tu veux t'y mettre)
- Tu veux des résultats concrets : du contenu qui tient, du code qui marche, des décisions challengées
- Tu préfères installer un truc qui marche plutôt que passer 3 heures à bricoler un prompt

Pas besoin d'être dev.

---

## Installation

### Méthode recommandée — le plugin (tout d'un coup)

Dans Claude Code, tape ces deux commandes :

```
/plugin marketplace add ismax-ai/claude-code-skills-fr
/plugin install ismax-skills-fr@ismax
```

Les 115 skills s'installent d'un coup. Quand j'en ajoute de nouveaux, tu les récupères avec :

```
/plugin marketplace update ismax
```

### Méthode alternative — copier-coller (sans plugin)

Si tu préfères, colle ce texte dans Claude Code. Il s'occupe du reste :

```
Va sur https://github.com/ismax-ai/claude-code-skills-fr,
lis les fichiers SKILL.md dans plugins/ismax-skills-fr/skills/,
et installe-les tous comme skills Claude Code.
```

> Pas encore Claude Code ? Installe-le depuis [claude.ai/code](https://claude.ai/code). C'est la version de Claude qui travaille directement sur ton ordi.

---

## Les 15 skills maison

> Catalogue complet des 115 (avec covers et fiches) : https://claude-pour-les-debutants.fr/skills-claude-code-gratuits

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
| 10 | **L'Aigle Superviseur** | `/eagle-supervisor` | 6 passes qualité séquentielles. Un FAIL, rien ne sort |
| 11 | **ELI5** | `/eli5` | Repère chaque terme que ton lecteur ne comprend pas et le rend simple |
| 12 | **Clarté** | `/clarity` | Coupe le flou et le blabla. Te dit où le lecteur décroche |
| 13 | **Anti-Bullshit** | `/anti-bullshit` | Détecte l'oversell, les fausses promesses, le marketing creux |
| 14 | **Le Sceptique** | `/scepticisme` | Fait relire ton contenu par un inconnu méfiant. Score sur 100 |
| 15 | **Fact-Check** | `/fact-check-claims` | Vérifie chaque affirmation contre les sources officielles en direct |

---

## Les 4 familles

### ✦ Critique et qualité

Les skills qui attaquent ton travail avant que ton audience le fasse.

**Sentinel** lance 3 batteries d'attaques. 9 techniques CIA (pre-mortem, hypothèses non vérifiées, auto-critique structurée). 6 chapeaux de Bono (faits purs, émotions, critique, créativité). 5 checks OWASP adaptés au contenu. Score sur 10, verdict clair : PASS, AMEND ou FAIL.

**L'Aigle Superviseur** exécute 6 passes séquentielles : format, structure, langue, ton, audience, promesse. Chaque passe ne regarde qu'une seule chose. Un seul FAIL et rien ne sort.

**L'Avocat du Diable** prend ton idée. Il la steel-man d'abord (la renforce au maximum). Puis il l'attaque. Pre-mortem, inversion, questionnement socratique. 7 objections max, classées par sévérité. Si l'idée survit, elle est solide.

**Karen** ne s'intéresse pas à ce que tu dis avoir fait. Elle regarde ce qui est VRAIMENT fait. Code qui compile mais ne tourne pas ? Feature "terminée" mais jamais testée ? Karen trouve le trou. C'est son truc.

### ✦ Clarté et crédibilité

Les skills qui rendent ton contenu clair, honnête et vérifié avant que tu le publies.

**ELI5** scanne ton contenu et détecte chaque terme ou produit que tu cites sans l'expliquer. Pour chacun, il propose une définition courte, comme si tu l'expliquais à un débutant complet. Le fond ne change pas, tout devient accessible.

**Clarté** relit ton texte phrase par phrase et te dit exactement où le lecteur va décrocher. Il repère les mots vagues comme "optimise" ou "gère" et les remplace par ce que ça fait vraiment.

**Anti-Bullshit** relit ton contenu commercial avant publication. Il signale l'oversell, les fausses promesses et les histoires trop lisses pour être vraies, avec une reformulation concrète pour chaque problème.

**Le Sceptique** fait relire ton contenu par un inconnu méfiant qui ne te connaît pas et a déjà vu trop de promesses creuses. Il challenge chaque affirmation, met un score sur 100, et te liste ce qui manque pour qu'on te croie.

**Fact-Check** vérifie chaque affirmation factuelle de ton contenu (chiffre, date, fonctionnalité, citation) contre les sources officielles en direct. Pour t'éviter de publier une info devenue fausse depuis ta date de formation.

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
2. /clarity           → coupe le flou
3. /anti-bullshit     → enlève l'oversell
4. /scepticisme       → un inconnu méfiant te relit
5. /fact-check-claims → vérifie les chiffres
6. Tu publies
```

### Pour du code

```
1. Tu codes
2. /karen             → vérifie que c'est fait pour de vrai
3. /sentinel          → red team technique
4. Tu corriges
5. Tu livres
```

### Pour une décision

```
1. Tu poses ta décision
2. /devils-advocate   → challenge les hypothèses
3. /sentinel          → red team complet
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

7 skills sur 15 sont des traductions intégrales de projets open source. 8 sont des créations originales.

Détails complets, auteurs originaux et sources dans [CREDITS.md](CREDITS.md).

---

## Pour aller plus loin

Cette bibliothèque c'est un bout de mon workflow IA. Si tu veux voir comment j'utilise ces skills en vrai, comment je crée du contenu, et les systèmes que je construis au quotidien :

→ **[Ma newsletter](https://redigeretvendreavecia.substack.com)** — 2 emails par semaine. Tutoriels IA en français, pas de blabla
→ **[Mon Instagram](https://instagram.com/ismaelalaoui_)** — workflows IA en carrousels, tout est actionnable

---

## Licence

MIT. Utilise, modifie, partage.
