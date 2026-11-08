# Ergebnisse der Revision mit Task 1

- Split exakt wie Task 1: 2727 Training / 682 Validation; 204 fehlende Labels vor dem Split ausgeschlossen.
- Baseline mit allen 16 Originalfeatures: Validation-MAE 1.2939 µg/m³; R² 0.4117.
- Winsor-P95 allein: MAE 0.9336; Verbesserung 27.84%.
- IQR allein als Vergleich: MAE 0.9727; Verbesserung 24.82%.
- Negative Werte → P5 allein: MAE 1.2932; negative Werte → Imputation allein: 1.2918.
- NO-P5 ungefiltert -0.600; aus nichtnegativen Trainingswerten 0.100. Ungefiltertes P5 würde den Fehler nicht beheben.
- 144 Konfigurationen geprüft. Ausgewählt: **Median + Neg-P5 + Winsor-P95 + Power**. Validation-MAE 0.8414; R² 0.7282.
- MAE-Gewinn zur selben Baseline: 0.4525 µg/m³ bzw. 34.97%; R²-Differenz 0.3165.
- Training-MAE 0.8189; Validation–Training-Abstand 0.0225. Ein Abstand allein beweist kein Overfitting.
- 72 Scaling-Paare: maximale absolute MAE-Änderung 7.44e-15; unregularisierte OLS ist gegenüber affiner Skalierung grundsätzlich invariant.
- Abschließender Test: MAE 2.1935 µg/m³; R² 0.0382; bewertet 1419 von 1464 Zeilen. 45 fehlende Labels bleiben fehlend, alle Zeilen bleiben exportiert.
- Export: 2727 / 682 / 1464 Zeilen; 19 Features und unverändertes Label.

## Interpretation und Grenzen

Die P95-Kappung erhält das Feature und seine üblichen Werte, begrenzt aber alle Messungen oberhalb des Trainings-P95. Das kann auch echte Belastungsspitzen betreffen. IQR-Imputation ist ein anders definierter Eingriff und kann entsprechend anders wirken. Die Kombinationstabellen zeigen, ob beide neuen Tipps zusammen mehr bringen als ihre isolierten Effekte.

Keine Feature Selection in Task 2. Derselbe Split wie Task 1 verbessert die Vergleichbarkeit des Projektablaufs, aber die Task-1-Baseline enthält nur ein Feature und ist nicht die Task-2-Baseline. Zufällige Mischung benachbarter Stunden sowie 144 Varianten auf derselben Validation können optimistische Gütewerte ergeben.

Der Test war bereits bekannt; diese Revision ist kein neuer unabhängiger Holdout-Versuch. Keine Modellauswahl anhand des Tests. Unbekannte Testmonate ['2024-11', '2024-12'] werden über die OHE-Nullkodierung abgebildet. Bei negativem R² ist der quadratische Fehler größer als bei einer konstanten Vorhersage des Mittelwerts der bewerteten Labels. Änderungen gegenüber der alten Version sind wegen des neuen Splits nicht allein der Ausreißerbehandlung zurechenbar.
