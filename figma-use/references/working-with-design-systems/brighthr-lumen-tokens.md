# BrightHR Lumen — Color Tokens & Design Variables

> Part of the Lumen component reference. See [brighthr-lumen-index.md](brighthr-lumen-index.md) for the full component key inventory.

**File:** [BrightHR Colours & Styles](https://www.figma.com/design/Tutg1Xnl9e07uED4DZjyUB) · File Key: `Tutg1Xnl9e07uED4DZjyUB`  
**Collections:** `Primitives` (73 vars, BrightHR mode) + `Tokens` (114 vars, Light / Dark / Mode)

> **Rule:** Always bind to `Tokens` variables. Never reference `Primitives` directly in fills, strokes, or text — they are raw values, not semantic roles.

---

## Binding Variables in Code

```js
// Resolve a variable by name, then bind to a fill
const collections = await figma.variables.getLocalVariableCollectionsAsync();
const tokensCollection = collections.find(c => c.name === "Tokens");
// Iterate vars to find by name:
for (const varId of tokensCollection.variableIds) {
  const v = await figma.variables.getVariableByIdAsync(varId);
  if (v.name === "bg/page/Primary") {
    const newFill = figma.variables.setBoundVariableForPaint(
      { type: "SOLID", color: { r: 1, g: 1, b: 1 } },
      "color",
      v
    );
    frame.fills = [newFill];
    break;
  }
}
```

**Known stable variable IDs** (verify with `getLocalVariableCollectionsAsync` if unsure):
```
bg/page/Primary (Surface/Bg/Primary):    87720b593e2892bee899ba1777c2c3b0aeaa4699
bg/page/Secondary (Surface/Bg/Secondary): e41e811096a50e4934345543d4776f57099b47e5
text/main/Primary (Text/Main/Primary):   67d9746fa4a716f0e2b716af1869b7d8450701d3
border/container/Primary:               f672282e56914075dc2d0213a51e829d392ff0ec
```

---

## Text Tokens

| Variable | Scope | Usage |
|---|---|---|
| `text/main/Primary` | TEXT_FILL | Primary body / heading text |
| `text/main/Secondary` | TEXT_FILL | Secondary / muted text |
| `text/main/Disabled` | TEXT_FILL | Disabled state text |
| `text/main/Invert` | TEXT_FILL | Text on dark / coloured backgrounds |
| `text/link/Default` | TEXT_FILL | Link default |
| `text/link/Hover` | TEXT_FILL | Link hover |
| `text/link/Active` | TEXT_FILL | Link active |
| `text/link/linkSecondary` | TEXT_FILL | Secondary link style |
| `text/asset/accentPrimary` | TEXT_FILL | Accent text (sky blue) |
| `text/asset/accentSecondary` | TEXT_FILL | Secondary accent text |
| `text/asset/successPrimary` | TEXT_FILL | Success state text |
| `text/asset/errorPrimary` | TEXT_FILL | Error state text |
| `text/asset/warning` | TEXT_FILL | Warning text |
| `text/asset/information` | TEXT_FILL | Information text |
| `textButtonPrimary` | TEXT_FILL | Primary button label |
| `textButtonSecondary` | TEXT_FILL | Secondary button label |
| `textButtonInvert` | TEXT_FILL | Inverted button label |

---

## Background Tokens

| Variable | Scope | Usage |
|---|---|---|
| `bg/page/Primary` | FRAME_FILL, SHAPE_FILL | Main page background (white) |
| `bg/page/Secondary` | FRAME_FILL, SHAPE_FILL | Subtle secondary bg |
| `bg/page/Tertiary` | ALL_SCOPES | Tertiary bg |
| `bg/page/Nav` | FRAME_FILL, SHAPE_FILL | Side nav bg |
| `bg/information/Bold` | FRAME_FILL, SHAPE_FILL | Information — bold |
| `bg/information/Default` | ALL_SCOPES | Information — default |
| `bg/information/Subtle` | FRAME_FILL, SHAPE_FILL | Information — subtle |
| `bg/error/Bold` | FRAME_FILL, SHAPE_FILL | Error — bold |
| `bg/error/Default` | FRAME_FILL, SHAPE_FILL | Error — default |
| `bg/success/Bold` | FRAME_FILL, SHAPE_FILL | Success — bold |
| `bg/success/Default` | FRAME_FILL, SHAPE_FILL | Success — default |
| `bg/success/Subtle` | FRAME_FILL, SHAPE_FILL | Success — subtle |
| `bg/warning/Bold` | FRAME_FILL, SHAPE_FILL | Warning — bold |
| `bg/warning/Default` | ALL_SCOPES | Warning — default |
| `bg/warning/Subtle` | FRAME_FILL, SHAPE_FILL | Warning — subtle |
| `bg/lightWarning/Bold` | FRAME_FILL, SHAPE_FILL | Light warning — bold |
| `bg/lightWarning/Subtle` | FRAME_FILL, SHAPE_FILL | Light warning — subtle |
| `bg/neutral/Bold` | FRAME_FILL, SHAPE_FILL | Neutral — bold |
| `bg/neutral/Subtle` | FRAME_FILL, SHAPE_FILL | Neutral — subtle |
| `bg/neutral/Disabled` | FRAME_FILL, SHAPE_FILL | Disabled state bg |
| `bg/accent/Bold` | ALL_SCOPES | Accent — bold |
| `bg/accent/Default` | ALL_SCOPES | Accent bg (sky blue) |
| `bg/accent/Subtle` | ALL_SCOPES | Accent — subtle |
| `bg/button/primary/Default` | FRAME_FILL, SHAPE_FILL | Primary button bg |
| `bg/button/primary/Hover` | FRAME_FILL, SHAPE_FILL | Primary button hover |
| `bg/button/secondary/Default` | FRAME_FILL, SHAPE_FILL | Secondary button bg |
| `bg/button/secondary/Hover` | FRAME_FILL, SHAPE_FILL | Secondary button hover |
| `bg/button/destructive/Default` | FRAME_FILL, SHAPE_FILL | Destructive button bg |
| `bg/button/Disabled` | FRAME_FILL, SHAPE_FILL | Disabled button bg |
| `bg/button/White` | FRAME_FILL, SHAPE_FILL | White button bg |
| `bg/input/Default` | FRAME_FILL, SHAPE_FILL | Input field bg |
| `bg/input/Selected` | FRAME_FILL, SHAPE_FILL | Input selected state |
| `bg/input/Disabled` | FRAME_FILL, SHAPE_FILL | Input disabled state |
| `bg/switch/BgOff` | FRAME_FILL, SHAPE_FILL | Switch off bg |
| `bg/switch/BgOn` | FRAME_FILL, SHAPE_FILL | Switch on bg |
| `bg/switch/Handle` | FRAME_FILL, SHAPE_FILL | Switch handle |

---

## Border Tokens

| Variable | Scope | Usage |
|---|---|---|
| `border/container/Primary` | STROKE_COLOR | Primary card / container border |
| `border/container/Secondary` | STROKE_COLOR | Secondary container border |
| `border/Divider` | STROKE_COLOR | Horizontal / vertical dividers |
| `border/input/Default` | STROKE_COLOR | Input border — default |
| `border/input/Focus` | STROKE_COLOR | Input border — focused |
| `border/input/Error` | STROKE_COLOR | Input border — error |
| `border/button/PrimaryOutline` | STROKE_COLOR | Primary button outline |
| `border/button/PrimaryFocus` | STROKE_COLOR | Primary button focus ring |
| `border/button/SecondaryOutline` | STROKE_COLOR | Secondary button outline |
| `border/button/SecondaryFocus` | STROKE_COLOR | Secondary button focus ring |
| `border/button/WhiteOutline` | STROKE_COLOR | White button outline |
| `border/semantic/success` | STROKE_COLOR | Success border |
| `border/semantic/error` | STROKE_COLOR | Error border |
| `border/semantic/warning` | STROKE_COLOR | Warning border |
| `border/semantic/information` | STROKE_COLOR | Information border |
| `border/semantic/borderAccent` | STROKE_COLOR | Accent border |
| `borderControlDefault` | ALL_SCOPES | Checkbox / radio border default |
| `borderControlDisabled` | ALL_SCOPES | Checkbox / radio border disabled |

---

## Icon Tokens

| Variable | Scope | Usage |
|---|---|---|
| `icon/semantic/neutralPrimary` | SHAPE_FILL, STROKE_COLOR | Default icon colour |
| `icon/semantic/neutralSecondary` | ALL_SCOPES | Secondary icon colour |
| `icon/semantic/accent` | SHAPE_FILL, STROKE_COLOR | Accent icon (sky blue) |
| `icon/semantic/success` | SHAPE_FILL, STROKE_COLOR | Success icon |
| `icon/semantic/error` | SHAPE_FILL, STROKE_COLOR | Error icon |
| `icon/semantic/warning` | SHAPE_FILL, STROKE_COLOR | Warning icon |
| `icon/semantic/informationBold` | SHAPE_FILL, STROKE_COLOR | Information icon |
| `icon/invert` | SHAPE_FILL, STROKE_COLOR | Icon on dark bg |
| `icon/input/Default` | SHAPE_FILL, STROKE_COLOR | Input icon — default |
| `icon/input/Focus` | SHAPE_FILL, STROKE_COLOR | Input icon — focused |
| `icon/input/Disabled` | SHAPE_FILL, STROKE_COLOR | Input icon — disabled |

---

## Control Surface Tokens

| Variable | Scope | Usage |
|---|---|---|
| `surfaceControlUnselected` | FRAME_FILL, SHAPE_FILL | Unselected checkbox / radio |
| `surfaceControlSelected` | FRAME_FILL, SHAPE_FILL | Selected checkbox / radio |
| `surfaceControlDisabled` | FRAME_FILL, SHAPE_FILL | Disabled control |

---

## Spacing Tokens

| Variable | Scope | Approx. px |
|---|---|---|
| `spacing/None` | WIDTH_HEIGHT, GAP | 0 |
| `spacing/sm` | ALL_SCOPES | 4 |
| `spacing/base` | WIDTH_HEIGHT, GAP | 8 |
| `spacing/lg` | WIDTH_HEIGHT, GAP | 12 |
| `spacing/xl` | WIDTH_HEIGHT, GAP | 16 |
| `spacing/2xl` | WIDTH_HEIGHT, GAP | 24 |
| `spacing/3xl` | WIDTH_HEIGHT, GAP | 32 |
| `spacing/4xl` | WIDTH_HEIGHT, GAP | 40 |
| `spacing/5xl` | WIDTH_HEIGHT, GAP | 48 |
| `spacing/6xl` | WIDTH_HEIGHT, GAP | 64 |

---

## Border Radius Tokens

| Variable | Scope |
|---|---|
| `radius/roundedNone` | CORNER_RADIUS |
| `radius/roundedSm` | CORNER_RADIUS |
| `radius/roundedBase` | CORNER_RADIUS |
| `radius/roundedmd` | CORNER_RADIUS |
| `radius/roundedLg` | CORNER_RADIUS |
| `radius/roundedXl` | CORNER_RADIUS |
| `radius/rounded2xl` | CORNER_RADIUS |
| `radius/rounded3xl` | CORNER_RADIUS |
| `radius/roundedFull` | CORNER_RADIUS |
