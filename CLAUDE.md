# City Pages Project

## What This Project Does
Generates Elementor JSON templates for individual U.S. **city** pages. The **New York City page** is the structural template — every city page reuses its exact sections, layout, widget IDs, and styles, and only the copy and JSON-LD schema **values** change to be city-specific. Per-city copy comes from city copy files.

## Background
Every city page is built from a single reference city: **New York City**. The reference defines the sections, layout, widget IDs, and styles; a new city page keeps all of that identical and only swaps in city-specific copy and schema **values**. This project is purely city pages — there are no state pages.

## Files
- `template.json` — Base Elementor JSON: the **New York City page** export. Contains the JSON-LD schema embedded inside an HTML widget. This is the structural base every city page is built from. Never modify this.
- `new-york.md` — Master copy reference. Lists every key (LABEL) and shows which widget it maps to, and (for `SCHEMA_*` keys) which schema field. Keys stay constant across all pages; only the value to the right of the `:` changes. **Single source of truth for which keys exist.**
- `new-york-schema.html` — Standalone, readable copy of the JSON-LD schema script. The same content is embedded inside the HTML widget in `template.json`. Edit this file first when changing schema structure, then mirror the change into the template.
- `copy/[city].md` — Per-city copy, using the exact same keys as `new-york.md`.
- `output/[city].json` — Final Elementor JSON output per city.

### Do NOT read / do NOT crawl
- `notes.txt` — internal scratch notes; not part of the build.
- `dont-read.css` — not part of the build.

Never open, read, crawl, or use these two files. Skip them entirely when scanning the project.

## How to Run
When given a new city copy file (`copy/[city].md`):
1. Start from `template.json` (the New York City base).
2. Replace every New York City / reference value with the city's values from the copy file.
3. Update the embedded JSON-LD schema: city name, slug, MAC/Medicaid references, descriptions, HowTo step 2, `areaServed`, breadcrumb, etc. Generating a city page without updating the schema is incomplete.
4. Validate that the result parses as JSON, then save to `output/[city].json`.

MAC (e.g. National Government Services, New York's Jurisdiction K contractor) and Medicaid program names depend on the state the city sits in — always take them from the city copy file, never assume them.

## Rules
- Never modify `template.json`.
- Never modify `new-york-schema.html` except to make a deliberate schema-structure change, in which case re-embed the updated script into `template.json`'s HTML widget in the same edit.
- Never read or crawl `notes.txt` or `dont-read.css`.
- Keep all JSON structure, IDs, and styles intact. Only replace text content values.
- **Never invent, rename, add, or remove keys (labels).** The keys defined in `new-york.md` are the contract between the copy files and the template. A `copy/[city].md` file must use the exact same keys as `new-york.md` — only the value to the right of the `:` changes per city. If a city copy file is missing a key that exists in `new-york.md`, stop and ask the user rather than guessing a value. If a city copy file contains a key that does not exist in `new-york.md`, stop and ask — do not silently add new keys to the template. This includes the legacy `WV_`/`STATE` key names — keep them exactly as written.
- Treat `new-york.md` as the single source of truth for which keys exist and what each one maps to in the template. Do not introduce new keys without first updating `new-york.md`.
- Images: `new-york.md` currently defines no image-URL keys (images live inside `template.json`). The shared Tim Daniels headshot is identical on every page and must NOT be changed. If a city needs its own image, add the key to `new-york.md` first (the key contract), then wire it into the template.
- The state-dropdown widget in the template lists all 50 states — entries there are correct and should not be replaced.
- Output must be valid, importable Elementor JSON.

## Implementation Notes
- Always use Python (not PowerShell) to read/write JSON files. PowerShell 5.1 writes UTF-8 with a BOM, which makes the file invalid for Elementor import.
- Text in the JSON often contains embedded HTML markup (e.g. `<strong>`, `<em>`) and escaped slashes (e.g. `<\/strong>`, `95\/GT`). Replacements must match the actual raw file content including these characters, not just the plain-text version from the copy files.
- After generating each output file, validate it parses as JSON before saving (`json.loads(content)`).
- Do not leave any helper or temporary scripts (e.g. `.py` files) in the project folder. Run logic in-memory and clean up after.
