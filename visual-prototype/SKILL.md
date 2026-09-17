---
name: visual-prototype
description: Use when a feature's open questions are visual — how a screen looks, moves, or behaves — and the project already has a running UI with its own design system. Triggers include "design this", "how should it look", "mock this up", "prototype the screen", a user who reacts to pixels rather than descriptions, and any UI change where two reasonable layouts exist.
---

# Prototyping Visual Decisions

## Overview

A prototype is an instrument for settling one visual decision, not a picture of a screen. It comes before the spec; the answers it produces are what the spec records.

The deliverable is a self-contained HTML page in the project. **Start from `template.html` in this skill's folder.** It already carries the page mechanics: charset line, source header, one state driving every frame, theme switch, time slider with play, numbered variants with captions, readouts measured from the DOM, verdict block. Replace its TODOs; keep its structure.

## Before drawing: the fidelity contract

Approximated or stale styling moves the argument off the design and onto the mockup — you end up defending your colors instead of your idea.

1. **Read the tree the user works in.** Compare `git log -1` with the user's repo; if you are in a worktree or a copy that differs, read their files on disk — uncommitted work is invisible to git. Say which tree you used.
2. **Follow imports, not filenames, to the live tokens**, starting at the entry point. `tokens.css` may belong to a design system already replaced.
3. **Find the screen by route to component**, never by name resemblance.
4. **Copy values verbatim** — real `oklch()`, radii, font stack. A conversion the medium forces is made from the source and said.
5. **Name the source files and the commit** in the page header and in your message.
6. **Build both themes when the project has a dark theme.** When it has none, draw light only and say so.

Never assert fidelity you did not verify: "real project tokens" is a claim about files you read this session.

## The page

1. **Always an HTML page — never a Storybook story, even when the project has Storybook.** A story stops compiling after the next refactor, opens only with a running server, and lands in the test run.
2. **It lives in the project** as `.concepts/YYYY-MM-DD-<topic>.html`, committed next to the spec that records its decisions.
3. **Every round is kept.** Before rewriting the page, copy it to `YYYY-MM-DD-<topic>-roundN.html`. The unsuffixed file is always the current round; its header lists past rounds, what was chosen, and their files.
4. **Variants side by side on the same data**, each captioned with what it answers and what it costs.
5. **Variants are numbered, never lettered, and the number carries the lineage.** First round: 1.1, 1.2, 1.3. The next round refines the pick one level down: picking 1.2 gives 1.2.1, 1.2.2. An independent question on the same page is block 2: 2.1, 2.2. A number is never reused; a new concept starts again from 1.1. Options listed in the conversation are numbered the same way. Digits read the same in every script; letters do not — Cyrillic «З» reads as 3.
6. **Every decision block ends with a verdict** in the conversation's language: `Recommend 1.2 — <the one reason that decided it>`. A round with a single variant ends with `Still open` and the questions as a numbered list.
7. **The charset stays on the first line and every asset is embedded as `data:`** — images, SVG, fonts — generated from the project's own file. An embedded preview declares the encoding and hides relative paths that fail; the same file opened from disk shows mojibake and blank images, and readouts measured on fallback glyphs come out plausible and wrong. If the project's font file is not on disk, the header names the font actually used.

## Controls

The reviewer settles the decision by flipping states, not by imagining them.

1. **One control per variable that changes the answer**, its extremes as the options: fewest and most items, shortest and longest text, empty and full. Middle cases always look fine.
2. **One state drives every frame** — all variants and both themes change together.
3. **A readout under each frame**, measured from the rendered DOM: height, pixels off-screen, pixels under the status bar.
4. **A slider for every continuous variable**, time first, with a play button that walks the range. "Free until 14:00" is wrong at 13:59, and without the slider nobody sees 13:59.
5. **Motion and gestures work on the page** when the question is about them: the animation plays, slows down, and parks on any moment under the slider; a long-press or drag uses the project's own delays and thresholds.

## After the prototype

1. **Fold the decisions into a spec** with the reasoning and the readout numbers. Say where the implementation departs from the prototype and why.
2. **Once the user approves a variant, ask one numbered question before implementing.**
   1. The project has Storybook (a `.storybook/` folder, or `storybook` in `package.json`): `1. Build the chosen variant into components with Storybook stories (recommended)` or `2. Implement it directly, without stories`. Stories keep the decided states checkable in the codebase once the concept page is frozen.
   2. The project has no Storybook: say that it is not set up and that you will not set it up without the user's consent. Then ask: `1. Implement the chosen variant directly` or `2. Set up Storybook first`.

## Common mistakes

Only the ones the rules above do not already spell out.

| Mistake | What it costs |
|---|---|
| Scaffold class names shared with the mock's components | Scaffold styles land on the mock; text vanishes and the bug looks like a design flaw. Keep the template's `cx-` prefix |
| Checking the page only in an embedded preview | The preview supplies the encoding and hides failed asset loads. Open the file from disk, or tell the user you could not |
| Measuring before fonts load | Readouts computed on fallback glyphs. The template waits for `document.fonts.ready` |
| Cost captions taken for a verdict | "So what do you suggest?" — one more round |
