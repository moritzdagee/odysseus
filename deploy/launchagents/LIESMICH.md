# Autostart-Dateien (MacBook)

Die zwei Hintergrund-Dienste, die Odysseus auf Moritz' MacBook dauerhaft laufen
lassen. Quelle ist dieser Ordner, wirksam ist die Kopie in `~/Library/LaunchAgents/`.
Seit 24.09.2026 versioniert, vorher lagen sie nur dort (Beschreibung in
`CLAUDE-SETUP-NOTES.md`).

| Datei | Dienst |
|---|---|
| `claude.macbook.odysseus.chromadb.plist` | Gedaechtnis-Datenbank (ChromaDB) auf Port 8100 |
| `claude.macbook.odysseus.ui.plist` | die Odysseus-Oberflaeche auf Port 7860 (`odysseus-ui`) |

## Installieren

```bash
cmp deploy/launchagents/claude.macbook.odysseus.ui.plist ~/Library/LaunchAgents/claude.macbook.odysseus.ui.plist \
  || { cp -p ~/Library/LaunchAgents/claude.macbook.odysseus.ui.plist ~/Library/LaunchAgents/claude.macbook.odysseus.ui.plist.vor-kopie-$(date +%Y%m%d-%H%M%S);
       cp deploy/launchagents/claude.macbook.odysseus.ui.plist ~/Library/LaunchAgents/;
       launchctl bootout gui/$(id -u)/claude.macbook.odysseus.ui; launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/claude.macbook.odysseus.ui.plist; }
```

Fuer `chromadb` genauso. Erst vergleichen, nur bei Abweichung kopieren und neu laden.
