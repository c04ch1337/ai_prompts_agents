# 🎯 Custom Instructions for Google AI Studio - Jamey Code Project

Here are comprehensive custom instructions to ensure consistent, high-quality development for your Digital Twin frontend:

---

## **🤖 PROJECT-SPECIFIC CUSTOM INSTRUCTIONS**

### **PROJECT CONTEXT & GOAL**
```markdown
Project: "Jamey Code Digital Twin" - Claude Desktop Alternative
Goal: Create a desktop AI assistant with full system integration
Backend: Rust + Axum + PostgreSQL + OpenRouter
Frontend: React + TypeScript + Electron + Tailwind CSS
Target: Seamless system integration with real-time capabilities
```

### **DEVELOPMENT PHILOSOPHY**
```markdown
- **Security First**: Even with full system access, implement proper safeguards
- **Type Safety**: Exact type matching between TypeScript and Rust
- **Real-time Performance**: Optimize for streaming and instant updates
- **Native Desktop Experience**: Feel like a native app, not a web page
- **Claude Desktop Parity**: Match or exceed Claude Desktop UX patterns
```

---

## **🎨 CODE STYLE & ARCHITECTURE INSTRUCTIONS**

### **1. TypeScript/React Conventions**
```typescript
// FILE ORGANIZATION
- Use functional components with hooks
- Prefer explicit typing over 'any'
- Use named exports over default exports
- Group related components in feature folders
- Separate concerns: hooks, services, components, types

// NAMING CONVENTIONS
- Interfaces: PascalCase with 'I' prefix (IChatMessage, IApiResponse)
- Components: PascalCase (ChatInterface, MessageBubble)
- Hooks: camelCase with 'use' prefix (useWebSocket, useSession)
- Services: camelCase (apiService, websocketService)
- Constants: UPPER_SNAKE_CASE (API_ENDPOINTS, DEFAULT_SETTINGS)

// CODE STYLE
- Use async/await over Promise chains
- Destructure props in component parameters
- Use const assertions for immutable objects
- Prefer template literals over string concatenation
- Use optional chaining and nullish coalescing
```

### **2. Component Architecture Rules**
```typescript
// COMPONENT STRUCTURE
interface ComponentProps {
  // Props should be minimal and well-typed
  data: DataType;
  onEvent: (payload: EventPayload) => void;
  isLoading?: boolean;
}

const MyComponent: React.FC<ComponentProps> = ({ 
  data, 
  onEvent, 
  isLoading = false 
}) => {
  // Hooks at the top
  const [state, setState] = useState<StateType>(initialState);
  const { data: hookData } = useCustomHook();
  
  // Event handlers
  const handleAction = useCallback((payload: Payload) => {
    // Logic here
    onEvent(payload);
  }, [onEvent]);
  
  // Derived state
  const derivedData = useMemo(() => 
    data.filter(item => item.isActive), 
  [data]);
  
  // Render logic
  if (isLoading) return <LoadingSpinner />;
  
  return (
    <div className="container">
      {/* JSX with proper accessibility */}
    </div>
  );
};

export { MyComponent };
```

### **3. State Management Patterns**
```typescript
// STATE MANAGEMENT RULES
- Use React Query for server state
- Use Zustand or Context + useReducer for complex client state
- Keep state as close to usage as possible
- Normalize state shape to avoid duplication
- Use immer for immutable updates if needed

// HOOK PATTERNS
const useChat = (sessionId: string) => {
  const [messages, setMessages] = useState<Message[]>([]);
  const queryClient = useQueryClient();
  
  // Separate concerns into custom hooks
  const { sendMessage, isSending } = useSendMessage();
  const { messages, isLoading } = useMessages(sessionId);
  
  return {
    messages,
    sendMessage,
    isSending,
    isLoading
  };
};
```

---

## **🔧 TECHNICAL REQUIREMENTS & CONSTRAINTS**

### **1. Backend Integration Specifications**
```typescript
// CRITICAL: Must match Rust backend exactly
interface BackendTypes {
  // Session Management
  Session: {
    id: string;
    title: string;
    created_at: string; // ISO string from Rust
    last_activity: string;
  };
  
  // Message Types
  Message: {
    id: string;
    role: 'user' | 'assistant' | 'system' | 'tool';
    content: string;
    timestamp: string;
    tool_calls?: ToolCall[];
    tool_results?: ToolResult[];
  };
  
  // Tool System
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
} as const;
```

### **2. Real-time Communication Rules**
```typescript
// WEBSOCKET IMPLEMENTATION RULES
- Use ReconnectingWebSocket for reliability
- Implement exponential backoff for reconnections
- Handle connection states: connecting, open, closing, closed
- Buffer messages when disconnected
- Use message queuing for important events

// STREAMING REQUIREMENTS
- Display partial messages as they stream
- Show tool execution progress in real-time
- Update system status indicators live
- Animate state changes smoothly
```

### **3. Performance Constraints**
```typescript
// PERFORMANCE TARGETS
- First render: < 100ms
- Message streaming: < 50ms latency
- Tool execution updates: < 100ms
- Large conversation scroll: 60fps
- Memory usage: < 500MB typical

// OPTIMIZATION RULES
- Virtualize long message lists
- Lazy load heavy components
- Memoize expensive calculations
- Debounce rapid events
- Use React.memo for expensive re-renders
```

---

## **🎯 SPECIFIC KNOWLEDGE & DOMAIN EXPERTISE**

### **1. Claude Desktop Behavior Patterns**
```markdown
# CLAUDE DESKTOP UX PATTERNS TO EMULATE

**Input Behavior:**
- Shift+Enter for new line, Enter to send
- Auto-resizing textarea
- File drag-and-drop with preview
- Smart suggestions based on context

**Message Display:**
- Streaming text with typing indicator
- Code blocks with syntax highlighting
- Tool execution with progress indicators
- Collapsible long outputs

**Navigation:**
- Collapsible sidebar with sessions
- Quick search through history
- Keyboard shortcuts for all actions
- System tray integration

**System Integration:**
- Native file dialogs
- System notification support
- Auto-start on login option
- Update notifications
```

### **2. System Integration Expertise**
```typescript
// ELECTRON-SPECIFIC KNOWLEDGE
- Use contextBridge for secure IPC
- Implement proper app lifecycle management
- Handle multiple windows appropriately
- Support platform-specific features
- Manage auto-updates safely

// SYSTEM TOOLS INTEGRATION
- File system operations with progress
- Process management with real-time updates
- Memory visualization with search
- System monitoring with charts
```

### **3. AI/LLM Integration Patterns**
```typescript
// STREAMING HANDLING
interface StreamingHandler {
  onMessageStart: (message: PartialMessage) => void;
  onContentDelta: (messageId: string, content: string) => void;
  onToolCallStart: (toolCall: ToolCall) => void;
  onToolCallComplete: (result: ToolResult) => void;
  onMessageComplete: (message: Message) => void;
  onError: (error: Error) => void;
}

// CONTEXT MANAGEMENT
- Maintain conversation context window
- Handle token counting and limits
- Manage system prompts appropriately
- Handle tool schemas and validation
```

---

## **🚀 DEVELOPMENT WORKFLOW & QUALITY**

### **1. Implementation Priority Order**
```markdown
PHASE 1: CORE CHAT (Week 1)
1. Basic chat interface with streaming
2. Message history and session management
3. WebSocket connection management
4. Basic styling and layout

PHASE 2: SYSTEM INTEGRATION (Week 2)
1. Tool execution visualization
2. File system browser
3. Process manager interface
4. Memory viewer

PHASE 3: POLISH & PERFORMANCE (Week 3)
1. Advanced features (search, export, etc.)
2. Performance optimizations
3. Cross-platform testing
4. Documentation and error handling
```

### **2. Quality Assurance Rules**
```typescript
// TESTING REQUIREMENTS
- Unit tests for all utilities and hooks
- Integration tests for API communication
- E2E tests for critical user flows
- Performance tests for large datasets
- Cross-browser and cross-platform testing

// ERROR HANDLING PATTERNS
const ErrorBoundary: React.FC<{ children: ReactNode }> = ({ children }) => {
  const [hasError, setHasError] = useState(false);
  
  useEffect(() => {
    const handleError = (error: Error) => {
      console.error('App error:', error);
      setHasError(true);
    };
    
    window.addEventListener('error', handleError);
    return () => window.removeEventListener('error', handleError);
  }, []);
  
  if (hasError) {
    return <ErrorFallback />;
  }
  
  return children;
};
```

### **3. Documentation Standards**
```markdown
# DOCUMENTATION REQUIREMENTS

**Component Documentation:**
- Props interface with JSDoc comments
- Usage examples
- Behavior descriptions
- Accessibility notes

**Hook Documentation:**
- Parameters and return values
- Side effects and dependencies
- Error states
- Usage examples

**Service Documentation:**
- API endpoints and payloads
- Error responses
- Authentication requirements
- Rate limiting information
```

---

## **🔮 ADVANCED FEATURES & EXTENSIBILITY**

### **1. Plugin System Considerations**
```typescript
// EXTENSIBILITY PATTERNS
interface PluginAPI {
  registerTool: (tool: ToolDefinition) => void;
  registerView: (view: ViewDefinition) => void;
  registerCommand: (command: CommandDefinition) => void;
}

// THEME SYSTEM
interface Theme {
  colors: ColorPalette;
  spacing: SpacingScale;
  typography: TypographyScale;
  components: ComponentThemes;
}
```

### **2. Internationalization Ready**
```typescript
// I18N PREPARATION
const i18n = {
  locales: ['en', 'es', 'fr', 'de', 'ja', 'zh'],
  defaultLocale: 'en',
  namespace: 'translation',
} as const;
```

### **3. Analytics & Monitoring**
```typescript
// TELEMETRY (OPT-IN)
interface AnalyticsEvent {
  type: 'message_sent' | 'tool_executed' | 'error_occurred';
  timestamp: number;
  session_id: string;
  data: Record<string, any>;
}
```

---

## **🎪 SPECIFIC PROMPTING TECHNIQUES**

### **When Asking for Implementation:**
```markdown
ALWAYS INCLUDE:
- Exact TypeScript interfaces matching Rust backend
- Proper error handling patterns
- Loading states and user feedback
- Accessibility attributes
- Performance considerations
- Testing strategy
```

### **When Asking for Components:**
```markdown
COMPONENT REQUIREMENTS CHECKLIST:
✅ TypeScript interface for props
✅ Proper styling with Tailwind CSS
✅ Loading and error states
✅ Keyboard navigation support
✅ Screen reader accessibility
✅ Responsive design
✅ Performance optimizations
✅ Usage examples
```

### **When Debugging Issues:**
```markdown
PROBLEM-SOLVING APPROACH:
1. Reproduce the issue with minimal example
2. Check TypeScript types match Rust exactly
3. Verify WebSocket message formats
4. Check error boundaries and fallbacks
5. Validate state management logic
6. Test across different platforms
```

---

## **📚 KEY REFERENCES & INSPIRATION**

```markdown
# REFERENCE APPLICATIONS
- Claude Desktop (primary UX reference)
- VS Code (for code editing features)
- Windows Terminal (for performance patterns)
- Discord (for real-time communication)
- Obsidian (for knowledge management)

# TECHNICAL REFERENCES
- React TypeScript Cheatsheets
- Electron Best Practices
- WebSocket RFC 6455
- Monaco Editor Documentation
- Tailwind CSS Patterns
```

These custom instructions will ensure Google AI Studio produces consistent, high-quality code that perfectly integrates with your Rust backend while maintaining Claude Desktop-level user experience and performance.

**Remember to provide this context at the start of every session with Google AI Studio to maintain consistency across development iterations.**
