# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Project Overview

This is the marketing website for TAS (True Authentic Self), hosted at **www.scan-tas.com** via **GitHub Pages**. It is a static HTML/CSS site — no framework, no server, no build pipeline beyond compiling Tailwind CSS.

## File Structure

```
index.html      # Main (and only) page
input.css       # Tailwind source — edit this for style changes
style.css       # Compiled Tailwind output — MUST be committed (GitHub Pages serves it directly)
public/         # Static assets: images, favicon, logo SVG
CNAME           # GitHub Pages custom domain (www.scan-tas.com)
package.json    # Dev dependencies (Tailwind CLI only)
```

## Build

After any change to `input.css` or class names in `index.html`, recompile CSS:

```bash
npm install          # only needed once after cloning
npx @tailwindcss/cli -i input.css -o style.css
```

`style.css` is the compiled output and **must be committed** to the repo — GitHub Pages serves it as a static file.

## Deployment

GitHub Pages deploys automatically from the `master` branch. Merging a PR into `master` publishes it to www.scan-tas.com. No CI/CD pipeline exists.

## Design System

This site shares design tokens with the TAS mobile app (`../tas/mobile/design/`). All color and typography choices should align with those tokens.

### Colors

| Token class | Hex | Use |
|---|---|---|
| `bg-bg-dark` | `#071f2c` | Primary page/section background |
| `bg-surface-dark` | `#1d6476` | Alternate section backgrounds, dividers |
| `text-brand-primary` / `bg-brand-primary` | `#e3b931` | Accent — headings, CTAs, borders |
| `bg-brand-secondary` | `#f89a38` | Gradient endpoint |
| `text-bg-dark` | `#071f2c` | Dark text on brand-primary buttons |
| `text-text-inverse` | `#fcfdfd` | Primary body text on dark backgrounds |
| `text-text-muted` | `#6c7881` | Secondary/muted text |

Rules:
- Never use raw hex values in `index.html` — always use the token classes above
- Brand accent is `brand-primary` (`#e3b931`), not `#FFB200`
- Page backgrounds use `bg-dark` (`#071f2c`), not gray or `#202127` (which is text.primary)
- Alternate sections use `bg-surface-dark` (`#1d6476`)

### Typography

- Font: **Kanit** (loaded from Google Fonts, all weights)
- Hero text: `font-bold text-5xl md:text-7xl` with Kanit
- Section labels: `text-xl` in `text-brand-primary`
- Body: base font size, regular weight

### Gradients

Follow the mobile design system gradient patterns:
- Dark hero: `bg-linear-to-r from-bg-dark from-60% to-brand-primary`
- Dark section overlay: `bg-linear-to-r from-surface-dark from-30%`

## Agent Orchestration

When working autonomously on tasks in this repo, use a two-tier model strategy to balance capability and cost.

### Planning (Orchestrator)

Use a high-capability model (e.g. `claude-opus-4-6`) for:
- Breaking down ambiguous or multi-step requests into concrete subtasks
- Making architectural decisions (e.g. restructuring sections, changing layout patterns)
- Reviewing completed work and deciding if it meets the goal
- Any task requiring judgment about design intent or cross-file impact

The orchestrator should produce a clear task list before any files are touched.

### Execution (Subagents)

Spin up subagents (via the `Agent` tool with `subagent_type=general`) for each discrete execution task:
- Editing a single file or set of related files
- Running the Tailwind build and verifying output
- Searching the codebase for specific patterns

Use a lighter model (e.g. `claude-haiku-4-5-20251001`) for subagents doing straightforward edits or searches. Use `claude-sonnet-4-6` for subagents that need more reasoning (e.g. restructuring HTML sections).

### Guidelines

- Never do planning and execution in the same context pass — plan first, then delegate
- Each subagent should receive only the context it needs: the relevant file(s), the specific instruction, and the design rules from this file
- Subagents should not be given the full conversation history
- After all subagents complete, the orchestrator reviews the diff and runs the Tailwind build to confirm nothing is broken
- Prefer more smaller subagents over fewer large ones to keep context windows small and costs low

## Important Notes

- `node_modules/` is gitignored — never commit it
- The contact form posts to HubSpot (`forms.hubspot.com`)
- Google Analytics tag ID: `G-3THWQM5VTC` — do not remove
- `nab.html` is an older draft page — not linked from the main site
- The mobile menu toggle is handled by `toggleMenu()` in inline `<script>`
