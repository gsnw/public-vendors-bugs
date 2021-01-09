# Falsche Sortierung bei Zugriffsregeln im Bereich Regelketten

Bei den Zugriffsregeln gibt es ein Sortierfehler in der WebGUI, welcher die Regelketten nicht nach der Verarbeitungsreihenfolge anzeigt, sondern vermutlich nach einer Anzahl ID.

## Betroffene Geräte

Router: be.IP  
Firmware: 10.2 Rev-8

## Schritte zum Reproduzieren

1) Über 10 Zugriffsfilter Regeln erstellen
2) Regeln nacheinander in eine Regelkette einreihen
3) Sobald die 10te Regel hinzugefügt wird, wird diese Regel an zweiter Stelle der Regelkette angezeigt und nicht an das Ende. Alle darauffolgenden Regeln werden auch nicht an das Ende gesetzt.
