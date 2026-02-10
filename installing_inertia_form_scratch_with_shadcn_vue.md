### Role

Act as a **Senior Full-Stack Developer** specializing in Laravel, Vue.js, and Modern UI Frameworks.

### Task

Configure a fresh Laravel application to use **Inertia.js v2** with **Vue 3**, ensuring the existing **Tailwind CSS** setup is functional, and integrate **Shadcn-vue** with correct directory mapping.

### Context

I have a fresh Laravel installation with Tailwind CSS pre-installed. I need to convert the frontend to an Inertia SPA. My project uses lowercase folder names for the JS structure (e.g., `resources/js/pages`).

### Instructions

1. **Server-Side:**
   - Install `inertiajs/inertia-laravel` via composer.
   - Create `resources/views/app.blade.php` with:
     - `@vite(['resources/js/app.js', 'resources/css/app.css'])`
     - The `@inertia` directive.
   - Generate and register `HandleInertiaRequests` middleware in `bootstrap/app.php`.

2. **Vite & Path Aliasing (Required for Shadcn):**
   - Install `@types/node` as a dev dependency.
   - Update `vite.config.js`:
     - Add `import path from 'path'` and define an alias: `'@': path.resolve(__dirname, './resources/js')`.
     - Ensure the Vue plugin is active: `vue()`.

3. **Tailwind Configuration:**
   - Update `tailwind.config.js` to include: `'./resources/js/**/*.vue'`.
   - Ensure the Shadcn components directory is also covered.

4. **Client-Side (resources/js/app.js):**
   - **DO NOT remove** `import './bootstrap';`.
   - **MUST import** the main CSS file: `import '../css/app.css';`.
   - Initialize the app using `createInertiaApp`.
   - **CRITICAL:** Use lowercase `pages` for the directory resolving:
     ```javascript
     resolve: (name) => {
       const pages = import.meta.glob("./pages/**/*.vue", { eager: true });
       return pages[`./pages/${name}.vue`];
     },
     ```

5. **Shadcn-vue Integration:**
   - Run `npx shadcn-vue@latest init`.
   - Configure `components.json` to use `@/components/ui` for components and `@/lib/utils` for utilities.
   - Add a sample component: `npx shadcn-vue@latest add button`.

6. **Verification:**
   - Create a sample `Home.vue` in `resources/js/pages/` using `<script setup lang="ts">`.
   - **Test Case:** Use a Shadcn `Button` component inside `Home.vue` with Tailwind classes to verify everything (Inertia + Vue + Tailwind + Shadcn) is working together.
   - Update `routes/web.php` to render `Home.vue` using `Inertia::render('Home')`.

### Constraints

- Strictly follow Inertia.js v2 and Shadcn-vue documentation.
- Use lowercase `pages` folder for all Inertia page components.
- Maintain existing PostCSS/Tailwind configurations without breaking them.
