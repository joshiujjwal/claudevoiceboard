# claudevoiceboard — TODO

**Type:** Voice-Controlled AI Development Environment  
**Stack:** TypeScript, Node.js, Cartesia, Claude Code CLI, MCP, Excalidraw  
**Status:** ~5% complete (README/design only — no source code)

---

## Actions To Take

- [ ] **Initialize Node.js project and scaffolding** — Create `package.json` with dependencies (TypeScript, Cartesia SDK, `@modelcontextprotocol/sdk`, React, Vite); add `tsconfig.json`; create `src/` directory matching the README architecture
- [ ] **Implement Cartesia voice integration** — Build `src/voice/cartesia-client.ts` for STT/TTS streaming and `src/voice/audio-capture.ts` for microphone input with voice activity detection (VAD)
- [ ] **Build Claude Code CLI bridge** — Implement `src/claude/claude-code-bridge.ts` to spawn and communicate with Claude Code CLI; add `src/claude/command-parser.ts` to convert voice transcriptions to commands
- [ ] **Implement MCP Whiteboard Server** — Create `src/mcp/whiteboard-server/index.ts` with MCP tools (`draw_diagram`, `add_element`, `connect_elements`, `export_canvas`); integrate Excalidraw JSON manipulation
- [ ] **Add tests, Docker, and `.env.example`** — Write unit tests for voice, CLI bridge, and MCP server; add Docker Compose to run all services together; create `.env.example` with required API keys; add GitHub Actions CI
