---
name: prompt-engineer
description: "Boite a outils pour construire, tester et ameliorer tes prompts. A/B testing, versioning, boucle de regression. Utiliser quand un prompt doit passer du brouillon a la production."
---

# Prompt Engineer

> Par [Alireza Rezvani](https://github.com/alirezarezvani) — traduit et adapte en francais.

Tes prompts passent du brouillon ad-hoc a des assets de production. Tests mesurables, versioning, protection contre les regressions. On optimise avec des donnees, pas au feeling.

## Quand utiliser

- Tu lances une feature IA et tu veux des outputs fiables
- La qualite de tes prompts se degrade apres un changement de modele
- Plusieurs personnes editent les memes prompts (besoin d'historique)
- Tu veux des preuves pour choisir entre deux versions de prompt
- Tu veux du versioning coherent sur tous tes prompts

## Capacites

- Test A/B entre deux prompts sur des cas structures
- Scoring quantitatif : adherence, pertinence, securite
- Suivi de versions avec historique immutable et changelog
- Diffs entre versions pour voir ce qui change le comportement
- Templates reutilisables
- Boucle de regression quand le modele ou le prompt change

## Workflows

### 1. Test A/B de prompts

Preparer des cas de test en JSON, puis comparer deux prompts :

1. Definir les cas de test (input + output attendu + contenu interdit)
2. Executer le prompt A et le prompt B sur chaque cas
3. Scorer les outputs : couverture du contenu attendu, violations, conformite format, longueur
4. Le prompt avec le meilleur score = candidat baseline

### 2. Versioning

Chaque prompt a un identifiant semantique (ex: `classifieur_support`, `accroche_carousel`).

Regles :
- Enregistrer auteur + note de changement pour chaque revision
- Ne jamais ecraser une version precedente
- Diff AVANT de promouvoir une nouvelle version en production
- Changelog accessible a toute l'equipe

### 3. Boucle de regression

1. Stocker la version baseline
2. Proposer une modification
3. Relancer le test A/B
4. Promouvoir uniquement si le score s'ameliore ET zero violation

### 4. Design des evaluations

Chaque cas de test definit :

| Champ | Role |
|-------|------|
| `input` | Entree realiste, type production |
| `expected_contains` | Marqueurs/contenu obligatoire |
| `forbidden_contains` | Phrases interdites ou contenu non securise |
| `expected_regex` | Patterns structurels attendus |

Ca permet un scoring deterministe entre variantes.

## Pieges a eviter

1. Choisir un prompt sur un seul cas → utiliser une suite de tests complete avec cas limites
2. Changer prompt ET modele en meme temps → isoler les variables
3. Oublier les checks `forbidden_contains` → toujours verifier ce qui ne doit PAS etre la
4. Modifier un prompt sans metadata (auteur, raison) → chaque edit est trace
5. Deployer sans diff semantique → comparer avant de promouvoir
6. Optimiser un benchmark en cassant les cas limites → suivre la suite complete
7. Changer de modele sans relancer la baseline → re-tester systematiquement

## Checklist avant promotion

- [ ] L'intention de la tache est explicite et non ambigue
- [ ] Le format de sortie est specifie
- [ ] Les contraintes de securite sont explicites
- [ ] Pas d'instructions contradictoires
- [ ] Pas de tokens inutiles
- [ ] Le score A/B s'ameliore et zero violation

## Strategie de deploiement

1. Creer la version baseline
2. Proposer le candidat
3. Lancer la suite A/B sur les memes cas
4. Promouvoir uniquement si le score global s'ameliore et zero violation
5. Suivre les retours post-deploiement et alimenter la suite de tests avec les nouveaux cas d'echec
