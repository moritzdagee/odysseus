## 2026-08-11 Zweig-Aufraeumen nach Bestandsaufnahme (Protokoll)

Anlass: Bestandsaufnahme vom 11.08. fand suite-weit rund 120 liegengebliebene
Zweige. Entscheidungsbaum: inhaltlich leer gegen den Hauptzweig (git diff/cherry) -> geloescht;
reine Doku-Zweige -> per PR uebernommen; alles andere -> erst als Tag archiv/<zweig>-2026-08-11
gesichert, dann geloescht (Wiederherstellung: git checkout -b <zweig> <tag>).
Zweige mit Commits juenger als 24h blieben unangetastet. Arbeitskopien nur entfernt,
wenn sauber und ohne aktive Sitzung.

- ZWEIG-REMOTE fix/api-chat-webhook-stub-fire-and-forget | GELOESCHT | leer gegen dev
- ZWEIG-REMOTE fix/native-agent-loop-guard-signals | ARCHIVIERT+GELOESCHT | Tag archiv/fix/native-agent-loop-guard-signals-2026-08-11
- ZWEIG-REMOTE main | UEBERSPRUNGEN | Schutzliste
- ZWEIG-REMOTE test/oversized-test-split-plan-3983 | ARCHIVIERT+GELOESCHT | Tag archiv/test/oversized-test-split-plan-3983-2026-08-11

