---
name: prompt-engineer
description: "Boite a outils pour analyser, tester et ameliorer les prompts IA. A/B testing automatise, versioning avec historique immutable, boucle de regression, templates reutilisables et grille d'evaluation. Utiliser quand l'utilisateur veut ameliorer ses prompts, construire des templates, optimiser un workflow de contenu IA, ou quand il mentionne 'prompt engineering', 'ameliorer mes prompts', 'qualite d'ecriture IA', 'templates de prompt' ou 'workflow de contenu IA'."
license: MIT
metadata:
  version: 1.0.0
  author: Alireza Rezvani
  category: marketing
  updated: 2026-03-06
---

# Boite a Outils Prompt Engineer

> Fork de [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) -- traduit integralement en francais.

## Vue d'ensemble

Ce skill sert a faire passer les prompts du brouillon ad-hoc a des assets de production avec des tests reproductibles, du versioning et une protection contre les regressions. L'accent est mis sur la qualite mesurable plutot que sur l'intuition. L'utiliser quand on lance une nouvelle feature LLM qui necessite des outputs fiables, quand la qualite des prompts se degrade apres un changement de modele ou d'instructions, quand plusieurs personnes editent les memes prompts et ont besoin d'historique/diffs, quand on veut un choix de prompt base sur des preuves pour la mise en production, ou quand on veut une gouvernance coherente des prompts sur tous les environnements.

## Capacites principales

- Evaluation A/B de prompts sur des cas de test structures
- Scoring quantitatif pour l'adherence, la pertinence et les verifications de securite
- Suivi de versions avec historique immutable et changelog
- Diffs de prompts pour identifier les modifications qui impactent le comportement
- Templates de prompts reutilisables et guide de selection
- Workflows compatibles regression pour les mises a jour de modeles/prompts

## Workflows cles

### 1. Lancer un test A/B de prompts

Preparer des cas de test en JSON puis lancer :

```bash
python3 scripts/prompt_tester.py \
  --prompt-a-file prompts/a.txt \
  --prompt-b-file prompts/b.txt \
  --cases-file testcases.json \
  --runner-cmd 'my-llm-cli --prompt {prompt} --input {input}' \
  --format text
```

L'input peut aussi venir de stdin ou d'un payload JSON via `--input`.

### 2. Choisir le gagnant avec des preuves

Le testeur score les outputs par cas et agregge :

- couverture du contenu attendu
- violations de contenu interdit
- conformite regex/format
- coherence de la longueur de sortie

Utiliser le prompt avec le meilleur score comme candidat baseline, puis lancer la suite de regression.

### 3. Versionner les prompts

```bash
# Ajouter une version
python3 scripts/prompt_versioner.py add \
  --name support_classifier \
  --prompt-file prompts/support_v3.txt \
  --author alice

# Diff entre versions
python3 scripts/prompt_versioner.py diff --name support_classifier --from-version 2 --to-version 3

# Changelog
python3 scripts/prompt_versioner.py changelog --name support_classifier
```

### 4. Boucle de regression

1. Stocker la version baseline.
2. Proposer des modifications du prompt.
3. Relancer le test A/B.
4. Promouvoir uniquement si le score et les contraintes de securite s'ameliorent.

## Interfaces des scripts

- `python3 scripts/prompt_tester.py --help`
  - Lit les prompts/cas depuis stdin ou `--input`
  - Commande runner externe optionnelle
  - Produit des metriques en texte ou JSON
- `python3 scripts/prompt_versioner.py --help`
  - Gere l'historique des prompts (`add`, `list`, `diff`, `changelog`)
  - Stocke les metadonnees et les snapshots de contenu en local

## Pieges, bonnes pratiques et checklist de relecture

**Erreurs a eviter :**
1. Choisir un prompt a partir d'un seul cas -- utiliser une suite de tests realiste et riche en cas limites.
2. Changer le prompt et le modele en meme temps -- toujours isoler les variables.
3. Oublier les checks `must_not_contain` (contenu interdit) dans les criteres d'evaluation.
4. Modifier des prompts sans metadonnees de version, auteur ou justification du changement.
5. Sauter les diffs semantiques avant de deployer une nouvelle version de prompt.
6. Optimiser un benchmark tout en degradant les cas limites -- suivre la suite complete.
7. Changer de modele sans relancer la suite A/B baseline.

**Avant de promouvoir un prompt, confirmer :**
- [ ] L'intention de la tache est explicite et non ambigue.
- [ ] Le schema/format de sortie est explicite.
- [ ] Les contraintes de securite et d'exclusion sont explicites.
- [ ] Pas d'instructions contradictoires.
- [ ] Pas de tokens superflus.
- [ ] Le score A/B s'ameliore et le nombre de violations reste a zero.

## References

- [references/prompt-templates.md](references/prompt-templates.md) -- Templates de prompts reutilisables
- [references/technique-guide.md](references/technique-guide.md) -- Guide des techniques de prompting
- [references/evaluation-rubric.md](references/evaluation-rubric.md) -- Grille d'evaluation et scoring
- [README.md](README.md) -- Demarrage rapide et installation

## Design des evaluations

Chaque cas de test doit definir :

- `input` : entree realiste, similaire a la production
- `expected_contains` : marqueurs/contenu obligatoire
- `forbidden_contains` : phrases interdites ou contenu non securise
- `expected_regex` : patterns structurels requis

Ca permet un scoring deterministe entre variantes de prompts.

## Politique de versioning

- Utiliser des identifiants semantiques par feature (`support_classifier`, `ad_copy_shortform`).
- Enregistrer auteur + note de changement pour chaque revision.
- Ne jamais ecraser les versions precedentes.
- Diff avant de promouvoir un nouveau prompt en production.

## Strategie de deploiement

1. Creer la version baseline du prompt.
2. Proposer le prompt candidat.
3. Lancer la suite A/B sur les memes cas.
4. Promouvoir uniquement si le gagnant ameliore la moyenne et maintient le nombre de violations a zero.
5. Suivre les retours post-deploiement et alimenter la suite de tests avec les nouveaux cas d'echec.

---

## Reference : prompt-templates.md

> Contenu original de `references/prompt-templates.md`

### 1) Extracteur structure

```text
You are an extraction assistant.
Return ONLY valid JSON matching this schema:
{{schema}}

Input:
{{input}}
```

**Usage :** extraction de donnees structurees a partir de texte libre. Le schema JSON definit le format de sortie attendu.

### 2) Classifieur

```text
Classify input into one of: {{labels}}.
Return only the label.

Input: {{input}}
```

**Usage :** classification en une seule etiquette parmi un ensemble fini.

### 3) Resumeur

```text
Summarize the input in {{max_words}} words max.
Focus on: {{focus_area}}.
Input:
{{input}}
```

**Usage :** resume contraint par un nombre de mots et un axe de focus.

### 4) Reecriture avec contraintes

```text
Rewrite for {{audience}}.
Constraints:
- Tone: {{tone}}
- Max length: {{max_len}}
- Must include: {{must_include}}
- Must avoid: {{must_avoid}}

Input:
{{input}}
```

**Usage :** reecriture ciblee avec ton, longueur, inclusions et exclusions specifies.

### 5) Generateur de paires Q/R

```text
Generate {{count}} Q/A pairs from input.
Output JSON array: [{"question":"...","answer":"..."}]

Input:
{{input}}
```

**Usage :** generation automatique de questions/reponses a partir d'un contenu source.

### 6) Triage d'incidents

```text
Classify issue severity: P1/P2/P3/P4.
Return JSON: {"severity":"...","reason":"...","owner":"..."}
Input:
{{input}}
```

**Usage :** classification de severite d'incidents avec assignation automatique.

### 7) Resume de code review

```text
Review this diff and return:
1. Risks
2. Regressions
3. Missing tests
4. Suggested fixes

Diff:
{{input}}
```

**Usage :** analyse rapide d'un diff avec identification des risques, regressions, tests manquants et corrections suggerees.

### 8) Reecriture persona

```text
Respond as {{persona}}.
Goal: {{goal}}
Format: {{format}}
Input: {{input}}
```

**Usage :** reecriture d'un contenu en adoptant un persona specifique avec un objectif et un format donnes.

### 9) Verification de conformite

```text
Check input against policy.
Return JSON: {"pass":bool,"violations":[...],"recommendations":[...]}
Policy:
{{policy}}
Input:
{{input}}
```

**Usage :** verification automatique de conformite a une politique donnee, avec liste des violations et recommandations.

### 10) Critique de prompt

```text
Critique this prompt for clarity, ambiguity, constraints, and failure modes.
Return concise recommendations and an improved version.
Prompt:
{{input}}
```

**Usage :** meta-analyse d'un prompt pour identifier les ambiguites, contraintes manquantes et modes d'echec. Retourne des recommandations et une version amelioree.

---

## Reference : technique-guide.md

> Contenu original de `references/technique-guide.md`

### Regles de selection

- **Zero-shot** : taches deterministes et simples
- **Few-shot** : ambiguite de formatage ou cas limites d'etiquettes
- **Chain-of-thought** : taches de raisonnement en plusieurs etapes
- **Structured output** : parsing en aval ou integration requise
- **Self-critique / meta prompting** : boucles d'amelioration de prompts

### Checklist de construction de prompt

- Role et objectif clairs
- Format de sortie explicite
- Contraintes et exclusions
- Instructions pour la gestion des cas limites
- Usage minimal de tokens pour les taches repetitives

### Checklist des patterns d'echec

- Objectif trop large
- Schema de sortie manquant
- Contraintes contradictoires
- Pas d'exemples negatifs pour les comportements non securises
- Hypotheses implicites non formulees dans le prompt

---

## Reference : evaluation-rubric.md

> Contenu original de `references/evaluation-rubric.md`

### Grille d'evaluation

Scorer chaque cas sur 0-100 via des criteres ponderes :

- Couverture du contenu attendu : +poids
- Violations de contenu interdit : -poids
- Conformite regex/format : +poids
- Coherence de la longueur de sortie : +/-poids

### Seuils d'acceptation recommandes

- Score moyen >= 85
- Aucun cas en dessous de 70
- Zero hit critique de contenu interdit

---

## Reference : README.md

> Contenu original du `README.md` du toolkit

### Demarrage rapide

```bash
# Lancer une evaluation A/B de prompts
python3 scripts/prompt_tester.py \
  --prompt-a-file prompts/a.txt \
  --prompt-b-file prompts/b.txt \
  --cases-file testcases.json \
  --format text

# Stocker une version de prompt
python3 scripts/prompt_versioner.py add \
  --name support_classifier \
  --prompt-file prompts/a.txt \
  --author team
```

### Outils inclus

- `scripts/prompt_tester.py` : A/B testing avec scoring par cas et gagnant agrege
- `scripts/prompt_versioner.py` : historique des prompts (`add`, `list`, `diff`, `changelog`) dans un store JSONL local

### Installation

#### Claude Code

```bash
cp -R marketing-skill/prompt-engineer-toolkit $HOME/.claude/skills/prompt-engineer-toolkit
```

#### OpenAI Codex

```bash
cp -R marketing-skill/prompt-engineer-toolkit ~/.codex/skills/prompt-engineer-toolkit
```

#### OpenClaw

```bash
cp -R marketing-skill/prompt-engineer-toolkit ~/.openclaw/skills/prompt-engineer-toolkit
```
