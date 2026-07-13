# AI Book Reel Studio

Privacy-first, AI-powered desktop application for creating professional Instagram Reels from book photos. Built with Electron, React, TypeScript, and FFmpeg.

## Features

### Core Capabilities
- **18 Specialized AI Agents** orchestrate the entire creative pipeline
- **Local-first processing** with optional cloud AI for heavy tasks
- **Cinematic camera movements** (dolly, orbit, zoom, macro, parallax)
- **Music-synchronized editing** with beat detection
- **Professional typography** with elegant animations
- **Multi-resolution export** (1080p, 2K, 4K at 30/60fps, HDR)

### Privacy & Security
- 100% local processing by default (no internet required)
- Encrypted temporary files (XChaCha20-Poly1305)
- Automatic cleanup of processing artifacts
- Optional cloud AI with explicit per-agent consent
- Blockchain-anchored verification records

### AI Agents
1. **Creative Director** - Designs overall creative direction
2. **Book Analysis** - Extracts colors, typography, genre, mood from covers
3. **Image Enhancement** - Upscales, denoises, color-corrects photos
4. **Storyboard** - Plans scene sequence and timing
5. **Background Intelligence** - Generates complementary backgrounds
6. **Camera Director** - Plans cinematic camera movements
7. **Motion Graphics** - Creates transitions and effects
8. **Typography** - Designs text layouts and animations
9. **Music Intelligence** - Analyzes tempo, beats, structure
10. **Sound Design** - Adds cinematic sound effects
11. **Marketing** - Generates hooks, captions, CTAs, hashtags
12. **Viral Optimization** - Optimizes for retention and engagement
13. **Brand Consistency** - Enforces visual identity
14. **Accessibility** - Ensures contrast, safe zones, readable text
15. **Privacy & Security** - Sanitizes data, encrypts temp files
16. **Blockchain Verification** - Creates ownership records
17. **Quality Assurance** - Automated visual/audio quality checks
18. **Export** - Renders final video in multiple formats

## Quick Start

### Prerequisites
- Node.js 20+
- FFmpeg (bundled)
- Redis (for job queue, optional for local dev)

### Installation
```bash
# Clone and install
git clone https://github.com/thebookclub/ai-book-reel-studio.git
cd ai-book-reel-studio
npm install

# Development
npm run dev

# Production build
npm run build:all
```

### First Reel (4 clicks)
1. Click **"New Project"**
2. Upload 1-5 book cover photos + enter metadata
3. (Optional) Add background music
4. Click **"Create Reel"**

Watch the AI agents work in real-time, then preview and export.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    ELECTRON MAIN PROCESS                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │ Orchestrator│──│  Job Queue  │──│   FFmpeg    │          │
│  │  Service    │  │  (BullMQ)   │  │  Renderer   │          │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘          │
│         │                │                │                  │
│         ▼                ▼                ▼                  │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                    AGENT REGISTRY                        │ │
│  │  [CreativeDirector] [Storyboard] [BookAnalysis] ... [QA] │ │
│  └─────────────────────────────────────────────────────────┘ │
│         │                │                │                  │
│         ▼                ▼                ▼                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │  Local AI   │  │  Cloud AI   │  │   Asset     │          │
│  │  (ONNX)     │  │  Gateway    │  │  Manager    │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   ELECTRON RENDERER PROCESS                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │   React UI  │  │  Preview    │  │  Settings   │          │
│  │  Components │  │  Player     │  │  Panel      │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
```

## Tech Stack

| Layer | Technology |
|-------|------------|
| Framework | Electron 28 + React 18 + TypeScript |
| State | Zustand + Immer |
| UI | Radix UI + Tailwind CSS |
| Video | FFmpeg (fluent-ffmpeg) |
| Local AI | ONNX Runtime (ONNX models) |
| Cloud AI | Vercel AI SDK (OpenAI, Anthropic, Replicate) |
| Database | SQLite (better-sqlite3) + WAL mode |
| Queue | BullMQ + Redis |
| Encryption | libsodium (XChaCha20-Poly1305) |
| Packaging | electron-builder |

## Project Structure

```
src/
├── main/                 # Electron Main Process
│   ├── index.ts         # Entry point
│   ├── ipc/             # IPC handlers
│   ├── services/        # Core services (DB, Queue, FFmpeg, etc.)
│   ├── agents/          # 18 AI Agent implementations
│   └── utils/           # Utilities
├── renderer/            # React Frontend
│   ├── src/
│   │   ├── components/  # Reusable UI components
│   │   ├── pages/       # Page views
│   │   ├── hooks/       # Custom React hooks
│   │   ├── store/       # Zustand stores
│   │   ├── types/       # TypeScript types
│   │   └── styles/      # Tailwind + globals
├── preload/             # Context bridge
└── shared/              # Shared types & utilities
```

## Configuration

### Environment Variables
```bash
# .env
REDIS_URL=redis://localhost:6379
MASTER_KEY=your-32-byte-hex-key  # For encryption
OPENAI_API_KEY=sk-...            # Optional cloud AI
ANTHROPIC_API_KEY=sk-...         # Optional cloud AI
REPLICATE_API_TOKEN=...          # Optional cloud AI
```

### Privacy Modes
| Mode | Description |
|------|-------------|
| `local` | 100% offline, local models only |
| `hybrid` | Local by default, cloud for heavy tasks (opt-in) |
| `cloud` | Best quality models, requires internet |

## Development

### Commands
```bash
npm run dev          # Start dev servers (main + renderer)
npm run dev:main     # Main process only
npm run dev:renderer # Renderer only
npm run build        # Build all
npm run build:main   # Build main process
npm run build:renderer # Build renderer
npm run lint         # ESLint
npm run typecheck    # TypeScript check
npm run test         # Vitest unit tests
npm run test:ui      # Vitest UI
npm run pack         # Package without installer
npm run dist         # Create distributables
```

### Adding a New Agent
1. Create `src/main/agents/yourAgent.ts` extending `BaseAgent`
2. Implement `validate()`, `getDefaults()`, `process()`
3. Register in `src/main/agents/index.ts`
4. Add to pipeline in `OrchestratorService`

## Distribution

### Platforms
- **Windows**: NSIS installer + portable
- **macOS**: DMG + ZIP (notarized)
- **Linux**: AppImage + DEB

### Auto-Updates
Configured via `electron-updater` with GitHub Releases.

## License

MIT License - see [LICENSE](LICENSE) for details.

## Contributing

1. Fork the repository
2. Create feature branch
3. Make changes with tests
4. Submit PR with description

## Support

- GitHub Issues: Bug reports & feature requests
- Discord: Community support
- Email: support@thebookclub.com