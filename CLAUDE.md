# FC Assistant — Claude Desktop Instructions

This repository contains the skills for the Functional Consultant AI assistant, designed to run in **Claude Desktop** using Projects.

## How to use

Each client engagement lives in its own Claude Desktop Project. The `fc-assistant` skill is set as the Project's custom instructions and acts as the entry point for all engagement work.

If no project configuration is found in the custom instructions, Claude will ask for it interactively and produce a ready-to-paste configuration block.

## Resources directory structure

- `resources/commercial/` — pre-sales materials (proposals, SOW, RFP, audit documents) — input only, never written by Claude
- `resources/workshops/` — workshop outputs (transcripts, notes, process diagrams, client system docs) — input only, never written by Claude
- `skills/` — all FC assistant skills (SKILL.md files)
- `docs/` — internal documentation and implementation plans

All deliverables (Workshop Guide, Requirements Register, FDRs, Solution Overview, Functional Document, etc.) are published exclusively to Confluence — never as local files.
