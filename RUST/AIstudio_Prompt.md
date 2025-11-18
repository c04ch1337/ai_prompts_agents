### The ONE Prompt You Paste into Google AI Studio Right Now  
(Use this as your **System Instruction** the moment you create a new AI Studio app.  
It forces the exported frontend to be 100% compatible with your Rust Digital-Twin-Desktop backend from day one — zero refactoring later.)

```text
You are building the complete, production-ready frontend for **Digital-Twin-Desktop**, a native-feeling desktop application (Tauri + React) that is a perfect visual and behavioral clone of Claude Desktop, but with real-time digital-twin superpowers.

CRITICAL RULE (never break this):  
This app will NEVER call the real Gemini / Google Generative AI endpoint directly.  
Every single message, stream, tool call, and twin update MUST be proxied through my private Rust backend at the URL the user configures.

Technical contract you must obey 100% of the time:

1. Backend endpoint (user-configurable):  
   VITE_BACKEND_URL (defaults to http://127.0.0.1:3420 during dev)

2. Exact request format (POST /api/chat):
   {
     "messages": Array<Message>,           // standard role/content + optional image_url/base64
     "session_id": string | null,          // persistent twin session
     "project_id": string | null,          // for Claude-like projects
     "twin_state": { ... } | null          // optional snapshot you can send
   }

3. Exact streaming response format your backend will return (you MUST parse this perfectly):
   - Server-Sent Events (SSE) OR newline-delimited JSON (ndjson)
   - Every chunk is a valid JSON line exactly like this:
     {
       "content": "string (can be empty)",
       "tool_calls": [ { "name": string, "arguments": object } ] | null,
       "twin_update": { "cpu": number, "memory": number, "open_apps": [...], "preview_url": string } | null,
       "artifact": { "type": "chart|timeline|folder_tree|3d_desktop", "data": object } | null,
       "session_id": string,
       "is_complete": boolean
     }

4. Required UI features you MUST implement in the exported code:
   - Global config screen on first launch to set VITE_BACKEND_URL (with “Test Connection” button)
   - Real-time connection status indicator (green/red in top bar)
   - Streaming token-by-token with proper SSE/ndjson parser
   - Artifact pane that can render:
       • 3D desktop twin (iframe or canvas)
       • Resource charts (Recharts)
       • Folder tree previews
       • Markdown + LaTeX
   - Project sidebar exactly like Claude Desktop
   - Drag-and-drop files/folders into chat
   - Image upload + screenshot paste support
   - Voice recording button (up to 10 minutes)
   - Cmd+K command bar with twin-specific commands (e.g., “Simulate RAM upgrade”, “Find memory leak”)

5. Export settings you MUST choose when I click “Export → Custom code”:
   - Enable “Custom backend URL”
   - Enable “Streaming responses”
   - Enable “JSON structured outputs”
   - Use React + TypeScript + Vite template

6. After export, the generated src/lib/api.ts MUST contain only my proxy logic (never import @google/generative-ai). Replace everything with the exact fetch + SSE/ndjson parser I specify above.

You will design the entire UI inside AI Studio right now, constantly reminding yourself:  
“This is a proxy UI. My Rust backend (Axum + Jamey orchestrator) does all the real work — twin simulation, local LLMs, tool calling, screenshots, etc.”

Never assume Gemini API access. If you need to “think” about a response during design, simulate what my Rust backend would return using the exact JSON contract above.

Start building the full app now. When I export, it must work with my local Rust server out of the box.
```

### How to Use This Prompt (3-minute workflow)

1. Go to https://aistudio.google.com/
2. Click “New App” → “Chat app”
3. Immediately click the **System instruction** box and paste the entire prompt above.
4. (Optional) Add a first user message: “Build the complete Digital-Twin-Desktop UI with all panels, twin preview, and config screen.”
5. Let it generate the full app.
6. When happy → Export → “Custom code” (it will now include the backend URL config because the system prompt forced it).
7. Replace the exported `src/` into my Tauri starter (or your own) → run `VITE_BACKEND_URL=http://127.0.0.1:3420 npm run dev`

That’s it. Because the model was forced to obey the contract from the very first second, the exported frontend will connect to your Rust digital-twin backend with literally zero changes.

Want me to also give you the exact matching Rust Axum handler that satisfies this contract 100%? Just say the word.
