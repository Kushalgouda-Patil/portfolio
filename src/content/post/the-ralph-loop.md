---
title: "The Ralph Loop: Let the AI Keep Trying"
description: "A single bash loop that lets AI agents retry until they get it right — no more manual back-and-forth debugging."
publishDate: "2026-05-26"
tags: ["ai", "llm", "technology"]
---

*A technique so simple it fits in one line. Yet it's changing how developers build software.*

You type a prompt. It gives you some code. You test it. Something breaks. You go back, explain the error, ask it to fix it. It gives you new code. You test again. Repeat.

That back-and-forth is exhausting. And the longer it goes, the worse the AI gets – because it starts getting confused by all the previous attempts piling up in the conversation.

The Ralph Wiggum Loop fixes this with a single bash script:

```bash
while :; do cat PROMPT.md | claude-code ; done
```

## Who Is Ralph Wiggum?

Ralph Wiggum is a character from The Simpsons. He's not the smartest kid. He fails constantly. But he never, ever stops trying. That's exactly the vibe of this technique.

The developer who invented this, Geoffrey Huntley, named it after Ralph because the approach isn't about being clever it's about being relentlessly persistent. Run, fail, try again, run, fail, try again until it works.

## So What Is the Ralph Loop, Actually?

Imagine you have a task: *Build me an API endpoint that creates a user.*

Instead of going back and forth with an AI in a chat window, you:

1. Write your task in a file called `PROMPT.md`
2. Run the Ralph loop
3. Walk away

The AI reads your prompt, writes some code, and exits. The loop immediately restarts it. The AI reads the prompt again – fresh, with no memory of the previous attempt – looks at what code already exists, figures out what's still broken, and tries again.

This keeps going until your tests pass or you hit the iteration limit.

You come back from lunch. There are 5 commits in your repo. The feature is done.

## Why Does Restarting Fresh Actually Help?

Here's the problem with long AI conversations: the AI remembers everything you said. That sounds like a good thing. It's not.

After 20 turns of debugging, the AI's "memory" is full of:

- Every error message from every failed attempt
- Old versions of code it already changed
- Half-finished ideas it tried and abandoned

It gets confused. It starts mixing up old attempts with new ones. It hallucinates. The quality drops.

With Ralph, every single attempt is a fresh start. The AI doesn't remember the previous failures. It just sees the current state of your code and your original instructions.

**The key idea: progress lives in your files and git commits, not in the AI's memory.**

The AI is the worker. Git is the memory.

## A Real Example

Say you want to add a login endpoint to your backend. Your `PROMPT.md` might look like:

```
Build a POST /login endpoint that:
- Accepts email and password
- Returns a JWT token on success
- Returns 401 on wrong credentials
- Has a passing test in tests/login_test.rs
```

You run Ralph. The AI writes the endpoint. Maybe the test fails. The loop restarts. The AI sees the failing test, figures out what's wrong, fixes it. Eventually the test passes. Loop exits.

You didn't type a single follow-up message.

## Where It Works Best

- Small, well-defined tasks (not "rewrite the whole app")
- Anything where you can write a test that says "this is done"
- Boilerplate-heavy work like CRUD endpoints, SDK integrations, webhook handlers
- Bug fixes where you can write a failing test first

## Where It Doesn't Work

- Big tasks that touch the entire codebase
- Things where "done" is hard to define precisely
- Tasks that need your judgment call mid-way