---
name: figma-generate-design
description: "Use this skill alongside figma-use when the task involves translating an application page, view, or multi-section layout into Figma. Triggers: 'write to Figma', 'create in Figma from code', 'push page to Figma', 'take this app/page and build it in Figma', 'create a screen', 'build a landing page in Figma', 'update the Figma screen to match code'. This is the preferred workflow skill whenever the user wants to build or update a full page, screen, or view in Figma from code or a description. Discovers design system components, variables, and styles via search_design_system, imports them, and assembles screens incrementally section-by-section using design system tokens instead of hardcoded values."
disable-model-invocation: false
---

# Build / Update Screens from Design System

Use this skill to create or update full-page screens in Figma by **reusing the published design system** — components, variables, and styles — rather than drawing primitives with hardcoded values. The key insight: the Figma file likely has a published design system with components, color/spacing variables, and text/effect styles that correspond to the codebase's UI components and tokens. Find and use those instead of drawing boxes with hex colors.

**MANDATORY**: You MUST also load [figma-use](../figma-use/SKILL.md) before any `use_figma` call. That skill contains critical rules (color ranges, font loading, etc.) that apply to every script you write.

**Always pass `skillNames: "figma-generate-design"` when calling `use_figma` as part of this skill.** This is a logging parameter — it does not affect execution.

## Skill Boundaries

- Use this skill when the deliverable is a **Figma screen** (new or updated) composed of design system component instances.
- If the user wants to generate **code from a Figma design**, switch to [figma-implement-design](../figma-implement-design/SKILL.md).
- If the user wants to create **new reusable components or variants**, use [figma-use](../figma-use/SKILL.md) directly.
- If the user wants to write **Code Connect mappings**, switch to [figma-code-connect-components](../figma-code-connect-components/SKILL.md).

## Prerequisites

- Figma MCP server must be connected
- The target Figma file must have a published design system with components (or access to a team library)
- User should provide either:
  - A Figma file URL / file key to work in
  - Or context about which file to target (the agent can discover pages)
- Source code or description of the screen to build/update

## BrightHR Design System Sources (MANDATORY)

**When building BrightHR screens, ONLY use components, variables, and styles from these specific files:**

### 1. Main Component Library
**File:** [Components: Lumen (Bright-UI)](https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?m=auto&node-id=2489-6991&t=APCPvfB1uOfd6NfH-1)
- **File Key:** `3dNASwqNXrYbGQyHsf1Sgg`
- **Usage:** Use ONLY components from this file for all UI primitives (buttons, badges, inputs, cards, navigation, etc.)
- **CRITICAL:** Do NOT use components from other published design systems (BEX, dotcom, Peninsula, Mobile Apps, etc.) even if they appear in search results
- **CRITICAL:** When consuming instances, unless the users explicitly ask, if available, use the default variant based on the design system defaults to avoid visually incorrect results (e.g. the Badge component defaults are State: Default, Background: Default, the badge also consumes a nested instance called Badge Master, the defaults of which are Size: Base, Uppercase: False, Show Marker: False, Show Leading Icon: False, Show Trailing Icon: False, Label: "Hello world!" Primary vs Neutral button). If not possible to match the exact variant, select the closest one and note the discrepancy for later review, don't just hardcode values.
- **CRITICAL:** When implementing avatars of any kind always use the Avatar component with these props.
- **CRITICAL:** When creating a file from scratch, always start with the following components as part of the initial layout, Side Nav (https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?node-id=3335-13371&t=APCPvfB1uOfd6NfH-4) with props Sub menu: Off and the Global Header/Navigation (https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?node-id=3324-12887&t=APCPvfB1uOfd6NfH-4) with props Product: BrightHR, Size: Desktop.

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

### Component Discovery Strategy for BrightHR

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

4. **Font loading pattern:**
```js
// Always load Albert Sans fonts, never Inter or other fonts
await figma.loadFontAsync({family: "Albert Sans", style: "Regular"});
await figma.loadFontAsync({family: "Albert Sans", style: "Medium"});
await figma.loadFontAsync({family: "Albert Sans", style: "SemiBold"});
await figma.loadFontAsync({family: "Albert Sans", style: "Bold"});
```

## Parallel Workflow with generate_figma_design (Web Apps Only)

When building a screen from a **web app** that can be rendered in a browser, the best results come from running both approaches in parallel:

1. **In parallel:**
   - Start building the screen using this skill's workflow (use_figma + design system components)
   - Run `generate_figma_design` to capture a pixel-perfect screenshot of the running web app
2. **Once both complete:** Update the use_figma output to match the pixel-perfect layout from the `generate_figma_design` capture. The capture provides the exact spacing, sizing, and visual treatment to aim for, while your use_figma output has proper component instances linked to the design system.
3. **Once confirmed looking good:** Delete the `generate_figma_design` output — it was only used as a visual reference.

This combines the best of both: `generate_figma_design` gives pixel-perfect layout accuracy, while use_figma gives proper design system component instances that stay linked and updatable.

**This workflow only applies to web apps** where `generate_figma_design` can capture the running page. For non-web apps (iOS, Android, etc.) or when updating existing screens, use the standard workflow below.

## Creating Responsive Versions from Desktop Frames

When the user requests **tablet and mobile versions** of an existing desktop frame, follow this specialized workflow instead of building from scratch. This ensures proper adaptation of interactive components and layout for smaller viewports.

### When to Use This Workflow

Trigger this workflow when the user:
- Explicitly asks for "tablet" or "mobile" versions
- Mentions creating responsive versions from an existing desktop design
- References specific desktop frames to adapt for smaller screens
- Says "make this responsive" or "create mobile/tablet layouts"

### Standard Viewport Sizes

- **Desktop:** 1440px (or existing frame width if larger than 1024px)
- **Tablet:** 768px width
- **Mobile:** 375px width

### Standard Padding

- **Main frame:** NO padding on the main frame itself
- **Inner containers:** 20px padding on left and right sides for all inner content containers
- **Tablet content width:** 728px (768 - 40px total padding)
- **Mobile content width:** 335px (375 - 40px total padding)

### Required Steps

#### 1. Clone and Resize the Desktop Frame

Start by cloning the desktop frame and positioning the responsive versions adjacent to avoid overlap. **Enable auto-layout on the cloned frames** to ensure content flows properly:

```js
// Load required fonts
await figma.loadFontAsync({ family: "Albert Sans", style: "Regular" });
await figma.loadFontAsync({ family: "Albert Sans", style: "SemiBold" });
await figma.loadFontAsync({ family: "Albert Sans", style: "Bold" });
await figma.loadFontAsync({ family: "Albert Sans", style: "Medium" });

// Get the original desktop frame
const originalFrame = await figma.getNodeByIdAsync("DESKTOP_FRAME_ID");
if (!originalFrame || originalFrame.type !== "FRAME") {
  throw new Error("Original frame not found");
}

// Clone for tablet
const tabletFrame = originalFrame.clone();
tabletFrame.name = originalFrame.name + " - Tablet";
tabletFrame.x = originalFrame.x + originalFrame.width + 200;
tabletFrame.y = originalFrame.y;
tabletFrame.resize(768, tabletFrame.height);

// Enable auto-layout if not already set (NO padding on main frame)
if (tabletFrame.layoutMode === "NONE") {
  tabletFrame.layoutMode = "VERTICAL";
  tabletFrame.primaryAxisSizingMode = "AUTO";
  tabletFrame.counterAxisSizingMode = "FIXED";
}

// Clone for mobile
const mobileFrame = originalFrame.clone();
mobileFrame.name = originalFrame.name + " - Mobile";
mobileFrame.x = tabletFrame.x;
mobileFrame.y = tabletFrame.y + tabletFrame.height + 200;
mobileFrame.resize(375, mobileFrame.height);

// Enable auto-layout if not already set (NO padding on main frame)
if (mobileFrame.layoutMode === "NONE") {
  mobileFrame.layoutMode = "VERTICAL";
  mobileFrame.primaryAxisSizingMode = "AUTO";
  mobileFrame.counterAxisSizingMode = "FIXED";
}

return { 
  success: true, 
  tabletFrameId: tabletFrame.id,
  mobileFrameId: mobileFrame.id
};
```

#### 2. Swap Global Product Header Component Size

The Global product header component (https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?node-id=3324-12887) has Size variants: Desktop, Tablet, Mobile. **Always swap the Size property** to match the viewport:

```js
// For tablet
const tabletFrame = await figma.getNodeByIdAsync("TABLET_FRAME_ID");
const tabletHeader = tabletFrame.findOne(n => 
  n.type === "INSTANCE" && n.name === "Global product headers"
);
if (tabletHeader && tabletHeader.type === "INSTANCE") {
  // Swap to Tablet size variant
  tabletHeader.setProperties({ "Size": "Tablet" });
  tabletHeader.resize(768, tabletHeader.height);
  tabletHeader.x = 0;
}

// For mobile
const mobileFrame = await figma.getNodeByIdAsync("MOBILE_FRAME_ID");
const mobileHeader = mobileFrame.findOne(n => 
  n.type === "INSTANCE" && n.name === "Global product headers"
);
if (mobileHeader && mobileHeader.type === "INSTANCE") {
  // Swap to Mobile size variant
  mobileHeader.setProperties({ "Size": "Mobile" });
  mobileHeader.resize(375, mobileHeader.height);
  mobileHeader.x = 0;
}
```

#### 3. Hide Side Navigation on Tablet and Mobile

Side navigation should be hidden on smaller viewports (typically replaced with a hamburger menu in the header):

```js
const sideNav = frame.findOne(n => n.name === "BrightSafe Navigation" || n.name === "Side Nav");
if (sideNav) {
  sideNav.visible = false;
}
```

#### 4. Make Buttons and Select Menus Full Width

**Critical for mobile UX:** Buttons and select menus should stretch to fill the available width using auto-layout sizing. This improves touch targets and follows BrightHR mobile patterns.

```js
// Helper function to ensure inner container has auto-layout with padding
function ensureAutoLayout(frame) {
  if (frame.layoutMode === "NONE") {
    frame.layoutMode = "VERTICAL";
    frame.primaryAxisSizingMode = "AUTO";
    frame.counterAxisSizingMode = "FIXED";
    frame.paddingLeft = 20; // Padding on inner containers only
    frame.paddingRight = 20;
    frame.itemSpacing = 16;
  }
}

// For tablet - use FILL to stretch to parent width
const tabletButtons = tabletFrame.findAll(n => 
  n.type === "INSTANCE" && n.name === "Button"
);
tabletButtons.forEach(btn => {
  // Ensure parent has auto-layout
  if (btn.parent && btn.parent.type === "FRAME") {
    ensureAutoLayout(btn.parent);
  }
  // Set button to fill width
  btn.layoutSizingHorizontal = "FILL";
});

const tabletSelects = tabletFrame.findAll(n => 
  n.type === "INSTANCE" && n.name.includes("Select Field")
);
tabletSelects.forEach(select => {
  if (select.parent && select.parent.type === "FRAME") {
    ensureAutoLayout(select.parent);
  }
  select.layoutSizingHorizontal = "FILL";
});

// For mobile - same pattern
const mobileButtons = mobileFrame.findAll(n => 
  n.type === "INSTANCE" && n.name === "Button"
);
mobileButtons.forEach(btn => {
  if (btn.parent && btn.parent.type === "FRAME") {
    ensureAutoLayout(btn.parent);
  }
  btn.layoutSizingHorizontal = "FILL";
});

const mobileSelects = mobileFrame.findAll(n => 
  n.type === "INSTANCE" && n.name.includes("Select Field")
);
mobileSelects.forEach(select => {
  if (select.parent && select.parent.type === "FRAME") {
    ensureAutoLayout(select.parent);
  }
  select.layoutSizingHorizontal = "FILL";
});
```

#### 5. Adjust Content Containers and Stack Vertically

**Use auto-layout to make content containers flush to frame edges with proper padding.** This ensures consistent spacing and responsive behavior:

```js
// Standard padding (same for both tablet and mobile)
const FRAME_PADDING = 20;

// Helper to convert container to auto-layout and make it flush
function makeContainerFlush(section, frameWidth) {
  // Enable auto-layout for vertical stacking
  if (section.layoutMode === "NONE") {
    section.layoutMode = "VERTICAL";
    section.primaryAxisSizingMode = "AUTO";
    section.itemSpacing = 16;
  }
  
  // Make container flush to frame edges
  section.layoutSizingHorizontal = "FILL";
  section.x = 0;
  
  // Add padding inside the container
  section.paddingLeft = FRAME_PADDING;
  section.paddingRight = FRAME_PADDING;
  section.paddingTop = 16;
  section.paddingBottom = 16;
}

// Tablet adjustments
const tabletContentSections = tabletFrame.findAll(n => 
  n.type === "FRAME" && (n.name.includes("Frame") || n.name.includes("Group"))
);
tabletContentSections.forEach(section => {
  makeContainerFlush(section, 768);
});

// Mobile adjustments
const mobileContentSections = mobileFrame.findAll(n => 
  n.type === "FRAME" && (n.name.includes("Frame") || n.name.includes("Group"))
);
mobileContentSections.forEach(section => {
  makeContainerFlush(section, 375);
});
```

#### 6. Stack Side-by-Side Content Vertically

**CRITICAL:** Any content that appears side-by-side on desktop MUST stack vertically on tablet and mobile. This includes:
- **Multiple cards** (e.g., 2 or 3 cards in a row → stack vertically)
- **Image + text layouts** (image left, text right → stack as image above, text below)
- **Multi-column grids** (e.g., 3-column grid → single column)

Desktop designs often have side-by-side image cards or multi-column grids. On tablet and mobile, these should stack vertically using auto-layout:

```js
// Find the parent container of image groups or side-by-side content
const imageContainer = mobileFrame.findOne(n => 
  n.type === "FRAME" && n.children.some(child => /Group \d+/.test(child.name))
);

if (imageContainer) {
  // Convert container to auto-layout for vertical stacking
  imageContainer.layoutMode = "VERTICAL";
  imageContainer.primaryAxisSizingMode = "AUTO";
  imageContainer.counterAxisSizingMode = "FIXED";
  imageContainer.itemSpacing = 24; // Gap between stacked items
  imageContainer.paddingLeft = 20; // Apply padding to inner container
  imageContainer.paddingRight = 20;
  imageContainer.layoutSizingHorizontal = "FILL";
  
  // Make each image group fill the container width
  const imageGroups = imageContainer.findAll(n => 
    n.type === "FRAME" && /Group \d+/.test(n.name)
  );
  
  imageGroups.forEach(group => {
    group.layoutSizingHorizontal = "FILL";
    // Ensure groups use auto-layout for proper stacking (image above, text below)
    if (group.children.length > 0 && group.layoutMode === "NONE") {
      group.layoutMode = "VERTICAL"; // Stack children vertically
      group.primaryAxisSizingMode = "AUTO";
      group.itemSpacing = 16;
    }
  });
}

// Handle image + text layouts (convert horizontal to vertical stacking)
const horizontalLayouts = mobileFrame.findAll(n => 
  n.type === "FRAME" && n.layoutMode === "HORIZONTAL" && 
  n.children.some(child => child.type === "RECTANGLE" || child.name.toLowerCase().includes("image"))
);

horizontalLayouts.forEach(layout => {
  // Convert to vertical stacking (image above, text below)
  layout.layoutMode = "VERTICAL";
  layout.primaryAxisSizingMode = "AUTO";
  layout.itemSpacing = 16;
  layout.paddingLeft = 20; // Apply padding to inner container
  layout.paddingRight = 20;
});
```

#### 7. Validate Each Responsive Version

After creating each responsive version, validate with screenshots:

```js
// Take screenshots to verify layout
const tabletScreenshot = await get_screenshot(fileKey, tabletFrameId);
const mobileScreenshot = await get_screenshot(fileKey, mobileFrameId);
```

**Check for common issues:**
- Buttons and select menus are full width (using `layoutSizingHorizontal = "FILL"`)
- Global header component shows correct Size variant (Tablet/Mobile, not Desktop)
- Content stacks vertically without overlapping (using auto-layout)
- Side-by-side content from desktop (cards, columns) now stacks vertically
- Image + text layouts stack vertically (image above, text below)
- Text is not clipped or cut off
- Side navigation is hidden
- Padding is on inner containers (NOT main frame): 20px left and right
- Inner containers are auto-layout frames
- Frames are flush to parent edges (x = 0, using FILL sizing)
- Touch targets are adequately sized (min 44px height for interactive elements)
- Auto-layout is enabled on all container frames

### Complete Example: Creating Tablet and Mobile from Desktop

```js
// Load fonts
await figma.loadFontAsync({ family: "Albert Sans", style: "Regular" });
await figma.loadFontAsync({ family: "Albert Sans", style: "Medium" });
await figma.loadFontAsync({ family: "Albert Sans", style: "SemiBold" });
await figma.loadFontAsync({ family: "Albert Sans", style: "Bold" });

const originalFrame = await figma.getNodeByIdAsync("1:3947");
const createdFrames = [];

// Create tablet version
const tabletFrame = originalFrame.clone();
tabletFrame.name = originalFrame.name + " - Tablet";
tabletFrame.x = originalFrame.x + originalFrame.width + 200;
tabletFrame.resize(768, tabletFrame.height);

// Enable auto-layout (NO padding on main frame)
if (tabletFrame.layoutMode === "NONE") {
  tabletFrame.layoutMode = "VERTICAL";
  tabletFrame.primaryAxisSizingMode = "AUTO";
  tabletFrame.counterAxisSizingMode = "FIXED";
}

// Hide side nav
const tabletSideNav = tabletFrame.findOne(n => n.name === "BrightSafe Navigation");
if (tabletSideNav) tabletSideNav.visible = false;

// Swap header size
const tabletHeader = tabletFrame.findOne(n => n.name === "Global product headers");
if (tabletHeader && tabletHeader.type === "INSTANCE") {
  tabletHeader.setProperties({ "Size": "Tablet" });
  tabletHeader.resize(768, tabletHeader.height);
  tabletHeader.layoutSizingHorizontal = "FILL";
}

// Make buttons full width (ensure parent containers have auto-layout)
tabletFrame.findAll(n => n.type === "INSTANCE" && n.name === "Button").forEach(btn => {
  if (btn.parent && btn.parent.type === "FRAME" && btn.parent.layoutMode === "NONE") {
    btn.parent.layoutMode = "VERTICAL";
    btn.parent.primaryAxisSizingMode = "AUTO";
  }
  btn.layoutSizingHorizontal = "FILL";
});

// Make select fields full width
tabletFrame.findAll(n => n.type === "INSTANCE" && n.name.includes("Select Field")).forEach(select => {
  if (select.parent && select.parent.type === "FRAME" && select.parent.layoutMode === "NONE") {
    select.parent.layoutMode = "VERTICAL";
    select.parent.primaryAxisSizingMode = "AUTO";
  }
  select.layoutSizingHorizontal = "FILL";
});

// Make all content sections flush to edges with FILL
tabletFrame.findAll(n => n.type === "FRAME").forEach(frame => {
  if (frame !== tabletFrame) {
    frame.layoutSizingHorizontal = "FILL";
    frame.x = 0;
  }
});

createdFrames.push(tabletFrame.id);

// Create mobile version (similar pattern)
const mobileFrame = originalFrame.clone();
mobileFrame.name = originalFrame.name + " - Mobile";
mobileFrame.x = tabletFrame.x;
mobileFrame.y = tabletFrame.y + tabletFrame.height + 200;
mobileFrame.resize(375, mobileFrame.height);

// Enable auto-layout (NO padding on main frame)
if (mobileFrame.layoutMode === "NONE") {
  mobileFrame.layoutMode = "VERTICAL";
  mobileFrame.primaryAxisSizingMode = "AUTO";
  mobileFrame.counterAxisSizingMode = "FIXED";
}

const mobileSideNav = mobileFrame.findOne(n => n.name === "BrightSafe Navigation");
if (mobileSideNav) mobileSideNav.visible = false;

const mobileHeader = mobileFrame.findOne(n => n.name === "Global product headers");
if (mobileHeader && mobileHeader.type === "INSTANCE") {
  mobileHeader.setProperties({ "Size": "Mobile" });
  mobileHeader.resize(375, mobileHeader.height);
  mobileHeader.layoutSizingHorizontal = "FILL";
}

mobileFrame.findAll(n => n.type === "INSTANCE" && n.name === "Button").forEach(btn => {
  if (btn.parent && btn.parent.type === "FRAME" && btn.parent.layoutMode === "NONE") {
    btn.parent.layoutMode = "VERTICAL";
    btn.parent.primaryAxisSizingMode = "AUTO";
  }
  btn.layoutSizingHorizontal = "FILL";
});

mobileFrame.findAll(n => n.type === "INSTANCE" && n.name.includes("Select Field")).forEach(select => {
  if (select.parent && select.parent.type === "FRAME" && select.parent.layoutMode === "NONE") {
    select.parent.layoutMode = "VERTICAL";
    select.parent.primaryAxisSizingMode = "AUTO";
  }
  select.layoutSizingHorizontal = "FILL";
});

// Make all content sections flush to edges with FILL
mobileFrame.findAll(n => n.type === "FRAME").forEach(frame => {
  if (frame !== mobileFrame) {
    frame.layoutSizingHorizontal = "FILL";
    frame.x = 0;
  }
});

createdFrames.push(mobileFrame.id);

return { 
  success: true, 
  createdFrames,
  tabletFrameId: tabletFrame.id,
  mobileFrameId: mobileFrame.id
};
```

### Best Practices for Responsive Adaptations

- **Work incrementally:** Create tablet first, validate, then create mobile
- **Clone, don't rebuild:** Always clone the desktop frame to preserve all content and structure
- **Padding on inner containers only:** NO padding on main frame. Apply 20px left/right padding to inner content containers
- **Inner containers must be auto-layout:** Convert all inner content containers to auto-layout frames with proper padding
- **Stack side-by-side content:** Any content side-by-side on desktop (cards, columns) must stack vertically on tablet/mobile
- **Stack image + text layouts:** Image left + text right on desktop becomes image above + text below on tablet/mobile
- **Maximize auto-layout usage:** Convert all container frames to use auto-layout (`layoutMode = "VERTICAL"` for stacking)
- **Use FILL for width:** Set `layoutSizingHorizontal = "FILL"` for all content containers, buttons, and inputs
- **Zero out X positions:** Set `x = 0` for child frames inside auto-layout containers to ensure they're flush to edges
- **Font sizes stay the same:** Don't reduce text size on mobile unless explicitly requested
- **Validate visually:** Screenshot each viewport to catch clipping, overlap, and sizing issues
- **Avoid manual positioning:** Let auto-layout handle positioning and spacing instead of setting x/y coordinates manually

## Required Workflow

**Follow these steps in order. Do not skip steps.**

### Step 1: Understand the Screen

Before touching Figma, understand what you're building:

1. If building from code, read the relevant source files to understand the page structure, sections, and which components are used.
2. Identify the major sections of the screen (e.g., Header, Hero, Content Panels, Pricing Grid, FAQ Accordion, Footer).
3. For each section, list the UI components involved (buttons, inputs, cards, navigation pills, accordions, etc.).

### Step 2: Discover Design System — Components, Variables, and Styles

You need three things from the design system: **components** (buttons, cards, etc.), **variables** (colors, spacing, radii), and **styles** (text styles, effect styles like shadows). Don't hardcode hex colors or pixel values when design system tokens exist.

#### 2a: Discover components

**Preferred: inspect existing screens first.** If the target file already contains screens using the same design system, skip `search_design_system` and inspect existing instances directly. A single `use_figma` call that walks an existing frame's instances gives you an exact, authoritative component map:

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const uniqueSets = new Map();
frame.findAll(n => n.type === "INSTANCE").forEach(inst => {
  const mc = inst.mainComponent;
  const cs = mc?.parent?.type === "COMPONENT_SET" ? mc.parent : null;
  const key = cs ? cs.key : mc?.key;
  const name = cs ? cs.name : mc?.name;
  if (key && !uniqueSets.has(key)) {
    uniqueSets.set(key, { name, key, isSet: !!cs, sampleVariant: mc.name });
  }
});
return [...uniqueSets.values()];
```

Only fall back to `search_design_system` when the file has no existing screens to reference. When using it, **search broadly** — try multiple terms and synonyms (e.g., "button", "input", "nav", "card", "accordion", "header", "footer", "tag", "avatar", "toggle", "icon", etc.). Use `includeComponents: true` to focus on components.

**For BrightHR:** When searching, ONLY use results where `libraryName === "Components: Lumen (Bright-UI)"` for UI components or `libraryName === "Components: Icons v2.0"` for icons. Filter out all other libraries (BEX, dotcom, Peninsula, Mobile Apps, etc.).

**Include component properties** in your map — you need to know which TEXT properties each component exposes for text overrides. Create a temporary instance, read its `componentProperties` (and those of nested instances), then remove the temp instance.

Example component map with property info:

```
Component Map:
- Button → key: "abc123", type: COMPONENT_SET
  Properties: { "Label#2:0": TEXT, "Has Icon#4:64": BOOLEAN }
- PricingCard → key: "ghi789", type: COMPONENT_SET
  Properties: { "Device": VARIANT, "Variant": VARIANT }
  Nested "Text Heading" has: { "Text#2104:5": TEXT }
  Nested "Button" has: { "Label#2:0": TEXT }
```

#### 2b: Discover variables (colors, spacing, radii)

**Inspect existing screens first** (same as components). Or use `search_design_system` with `includeVariables: true`.

> **WARNING: Two different variable discovery methods — do not confuse them.**
>
> - `use_figma` with `figma.variables.getLocalVariableCollectionsAsync()` — returns **only local variables defined in the current file**. If this returns empty, it does **not** mean no variables exist. Remote/published library variables are invisible to this API.
> - `search_design_system` with `includeVariables: true` — searches across **all linked libraries**, including remote and published ones. This is the correct tool for discovering design system variables.
>
> **Never conclude "no variables exist" based solely on `getLocalVariableCollectionsAsync()` returning empty.** Always also run `search_design_system` with `includeVariables: true` to check for library variables before deciding to create your own.

**Query strategy:** `search_design_system` matches against **variable names** (e.g., "Neutral/Neutral-900", "core/gray/100", "space/400"), not categories. Run multiple short, simple queries in parallel rather than one compound query:

- **Primitive colors:** "neutral", "danger", "accent", "warning", "success", "white", "brand"
- **Semantic colors:** "background", "foreground", "border", "surface", "text"
- **Spacing/sizing:** "space", "radius", "gap", "padding"

**For BrightHR:** Search for variables in the BrightHR Colours & Styles file (fileKey: `Tutg1Xnl9e07uED4DZjyUB`) and Web Typography file (fileKey: `OudUc4pq10fGO5oHkA674i`). When searching for typography variables, look for patterns used in Albert Sans definitions.

If initial searches return empty, try shorter fragments or different naming conventions — libraries vary widely ("grey" vs "gray", "spacing" vs "space", "color/bg" vs "background").

Inspect an existing screen's bound variables for the most authoritative results:

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const varMap = new Map();
frame.findAll(() => true).forEach(node => {
  const bv = node.boundVariables;
  if (!bv) return;
  for (const [prop, binding] of Object.entries(bv)) {
    const bindings = Array.isArray(binding) ? binding : [binding];
    for (const b of bindings) {
      if (b?.id && !varMap.has(b.id)) {
        const v = await figma.variables.getVariableByIdAsync(b.id);
        if (v) varMap.set(b.id, { name: v.name, id: v.id, key: v.key, type: v.resolvedType, remote: v.remote });
      }
    }
  }
});
return [...varMap.values()];
```

For library variables (remote = true), import them by key with `figma.variables.importVariableByKeyAsync(key)`. For local variables, use `figma.variables.getVariableByIdAsync(id)` directly.

See [variable-patterns.md](../figma-use/references/variable-patterns.md) for binding patterns.

#### 2c: Discover styles (text styles, effect styles)

Search for styles using `search_design_system` with `includeStyles: true` and terms like "heading", "body", "shadow", "elevation". Or inspect what an existing screen uses:

```js
const frame = figma.currentPage.findOne(n => n.name === "Existing Screen");
const styles = { text: new Map(), effect: new Map() };
frame.findAll(() => true).forEach(node => {
  if ('textStyleId' in node && node.textStyleId) {
    const s = figma.getStyleById(node.textStyleId);
    if (s) styles.text.set(s.id, { name: s.name, id: s.id, key: s.key });
  }
  if ('effectStyleId' in node && node.effectStyleId) {
    const s = figma.getStyleById(node.effectStyleId);
    if (s) styles.effect.set(s.id, { name: s.name, id: s.id, key: s.key });
  }
});
return {
  textStyles: [...styles.text.values()],
  effectStyles: [...styles.effect.values()]
};
```

Import library styles with `figma.importStyleByKeyAsync(key)`, then apply with `node.textStyleId = style.id` or `node.effectStyleId = style.id`.

See [text-style-patterns.md](../figma-use/references/text-style-patterns.md) and [effect-style-patterns.md](../figma-use/references/effect-style-patterns.md) for details.

### Step 3: Create the Page Wrapper Frame First

**Do NOT build sections as top-level page children and reparent them later** — moving nodes across `use_figma` calls with `appendChild()` silently fails and produces orphaned frames. Instead, create the wrapper first, then build each section directly inside it.

Create the page wrapper in its own `use_figma` call. Position it away from existing content and return its ID:

```js
// Find clear space
let maxX = 0;
for (const child of figma.currentPage.children) {
  maxX = Math.max(maxX, child.x + child.width);
}

const wrapper = figma.createFrame();
wrapper.name = "Homepage";
wrapper.layoutMode = "VERTICAL";
wrapper.primaryAxisAlignItems = "CENTER";
wrapper.counterAxisAlignItems = "CENTER";
wrapper.resize(1440, 768);
wrapper.layoutSizingHorizontal = "FIXED";
wrapper.layoutSizingVertical = "HUG";
wrapper.x = maxX + 200;
wrapper.y = 0;

return { success: true, wrapperId: wrapper.id };
```
- **CRITICAL:**
Inside this new wrapper create a Layout frame called 'Layout Container' with Autolayout set to Horizontal, and always start with the Side Nav component as part of the initial layout (https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?node-id=3335-13371&t=APCPvfB1uOfd6NfH-4) with props Sub menu: Off. The Side Nav sits flush against the left edge of the Layout Container.

Then create a "Main" holding frame inside the Layout Container to hold the main content sections, positioned adjacent to the right of the Side Nav. This Main frame establishes the layout boundaries for the rest of the screen build, ensuring consistent spacing and alignment conventions. Inside this Main frame is where you'll build each section (Header, Hero, Content Panels, etc.) in subsequent `use_figma` calls. The main frame should have Horizontal Sizing set to FILL to stretch to the available space next to the Side Nav, and Vertical Sizing set to HUG so it grows with the content sections you'll add inside it.

```js
const main = figma.createFrame();
main.name = "Main";
main.layoutMode = "VERTICAL";
main.primaryAxisAlignItems = "CENTER";
main.counterAxisAlignItems = "CENTER";
main.resize(1440, 100);
main.layoutSizingHorizontal = "FILL";
main.layoutSizingVertical = "HUG";
main.x = maxX + 200;
main.y = 0;

return { success: true, mainId: main.id };
```

Now Import the Global Header/Navigation (https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg/Components--Lumen--Bright-UI-?node-id=3324-12887&t=APCPvfB1uOfd6NfH-4) with props Product: BrightHR, Size: Desktop. This is the first component and sits flush to the top edge of the Main frame and ensure it pushes down the rest of the content that you'll build next.

### Step 4: Build Each Section Inside the Main frame

**This is the most important step.** Build one section at a time, each in its own `use_figma` call. At the start of each script, fetch the main frame by ID and append new content directly to it.

```js
const createdNodeIds = [];
const main = await figma.getNodeByIdAsync("MAIN_ID_FROM_STEP_3");

// Import design system components by key
const buttonSet = await figma.importComponentSetByKeyAsync("BUTTON_SET_KEY");
const primaryButton = buttonSet.children.find(c =>
  c.type === "COMPONENT" && c.name.includes("variant=primary")
) || buttonSet.defaultVariant;

// Import design system variables for colors and spacing
const bgColorVar = await figma.variables.importVariableByKeyAsync("BG_COLOR_VAR_KEY");
const spacingVar = await figma.variables.importVariableByKeyAsync("SPACING_VAR_KEY");

// Build section frame with variable bindings (not hardcoded values)
const section = figma.createFrame();
section.name = "Header";
section.layoutMode = "HORIZONTAL";
section.setBoundVariable("paddingLeft", spacingVar);
section.setBoundVariable("paddingRight", spacingVar);
const bgPaint = figma.variables.setBoundVariableForPaint(
  { type: 'SOLID', color: { r: 0, g: 0, b: 0 } }, 'color', bgColorVar
);
section.fills = [bgPaint];

// Import and apply text/effect styles
const shadowStyle = await figma.importStyleByKeyAsync("SHADOW_STYLE_KEY");
section.effectStyleId = shadowStyle.id;

// Create component instances inside the section
const btnInstance = primaryButton.createInstance();
section.appendChild(btnInstance);
createdNodeIds.push(btnInstance.id);

// Append section to main
main.appendChild(section);
section.layoutSizingHorizontal = "FILL"; // AFTER appending

createdNodeIds.push(section.id);
return { success: true, createdNodeIds };
```

After each section, validate with `get_screenshot` before moving on. Look closely for cropped/clipped text (line heights cutting off content) and overlapping elements — these are the most common issues and easy to miss at a glance.

#### Override instance text with setProperties()

Component instances ship with placeholder text ("Title", "Heading", "Button"). Use the component property keys you discovered in Step 2 to override them with `setProperties()` — this is more reliable than direct `node.characters` manipulation. See [component-patterns.md](../figma-use/references/component-patterns.md#overriding-text-in-a-component-instance) for the full pattern.

For nested instances that expose their own TEXT properties, call `setProperties()` on the nested instance:

```js
const nestedHeading = cardInstance.findOne(n => n.type === "INSTANCE" && n.name === "Text Heading");
if (nestedHeading) {
  nestedHeading.setProperties({ "Text#2104:5": "Actual heading from source code" });
}
```

Only fall back to direct `node.characters` for text that is NOT managed by any component property.

#### Read source code defaults carefully

When translating code components to Figma instances, check the component's default prop values in the source code, not just what's explicitly passed. For example, `<Button size="small">Register</Button>` with no variant prop — check the component definition to find `variant = "primary"` as the default. Selecting the wrong variant (e.g., Neutral instead of Primary) produces a visually incorrect result that's easy to miss.

#### What to build manually vs. import from design system

| Build manually | Import from design system |
|----------------|--------------------------|
| Page wrapper frame | **Components**: buttons, cards, inputs, nav, etc. |
| Section container frames | **Variables**: colors (fills, strokes), spacing (padding, gap), radii |
| Layout grids (rows, columns) | **Text styles**: heading, body, caption, etc. |
| | **Effect styles**: shadows, blurs, etc. |

**Never hardcode hex colors or pixel spacing** when a design system variable exists. Use `setBoundVariable` for spacing/radii and `setBoundVariableForPaint` for colors. Apply text styles with `node.textStyleId` and effect styles with `node.effectStyleId`.

### Step 5: Validate the Full Screen

After composing all sections, call `get_screenshot` on the full page frame and compare against the source. Fix any issues with targeted `use_figma` calls — don't rebuild the entire screen.

**Screenshot individual sections, not just the full page.** A full-page screenshot at reduced resolution hides text truncation, wrong colors, and placeholder text that hasn't been overridden. Take a screenshot of each section by node ID to catch:
- **Cropped/clipped text** — line heights or frame sizing cutting off descenders, ascenders, or entire lines
- **Overlapping content** — elements stacking on top of each other due to incorrect sizing or missing auto-layout
- Placeholder text still showing ("Title", "Heading", "Button")
- Truncated content from layout sizing bugs
- Wrong component variants (e.g., Neutral vs Primary button)

### Step 6: Updating an Existing Screen

When updating rather than creating from scratch:

1. Use `get_metadata` to inspect the existing screen structure.
2. Identify which sections need updating and which can stay.
3. For each section that needs changes:
   - Locate the existing nodes by ID or name
   - Swap component instances if the design system component changed
   - Update text content, variant properties, or layout as needed
   - Remove deprecated sections
   - Add new sections
4. Validate with `get_screenshot` after each modification.

```js
// Example: Swap a button variant in an existing screen
const existingButton = await figma.getNodeByIdAsync("EXISTING_BUTTON_INSTANCE_ID");
if (existingButton && existingButton.type === "INSTANCE") {
  // Import the updated component
  const buttonSet = await figma.importComponentSetByKeyAsync("BUTTON_SET_KEY");
  const newVariant = buttonSet.children.find(c =>
    c.name.includes("variant=primary") && c.name.includes("size=lg")
  ) || buttonSet.defaultVariant;
  existingButton.swapComponent(newVariant);
}
return { success: true, mutatedNodeIds: [existingButton.id] };
```

## Reference Docs

For detailed API patterns and gotchas, load these from the [figma-use](../figma-use/SKILL.md) references as needed:

- [component-patterns.md](../figma-use/references/component-patterns.md) — importing by key, finding variants, setProperties, text overrides, working with instances
- [variable-patterns.md](../figma-use/references/variable-patterns.md) — creating/binding variables, importing library variables, scopes, aliasing, discovering existing variables
- [text-style-patterns.md](../figma-use/references/text-style-patterns.md) — creating/applying text styles, importing library text styles, type ramps
- [effect-style-patterns.md](../figma-use/references/effect-style-patterns.md) — creating/applying effect styles (shadows), importing library effect styles
- [gotchas.md](../figma-use/references/gotchas.md) — layout pitfalls (HUG/FILL interactions, counterAxisAlignItems, sizing order), paint/color issues, page context resets

## Error Recovery

Follow the error recovery process from [figma-use](../figma-use/SKILL.md#6-error-recovery--self-correction):

1. **STOP** on error — do not retry immediately.
2. **Read the error message carefully** to understand what went wrong.
3. If the error is unclear, call `get_metadata` or `get_screenshot` to inspect the current file state.
4. **Fix the script** based on the error message.
5. **Retry** the corrected script — this is safe because failed scripts are atomic (nothing is created if a script errors).

Because this skill works incrementally (one section per call), errors are naturally scoped to a single section. Previous sections from successful calls remain intact.

## Best Practices

### BrightHR-Specific Requirements

- **Component Library Filter:** ONLY use components from "Components: Lumen (Bright-UI)" (fileKey: `3dNASwqNXrYbGQyHsf1Sgg`). Reject all search results from BEX, dotcom, Peninsula, Mobile Apps, or other libraries.
- **Icons:** ONLY use icons from "Components: Icons v2.0" (fileKey: `2lYWKHwYwf4iAYJxILH2Wr`).
- **Font:** ONLY use **Albert Sans** font family. Never use Inter, Roboto, or any other font. Always load the required font weights before text operations.
- **Color Variables:** ONLY use color variables from "BrightHR Colours & Styles" (fileKey: `Tutg1Xnl9e07uED4DZjyUB`). Never hardcode hex colors.
- **Typography Variables:** ONLY use typography variables from "Web Typography" (fileKey: `OudUc4pq10fGO5oHkA674i`).

### General Best Practices

- **Always search before building.** The design system likely has the component, variable, or style you need. Manual construction and hardcoded values should be the exception, not the rule.
- **Search broadly.** Try synonyms and partial terms. A "NavigationPill" might be found under "pill", "nav", "tab", or "chip". For variables, search "color", "spacing", "radius", etc.
- **Prefer design system tokens over hardcoded values.** Use variable bindings for colors, spacing, and radii. Use text styles for typography. Use effect styles for shadows. This keeps the screen linked to the design system.
- **Prefer component instances over manual builds.** Instances stay linked to the source component and update automatically when the design system evolves.
- **Work section by section.** Never build more than one major section per `use_figma` call.
- **Return node IDs from every call.** You'll need them to compose sections and for error recovery.
- **Validate visually after each section.** Use `get_screenshot` to catch issues early.
- **Match existing conventions.** If the file already has screens, match their naming, sizing, and layout patterns.