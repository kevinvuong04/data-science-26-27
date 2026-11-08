# Quellen und bewusste Anpassungen

Es wurden ausschließlich die im Projekt bereitgestellten fachlichen Quellen verwendet. Paketinstallation dient der Ausführung der vorhandenen Lab-Methoden, nicht der Einführung neuer fachlicher Methoden. Zellenangaben sind **0-basiert** gemäß Notebook-JSON. Pfade beziehen sich auf den ursprünglichen Projektordner.

## Maßgebliche Aufgabenstellung

- `Project_Case Study/Case Study Description.pdf`, S. 8: lineare Regression, minimal vorbereitete Baseline, Validation für jeden Schritt, Training/Validation-Vergleich, finaler Test, keine Zeilenlöschung oder Labeländerung in Validation/Test, MAE und R², drei CSV-Exporte, Learnings.
- S. 10–11: zehn Minuten Präsentation, ein ausgeführtes Notebook und eine PowerPoint pro Vortrag, Daten beilegen, Interpretation statt Code auf Folien.
- S. 15: ältere Tipps nennen wiederholte Testauswertung und Feature Removal. Diese widersprechen der aktuellen S. 8 bzw. dem Feedback. Priorität: aktuelle Task-2-Folie und ausdrückliches Feedback. Test erst nach Auswahl, keine Feature Selection.
- S. 7: Ein-Feature-Baseline gehört zu Task 1b, nicht zu Task 2.

## Codeherkunft

| Baustein | Konkrete Vorlage | Übernahme / Anpassung |
|---|---|---|
| Imports, CSV-Import, grobe Struktur | `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zellen 3–4 | PM2.5-Dateien, Komma-Trenner; Testimport nach hinten verschoben |
| 80/20-Split | Altes Notebook Zelle 4; `Python Labs/XX - Building Pipelines for Data Preparation/Pipeline_Demo2_with_OutlierHandlingClass.ipynb`, Zelle 7 | `shuffle=False` für zeitlich geordnete Messungen; keine zufällige Durchmischung |
| Fehlende Trainingslabels | `Python Labs/04 - Missing Value Handling/Missing Value Handling_Demo.ipynb`, Zelle 20 | Ausschluss nur im Fit-Training; Validation/Test vollständig |
| describe, Missing Counts, Histogramme | Missing-Value-Demo Zellen 9–10; `Python Labs/06 - Transformation/TRANSFORMATION_SCRIPT.ipynb`, Zellen 25, 30–31; Pandas Exploration | Trainingsdiagnose und Schiefe, ergänzt um Berichtstabelle |
| Datumsparsing | Altes Notebook Zelle 7 | `pd.to_datetime`; zusätzliche einfache Datumsdifferenz in Tagen als numerische Darstellung statt Bike-Rental-Wochentag/Saison |
| Zyklische Windrichtung | `TRANSFORMATION_SCRIPT.ipynb`, Zelle 16 | Formel mit 360 statt 12; Anwendung auf Wind statt Monat; Grad aus Wertebereich angenommen |
| IQR-Ausreißer → NaN | `Pipeline_Demo2_with_OutlierHandlingClass.ipynb`, Zelle 3 | Gleiches Q1/Q3/1,5-IQR-Verfahren. **Korrigiert:** Grenzen im Fit speichern, auf andere Splits nur anwenden. Funktionale Module statt neuer Transformer-Klasse |
| Imputation | `Python Labs/XX - Building Pipelines for Data Preparation/Pipeline_Demo.ipynb`, Zelle 8; Missing-Value-Demo | `SimpleImputer`, Mittelwert bzw. Median; Fit nur Training |
| OHE | `Pipeline_Demo.ipynb`, Zellen 18–22; `TRANSFORMATION_SCRIPT.ipynb`, Zelle 3; altes Notebook Zelle 21 | Trainierter Encoder statt separat erzeugter Dummy-Layouts; `drop='first'`, `handle_unknown='ignore'`; dichte Ausgabe für DataFrame |
| PowerTransformer | `TRANSFORMATION_SCRIPT.ipynb`, Zelle 41 | `PowerTransformer(standardize=False)` auf vorab benannte schiefe Messgrößen; nur Trainings-Fit |
| StandardScaler | `Python Labs/07 - Scaling/SCALING.ipynb`, Abschnitt Z-score standardization; altes Notebook Zelle 24 | Nur numerische Features, Fit/Transform getrennt, OHE-Dummies nicht skaliert |
| Regression, MAE, R² | `Python Labs/10a - Regression Analysis- Part 1 Script and Lab/REGRESSION_ANALYSIS.ipynb`, Zellen 5, 9, 12; altes Notebook Zelle 29 | Gleiches LinearRegression-Modell für alle Versuche; zusätzlich Training und Labelabdeckung |
| Modulidee, Fit/Apply-Trennung | Pipeline-Demos und Funktionen des alten Notebooks | Kleine Funktionen und expliziter Zustand; kompletter Code im einen Abgabe-Notebook sichtbar |
| CSV und Ergebnislog | Altes Notebook Zellen 29, 36, 38; Pandas-Labs | Drei vorbereitete Splits, CSV-Log aller Varianten und separat finaler Test |
| Grafiken | Pandas-/Matplotlib-Labs, alte Vorher-/Nachher-Plots | Ergebnisvergleich, Deltas, Datenqualität, Training/Validation |

## Zusätzlich geschriebener Organisationscode

Kein neuer Modellalgorithmus wurde eingeführt. Damit die Fehler des alten Projekts behoben werden, sind folgende Verbindungen und Kontrollen nötig und bewusst neu formuliert:

- Portabler Dateipfad mit `Path`, Ausgabeordner, lesbare Konfigurationen und Modellnamen.
- Fünf verschachtelte Schleifen für die vollständigen 32 Kombinationen und Wiederverwendung bereits gerechneter Einzelvarianten.
- Einheitlicher Zustand für trainierte Imputation, Encoder, IQR-Grenzen, Power-Parameter und Scaling.
- Auswahl nach Validation-MAE, numerische Gleichstandstoleranz 1e-8 und einfachere Variante bei Gleichstand.
- Deltas, Kombinationsvergleich, Scaling-Paare und aus Ergebnissen generierte Learnings.
- Integritätsprüfungen für Labelwerte, Zeilen, Indizes, endliche Features und erneutes Einlesen der CSVs.
- Maskierung fehlender Labels **nur für die Metrik**, ohne Zeilen aus Validation/Test zu löschen.
- Export von Zeilenmanifesten, Ausschlussprotokoll, Parametern und Präsentationsdaten.

Diese Ergänzungen verbinden Lab-Code und altes Projekt für die neue Aufgabenstellung. Sie sind nicht als wörtlich übernommener Lab-Code ausgegeben.

## Fachliche Grundlage aus den Vorlesungen

- `WI7-DataScience-WS_Lectures/4. Data Preparation/Lectures MV and Outlier Handling/2025_MissingValue_Handling.pdf`, S. 19–26: unterschiedliche Imputationen und Risiken der Löschung; S. 12–18: MCAR/MAR/NMAR sind Annahmen, nicht allein anhand von NaN-Zahlen beweisbar.
- Im selben Ordner `2025_OutlierHandling.pdf`, S. 3–9: Ausreißer sind kontextabhängig, können reale Variation sein; Ersetzen/Imputation als Alternative zur Zeilenlöschung.
- `WI7-DataScience-WS_Lectures/4. Data Preparation/Lectures Transformation and Scaling/2025_DataPreparation1_Transformation.pdf`, S. 7, 14, 17, 25–27: OHE, Normalitätsrestriktion, Schiefe, Power-Transformation und deren Grenzen.
- Im selben Ordner `2025_DataPreparation_FeatureScaling.pdf`, S. 4, 12–14: Skalen und Distanzmethoden, Standardisierung und Formtreue. Die OLS-Invarianz ist die im Notebook erklärte algebraische Folgerung, zusätzlich durch alle 16 Scaling-Paare überprüft.
- `Altes Projekt mit Feedback/Feedback.md`: alle dort genannten inhaltlichen Korrekturen sind unten zugeordnet.

## Feedback vollständig zugeordnet

| Feedback | Umsetzung |
|---|---|
| Baseline nur drei Features | Alle 16 Originalfeatures technisch repräsentiert |
| Baseline analysieren | Trainingslabel-Verteilung, MAE-Einheit, R²-Einordnung und Qualitätsdiagnose |
| Validation Data-Split | Zeitlicher 80/20-Split vor dem Fit, Test separat |
| Zwischenschritte bewerten | Baseline und fünf isolierte Änderungen |
| Modularisierung | Repräsentation, IQR-Fit/Apply, Vorbereitung Fit/Apply, Bewertung |
| Noch keine Feature Selection | Keine Feature-Entfernung nach Korrelation, Importance oder Validierung |
| Scaling erklären | Algebraische Erklärung und 16 direkte Paarvergleiche |
| Grafischer Gesamtvergleich | Alle 32 Varianten; Training/Validation und relative Gewinne |
| Delta zur Baseline | Absolute und relative MAE-Verbesserung sowie absolute R²-Differenz |
| Sprechende Modellnamen | Mittelwert/Median + aktive Maßnahmen |
| Kombinationen erproben | Vollständige 2×2×2×2×2-Kombinatorik; Median/IQR-Interaktion explizit |
| Oberflächliche Learnings / keine Coaching-Folien | Ergebnisbezogene Learnings, Grenzen, Präsentation mit Sprecherhinweisen |

## Bewusste Grenzen

Ein Task-1-Bericht des aktuellen Projekts fehlt. Die Kurzdiagnose ist eine eigenständige Ableitung aus Training und muss fachlich mit dem Team abgeglichen werden. Sensorfehler, Einheiten nicht ausdrücklich beschriebener Features und Missing-Mechanismen werden nicht erfunden. Das Modell wird nach dem Test nicht erneut optimiert. Keine Zusage einer bestimmten Note.
