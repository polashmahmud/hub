# TASK: Wire Vue Sonner to Laravel Inertia

# ROLE: Senior Laravel & Vue.js Developer

## CONTEXT

- Laravel 11 + Inertia Vue 3 stack
- Flash messages must surface as Vue Sonner toasts
- All UI knobs (position, theme, duration, density) configurable from `.env`

## IMPLEMENTATION STEPS

### 1) Dependency

Install: `npm install vue-sonner`

### 2) Backend config

Create `config/toast.php` with tunable options:

```php
<?php

return [
    /*
    |--------------------------------------------------------------------------
    | Toast Notification Enabled
    |--------------------------------------------------------------------------
    |
    | This option controls whether toast notifications are enabled globally.
    |
    */
    'enabled' => env('TOAST_ENABLED', true),

    /*
    |--------------------------------------------------------------------------
    | Toast Position
    |--------------------------------------------------------------------------
    |
    | Available positions: top-left, top-center, top-right,
    | bottom-left, bottom-center, bottom-right
    |
    */
    'position' => env('TOAST_POSITION', 'top-right'),

    /*
    |--------------------------------------------------------------------------
    | Toast Theme
    |--------------------------------------------------------------------------
    |
    | Available themes: light, dark, system
    |
    */
    'theme' => env('TOAST_THEME', 'light'),

    /*
    |--------------------------------------------------------------------------
    | Rich Colors
    |--------------------------------------------------------------------------
    |
    | Makes error and success states more colorful
    |
    */
    'rich_colors' => env('TOAST_RICH_COLORS', false),

    /*
    |--------------------------------------------------------------------------
    | Close Button
    |--------------------------------------------------------------------------
    |
    | Shows close button on hover
    |
    */
    'close_button' => env('TOAST_CLOSE_BUTTON', false),

    /*
    |--------------------------------------------------------------------------
    | Expand Toasts
    |--------------------------------------------------------------------------
    |
    | Expands toasts by default
    |
    */
    'expand' => env('TOAST_EXPAND', false),

    /*
    |--------------------------------------------------------------------------
    | Visible Toasts
    |--------------------------------------------------------------------------
    |
    | Number of toasts visible at once
    |
    */
    'visible_toasts' => env('TOAST_VISIBLE_TOASTS', 3),

    /*
    |--------------------------------------------------------------------------
    | Duration
    |--------------------------------------------------------------------------
    |
    | Duration in milliseconds (4000ms = 4 seconds)
    |
    */
    'duration' => env('TOAST_DURATION', 4000),

    /*
    |--------------------------------------------------------------------------
    | Offset
    |--------------------------------------------------------------------------
    |
    | Offset from the edges of the screen
    |
    */
    'offset' => env('TOAST_OFFSET', "20px"),

    /*
    |--------------------------------------------------------------------------
    | Hotkey
    |--------------------------------------------------------------------------
    |
    | Keyboard shortcut to focus toast area (default: ['altKey', 'KeyT'])
    |
    */
    'hotkey' => env('TOAST_HOTKEY', ['altKey', 'KeyT']),

    /*
    |--------------------------------------------------------------------------
    | Custom Classes
    |--------------------------------------------------------------------------
    |
    | Custom CSS classes for styling toasts
    |
    */
    'classes' => [
        'toast' => '',
        'title' => '',
        'description' => '',
        'actionButton' => '',
        'cancelButton' => '',
        'closeButton' => '',
        'error' => '',
        'success' => '',
        'warning' => '',
        'info' => '',
    ],
];
```

### 3) Middleware integration

Update `app/Http/Middleware/HandleInertiaRequests.php` to share flash + config:

```php
<?php

namespace App\Http\Middleware;

use Illuminate\Http\Request;
use Illuminate\Support\Arr;
use Inertia\Middleware;

class HandleInertiaRequests extends Middleware
{
    protected $rootView = 'app';

    public function version(Request $request): ?string
    {
        return parent::version($request);
    }

    public function share(Request $request): array
    {
        return [
            ...parent::share($request),
            'name' => config('app.name'),
            'auth' => [
                'user' => $request->user(),
            ],

            'notification' => collect(Arr::only($request->session()->all(), ['success', 'error', 'info', 'warning']))
                ->mapWithKeys(function ($notification, $key) {
                    return ['type' => $key, 'body' => $notification];
                }),
            'toast' => [
                'position' => config('toast.position'),
                'theme' => config('toast.theme'),
                'richColors' => config('toast.rich_colors'),
                'closeButton' => config('toast.close_button'),
                'expand' => config('toast.expand'),
                'visibleToasts' => config('toast.visible_toasts'),
                'duration' => config('toast.duration'),
                'offset' => config('toast.offset'),
                'hotkey' => config('toast.hotkey'),
                'enabled' => config('toast.enabled'),
                'classes' => config('toast.classes'),
            ],
        ];
    }
}
```

### 4) Frontend plugin

Create `resources/js/plugins/notifications.ts` to listen on Inertia finish and emit Vue Sonner events:

```typescript
import { router, usePage } from "@inertiajs/vue3";
import { toast } from "vue-sonner";

export const notifictions = () => {
  router.on("finish", () => {
    const page = usePage();
    const toastConfig = page.props.toast as any;

    if (!toastConfig?.enabled) return;

    const notification = page.props.notification as
      | { type?: string; body?: string }
      | undefined;

    if (notification && notification.type && notification.body) {
      const options = {
        duration: toastConfig.duration,
      };

      switch (notification.type) {
        case "success":
          toast.success(notification.body, options);
          break;
        case "error":
          toast.error(notification.body, options);
          break;
        case "info":
          toast.info(notification.body, options);
          break;
        case "warning":
          toast.warning(notification.body, options);
          break;
      }
    }
  });
};
```

### 5) Layout wiring

In `resources/js/layouts/AppLayout.vue`, place the toaster and bind props from Inertia page props:

```vue
<script setup lang="ts">
import AppLayout from "@/layouts/app/AppSidebarLayout.vue";
import type { BreadcrumbItem } from "@/types";
import { Toaster } from "vue-sonner";
import "vue-sonner/style.css";
import { usePage } from "@inertiajs/vue3";

type Props = {
  breadcrumbs?: BreadcrumbItem[];
};

withDefaults(defineProps<Props>(), {
  breadcrumbs: () => [],
});

const toastConfig = usePage().props.toast as any;
</script>

<template>
  <AppLayout :breadcrumbs="breadcrumbs">
    <Toaster
      v-if="toastConfig?.enabled"
      :position="toastConfig.position"
      :theme="toastConfig.theme"
      :rich-colors="toastConfig.richColors"
      :close-button="toastConfig.closeButton"
      :expand="toastConfig.expand"
      :visible-toasts="toastConfig.visibleToasts"
      :duration="toastConfig.duration"
      :offset="toastConfig.offset"
      :hotkey="toastConfig.hotkey"
      :toast-options="{
        classes: toastConfig.classes,
      }"
    />
    <slot />
  </AppLayout>
</template>
```

### 6) App boot

Register the plugin in `resources/js/app.ts` so each navigation processes flashes:

```typescript
import { createInertiaApp } from "@inertiajs/vue3";
import { resolvePageComponent } from "laravel-vite-plugin/inertia-helpers";
import type { DefineComponent } from "vue";
import { createApp, h } from "vue";
import "../css/app.css";
import { initializeTheme } from "./composables/useAppearance";
import { notifictions } from "./plugins/notifictions";

const appName = import.meta.env.VITE_APP_NAME || "Laravel";

createInertiaApp({
  title: (title) => (title ? `${title} - ${appName}` : appName),
  resolve: (name) =>
    resolvePageComponent(
      `./pages/${name}.vue`,
      import.meta.glob<DefineComponent>("./pages/**/*.vue"),
    ),
  setup({ el, App, props, plugin }) {
    createApp({ render: () => h(App, props) })
      .use(plugin)
      .use(notifictions)
      .mount(el);
  },
  progress: {
    color: "#4B5563",
  },
});

// This will set light / dark mode on page load...
initializeTheme();
```

## OUTPUT REQUIREMENT

- Files contain the snippets above verbatim
- TypeScript remains strict and Vue 3 script-setup friendly
- Flash messages render as toasts with the configured position, theme, and duration

```

```
