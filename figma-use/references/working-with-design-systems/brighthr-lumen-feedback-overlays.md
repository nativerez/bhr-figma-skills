# BrightHR Lumen — Feedback & Overlay Components

> Part of the Lumen component reference. See [brighthr-lumen-index.md](brighthr-lumen-index.md) for the full key inventory.

---

## Feedback Components

### Alert
**Key:** `967eb0bb2625d93c53896303b5dab12616b09b03`  
**Variants:** 24  
**Usage:** Sits at the top of content sections. Use for page-level state (info, warning, error, success). Not for user-action feedback — use Toast for that.

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `Information` \| `Warning` \| `Error` \| `Success` | `Information` |
| Inline | VARIANT | `False` \| `True` | `False` |
| Size | VARIANT | `base` \| `sm` \| `xs` | `base` |
| Show Title | BOOLEAN | — | `true` |
| Show Description | BOOLEAN | — | `true` |
| Show Lead Icon | BOOLEAN | — | `true` |
| Show Agree | BOOLEAN | — | `false` |
| Show Dismiss | BOOLEAN | — | `false` |
| Show Buttons | BOOLEAN | — | `false` |
| Title Text | TEXT | — | `Title` |
| Description Text | TEXT | — | *(lorem ipsum)* |
| Icon | INSTANCE_SWAP | — | — |

---

### Toast
**Key:** `9fad757bd940c48ab458fcea2c0ef6ca27917890`  
**Variants:** 5  
**Usage:** User-action-triggered notifications (e.g. success/error after form submit).

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `Success` \| `Error` \| `Information` \| `Tip` \| `Warning` | `Success` |
| Show Button | BOOLEAN | — | `true` |
| Show Icon | BOOLEAN | — | `false` |
| Show Dismiss Timer | BOOLEAN | — | `false` |
| Message | TEXT | — | `{A brief toast messages goes here}` |
| Icon | INSTANCE_SWAP | — | — |

---

### Loader Spinner
**Key:** `d67f390b49be39dba013d6c7759e8c3a7c2f70a0`  
**Variants:** 2

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `Indeterminate Spinner` \| `Indeterminate XING` | `Indeterminate Spinner` |

---

### Progress Bar Rail
**Key:** `1cc088e5ffb96e4bc47d89f865323a0cf4246ee8`  
**Variants:** 9

| Property | Type | Options | Default |
|---|---|---|---|
| Amount | VARIANT | `0%` \| `10%` \| `25%` \| `35%` \| `50%` \| `65%` \| `75%` \| `90%` \| `100%` | `0%` |

---

## Overlay Components

### Modal
**Key:** `d5fce75be566ff374a864c7421dd2c2b00bf10f9`  
**Variants:** 10

| Property | Type | Options | Default |
|---|---|---|---|
| Variant | VARIANT | `Default` \| `Destructive` | `Default` |
| Size | VARIANT | `xs` \| `sm` \| `base` \| `lg` \| `xl` | `xs` |
| Show Header | BOOLEAN | — | `true` |
| Show Footer | BOOLEAN | — | `true` |
| Show Checkbox | BOOLEAN | — | `false` |

---

### Modal Header
**Key:** `82bac7922ed9d67ca770206c21f022d7d22d72ca`  
**Variants:** 3

| Property | Type | Options | Default |
|---|---|---|---|
| Variant | VARIANT | `Primary` \| `Inverted` \| `Destructive` | `Primary` |
| Show Close | BOOLEAN | — | `true` |

---

### Modal Footer
**Key:** `0fc22d1d5e8a55496c47b9dc2e43eaaf64c318c6`  
**Variants:** 2

| Property | Type | Options | Default |
|---|---|---|---|
| Variant | VARIANT | `Default` \| `Destructive` | `Default` |
| Show secondary CTA | BOOLEAN | — | `true` |
| Show summary | BOOLEAN | — | `true` |
| Show background | BOOLEAN | — | `false` |
| Summary text | TEXT | — | `optional summary information goes here` |

---

### Tooltip
**Key:** `75bb5966aa02a36e414b709c2e4c7d8430411e76`  
**Variants:** 12

| Property | Type | Options | Default |
|---|---|---|---|
| Direction | VARIANT | `Top` \| `Top Start` \| `Top End` \| `Bottom` \| `Bottom Start` \| `Bottom End` \| `Left` \| `Left Start` \| `Left End` \| `Right` \| `Right Start` \| `Right End` | `Top` |
| Title | TEXT | — | `Example tooltip text` |

---

### Context Menu
**Key:** `196053c53c8e6f64f9adb1381581bf10b8c58c25`  
**Variants:** 4  
**Usage:** Actions menu for space-constrained UIs (e.g. table row actions).

| Property | Type | Options | Default |
|---|---|---|---|
| State | VARIANT | `Default` \| `Hover` \| `Active` \| `Disabled` | `Disabled` |
| Show Label | BOOLEAN | — | `true` |
| Label | TEXT | — | `{Label}` |

---

### Context Menu Option State
**Key:** `9809e6d620c069f88744d48b2807682b33c4a7a7`  
**Variants:** 8  
**Usage:** Individual menu option row (standard or destructive).

| Property | Type | Options | Default |
|---|---|---|---|
| Destructive | VARIANT | `False` \| `True` | `False` |
| State | VARIANT | `Default` \| `Hover` \| `Active` \| `Disabled` | `Default` |
| Show Description | BOOLEAN | — | `true` |
| Show Leading Icon | BOOLEAN | — | `true` |
| Label | TEXT | — | `{Label}` |
| Description | TEXT | — | `{Description}` |
| Leading Icon | INSTANCE_SWAP | — | — |
