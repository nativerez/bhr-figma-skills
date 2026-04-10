# BrightHR Lumen — Navigation & Actions

> Part of the Lumen component reference. See [brighthr-lumen-index.md](brighthr-lumen-index.md) for the full key inventory.

---

## Navigation Components

### BrightHR Navigation (Side Nav)
**Key:** `a5e9bbfbcc0756e5fe5474fd5fbdf68b8919c39a`  
**Variants:** 2  
**Usage:** Required left sidebar on all BrightHR desktop and tablet screens.

| Property | Type | Options | Default |
|---|---|---|---|
| Sub menu | VARIANT | `Off` \| `On` | `Off` |

```js
const nav = await figma.importComponentSetByKeyAsync("a5e9bbfbcc0756e5fe5474fd5fbdf68b8919c39a");
const inst = nav.defaultVariant.createInstance();
inst.setProperties({ "Sub menu": "Off" }); // "On" when sub-nav is expanded
```

---

### BrightHR Lite Navigation
**Key:** `c63f94a7b79e460202bc85681a634fbac7e9e198`  
**Variants:** 2  
**Usage:** Use for BrightHR Lite product screens.

| Property | Type | Options | Default |
|---|---|---|---|
| Sub menu | VARIANT | `Off` \| `On` | `Off` |

---

### Global product headers
**Key:** `cfc3e56a801d75f708d2b1abe11954eee3879701`  
**Variants:** 12  
**Usage:** Required top header. First child of the `Main` frame on every screen. Match `Size` to the current breakpoint.

| Property | Type | Options | Default |
|---|---|---|---|
| Product | VARIANT | `BrightHR` \| `HR Lite` \| `BrightSafe` \| `BrightSafe Lite` | `BrightHR` |
| Size | VARIANT | `Desktop` \| `Tablet` \| `Mobile` | `Desktop` |

**Breakpoint → Size:**
- 1440px → `Desktop`
- 1024px → `Tablet`
- 390px → `Mobile`

```js
const hdrSet = await figma.importComponentSetByKeyAsync("cfc3e56a801d75f708d2b1abe11954eee3879701");
const hdr = hdrSet.children.find(c =>
  c.name.includes("Product=BrightHR") && c.name.includes("Size=Desktop")
);
main.appendChild(hdr.createInstance());
```

---

### BrightHR Sub menus
**Key:** `70159def45527b992ea193ee6dcbdff3eb07583d`  
**Variants:** 5

| Property | Type | Options | Default |
|---|---|---|---|
| Sub menu | VARIANT | `My profile` \| `Documents` \| `Navigators` \| `Notifications` \| `Quick actions` | `My profile` |

---

## Button Components

### Button
**Key:** `beec2f9c0eb16e7ce0096c6b233f1a409c8deb80`  
**Variants:** 300

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `primary` \| `secondary` \| `tertiary` \| `destructive` \| `inline` | `primary` |
| State | VARIANT | `Default` \| `Hover` \| `Disabled` \| `Focused` \| `Loading` | `Default` |
| icon | VARIANT | `none` \| `left` \| `right` | `none` |
| size | VARIANT | `sm` \| `base` \| `l` \| `xl` | `sm` |
| Text | TEXT | — | `Click me!` |
| icon instance | INSTANCE_SWAP | — | — |

```js
const btnSet = await figma.importComponentSetByKeyAsync("beec2f9c0eb16e7ce0096c6b233f1a409c8deb80");
const btn = btnSet.children.find(c =>
  c.name.includes("Type=primary") && c.name.includes("State=Default") &&
  c.name.includes("icon=none") && c.name.includes("size=base")
);
const inst = btn.createInstance();
inst.setProperties({ "Text#22641:241": "Save Changes" }); // verify suffix via inst.componentProperties
```

> ⚠️ **Text property key:** Live key is `Text#22641:241`. Always verify via `inst.componentProperties`.

**Common variant names:**
- `Type=primary, State=Default, icon=none, size=sm`
- `Type=primary, State=Default, icon=none, size=base`
- `Type=secondary, State=Default, icon=none, size=sm`
- `Type=secondary, State=Default, icon=none, size=base`
- `Type=destructive, State=Default, icon=none, size=base`
- `Type=tertiary, State=Default, icon=none, size=base`

---

### ButtonLink
**Key:** `3fb7d95e7c48c002f7c7053ecca2ce571f4bb4fe`  
**Variants:** 24

| Property | Type | Options | Default |
|---|---|---|---|
| Size | VARIANT | `xl` \| `l` \| `base` \| `sm` \| `xs` | `xs` |
| State | VARIANT | `Default` \| `Hover` \| `Disabled` | `Default` |
| Type | VARIANT | `Blue` \| `White` | `Blue` |
| Icon | VARIANT | `False` | `False` |
| Lead | BOOLEAN | — | `false` |
| Trailing | BOOLEAN | — | `true` |
| Text | TEXT | — | `Click me!` |

---

### Icon button
**Key:** `a3dea3986bfc3303df1e3b88c605824ce9b30134`  
**Variants:** 16

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `action` \| `destructive` | `action` |
| size | VARIANT | `base` \| `sm` | `base` |
| state | VARIANT | `default` \| `hover` \| `focused` \| `loading` | `default` |
| icon | INSTANCE_SWAP | — | — |

---

### Split Button
**Key:** `4dbc73f93d4bb458bd488fdc04f28f167d27ffa0`  
**Variants:** 21  
**Usage:** Use when space is limited and several secondary actions exist.

| Property | Type | Options | Default |
|---|---|---|---|
| Type | VARIANT | `Primary` \| `Outline` | `Primary` |
| Size | VARIANT | `xs` \| `base` \| `lg` | `lg` |
| Menu | VARIANT | `False` \| `True` | `False` |
| State Secondary | VARIANT | `Default` \| `Hover` \| `Focus` \| `Disabled` | `Default` |
| Label | TEXT | — | `Primary action` |
| Show Icon | BOOLEAN | — | `false` |
