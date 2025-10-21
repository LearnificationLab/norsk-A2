# SCRATCHPAD

## Background and Motivation
The existing prompt shuffle app already loads questions and supports hints, but it still uses a game-style flow with multiple controls and scoring. The goal is to simplify the experience so learners immediately see a question and advance with a single `Next` button while keeping optional `Tip` and `Translate` toggles. We must preserve embeddability and static hosting.
Teachers periodically swap in new speaking prompts. We now need to refresh `data/questions.json` with a new batch provided in `input.json` so the simplified app serves updated content without altering behavior. The supplied file currently uses JavaScript object literal syntax (`prompt`, `hints`) instead of strict JSON keys, so we must normalize it before replacing the live dataset.
The latest dataset compresses several hint variants into a single slash-delimited string. Learners want each variant on its own line when the tip is revealed to make scanning easier on small screens.

## Key Challenges and Analysis
- Simplify without regressions: Remove the start/stop/summary flow and scoring while keeping fetch, validation, and error handling intact.
- Randomization: Continue to present questions in varied order with no immediate repeats using the existing queue logic or a pared-down equivalent.
- UI state: Ensure translation and tip toggles reset cleanly on each new question and keep ARIA/keyboard affordances.
- Copy and layout refresh: Update page text and Tailwind layout to reflect the streamlined flow while keeping contrast/accessibility.
- Preserve JSON schema: Confirm that the replacement dataset matches the expected `{ text, translation, tip }` structure and surface any gaps. `input.json` presently uses `prompt` and `hints` (array) and omits quotes around property names, so we must translate the structure into valid JSON with the canonical fields.
- If we split slash-separated hints, we must keep backwards compatibility in case a tip lacks delimiters and ensure the UI spacing remains consistent.

## High-level Task Breakdown
1. Update `index.html` to drop the start/summary sections and unused controls, leaving a single session view with `Next`, `Tip`, and `Translate`.
2. Refactor `app.js` to remove shuffle preview, scoring, skip/answered logic, and start/summary view management while keeping data loading/validation.
3. Streamline the question-advance logic so `Next` pulls a new random question, resets hint state, and refreshes any lightweight status messaging.
4. Adjust UI text and styling to match the simplified interaction and ensure hidden/visible states remain accessible.
5. Remove residual references (buttons, counters) and verify error handling and fallback JSON flow still work.
6. Convert `input.json` into a schema-compliant JSON payload (`text`, `translation`, `tip`) while handling the hints array (choose primary hint or join as needed).
7. Validate the transformed data (no empty text/translation/tip) and overwrite `data/questions.json`; raise any anomalies back to Analyst if requirements shift (e.g., multiple tips needed).
8. Parse slash-delimited tip strings into distinct lines when rendering, falling back gracefully for single-tip entries.
9. Verify the UI renders multi-line tips neatly across viewports without breaking existing toggles or spacing.

## Project Status Board
- [DONE] Simplify `index.html` structure to a single session layout with only `Next`, `Translate`, and `Tip`.
- [DONE] Clean `app.js` of start/stop/score logic while preserving fetch/validation and hint toggles.
- [DONE] Implement straightforward random-next flow with per-question state reset.
- [DONE] Refresh copy/styling to reflect the new flow and confirm accessibility states.
- [DONE] Remove leftover counters or summary elements and sanity-check error messaging/fallback data.
- [DONE] Normalize `input.json` structure into valid JSON with `text`, `translation`, and single `tip` strings.
- [DONE] Replace `data/questions.json` with the validated dataset and spot-check in the app.
- [DONE] Update tip rendering to break slash-delimited strings into separate lines while retaining existing toggle behavior.

## Lessons
- Randomization now relies on a simple shuffled index queue; when the queue resets, we swap the first element if it matches the previous question to avoid immediate repeats without extra bookkeeping.
- `file://` sessions fetch `data/questions.json` via a resolved file URL; if the file is unreachable or empty, we surface a friendly “no questions” message instead of falling back to embedded JSON.
- When normalizing legacy prompt dumps, a quick regex pass can quote keys and reinsert missing commas before JSON parsing, then join hint variants with ` / ` to retain context in a single `tip` string.
- Tip strings are now split into stacked spans on the fly, so content authors can keep the existing ` / ` delimiter without changing the JSON format while learners see one hint per line.
