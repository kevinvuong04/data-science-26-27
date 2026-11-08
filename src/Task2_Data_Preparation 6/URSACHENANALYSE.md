# Ursachenprüfung und Verbesserung von Task 2

## Ergebnis

Die Implementierung wurde kontrolliert: unveränderte Rohdaten, identischer Task-1-Split, Training-only-Parameter, passende Feature-Reihenfolge und unveränderte Zielwerte. Die final exportierten Daten reproduzieren die Vorhersagen. Ein Vertauschen von Zeilen oder erneutes Fitten auf Testdaten erklärt den Abstand nicht.

| Stand | MAE Training | MAE Validation | R² Validation | MAE Test | R² Test |
|---|---:|---:|---:|---:|---:|
| Bisher: Median + Neg-Imputation + P95 + Power | 0,8212 | 0,8499 | 0,7273 | 2,2386 | 0,0113 |
| Neu: Median + Neg-P5 + P95 + Power | 0,8189 | 0,8414 | 0,7282 | 2,1935 | 0,0382 |

Die Verbesserung gegenüber dem bisherigen Stand beträgt auf Validation rund 1,01 %, im Test rund 2,02 %. Diese kleinen Unterschiede sind kein Nachweis statistischer Signifikanz. Gegenüber der vollständigen Baseline (Validation-MAE 1,2939) verbessert sich der Validation-MAE um 34,97 %. Der Test bleibt schwach.

## Was den Testabstand erklärt – und was nicht bewiesen ist

**1. Zeitliche Extrapolation und unbekannte Monatskategorien.** Training und Validation enthalten Juni bis Oktober 2024; Test enthält November und Dezember. Der zufällige 80/20-Split prüft keine Vorhersage auf spätere Monate. Die bisherigen Monats-Dummies können neue Kategorien nicht lernen: Beide Testmonate werden beim Spaltenabgleich wie die Trainingsreferenz behandelt. Dazu kommt ein negativer linearer Tagestrend. Im neu ausgewählten Modell beträgt dessen Koeffizient −0,01237 pro Tag. Der kombinierte Zeitbeitrag zur Vorhersage beträgt durchschnittlich −0,443 im Training und −2,239 im Test, also rund 1,80 µg/m³ weniger. Die mittlere Testabweichung beträgt −1,985 µg/m³: Das Modell unterschätzt systematisch. Die Zerlegung belegt diesen Modellmechanismus, jedoch keinen isolierten kausalen Anteil am Testfehler; auch andere Eingaben und die Zielverteilung ändern sich.

**2. Andere Verfügbarkeit der Messdaten.** In den tatsächlich verwendeten Trainingszeilen fehlen Wetterdaten bei rund 11,4 %, im Test bei 67,3 %. Im ursprünglichen Trainingsfile sind es 11,8 %. CO₂ fehlt im Test bei 58,6 %, Black Carbon bei 48,7 %. Die Imputation bleibt korrekt auf Trainingswerte beschränkt, ersetzt im Test aber wesentlich mehr echte Information. Fehlende Wetterdaten allein erklären den Abstand nicht: Unter den bewertbaren Testzeilen beträgt der MAE mit vorhandenen Wetterdaten 2,670, bei fehlenden Wetterdaten 1,984. Diese Gruppen liegen in verschiedenen Zeitabschnitten und sind nicht als kontrollierter Vergleich zu verstehen.

**3. Die P95-Kappung greift im Test viel häufiger.** Die feste Trainingsgrenze beträgt 11,3155 µg/m³ für pm25_lowcost. Sie wird von 4,55 % aller Trainingszeilen und 18,03 % aller Testzeilen überschritten; fehlende Messwerte sind in diesen Nennern enthalten. Bei den 254 bewertbaren Testzeilen oberhalb der Grenze beträgt der MAE 3,699, in den übrigen 1.165 Zeilen 1,865. Hohe echte Belastungen können durch die Kappung an Information verlieren. Die Gruppenanalyse beweist nicht, dass die Kappung den gesamten Mehrfehler verursacht. Die Grenze wurde nicht anhand des Tests verändert.

**4. Andere Zielverteilung.** Der Zielmittelwert steigt von 3,330 im Training auf 3,909 im Test, die Standardabweichung von 2,206 auf 2,906. Die Zielwerte werden unverändert belassen. Der Fehler steigt im Test von November (MAE 1,775) auf Dezember (2,574). Ein kleiner Training-/Validation-Abstand schützt nicht vor solchen Verteilungsunterschieden.

## Welche Verbesserungen wurden geprüft?

Der bisherige stufenweise Vergleich hatte Power nur auf einzelnen Grundvorbereitungen geprüft. Jetzt wird Power mit allen 18 Grundvorbereitungen verglichen. Dazu kommen drei begründete Kalenderdarstellungen: Baseline, bisherige Auswahl und neue P5-Kombination. Mit den drei ergänzten Zeitspalten-Vergleichen sind es insgesamt 50 Varianten, weiterhin keine vollständige Suche über alle Möglichkeiten.

Die Kalenderalternative verwendet ausschließlich die vorhandene Logik: Datumsableitungen aus dem alten Projekt (Zelle 7), Sinus/Cosinus aus dem Transformations-Lab (Zelle 16). Timestamp wird durch Stunde/Wochentag, year_month durch zyklischen Monat repräsentiert. Alle übrigen Features bleiben erhalten. Die Kalenderalternative erreicht auf der neuen P5-Kombination Validation-MAE 0,8439, gegenüber 0,8414 mit bisheriger Zeitdarstellung. Sie wird nach dem festgelegten MAE-Kriterium nicht übernommen und nicht separat auf dem Test bewertet.

Die neue Auswahl verwendet dieselben bereits vorhandenen Funktionen und LinearRegression. Nur P5 statt Negativwert-Imputation wird übernommen. Es wird kein neues Modellverfahren eingeführt. Das Entfernen der Zeitspalten wird zusätzlich geprüft, aber nicht in die finale Variante übernommen. Grenzen, Mediane und PowerTransformer stammen weiterhin nur aus Training. Kein Testlabel und keine Testzeile wird verändert oder entfernt.

## Grenzen und Konsequenz

Der bekannte Test wurde zur Diagnose genutzt; die Kalenderidee wurde danach formuliert. Auswahl über Validation ist notwendig, stellt aber keinen unberührten Test wieder her. Auch Validation wurde mehrfach verwendet. Es gibt daher keinen belastbaren Nachweis, dass die kleine Verbesserung auf neuen Daten anhält.

Eine zeitlich getrennte Zusatzvalidierung wäre für die spätere Anwendung aussagekräftiger; sie ersetzt hier ausdrücklich nicht den vereinbarten Task-1-Split. Um das Testproblem zuverlässig zu lösen, wären eine neue unabhängige spätere Prüfperiode und bessere Messdatenabdeckung nötig. Änderungen von Modellklasse gehören in Task 3. Testabhängige Quantile, Korrektur der Vorhersagen um den beobachteten Test-Bias oder Auswahl anhand besserer Testwerte wären keine saubere Lösung.

Die Abgabe berichtet deshalb die kleine regelkonforme Verbesserung und die verbleibende schlechte Übertragbarkeit offen. Keine Präsentation erstellt.

## Ergebnis des Zeitspalten-Vergleichs

| Datenstand | Validation-MAE mit Zeitspalten | ohne Zeitspalten | Änderung durch Weglassen |
|---|---:|---:|---:|
| Vollständige Baseline | 1,293881 | 1,343886 | +0,050006 |
| Median + Neg-Imputation + P95 | 0,928889 | 0,923254 | −0,005635 |
| Median + Neg-P5 + P95 + Power | 0,841373 | 0,848958 | +0,007585 |

Ein kleinerer MAE ist besser. Das Entfernen hilft also leicht auf der Grundvorbereitung, aber nicht auf der Baseline oder der bislang besten Gesamtvariante. Das zeigt, weshalb Einzelmaßnahmen und Kombinationen getrennt geprüft werden müssen. Bei der Grundvorbereitung sinkt zwar der MAE, das Validation-R² verschlechtert sich von 0,6583 auf 0,6436; das festgelegte Auswahlkriterium bleibt MAE.

**Finale Auswahl aus 50 Varianten:** Median + Neg-P5 + Winsor-P95 + Power **mit Zeitfeatures**. Ihr Validation-MAE 0,841373 bleibt der kleinste untersuchte Wert. Der Ausschluss der Zeitspalten wurde durchgeführt und bewertet, aber nicht in die finalen CSVs übernommen. Damit wird nicht der bereits bekannte bessere Testwert der Variante ohne Zeitfeatures zum Auswahlkriterium gemacht.

Das ausgewählte Modell und die finalen Exporte bleiben deshalb inhaltlich unverändert: Test-MAE 2,193486 und Test-R² 0,038223. Die Einschränkung der schlechten zeitlichen Übertragbarkeit bleibt bestehen. Die drei neuen Kandidaten werden nicht zusätzlich auf dem Test zur Auswahl verglichen. Alle Zeilen und Zielwerte bleiben erhalten. Keine Präsentation erstellt.
