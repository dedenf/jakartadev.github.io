# Dark Theme Support Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Implement a three-state (Light, Dark, System) theme toggle for the JakartaDev blog with persistent storage and flash prevention.

**Architecture:** 
- Transition hardcoded colors to CSS Custom Properties.
- Use a blocking inline script in the head to apply themes before render.
- Implement a 3-state cycle toggle in the header.

**Tech Stack:** Jekyll (Liquid), Sass, Vanilla JavaScript.

---

### Task 1: Define CSS Variables

**Files:**
- Modify: `_sass/_variables.scss`

- [ ] **Step 1: Add CSS Variable definitions to `_sass/_variables.scss`**

Add the following at the beginning of the file (after imports):

```scss
:root {
  --bg-color: #ffffff;
  --text-color: #101820;
  --link-color: #00A0FF;
  --header-bg: #fafafa;
  --footer-bg: #fafafa;
  --border-color: #eeeeee;
  --code-bg: #fafafa;
  --code-text: #333333;
  --nav-link-color: #444444;
  --footer-text: #7a7a7a;
  --blockquote-bg: #ffffff;
}

[data-theme='dark'] {
  --bg-color: #121212;
  --text-color: #e0e0e0;
  --link-color: #4db8ff;
  --header-bg: #1a1a1a;
  --footer-bg: #1a1a1a;
  --border-color: #333333;
  --code-bg: #1e1e1e;
  --code-text: #d4d4d4;
  --nav-link-color: #bbbbbb;
  --footer-text: #999999;
  --blockquote-bg: #1e1e1e;
}

// System preference fallback
@media (prefers-color-scheme: dark) {
  :root:not([data-theme='light']) {
    --bg-color: #121212;
    --text-color: #e0e0e0;
    --link-color: #4db8ff;
    --header-bg: #1a1a1a;
    --footer-bg: #1a1a1a;
    --border-color: #333333;
    --code-bg: #1e1e1e;
    --code-text: #d4d4d4;
    --nav-link-color: #bbbbbb;
    --footer-text: #999999;
    --blockquote-bg: #1e1e1e;
  }
}
```

- [ ] **Step 2: Commit**

```bash
git add _sass/_variables.scss
git commit -m "feat: define core theme variables"
```

---

### Task 2: Apply Variables to Base Styles

**Files:**
- Modify: `_sass/basscss/_color-base.scss`

- [ ] **Step 1: Replace hardcoded colors in `_sass/basscss/_color-base.scss`**

```scss
body {
  color: var(--text-color);
  background-color: var(--bg-color);
  -moz-osx-font-smoothing: grayscale;
}

a {
  color: var(--link-color);
  text-decoration: none;
}

pre,
code {
  background-color: var(--code-bg);
  color: var(--code-text);
  border-radius: $border-radius;
}

hr {
  border: 0;
  border-bottom-style: solid;
  border-bottom-width: $border-width;
  border-bottom-color: var(--border-color);
}
```

- [ ] **Step 2: Commit**

```bash
git add _sass/basscss/_color-base.scss
git commit -m "feat: apply theme variables to base styles"
```

---

### Task 3: Apply Variables to Layout Components

**Files:**
- Modify: `_sass/_header.scss`
- Modify: `_sass/_footer.scss`
- Modify: `_sass/_blockquotes.scss`

- [ ] **Step 1: Update `_sass/_header.scss`**

Replace hardcoded values with variables:
`background-color: var(--header-bg);`
`border-top: thin solid var(--border-color);` (if applicable)

- [ ] **Step 2: Update `_sass/_footer.scss`**

Replace hardcoded values:
`background-color: var(--footer-bg);`
`color: var(--footer-text);`

- [ ] **Step 3: Update `_sass/_blockquotes.scss`**

`background-color: var(--blockquote-bg);`

- [ ] **Step 4: Commit**

```bash
git add _sass/_header.scss _sass/_footer.scss _sass/_blockquotes.scss
git commit -m "feat: apply theme variables to header, footer, and blockquotes"
```

---

### Task 4: Add Flash Prevention Script

**Files:**
- Modify: `_includes/head.html`

- [ ] **Step 1: Add initialization script to `_includes/head.html`**

Add this at the top of the `<head>` section, before any CSS loads:

```html
<script>
  (function() {
    try {
      var theme = localStorage.getItem('theme');
      var supportDarkMode = window.matchMedia('(prefers-color-scheme: dark)').matches === true;
      if (!theme && supportDarkMode) theme = 'system';
      if (!theme) theme = 'light';
      
      if (theme === 'dark') {
        document.documentElement.setAttribute('data-theme', 'dark');
      } else if (theme === 'light') {
        document.documentElement.setAttribute('data-theme', 'light');
      }
      // 'system' is handled by the media query in Task 1
    } catch (e) {}
  })();
</script>
```

- [ ] **Step 2: Commit**

```bash
git add _includes/head.html
git commit -m "feat: add theme initialization script to prevent white flash"
```

---

### Task 5: Add Toggle UI and Logic

**Files:**
- Modify: `_includes/header.html`
- Modify: `_layouts/default.html` (or create a new JS file)

- [ ] **Step 1: Add Toggle Button to `_includes/header.html`**

Insert after the navigation links:

```html
<button id="theme-toggle" class="nav-item p1" aria-label="Toggle theme">
  <span id="theme-toggle-icon"></span>
</button>
```

- [ ] **Step 2: Add Toggle Logic to `_layouts/default.html`**

Add before the closing `</body>` tag:

```html
<script>
  const toggleBtn = document.getElementById('theme-toggle');
  const iconContainer = document.getElementById('theme-toggle-icon');

  const themes = ['system', 'light', 'dark'];
  const icons = {
    system: '💻', // Or SVG
    light: '☀️',
    dark: '🌙'
  };

  function getTheme() {
    return localStorage.getItem('theme') || 'system';
  }

  function setTheme(theme) {
    if (theme === 'system') {
      document.documentElement.removeAttribute('data-theme');
    } else {
      document.documentElement.setAttribute('data-theme', theme);
    }
    localStorage.setItem('theme', theme);
    updateIcon(theme);
  }

  function updateIcon(theme) {
    iconContainer.textContent = icons[theme];
    toggleBtn.setAttribute('aria-label', `Toggle theme (current: ${theme})`);
  }

  toggleBtn.addEventListener('click', () => {
    const currentTheme = getTheme();
    const nextTheme = themes[(themes.indexOf(currentTheme) + 1) % themes.length];
    setTheme(nextTheme);
  });

  // Init icon
  updateIcon(getTheme());
</script>

<style>
  #theme-toggle {
    background: none;
    border: none;
    cursor: pointer;
    font-size: 1.2rem;
    padding: 0.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    color: var(--nav-link-color);
  }
</style>
```

- [ ] **Step 3: Commit**

```bash
git add _includes/header.html _layouts/default.html
git commit -m "feat: add theme toggle button and logic"
```
