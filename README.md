# Claude Voice Board

A voice-controlled development environment that connects Claude AI with voice synthesis APIs (like Cartesia) to enable hands-free coding and visual collaboration through whiteboard MCP integration.

## Overview

This project bridges three powerful technologies:

1. **Voice AI APIs** (Cartesia, ElevenLabs, etc.) - For natural speech-to-text and text-to-speech
2. **Claude Code CLI** - Anthropic's AI-powered coding assistant
3. **Whiteboard MCP** (Excalidraw-based) - Visual collaboration via Model Context Protocol

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Claude Voice Board                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   ┌──────────────┐      ┌──────────────┐      ┌──────────────┐     │
│   │   Voice AI   │      │  Claude Code │      │  Whiteboard  │     │
│   │   (Cartesia) │◄────►│     CLI      │◄────►│     MCP      │     │
│   └──────────────┘      └──────────────┘      └──────────────┘     │
│         │                      │                      │             │
│         ▼                      ▼                      ▼             │
│   ┌──────────────┐      ┌──────────────┐      ┌──────────────┐     │
│   │    STT/TTS   │      │   AI Agent   │      │  Excalidraw  │     │
│   │   Pipeline   │      │   Actions    │      │   Canvas     │     │
│   └──────────────┘      └──────────────┘      └──────────────┘     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Concept

### The Problem

Traditional coding workflows require constant keyboard and mouse interaction. This creates barriers for:
- Developers with accessibility needs
- Pair programming and brainstorming sessions
- Rapid prototyping while thinking aloud
- Teaching and demonstration scenarios

### The Solution

Voice Board enables a conversational coding experience:

1. **Speak your intent** → Voice AI transcribes to text
2. **Claude processes** → Understands context and executes actions
3. **Visual feedback** → Whiteboard displays diagrams, architecture, code flow
4. **Voice response** → Claude speaks results back via TTS

## Architecture

### Component 1: Voice AI Integration

**Cartesia AI** provides low-latency, high-quality voice synthesis:

```
User Speech → Microphone → STT (Speech-to-Text) → Text Command
                                                        │
Claude Response → TTS (Text-to-Speech) → Speaker ◄─────┘
```

**Key Features:**
- Real-time streaming transcription
- Natural voice synthesis for Claude's responses
- Voice activity detection (VAD)
- Multi-language support

**Alternative Voice APIs:**
- ElevenLabs
- OpenAI Whisper + TTS
- Deepgram
- AssemblyAI

### Component 2: Claude Code CLI

Claude Code is Anthropic's official CLI tool that enables:

- Code generation and editing
- File system operations
- Git operations
- Running tests and builds
- Web searches and fetches
- **MCP server connections**

**Integration Points:**
```javascript
// Programmatic interaction with Claude Code
const claudeCode = spawn('claude', ['--print', '--output-format', 'json']);
claudeCode.stdin.write(voiceTranscription);
```

### Component 3: Whiteboard MCP (Excalidraw)

The Model Context Protocol (MCP) allows Claude to interact with external tools. A whiteboard MCP server exposes drawing capabilities:

**MCP Server Capabilities:**
```json
{
  "tools": [
    {
      "name": "draw_diagram",
      "description": "Draw architecture diagrams on the whiteboard"
    },
    {
      "name": "add_shape",
      "description": "Add shapes (rectangle, circle, arrow, text)"
    },
    {
      "name": "create_flowchart",
      "description": "Generate flowcharts from descriptions"
    },
    {
      "name": "annotate_code",
      "description": "Visual code annotations and explanations"
    },
    {
      "name": "export_canvas",
      "description": "Export whiteboard as PNG/SVG"
    }
  ]
}
```

**Excalidraw Integration:**
- Open-source whiteboard with infinite canvas
- Collaborative real-time editing
- Hand-drawn aesthetic for diagrams
- JSON-based scene format (easy MCP integration)

## Data Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Voice Command Flow                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. User speaks: "Draw a REST API architecture diagram"            │
│                          │                                          │
│                          ▼                                          │
│  2. Cartesia STT transcribes speech to text                        │
│                          │                                          │
│                          ▼                                          │
│  3. Voice Board sends command to Claude Code                       │
│                          │                                          │
│                          ▼                                          │
│  4. Claude Code processes and calls whiteboard MCP                 │
│     └─► mcp__whiteboard__draw_diagram(type: "rest-api")            │
│                          │                                          │
│                          ▼                                          │
│  5. MCP Server renders diagram in Excalidraw                       │
│                          │                                          │
│                          ▼                                          │
│  6. Claude responds: "I've created a REST API diagram..."          │
│                          │                                          │
│                          ▼                                          │
│  7. Cartesia TTS speaks the response                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Project Structure

```
claudeVoiceBoard/
├── src/
│   ├── voice/
│   │   ├── cartesia-client.ts    # Cartesia API integration
│   │   ├── audio-capture.ts      # Microphone input handling
│   │   ├── audio-playback.ts     # Speaker output handling
│   │   └── vad.ts                # Voice activity detection
│   │
│   ├── claude/
│   │   ├── claude-code-bridge.ts # Claude Code CLI interface
│   │   ├── command-parser.ts     # Parse voice to structured commands
│   │   └── response-handler.ts   # Handle Claude responses
│   │
│   ├── mcp/
│   │   ├── whiteboard-server/
│   │   │   ├── index.ts          # MCP server entry point
│   │   │   ├── tools.ts          # Whiteboard tool definitions
│   │   │   └── excalidraw-api.ts # Excalidraw scene manipulation
│   │   └── mcp-config.json       # MCP configuration
│   │
│   └── ui/
│       ├── web-client/           # Browser-based Excalidraw UI
│       └── electron-app/         # Optional desktop app
│
├── config/
│   ├── claude-code-settings.json
│   └── voice-settings.json
│
├── docs/
│   ├── setup-guide.md
│   ├── voice-commands.md
│   └── mcp-tools.md
│
└── examples/
    ├── diagram-commands.md
    └── coding-workflows.md
```

## Setup Guide

### Prerequisites

- Node.js 18+
- Claude Code CLI installed (`npm install -g @anthropic-ai/claude-code`)
- Cartesia API key
- Anthropic API key (for Claude)

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/claudeVoiceBoard.git
cd claudeVoiceBoard

# Install dependencies
npm install

# Configure environment
cp .env.example .env
# Edit .env with your API keys:
# ANTHROPIC_API_KEY=your-key
# CARTESIA_API_KEY=your-key
```

### Configure MCP for Claude Code

Add the whiteboard MCP server to your Claude Code configuration:

```json
// ~/.claude/settings.json
{
  "mcpServers": {
    "whiteboard": {
      "command": "node",
      "args": ["./src/mcp/whiteboard-server/index.ts"],
      "env": {}
    }
  }
}
```

### Run the Application

```bash
# Start the whiteboard MCP server
npm run mcp:whiteboard

# Start the voice interface
npm run voice

# Or run everything together
npm run start
```

## Voice Commands Examples

### Code Operations

| Voice Command | Action |
|--------------|--------|
| "Create a new TypeScript file called utils" | Creates `utils.ts` |
| "Add a function to validate email addresses" | Writes validation function |
| "Run the tests" | Executes test suite |
| "Show me the git status" | Displays git status |
| "Commit with message fix login bug" | Creates git commit |

### Whiteboard Operations

| Voice Command | Action |
|--------------|--------|
| "Draw a system architecture diagram" | Creates architecture diagram |
| "Add a database box connected to the API" | Adds shapes with connectors |
| "Create a flowchart for user authentication" | Generates auth flow diagram |
| "Annotate this function with its complexity" | Adds code annotations |
| "Export the whiteboard as PNG" | Saves canvas as image |

### Combined Workflows

| Voice Command | Action |
|--------------|--------|
| "Explain this code on the whiteboard" | Reads code + draws explanation |
| "Show me how these modules connect" | Analyzes imports + draws diagram |
| "Walk me through the API endpoints visually" | Parses routes + creates visual |

## MCP Whiteboard Server

The whiteboard MCP server exposes Excalidraw functionality to Claude:

### Tool: `draw_diagram`

```typescript
interface DrawDiagramParams {
  type: 'architecture' | 'flowchart' | 'sequence' | 'erd' | 'custom';
  description: string;
  style?: 'minimal' | 'detailed';
}
```

### Tool: `add_element`

```typescript
interface AddElementParams {
  type: 'rectangle' | 'ellipse' | 'diamond' | 'arrow' | 'text' | 'line';
  x: number;
  y: number;
  width?: number;
  height?: number;
  text?: string;
  strokeColor?: string;
  backgroundColor?: string;
}
```

### Tool: `connect_elements`

```typescript
interface ConnectElementsParams {
  fromId: string;
  toId: string;
  type: 'arrow' | 'line';
  label?: string;
}
```

### Tool: `get_canvas_state`

Returns the current Excalidraw scene as JSON for context.

### Tool: `clear_canvas`

Clears all elements from the whiteboard.

## Technology Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| Voice STT | Cartesia API | Speech-to-text transcription |
| Voice TTS | Cartesia API | Text-to-speech synthesis |
| AI Engine | Claude (via Claude Code) | Natural language understanding |
| MCP Protocol | @modelcontextprotocol/sdk | Tool integration standard |
| Whiteboard | Excalidraw | Visual canvas rendering |
| Runtime | Node.js / Bun | Server and CLI runtime |
| UI Framework | React | Whiteboard web interface |

## Related Projects

- [Excalidraw](https://github.com/excalidraw/excalidraw) - Open-source whiteboard
- [Claude Code](https://github.com/anthropics/claude-code) - Anthropic's CLI tool
- [MCP Specification](https://modelcontextprotocol.io/) - Model Context Protocol
- [Cartesia AI](https://cartesia.ai/) - Voice AI platform

## Roadmap

- [ ] Core voice capture and playback
- [ ] Cartesia STT/TTS integration
- [ ] Claude Code bridge
- [ ] Basic whiteboard MCP server
- [ ] Excalidraw scene manipulation
- [ ] Diagram generation from descriptions
- [ ] Real-time collaborative mode
- [ ] Code-to-diagram visualization
- [ ] Voice command customization
- [ ] Plugin system for additional MCPs

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License - see [LICENSE](LICENSE) for details.
