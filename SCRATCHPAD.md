# SCRATCHPAD

## Background and Motivation
The existing prompt shuffle app already loads questions and supports hints, but it still uses a game-style flow with multiple controls and scoring. The goal is to simplify the experience so learners immediately see a question and advance with a single `Next` button while keeping optional `Tip` and `Translate` toggles. We must preserve embeddability and static hosting.

## Key Challenges and Analysis
- Simplify without regressions: Remove the start/stop/summary flow and scoring while keeping fetch, validation, and error handling intact.
- Randomization: Continue to present questions in varied order with no immediate repeats using the existing queue logic or a pared-down equivalent.
- UI state: Ensure translation and tip toggles reset cleanly on each new question and keep ARIA/keyboard affordances.
- Copy and layout refresh: Update page text and Tailwind layout to reflect the streamlined flow while keeping contrast/accessibility.

## High-level Task Breakdown
1. Update `index.html` to drop the start/summary sections and unused controls, leaving a single session view with `Next`, `Tip`, and `Translate`.
2. Refactor `app.js` to remove shuffle preview, scoring, skip/answered logic, and start/summary view management while keeping data loading/validation.
3. Streamline the question-advance logic so `Next` pulls a new random question, resets hint state, and refreshes any lightweight status messaging.
4. Adjust UI text and styling to match the simplified interaction and ensure hidden/visible states remain accessible.
5. Remove residual references (buttons, counters) and verify error handling and fallback JSON flow still work.

## Project Status Board
- [DONE] Simplify `index.html` structure to a single session layout with only `Next`, `Translate`, and `Tip`.
- [DONE] Clean `app.js` of start/stop/score logic while preserving fetch/validation and hint toggles.
- [DONE] Implement straightforward random-next flow with per-question state reset.
- [DONE] Refresh copy/styling to reflect the new flow and confirm accessibility states.
- [DONE] Remove leftover counters or summary elements and sanity-check error messaging/fallback data.

## Lessons
- Randomization now relies on a simple shuffled index queue; when the queue resets, we swap the first element if it matches the previous question to avoid immediate repeats without extra bookkeeping.
- `file://` sessions fetch `data/questions.json` via a resolved file URL; if the file is unreachable or empty, we surface a friendly “no questions” message instead of falling back to embedded JSON.
