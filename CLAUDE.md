# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Smudged is a browser-based quiz application themed around the co-op ghost-hunting game Phasmophobia. It has no build system — the entire app is a single HTML file served alongside a JSON question bank.

## Running the App

Open `phasmo_quiz.html` directly in a browser, or serve both files from any static web server:

```
# Python (simplest option)
python -m http.server 8080
# then visit http://localhost:8080/phasmo_quiz.html
```

The fetch call for `questions.json` requires HTTP (not `file://`), though an embedded JSON fallback in the HTML catches the failure silently.

## Architecture

All logic lives in `phasmo_quiz.html` (inline `<style>`, markup, and `<script>`). There are no external JS dependencies, no build step, and no framework.

### Files

- **`phasmo_quiz.html`** — Everything: CSS, HTML structure, and JavaScript (~706 lines)
- **`questions.json`** — Question database (~150 questions, 1 366 lines)

### Three-Screen Flow

```
Title Screen  →  Quiz Screen (12 questions)  →  Results Screen
```

- `init()` — fetches `questions.json`; falls back to embedded JSON on failure
- `startQuiz()` → `pickQuestions()` — selects 4 questions each from the three types
- `renderQuestion()` — builds the current question UI
- `choose()` — records the answer, shows feedback, advances
- `showResults()` — computes score and rank, renders field report table

### Question Types

Each question in `questions.json` carries a `type` field: `"behavior"`, `"tell"`, or `"sanity"`. The quiz always draws 4 of each type (12 total), shuffled via Fisher-Yates.

### Ranking Tiers

| Score | Rank |
|-------|------|
| 100% | Cryptid Hunter |
| ≥75% | Seasoned Investigator |
| ≥50% | Junior Apprentice |
| ≥25% | Waiver Signed |
| <25% | Missing Person |

## Adding or Editing Questions

Edit `questions.json`. Each entry shape:

```json
{
  "type": "behavior",
  "text": "Question prompt",
  "detail": "Short hint/context",
  "options": ["A", "B", "C", "D"],
  "answer": 0,
  "explain": "HTML string shown after answering"
}
```

`answer` is the zero-based index into `options`. The `explain` field accepts inline HTML (spans, `<br>`, etc.) — the existing entries use styled `<span>` tags for colour-coded game terms.
