# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona’s Project Pulse dashboard as a small static app that helps contributors see active projects, owners, status, recent activity, priority or risk, and a short summary. The page should render project cards from JSON, use a polished and accessible responsive layout, and be previewable through the **Run Project Pulse Dashboard** VS Code launch configuration.

## Repository facts and assumptions

**Repository facts**
- `.github/project-pulse-brief.md` defines the required app files, project fields, and launch behavior.
- `.github/agents/` contains the Planner, Designer, Coder, and Orchestrator agent definitions. The Coder may create `.vscode/launch.json` when assigned.
- The repository has no existing `app/` directory, app framework, package manifest, or app-specific test suite.
- `.github/workflows/3-step.yml` checks the expected app files, required content, and JSON syntax. These checks do not prove the page renders correctly in a browser.
- `.vscode/tasks.json` is for the exercise’s Copilot CLI terminal; keep the dashboard preview configuration separate in `.vscode/launch.json`.

**Assumptions to confirm during implementation**
- Keep the implementation framework-free and avoid adding dependencies or files beyond the requested app files.
- Load JSON with browser `fetch` and render it with vanilla JavaScript in `app/index.html`. This requires serving the app over HTTP; opening the HTML directly with `file://` is not a supported preview path.
- Add a `summary` field to each project in addition to the five required fields, since the brief asks for a contributor-friendly summary.
- Use understandable, consistent status and priority labels, with styling fallbacks for unrecognized values.

## Ordered implementation steps

### 1. Agree on the integration contract

**Owner:** Orchestrator, with input from Designer and Coder  
**Files:** No implementation files

Before work splits, agree on the card’s semantic structure and stable CSS hooks, the JSON field names, and the behavior for loading, empty, and error states. The contract should include the required fields `name`, `owner`, `status`, `recentActivity`, and `priority`, plus `summary`.

**Depends on:** None.  
**Output:** Shared contracts for parallel work; no file changes.

### 2. Create the project data and launch configuration

**Owner:** Coder  
**Files:** `app/project-data.json`, `.vscode/launch.json`

Create a top-level `projects` array with multiple representative projects. Each project should include the required fields and a short `summary`.

Create strict JSON for a VS Code configuration named **Run Project Pulse Dashboard**. Configure it to run `python3 -m http.server 5500` with `cwd` set to `${workspaceFolder}/app`, and open `http://localhost:%s/index.html` when the server is ready. This ensures the browser opens the dashboard page rather than a directory listing.

**Depends on:** Step 1’s data and launch contracts.  
**Can run in parallel with:** Step 3.

### 3. Design and implement the dashboard styling

**Owner:** Designer  
**Files:** `app/styles.css`

Create a polished, responsive visual system for the agreed page structure: clear hierarchy, readable spacing and typography, visible project cards and status badges, and distinct but accessible priority treatment. Include the required `.dashboard` and `.project-card` selectors, along with `border-radius` and `box-shadow`. Ensure layouts adapt to narrow screens and that status or priority is not communicated through color alone.

**Depends on:** Step 1’s markup and selector contract.  
**Can run in parallel with:** Step 2.

### 4. Build and integrate the dashboard page

**Owner:** Coder  
**Files:** `app/index.html`

Create a page with the exact title **Project Pulse**, link `styles.css`, and load `project-data.json`. Render a `.project-card` for each project, showing its name, owner, status, recent activity, priority, and summary. Implement clear loading, empty-data, and data-load error states. Use semantic HTML and insert data as text rather than interpreting it as markup.

**Depends on:** Steps 1–3; the page must follow the agreed data and CSS contracts.  
**Must run after:** The contracts from Step 1 and the outputs of Steps 2 and 3 are available.

### 5. Integrate and validate

**Owner:** Orchestrator coordinates; Coder fixes implementation issues; Designer advises on visual or accessibility issues  
**Files:** As needed within each specialist’s assigned files

Review the complete app against the requirements below, resolve integration issues, and verify the configured preview in VS Code.

**Depends on:** Steps 2–4.

## Responsibilities and file assignments

| Agent | Responsibilities | Assigned files |
|---|---|---|
| **Planner** | Produce this plan, identify constraints, dependencies, parallel work, risks, and validation expectations. | `docs/project-pulse-plan.md` |
| **Orchestrator** | Coordinate the shared contract, assign bounded scopes, manage phase order, resolve integration issues, and confirm the final handoff. Does not implement the app. | No app implementation files |
| **Designer** | Define information hierarchy, responsive layout, visual treatment, and accessibility; implement the agreed styles. | `app/styles.css` |
| **Coder** | Implement the JSON data, dashboard page and rendering behavior, and runnable preview configuration; validate assigned work. | `app/project-data.json`, `app/index.html`, `.vscode/launch.json` |

Keep ownership non-overlapping: Designer owns CSS; Coder owns HTML, data, and launch configuration. Any changes that cross these boundaries should be coordinated by the Orchestrator.

## Dependencies and parallel work

- Step 1 precedes implementation because the HTML, CSS, and JSON must agree on selectors and field names.
- Steps 2 and 3 can run in parallel after Step 1: the data and launch configuration do not depend on CSS, and CSS can be built against the agreed markup contract.
- Step 4 follows Steps 2 and 3 because the page integrates both their outputs.
- Step 5 is sequential after integration. The app must be served from `app/` to verify JSON loading and the browser launch target.

## Edge cases and risks

- **Fetch from `file://`:** Browsers may block JSON requests when the HTML is opened directly. Use the launch configuration’s HTTP server for preview.
- **Missing, invalid, or empty data:** Show a helpful error or empty state instead of leaving a blank dashboard.
- **Unexpected status or priority labels:** Keep badges readable and styled with a safe fallback; do not rely on a fixed set of labels for rendering.
- **Long text or many projects:** Ensure summaries and recent activity wrap without breaking the card layout; keep the grid usable at mobile widths.
- **Untrusted text content:** Render project values as text, not HTML, to avoid interpreting data as markup.
- **Accessibility:** Provide meaningful headings and labels, keyboard-usable controls if any are added, sufficient contrast, and non-color status cues.
- **Launch failures:** Port 5500 may already be in use, or server-ready output matching may differ. Confirm the configuration starts the server and opens the explicit `index.html` URL.
- **Validation limits:** Automated workflow checks verify required files, phrases, and JSON syntax, but do not validate browser rendering, accessibility, or launch behavior.

## Validation expectations

1. Confirm all four assigned deliverables exist: `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`.
2. Parse `app/project-data.json` and `.vscode/launch.json` as JSON; verify every project has `name`, `owner`, `status`, `recentActivity`, `priority`, and `summary`.
3. Check that `app/index.html` has the exact **Project Pulse** title, references both `styles.css` and `project-data.json`, and renders project cards with status, activity, priority, and summary.
4. Check that `app/styles.css` includes `.dashboard`, `.project-card`, rounded card styling, shadows, and responsive behavior.
5. Run the repository’s Step 3 workflow checks where available; they cover required files, key content, and JSON syntax.
6. In VS Code, select **Run Project Pulse Dashboard** and verify the browser opens `index.html`, displays the dashboard rather than a directory listing, and shows multiple populated cards.
7. Verify at least one narrow viewport and check loading, empty, and failed-data behavior. Address any integration or accessibility issues found before handoff.
