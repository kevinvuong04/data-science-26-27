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

Split identisch mit Task 1b (Zeilen ohne Label vor dem Split entfernt, 80/20, `random_state=42`).

| | Validierung MAE | Validierung R² | Test MAE | Test R² |
|---|---|---|---|---|
| Baseline (alle Features, Median) | 1,34 | 0,36 | 2,22 | −0,15 |
| Finales Modell (M1 + Zeilenlöschung + IQR-Kappung + Feuchte-Interaktion) | 0,77 | 0,79 | 1,58 | 0,45 |
