# Watson

A Claude Code skill for structured prototype creation in Faire's Prototype Playground. Watson acts as a design sidekick — adapting depth based on complexity, from lightweight scaffolding to full design discussions with competitive research.

## What it does

- **Design discussion** before building — works through user flows, interactions, states, and layout decisions using structured questions
- **Figma pre-fetch** — when a Figma link is provided, fetches frame data in the background while the discussion runs in parallel
- **Complexity-adaptive** — skips the discussion for simple prototypes, expands for complex ones
- **Pattern research** — optionally researches how other companies solve the same UX problem
- **Full scaffolding** — creates all required files (page, route, config, tool entry) following playground conventions

## Installation

Copy files into your Claude Code config:

```bash
# Skill
cp -r skills/watson/ ~/.claude/skills/watson/

# Command
cp -r commands/watson/ ~/.claude/commands/watson/
```

## Usage

### `/watson` — Full prototype workflow
Invoke when starting a new prototype. Runs the complete flow: setup questions, complexity check, design discussion, build, and publish.

### `/watson:discuss` — Mid-iteration design discussion
Invoke anytime during iteration to think through a specific design decision. Reads the current prototype code and facilitates a focused discussion using structured questions.

## Structure

```
skills/watson/
  SKILL.md                              # Main skill definition
  references/
    playground-conventions.md           # Build templates and scaffolding reference

commands/watson/
  discuss.md                            # /watson:discuss command
```
