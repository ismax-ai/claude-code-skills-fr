---
name: agent-auto-ameliorant
description: "Il produit, il critique, il corrige. Tout seul. Curation de la memoire auto de Claude Code : analyse, promotion en regles, extraction en skills reutilisables."
---

# Agent Auto-Ameliorant

> Inspire de [pskoett/self-improving-agent](https://github.com/pskoett/self-improving-agent) — adapte en francais.

La memoire auto de Claude Code capture des patterns. Ce skill ajoute l'intelligence : il analyse ce que Claude a appris, promeut les patterns prouves en regles, et extrait les solutions recurrentes en skills reutilisables.

## Commandes

| Commande | Ce que ca fait |
|----------|---------------|
| `/si:review` | Analyse MEMORY.md — trouve les candidats a promotion, les entrees obsoletes, les opportunites de consolidation |
| `/si:promote` | Fait passer un pattern de MEMORY.md → CLAUDE.md ou `.claude/rules/` |
| `/si:extract` | Transforme un pattern prouve en skill standalone |
| `/si:status` | Dashboard sante memoire — nombre de lignes, fichiers thematiques, recommandations |
| `/si:remember` | Sauvegarde explicitement un savoir important dans la memoire auto |

## Comment ca s'articule

```
┌──────────────────────────────────────────────────┐
│              Pile memoire Claude Code              │
├─────────────┬──────────────────┬─────────────────┤
│  CLAUDE.md  │   Memoire auto   │  Memoire session │
│  (toi)      │   (Claude)       │  (Claude)        │
│  Regles &   │   MEMORY.md      │  Logs de         │
│  standards  │   + fichiers     │  conversation    │
│  Charge     │   thematiques    │  Contextuel      │
│  complet    │   200 premieres  │                  │
│             │   lignes         │                  │
├─────────────┴──────────────────┴─────────────────┤
│          ↑ /si:promote    ↑ /si:review           │
│       Agent Auto-Ameliorant (ce skill)            │
│          ↓ /si:extract    ↓ /si:remember         │
├──────────────────────────────────────────────────┤
│  .claude/rules/   │  Nouveaux skills  │  Logs    │
│  (regles scopees) │  (extraits)       │  erreurs │
└──────────────────────────────────────────────────┘
```

## Architecture memoire

| Fichier | Qui ecrit | Charge quand |
|---------|-----------|-------------|
| `./CLAUDE.md` | Toi (+ `/si:promote`) | Chaque session, en entier |
| `MEMORY.md` | Claude (auto) | 200 premieres lignes |
| `memory/*.md` | Claude (debordement) | A la demande |
| `.claude/rules/*.md` | Toi (+ `/si:promote`) | Quand les fichiers concernes sont ouverts |

## Cycle de promotion

```
1. Claude decouvre un pattern → memoire auto (MEMORY.md)
2. Le pattern revient 2-3 fois → /si:review le signale comme candidat
3. Tu approuves → /si:promote le fait monter en CLAUDE.md ou rules/
4. Le pattern devient une regle appliquee, plus juste une note
5. L'entree MEMORY.md est supprimee → libere de l'espace pour de nouveaux apprentissages
```

## Concepts cles

### La memoire auto capture, elle ne trie pas

La memoire auto est excellente pour enregistrer ce que Claude apprend. Mais elle ne juge pas :
- Quel apprentissage est temporaire vs permanent
- Quels patterns devraient devenir des regles
- Quand la limite de 200 lignes gaspille de l'espace sur des entrees obsoletes
- Quelles solutions meritent de devenir des skills

C'est le role de ce skill.

### Promotion = graduation

Quand tu promeus un apprentissage, il passe du brouillon de Claude (MEMORY.md) au systeme de regles de ton projet (CLAUDE.md ou `.claude/rules/`).

- **MEMORY.md** : "Ce projet utilise pnpm" (contexte de fond)
- **CLAUDE.md** : "Utiliser pnpm, pas npm" (instruction appliquee)

Les regles promues ont une priorite superieure et sont chargees en entier (pas tronquees a 200 lignes).

### Le dossier rules/ pour le savoir scope

Tout ne va pas dans CLAUDE.md. Utilise `.claude/rules/` pour les patterns qui s'appliquent uniquement a certains types de fichiers :

```yaml
# Exemple : regle scopee sur les tests API
---
paths:
  - "src/api/**/*.test.ts"
  - "tests/api/**/*"
---
- Utiliser supertest pour les tests d'endpoints API
- Mocker les services externes avec msw
- Toujours tester les reponses d'erreur, pas juste le happy path
```

Ca se charge uniquement quand Claude travaille sur des fichiers de test API. Zero overhead sinon.

## Agents internes

### memory-analyst
Analyse MEMORY.md et les fichiers thematiques pour identifier :
- Entrees qui reviennent entre sessions (candidats a promotion)
- Entrees obsoletes qui referencent des fichiers supprimes
- Entrees liees qui devraient etre consolidees
- Ecarts entre ce que MEMORY.md sait et ce que CLAUDE.md impose

### skill-extractor
Prend un pattern prouve et genere un skill complet :
- Fichier SKILL.md avec frontmatter correct
- Documentation de reference
- Exemples et cas limites
- Pret a installer
