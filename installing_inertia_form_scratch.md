# INERTIA_SETUP_GUIDE.md

## Overview

This guide outlines the standard procedure for integrating **Inertia.js v2** with **Vue 3** in a fresh **Laravel** application. It covers both server-side (Laravel) and client-side (Vue/Vite) configurations.

---

## Part 1: Server-Side Setup (Laravel)

### 1. Install Server-Side Adapter

Run the following command to install the official Inertia Laravel adapter via Composer:

```bash
composer require inertiajs/inertia-laravel

```

### 2. Setup Root Template

Create a root blade file at `resources/views/app.blade.php`. This file will serve as the shell for your application.

**File:** `resources/views/app.blade.php`

```blade
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    @vite('resources/js/app.js')
    @inertiaHead
  </head>
  <body>
    @inertia
  </body>
</html>

```

### 3. Setup Middleware

Generate the Inertia middleware using the artisan command:

```bash
php artisan inertia:middleware

```

Next, register this middleware in your `bootstrap/app.php` file by appending it to the `web` group:

**File:** `bootstrap/app.php`

```php
use App\Http\Middleware\HandleInertiaRequests;

->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        HandleInertiaRequests::class,
    ]);
})

```

---

## Part 2: Client-Side Setup (Vue 3 + Vite)

### 1. Install Dependencies

Install the Inertia Vue 3 adapter, Vue core, and the Vite Vue plugin:

```bash
npm install @inertiajs/vue3 vue @vitejs/plugin-vue

```

### 2. Configure Vite

Update your `vite.config.js` to include the Vue plugin.

**File:** `vite.config.js`

```javascript
import { defineConfig } from "vite";
import laravel from "laravel-vite-plugin";
import vue from "@vitejs/plugin-vue";

export default defineConfig({
  plugins: [
    laravel({
      input: ["resources/css/app.css", "resources/js/app.js"],
      refresh: true,
    }),
    vue({
      template: {
        transformAssetUrls: {
          base: null,
          includeAbsolute: false,
        },
      },
    }),
  ],
});
```

### 3. Initialize the Inertia App

Update your main JavaScript entry point to boot the Vue application with Inertia.

**File:** `resources/js/app.js`

```javascript
import "./bootstrap";
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

### 4. Create a Demo Page

Create your first Vue page component.

**File:** `resources/js/Pages/Home.vue`

```vue
<script setup>
// Component logic here
</script>

<template>
  <div>
    <h1>Hello Inertia + Vue!</h1>
  </div>
</template>
```

### 5. Define the Route

Update your web routes to render the component.

**File:** `routes/web.php`

```php
use Illuminate\Support\Facades\Route;
use Inertia\Inertia;

Route::get('/', function () {
    return Inertia::render('Home');
});

```

---

## Part 3: The AI Prompt (Copy & Paste)

Use the prompt below to instruct GitHub Copilot, ChatGPT, or Cursor to perform this setup automatically in any new Laravel project.

````markdown
### Role

Act as a **Senior Full-Stack Developer** specializing in Laravel and Vue.js.

### Task

Configure a fresh Laravel application to use **Inertia.js v2** with **Vue 3**.

### Context

I have a fresh Laravel installation. I need you to convert the frontend architecture from Blade to a modern Single Page Application (SPA) using Inertia.

### Instructions

1.  **Server-Side:**
    - Install `inertiajs/inertia-laravel` via composer.
    - Create the `resources/views/app.blade.php` root template with `@vite` and `@inertia` directives.
    - Generate and register the `HandleInertiaRequests` middleware in `bootstrap/app.php`.

2.  **Client-Side:**
    - Install NPM packages: `@inertiajs/vue3`, `vue`, and `@vitejs/plugin-vue`.
    - Configure `vite.config.js` to use the Vue plugin.
    - **Update `resources/js/app.js` following strictly these rules:**
      - **DO NOT remove** the existing `import './bootstrap';` line at the top.
      - Append the Inertia and Vue imports below it.
      - Initialize the app using `createInertiaApp` with `import.meta.glob` (eager loading).
      - The final code structure should look like this:

        ```javascript
        import "./bootstrap";
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

3.  **Verification:**
    - Create a sample `Home.vue` in `resources/js/Pages/`.
    - Update `routes/web.php` to render this page using `Inertia::render`.

### Constraints

- Follow the official Inertia.js v2 documentation patterns.
- Use `<script setup>` syntax for Vue components.
- Ensure proper strict types and imports are used where applicable.
````
