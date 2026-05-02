# Sheaf CLI Documentation

The CLI tool streamlines component installation, theme management, and project setup to accelerate your Laravel development workflow.

## Installation

Install the Sheaf CLI package in your Laravel project using Composer:

```bash
composer require sheaf/cli
```

**Requirements:**

- Laravel 10.0 or higher
- PHP 8.1 or higher
- Tailwind CSS 4.0 or higher
- Alpine.js (auto-installed during initialization)

## Package Initialization

After installing the CLI, initialize Sheaf with all required dependencies and configurations:

```bash
php artisan sheaf:init
```
This is a **one-time setup command** that prepares your Laravel project for Sheaf components.

**Command Options**
- `--with-dark-mode` : Include dark mode theme variables and utilities (default = false)
- `--with-phosphor` : Install and configure Phosphor Icons package (default = false)
- `--css-file=app.css` : Target CSS file name for package assets injection (relative to resources/css/) (default = app.css)
- `--theme-file=theme.css` : Name for the generated theme CSS file (relative to resources/css/) (default = theme.css)
- `--skip-prompts` : Skip interactive prompts and use default configuration (default = false)
- `--force` : Force overwrite existing files and configurations (default = false)


#### What the Initialization Does
The `sheaf:init` command transforms your Laravel project by:

**CSS Theme System**
- Installs CSS custom properties for consistent theming
- Adds utility classes for component styling
- Sets up CSS variable management

**Dark Mode Support**
- Configures automatic theme switching
- Integrates with Alpine.js for reactive updates
- Provides theme persistence across sessions

**JavaScript Utilities**
- Installs Alpine.js if not present
- Adds reactive theme management system
- Creates utility functions for component interactions

**Dependencies & Structure**
- Installs required packages automatically
- Creates organized directory structure
- Updates your existing CSS and JS files

### Interactive Configuration

The command will guide you through configuration options:

1. **Dark Mode Setup** - Choose whether to include dark theme support
2. **Phosphor Icons** - Optionally install the comprehensive icon library
3. **File Locations** - Customize CSS and JavaScript file locations
4. **Dependency Management** - Select which packages to install

#### Interactive Setup Process

The initialization command guides you through configuration:

```bash
php artisan sheaf:init
```

**Step 2: Icon Library**
```
? Install Phosphor Icons library? (Y/n)
```
Optionally install the comprehensive Phosphor icon set.

**Step 1: Dark Mode Configuration**
```
? Include dark mode theme support? (Y/n)
```
Choose whether to include dark theme switching capabilities.

If you choose **Yes**, see the documentation for details: https://sheafui.dev/docs/guides/dark-mode

**Step 3: File Locations**
```
? Target CSS file for package assets integration: (app.css)
? Theme CSS file name: (theme.css)
```
Customize where theme files should be created (relative to resources folder).

**Step 4: Dependencies**
```
? Will this project use Livewire? (Y/n)
```
If you choose **No**, Alpine.js will be skipped. If you choose **Yes**, it will be installed and configured automatically (only if it’s not already present).

#### Command Options

**Skip Interactive Prompts**
```bash
php artisan sheaf:init --skip-prompts
```
Uses default settings for all configuration options.

**Include All Features**
```bash
php artisan sheaf:init --with-dark-mode --with-phosphor
```
Enables dark mode and installs Phosphor icons automatically.

**Force Overwrite**
```bash
php artisan sheaf:init --force
```
Overwrites existing files without confirmation prompts.

**Custom File Locations**
```bash
php artisan sheaf:init --css-file=styles/main.css --theme-file=custom-theme
```
Specify custom locations for generated files.

**Complete Example with Options**
```bash
php artisan sheaf:init \
  --with-dark-mode \
  --with-phosphor \
  --css-file=resources/css/app.css \
  --theme-file=sheaf-theme \
  --skip-prompts
```


### Generated Files

After initialization, you'll have:

```
resources/
├── css/
│   ├── app.css (updated with theme import)
│   └── theme.css (CSS custom properties)
└── js/
    ├── utils.js (Alpine.js utilities)
    ├── app.js (updated with imports)
    └── globals/
        └── theme.js (Dark mode system)
```

### Important: Include Assets in Your Layout

**Critical:** Update your main layout file to include the compiled assets:

```html
{{-- resources/views/layouts/app.blade.php --}}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ config('app.name') }}</title>
    
    {{-- Include Sheaf UI CSS --}}
    @vite(['resources/css/app.css'])
</head>
<body>
    {{-- Your app content --}}
    <main>
        @yield('content')
    </main>
    
    {{-- Include Sheaf UI JavaScript --}}
    @vite(['resources/js/app.js'])
</body>
</html>
```
#### If you are using Livewire version 3


```html
{{-- resources/views/layouts/app.blade.php --}}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ config('app.name') }}</title>
    
    {{-- Include Sheaf UI CSS --}}
    @vite(['resources/css/app.css'])

    {{-- include --}}
    {+ @livewireStyles +}
</head>
<body>
    {{-- Your app content --}}
    <main>
        @yield('content')
    </main>
    
    {{-- Include Sheaf UI JavaScript --}}
    @vite(['resources/js/app.js'])


    {{-- include --}}
    {+ @livewireScripts +}
</body>
</html>
```
**For Laravel Mix users:**
```html
{{-- Replace @vite() with mix() --}}
<link rel="stylesheet" href="{{ mix('css/app.css') }}">
<script src="{{ mix('js/app.js') }}"></script>
```

---

## Authentication & Account Management

### Login to Your Account

You can authenticate with your SheafUI Pro account, to install your purchased component:

```bash
php artisan sheaf:login
```

This command will:

- Prompt for your Sheaf account credentials

### View Current Account

Check which account is currently authenticated:

```bash
php artisan sheaf:whoami
```

This command displays:

- **Email Address** - Your registered Sheaf account email

**Example Output:**
```
You're login as sheafuipro@gmail.com
```

### Logout from Your Account

Remove stored authentication credentials and logout:

```bash
php artisan sheaf:logout
```

## Component Management

### Installing Components

Install individual components with all their dependencies:

```bash
php artisan sheaf:install component-name
```

**Examples:**

```bash
# Install a button component
php artisan sheaf:install button

# Install a modal component (may include dependencies)
php artisan sheaf:install modal
```

### What Happens During Installation

1. **Component Files** - Blade components are added to `resources/views/components/ui/`
2. **Dependencies** - Required components and packages are automatically installed/updated (with confirmation)
3. **Assets** - CSS and JavaScript assets are integrated
4. **Documentation** - Usage examples are available at `https://sheafui.dev/docs/components/component-name`

#### Installation Options

**Force Overwrite Existing Components**
```bash
php artisan sheaf:install button --force
```
Replaces existing component files without confirmation.

**Preview Installation (Dry Run)**
```bash
php artisan sheaf:install button --dry-run
```
Shows what files would be created/modified without making changes.

**Install with All Dependencies**
```bash
php artisan sheaf:install button --internal-deps --external-deps
```
- `--internal-deps`: Install required Sheaf components
- `--external-deps`: Install required npm/composer packages

**Install Only Dependencies**
```bash
php artisan sheaf:install button --only-deps
```
Installs only the dependencies.


**Skip Dependency Installation**
```bash
php artisan sheaf:install button --skip-deps
```
Installs only the main component, skipping dependencies.


### Example Installation Output

```bash
php artisan sheaf:install radio         

═════════════════════════════════════
  Installing:  radio
═════════════════════════════════════

  Radio  has been installed successfully.
 resources/views/components/ui/radio/group.blade.php has been  created 

 resources/views/components/ui/radio/item.blade.php has been  created 

 resources/views/components/ui/radio/indicator.blade.php has been  created 

 Radio component requires internal dependencies to function properly.

 ┌ Install required dependencies? ──────────────────────────────┐
 │ Yes                                                          │
 └──────────────────────────────────────────────────────────────┘

 ↳ Installing Radio internal dependencies.

  Icon  has been installed successfully.
 resources/views/components/ui/icon/index.blade.php has been  created 

 All Radio dependencies installed successfully.

  INFO  Full documentation: https://sheafui.dev/docs/components/radio. 

```

### Batch Operations

**Install Multiple Related Components**
```bash
# Install all form components
php artisan sheaf:install button input select textarea switch radio

```

### Updating Components

update individual component with all their dependencies:

```bash
php artisan sheaf:update component-name
```

**Examples:**

```bash
# Update a button component
php artisan sheaf:update button

# Update a modal component (may include dependencies update)
php artisan sheaf:update modal
```

### Removing Components
Removes one or more Sheaf UI components from your Laravel project, including their files, dependencies, and configuration entries.

#### usage
```bash
    php artisan sheaf:remove [name]*
```

#### Behavior
The `sheaf:remove` command removes Sheaf UI components from your project and cleans up all associated files and dependencies. The removal process includes:

1. Component verification: Checks if the component exists in resources/views/components/ui/ before attempting removal
2. File deletion: Removes component directories and files from the UI components directory
3. Dependency cleanup: Recursively removes internal dependencies (after confirmation) that are no longer used by any remaining components
4. Helper cleanup: Removes helper components that are no longer needed
5. Configuration updates: Updates both sheaf.json and sheaf-lock.json to reflect the removed components

The command intelligently tracks component usage across your project. If a removed component was the only one using a particular dependency or helper, those are automatically removed as well (after confirmation).

#### Examples
**Remove a single component**
```bash
php artisan sheaf:remove button
```

**Remove Multiple components**
```bash
php artisan sheaf:remove radio modal select card
```
## Discovering Components

### List Available Components

Browse all available components in the Sheaf UI library:

```bash
php artisan sheaf:list
```

### Filtering Options



### Getting Help

**Check Component Documentation**
- Online docs: `https://sheafui.dev/docs/components/{component-name}`

**Community Support**
- GitHub Issues: `https://github.com/sheafui/cli/issues`


### Best Practices

1. **Version Control**: Always commit your project before running Sheaf UI commands
2. **Component Organization**: Keep components in the default `ui/` namespace for consistency
3. **Customization**: Document your customizations for team members
4. **Dependencies**: Regularly update both Sheaf UI and its dependencies
5. **Testing**: Test components after installation in your specific use cases
