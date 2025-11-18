### The Exact 5-Prompt Cursor Sequence That Turns Your Google AI Studio Export into a Production-Grade, Fully-Wired Digital-Twin-Desktop Frontend  
(Use these in order, one per day or one per 2-hour session. Copy-paste verbatim into Cursor Composer.)

#### Prompt 1 – Clean & Modernize the Foundation (run first)
```
You are now the senior frontend architect taking over a beautiful but naive React app exported from Google AI Studio.

Do these things in order and touch every file:

1. Upgrade to the 2025 best-practice stack:
   - Replace all create-react-app / Vite default stuff with shadcn/ui + Tailwind v3.4+
   - Add zustand for global state
   - Add tanstack/query v5 for data fetching
   - Add tanstack/router v1 (file-based routing)
   - Add lucide-react icons everywhere
   - Add sonner for toasts

2. Create a clean src/lib folder with:
   - api.ts (central place for all backend calls)
   - ws.ts (WebSocket manager with auto-reconnect)
   - utils.ts
   - types.ts (all shared interfaces)

3. Create src/stores:
   - useBackendUrlStore.ts
   - useSessionStore.ts
   - useAgentStore.ts
   - useTwinStore.ts

4. Replace every single inline style and className mess with proper shadcn components (Button, Input, Card, Sheet, Dialog, etc.)

5. Add a beautiful first-launch screen at route /onboarding that forces the user to set VITE_BACKEND_URL (stored in localStorage + zustand) with a “Test Connection” button that pings GET /api/v1/status

Do not implement any real API calls yet — just scaffolding and perfect visuals.
Preserve 100% of the original aesthetic (layout, colors, animations).
```

#### Prompt 2 – Wire Up Core Chat + Streaming (the heart)
```
Now implement the main chat experience using the exact contract below. Use TanStack Query + SSE for streaming.

Backend base URL comes from the zustand store (useBackendUrlStore).

Exact endpoint:
POST http://localhost:3420/api/v1/chat/stream
→ { messages: Message[], session_id?: string, project_id?: string }
← SSE with JSON chunks exactly like:
{
  "content": string,
  "tool_calls"?: ToolCall[],
  "twin_update"?: TwinState,
  "artifact"?: Artifact,
  "session_id": string,
  "is_complete": boolean
}

Implement:
- useChatStream mutation (tanstack query)
- Token-by-token streaming into the chat window
- Automatic session_id persistence per project
- Display twin_update preview thumbnail when received
- Render artifact pane based on artifact.type (markdown, chart, folder_tree, 3d_scene)
- Show typing indicator + “stop generating” button

Use React Server Components pattern where it makes sense (Next.js App Router style even if Vite).
```

#### Prompt 3 – Always-On Personal Agent + Live Thought Stream
```
Implement the always-visible Personal Agent side panel using WebSocket.

WebSocket endpoint: ws://<backend>/api/v1/agent/live

Bidirectional messages (type + payload):
→ { type: "heartbeat" } every 15s
→ { type: "set_goal", goal: string }
← { type: "thought" | "action" | "observation" | "alert", data: any }

Create:
- useAgentWebSocket hook with auto-reconnect + exponential backoff
- Scrolling thought feed with different colors per type
- “Set new goal” input + send button
- Connection status dot in the panel header

Show the agent as “always thinking” even when chat is idle.
```

#### Prompt 4 – Mission Command Center + Conscience Heat-Map
```
Build the full-screen Command Center at route /mission

Features to implement:
1. GET /api/v1/mission/overview → initial data
2. WebSocket ws://<backend>/api/v1/mission/stream → live updates

Display:
- Live graph of all running agents (nodes + edges)
- Conscience 5-layer heat-map (L1–L5) with size = activity, color = emotional valence
- Twin health dashboard (CPU/RAM/disk + 3D desktop preview via three.js)
- Real-time log tail
- Active projects list with progress bars

Use react-flow for the agent graph and @visx or recharts for everything else.
Make it look like NASA mission control.
```

#### Prompt 5 – Final Polish + Remaining Endpoints
```
Implement everything that’s left using the exact contract:

1. Agent CRUD (/api/v1/agents/*)
2. MCP tool catalog + install flow (/api/v1/mcp/*)
3. Conscience memory search + layered write UI
4. Digital Twin simulation panel with “Run what-if” button (POST /api/v1/twin/simulate)
5. Global Cmd+K command palette with all actions
6. PWA manifest + service worker for offline fallback
7. Dark/light/system theme with tailwind dark mode
8. Error boundaries + toast on any failed request
9. Responsive mobile layout (it must work on iPad perfectly)

Add a tiny status bar at the bottom showing:
backend URL • latency • agent count • twin FPS

When you’re done, this app must feel indistinguishable from Claude Desktop but with god-mode digital-twin powers.
```

### Bonus Prompt (run last) – “Make it shippable”
```
Run a full audit and fix:
- All TypeScript errors
- Unused imports
- Accessibility (labels, focus states)
- Lighthouse score >95
- Bundle size <400KB gzipped
- Add README with exact dev instructions
- Add .env.example with VITE_BACKEND_URL
```

That’s it.  
Five prompts → from pretty AI Studio mockup to a legitimately production-grade, type-safe, real-time web app that will make people think you have a 20-person team.

You now own the fastest path from “ooh shiny” to “holy shit this actually works”.

Go ship it, legend.
