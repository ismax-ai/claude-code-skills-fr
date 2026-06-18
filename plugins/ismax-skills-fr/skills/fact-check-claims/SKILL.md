---
name: fact-check-claims
description: "Utilise ce skill avant de publier un contenu qui affirme des faits, pour vérifier chaque affirmation contre une source officielle en direct plutôt que de te fier à ta mémoire. À utiliser quand : 1) Tu écris un guide, une newsletter, un post ou une page qui cite des chiffres, des dates, des fonctionnalités d'outils ou des citations, 2) Tu veux éviter de publier une info devenue fausse depuis ta date de formation, 3) Un contenu mentionne une personne, une étude, une loi ou une statistique. Exemple : <example>Contexte : la créatrice a rédigé un article qui dit qu'un outil propose une fonctionnalité gratuite et que son concurrent compte 30 millions d'utilisateurs. user: 'Mon article est prêt, je le publie ?' assistant: 'Avant de publier, je lance fact-check-claims pour vérifier chaque affirmation factuelle contre les sources officielles en direct.' <commentary>L'article contient des affirmations vérifiables (fonctionnalité, chiffre) qui peuvent avoir changé, donc on vérifie avant publication.</commentary></example>"
color: blue
keyword: SKILLS
dm_intro: "Salut ! Voici le skill fact-check-claims. Il vérifie chaque fait de ton contenu contre les sources officielles en direct, pour t'éviter de publier un chiffre ou une date qui a changé depuis."
install_prompt: |
  Installe le skill fact-check-claims depuis github.com/ismax-ai/claude-code-skills-fr.
  Étape 1, clone le repo en depth=1 dans un dossier temporaire.
  Étape 2, copie plugins/ismax-skills-fr/skills/fact-check-claims/ vers ~/.claude/skills/.
  Étape 3, supprime le dossier temporaire et confirme-moi quand tout est OK.
ready_for_promo: true
---

## Ce que ce skill résout

Tu publies un contenu et il contient une info fausse. Pas par malhonnêteté, juste parce que ta mémoire est figée à ta date de formation, et que les faits bougent tout le temps. Un outil qui change de prix, une fonctionnalité qui n'existe pas encore, un chiffre d'utilisateurs périmé, une citation mal attribuée, une date approximative. Un seul fait faux suffit à faire douter le lecteur de tout le reste.

Ce skill scanne ton contenu, repère chaque affirmation vérifiable, et la confronte à une source officielle consultée en direct. Pas à ta mémoire. À la vraie page, maintenant.

Il couvre tous les domaines, pas seulement la tech : fonctionnalités d'outils, prix, statistiques de marché, biographies, citations attribuées, études scientifiques, articles de loi, données géographiques, et tout chiffre ou date précis.

## Comment il fonctionne

Le skill suit cinq étapes.

### 1. Extraction des affirmations vérifiables

Il scanne ton texte et isole les phrases qui contiennent un fait testable :
- Une affirmation absolue ("jamais", "tous les", "personne ne", "le seul à")
- Un chiffre précis ("30 millions d'utilisateurs", "47 € par mois", "10 secondes")
- Une date précise ("depuis mai 2025", "fondée en 2021", "lancé l'an dernier")
- Un nom propre suivi d'une affirmation ("X a fait Y", "X a déclaré Z")
- Une comparaison ("X est plus rapide que Y", "X coûte moitié moins que Y")

### 2. Vérification sur la source officielle

Pour chaque affirmation, le skill identifie la source de référence du domaine et va la lire en direct (via l'outil de récupération de page web) :

| Type d'affirmation | Où on vérifie en premier |
|---|---|
| Fonctionnalité d'un outil | La page officielle ou la doc de l'éditeur |
| Prix, plan, quota | La page tarifs officielle de l'éditeur |
| Statistique d'entreprise | Le communiqué ou rapport officiel de l'entreprise |
| Personne publique | Son site, sa page LinkedIn, sa fiche Wikipédia (croisée) |
| Étude scientifique | Le journal source, le DOI, ou la prépublication |
| Loi ou régulation | Le texte officiel (Legifrance, EUR-Lex) |
| Chiffre de marché | La source primaire citée, pas un blog qui recopie |

Chaque affirmation reçoit un statut :
- **[VÉRIFIÉ]** : la source confirme. On note l'URL et l'extrait exact.
- **[CONTREDIT]** : la source dit autre chose. On note l'URL et la version correcte.
- **[NON VÉRIFIABLE]** : introuvable. À reformuler prudemment ou à retirer.

### 3. Croisement quand l'affirmation est ambiguë

Pour les faits absents d'une doc officielle (chiffres de marché, citations, événements récents), le skill fait une recherche web et exige au moins deux sources indépendantes qui concordent. Il se méfie des sources qui se recopient en boucle (un blog cite un blog qui cite le premier blog). Si la seule preuve est un unique post sur les réseaux, le statut reste [NON VÉRIFIABLE].

### 4. Rapport

Le skill produit un rapport clair : nombre total d'affirmations, combien sont vérifiées, contredites, non vérifiables. Pour chaque affirmation contredite, il donne la phrase d'origine, la correction, et l'URL de la source. Tu vois exactement quoi corriger et pourquoi.

### 5. Verdict

- **PASSE** : zéro affirmation contredite. Tu peux publier.
- **PASSE AVEC CORRECTIONS** : une à trois affirmations à corriger. On applique les corrections, c'est bon.
- **ÉCHEC** : plus de trois affirmations fausses, ou une seule mais critique (un prix faux, une citation inventée, une date erronée). On réécrit la section et on revérifie.

## Règles de fonctionnement

- Un fait n'est jamais marqué [VÉRIFIÉ] depuis la mémoire. La page consultée en direct est la seule autorité.
- Pour marquer [VÉRIFIÉ] ou [CONTREDIT], le rapport doit contenir trois éléments : l'URL consultée, l'extrait exact copié de la source, et l'horodatage de la consultation. Sans ces trois éléments, le statut redevient [NON VÉRIFIABLE].
- Ne jamais sauter une vérification parce que le fait semble "mineur" ou "évident". Les faits génériques sont précisément ceux que les lecteurs recoupent.
- Ne jamais remplacer une vérification par une reformulation vague. Vague n'égale pas correct, juste impossible à attaquer (et mauvais pour le lecteur).
- "Je n'ai pas trouvé" ne veut pas dire "ça n'existe pas". Si une source est introuvable, on le dit, on ne tranche pas.

## Comment l'utiliser

Donne-lui ton contenu à vérifier.

```
/fact-check-claims chemin/vers/mon-article.md
```

Exemple concret. Tu rédiges un post qui dit : *"Cet assistant compte 30 millions d'utilisateurs et propose la recherche web gratuitement depuis 2024."* Le skill isole deux affirmations : le chiffre d'utilisateurs et la date de gratuité. Il va lire la page officielle et le communiqué de l'éditeur. Verdict : le chiffre est [CONTREDIT] (la source officielle annonce un autre nombre), la fonctionnalité gratuite est [VÉRIFIÉ] mais [CONTREDIT] sur la date (depuis 2025, pas 2024). Il te rend les deux corrections avec les liens. Tu corriges en trente secondes au lieu de publier deux erreurs.

Pense à utiliser ce skill en complément d'une relecture de style : la relecture juge la qualité (est-ce clair, bien écrit), le fact-check juge la vérité (est-ce exact). Les deux sont nécessaires et indépendants.
