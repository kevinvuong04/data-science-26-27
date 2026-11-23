# Task 2 – Model Baseline und Data Preparation

Notebook: `Task2_Data_Preparation.ipynb` (im ausgeführten Zustand, alle Result Cells sichtbar)

## Ordnerstruktur

| Pfad | Inhalt |
|---|---|
| `Data/pm25_train.csv`, `Data/pm25_test.csv` | Originaldaten aus der Aufgabenstellung (unverändert) |
| `Output/evaluation_log.csv` | Log-File aller Experimente (Name, Modul, Beschreibung, MAE/R² Train + Validierung, Delta zur Baseline) |
| `Output/test_evaluation.csv` | Baseline vs. finales Modell auf Validierung und Test |
| `Output/Task2_preprocessed_*.csv` | Vorbereitete Trainings-, Validierungs- und Testdaten für Task 3 |

CSV-Log-Dateien sind im deutschen Excel-Format gespeichert (`;` als Trenner, `,` als Dezimalzeichen). Die drei exportierten Datensätze verwenden das Standardformat (`,` / `.`), damit Task 3 sie direkt mit `pd.read_csv()` laden kann.

## Ausführen

Benötigte Pakete: `pandas`, `numpy`, `matplotlib`, `scikit-learn`, `ipykernel`

```bash
uv add pandas numpy matplotlib scikit-learn
uv add --dev ipykernel
```

Das Notebook aus diesem Ordner heraus starten, da die Pfade relativ sind (`Data/`, `Output/`).

## Ergebnis in Kürze

| | Validierung MAE | Validierung R² | Test MAE | Test R² |
|---|---|---|---|---|
| Baseline (alle Features, Median) | 1,27 | 0,32 | 2,21 | −0,13 |
| Finales Modell (M1 + M2 + M3 Winsor 95 + M5) | 0,78 | 0,74 | 1,63 | 0,42 |
