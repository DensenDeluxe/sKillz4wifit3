# wifi-layer installieren

Ein gemeinsamer WLAN-Skill mit drei Einstiegspunkten. Er unterstützt Protokollreviews und Änderungen an 802.11, RSN, WPA2/WPA3-Enterprise, EAP/EAPOL, PMF und der Capture-Verarbeitung.

## Mini-Installation

1. Dieses Repository klonen oder als ZIP herunterladen.
2. Die drei Ordner `.agents`, `.claude` und `.cursor` aus diesem Verzeichnis in das Stammverzeichnis deines **wifit3-Checkouts** kopieren und mit vorhandenen Ordnern zusammenführen.
3. Eine neue Agent-Sitzung im wifit3-Projekt starten und den Skill in der Skill-Liste prüfen.

PowerShell-Variante; den Projektpfad anpassen:

```powershell
git clone https://github.com/DensenDeluxe/sKillz4wifit3.git
$bundle = (Resolve-Path './sKillz4wifit3/wifi-layer').Path
$project = (Resolve-Path 'C:/Pfad/zu/wifit3').Path
foreach ($agent in @('.agents', '.claude', '.cursor')) {
    $destination = Join-Path $project "$agent/skills"
    New-Item -ItemType Directory -Force -Path $destination | Out-Null
    Copy-Item -LiteralPath (Join-Path $bundle "$agent/skills/wifi-layer") -Destination $destination -Recurse -Force
}
```

Die Kopierbefehle installieren beziehungsweise aktualisieren nur den Ordner `wifi-layer`; andere Skills bleiben erhalten. Für Claude Code und Cursor muss der gemeinsame Ordner `.agents/skills/wifi-layer` ebenfalls mitkopiert werden.

## Einstiegspunkte

| Assistent | Projektpfad | Inhalt |
| --- | --- | --- |
| Codex | `.agents/skills/wifi-layer/SKILL.md` | Vollständiger Workflow und fünf Fachreferenzen |
| Claude Code | `.claude/skills/wifi-layer/SKILL.md` | Verweis auf den gemeinsamen Workflow |
| Cursor | `.cursor/skills/wifi-layer/SKILL.md` | Verweis auf den gemeinsamen Workflow |

Beispielauftrag: „Nutze den Skill wifi-layer und reviewe die Enterprise-AKM- und PMF-Behandlung dieses Branches.“

Die fünf Referenzen behandeln Capture und Zustand, Enterprise/EAP und Schlüssel, RSN und PMF, Validierung sowie Quellen. Hardware- und Funkversuche benötigen weiterhin den dafür vorgesehenen Projektablauf.

Die Installationspfade sind in der offiziellen Dokumentation beschrieben: [Codex](https://learn.chatgpt.com/docs/build-skills), [Claude Code](https://code.claude.com/docs/en/skills) und [Cursor](https://cursor.com/docs/skills).
