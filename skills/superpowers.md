---
name: superpowers
description: "Decoupe ta tache en sous-agents paralleles. Tout avance en meme temps. Utilise ce skill au debut de chaque session pour orchestrer tes autres skills."
---

# Superpowers

Tu as 10 skills. Ce meta-skill t'apprend a les utiliser ensemble.

Le probleme : tu installes des skills, tu les oublies, tu fais tout a la main. Les skills existent mais personne ne les appelle. Ce skill corrige ca.

## La regle

**Invoquer les skills pertinents AVANT de repondre.** Meme 1% de chance qu'un skill s'applique = l'invoquer pour verifier. Si le skill ne colle pas, pas grave. Mais ne pas verifier = erreur.

## Ordre de priorite

Quand plusieurs skills pourraient s'appliquer :

1. **Skills de processus d'abord** (devils-advocate, sentinel, karen) — ils determinent COMMENT aborder la tache
2. **Skills de production ensuite** (prompt-engineer, create-skill, ralph) — ils guident l'execution

"Construis X" → brainstorming d'abord, puis execution.
"Verifie X" → karen ou sentinel d'abord, puis corrections.

## Red flags

Ces pensees signifient STOP — tu rationalises :

| Pensee | Realite |
|--------|---------|
| "C'est juste une question simple" | Les questions sont des taches. Verifier les skills. |
| "J'ai besoin de contexte d'abord" | Le check des skills vient AVANT les questions de clarification. |
| "Pas besoin d'un skill formel" | Si un skill existe, l'utiliser. |
| "Je me souviens de ce skill" | Les skills evoluent. Lire la version actuelle. |
| "Le skill est excessif pour ca" | Les trucs simples deviennent complexes. L'utiliser. |
| "Je vais juste faire ce truc d'abord" | Verifier AVANT de faire quoi que ce soit. |

## Types de skills

**Rigides** (sentinel, karen, ralph) : suivre a la lettre. Ne pas adapter pour "gagner du temps".

**Flexibles** (prompt-engineer, superpowers) : adapter les principes au contexte.

Le skill lui-meme te dit lequel il est.

## Sous-agents paralleles

Quand une tache est complexe, la decouper :

1. Identifier les sous-taches independantes
2. Lancer un agent par sous-tache (en parallele)
3. Chaque agent utilise le skill adapte a sa tache
4. Rassembler les resultats

Exemple : tu veux publier un article.
- Agent 1 : `/prompt-engineer` pour ameliorer le brouillon
- Agent 2 : `/sentinel` pour red team le contenu
- Agent 3 : `/karen` pour verifier les claims factuels

Les 3 tournent en meme temps. Tu gagnes du temps. Le resultat est meilleur.

## Instructions utilisateur

Les instructions disent QUOI, pas COMMENT. "Ajoute X" ou "Corrige Y" ne veut pas dire sauter les workflows. Le skill te dit comment faire le travail. L'instruction te dit quel travail faire.
