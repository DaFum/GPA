# ProTab Studio | Professional Edition

A professional-grade Guitar Tab & Score Viewer with advanced analysis capabilities, built as a high-performance Single-Page Application (SPA).

## Purpose

ProTab Studio is designed for musicians, students, and educators who need a reliable, feature-rich tool to view, play, and analyze guitar pro files (`.gp`, `.gp5`, `.gpx`) and other music notation formats. It operates entirely in the browser, ensuring privacy and offline capability (once loaded).

## Features

-   **Multi-Format Support**: Reads Guitar Pro (GP3-GP7, GPX), MusicXML, and AlphaTex files.
-   **Advanced Playback**:
    -   High-quality sound synthesis via AlphaSynth.
    -   Variable tempo control (25% - 200%).
    -   Looping and Metronome.
    -   Multi-track solo/mute capabilities.
-   **Musical Analysis**:
    -   **Harmony**: Pitch distribution histograms, automatic Key and Scale detection.
    -   **Rhythm**: Heatmaps of rhythmic density.
    -   **Complexity**: Algorithmically calculated difficulty score based on techniques and speed.
-   **Responsive UI**: Dark/Light themes, collapsible sidebar, and mobile-friendly design.
-   **No Installation**: Runs directly in any modern web browser.

## Setup

Since ProTab Studio is a "Single File Solution", setup is incredibly simple.

### Prerequisites
-   A modern web browser (Chrome, Firefox, Safari, Edge).
-   Internet connection (for initial loading of CDN resources like fonts and AlphaTab library).

### Running the App
1.  Clone this repository or download the `index.html` file.
2.  Open `index.html` in your web browser.
3.  That's it!

### Development
No build step is required. You can edit `index.html` directly in any text editor.
-   **Styles**: CSS is located in the `<style>` block in the head.
-   **Logic**: Application code is in the `<script>` blocks at the end of the body.
-   **Architecture**: See [AGENTS.md](AGENTS.md) for a detailed breakdown of the internal architecture.

## Usage

### Loading a Score
-   **Drag & Drop**: Drag a supported file (`.gp`, `.xml`, etc.) onto the "Drop File Here" zone in the sidebar.
-   **Browse**: Click the drop zone to open the system file dialog.
-   **TEX Input**: Use the "AlphaTab TEX" editor in the sidebar to write or paste AlphaTex notation directly.

### Navigation & Playback
-   **Space**: Play/Pause.
-   **Escape**: Stop playback.
-   **Mouse Wheel**: Scroll through the score.
-   **Slider**: Use the timeline scrubber to jump to specific points.
-   **Zoom**: Use the +/- buttons or `Ctrl` + `+/-` to adjust the score size.

### Analysis
Click the "Analysis" button in the sidebar (or use the "Tools" section) to open the dashboard. Here you can view:
-   Key signature and scale estimates.
-   Detailed instrument metadata.
-   Technical complexity metrics.

### Settings
Click the gear icon in the header to:
-   Switch between Score, Tab, or Mixed views.
-   Toggle auto-scroll and count-in.
-   Adjust sound quality preferences.

## License

This project is licensed under the MIT License.
