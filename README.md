# ✅ To-Do List App

A lightweight, browser-based task management application built with vanilla HTML, CSS, and JavaScript. Tasks persist across sessions using the browser's `localStorage` API — no backend or database required.

---

## 📋 Table of Contents

- [Demo](#demo)
- [Features](#features)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Usage Guide](#usage-guide)
- [Code Overview](#code-overview)
  - [HTML](#html-todolisthtml)
  - [CSS](#css-todolistcss)
  - [JavaScript](#javascript-todolistjs)
- [Data Persistence](#data-persistence)
- [Customization](#customization)
- [Browser Compatibility](#browser-compatibility)
- [Known Limitations](#known-limitations)
- [Future Improvements](#future-improvements)
- [License](#license)

---

## Demo

> Open `ToDoList.html` directly in any modern browser — no server or build step needed.

---

## Features

| Feature | Description |
|---|---|
| ➕ Add Tasks | Type a task and click **Add Task** (or press Enter) to append it to the list |
| ✅ Mark Complete | Click a task to toggle a strikethrough "checked" state |
| ❌ Delete Tasks | Click the × icon on any task to remove it instantly |
| 💾 Persistent Storage | All tasks are saved to `localStorage` and restored on page reload |
| 📱 Responsive Design | Fluid layout that works on desktop and mobile screens |

---

## Project Structure

```
project/
├── ToDoList.html   # Application markup and entry point
├── ToDoList.css    # Styles, layout, and visual states
└── ToDoList.js     # Application logic and localStorage handling
```

All three files are co-located and linked by relative paths — no bundler, package manager, or build tool is required.

---

## Getting Started

### Prerequisites

- Any modern web browser (Chrome, Firefox, Edge, Safari)
- No Node.js, npm, or server setup needed

### Running the App

1. **Clone or download** the repository to your local machine.
2. Ensure all three files (`ToDoList.html`, `ToDoList.css`, `ToDoList.js`) are in the **same directory**.
3. Open `ToDoList.html` in your browser:
   - Double-click the file in your file explorer, **or**
   - Right-click → *Open With* → your preferred browser, **or**
   - Drag and drop the file into a browser window.

The app loads immediately — no internet connection is required after the initial page load (the Google Fonts stylesheet is fetched from a CDN on first load).

---

## Usage Guide

### Adding a Task

1. Click the input field labelled **"Write Task"**.
2. Type your task description.
3. Click the **Add Task** button.

> If the input field is empty when you click **Add Task**, an alert will prompt you to enter something.

### Completing a Task

- Click anywhere on the task text to toggle it between **active** and **completed** states.
- Completed tasks are visually distinguished by a grey colour and a strikethrough line.
- Click again to un-complete the task.

### Deleting a Task

- Click the **×** symbol on the right side of any task to permanently remove it.
- This action cannot be undone.

### Persistence

- All tasks (including their checked/unchecked state) are automatically saved whenever you add, complete, or delete a task.
- Refreshing or closing and reopening the browser tab will restore all previously saved tasks.

---

## Code Overview

### HTML (`ToDoList.html`)

```
body
└── div.container          ← Full-viewport gradient background
    └── div.todo-app       ← Centered white card
        ├── h2             ← App title: "To-Do List"
        ├── div.row        ← Input + button row
        │   ├── input#input-box    ← Text field for new tasks
        │   └── button             ← "Add Task" trigger (onclick="addTask()")
        └── ul#list-container      ← Dynamically populated task list
```

- The `<ul id="list-container">` starts empty; all `<li>` elements are injected by JavaScript at runtime.
- The `<script>` tag is placed at the bottom of `<body>` to ensure the DOM is fully loaded before the script executes.

---

### CSS (`ToDoList.css`)

#### Layout

- **`.container`** — Full-viewport wrapper with a diagonal purple/indigo gradient (`linear-gradient(135deg, #153677, #4e085f)`).
- **`.todo-app`** — Centred white card (`max-width: 540px`) with rounded corners and generous padding.
- **`.row`** — Flexbox row holding the input and button side by side on a light grey (`#edeef0`) pill-shaped background.

#### Interactive States

| Selector | Purpose |
|---|---|
| `ul li` | Base task item — padded, cursor pointer, relative positioning for pseudo-elements |
| `ul li::before` | Circular bullet indicator on the left of each task |
| `ul li.checked` | Applied by JS on click — grey text + `text-decoration: line-through` |
| `ul li span` | The × delete icon, absolutely positioned to the right |
| `ul li span:hover` | Subtle grey circle background on hover for affordance |

#### Typography

- Uses the **Poppins** font family (loaded via Google Fonts CDN, declared in the `@import` or link in HTML).
- Base task font size is `17px`; button is `16px`.

---

### JavaScript (`ToDoList.js`)

#### Key Variables

```javascript
const inputBox       = document.getElementById("input-box");
const listContainer  = document.getElementById("list-container");
```

#### `addTask()`

Called by the **Add Task** button's `onclick` handler.

```
1. Guard: if inputBox is empty → alert the user and return.
2. Create a new <li> element.
3. Set its innerHTML to the current value of inputBox.
4. Append the <li> to listContainer.
5. Create a <span> element containing the × character (Unicode U+00D7).
6. Append the <span> inside the <li>.
7. Clear inputBox.
8. Call saveData() to persist the updated list.
```

#### Event Delegation on `listContainer`

A single `"click"` listener on the `<ul>` handles both task toggling and deletion via event delegation:

```javascript
listContainer.addEventListener("click", function(e) {
    if (e.target.tagName === "LI") {
        e.target.classList.toggle("checked"); // Toggle completion state
        saveData();
    } else if (e.target.tagName === "SPAN") {
        e.target.parentElement.remove();      // Delete the parent <li>
        saveData();
    }
});
```

This pattern is efficient because only one listener is registered regardless of how many tasks exist.

#### `saveData()`

```javascript
function saveData() {
    localStorage.setItem("data", listContainer.innerHTML);
}
```

Serialises the entire inner HTML of the task list and stores it under the key `"data"` in `localStorage`.

#### `showTask()`

```javascript
function showTask() {
    listContainer.innerHTML = localStorage.getItem("data");
}
showTask(); // Called immediately on script load
```

Restores the task list from `localStorage` when the page loads. If no data exists, `localStorage.getItem` returns `null`, and setting `innerHTML` to `null` renders the string `"null"` — see [Known Limitations](#known-limitations).

---

## Data Persistence

The app uses the browser's **`localStorage` API** — a synchronous key-value store available in all modern browsers.

| Property | Detail |
|---|---|
| **Storage key** | `"data"` |
| **Value format** | Raw HTML string (the `innerHTML` of `<ul#list-container>`) |
| **Scope** | Origin-scoped (tied to the file path or domain) |
| **Capacity** | ~5 MB per origin (more than sufficient for task lists) |
| **Persistence** | Survives page refreshes and browser restarts; cleared by the user or `localStorage.clear()` |

> ⚠️ Because tasks are stored as raw HTML, the app is susceptible to XSS if task text contains HTML tags. See [Known Limitations](#known-limitations).

---

## Customization

### Change the Background Gradient

In `ToDoList.css`, edit the `.container` rule:

```css
.container {
    background: linear-gradient(135deg, #153677, #4e085f); /* change these colours */
}
```

### Change the Button Colour

```css
button {
    background: #ff5945; /* replace with any hex/rgb value */
}
```

### Change the App Card Width

```css
.todo-app {
    max-width: 540px; /* increase or decrease as needed */
}
```

### Add Enter-Key Support

In `ToDoList.js`, add the following after the variable declarations:

```javascript
inputBox.addEventListener("keydown", function(e) {
    if (e.key === "Enter") addTask();
});
```

---

## Browser Compatibility

| Browser | Supported |
|---|---|
| Chrome 60+ | ✅ |
| Firefox 55+ | ✅ |
| Edge 79+ | ✅ |
| Safari 11+ | ✅ |
| Internet Explorer | ❌ (no CSS Grid/Flexbox + localStorage quirks) |

---

## Known Limitations

1. **No Enter-key support** — Submitting a task requires clicking the button; pressing Enter does nothing by default.
2. **Raw HTML storage (XSS risk)** — Task content is saved and restored as raw HTML. A user who types `<img src=x onerror=alert(1)>` as a task will execute that script on reload. For a local personal-use app this is low-risk, but it should be addressed before any networked or multi-user deployment.
3. **`null` render on first load** — If `localStorage` has no `"data"` key, `listContainer.innerHTML = null` renders the literal text `"null"` in some browsers. Fix:
   ```javascript
   function showTask() {
       listContainer.innerHTML = localStorage.getItem("data") || "";
   }
   ```
4. **No edit functionality** — Tasks cannot be edited after being added; they must be deleted and re-added.
5. **No task ordering** — Tasks cannot be reordered (e.g., via drag-and-drop).
6. **Single list only** — The app supports one global list; there is no concept of categories, projects, or multiple lists.

---

## Future Improvements

- [ ] **Enter-key submission** for faster task entry
- [ ] **Inline task editing** — double-click a task to edit its text in place
- [ ] **Due dates & priorities** — attach a date and a priority level to each task
- [ ] **Drag-and-drop reordering** using the HTML5 Drag and Drop API or a library like SortableJS
- [ ] **Multiple lists / categories** for organising tasks by project
- [ ] **JSON-based storage** — replace raw HTML serialisation with structured JSON for safety and flexibility
- [ ] **Undo delete** — a brief toast notification with an Undo action after deletion
- [ ] **Task counter** — display pending vs. completed task counts in the header
- [ ] **Dark mode** — a toggle that switches between light and dark themes

---

## License

This project is open source and available under the [MIT License](LICENSE).
