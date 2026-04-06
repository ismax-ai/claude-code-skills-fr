# Skills Claude Code : fais relire ton contenu par des experts avant de publier

> 2 skills qui attaquent systématiquement ton contenu. S'il survit, il est prêt.

---

## Prérequis

Tu as besoin de **Claude Code** (l'assistant IA en ligne de commande d'Anthropic). Si tu ne l'as pas encore, c'est la version "pro" de Claude qui travaille directement sur ton ordinateur.

Tu peux l'installer depuis [claude.ai/code](https://claude.ai/code) ou depuis l'app Claude Desktop.

---

## Le problème

Tu écris un post. Tu relis. Tu corriges une virgule. Tu publies.

Et là, un commentaire : "T'as vérifié tes sources ?" ou pire, le silence. Personne ne réagit parce que le texte sonnait robot sans que tu t'en rendes compte.

Le problème, c'est pas ta rédaction. C'est que tu relis avec les mêmes yeux qui ont écrit. Tu ne peux pas être ton propre critique.

## La solution

2 skills Claude Code qui simulent des regards extérieurs sur ton contenu :

| Skill | Ce qu'il fait | En une phrase |
|-------|---------------|---------------|
| `/sentinel` | Attaque ton contenu avec 20 profils professionnels | Un red team de sécurité pour tes textes |
| `/eagle-supervisor` | 6 vérifications séquentielles avant publication | Un directeur éditorial exigeant |

**Un skill, c'est quoi ?** C'est une instruction que tu donnes à Claude une seule fois. Après, tu l'appelles quand tu veux en tapant `/sentinel` ou `/eagle-supervisor`. Comme un raccourci vers un expert spécialisé.

---

## Installation

Copie un de ces textes dans Claude Code. Claude s'occupe du reste.

### Installer /sentinel

```
Va sur https://github.com/dersoiralaoui-ai/claude-code-skills,
lis le fichier skills/sentinel.md,
et installe-le comme commande Claude Code.
```

### Installer /eagle-supervisor

```
Va sur https://github.com/dersoiralaoui-ai/claude-code-skills,
lis le fichier skills/eagle-supervisor.md,
et installe-le comme commande Claude Code.
```

### Installer les deux d'un coup

```
Va sur https://github.com/dersoiralaoui-ai/claude-code-skills,
lis les fichiers dans le dossier skills/,
et installe-les tous comme commandes Claude Code.
```

Pas de terminal. Pas de ligne de commande. Pas de dossier à créer. Claude fait tout.

---

## Les skills en détail

### /sentinel, le Red Team

En cybersécurité, un **Red Team** est une équipe qui attaque volontairement un système pour trouver les failles AVANT qu'un vrai attaquant ne le fasse.

Sentinel fait la même chose avec ton contenu. Il lance 3 batteries d'attaques :

**1. CIA Structured Analytic Techniques (9 techniques)**

Les mêmes outils que les analystes du renseignement utilisent pour éviter les biais cognitifs.

- **Pre-mortem** : "On est 6 mois plus tard, ça a échoué. Pourquoi ?"
- **Key Assumptions Check** : "Quelles hypothèses non vérifiées dans ce texte ?"
- **Devil's Advocacy** : "L'argument le plus fort CONTRE ta position ?"
- Et 6 autres techniques (analyse des hypothèses concurrentes, auto-critique structurée, détection de tromperie...)

**2. Six Thinking Hats (6 perspectives)**

La méthode d'Edward de Bono. Chaque "chapeau" force un angle différent :

- Chapeau blanc : les faits purs (qu'est-ce qui est prouvé ?)
- Chapeau noir : la critique (qu'est-ce qui peut foirer ?)
- Chapeau rouge : les émotions (quelle réaction instinctive du lecteur ?)
- Chapeau vert : la créativité (quel angle mort ?)
- Chapeau jaune : l'optimisme (quelles forces sous-exploitées ?)
- Chapeau bleu : le processus (le raisonnement est-il solide ?)

**3. OWASP LLM (5 vérifications)**

Adapté des standards de sécurité pour l'IA :

- Le contenu affirme-t-il des choses sans source ?
- Le ton est-il adapté à l'audience ?
- Les sources citées sont-elles fiables ?

**Résultat** : un score sur 10 avec un verdict clair.
- **PASS** (7+) : ton contenu est solide
- **AMEND** (4-6) : des corrections sont nécessaires
- **FAIL** (<4) : réécriture nécessaire

---

### /eagle-supervisor, l'Aigle Superviseur

L'aigle survole tout ton contenu depuis le ciel. Il ne réécrit rien. Il vérifie tout.

**6 passes séquentielles. 1 seul FAIL = rien ne sort.**

| Passe | Nom | Ce qu'elle vérifie |
|-------|-----|-------------------|
| A | **Format** | Le bon type de contenu est identifié |
| B | **Structure** | Les blocs sont complets, les transitions fluides |
| C | **Langue** | 0 pattern robotique, 0 calque anglais, rythme naturel |
| D | **Ton** | Ça sonne comme toi, pas comme un chatbot ou un marketeur |
| E | **Audience** | Un non-expert comprend chaque mot |
| F | **Promesse** | Le titre est tenu dans le contenu |

**Pourquoi 6 passes au lieu d'une ?**

Parce qu'une relecture "globale" rate toujours quelque chose. Tu regardes le fond, tu oublies la forme. Tu vérifies le ton, tu oublies l'audience. L'aigle découpe la vérification en 6 angles. Chaque passe ne regarde qu'une seule chose. Rien ne passe entre les mailles.

**Le test clé** : l'aigle lit tes 3 premiers paragraphes "à voix haute". Si ça sonne comme une présentation PowerPoint, c'est FAIL. Si ça sonne comme un message à un collègue, c'est PASS.

---

## Utilisation

Une fois installé, tape dans Claude Code :

```
/sentinel                → analyse le dernier contenu de la conversation
/sentinel content        → challenge un texte spécifique
/sentinel code           → challenge du code
/sentinel decision       → challenge une décision business

/eagle-supervisor        → 6 passes de vérification avant publication
```

### Workflow recommandé

1. Tu écris ton contenu
2. `/sentinel` pour attaquer et trouver les failles
3. Tu corriges
4. `/eagle-supervisor` pour vérifier que tout est propre
5. Tu publies

Sentinel trouve les failles, l'aigle vérifie que les corrections tiennent.

---

## Exemples concrets

### /sentinel sur un post LinkedIn

Tu écris :
> "L'IA va remplacer 80% des jobs d'ici 2030. Les entreprises qui n'adoptent pas l'IA maintenant vont disparaître."

Sentinel répond :
```
🔴 RED TEAM — CONTENT

CIA SATs : 5/9 passed
  ❌ Key Assumptions Check : "80% des jobs", source ? Aucune étude sérieuse ne donne ce chiffre.
  ❌ Pre-mortem : si ce post est partagé, le premier commentaire sera "source ?"
  ❌ Deception Detection : "vont disparaître" = affirmation absolue non prouvable
  ❌ Devil's Advocacy : des entreprises sans IA dominent encore des secteurs entiers

Six Hats : 4/6 clean
  ⚠️ Blanc (Faits) : 0 source citée
  ⚠️ Rouge (Émotions) : génère de l'anxiété, pas de la réflexion

OWASP : 3/5 passed
  ❌ Misinformation : chiffre inventé ou non sourcé
  ❌ Excessive Agency : promet une certitude que personne n'a

SCORE : 4/10
VERDICT : AMEND
→ Remplacer "80%" par un chiffre sourcé (McKinsey, OCDE)
→ Remplacer "vont disparaître" par une nuance vérifiable
→ Ajouter 1 source minimum
```

### /eagle-supervisor sur une newsletter

```
🦅 EAGLE SUPERVISOR — NEWSLETTER

PASSE A — FORMAT : newsletter ✅
PASSE B — STRUCTURE : WARN
  ⚠️ Pas de transition entre la section 2 et 3 (saut brutal)
PASSE C — LANGUE : FAIL
  ❌ "Dans le monde d'aujourd'hui" (phrase de remplissage IA)
  ❌ 4 phrases consécutives de 12-14 mots (rythme monotone)
PASSE D — TON : PASS ✅
PASSE E — AUDIENCE : WARN
  ⚠️ "Fine-tuning" utilisé sans explication
PASSE F — PROMESSE : PASS ✅

VERDICT : CORRIGE AVANT
1. Retirer "Dans le monde d'aujourd'hui" (passe C)
2. Varier la longueur des phrases section 3 (passe C)
3. Expliquer "fine-tuning" à la première mention (passe E)
4. Ajouter une transition entre section 2 et 3 (passe B)
```

---

## La philosophie

Quand tu écris, ton cerveau comble les trous. Tu sais ce que tu voulais dire, donc tu lis ce que tu voulais dire, pas ce qui est écrit.

Ces skills simulent des regards que tu n'as pas :
- Un analyste qui cherche les hypothèses non vérifiées
- Un critique qui cherche les failles logiques
- Un lecteur non-expert qui bute sur le jargon
- Un détecteur qui repère les patterns IA

Si ton contenu survit à ces attaques, il survivra à ton audience.

---

## Crédits et inspirations

- **Red Team / CIA SATs** : adapté des [Structured Analytic Techniques](https://www.cia.gov/resources/csi/books-monographs/structured-analytic-techniques-for-intelligence-analysis/) utilisées par la communauté du renseignement
- **Six Thinking Hats** : méthode créée par [Edward de Bono](https://www.debonogroup.com/services/six-thinking-hats/)
- **OWASP LLM Top 10** : adapté des [standards de sécurité pour les LLM](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- **Boucle autonome** : inspiré par les travaux de [Geoffrey Huntley](https://ghuntley.com/ralph/) sur Claude Code

---

## Autres skills recommandés

Ces skills ne sont pas les miens, mais je les utilise et je les recommande :

| Skill | Auteur | Ce qu'il fait |
|-------|--------|---------------|
| **Devil's Advocate** | [notmanas](https://github.com/notmanas/claude-code-skills) | Pre-mortem et inversion de tes plans |
| **Karen** | [darcyegb](https://github.com/darcyegb/ClaudeCodeAgents) | Reality-check tes features "terminées" |

### Installer Devil's Advocate

```
Va sur https://github.com/notmanas/claude-code-skills,
trouve le skill devil's advocate,
et installe-le dans mes commandes Claude Code.
```

### Installer Karen

```
Va sur https://github.com/darcyegb/ClaudeCodeAgents/blob/master/karen.md,
lis le contenu de ce fichier,
et installe-le comme commande Claude Code.
```

---

## Licence

MIT. Utilise, modifie, partage librement.
