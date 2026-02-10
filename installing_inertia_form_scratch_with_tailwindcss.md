### Role

Act as a **Senior Full-Stack Developer** specializing in Laravel and Vue.js.

### Task

Configure a fresh Laravel application to use **Inertia.js v2** with **Vue 3**, ensuring the existing **Tailwind CSS** setup is fully integrated and functional.

### Context

I have a fresh Laravel installation where Tailwind CSS and Vite are already pre-installed. I need you to convert the frontend architecture to an Inertia-powered SPA without removing or breaking the Tailwind CSS configuration.

### Instructions

1. **Server-Side:**
   - Install `inertiajs/inertia-laravel` via composer.
   - Create the `resources/views/app.blade.php` root template. It must include:
     - `@vite(['resources/js/app.js', 'resources/css/app.css'])` (Ensuring CSS is loaded).
     - The `@inertia` directive.
   - Generate and register the `HandleInertiaRequests` middleware in `bootstrap/app.php`.

2. **Tailwind & Vite Configuration:**
   - **Tailwind:** Update `tailwind.config.js` to ensure the `content` array includes: `'./resources/js/**/*.vue'`, `'./resources/views/**/*.blade.php'`.
   - **Vite:** Configure `vite.config.js` to include the `@vitejs/plugin-vue` while maintaining the existing Laravel plugin.

3. **Client-Side (resources/js/app.js):**
   - **DO NOT remove** the existing `import './bootstrap';` line.
   - **MUST import** the main CSS file: `import '../css/app.css';` to ensure Tailwind styles apply.
   - Append the Inertia and Vue imports and initialize the app using the following structure:

     ```javascript
     import "./bootstrap";
     import "../css/app.css";
     import { createApp, h } from "vue";
     import { createInertiaApp } from "@inertiajs/vue3";

     createInertiaApp({
       resolve: (name) => {
         const pages = import.meta.glob("./Pages/**/*.vue", { eager: true });
         return pages[`./Pages/${name}.vue`];
       },
       setup({ el, App, props, plugin }) {
         createApp({ render: () => h(App, props) })
           .use(plugin)
           .mount(el);
       },
     });
     ```

4. **Verification:**
   - Create a sample `Home.vue` in `resources/js/Pages/` using `<script setup>` syntax.
   - Include some Tailwind CSS utility classes (e.g., `class="text-3xl font-bold text-blue-600"`) in the template to verify Tailwind is working.
   - Update `routes/web.php` to render this page using `Inertia::render`.

### Constraints

- Strictly follow Inertia.js v2 documentation.
- Do not overwrite existing CSS/PostCSS configurations; supplement them for Vue support.
- Ensure the application remains a clean, functional SPA.
