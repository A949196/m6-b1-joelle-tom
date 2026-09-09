# Note de recommandation — Dérive `pyrenex_risk_v2`

**Pour :** Sophie Léger (Lead Data, Pyrenex)  **De :** Tom & Joelle

## Constat (chiffré)
Sur les 12 dernières semaines, le F1 macro passe de 0.61 à 0.55 (-0.056).
1 variable(s) d'entrée ont nettement changé de profil : `int_rate` (PSI 0.44).
La fiabilité des probabilités annoncées (écart moyen entre probabilité prédite et défaut observé) passe de 0.240 à 0.315.

## Diagnostic
**Data drift.** Le modèle trie les dossiers avec la même qualité qu'au départ (capacité de tri : 0.74 → 0.75, écart +0.011 pour une tolérance de 0.03), alors que les dossiers entrants, eux, ont changé. La logique de risque tient donc ; c'est la population qui a bougé.
Évolution tendance progressive : le plus fort mouvement hebdomadaire ne porte que 18% de l'amplitude totale, ce qui écarte l'hypothèse d'un incident technique ponctuel.

## Recommandation
**Réentraîner sur données récentes.**
Le modèle trie toujours correctement les dossiers, mais la clientèle entrante a changé : les probabilités annoncées ne sont plus fiables. Un réentraînement les recale sans revoir l'architecture.

## Coût estimé
~2 j-homme ; risque prod faible, la CI/CD M5 bloque en cas de dégradation. Fenêtre d'intervention : sous 1 mois.

## Décision suggérée
> Réentraîner sur données récentes, sous 1 mois.

---
_Réserves : les défauts des dernières semaines ne sont pas encore tous constatés ; trois mois d'historique ne permettent pas d'écarter un effet saisonnier._
