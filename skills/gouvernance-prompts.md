---
name: gouvernance-prompts
description: "Audit qualite de tes prompts. Coherence et versioning. Utiliser quand tu geres des prompts en production : versioning, A/B tests, registre, prevention des regressions."
---

# Gouvernance Prompts

> Inspire de [chad848](https://github.com/chad848) — traduit et adapte en francais.

Les prompts c'est du code. Ils changent le comportement en production. Les deployer avec la meme rigueur que du code applicatif.

## 3 modes

| Mode | Situation | Ce que ca fait |
|------|-----------|----------------|
| **1 — Registre** | Pas de gestion centralisee des prompts | Concevoir un registre avec versioning, promotion par environnement, piste d'audit |
| **2 — Pipeline d'evaluation** | Les prompts sont stockes mais pas testes | Construire un pipeline d'evaluation qui detecte les regressions avant la production |
| **3 — Iteration gouvernee** | Registre et evals existent | Designer le workflow complet : branche, test, eval, review, promotion, rollback |

## Avant de commencer

Collecter le contexte :

1. **Etat actuel** : comment sont stockes les prompts ? Combien en production ? Un changement de prompt a-t-il deja cause une regression non detectee ?
2. **Objectif** : quel est le probleme principal ? (chaos de versioning, pas d'evals, A/B testing a l'aveugle ?)
3. **Stack IA** : quel(s) fournisseur(s) LLM ? Quel framework ? Quelle infra de test/CI existante ?

---

## Mode 1 — Registre de prompts

### Version minimale (fichiers)

```
prompts/
  registry.yaml          # Index de tous les prompts
  classifieur/
    v1.0.0.md            # Contenu du prompt
    v1.1.0.md
  accroche/
    v1.0.0.md
```

Le fichier `registry.yaml` contient pour chaque prompt :
- Identifiant, description, proprietaire
- Liste des versions avec statut (production, staging, archive)
- Date de promotion et auteur

### Version production (base de donnees)

Pour les equipes plus larges : registre accessible par API avec tables pour les prompts et leurs versions, tracking du slug, contenu, modele, environnement, score d'eval et metadata de promotion.

---

## Mode 2 — Pipeline d'evaluation

### Types d'evaluations

| Type | Ce que ca mesure | Quand l'utiliser |
|------|-----------------|-----------------|
| **Match exact** | Output = chaine attendue | Classification, extraction, output structure |
| **Verification contenu** | Output contient les elements requis | Extraction de points cles, resumes |
| **LLM-juge** | Un autre LLM score la qualite 1-5 | Generation ouverte, ton, utilite |
| **Similarite semantique** | Similarite d'embedding avec reponse gold | Comparaisons tolerantes aux paraphrases |
| **Validation schema** | Output conforme au schema JSON | Taches de sortie structuree |
| **Evaluation humaine** | Humain note 1-5 sur criteres | Enjeux eleves, gates de lancement |

### Dataset gold

Chaque prompt a besoin d'un dataset gold : des paires input/output attendu qui definissent le comportement correct.

Regles :
- Minimum 20 exemples pour la couverture de base, 100+ pour la confiance en production
- Couvrir les cas limites et les modes d'echec, pas juste le happy path
- Revu et approuve par un expert du domaine
- Versionne avec le prompt (un changement de prompt peut necessiter une mise a jour du dataset)

### Seuils

- Classification/extraction : 95%+ match exact
- Resume : 0.85+ score LLM-juge
- Output structure : 100% validation schema
- Generation ouverte : 80%+ approbation humaine

---

## Mode 3 — Iteration gouvernee

Le cycle de deploiement complet avec des gates a chaque etape :

1. **BRANCHE** — Creer une branche pour le changement de prompt
2. **DEVELOPPE** — Editer le prompt en dev, tests manuels
3. **EVALUE** — Lancer le pipeline d'eval vs dataset gold (automatise en CI)
4. **COMPARE** — Comparer le score du nouveau prompt vs production actuelle
5. **REVIEW** — PR review : resultats d'eval + diff du prompt
6. **PROMEUT** — Staging vers Production avec gate d'approbation
7. **MONITORE** — Surveiller les metriques production pendant 24-48h
8. **ROLLBACK** — Retour a la version precedente en une commande si besoin

### A/B testing de prompts

- Assignment stable (meme utilisateur = meme variante, base sur hash user_id)
- Logger chaque assignment (user_id, slug prompt, variante)
- Definir la metrique de succes AVANT de commencer (pas apres)
- Minimum 1 semaine ou 1000 requetes par variante
- Significativite statistique : p<0.05 avant de declarer un gagnant

---

## Declencheurs proactifs

Signaler sans qu'on te le demande :

| Signal | Probleme |
|--------|----------|
| Prompts hardcodes dans le code | Les changements de prompt necessitent un deploiement de code |
| Pas de dataset gold pour les prompts en production | Chaque changement peut silencieusement degrader la qualite |
| Taux de reussite des evals en baisse | Les mises a jour de modele peuvent silencieusement casser les prompts |
| Pas de capacite de rollback | Un mauvais prompt en production = bloques jusqu'au prochain deploy |
| Une seule personne detient le savoir des prompts | Bus factor de 1 |
| Deploiement de prompt sans eval | Chaque deploy sans eval est un pari |

---

## Anti-patterns

| Anti-pattern | Pourquoi ca echoue | Meilleure approche |
|---|---|---|
| Prompts hardcodes dans le code source | Iteration lente, couplage des preoccupations | Registre versionne separe du code applicatif |
| Deploy sans evals | Regressions silencieuses | Gater chaque changement sur le pipeline d'eval |
| Un seul dataset gold pour toujours | Le dataset derive des usages reels | Revoir et mettre a jour trimestriellement |
| A/B test sans metrique definie a l'avance | Biais de selection post-hoc | Definir la metrique avant de lancer le test |
| Pas de rollback | Urgence en production sans recours | Chaque promotion = rollback en une commande |
