# Copilot instructions for Space Quiz

## Project shape

This is a dependency-free web app contained in one `index.html` file. It is intentionally served directly from the filesystem:

- Open `index.html` in a browser or the integrated browser; no server or package install is required.
- HTML, CSS, quiz data, state management, rendering, and event handlers all live in the same file.
- `PRODUCT.md` records product constraints and audience. `DESIGN.md` records the mission-console visual system and accessibility expectations.

## Build, test, and lint

There is no package manifest, build script, automated test runner, or repository lint configuration.

- **Run:** open `index.html` directly, or use the integrated browser with `file:///C:/Users/ukshi/OneDrive/Desktop/space-quiz/index.html`.
- **Smoke test:** start the mission, answer at least one question correctly and one incorrectly, advance through all 10 questions, and verify the results screen and restart button.
- **Single-question check:** after launch, select one answer and verify the feedback text, answer state class, disabled answer buttons, and score before advancing.
- **Design detector:** after UI edits, run `node "C:\Users\ukshi\AppData\Roaming\com.github.githubapp\app-skills\impeccable\scripts\detect.mjs" --json index.html` when that Impeccable skill is available.

## Architecture

The quiz is a small state machine driven by three DOM screens:

- `startScreen` is the launch state.
- `quizScreen` is rendered from the current item in the `questions` array.
- `resultsScreen` is populated by `finishQuiz()` based on the final score.

The main state is `currentQuestion`, `score`, and `answered`. `renderQuestion()` rebuilds the answer buttons for the current question. `selectAnswer()` locks the question, applies `.correct` or `.wrong`, updates `feedback`, and reveals `nextButton`. `startQuiz()` resets the run; `finishQuiz()` maps score percentage to the emoji, title, and message.

Progress is split between `updateProgress()` for per-question state and `finishQuiz()` for the completed state. The progress bar uses `aria-valuenow` and a transform-based fill animation.

## Key conventions

- Keep the app self-contained in `index.html`; do not introduce a framework, bundler, external font, CDN asset, or runtime dependency unless the product constraints are explicitly changed.
- Add or edit quiz content in the `questions` array. Each item must have `question`, four `answers`, and a zero-based `correct` index.
- Preserve the screen flow and guard against duplicate answers with the `answered` flag. Answer buttons are disabled after selection.
- Use native `<button type="button">` controls. Preserve visible `:focus-visible` styles and keyboard operation when changing controls.
- Keep status updates in the existing `role="status"` feedback region and keep the progress element's ARIA attributes synchronized with visual progress.
- Use the existing CSS custom properties for colors and surfaces. Add light-theme values in `:root` and matching dark-theme overrides in `@media (prefers-color-scheme: dark)`.
- Preserve the mission-console visual language: narrow centered column, stacked answer controls, cyan navigation accents, yellow solar accents, green correct state, red incorrect state, and concise instrument-style labels.
- Keep authored motion paired with `@media (prefers-reduced-motion: reduce)`. Do not rely on color alone for answer feedback; retain the text feedback and state labels.
- Prefer transform/opacity for motion rather than layout-property animation. Keep the existing responsive breakpoint at narrow mobile widths in mind when changing answer rows or result content.
- Treat `PRODUCT.md` and `DESIGN.md` as the source of truth for product behavior and visual/accessibility direction; update them only when those durable decisions change.
