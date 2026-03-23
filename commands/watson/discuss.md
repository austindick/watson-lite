---
name: watson:discuss
description: Trigger a design discussion with Watson mid-iteration
argument-hint: "[question or area to discuss]"
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
---

# Watson: Discuss

A focused design discussion for when you're already building and need to think something through.

## When to Use

- Mid-build, hit a design decision you're unsure about
- Want to explore alternatives for a specific interaction or layout
- Rethinking a flow or pattern after seeing it built
- Need to decide between multiple approaches

## How It Works

### 1. Understand the Context

Read the current prototype files to understand what's already built. Check:
- The page file(s) for current structure and components
- Any existing patterns or decisions already in play

### 2. Frame the Discussion

If the user provided a specific question (e.g., `/watson:discuss should this be a drawer or a full page?`), start there.

If they just invoked `/watson:discuss` with no specifics, ask:

- header: "Discuss"
- question: "What do you want to think through?"
- options: [2-3 likely areas based on what's currently built, "Something else"]

### 3. Discuss Using AskUserQuestion

Use the same `AskUserQuestion` pattern as the main Watson skill:

- **2-4 options** per question — concrete and specific
- **Headers max 12 characters**
- **"Other" is added automatically** — users can reference by number
- **Freeform escape:** If the user picks "Other" and wants to explain freely, switch to plain text. Resume `AskUserQuestion` after.

**Be opinionated.** You've seen the code — reference what's already built when framing options:

```
- header: "Navigation"
- question: "You're using tabs for 4 sections — the content in 'Settings' is pretty short. Combine it with 'Details'?"
- options: ["Keep 4 tabs", "Merge Settings into Details (3 tabs)", "Let me think about it"]
```

**Annotate options with code context** when it helps the decision:

```
- header: "Pattern"
- question: "How should the filter work?"
- options: [
    "Dropdown filters (you already have FilterButton imported)",
    "Sidebar filters (new pattern, more space)",
    "Inline toggles (minimal, fits current layout)"
  ]
```

### 4. Gentle Challenges

Same rules as the main Watson skill — challenge when:
- A simpler pattern might work
- There's an accessibility implication
- A content decision is missing

Don't challenge when the choice is standard or the designer gives a reason.

### 5. Land the Decision

When the discussion reaches a conclusion, summarize clearly:

> **Decision:** Using a slide-in drawer for order details instead of navigating to a new page. Drawer will show order summary + line items with a "View full details" link for overflow.

Then:

- header: "Next"
- question: "Want me to implement this change, or keep discussing?"
- options: ["Implement it", "Keep discussing", "I'll handle it"]

If "Implement it" — make the changes to the prototype code.
If "Keep discussing" — continue the conversation.
If "I'll handle it" — done, return control to the user.
