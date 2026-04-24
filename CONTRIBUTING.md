# Contributing to moat-lms-blocker

Thank you for your interest in contributing!  We welcome contributions from everyone.

## How to Contribute

### Reporting Bugs

If you find a bug, please create an issue with:

- A clear, descriptive title
- Steps to reproduce the problem
- Expected vs. actual behavior
- Your environment (OS, browser, versions)
- Screenshots if applicable

### Suggesting Features

Feature requests are welcome! Please:

- Check if the feature has already been requested
- Clearly describe the feature and its use case
- Explain why it would be beneficial

### Pull Requests

1. **Fork the repository** and create your branch from `main`

   ```bash
   git checkout -b feature/my-new-feature
   ```

2. **Make your changes**
   - Follow the existing code style
   - Add tests if applicable
   - Update documentation as needed

3. **Commit your changes** with clear, descriptive messages

   ```bash
   git commit -m "Add feature:  description of what you added"
   ```

4. **Push to your fork**

   ```bash
   git push origin feature/my-new-feature
   ```

5. **Open a Pull Request**
   - Use the PR template
   - Link any related issues
   - Describe what changed and why

### Code Review Process

- Maintainers will review your PR as soon as possible
- Address any requested changes
- Once approved, a maintainer will merge your PR

## Development Setup

This project uses AGLint for validating filter list syntax.

### Prerequisites

- Node.js 24 or higher (22 or higher required by AGLint's dependency @adguard/agtree)
- npm (comes with Node.js)

### Setup

1. Clone the repository:

   ```bash
   git clone https://github.com/mobilemind/moat-lms-blocker.git
   cd moat-lms-blocker
   ```

2. Install dependencies:

   ```bash
   npm install
   ```

### Linting Filter Lists

Before submitting changes to filter files, run the linter:

```bash
npm run lint
```

To automatically fix issues (when possible):

```bash
npm run lint:fix
```

The linter checks all `.txt` files in the `filters/` directory for:

- Valid uBlock Origin syntax
- Proper comment formatting
- Correct rule structure
- Common syntax errors

All filter files must pass linting before being merged.

### Git Hooks (Optional)

The repo ships a `pre-push` hook in `.githooks/` that runs the project
linters before each `git push`, in this order:

1. `aglint` on filter lists (`npm run lint:filters`)
2. `prettier --check` on JSON (`npm run lint:json`)
3. `prettier --check` on YAML (`npm run lint:yaml`)
4. `markdownlint-cli2` on Markdown (`npm run lint:markdown`)
5. [`actionlint`](https://github.com/rhysd/actionlint) on GitHub Actions
   workflows
6. [`shellcheck`](https://www.shellcheck.net/) on tracked shell scripts

Each tool is optional: if it is missing (no `node_modules`, or
`actionlint`/`shellcheck` not on `PATH`) the check is skipped with a
warning. If a tool is present and reports errors, the push is blocked.
All checks run on every invocation so failures are reported together.

To opt in, point Git at the hooks directory once per clone:

```bash
git config core.hooksPath .githooks
```

Install dependencies and the standalone linters:

```bash
npm install
brew install actionlint shellcheck   # macOS / Homebrew
```

Bypass the hook for a single push with `git push --no-verify`.

## Code of Conduct

Please be respectful and constructive.  We're all here to build something great together.

## Questions?

Feel free to open an issue with your question or reach out to the maintainers.

Thank you for contributing!  🎉
