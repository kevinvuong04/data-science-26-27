# Task 2 – Data Preparation

Aktuelle Abgabe: Neuaufbau mit kleinen Funktionen und expliziten Modulaufrufen nach dem alten Projekt. Keine Präsentation enthalten.

## Einstieg

1. ZIP vollständig entpacken und `Task2_Data_Preparation.ipynb` im enthaltenen Ordner öffnen.
2. Einen Python-Kernel mit den Paketen aus `requirements.txt` wählen. Bei Bedarf: `python -m pip install -r requirements.txt` (Jupyter/VS Code als Notebook-Oberfläche separat erforderlich).
3. Alle Zellen von oben nach unten ausführen. Rohdaten und Notebook müssen im selben Arbeitsordner liegen. Die Ergebnisdateien werden dabei neu geschrieben.

Das Notebook ist bereits vollständig ausgeführt. Alle 50 Codezellen wurden ohne Fehler ausgeführt.

## Herkunft und notwendige Anpassungen

Vor den Schritten stehen Originalauszüge, Dateiname, Zellnummer (0-basiert) und die jeweilige Anpassung. `ORIGINALSTELLEN.md` sammelt die überprüften Auszüge. Die vollständigen Original-Labs, Vorlesungen, Aufgabenstellung und das alte Projekt bleiben im ursprünglichen Projektordner; zur Ausführung werden sie nicht benötigt.

Die Funktionen für regelbasierte Vorbereitung, Imputation, Encoding, Scaling und Modellbewertung folgen dem alten Projekt. Missing-Value-, Transformation-, Scaling- und IQR-Lab liefern die jeweiligen Verfahren. Es handelt sich ausdrücklich nicht um unverändert kopierten Code: Spaltennamen und Datenfluss sind angepasst. P95/P5 setzen euren zusätzlichen Tipp um. Trainingsstatistiken werden weitergereicht, die gemeinsame Dummy-Referenz wird korrigiert und wenige Export-/Prüfzeilen sichern die Aufgabenregeln. Diese Ergänzungen sind gekennzeichnet.

## Umfang und Regeln

- Exakter Task-1-Split: fehlende Trainingslabels ausschließen, danach 80/20 mit `random_state=42`; 2.727 Training und 682 Validation. Alle 1.464 Testzeilen bleiben enthalten.
- Baseline mit sämtlichen Originalfeatures in numerischer Darstellung; keine Feature Selection.
- P95 begrenzt nur `pm25_lowcost`. Negative Schadstoffwerte werden alternativ durch P5 aus gültigen Trainingsmessungen ersetzt oder als fehlend imputiert. Labels bleiben unverändert.
- Alle Grenzen, Imputationswerte und Transformer werden ausschließlich im Training gelernt.
- 18 vollständige Grundkombinationen; drei Transformationen als Einzeltests; sieben zusätzliche Transformationskombinationen auf der besten Grundvorbereitung. Zusätzlich 16 Power-Kombinationen und drei Kalenderalternativen: insgesamt 47 Varianten. Die stufenweise Auswahl ist keine Vollsuche über alle 144 Möglichkeiten und kann bessere Kombinationen übersehen.
- Auswahl nur über Validation-MAE; Test erst danach im Notebook. Der Test ist aus früheren Arbeiten bekannt und kein frischer Holdout.

## Dateien

- `Task2_Data_Preparation.ipynb`: vollständiger Code, Quellen, Ausgaben und Interpretation.
- `pm25_train.csv`, `pm25_test.csv`: unveränderte bereitgestellte Rohdaten.
- `pm25_preprocessed_train.csv`, `pm25_preprocessed_validation.csv`, `pm25_preprocessed_test.csv`: finale Daten inklusive unveränderter Zielwerte.
- `modellvergleich.csv`, `grundkombinationen.csv`, `baseline_und_auswahl.csv`, `test_ergebnis.csv`: Kennzahlen und Vergleiche.
- Drei PNG-Dateien: Trainingsdiagnose, MAE-Vergleich und relative MAE-Verbesserungen.
- `zeilen_*.csv` und `ausgeschlossene_trainingszeilen.csv`: Zeilenherkunft und ausgeschlossene fehlende Trainingslabels.
- `trainingsgrenzen_p5.csv`, `trainingsgrenze_p95.csv`, `trainingsmediane_final.csv`, `power_parameter.csv`: nachvollziehbare Trainingsparameter.
- `test_vorhersagen.csv`: Vorhersagen mit Zeilenherkunft.


## Aktueller Stand nach Ursachenprüfung

Ausgewählt: **Median + Neg-P5 + Winsor-P95 + Power**. Validation-MAE **0,8414**, R² **0,7282**; Test-MAE **2,1935**, R² **0,0382**. Der Test bleibt deutlich schwächer. `URSACHENANALYSE.md` erläutert die belegten Unterschiede und Grenzen. `diagnose_*.csv` enthalten Fehlquoten, Residuen, Gruppenfehler und Modellanteile. Dieselbe Analyse steht am Ende des ausgeführten Notebooks.

Die Kalenderalternative basiert auf dem alten Projekt und dem Transformations-Lab, wird wegen etwas schlechterem Validation-MAE nicht übernommen. Alle zusätzlichen Varianten werden vor der finalen Testanwendung bewertet. Kein weiterer Testvergleich zur Modellauswahl.
