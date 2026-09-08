# Synthèse de détection de dérive — M6-B1

Référence : `reference_set.csv` (1500 lignes) · Production : `prod_3months.csv` (3000 lignes)
Features testées : 14 (8 numériques, 6 catégorielles) — exhaustif.
Seuils PSI (repères conventionnels, pas des frontières) : < 0.1 stable · 0.1-0.25 suspect · > 0.25 dérive.
Tests multiples corrigés par Holm-Bonferroni à alpha = 0.05.
Calculs : `src/drift_detection.py`, couvert par `tests/`.

| feature | type | PSI | p-value | signif. (Holm) | verdict | commentaire |
|---|---|---|---|---|---|---|
| int_rate | numerique | 0.444 | 4.63e-65 | True | dérive | signaux concordants, moyenne +22.9 % |
| revol_util | numerique | 0.187 | 3.82e-20 | True | suspect | signaux concordants, moyenne +16.6 % |
| annual_inc | numerique | 0.067 | 2.28e-09 | True | stable | significativité portée par la taille d'échantillon, ampleur faible |
| grade | categorielle | 0.043 | 3.65e-07 | True | stable | significativité portée par la taille d'échantillon, ampleur faible |
| installment | numerique | 0.015 | 6.63e-01 | False | stable | aucun signal |
| dti | numerique | 0.011 | 1.10e-01 | False | stable | aucun signal |
| emp_length | categorielle | 0.01 | 4.92e-01 | False | stable | aucun signal |
| purpose | categorielle | 0.009 | 1.99e-01 | False | stable | aucun signal |
| fico_range_low | numerique | 0.007 | 4.89e-01 | False | stable | aucun signal |
| loan_amnt | numerique | 0.003 | 9.89e-01 | False | stable | aucun signal |
| home_ownership | categorielle | 0.002 | 6.31e-01 | False | stable | aucun signal |
| delinq_2yrs | numerique | 0.002 | 8.88e-01 | False | stable | aucun signal |
| verification_status | categorielle | 0.001 | 5.86e-01 | False | stable | aucun signal |
| term | categorielle | 0.001 | 3.16e-01 | False | stable | aucun signal |

## Synthèse globale

1 feature(s) au-delà de PSI 0.25, 1 en zone suspecte.
Features les plus déplacées : `int_rate`, `revol_util`.

_(2-3 lignes d'interprétation métier à rédiger ici.)_

> Un chiffre n'est pas un verdict, un verdict n'est pas un diagnostic.
