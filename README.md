# visual-prototype

An agent skill for settling visual decisions in an existing product **before** the spec is written.

When the open questions are about how a screen looks, moves or behaves, describing it in words doesn't get you far: people react to pixels. With this skill the agent builds one self-contained HTML page inside the project. The page puts variants side by side, uses the project's real design tokens, and has controls the reviewer flips: theme, fewest and most items, shortest and longest text, time of day, gestures. Every decision block ends with a recommendation.

```
.concepts/
├── 2026-09-17-booking-card.html          ← current round
└── 2026-09-17-booking-card-round1.html   ← kept, still opens
```

## Why

Mockups made from memory have made-up colors, radii and fonts, so the review ends up about the mockup and not the idea. Screenshots show one state, and that state is usually the one that looks fine. A Storybook story breaks after the next refactor, needs a running server and ends up in the test run.

A concept page avoids all three problems. It's one file, it opens from disk, and it shows the extremes of every variable that changes the answer.

## What the agent does

**Reads the real design system.**
- Reads the tree you actually work in, including uncommitted changes. If its copy differs from your checkout, it tells you.
- Follows imports from the app's entry point to the live tokens instead of trusting file names, and copies `oklch()` values, radii and fonts exactly.
- Names the source files and commit in the page header.
- Draws both themes if the project has a dark theme.

**Builds one page for the decision.**
- Puts numbered variants side by side on the same data: `1.1`, `1.2`, then `1.2.1`, `1.2.2` when refining a pick. It uses digits rather than letters because they read the same in any script.
- Captions each variant with the question it answers and what it costs.
- Ends each block with a verdict, such as `Recommend 1.2 — <the reason that decided it>`, or with `Still open` and a numbered list of questions.
- Embeds every asset as `data:` so the file looks the same opened from disk as it does in a preview.

**Makes states flippable, not imagined.**
- Adds one control per variable that matters, offering only the extremes as options.
- Uses one state object to drive every variant and both themes together.
- Shows a readout under each frame, measured from the rendered DOM after fonts load: content height, pixels off-screen, pixels under the status bar.
- Gives continuous variables a slider with a play button. For example, "Free until 14:00" is wrong at 13:59, and the slider lets you see 13:59.

**Keeps history and hands off cleanly.**
- Copies each round to `-roundN.html` before rewriting the page, so earlier variants stay viewable.
- Once you approve a variant, writes the decisions and readout numbers into a spec.
- Before implementing, asks whether to build the variant with Storybook stories or directly. It never sets up Storybook without asking.

## Install

The skill is the `visual-prototype/` folder: `SKILL.md` plus `template.html`.

For all projects:

```bash
git clone https://github.com/KiraMurano/visual-concept.git /tmp/visual-concept && cp -R /tmp/visual-concept/visual-prototype ~/.claude/skills/
```

For one project, run this from the project root:

```bash
git clone https://github.com/KiraMurano/visual-concept.git /tmp/visual-concept && mkdir -p .claude/skills && cp -R /tmp/visual-concept/visual-prototype .claude/skills/
```

Restart Claude Code, or start a new session, so it picks up the skill. Other agents that read `SKILL.md` folders can use it the same way.

## Usage

The skill triggers on its own when the project already has a running UI and the question is visual:

> design the empty state for the bookings list
>
> how should the room card look when the title is long?
>
> prototype the new filter sheet

You can also name it directly: *"use visual-prototype for the settings screen"*.

A typical session:

1. The agent reads the tokens and components, then writes `.concepts/YYYY-MM-DD-<topic>.html` with variants `1.1–1.3` and a recommendation.
2. You open the page, flip the controls and reply: *"1.2, but the time label is too loud"*.
3. The agent saves round 1 as `-round1.html` and redraws the page with `1.2.1` and `1.2.2`.
4. You approve one. The agent records the decisions in the spec and asks how to implement it.

## What's inside

| File | Purpose |
|---|---|
| [`visual-prototype/SKILL.md`](visual-prototype/SKILL.md) | The rules: fidelity contract, page structure, controls, what happens after approval, common mistakes |
| [`visual-prototype/template.html`](visual-prototype/template.html) | Starting page: charset line, source header, state object, theme switch, sliders with play, numbered variants, DOM readouts, verdict block. The agent replaces its `TODO`s and keeps the structure |

## How it was tested

The skill was tested in 32 runs across two product repositories, a mobile web app and a desktop tracker. Each run used a fresh copy and the same prompts, with and without the skill.

| Behaviour | With skill | Without |
|---|---|---|
| Wrote a recommendation on the page | 16 / 16 | 2 / 16 |
| Kept the previous round when asked to fix a concept | 5 / 5 | 0 / 5 |
| Flagged that its copy might differ from the user's checkout | 16 / 16 | 0 / 16 |

Runs with the skill took about twice as long.

## License

MIT, see [LICENSE](LICENSE).
