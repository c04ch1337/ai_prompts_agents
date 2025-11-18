## 🚀 **FRONTEND DEVELOPMENT PROMPT FOR GOOGLE AI STUDIO**

### **PROJECT OVERVIEW**
Develop a Claude Desktop-like application frontend that connects to my Rust backend Digital Twin system. The frontend should provide a seamless, intuitive interface for interacting with the AI agent that has full system access capabilities.

### **TECHNICAL REQUIREMENTS**

#### **Core Stack:**
```typescript
// REQUIRED TECHNOLOGIES
- React 18 + TypeScript
- Tailwind CSS for styling
- Vite as build tool
- Electron for desktop wrapper
- Axios for API communication
- WebSockets for real-time updates
- Monaco Editor for code editing features
```

#### **Backend Integration Specifications:**
```rust
// BACKEND API ENDPOINTS (Rust Axum)
- POST /api/v1/chat/completions - Main chat endpoint
- GET  /api/v1/sessions - Session management
- GET  /api/v1/memories - Long-term memory access
- POST /api/v1/tools/execute - Tool execution
- WS   /ws - WebSocket for real-time updates
```

### **ARCHITECTURAL REQUIREMENTS**

#### **1. State Management Structure:**
```typescript
interface AppState {
  // Session Management
  currentSession: Session;
  sessions: Session[];
  
  // Chat Interface
  messages: Message[];
  isProcessing: boolean;
  
  // System Integration
  systemStatus: SystemStatus;
  activeTools: Tool[];
  memoryContext: MemoryContext;
  
  // Configuration
  settings: AppSettings;
}

interface Session {
  id: string;
  title: string;
  createdAt: Date;
  lastActivity: Date;
  messageCount: number;
}

interface Message {
  id: string;
  role: 'user' | 'assistant' | 'system' | 'tool';
  content: string;
  timestamp: Date;
  toolCalls?: ToolCall[];
  toolResults?: ToolResult[];
}
```

#### **2. Real-time Communication:**
```typescript
// WebSocket Events to Handle
type WebSocketEvent = 
  | { type: 'message_start'; message: Partial<Message> }
  | { type: 'message_delta'; messageId: string; content: string }
  | { type: 'message_complete'; message: Message }
  | { type: 'tool_call_start'; call: ToolCall }
  | { type: 'tool_call_complete'; result: ToolResult }
  | { type: 'system_status'; status: SystemStatus }
  | { type: 'memory_update'; memory: MemoryRecord };
```

#### **3. Component Architecture:**
```
src/
├── components/
│   ├── chat/
│   │   ├── ChatInterface.tsx      # Main chat component
│   │   ├── MessageBubble.tsx      # Individual messages
│   │   ├── ToolCallView.tsx       # Tool execution visualizer
│   │   └── TypingIndicator.tsx    # Real-time typing
│   ├── sidebar/
│   │   ├── SessionList.tsx        # Session management
│   │   ├── SystemMonitor.tsx      # Real-time system stats
│   │   └── ToolPanel.tsx          # Available tools display
│   ├── system/
│   │   ├── FileExplorer.tsx       # Integrated file browser
│   │   ├── ProcessManager.tsx     # System process viewer
│   │   └── MemoryViewer.tsx       # Long-term memory browser
│   └── common/
│       ├── CodeEditor.tsx         # Monaco-based code editor
│       ├── MarkdownRenderer.tsx   # Enhanced markdown
│       └── StatusIndicator.tsx    # Connection status
├── hooks/
│   ├── useWebSocket.ts            # WebSocket management
│   ├── useSession.ts              # Session management
│   ├── useTools.ts                # Tool execution
│   └── useMemory.ts               # Memory operations
├── services/
│   ├── api.ts                     # REST API client
│   ├── websocket.ts               # WebSocket service
│   └── storage.ts                 # Local storage management
└── types/
    └── backend.ts                 # TypeScript types matching Rust structs
```

### **UI/UX REQUIREMENTS**

#### **1. Claude Desktop-like Interface:**
```typescript
// Design Specifications
- Sidebar navigation (collapsible)
- Main chat area with message history
- Bottom input bar with file attachment
- System status indicators
- Dark/light theme support
- Keyboard shortcuts matching Claude Desktop
```

#### **2. Real-time Features:**
- Streaming message responses
- Live tool execution progress
- System resource monitoring
- Connection status indicators
- Auto-scrolling with pause on user interaction

#### **3. System Integration Views:**
```typescript
// Required System Panels
interface SystemPanels {
  fileExplorer: {
    currentPath: string;
    fileTree: FileNode[];
    allowNavigation: boolean;
  };
  processManager: {
    processes: ProcessInfo[];
    refreshInterval: number;
  };
  memoryViewer: {
    memories: MemoryRecord[];
    searchQuery: string;
    memoryTypes: MemoryType[];
  };
}
```

### **BACKEND INTEGRATION SPECIFICS**

#### **1. Type Matching (Critical):**
```typescript
// MUST MATCH RUST BACKEND TYPES EXACTLY
interface ToolCall {
  id: string;
  name: string;
  args: Record<string, any>;
}

interface ToolResult {
  id: string;
  name: string;
  output: string;
  success: boolean;
}

interface MemoryRecord {
  id: string;
  session_id: string;
  content: string;
  memory_type: 'conversation' | 'knowledge' | 'preference' | 'fact' | 'skill' | 'relationship';
  importance: number;
  created_at: string;
  last_accessed: string;
}

interface SessionStateResponse {
  session_id: string;
  agent_state: 'thinking' | 'executing' | 'responding' | 'idle';
  active_tools: string[];
  memory_usage: number;
}
```

#### **2. API Service Implementation:**
```typescript
class DigitalTwinAPI {
  private baseURL: string;
  
  async sendMessage(messages: Message[], tools?: ToolSpec[]): Promise<Message> {
    // Implementation that matches Rust backend
  }
  
  async executeTool(toolCall: ToolCall): Promise<ToolResult> {
    // Implementation that matches Rust backend
  }
  
  async searchMemories(query: MemoryQuery): Promise<MemoryRecord[]> {
    // Implementation that matches Rust backend
  }
  
  async getSessions(): Promise<Session[]> {
    // Implementation that matches Rust backend
  }
}
```

#### **3. WebSocket Service:**
```typescript
class DigitalTwinWebSocket {
  connect(sessionId: string): void {
    // Connect to Rust backend WebSocket
  }
  
  onMessage(callback: (event: WebSocketEvent) => void): void {
    // Handle real-time events from backend
  }
  
  sendUserMessage(content: string, attachments?: File[]): void {
    // Send messages to backend
  }
}
```

### **FEATURE REQUIREMENTS**

#### **1. Core Chat Features:**
- [ ] Streaming responses with typing indicators
- [ ] Tool execution with real-time progress
- [ ] Message history with infinite scroll
- [ ] File upload and attachment
- [ ] Code syntax highlighting
- [ ] Markdown rendering with LaTeX support
- [ ] Copy/paste functionality
- [ ] Search through conversation history

#### **2. System Integration Features:**
- [ ] Real-time system monitoring dashboard
- [ ] File system browser and editor
- [ ] Process management interface
- [ ] Memory exploration and search
- [ ] Tool registry browser
- [ ] Configuration management

#### **3. Advanced Features:**
- [ ] Session import/export
- [ ] Memory management (pin, delete, search)
- [ ] Custom tool creation interface
- [ ] System command interface
- [ ] Auto-complete for tool parameters
- [ ] Context window management

### **PERFORMANCE REQUIREMENTS**

```typescript
// Performance Specifications
- Message rendering: < 50ms for 1000 messages
- WebSocket reconnection: < 2 seconds
- Tool execution updates: real-time (< 100ms)
- File operations: progress indicators for large files
- Memory usage: < 500MB for typical sessions
```

### **ELECTRON CONFIGURATION**

```javascript
// electron/main.js requirements
const mainWindow = new BrowserWindow({
  width: 1200,
  height: 800,
  minWidth: 800,
  minHeight: 600,
  webPreferences: {
    nodeIntegration: false,
    contextIsolation: true,
    enableRemoteModule: false,
    preload: path.join(__dirname, 'preload.js')
  },
  titleBarStyle: 'hiddenInset', // macOS style
  frame: process.platform !== 'darwin' // Windows/Linux frame
});

// Preload script for secure backend communication
contextBridge.exposeInMainWorld('electronAPI', {
  openFile: () => ipcRenderer.invoke('dialog:openFile'),
  getSystemInfo: () => ipcRenderer.invoke('system:info'),
  // ... other secure methods
});
```

### **DEVELOPMENT WORKFLOW**

#### **1. Setup Instructions:**
```bash
# Development environment
npm create vite@latest jamey-frontend -- --template react-ts
cd jamey-frontend
npm install tailwindcss @types/ws monaco-editor axios
npm install -D electron electron-builder
```

#### **2. Build Configuration:**
```javascript
// vite.config.ts for Electron
export default defineConfig({
  base: './',
  build: {
    outDir: 'dist-react',
  },
  server: {
    port: 3001,
    proxy: {
      '/api': 'http://localhost:3000',
      '/ws': {
        target: 'ws://localhost:3000',
        ws: true
      }
    }
  }
});
```

### **TESTING REQUIREMENTS**

```typescript
// Test coverage requirements
- Unit tests for all hooks and utilities
- Integration tests for API communication
- E2E tests for critical user flows
- Performance tests for large conversations
- Cross-platform testing (Windows, macOS, Linux)
```

### **DELIVERABLES EXPECTED**

1. **Complete React TypeScript application** with all components
2. **Electron wrapper** with proper build configuration
3. **Full backend integration** with type-safe API clients
4. **Real-time WebSocket implementation**
5. **System monitoring dashboard**
6. **Comprehensive documentation** for extension
7. **Build scripts** for all platforms

### **INTEGRATION VALIDATION**

The frontend must successfully:
- [ ] Connect to Rust backend on localhost:3000
- [ ] Handle streaming responses from OpenRouter
- [ ] Execute system tools through the backend
- [ ] Display real-time system status
- [ ] Manage long-term memory through PostgreSQL
- [ ] Handle file operations through backend tools
- [ ] Maintain session state across restarts

---

## 🎯 **CRITICAL SUCCESS FACTORS**

1. **Type Safety**: Frontend types must exactly match Rust backend structs
2. **Real-time Performance**: Smooth streaming and tool execution updates
3. **System Integration**: Seamless connection to backend system tools
4. **User Experience**: Claude Desktop-level polish and responsiveness
5. **Cross-platform**: Works on Windows, macOS, and Linux
