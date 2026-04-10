# BrightHR Lumen — Data Display, Navigation Aids & Identity

> Part of the Lumen component reference. See [brighthr-lumen-index.md](brighthr-lumen-index.md) for the full key inventory.

---

## Data Display

### Badge
**Key:** `646d8937657ce4d86e7cdffad980def6fc2447be`  
**Variants:** 24  
**Usage:** Status indicator. Always shows a text label; iconography optional.

| Property | Type | Options | Default |
|---|---|---|---|
| State | VARIANT | `Default` \| `Info` \| `Success` \| `Warning` \| `Error` \| `Notification` | `Default` |
| Background | VARIANT | `Default` \| `Dark` \| `White` \| `None` | `Default` |

> **Nested label:** Badge wraps a `Badge Master` instance that holds the visible text. Set it after appending:

```js
const inst = badgeSet.defaultVariant.createInstance();
parent.appendChild(inst);
const master = inst.findOne(n => n.name === "Badge Master");
if (master) master.setProperties({ "Label#9026:5": "Active" });
```

**Badge Master properties:**

| Property | Default |
|---|---|
| Size (VARIANT) | `base` |
| Uppercase (VARIANT) | `False` |
| Show Marker (BOOLEAN) | `false` |
| Show Leading Icon (BOOLEAN) | `false` |
| Show Trailing Icon (BOOLEAN) | `false` |
| Label (TEXT) | `Hello world!` |

---

### Tag
**Key:** `e77c7f567a343c7597108ba3dee62ab0e56e3717`  
**Variants:** 12

| Property | Type | Options | Default |
|---|---|---|---|
| State | VARIANT | `Default` \| `Hover` \| `Selected` \| `Disabled` | `Default` |
| Size | VARIANT | `base` \| `sm` \| `xs` | `xs` |
| Dismissable | BOOLEAN | — | `true` |
| Show Count | BOOLEAN | — | `false` |
| Show Icon | BOOLEAN | — | `false` |
| Show Marker | BOOLEAN | — | `false` |
| Label | TEXT | — | `{label}` |
| Icon | INSTANCE_SWAP | — | — |

---

### Table Cell
**Key:** `735e9bce9462b37d45a2b7c55055cdd2c7b8cf62`  
**Variants:** 24

| Property | Type | Options | Default |
|---|---|---|---|
| Size | VARIANT | `sm` \| `base` | `base` |
| Stripe | VARIANT | `True` \| `False` | `False` |
| Selected | VARIANT | `False` \| `True` | `False` |
| Alignment | VARIANT | `Left` \| `Right` | `Left` |
| Indent | VARIANT | `None` \| `Indented left` | `None` |
| Show Value | BOOLEAN | — | `true` |
| Show Value Label | BOOLEAN | — | `true` |
| Show Value Description | BOOLEAN | — | `true` |
| Show Badge | BOOLEAN | — | `false` |
| Show Checkbox | BOOLEAN | — | `false` |
| Show Button | BOOLEAN | — | `false` |
| Show Menu | BOOLEAN | — | `false` |
| Show Lead Icon | BOOLEAN | — | `false` |
| Show Trailing Icon | BOOLEAN | — | `false` |
| Show Progress Bar | BOOLEAN | — | `false` |
| Show Avatar | BOOLEAN | — | `false` |
| Show Avatar Group | BOOLEAN | — | `false` |
| Show Border | BOOLEAN | — | `true` |

---

### Table Cell Header
**Key:** `d496aa8d177f7d2f36de4bcb34e9773de4ca13d4`  
**Variants:** 16

| Property | Type | Options | Default |
|---|---|---|---|
| Stripe | VARIANT | `False` \| `True` | `False` |
| Sorting | VARIANT | `Off` \| `Unsorted` \| `Ascending` \| `Descending` | `Off` |
| Alignment | VARIANT | `Left` \| `Right` | `Left` |
| Border | VARIANT | `True` | `True` |
| Label | TEXT | — | `{header cell}` |
| Show Value | BOOLEAN | — | `true` |
| Show Checkbox | BOOLEAN | — | `false` |
| Show Border | BOOLEAN | — | `true` |
| Show Tooltip | BOOLEAN | — | `false` |
| Info Marker | BOOLEAN | — | `false` |

---

### Pagination
**Key:** `543c567fe9f56d6b0d3841e0986eda183fd3f3ad`  
**Variants:** 2

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `Default` \| `Simple` | `Default` |

---

### Pagination Item
**Key:** `66567542fc591391eadd351b45046b045440a404`  
**Variants:** 5

| Property | Type | Options | Default |
|---|---|---|---|
| State | VARIANT | `Default` \| `Active` \| `Disabled` \| `Hover` \| `Separator` | `Default` |

---

## Navigation Aids

### Breadcrumbs
**Key:** `6d51343489ac10afcc05beca825bed2c4e39d6b9`  
**Variants:** 20

| Property | Type | Options | Default |
|---|---|---|---|
| Links | VARIANT | `1` \| `2` \| `3` \| `4` \| `5` | `1` |
| Size | VARIANT | `sm` \| `base` \| `lg` \| `xl` | `sm` |
| Show Home Icon | BOOLEAN | — | `true` |

---

### Breadcrumb Link
**Key:** `684bbb7d7d2980e5ed0bf734bd60421aa50f1d26`  
**Variants:** 12

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `Home` \| `Previous` \| `Active` | `Home` |
| Size | VARIANT | `sm` \| `base` \| `lg` \| `xl` | `sm` |

---

### StyledTab
**Key:** `d034c056dfc58fcb5d8d075a49670f46cf3a7c5c`  
**Variants:** 12

| Property | Type | Options | Default |
|---|---|---|---|
| Orientation | VARIANT | `Horizontal` \| `Vertical` | `Horizontal` |
| Size | VARIANT | `base` \| `l` | `l` |
| State | VARIANT | `Default` \| `Selected` \| `Hover` | `Default` |
| Lead Icon | BOOLEAN | — | `false` |
| Dropdown | BOOLEAN | — | `false` |
| Show badge | BOOLEAN | — | `false` |
| Show Beta Badge | BOOLEAN | — | `false` |
| Label | TEXT | — | `{Label}` |
| Icon | INSTANCE_SWAP | — | — |

---

### Accordion Expander
**Key:** `c248b4d1a2433d161ea63be97a88713d53fc3ec0`  
**Variants:** 2

| Property | Type | Options | Default |
|---|---|---|---|
| Expanded | VARIANT | `False` \| `True` | `False` |
| Outlined | VARIANT | `False` | `False` |
| Title | TEXT | — | `{Ah! Look. I'm an open accordion.}` |
| Description | TEXT | — | `How do? I'm sooooo open it's crazy.` |
| Show Description | BOOLEAN | — | `true` |

---

### Accordion Header
**Key:** `5febfc467564ce9dc0615357f14a043036e72d6d`  
**Variants:** 8

| Property | Type | Options | Default |
|---|---|---|---|
| Expanded | VARIANT | `True` \| `False` | `False` |
| Background | VARIANT | `True` \| `False` | `True` |
| Chevron | VARIANT | `Left` \| `Right` | `Left` |
| Title | TEXT | — | `Accordion title` |
| Description | TEXT | — | `Description of this section` |
| Show Description | BOOLEAN | — | `false` |
| Show Badge | BOOLEAN | — | `false` |
| Show Counter | BOOLEAN | — | `false` |
| Show Menu | BOOLEAN | — | `false` |
| Show Info Icon | BOOLEAN | — | `false` |

---

## Identity / Media

### Avatar
**Key:** `a0504d0baf999d75091404079124a48181da918a`  
**Variants:** 30  
**Usage:** Always use this component for user avatars — never create custom avatar shapes.

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `Placeholder` \| `Image` \| `No image` | `Placeholder` |
| Size | VARIANT | `xs` \| `sm` \| `base` \| `lg` \| `xl` \| `xxl` | `xxl` |
| Shape | VARIANT | `Rounded` \| `Square` | `Rounded` |
| Border | BOOLEAN | — | `true` |
| Presence | BOOLEAN | — | `true` |
| Icon | INSTANCE_SWAP | — | — |
| Presence Icon | INSTANCE_SWAP | — | — |
