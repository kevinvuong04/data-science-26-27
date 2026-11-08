# Originalstellen und notwendige Anpassungen

Dies sind verifizierte Ausschnitte der lokalen Originaldateien. Zellen sind 0-basiert. Der fachliche Code steht vollständig im Notebook; dieses Dokument ist eine Lesehilfe.

## 1. Imports
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 3.

```python
import pandas as pd 
import numpy as np
```

Anpassung: Unbenutzte Importe entfallen; PowerTransformer aus dem Transformations-Lab kommt hinzu.

## 2. Daten und Split
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 4.

```python
train_df, val_df = train_test_split(train_df, test_size=0.2, random_state=42)
```

Anpassung: PM2.5-Datei statt Bike Rental; der Testimport folgt erst nach der Auswahl. Fehlende Trainingslabels vor dem Split ausschließen wie Task 1, Zelle 14. Sonst exakt derselbe Split.

## 3. Trainingsdiagnose
Quelle: `Python Labs/04 - Missing Value Handling/Missing Value Handling_Demo.ipynb`, Zelle 9.

```python
#show descriptive statistics
df.describe()
```

Anpassung: Nur den Fit-Trainingsteil untersuchen. Die Task-1-Exploration motiviert die Schritte, ihre Gesamtstatistiken werden aber nicht für Imputation oder Quantile übernommen.

## 4. Technische Datumsumwandlung
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 7.

```python
def rule_based(df):

    # Kopie erzeugen, damit wir den ursprünglichen Datensatz nicht verändern
    df = df.copy()
```

Anpassung: Die Funktionsform und pd.to_datetime bleiben erhalten. Statt Wochentag/Saison wird timestamp als Tage seit Trainingsbeginn dargestellt. Diese Datumsdifferenz ist notwendiger Anpassungscode, keine wörtliche Lab-Stelle.

## 5. One-Hot-Encoding
Quelle: `Python Labs/06 - Transformation/TRANSFORMATION_SCRIPT.ipynb`, Zelle 3.

```python
#one hot encoding of color, leave out one category to avoid dummy variable trap
color_oh_encoded = pd.get_dummies(df['Color'], prefix='Color', drop_first=True)
color_oh_encoded
```

Anpassung: Color wird year_month. Ergänzend wird die Funktion one_hot_and_align aus dem alten Projekt, Zelle 21, übernommen und um die gemeinsame Referenzkategorie korrigiert.

## 6. Fehlende Werte
Quelle: `Python Labs/04 - Missing Value Handling/Missing Value Handling_Demo.ipynb`, Zelle 32.

```python
df['random'].fillna(df['random'].mean(), inplace=True)
```

Anpassung: Die Mittelwert-/Medianwerte werden vor dem Funktionsaufruf nur auf dem jeweiligen Training berechnet und an Validation weitergegeben. Funktionsform aus missing_value im alten Projekt, Zelle 14. Kein inplace=True, damit die Zuweisung zuverlässig auf dem DataFrame erfolgt.

## 7. Modell trainieren und bewerten
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 29.

```python
def train_and_evaluate(X_train, y_train, X_val, y_val, name, log_dict):
    # Modell initialisieren und trainieren
    model = LinearRegression()
    model.fit(X_train, y_train)

    # Vorhersage auf Validation
    y_pred = model.predict(X_val)

    # Kennzahlen
    mae = mean_absolute_error(y_val, y_pred)
    r2  = r2_score(y_val, y_pred)

    # Ausgabe
    print(f"Evaluation von {name}")
    print("MAE:", mae)
    print("R² :", r2)
    print()

    # Loggen
    log_dict[name] = {"MAE": mae, "R2": r2}

    return model
```

Anpassung: Die alte train_and_evaluate-Funktion bleibt fast unverändert. Neu sind nur MAE/R² für Training und die Generalisierungslücke, wie in Task 2 verlangt.

## 8. Baseline
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 27.

```python
X_train_base = X_train_base.fillna(X_train_base.mean())
X_val_base   = X_val_base.fillna(X_train_base.mean())
```

Anpassung: Alle Originalfeatures statt nur drei ausgewählter Features; Mittelwerte explizit vor dem Auffüllen aus Training berechnen.

## 9. Median als einzelne Maßnahme
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 14.

```python
windspeed = df["windspeed"].median()
```

Anpassung: Dieselbe Median-/fillna-Logik, auf alle Eingabefeatures angewendet. Den Trainingsmedian weiterreichen statt im Validierungsset neu berechnen.

## 10. Negative Werte und Winsorisierung
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 7.

```python
df.loc[df["windspeed"] == -1, "windspeed"] = np.nan
```

Anpassung: Die .loc-Regel und die kleine Funktion mit df.copy stammen aus dem alten Projekt. Die P5-/P95-Regeln selbst sind neu aus eurem Tipp. Quantile werden nur im Training berechnet.

## 11. IQR als Vergleich
Quelle: `Python Labs/XX - Building Pipelines for Data Preparation/Pipeline_Demo2_with_OutlierHandlingClass.ipynb`, Zelle 3.

```python
Q1 = X.quantile(0.25)
            Q3 = X.quantile(0.75)
            IQR = Q3 - Q1
            X[(X < (Q1 - 1.5 * IQR)) | (X > (Q3 + 1.5 * IQR))] = np.nan
```

Anpassung: Nur den IQR-Zweig der Originalklasse als kleine Funktion verwenden. Grenzen separat im Training berechnen und übergeben, statt sie bei jedem transform auf Validation neu zu lernen.

## 13. Grundkombinationen vergleichen
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 38.

```python
pd.DataFrame(log_results)
```

Anpassung: Log wie im alten Projekt in einen DataFrame umwandeln; Zeilen statt Spalten pro Modell, MAE-Sortierung und Deltas ergänzen.

## 15. Power-Transformation
Quelle: `Python Labs/06 - Transformation/TRANSFORMATION_SCRIPT.ipynb`, Zelle 41.

```python
from sklearn.preprocessing import PowerTransformer

pt = PowerTransformer(standardize=False)
trans_x= pt.fit_transform(np.array(df['petal length (cm)'].array.reshape(-1, 1)))

df['petal length (cm) ptransformed']=trans_x

df['petal length (cm) ptransformed'].hist(bins=20,figsize=(5, 5))
#Does not work in this example - skew is not clear enough
```

Anpassung: Dieselbe PowerTransformer-Klasse und standardize=False. Kleine train/apply-Funktionen nach scale_train/scale_apply aus dem alten Projekt, Zelle 24. Nur im Training fitten; identische Spalten in Validation transformieren.

## 16. Windrichtung zyklisch
Quelle: `Python Labs/06 - Transformation/TRANSFORMATION_SCRIPT.ipynb`, Zelle 16.

```python
#Demonstrate cyclic encoding of Month feature
df_cyclic_encoding['Month_sin'] = np.sin(2 * np.pi * df_cyclic_encoding['Month']/12)
df_cyclic_encoding['Month_cos'] = np.cos(2 * np.pi * df_cyclic_encoding['Month']/12)
df_cyclic_encoding
```

Anpassung: Windrichtung statt Monat; 360 statt 12. Die Winkelkomponenten ersetzen das Rohfeature, keine Auswahl nach Korrelation. Grad wird aus dem Wertebereich angenommen. Eine kleine df.copy-Funktion folgt dem alten rule_based-Muster.

## 17. Standardisierung
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 24.

```python
# Liste der numerischen Features, die wirklich skaliert werden sollen:
to_scale = ["instant", "atemp", "hum", "windspeed"]

def scale_train(df):
    df = df.copy()

    # Scaler initialisieren
    scaler = StandardScaler()

    # Fit + Transform auf Trainingsdaten
    df[to_scale] = scaler.fit_transform(df[to_scale])

    return df, scaler

def scale_apply(df, scaler):
    df = df.copy()

    # Transform auf Validation/Test anwenden
    df[to_scale] = scaler.transform(df[to_scale])

    return df

log_results = {}
```

Anpassung: Die beiden Funktionen scale_train/scale_apply bleiben erhalten. Spaltenliste wird als Argument übergeben, damit Rohwind oder Sinus/Cosinus korrekt skalierbar sind; Monatsdummies werden nicht skaliert.

## 19. Gesamtergebnis und Diagramme
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 38.

```python
pd.DataFrame(log_results)
```

Anpassung: Log-DataFrame wie im Original; Deltas und Balkendiagramme mit Pandas/Matplotlib entsprechend Feedback. Die Grafiken haben denselben Datensatz als Vergleichsbasis.

## 22. Finalen Test vorbereiten
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 36.

```python
# Trainieren der Testdaten
test_M1 = rule_based(test_df)
test_M2 = missing_value(test_M1)
test_M3 = feature_removal(test_M2)
```

Anpassung: Dieselbe explizite Folge benannter Funktionen; nur die tatsächlich ausgewählten Schritte. Ausschließlich bereits berechnete Trainingsgrenzen, Trainingsmedian und PowerTransformer verwenden.

## 23. Drei CSVs exportieren
Quelle: `Altes Projekt mit Feedback/Abgabe_Task_2_Kevin_Izem-2 2/old_Abgabe_Task2.ipynb`, Zelle 36.

```python
train_M5_scaled.to_csv("Task2_Final_preprocessed_train_M5.csv", index=False)
test_M5_scaled.to_csv("Task2_Final_preprocessed_test_M5.csv", index=False)
```

Anpassung: Zusätzlich Validation exportieren; unveränderte Labels aus den jeweiligen Originaldaten hinzufügen. Kein ID-Feature im Datensatz; Zeilenherkunft in separaten Dateien.

## Erweiterung: Power-Kombinationen und Kalender

16 zusätzliche explizite Aufrufe der vorhandenen Power-Funktionen: Transformation-Script Zelle 41 und altes Projekt Zelle 29. Keine neue Methode.

Kalender: altes Projekt Zelle 7 verwendet `pd.to_datetime`, `.dt.weekday`, `.dt.month` und entfernt das Rohdatum nach Ableitungen. Transformation-Script Zelle 16 verwendet `np.sin(2 * np.pi * Month / 12)` und Cosinus. Anpassung: dieselbe Formel für Stunde (24), Wochentag (7), Monat (12); drei Validation-Vergleiche.

Die Ursachenanalyse ergänzt einfache Fehler-Gruppierungen und eine algebraische Zerlegung mit `LinearRegression.coef_`. Dieser zusätzliche Diagnosecode ist ausdrücklich keine Original-Lab-Kopie und kein Bestandteil der Datenvorbereitung.

## Eigener Schritt: Zeitspalten entfernen

Original: altes Projekt, Zelle 18, `feature_removal`: `df.copy()`, `df.drop(columns=drop_list, errors="ignore")`, `return df`. Anpassung ausschließlich der Spaltenliste: timestamp und alle year_month-Dummies. Drei explizite Paarvergleiche mit der vorhandenen Bewertungsfunktion (altes Projekt Zelle 29). Keine Zeilen- oder Labeländerung.
