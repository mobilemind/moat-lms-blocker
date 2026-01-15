# Testing Guide

This guide helps you verify the filter lists are working and use them to debug your eLearning content.

## Quick Verification

1. Install [uBlock Origin](https://ublockorigin.com/) in Firefox or Edge
2. Subscribe to one or more filter lists (see [README](README.md))
3. Open browser Developer Tools (F12) → Network tab
4. Launch your eLearning content from an LMS
5. Look for blocked requests (shown as failed/cancelled in Network tab)

## Test Environment: SCORM Cloud

[SCORM Cloud](https://cloud.scorm.com) offers a free trial and is ideal for testing.

### Setup

1. Sign up at https://cloud.scorm.com (free trial)
2. Upload your content package (SCORM, xAPI, cmi5, or AICC)
3. Enable the appropriate Moat filter list(s) in uBlock Origin
4. Launch the content from SCORM Cloud

### What to Observe

**With blockers disabled:**

- Content loads and communicates with LMS
- SCORM Cloud shows progress, scores, completion status
- Network tab shows successful API calls

**With blockers enabled:**

- Content still loads and renders
- LMS communication is blocked (requests fail in Network tab)
- SCORM Cloud shows no data received or timeout errors
- Content may show "LMS not found" or similar errors (expected)

## Sample Content for Testing

### Golf Examples (Official SCORM Samples)

Download from: https://scorm.com/scorm-explained/technical-scorm/golf-examples/

| Package | Protocol | Use With |
|---------|----------|----------|
| RuntimeBasicCalls_SCORM12.zip | SCORM 1.2 | moat-scorm12.txt |
| RuntimeBasicCalls_SCORM2004_3rdEdition.zip | SCORM 2004 | moat-scorm2004.txt |

### Your Own Content

Test content exported from common authoring tools:

| Authoring Tool | Exports To | Filter List |
|----------------|------------|-------------|
| Articulate Storyline/Rise | SCORM 1.2, SCORM 2004, xAPI | moat-scorm12/2004.txt, moat-xapi.txt |
| Adobe Captivate | SCORM 1.2, SCORM 2004 | moat-scorm12/2004.txt |
| Lectora | SCORM 1.2, SCORM 2004, AICC | moat-scorm12/2004.txt, moat-aicc.txt |
| iSpring | SCORM 1.2, SCORM 2004, xAPI | moat-scorm12/2004.txt, moat-xapi.txt |

## Debugging Scenarios

### Scenario 1: Test Protocol Fallback

Many content packages support multiple protocols. Use blockers to test fallback behavior:

1. Enable `moat-xapi.txt` to block xAPI
2. Launch content that supports both xAPI and SCORM
3. Verify content falls back to SCORM communication
4. Check Network tab to confirm xAPI calls blocked, SCORM calls succeed

### Scenario 2: Isolate LMS Communication Issues

If content works locally but fails in your LMS:

1. Test content in SCORM Cloud with blockers **disabled** (baseline)
2. If it works in SCORM Cloud, the issue is likely your LMS configuration
3. Enable blockers and test locally to develop without LMS interference

### Scenario 3: Test Offline/Disconnected Behavior

See how content behaves when it can't reach the LMS:

1. Enable all relevant blockers
2. Launch content and interact with it
3. Observe error handling, data persistence, user messaging
4. Useful for mobile/offline learning scenarios

## Using Browser Developer Tools

### Network Tab

1. Open Developer Tools (F12)
2. Go to Network tab
3. Filter by "XHR" or "JS" to focus on relevant requests
4. Blocked requests show as failed (red) or cancelled

**Look for these patterns when blockers are active:**

| Protocol | Blocked Requests |
|----------|------------------|
| SCORM | `scormdriver.js`, `APIWrapper.js`, files in `/lms/` |
| xAPI | `/xAPI/statements`, `/xAPI/activities`, `/tcapi/` |
| cmi5 | `cmi5.xml`, `/auth-token`, requests with `fetch=` parameter |
| AICC | Requests with `command=getparam`, `aicc_url=` parameter |

### Console Tab

Watch for JavaScript errors when LMS communication is blocked:
- "API not found"
- "LMSInitialize failed"
- "Unable to connect to LRS"

These errors are expected when blockers are working correctly.

## Toggling Filter Lists

To quickly enable/disable blockers during testing:

1. Click uBlock Origin icon in browser toolbar
2. Click the gear icon to open Dashboard
3. Go to "Filter lists" tab
4. Check/uncheck filter lists under "Custom"
5. Click "Apply changes"

Alternatively, temporarily disable uBlock Origin entirely by clicking the power icon.

## Troubleshooting

### Content doesn't load at all

The blockers should only block LMS communication, not content. If content fails to load:
- Disable blockers and verify content loads
- Check if content requires LMS APIs to initialize (some poorly-designed content does)
- Check Console tab for unrelated JavaScript errors

### Blockers don't seem to work

- Verify filter lists are enabled (checked) in uBlock Origin dashboard
- Hard refresh the page (Ctrl+Shift+R / Cmd+Shift+R)
- Check that you're using the correct filter list for your content's protocol
- Verify uBlock Origin is enabled for the site (check the power icon)

### Requests still getting through

Some patterns may not be covered. Check the Network tab for the specific URLs and consider:
- Opening an issue on the [GitHub repo](https://github.com/mobilemind/moat-lms-blocker/issues)
- Adding custom rules in uBlock Origin's "My filters" tab
