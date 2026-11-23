# Dokumentation Task 2 – Data Preparation

Stand: 23.11.2026

## Wo liegt was?

- **Notebook und Daten:** Branch `AI-Task2-DataPreparation`, Ordner `Task2_Data_Preparation/`
  - `Task2_Data_Preparation.ipynb` (ausgeführt)
  - `Data/` Originaldaten, `Output/` Log-File, Testauswertung, exportierte Datensätze für Task 3
- **Kontext:** dieser Branch (`AI`), Ordner `Kontext/`

## Vorgehen

1. Split wie in Task 1: `train_test_split(test_size=0.2, random_state=42)`. Zeilen ohne Label werden nur im Trainingsanteil entfernt.
2. Baseline mit **allen 14 numerischen Features** und Median-Imputation (Feedback Vorjahr: keine Baseline auf wenigen Features).
3. Sechs Module mit einheitlicher Schnittstelle `modul(train_part, other_parts, method)`:
   - M1 Regelkorrekturen (negative `no` → 0, kumulierter Zähler `rain_vol` → `rain_hourly`)
   - M2 Missing Values (Median, Mittelwert, zeitliche Interpolation, Zeilenlöschung nur im Training)
   - M3 Outlier Handling (Winsor 95/99, IQR, Pearson-optimiert, Log, Rang)
   - M4 Scaling (Standardisierung)
   - M5 Feature Engineering (Tageszeit sin/cos, Windrichtung sin/cos, `pm25_lowcost × relative_humidity`)
   - M6 Feature Selection (Korrelationsfilter, VIF)
4. Jedes Modul einzeln, danach Kombinationen und eine Kombinationsmatrix (M2 × M3 × M5). Insgesamt 36 Experimente plus Baseline im Log-File.
5. Auswahl nach Validierungs-MAE, danach einmalig auf dem Test. Anschließend Analyse der Lücke zwischen Validierung und Test.

## Interpretation des Prof-Hinweises (Pearson vs. Spearman)

- `pm25_lowcost`: Pearson 0,08 gegenüber Spearman 0,72. Der Zusammenhang ist eigentlich stark und monoton, wird aber von wenigen Extremwerten (bis 595 µg/m³) verdeckt.
- Über die Lücke zwischen beiden Korrelationen werden die Features für das Outlier Handling automatisch bestimmt: `pm25_lowcost`, `black_carbon`, `no`.
- Die Extremwerte treten nachts bei über 90 % Luftfeuchte auf (Feuchteeffekt optischer Sensoren). Daraus folgt das Interaktionsfeature in M5.
- Die Formulierung „bessere Raum" aus der Notiz ist nicht eindeutig. Abgedeckt sind die naheliegenden Lesarten: bessere Kappungsgrenze (IQR bzw. 95 % statt 99 %), Rangraum (Rang-Transformation = Spearman) und Log-Raum. Im Modell schneiden Kappung (95 % bzw. IQR) am besten ab. **Bitte im Coaching kurz nachfragen, was genau gemeint war.**

## Ergebnisse

| | Val MAE | Val R² | Test MAE | Test R² |
|---|---|---|---|---|
| Baseline | 1,27 | 0,32 | 2,21 | −0,13 |
| Final (M1 + M2 Zeilenlöschung + M3 Winsor 95 + alle M5-Features) | 0,78 | 0,74 | 1,63 | 0,42 |

Wichtigste Erkenntnisse:
- Outlier Handling bringt allein etwa −33 % MAE, die Feuchte-Interaktion danach weitere etwa −6 Prozentpunkte.
- Scaling: exakt 0 % Effekt (bei linearer Regression erwartbar und im Notebook erklärt).
- Die Top-4-Modelle liegen innerhalb von 0,01 MAE. Entscheidend ist die Kombination M1 + M3 + M5, nicht die Wahl der M2-Variante.
- Validierung vs. Test: Im Test fehlen Wetterdaten zu 67 % und `black_carbon` zu 49 %. Außerdem liegt der Test in einer anderen Saison (Nov/Dez). Die Stress-Validierung zeigt, dass etwa die Hälfte der Lücke auf die fehlenden Werte zurückgeht.

## Offene Punkte / Hinweise fürs Team

- Präsentationsfolien sind noch nicht erstellt. Das Notebook enthält dafür in jedem Abschnitt Interpretationen und Learnings.
- Die Learnings in Abschnitt 12.4 sind aus dieser Bearbeitung abgeleitet. Eigene Team-Erfahrungen sollten dort noch ergänzt werden.
- Empfehlung für Task 3: nichtlineare Verfahren testen und zusätzlich zeitlich validieren (z. B. Oktober als Validierung).
