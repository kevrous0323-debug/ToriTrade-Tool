# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ToriTrade Tools is a French-language, browser-based trading toolkit for the Tori trendline breakout strategy. It targets traders operating FTMO-funded or personal accounts. The entire project is 3 self-contained HTML files with no build step, no frameworks, and no external dependencies.

## Running the Project

Open any HTML file directly in a browser — no server, no build, no install:

```
open index.html          # navigation hub
open calculatrice.html   # position size calculator
open checklist.html      # 28-point pre-trade checklist
```

There are no tests, no linters, no CI/CD, and no package manager.

## Architecture

Each file is entirely self-contained: HTML structure, `<style>` block with all CSS, and `<script>` block with all JavaScript live in a single file. There are no shared modules, imports, or external stylesheets.

**Data persistence:** `localStorage` only. `calculatrice.html` saves capital and risk percentage selection across sessions. `checklist.html` does not persist checkbox state (resets on reload by design — each trade session is a fresh check).

**Navigation:** `index.html` links to the two tools via plain `<a href>`. No router.

## Styling Conventions

All three files use the GitHub Dark (Primer) color palette consistently:

| Token | Value | Usage |
|---|---|---|
| Page background | `#0d1117` | `body`, inputs |
| Card background | `#161b22` | `.card`, panels |
| Border | `#30363d` | all borders |
| Primary text | `#e6edf3` | body text |
| Heading text | `#f0f6fc` | `h1`, `h2` |
| Muted text | `#8b949e` | labels, subtitles |
| Blue accent | `#58a6ff` | focus states, links |
| Green | `#3fb950` | LONG direction, success states |
| Red | `#f85149` | SHORT direction, warnings |
| Amber | `#e3b341` | medium-risk warnings |

Font stack: `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`. Monospace inputs use `'SF Mono', Consolas, monospace`.

## Domain Logic

### Position Size Formula (calculatrice.html)

```
Position Size ($) = Capital × Risk% ÷ SL%
Units             = Position Size ÷ Entry Price
```

**Risk modes:** 1% (FTMO conservative) or 2% (Tori default strategy).

**SL% verdict thresholds:**
- ≤ 6% → LOW RISK (green) — setup near intersection, acceptable entry
- 6–10% → MED RISK (amber) — suboptimal, breakeven required quickly
- > 10% → HIGH RISK (red) — aggressive management mandatory

**Validation rules enforced:**
- SL must be below entry for LONG, above for SHORT
- Position size cannot exceed total capital
- TP must give R:R ≥ 2:1 (minimum shown; violation flagged)

### Checklist (checklist.html)

28 binary checkpoints across 5 collapsible sections. All 28 must be checked before the "✅ Trade validé" verdict appears. The cancel-trade rules (shown in a red banner) are hard disqualifiers that override any number of checked items.

**Section order reflects top-down analysis:** Weekly/Daily macro → trendline qualification → trade validation (SL/TP/R:R) → entry confirmation → FTMO compliance.

**Timeframe tags** used in checklist items: `W` (Weekly), `D` (Daily), `4H`, `1H`, `15M`, `FTMO`.

**Key trendline quality rules (baked into checklist):**
- Drawn with RAY tool (not a segment — must extend infinitely)
- 3+ taps, ≥ 6 candles between taps, ≥ 3 weeks between first and last tap
- Slope < 45° (verified on 3-month 4H chart)
- Price never breached the trendline between Point A and Point B
- Both the action line and safety line (opposing trendline) must be drawn

## UI Patterns

Toggle buttons use `.active-long` (green) / `.active-short` (red) classes swapped via JS. Result cards use inline `style` updates for dynamic color changes. No CSS-in-JS, no class toggling for colors beyond the direction toggle.

All UI text is in French. Maintain French for any additions to existing tools.
