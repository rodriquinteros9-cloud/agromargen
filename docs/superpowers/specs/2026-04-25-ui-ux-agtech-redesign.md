# Spec: AgTech UI/UX Redesign

## 1. Overview
The goal of this redesign is to elevate the visual and user experience of the `AGROMARGEN_2026-27.html` application to a professional, premium "AgTech" level, comparable to industry leaders. The application will transition from a vertically stacked, traditional web page layout to a Single-Page Application (SPA) dashboard layout, entirely within the existing vanilla HTML/CSS/JS file.

## 2. Design Choices & Visual Language
* **Navigation Style**: SPA Tabs via a persistent left Sidebar.
* **Color Theme**: Light Mode Corporate (Clean & Analytical).
* **Typography**: Maintain `Manrope` but leverage better font-weights and color contrast for hierarchy.
* **Overall Vibe**: Clean, spacious, data-dense but highly readable. Financial/Analytical software aesthetics.

## 3. Architecture Changes (Vanilla HTML/CSS/JS)

### 3.1. Layout Structure (HTML)
The DOM structure of the main application shell (`#appShell`) will be reorganized into a classic Dashboard Layout:
```html
<div id="appShell" class="dashboard-layout">
  <!-- Left Sidebar -->
  <aside class="sidebar">
     <div class="sidebar-header">...</div> <!-- Logo and App Name -->
     <nav class="sidebar-nav">...</nav> <!-- Navigation Tabs -->
     <div class="sidebar-footer">...</div> <!-- Settings, User info -->
  </aside>

  <!-- Main Content Area -->
  <main class="main-content">
     <header class="topbar">...</header> <!-- Context filters (Dept, Producer) -->
     
     <div class="content-wrapper">
         <!-- Module Panels (Only one visible at a time) -->
         <section id="homePanel" class="tab-pane active">...</section>
         <section id="decPanel" class="tab-pane">...</section>
         <section id="bePanel" class="tab-pane">...</section>
         <section id="qualPanel" class="tab-pane">...</section>
         <section id="cobPanel" class="tab-pane">...</section>
         <section id="sensPanel" class="tab-pane">...</section>
     </div>
  </main>
</div>
```

### 3.2. Styling updates (CSS)
1. **CSS Variables (Theming)**:
   * Update backgrounds: `--surface-app: #f8fafc`, `--surface-card: #ffffff`.
   * Update brand colors: `--brand-primary: #059669` (Emerald), `--text-main: #0f172a` (Slate 900), `--text-muted: #64748b` (Slate 500).
   * Update borders and shadows: `--border-color: #e2e8f0`, `--shadow-card: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -1px rgba(0, 0, 0, 0.03)`.
2. **Dashboard Grid**:
   * Implement CSS Grid for `.dashboard-layout` (`grid-template-columns: 250px 1fr`).
   * Sidebar will be fixed or sticky with `100vh` height. Main content will scroll independently.
3. **Component Polish**:
   * Standardize padding (`padding: 24px`) for panels.
   * Round corners using `--radius-md: 8px` to give a sharp, modern feel rather than overly pill-shaped.
   * Standardize Tables: Remove heavy backgrounds, use clean bottom-borders for rows, subtle striping.
   * Update buttons to a unified corporate style (solid primary, outlined secondary).

### 3.3. Interaction Logic (JavaScript)
* Introduce a robust `switchTab(tabId)` function.
* This function will:
  1. Remove `.active` class from all `.tab-pane` sections.
  2. Add `.active` class to the target `.tab-pane`.
  3. Update the visual state of the Sidebar navigation links to reflect the active tab.
  4. Hide or show the contextual action buttons in the topbar depending on the module (e.g., Export to PDF button only visible where applicable).
* Remove the old `toggleDec()`, `toggleBE()` logic that relied on max-height animations for stacked content.

## 4. Implementation Steps
1. **Step 1: CSS Foundation**: Update CSS root variables and add the grid layout classes.
2. **Step 2: HTML Restructuring**: Wrap the existing modules inside the new `<aside>` and `<main>` containers. Move buttons to the new sidebar navigation.
3. **Step 3: JavaScript Routing**: Implement `switchTab()` and map the new sidebar buttons to this function. Update initialization logic to load the "Inicio" tab by default.
4. **Step 4: Polish & Refinement**: Adjust table styles, input fields, and tooltips to match the new Corporate Light theme. Test responsive behavior (Sidebar becomes a bottom nav or hamburger menu on mobile).

## 5. Scope & Constraints
* **Scope**: UI/UX layer only. No changes to the underlying business logic, math calculations, or export functionalities (jsPDF/SheetJS).
* **Constraints**: Must remain a single file `AGROMARGEN_2026-27.html` with zero build dependencies. Responsive design must still work on tablets and desktop (AgTech standard).
