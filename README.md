# sKillz4wifit3

Recherchebasierte Agent-Skills für Reviews und Entwicklung an [wifit3](https://github.com/derv82/wifit3).

| Skill | Schwerpunkt | Assistenten | Installation |
| --- | --- | --- | --- |
| [wifi-layer](wifi-layer/.agents/skills/wifi-layer/SKILL.md) | 802.11, RSN, WPA2/WPA3-Enterprise, EAP/EAPOL, PMF und Capture-Auswertung | Codex, Claude Code, Cursor | [Mini-Anleitung](wifi-layer/README.md) |

Jeder Skill bekommt einen eigenen Ordner. Darin liegen die Einstiegspunkte nach Assistent getrennt; gemeinsame Fachreferenzen werden nur einmal gepflegt.

```text
wifi-layer/
├── .agents/skills/wifi-layer/   # Hauptskill für Codex + gemeinsame Referenzen
├── .claude/skills/wifi-layer/   # Claude-Code-Einstieg
├── .cursor/skills/wifi-layer/   # Cursor-Einstieg
└── README.md                   # Mini-Installation
```

Die Skills sind für die Verwendung innerhalb eines wifit3-Checkouts vorgesehen. Sie berücksichtigen dessen Projektregeln und verweisen auf dessen Quellcode. Quellen, Prüfdatum und Grenzen der Recherche stehen in den jeweiligen Referenzen; der zugrunde liegende Recherchebericht selbst ist nicht enthalten.
