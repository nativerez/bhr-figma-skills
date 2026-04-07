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

### 4. Make Buttons and Select Menus Full Width

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

### 5. Adjust Content Containers and Stack Vertically

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

### 6. Stack Side-by-Side Content Vertically

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

### 7. Validate Each Responsive Version

After creating each responsive version, validate with screenshots:

```js
// Take screenshots to verify layout
const tabletScreenshot = await get_screenshot(fileKey, tabletFrameId);
const mobileScreenshot = await get_screenshot(fileKey, mobileFrameId);
```

**Check for common issues:**
- **Frames created on same page as desktop original** (not on a different page)
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
- **Apply padding to inner containers, not the main frame** — main frames should be flush to edges
- **Use `layoutSizingHorizontal = "FILL"` for full-width elements** — buttons, selects, content containers
- **Convert all horizontal layouts to vertical** — side-by-side content must stack on mobile
- **Swap component variants for responsive components** — Global Header, navigation, etc. have Size props
- **Validate with screenshots** — visual confirmation catches issues text logs miss
