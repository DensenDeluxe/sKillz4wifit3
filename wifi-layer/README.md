# Install the Wi-Fi skills

This bundle contains two shared skills with entry points for Codex, Claude Code, and Cursor:

- `wifi-layer` supports protocol reviews and changes involving 802.11, RSN, WPA2/WPA3-Enterprise, EAP/EAPOL, PMF, capture processing, and security claims shown or gated by the UI and campaigns.
- `wifi-vulns` performs research-only collection and validation of fresh CVEs, historical attack classes, patch evidence, public PoC evidence, and scientific audit provenance. A named subject is one dossier; a worldwide multi-window survey runs only for a broad review.

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
    foreach ($skill in @('wifi-layer', 'wifi-vulns')) {
        Copy-Item -LiteralPath (Join-Path $bundle "$agent/skills/$skill") -Destination $destination -Recurse -Force
    }
}
```

The copy commands install or update only the two Wi-Fi skill directories, preserving other skills. Claude Code and Cursor also need the shared `.agents/skills` directories because their entry points link to the common workflows.

## Entry points

| Skill | Codex | Claude Code | Cursor |
| --- | --- | --- | --- |
| `wifi-layer` | `.agents/skills/wifi-layer/SKILL.md` | `.claude/skills/wifi-layer/SKILL.md` | `.cursor/skills/wifi-layer/SKILL.md` |
| `wifi-vulns` | `.agents/skills/wifi-vulns/SKILL.md` | `.claude/skills/wifi-vulns/SKILL.md` | `.cursor/skills/wifi-vulns/SKILL.md` |

Example prompt: "Use the wifi-layer skill to review this branch's Enterprise AKM and PMF handling."

Research example: "Use the wifi-vulns skill to produce a source-validated watchlist of Wi-Fi CVEs updated during the last 90 days and map their evidence to wifit3."

The `wifi-layer` references cover capture and state, Enterprise/EAP and keys, RSN and PMF, validation, and sources. The `wifi-vulns` references cover research method, normalized vulnerability records, worldwide wifit3 scope, scientific audit evidence, and provenance. `wifi-vulns` is strictly research-only and does not execute PoCs, scan networks, transmit frames, or modify product code.

The installation paths are documented in the official guides for [Codex](https://learn.chatgpt.com/docs/build-skills), [Claude Code](https://code.claude.com/docs/en/skills), and [Cursor](https://cursor.com/docs/skills).
