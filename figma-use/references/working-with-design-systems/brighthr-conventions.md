# BrightHR Design System Conventions

> **Also load:** [brighthr-lumen-components.md](brighthr-lumen-components.md) — the complete Lumen component reference with every component key, props, and color token catalogue. That file is the primary lookup for "what key do I use to import X component?" questions.

When working with BrightHR Figma files, prioritize metadata that enables AI-assisted workflows. The following conventions ensure that MCP tools can correctly interpret and use the design system.

---

## BrightHR Design System Sources (MANDATORY FOR SCREEN BUILDING)

**When building BrightHR screens, ONLY use components, variables, and styles from these specific files:**

### 1. Main Component Library
**File:** [Components: Lumen (Bright-UI)](https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?m=auto&node-id=2489-6991&t=APCPvfB1uOfd6NfH-1)
- **File Key:** `3dNASwqNXrYbGQyHsf1Sgg`
- **Usage:** Use ONLY components from this file for all UI primitives (buttons, badges, inputs, cards, navigation, etc.)
- **CRITICAL:** Do NOT use components from other published design systems (BEX, dotcom, Peninsula, Mobile Apps, etc.) even if they appear in search results

### 2. Icons
**File:** [Components: Icons v2.0](https://www.figma.com/design/2lYWKHwYwf4iAYJxILH2Wr/Components--Icons-v2.0?m=auto&node-id=101-1306&t=2yn2Ohxb9kYXuwll-1)
- **File Key:** `2lYWKHwYwf4iAYJxILH2Wr`
- **Usage:** Use only the official BHR Icons from this file
- **CRITICAL:** Do NOT use icons from other libraries

### 3. Typography Variables
**File:** [Web Typography](https://www.figma.com/design/OudUc4pq10fGO5oHkA674i/Web-Typography?m=auto)
- **File Key:** `OudUc4pq10fGO5oHkA674i`
- **Font Family:** **Albert Sans** (ONLY — never use Inter, Roboto, or any other font)
- **Usage:** Use only the typography variables defined in this file
- **CRITICAL:** Always use `await figma.loadFontAsync({family: "Albert Sans", style: "Regular"})` (or "Medium", "SemiBold", "Bold") before any text operations

### 4. Color Tokens & Variables
**File:** [BrightHR Colours & Styles](https://www.figma.com/design/Tutg1Xnl9e07uED4DZjyUB/BrightHR--Colours---Styles?m=auto)
- **File Key:** `Tutg1Xnl9e07uED4DZjyUB`
- **Usage:** Use only the color variables and tokens defined in this file
- **CRITICAL:** Never hardcode hex colors — always bind to variables from this file

---

## New File Layout Structure (CRITICAL)

When creating a new BrightHR screen from scratch, follow this exact layout structure:

### 1. Create Page Wrapper Frame

```js
// Find clear space
let maxX = 0;
for (const child of figma.currentPage.children) {
  maxX = Math.max(maxX, child.x + child.width);
}

const wrapper = figma.createFrame();
wrapper.name = "Homepage"; // Or appropriate screen name
wrapper.layoutMode = "VERTICAL";
wrapper.primaryAxisAlignItems = "CENTER";
wrapper.counterAxisAlignItems = "CENTER";
wrapper.resize(1440, 768);
wrapper.layoutSizingHorizontal = "FIXED";
wrapper.layoutSizingVertical = "HUG";
wrapper.x = maxX + 200;
wrapper.y = 0;
```

### 2. Create Layout Container (Horizontal)

Inside the wrapper, create a Layout Container frame with auto-layout set to Horizontal:

```js
const layoutContainer = figma.createFrame();
layoutContainer.name = "Layout Container";
layoutContainer.layoutMode = "HORIZONTAL";
layoutContainer.layoutSizingHorizontal = "FILL";
layoutContainer.layoutSizingVertical = "HUG";
wrapper.appendChild(layoutContainer);
```

### 3. Add Side Nav (Required)

Import and add the Side Nav component as the first child of the Layout Container. It sits flush against the left edge:

```js
// Import Side Nav from Lumen library
const sideNavKey = "SIDE_NAV_COMPONENT_KEY"; // Get from component search
const sideNavSet = await figma.importComponentSetByKeyAsync(sideNavKey);
const sideNav = sideNavSet.defaultVariant || sideNavSet.children[0];
const sideNavInstance = sideNav.createInstance();
sideNavInstance.setProperties({ "Sub menu": "Off" }); // Default: Sub menu Off
layoutContainer.appendChild(sideNavInstance);
```

### 4. Create Main Frame (Content Container)

Create a Main frame to hold all content sections. This sits to the right of the Side Nav:

```js
const main = figma.createFrame();
main.name = "Main";
main.layoutMode = "VERTICAL";
main.primaryAxisAlignItems = "CENTER";
main.counterAxisAlignItems = "CENTER";
main.layoutSizingHorizontal = "FILL"; // Stretches to available space
main.layoutSizingVertical = "HUG"; // Grows with content
layoutContainer.appendChild(main);
```

### 5. Add Global Header (Required)

Import and add the Global Header as the first child of the Main frame. It sits flush to the top edge:

```js
// Import Global Header from Lumen library
const headerKey = "GLOBAL_HEADER_COMPONENT_KEY"; // Get from component search
const headerSet = await figma.importComponentSetByKeyAsync(headerKey);
const header = headerSet.defaultVariant || headerSet.children[0];
const headerInstance = header.createInstance();
headerInstance.setProperties({ 
  "Product": "BrightHR", 
  "Size": "Desktop" 
}); // Default props
main.appendChild(headerInstance);
```

### 6. Build Content Sections

All subsequent content sections (Hero, Content Panels, etc.) get appended to the Main frame below the Global Header.

### Complete Structure

```
Page Wrapper (VERTICAL auto-layout)
└── Layout Container (HORIZONTAL auto-layout)
    ├── Side Nav Instance (Sub menu: Off)
    └── Main (VERTICAL auto-layout, FILL horizontal, HUG vertical)
        ├── Global Header Instance (Product: BrightHR, Size: Desktop)
        ├── [Content Section 1]
        ├── [Content Section 2]
        └── [Content Section N...]
```

---

## Component Discovery and Usage

### Component Library Filtering

When discovering components for BrightHR screens:

1. **Filter by library name:** When using `search_design_system`, only consider results where `libraryName` matches:
   - `"Components: Lumen (Bright-UI)"` for UI components
   - `"Components: Icons v2.0"` for icons
   
2. **Reject all other libraries:** If search results include components from BEX, dotcom, Peninsula, Mobile Apps, or any other library, ignore them completely.

3. **Example filtering pattern:**
```js
// After calling search_design_system
const lumenComponents = searchResults.components.filter(c => 
  c.libraryName === "Components: Lumen (Bright-UI)"
);
// ONLY use components from lumenComponents
```

### Using Component Defaults

**CRITICAL:** When consuming instances, unless the users explicitly ask, if available, use the default variant based on the design system defaults to avoid visually incorrect results.

#### Example: Badge Component Defaults
- State: Default
- Background: Default
- Badge also consumes a nested instance called Badge Master with these defaults:
  - Size: Base
  - Uppercase: False
  - Show Marker: False
  - Show Leading Icon: False
  - Show Trailing Icon: False
  - Label: "Hello world!"

#### Example: Button Component Defaults
- Use Primary variant as default (not Neutral) unless explicitly specified otherwise in the source code

**If not possible to match the exact variant, select the closest one and note the discrepancy for later review — don't hardcode values.**

### Required Components for New Files

**CRITICAL:** When creating a file from scratch, always start with the following components as part of the initial layout:

1. **Side Nav:** [Side Nav component](https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?node-id=3335-13371&t=APCPvfB1uOfd6NfH-4)
   - Default props: Sub menu: Off

2. **Global Header/Navigation:** [Global Header](https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?node-id=3324-12887&t=APCPvfB1uOfd6NfH-4)
   - Default props: Product: BrightHR, Size: Desktop

### Avatar Usage

**CRITICAL:** When implementing avatars of any kind, always use the Avatar component from the Lumen library with the appropriate props.

### Font Loading Pattern

```js
// Always load Albert Sans fonts, never Inter or other fonts
await figma.loadFontAsync({family: "Albert Sans", style: "Regular"});
await figma.loadFontAsync({family: "Albert Sans", style: "Medium"});
await figma.loadFontAsync({family: "Albert Sans", style: "SemiBold"});
await figma.loadFontAsync({family: "Albert Sans", style: "Bold"});
```

---

## 1. Component Descriptions (MANDATORY)

All published components, component sets, and instances should have descriptions that explain intent and usage constraints.

### What to document

- **Purpose**: What is this component for?
- **Usage constraints**: When should/shouldn't it be used?
- **Non-obvious behavior**: Any special properties or states

### Where descriptions appear

- Figma's dev mode and component panel
- MCP context when reading component metadata
- AI-generated code suggestions

### Examples

```
Button component:
"Primary action button. Use for the single most important action on a page. Don't use more than one per section."

Input component:
"Text input field with built-in validation states. Use error variant for validation feedback, not for warnings."

Card component:
"Content container with elevation. Can contain any child components. Maintains 16px padding on all breakpoints."
```

### How to set

```js
component.description = "Your description here";
componentSet.description = "Component set description (this is what users see)";
```

**Note:** Variant children also have `description` fields, but the component set description is what's displayed. Set it on the component set, not individual variants.

## 2. Variable Scopes (MANDATORY)

**Never use `ALL_SCOPES` — it pollutes every property picker with irrelevant tokens.**

Always set specific scopes when creating variables. This helps AI tools understand where tokens should be applied and keeps the Figma UI clean.

### Standard scope mappings

| Token type | Scopes |
|------------|--------|
| Background colors | `["FRAME_FILL", "SHAPE_FILL"]` |
| Text colors | `["TEXT_FILL"]` |
| Border colors | `["STROKE_COLOR"]` |
| Spacing tokens | `["GAP", "WIDTH_HEIGHT"]` |
| Border radius | `["CORNER_RADIUS"]` |
| Font sizes | `["FONT_SIZE"]` |
| Font weights | `["FONT_WEIGHT"]` |
| Line heights | `["LINE_HEIGHT"]` |
| Opacity values | `["OPACITY"]` |

### How to set

```js
variable.scopes = ["FRAME_FILL", "SHAPE_FILL"]; // backgrounds
variable.scopes = ["TEXT_FILL"]; // text colors
variable.scopes = ["GAP"]; // spacing
```

See [variable-patterns.md](../variable-patterns.md) for the complete scope reference.

## 3. Code Syntax Annotations (REQUIRED FOR ALL VARIABLES)

Every variable should have platform-specific code syntax annotations showing how the token is referenced in code.

### Why this matters

- AI tools need to know the **actual** code reference, not just the Figma name
- Dev mode shows accurate code snippets instead of guesses
- Prevents incorrect code generation

### Platform-specific formats

```js
// For web (CSS custom properties)
variable.setCodeSyntax("WEB", "var(--color-bg-primary)");
variable.setCodeSyntax("WEB", "var(--spacing-lg)");

// For React Native / iOS
variable.setCodeSyntax("IOS", "UIColor.primaryBackground");

// For Android
variable.setCodeSyntax("ANDROID", "R.color.bg_primary");
```

### How to set

```js
// Always set WEB syntax for BrightHR projects
variable.setCodeSyntax("WEB", `var(--${variable.name.replace(/\//g, "-")})`);
```

## 4. Naming Conventions

### Variables

Use **lowercase** slash-delimited paths that map to the token hierarchy:

```
color/bg/primary
color/bg/secondary
color/text/primary
color/text/muted
color/border/default
spacing/xs
spacing/sm
spacing/md
radius/sm
radius/md
```

**Never use spaces** inside path segments: `color/bg primary` is wrong; `color/bg/primary` is correct.

### Components

- **Public components**: Plain PascalCase — `Button`, `Input`, `Card`
- **Private sub-components**: Underscore prefix + slash namespace — `_Button/Icon`, `_Input/Indicator`
- **Documentation components**: Dot prefix — `.ExampleCard`, `.GuidelineHeader`

### Modes

Mode names can use spaces and mixed case (these are labels, not code identifiers):

- `Light` / `Dark` (preferred)
- `BrightHR Light` / `BrightHR Dark` (acceptable for branded themes)

## 5. Semantic Variable Structure (REQUIRED)

Follow the two-tier token architecture:

### Tier 1: Primitives Collection

- Holds raw color values
- Not exposed to consumers (`scopes: []`)
- Flat `{family}/{step}` format:

```
blue/100
blue/200
...
blue/900
gray/50
gray/100
...
neutral/900
```

### Tier 2: Semantic Collection

- Aliases primitives (never holds raw values)
- Role-based naming
- Supports light/dark modes via mode values

```
color/bg/primary      → blue/500 (light) / blue/200 (dark)
color/bg/secondary    → gray/100 (light) / gray/800 (dark)
color/text/primary    → gray/900 (light) / neutral/50 (dark)
```

**Rule:** If you need a new color value, create the primitive first, then create the semantic alias.

## 6. Text Styles & Effect Styles

### Text Styles

All text styles should have:
- Descriptive names using slash hierarchy: `Heading/XL`, `Body/Default`, `Caption/Small`
- Description explaining usage: `"Page title. 48px, only one per page."`
- Bound variables for `fontSize`, `lineHeight`, `letterSpacing` where applicable

### Effect Styles

All shadow/elevation styles should have:
- Role-based names: `Elevation/100`, `Elevation/200`, `Shadow/Focus`
- Description explaining usage: `"Elevated card state. Use for floating panels."`
- Bound variables for `color`, `radius`, `spread` where applicable

## 7. Code Connect Mappings

Map all published components to their code implementations:

### Minimum requirement (component_browser tier)

```js
// Associates component with code path
{
  source: "src/components/Button.tsx",
  componentName: "Button",
  label: "React"
}
```

### Full mapping (figmadoc tier)

Include templates for precise prop-level mapping when:
- Component has complex prop variations
- Dev mode needs to show accurate prop usage
- Code Connect is actively used by the dev team

## 8. Quick Validation Checklist

Before completing any design system work, verify:

- [ ] All components have descriptions
- [ ] All variables have explicit scopes (not `ALL_SCOPES`)
- [ ] All variables have WEB code syntax annotations
- [ ] Variable naming follows lowercase slash-delimited convention
- [ ] Semantic variables alias primitives (no raw hex values)
- [ ] Text styles and effect styles have descriptions
- [ ] Components mapped to code via Code Connect

## 9. Inspection Scripts

Use these to audit existing files for missing metadata:

### Find components without descriptions

```js
const results = [];
for (const page of figma.root.children) {
  await figma.setCurrentPageAsync(page);
  page.findAll(n => {
    if ((n.type === 'COMPONENT' || n.type === 'COMPONENT_SET') && !n.description) {
      results.push(`[${page.name}] ${n.name} — MISSING DESCRIPTION`);
    }
    return false;
  });
}
return results.join('\n');
```

### Find variables with ALL_SCOPES

```js
const collections = await figma.variables.getLocalVariableCollectionsAsync();
const results = [];
for (const coll of collections) {
  for (const varId of coll.variableIds) {
    const v = await figma.variables.getVariableByIdAsync(varId);
    if (v.scopes.includes('ALL_SCOPES')) {
      results.push(`${coll.name}/${v.name} — using ALL_SCOPES`);
    }
  }
}
return results.join('\n');
```

### Find variables without code syntax

```js
const collections = await figma.variables.getLocalVariableCollectionsAsync();
const results = [];
for (const coll of collections) {
  for (const varId of coll.variableIds) {
    const v = await figma.variables.getVariableByIdAsync(varId);
    const webSyntax = v.getCodeSyntax('WEB');
    if (!webSyntax || !webSyntax.trim()) {
      results.push(`${coll.name}/${v.name} — MISSING WEB CODE SYNTAX`);
    }
  }
}
return results.join('\n');
```

---

These conventions ensure BrightHR design system files are AI-ready and provide the metadata needed for accurate code generation and design-to-code workflows.
