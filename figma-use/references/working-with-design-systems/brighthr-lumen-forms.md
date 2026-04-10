# BrightHR Lumen — Form Input Components

> Part of the Lumen component reference. See [brighthr-lumen-index.md](brighthr-lumen-index.md) for the full key inventory.

---

### Input Field
**Key:** `0fad07e208acfaa23f3f834352810b57e4868a6f`  
**Variants:** 4

| Property | Type | Options | Default |
|---|---|---|---|
| Size | VARIANT | `base` \| `xl` | `base` |
| Orientation | VARIANT | `Stacked` \| `Long` | `Stacked` |
| Description | BOOLEAN | — | `true` |
| Label | BOOLEAN | — | `true` |
| Required | BOOLEAN | — | `false` |
| Error Description | BOOLEAN | — | `false` |

> **Overriding text:** Label, description, and placeholder live in nested text nodes. Traverse `instance.findAll(n => n.type === "TEXT")` and check `componentPropertyReferences` on each to identify which to override.

---

### Input Search
**Key:** `35f08b7a52a9a5efa6383e113f8dbb5c0cc9bc4a`  
**Variants:** 3

| Property | Type | Options | Default |
|---|---|---|---|
| State | VARIANT | `Placeholder` \| `Active` \| `Disabled` | `Placeholder` |

---

### Input Field Text Area
**Key:** `04a9737a0ab2cdaf232bbcf6ebec459b60c97e9b`  
**Variants:** 2

| Property | Type | Options | Default |
|---|---|---|---|
| Orientation | VARIANT | `Stacked` \| `Long` | `Stacked` |
| Description | BOOLEAN | — | `true` |
| Label | BOOLEAN | — | `true` |

---

### Select Field
**Key:** `cc100ea329775132cbc5733225256c0167a903bd`  
**Variants:** 5

| Property | Type | Options | Default |
|---|---|---|---|
| State | VARIANT | `Placeholder` \| `Default` \| `Hover/Focus/Active` \| `Error` \| `Disabled` | `Placeholder` |
| Filter | VARIANT | `Off` | `Off` |

---

### Select Option
**Key:** `918c4a003d49da4f4d00a68d9f4703c9bcbff0d8`  
**Variants:** 6

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `Inline` \| `Stacked` | `Inline` |
| Selected | VARIANT | `False` \| `True` | `False` |
| Enabled | VARIANT | `False` \| `True` | `True` |

---

### Checkbox
**Key:** `a6f5ae452ca4b7f689555b28ea39c113e9a10a39`  
**Variants:** 6

| Property | Type | Options | Default |
|---|---|---|---|
| State | VARIANT | `Checked` \| `Unchecked` \| `Indeterminate` | `Checked` |
| Enabled | VARIANT | `True` \| `False` | `True` |

---

### Checkbox Block
**Key:** `c3e24d0703efaeeb55017dee704ab2bf6cfc1785`  
**Variants:** 4

| Property | Type | Options | Default |
|---|---|---|---|
| Selected | VARIANT | `True` \| `False` | `False` |
| Enabled | VARIANT | `True` \| `False` | `True` |
| Label | TEXT | — | `Label` |
| Description | TEXT | — | `{Optional description goes here}` |
| Show Description | BOOLEAN | — | `true` |
| Show Input | BOOLEAN | — | `true` |

---

### Radio
**Key:** `e6d1b00eeb058fcdb02fd76e71d07f12ebf64c09`  
**Variants:** 4  
**Usage:** Individual radio button. Compose a group with a vertical auto-layout frame.

| Property | Type | Options | Default |
|---|---|---|---|
| Selected | VARIANT | `True` \| `False` | `True` |
| Enabled | VARIANT | `True` \| `False` | `True` |

---

### Radio Block
**Key:** `e9d758f5b63e66813c4ec1b8e3793b9cf0cfe0a6`  
**Variants:** 16  
**Usage:** Card-style radio with optional icon. Use for visual selection UIs (plan picker, visibility selector).

| Property | Type | Options | Default |
|---|---|---|---|
| Selected | VARIANT | `False` \| `True` | `False` |
| Text Align | VARIANT | `Centered` \| `Left` | `Centered` |
| Enabled | VARIANT | `True` \| `False` | `True` |
| Size | VARIANT | `base` \| `lg` | `base` |
| Label | TEXT | — | `Label` |
| Description | TEXT | — | `{Optional description goes here}` |
| Show Description | BOOLEAN | — | `true` |
| Show Icon | BOOLEAN | — | `true` |
| Icon | INSTANCE_SWAP | — | — |

---

### Switch (Toggle)
**Key:** `e18619dd7fda5f378c06998553afe2f9efcf2174`  
**Variants:** 8

| Property | Type | Options | Default |
|---|---|---|---|
| Selected | VARIANT | `True` \| `False` | `False` |
| Enabled | VARIANT | `True` \| `False` | `False` |
| Size | VARIANT | `sm` \| `base` | `sm` |

> ⚠️ Default has `Enabled=False`. Always set `Enabled=True` for an interactive toggle.

```js
const swSet = await figma.importComponentSetByKeyAsync("e18619dd7fda5f378c06998553afe2f9efcf2174");
const inst = swSet.children.find(c =>
  c.name.includes("Selected=True") && c.name.includes("Enabled=True") && c.name.includes("Size=base")
).createInstance();
```

---

### Segmented Control
**Key:** `7803214fffecfa176b9f5f942346335b1e088345`  
**Variants:** 12

| Property | Type | Options | Default |
|---|---|---|---|
| State | VARIANT | `Option A` \| `Option B` \| `Option C` | `Option A` |
| Size | VARIANT | `xs` \| `sm` \| `base` \| `Lg` | `xs` |
| Show third option | BOOLEAN | — | `true` |
