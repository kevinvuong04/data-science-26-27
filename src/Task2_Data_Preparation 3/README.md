# Task 2 · Data Preparation · aktualisierte Fassung

Vollständig ausgeführte Revision mit dem exakten Task-1-Split und den besprochenen P95-/P5-Regeln. Das Abgabepaket enthält genau ein Task-2-Notebook und eine PowerPoint.

## Einstieg

- `Task2_Data_Preparation.ipynb`: kompletter modularer Code, Erläuterungen und ausgeführte Ergebniszellen.
- `Task2_Data_Preparation.pptx`: zehn aktualisierte Folien mit editierbaren Tabellen/Diagrammen und Sprecherhinweisen für ungefähr zehn Minuten.
- `results/LEARNINGS.md`: berechnete Ergebnisse und Einordnung.
- `QUELLEN_UND_ENTSCHEIDUNGEN.md`: genaue Zuordnung zu Labs, Vorlesungen, Task 1 und Feedback.
- `sources/TASK1_QUELLE.md` und `task1_split_manifest.csv`: Herkunft der neuen Quelle und exakter Splitabgleich.

## Was geändert wurde

1. Task-1-Split übernommen: 204 fehlende Trainingslabels vor dem 80/20-Split ausschließen, `random_state=42`; **2.727 Training / 682 Validation** in identischer Reihenfolge.
2. **Winsor-P95**: `pm25_lowcost` oberhalb des Trainings-P95 (11.3155 µg/m³) auf diese Grenze setzen. Keine Labelkappung.
3. **Neg-P5**: negative Schadstoffwerte durch P5 gültiger nichtnegativer Trainingswerte ersetzen. Für NO: 0.100; ungefiltertes P5 wäre −0,6. **Alternative:** negative Werte zu NaN und anschließend imputieren.
4. **144 Varianten** inklusive Baseline, acht Einzelmaßnahmen, aller Kombinationen und IQR als Vergleich. IQR und Winsor sind Alternativen.
5. Notebook, drei CSVs, Parameter, Grafiken, Learnings und Präsentation aus dem neuen Lauf aktualisiert.

## Ergebnisse

| Bewertung | MAE (µg/m³) | R² |
|---|---:|---:|
| Baseline auf Validation | 1.2939 | 0.4117 |
| Ausgewählt auf Validation | 0.8414 | 0.7282 |
| Ausgewählt auf offiziellem Test | 2.1935 | 0.0382 |

Ausgewählt: **Median + Neg-P5 + Winsor-P95 + Power**. Validation-MAE-Gewinn: **34.97%** gegenüber der Baseline auf demselben Split. Die Testgüte bleibt deutlich schwächer; R² nahe null zeigt geringe erklärte Variation auf den späteren Testmessungen.

Die früheren Task-2-Kennzahlen basieren auf einem anderen, zeitlichen Split. Ein Vergleich alt/neu isoliert deshalb nicht den Effekt der Winsorisierung. Der offizielle Test war bereits bekannt und ist kein neuer unberührter Holdout. Die Auswahl erfolgte nur auf Validation; kein Refit auf Training plus Validation.

## Übergabe für Task 3

- `results/pm25_preprocessed_train.csv`: **2.727** Zeilen.
- `results/pm25_preprocessed_validation.csv`: **682** Zeilen.
- `results/pm25_preprocessed_test.csv`: **1.464** Zeilen.
- Alle drei: **19 vorbereitete Features** plus unverändertes `pm25_target` in Originaleinheiten.
- Testmetriken verwenden **1.419 beobachtete Labels**; 45 fehlende Labels bleiben fehlend, alle Testzeilen erhalten Vorhersagen.
- `row_manifest_*.csv`: Originalzeilennummer und Zeitstempel getrennt von den Modellfeatures.
- `evaluation_log.csv`: alle 144 Konfigurationen mit Training-/Validation-MAE und R², Deltas und Generalisierungslücke.
- `single_steps.csv`, `combination_effect.csv`, `combination_interactions.csv`, `scaling_pairs.csv`: isolierte Effekte, Kombinationen und 72 Scaling-Paare.
- `selected_model.json`, `selected_preparation_audit.csv`, `training_p05_bounds.csv`, `training_p95_bound.csv`, `selected_imputation_values.csv` und gegebenenfalls Power-/IQR-Parameter: Auswahl und Eingriffe.
- `data/`: unveränderte Kopien der beiden bereitgestellten CSVs.
- `figures/`: alle Ergebnisse, einschließlich sechs lesbarer Gruppen mit je 24 Varianten.

Nur auf dem Training fitten. Vorbereitungsparameter nicht auf Validation/Test neu lernen. Fehlende Labels ausschließlich bei Metrikberechnung maskieren, nie imputieren. Die ausgewählte Power-Transformation und übrige Vorbereitung sind in den CSVs bereits enthalten.

## Neu ausführen

Python 3.12 verwenden. In VS Code die bestehende Projekt-`.venv` als Notebook-Kernel wählen. Für eine neue Umgebung im entpackten Ordner:

```sh
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Danach **Run All**. Ausführung im Abgabeordner oder im übergeordneten Projektordner unterstützt. Rohdaten werden nicht verändert. Ergebnisdateien werden neu geschrieben. Bei bewusst geänderten Konfigurationen Ergebnisse in einem frischen Ausgabeordner erzeugen, damit keine alten optionalen Parameterdateien liegen bleiben. Die PowerPoint dokumentiert den gelieferten Lauf und muss nach fachlichen Codeänderungen ebenfalls aktualisiert werden.

## Vor der Abgabe

Die Task-1-Quelle ist jetzt berücksichtigt; der vormals offene Quellenabgleich entfällt. Teamnamen und Vortragsaufteilung selbst ergänzen. Die Interpretation der Windrichtung in Grad bleibt eine aus dem Wertebereich abgeleitete Annahme. Fachliche Grenzen der Quantilkorrektur, zeitliche Abhängigkeit und die bereits bekannte Testbewertung offen diskutieren.
