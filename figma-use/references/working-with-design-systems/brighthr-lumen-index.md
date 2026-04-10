# BrightHR Lumen — Index & Quick Reference

> Source: [Components: Lumen (Bright-UI)](https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg) · File Key: `3dNASwqNXrYbGQyHsf1Sgg`

**Start here.** This file tells you which component to use and where to find its props. Load the relevant detail file only when you need to check specific variant options or usage guidance.

---

## Detail Files

| File | Contents |
|---|---|
| [brighthr-lumen-nav-actions.md](brighthr-lumen-nav-actions.md) | Navigation shell (Side Nav, Global Header, Sub menus) + all Button types |
| [brighthr-lumen-forms.md](brighthr-lumen-forms.md) | Input Field, Search, TextArea, Select, Checkbox, Radio, Switch, Segmented |
| [brighthr-lumen-feedback-overlays.md](brighthr-lumen-feedback-overlays.md) | Alert, Toast, Spinner, Progress Bar, Modal, Tooltip, Context Menu |
| [brighthr-lumen-data-display.md](brighthr-lumen-data-display.md) | Badge, Tag, Table, Pagination, Breadcrumb, Tabs, Accordion, Avatar |
| [brighthr-lumen-tokens.md](brighthr-lumen-tokens.md) | Color tokens, spacing, border radius, variable binding code |

---

## How to Import Components

```js
// Always use importComponentSetByKeyAsync — never hardcode component node IDs
const set = await figma.importComponentSetByKeyAsync("COMPONENT_SET_KEY");

// Find a specific variant by matching name segments
const variant = set.children.find(c =>
  c.name.includes("Type=primary") && c.name.includes("size=base")
);
const instance = variant.createInstance();

// Override text — suffix (#N:N) MUST match the live key in componentProperties
instance.setProperties({ "Text#22641:241": "Save Changes" });
parent.appendChild(instance);
```

> **Property key suffixes:** Keys like `Text#22641:241` are internal. This reference strips suffixes for readability. Always verify live keys via `instance.componentProperties` before setting.

---

## Quick Import Block (Most Used)

```js
// Navigation shell — required on every screen
const navSet    = await figma.importComponentSetByKeyAsync("a5e9bbfbcc0756e5fe5474fd5fbdf68b8919c39a"); // BrightHR Navigation
const hdrSet    = await figma.importComponentSetByKeyAsync("cfc3e56a801d75f708d2b1abe11954eee3879701"); // Global product headers

// Buttons
const btnSet    = await figma.importComponentSetByKeyAsync("beec2f9c0eb16e7ce0096c6b233f1a409c8deb80"); // Button

// Form controls
const inputSet  = await figma.importComponentSetByKeyAsync("0fad07e208acfaa23f3f834352810b57e4868a6f"); // Input Field
const srchSet   = await figma.importComponentSetByKeyAsync("35f08b7a52a9a5efa6383e113f8dbb5c0cc9bc4a"); // Input Search
const selSet    = await figma.importComponentSetByKeyAsync("cc100ea329775132cbc5733225256c0167a903bd"); // Select Field
const swSet     = await figma.importComponentSetByKeyAsync("e18619dd7fda5f378c06998553afe2f9efcf2174"); // Switch
const radSet    = await figma.importComponentSetByKeyAsync("e6d1b00eeb058fcdb02fd76e71d07f12ebf64c09"); // Radio
const chkSet    = await figma.importComponentSetByKeyAsync("a6f5ae452ca4b7f689555b28ea39c113e9a10a39"); // Checkbox

// Feedback & data
const badgeSet  = await figma.importComponentSetByKeyAsync("646d8937657ce4d86e7cdffad980def6fc2447be"); // Badge
const alertSet  = await figma.importComponentSetByKeyAsync("967eb0bb2625d93c53896303b5dab12616b09b03"); // Alert
const toastSet  = await figma.importComponentSetByKeyAsync("9fad757bd940c48ab458fcea2c0ef6ca27917890"); // Toast
const tblCell   = await figma.importComponentSetByKeyAsync("735e9bce9462b37d45a2b7c55055cdd2c7b8cf62"); // Table Cell
const tblHdr    = await figma.importComponentSetByKeyAsync("d496aa8d177f7d2f36de4bcb34e9773de4ca13d4"); // Table Cell Header
const modalSet  = await figma.importComponentSetByKeyAsync("d5fce75be566ff374a864c7421dd2c2b00bf10f9"); // Modal
const avatarSet = await figma.importComponentSetByKeyAsync("a0504d0baf999d75091404079124a48181da918a"); // Avatar
```

---

## Full Component Key Inventory (113 component sets)

### Navigation / App Shell
| Component | Key |
|---|---|
| BrightHR Navigation | `a5e9bbfbcc0756e5fe5474fd5fbdf68b8919c39a` |
| BrightHR Lite Navigation | `c63f94a7b79e460202bc85681a634fbac7e9e198` |
| BrightSafe Navigation | `33d53e012b3a0bea32b6a487945b2a737c470aa6` |
| BrightSafe Lite Navigation | `d2943e41fe88e52a573124e7a2f1121208f068a4` |
| Global product headers | `cfc3e56a801d75f708d2b1abe11954eee3879701` |
| BrightHR Sub menus | `70159def45527b992ea193ee6dcbdff3eb07583d` |
| BrightHR Lite Sub menus | `925f6ed504e982c8842495cb139a32a3285c2bc3` |
| BrightSafe Sub menus | `d8e4cd55e4fcf3d35adbadc3b9241969b0d1a822` |
| BrightSafe Lite Sub menus | `0fabe7d59b12c85e50634d3ca4ea3196cbd32d40` |
| Nav item | `8cf830f31b15c96d5e827477bf40a81e984eba8a` |
| Actions item | `7c0bfc622003e508449e1e1f90641c2454b2d569` |
| Avatar profile | `ba1382478774e02da6af46be623c18799b2fe9fb` |
| Everything button (FAB) | `ca2e83e79f199dc7973458dbf1b521db25e1a8fa` |
| Account Switcher | `f000425eaed60deb79e0b26b49b280d7ac6cc78b` |
| Global Navigation Item | `241342b4ccceaaf163bd3c73c535baea6fa5ed35` |
| Quick Actions Menu | `c1149287ff351a7b088239e22f2829b42207f777` |

### Buttons
| Component | Key |
|---|---|
| Button | `beec2f9c0eb16e7ce0096c6b233f1a409c8deb80` |
| ButtonLink | `3fb7d95e7c48c002f7c7053ecca2ce571f4bb4fe` |
| Icon button | `a3dea3986bfc3303df1e3b88c605824ce9b30134` |
| Split Button | `4dbc73f93d4bb458bd488fdc04f28f167d27ffa0` |

### Form Inputs
| Component | Key |
|---|---|
| Input Field | `0fad07e208acfaa23f3f834352810b57e4868a6f` |
| Input Search | `35f08b7a52a9a5efa6383e113f8dbb5c0cc9bc4a` |
| Input Field Text Area | `04a9737a0ab2cdaf232bbcf6ebec459b60c97e9b` |
| Auth Input Field | `35c5afb6c23e85868df7ee26f5ef2e6e9ef5e7f6` |
| Label | `0f4401880eda6296e6416b1e9f74f6b88e5b2387` |
| Definition | `3db2a479a79388f1690353ac83c6ce413f69dd45` |
| Error Description | `7fd1a4fe6f0a6270227156306ad65693875a57e7` |
| Character Count | `fc6dc5d7c0cd7e19c6bbc40dcef99d423030fd6d` |
| Select Field | `cc100ea329775132cbc5733225256c0167a903bd` |
| Select Option | `918c4a003d49da4f4d00a68d9f4703c9bcbff0d8` |
| Select Field Menu | `9d80ef23970995f256e1c00fa35a72526fd6107f` |
| Form Select Field | `e8c3e517d2c42caa3cfea0da8c9a69d1cac3e781` |
| Select Field Bright Menu Options | `7f1bec7833acadf4d22607f45e4c71064b9ad331` |
| Select Field Mac Menu Options | `da8f98541abeedb1f8cfb4d6d0d1fdcb3d449454` |
| Checkbox | `a6f5ae452ca4b7f689555b28ea39c113e9a10a39` |
| Checkbox Label | `91eae98b1ac22b5019b99276d0a0361d9477140c` |
| Checkbox Block | `c3e24d0703efaeeb55017dee704ab2bf6cfc1785` |
| Radio | `e6d1b00eeb058fcdb02fd76e71d07f12ebf64c09` |
| Radio Block | `e9d758f5b63e66813c4ec1b8e3793b9cf0cfe0a6` |
| Radio Block Illustration | `f965f92c0fff7a292fcc6627fef5affc8e4f8c9e` |
| Switch | `e18619dd7fda5f378c06998553afe2f9efcf2174` |
| Segmented control | `7803214fffecfa176b9f5f942346335b1e088345` |

### Feedback
| Component | Key |
|---|---|
| Alert | `967eb0bb2625d93c53896303b5dab12616b09b03` |
| Toast | `9fad757bd940c48ab458fcea2c0ef6ca27917890` |
| Loader Spinner | `d67f390b49be39dba013d6c7759e8c3a7c2f70a0` |
| Progress Bar Rail | `1cc088e5ffb96e4bc47d89f865323a0cf4246ee8` |

### Overlays
| Component | Key |
|---|---|
| Modal | `d5fce75be566ff374a864c7421dd2c2b00bf10f9` |
| Modal Header | `82bac7922ed9d67ca770206c21f022d7d22d72ca` |
| Modal Footer | `0fc22d1d5e8a55496c47b9dc2e43eaaf64c318c6` |
| Modal Title | `9ea78e7f52ba85470c3f05cf61a3e161faa7319f` |
| Close | `c4f08ab0898f15954020171ce7712eb168640799` |
| Tooltip | `75bb5966aa02a36e414b709c2e4c7d8430411e76` |
| Tooltip - popout | `c079c74d7c5af760014f90750b9c71d263314b26` |
| Context Menu | `196053c53c8e6f64f9adb1381581bf10b8c58c25` |
| Context Menu Option State | `9809e6d620c069f88744d48b2807682b33c4a7a7` |

### Data Display
| Component | Key |
|---|---|
| Badge | `646d8937657ce4d86e7cdffad980def6fc2447be` |
| Badge Master | `1085e37d9215f8381984235f0258349a565fab3c` |
| Tag | `e77c7f567a343c7597108ba3dee62ab0e56e3717` |
| Table Layout | `d0b32624b31b0b30dcbeabaed5caef351a01406b` |
| Table Cell | `735e9bce9462b37d45a2b7c55055cdd2c7b8cf62` |
| Table Cell Header | `d496aa8d177f7d2f36de4bcb34e9773de4ca13d4` |
| Table Cell Skeleton | `1a6577ebee5a8fd28d0935eb39b4245850492312` |
| Table Cell Sorting | `4d355d28e3bc2d1556d7b0063e5ae93c1239ba19` |
| Accordion row | `960ca9d41e9abf5feb270e0c0e4e28245ea64cf8` |
| Mobile Row | `f5427f0c14d596852eb79ebb1da86b4f7f5349c8` |
| Pagination | `543c567fe9f56d6b0d3841e0986eda183fd3f3ad` |
| Pagination Item | `66567542fc591391eadd351b45046b045440a404` |
| Carousel/Arrows | `e36b85ddf8c546f64d73521f02c914427d48ff63` |
| Avatar | `a0504d0baf999d75091404079124a48181da918a` |
| Donut | `ae2f6d8e082b14d23e4ffcbc66139a3df26f1d72` |
| Empty states illustrations | `238da92b2301df783a49c3c9711ab691996a1c63` |
| Empty states illustrations - Payroll | `283074e1a6c7045b939e5e6f5f3cb28019220aca` |

### Navigation Aids
| Component | Key |
|---|---|
| Breadcrumb Link | `684bbb7d7d2980e5ed0bf734bd60421aa50f1d26` |
| Breadcrumbs | `6d51343489ac10afcc05beca825bed2c4e39d6b9` |
| StyledTab | `d034c056dfc58fcb5d8d075a49670f46cf3a7c5c` |
| Accordian Arrow | `cdc6cf191d14b2d6d1baa10b756a7683725a62bc` |
| Accordion Header | `5febfc467564ce9dc0615357f14a043036e72d6d` |
| Accordion Expander | `c248b4d1a2433d161ea63be97a88713d53fc3ec0` |

### Date / Time
| Component | Key |
|---|---|
| Date Picker | `e30d0b650a3bf411bdd398422928a8a3a45f323b` |
| Date Picker Cell | `d3cd386c26471c07805ac343edb22c1f7c3970a6` |
| Date Picker State | `ce1ecd03dff6ebed1c6d0ac9940217da587c669d` |
| Date Range Picker | `21d93487fd8144a714d5d1933d0e35a42b664a5f` |
| Date Range Picker Tabs | `4140a185dcb988314a254b52b739b5671f90fd9a` |
| Date Range Picker Select | `ee52f7eff26a5e87ca585ba18749ae423847a1d2` |
| Date Range Picker Single Date | `72f5247cd109880eb739753c373e2982908c6729` |
| Time Picker | `7cf64c7da8f1f07ea56d66288382c4b62e68fe83` |
| Time Picker Field | `28f60cae5e2d3cf41b6efaeb4f5b4708e74348d5` |
| Time Picker Panel | `1410052a979261b28e94bcec7ec661f1088038f9` |
| Time Increment | `74fa3ab43b9a3ab48317466d90099392cec49730` |
| Time Increment List | `40f294279223f239f1e08fa0a81ec4419ff6b2fa` |
| Duration Picker | `6bfd97136a1dec6efd6c9c8c6a918519e0316501` |
| Duration Picker Arrows | `1115bdb0e8f7c6e38f3b4d202e07de9f714e2a59` |

### Misc
| Component | Key |
|---|---|
| Note Item | `870b25aba832bf51e2bb8a553bb0282f5beab10d` |
| Note Area | `026df812f0b19d61022de51c9cc08b28fb7735f5` |
| Notes Wrapper | `84131fb52ff42ac3c8ae97729fe556f1a8d06b81` |
| Employee Picker Card | `08455c7eccee8490e9a28c18f729f5ee22e8b17b` |
| File Upload Snippet | `6ef26b5dc56877f40c45136331482dbffaa6eea1` |
| Typeahead Focus States | `e22f7be23dea7ae01e57f0b6f53a211be4c0ada2` |
| Typeahead Select State | `db56a12214dba92f9cbd765b4193cb87c107a999` |
| User Card Footer | `a9c00f1cf50231c3577749c48d9799fd25ba8e57` |

---

## Icons Library

**File:** [Components: Icons v2.0](https://www.figma.com/design/2lYWKHwYwf4iAYJxILH2Wr) · File Key: `2lYWKHwYwf4iAYJxILH2Wr`

Always import icons from this file — never use icons from other libraries. Swap via `INSTANCE_SWAP` props on components:

```js
const iconComp = await figma.importComponentByKeyAsync("ICON_KEY");
instance.setProperties({ "icon instance#22641:0": iconComp.id });
```
