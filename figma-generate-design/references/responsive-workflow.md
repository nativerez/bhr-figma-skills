# Creating Responsive Versions from Desktop Frames

When the user requests **tablet and mobile versions** of an existing desktop frame, follow this specialized workflow instead of building from scratch. This ensures proper adaptation of interactive components and layout for smaller viewports.

## When to Use This Workflow

Trigger this workflow when the user:
- Explicitly asks for "tablet" or "mobile" versions
- Mentions creating responsive versions from an existing desktop design
- References specific desktop frames to adapt for smaller screens
- Says "make this responsive" or "create mobile/tablet layouts"

## Standard Viewport Sizes

- **Desktop:** 1440px (or existing frame width if larger than 1024px)
- **Tablet:** 768px width
- **Mobile:** 375px width

## Standard Padding

- **Main frame:** NO padding on the main frame itself
- **Inner containers:** 20px padding on left and right sides for all inner content containers
- **Tablet content width:** 728px (768 - 40px total padding)
- **Mobile content width:** 335px (375 - 40px total padding)

## Required Steps

### 1. Clone and Resize the Desktop Frame

Start by cloning the desktop frame and positioning the responsive versions adjacent to avoid overlap. **Enable auto-layout on the cloned frames** to ensure content flows properly.

**CRITICAL:** Ensure you're on the same page as the original desktop frame before cloning. Frames must be created on the same page to maintain context and organization.

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

// CRITICAL: Switch to the page containing the original frame
const sourcePage = originalFrame.parent.type === "PAGE" ? originalFrame.parent : figma.currentPage;
await figma.setCurrentPageAsync(sourcePage);

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

// Helper function to preserve icon sizes (prevent stretching/resizing)
function preserveIconSizes(node) {
  if (!node) return;
  
  // Identify icon nodes by:
  // - Instance from icon library (Icons v2.0)
  // - Node name contains "icon" (case insensitive)
  // - SVG or vector graphics
  const isIcon = 
    (node.type === "INSTANCE" && node.name.toLowerCase().includes("icon")) ||
    (node.type === "COMPONENT" && node.name.toLowerCase().includes("icon")) ||
    node.name.toLowerCase().includes("icon") ||
    node.type === "VECTOR" ||
    node.type === "BOOLEAN_OPERATION";
  
  if (isIcon) {
    // Store original dimensions
    const originalWidth = node.width;
    const originalHeight = node.height;
    
    // If in auto-layout parent, set to fixed size (prevents stretching)
    if (node.parent && node.parent.layoutMode !== "NONE") {
      node.layoutSizingHorizontal = "FIXED";
      node.layoutSizingVertical = "FIXED";
    }
    
    // Ensure dimensions haven't changed
    if (node.width !== originalWidth || node.height !== originalHeight) {
      node.resize(originalWidth, originalHeight);
    }
  }
  
  // Recursively process children (but not component instances)
  if ("children" in node && node.type !== "INSTANCE") {
    for (const child of node.children) {
      preserveIconSizes(child);
    }
  }
}

// Preserve icon sizes in both responsive frames
preserveIconSizes(tabletFrame);
preserveIconSizes(mobileFrame);

return { 
  success: true, 
  tabletFrameId: tabletFrame.id,
  mobileFrameId: mobileFrame.id
};
```

### 2. Swap Global Product Header Component Size

The Global product header component has Size variants: Desktop, Tablet, Mobile. **Always swap the Size property** to match the viewport:

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

### 3. Hide Side Navigation on Tablet and Mobile

Side navigation should be hidden on smaller viewports (typically replaced with a hamburger menu in the header):

```js
const sideNav = frame.findOne(n => n.name === "BrightSafe Navigation" || n.name === "Side Nav");
if (sideNav) {
  sideNav.visible = false;
}
```

### 4. Convert All Horizontal Auto-Layout Frames to Vertical (CRITICAL)

**This is a universal transformation rule for responsive design.** On tablet and mobile, horizontal layouts that work on desktop become problematic due to limited width. ALL auto-layout frames (NOT component instances) set to HORIZONTAL must be converted to VERTICAL, at every nesting level.

**This applies to:**
- Parent and nested auto-layout **frames** (recursively)
- Content containers, card grids, button groups, form rows
- Any regular frame with `layoutMode === "HORIZONTAL"`

**This does NOT apply to:**
- Component instances (preserve their internal layout)
- Regular frames without auto-layout

**Why this matters:**
- Horizontal layouts on desktop (e.g., 3 cards in a row) don't fit on mobile
- Limited width forces content to stack vertically
- Child elements need to fill available width instead of sitting side-by-side

```js
// Recursive function to convert horizontal auto-layout frames to vertical
function convertHorizontalToVertical(node, isTablet = false) {
  // Skip component instances - preserve their internal layout
  if (node.type === "INSTANCE") {
    // Only ensure instances fill width if parent has auto-layout
    if (node.parent && node.parent.layoutMode !== "NONE") {
      node.layoutSizingHorizontal = "FILL";
      node.layoutSizingVertical = "HUG"; // Never use FIXED height
    }
    return; // Don't recurse into instances
  }
  
  // Convert horizontal auto-layout frames to vertical
  if (node.type === "FRAME" && node.layoutMode === "HORIZONTAL") {
    node.layoutMode = "VERTICAL";
    node.primaryAxisSizingMode = "AUTO"; // Height hugs content (HUG)
    node.counterAxisSizingMode = "FIXED"; // Width stays fixed
    
    // Set alignment to top-left for stacked children
    node.primaryAxisAlignItems = "MIN"; // Top align
    node.counterAxisAlignItems = "MIN"; // Left align
    
    // Ensure consistent spacing between stacked items
    // If horizontal spacing was 0 or very small (auto/packed layout), use 16px for vertical
    // Otherwise preserve existing spacing for consistency
    if (node.itemSpacing === 0 || node.itemSpacing < 8) {
      node.itemSpacing = 16; // Standard vertical spacing for mobile
    }
    // Note: Spacing >= 8px is preserved to maintain intentional design decisions
    
    // Make container fill width if parent has auto-layout
    if (node.parent && node.parent.layoutMode !== "NONE" && node.parent.type !== "PAGE") {
      node.layoutSizingHorizontal = "FILL";
      node.layoutSizingVertical = "HUG"; // Never use FIXED height
    }
  }
  
  // Handle text nodes to prevent 1px width issues
  if (node.type === "TEXT") {
    // Ensure text frames fill available width and hug height
    if (node.parent && node.parent.layoutMode !== "NONE") {
      node.layoutSizingHorizontal = "FILL";
      node.layoutSizingVertical = "HUG";
    }
    
    // Change center-aligned and right-aligned text to left-aligned for mobile
    if (node.textAlignHorizontal === "CENTER" || node.textAlignHorizontal === "RIGHT") {
      node.textAlignHorizontal = "LEFT";
    }
    
    return; // Don't recurse into text nodes
  }
  
  // Handle icons - preserve their size (prevent stretching)
  const isIcon = 
    (node.type === "INSTANCE" && node.name.toLowerCase().includes("icon")) ||
    (node.type === "COMPONENT" && node.name.toLowerCase().includes("icon")) ||
    node.name.toLowerCase().includes("icon") ||
    node.type === "VECTOR" ||
    node.type === "BOOLEAN_OPERATION";
  
  if (isIcon) {
    // Store original dimensions
    const originalWidth = node.width;
    const originalHeight = node.height;
    
    // Set to fixed size in auto-layout (prevents stretching)
    if (node.parent && node.parent.layoutMode !== "NONE") {
      node.layoutSizingHorizontal = "FIXED";
      node.layoutSizingVertical = "FIXED";
    }
    
    // Ensure dimensions preserved
    if (node.width !== originalWidth || node.height !== originalHeight) {
      node.resize(originalWidth, originalHeight);
    }
    
    return; // Don't recurse into icons
  }
  
  // Recursively process all children
  if ("children" in node) {
    for (const child of node.children) {
      convertHorizontalToVertical(child, isTablet);
      
      // After converting parent to vertical, make child frames fill width
      // CRITICAL: Only set sizing if parent has auto-layout
      if (node.layoutMode === "VERTICAL" && child.type === "FRAME") {
        child.layoutSizingHorizontal = "FILL";
        child.layoutSizingVertical = "HUG"; // Never use FIXED height
      }
    }
  }
}

// Apply to tablet frame
convertHorizontalToVertical(tabletFrame, true);

// Apply to mobile frame
convertHorizontalToVertical(mobileFrame, false);
```

**What this does:**
1. **Finds all horizontal auto-layout frames** (skips component instances)
2. **Converts them to vertical** (`layoutMode = "VERTICAL"`)
3. **Sets top-left alignment** for stacked children (`primaryAxisAlignItems = "MIN"`, `counterAxisAlignItems = "MIN"`)
4. **Ensures consistent spacing** (16px for auto/zero spacing, preserves existing spacing ≥ 8px)
5. **Sets proper sizing** (HUG height always, FILL width for nested frames)
6. **Only sets sizing properties when parent has auto-layout** (prevents errors)
7. **Preserves component instance layouts** (only makes them fill width)
8. **Fixes text frames** (FILL width, HUG height, changes CENTER/RIGHT → LEFT alignment)
9. **Preserves icon sizes** (FIXED sizing to prevent stretching, maintains original dimensions)

**Common patterns this handles:**
- Card grids → vertical stack of full-width cards
- Button groups side-by-side (desktop) → stacked buttons (mobile)
- Form rows with multiple inputs → vertical stack of full-width inputs
- Navigation pills in a row → vertical list of nav items
- Image + text layouts (horizontal) → image above text (vertical)

**Important:** Component instances (like buttons, inputs, cards) preserve their internal auto-layout settings. Only their container sizing changes (FILL width, HUG height). Icons maintain their original dimensions and are never stretched or resized.

### 5. Make Buttons and Select Menus Full Width

**Critical for mobile UX:** Buttons and select menus should stretch to fill the available width using auto-layout sizing. This improves touch targets and follows mobile design patterns.

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

### 6. Adjust Content Containers and Stack Vertically

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

### 6.1. Mobile-Only: Consolidate Padding to Innermost Content Frames

**MOBILE OPTIMIZATION:** Desktop designs often have padding at multiple nesting levels (parent frame → child frame → content). On mobile's limited width, this creates excessive horizontal whitespace. Remove redundant outer padding and keep it only on the innermost frame containing actual content.

```js
// Helper to check if a frame contains actual content (text, buttons, inputs, etc.)
function hasActualContent(node) {
  if (!node || !("children" in node)) return false;
  
  for (const child of node.children) {
    // Direct content nodes
    if (child.type === "TEXT") return true;
    if (child.type === "INSTANCE" && (
      child.name.includes("Button") || 
      child.name.includes("Input") || 
      child.name.includes("Select") ||
      child.name.includes("Field")
    )) return true;
    
    // Recurse into regular frames (not instances)
    if (child.type === "FRAME" && hasActualContent(child)) return true;
  }
  
  return false;
}

// Helper to find innermost content-containing frames
function findInnermostContentFrames(node, results = []) {
  if (node.type === "INSTANCE") return results; // Skip component instances
  
  if (node.type === "FRAME") {
    // Check if this frame has direct content
    const hasDirectContent = node.children.some(child => 
      child.type === "TEXT" || 
      (child.type === "INSTANCE" && (
        child.name.includes("Button") || 
        child.name.includes("Input") || 
        child.name.includes("Select") ||
        child.name.includes("Field")
      ))
    );
    
    if (hasDirectContent) {
      // This is an innermost content frame
      results.push(node);
    } else {
      // Recurse into children
      for (const child of node.children) {
        findInnermostContentFrames(child, results);
      }
    }
  } else if ("children" in node) {
    // Not a frame, but has children - recurse
    for (const child of node.children) {
      findInnermostContentFrames(child, results);
    }
  }
  
  return results;
}

// Helper to remove padding from all ancestor frames
function removePaddingFromAncestors(contentFrame, rootFrame) {
  let current = contentFrame.parent;
  
  while (current && current !== rootFrame && current.type !== "PAGE") {
    // Only remove padding from regular frames, not instances
    if (current.type === "FRAME") {
      current.paddingLeft = 0;
      current.paddingRight = 0;
    }
    current = current.parent;
  }
}

// Apply to MOBILE ONLY (not tablet)
const contentFrames = findInnermostContentFrames(mobileFrame);

contentFrames.forEach(frame => {
  // Ensure this frame has standard mobile padding
  if (frame.type === "FRAME") {
    frame.paddingLeft = 20;
    frame.paddingRight = 20;
    
    // Remove padding from all ancestor frames
    removePaddingFromAncestors(frame, mobileFrame);
  }
});
```

**What this does:**
1. **Finds innermost frames** that directly contain content (text, buttons, inputs)
2. **Ensures those frames have 20px horizontal padding** for proper spacing
3. **Removes padding from all ancestor frames** to eliminate redundant whitespace
4. **Skips component instances** entirely (preserves their internal spacing)
5. **Mobile-only optimization** (doesn't affect tablet or desktop)

**Example transformation:**
```
Before (Desktop):
└─ Outer Frame (padding: 24px) 
   └─ Middle Frame (padding: 16px)
      └─ Content Frame (padding: 20px)
         └─ Text / Buttons

After (Mobile):
└─ Outer Frame (padding: 0px) ✓ Removed
   └─ Middle Frame (padding: 0px) ✓ Removed  
      └─ Content Frame (padding: 20px) ✓ Kept
         └─ Text / Buttons
```

This maximizes available width on mobile while maintaining proper spacing around content.

### 7. Stack Side-by-Side Content Vertically

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

### 8. Validate Each Responsive Version

After creating each responsive version, validate with screenshots:

```js
// Take screenshots to verify layout
const tabletScreenshot = await get_screenshot(fileKey, tabletFrameId);
const mobileScreenshot = await get_screenshot(fileKey, mobileFrameId);
```

**Check for common issues:**
- **Frames created on same page as desktop original** (not on a different page)
- **All horizontal auto-layout frames converted to vertical** (check at all nesting levels, but skip component instances)
- **Vertical frames have top-left alignment** (`primaryAxisAlignItems = "MIN"`, `counterAxisAlignItems = "MIN"`)
- **Consistent spacing between stacked elements** (16px for previously auto/zero spacing, preserved for intentional gaps ≥ 8px)
- **Mobile: Padding consolidated to innermost content frames** (no redundant padding on ancestor frames, only 20px on frames with actual content)
- **Icons maintain original size** (not stretched or resized, `layoutSizingHorizontal = "FIXED"`, `layoutSizingVertical = "FIXED"`)
- **Child frames in vertical layouts use FILL width and HUG height** (never FIXED height)
- **Text frames fill width and hug height** (no 1px wide text, `layoutSizingHorizontal = "FILL"`, `layoutSizingVertical = "HUG"`)
- **Center-aligned and right-aligned text changed to left-aligned** for mobile (`textAlignHorizontal = "LEFT"`)
- **Component instances preserve their internal layout** (only their container sizing changes)
- **No errors about layoutSizing on non-auto-layout parents** (check parent has auto-layout before setting sizing)
- Buttons and select menus are full width (using `layoutSizingHorizontal = "FILL"`)
- Content containers have proper padding (20px left/right on inner containers, not the main frame)
- Side-by-side content is stacked vertically
- Global header Size variant matches viewport
- Side navigation is hidden
- No cropped/clipped text
- No overlapping elements
- All content fits within viewport width

## Best Practices

- **Always switch to the source page first** — Use `await figma.setCurrentPageAsync()` to ensure responsive frames are created on the same page as the original desktop frame
- **Always clone first, then resize** — never try to resize in place and then clone
- **Enable auto-layout on cloned frames immediately** after resizing to ensure content flows
- **Preserve icon sizes** — icons should maintain original dimensions with FIXED sizing; never stretch or resize them
- **Apply padding to inner containers, not the main frame** — main frames should be flush to edges
- **Mobile-only: Consolidate padding to innermost frames** — remove redundant padding from ancestor frames; keep 20px only on frames that directly contain text/buttons/inputs
- **Convert horizontal auto-layout frames to vertical (CRITICAL)** — recursively transform frames (not instances) at every nesting level, always check parent has auto-layout before setting sizing
- **Preserve intentional spacing, standardize small gaps** — spacing ≥ 8px is preserved for consistency; spacing < 8px (auto/packed layouts) becomes 16px for mobile
- **Set top-left alignment for vertical stacked content** — use `primaryAxisAlignItems = "MIN"` and `counterAxisAlignItems = "MIN"` when converting to vertical
- **Always use HUG height, never FIXED** — heights should grow with content, not be constrained
- **Preserve component instance layouts** — only change their container sizing (FILL width, HUG height), not their internal layoutMode
- **Fix text frames to prevent 1px width** — text nodes should use `layoutSizingHorizontal = "FILL"` and `layoutSizingVertical = "HUG"` when in auto-layout parents
- **Change center-aligned and right-aligned text to left-aligned** — convert `textAlignHorizontal = "CENTER"` or `"RIGHT"` to `"LEFT"` for mobile readability
- **Use `layoutSizingHorizontal = "FILL"` for full-width elements** — buttons, selects, content containers, text frames, and all child frames in vertical layouts
- **Swap component variants for responsive components** — Global Header, navigation, etc. have Size props
- **Validate with screenshots** — visual confirmation catches issues text logs miss
