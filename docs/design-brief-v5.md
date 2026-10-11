# Seshi — design brief v5: the lead, its team, and personas

The v4 handoff is built and in daily use (0.18.1): the docked sheet on the right, the Inbox in
it, follow-up and "why" popovers, Seshi Walnut as the default theme, motion. This brief is one
new feature area. Same format as before: an interactive cell-grid HTML prototype at 160×45 (plus
how it degrades at 100×30 and grows at 240×60), with the state machine as the spec.

## What Seshi is (one line)

A terminal app where one person runs many AI coding agents (Claude Code, Codex, …) side by side,
each in its own git worktree, and only looks up when one needs them.

## The problem

Today you run six agents by hand: you brief each one, you remember which is doing what, and you
carry messages between them. With several going, you lose track of what a session is for, and of
which pane you're even in.

## The idea

You talk to **one** agent, the **lead**. It breaks the work up, starts other agents to do the
pieces, briefs them, checks on them and reports back to you. You can watch the whole team at a
glance, read what any two of them said to each other, and step in on any of them at any moment.

**Personas** are the roles the lead (or you) can call on: a saved name, a brief, which AI runs
it, and what it may do. "Reviewer, on Codex, read-only." "Test writer, on Claude, own worktree."

## What already exists (design on top of it)

- Any agent can already list the other sessions, read a session's screen, send one a message,
  start a helper in its own worktree, and wait for it. The plumbing is there; nothing shows it.
- The sidebar lists sessions under their folder, with state glyph and colour (working, needs
  you, done, idle, asleep `zzZ`), and an agent's own subagents under its row (`↳ name ×3`).
- The docked sheet on the right (72 columns; the whole pane column on narrow screens) holds the
  Inbox. It's the natural home for a new panel.
- `m` sends a session a follow-up from anywhere; `i` says why it has the state it has.

## What to design

For each: where it lives, what's on it, its states (empty, loading, error, many items), keyboard
and mouse, and how it looks at 100×30.

### 1. The team view

The centre of this brief. One lead, the agents it started, and the agents those started.

- **Shape.** A tree is the honest shape (who started whom). Is it a tree in the sheet, a tree
  that takes over the sidebar's section for that project, or a full view of its own? We removed
  an earlier "map" view because it was decoration; this one has to be something you'd leave open.
- **A node** shows: the persona or name, which AI and model, its state, how long it's been in
  that state, the task it was given in one line, and its last message to or from its parent.
- **The lines between nodes** are the conversation. Show when a message is going down (a task, a
  follow-up) or up (a result, a question), and let you open the exchange between two nodes as a
  short thread, newest last.
- **States to cover:** just you and the lead (nothing delegated yet); three helpers working; one
  helper blocked on a question the lead can't answer, so it needs *you*; one failed; one finished
  and waiting for the lead to read it; a helper that started helpers of its own; twelve helpers
  (does it scroll, fold, or summarise?); a second lead in another project at the same time.
- **Cost.** Each node costs money and memory. Show tokens or spend per node and for the whole
  team if there's a quiet place for it.

### 2. Stepping in

You can always click into any session and type. What's missing is making that clear to everyone.

- **Take over:** you take a helper from the lead. The lead is told, stops messaging it, and the
  node shows that you have it. **Hand back** returns it, with a line saying what you did.
- **Pause the team:** one action that stops the lead from starting or messaging anyone, for when
  it's going the wrong way. And its opposite.
- **Answer for the lead:** a helper asks the lead something the lead passes up to you. That's an
  Inbox item today; show how it reads when it came through two levels.
- **Stop one / stop all**, with a confirm that says exactly what closes and what work is kept.

### 3. Starting a lead

How does a session become the lead? Options to explore: a key on any agent row ("make this the
lead"), a "Start a team" action that asks for the goal and which personas it may use, or the lead
being a persona itself. Show the first minute: goal typed, lead thinking, first helper appearing.

### 4. Personas

- **The list**, in the sheet on the right: name, the AI and model it runs on, one line of what
  it's for, read-only or may-edit, own worktree or not. Yours (follow you everywhere) and this
  project's (live in the repo, shared with the team) as two groups.
- **Calling one yourself:** pick it, type the task, Enter. A session starts with the brief
  loaded, and its sidebar row carries the persona's name so you can tell what it is.
- **Making and editing one:** name, brief (a few paragraphs of text; this needs a real text
  area), AI, model, permissions. Duplicate, delete with confirm. Empty state for a new install,
  with three starter personas offered.
- **A persona at work** in the team view and the sidebar: how a row says "this is the Reviewer"
  without losing its state colour or the name of the folder it's in.

### 5. Knowing where you are

Related, and small. When you move between panes or sessions it's easy to miss that you moved.
Built today: the pane you land on flashes its border and title for a moment, and panes you're not
in are dimmed. Please refine both, and say how focus should read when the pane you jumped to
belongs to a team (does the team view follow you?).

## Things we already know

- A fresh session per call is the default for a persona. A long-running one that remembers
  earlier requests (a Planner) is a later option; leave room for it, don't design it fully.
- The lead is an ordinary agent session with a brief, not a new kind of thing. Anything a
  helper can show, the lead can show.
- Nothing here may hide an agent that needs you. The Inbox stays the one place that lists them.

## Constraints

- A terminal grid: one character per cell, 24-bit colour, bold/italic/underline/dim only. Nerd
  Font glyphs are used when the font has them (pill ends, the cog), with plain fallbacks. Lines
  between nodes are box-drawing characters; there are no curves and no half-cell positions.
- Cards draw with flush edges (thin eighth-block lines hugging the fill) or rounded corners, a
  setting; design for both.
- Everything works by keyboard (leader `Ctrl+Space` + a key; bare keys when the sidebar has
  focus; Alt+arrows move between panes) **and** by mouse (every chip clickable, hover states).
- Free keys: ADR 0009 (`docs/adr/0009-leader-keys-after-the-cut.md`) lists what's taken.
- Motion is short (90–140 ms) and can be turned off; nothing may depend on it.
- Windows Terminal, Ghostty, Alacritty, kitty, iTerm2. Windows, macOS and Linux.
- Every theme keeps working, and the contrast audit keeps passing.
