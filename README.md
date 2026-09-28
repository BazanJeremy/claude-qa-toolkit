# claude-qa-toolkit

**Boîte à outils QA pour Claude Code — clôture de session disciplinée, triage
déterministe des tests flaky, génération de plans de test depuis une user
story. Trois skills, zéro service externe, zéro clé API.**

[![License: MIT](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-plugin-d97757)](https://docs.anthropic.com/en/docs/claude-code/plugins)

> 🇬🇧 [English version](README.en.md)

## Le problème

Trois frictions récurrentes du travail QA assisté par IA :

- **La continuité entre sessions.** Chaque nouvelle session repart de zéro,
  redécouvre l'état du projet et rejoue des décisions déjà tranchées.
- **Le triage des tests instables.** Face à un historique CI bruité, la
  tentation est de raconter une cause plausible au lieu de la mesurer.
- **La dérive des plans de test.** Un plan généré librement invente des
  exigences et perd la trace des critères d'acceptation réels.

Ce plugin encapsule trois disciplines de travail — pas du code exécutable,
des protocoles que l'agent suit — avec le même principe que les outils
associés (voir plus bas) : **le déterministe décide, la narration explique.**

## Les trois skills

| Skill | Déclenchement | Ce qu'il garantit |
|---|---|---|
| `session-close` | « fin de session », « on clôture », feature terminée | Réécriture complète du fichier de contexte actif (≤ 40 lignes) + journal append-only : la session suivante démarre sur des faits |
| `flaky-triage` | historique CI fourni, « quels tests sont flaky ? » | Score sur 3 signaux (intermittence, bascule, durée), cause probable seuillée par la confiance — `unknown` plutôt qu'une invention, score amorti sous 4 runs |
| `test-plan-generator` | user story fournie, « génère le plan de test » | Cas nominaux/négatifs/limites priorisés par le risque, matrice de traçabilité AC → cas, ambiguïtés remontées en questions ouvertes |

## Installation

Depuis ce dépôt (qui est sa propre marketplace) :

```bash
claude plugin marketplace add BazanJeremy/claude-qa-toolkit
claude plugin install claude-qa-toolkit@claude-qa-toolkit --scope project
```

Ou depuis un clone local :

```bash
claude plugin marketplace add /chemin/vers/claude-qa-toolkit
claude plugin install claude-qa-toolkit@claude-qa-toolkit --scope project
```

Les skills se chargent à la session suivante et se déclenchent
automatiquement sur les phrases indiquées ci-dessus — aucune commande à
mémoriser.

### Hors Claude Code

Les trois skills respectent le format `SKILL.md` et ne dépendent d'aucun
mécanisme propre à Claude Code. Ils s'installent donc aussi dans les agents
qui lisent ce format :

```bash
npx skills add BazanJeremy/claude-qa-toolkit
```

`npx skills` est un installeur communautaire, pas un canal officiel : la voie
marketplace ci-dessus reste la voie de référence.

## Conception

- **Déterministe d'abord.** Les heuristiques de `flaky-triage` (pondérations
  0.4/0.4/0.2, amortissement sous 4 runs, plancher de confiance 0.4) sont
  celles éprouvées dans [FlakySense](https://github.com/BazanJeremy/flakysense) ;
  le skill applique la méthode là où l'outil applique le code.
- **L'ambiguïté est un livrable.** `test-plan-generator` transforme chaque
  zone floue en question ouverte au lieu de la combler par une hypothèse.
- **La continuité est un rituel, pas une mémoire magique.**
  `session-close` impose deux disciplines d'écriture opposées : réécriture
  intégrale du contexte chaud, append-only pour l'archive.
- **Progressive disclosure.** Chaque `SKILL.md` reste court ; les matrices
  détaillées (signatures de causes, gabarit de plan) vivent dans
  `references/` et ne chargent le contexte que si nécessaire.

## Limites connues

- Les skills sont des protocoles suivis par l'agent, pas des exécutables :
  la reproductibilité est méthodologique, pas mécanique.
- `flaky-triage` exige un historique multi-runs ; un rapport JUnit isolé ne
  produit aucun verdict.
- Heuristiques calibrées sur les scénarios synthétiques de FlakySense ; sur
  un historique réel volumineux, les seuils peuvent demander un réglage.

## Projets associés

Ces outils partagent les mêmes principes : **le déterministe d'abord, l'IA là où elle apporte — le QA reste l'arbitre.** Tous tournent en local, aucune clé API requise.

| Projet | Focus |
|---|---|
| [claude-qa-toolkit](https://github.com/BazanJeremy/claude-qa-toolkit) **← ce repo** | Plugin Claude Code : disciplines QA en skills |
| [EvalForge](https://github.com/BazanJeremy/EvalForge) | Évaluation de LLM & calibration du juge |
| [ReleaseGuard](https://github.com/BazanJeremy/ReleaseGuard) | Verrou de release GO/NO-GO explicable |
| [FlakySense](https://github.com/BazanJeremy/flakysense) | Diagnostic statistique des tests flaky |
| [Anomaly Sentinel](https://github.com/BazanJeremy/anomaly-sentinel) | Tester les IA de détection d'anomalies (medtech · fintech) |
| [TestScribe](https://github.com/BazanJeremy/testscribe) | Enrichissement de bug reports assisté par IA |
| [SkyGuard](https://github.com/BazanJeremy/skyguard) | Quality gate sécurité pour systèmes critiques avioniques |

## Auteur

**Jérémy Bazan** — Ingénieur QA / Lead Tech QA, spécialisation AI-driven Quality.
ISTQB Foundation v4. Intégration de LLM (Claude, GPT) dans des pipelines QA de
production au sein d'un grand groupe du secteur de l'énergie.

[LinkedIn](https://www.linkedin.com/in/jeremy-bazan/) · [GitHub](https://github.com/BazanJeremy)

## Licence

[MIT](LICENSE)
