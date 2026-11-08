# Ergebnisse und Learnings

- **Baseline:** Validation-MAE 1.5017 µg/m³; R² -0.0962. Alle 16 Ausgangsfeatures sind repräsentiert.
- **Stärkste isolierte Variante:** Mittelwert + IQR, MAE 1.1753. Einzelmaßnahmen sind nicht automatisch in Kombination besser.
- **Ausgewählt aus 32 Varianten:** Mittelwert + IQR + Power + Windzyklus; Validation-MAE 1.0988; R² 0.3126.
- **Baseline-Verbesserung:** 0.4029 µg/m³ bzw. 26.83%; R²-Differenz 0.4089.
- **Median + IQR:** gemeinsamer Gewinn 0.2858; Summe der isolierten Gewinne 0.3569; zusätzliche Interaktion -0.0711 µg/m³. Die Wirkung ist nicht einfach additiv.
- **Scaling:** maximale absolute MAE-Änderung über die 16 Paarvergleiche 1.01e-13. Standardisierung ist hier kein automatischer Qualitätsgewinn.
- **Generalisierung:** Training-MAE 0.9782, Validation-MAE 1.0988; Abstand 0.1206. Ein Abstand kann Überanpassung oder zeitliche Verteilungsänderungen widerspiegeln; ein einzelner Split trennt beides nicht sicher.
- **Finaler Test:** MAE 2.4881 µg/m³; R² -0.5329; bewertet 1419 von 1464 Zeilen. Alle Testzeilen exportiert, Labels unverändert.
- **Übergabe:** 2686 Trainingszeilen, 723 Validierungszeilen und 1464 Testzeilen, jeweils 20 vorbereitete Features. Kein Refit auf Validation.

## Deutung des Testbefunds

Der negative Test-R² bedeutet einen höheren quadratischen Fehler als die konstante Vorhersage des Mittelwerts der beobachteten Testlabels. Das Modell liefert somit keinen überzeugenden Nachweis präziser Kalibrierung für spätere Monate. Die Verbesserung auf Validation darf diesen Befund nicht verdecken.

## Grenzen und offene fachliche Punkte

- Keine aktuelle Task-1-Zusammenfassung vorhanden. Diagnosen und Maßnahmen mit dem Team abgleichen.
- Die Zeitdarstellung als linearer Trend und redundantes year_month sind eine minimale technische Repräsentation, keine behauptete physikalische Ursache.
- Unbekannte Testmonate: ['2024-11', '2024-12']; OHE fällt auf die Nullkodierung zurück. Zeitliche Drift und Extrapolation können Testfehler erhöhen.
- Statistik allein belegt keine Sensorfehler. IQR-Behandlung kann reale Schadstoffspitzen ersetzen; gegebenenfalls fachlich prüfen.
- Fehlende Labels erlauben dort keine Fehlerberechnung. Fehlende Sensorwerte sind nicht automatisch zufällig; die Diagnose beweist weder MCAR noch MAR.
- Auswahl unter 32 Varianten auf nur einem Validierungssplit; keine Behauptung universeller Überlegenheit oder garantierter Punktzahl.
- Feature Selection und andere Modellalgorithmen gehören entsprechend Feedback nicht in diese Umsetzung von Task 2.
