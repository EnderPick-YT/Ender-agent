# Ender Agent

Ender Agent is an autonomous AI agent running in a terminal (Windows, single portable .exe) that combines:

- Multi-session chat with a 3x3 panel grid (up to 9 simultaneous chats)
- Native tools: terminal, files, web, embedded Python, WASM plugins, API testing
- Work modes: autonomous, agent (auto-continuation + triggers), effort (5 levels)
- JARVIS: full PC control (mouse, keyboard, windows, applications, clipboard, power)
- Remote cloud: persistent sessions on your own server (Python/Node) — survive reboots
- Rich TUI: bubbles + lipgloss, themes, configurable hotbar, interactive overlays
- Security: YOLO mode, approvals, ANSI sanitization, zero external dependencies

---

## Key Features (v1.9.0)

### Embedded Python (tool python)
- Official Python 3.12.7 embeddable runtime inside the package (runtime/python/)
- Runs Python code on any PC without installation
- Execution with `python -I -B script.py` (isolated, no __pycache__)
- Full standard library: json, sqlite3, hashlib, ssl, urllib, csv, xml
- Real timeout with taskkill /T killing the process tree (prevents freezes)

### WASM Plugins (tool wasm_*)
- Load .wasm plugins from ender-data/plugins/ (wazero pure Go, no CGO)
- Exported functions (i32)->(i32) with JSON string <-> JSON string convention
- Dynamic tools: wasm_<plugin>_<fn> appearing in toolSchemas
- Auto-discovery on startup + /wasm list|reload|info

### API Testing Client (/api + tool api_request)
- Interactive overlay: Request | Response | History | Collections | Env
- HTTP/GraphQL/WebSocket with validations (status, jsonpath, regex, schema, latency)
- Variables {{VAR}} resolved from cfg.ApiVars
- Postman-compatible collections, rotating JSONL history

### Vector Memory / Local RAG (tool vector_search)
- Local embeddings via sentence-transformers (embedded Python) + HNSW/FAISS index
- Indexes: sessions, skills, workspace (*.go/*.md/*.txt)
- Semantic k-NN search with source filters
- /memory reindex|stats|clear + live overlay

### Session Recorder (/record)
- Records everything: input, output, tools, thoughts, timestamps, resize, theme, scroll
- .enderrec format (JSONL + zstd) reproducible frame-by-frame
- Replay with controls: play/pause, 0.5x-4x, seek, export PNG -> video (ffmpeg)

### Effort Mode (5 Levels)
| Level | Name | Behavior |
|-------|------|----------|
| 0 | express | Straight to the point, no fluff |
| 1 | normal | Balanced (default) |
| 2 | high | Plan -> act -> verify -> review |
| 3 | intense | Exhaustive plan, cross-validation, self-audit |
| 4 | obsessive | Perfection: rewrite until no further improvement possible |

Higher effort -> slower and better. Injected into the system prompt.

### Smart Battery Saver
- Adaptive tick: 90 ms with active work -> 1 s at complete rest
- Detects: busy agents, dirty flag, pending triggers, visible toast, last keystroke >2 s
- ~10x fewer CPU/GPU cycles when Ender is just waiting

### JARVIS — Full PC Control (/jarvis)
| Tool | Action |
|------|--------|
| pc_mouse_move/click/drag/wheel | Native mouse (Windows API) |
| pc_keyboard_send/combo | SendKeys + combos (Ctrl+Shift+Esc...) |
| pc_windows_list/focus/minimize/close | Window management |
| pc_apps_open | Open with associated program |
| pc_clipboard_read/write | Clipboard |
| pc_screenshot | PNG capture |
| pc_volume_get/set/mute | Audio |
| pc_power_sleep/restart/shutdown | Power (with confirmation unless auto-power) |
| pc_run | Arbitrary PowerShell (120s timeout + kill tree) |

### Remote Cloud — Sessions That Survive
- Your own server (Python server.py or Node index.js) on your VPS/Pterodactyl
- Contract v3: /session/new|message|events|delete|list + auth X-Ender-Key (scrypt)
- Write in your local chat -> the turn runs ON THE SERVER -> shut down your PC -> boot another -> see the same session with full history
- Multi-client: PC-A and PC-B view the same session in real time

### Professional GUI
- 3x3 resizable panels, mergeable sessions (rectangles)
- Session sidebar with badges, color pivots, independent scroll
- Configurable hotbar (/hotbar): draggable segments, free text, effort visible
- Overlay /settings: everything configurable (General, Agent, JARVIS, UI, Cloud, System)
- Overlay /skills: create/view/delete skills visually (mini-inputs, MD viewer)
- Render auto-repair: invariant "exactly ht lines of w cells" verified every frame
- Render fuzzing: 4 tests catching glitches (absurd dimensions, hostile ANSI, overlays, resize during streaming)

---

## Technical Architecture

```
+---------------------------------------------------------------+
|  ender.exe (single binary, ~11 MB, Go 1.27, bubbles TUI)     |
+---------------------------------------------------------------+
|  Hub (goroutines)                                             |
|  +- Agents (chat/side/sub)  <- runSync (streaming SSE)       |
|  +- Tools: terminal | files | web | python | wasm | api       |
|  +- Triggers: timer | process | file  (agent mode)            |
|  +- WASM Runtime (wazero)  <- isolated .wasm plugins          |
|  +- Python Embedded (3.12)  <- tool python                    |
|  +- Vector Index (HNSW)     <- embeddings via python          |
|  +- Session Recorder        <- .enderrec (zstd+JSONL)         |
|  +- Cloud Client            <- full-server protocol v3        |
|  +- JARVIS (Windows API)    <- 16 PC tools                    |
+---------------------------------------------------------------+
|  Config: ender-data/config.json (persistent, hot-reload)     |
|  Sessions: ender-data/sessions/*.json (auto-save + checkpoint)|
|  Skills: ender-data/skills/ (skill_manage tool + GUI)        |
|  Plugins: ender-data/plugins/*.wasm                          |
|  Runtime: runtime/python/ (embedded, shipped with exe)       |
+---------------------------------------------------------------+
```

---

## Installation & Usage

```bash
# 1. Download ender.exe (single file, ~11 MB)
# 2. Place it anywhere (Desktop, folder, USB)
# 3. Run ender.exe (first run: Defender wizard -> language -> provider -> API key)
```

Essential Commands:
```
/help           # full command list
/new            # new chat (focused panel)
/side           # side chat
/skills         # skills GUI (create/view/delete)
/jarvis         # JARVIS panel (toggle YOLO, auto-power, agent, boost)
/effort 3       # intense level (or: /effort intense)
/api            # API testing client
/memory reindex # index workspace for RAG
/record start   # start recording session
/record replay  # replay recording
/settings       # everything configurable
/cloud          # cloud mode (client/server)
/theme dracula  # change theme
/hotbar         # customize status bar
```

---

## Tests & Quality

| Metric | Value |
|--------|-------|
| Total Tests | 89 (all passing) |
| Render Fuzzing | 4 tests (dimensions, hostile ANSI, overlays, resize) |
| Coverage | Tools, cloud, WASM, python, effort, battery, skills, mouse, recorder |
| Race Detector | Thread-safe code (locks in Hub, sync_ui.go) |
| Auto-Repair | View verifies and corrects its own output every frame |
| Build | go build -o ender.exe . (single binary, no CGO except wazero) |

---

## Distribution

Single .exe download (~11 MB) + runtime/python/ folder (embedded Python 3.12.7, ~10 MB).

The Python runtime is shipped alongside the exe — users download both and keep them together. No installation, no PATH required.

```
YourFolder/
+-- ender.exe
+-- runtime/
    +-- python/          # Python 3.12.7 embeddable
        +-- python.exe
        +-- python312.dll
        +-- libcrypto-3.dll
        +-- libssl-3.dll
        +-- ... (stdlib .pyd + .dlls)
```

On first run, ender-data/ is created automatically next to the exe.

---

## Security & Privacy

- Offline-first: works without internet (except remote LLM)
- Zero telemetry: no data leaves your machine unless you explicitly send it
- Approval gates: terminal, write_file, pc_* ask for confirmation (unless YOLO)
- ANSI sanitization: command output stripped of layout-breaking escape sequences
- Cloud auth: scrypt + salt, key in secure.txt (rotatable via /cloud password)
- WASM isolation: each plugin in its own wazero runtime, no host access except declared imports

---

## Extensibility

| Mechanism | Purpose |
|-----------|---------|
| Skills (skill_manage) | Reusable procedures the AI invokes as tools |
| WASM Plugins | Native code (Rust/C++/Go->WASM) exposed as JSON tools |
| Python Tool | Python scripts with full stdlib, no installation needed |
| API Client | REST/GraphQL/WS testing without leaving the TUI |
| Triggers | Self-scheduling: timer, process open/close, file appear/change |
| Personality / System Prompt | Global behavior configurable (/settings or config.json) |

---

## License

MIT — free for personal, commercial use, modification, and distribution.

---

## Credits

- Bubble Tea / Lip Gloss / Bubbles (Charmbracelet) — TUI foundation
- Wazero — WebAssembly runtime pure Go
- Python Software Foundation — Embeddable Python
- Sentence-Transformers — Local embeddings
- FAISS / HNSW — Vector search
- Go Community — Tooling, testing, fuzzing

---

## Links

- Repository: github.com/yourusername/ender-agent
- Releases: Signed binaries + runtime/python/ folder
- Issues: Bug reports, feature requests, WASM plugin showcases

---

Ender Agent is not just a chat: it is an autonomous work environment that lives in your terminal, controls your PC, remembers everything, and fits in a single .exe.
