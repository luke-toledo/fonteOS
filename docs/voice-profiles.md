# Voice Profiles

How to build an intelligence file that makes Claude write like you — not like an AI approximation of you.

---

## What a voice profile is

A voice profile is an intelligence file that captures how you actually write and think. Not your positioning. Not your brand pillars. Your actual voice — the patterns, beliefs, rhythms, and hard limits that make your content sound like you rather than a generic AI output.

Without a voice profile, Claude writes serviceable prose that matches your instructions but not your sensibility. With one, it writes drafts that sound like you at your best — which you then refine rather than rewrite from scratch.

A voice profile lives at `intelligence/voice.md`. It's loaded before any content generation work.

---

## Structure

```markdown
---
type: intelligence
confidence: high
tags: [voice, content]
updated: YYYY-MM-DD
---

# Voice Profile — [Your Name]

> **Confidence:** High
> Connected to: [[core]], [[intelligence/audience]]

## Core Identity

Who you are, what you're building, and how you communicate. Your background,
your operating context, the lens you bring to your work. 2-3 paragraphs.
This isn't a bio — it's Claude understanding your worldview.

## Beliefs & Contrarian Takes

Q&A format. The questions that reveal your actual opinions, not your
positioning. What do you believe that most people in your field disagree with?
Where do you think the consensus is wrong? What do you say in private that
you'd now say in public?

Include 5-10 questions with your real answers. Stream of consciousness is fine.

Example format:
Q: What's the biggest lie in [your industry]?
A: [your actual take]

## Writing Mechanics

How you write first drafts. The specific mechanics:
- Sentence length and rhythm
- Words you reach for vs. words you avoid
- How you open (first lines, hooks)
- How you handle transitions
- Your relationship with data and evidence
- What makes your writing feel alive vs. what kills it

Include 2-3 example sentences that sound like you.

## Aesthetic Crimes

What triggers your bullshit detector. Be specific:
- The worst version of content in your space (name the patterns, not people)
- Phrases that signal someone isn't thinking
- The visual or structural sins
- What "trying too hard" looks like in your context

This section is as important as the positive voice cues.
Claude uses it to know what NOT to do.

## Voice & Personality

What you sound like when people lean in. Your humor style (or why you don't
use humor). How you handle vulnerability — do you go there, and if so, how?
How you handle being wrong or updating your view. Your relationship with
disagreement.

## Structural Preferences

How you organize content:
- Post or essay structure you return to
- How you pace ideas (fast to slow, specific to general, etc.)
- How long is too long
- How you handle complexity — do you simplify, or do you respect the complexity?

## Hard Nos

Non-negotiables. Content you would refuse to produce even if it drove growth.
Topics you won't touch. Takes you won't take. Lines you won't cross.

Be specific. "I don't do engagement bait" is less useful than "I won't end
posts with a question designed to generate comments rather than thought."

## Quick Reference Card

Always:
- [5-7 rules that govern everything]

Never:
- [5-7 anti-patterns to avoid]

Signature phrases or structural moves:
- [patterns that are distinctly yours]

Voice calibration note:
- [One sentence on the difference between your best and worst writing]

## Instructions for Claude

Explicit rules for how to use this document when generating content:

1. Load this file before any content drafting task.
2. First draft should feel like [your name] wrote it, not like "content."
3. If the brief conflicts with voice principles, flag it before writing.
4. Hard Nos are hard stops — do not proceed if a request violates them.
5. After drafting: check against Aesthetic Crimes. Rewrite anything that matches.
6. The Quick Reference Card is the shorthand — use it for quick checks.
7. Spirit over letter. Don't overfIt to any single rule at the expense of the whole.
```

---

## How to build it

Don't write this file yourself. Interview it out with Claude.

**Step 1 — Start the interview**

```
I want to build a voice profile intelligence file. Interview me — ask me
questions about how I write, what I believe, what I hate in content,
and what I sound like at my best. One question at a time.
Don't summarize until I say I'm done.
```

**Step 2 — Answer honestly**

Claude will ask questions like:
- "What's a piece of writing you've done that felt most like you? What made it work?"
- "What do you see in other content in your space that makes you cringe?"
- "What's a belief you hold that your peers would push back on?"
- "Read me a sentence that sounds like you."

Answer in stream of consciousness. Don't optimize. The voice profile is built from raw material, not edited answers.

**Step 3 — Crystallize**

After 20-30 minutes of Q&A, tell Claude to crystallize the answers into the voice profile structure above. Review it. Push back on anything that doesn't sound right. The file should make you say "yes, that's me" — not "close enough."

**Step 4 — Test it**

Ask Claude to write a short piece using the voice profile. Compare it to something you've actually written. Identify the gaps. Refine the file.

This is an ongoing artifact — update it as your voice evolves or as you discover new patterns.

---

## How it's used in sessions

Any session involving content should load the voice profile first. You can tell Claude explicitly:

```
Load intelligence/voice.md before we start drafting.
```

Or build a content agent (see [customization.md](customization.md)) that includes "load voice.md first" as step one of its instructions.

The voice profile is most powerful when combined with a humanize skill run after drafting. The voice file gives Claude your patterns; the humanize skill removes the AI patterns. Together, drafts need revision, not reconstruction.

---

## What good looks like

A good voice profile passes three tests:

1. **The recognition test** — Someone who knows your writing reads a Claude-generated draft with no attribution. They'd say it sounds like you.

2. **The anti-pattern test** — The Aesthetic Crimes section is specific enough that Claude can identify a draft line and say "this violates the aesthetic crimes list" rather than just "this doesn't sound right."

3. **The hard no test** — If you ask Claude to write something that violates your Hard Nos, it stops and says why rather than trying to thread the needle.

If a voice profile doesn't pass all three, keep refining. The investment compounds — every content session gets better.
