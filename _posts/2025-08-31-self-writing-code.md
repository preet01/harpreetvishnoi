---
layout: post
title: "Self Writing Code"
date: 2025-08-31
read_time: 3
---

Imagine walking into Netflix on a Monday morning. Overnight, a new feature has already been developed, tested, and rolled out to millions of users. Streaming is now 5% more efficient and bug reports are down by 10%.

That's the kind of future we're heading toward. Recently, I was reading about a feature Cursor introduced called "Shadow Workspace" — a system designed to let AI suggest edits safely in the background.

In its current form, when the AI proposes an edit, Cursor routes the change from the renderer process of the user's main window to a hidden shadow window tied to the same workspace. Inside this shadow window, the edit is applied to a separate Text Model instance, which is then passed through the Language Server Protocol (LSP) to generate lints, type errors, and semantic feedback.

> LSP was developed by Microsoft in 2016. It's like a translator between your code editor (VS Code, Cursor, etc.) and language-specific tooling (compilers, linters, analyzers). Instead of each editor re-implementing logic for every language, the editor talks to a language server over a standard protocol. Lints is just the editor yelling at you when you mess up — static code analysis that checks source code for potential errors before running it.

Cursor states: "Letting AIs get lints for their edits is one of the most impactful ways to improve code generation" when model parameters remain constant. Lints enable transformation from partially functional code to fully operational code, and prove especially valuable when context limitations require the AI to make initial assumptions about method or service calls.

Because the shadow window is isolated, the user's active editing session remains unaffected while the AI receives structured diagnostics. The lint results travel back via inter-process communication to the AI agent, which uses this feedback loop to iteratively refine its edits until the code passes lint checks.

## Suggesting Fix vs. Running Code

Running code is a bigger jump than linting. You need to actually save files to disk — and projects have side effects: build caches, node_modules, venv folders, logs. If the AI ran its changes in your real folder, it could break everything.

For now there are few ways to run AI-generated code and test it, and most have restrictions around the OS layer. Soon, Cursor will find a way to fix this and be one step closer to the ideal state of self-writing code. Lex Fridman calls this process scary — because at a point where the majority of coding tasks can be fully automated, developers will need to reskill themselves entirely.
