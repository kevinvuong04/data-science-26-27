# Task 2 – Data Preparation

Aktuelle Abgabe: Neuaufbau mit kleinen Funktionen und expliziten Modulaufrufen nach dem alten Projekt. Keine Präsentation enthalten.

## Einstieg

1. ZIP vollständig entpacken und `Task2_Data_Preparation.ipynb` im enthaltenen Ordner öffnen.
2. Einen Python-Kernel mit den Paketen aus `requirements.txt` wählen. Bei Bedarf: `python -m pip install -r requirements.txt` (Jupyter/VS Code als Notebook-Oberfläche separat erforderlich).
3. Alle Zellen von oben nach unten ausführen. Rohdaten und Notebook müssen im selben Arbeitsordner liegen. Die Ergebnisdateien werden dabei neu geschrieben.

Das Notebook ist bereits vollständig ausgeführt. Alle 51 Codezellen wurden ohne Fehler ausgeführt.

## Herkunft und notwendige Anpassungen

Vor den Schritten stehen Originalauszüge, Dateiname, Zellnummer (0-basiert) und die jeweilige Anpassung. `ORIGINALSTELLEN.md` sammelt die überprüften Auszüge. Die vollständigen Original-Labs, Vorlesungen, Aufgabenstellung und das alte Projekt bleiben im ursprünglichen Projektordner; zur Ausführung werden sie nicht benötigt.

Die Funktionen für regelbasierte Vorbereitung, Imputation, Encoding, Scaling und Modellbewertung folgen dem alten Projekt. Missing-Value-, Transformation-, Scaling- und IQR-Lab liefern die jeweiligen Verfahren. Es handelt sich ausdrücklich nicht um unverändert kopierten Code: Spaltennamen und Datenfluss sind angepasst. P95/P5 setzen euren zusätzlichen Tipp um. Trainingsstatistiken werden weitergereicht, die gemeinsame Dummy-Referenz wird korrigiert und wenige Export-/Prüfzeilen sichern die Aufgabenregeln. Diese Ergänzungen sind gekennzeichnet.

## Umfang und Regeln

- Exakter Task-1-Split: fehlende Trainingslabels ausschließen, danach 80/20 mit `random_state=42`; 2.727 Training und 682 Validation. Alle 1.464 Testzeilen bleiben enthalten.
- Baseline mit sämtlichen Originalfeatures in numerischer Darstellung; Zeitspalten werden zusätzlich als eigener Schritt geprüft.
- P95 begrenzt nur `pm25_lowcost`. Negative Schadstoffwerte werden alternativ durch P5 aus gültigen Trainingsmessungen ersetzt oder als fehlend imputiert. Labels bleiben unverändert.
- Alle Grenzen, Imputationswerte und Transformer werden ausschließlich im Training gelernt.
- 18 vollständige Grundkombinationen; drei Transformationen als Einzeltests; sieben zusätzliche Transformationskombinationen auf der besten Grundvorbereitung. Zusätzlich 16 Power-Kombinationen und drei Kalenderalternativen: insgesamt 50 Varianten. Die stufenweise Auswahl ist keine Vollsuche über alle 144 Möglichkeiten und kann bessere Kombinationen übersehen.
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


## Zeitspalten: eigener Vorbereitungsschritt, Auswahl auf Validation

Die vollständige Baseline bleibt erhalten: alle 16 ursprünglichen Features, bei Bedarf numerisch dargestellt. Die Case Study nennt in „How To – Task 2“ ausdrücklich „Features to be removed (feature selection)“. Das ältere Projektfeedback („Noch keine Feature Selection in Task 2“) ist dazu widersprüchlich. Für diesen ausdrücklich beauftragten Vergleich wird der aktuelle Aufgabentext zugrunde gelegt; eine zusätzliche Freigabe wird nicht als Voraussetzung behauptet.

`timestamp` und sämtliche aus `year_month` erzeugten Dummy-Spalten werden als **eigener Schritt** entfernt. Begründung: Ein fortgeschriebener Zeittrend und bisher unbekannte Monate können die Übertragung auf spätere Zeiträume erschweren. Dies wird isoliert auf der Baseline und kombiniert mit zwei vorbereiteten Datenständen geprüft. Es werden keine Zeilen oder Zielwerte gelöscht.

Der Test ist aus früheren Untersuchungen bekannt und hat die Fragestellung mitgeprägt. Die Entscheidung über den neuen Schritt erfolgt trotzdem nur anhand der Validation, nicht anhand des früher beobachteten besseren Testwerts. Der Test wird dadurch nicht wieder zu einer unabhängigen Prüfmenge.

Die Datei `vergleich_zeitspalten.csv` enthält die drei direkten Paarvergleiche. Die zusätzliche Funktion folgt `feature_removal` aus dem alten Projekt (Zelle 18). Alle neuen Codezeilen sind für Einsteiger kommentiert.

## Ergebnis des Zeitspalten-Vergleichs

| Datenstand | Validation-MAE mit Zeitspalten | ohne Zeitspalten | Änderung durch Weglassen |
|---|---:|---:|---:|
| Vollständige Baseline | 1,293881 | 1,343886 | +0,050006 |
| Median + Neg-Imputation + P95 | 0,928889 | 0,923254 | −0,005635 |
| Median + Neg-P5 + P95 + Power | 0,841373 | 0,848958 | +0,007585 |

Ein kleinerer MAE ist besser. Das Entfernen hilft also leicht auf der Grundvorbereitung, aber nicht auf der Baseline oder der bislang besten Gesamtvariante. Das zeigt, weshalb Einzelmaßnahmen und Kombinationen getrennt geprüft werden müssen. Bei der Grundvorbereitung sinkt zwar der MAE, das Validation-R² verschlechtert sich von 0,6583 auf 0,6436; das festgelegte Auswahlkriterium bleibt MAE.

**Finale Auswahl aus 50 Varianten:** Median + Neg-P5 + Winsor-P95 + Power **mit Zeitfeatures**. Ihr Validation-MAE 0,841373 bleibt der kleinste untersuchte Wert. Der Ausschluss der Zeitspalten wurde durchgeführt und bewertet, aber nicht in die finalen CSVs übernommen. Damit wird nicht der bereits bekannte bessere Testwert der Variante ohne Zeitfeatures zum Auswahlkriterium gemacht.

Das ausgewählte Modell und die finalen Exporte bleiben deshalb inhaltlich unverändert: Test-MAE 2,193486 und Test-R² 0,038223. Die Einschränkung der schlechten zeitlichen Übertragbarkeit bleibt bestehen. Die drei neuen Kandidaten werden nicht zusätzlich auf dem Test zur Auswahl verglichen. Alle Zeilen und Zielwerte bleiben erhalten. Keine Präsentation erstellt.
