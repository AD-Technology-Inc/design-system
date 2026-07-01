# @ad-technology-inc/design-system

A premium, state-of-the-art CSS-first Design System built for modern SaaS applications. Compatible with vanilla HTML/CSS and fully integrated with Tailwind CSS v4.

---

## 🚀 Installation

Install the package via your preferred package manager:

```bash
npm install @ad-technology-inc/design-system
# or
yarn add @ad-technology-inc/design-system
# or
pnpm add @ad-technology-inc/design-system
```

---

## 🛠️ Usage

### 1. Vanilla CSS Integration
Import the clean CSS bundle into your main stylesheet:

```css
@import "@ad-technology-inc/design-system";
```

### 2. Tailwind CSS v4 Integration
If your project is built with Tailwind CSS v4, import Tailwind and then load our theme wrapper stylesheet:

```css
@import "tailwindcss";

/* Imports all components and links theme variables to Tailwind v4 */
@import "@ad-technology-inc/design-system/app.css";
```

#### Configuring Dark Mode:
By default, Tailwind CSS v4 handles dark mode using media queries. If your project uses class-based dark mode (e.g. toggling `.dark` class on the `<html>` element), define the custom variant in your stylesheet:

```css
@import "tailwindcss";
@custom-variant dark (&:is(.dark *)); /* Only add if using class-based dark mode */

@import "@ad-technology-inc/design-system/app.css";
```

#### Disabling Dark Mode (Light Mode Only):
If you want to disable dark mode entirely and force your website to stay in light mode (even if the user's OS prefers dark mode), simply add the `light` class to your root `<html>` element:

```html
<html class="light">
```
This disables the built-in prefers-color-scheme media query and keeps the light-mode variables active.

---

## 🎨 Theme Variables & Customization

The design system exposes core variables as CSS Custom Properties, making them easily customizable at runtime. Simply override them in your project's `:root` selector:

```css
:root {
  --primary: hsl(220 100% 50%);  /* Custom primary brand color */
  --radius: 0.5rem;             /* Custom border radius */
}

.dark {
  --primary: hsl(200 100% 60%);  /* Custom dark mode primary */
}
```

### Core Semantic Tokens Available:
* `--background` / `--foreground`: Base canvas & text
* `--card`: Cards & panel surfaces
* `--primary` / `--primary-hover` / `--primary-foreground`: Brand actions
* `--secondary` / `--secondary-hover` / `--secondary-foreground`: Secondary actions
* `--accent` / `--accent-hover` / `--accent-foreground`: Interactive item highlights
* `--neutral` / `--neutral-hover` / `--neutral-foreground`: Borders, helper text, and striped table backgrounds
* `--border`: Structural container borders
* `--success` / `--warning` / `--info` / `--danger`: Semantic indicators

---

## ⚡ Component & Form reference

The package includes highly optimized, pre-styled custom classes:

- **`.button`**: Standardized, transition-ready buttons. Variants: `.button-primary`, `.button-secondary`, `.button-outline`, `.button-danger`, and `.button-link`.
- **`.card`**: Modern panel container (`.card-header`, `.card-title`, `.card-description`, `.card-content`, `.card-footer`).
- **`.form-group`**: Form elements:
  * `.form-input` / `.form-select` / `.form-textarea`: Form control fields
  * `.form-file`: Styled file upload buttons
  * `.form-range`: Styled sliders with hover scale effects
  * `.form-checkbox` / `.form-radio`: Fully styled checkbox/radio components replacing native browser styles
- **`.table`**: Zebra-striped data tables (`.table-striped`, `.table-hover` with contrast correction for striped rows).
- **`.badge`**: Status labels (`.badge-success`, `.badge-warning`, `.badge-danger`).
- **`.alert`**: Alert containers (`.alert-success`, `.alert-warning`, `.alert-danger`).

---

## 🔧 Local Development & Sandbox (Vue Playground)

If you are contributing to this design system project:

1. Clone the repository and install local dev dependencies:
   ```bash
   npm install
   ```
2. Start the Vue sandbox to preview components:
   ```bash
   npm run dev
   ```
3. Compile the production bundles:
   ```bash
   npm run build
   ```
