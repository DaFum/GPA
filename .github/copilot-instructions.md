# Copilot Instructions for GPA - ProTab Studio

## Repository Overview

**GPA (ProTab Studio | Professional Edition)** is a single-file static web application for viewing and analyzing guitar tablature and musical scores. The entire application is contained in `index.html` (3,226 lines) with no build system, backend, or bundler required.

- **Type:** Single-page web application (SPA)
- **Technology:** Pure HTML5, CSS3, JavaScript (ES6+)
- **Size:** ~96KB single file
- **Dependencies:** External CDN libraries only (no npm, no package.json)
- **Target Runtime:** Modern web browsers
- **License:** MIT

## Architecture & File Structure

### Repository Files
```
/
├── .github/
│   └── agents/
│       └── Cleaner.md          # Custom agent configuration
├── index.html                   # Complete application (HTML + CSS + JS)
├── AGENTS.md                    # Architecture documentation (German)
├── README.md                    # Minimal readme (5 bytes)
└── LICENSE                      # MIT License
```

### Single-File Architecture

`index.html` contains three major sections:

1. **CSS Styles (lines 16-1378)**: Complete styling with CSS variables for dark/light themes
2. **HTML Structure (lines 1380-1728)**: Semantic markup with accessibility features
3. **JavaScript Application (lines 1729-3226)**: Complete application logic

### Key UI Components

- **Sidebar** (`.sidebar`): File upload, TEX editor, track list, analysis controls
- **Score Display** (`#at-container`): AlphaTab rendering container
- **Transport Controls** (`.transport-controls`): Playback, tempo, volume controls
- **Analysis Dashboard** (`.analysis-dashboard`): Music theory analysis overlay
- **Settings Panel** (`.settings-panel`): Application configuration

### JavaScript Architecture (Agent Pattern)

The application is organized into logical "agents" as documented in `AGENTS.md`:

1. **File & Score Agent**: Handles file upload, drag-and-drop, and score loading
2. **Playback & Transport Agent**: Controls playback (play, pause, stop, seek)
3. **Track Management Agent**: Manages individual instrument tracks (mute/solo)
4. **Analysis & Theory Agent**: Performs music analysis (key detection, pitch histogram, complexity)
5. **UI & Theme Agent**: Manages themes, sidebar, responsive layout

Main classes:
- `ProTabApp` (line 2092): Main application controller
- `ScoreAnalyzer` (line 1917): Music theory analysis engine
- `StorageManager` (line 1795): LocalStorage helper
- `ToastManager` (line 1844): Notification system

### External Dependencies (CDN)

- **AlphaTab v1.3.0**: Music notation rendering engine
  - Primary: `https://cdn.jsdelivr.net/npm/@coderline/alphatab@1.3.0/dist/alphaTab.min.js`
  - Fallbacks: unpkg.com, ga.jspm.io, cdn.skypack.dev
- **Google Fonts**: Inter (UI), JetBrains Mono (monospace)
- **Font Awesome 6.4.0**: Icon library
- **SoundFont**: sonivox.sf2 from cdn.jsdelivr.net

## Build, Test, and Validation

### No Build System Required

**This project has NO build, compile, or bundle steps.** The application is ready to use as-is.

### Testing/Running the Application

**To test changes locally:**

```bash
# Start a simple HTTP server (choose one)
python3 -m http.server 8080
# OR
python -m http.server 8080

# Then open: http://localhost:8080/index.html
```

**Why a server is needed:** Modern browsers block local file loading of external resources due to CORS policies. A local HTTP server is required to test the application with its CDN dependencies.

### Validation Steps

1. **HTML Validation**:
   ```bash
   # Python HTML parser (built-in validation)
   python3 -c "from html.parser import HTMLParser; p = HTMLParser(); p.feed(open('index.html').read()); print('Valid HTML')"
   ```

2. **JavaScript Syntax Check** (optional, Node.js required):
   ```bash
   # Extract and check JS syntax (if Node.js is available)
   node --check <(sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d')
   ```

3. **Manual Browser Testing**:
   - Open in Chrome/Firefox/Safari
   - Check browser console for errors (F12)
   - Test file upload with a .gp or .xml file
   - Verify playback controls work
   - Check theme toggle (dark/light)

### No CI/CD Workflows

**There are NO GitHub Actions workflows or automated CI/CD pipelines configured.** Changes should be manually validated before committing.

## Making Changes

### Code Modification Guidelines

1. **Preserve Single-File Architecture**: All changes must be in `index.html`
2. **Maintain Agent Separation**: Keep logical separation between agents (comment sections clearly)
3. **CSS Variables**: Use existing CSS variables (`--primary`, `--bg-app`, etc.) for consistency
4. **Responsive Design**: Test changes on mobile (sidebar collapses at narrow widths)
5. **Accessibility**: Maintain ARIA labels and semantic HTML
6. **Theme Support**: Ensure changes work in both dark and light themes

### Common Modification Patterns

**Adding a new feature:**
- Determine which agent owns the feature
- Add HTML elements in appropriate section
- Add CSS styles following existing patterns
- Add JavaScript logic to the relevant class/agent
- Update event bindings in `bindEvents()` method

**Styling changes:**
- Modify CSS variables in `:root` or `[data-theme="light"]`
- Follow BEM-like naming conventions (`.component-element`)
- Use existing utility classes where possible

**JavaScript changes:**
- Use ES6+ syntax (classes, arrow functions, template literals)
- Follow existing error handling patterns
- Use `this.toast.show()` for user notifications
- Use `this.storage` for persistent data

### Testing Changes

**ALWAYS test changes by:**
1. Starting local HTTP server: `python3 -m http.server 8080`
2. Opening `http://localhost:8080/index.html` in browser
3. Opening browser DevTools (F12) to check for errors
4. Testing the modified functionality
5. Testing in both dark and light themes
6. Testing responsive behavior (resize window or use device emulation)

**Common issues:**
- **CORS errors**: Must use HTTP server, not `file://` protocol
- **AlphaTab not loading**: Check browser console, may need fallback CDN
- **Layout breaks**: Check CSS conflicts or missing closing tags
- **JS errors**: Check browser console for syntax or runtime errors

## Important Notes

### What NOT to Do

- ❌ Do NOT create package.json or add npm dependencies
- ❌ Do NOT add build tools (webpack, vite, rollup, etc.)
- ❌ Do NOT split the file into modules (must remain single-file)
- ❌ Do NOT add server-side code or API endpoints
- ❌ Do NOT modify .github/agents/ directory (contains agent configurations)

### What to Do

- ✅ Make all changes directly in `index.html`
- ✅ Test locally with HTTP server before committing
- ✅ Maintain the single-file architecture
- ✅ Follow existing code patterns and style
- ✅ Document complex changes with inline comments
- ✅ Update AGENTS.md if adding new logical components

### Performance Considerations

- Application loads from CDN dependencies (requires internet)
- AlphaTab library is ~2MB (loaded on demand)
- SoundFont is ~25MB (loaded only when playing audio)
- Initial page load is fast (~96KB HTML)

### Browser Compatibility

Targets modern browsers (ES6+ required):
- Chrome/Edge 90+
- Firefox 88+
- Safari 14+

Does NOT support IE11 or older browsers.

## File Permissions & Access

The `.github/agents/` directory contains configuration for custom cleaning agents. Do NOT read or modify these files unless specifically instructed to work on agent configuration.

## Quick Reference Commands

```bash
# Validate HTML
python3 -c "from html.parser import HTMLParser; HTMLParser().feed(open('index.html').read())"

# Start local server
python3 -m http.server 8080

# Count lines
wc -l index.html

# Search for specific code
grep -n "className\|getElementById" index.html

# Check file size
ls -lh index.html
```

## Summary for Coding Agents

**TRUST THESE INSTRUCTIONS**: This repository has no build system, no tests, no CI/CD. It's a single self-contained HTML file. When making changes:

1. Edit `index.html` directly
2. Test with `python3 -m http.server 8080` and browser
3. Check browser console for errors
4. Verify both themes work (dark/light)
5. Test responsive behavior
6. Commit when validated

**Do NOT search for package.json, webpack.config.js, or GitHub workflows - they don't exist and aren't needed.**
