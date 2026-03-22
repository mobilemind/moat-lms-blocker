# Copilot Instructions — Moat LMS Blocker

## Project Overview

Moat LMS Blocker is a collection of [uBlock Origin](https://ublockorigin.com/) filter
lists that block communications between eLearning content and Learning Management
Systems (LMS) or Learning Record Stores (LRS). It is used by developers and QA
engineers to debug eLearning integrations — for example, blocking xAPI to test SCORM
fallback, or blocking SCORM to observe offline behavior.

Five filter files live in `filters/`, one per protocol:

| File | Blocks |
|------|--------|
| `moat-scorm12.txt` | SCORM 1.2 runtime scripts and LMS API endpoints |
| `moat-scorm2004.txt` | SCORM 2004 runtime scripts and LMS API endpoints |
| `moat-xapi.txt` | xAPI/Tin Can statement endpoints, LRS paths, TinCanJS library |
| `moat-cmi5.txt` | cmi5 launch parameters, course manifests, auth endpoints |
| `moat-aicc.txt` | AICC/HACP protocol commands and communication files |

## Domain Knowledge

**Protocol genealogy**: AICC → SCORM 1.2 → SCORM 2004 → xAPI (Tin Can) → cmi5
(cmi5 is an xAPI profile; all cmi5 traffic is xAPI but the reverse is not true)

**Key authoring tools** whose runtime scripts and URL patterns appear in filter rules:

- Articulate (Storyline, Rise 360) — widely deployed; Rustici Engine under the hood
- Adobe Captivate (Classic and New)
- Lectora / ELB Learning
- dominKnow ONE / Convey
- iSpring

**Key LMS and LRS platforms**:

- Moodle (open-source; SCORM module at `/mod/scorm/`, xAPI via web services)
- Canvas by Instructure
- SCORM Cloud / Rustici Engine — widely OEM'd; patterns like `cloud.scorm.com` and
  `RusticiEngine/` are vendor-neutral and high-value across many LMS deployments
- Cornerstone OnDemand, Docebo, SAP SuccessFactors, Totara, Absorb LMS

**scorm-again** is the most common open-source SCORM runtime in modern web courses;
its script names are `scorm-again.js` and `scorm-again.min.js`.

## Filter File Format

Every filter file begins with a required metadata header:

```text
! Title: Moat <Protocol> Blocker
! Description: ...
! Homepage: https://github.com/mobilemind/moat-lms-blocker
! License: MIT
! Last modified: YYYY-MM-DD
! Expires: 30 days
```

- **`! Last modified:`** MUST be updated whenever a filter file changes.
- **`Expires: 30 days`** is intentional — do not suggest changing it.

**uBlock Origin filter syntax:**

| Pattern | Meaning |
|---------|---------|
| `/path\.js$/$script,1p` | Block a first-party script whose URL matches the regex |
| `\|\|domain.tld^` | Block all requests to a domain |
| `\|\|*/path/$xhr,document` | Block XHR/document requests matching the path on any host |
| `$script` | Resource type: JavaScript file |
| `$xhr` | Resource type: XMLHttpRequest / fetch |
| `$document` | Resource type: main frame document |
| `1p` | First-party flag: apply only when origin matches the page domain |

**`1p` flag rules** — violations are a common review error:

- Use `1p` only on `$script` patterns for scripts served from the same domain as the
  page (typical for LMS-hosted SCORM runtime scripts like `scormdriver.js`).
- Do **not** add `1p` to `||domain^` patterns or `$xhr,document` patterns.

**Syntax is validated by AGLint** (`npm run lint:filters`). AGLint must pass — CI
enforces this on every PR.

## Version Currency

This project intentionally tracks the latest stable releases. Do not flag versions of
GitHub Actions (e.g., `actions/checkout`), Node.js, npm, or AGLint as
"potentially non-existent" or "unverified." Trust the versions in `.nvmrc`,
`package.json` engines, and workflow files.

## PR Review Guidelines

### Filter changes (most common PR type)

- **`! Last modified:` updated?** Required in every changed `.txt` file.
- **Pattern precision**: Patterns must not be overly broad (e.g., bare `/lms/` would
  match unrelated sites). Require path anchors or domain context.
- **`1p` flag correct?** See `1p` flag rules above.
- **Resource type flags correct?** Use `$script` for JS files; `$xhr,document` for
  tracking endpoints and API calls.
- **Coverage breadth**: If a pattern targets one authoring tool (e.g., Lectora),
  check whether Articulate, Captivate, dominKnow, or iSpring produce similar patterns
  and suggest adding them.
- **Protocol placement**: xAPI endpoint patterns belong in `moat-xapi.txt`; cmi5
  launch params in `moat-cmi5.txt`; etc. Flag misplaced rules.
- **AGLint**: Confirm `npm run lint:filters` passes. This is required.

### Dependabot PRs (npm / GitHub Actions)

- Patch/minor npm bumps for linting tools are routine — verify CI passes.
- Major version bumps warrant checking changelogs for breaking changes.
- Actions version bumps: verify the updated workflow still follows project conventions
  (minimal permissions, `pull_request` trigger, existing formatting style).

### Documentation PRs

- Markdown must pass `npm run lint:markdown`.
- `raw.githubusercontent.com` filter subscription URLs must match actual file paths.

## Issue Evaluation

### Bug reports

Required before acting on a bug report:

- Which filter list(s) are subscribed (SCORM 1.2, SCORM 2004, xAPI, cmi5, or AICC)
- Browser name and uBlock Origin version
- Steps to reproduce (what content, what LMS, what action triggers the issue)

If steps to reproduce are absent, request them. If the blocked pattern is already
covered, verify the user has subscribed correctly and uBO is enabled on that domain.

**Template gaps to note on issues that omit key fields:**

- The bug report template does not ask for uBlock Origin version — suggest the reporter
  add it (e.g., "uBlock Origin 1.x.y").
- The filter list "Last modified" date or subscription URL helps diagnose stale caches.
- Note whether the issue appeared after a browser or extension update.

### Feature requests

A complete feature request includes:

- Which protocol is involved
- What authoring tool or LMS produced the unblocked traffic
- A sample request URL or network pattern from browser DevTools Network tab

**Template gaps to suggest on feature requests that lack key fields:**

- Protocol selection is absent from the feature request template (unlike bug reports).
  Suggest the reporter specify the protocol.
- Authoring tool name and version are not asked for — request them.
- A sample request URL or network pattern significantly speeds up implementation.

If a feature request targets a pattern more logically placed in a different protocol
file than the reporter expects, cross-reference it.

### Template improvement suggestions (raise on relevant issues)

When reviewing issues, proactively suggest these additions to reporters so future
issues are more actionable:

- **Bug reports**: add uBlock Origin version; add subscription URL or filter list
  "Last modified" date; note whether the issue appeared after a software update.
- **Feature requests**: specify protocol; name the authoring tool and version; include
  a sample request URL or DevTools Network entry.

## GitHub Actions / Workflow Conventions

- All workflow jobs declare `permissions: contents: read` (minimal permissions).
- Use `pull_request` trigger — **not** `pull_request_target`. This is intentional for
  security on a public repository.
- Actions pinned to major version tags (`@v6`, `@v1`). Dependabot keeps them current.
  Do **not** suggest pinning to commit SHAs.
- New workflows follow the pattern of existing ones: `ubuntu-latest`, Node.js 24,
  `npm ci --ignore-scripts`, `permissions: contents: read` at the job level.
- YAML validated by Prettier (`npm run lint:yaml`). No yamllint is used.

## Shell Scripts

All shell scripts (`.sh` files) must be POSIX-compliant:

- `#!/bin/sh` shebang — not `#!/bin/bash`
- Must pass `shellcheck -s sh`
- No `local`, `[[`, arrays, brace expansion, or process substitution
- Always quote variables: `"$var"` not `$var`
- Use `command -v` instead of `which`; use `$(...)` instead of backticks
