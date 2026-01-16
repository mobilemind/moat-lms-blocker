# Moat LMS Blocker - uBlock Origin Filter Lists

[![Lint Filter Lists](https://github.com/mobilemind/moat-lms-blocker/actions/workflows/lint-filters.yml/badge.svg)](https://github.com/mobilemind/moat-lms-blocker/actions/workflows/lint-filters.yml)
[![Lint JSON Files](https://github.com/mobilemind/moat-lms-blocker/actions/workflows/lint-json.yml/badge.svg)](https://github.com/mobilemind/moat-lms-blocker/actions/workflows/lint-json.yml)
[![Lint Markdown Files](https://github.com/mobilemind/moat-lms-blocker/actions/workflows/lint-markdown.yml/badge.svg)](https://github.com/mobilemind/moat-lms-blocker/actions/workflows/lint-markdown.yml)
[![Lint YAML Files](https://github.com/mobilemind/moat-lms-blocker/actions/workflows/lint-yaml.yml/badge.svg)](https://github.com/mobilemind/moat-lms-blocker/actions/workflows/lint-yaml.yml)

Filter lists for [uBlock Origin](https://ublockorigin.com/) that block
communications between eLearning content and Learning Management Systems (LMS)
or Learning Record Stores (LRS).

Useful for debugging eLearning content and LMS integrations by preventing
tracking data from being sent to the server. For example, block xAPI to test
fallback to SCORM, or block SCORM 1.2 and SCORM 2004 to test fallback to AICC
HACP.

## Quick Subscribe Links

Click to subscribe in uBlock Origin:

| Filter List | Description | Subscribe |
|-------------|-------------|-----------|
| **SCORM 1.2** | Blocks SCORM 1.2 runtime communications | [Subscribe](https://subscribe.adblockplus.org/?location=https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-scorm12.txt&title=Moat%20SCORM%201.2%20Blocker) |
| **SCORM 2004** | Blocks SCORM 2004 runtime communications | [Subscribe](https://subscribe.adblockplus.org/?location=https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-scorm2004.txt&title=Moat%20SCORM%202004%20Blocker) |
| **xAPI** | Blocks Experience API (Tin Can) communications | [Subscribe](https://subscribe.adblockplus.org/?location=https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-xapi.txt&title=Moat%20xAPI%20Blocker) |
| **cmi5** | Blocks cmi5 launch parameters and communications | [Subscribe](https://subscribe.adblockplus.org/?location=https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-cmi5.txt&title=Moat%20cmi5%20Blocker) |
| **AICC** | Blocks AICC/HACP protocol communications | [Subscribe](https://subscribe.adblockplus.org/?location=https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-aicc.txt&title=Moat%20AICC%20Blocker) |

## Manual Installation

1. Open uBlock Origin dashboard (click the uBO icon, then the gear icon)
2. Go to the **Filter lists** tab
3. Scroll to the bottom and check **Import** under "Custom"
4. Paste one or more of these URLs:

```text
https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-scorm12.txt
https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-scorm2004.txt
https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-xapi.txt
https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-cmi5.txt
https://raw.githubusercontent.com/mobilemind/moat-lms-blocker/main/filters/moat-aicc.txt
```

5. Click **Apply changes**

## Toggling Filter Lists

Each subscribed filter list appears with a checkbox in the uBlock Origin Filter
lists tab. Uncheck to disable, check to enable. Click **Apply changes** after
toggling.

## Browser Compatibility

| Browser | Support |
|---------|---------|
| **Firefox** | Full support |
| **Edge** | Full support |
| **Chrome** | Limited (use [uBlock Origin Lite](https://chromewebstore.google.com/detail/ublock-origin-lite/ddkjiahejlhfcafbddmgiahcphecmpfh)) |
| **Brave** | Full support |
| **Opera** | Full support |

Note: Chrome's Manifest V3 restrictions limit uBlock Origin functionality.
Firefox and Edge provide the best experience.

## What Gets Blocked

### SCORM 1.2 & 2004

- Runtime library scripts (scormdriver.js, APIWrapper.js, etc.)
- LMS API communication files
- Common authoring tool output (Articulate, Captivate, Lectora)

### xAPI (Experience API)

- Statement endpoints (`/xAPI/statements/`)
- Agent and activity endpoints
- State and profile endpoints
- TinCanJS libraries
- Common LRS paths (`/tcapi/`, `/lrs/`)

### cmi5

- Launch parameters (fetch, registration, activityId)
- Course structure manifests (cmi5.xml, tincan.xml)
- Authentication endpoints
- cmi5 client libraries

### AICC

- HACP protocol commands (getparam, putparam, exitau, etc.)
- AICC communication files

## Testing & Debugging

See [TESTING.md](TESTING.md) for:

- Verifying blockers are working
- Using SCORM Cloud as a test environment
- Debugging scenarios (protocol fallback, offline behavior)
- Browser Developer Tools tips

## Contributing

Found a pattern that should be blocked? See [CONTRIBUTING.md](CONTRIBUTING.md) for
guidelines on reporting issues and submitting pull requests.

Filter lists are validated using [AGLint](https://github.com/AdguardTeam/AGLint), a
universal adblock filter list linter that checks for syntax errors and validates
uBlock Origin filter syntax.

## License

MIT License - See [LICENSE](LICENSE) file.
