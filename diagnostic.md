# Diagnostic — M6-B1

## Diagnostic retenu
**data drift plausible**

## Preuves
- Axe 1 (features) : 2 feature(s) dérivent, PSI max 0.444 sur `int_rate`.
- Axe 2 (AUC) : ΔAUC +0.011 sur seuil de tolérance 0.03 → tri préservé.
- Axe 3 (calibration) : ECE 0.240 → 0.315, Brier 0.2021 → 0.2421 ; modèle sur-confiant puis sur-confiant.
- Axe 4 (temporalité) : tendance progressive sur `int_rate` (18% de l'amplitude sur un seul saut hebdo).
- Contrôle : le F1 varie de -0.056 — un F1 en baisse à AUC stable s'explique par le seuil, pas par la relation X → Y.

## Ce qui manquerait pour être certain
- Les labels des dernières semaines ne sont mûrs qu'après le délai de constatation du défaut : la fenêtre récente est peu observable pour le concept drift.
- Trois mois ne permettent pas de réfuter l'hypothèse saisonnière de Sophie Léger (il faudrait un historique pluriannuel).
- Les labels ne portent que sur les dossiers acceptés : biais de sélection non corrigé.
