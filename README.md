# M6-B1 — Détection et diagnostic de dérive `pyrenex_risk_v2`

**Binôme :** Tom & Joelle · **Semaine 8** · Client : Sophie Léger (Pyrenex)

Trois mois après la mise en production du scoring v2, le script d'évaluation continue
émet des alertes. Ce repo détecte la dérive, la diagnostique et recommande une action
chiffrée. **Il ne remédie pas** — la boucle de rétroaction est le sujet de M6-B2.

## Résultat en une ligne

**Data drift confirmé sur `int_rate` (PSI 0.44), pouvoir de tri intact (AUC 0.74 → 0.75).
Réentraînement sur données récentes recommandé sous 1 mois, ~2 j-homme.**

## Résultats — triangulation des 4 axes

| Axe | Mesure | Résultat | Lecture |
|---|---|---|---|
| 1 — Features | PSI max | **0.44** sur `int_rate` | au-delà de 0.25 : dérive forte |
| 2 — Pouvoir de tri | ΔAUC (S1-4 → S9-12) | **+0.011** (0.74 → 0.75) | dans la tolérance de 0.03 : tri intact |
| 3 — Calibration | ECE | **0.240 → 0.315** | probabilités annoncées moins fiables |
| 4 — Temporalité | plus gros saut hebdo | **18 %** de l'amplitude | tendance progressive, pas un incident ETL |

Symptôme client : F1 macro 0.61 → 0.55 (−0.056).

**Diagnostic :** data drift. Les entrées ont bougé, la logique de risque tient.
Preuve : PSI 0.44 sur `int_rate` **avec** AUC stable — un concept drift ferait décrocher
l'AUC. Détail dans [`diagnostic.md`](diagnostic.md).

**Recommandation :** réentraîner sur données récentes, sous 1 mois, ~2 j-homme, risque
prod faible (la CI/CD M5 bloque en cas de dégradation). Note client dans
[`note_recommandation.md`](note_recommandation.md).

**Ce qui manquerait pour être certain :** les défauts des dernières semaines ne sont pas
tous constatés, et trois mois d'historique ne permettent pas d'écarter un effet
saisonnier. Condition de réfutation : si le PSI sur `int_rate` redescend sous 0.10 d'ici
quatre semaines, l'hypothèse d'un glissement durable du marché tombe.

## Jeu de référence retenu pour la boucle B2

**Retenu :** le `reference_set.csv` de M5-B2 (500 lignes), option simple.
**Raison :** nos seuils M5-B2 ont été calibrés sur ce jeu ; les garder évite une
recalibration complète (regel du golden run + bootstrap, ~30 min) sans bénéfice pour la
boucle de B2. Un seul jeu sert la boucle, celui-là.

Le `reference_set.csv` fourni avec ce brief (1500 lignes, 17,5 % de défauts) reste le
témoin de M6-B1 et n'est pas remplacé : c'est lui qui sert à mesurer la dérive de
`prod_3months.csv`.


## Reproduire

```bash
uv sync
python -m pytest tests/ -q                                      # tests sur drift_detection
jupyter notebook notebooks/M6-B1_binome_drift_analysis.ipynb    # Kernel → Restart & Run All
docker compose up -d                                            # stack M5 + dashboard drift
```

## Dashboard

Pyrenex Drift — suivi dérive v2, chargé automatiquement au `docker compose up` depuis
`grafana/provisioning/dashboards/`).

Trois panels live, tous branchés sur des métriques réellement exposées par la stack M5 :
probabilités prédites (médiane / p90), répartition des classes prédites (en ratio),
volume et taux d'erreur.

### Pourquoi le PSI et le F1 ne sont pas dans Grafana

Le PSI et le F1 sur 12 semaines sont des mesures **batch** : ils comparent deux
populations entières et supposent des labels mûrs, qu'aucun service de la stack M5
n'expose sur son `/metrics`. Les y mettre produirait des panels « No data » ; ils restent
donc des figures du notebook.

## Structure

```
data/                   reference_set · prod_3months · predictions_log (fournis)
notebooks/              analyse exécutable top-bottom
src/  drift_detection.py    PSI · KS · Chi² · drift_report
      recommendations.py    DriftDiagnosis → action proportionnée
tests/                  pytest sur drift_detection
grafana/provisioning/dashboards/   pyrenex_drift.json + dashboards.yml
drift_summary.md · diagnostic.md · note_recommandation.md   (générés)
```