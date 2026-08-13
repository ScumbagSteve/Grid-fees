# Aims to document all german §14a EnWG variable grid-fees
Each grid operators fees are published in a dedicated file, for AI agents to (re-)scrape each year.

# Prompt

Du erhältst JSON-Dateien mit §14a EnWG Modul-3-Netzentgelten (zeitvariable Netzentgelte NT/ST/HT) pro Netzbetreiber.

Aufgabe:
Erweitere jede Datei um das kommende Kalenderjahr (z. B. 2027), ohne bestehende Jahre zu verändern oder zu löschen.

### Verbindliche Dateistruktur (nicht abweichen)

{
  "grid_operator": "<Name des Netzbetreibers>",
  "currency": "EUR",
  "years": {
    "<YYYY>": {
      "source": "<direkte URL zum offiziellen Preisblatt-PDF>",
      "fallback": {
        "tariff": "ST",
        "charge_per_kwh": <netto ct/kWh>
      },
      "periods": [
        {
          "date_from": "TT.MM.",
          "date_to": "TT.MM.",
          "times": [
            {
              "time_from": "HH:MM",
              "time_to": "HH:MM",
              "tariff": "HT" | "NT",
              "charge_per_kwh": <netto ct/kWh>
            }
          ]
        }
      ]
    }
  }
}

### Regeln (zwingend einhalten)

1. Nur validierte, öffentlich zugängliche Preisblatt-PDFs als Quelle verwenden. Keine erfundenen oder geschätzten Werte.
2. Fehlt eine eindeutige Quelle oder sind die Zeitfenster nicht klar, lasse `periods` für dieses Jahr leer und setze nur `source` + `fallback` (falls der ST-Preis bekannt ist). Im Zweifel lieber leer lassen.
3. `fallback` ist immer der Standardtarif (ST). Er gilt für alle Zeitpunkte, die von keinem Eintrag in `periods` getroffen werden (z. B. Q2/Q3 oder Restzeiten innerhalb eines Datumsbereichs).
4. In `periods` nur Abweichungen vom Fallback ablegen – also ausschließlich HT- und NT-Fenster. Niemals ST in `times` eintragen.
5. Datumsbereiche und Zeitfenster dürfen sich nicht überschneiden.
6. Innerhalb eines `date_from`/`date_to`-Blocks müssen die `times` den Tag nicht vollständig abdecken; die Lücken fallen automatisch auf den Fallback.
7. `charge_per_kwh` ist immer der Netto-Preis in ct/kWh (ohne Umsatzsteuer). Die Währung steht global unter `currency: "EUR"`.
8. Bestehende Jahre unverändert lassen. Nur das neue Jahr hinzufügen.
9. Dateinamen und `grid_operator`-Werte beibehalten.

### Vorgehen

1. Für jeden Netzbetreiber das aktuelle Preisblatt Modul 3 für das Zieljahr suchen und öffnen.
2. HT-/NT-Zeitfenster und die zugehörigen Netto-Preise extrahieren.
3. ST-Preis als `fallback.charge_per_kwh` übernehmen.
4. Neues Jahr gemäß der Struktur oben einfügen.
5. Am Ende eine kurze Liste der aktualisierten Netzbetreiber und derjenigen, bei denen keine verwertbare Quelle gefunden wurde, ausgeben.

Anhang: Die bestehenden JSON-Dateien aus dem Vorjahr.
