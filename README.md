# 🎨 Modern SaaS Design System

A high-premium, responsive, state-of-the-art Design System built with **Vue 3**, **Vite**, and **Tailwind CSS v4**. It features an elegant custom typographic configuration, semantic color tokens, and a fully interactive **Light, Dark, and System theme synchronization layer**.

---

## ✨ Features

- **🌓 Auto-Sensing Theme Engine**: Supports explicit `Light` and `Dark` configurations as well as automatic `System` matching with live `matchMedia` listeners to handle OS-level appearance updates on the fly.
- **💾 LocalStorage Persistence**: Instantly remembers user preferences across sessions to avoid theme flashes during page reloads.
- **📱 Fully Responsive Layout**: Built with a mobile-first philosophy, utilizing a responsive grid dashboard, interactive layout shifts, and a sliding drawer menu for viewports under `768px`.
- **🛠️ Reusable CSS Component Suite**: Custom class definitions for core SaaS building blocks including buttons, dialogs, empty states, input forms, tables, breadcrumbs, and status badges.
- **✒️ Premium Typography**: Custom typography system configured around Google Fonts' **Inter** sans-serif font face.

---

## 📁 Directory Structure

```bash
├── index.html                  # Main HTML Entrypoint (loads fonts and sets initial theme)
├── src/
│   ├── main.js                 # App mounting and CSS bootstrapping
│   ├── App.vue                 # Fully interactive dashboard and theme controller
│   └── css/
│       ├── app.css             # Main stylesheet loading Tailwind & layouts/components
│       ├── theme/
│       │   ├── colors.css      # Core HSL color variables (Light & Dark definitions)
│       │   └── typography.css  # Typography configurations & Inter setup
│       ├── layouts/
│       │   ├── app-shell.css   # Main app shell structures (Sidebar + Header + Main Area)
│       │   ├── sidebar-layout.css
│       │   └── split-layout.css
│       └── components/
│           ├── badge.css       # Status badges (Success, Warning, Danger)
│           ├── button.css      # Primary, Secondary, Outline, Danger variants
│           ├── card.css        # Content cards, headers, footers
│           ├── dialog.css      # Interactive overlays and modals
│           ├── empty-state.css # Fallback states when data is absent
│           ├── form.css        # Labels, inputs, select fields, and checkboxes
│           ├── navbar.css      # Header navigation bar
│           └── table.css       # Data tables with zebra striping and hover effects
```

---

## 🚀 Getting Started

### 1. Installation
Install the project dependencies using npm:
```bash
npm install
```

### 2. Development Server
Launch the local development server:
```bash
npm run dev
```
Open the printed URL (typically `http://localhost:5173/` or `http://localhost:5174/`) in your browser to view the design system showcase.

### 3. Production Build
Compile and optimize the design system for production deployment:
```bash
npm run build
```

---

## 🎨 Using Theme Tokens & Tailwind CSS

The design system binds core colors to CSS Custom Properties, exposing them directly to Tailwind v4's theme utility layer.

### 1. Color Customization
Tailwind utility mapping is declared in `src/css/app.css`:
```css
@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-primary: var(--primary);
  --color-secondary: var(--secondary);
  --color-border: var(--border);
  --color-ring: var(--ring);
}
```

This allows using Tailwind v4 color utilities in your HTML/Vue files:
```html
<!-- Automatically switches colors in Light / Dark mode based on classes -->
<div class="bg-background text-foreground border-border">
  <button class="bg-primary text-primary-foreground">Save Changes</button>
</div>
```

### 2. Custom Dark Mode Variant
Dark mode selection applies the `.dark` class to the root `<html>` element. Custom variant checking is registered using Tailwind v4's `@custom-variant`:
```css
@custom-variant dark (&:is(.dark *));
```

This maps styles to children when nested within `.dark`, enabling custom utility switches:
```html
<p class="text-slate-600 dark:text-slate-300">
  This text switches colors automatically.
</p>
```

---

## ⚡ Component Reference

The CSS rules are fully modular. Below is an overview of the key custom classes:

- **`.app-shell`**: Sets up the dashboard container containing sidebar and content area.
- **`.sidebar` & `.app-shell-sidebar`**: Handles the sidebar container, complete with slide-in states for mobile drawer.
- **`.button`**: Implements standardized hover transitions. Styles are divided into `.button-primary`, `.button-secondary`, `.button-outline`, `.button-danger`, and `.button-link`.
- **`.card`**: Wraps content elements, featuring `.card-header`, `.card-title`, `.card-description`, `.card-content`, and `.card-footer`.
- **`.badge`**: Displays dynamic inline statuses. Available classes: `.badge` (Neutral/Gray), `.badge-success` (Green), `.badge-warning` (Amber), `.badge-danger` (Red).
- **`.dialog-overlay` & `.dialog`**: Creates fixed screens and centered modals for interactive warnings/confirmations.
