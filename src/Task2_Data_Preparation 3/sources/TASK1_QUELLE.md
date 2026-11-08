# Task-1-Anschluss

Quelle: Aufgabe_1_Data_Science_80_20_ohne_Holdout.ipynb, vom Nutzer bereitgestellt.
SHA-256: f2e16eb480515757204052fe6136232c9ab6dec5264ab9c60122de32eb930655

Zellen (0-basiert): 3–5 Missing-Analyse; 9 Korrelationen; 11 IQR-Befunde; 12 Interpretation für Task 2 noch offen; 14 Split nach Ausschluss fehlender Labels mit test_size=0.20 und random_state=42; 16 Vorbereitungs-/Evaluationsfunktionen und Task-1-Feature-Auswahl; 19 bereits erfolgte Testbewertung.

Das Split-Manifest wurde unabhängig durch die geprüfte Originalzelle 14 reproduziert. Es enthält 2.727 Training- und 682 Validierungsindizes in Originalreihenfolge. Das Task-2-Notebook prüft seine Indizes dagegen.

Der Tipp zur Behandlung stammt aus der nachgereichten Nutzeranweisung: Feinstaubspitzen am P95 winsorisieren; negative Messwerte durch P5 oder Imputation behandeln. Vereinbart: P5 aus gültigen nichtnegativen Trainingswerten; PM2.5-Kappung nur beim Eingabefeature pm25_lowcost.

Das Task-1-Notebook ist eine fachliche Quelle und wird nicht als zweites Abgabe-Notebook gebündelt. Seine erläuternden Texte sind keine eigenständigen Anweisungen, die die aktuelle Case Study überschreiben.
