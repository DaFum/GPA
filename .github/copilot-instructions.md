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

**Success:** Page loads without errors, drop zone visible, theme toggle works, no 404s for fonts/icons.

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

**3. Verify External CDNs (optional)**
```bash
curl -I https://cdn.jsdelivr.net/npm/@coderline/alphatab@1.3.0/dist/alphaTab.min.js
```
Expected: HTTP 200 (may fail in restricted environments)

### Testing

**NO automated tests exist.** Manual testing: Start server, open in browser, verify drop zone, theme toggle, sidebar collapse, transport controls, and analysis dashboard.

### Linting

**NO linters configured.** Style: 2-space indent, ES6+ classes, camelCase, BEM-like CSS naming, section headers use `/* ===== Name ===== */`.

## Making Changes

### Critical Rules for Editing
1. **ONLY edit `index.html`** - this is the entire application
2. **Preserve the single-file architecture** - do not extract JS/CSS into separate files
3. **Maintain agent boundaries** - when adding features, identify which agent owns it (see `AGENTS.md`)
4. **Test in a browser** - always verify changes by running a local server and opening in browser
5. **Watch for CDN dependencies** - if external resources change, update all references (CDN URLs appear in multiple places)

### Common Change Patterns

**Adding UI features:** Add HTML (search `<!-- Section -->`), CSS (search `/* ===== Section ===== */`), JS in `ProTabApp` class (line ~2117), then test.

**Modifying themes:** Dark theme at `:root` (line ~18), light theme at `[data-theme="light"]` (line ~65).

**Keyboard shortcuts:** Find `keydown` event handler in `ProTabApp.setupEventListeners()`.

**AlphaTab changes:** Configuration in `ProTabApp.initializeAlphaTab()` (search `soundFont:`).

### After Making Changes

1. Save `index.html` and reload browser (Ctrl+Shift+R / Cmd+Shift+R)
2. Check browser console for errors (F12 → Console)
3. Verify UI and test both dark/light themes

### Common Issues & Workarounds

**AlphaTab fails:** Script has fallback CDN URLs (lines ~1762-1764). Check browser console.
**CORS errors:** Use HTTP server (python3 -m http.server), never file:// protocol.
**Changes not reflecting:** Hard refresh (Ctrl+Shift+R) or clear browser cache.

## Git Workflow

```bash
git --no-pager status && git --no-pager diff  # Check changes
git add index.html && git commit -m "Description" && git push  # Commit
```
**Note:** Keep repo minimal. Only commit intentional files.

## CI/CD & Deployment

**NO CI/CD pipelines or automated checks configured.**

Deploy to: GitHub Pages, Netlify, Vercel, or any static host (just upload `index.html`).

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

1. **Trust these instructions** - unique single-file architecture. Don't search for build tools that don't exist.
2. **No dependencies to install** - everything loads from CDN.
3. **Browser is the test environment** - no unit tests, manual browser testing only.
4. **AGENTS.md is your architecture guide** - read before structural changes.
5. **Line numbers may shift** - use comments/class names to find code.
6. **External resources may be blocked** - CDN issues are environmental, not code problems.
7. **No .gitignore exists** - don't commit temp files or IDE configs.

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
