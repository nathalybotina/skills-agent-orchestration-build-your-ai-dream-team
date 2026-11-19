# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona's lightweight, static **Project Pulse** dashboard for contributors. The dashboard should show project ownership, status, recent activity, priority/risk, and contributor-friendly summaries through a polished, accessible card layout. It must run from VS Code using **Run Project Pulse Dashboard**, serve from `app/`, and open `index.html` rather than a directory listing.

## Ordered Implementation Steps

### 1. Confirm requirements and ownership

**Owner:** Orchestrator, with Planner

- Use `.github/project-pulse-brief.md` as the source of product requirements.
- Use the existing custom agent definitions under `.github/agents/`.
- Preserve the repository's static-app scope: HTML, CSS, JSON, and VS Code launch configuration.
- Keep file ownership explicit so Designer and Coder do not overwrite each other's work.

**Dependency:** This must happen before implementation.

### 2. Establish the visual and accessibility direction

**Owner:** Designer
**Assigned file:** `app/styles.css`

Designer should define and implement:

- Dashboard information hierarchy and responsive card layout.
- Clear page title and supporting contributor-oriented context.
- Visible status badges and priority/risk treatment.
- Readable typography, spacing, contrast, focus states, and semantic visual grouping.
- Responsive behavior for narrow and wide screens.
- Required deterministic selectors: `.dashboard` and `.project-card`.
- Polished card styling using rounded corners, shadows, borders, and appropriate color contrast.

Designer should report design decisions and validation recommendations to the Orchestrator.

**Dependency:** CSS should align with the HTML structure created by Coder. The class contract should be agreed before final integration.

### 3. Define representative project data

**Owner:** Coder
**Assigned file:** `app/project-data.json`

Coder should create valid JSON with:

- A top-level `projects` array.
- Multiple representative projects.
- Every project containing `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Values that demonstrate different statuses and priority/risk levels.
- Contributor-friendly activity and summary text where appropriate.

The data contract must remain stable so the HTML rendering logic can consume it predictably.

**Dependency:** This can run in parallel with initial CSS work, but the field names must be finalized before HTML rendering is completed.

### 4. Build the dashboard page

**Owner:** Coder
**Assigned file:** `app/index.html`

Coder should implement:

- A valid, semantic HTML document.
- The exact page title and visible heading **Project Pulse**.
- References to `styles.css` and `project-data.json`.
- A dashboard container using the `.dashboard` hook.
- Visible project cards using the `project-card` class.
- Rendering of each project's name, owner, status, recent activity, priority/risk, and contributor-friendly summary when represented in the data.
- Accessible labels, heading hierarchy, meaningful status text, and non-color-only status communication.
- A deterministic approach for loading the JSON data, such as a small inline script using `fetch()`.

The page must display the dashboard UI, not a server directory listing.

**Dependencies:**

- Depends on the project data field contract from Step 3.
- Depends on the class and layout contract from Designer.
- Must be completed before final visual and browser validation.

### 5. Add the runnable preview configuration

**Owner:** Coder
**Assigned file:** `.vscode/launch.json`

Create strict JSON with:

- A configuration named **Run Project Pulse Dashboard**.
- A server command equivalent to `python3 -m http.server 5500`.
- `cwd` set to `${workspaceFolder}/app`.
- A server-ready action that opens `http://localhost:%s/index.html`.
- A launch target that serves the `app/` directory and explicitly opens `index.html`.

The configuration should use the debugger/runtime properties supported by the installed VS Code tooling.

**Dependencies:**

- Can be drafted in parallel with the app files.
- Must be tested after `app/index.html` exists.
- Must not depend on `.vscode/tasks.json`; the existing task only starts Copilot CLI.

### 6. Integrate and review

**Owner:** Orchestrator
**Files:** All four implementation files.

- Confirm Designer's selectors match Coder's markup.
- Confirm JSON fields match the rendering code.
- Confirm `styles.css` and `project-data.json` are loaded from the expected relative paths.
- Confirm launch configuration paths and URL target the dashboard frontend.
- Resolve conflicts before validation.

**Dependency:** Sequential after the parallel design, data, build, and launch work.

## File Assignments

| File | Primary owner | Responsibilities |
|---|---|---|
| `app/index.html` | Coder | Semantic dashboard structure, exact Project Pulse title, data loading, visible project cards, field rendering, accessibility |
| `app/styles.css` | Designer | Layout, responsive behavior, visual hierarchy, badges, spacing, contrast, `.dashboard`, `.project-card`, rounded cards, shadows |
| `app/project-data.json` | Coder | Valid top-level `projects` array and complete project records |
| `.vscode/launch.json` | Coder | Strict JSON launch configuration named **Run Project Pulse Dashboard**, `app/` working directory, HTTP server, `index.html` URL |

## Designer Responsibilities

- Establish the information hierarchy and visual language.
- Make status and priority understandable without relying only on color.
- Define responsive card behavior and readable spacing.
- Implement polished styles within `app/styles.css`.
- Use the required `.dashboard` and `.project-card` selectors.
- Review the integrated markup for visual and accessibility consistency.
- Report design tradeoffs and remaining visual risks.

## Coder Responsibilities

- Create and connect `app/index.html` and `app/project-data.json`.
- Implement deterministic JSON loading and rendering behavior.
- Ensure all required project fields are visible.
- Create `.vscode/launch.json` with strict JSON and the required launch behavior.
- Keep implementation simple, explicit, and testable.
- Validate JSON, inspect browser behavior, and report errors or limitations.
- Avoid modifying design-owned CSS unless explicitly reassigned.

## Dependencies

1. The brief and agent definitions establish the requirements and ownership model.
2. The JSON field contract must be stable before finalizing HTML rendering.
3. Designer's class and layout contract must be known before final CSS/HTML integration.
4. The launch configuration depends on the completed `app/` directory and `index.html`.
5. Browser validation must occur only after all app files and launch configuration are present.
6. Final handoff documentation should reference the validated files and launch name.

## Parallel Work Decisions

After requirements are confirmed, these tasks can run in parallel because their primary file scopes do not overlap:

- Designer develops `app/styles.css`.
- Coder creates `app/project-data.json`.
- Coder drafts `.vscode/launch.json`.
- Coder begins the semantic HTML skeleton in `app/index.html`.

The Orchestrator must still communicate the shared class names and data fields. Final rendering and integration remain sequential until the data and markup contracts are stable.

These tasks must remain sequential:

1. Requirements research before delegation.
2. Data-field and markup contract agreement before finalizing rendering logic.
3. HTML/data/CSS integration after parallel work.
4. Launch testing after `app/index.html` and `app/project-data.json` exist.
5. Final browser and file validation after integration.
6. Any final handoff summary after validation results are known.

## Edge Cases and Risks

- **Direct file loading:** `fetch("project-data.json")` may fail when opening `index.html` via `file://`; validation must use the configured HTTP server.
- **Missing or malformed JSON:** Display a clear user-facing fallback instead of leaving an empty dashboard or exposing an uncaught error.
- **Empty `projects` array:** Show an intentional empty-state message.
- **Incomplete project records:** Avoid rendering undefined values; provide safe fallback text or flag invalid data.
- **Status and priority values:** Use consistent labels and styles for unknown values so new data does not break the UI.
- **Server port conflicts:** Port `5500` may already be occupied; stop stale servers before testing or document the remediation.
- **Directory listing risk:** A missing `/index.html` URL or incorrect `cwd` can open a directory listing instead of the dashboard.
- **Accessibility:** Color-only badges, insufficient contrast, missing labels, and poor keyboard focus would undermine the dashboard.
- **Responsive overflow:** Long project names, activity text, or owner names must wrap without breaking cards.
- **Strict JSON:** `.vscode/launch.json` and `app/project-data.json` must not contain comments or trailing commas.
- **File ownership conflicts:** Designer and Coder should not edit the same file concurrently without an explicit handoff.

## Validation Expectations

### Static validation

- Confirm all required files exist:
  - `app/index.html`
  - `app/styles.css`
  - `app/project-data.json`
  - `.vscode/launch.json`
- Parse both JSON files with `python3 -m json.tool`.
- Confirm `index.html` contains `Project Pulse`, `styles.css`, `project-data.json`, `project-card`, `status`, `recentActivity`, and `priority`.
- Confirm `styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- Confirm the data contains a top-level `projects` array and complete required fields.
- Confirm `launch.json` contains **Run Project Pulse Dashboard**, `index.html`, the `app` working directory, and the required server command/URL.

### Browser validation

- Start **Run Project Pulse Dashboard** from VS Code Run and Debug.
- Confirm the browser opens `http://localhost:5500/index.html`.
- Confirm the first view is the Project Pulse dashboard, not a directory listing.
- Confirm multiple project cards are visible.
- Confirm status, recent activity, priority, owner, and project names are readable.
- Test narrow and wide viewports.
- Check keyboard focus, readable contrast, and fallback behavior for loading errors.
- Stop the preview server after testing.

### Repository validation

- Review the integrated diff for accidental files or scope violations.
- Run the repository's existing validation script where applicable.
- Do not stage, commit, or push as part of implementation planning; git operations remain learner-controlled through Copilot CLI prompts.

## Open Questions

- Should project summaries be stored in a separate `summary` field, or derived from existing fields? The brief requests summaries but does not list `summary` among the required JSON fields.
- Should the dashboard be entirely static HTML with inline JavaScript, or should a separate script file be introduced? The brief specifies only three app files, so inline rendering is the least disruptive option.
- Is the Python debugger extension guaranteed in every target Codespace image? The launch configuration should be tested in the actual Codespace.
- Should unknown statuses and priorities be normalized to a fixed vocabulary? A small documented set such as Active, At Risk, Complete, High, Medium, and Low would make styling deterministic.
- Should the launch configuration include a pre-launch task or integrated terminal setting? Additional settings should be avoided unless needed for reliable preview behavior.
