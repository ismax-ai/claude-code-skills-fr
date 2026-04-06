# Installation

## Prérequis

Tu as besoin de **Claude Code**. C'est la version de Claude qui travaille directement sur ton ordi.

Si tu ne l'as pas encore, installe-le depuis [claude.ai/code](https://claude.ai/code) ou depuis l'app Claude Desktop.

---

## Méthode 1 : tout installer d'un coup

Ouvre Claude Code et colle ce texte :

```
Va sur https://github.com/ismax-ai/claude-code-skills-fr,
lis les fichiers dans le dossier skills/,
et installe-les tous comme commandes Claude Code.
```

Claude va lire chaque fichier et créer les commandes. Tu n'as rien d'autre à faire.

---

## Méthode 2 : installer un skill précis

Ouvre Claude Code et colle ce texte (remplace le nom du fichier) :

```
Va sur https://github.com/ismax-ai/claude-code-skills-fr,
lis le fichier skills/sentinel.md,
et installe-le comme commande Claude Code.
```

Les noms de fichiers disponibles :

| Skill | Fichier |
|-------|---------|
| Superpowers | `skills/superpowers.md` |
| Créateur de Skills | `skills/createur-de-skills.md` |
| Prompt Engineer | `skills/prompt-engineer.md` |
| Agent Auto-Améliorant | `skills/agent-auto-ameliorant.md` |
| Gouvernance Prompts | `skills/gouvernance-prompts.md` |
| L'Avocat du Diable | `skills/devils-advocate.md` |
| Sentinel | `skills/sentinel.md` |
| Karen | `skills/karen.md` |
| Ralph | `skills/ralph.md` |
| L'Aigle Superviseur | `skills/eagle-supervisor.md` |

---

## Méthode 3 : copier-coller manuel

Si tu préfères faire à la main :

1. Ouvre le fichier du skill que tu veux (dans le dossier `skills/`)
2. Copie tout le contenu
3. Crée un fichier dans ton dossier `.claude/commands/` avec le même nom
4. Colle le contenu
5. C'est bon. Tu peux lancer la commande

Le dossier `.claude/commands/` est à la racine de ton projet. Si le dossier n'existe pas, crée-le.

---

## Vérifier que ça marche

Après installation, tape dans Claude Code :

```
/sentinel
```

Si Claude te demande ce qu'il doit analyser, c'est installé. Si la commande n'est pas reconnue, relance Claude Code.

---

## Désinstaller

Supprime le fichier correspondant dans `.claude/commands/`. C'est tout.
