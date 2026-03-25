---
name: dev-browser
description: Browser automation with persistent page state. Use when users ask to navigate websites, fill forms, take screenshots, extract web data, test web apps, or automate browser workflows. Trigger phrases include "go to [url]", "click on", "fill out the form", "take a screenshot", "scrape", "automate", "test the website", "log into", or any browser interaction request.
---

# Dev Browser

A CLI for controlling browsers with sandboxed JavaScript scripts.

## Installation

```bash
npm install -g dev-browser
dev-browser install
```

## Usage

Run `dev-browser --help` to learn more.

## IMPORTANT: Avoid Permission Prompts

**Never use heredocs** (`<<'EOF'`), output redirection (`2>/dev/null`), or semicolons (`;`) in commands. These trigger "ambiguous syntax" permission prompts in Claude Code.

Instead, **always write scripts to `.dev-browser/` in the project root** (the current working directory, not the skill directory) and run them with `dev-browser run`:

```javascript
// .dev-browser/navigate.js
const page = await browser.getPage("main");
await page.goto("https://example.com");
await page.waitForSelector("body");
console.log(JSON.stringify({ title: await page.title(), url: page.url() }));
const buf = await page.screenshot();
await saveScreenshot(buf, "main.png");
```

```bash
dev-browser run .dev-browser/navigate.js
```

Use `.js` extension. Use short task-descriptive names (e.g., `fill-login.js`, `scrape-prices.js`). If a file exists that you didn't create, add a numeric suffix.

## Sandbox Constraints

Scripts run in a QuickJS WASM sandbox, **not Node.js**:

- **No `require()` / `import()`** — no module loading
- **No TypeScript** — plain JavaScript only, everywhere (including `page.evaluate()`)
- **No `fs`, `fetch`, `process`** — use `saveScreenshot()`, `writeFile()`, `readFile()` for I/O (sandboxed to `~/.dev-browser/tmp/`)

The `browser` global is pre-connected. Screenshots must use `saveScreenshot(buf, name)` — not `page.screenshot({ path })`.
