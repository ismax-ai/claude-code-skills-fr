---
name: ralph
description: "Boucle autonome jusqu'a completion. Mode interne (skill) ou externe (loop bash avec contexte frais)."
---

# Ralph — La Boucle Infinie

> Inspire de [Geoffrey Huntley](https://ghuntley.com/ralph/) — traduit en francais, enrichi avec structure plan d'implementation et double mode.

2 modes disponibles :

## Mode 1 : Skill interne (dans la session)

Boucle qui continue dans la session courante. Bon pour les taches moyennes.

### Utilisation
```
/ralph "<tache>" [--max=N]
```

### Protocole
1. **ANALYSE** la tache et decoupe-la en etapes
2. **EXECUTE** une etape
3. **VERIFIE** le resultat
4. **SI ERREUR** → corrige et reessaye
5. **SI SUCCES** → passe a l'etape suivante
6. **REPETE** jusqu'a completion totale

### Affichage progression
```
+-------------------------------------------+
|          RALPH LOOP - Iteration N/X       |
+-------------------------------------------+
| Tache: [description courte]               |
| Etape actuelle: [X sur Y]                 |
| Statut: [EN COURS / SUCCES / ERREUR]      |
| Prochaine action: [description]           |
+-------------------------------------------+
```

### Parachutes de securite
- **MAX ITERATIONS** : Defaut = 10, ajustable avec --max=N
- **CHECKPOINT** : Sauvegarde apres chaque etape reussie
- **PAUSE si erreur critique** : Demande confirmation avant de continuer

---

## Mode 2 : Loop externe (contexte frais a chaque iteration)

Boucle bash externe qui relance Claude en headless a chaque iteration. Chaque iteration a 100% du contexte disponible. Pour les gros projets.

### Principe
```bash
# Depuis le dossier du projet :

# 1. Creer un fichier AGENTS.md dans le projet (guide operationnel)
# 2. Creer un dossier specs/ avec les specifications du projet
# 3. Lancer en mode plan (genere IMPLEMENTATION_PLAN.md)
# 4. Lancer en mode build (1 tache par iteration, commit, recommence)
```

### Comment ca marche
- Claude lit le plan sur disque (IMPLEMENTATION_PLAN.md)
- Prend 1 tache, l'implemente, teste, commit, push
- Sort. La boucle relance avec un contexte 100% frais
- Pas de degradation de contexte, meme apres 50 iterations

### Setup
1. Un script bash qui boucle sur `claude --headless`
2. Un prompt de planification (genere le plan)
3. Un prompt de build (execute 1 tache du plan)
4. Un fichier AGENTS.md (guide operationnel pour Claude)

Le script bash est simple : une boucle `while true` qui lance Claude, attend qu'il finisse, et relance. Claude lit le plan a chaque iteration, trouve la prochaine tache non cochee, et l'execute.

### Quand utiliser quel mode

| Critere | Mode 1 (skill) | Mode 2 (loop externe) |
|---|---|---|
| Taille tache | Moyenne (< 1h) | Grande (multi-heures) |
| Contexte | Se degrade | Frais a chaque iteration |
| Commit | Manuel | Auto a chaque iteration |
| Subagents | Non | Oui (paralleles) |
| Setup | Zero | Script bash + specs/ |

---

## Completion (Mode 1)

```
+-------------------------------------------+
|        RALPH LOOP - TERMINE               |
+-------------------------------------------+
| Iterations totales: N                     |
| Temps total: Xm Ys                        |
| Resultat: [description]                   |
| Fichiers crees: [liste]                   |
+-------------------------------------------+
```

## Argument

$ARGUMENTS
