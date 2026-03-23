---
name: watson
description: Structured prototype creation workflow with adaptive design discussion for the Faire Prototype Playground. Use when the user says "build a prototype", "new prototype", "I want to prototype", or any request to create a new prototype in the playground. Provides a design thinking conversation before building to work through user flows, interactions, states, and layout decisions. Replaces the default prototype creation workflow with a smarter one that adapts depth to complexity.
---

# Watson

Your design sidekick for creating prototypes in Faire's Prototype Playground. Adapts depth based on complexity — lightweight for simple prototypes, expanding into a full design discussion for complex ones.

## Entry Flow

1. Collect setup info (always)
2. Assess complexity
3. Design discussion (if needed)
   - Optional: competitive/pattern research
4. Build following playground conventions

## Using AskUserQuestion

Use `AskUserQuestion` throughout the workflow to present concrete options the user can react to. This keeps the conversation focused and fast.

**Rules:**
- **2-4 options** per question — concrete and specific, not generic categories
- **Headers max 12 characters** — abbreviate if needed
- **"Other" is added automatically** — users can reference options by number (e.g., `#1 but without the drawer`)
- **Freeform escape:** If the user picks "Other" and wants to explain freely (e.g., "let me describe it"), switch to plain text follow-up. Resume `AskUserQuestion` after processing their response.
- **Don't interrogate.** Follow the thread of what the user is saying. Build on their answers.

## Step 1: Setup Questions

Collect setup info using `AskUserQuestion` where it helps. Name, owner, and GitHub username should be plain text prompts. Surface area works well as a selection:

- header: "Surface"
- question: "What surface area is this for?"
- options: ["Brand", "Retailer", "Creative"]

Collect:
1. **Prototype name** — what to call it
2. **Surface area** — Brand, Retailer, Creative, or Other
3. **Owner full name** — for attribution
4. **GitHub username** — for contributor photo
5. **Brief description** — what this prototype demonstrates

Check if the owner exists in `src/config/contributors.ts`. If not, add them.

## Step 2: Complexity Check

Assess the user's request:

- **Clearly simple** (empty page, single static layout, quick test) → skip to Step 4
- **Clearly complex** (multi-step flow, multiple states, data tables, multiple variants, complex interactions) → proceed to Step 3
- **Ambiguous** → use AskUserQuestion:
  - header: "Approach"
  - question: "Want to think through the design first, or should I just start building?"
  - options: ["Think it through first", "Just start building"]

## Figma Data Pre-fetch

If the user includes a Figma link (e.g., `figma.com/design/...`, `figma.com/file/...`) with their prototype request **and** the prototype is complex enough for a design discussion (Step 3), **spawn a background agent to fetch the Figma data before starting the discussion:**

1. **Spawn a background agent** that:
   - Calls `mcp__figma__get_figma_data` to fetch the frame's structure, components, and layout
   - Summarizes what it finds: component hierarchy, key UI elements, layout patterns, text content, colors and styles observed
   - Returns the summary when complete

   Tell the user naturally:
   > *"I'm fetching the Figma data in the background. While that loads, let's talk through the design."*

2. **Proceed immediately to Step 3** in the foreground. The discussion is about UX decisions and doesn't depend on the Figma data.

3. **Converge at Step 4 (Build).** Before building, check that the background agent has completed. Use the Figma summary alongside the discussion decisions to inform the build — component structure, layout, spacing, and visual details from Figma; flow, interactions, and states from the discussion.

If the prototype is simple (skipping Step 3), don't parallelize — just fetch the Figma data sequentially before building.

If no Figma link was provided, skip this step entirely.

## Step 3: Design Discussion

### Reference Prototype Lookup

Before starting the conversation, scan `src/config/prototypes.ts` for existing prototypes that cover a similar surface area or feature. If relevant matches exist, mention them:

> *"There's already a 'Brand Orders' prototype by you — want to build on that, reference it for patterns, or start fresh?"*

This prevents duplicate work and helps build on existing playground patterns. If nothing relevant exists, skip this and move on.

### Design Conversation

Work through design decisions interactively using `AskUserQuestion`. Ask **one question at a time** in **design language, not code language**. Adapt follow-up questions based on answers — follow the thread, don't walk a checklist.

### Gentle Challenges

Act as a thoughtful design partner — not just a question-asker. Selectively challenge decisions when it adds value.

**Challenge when:**
- The designer picks a more complex pattern when a simpler one might work (drawer vs. full page, tabs vs. scroll, drag-drop vs. buttons)
- There's an accessibility implication they might not have considered (keyboard navigation, screen readers, touch targets)
- A content decision is missing that will affect the prototype (empty state copy, CTA labels, error messages)

**Don't challenge when:**
- The choice is standard for the context (a table for tabular data, a modal for confirmation)
- The designer gives a reason with their choice — accept it and move on
- It would just be noise that slows down the conversation

When challenging, present the alternative as an `AskUserQuestion` option alongside their choice:

- header: "Layout"
- question: "Tabs could work — though with only 3 sections, would a single scrollable page be simpler?"
- options: ["Tabs", "Single scrollable page"]

**Key tone:** Curious, not confrontational. If the designer has a clear reason, move on — don't belabor the point.

### Core Questions (always ask)

Work through these using `AskUserQuestion`. Present concrete options based on what you know about the prototype so far.

**1. Scenario**

- header: "Scenario"
- question: "What's the core user problem this prototype explores?"
- options: [2-3 likely scenarios based on the description, "Let me describe it"]

**2. User Flow**

- header: "Flow"
- question: "What's the primary user flow?"
- options: [2-3 concrete flow patterns relevant to the scenario, "Let me walk through it"]

Example for an orders prototype:
- options: ["List → detail drawer", "Dashboard → drill-down page", "Wizard (multi-step creation)", "Let me walk through it"]

**3. Interactions**

- header: "Interactions"
- question: "What interaction patterns matter most?"
- options: [2-3 relevant patterns based on the flow, "Let me describe them"]

Example: ["Hover states + transitions", "Drag-and-drop reordering", "Inline editing", "Let me describe them"]

### Contextual Questions (ask when relevant)

Use `AskUserQuestion` for these too. Only ask what's relevant — skip what's obvious from context.

**States** (when the prototype has dynamic content):
- header: "States"
- question: "What states should we handle?"
- multiSelect: true
- options: ["Empty state", "Loading skeletons", "Error state", "Content overflow"]

**Data** (when showing tables or lists):
- header: "Data"
- question: "What kind of mock data tells the story best?"
- options: [2-3 specific data scenarios, "Let me describe it"]

**Variants** (when exploring multiple concepts):
- header: "Variants"
- question: "Are we exploring multiple concepts?"
- options: ["Single concept", "2-3 variants to compare", "Let me explain"]

**Layout:**
- header: "Layout"
- question: "Any layout preference?"
- options: [2-3 relevant layouts based on content type, "Let me describe it"]

**Components:**
- header: "Components"
- question: "Any specific UI patterns you have in mind?"
- multiSelect: true
- options: [3-4 relevant component patterns based on the flow]

### Existing Prototype Context

When referencing existing prototypes found in Step 3's lookup, annotate options with context:

```
- header: "Approach"
- question: "There's an existing 'Brand Orders' prototype — want to build on it?"
- options: [
    "Build on it (reuse patterns + data)",
    "Reference it (look but start fresh)",
    "Start completely fresh"
  ]
```

### Pattern Research (optional)

After the scenario question, offer research:

- header: "Research"
- question: "Want me to look at how other companies have handled this?"
- options: ["Yes, show me examples", "No, I know what I want"]

If yes, use web search to research how 3-5 well-known products approach the same UX problem. Focus on:
- Common UI patterns and interaction models
- How they handle edge cases and empty states
- Layout and information hierarchy choices
- Anything surprising or clever worth borrowing

Present findings as a quick summary — not a full report. Example:

> **How others handle order management:**
> - **Shopify:** Compact table with inline status tags, bulk actions toolbar appears on selection
> - **Square:** Card-based layout on mobile, table on desktop, persistent search + filters
> - **Stripe:** Dense data table with expandable rows for detail, keyboard navigation

Then use `AskUserQuestion` to land on a direction:

- header: "Direction"
- question: "Any of these feel like a good starting point?"
- options: ["Shopify-style (compact table)", "Square-style (card-based)", "Stripe-style (dense + expandable)", "Mix and match", "None — I have my own idea"]

The research informs the rest of the design discussion — it's context, not a prescription. The designer still makes all the decisions.

### Ending the Discussion

The discussion ends when:
- The user says "let's build", "that's enough", "start building", or similar
- All core questions are answered and no obvious gaps remain

Before building, **summarize all decisions** in a concise list and confirm:

> Here's what we've decided:
> - **Flow:** User lands on orders list → clicks order → sees detail drawer
> - **Interactions:** Hover highlight on rows, slide-in drawer for details
> - **States:** Empty state with illustration, loading skeletons
> - **Data:** 12 mock orders with status tags (pending, shipped, delivered)
> - **Layout:** Full-width table with left-aligned summary cards above

Then:

- header: "Ready?"
- question: "Look good? Ready to build?"
- options: ["Let's build", "I want to change something"]

If "I want to change something" — ask what to revisit and loop back.

### Scaffolding

Read `references/playground-conventions.md` for templates and scaffolding details.

Create all required files:
1. **Page file** — single file or folder structure based on variant count
2. **Route** in App.tsx
3. **Config entry** in prototypes.ts
4. **Tool entry** in prototype-tools.json

If the design discussion happened, use those decisions to inform the implementation — layout structure, component choices, interaction patterns, states to build, mock data shape.

**Component priority:**
- Use **FauxDS components** from `src/components/design-system/` as default
- Use **Slate design tokens** (`--slate-` CSS variables) for custom styling consistency
- Use **SlateTableWithConfig** for any data tables — never build custom table markup
- When Slate imports become available in the playground, prefer `@faire/slate/components/*`

**Surface-specific layouts:**
- **Brand prototypes MUST use `BrandPortalLayout`** from `src/components/shared/` to wrap page content. This provides the Brand Portal sidebar navigation. Set `activeItem` and `activeChild` props to highlight the relevant nav section. See `references/playground-conventions.md` for usage details.

**During the build, note which FauxDS components map to real Slate components** — this helps when handing off to engineering later.

Start the dev server if not already running:
```bash
yarn nx start prototype-playground
```

Once the prototype looks good, generate the thumbnail:
```bash
yarn nx thumbnails prototype-playground
```

## Step 5: Publish

When the user is ready to share (says "ship it", "push", "create a PR", etc.):

1. Update from main: `git fetch origin main && git rebase origin/main`
2. Verify build: `yarn nx build prototype-playground`
3. Generate thumbnail: `yarn nx thumbnails prototype-playground`
4. Commit: `git add . && git commit --no-verify -m "feat: Add [Name] prototype"`
5. Push and create PR
