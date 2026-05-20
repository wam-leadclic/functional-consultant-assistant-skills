# Agent Parameters — FC Assistant

## Project
- **Project name:** [Project name]
- **Client:** [Client name]
- **Engagement start:** [YYYY-MM-DD]

## Language
- **Output language:** es
*(ISO 639-1 code — e.g. `es`, `en`, `fr`, `pt`. Default: `es`. All generated documents, tables, and output text must use this language.)*

## Integrations
- **Has integrations:** [yes / no]
*(Set to `no` if the project has no third-party system integrations. This marks Integration Map as N/A and removes it as a phase gate.)*

## Confluence
- **Base URL:** https://[org].atlassian.net/wiki
- **Space key:** [SPACE-KEY]
- **Project root page ID:** [Page ID — find it in the page URL: .../pages/[ID]/...]

## Commercial materials sources
*(Add one entry per source. At least one source required.)*
- type: local
  path: ./resources/commercial/
# - type: google_drive
#   folder_id: [Google Drive folder ID for commercial materials]
# - type: confluence
#   page_id: [Confluence page ID containing pre-sales documentation]

## Workshop materials sources
*(Add one entry per source. At least one source required.)*
- type: local
  path: ./resources/workshops/
# - type: google_drive
#   folder_id: [Google Drive folder ID for workshop outputs]
# - type: confluence
#   page_id: [Confluence page ID containing workshop documentation]
