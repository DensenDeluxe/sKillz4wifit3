# sKillz4wifit3

Research-backed agent skills for code review and development in [wifit3](https://github.com/derv82/wifit3).

| Skill | Focus | Agents | Installation |
| --- | --- | --- | --- |
| [wifi-layer](wifi-layer/.agents/skills/wifi-layer/SKILL.md) | 802.11, RSN, WPA2/WPA3-Enterprise, EAP/EAPOL, PMF, and capture analysis | Codex, Claude Code, Cursor | [Quick install](wifi-layer/README.md) |

Each skill has its own directory, with separate entry points for each agent and a single set of shared technical references.

```text
wifi-layer/
├── .agents/skills/wifi-layer/   # Main Codex skill and shared references
├── .claude/skills/wifi-layer/   # Claude Code entry point
├── .cursor/skills/wifi-layer/   # Cursor entry point
└── README.md                   # Quick installation guide
```

These skills are intended for use inside a wifit3 checkout. They follow its project rules and reference its source code. The references document sources, verification dates, and research limitations; the original research report is not included.
