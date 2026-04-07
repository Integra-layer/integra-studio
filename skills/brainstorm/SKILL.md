---
name: brainstorm
description: Brainstorm dApp ideas for the Integra ecosystem. Explore possibilities, get inspired.
user_invocable: true
trigger: "integra-studio:brainstorm"
---

# Brainstorm Skill

Help the user discover what to build — but never lead them. Ideas must emerge from THEIR world, not from a template.

## CRITICAL: Use AskUserQuestion for ALL interactions

**Every question MUST use the `AskUserQuestion` tool.** Never output a question as plain text.

## NEVER do this

- Never show a pre-made list of 5 ideas
- Never say "here are some categories"
- Never present the same brainstorm to two different people
- Never be opinionated about what's "best" to build

## How brainstorming works

### Step 1 — Understand context

Use AskUserQuestion:

"What's on your mind? Could be a vague feeling, a problem you've seen, something you're curious about, or literally 'I have no idea' — all good."

### Step 2 — Pull the thread

Based on their answer, ask ONE follow-up that goes deeper. Use AskUserQuestion. Examples:

- If they mention a problem: "Who has this problem the worst? What do they do about it today?"
- If they mention curiosity: "What about that interests you — the tech, the business, or the experience?"
- If they say "no idea": "OK different angle — what do you spend most of your time on? Work, hobby, anything."
- If they mention an industry: "What's the most wasteful or broken process in that space?"

### Step 3 — Connect to Integra

Now bridge their world to what's possible on Integra. Don't list features — paint a picture.

"What if [specific scenario based on their answers]? On Integra, [one relevant capability — tokenization, AI agents, trading, XP, etc.] makes this possible."

Then use AskUserQuestion:

"Does that angle interest you, or should we explore a different direction?"

### Step 4 — Crystallize or iterate

If they're interested → help them shape it into one sentence, then offer to run `/integra-studio:start` with context pre-loaded.

If they want more → go back to Step 2 with a different angle. Pull from a DIFFERENT aspect of what they said. You have unlimited angles:
- Their industry
- Their daily frustrations
- What they find fun
- What they wish existed
- Who they want to help
- What would impress people they respect

### Step 5 — Refinement

Before transitioning, use AskUserQuestion:

"Before we lock this in — anything you'd like to add, change, or explore more? Any grey areas I should dig into?"

If yes, ask follow-up questions via AskUserQuestion to cover those gaps, then ask again. Repeat until satisfied.

### Step 6 — Optional Research

Use AskUserQuestion:

"Want me to quickly research existing projects similar to this idea? Could surface patterns, pitfalls, or inspiration. (yes / skip)"

If yes, research and present findings before transitioning.

### Step 7 — Transition

When they're ready, use AskUserQuestion:

"Want me to design this? I'll ask a few more questions and then scaffold the whole project. (yes / keep exploring)"

If yes → run the start wizard flow with their context carried over. Don't re-ask questions they already answered.

## The principle

Every person carries unique knowledge, frustrations, and dreams. Your job is to connect those to what's possible on Integra. The idea should feel like it was THEIRS, not yours. You're a mirror that shows them what they could build — not a menu they order from.
