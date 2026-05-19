# BrightHR Lumen — Component Audit Report

> File: [Components: Lumen (Bright-UI)](https://www.figma.com/design/3dNASwqNXrYbGQyHsf1Sgg) · File Key: `3dNASwqNXrYbGQyHsf1Sgg`
> Audited: 19 May 2026 · 128 component sets found

---

## Summary

| Issue | Count |
|---|---|
| Missing component descriptions | 101 / 128 |
| Unnamed default properties (`Property 1`) | 7 components |
| Numbered INSTANCE_SWAP slots (Modal) | 10 slots |
| Inconsistent prop casing | 4 components |
| Duplicate / conflicting props | 3 components |
| Typos in component or prop names | 5 |

---

## 1. Missing Descriptions

101 of ~120 real component sets have no description. The following **do** have one:

- Alert
- BrightHR Sub menus, BrightHR Lite Sub menus, BrightSafe Sub menus, BrightSafe Lite Sub menus
- BrightSafe Navigation
- Everything button (FAB)
- Quick Actions Menu
- Badge
- Button
- Split Button
- Context Menu, Context Menu Option State
- Select Option
- Tooltip *(but not Tooltip - popout)*

**All other components are missing descriptions**, including high-traffic ones:

`Avatar`, `Icon button`, `ButtonLink`, `Character Count`, `Checkbox`, `Checkbox Label`, `Checkbox Block`, `Date Picker`, `Date Range Picker`, `Duration Picker`, `Employee Picker Card`, `Empty states Illustrations`, `File Upload Snippet`, `Input Field`, `Label`, `Input Search`, `Error Description`, `Input Field Text Area`, `Definition`, `Beta Badge`, `Beta Flask`, `Modal`, `Modal Header`, `Modal Footer`, `Note Item`, `Note Area`, `Pagination`, `Progress Bar Rail`, `Radio`, `Radio Block`, `Select Field`, `Form Select Field`, `Segmented control`, `Loader Spinner`, `Switch`, `Table Cell`, `Table Cell Header`, `Table Layout`, `StyledTab`, `Tag`, `Time Picker`, `Toast`, `Tooltip - popout`, `Typeahead Focus States`, `Typeahead Select State`, `User Card`, `Wizard`, `Wizard Step`, `Breadcrumb Link`, `Breadcrumbs`, `Donut`, `Accordion Header`, `Accordion Expander`

---

## 2. Unnamed Default Properties (`Property 1`)

These components have a Figma-generated default property name that was never renamed:

| Component | Page |
|---|---|
| `Character Count` | Character Count |
| `Facebook` | Social Icons |
| `Tooltip - popout` | Tooltips |
| `User Card` | User Card |
| `Profile Image Button` | Profile |
| `Shift-context` | Rota statuses |
| `Rota Tools Menu` | Rota statuses |

---

## 3. Modal — Numbered Slot Anti-Pattern

`Modal` has 10 numbered INSTANCE_SWAP body slots (`Modal/Body`, `Modal/Body2` … `Modal/Body10`). This forces consumers to know in advance how many body sections they need and pick the correct numbered slot. A single composable body slot is the correct pattern.

| Props to fix |
|---|
| `Modal/Body`, `Modal/Body2`, `Modal/Body3`, `Modal/Body4`, `Modal/Body5` |
| `Modal/Body6`, `Modal/Body7`, `Modal/Body8`, `Modal/Body9`, `Modal/Body10` |

---

## 4. Inconsistent Prop Casing

Props within the same component mix Title Case and lowercase:

| Component | Lowercase prop(s) | Title Case props in same component |
|---|---|---|
| `BrightSafe Navigation` | `sub menu` | — (equivalent on other nav sets: `Sub menu`) |
| `Button` | `icon instance`, `icon`, `size` | `Text`, `Type`, `State` |
| `Icon button` | `icon`, `size`, `state` | `Type` |
| `Checkbox Label` | `Show icon` | `Show Avatar`, `Show Description` |

---

## 5. Duplicate and Conflicting Props

| Component | Props | Issue |
|---|---|---|
| `Employee Picker Card` | `Selected` × 2 | Exact duplicate key |
| `Employee Picker Card` | `Disabled` + `Disable` | Two props for the same concept — `Disable` is a typo |
| `ButtonLink` | `Lead` + `Leading Icon`, `Trailing` + `Trailing Icon` | Four props for two concepts — redundant pairs |
| `Button` | `icon instance` + `icon` | Two icon-related props — purpose of each is unclear |

---

## 6. Typos and Naming Issues

### In prop names

| Component | Current | Should be |
|---|---|---|
| `Nav item` | `Seperator` | `Separator` |

### In component names

| Component | Current | Should be |
|---|---|---|
| `Accordian Arrow` | `Accordian` | `Accordion` |
| `Segmented control` | `Segmented control` | `Segmented Control` (Title Case) |
| `StyledTab` (Tabs page) | `StyledTab` | `Tab` (internal name leaking out) |
| `Empty states llustrations` | `llustrations` | `Illustrations` (missing capital I — both variants) |

### Internal prop leaking to consumers

| Component | Prop | Issue |
|---|---|---|
| `Note Area` | `Show Kitchen Sink` | Debug/internal prop exposed to consumers |

---

## 7. Component Names Not In Index

These component sets exist in the file but are not documented in [brighthr-lumen-index.md](brighthr-lumen-index.md):

| Component | Key | Page |
|---|---|---|
| `Beta Badge` | `a37a36be65326c5e8b280335a96df1039652b990` | Labs Beta Badge |
| `Beta Flask` | `8ded64551f0a331bdb6cadd134c42c243f1d2fcf` | Labs Beta Badge |
| `User Card` | `501a551cfae7b0e8d80e4fba42a4d45f4a1efa4b` | User Card *(only footer is indexed)* |
| `Wizard` | `f8f3e687ea981cb819fe71e7af94b57b186a5700` | Wizard |
| `Wizard Step` | `9fa29eb1b217cafd02390f2f92cce0fe5327c305` | Wizard |
| `Bright Product` | `7d56ded576600d992d63fa5443e85242fa4f936b` | Bright Product List |
| `Stepper` | `a27f79972b7be865c9f0c8f02b60b196dbfa5783` | WIP: Stepper |
| `Numerical Spinner` | `e04a99a4223383d77484217f9e99ef86b60e5112` | WIP: Stepper |
| `Stepper Dots` | `8e1e41a4900853b4b437ff9527668343c70806af` | WIP: Vertical Stepper |
| `Facebook` | `d09b76877e68c6e1132d955fbc895dbd225163a3` | Social Icons |
| `Profile Header` | `99c071dc0df2696710359b8b752277cf43d8a024` | Profile |
| `Profile Image Button` | `b591a7bf43887c76a3919df06939e6b1ec5a51a5` | Profile |
| `Status` | `55cca90f42515c6b91da9b84ae4c316b800c8c87` | Profile |
| `Profile Image` | `0cedebba9d21349f2cc6b79a592cb42dbbad79b3` | Profile |
| `Shift-context` | `8ad983fceb1ce52ed2528522996afe2ed5ccad9c` | Rota statuses |
| `Rota Tools Menu` | `d07a0ce246f8c0883932d2362e858756c84f2b9d` | Rota statuses |
| `Rota Extras Menu` | `f5122dd8bef4569c14951bf54326b8b6b1eeaf98` | Rota statuses |
| `Folder` | `409afb3fd254ebb0457cba152badd745e15fe1a9` | Files |
| `File` | `1077e553c7f96d7341178d992e92f2b270319bcb` | Files |

### In index but not found in file

| Component | Key |
|---|---|
| `Radio Block Illustration` | `f965f92c0fff7a292fcc6627fef5affc8e4f8c9e` |
