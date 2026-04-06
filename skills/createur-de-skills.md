---
name: createur-de-skills
description: "Tu decris ce que tu veux, il genere le skill complet. Structure, frontmatter, anti-patterns, test. Pret a installer."
---

# Createur de Skills

Tu veux un skill Claude Code. Tu decris ce qu'il doit faire. Ce skill genere le fichier `.md` complet, pret a copier dans ton projet.

## C'est quoi un skill

Un **skill** = un guide de reference pour une technique, un pattern ou un outil. Il aide Claude a trouver et appliquer des approches eprouvees.

**Un skill c'est :** une technique reutilisable, un pattern, un guide de reference.

**Un skill c'est PAS :** un recit de comment tu as resolu un probleme une fois.

## Quand creer un skill

**Creer quand :**
- La technique n'etait pas intuitive
- Tu la reutiliseras sur d'autres projets
- Le pattern s'applique largement (pas specifique a un projet)

**Ne PAS creer pour :**
- Une solution unique
- Des pratiques standard deja documentees
- Des conventions specifiques au projet (les mettre dans CLAUDE.md)

## 3 types de skills

| Type | Description | Exemple |
|------|-------------|---------|
| **Technique** | Methode concrete avec des etapes | Attente conditionnelle, tracage de cause racine |
| **Pattern** | Facon de penser un probleme | Aplatir avec flags, tester les invariants |
| **Reference** | Documentation API, syntaxe, outils | Guide d'un framework, commandes |

## Structure du fichier

```markdown
---
name: nom-du-skill
description: "Utiliser quand [conditions declencheuses specifiques]"
---

# Nom du Skill

## Vue d'ensemble
Quoi et pourquoi en 1-2 phrases.

## Quand utiliser
Liste avec SYMPTOMES et cas d'usage.
Quand NE PAS utiliser.

## Pattern principal
Avant/apres, exemples de code.

## Reference rapide
Table ou bullets pour scanner vite.

## Erreurs courantes
Ce qui peut mal tourner + corrections.
```

## Regles du frontmatter

- `name` : lettres, chiffres, tirets uniquement (pas de parentheses, pas de caracteres speciaux)
- `description` : commence par "Utiliser quand..." — decrit QUAND utiliser, PAS ce que le skill fait
- Max 1024 caracteres au total

La description decrit les **conditions de declenchement**. Pas le workflow. Pas le processus. Juste quand l'activer.

```yaml
# ❌ MAUVAIS : resume le workflow
description: "Revue de code entre chaque tache avec dispatch de sous-agents"

# ✅ BON : conditions de declenchement uniquement
description: "Utiliser quand tu executes des plans avec des taches independantes"
```

Pourquoi : si la description resume le workflow, Claude risque de suivre la description au lieu de lire le skill complet.

## Processus de creation (TDD adapte)

### 1. RED — Observer le comportement de base

Lancer le scenario SANS le skill. Documenter :
- Quels choix Claude fait naturellement
- Quelles rationalisations il utilise
- Quels cas il rate

### 2. GREEN — Ecrire le skill minimal

Ecrire le skill qui repond aux defauts observes. Pas de contenu hypothetique. Uniquement ce qui corrige les problemes reels.

Lancer le meme scenario AVEC le skill. Verifier que Claude suit les regles.

### 3. REFACTOR — Boucher les trous

Claude a trouve une nouvelle rationalisation ? Ajouter un contre-argument explicite. Re-tester jusqu'a ce que ce soit solide.

## Table anti-rationalisations

Pour les skills qui imposent de la discipline, ajouter une table :

| Excuse | Realite |
|--------|---------|
| "C'est trop simple pour tester" | Le simple casse. Le test prend 30 secondes. |
| "Je testerai apres" | Apres = jamais. |
| "C'est evident" | Evident pour toi ≠ evident pour Claude. Tester. |

## Anti-patterns

| Pattern | Pourquoi c'est mauvais |
|---------|----------------------|
| Recit narratif ("Le 3 mars on a trouve...") | Trop specifique, pas reutilisable |
| Exemples dans 5 langages | Qualite mediocre, maintenance lourde |
| Code dans les flowcharts | Impossible a copier-coller |
| Labels generiques (step1, helper2) | Les labels doivent avoir du sens |

## La regle d'or

```
PAS DE SKILL SANS TEST D'ABORD
```

Ecrire le skill avant de tester ? Le supprimer. Recommencer.
Modifier un skill sans tester ? Meme violation.

Pas d'exception. Pas pour "un ajout simple". Pas pour "juste une section".
