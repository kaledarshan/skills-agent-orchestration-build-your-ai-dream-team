# Project Pulse implementation plan

## Summary

Create a lightweight, static Project Pulse dashboard that helps contributors
quickly understand the team's projects: who owns each one, its status, recent
activity, priority or risk, and a short contributor-friendly summary. The
finished page should have a polished, accessible, responsive card-based layout
and open as the dashboard—not a directory listing—through the VS Code launch
configuration.

The implementation is limited to these four deliverables:

- `app/index.html`
- `app/styles.css`
- `app/project-data.json`
- `.vscode/launch.json`

Use plain static HTML, CSS, JSON, and browser-supported JavaScript as needed.
There is no existing app scaffold, package manager, or test framework, so do
not add a framework or build tooling. Do not add interactions or product
features beyond displaying project data and supporting the specified preview.

## Roles and file ownership

| Role | Responsibility |
|---|---|
| **Orchestrator** | Owns the overall sequence, assigns the file scopes below, coordinates Designer and Coder, integrates their work, and checks the final deliverables against this plan. |
| **Designer** | Advises on information hierarchy, responsive layout, accessible markup and styling, card composition, status badges, priority treatment, spacing, contrast, and typography. Focuses on the design brief and `.dashboard` / `.project-card` styling hooks; does not own launch configuration or data authoring. |
| **Coder** | Implements the assigned static files and launch configuration, keeps behavior deterministic and testable, handles data-loading/rendering failures explicitly, and runs the available validation. |

| File | Owner | Scope and acceptance notes |
|---|---|---|
| `app/project-data.json` | Coder; Designer may advise on readable content | Provide a valid JSON object with a top-level `projects` array. Include multiple representative project records. Each record has non-empty `name`, `owner`, `status`, `recentActivity`, and `priority` values plus a short contributor-friendly `summary`. Keep the data small and illustrative; do not invent a schema beyond these fields. |
| `app/index.html` | Coder, incorporating Designer's recommendations | Build the dashboard page with the exact document title and visible heading `Project Pulse`. Reference `styles.css` and load/reference `project-data.json`. Render the records as visible project cards using the `project-card` class. Each card shows all required fields, including summary. Use semantic structure, meaningful heading order, and accessible status/priority text rather than relying on color alone. |
| `app/styles.css` | Coder, following Designer's visual direction | Provide the `.dashboard` and `.project-card` selectors. Deliver the polished card-based appearance with clear typography and hierarchy, readable spacing, contrast, rounded corners (`border-radius`), shadows (`box-shadow`), status badges, and a responsive layout that remains usable on narrow screens. |
| `.vscode/launch.json` | Coder | Create strict JSON without comments. Add the launch configuration named `Run Project Pulse Dashboard`; set its working directory to `${workspaceFolder}/app`, run `python3 -m http.server 5500`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`. Ensure the server serves from `app/` and the browser opens `index.html`, not the server root or directory listing. |

## Phases and ordered steps

### Phase 1 — Confirm the contract and design direction

1. Orchestrator shares this plan and the Project Pulse brief with Designer and
   Coder.
2. Designer confirms the card information order, visual hierarchy, status and
   priority presentation, accessible color/contrast approach, and responsive
   behavior.
3. Coder confirms the data fields, page-to-data loading approach, and exact
   launch settings before implementation.

**Outputs:** agreed field contract and design direction. No files need to be
changed in this phase.

### Phase 2 — Create data and implement the dashboard

1. Coder creates `app/project-data.json` with the top-level `projects` array
   and representative records containing every required field and a short
   `summary`.
2. Coder creates `app/index.html` and `app/styles.css` to display the project
   data as styled, responsive, accessible cards.
3. Coder ensures the data-loading/rendering path handles an empty project list
   and reports a useful visible error if the JSON cannot be loaded or parsed;
   a failed load must not leave a misleading blank dashboard.
4. Designer reviews the delivered page against the visual and accessibility
   expectations and reports focused corrections to Coder.
5. Coder incorporates any in-scope review corrections.

### Phase 3 — Add the runnable preview

1. Coder creates `.vscode/launch.json` with strict JSON and the exact command,
   working directory, launch name, and browser URL in the file assignment
   above.
2. Orchestrator checks that the launch target is consistent with the app
   directory and page references.

### Phase 4 — Validate and hand off

1. Run JSON parsing and static contract checks on all deliverables.
2. Run the VS Code launch configuration and confirm the browser shows Project
   Pulse cards at the configured URL.
3. Report files completed, checks performed, any limitations, and any
   unresolved assumptions. Do not stage, commit, or push as part of this
   implementation.

## Dependencies and parallel-work decisions

- The data shape is a prerequisite for the page's rendering logic. Agree on
  the required fields in Phase 1; Coder may author the JSON and page/CSS in
  parallel only after that contract is fixed.
- `app/index.html` and `app/styles.css` can be implemented in parallel after
  Designer establishes the visual direction and the shared class/semantic
  hooks (`.dashboard`, `.project-card`, and status badge treatment). Coder
  coordinates any markup/style contract changes.
- `app/project-data.json` can be authored independently of layout once its
  schema is fixed. It may proceed in parallel with HTML/CSS, but final
  rendering validation depends on all three app files being present.
- `.vscode/launch.json` is technically independent of the dashboard markup,
  so it may be authored in parallel once the required command, `cwd`, port,
  and URL are fixed. Full launch/browser validation must wait until the app
  files exist.
- Integration and smoke testing are sequential: finish file integration,
  parse/check the files, then launch and inspect the page. If a validation
  issue reveals a contract mismatch, fix the owning file and repeat the
  affected checks.

## Edge cases and implementation constraints

- **No projects:** show a clear empty-state message inside the dashboard rather
  than rendering no content.
- **Data fetch or parse failure:** show a readable error state and avoid
  silently failing. Use no framework or external service.
- **Incomplete or unfamiliar values:** show the supplied text clearly; do not
  assume a fixed list of statuses or priorities. Status and priority must
  remain understandable without color.
- **Long names, summaries, or activity text:** allow wrapping and preserve
  card readability at narrow viewport sizes.
- **Accessibility:** use semantic page regions and headings, sufficient
  contrast, visible text labels, and responsive layouts that do not require
  horizontal scrolling at typical mobile widths.
- **Launch behavior:** use the exact workspace-relative app `cwd` and open
  `index.html` explicitly to avoid a directory listing. Keep the port and URL
  deterministic as specified.
- **Scope:** deliver a static dashboard and preview configuration only. Do
  not add unsupported filtering, editing, persistence, API integrations,
  dependency manifests, or testing frameworks.

## Validation expectations

1. **JSON syntax**
   - Run `python3 -m json.tool app/project-data.json`.
   - Run `python3 -m json.tool .vscode/launch.json`.
   - Confirm the data document is an object with a top-level `projects` array
     and that every record contains `name`, `owner`, `status`,
     `recentActivity`, `priority`, and `summary`.
2. **Static contract checks**
   - Confirm `app/index.html` has the title and visible `Project Pulse`
     heading, references `styles.css` and `project-data.json`, and renders
     visible cards with the `project-card` class.
   - Confirm each card presents the project name, owner, status,
     recentActivity, priority, and summary.
   - Confirm `app/styles.css` contains `.dashboard`, `.project-card`,
     responsive layout rules, `border-radius`, and `box-shadow`, and gives
     status/priority readable text treatment.
   - Confirm `.vscode/launch.json` is strict JSON and contains the exact
     launch name `Run Project Pulse Dashboard`, `cwd` `${workspaceFolder}/app`,
     command `python3 -m http.server 5500`, and
     `http://localhost:%s/index.html` server-ready URL.
   - These checks may be performed with existing command-line tools or
     concise ad hoc checks; no validation files or new framework are required.
3. **Launch/browser smoke test**
   - In VS Code, select **Run Project Pulse Dashboard** and start it.
   - Verify the server runs from `app/`, the browser opens
     `http://localhost:5500/index.html`, and the rendered page shows the
     Project Pulse dashboard with multiple cards and all project fields.
   - Check a narrow viewport and keyboard/screen-reader-friendly semantic
     structure; confirm the page does not rely on color alone to convey
     status or priority.
   - Stop the preview server after testing.
4. **Existing exercise validator caveat**
   - `scripts/validate-exercise.sh` validates the exercise repository, not
     dashboard runtime behavior. It also explicitly expects learner answer
     files such as `docs/project-pulse-plan.md`, the app files, and
     `.vscode/launch.json` to remain untracked in the template. Running it in
     the learner workflow is appropriate while those outputs are untracked;
     do not treat its tracking check as a dashboard test or change the
     validator to accommodate this plan. Its existing checks should otherwise
     remain passing.

## Open questions and assumptions

- **Summary field:** the brief requires a short contributor-friendly summary,
  although its enumerated JSON keys omit `summary`. This plan treats `summary`
  as an additional required project field and requires it to appear in each
  card.
- **Representative content:** the brief does not prescribe real project
  names, owners, statuses, activities, or priorities. Use clearly illustrative
  sample data and avoid implying it is live or authoritative.
- **Status/priority vocabulary:** no fixed enum or scale is specified. Use
  concise, human-readable example values and style them without assuming
  additional behaviors.
- **Browser support mechanism:** use the VS Code launch configuration and
  `serverReadyAction` URL specified in the brief/step instructions; do not
  replace it with another launcher or add a package-based server.
- **Runtime test tooling:** no app test framework exists. JSON parsing, static
  contract checks, and a manual browser smoke test are the appropriate
  validation for this static exercise.
