# AI Skills

Agent skills for real engineering — not vibe coding.

Building real software with an agent is hard. Frameworks like BMAD and Spec-Kit try to help by owning the whole process, and in doing so take away your control: when the process has a bug, you can't reach in and fix it.

These skills go the other way. They're small, composable, and easy to adapt. Each one does a single job, and they call each other rather than hiding behind an orchestrator. They work with any model and any agent. Fork them, rewrite them, make them yours.

## Installation (30-second setup)

Two ways in, two philosophies. **The Claude Code plugin** installs the whole set as a managed, read-only bundle that updates when this repo does. **The `skills` CLI** copies editable skill files into your project, so you can hack on them per-project. Pick one — installing both leaves you with every skill twice.

### 1. Get the skills

<details>
<summary><strong>Claude Code</strong></summary>

```bash
claude plugin marketplace add kyeltech/ai-skills
claude plugin install ai-skills@kyeltech
```

Or, from inside a session:

```
/plugin marketplace add kyeltech/ai-skills
/plugin install ai-skills@kyeltech
```

The marketplace only needs adding once per machine. After that, `claude plugin update ai-skills` pulls the latest.

</details>

<details>
<summary><strong>Codex, Cursor, and other agents</strong></summary>

```bash
npx skills@latest add kyeltech/ai-skills
```

Pick the skills you want and which coding agents to install them on. **The installer lets you choose which skills to take, so make sure `setup-skills` is one of them** — most of the engineering skills read the config it writes.

To take a single skill:

```bash
npx skills@latest add kyeltech/ai-skills --skill=tdd
```

</details>

<details>
<summary><strong>For tinkerers</strong></summary>

Use the same installer on any agent, including Claude Code:

```bash
npx skills@latest add kyeltech/ai-skills
```

It writes the skills into your repo as ordinary files you own and can edit. Nothing updates behind your back — pull the latest when you want it:

```bash
npx skills update
```

</details>

<details>
<summary><strong>GitHub Copilot</strong></summary>

Copilot reads skills from fixed paths rather than an installer. Copy the folders you want:

- **Per project** → `.github/skills/<skill-name>/SKILL.md`
- **Personal, all projects** → `~/.copilot/skills/<skill-name>/SKILL.md`

```bash
git clone https://github.com/kyeltech/ai-skills.git /tmp/ai-skills
mkdir -p .github/skills
cp -R /tmp/ai-skills/skills/* .github/skills/
```

</details>

### 2. Run `/setup-skills`

In your agent, once per repo. It will:

- Ask which issue tracker you want to use (GitHub, GitLab, or local markdown files)
- Ask what labels you apply when you triage tickets (`/triage` uses labels)
- Ask where to save any docs the skills create

### 3. That's it — run `/ask-me` if you're not sure where to start.

## Why These Skills Exist

Each of these fixes a failure mode that shows up over and over with coding agents.

### #1: The agent didn't build what I wanted

**The problem.** The most common failure in software isn't bad code, it's misalignment. You thought the agent understood. It didn't. You find out after it's built the wrong thing.

**The fix** is a grilling session — making the agent interview *you* in detail before a line of code gets written:

- [`/grill-me`](./skills/grill-me/SKILL.md) — for anything that isn't a codebase
- [`/grill-with-docs`](./skills/grill-with-docs/SKILL.md) — same interview, but it writes down what it learns

Use one of them *every* time you start something non-trivial. They're the highest-leverage skills here.

### #2: The agent is way too verbose

**The problem.** An agent dropped into your project doesn't know your jargon, so it uses twenty words where one would do. "There's a problem when a lesson inside a section of a course is given a spot in the file system" instead of "there's a problem with the materialization cascade."

**The fix** is a shared language — a `CONTEXT.md` that decodes your project's vocabulary. [`/grill-with-docs`](./skills/grill-with-docs/SKILL.md) builds it as a side effect of the interview, and [`/domain-modeling`](./skills/domain-modeling/SKILL.md) keeps it sharp.

The payoff compounds: variables and files get named consistently, the codebase gets easier for the agent to navigate, and the agent burns fewer tokens thinking because it has a more concise language to think in.

### #3: The code doesn't work

**The problem.** You and the agent are aligned, and it *still* produces something broken. That's a feedback problem — without a signal telling it how the code actually behaves, it's flying blind.

**The fix** is feedback loops: static types, browser access, and real tests. [`/tdd`](./skills/tdd/SKILL.md) drives a red-green-refactor loop — failing test first, then the fix — and carries opinions about what separates a good test from a useless one.

When something is already broken, [`/bug-finder`](./skills/bug-finder/SKILL.md) refuses to theorise until it has one command that reliably goes red on *this* bug, then minimises, hypothesises, fixes, and locks it down with a regression test.

### #4: We built a ball of mud

**The problem.** Agents accelerate coding, which means they accelerate entropy. Codebases get complex faster than they ever have.

**The fix** is treating design as a daily practice, not a rewrite you'll do later. The best modules are *deep* — a lot of behaviour behind a small interface. That vocabulary lives in [`/codebase-design`](./skills/codebase-design/SKILL.md) and runs underneath the rest.

[`/improve-codebase-architecture`](./skills/improve-codebase-architecture/SKILL.md) surveys the codebase for deepening opportunities and hands you the candidates as an HTML report. Run it every few days. It's a survey, not a rescue — it will find real candidates in an old codebase, but it won't untangle the mud for you.

## Reference

Skills split on one axis: **who can invoke them.**

**User-invoked** skills only run when you type them (`/grill-me`). Their job is to orchestrate. **Model-invoked** skills can be typed by you *or* reached for automatically when the task fits — they hold the reusable discipline. A user-invoked skill may call model-invoked ones, but never another user-invoked one.

### User-invoked

| Skill | What it does |
| --- | --- |
| [`ask-me`](./skills/ask-me/SKILL.md) | Router over everything here. Ask which skill or flow fits your situation. |
| [`setup-skills`](./skills/setup-skills/SKILL.md) | Configure a repo for these skills — issue tracker, triage labels, doc layout. Run once, first. |
| [`grill-me`](./skills/grill-me/SKILL.md) | A relentless interview to sharpen a plan or design. Stateless — saves nothing. |
| [`grill-with-docs`](./skills/grill-with-docs/SKILL.md) | The same interview, but it builds your domain model as it goes, updating `CONTEXT.md` and ADRs inline. |
| [`to-spec`](./skills/to-spec/SKILL.md) | Turn the current conversation into a spec and publish it to the tracker. No interview — pure synthesis. |
| [`to-tickets`](./skills/to-tickets/SKILL.md) | Break a plan or spec into tracer-bullet tickets, each declaring its blocking edges. |
| [`implement`](./skills/implement/SKILL.md) | Build the work from a spec or ticket, driving `/tdd` at agreed seams and closing with `/code-review`. |
| [`triage`](./skills/triage/SKILL.md) | Move incoming issues and external PRs through a state machine of triage roles into agent-ready briefs. |
| [`wayfinder`](./skills/wayfinder/SKILL.md) | Plan work too big for one session as a map of decision tickets, resolved one at a time until the way is clear. |
| [`improve-codebase-architecture`](./skills/improve-codebase-architecture/SKILL.md) | Scan for deepening opportunities, report them visually, then grill through whichever one you pick. |
| [`handoff`](./skills/handoff/SKILL.md) | Compact the current conversation into a document another agent can pick up. |
| [`teach`](./skills/teach/SKILL.md) | Teach you a concept across multiple sessions, using the current directory as a stateful workspace. |

### Model-invoked

| Skill | What it does |
| --- | --- |
| [`grilling`](./skills/grilling/SKILL.md) | The interview primitive behind `grill-me`, `grill-with-docs`, `triage` and `wayfinder`. |
| [`tdd`](./skills/tdd/SKILL.md) | Red-green-refactor, one vertical slice at a time, with opinions on what makes a test worth keeping. |
| [`bug-finder`](./skills/bug-finder/SKILL.md) | Disciplined diagnosis loop for hard bugs and performance regressions. Feedback loop first, theories second. |
| [`code-review`](./skills/code-review/SKILL.md) | Two-axis review of a diff — Standards and Spec — run as parallel sub-agents so neither pollutes the other. |
| [`domain-modeling`](./skills/domain-modeling/SKILL.md) | Build and sharpen the project's ubiquitous language; record hard-to-reverse decisions as ADRs. |
| [`codebase-design`](./skills/codebase-design/SKILL.md) | Vocabulary for deep modules — interface, depth, seam, adapter, leverage, locality. |
| [`prototype`](./skills/prototype/SKILL.md) | A throwaway prototype that answers one design question. Keep the answer, delete the code. |
| [`research`](./skills/research/SKILL.md) | Investigate a question against primary sources and leave a cited Markdown file in the repo. |
| [`resolving-merge-conflicts`](./skills/resolving-merge-conflicts/SKILL.md) | Work an in-progress merge or rebase hunk by hunk, resolving by intent. Never `--abort`. |

## Repo layout

```
.claude-plugin/
  plugin.json         # the Claude Code plugin manifest
  marketplace.json    # makes this repo its own single-plugin marketplace
skills/
  <skill-name>/
    SKILL.md          # the skill itself; frontmatter name must match the folder
    agents/
      openai.yaml     # optional Codex-specific display metadata
    *.md              # optional reference files the skill points at
```

A skill is just a folder with a `SKILL.md` in it. The frontmatter carries `name`, `description`, and optionally `disable-model-invocation: true` (making it user-invoked) and `argument-hint`. To add one, create the folder, write the file, and add its path to `skills` in `.claude-plugin/plugin.json`.

## Credits
used under the MIT License, then modified.

This repo is MIT licensed — see [LICENSE](./LICENSE), and [NOTICE](./NOTICE) for upstream copyright.
