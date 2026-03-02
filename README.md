
GhaniyOS 2.0
Complete Developer & User Documentation

Version 2.0  |  Single-File Browser OS  |  446 KB
Built with HTML5 • CSS3 • Vanilla JavaScript

34+	7,800+	446 KB	0 deps
Built-in Apps	Lines of Code	Total File Size	Zero Dependencies
 
1. Overview
GhaniyOS is a fully self-contained operating-system simulation running inside a single HTML file — no server, no build step, no internet connection required (after initial load). Open index.html in any modern browser and you have a complete desktop environment complete with a window manager, taskbar, virtual file system, terminal emulator, and 34+ applications.

1.1  Design Philosophy
•	Single-file architecture — the entire OS ships as one index.html file
•	Zero npm dependencies — all engines are written in vanilla JavaScript
•	Offline-first — core functionality works without a network connection
•	Persistent state — user files, settings, and preferences survive page reloads via localStorage
•	Open ecosystem — optional CDN libraries (PDF.js, Pyodide, JSZip) are loaded on demand

1.2  Browser Compatibility
GhaniyOS requires a modern browser with full ES2020+ support.

Browser	Min Version	Status
Chrome / Edge	90+	Fully supported
Firefox	88+	Fully supported
Safari	15+	Fully supported
Mobile Chrome / Safari	Current	Supported (touch events active)

1.3  Getting Started
1.	Download or copy index.html to your local machine
2.	Open the file in any modern browser (double-click or drag into browser window)
3.	Click the desktop or press the Windows key (⊞) to open the Start Menu
4.	Click any app to launch it, or type in the Start Menu search box
5.	Drag window title bars to move, click the resize handle to resize, click × to close
Tip: For the best experience, use Chrome or Edge and enable "Allow sites to access camera/microphone" when prompted by the Camera and Voice Recorder apps.
 
2. Architecture
The codebase is divided into nine logical layers, all living inside a single <script> tag.

2.1  Layer Structure

#	Layer / Object	Responsibility
1	State	Thin localStorage wrapper — get/set/remove keys with automatic JSON serialisation
2	VFS	Virtual File System — in-memory tree with mkdir, read, write, ls, rm, mv operations
3	WM	Window Manager — creates, moves, resizes, focuses, minimises, and closes floating windows
4	Notif	Notification centre — queues and renders toast messages and the notification panel
5	Toast	Quick toast utility — show(message, type, duration) shorthand
6	Term	Terminal emulator — bash-like shell with 30+ built-in commands, piping, and history
7	Apps	App renderer object — one method per app returns an HTML string for the window body
8	OS	OS controller — appMeta registry, open() dispatcher, Start Menu, search
9	Engines	One JS engine per app — all the runtime logic (DOOM loop, spreadsheet evaluator, etc.)

2.2  Data Flow
When a user opens an app the flow is:

User click → OS.open(name) → Apps[name]() returns HTML → WM.make() creates window
                              → setTimeout(engineInit, 80ms) wires up JS logic
                              → Engine reads/writes State via localStorage

2.3  Virtual File System
VFS is an in-memory object tree. Data is persisted to localStorage under the key "vfs". The tree starts with these default directories:
•	/home/user — user home directory
•	/home/user/Documents — document storage
•	/home/user/Pictures — image storage
•	/home/user/Downloads — download staging
•	/home/user/Music — audio files
•	/home/user/Videos — video files
•	/tmp — temporary files (cleared on VFS reset)
•	/Trash — deleted items

The VFS is entirely virtual — no real files on disk are created or read. File content is stored as strings in localStorage.
 
3. Window Manager
The Window Manager (WM) provides a classic floating-window desktop experience.

3.1  Window Controls

Shortcut	Action
Drag title bar	Move window anywhere on the desktop
Drag resize handle (⇲)	Resize window from the bottom-right corner
Click window body	Bring window to front (raise z-index)
Click − (minimise)	Collapse window to the taskbar
Click □ (maximise)	Toggle full-screen / restored size
Click × (close)	Close window and free resources
Double-click title bar	Maximise / restore window

3.2  Taskbar
•	Running apps appear as icon buttons in the taskbar
•	Click a taskbar button to show/hide the corresponding window
•	Right-click a taskbar button for a context menu (Close, Move to front)
•	The system clock and notification bell are in the bottom-right corner

3.3  Start Menu
•	Click the  GhaniyOS logo in the bottom-left, or press the Windows key
•	Type in the search box to filter all 34+ apps instantly
•	Apps are arranged in a grid — click to launch
•	The Start Menu closes when you click outside it or open an app
 
4. Built-in Apps
4.1  System & Productivity

	App	Description	Key Features
⬛	Terminal	Full bash-like terminal emulator with 30+ commands, command history, tab completion, and piped output. Supports: ls, cd, cat, mkdir, rm, mv, cp, touch, echo, grep, find, curl (simulated), python3 (via PyREPL bridge), and more.	History ↑/↓, Ctrl+C, clear, multiple sessions
🗂️	Files	Dual-pane file manager for the VFS. Browse directories, create/rename/delete files and folders, drag-and-drop between panes. Integrates with Trash for safe deletes.	Breadcrumb nav, context menus, file icons by type
🌐	Browser	Embedded web browser using an iframe. Supports URL navigation, back/forward history, bookmarks, and a basic ad-filter. Some sites block iframe embedding (X-Frame-Options).	Bookmarks bar, history, dark-mode toggle
📝	Notes	Plain-text note editor with auto-save to VFS. Supports multiple notes, search, and Markdown preview toggle. Notes persist across sessions.	Auto-save, Markdown preview, word count
🧮	Calculator	Scientific calculator with basic arithmetic, trigonometry (sin, cos, tan), logarithms, powers, square roots, and memory (M+, MR, MC).	Scientific mode, keyboard input, history
⚙️	Settings	System preferences: wallpaper, accent colour, font size, username, clock format, and VFS reset. Changes apply immediately without a page reload.	Live preview, theme export
📶	Network	Simulated network monitor showing interface stats, ping test, DNS lookup, and a bandwidth graph. All data is simulated for demonstration purposes.	Animated bandwidth graph, ping simulation
🗑️	Trash	Recycle bin for files deleted from the Files app. Restore individual items or empty the Trash entirely.	Restore, empty, item count badge
 
4.2  Creative & Media

	App	Description	Key Features
🎨	Paint	Full raster paint app similar to MS Paint. 12 drawing tools, colour picker, fill bucket, text tool, shape tools (rectangle, ellipse, line, polygon), eraser, zoom.	Undo/Redo, zoom, save as PNG/JPG, open image
🖼️	ImgEditor	Advanced image editor inspired by Photopea/GIMP. 16 tools, unlimited layers with opacity and blend modes, 10 one-click filters, 5 adjustable parameters, histogram, undo/redo stack (20 levels).	16 tools, layers, filters, histogram, PNG/JPG export
🎵	Music	Local audio player. Load MP3, FLAC, OGG, WAV files from your computer. Playlist management, shuffle, repeat, seek bar, volume control.	Playlist, shuffle, repeat, seek, volume
🎬	Media	General-purpose media player for video and audio files. Supports all formats the browser supports natively (MP4, WebM, OGG, MP3, WAV).	Playback controls, fullscreen, volume, subtitles
🎞️	VideoEditor	Non-linear video editor with timeline, 10 visual filters, brightness/contrast/saturation/speed adjustments, fade in/out effects, and export.	Timeline, 10 filters, speed control, export
🎙️	Recorder	Microphone recorder with live waveform visualisation. Records to WebM/Opus, displays all recordings with playback controls, download button per recording.	Live waveform, multi-recording list, download
📷	Camera	Live webcam capture. Take photos (saved as in-app gallery), switch between front/rear cameras. Photos are downloadable as JPEG. Requires browser camera permission.	Photo capture, gallery strip, front/rear switch
📺	Streams	Stream launcher inspired by Streamlink. 16 curated live channels across Gaming, Music, IRL, and Learn categories (Twitch, YouTube). Opens streams in a new browser tab.	16 channels, 4 categories, search, save favourites
 
4.3  Development

	App	Description	Key Features
💻	CodeEditor	VSCode-inspired source code editor. 5 built-in starter files (JS, CSS, Python, HTML, JSON). Live JavaScript execution with console output capture. Line numbers, minimap, tab bar, file tree.	Run JS (Ctrl+S), Tab indent, Ctrl+/ comment, Find/Replace, HTML preview, export
🐍	PyREPL	Real Python 3.12 REPL running inside the browser via Pyodide (CPython compiled to WebAssembly). Loads ~10 MB from CDN on first use. Falls back to a basic JS-based Python emulator when offline.	Real CPython 3.12, 7 code examples, history, Ctrl+Enter to run
📦	Archive	ZIP file manager powered by JSZip (CDN). Open and browse ZIP archives with a file tree, view file metadata. Create new ZIPs with sample content.	Open ZIP, file tree, metadata, create & download ZIP

4.4  Office & Data

	App	Description	Key Features
📊	Spreadsheet	Excel-like spreadsheet with 26 columns × 50 rows. Supports formula evaluation (=SUM, =AVERAGE, =IF), cell references (A1:B5), CSV import, and CSV export. Click a cell to select, double-click to edit.	=SUM/AVERAGE/IF, cell refs, CSV import/export
📄	PDF	PDF reader powered by PDF.js (CDN). Real page rendering to canvas, page thumbnails, text extraction panel, zoom controls (25%–400%), drag-and-drop to open.	Real rendering, thumbnails, text extraction, zoom
📅	Calendar	Monthly calendar with event management. Add colour-coded events to any day, upcoming events sidebar, persistent storage. Navigate months and jump back to today.	Add/delete events, colour codes, persistent, upcoming sidebar
👥	Contacts	Contact book with search, add, edit, and delete. Contacts persist across sessions. Each contact has a name, phone, email, avatar emoji, and a colour accent.	CRUD, search, avatar, persistent
 
4.5  Utilities & Entertainment

	App	Description	Key Features
⛅	Weather	Real weather data via wttr.in API. Shows temperature, description, humidity, wind speed, feels-like, and a 5-day forecast. Falls back to a convincing simulation if the API is unavailable.	Live data (wttr.in), 5-day forecast, fallback simulation
🕐	Clock	Four-in-one time app: animated analog clock (canvas-drawn), stopwatch with lap times, countdown timer with preset adjustments (±10s, ±1m, ±5m), and a live world clock for 10 cities.	Analog canvas clock, stopwatch laps, timer, 10-city world clock
🗺️	Maps	Embedded OpenStreetMap viewer. Search any location by name or coordinates. The map iframe updates in real time. An external link opens the location in Google Maps.	OSM embed, location search, Google Maps link
🔫	DOOM	Full DDA raycaster FPS game inspired by the original DOOM engine. 16×16 tile map, 8 enemies with HP bars, minimap, weapon animation, damage effects, score tracking, death and win screens.	WASD/mouse, DDA raycasting, 8 enemies, minimap, FPS counter
▶️	Play Store	App store with 60+ installable web apps and games, organised in categories. Apps open in the embedded browser. Tracks recently opened and favourites.	60+ apps, categories, search, recently opened
🤖	Android	PWA launcher with 45+ popular Progressive Web Apps (Android-style grid). Opens PWAs in the embedded browser.	45+ PWAs, category filter
 
5. Terminal Reference
The GhaniyOS terminal is a fully featured shell emulator with 30+ commands. Multiple terminal windows can run simultaneously, each with independent history and working directory.

5.1  File System Commands

Shortcut	Action
ls [-la] [path]	List directory contents. -l for long format, -a to show hidden files
cd <path>	Change directory. Supports ~, .., and absolute/relative paths
pwd	Print working directory
mkdir [-p] <dir>	Create directory. -p creates parent directories
touch <file>	Create empty file or update modification time
cat <file>	Print file contents to terminal
echo <text> [> file]	Print text or redirect to file (> overwrite, >> append)
cp <src> <dest>	Copy file or directory
mv <src> <dest>	Move or rename file/directory
rm [-r] <path>	Remove file (-r for recursive directory deletion)
find <dir> -name <pattern>	Search for files by name pattern
grep <pattern> <file>	Search file contents for pattern

5.2  System Commands

Shortcut	Action
help	List all available commands with brief descriptions
clear	Clear the terminal screen
history	Show command history for this session
whoami	Print current username
uname -a	Print OS information
ps	List simulated running processes
top	Show simulated process monitor (updates every 2 s)
df -h	Show VFS disk usage in human-readable format
date	Print current date and time
env	Print environment variables

5.3  Developer Commands

Shortcut	Action
node <file>	Execute JavaScript file contents (evaluates in browser context)
python3 <file>	Open file in PyREPL (requires Pyodide to be loaded)
curl <url>	Simulated HTTP request — prints mock response headers and body
wget <url> -O <file>	Simulated download — creates a file with mock content in VFS
npm install <pkg>	Simulated npm (shows install animation, marks package as installed)
git status / log / diff	Simulated git commands (demo output)
open <app>	Open any GhaniyOS app from the terminal (e.g. open Browser)

Keyboard: Press ↑/↓ to navigate history. Press Tab for basic command completion. Press Ctrl+C to cancel a running command. Type clear to reset the screen.
 
6. Code Editor
The Code Editor is a VSCode-inspired IDE with syntax-aware features, live code execution, and a built-in file explorer.

6.1  Interface Layout
•	Left sidebar — file explorer with all open files
•	Tab bar — switch between open files; click × to close a tab
•	Main editor — textarea with line numbers and minimap
•	Terminal panel (bottom) — output console for executed code
•	Status bar — current language, cursor position, filename

6.2  Keyboard Shortcuts

Shortcut	Action
Ctrl + S	Save and run the current file
Ctrl + /	Toggle line comment (adds // for JS, # for Python)
Tab	Insert 2 spaces (indent)
Ctrl + Enter (editor)	Run code (same as Ctrl+S)
Ctrl + Z	Undo
Ctrl + Shift + Z	Redo

6.3  Running Code
Behaviour depends on the file extension of the active file:

Extension	Run Behaviour
.js	Evaluates in the browser context with a captured console. Output (console.log, errors, return value) appears in the terminal panel.
.html	Creates a Blob URL and opens the HTML in a new browser tab.
.py	Opens the PyREPL app and loads the file content for execution.
.css / .json / .md	These file types cannot be run directly. Use the editor to view and edit them.

6.4  Built-in Starter Files
•	main.js — JavaScript demo with greet(), array map/reduce, console output
•	style.css — Sample CSS with CSS variables and card component
•	hello.py — Python demo with Fibonacci, math, JSON, and f-strings
•	index.html — Minimal HTML page with inline style and script
•	data.json — GhaniyOS metadata as a structured JSON object
 
7. Python REPL
GhaniyOS ships a real Python 3.12 interpreter running via Pyodide — CPython compiled to WebAssembly. This is not a simulation; it runs the actual Python runtime in your browser.

7.1  Loading
On first open, PyREPL downloads the Pyodide runtime (~10 MB) from the jsDelivr CDN. This is a one-time download cached by the browser. Subsequent opens are instant.
Offline fallback: If Pyodide cannot load (no internet, CDN blocked), PyREPL activates a lightweight JavaScript-based Python emulator that supports basic print(), arithmetic, for loops, functions, and list comprehensions.

7.2  Supported Standard Library Modules
All of Python's standard library is available once Pyodide loads. Commonly used modules include:
•	math — sin, cos, sqrt, pi, e, log, factorial, etc.
•	json — loads, dumps, JSONDecodeError
•	re — fullmatch, findall, sub, compile, groups
•	random — random, randint, choice, shuffle, sample
•	datetime — datetime, date, timedelta, timezone
•	itertools — chain, product, combinations, permutations, groupby
•	functools — reduce, partial, lru_cache
•	collections — Counter, defaultdict, OrderedDict, namedtuple
•	string — ascii_letters, digits, punctuation, Template
•	io — StringIO, BytesIO

7.3  Keyboard Shortcuts

Shortcut	Action
Ctrl + Enter	Run the code in the editor
Tab	Insert 4 spaces (Python indent)
Ctrl + ↑	Load previous history entry into editor
Click example chip	Load example code into editor

7.4  Built-in Examples
Seven example scripts are accessible from the top bar:
6.	Hello World — basic print()
7.	Fibonacci — iterative sequence with a loop
8.	List Comprehension — squares using [x**2 for x in range(1,11)]
9.	Dict — for loop over dict.items() with f-strings
10.	Math — math.pi, math.e, math.sqrt formatted to 10 decimal places
11.	JSON — json.dumps with indent=2
12.	Regex — re.findall on phone/email patterns
 
8. Image Editor (ImgEditor)
ImgEditor is a full-featured raster image editor inspired by Photopea and GIMP. It supports multiple layers, a 16-tool palette, non-destructive adjustment previews, a real histogram, and PNG/JPEG export.

8.1  Tools

Icon	Tool	Description
🖌️	Brush	Soft or hard round brush. Size (1–200 px), opacity (1–100%), and hardness (0–100%) are configurable. Low hardness = airbrush-style edges.
✏️	Pencil	Hard-edge pencil. Uses 1/3 of brush size, full opacity — ideal for pixel art.
🩸	Fill	Flood fill with tolerance (~30 value units). Fills contiguous same-colour regions.
🗑️	Eraser	Removes pixels using destination-out compositing. Fully transparent result on that layer.
💧	Eyedropper	Sample a pixel colour and set it as the foreground colour.
⬚	Rect Select	Draws a rectangle selection outline (visual only in this build).
⬜	Rectangle	Draws a filled + stroked rectangle. Colour = foreground, fill = 20% opacity.
⭕	Ellipse	Draws a filled + stroked ellipse.
✂️	Crop	Marks a crop region (visual preview; commit with Enter in future builds).
🔍	Zoom	Click to zoom in 25%. Shift+click or Ctrl+Scroll to zoom out.
✋	Pan	Pan the canvas view within the scrollable area.
🔤	Text	Opens a prompt for text input, then renders it at the clicked position.
📐	Line	Draws a straight line from drag start to release point.
⭐	Star	Draws a 5-point star shape.
🌈	Gradient	Draws a linear gradient across the full canvas from drag start to release.
🔮	Smudge	Blends adjacent pixels for a smeared, watercolour-like effect.

8.2  Filters
Filters are applied non-destructively to the active layer. Click a chip to apply instantly:
•	Blur — applies CSS blur(3px) via offscreen canvas composite
•	Sharpen — contrast+brightness boost for perceived sharpness
•	Grayscale — removes colour using grayscale(1) filter
•	Sepia — warm retro tone via sepia(1)
•	Invert — inverts all pixel values
•	Emboss — high-contrast, low-brightness relief effect
•	Edge — invert + grayscale + high contrast for edge detection
•	Noise — adds random luminance noise per pixel
•	Vignette — dark radial gradient overlay from centre outward
•	HDR — contrast + saturation + brightness boost

8.3  Adjustments
Five sliders allow live preview (shown on the overlay canvas). Click Apply to commit to the layer:
•	Brightness — range −100 to +100 (maps to CSS brightness 0–2)
•	Contrast — range −100 to +100
•	Saturation — range −100 to +100
•	Hue — range 0–360° hue rotation
•	Temperature — range −100 to +100 (visual warmth shift)
Tip: Adjust sliders, check the live preview on the canvas, then click Apply. Click Reset to discard all pending adjustments.
 
9. GhaniyDOOM
GhaniyDOOM is a real-time 3D raycaster game built from scratch using the HTML5 Canvas API. It uses the same DDA (Digital Differential Analysis) algorithm as the original DOOM engine to cast rays and determine wall heights.

9.1  Controls

Shortcut	Action
W / Arrow Up	Move forward
S / Arrow Down	Move backward
A / Arrow Left	Rotate left
D / Arrow Right	Rotate right
Mouse move (after click)	Free look (pointer lock) — click the game canvas to activate
Space	Shoot — fires a hitscan ray along view direction, up to 8 tile units
E	Interact (reserved for doors/switches in future builds)
R (on death screen)	Restart game

9.2  Map & Enemies
•	16×16 tile map with a multi-room layout
•	8 enemies (👹 👺 👻 💀 👾 🤖 😈 and variants) each with 2–4 HP
•	Enemies deal contact damage (0.8 DPS when within 0.6 tiles)
•	Each kill is worth 100 points. Kill all 8 for a victory message.
•	Minimap in the top-left corner shows the map, your position, and enemy positions

9.3  Technical Details
•	Renders at 50% resolution (upscaled via CSS) for performance
•	DDA raycasting at native canvas width = one ray per column
•	Sprites sorted back-to-front and z-buffer culled
•	60 FPS target via requestAnimationFrame
•	HP bar and ammo HUD overlaid in HTML (not canvas)
•	Damage flash overlay for low-HP warning (< 30 HP)
 
10. GhaniyStream
GhaniyStream is a stream launcher inspired by the open-source Streamlink project. It provides a curated directory of live streams and video channels that open in a new browser tab.

10.1  Channel Directory

Category	Channels
🔴 Live	Minecraft, Fortnite, League of Legends, VALORANT, Chess
🎮 Gaming	Full Twitch gaming category directories
🎵 Music	Lofi Hip Hop Radio, Classical Music, Synthwave Radio
🗣️ IRL	NASA Live, ISS Live Feed, Earth from Space (live NASA cameras)
📚 Learn	CS50 Lectures, Fireship, freeCodeCamp, 3Blue1Brown, MIT OpenCourseWare
⭐ Saved	Channels you have starred (persists during session)

GhaniyStream opens streams in a new browser tab. It does not embed or proxy stream content — it acts as a launcher. Some streams may require a free account on their platform.
 
11. Extending GhaniyOS
Because GhaniyOS is a single HTML file, extending it is straightforward. All you need is a text editor.

11.1  Adding a New App
Follow these four steps:
13.	Register the app in OS.appMeta (inside the OS = { ... } object):
'MyApp' : { icon: '🚀', w: 600, h: 400 },

14.	Add a renderer to the Apps object (before the }; // end Apps comment):
MyApp() {
  return `<div style="padding:20px">Hello from MyApp!</div>`;
},

15.	Add a dispatch branch in OS.open():
else if(name==='MyApp') content=Apps.MyApp();

16.	Optionally register an init function:
if(name==='MyApp') setTimeout(()=>myAppInit(), 80);

17.	Add the app to populateStartMenu() if you want it in the Start Menu grid

11.2  Using the VFS API
VFS.write("/home/user/test.txt", "Hello!");  // create or overwrite
const text = VFS.read("/home/user/test.txt"); // returns string or null
const files = VFS.ls("/home/user");          // returns array of names
VFS.mkdir("/home/user/Projects");            // create directory
VFS.rm("/home/user/test.txt");               // delete file
VFS.mv("/home/user/a.txt", "/home/user/b.txt"); // rename/move

11.3  Using the State API
State.set("mykey", { x: 1, y: 2 });   // auto-serialised to JSON
const val = State.get("mykey", null); // returns parsed value or default
State.remove("mykey");               // delete key

11.4  Showing Notifications
Toast.show("File saved!", "success", 2000); // type: success|error|warn|info
Notif.add("title", "body text", "info");    // adds to notification centre

11.5  Opening Apps Programmatically
OS.open("Browser", { url: "https://example.com" });
OS.open("Notes", { content: "Pre-filled text" });
OS.open("PDF",   { file: fileObject });
 
12. Troubleshooting

Issue	Solution
Pyodide / PDF.js / JSZip does not load	These libraries load from CDN on first use. Check your internet connection. The features will fall back to a simulated/limited mode when offline.
Camera or Microphone not working	Click "Allow" in the browser permission prompt. Make sure no other app is using the device. HTTPS is required on some browsers; open the file from a local server if needed.
Weather shows simulated data	The wttr.in API is blocked or offline. The app will show realistic simulated weather instead.
Maps iframe is blank	Some networks block OpenStreetMap. Try using a different network or VPN. The external link to Google Maps still works.
localStorage quota exceeded	Too many files in the VFS. Open Settings and click Reset VFS to clear all stored files, or delete individual files via the Files app.
App window is off-screen	Click the taskbar button to show/hide the window. If still stuck, close and reopen the app from the Start Menu.
Fonts look wrong	GhaniyOS uses Google Fonts (Geist, JetBrains Mono). Without internet these fall back to system fonts — this is cosmetic only.
Code Editor shows run errors	The code runs in the same browser context. Avoid code that calls alert(), document.write(), or accesses restricted APIs. Use console.log() for output.
DOOM is very slow	The game renders at half resolution to maintain 60 FPS. On very old hardware, try closing other windows first.
 
13. Release Notes
Version 2.0  —  Current Release
•	New: DOOM-style raycaster game with DDA engine, 8 enemies, minimap
•	New: VSCode-inspired Code Editor with live JS execution
•	New: Real Python 3.12 REPL via Pyodide WebAssembly
•	New: Full image editor (ImgEditor) — 16 tools, layers, filters, histogram
•	New: GhaniyStream — curated live stream launcher (16 channels)
•	New: Real PDF reader via PDF.js with page thumbnails & text extraction
•	New: Spreadsheet app with formula evaluation (=SUM, =AVERAGE, =IF)
•	New: Calendar with persistent colour-coded events
•	New: Contacts app with CRUD and search
•	New: Weather app with live wttr.in data and 5-day forecast
•	New: Clock with analog canvas, stopwatch, timer, and world clock
•	New: Voice Recorder with live waveform and MediaRecorder API
•	New: Archive Manager with JSZip integration
•	New: Camera app with photo capture and gallery
•	Improved: Start Menu expanded to 29 apps
•	Improved: All app engines wired with full JS logic (previously UI-only)
•	Fixed: </script> inside CE.files string causing script tag truncation
•	Fixed: Nested template literal quote conflicts in ceBuildTree/ceBuildTabs
•	Fixed: Play Store category filter using template literal with single quotes

Version 1.0  —  Initial Release
•	Window manager with drag, resize, minimise, maximise
•	Virtual File System with localStorage persistence
•	Terminal emulator with 30+ commands
•	Files, Notes, Calculator, Paint, Media, Browser, Settings
•	Play Store (60+ apps), Android PWA Launcher (45+ apps)
•	WebVM Linux App Store integration
•	Pixelify vector/raster paint (Photoshop/Illustrator-inspired)
 
Appendix A  —  Optional CDN Libraries
GhaniyOS loads these libraries on demand. Core features work without them; they enhance specific apps.

Library	Version	App	Purpose
Pyodide	0.25.0	PyREPL	CPython 3.12 compiled to WebAssembly — real Python execution
PDF.js	3.11.174	PDF Reader	Mozilla's PDF rendering engine for canvas-based page display
JSZip	3.10.1	Archive Manager	ZIP file creation and extraction in the browser

All CDN libraries are loaded from jsDelivr (cdn.jsdelivr.net) or cdnjs (cdnjs.cloudflare.com) — both are highly available global CDNs. Pyodide also uses its own CDN mirror.

Appendix B  —  File Size Breakdown

Section	Est. Lines	Est. Size
HTML structure + CSS styles	~800	~42 KB
App renderers (Apps object)	~1,800	~110 KB
App JS engines (34 engines)	~3,200	~185 KB
Play Store database (60+ entries)	~900	~52 KB
Terminal engine + commands	~700	~40 KB
Window Manager + OS controller	~400	~17 KB
Total	~7,800	~446 KB

