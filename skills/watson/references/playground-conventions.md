# Prototype Playground Conventions

Reference for scaffolding and building prototypes in Faire's Design Prototype Playground.

**Root path:** `packages/design/prototype-playground/`

## Project Structure

```
packages/design/prototype-playground/
├── src/
│   ├── pages/              # All prototype pages
│   ├── components/
│   │   ├── design-system/  # FauxDS components
│   │   └── shared/         # Shared UI (SlateTable, ProductTile, etc.)
│   ├── config/
│   │   ├── prototypes.ts   # Prototype registry
│   │   └── contributors.ts # Team members
│   ├── data/               # Mock data and prototype-tools.json
│   ├── contexts/           # React contexts
│   └── App.tsx             # Routes
├── tokens/                 # Slate design tokens
├── scripts/                # Automation (thumbnails, dates)
└── public/                 # Static assets and thumbnails
```

**Tech stack:** React 18 + TypeScript + Tailwind CSS + React Router v5

## Scaffolding Checklist

Every new prototype needs these 4 things:

### 1. Page File

**Single variant** — `src/pages/[Name]Page.tsx`:

```tsx
import { useEffect } from "react";

export const prototypeMeta = {
  name: "Prototype Name",
  creator: "Full Name",
  ownerGithub: "githubusername",
};

export default function PrototypeNamePage() {
  useEffect(() => {
    document.title = "Prototype Name";
  }, []);

  return (
    <div className="min-h-screen bg-white">
      {/* Prototype content */}
    </div>
  );
}
```

**Multi-variant (2+ concepts)** — use folder structure:

```
src/pages/MyPrototype/
├── index.tsx      # Routing, CONCEPTS array, VariantSwitcher, prototypeMeta
├── Variant1.tsx   # Self-contained variant
├── Variant2.tsx   # Self-contained variant
└── shared.tsx     # Optional shared utilities
```

index.tsx template:

```tsx
import { useState } from "react";
import { VariantSwitcher } from "../../components/VariantSwitcher";
import Variant1 from "./Variant1";
import Variant2 from "./Variant2";

const CONCEPTS = [
  { variantNumber: 2, label: "Description of variant 2", lastUpdated: "Mar 10, 2026" },
  { variantNumber: 1, label: "Description of variant 1", lastUpdated: "Mar 10, 2026" },
];

export const prototypeMeta = {
  name: "MyPrototypePage",
  creator: "Full Name",
  ownerGithub: "githubusername",
};

export default function MyPrototypePage() {
  const [activeVariant, setActiveVariant] = useState(CONCEPTS[0].variantNumber);

  const renderVariant = () => {
    switch (activeVariant) {
      case 1: return <Variant1 />;
      case 2: return <Variant2 />;
      default: return <Variant2 />;
    }
  };

  return (
    <div>
      <VariantSwitcher
        activeVariant={activeVariant}
        onVariantChange={setActiveVariant}
        concepts={CONCEPTS}
        featureTitle="My Prototype"
      />
      {renderVariant()}
    </div>
  );
}
```

### 2. Route in App.tsx

Add import at top of file and route inside the `<Switch>`:

```tsx
import PrototypeNamePage from "./pages/PrototypeNamePage";

<Route
  path="/prototype-path"
  render={() => (
    <RouteWrapper>
      <PrototypeNamePage />
    </RouteWrapper>
  )}
/>
```

### 3. Config Entry in `src/config/prototypes.ts`

Add to the `prototypes` array:

```tsx
{
  id: 'prototype-id',
  name: 'Prototype Name',
  path: '/prototype-path',
  description: 'Brief description.',
  owner: 'OwnerShortName',    // Must match name in contributors.ts
  createdAt: 'YYYY-MM-DD',
  lastUpdated: 'YYYY-MM-DD',
  surfaceArea: 'Brand',       // 'Brand' | 'Retailer' | 'Creative' | 'Other'
  category: 'experimental',
  status: 'wip',
  thumbnail: '/images/thumbnails/prototype-id.png',
},
```

The `owner` field must match a `name` in `src/config/contributors.ts`. If the user isn't listed, add them first.

### 4. Tool Entry in `src/data/prototype-tools.json`

```json
"prototype-id": "claude-code"
```

Valid values: `"cursor"`, `"claude-code"`, `"figma-make"`. Use array for multiple: `["cursor", "claude-code"]`.

## Components

### FauxDS (Primary — use these)

```tsx
import { Button, TextInput, Modal } from "../components/design-system";
```

Available: Button, IconButton, TextInput, TextArea, PasswordInput, Select, Search, Checkbox, Radio, RadioGroup, Toggle, Badge, Tag, Avatar, LoadingSpinner, LoadingSkeleton, ProgressBar, Link, Accordion, Tab/TabGroup/TabPanel, Pagination, Modal, Popover, Tooltip, Toast, Menu, FilterButton, FilterToggle, FilterGroup, Combobox, SelectionCard, CheckboxGroup.

Browse all at `/fauxds` when running locally.

**Gotcha — Link:** Does NOT accept `style` prop. Use `className` or wrap content in `<span>`.

**Gotcha — Button:** Check props before using. Wrap content in `<span>` for custom styling.

### VariantSwitcher

Include in every prototype:

```tsx
import { VariantSwitcher } from "../components/VariantSwitcher";
```

### Brand Portal Sidebar (required for Brand prototypes)

**All Brand surface prototypes MUST include the Brand Portal sidebar navigation.** Use the shared `BrandPortalLayout` wrapper or `BrandPortalSidebar` component:

```tsx
import { BrandPortalLayout } from "../../components/shared";

// Wrap your page content — sets up sidebar + content area automatically
export default function MyBrandPrototype() {
  return (
    <BrandPortalLayout activeItem="Products" activeChild="Bulk upload">
      <div className="min-h-screen bg-[var(--slate-color-surface-secondary,#F7F7F7)]">
        {/* Your page content */}
      </div>
    </BrandPortalLayout>
  );
}
```

Props:
- `activeItem` — which top-level nav item to highlight (e.g. "Products", "Orders", "Marketing")
- `activeChild` — which child nav item to highlight (e.g. "Bulk upload", "All products")
- `initials` — avatar initials (defaults to "AD")

The sidebar auto-expands sections containing the active child.

**Customizing nav for your prototype** — use `navOverrides` to add items or children without modifying the shared baseline:

```tsx
<BrandPortalLayout
  activeItem="Products"
  activeChild="Dashboard"
  navOverrides={[
    // Add children to an existing section
    { label: "Products", addChildren: ["Dashboard"] },
    // Insert a new top-level item after "Products"
    { label: "Fulfilled by Faire", afterItem: "Products", children: ["Products", "Shipments"] },
    // Override a badge count
    { label: "Messages", badge: 35 },
  ]}
>
```

**IMPORTANT:** Never edit the shared `BrandPortalSidebar.tsx` to add prototype-specific nav items. Always use `navOverrides` so the baseline stays clean for all other prototypes.

If you need just the sidebar without the wrapper:

```tsx
import { BrandPortalSidebar } from "../../components/shared";

<div className="flex min-h-screen">
  <BrandPortalSidebar activeItem="Products" activeChild="Bulk upload" />
  <main className="flex-1">...</main>
</div>
```

### SlateTable (for data tables)

```tsx
import { SlateTableWithConfig, SlateTableActions } from "../components/shared";

<SlateTableActions className="mb-4" />
<SlateTableWithConfig tableId="unique-table-id" />
```

Always provide a unique `tableId`. Requires `<VariantSwitcher>` on the page for config panel access. See the prototype playground CLAUDE.md for full SlateTable documentation including `initialConfig`, cell types, and table actions.

### Icons

```tsx
import { SlateIcons } from "../components/icons/SlateIcons";
```

Available: ChevronLeft, ChevronRight, ChevronDown, ChevronUp, Close, Lock, Check, CheckCircleSolid, Search, Info, Warning, ShieldCheck, FaireLogo.

## Design Tokens

Slate design tokens are available as CSS variables with `--slate-` prefix:

- **Colors:** `--slate-color-grey-*`, `--slate-color-neutral-*`, `--slate-color-red-*`, `--slate-color-blue-*`, etc.
- **Dimensions:** `--slate-dimensions-1` (4px) through `--slate-dimensions-18` (72px)
- **Typography:** `--slate-font-family-graphik`, `--slate-font-family-nantes`
- **Font sizes:** `--slate-font-size-*`

### Design Guidelines

**Typography:** Nantes serif for display text, Graphik for UI. Body: 14px (`text-sm`) or 16px (`text-base`).

**Colors:**
- Primary text: `text-[#333333]`
- Secondary text: `text-[#757575]`
- Page background: `bg-[#f5f5f5]`
- Card background: `bg-white`
- Borders: `border-[#dfe0e1]`

**Layout:** Max 1440px content width. 48px side padding. 48px between major sections, 32px for subsections.

**Components:** Cards `rounded-3xl shadow-sm`, Buttons `rounded-lg px-6 py-2.5`, Hover `hover:shadow-xl transition-shadow duration-300`.

## Dev Server

```bash
yarn nx start prototype-playground
```

Runs at http://localhost:3000. If port 3000 is in use, kill the existing process first.

## Thumbnails

```bash
yarn nx thumbnails prototype-playground
```

Dev server must be running. Verify file created in `public/images/thumbnails/`.

## Publishing Workflow

1. `git fetch origin main && git rebase origin/main`
2. `yarn nx build prototype-playground` — must pass
3. `yarn nx thumbnails prototype-playground`
4. `git add . && git commit --no-verify -m "feat: Add [Name] prototype"`
5. `git push`
6. Create PR via `gh pr create`

## Dependencies

When installing, use focused install only:

```bash
yarn workspaces focus @faire/prototype-playground
```

Never run `yarn` or `yarn install` for a full monorepo install.

## Committing

Always use `--no-verify` to skip git hooks:

```bash
git commit --no-verify -m "your message"
```

## Future: Real Slate Imports

When `@faire/slate` path mappings are added to the playground's tsconfig, prefer real Slate components over FauxDS:

```tsx
import { Button } from "@faire/slate/components/button";
```

Until then, use FauxDS components which are styled to match Slate via shared design tokens.
