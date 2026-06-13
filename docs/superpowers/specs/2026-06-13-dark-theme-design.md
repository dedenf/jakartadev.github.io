# Design Spec: Dark Theme Support for JakartaDev Blog

## 1. Overview
Implement a dark theme for the JakartaDev blog with a user-facing toggle that supports three states: Light, Dark, and System Preference.

## 2. Goals
- Provide a comfortable reading experience in low-light environments.
- Allow users to manually override or follow their system settings.
- Prevent "white flash" (Flash of Unstyled Content) on page load.
- Maintain consistency with the existing Pixyll theme aesthetics.

## 3. Architecture

### CSS Custom Properties
Transition core colors from Sass variables to CSS Custom Properties.

```css
:root {
  --bg-color: #ffffff;
  --text-color: #101820;
  --link-color: #00A0FF;
  --header-bg: #fafafa;
  --footer-bg: #fafafa;
  --border-color: #eee;
  --code-bg: #f5f5f5;
  --code-text: #333;
}

[data-theme='dark'] {
  --bg-color: #121212;
  --text-color: #e0e0e0;
  --link-color: #00A0FF;
  --header-bg: #1a1a1a;
  --footer-bg: #1a1a1a;
  --border-color: #333;
  --code-bg: #1e1e1e;
  --code-text: #d4d4d4;
}
```

### Flash Prevention (Head Script)
An inline script in `_includes/head.html` to apply the theme before rendering.

```javascript
(function() {
  const theme = localStorage.getItem('theme') || 'system';
  if (theme !== 'system') {
    document.documentElement.setAttribute('data-theme', theme);
  }
})();
```

## 4. Components

### Theme Toggle Button
- **Location:** `_includes/header.html` (end of nav)
- **States:** `system` (default) -> `light` -> `dark` -> `system`
- **Icon:** SVG changing based on state.
- **Behavior:** Cycle through states on click, updating `localStorage` and `data-theme` attribute.

## 5. Implementation Steps
1. **Variable Definition:** Add `:root` and `[data-theme]` blocks to `_sass/_variables.scss`.
2. **Style Updates:** Replace hardcoded colors in `_sass/basscss/_color-base.scss`, `_sass/_header.scss`, `_sass/_footer.scss`, etc.
3. **Initialization:** Add the blocking script to `_includes/head.html`.
4. **Toggle Component:** Add the button and icons to `_includes/header.html`.
5. **Interactive Script:** Add the toggle handling script to `_layouts/default.html` or a separate JS file.

## 6. Testing
- **System Detection:** Set OS to dark/light and check blog (when in 'system' mode).
- **Persistence:** Switch theme, refresh page, verify it stays.
- **Visuals:** Ensure text contrast is sufficient and links are readable.
- **Flash Test:** Throttle network/CPU to ensure no white flash on load.
