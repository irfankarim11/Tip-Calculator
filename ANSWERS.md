# ANSWERS.md

## 1. How to run

No installation required.

```bash
open index.html
```

Or with a local server (to avoid any `file://` edge cases):

```bash
npx serve .
# visit http://localhost:3000
```

That's it. Zero dependencies, no build step. Works in any modern browser.

---

## 2. Stack & design choices

**Stack:** Vanilla HTML + CSS + JS, single file. The task is a form with three inputs and four derived outputs — there's no state management complexity that justifies a framework. Adding React or Vue would mean a build toolchain, a `node_modules` folder, and a more complex run command, all to render something that updates four `<span>` values on input events. I picked the stack that lets the submission run with `open index.html`.

**Visual decision 1 — receipt metaphor instead of a card UI.**
Most tip calculators look like dashboards or mobile apps. I styled this as a physical restaurant receipt: perforated top edge, cream paper tone, serif display type, dashed dividers, monospaced numbers. This decision affects the entire layout but is most visible in the header and the `::before` pseudo-element that creates the torn-edge scallop. The reason: a tip calculator is used at the exact moment you have a physical receipt in your hand. Making the UI look like what you're holding creates immediate, instinctive orientation — you don't have to map an abstract dashboard onto a physical object.

**Visual decision 2 — per-person share anchored at the bottom of the results block, set in accent color at 1.35rem, separated by a full-weight rule.**
Everything above (subtotal, tip, grand total) is supporting information. The thing the user actually needs — *what do I owe?* — is the last line, largest, and loudest. I deliberately inverted the usual reading order (don't bury the lede) because in testing I noticed people's eyes jump to the bottom of a results card naturally, expecting the "answer" there. The thick rule above the per-person line acts as a visual stop, mimicking the double-underline convention on paper receipts.

---

## 3. Responsive & accessibility

**360px phone vs 1440px laptop:**
On narrow screens the tip preset grid collapses to a 3-column row with the custom input spanning full width below it (via `grid-column: 1 / -1`). Inner padding shrinks from `3rem` to `1.25rem` so the receipt doesn't feel cramped. The receipt card itself is `max-width: 480px` so on a 1440px laptop it sits centered with breathing room rather than stretching into an unreadable wide form. Font sizes stay the same — there's no reason to upscale them on desktop since the user is reading at a closer distance.

The `inputmode="decimal"` / `inputmode="numeric"` attributes ensure mobile browsers show the numeric keypad rather than QWERTY. This matters because the results panel is below the inputs — when the keyboard opens it could push content off screen. With a numeric keyboard (which is shorter) this is less of a problem, and the results panel is live so users can scroll down after typing.

**Accessibility handled:**
- All inputs have explicit `<label>` or `aria-label` associations.
- Error messages use `role="alert"` and `aria-live="polite"` so screen readers announce them when they appear, without interrupting typing.
- The tip preset buttons use `aria-pressed` (true/false) so a screen reader user knows which preset is active without needing to see the visual highlight.
- Tab order follows the natural DOM order: bill → tip presets → custom tip → people → reset.
- Enter key in any text field moves focus to the next logical field (bill → custom tip → people → reset), so keyboard-only users can complete the form without reaching for a mouse.
- All interactive elements have `:focus-visible` outlines in the accent color.
- Color contrast: ink (`#1a1612`) on cream (`#f5f0e8`) exceeds WCAG AA at all text sizes. Error red (`#c0392b`) on cream also passes.

**Accessibility skipped:**
- I didn't add a live region announcing the full results summary after each keystroke (e.g. "Per person: 450 rupees"). This would be the ideal experience for a screen reader user but requires careful debouncing — announce too eagerly and you interrupt the user while they're still typing; announce too late and it feels broken. Getting this right is a half-day job on its own (testing with VoiceOver, NVDA, TalkBack), and I prioritised getting the visual/keyboard experience correct first. The results section does have `aria-live="polite"` and `aria-atomic="true"` as a baseline.

---

## 4. AI usage

**Tool used:** Claude (this conversation).

**Where I used it:**
- Initial HTML/CSS structure, specifically the receipt card layout and the `::before` torn-edge scallop pattern using stacked `radial-gradient`.
- The `DM Mono` + `Playfair Display` + `Cormorant Garamond` font pairing.
- The `flashUpdate` function that briefly colours a result value in accent orange when it changes.
- Boilerplate for `aria-pressed` on the tip preset buttons.

**One thing I changed:**
The AI's initial rounding logic was `Math.round(exact * 100) / 100` — round to nearest. I changed it to `Math.ceil(exact * 100) / 100` — always round up. The reason: "round to nearest" means that sometimes a table of four people collectively pays *less* than the grand total (if the exact share was e.g. Rs 112.4249, rounding nearest gives Rs 112.42, and 4 × 112.42 = Rs 449.68 vs a grand total of Rs 449.70). In a real bill-split scenario this means someone is short, and figuring out who covers the gap is awkward. Rounding up means the group always covers the full bill plus a small overage (never more than `(n-1) paisa` where n is the number of people). The overage is surfaced in the remainder note below the per-person line so it's transparent, not hidden.

---

## 5. Honest gap

The custom tip input has a minor UX awkwardness: if you type a custom tip, then click a preset, clearing the custom input and re-entering a value re-activates "custom mode" automatically. But if you half-type something invalid in the custom field and then click a preset, the invalid custom value just sits there visually — even though the preset is now active and the error clears. A user glancing at the custom field might be confused about whether their half-typed number is in use.

With another day I'd add a `blur` handler on the custom tip input that clears it if the value is invalid when focus leaves, and I'd visually dim (opacity: 0.4) the custom input when a preset is active, so it's obvious which source is in use. I'd also add a small pill or indicator inside the custom input that shows "active" vs "inactive" without relying purely on border color.
