# Trading Strategy Checklist — 1H + 5M Hybrid (icer.html)

## Overview
- Purpose: A manual checklist to align 1H bias with 5m entry conditions for GBP/USD during London and New York sessions.
- Core idea: Confirm the 1H trend/bias first, then use up to three 5m "trade slots" to validate entry criteria. When the 1H bias and a 100%-confirmed 5m slot align, the indicator lights up (green for BUY, red for SELL).
- Tech stack: Single static HTML file using Tailwind CSS via CDN; pure client-side JavaScript and minimal CSS.

## Getting Started
- Open `icer.html` in any modern browser (Chrome, Edge, Firefox, Safari).
- Internet is required to load Tailwind from the CDN (`https://cdn.tailwindcss.com`). No build tools or local server needed.
- Everything is client-side; no data is persisted across page reloads.

## UI Walkthrough
- 1H Bias
  - Radios: `No bias`, `Buy`, `Sell`.
  - Checklist items: price vs 200MA, 1H structure (CHOCH → BOS), simplified order block, Williams %R(14) neutral, session (London or New York).
  - The 1H completion percentage and progress bar update as you check items.

- 5m Trade Slots (max 3)
  - Add slots with the `Add Slot` button; each slot has its own checklist:
    - Price vs 200MA (same side as 1H), Micro CHOCH → BOS, Retracement to 200MA or simplified OB, Williams %R(14) extreme + turn, Strong engulfing.
  - Each slot can be deactivated or removed; active slot count is displayed.
  - The "Best 5M Slot" percentage and bar show the highest-completion active slot.

- Quick Stats
  - `1H Completion`: percentage of checked 1H items.
  - `Best 5M Slot`: highest completion among active slots.
  - `Overall Success`: computed as `50% * 1H completion + 50% * best 5m slot`.

- Trade Indicator
  - Visual feedback depends on overall success and selected bias.
  - Threshold: when success ≥ 75%, the indicator pulses and shows `BUY` (green) or `SELL` (red) based on bias; below threshold, it shows a waiting state or `No Bias`.
  - Active Slot Info: displays the best slot status; shows confirmation when a slot reaches 100% and 1H is fully checked with a non-`none` bias.

- Controls
  - `Reset All`: clears all checkboxes, resets bias to `No bias`, and removes slots.
  - Dark mode toggle (`🌓`): toggles `document.documentElement.classList` with `dark` for a darker theme.

## How It Works (Logic)
- Data Model
  - `MAX_SLOTS = 3` limits the number of 5m slots.
  - `slots`: an array of slot objects `{ id, active, checks }` where `checks` include `ma`, `microStructure`, `retrace`, `wr`, `engulf`.
  - `onehKeys = ['ma','structure','ob','wr','session']` defines the 1H checklist.

- Completion Calculations
  - 5m slot completion (`computeSlotCompletion`): percentage of checked keys within a slot (excluding `sideMatch`).
  - 1H completion: ratio of checked 1H items to `onehKeys.length`.
  - Overall success: `round( (1H% * 0.5) + (best5% * 0.5) )`.

- Indicator Logic
  - Threshold: success ≥ 75% triggers pulse and color state.
  - Bias required: `buy` or `sell` must be selected to show buy/sell states; otherwise shows `WAIT`/`No Bias`.
  - Readiness: non-`none` bias, all 1H items checked, and the best active slot at 100%.
  - Utility: `checkTradeGo(successPercentage)` returns `true` when `successPercentage >= 75` (override point for success threshold).

## Usage Guide (Suggested Flow)
1. Select 1H bias (`Buy` or `Sell`) after confirming higher-timeframe conditions.
2. Check 1H items (price vs 200MA, structure, OB, W%R neutral, session).
3. Add a 5m slot and check its criteria as price action unfolds.
4. Use up to three slots; deactivate or remove as needed, focusing on the best-completion slot.
5. Watch `Overall Success` and the `Trade Indicator`. Enter only when threshold is met and readiness criteria are satisfied.
6. Toggle dark mode as desired. Use `Reset All` to start fresh.

## Customization
- Change maximum slots: update `MAX_SLOTS` in the script.
- Adjust success threshold:
  - Update `checkTradeGo(successPercentage)` or the `applyVisualFeedback` check `successPercentage >= 75`.
- Modify 1H or 5m conditions:
  - Edit labels in the HTML table templates.
  - Extend `createSlot().checks` and the slot table to add/remove criteria.
- Style tweaks: modify Tailwind classes in the HTML; edit CSS for `.pulse-success` and dark mode selectors.

## Limitations
- Manual process: no live market data or automation; relies on your own chart analysis.
- No persistence: state resets on page reload; no storage.
- Single-pair focus: written for GBP/USD but can be adapted.
- CDN dependency: Tailwind loads from a CDN; requires internet connectivity.

## File Structure
- Single file: `icer.html` containing markup, styles (embedded), and JavaScript logic.

## Notes
- Designed to encourage disciplined confirmation across timeframes and avoid contradictory trades.
- The indicator is an aid; it does not guarantee trade outcomes.