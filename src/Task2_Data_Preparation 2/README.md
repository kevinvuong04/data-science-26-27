# Task 2 · Data Preparation

Vollständig ausgeführte, modulare Umsetzung für die bereitgestellte PM2.5-Case-Study. Grundlage: ausschließlich lokale Aufgabenstellung, Labs, Vorlesungen sowie altes Projekt und Feedback.

## Öffnen

1. `Task2_Data_Preparation.ipynb` öffnen. Alle Ergebniszellen sind bereits enthalten.
2. `Task2_Data_Preparation.pptx` enthält zehn Folien mit Ergebnisinterpretation und Sprecherhinweisen für ungefähr zehn Minuten.
3. `results/LEARNINGS.md` enthält die berechneten Ergebnisse und Grenzen.
4. `QUELLEN_UND_ENTSCHEIDUNGEN.md` zeigt die Herkunft des Codes, bewusste Anpassungen und die Umsetzung jedes Feedbackpunkts.

## Neu ausführen

Python 3.12 verwenden. Im Projekt wurde eine lokale `.venv` eingerichtet; in VS Code deren Python als Notebook-Kernel auswählen. Für eine neue Umgebung im entpackten Abgabeordner:

```sh
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Anschließend im Notebook **Run All**. Die Ausführung funktioniert mit dem Abgabeordner als Arbeitsordner oder von dessen übergeordnetem Projektordner. Das Notebook schreibt seine Resultate und Grafiken erneut in `results/` bzw. `figures/`. Rohdaten werden nicht verändert. Die PowerPoint ist der Stand der gelieferten Ausführung; nach bewussten Änderungen am Notebook müssen ihre Zahlen entsprechend aktualisiert werden.

`requirements.txt` protokolliert die tatsächlich verwendeten Paketversionen. Die Analyselogik nutzt Pandas, NumPy, Matplotlib und scikit-learn aus den bereitgestellten Labs. Jupyter-Pakete dienen der Notebook-Ausführung. Fachlich wurden keine Onlinequellen verwendet.

## Ergebnis dieser Ausführung

| Kennzahl | Baseline | Ausgewählte Vorbereitung |
|---|---:|---:|
| Validation-MAE, µg/m³ | 1,5017 | 1,0988 |
| Validation-R² | −0,0962 | 0,3126 |
| MAE-Verbesserung | – | 26,83 % |

Ausgewählt: **Mittelwert + IQR + Power + Windzyklus**. Standardisierung bringt bei dieser unregularisierten linearen Regression praktisch keine Änderung. Der Median ist nicht automatisch besser als der Mittelwert; die Kombinationen werden vollständig geprüft.

**Der finale Test ist schwach:** MAE **2,4881 µg/m³**, R² **−0,5329**. Ein negativer Test-R² bedeutet einen höheren quadratischen Fehler als die konstante Vorhersage des Mittelwerts der bewerteten Testlabels. Die Verbesserung auf Validation ist daher kein Beleg für zuverlässige Übertragbarkeit auf spätere Monate. Neue Monatskategorien, zeitliche Extrapolation und veränderte Messbedingungen sind mögliche Erklärungen, keine nachgewiesenen Ursachen. Es erfolgte keine nachträgliche Modellanpassung anhand des Tests.

45 Testlabels fehlen. MAE/R² beziehen sich auf **1.419 beobachtete Labels**; alle **1.464 Testzeilen** bleiben im Export. Es werden keine Zielwerte imputiert.

## Inhalt und Übergabe für Task 3

- `data/`: unveränderte Kopien der beiden bereitgestellten CSV-Dateien.
- `results/pm25_preprocessed_train.csv`: **2.686** Fit-Trainingszeilen.
- `results/pm25_preprocessed_validation.csv`: **723** unveränderte Validierungszeilen.
- `results/pm25_preprocessed_test.csv`: **1.464** unveränderte Testzeilen.
- Alle drei Dateien: **20 vorbereitete Features** und `pm25_target` in Originaleinheiten. Zeilenherkunft in `row_manifest_*.csv`, kein zusätzlicher ID-Prädiktor.
- `evaluation_log.csv`: alle **32** Konfigurationen, Training-/Validation-MAE und R², Generalisierungslücke, absolute/relative MAE-Verbesserung und R²-Differenz.
- `single_steps.csv`, `combination_effect.csv`, `scaling_pairs.csv`: isolierte und kombinierte Effekte.
- `selected_model.json`: vor dem Test festgelegte Auswahlregel und Konfiguration.
- `selected_preparation_audit.csv`, `selected_iqr_bounds.csv`, `selected_power_parameters.csv`: Eingriffe und ausgewählte Parameter.
- `excluded_training_rows.csv`: 204 frühe Trainingszeilen ohne Label, deshalb nicht für den Fit verwendet.
- `final_test_metrics.csv`, `test_predictions.csv`: einmalige Endbewertung und Vorhersagen für alle Testzeilen.
- `figures/`: Diagramme aus dem Notebook, einschließlich Gesamtvergleich aller Varianten.

Für Task 3 nur mit dem vorbereiteten Training fitten und Validation zur Auswahl nutzen. Kein erneutes Fitten von Vorbereitungsparametern auf Validation/Test. Fehlende Testlabels bei der Metrikberechnung maskieren, niemals ersetzen. Die Validation wird nicht dem Training zugeschlagen.

## Vor der tatsächlichen Abgabe

- Die aktuelle Task-1-Zusammenfassung war nicht vorhanden. Die vorläufigen Trainingsdiagnosen und Maßnahmen mit dem Team abgleichen.
- Die Gradinterpretation der Windrichtung ist eine aus dem Wertebereich abgeleitete Annahme. Sensorfehler werden nicht aus statistischen Ausreißern allein behauptet.
- Die aktuelle Aufgabenfolie hat Vorrang vor widersprüchlichen älteren Tipps: Validation zur Auswahl, Test erst am Ende, keine Feature Selection in Task 2.
- Teamnamen und Vortragsaufteilung selbst ergänzen; aus dem alten Projekt werden keine aktuellen Teamzugehörigkeiten unterstellt.
- Die schlechten Testergebnisse im Vortrag offen diskutieren. Eine bestimmte Punktzahl kann daraus nicht zugesichert werden.
