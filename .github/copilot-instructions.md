# GPA (ProTab Studio) - Copilot Coding Agent Instructions

## Repository Overview

**Project Name:** GPA - ProTab Studio | Professional Edition  
**Type:** Single-page web application  
**Primary File:** `index.html` (3,531 lines, ~107KB)  
**Languages:** HTML, CSS, JavaScript  
**Architecture:** Single-file solution with no build system, no backend, no bundler  
**License:** MIT

This is a professional guitar tablature and score viewer with advanced music analysis capabilities. Everything (HTML structure, CSS styles, JavaScript logic) is contained in one file: `index.html`.

## Key Architecture Principles

### Single-File Solution
- **ALL code lives in `index.html`** - there are no separate JS, CSS, or build files
- The application is purely static and can be served directly via any web server or opened locally
- No compilation, transpilation, or bundling is required
- No package.json, no npm dependencies, no node_modules

### External Dependencies (CDN-loaded)
- **AlphaTab** v1.3.0: Guitar tab/score rendering engine (loaded from jsDelivr CDN)
- **Font Awesome 6.4.0**: Icon library (loaded from CDN)
- **Google Fonts**: Inter and JetBrains Mono fonts
- **SoundFont**: sonivox.sf2 for audio playback (loaded from CDN)

### Logical Agent Structure
The code is organized into functional "agents" (documented in `AGENTS.md`):
1. **File & Score Agent** - File upload, drag & drop, score loading (`.drop-zone`, `#at-container`)
2. **Playback & Transport Agent** - Play/pause/stop controls (`.transport-controls`, `.btn-transport`)
3. **Track Management Agent** - Individual track mute/solo controls (`.track-list`, `.track-item`)
4. **Analysis & Theory Agent** - Musical analysis, key detection, pitch histograms (`.analysis-dashboard`)
5. **UI & Theme Agent** - Theme switching, sidebar toggle, responsive layout (`data-theme`, `.sidebar-collapsed`)

## File Structure

```
/
├── index.html          # Single-file application (ALL code here)
├── AGENTS.md          # Architecture documentation (agent descriptions)
├── README.md          # Minimal readme
└── LICENSE            # MIT License
```

**Note:** No `.github/workflows/`, no test files, no build scripts, no configuration files exist.

## Build & Validation

### There Is No Build Process
This application requires **NO building, compiling, or transpilation**.

### Running the Application

**Method 1: Python HTTP Server (Recommended for testing)**
```bash
cd /home/runner/work/GPA/GPA
python3 -m http.server 8080
# Access at http://localhost:8080/index.html
```

**Method 2: Node.js HTTP Server**
```bash
cd /home/runner/work/GPA/GPA
npx http-server -p 8080
# Access at http://localhost:8080/index.html
```

**Method 3: Direct File Access**
```bash
# Simply open in browser (may have CORS limitations with CDN resources)
xdg-open index.html  # Linux
open index.html      # macOS
start index.html     # Windows
```

**Success Criteria:** 
- The page loads without console errors
- The drop zone is visible with the message "Drop File Here or click to browse"
- Theme toggle works (moon/sun icon in header)
- No 404 errors for Font Awesome or Google Fonts

### Validation Steps

**1. HTML Syntax Validation**
```bash
python3 -c "from html.parser import HTMLParser; parser = HTMLParser(); data = open('index.html').read(); parser.feed(data); print('HTML is syntactically valid')"
```
Expected output: `HTML is syntactically valid`

**2. Check File Size and Line Count**
```bash
wc -l index.html && du -h index.html
```
Expected output: ~3531 lines, ~107KB

**3. Verify External Dependencies are Reachable**
```bash
curl -I https://cdn.jsdelivr.net/npm/@coderline/alphatab@1.3.0/dist/alphaTab.min.js
curl -I https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css
```
Expected: HTTP 200 responses (note: may fail in restricted environments)

### Testing

**There are NO automated tests** in this repository.

Manual testing approach:
1. Start a local web server (see "Running the Application" above)
2. Open the application in a browser
3. Test core functionality:
   - Drop zone accepts files (though you need actual .gp/.gpx files to test fully)
   - Theme toggle switches between dark and light modes
   - Sidebar can collapse/expand
   - Transport controls are visible
   - Analysis dashboard opens when icon is clicked

### Linting

**There are NO linters configured** (no ESLint, Prettier, or other tools).

Code style conventions (inferred from existing code):
- Indentation: 2 spaces
- JavaScript: ES6+ class syntax, camelCase variables
- CSS: BEM-like naming for components (e.g., `.btn-transport`, `.track-item`)
- Comments: Section headers use `/* ===== Section Name ===== */` format

## Making Changes

### Critical Rules for Editing
1. **ONLY edit `index.html`** - this is the entire application
2. **Preserve the single-file architecture** - do not extract JS/CSS into separate files
3. **Maintain agent boundaries** - when adding features, identify which agent owns it (see `AGENTS.md`)
4. **Test in a browser** - always verify changes by running a local server and opening in browser
5. **Watch for CDN dependencies** - if external resources change, update all references (CDN URLs appear in multiple places)

### Common Change Patterns

**Adding a new UI feature:**
1. Add HTML markup in the appropriate section (search for section comments like `<!-- Header -->`)
2. Add CSS in the corresponding style section (search for `/* ===== Section Name ===== */`)
3. Add JavaScript in the appropriate agent section or add event listeners in `ProTabApp` class (starts at line ~2117)
4. Test by running the application

**Modifying themes:**
- Dark theme variables: `:root { ... }` (starts at line ~18)
- Light theme overrides: `[data-theme="light"] { ... }` (starts at line ~65)

**Adding keyboard shortcuts:**
- Find the keyboard event handler in `ProTabApp.setupEventListeners()` (search for `keydown` event)

**Modifying AlphaTab integration:**
- AlphaTab API configuration is in `ProTabApp.initializeAlphaTab()` (search for `soundFont:`)
- Renderer settings are in the AlphaTab settings object

### After Making Changes

1. **Save `index.html`**
2. **Reload the page in your browser** (hard refresh: Ctrl+Shift+R / Cmd+Shift+R)
3. **Check browser console** for JavaScript errors (F12 → Console tab)
4. **Verify the UI** looks correct and functionality works
5. **Test in both dark and light themes** (click theme toggle button)

### Common Issues & Workarounds

**AlphaTab fails to load:**
- The script includes fallback CDN URLs (lines ~1762-1764)
- If one CDN is down, the script automatically tries alternatives
- Check browser console for specific error messages

**CORS errors when testing locally:**
- Always use an HTTP server (python3 -m http.server), never file:// protocol
- CDN resources require HTTP/HTTPS protocol to load correctly

**Changes not reflecting:**
- Clear browser cache (Ctrl+Shift+Delete)
- Do a hard refresh (Ctrl+Shift+R)
- Check if you edited the correct file (there is only one: `index.html`)

## Git Workflow

### Checking Status
```bash
git --no-pager status
git --no-pager diff
```

### Committing Changes
```bash
git add index.html
git commit -m "Brief description of changes"
git push
```

**Note:** If you create any new files (documentation, etc.), ensure they are intentional. This repo should remain minimal.

## CI/CD & Deployment

**There are NO CI/CD pipelines, GitHub Actions, or automated checks configured.**

Deployment options:
- **GitHub Pages:** Can serve `index.html` directly from the repository
- **Netlify/Vercel:** Drop deployment - just upload the repository
- **Any static host:** Upload `index.html` and serve it

## Key Code Locations

### JavaScript Classes (all in `index.html`)
- `StorageManager` (line ~1820): localStorage utilities
- `ToastManager` (line ~1869): notification system
- `ScoreAnalyzer` (line ~1942): music analysis logic
- `ProTabApp` (line ~2117): main application class

### CSS Sections (all in `index.html` <style> tag)
- Line ~17: CSS Variables & Theming
- Line ~78: Base Styles  
- Line ~126: Header
- Line ~203: Sidebar
- Line ~278: File Upload
- Line ~360: Track List
- Line ~483: Score Area
- Line ~554: Transport Controls
- Line ~737: Analysis Dashboard
- Line ~1154: Responsive Design

### HTML Structure (in `index.html` <body> tag)
- Line ~1402: Header with logo and buttons
- Line ~1433: Main layout container
- Line ~1435: Sidebar (file upload, TEX editor, track list)
- Line ~1523: Score area (`#at-container` - AlphaTab renders here)
- Line ~1530: Transport controls
- Line ~1585: Analysis dashboard (overlay)
- Line ~1658: Settings panel (overlay)

## Important Notes

1. **Trust these instructions** - this is a unique single-file architecture. Don't search for build tools that don't exist.

2. **No dependencies to install** - if you think you need to run `npm install` or `pip install`, you don't. Everything loads from CDN.

3. **Browser is the test environment** - there are no unit tests. Manual browser testing is the validation method.

4. **AGENTS.md is your architecture guide** - read it before making structural changes to understand the agent boundaries.

5. **Line numbers may shift** - use comments and class names to find code sections rather than relying on line numbers.

6. **External resources may be blocked** - if working in a restricted network environment, AlphaTab and Font Awesome may not load. This is environmental, not a code issue.

7. **No .gitignore exists** - be careful not to commit temporary files, build artifacts, or IDE configs. Add a .gitignore if needed.

## Quick Reference Commands

```bash
# Navigate to repository
cd /home/runner/work/GPA/GPA

# Start test server
python3 -m http.server 8080

# Validate HTML syntax
python3 -c "from html.parser import HTMLParser; parser = HTMLParser(); parser.feed(open('index.html').read()); print('Valid')"

# Check file stats
wc -l index.html

# View git status and diff
git --no-pager status && git --no-pager diff

# Commit and push changes
git add index.html && git commit -m "Description" && git push
```

## Summary

This is an intentionally minimal, dependency-free, single-file web application. When coding for this repo:
- Edit ONLY `index.html`
- Test by opening in a browser via HTTP server
- No build, no tests, no CI/CD pipelines
- Refer to `AGENTS.md` for architecture understanding
- Focus on surgical, minimal changes
- Always validate in a browser after changes
