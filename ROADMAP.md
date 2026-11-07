# Roadmap du fork MuseScore

> Cette feuille de route décrit le travail de maintenance et de validation mené dans ce fork. Elle ne remplace pas la roadmap officielle du projet MuseScore.

## Objectif

Disposer d'une chaîne de contribution fiable sur Linux Mint, sans dégrader les validations Windows, macOS et Linux des autres architectures. Chaque étape doit être vérifiable par des résultats GitHub Actions reproductibles.

## P0 — Stabiliser la CI Linux Mint

- [x] Utiliser le runner Linux Mint auto-hébergé pour les jobs Linux ciblés.
- [x] Installer les dépendances système manquantes du workflow sans Qt, dont Ninja.
- [x] Lire le fichier .gitmodules du commit testé pour vérifier les sous-modules.
- [x] Permettre de limiter le parallélisme avec MUSESCORE_BUILD_JOBS.
- [x] Limiter à deux tâches de compilation simultanées sur la machine à mémoire contrainte.
- [x] Sérialiser les compilations courante et de référence des tests visuels.
- [ ] Valider une compilation Linux complète sur le runner Mint.
- [ ] Vérifier par un `workflow_dispatch` que les lancements manuels exécutent réellement les compilations et comparaisons.

Critère de sortie : build Linux terminé avec succès sur le SHA courant, sans arrêt de compilateur lié à la mémoire.

## P1 — Fiabiliser les tests visuels

- [x] Ajouter au déclenchement manuel une référence explicite (branche ou SHA), avec `main` par défaut.
- [ ] Exécuter successivement le build courant, le build de référence, puis la comparaison des images.
- [ ] Conserver les rapports et artefacts utiles en cas d'échec.
- [ ] Distinguer un échec de comparaison d'un échec de compilation.
- [ ] Documenter les limites connues du runner (RAM, espace disque, durée).

Critère de sortie : un run complet prouve que les étapes ont été exécutées et fournit un résultat exploitable.

## P2 — Préserver la couverture multiplateforme

- [x] Vérifier les workflows Windows et macOS : succès sur le commit testé.
- [x] Vérifier Linux sans Qt et les contrôles de sous-modules : succès sur le commit testé.
- [x] Vérifier la cible Linux ARM64 : succès sur le commit testé (la cible x64 reste à valider).
- [ ] Contrôler les dépendances, versions d'outils et chemins propres à chaque runner.

Critère de sortie : matrice de CI documentée avec statut réel par plateforme et architecture.

## P3 — Rendre les résultats reproductibles et lisibles

- [ ] Documenter les jobs attendus, prérequis du runner Mint et commandes de diagnostic.
- [ ] Éviter les relances automatiques non ciblées ; relancer uniquement après diagnostic.
- [ ] Conserver des limites de concurrence adaptées à la mémoire disponible.
- [ ] Utiliser des références de comparaison explicites pour les workflows manuels et PR.
- [ ] Réduire les alertes inutiles tout en gardant visibles les échecs bloquants.

Critère de sortie : un contributeur peut comprendre un échec avec le run, les logs et la documentation ; un job ignoré n'est jamais assimilé à un succès.

## P4 — Maintenance continue

- [ ] Réévaluer la roadmap après chaque validation complète et après les changements importants des workflows amont.
- [ ] Traiter d'abord les régressions CI, dépendances manquantes et erreurs reproductibles.
- [ ] Garder les modifications du fork ciblées, documentées et faciles à rebaser.
- [ ] Proposer séparément les changements génériques utiles au projet amont.

## Règles de validation

1. Un workflow vert ne vaut validation que si les jobs attendus ont réellement été exécutés ; expliquer les jobs ignorés.
2. Distinguer queued, in_progress, success, failure et cancelled.
3. Pour chaque échec, relever le job, l'étape, le SHA et le log qui établit le diagnostic.
4. Ne jamais fusionner une pull request sans accord explicite.
5. Ne pas modifier Windows/macOS pour contourner un problème propre au runner Linux Mint.
6. Cocher une tâche uniquement après preuve dans la CI ou dans le dépôt.

## État de suivi

- Pull request : [#1 — CI Linux Mint](https://github.com/claudeia206/MuseScore/pull/1)
- Branche : ci/self-hosted-linux-mint vers main
- Dernier état connu : les compilations de tests visuels ont été sérialisées pour limiter la pression mémoire. Le lancement manuel précédent n'avait exécuté que la préparation ; la validation visuelle complète reste à faire.
- Prochaine action : rendre le lancement manuel des tests visuels réellement exécutable, puis lancer et examiner un run complet sur le runner Mint.
