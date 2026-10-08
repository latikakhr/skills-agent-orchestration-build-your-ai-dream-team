# Project Pulse implementation plan

## Summary

Build a lightweight, static Project Pulse dashboard for contributors. It should make active projects, owners, status, recent activity, priority or risk, and concise contributor-friendly summaries easy to scan. The interface should use responsive project cards, clear status badges, readable spacing, and accessible visual hierarchy.

The required deliverables are `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`. Project data must be a JSON object with a top-level `projects` array; each project must include `name`, `owner`, `status`, `recentActivity`, and `priority`. The VS Code configuration must be named **Run Project Pulse Dashboard**, serve from `app/`, and open `index.html` rather than a directory listing.

This repository currently has no `app/` files or launch configuration. The README is exercise introduction, and `.vscode/tasks.json` is an unrelated folder-open task; leave it unchanged. No existing app framework, package manifest, or test harness is established, so keep the implementation static and avoid adding dependencies or tooling.

## Ordered implementation steps

1. **Agree the interface and data contract.** The Orchestrator confirms the required project fields, display of summaries and risk/priority, and stable `.dashboard` and `.project-card` CSS hooks. Decide how the page loads the JSON while being served locally. Keep the page useful and understandable if data is empty or unavailable.
2. **Produce the visual design and parallel implementation pieces.** The Designer defines and implements responsive styles using the agreed hooks. In parallel, the Coder prepares representative project records and the assigned launch configuration. These can proceed independently once the shared field names, hooks, and serving requirement are fixed.
3. **Implement the page and integrate the pieces.** The Coder builds semantic `index.html` markup that presents the agreed data fields, references the stylesheet and project data, and matches the Designer's CSS hooks. The Coder checks that the launch configuration's served URL targets `index.html` under `app/`.
4. **Validate the integrated dashboard and hand off.** Check the files and data contract, inspect responsive and accessible presentation, and exercise the named VS Code launch flow. Resolve any integration issues without changing unrelated files.

## File assignments and responsibilities

| Path | Owner | Assignment |
| --- | --- | --- |
| `app/styles.css` | **Designer** | Create the polished, responsive visual system: clear hierarchy, readable spacing and typography, project-card layout, status badges, and visible priority/risk treatment. Include deterministic `.dashboard` and `.project-card` selectors. Ensure color is not the only way status or priority is conveyed and support keyboard-visible focus if interactive elements are present. |
| `app/index.html` | **Coder** | Create the semantic dashboard document, title and page heading, link `styles.css`, and render project cards with the required project fields and short contributor-friendly summaries. Use the agreed `.dashboard` and `.project-card` hooks. Ensure project data is loaded in the served-page context rather than assuming `file://` access to JSON will work. |
| `app/project-data.json` | **Coder** | Supply representative, readable sample records in a top-level `projects` array. Every object includes `name`, `owner`, `status`, `recentActivity`, and `priority`; include the brief's short summary content as an agreed field. Keep values consistent with the labels and presentation in the page. |
| `.vscode/launch.json` | **Coder** | Add strict JSON with a predictable configuration named **Run Project Pulse Dashboard**. Set the working directory to `${workspaceFolder}/app`, serve that directory, and open `index.html` at the server URL—not the directory root. Use a serving/debug mechanism supported by the target VS Code environment; do not assume an extension, package script, or existing server task that the repository does not provide. |

**Designer scope:** owns the stylesheet and communicates the visual hierarchy, responsive behavior, accessibility decisions, and markup hooks to the Coder. Do not have both agents edit `index.html` or the same CSS file.

**Coder scope:** owns the HTML, JSON data, and launch configuration, then integrates and validates those files against the Designer's stylesheet contract. The Coder does not replace the Designer's design work with bare default styling.

## Dependencies and parallel/sequential decisions

- Step 1 is a prerequisite for both implementation branches: the data fields, summary field, CSS hooks, and local serving behavior must be agreed to prevent incompatible markup, styles, and data.
- After that contract is agreed, the Designer can work on `app/styles.css` in parallel with the Coder preparing `app/project-data.json` and `.vscode/launch.json`. These outputs do not edit overlapping files.
- The Coder's final `app/index.html` integration depends on the Designer's hooks/design contract and the agreed JSON shape. The Coder can draft it earlier, but final integration and validation must follow both branches.
- Integrated validation is sequential: it follows creation of all four deliverables. If validation exposes a mismatch, the owning agent fixes its assigned file, then the affected checks are rerun.
- Do not edit `.vscode/tasks.json`; the dashboard launcher is a separate `launch.json` deliverable.

## Edge cases

- An empty `projects` array should result in a clear empty state rather than broken markup or a blank unexplained panel.
- Missing, malformed, or unavailable project data should fail visibly and informatively; do not silently render misleading project details. JSON data must be served over the local server, since browser restrictions can prevent `fetch()` from working from a `file://` page.
- Status and priority values may be unfamiliar or differ in casing. Keep text legible and meaningful without relying on color alone; avoid a visual treatment that makes an unknown value disappear.
- Project names, owners, activity, and summaries can be long. Cards should wrap content and remain readable without horizontal page overflow at narrow viewport widths.
- Ensure adequate contrast, logical heading order, readable text sizing, and visible keyboard focus for any controls.
- A missing browser/debug extension or unavailable server command could prevent the launch configuration from running. Do not make success depend on an undocumented package, extension, port, or unrelated task.

## Validation expectations

There is no established app test harness or framework. Validate with available standard tools and a manual launch check; do not add dependencies just to validate this static deliverable.

1. **Files and contract:** confirm the four assigned paths exist; parse `app/project-data.json` with a JSON parser; check that the root is an object, `projects` is an array, and every project has `name`, `owner`, `status`, `recentActivity`, and `priority` (plus the agreed summary field).
2. **HTML and CSS:** inspect or parse `app/index.html` to confirm it is a complete document with a title, heading, stylesheet link, and project content/data integration. Confirm it uses the agreed `.dashboard` and `.project-card` hooks and references the local JSON and stylesheet correctly. Check the stylesheet contains the corresponding hooks, status/priority treatments, readable spacing, responsive behavior, and accessible contrast/focus choices. Use an available HTML parser or browser inspection if present; do not assume a parser package exists.
3. **Launch configuration:** parse `.vscode/launch.json` as strict JSON (for example, `python3 -m json.tool .vscode/launch.json`). Inspect that the configuration name is exactly **Run Project Pulse Dashboard**, its working directory resolves to `${workspaceFolder}/app`, and its serve/open behavior targets `index.html`, not a directory listing.
4. **Launch flow:** use the named VS Code configuration in the target environment. Verify the server serves files from `app/`, the browser opens the dashboard at `index.html`, the data and styles load, and no directory listing, missing-file response, or console loading error appears. If the target environment cannot run the selected browser/debug mechanism, report that limitation rather than claiming the launch was verified.
5. **Responsive and accessibility smoke check:** inspect a wide and narrow viewport; verify cards reflow without horizontal overflow, fields remain readable, status and priority are understandable without color, and any interactive controls can be reached and focused by keyboard.

## Risks and open questions

- **Launch mechanism support:** the repository does not establish a VS Code browser/debug extension or server command. Confirm the target Codespace/VS Code supports the chosen launch type and server-ready/open behavior; otherwise select a supported mechanism without modifying the unrelated folder-open task.
- **Summary field name:** the brief requires a contributor-friendly summary but does not prescribe a JSON key. Agree on one name (for example, `summary`) before the Designer/Coder integration, and keep it consistent in sample data and rendering.
- **Data rendering strategy:** decide whether the static page loads JSON with a small amount of plain JavaScript or uses another no-build approach supported by the exercise. In either case, preserve local HTTP serving and handle data-load errors explicitly.
- **Sample content:** the brief identifies the information to show but does not provide actual project records. Use clearly representative sample content rather than implying it is live team data.
- **Scope boundary:** do not introduce a framework, package/test harness, server task, or edits to `.vscode/tasks.json` unless separately authorized.
