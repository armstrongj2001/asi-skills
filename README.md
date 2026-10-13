# ASI Skills

Claude skills for construction, built and used on real jobs by [Armstrong Solutions Inc.](https://github.com/armstrongj2001)

Companion repo for the [AI in Construction](https://www.skool.com/ai-in-construction-8633) Skool community.

## Skills

| Skill | What it does | Runs in |
|---|---|---|
| [construction-project-tracker](skills/construction-project-tracker/SKILL.md) | Builds a 9-tab job schedule workbook in Excel: auto-drawing Gantt, inspections log, payment draws, action items, CSI reference. Branded to your company. | Claude in Excel |

## Install

1. Download the `.skill` file from [`dist/`](dist/).
2. Upload it to your Claude account's skills (in Claude's settings).
3. Open Claude in Excel on a blank workbook and ask: `build the schedule for 123 Main St`.

To customize a skill, fork this repo, edit its `SKILL.md`, and re-zip the skill folder.

## Layout

```
skills/<skill-name>/SKILL.md   source of truth — edit here
dist/<skill-name>.skill        packaged for upload
```

## License

MIT
