# Adding Dark Mode

Sheaf UI includes a comprehensive dark mode system that automatically detects user preferences, prevents flickering. The implementation uses Alpine.js for state management and Tailwind CSS for styling.


## Using CLI 
when using our [CLI](/docs/guides/cli-installation) it do most of work for you, when running 

```sh
php artisan sheaf:init
```

it will add : 
- ``resources/js/utils.js`` a utility for registering reactive magic property
- ``resources/js/globals/theme.js`` 

> here's full implementation of adding dark mode to your app 

## Implementation Overview

> This is the cleaner way we discover so far to add dark mode to your apps  

The dark mode system integrates seamlessly with Sheaf UI's ``theme-switcher`` component, which handles all user interactions and theme management automatically. The component fires `theme-changed` events that the theme switcher script listens to, providing a complete theming solution.

## Step 1: Setup the Utility
using the same way we manage `modals`, `toasts`, other global things you may consider

```js
// resources/js/utils.js
export default function defineReactiveMagicProperty(name, rawObject) {
    const instance = Alpine.reactive(rawObject);

    /** reactive objects are plain proxies and does not support hooks like stores,
     or scopes in alpine so we init manualy */
    if (typeof instance.init === 'function') {
        instance.init();
    }

    Alpine.magic(name, () => instance);
    // ex : if the magic called $modal we register Modal into the window
    window[name[0].toUpperCase() + name.slice(1)] = instance;
}
```

this is responsible for registering reactive object and expose it to be used as magic alpinejs property, and also bind it globally with `Window` Object  

<!-- ## Step 1: Theme Switcher Setup

Create the theme switcher functionality that handles theme detection, storage, and switching:

```javascript
// resources/js/components/theme-switcher.js
export default () => {
    // Get initial theme from localStorage or default to system
    const theme = localStorage.getItem("theme") ?? "system";
    
    // Initialize Alpine store with current theme
    window.Alpine.store(
        "theme",
        theme === "dark" ||
            (theme === "system" &&
                window.matchMedia("(prefers-color-scheme: dark)").matches)
            ? "dark"
            : "light"
    );

    // Listen for manual theme changes
    window.addEventListener("theme-changed", (event) => {
        let theme = event.detail;
        
        // Persist theme preference
        localStorage.setItem("theme", theme);
        
        // Handle system theme detection
        if (theme === "system") {
            theme = window.matchMedia("(prefers-color-scheme: dark)").matches
                ? "dark"
                : "light";
        }
        
        // Update Alpine store
        window.Alpine.store("theme", theme);
    });

    // Listen for system theme changes
    window
        .matchMedia("(prefers-color-scheme: dark)")
        .addEventListener("change", (event) => {
            // Only update if user has system preference selected
            if (localStorage.getItem("theme") === "system") {
                window.Alpine.store("theme", event.matches ? "dark" : "light");
            }
        });

    // React to theme changes and update DOM
    window.Alpine.effect(() => {
        const theme = window.Alpine.store("theme");
        
        theme === "dark"
            ? document.documentElement.classList.add("dark")
            : document.documentElement.classList.remove("dark");
    });
};
``` -->

## Step 2: Register $theme magic property

```js
// resources/js/globals/theme.js
import defineReactiveMagicProperty from '../utils';

document.addEventListener('alpine:init', () => {
    defineReactiveMagicProperty('theme', {
        currentTheme: null,
        storedTheme: null,

        init() {
            // at first we check local storage
            this.storedTheme = localStorage.getItem('theme') ?? 'system';

            // resolve the configured theme to be set only [light, dark]
            this.currentTheme = computeTheme(this.storedTheme);
            // Apply initial theme to DOM
            applyTheme(this.currentTheme);

            // Listen for system theme changes
            let media = window.matchMedia('(prefers-color-scheme: dark)');

            media.addEventListener('change', (event) => {
                if (this.storedTheme === 'system') {
                    this.currentTheme = event.matches ? 'dark' : 'light';
                    applyTheme(this.currentTheme);
                }
            });
        },

        setTheme(newTheme) {

            this.storedTheme = newTheme;
            localStorage.setItem('theme', newTheme);

            this.currentTheme = computeTheme(newTheme);

            applyTheme(this.currentTheme);
        },

        setLight() {
            this.setTheme('light');
        },

        setDark() {
            this.setTheme('dark');
        },

        setSystem() {
            this.setTheme('system');
        },

        toggle() {
            if (this.storedTheme === 'system') {
                // If system, toggle to opposite of current computed theme
                this.setTheme(this.currentTheme === 'dark' ? 'light' : 'dark');
            } else {
                // Toggle between light and dark
                this.setTheme(this.storedTheme === 'dark' ? 'light' : 'dark');
            }
        },

        get() {
            return {
                stored: this.storedTheme,
                current: this.currentTheme,

                isLight: this.isLight,
                isDark: this.isDark,
                isSystem: this.isSystem
            };
        },

        // Getter methods for easy template usage
        get isLight() {
            return this.storedTheme === 'light';
        },

        get isDark() {
            return this.storedTheme === 'dark';
        },

        get isSystem() {
            return this.storedTheme === 'system';
        },
        // some times we need to show only light or dark not system mode, so we handle 
        // these senarions using this getter that take care when the old mode is system
        get isResolvedToLight() {
            if (this.isSystem) {
                return resolved() === 'light'
            } else {
                return this.isLight;
            }
        },
        get isResolvedToDark() {
            if (this.isSystem) {
                return resolved() === 'dark'
            } else {
                return this.isDark;
            }
        }
    })
});

// static function 
function computeTheme(themePreference) {
    if (themePreference === 'system') {
        return resolved();
    }
    return themePreference;
}
function resolved() {
    return window.matchMedia('(prefers-color-scheme: dark)').matches
        ? 'dark'
        : 'light';
}

function applyTheme(theme) {
    if (theme === 'dark') {
        document.documentElement.classList.add('dark');
    } else {
        document.documentElement.classList.remove('dark');
    }
}
```

## Step 3: import the file



```javascript
// resource/js/app.js
import './globals/theme.js';
```

## Step 4: Prevent Flickering

Add flicker prevention scripts directly in your HTML head to ensure themes load before content renders:

```html
<!-- resources/views/layouts/app.blade.php -->
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <title>Your App {{ isset($title) ? '| ' . $title : '' }}</title>
    
    @vite(['resources/css/app.css'])
    
    <script>
        // Load dark mode before page renders to prevent flicker
        const loadDarkMode = () => {
            const theme = localStorage.getItem('theme') ?? 'system'
            
            if (
                theme === 'dark' ||
                (theme === 'system' &&
                    window.matchMedia('(prefers-color-scheme: dark)')
                    .matches)
            ) {
                document.documentElement.classList.add('dark')
            }
        }
                
        // Initialize on page load
        loadDarkMode();
        
        // Reinitialize after Livewire navigation (for spa mode)
        document.addEventListener('livewire:navigated', function() {
            loadDarkMode();
        });
    </script>
</head>

<body class="bg-gray-100 dark:bg-black text-gray-800 dark:text-white">
    {{ $slot }}
    
    @livewireScriptConfig
    @vite(['resources/js/app.js'])
    
    <!-- Ensure dark mode is applied after scripts load, this is also required to prevent flickering when many livewire component changes independently -->
    <script>
        loadDarkMode()
    </script>
</body>
</html>
```
## Step 5: Add Theme Switcher Component

Sheaf UI includes a comprehensive theme-switcher component with multiple variants. Add it to your layout or navigation:

```blade
{{-- Basic dropdown theme switcher --}}
<x-ui.theme-switcher variant="dropdown" />

{{-- Stacked toggle variant --}}
<x-ui.theme-switcher variant="stacked" />

{{-- Inline buttons variant --}}
<x-ui.theme-switcher variant="inline" />
```
> go the official docs of the [theme switcher component](/docs/components/theme-switcher)

### Testing Themes
Test your components in all three theme modes:
- Light mode
- Dark mode  
- System preference (both light and dark)

## Troubleshooting

### Theme Not Persisting
Ensure localStorage is available and not blocked by privacy settings.

### Flickering on Load
Make sure the flicker prevention script runs before any content renders and also leverage using livewire nivation events.

### System Theme Not Detected
Verify that the `prefers-color-scheme` media query is supported in your target browsers.



