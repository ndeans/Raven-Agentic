# Feature Specifications

## WP-13: UI Modifications (2026-05-17)

### 1. Scrollable Tables

**Scope:** All view pages (`view-uploads.xhtml`, `view-conversations.xhtml`, `view-conversation.xhtml`)

**Approach:** Wrap each `<h:dataTable>` / `<ui:repeat>` table in a `<div>` with `overflow-y: auto` and `max-height: 80vh`. The table itself remains full-width inside the wrapper.

**CSS to add to `style1.css`:**
```css
.table-scroll {
    overflow-y: auto;
    max-height: 80vh;
}
```

**Markup change (each view page):** Wrap the existing `<table>` element in:
```html
<div class="table-scroll">
    <!-- existing table -->
</div>
```

**Sticky headers:** Table headers (`<th>`) should remain visible while scrolling. Add to `style1.css`:
```css
.table-scroll th {
    position: sticky;
    top: 0;
    z-index: 1;
}
```

---

### 2. Color Mode System

#### 2a. CSS Changes (`style1.css`)

Apply the mode-independent overrides unconditionally (as specified in Ideas.md). Add light and dark overrides scoped to a body class modifier:

```css
/* --- Mode-independent --- */
table {
    border-spacing: 1px;
    background-color: #454130; 
}
th {
    background-color: #ebebe3;
    text-align: left;
    font-weight: 600;
}
td {
	font-family: sans-serif; 
    font-size: 10pt;
}
.btn-upload {
    background-color: goldenrod;
    border: 1px solid darkgrey;
}
.btn-dialogs {
    background-color: #16d96d;
    border: 1px solid darkgrey;
}

/* --- Light mode (default) --- */
body {
    background-color: floralwhite;
}
th { 
	color: #ffdf5d;
	background-color: grey;
}
td {
    color: black;
    background-color: floralwhite;
}
h1 {
    color: black;
}

/* --- Dark mode --- */
body.dark {
    background-color: #2f3533;
}
body.dark th { 
	color: #ffdf5d;
	background-color: grey;
}
body.dark td {
    color: #ffdf5d;
    background-color: #2f3533; 
}
body.dark h1 {
    color: cornsilk;
}
```

#### 2b. Client-Side Toggle (JavaScript)

A small inline script block (included in the JSF template or each page's `<h:head>`) reads the stored preference from `localStorage` on page load and applies the `dark` class to `<body>` before first paint, preventing a flash of the wrong mode.

```javascript
(function() {
    if (localStorage.getItem('ravenColorMode') === 'dark') {
        document.body.classList.add('dark');
    }
})();
```

The toggle function (called by the hamburger menu switch):
```javascript
function toggleColorMode() {
    var isDark = document.body.classList.toggle('dark');
    localStorage.setItem('ravenColorMode', isDark ? 'dark' : 'light');
}
```

#### 2c. Hamburger Menu (`view-uploads.xhtml`)

A hamburger button is added to the upper-right of the main page, above the data table. Clicking it opens a dropdown menu containing two items: an About link and the Light/Dark toggle switch.

**Markup (added to `view-uploads.xhtml`):**
```html
<div class="menu-container">
    <button class="hamburger" onclick="toggleMenu()">&#9776;</button>
    <div id="dropdown-menu" class="dropdown hidden">
        <a href="about.xhtml">About</a>
        <label class="toggle-label">
            Dark Mode
            <input type="checkbox" id="color-mode-toggle" onclick="toggleColorMode()"/>
        </label>
    </div>
</div>
```

The checkbox is initialised to reflect the current mode on page load (added to the inline script):
```javascript
document.addEventListener('DOMContentLoaded', function() {
    var toggle = document.getElementById('color-mode-toggle');
    if (toggle) toggle.checked = (localStorage.getItem('ravenColorMode') === 'dark');
});
```

Menu open/close:
```javascript
function toggleMenu() {
    document.getElementById('dropdown-menu').classList.toggle('hidden');
}
```

**CSS for hamburger and dropdown (added to `style1.css`):**
```css
.menu-container {
    position: absolute;
    top: 1em;
    right: 1em;
}
.hamburger {
    font-size: 1.5em;
    background: none;
    border: none;
    cursor: pointer;
}
.dropdown {
    position: absolute;
    right: 0;
    background-color: #ebebe3;
    border: 1px solid darkgrey;
    padding: 0.5em 1em;
    min-width: 160px;
    z-index: 10;
}
.dropdown a, .toggle-label {
    display: block;
    padding: 0.4em 0;
    color: black;
    text-decoration: none;
}
.hidden {
    display: none;
}
```

#### 2d. About Page

A minimal `about.xhtml` page is required as the target of the About link. Content TBD — placeholder page sufficient for now.

#### 2e. Persistence

User preference is stored in browser `localStorage` under the key `ravenColorMode` with values `'light'` or `'dark'`. No server-side storage required. Preference persists across sessions until the user changes it or clears browser storage.

---

### Files to be Modified

| File | Change |
|---|---|
| `Raven-Jakarta-Web/src/main/webapp/css/style1.css` | Add scrollable table CSS, color mode CSS, hamburger/dropdown CSS |
| `Raven-Jakarta-Web/src/main/webapp/view-uploads.xhtml` | Add table scroll wrapper, hamburger menu markup, inline JS |
| `Raven-Jakarta-Web/src/main/webapp/view-conversations.xhtml` | Add table scroll wrapper |
| `Raven-Jakarta-Web/src/main/webapp/view-conversation.xhtml` | Add table scroll wrapper |
| `Raven-Jakarta-Web/src/main/webapp/about.xhtml` | New page (placeholder) |


