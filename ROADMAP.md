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
- [x] Désactiver la persistance des identifiants Git dans les quatre checkout du workflow visuel pour éviter les en-têtes `Authorization` dupliqués sur le runner persistant.

Critère de sortie : build Linux terminé avec succès sur le SHA courant, sans arrêt de compilateur lié à la mémoire.

## P1 — Fiabiliser les tests visuels

- [x] Ajouter au déclenchement manuel une référence explicite (branche ou SHA), avec `main` par défaut.
- [ ] Exécuter successivement le build courant, le build de référence, puis la comparaison des images.
- [ ] Confirmer que la correction `persist-credentials: false` permet au clonage et aux sous-modules de passer.
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

## État de suivi — vérifié le 8 novembre 2026

- Pull request : [#1 — CI Linux Mint](https://github.com/claudeia206/MuseScore/pull/1), toujours ouverte et non fusionnée.
- Branche : `ci/self-hosted-linux-mint` vers `main`.
- SHA PR : `f793fc180d1667752cce17de155929e0e727057f` ; SHA de base `main` : `d299fc2ffcd977d077077da276c512a862c095c7`.
- Linux, run [42347976570](https://github.com/claudeia206/MuseScore/actions/runs/42347976570), SHA `f793fc1` : ARM64 **success** ; Linux x64 **failure**. Les logs du job [Linux x64](https://github.com/claudeia206/MuseScore/actions/runs/42347976570/job/128329792360) montrent qu'à l'étape Build (`797/2636`), `cc1plus` a été interrompu par un signal, puis `The runner has received a shutdown signal`. Cela ne prouve ni un OOM ni une erreur source ; la cause de l'arrêt du runner reste à établir.
- Tests visuels PR [42347976119](https://github.com/claudeia206/MuseScore/actions/runs/42347976119), SHA `f793fc1` : Setup **success** ; Build current **cancelled** ; Build reference et Generate and Compare **skipped**.
- Déclenchement manuel [42347949410](https://github.com/claudeia206/MuseScore/actions/runs/42347949410), SHA `7aacc410` : Setup **success**, référence explicite `main` acceptée ; Build current **cancelled** ; Build reference et Generate and Compare **skipped**. Le déclenchement et la référence sont donc validés jusqu'au Setup, pas la chaîne de compilation/comparaison.
- Sur le SHA PR `f793fc1` : Windows x64, macOS, Linux sans Qt, codestyle et sous-modules **success** ; ARM64 **success**. Windows portable **skipped** selon la condition du workflow. Linux x64 et tests visuels restent bloquants.
- Aucune case P0/P1 supplémentaire cochée : les jobs visuels attendus n'ont pas été exécutés.
- Prochaine action (P0) : diagnostiquer sur la machine Linux Mint le service/log du runner et la cause du signal d'arrêt. Après confirmation du runner opérationnel, relancer uniquement le build Linux x64 et le workflow visuel manuel avec une référence explicite ; vérifier ensuite les trois jobs réellement exécutés. Ne pas attribuer l'arrêt à la mémoire sans preuve locale.

