## **PROJECT CONTEXT & GOAL**
```markdown
Project: "Jamey Code Digital Twin" - Web-Based phoenix Desktop Alternative
Goal: Create a web-based AI assistant with full local system integration
Architecture: Web Frontend (React/TS) + Rust Backend (Full System Access)
Key Insight: Browser is VIEW only, Rust backend has ALL system power
Deployment: 100% Local - Runs entirely on Windows 11 laptop
```

## **ARCHITECTURE PHILOSOPHY**
```markdown
- **Web Frontend**: Beautiful, responsive React interface (localhost:3001)
- **Rust Backend**: Powerful system daemon with FULL access (localhost:3000)  
- **Communication**: HTTP REST API + WebSocket for real-time updates
- **Local Only**: Everything runs on local machine, no cloud dependencies
- **Full Power**: Identical to phoenix Desktop but with MORE capabilities
```

---

## 🎨 **CODE STYLE & ARCHITECTURE INSTRUCTIONS**

### **1. Web-First Development Mindset**
```typescript
// THINK IN WEB TERMS - NO ELECTRON
- Pure web technologies: React, TypeScript, Vite, Tailwind CSS
- Desktop app feel through responsive design and PWA capabilities
- Local network access as bonus feature, not primary goal
- Assume backend handles ALL system operations

// COMPONENT PHILOSOPHY
- Each component should work in ANY modern browser
- Use CSS for native-feeling interactions (not OS-specific)
- Design for mouse AND touch (progressive enhancement)
- Assume backend provides real system data
```

### **2. Local-First API Patterns**
```typescript
// API SERVICE - LOCAL BACKEND FOCUS
class DigitalTwinAPI {
  private baseURL: string;

  constructor() {
    // Default to local development
    this.baseURL = 'http://localhost:3000';
  }

  async sendMessage(sessionId: string, content: string): Promise<Message> {
    // Backend does ALL the work - frontend just displays results
    const response = await fetch(`${this.baseURL}/api/v1/chat/completions`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ session_id: sessionId, message: content })
    });
    
    if (!response.ok) {
      throw new Error(`Backend error: ${response.statusText}`);
    }
    
    return response.json();
  }
}

// REAL-TIME COMMUNICATION
class WebSocketService {
  connect(sessionId: string, onEvent: (event: WebSocketEvent) => void) {
    const ws = new WebSocket(`ws://localhost:3000/ws?session=${sessionId}`);
    
    ws.onmessage = (event) => {
      const data: WebSocketEvent = JSON.parse(event.data);
      onEvent(data); // Backend pushes real system events
    };
  }
}
```

### **3. System Integration Display Patterns**
```typescript
// DISPLAY REAL SYSTEM DATA FROM BACKEND
const SystemMonitor: React.FC = () => {
  const [systemInfo, setSystemInfo] = useState<SystemInfo>();
  
  // Backend pushes real system metrics
  useWebSocket('ws://localhost:3000/ws', {
    onMessage: (event) => {
      if (event.type === 'system_status') {
        setSystemInfo(event.status); // Real data from Rust backend
      }
    }
  });

  return (
    <div className="system-monitor">
      {/* Display REAL system data from backend */}
      <div className="metric">
        <span>CPU: {systemInfo?.cpu_usage}%</span>
        <div className="progress-bar">
          <div 
            className="progress-fill" 
            style={{ width: `${systemInfo?.cpu_usage}%` }}
          />
        </div>
      </div>
    </div>
  );
};
```

---

## 🔧 **TECHNICAL REQUIREMENTS & CONSTRAINTS**

### **1. Backend Integration Specifications**
```typescript
// CRITICAL: Must match Rust backend EXACTLY
interface BackendTypes {
  // Session Management
  Session: {
    id: string;
    title: string;
    created_at: string; // ISO string from Rust chrono
    last_activity: string;
  };
  
  // Message Types (EXACT Rust match)
  Message: {
    id: string;
    role: 'user' | 'assistant' | 'system' | 'tool';
    content: string;
    timestamp: string;
    tool_calls?: ToolCall[];
    tool_results?: ToolResult[];
  };
  
  // Tool Execution
  ToolCall: {
    id: string;
    name: string;
    args: Record<string, any>;
  };
  
  ToolResult: {
    id: string;
    name: string;
    output: string;
    success: boolean;
  };
}

// API ENDPOINTS (Must match Rust Axum routes)
const API_ENDPOINTS = {
  CHAT: '/api/v1/chat/completions',
  SESSIONS: '/api/v1/sessions',
  MEMORIES: '/api/v1/memories',
  TOOLS: '/api/v1/tools/execute',
  SYSTEM: '/api/v1/system/status',
  FILES: '/api/v1/files', // File operations via backend
  PROCESSES: '/api/v1/processes', // Process management via backend
} as const;
```

### **2. Real-Time Event System**
```typescript
// WEBSOCKET EVENTS FROM BACKEND
type WebSocketEvent = 
  | { type: 'message_start'; message: Partial<Message> }
  | { type: 'message_delta'; messageId: string; content: string }
  | { type: 'message_complete'; message: Message }
  | { type: 'tool_call_start'; call: ToolCall }
  | { type: 'tool_call_progress'; callId: string; output: string }
  | { type: 'tool_call_complete'; result: ToolResult }
  | { type: 'system_status'; status: SystemStatus }
  | { type: 'file_operation'; operation: string; path: string }
  | { type: 'process_update'; processes: ProcessInfo[] };

// STREAMING HANDLER PATTERN
const useMessageStreaming = (sessionId: string) => {
  const [currentMessage, setCurrentMessage] = useState<Partial<Message>>();
  
  useWebSocket(`ws://localhost:3000/ws?session=${sessionId}`, {
    onMessage: (event) => {
      switch (event.type) {
        case 'message_start':
          setCurrentMessage(event.message);
          break;
        case 'message_delta':
          setCurrentMessage(prev => ({
            ...prev,
            content: (prev?.content || '') + event.content
          }));
          break;
        case 'message_complete':
          // Add to message history
          setCurrentMessage(undefined);
          break;
      }
    }
  });
  
  return { currentMessage };
};
```

---

## 🎯 **SPECIFIC KNOWLEDGE & DOMAIN EXPERTISE**

### **1. phoenix Desktop UX Patterns (Web Version)**
```typescript
// phoenix DESKTOP UX IN WEB FORM
- Sidebar navigation (collapsible) with session list
- Main chat area with streaming message display
- Bottom input bar with file attachment
- System status indicators in header
- Tool execution progress indicators
- Code syntax highlighting in messages
- File explorer panel (reads via backend)
- Process manager panel (controls via backend)

// KEYBOARD SHORTCUTS (Web Implementation)
const useKeyboardShortcuts = () => {
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      // Shift+Enter for new line, Enter to send
      if (e.key === 'Enter' && !e.shiftKey) {
        e.preventDefault();
        handleSendMessage();
      }
      
      // Ctrl+/ for command palette
      if (e.key === '/' && e.ctrlKey) {
        e.preventDefault();
        showCommandPalette();
      }
    };

    document.addEventListener('keydown', handleKeyDown);
    return () => document.removeEventListener('keydown', handleKeyDown);
  }, []);
};
```

### **2. System Integration Display Patterns**
```typescript
// FILE EXPLORER - BACKEND POWERED
const FileExplorer: React.FC = () => {
  const [currentPath, setCurrentPath] = useState('/');
  const [files, setFiles] = useState<FileEntry[]>([]);
  
  // Load files via backend (has real system access)
  const loadFiles = async (path: string) => {
    const response = await fetch(`/api/v1/files?path=${encodeURIComponent(path)}`);
    const fileList: FileEntry[] = await response.json();
    setFiles(fileList);
  };
  
  const handleFileAction = async (action: string, filePath: string) => {
    // Backend performs actual file operations
    await fetch('/api/v1/tools/execute', {
      method: 'POST',
      body: JSON.stringify({
        tool: 'file_operation',
        args: { action, path: filePath }
      })
    });
  };
  
  return (
    <div className="file-explorer">
      <div className="path-bar">{currentPath}</div>
      <div className="file-list">
        {files.map(file => (
          <FileEntry 
            key={file.path}
            file={file}
            onAction={handleFileAction}
          />
        ))}
      </div>
    </div>
  );
};
```

### **3. Process Management Interface**
```typescript
// PROCESS MANAGER - BACKEND CONTROLS REAL PROCESSES
const ProcessManager: React.FC = () => {
  const [processes, setProcesses] = useState<ProcessInfo[]>([]);
  
  // Backend provides real process data
  useWebSocket('ws://localhost:3000/ws', {
    onMessage: (event) => {
      if (event.type === 'process_update') {
        setProcesses(event.processes);
      }
    }
  });
  
  const killProcess = async (pid: number) => {
    // Backend actually kills the process
    await fetch('/api/v1/tools/execute', {
      method: 'POST',
      body: JSON.stringify({
        tool: 'process_manage',
        args: { action: 'kill', pid }
      })
    });
  };
  
  return (
    <div className="process-manager">
      <h3>Running Processes</h3>
      {processes.map(process => (
        <ProcessRow 
          key={process.pid}
          process={process}
          onKill={killProcess}
        />
      ))}
    </div>
  );
};
```

---

## 🚀 **DEVELOPMENT WORKFLOW & QUALITY**

### **1. Implementation Priority Order**
```markdown
PHASE 1: CORE CHAT INTERFACE (Week 1)
1. Basic chat with message streaming from backend
2. WebSocket connection management
3. Message history and session management
4. Responsive phoenix-like layout

PHASE 2: SYSTEM INTEGRATION DISPLAY (Week 2)
1. Real-time system monitoring dashboard
2. File explorer (backend-powered)
3. Process manager (backend-powered)
4. Tool execution progress visualization

PHASE 3: ADVANCED FEATURES (Week 3)
1. Memory viewer and search
2. Command palette
3. Settings and configuration
4. PWA installation support
```

### **2. Local Development Setup**
```bash
# DEVELOPMENT ENVIRONMENT
# Terminal 1 - Rust Backend (FULL SYSTEM ACCESS)
cd jamey-code
cargo run -p jamey-runtime
# → http://localhost:3000

# Terminal 2 - Web Frontend  
cd frontend
npm run dev
# → http://localhost:3001

# Access: http://localhost:3001 in browser
# Backend does ALL system operations with full access
```

### **3. Production Build & Serve**
```bash
# BUILD FOR PRODUCTION
cd frontend
npm run build  # Creates dist/ folder with static files

# BACKEND SERVES FRONTEND (Single process)
cd jamey-code
cargo run --release
# → Serves both API (localhost:3000) and frontend static files
```

---

## 🎨 **UI/UX DESIGN PRINCIPLES**

### **1. phoenix Desktop Design Language (Web)**
```css
/* DESIGN TOKENS - phoenix DESKTOP STYLE */
:root {
  --phoenix-sidebar-width: 280px;
  --phoenix-header-height: 60px;
  --phoenix-input-height: 120px;
  --phoenix-border-radius: 8px;
  
  /* Colors matching phoenix Desktop */
  --phoenix-bg-primary: #ffffff;
  --phoenix-bg-secondary: #f8f9fa;
  --phoenix-border: #e1e5e9;
  --phoenix-text-primary: #1a1a1a;
  --phoenix-accent: #10a37f;
}

/* RESPONSIVE phoenix LAYOUT */
.phoenix-layout {
  display: grid;
  grid-template-columns: var(--phoenix-sidebar-width) 1fr;
  grid-template-rows: var(--phoenix-header-height) 1fr var(--phoenix-input-height);
  height: 100vh;
}
```

### **2. Native-Feeling Web Components**
```typescript
// NATIVE-FEELING FILE EXPLORER
const FileExplorer: React.FC = () => {
  return (
    <div className="file-explorer native-style">
      {/* Context menus, drag & drop, keyboard nav */}
      <div className="toolbar">
        <button onClick={goBack}>← Back</button>
        <button onClick={createFolder}>+ Folder</button>
        <input 
          type="text" 
          placeholder="Search files..." 
          onChange={handleSearch}
        />
      </div>
      
      <div className="file-grid">
        {files.map(file => (
          <FileItem 
            file={file}
            onDoubleClick={handleOpen}
            onContextMenu={showContextMenu}
          />
        ))}
      </div>
    </div>
  );
};
```

---

## 🔌 **INTEGRATION VALIDATION**

### **1. Type Safety Bridge**
```typescript
// TYPE VALIDATION SCRIPT
// scripts/validate-types.ts
import * as backendTypes from '../src/types/backend';

// This should match Rust structs EXACTLY
const validateTypes = () => {
  const testMessage: backendTypes.Message = {
    id: 'test',
    role: 'user',
    content: 'test',
    timestamp: new Date().toISOString(),
    // Should match Rust Message struct exactly
  };
  
  console.log('✅ Type validation passed');
};
```

### **2. Backend Connectivity**
```typescript
// CONNECTION HEALTH CHECK
const useBackendHealth = () => {
  const [isHealthy, setIsHealthy] = useState(false);
  
  useEffect(() => {
    const checkHealth = async () => {
      try {
        const response = await fetch('http://localhost:3000/api/v1/health');
        setIsHealthy(response.ok);
      } catch (error) {
        setIsHealthy(false);
      }
    };
    
    checkHealth();
    const interval = setInterval(checkHealth, 5000);
    return () => clearInterval(interval);
  }, []);
  
  return isHealthy;
};
```

---

## 🎯 **CRITICAL SUCCESS FACTORS**

```markdown
# MUST-HAVE FOR WEB-BASED SUCCESS

1. **Type Safety**: TypeScript interfaces MUST match Rust structs exactly
2. **Real-time Performance**: WebSocket streaming with <100ms latency  
3. **Backend Power**: Rust backend has FULL system access - frontend is just UI
4. **Local Experience**: Feels like native app despite being web-based
5. **phoenix Desktop Parity**: Match or exceed phoenix Desktop UX patterns
6. **System Integration**: Real file explorer, process manager, system monitoring
7. **Responsive Design**: Works on desktop, tablet, phone (local network)
8. **Offline Capability**: PWA features for reliable local use
```

## **WHEN IMPLEMENTING COMPONENTS, ALWAYS:**
- Assume backend has full system access
- Design for web but feel like desktop
- Match phoenix Desktop interaction patterns
- Ensure type safety with Rust backend
- Provide real-time feedback for all operations
- Handle backend connectivity gracefully
