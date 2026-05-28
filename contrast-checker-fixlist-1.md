# Contrast Checker QA and Portfolio Review

File tested: `contrast-checker.html`

Tested in browser at: `http://127.0.0.1:8765/contrast-checker.html`

## Person 1: QA Tester

I reset to the default black-on-white state before each test.

| Test | What Happened | Broke? |
|---|---|---|
| Type `#gg0000` in the foreground field | The text field accepted it. The ratio became `Contrast Ratio: NaN:1`. All badges changed to `Fail`. The preview stayed visually black on white because the invalid CSS color was ignored. There were no console errors. | Yes. Invalid input produces `NaN` instead of showing a validation error or preserving a clear invalid state. |
| Type `#fff` in the foreground field | The text field accepted it, but nothing recalculated. The ratio stayed `21.00:1`, badges stayed `Pass`, the picker stayed `#000000`, and the preview stayed black. | Yes. The code has a function that can parse 3-character hex values, but the UI only updates when the field length is exactly 7. |
| Set both fields to `#ffffff` | The ratio became `1.00:1`. All badges failed. The preview became white text on a white background. | No crash. The contrast result is correct. |
| Delete all text from one field | The field became empty. The ratio, badges, and preview stayed at the previous valid state, such as `21.00:1` from the default state. | Yes. The UI shows stale results when an input is empty. |
| Use the color picker and drag around rapidly | I could not directly automate the native OS color picker drag, but rapid color input changes were tested. The tool continued updating without crashing. A final value of `#ff6600` showed ratio `2.94:1`, with all badges failing. | Did not break under rapid input events. |
| Type a full valid hex like `#ff6600` manually | The text field updated the picker and preview. The ratio showed `2.94:1`. All badges failed. | No. This worked as expected. |

## Person 2: Skeptical Hiring Manager

Bluntly, this feels like a beginner or AI-assisted portfolio widget that was dropped in before hardening. It is visually clean enough, but it is too generic and too thin to prove strong frontend judgment.

The WCAG math is mostly right for WCAG 2.1 text contrast: the relative luminance formula, contrast ratio formula, and pass/fail thresholds are the expected ones. But the product behavior is incomplete. A real developer should catch invalid hex values, empty fields, 3-digit shorthand, stale results, accessible labels, and clearer large-text context.

## Biggest Red Flags

- Invalid full-length hex produces `NaN:1`.
- Empty or incomplete input leaves stale pass/fail results visible.
- `#fff` is supported by the parsing function but blocked by the input handler.
- Labels are not associated with inputs using `for` and `id`, which is especially weak for an accessibility-focused tool.
- `AA Large` and `AAA Large` lack explanation. The preview text is `18px`, which is not normal-weight WCAG large text.
- The UI is clean but generic: system font, gray card, badges, no thoughtful error states, no pass/fail visual treatment beyond text, no examples, no copy/share feature, and no deeper polish.

## Interview Questions I Would Ask

1. Why does `hexToRgb()` support 3-digit hex when the UI blocks it?
2. How would you validate hex input without showing stale results?
3. What exactly qualifies as large text in WCAG?
4. Why are the labels not programmatically associated with the inputs?
5. How would you test the contrast math around threshold boundaries like `4.49`, `4.50`, and `7.00`?
6. What would you change to make this feel like a polished design tool rather than a demo?

## Honest Verdict

This is a promising small exercise, but it is not portfolio-ready yet. The happy path works, but the edge cases expose that it has not been product-tested. It needs validation, clearer accessibility semantics, better handling of incomplete input, and more intentional UX details before I would treat it as strong portfolio work.
