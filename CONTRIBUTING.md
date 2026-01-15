# Contributing to Moat LMS Blocker

Thank you for your interest in improving these filter lists. Contributions
help make eLearning debugging easier for everyone.

## Reporting Issues

### Requests aren't being blocked

If LMS/LRS communication is getting through when it should be blocked:

1. Open an issue with:
   - The protocol (SCORM 1.2, SCORM 2004, xAPI, cmi5, AICC)
   - The URL pattern that should be blocked (from browser Network tab)
   - The authoring tool or LMS if known

2. Example issue title: "xAPI statements to /TinCan/ not blocked"

### False positives (legitimate content blocked)

If the filter lists are blocking content that should load:

1. Open an issue with:
   - Which filter list is causing the problem
   - The URL being incorrectly blocked
   - What the resource is (e.g., "course navigation script")

2. Example issue title: "moat-scorm12.txt blocks unrelated API.js file"

## Contributing Filter Rules

### Before submitting

1. Test your rule with uBlock Origin to verify it works
2. Ensure the rule doesn't cause false positives on common websites
3. Use the most specific pattern possible to avoid over-blocking

### Filter rule format

Follow uBlock Origin's [static filter syntax](https://github.com/gorhill/uBlock/wiki/Static-filter-syntax).

**For scripts (first-party only):**
```
/pattern\.js$script,1p
```

**For API endpoints:**
```
||*/endpoint/$xhr,document
```

**For query parameters:**
```
/[&?]parameter=/$xhr,document
```

### Submitting a Pull Request

1. Fork the repository
2. Edit the appropriate filter file in `filters/`
3. Update the `! Last modified:` date in the file header
4. Submit a PR with:
   - Description of what the rule blocks
   - Why it's needed (authoring tool, LMS, or protocol that uses this pattern)
   - How you tested it

### Example PR description

```
Add rule to block Acme LMS xAPI endpoint

- Adds `/acme-lrs/` pattern to moat-xapi.txt
- Acme LMS uses this non-standard path for xAPI statements
- Tested with Acme LMS trial account and sample xAPI content
```

## File structure

```
filters/
├── moat-aicc.txt      # AICC/HACP protocol
├── moat-cmi5.txt      # cmi5 profile
├── moat-scorm12.txt   # SCORM 1.2
├── moat-scorm2004.txt # SCORM 2004
└── moat-xapi.txt      # Experience API (Tin Can)
```

Add rules to the file matching the protocol. If a pattern applies to multiple
protocols, add it to each relevant file.

## Code of Conduct

- Be respectful and constructive
- Focus on technical merit
- Assume good intentions

## Questions?

Open an issue with the "question" label if you're unsure about anything.
