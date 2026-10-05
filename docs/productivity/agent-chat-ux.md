## What it does

`agent-chat-ux` is the discipline of making an [agent](https://www.aihero.dev/ai-coding-dictionary/agent) feel smarter through how it communicates: emojis used as a small stable language, reactions marking the seams of work, progress bars for long runs, and interactive cards or buttons wherever the user's answer is predictable.

It treats communication as UX, not decoration. Every pattern in it exists to cut friction: less typing, fewer ambiguous answers, visible progress on slow work, and acknowledgment that costs the reader nothing. The defining constraint is that each pattern has to earn its place per message: one signal per moment, never stacked, never celebrating bad news, and never carrying information that the text does not already carry on its own.

## When to reach for it

Type `/agent-chat-ux`, or the agent reaches for it automatically when a task fits.

Reach for this when the conversation itself is the product surface: you are building or tuning how an agent talks to its user, writing reply guidelines for an agent project, or noticing that users mistype answers, ask "is it stuck?", or miss consequential actions. It is model-invoked because the triggers are visible in the work: a question with a closed set of answers, a task that runs longer than a minute, an action the user must approve.

| Situation | What the skill gives you |
| --- | --- |
| Asking the user to choose, approve, or confirm | Card and button design: one question per card, 2 to 4 options, always a graceful exit |
| Long-running or background work | Seam signals: a 👍 on receipt, a progress bar while running, a ✅ on delivery |
| Users skimming long replies | Message landmarks: a tiny stable emoji vocabulary (📋 plan, 🔍 findings, ⚠️ warning, 🎯 decision needed) |
| Plain-text or limited clients | Fallbacks: numbered lists where cards do not exist, emoji where reactions do not |

For the mechanics of writing the skill itself rather than using it, see [writing-for-agents](https://aihero.dev/skills-writing-for-agents).

## The bounded-answer rule

The skill's leading word is **bounded**. When the answer is bounded, offer it as a card; when the question is open, ask it in plain text.

A bounded answer is one you can enumerate before the user replies: yes or no, pick one of three, approve or edit or cancel. Making the user type a bounded answer is pure friction: they invent phrasing, they typo, they wonder if they phrased it right, and the agent has to parse whatever arrives. A card removes all of that. Each button sends its exact label as the user's reply, so the agent receives a clean, parseable answer every time, and tapping is faster than typing.

Two constraints keep cards honest. One question per card: two unrelated questions in one card force the user to answer both or neither. And always a graceful exit ("Not now", "Skip", "Cancel"): a card with no way out is a trap. For approvals, the card body is the contract: it states the action, the target, and the consequence in full, and the button is the signature.

Where the platform has no cards, the rule survives as a numbered list plus "reply with the number". Never let a missing button push you back to an open question for a bounded answer.

## Seam signals for long work

The second half of the skill is about the seams: the moments between the user's message and the result. Three signals cover them, and the discipline is one per moment.

A **reaction** on the user's message is a message that costs nothing to receive. 👍 on receipt answers "did you hear me?" instantly, before any work starts. This is especially powerful for background or scheduled work: at a glance the user sees what was picked up, what is running, and what finished, without opening anything.

A **progress bar** answers "is it stuck?" Pick one design and use it everywhere, because consistency is what makes it scannable. Update at milestones (extract, transform, load, verify), not on a timer, and retire the bar when the work finishes: the bar means in progress, a single ✅ means done.

An **emoji vocabulary** works only if it stays small and stable: a glance carries meaning that would take a sentence, but only if the reader has learned what each mark means. One or two per message, in predictable positions. And never information only in emoji: screen readers announce emoji by name, and some clients render boxes, so the text must stand alone.

## Common questions

**Won't all this emoji annoy people?**

It will if it becomes noise, which is why the skill is strict about restraint: one or two per message, a fixed vocabulary, no celebration on bad news, and matching the user's energy. A terse user who writes plain text gets terse plain text back. The emoji are landmarks and signals, not personality.

**My chat surface has no cards, buttons, or reactions. Is this skill useless there?**

No. Every pattern has a plain-text fallback: numbered lists stand in for cards, a one-line progress bar works anywhere text renders, and the restraint rules (one signal per moment, text that stands alone) matter more, not less, when the surface is thin. The skill degrades gracefully by design.

**Doesn't this add tokens to every reply?**

Less than the alternative. A card replaces the paragraphs of hedging an agent writes around an open question ("you could reply with X, or Y, or..."), and a reaction replaces the "just checking in!" messages that carry no information. The patterns that cost tokens are the ones that prevent longer, more confused exchanges.

## It's working if

- Users tap instead of type for bounded choices, and misread answers get rare.
- Nobody asks "is it still running?" during long work: the progress bar or the last reaction already answered.
- Long replies get skimmed successfully: a reader can find the decision, the warning, or the link by scanning the landmarks.
- The text reads fine with every emoji stripped out. If removing the emoji removes meaning, the skill is being misapplied.

## Where it fits

This is a reach-for-it-anytime standalone reference: it applies anywhere an agent talks to a human, regardless of the task underneath.

Its closest neighbours are [grilling](https://aihero.dev/skills-grilling), because an interview is a long series of bounded questions and cards are the fastest way to run one, and [handoff](https://aihero.dev/skills-handoff), because the ✅-plus-summary pattern is what makes an async handoff readable at a glance. When you are unsure which skill fits the situation, [ask-matt](https://aihero.dev/skills-ask-matt) routes you over the whole set.
