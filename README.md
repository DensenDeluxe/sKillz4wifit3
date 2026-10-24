# sKillz4wifit3

Research-backed agent skills for code review and development in [wifit3](https://github.com/derv82/wifit3).

| Skill | Focus | Agents | Installation |
| --- | --- | --- | --- |
| [wifi-layer](wifi-layer/.agents/skills/wifi-layer/SKILL.md) | 802.11, RSN, WPA2/WPA3-Enterprise, EAP/EAPOL, PMF, capture analysis, and security claims in the UI or campaigns | Codex, Claude Code, Cursor | [Quick install](wifi-layer/README.md) |
| [wifi-vulns](wifi-layer/.agents/skills/wifi-vulns/SKILL.md) | Fresh Wi-Fi CVEs, historical vulnerability classes, patch evidence, PoC analysis, and scientific audit provenance | Codex, Claude Code, Cursor | [Quick install](wifi-layer/README.md) |

The bundle provides separate entry points for each agent and one shared reference set per skill.

```text
wifi-layer/
├── .agents/skills/              # Main Codex skills and shared references
│   ├── wifi-layer/
│   └── wifi-vulns/
├── .claude/skills/              # Claude Code entry points
├── .cursor/skills/              # Cursor entry points
└── README.md                    # Quick installation guide
```

These skills are intended for use inside a wifit3 checkout. They follow its project rules and reference its source code. The references document sources, verification dates, and research limitations; the original research report is not included.
