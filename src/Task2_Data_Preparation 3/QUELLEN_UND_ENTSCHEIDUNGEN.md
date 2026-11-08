# Quellen, Entscheidungen und Umsetzung des Feedbacks

## Verbindliche Grundlagen

Es werden ausschließlich lokale Kursmaterialien, das alte Projekt samt Feedback, das nachgereichte Task-1-Notebook und der neue fachliche Nutzerhinweis verwendet. Die Paketinstallation dient nur der Ausführung der Lab-Methoden. Zellenangaben sind 0-basiert, Pfade beziehen sich auf den ursprünglichen Projektordner.

- `Project_Case Study/Case Study Description.pdf`, S. 8: Task-2-Baseline mit minimaler Vorbereitung, lineare Regression für alle Varianten, Validation zur Auswahl, MAE/R², Overfitting-Prüfung durch Training/Validation, abschließender Test, drei vorbereitete CSVs, unveränderte Validierungs-/Testzeilen und Labels.
- S. 10–11: ausgeführtes Notebook, Daten und PowerPoint; zehn Minuten Vortrag mit Analyse und Learnings statt Codefolien.
- S. 15: ältere Tipps enthalten wiederholte Testauswertung und Feature Removal. Vorrang haben die aktuelle S. 8 und das Feedback: keine Feature Selection in Task 2, keine Auswahl anhand Test.
- S. 7: die Ein-Feature-Baseline gehört zu Task 1b, nicht zu Task 2.
- `Altes Projekt mit Feedback/Feedback.md`: vollständige Baseline, Validation-Split, Modularität, Einzelmodelle, Kombinationen, sprechende Namen, Deltas, Scaling-Erklärung und aussagekräftige Learnings.
- Nachgereicht: `Aufgabe_1_Data_Science_80_20_ohne_Holdout.ipynb`. Herkunft, Hash und Zellbezüge in `sources/TASK1_QUELLE.md`; der exakte Split ist in `sources/task1_split_manifest.csv` hinterlegt.
- Neuer Nutzerhinweis und bestätigte Umsetzung: PM2.5-Spitzen am 95. Perzentil winsorisieren; negative Schadstoffmesswerte entweder durch P5 gültiger nichtnegativer Trainingswerte ersetzen oder imputieren.

Dokumentinhalte sind fachliche Quellen; die konkrete Umsetzung folgt der Case Study und den ausdrücklichen Nutzeranweisungen. Die Feature Selection und der Refit aus Task 1 werden nicht automatisch in Task 2 übernommen.

## Anschluss an Task 1

Die Task-1-Exploration zeigt Missing Values, extreme `pm25_lowcost`-Werte und negative NO-Werte. Sie begründet die zu prüfenden Maßnahmen. Ihre vollständigen Datensatzstatistiken werden nicht als Vorbereitungsparameter benutzt: Quantile und Imputationswerte werden ausschließlich im Fit-Training berechnet.

Zelle 14 schließt 204 Zeilen ohne Label aus der ursprünglichen Trainingsdatei aus und splittet die verbleibenden 3.409 Zeilen mit `test_size=0.20, random_state=42`. Task 2 verwendet exakt dieselben **2.727 Trainings- und 682 Validierungszeilen in derselben Reihenfolge**. Das Notebook prüft diese Übereinstimmung gegen das unabhängig aus der Originalzelle erzeugte Manifest.

Keine nachträgliche Löschung von Validierungszeilen. Die 1.464 offiziellen Testzeilen bleiben vollständig; die 45 fehlenden Testlabels werden nicht ergänzt. MAE/R² verwenden nur die 1.419 beobachteten Labels.

Der frühere zeitliche Task-2-Split wird ersetzt. Frühere und neue Kennzahlen sind deshalb nicht als isolierter Winsorisierungseffekt vergleichbar. Innerhalb der neuen Revision haben alle Varianten denselben Split.

## Herkunft der Codebausteine

| Baustein | Lokale Vorlage | Anpassung |
|---|---|---|
| CSV-Import und Struktur | `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zellen 3–4 | PM2.5-Dateien, Komma-Trenner; Testimport erst nach Auswahl |
| Split und fehlende Trainingslabels | Task 1, Zelle 14; `Python Labs/04 - Missing Value Handling/Missing Value Handling_Demo.ipynb`, Zelle 20 | Exakt Task-1-Split; unabhängiger Indexabgleich |
| Diagnose | Task 1, Zellen 3–11; Pandas Exploration; Missing-Value-Demo Zellen 9–10 | Diagnose des Fit-Trainings, Quantiltabelle und Eingriffszahlen |
| Zeitrepräsentation | Altes Notebook Zelle 7, `pd.to_datetime` | Tage seit Trainingsbeginn als einfache numerische Repräsentation |
| Windzyklus | `Python Labs/06 - Transformation/TRANSFORMATION_SCRIPT.ipynb`, Zelle 16 | Sinus/Cosinus mit 360 statt 12; Grad aus Datenbereich abgeleitet |
| IQR | `Python Labs/XX - Building Pipelines for Data Preparation/Pipeline_Demo2_with_OutlierHandlingClass.ipynb`, Zelle 3 | Q1/Q3/1,5-IQR; **Korrektur:** Grenzen im Training speichern, nicht auf Validation/Test neu berechnen |
| Winsorisierung | Outlier-Vorlesung S. 9, neuer Nutzerhinweis; Pandas-Quantile/Masken aus Labs | `quantile(0.95)` und `.loc`-Ersetzung nur für `pm25_lowcost` oberhalb der Trainingsgrenze |
| Negative Messwerte | Neuer Nutzerhinweis; Rule-based-Funktion des alten Notebooks Zelle 7; Missing-Value-Lab | Negative Schadstoffwerte → P5 nichtnegativer Trainingswerte oder NaN; keine Regel auf Temperatur/Label |
| Imputation | `Python Labs/XX - Building Pipelines for Data Preparation/Pipeline_Demo.ipynb`, Zelle 8; Missing-Value-Demo | SimpleImputer, Mittelwert/Median; nur Trainings-Fit |
| OHE | Pipeline-Demo Zellen 18–22; Transformation-Script Zelle 3; altes Notebook Zelle 21 | Trainingsencoder, `drop='first'`, `handle_unknown='ignore'`, dichte Ausgabe |
| Power | Transformation-Script Zelle 41 | `PowerTransformer(standardize=False)`; Spalten vorab aus Trainingsschiefe bestimmt |
| Scaling | `Python Labs/07 - Scaling/SCALING.ipynb`, Z-score-Abschnitt; altes Notebook Zelle 24 | StandardScaler auf numerischen Features, getrenntes Fit/Transform |
| Regression und Bewertung | `Python Labs/10a - Regression Analysis- Part 1 Script and Lab/REGRESSION_ANALYSIS.ipynb`, Zellen 5, 9, 12; altes Notebook Zelle 29 | LinearRegression unverändert; MAE/R² für Training/Validation, Labelabdeckung |
| Grafiken, CSV, Log | Pandas-/Matplotlib-Labs; altes Notebook Zellen 29, 36, 38 | Alle Varianten, Deltas, Kombinationen, Parameter und drei Exporte |

## Präzise Definitionen der neuen Regeln

**Winsor-P95:** ausschließlich `pm25_lowcost`. Trainings-P95 aus beobachteten Werten nach optionaler Negativwertbehandlung. In den vorliegenden Trainingsdaten gibt es dort keine negativen Werte, daher ist P95 über alle Varianten identisch. Alle Werte > P95 werden auf P95 gesetzt; gültige kleinere Werte und NaNs bleiben erhalten. Keine Kappung von `pm25_target`, UFP, Black Carbon oder Gasen unter dieser Option.

**Neg-P5:** für `pm25_lowcost`, `no`, `no2`, `o3`, `co2`, `black_carbon`, `ufp`. Pro Feature P5 aus vorhandenen nichtnegativen Fit-Trainingswerten. Nur Werte < 0 werden ersetzt. Beim NO liegen 376 negative Trainingsmessungen vor; ungefiltertes P5 = −0,6, gültiges P5 = 0,1. Keine allgemeine untere Winsorisierung.

**Neg-Imputation:** gleiche Featureliste; Werte < 0 werden NaN und anschließend mit dem gewählten Trainingsmittelwert/-median imputiert. Bereits fehlende Werte bleiben ebenfalls Gegenstand der Imputation.

**IQR-Vergleich:** bisherige Methode auf sieben Schadstofffeatures. Nach optionaler Negativwertbehandlung Quartile fitten; außerhalb Q1−1,5·IQR bzw. Q3+1,5·IQR Werte zu NaN machen und imputieren. IQR und Winsor sind Alternativen, kein doppelter Eingriff auf dieselben Spitzen.

**Reihenfolge:** Repräsentation → negative Werte → Ausreißer → Imputation → Power → Scaling → OHE → lineare Regression. Das Label durchläuft keinen dieser Schritte.

## Kombinatorik und Auswahl

2 Imputationen × 3 Ausreißeroptionen × 3 Negativoptionen × 2 Power-Optionen × 2 Windoptionen × 2 Scaling-Optionen = **144 Konfigurationen**. Baseline und acht isolierte Änderungen werden separat gezeigt, außerdem alle Konfigurationen im Log und in sechs Diagrammgruppen.

Die Aus-Optionen sind Kontrollen. Ob eine Behandlung die Modellgüte verbessert, entscheidet allein die Validation. Die gewählte Variante enthält in dieser Ausführung beide neuen Regeln. Ausgewählt wird minimales Validation-MAE; Toleranz 1e-8, dann weniger optionale Schritte. Keine Auswahl anhand Test. Kein Refit auf Training plus Validation, damit Task 3 getrennte vorbereitete Datensätze erhält.

Sechs Baseline-nahe Kombinationen aus Winsor an/aus und Negativbehandlung keine/P5/Imputation sowie zwei Interaktionsdifferenzen machen das Zusammenspiel sichtbar. 72 direkte Scaling-Paare prüfen die erwartete OLS-Invarianz.

## Zusätzlicher Organisations- und Prüfcode

Neu geschrieben wurden nur notwendige Verbindungen und Kontrollen: Pfade, Konfigurationen, Schleifen, Zustandsverwaltung, verständliche Namen, Deltas, Auswahlregel, Logs, Ergebnistexte, Diagrammgruppen und CSV-Prüfungen. Die methodischen Bausteine stammen aus den oben zugeordneten Vorlagen bzw. dem neuen expliziten Tipp. Der Code wird nicht als vollständig wörtliche Lab-Kopie ausgegeben.

Das Notebook prüft Split-Identität, disjunkte Zeilen, Trainingsquantile, nichtnegative P5-Ersetzungen, unveränderte kleine PM2.5-Werte, obere Kappung, Negativ→NaN-Alternative, Batch-Unabhängigkeit von transform(), unveränderte Labels/Zeilen sowie Vorhersagereproduktion aus exportierten CSVs.

## Vorlesungsbezug

- `WI7-DataScience-WS_Lectures/4. Data Preparation/Lectures MV and Outlier Handling/2025_MissingValue_Handling.pdf`, S. 19–26: Imputationsalternativen und Risiken der Löschung; S. 12–18: MCAR/MAR/NMAR sind Annahmen.
- Im selben Ordner `2025_OutlierHandling.pdf`, S. 3–9: kontextabhängige Ausreißer, reale Variation, Imputation und Winsorisierung.
- `WI7-DataScience-WS_Lectures/4. Data Preparation/Lectures Transformation and Scaling/2025_DataPreparation1_Transformation.pdf`, S. 7, 14, 17, 25–27: OHE, Normalität, Schiefe, Power-Transformation und Grenzen.
- Im selben Ordner `2025_DataPreparation_FeatureScaling.pdf`, S. 4, 12–14: Skalen, Distanzmethoden, Standardisierung. Die OLS-Invarianz ist eine erklärte algebraische Folgerung und wird durch Paarvergleiche kontrolliert.

## Feedback umgesetzt

Vollständige Baseline; gleicher Split wie Task 1; modulare Fit-/Apply-Funktionen; isolierte Modelle und vollständige Kombinationen; keine Feature Selection; MAE/R² und Trainingsabstand; absolute/relative MAE-Gewinne; absolute R²-Differenz; sprechende Modellnamen; grafischer Vergleich aller Konfigurationen; Scaling-Erklärung; dokumentierte Eingriffe; Ergebnisinterpretation und Vortrag mit Sprecherhinweisen.

## Aussagegrenzen

Der offizielle Test war bereits in Task 1 und in der ersten Task-2-Version sichtbar. Er ist kein neuer unberührter Holdout. Die Überarbeitung folgt den neu bereitgestellten Quellen und bestätigten Regeln. Testergebnisse dürfen nicht zur weiteren Auswahl herangezogen werden. Zufällige zeitliche Mischung und wiederholte Nutzung derselben Validation begrenzen die Aussagekraft. Statistische Grenzwerte können echte Belastungsspitzen verändern. Kein Anspruch auf einen bestimmten Notenwert oder universelle Überlegenheit.
