# Install wifi-layer

One shared WLAN skill with three entry points. It supports protocol reviews and changes involving 802.11, RSN, WPA2/WPA3-Enterprise, EAP/EAPOL, PMF, and capture processing.

## Quick install

1. Clone this repository or download it as a ZIP.
2. Copy the `.agents`, `.claude`, and `.cursor` directories from this directory into the root of your **wifit3 checkout**, merging them with any existing directories.
3. Start a new agent session in the wifit3 project and check that the skill appears in the skill list.

PowerShell alternative; adjust the project path:

```powershell
git clone https://github.com/DensenDeluxe/sKillz4wifit3.git
$bundle = (Resolve-Path './sKillz4wifit3/wifi-layer').Path
$project = (Resolve-Path 'C:/path/to/wifit3').Path
foreach ($agent in @('.agents', '.claude', '.cursor')) {
    $destination = Join-Path $project "$agent/skills"
    New-Item -ItemType Directory -Force -Path $destination | Out-Null
    Copy-Item -LiteralPath (Join-Path $bundle "$agent/skills/wifi-layer") -Destination $destination -Recurse -Force
}
```

The copy commands install or update only the `wifi-layer` directory, preserving other skills. Claude Code and Cursor also need the shared `.agents/skills/wifi-layer` directory.

## Entry points

| Agent | Project path | Contents |
| --- | --- | --- |
| Codex | `.agents/skills/wifi-layer/SKILL.md` | Complete workflow and five technical references |
| Claude Code | `.claude/skills/wifi-layer/SKILL.md` | Links to the shared workflow |
| Cursor | `.cursor/skills/wifi-layer/SKILL.md` | Links to the shared workflow |

Example prompt: "Use the wifi-layer skill to review this branch's Enterprise AKM and PMF handling."

The five references cover capture and state, Enterprise/EAP and keys, RSN and PMF, validation, and sources. Hardware and on-air tests still require the project's established procedures.

The installation paths are documented in the official guides for [Codex](https://learn.chatgpt.com/docs/build-skills), [Claude Code](https://code.claude.com/docs/en/skills), and [Cursor](https://cursor.com/docs/skills).
