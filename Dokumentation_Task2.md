# Dokumentation Task 2 – Data Preparation

Stand: 23.11.2026 (überarbeitet)

## Wo liegt was?

- **Notebook und Daten:** Branch `AI-Task2-DataPreparation`, Ordner `Task2_Data_Preparation/`
  - `Task2_Data_Preparation.ipynb` (ausgeführt)
  - `Data/` Originaldaten, `Output/` Log-File, Testauswertung, exportierte Datensätze für Task 3
- **Kontext:** dieser Branch (`AI`), Ordner `Kontext/`

## Vorgehen (Stand nach Review)

1. Split exakt wie Task 1b: Zeilen ohne Label vor dem Split entfernen, dann 80/20 mit `random_state=42` (2.727 / 682 Zeilen). Kontrolle: Baseline-MAE 1,3417 = Task-1b-Modell mit allen Features.
2. Baseline mit allen 14 numerischen Features und Median-Imputation.
3. Sechs Module mit einheitlicher Schnittstelle (fit auf Training, Anwendung auf Validierung/Test):
   - M1 Regelkorrekturen (alle Konzentrationen >= 0, `rain_vol` -> `rain_hourly`)
   - M2 Missing Values (Median, Mittelwert, Regressionsimputation aus Lab 04, Zeilenlöschung nur im Training)
   - M3 Outlier Handling (Winsor 95/99, IQR, Pearson-optimiert, Log, Rang)
   - M4 Scaling, M5 Feature Engineering, M6 Feature Selection
4. Einzelmodule, Kombinationen, Kombinationsmatrix (36 Experimente + Baseline im Log).
5. Kontrollversuche (Abschnitt 6.1) für alle selbst gesetzten Schwellen.
6. Annahmen-Register (Abschnitt 12.2): Jede Annahme mit Beleg im Notebook.

## Änderungen nach dem Review

- Split war nicht identisch mit Task 1 -> korrigiert.
- Zeitliche Interpolation entfernt (nicht aus den Labs, mischt Validierungswerte ins Training) -> Regressionsimputation aus Lab 04.
- Stress-Validierung entfernt (keine saubere Ursachentrennung) -> Testanalyse nur beschreibend.
- Formulierungen vorsichtiger: Pearson/Spearman-Lücke = Hinweis, Feuchteeffekt = Hypothese.
- Negative Werte: Regel für alle Konzentrationen (142 negative o3-Werte im Test).
- Quellentabelle je Modul; eigene Ergänzungen gekennzeichnet.

## Ergebnisse

| | Val MAE | Val R² | Test MAE | Test R² |
|---|---|---|---|---|
| Baseline | 1,34 | 0,36 | 2,22 | −0,15 |
| Final (M1 + Zeilenlöschung + IQR-Kappung + Feuchte-Interaktion) | 0,77 | 0,79 | 1,58 | 0,45 |

Kontrollversuche: Lücken-Schwelle 0,15 besser als 0,10/0,25; Kappung nur oben besser als beidseitig; ohne Korrelationsfilter am besten.

## Offene Punkte / Hinweise fürs Team

- Präsentationsfolien sind noch nicht erstellt. Das Notebook enthält dafür in jedem Abschnitt Interpretationen und Learnings.
- Die Learnings in Abschnitt 12.4 sind aus dieser Bearbeitung abgeleitet. Eigene Team-Erfahrungen sollten dort noch ergänzt werden.
- Empfehlung für Task 3: nichtlineare Verfahren testen und zusätzlich zeitlich validieren (z. B. Oktober als Validierung).
