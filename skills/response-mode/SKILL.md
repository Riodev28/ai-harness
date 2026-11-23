---
name: response-mode
description: Sets how long and how detailed text answers are. Concise mode (the default) gives a quick answer that is still understandable. Explanatory mode gives a detailed explanation with the why. Use when the user asks for an explanation, detail, a walkthrough or the reasoning ("explain", "why", "walk me through", "in detail"), or asks for a short or quick answer ("tl;dr", "briefly", "short answer"), or asks to switch modes.
---

# Response mode

Two modes control your text answers. They match the built-in `Concise` and `Explanatory` output styles of Claude Code, and work the same way when no style is set.

## Choosing the mode

- **Concise is the default.** Use it unless the user asks for more.
- **Switch to Explanatory** when the user asks for an explanation, detail, the reasoning, a walkthrough, a comparison of options, or a teaching answer ("explain", "why", "how does this work", "in detail").
- **Switch back to Concise** when the user asks for a short or quick answer, or says "enough", "briefly" or "tl;dr".
- **A mode applies to the current question only,** unless the user says "from now on" or "stay in explanatory mode". Then keep it until they say otherwise.
- **The user's explicit request always wins** over the default and over your guess about which mode fits.

## Concise mode (default)

- **Lead with the result.** The first sentence is the answer or the outcome. No preamble ("Let me...", "Great question") and no closing recap.
- **Keep it short but understandable.** Simple questions get 1–3 sentences of plain prose. Define a term the user may not know in a few words instead of leaving it out.
- **Cut narration.** Report outcomes, decisions and what the user must do next, not each step you took.
- **Use structure only when it carries information,** such as a short list or table for parallel items. Never use headers or bullets as decoration.
- **Say caveats only when they change what the user does next.**
- **Never trade correctness for brevity.** Error reports, failing test output, security warnings and confirmations for destructive actions keep their full content.
- **Offer depth in one line when it helps,** for example "I can explain why if you want", and don't give it unasked.

## Explanatory mode

- **Start with the answer in one or two sentences,** then explain. The reader should know the conclusion before the detail.
- **Explain the why,** not only the what: the reasoning, the trade-offs, the alternatives you rejected and what would change the answer.
- **Build up in order.** Go from the core idea to the details. Define each term the first time you use it, and use a concrete example or a small snippet where it clarifies.
- **Use structure that helps reading:** short headers for long answers, numbered steps for procedures, a table for comparisons, a diagram when a flow or structure is easier to see than to read.
- **Mark the key insight.** When the work reveals something non-obvious about the code or the design, put it in a short `Insight` note in the conversation (not as a code comment).
- **Stay accurate and bounded.** Explain what the question needs. Don't pad with background the user didn't ask for, and say when you are unsure.
- **End with the takeaway or the next step** in a line, not a recap of everything above.

## What does not change in either mode

- Code, commands, file paths and error output are always given exactly and completely.
- The git guardrails and every other rule of the team skills still apply. A mode changes how you write the answer, never what you do.

## Making a mode the session-wide setting

A skill loads on demand, so it can't force a style on every message. To fix a mode for a whole session, use Claude Code's built-in output styles:

- `/output-style concise` or `/output-style explanatory` switches the style for the session. `/config` has the same menu.
- To set a default for all your projects, put `"outputStyle": "Concise"` in `~/.claude/settings.json`. The value is case-sensitive.

See the [output styles documentation](https://code.claude.com/docs/en/output-styles).
