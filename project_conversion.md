# Request: Project Conversion from React/TS to Laravel/Inertia/Vue 3

## Context

I have an existing **React + TypeScript + Shadcn UI** project located in the `/demo` folder. I want to migrate this entire project into a **Laravel + Inertia.js + Vue 3 (Composition API) + TypeScript + Shadcn-vue** stack.

### Project Structure:

- **Source Folder:** `/demo/src`
- **Laravel Target:** `/resources/js`
- **Inertia Pages:** `/resources/js/Pages`
- **Vue Components:** `/resources/js/components`
- **Backend:** `routes/web.php` and `app/Http/Controllers`

---

## Required Tasks:

### 1. Project Analysis

- Scan all files in `/demo/src` to identify components, pages, and routing logic.
- List all dependencies and identify custom Tailwind color configurations in the React project.

### 2. Color Palette & Theming (Crucial)

- **Maintain Design Fidelity:** During conversion, ensure the color pattern matches the original React project.
- **Shadcn-vue Theme Sync:** If the original project uses specific primary/secondary colors or custom HEX codes, adjust the `shadcn-vue` CSS variables (`global.css`) or `tailwind.config.js` to match.
- Do not let the default Shadcn-vue theme override the original project's visual identity. If a component uses a custom brand color, ensure the Vue version reflects this using appropriate Tailwind classes or CSS variable mapping.

### 3. File Conversion (React to Vue 3)

- **SFC Conversion:** Convert `.tsx` to Vue 3 SFC with `<script setup lang="ts">`.
- **Logic Mapping:** - `useState` → `ref()` / `reactive()`
  - `useEffect` → `onMounted()` / `watchEffect()`
  - `useMemo` → `computed()`
- **UI & Styling:** Refactor Shadcn (React) syntax to Shadcn-vue. Ensure that any color-specific props or tailwind classes (e.g., `text-primary`, `bg-sidebar-accent`) are correctly mapped to the new setup's theme variables.

### 4. Laravel Integration

- Create **Laravel Controllers** and methods for each page.
- Define routes in `web.php` and use `Inertia::render()`.
- Shift data-fetching logic from client-side (hooks) to server-side (controller props).

### 5. Dependency & Type Management

- Update `package.json` with Vue, TypeScript, and Shadcn-vue dependencies.
- Ensure `tailwind.config.js` is updated to include the necessary Shadcn-vue animations and color extensions.

---

## Expected Output for Each Component/Page:

1. **Laravel Route:** The code snippet for `web.php`.
2. **Controller Method:** The PHP logic.
3. **Vue 3 SFC:** The full code block including template and logic.
4. **Theming Instructions:** If specific color overrides are needed in `tailwind.config.js` or `app.css` to keep the UI consistent, please specify them.

Please proceed step-by-step, prioritizing visual consistency and the correct transition to the Inertia data-flow.
