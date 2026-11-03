---
description: Redesign the Vue 3 app from a horizontal top navbar to a modern SaaS vertical sidebar layout
---

# Sidebar Layout Redesign Skill

Transforms the app from a horizontal top navbar to a modern SaaS vertical sidebar layout: dark left rail (260px, `#0f172a`) with logo, nav links with icons, and profile/language controls at the bottom. Content scrolls on the right.

## Target Layout

```
┌──────────────┬──────────────────────────────────────┐
│  SIDEBAR     │  FilterBar (sticky top:0)             │
│  260px wide  ├──────────────────────────────────────┤
│  #0f172a bg  │                                      │
│  sticky      │  <router-view />  (scrollable)       │
│              │                                      │
│  [Logo]      │                                      │
│  [Nav links] │                                      │
│  ...         │                                      │
│  [Language]  │                                      │
│  [Profile]   │                                      │
└──────────────┴──────────────────────────────────────┘
```

---

## Pre-flight: Read These Files First

Before writing any code, read:
- `client/src/App.vue` (full file — template, script, and global CSS)
- `client/src/components/FilterBar.vue` (full file)
- `client/src/components/ProfileMenu.vue` (to understand emits and CSS class names)
- `client/src/components/LanguageSwitcher.vue` (to understand CSS class names for deep overrides)

---

## Step 1: Create Sidebar.vue

Use the Agent tool with `subagent_type: "vue-expert"` to create `client/src/components/Sidebar.vue`.

**Prompt to send vue-expert:**

> Create a new file at `client/src/components/Sidebar.vue`. This is the vertical navigation sidebar that replaces the existing horizontal top navbar.
>
> **Script (Options API — match App.vue's style):**
> ```javascript
> import { useI18n } from '../composables/useI18n'
> import LanguageSwitcher from './LanguageSwitcher.vue'
> import ProfileMenu from './ProfileMenu.vue'
>
> export default {
>   name: 'Sidebar',
>   components: { LanguageSwitcher, ProfileMenu },
>   emits: ['show-profile-details', 'show-tasks'],
>   setup(props, { emit }) {
>     const { t } = useI18n()
>     return { t, emit }
>   }
> }
> ```
>
> **Template structure:**
> ```html
> <aside class="sidebar">
>   <div class="sidebar-brand">
>     <h1 class="brand-name">{{ t('nav.companyName') }}</h1>
>     <span class="brand-sub">{{ t('nav.subtitle') }}</span>
>   </div>
>
>   <nav class="sidebar-nav">
>     <!-- 6 router-links, each with an inline SVG icon + label -->
>     <!-- Use :class="{ active: $route.path === '/...' }" for active state -->
>     <!-- Dashboard link must use exact path match to avoid matching all routes -->
>   </nav>
>
>   <div class="sidebar-footer">
>     <LanguageSwitcher />
>     <ProfileMenu
>       @show-profile-details="emit('show-profile-details')"
>       @show-tasks="emit('show-tasks')"
>     />
>   </div>
> </aside>
> ```
>
> **Nav links (in order) with inline SVG icons (18×18, fill="none", stroke="currentColor", stroke-width="1.5"):**
>
> 1. `to="/"` — `:class="{ active: $route.path === '/' }"` — Home/house icon — label: `{{ t('nav.overview') }}`
>    SVG path: `<path d="M3 7.5L9 2L15 7.5V15.5H11.5V11H6.5V15.5H3V7.5Z" stroke-linejoin="round"/>`
>
> 2. `to="/inventory"` — `:class="{ active: $route.path === '/inventory' }"` — Box icon — label: `{{ t('nav.inventory') }}`
>    SVG: `<rect x="3" y="7" width="12" height="9" rx="1"/>` + `<path d="M1 7L9 2L17 7"/>`  (viewBox="0 0 18 18")
>    Use: `<path d="M3 8L9 4.5L15 8V15H3V8Z"/>` + `<path d="M6 15V10H12V15"/>`
>
> 3. `to="/orders"` — `:class="{ active: $route.path === '/orders' }"` — List/clipboard icon — label: `{{ t('nav.orders') }}`
>    SVG: `<rect x="3" y="2" width="12" height="14" rx="1"/>` + `<path d="M6 6H12M6 9H12M6 12H9"/>`
>
> 4. `to="/spending"` — `:class="{ active: $route.path === '/spending' }"` — Bar chart icon — label: `{{ t('nav.finance') }}`
>    SVG: `<path d="M3 14V9M7 14V6M11 14V10M15 14V4"/>`
>
> 5. `to="/demand"` — `:class="{ active: $route.path === '/demand' }"` — Trending up icon — label: `{{ t('nav.demandForecast') }}`
>    SVG: `<path d="M2 13L7 8L11 11L16 5"/><path d="M12 5H16V9"/>`
>
> 6. `to="/reports"` — `:class="{ active: $route.path === '/reports' }"` — Document icon — label: `Reports`
>    SVG: `<path d="M5 2H11L15 6V16H5V2Z"/><path d="M11 2V6H15"/><path d="M7 9H13M7 12H11"/>`
>
> **Scoped CSS (complete — implement exactly):**
> ```css
> .sidebar {
>   width: 260px;
>   min-width: 260px;
>   background: #0f172a;
>   border-right: 1px solid rgba(255, 255, 255, 0.06);
>   display: flex;
>   flex-direction: column;
>   height: 100vh;
>   position: sticky;
>   top: 0;
>   overflow-y: auto;
> }
>
> .sidebar-brand {
>   padding: 1.5rem 1.25rem 1.25rem;
>   border-bottom: 1px solid rgba(255, 255, 255, 0.08);
>   flex-shrink: 0;
> }
>
> .brand-name {
>   font-size: 1.125rem;
>   font-weight: 700;
>   color: #ffffff;
>   letter-spacing: -0.025em;
>   margin-bottom: 0.25rem;
> }
>
> .brand-sub {
>   font-size: 0.75rem;
>   color: #94a3b8;
>   font-weight: 400;
>   display: block;
> }
>
> .sidebar-nav {
>   flex: 1;
>   padding: 0.75rem;
>   display: flex;
>   flex-direction: column;
>   gap: 0.125rem;
>   overflow-y: auto;
> }
>
> .sidebar-nav a {
>   display: flex;
>   align-items: center;
>   gap: 0.75rem;
>   padding: 0.625rem 0.875rem;
>   color: #94a3b8;
>   text-decoration: none;
>   font-size: 0.875rem;
>   font-weight: 500;
>   border-radius: 6px;
>   border-left: 3px solid transparent;
>   transition: all 0.15s ease;
>   margin-left: -3px; /* compensate for border-left so text stays aligned */
> }
>
> .sidebar-nav a:hover {
>   color: #e2e8f0;
>   background: rgba(255, 255, 255, 0.06);
> }
>
> .sidebar-nav a.active {
>   color: #ffffff;
>   background: rgba(37, 99, 235, 0.18);
>   border-left-color: #2563eb;
> }
>
> .sidebar-nav a svg {
>   flex-shrink: 0;
>   opacity: 0.8;
> }
>
> .sidebar-nav a.active svg {
>   opacity: 1;
> }
>
> .sidebar-footer {
>   padding: 1rem 0.875rem;
>   border-top: 1px solid rgba(255, 255, 255, 0.08);
>   display: flex;
>   flex-direction: column;
>   gap: 0.5rem;
>   flex-shrink: 0;
> }
>
> /* Flip ProfileMenu and LanguageSwitcher dropdowns to open upward */
> .sidebar-footer :deep(.dropdown-menu) {
>   top: auto;
>   bottom: calc(100% + 0.5rem);
>   left: 0;
>   right: auto;
>   min-width: 200px;
> }
>
> /* Style buttons for dark sidebar background */
> .sidebar-footer :deep(.language-button),
> .sidebar-footer :deep(.profile-button) {
>   background: rgba(255, 255, 255, 0.06);
>   border-color: rgba(255, 255, 255, 0.12);
>   color: #e2e8f0;
>   width: 100%;
>   justify-content: flex-start;
> }
>
> .sidebar-footer :deep(.language-button):hover,
> .sidebar-footer :deep(.profile-button):hover {
>   background: rgba(255, 255, 255, 0.1);
> }
>
> .sidebar-footer :deep(.profile-name) {
>   color: #e2e8f0;
> }
>
> .sidebar-footer :deep(.globe-icon) {
>   color: #94a3b8;
> }
>
> .sidebar-footer :deep(.chevron) {
>   color: #94a3b8;
>   margin-left: auto;
> }
> ```

Wait for vue-expert to complete before proceeding.

---

## Step 2: Update App.vue and FilterBar.vue

Use the Agent tool with `subagent_type: "vue-expert"` to modify both files.

**Prompt to send vue-expert:**

> Make the following changes to two files. Read each file in full before editing.
>
> ---
>
> ### FILE 1: `client/src/App.vue`
>
> **TEMPLATE — replace the entire `<template>` block with:**
> ```html
> <template>
>   <div class="app">
>     <Sidebar
>       @show-profile-details="showProfileDetails = true"
>       @show-tasks="showTasks = true"
>     />
>     <div class="app-body">
>       <FilterBar />
>       <main class="main-content">
>         <router-view />
>       </main>
>     </div>
>
>     <ProfileDetailsModal
>       :is-open="showProfileDetails"
>       @close="showProfileDetails = false"
>     />
>
>     <TasksModal
>       :is-open="showTasks"
>       :tasks="tasks"
>       @close="showTasks = false"
>       @add-task="addTask"
>       @delete-task="deleteTask"
>       @toggle-task="toggleTask"
>     />
>   </div>
> </template>
> ```
>
> **SCRIPT — these import/component changes only (leave setup() completely unchanged):**
> - Add import: `import Sidebar from './components/Sidebar.vue'`
> - Remove imports for `LanguageSwitcher` and `ProfileMenu` (they now live inside Sidebar.vue)
> - In `components: {}`, add `Sidebar` and remove `LanguageSwitcher` and `ProfileMenu`
>
> **CSS — in the global (unscoped) `<style>` block:**
>
> 1. Add this `:root` block at the very top of the style block (before `* { margin: 0 ... }`):
> ```css
> :root {
>   --sidebar-width: 260px;
>   --color-bg: #f8fafc;
>   --color-surface: #ffffff;
>   --color-sidebar: #0f172a;
>   --color-border: #e2e8f0;
>   --color-border-muted: #cbd5e1;
>   --color-text-primary: #0f172a;
>   --color-text-secondary: #64748b;
>   --color-brand: #2563eb;
>   --color-brand-hover: #3b82f6;
>   --color-success: #059669;
>   --color-warning: #ea580c;
>   --color-danger: #dc2626;
> }
> ```
>
> 2. Replace the `.app` rule with:
> ```css
> .app {
>   display: flex;
>   flex-direction: row;
>   min-height: 100vh;
> }
> ```
>
> 3. Add this new rule (after `.app`):
> ```css
> .app-body {
>   flex: 1;
>   min-width: 0;
>   display: flex;
>   flex-direction: column;
>   overflow: hidden;
> }
> ```
>
> 4. Replace the `.main-content` rule with:
> ```css
> .main-content {
>   flex: 1;
>   padding: 1.5rem 2rem;
>   overflow-y: auto;
> }
> ```
>
> 5. **Delete entirely** these rules (they belong to the old top-nav — remove all of them):
>    - `.top-nav`
>    - `.nav-container`
>    - `.logo`
>    - `.logo h1` (or `.logo > h1`)
>    - `.subtitle`
>    - `.nav-tabs`
>    - `.nav-tabs a`
>    - `.nav-tabs a:hover`
>    - `.nav-tabs a.active`
>    - `.nav-tabs a.active::after`
>
> 6. **Leave all other CSS rules completely untouched** — every rule from `.page-header` onward must remain exactly as-is.
>
> ---
>
> ### FILE 2: `client/src/components/FilterBar.vue`
>
> In `<style scoped>`, make these two changes:
>
> 1. In `.filters-bar`: change `top: 70px` to `top: 0`
> 2. In `.filters-container`: remove the `max-width: 1600px;` and `margin: 0 auto;` lines (keep all other properties including `padding: 0 2rem`)

Wait for vue-expert to complete before verifying.

---

## Step 3: Verify

Use Playwright MCP tools to verify the redesign:

1. Navigate to `http://localhost:3000`
2. Take a screenshot — confirm dark sidebar on left, content area on right
3. Click each of the 6 nav links and confirm:
   - The clicked link shows the blue left-border active state
   - The correct view loads
4. Scroll the content area — confirm FilterBar sticks to the top while sidebar stays fixed
5. Click ProfileMenu in the sidebar footer — confirm dropdown opens upward (not clipped)
6. Click LanguageSwitcher — confirm dropdown opens upward

If the dev server is not running, tell the user to run `/start` first and then manually verify.

Also run a build check to confirm no compile errors:
```bash
cd /Users/max.tharr/trial-stuff/anthropic-basecamp/inventory-management/client && npm run build
```
