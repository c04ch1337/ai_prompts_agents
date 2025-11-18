
```css
/* ============================================================================
   FERRELLGAS THEMED CLAUDE DESKTOP
   Claude Desktop interface with Ferrellgas color scheme
   ============================================================================ */

/* ----------------------------------------------------------------------------
   ROOT VARIABLES & TOKENS - FERRELLGAS COLOR SCHEME
   ---------------------------------------------------------------------------- */

:root {
  /* ========== FERRELLGAS COLOR PALETTE ========== */
  
  /* Brand Colors - Ferrellgas Blue & Red */
  --brand-primary: #0046be;        /* Ferrellgas Primary Blue */
  --brand-primary-hover: #0039a1;  /* Darker Blue */
  --brand-primary-active: #002d72; /* Ferrellgas Dark Blue */
  --brand-light: #e6ecfa;          /* Light Blue Tint */
  --brand-dark: #002d72;           /* Ferrellgas Secondary Blue */
  --brand-accent: #da291c;         /* Ferrellgas Red */
  --brand-accent-hover: #b82217;   /* Darker Red */
  
  /* Neutral Grays - Light Mode */
  --gray-50: #FAFAFA;
  --gray-100: #F5F5F5;
  --gray-200: #EEEEEE;
  --gray-300: #E0E0E0;
  --gray-400: #BDBDBD;
  --gray-500: #9E9E9E;
  --gray-600: #757575;
  --gray-700: #616161;
  --gray-800: #424242;
  --gray-900: #212121;
  
  /* Semantic Colors - Adjusted for Ferrellgas */
  --success: #16A34A;
  --success-light: #e6f7ec;
  --success-dark: #15803D;
  
  --warning: #f57c00;
  --warning-light: #fff3e0;
  --warning-dark: #e65100;
  
  --error: #da291c;                /* Ferrellgas Red */
  --error-light: #fde8e6;
  --error-dark: #b82217;
  
  --info: #0046be;                 /* Ferrellgas Blue */
  --info-light: #e6ecfa;
  --info-dark: #002d72;
  
  /* ========== LIGHT MODE COLORS ========== */
  --bg-primary: #FFFFFF;
  --bg-secondary: #F8F9FA;
  --bg-tertiary: #E9ECEF;
  --bg-elevated: #FFFFFF;
  --bg-overlay: rgba(0, 0, 0, 0.4);
  
  --text-primary: #212121;
  --text-secondary: #616161;
  --text-tertiary: #9E9E9E;
  --text-placeholder: #BDBDBD;
  --text-inverse: #FFFFFF;
  
  --border-default: #E0E0E0;
  --border-subtle: #F5F5F5;
  --border-strong: #BDBDBD;
  
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
  --shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
  
  /* ========== TYPOGRAPHY ========== */
  
  /* Font Families */
  --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", 
               "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", 
               sans-serif;
  --font-mono: "SF Mono", "Monaco", "Inconsolata", "Fira Code", "Fira Mono", 
               "Roboto Mono", monospace;
  
  /* Font Sizes */
  --text-xs: 0.75rem;      /* 12px */
  --text-sm: 0.875rem;     /* 14px */
  --text-base: 1rem;       /* 16px */
  --text-lg: 1.125rem;     /* 18px */
  --text-xl: 1.25rem;      /* 20px */
  --text-2xl: 1.5rem;      /* 24px */
  --text-3xl: 1.875rem;    /* 30px */
  --text-4xl: 2.25rem;     /* 36px */
  
  /* Font Weights */
  --font-light: 300;
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;
  
  /* Line Heights */
  --leading-tight: 1.25;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;
  
  /* ========== SPACING SCALE ========== */
  --space-0: 0;
  --space-1: 0.25rem;     /* 4px */
  --space-2: 0.5rem;      /* 8px */
  --space-3: 0.75rem;     /* 12px */
  --space-4: 1rem;        /* 16px */
  --space-5: 1.25rem;     /* 20px */
  --space-6: 1.5rem;      /* 24px */
  --space-8: 2rem;        /* 32px */
  --space-10: 2.5rem;     /* 40px */
  --space-12: 3rem;       /* 48px */
  --space-16: 4rem;       /* 64px */
  --space-20: 5rem;       /* 80px */
  
  /* ========== BORDER RADIUS ========== */
  --radius-sm: 0.25rem;   /* 4px */
  --radius-md: 0.5rem;    /* 8px */
  --radius-lg: 0.75rem;   /* 12px */
  --radius-xl: 1rem;      /* 16px */
  --radius-2xl: 1.5rem;   /* 24px */
  --radius-full: 9999px;
  
  /* ========== TRANSITIONS ========== */
  --transition-fast: 150ms cubic-bezier(0.4, 0, 0.2, 1);
  --transition-base: 200ms cubic-bezier(0.4, 0, 0.2, 1);
  --transition-slow: 300ms cubic-bezier(0.4, 0, 0.2, 1);
  
  /* ========== Z-INDEX SCALE ========== */
  --z-dropdown: 1000;
  --z-sticky: 1020;
  --z-fixed: 1030;
  --z-modal-backdrop: 1040;
  --z-modal: 1050;
  --z-popover: 1060;
  --z-tooltip: 1070;
}

/* ========== DARK MODE - FERRELLGAS THEMED ========== */

[data-theme="dark"] {
  /* Dark Mode Backgrounds */
  --bg-primary: #121212;
  --bg-secondary: #1e1e1e;
  --bg-tertiary: #2d2d2d;
  --bg-elevated: #252525;
  --bg-overlay: rgba(0, 0, 0, 0.6);
  
  /* Dark Mode Text */
  --text-primary: #ffffff;
  --text-secondary: #b0b0b0;
  --text-tertiary: #888888;
  --text-placeholder: #666666;
  --text-inverse: #121212;
  
  /* Dark Mode Borders */
  --border-default: #3a3a3a;
  --border-subtle: #2d2d2d;
  --border-strong: #4a4a4a;
  
  /* Dark Mode Shadows */
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.3);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.4), 0 2px 4px -1px rgba(0, 0, 0, 0.3);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.4), 0 4px 6px -2px rgba(0, 0, 0, 0.3);
  --shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.4), 0 10px 10px -5px rgba(0, 0, 0, 0.3);
  
  /* Adjust Brand Colors for Dark Mode */
  --brand-primary: #4d7cff;
  --brand-primary-hover: #3a6bf0;
  --brand-light: #1a2b5c;
  --brand-accent: #ff6b5c;
  --brand-accent-hover: #ff5544;
}

/* ----------------------------------------------------------------------------
   BASE STYLES
   ---------------------------------------------------------------------------- */

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  font-size: 16px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

body {
  font-family: var(--font-sans);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  color: var(--text-primary);
  background-color: var(--bg-primary);
  transition: background-color var(--transition-base), color var(--transition-base);
}

/* ----------------------------------------------------------------------------
   LAYOUT COMPONENTS
   ---------------------------------------------------------------------------- */

/* Main Application Container */
.app-container {
  display: flex;
  height: 100vh;
  overflow: hidden;
  background-color: var(--bg-primary);
}

/* Sidebar */
.sidebar {
  width: 260px;
  background-color: var(--bg-secondary);
  border-right: 1px solid var(--border-default);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  transition: all var(--transition-base);
}

.sidebar-header {
  padding: var(--space-4);
  border-bottom: 1px solid var(--border-default);
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: linear-gradient(135deg, var(--brand-primary) 0%, var(--brand-dark) 100%);
  color: white;
}

.sidebar-content {
  flex: 1;
  overflow-y: auto;
  padding: var(--space-2);
}

.sidebar-footer {
  padding: var(--space-4);
  border-top: 1px solid var(--border-default);
}

/* Main Chat Area */
.chat-container {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.chat-header {
  padding: var(--space-4) var(--space-6);
  border-bottom: 1px solid var(--border-default);
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: var(--bg-primary);
  height: 60px;
}

.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: var(--space-6);
  display: flex;
  flex-direction: column;
  gap: var(--space-6);
}

.chat-input-container {
  padding: var(--space-6);
  border-top: 1px solid var(--border-default);
  background-color: var(--bg-primary);
}

/* ----------------------------------------------------------------------------
   MESSAGE COMPONENTS
   ---------------------------------------------------------------------------- */

/* Message Bubble */
.message {
  display: flex;
  gap: var(--space-3);
  max-width: 48rem;
  margin: 0 auto;
  width: 100%;
}

.message-user {
  flex-direction: row-reverse;
}

.message-avatar {
  width: 32px;
  height: 32px;
  border-radius: var(--radius-full);
  background-color: var(--bg-tertiary);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.message-avatar-assistant {
  background: linear-gradient(135deg, var(--brand-primary), var(--brand-dark));
  color: white;
  font-weight: var(--font-medium);
  font-size: var(--text-sm);
}

.message-avatar-user {
  background: linear-gradient(135deg, var(--brand-accent), var(--brand-accent-hover));
  color: white;
  font-weight: var(--font-medium);
  font-size: var(--text-sm);
}

.message-content {
  flex: 1;
  background-color: var(--bg-secondary);
  padding: var(--space-4);
  border-radius: var(--radius-lg);
  font-size: var(--text-base);
  line-height: var(--leading-relaxed);
  transition: background-color var(--transition-fast);
  border-left: 3px solid var(--brand-primary);
}

.message-user .message-content {
  background-color: var(--brand-light);
  color: var(--text-primary);
  border-left: 3px solid var(--brand-accent);
  border-right: none;
}

[data-theme="dark"] .message-user .message-content {
  background-color: rgba(218, 41, 28, 0.1);
  color: var(--text-primary);
}

.message-content:hover {
  background-color: var(--bg-tertiary);
}

/* Message Streaming Indicator */
.message-streaming {
  display: inline-flex;
  gap: var(--space-1);
  align-items: center;
}

.message-streaming-dot {
  width: 6px;
  height: 6px;
  border-radius: var(--radius-full);
  background-color: var(--brand-primary);
  animation: streaming-pulse 1.4s ease-in-out infinite;
}

.message-streaming-dot:nth-child(2) {
  animation-delay: 0.2s;
}

.message-streaming-dot:nth-child(3) {
  animation-delay: 0.4s;
}

@keyframes streaming-pulse {
  0%, 80%, 100% {
    opacity: 0.3;
    transform: scale(0.8);
  }
  40% {
    opacity: 1;
    transform: scale(1);
  }
}

/* Code Blocks in Messages */
.message-content pre {
  background-color: var(--bg-tertiary);
  padding: var(--space-4);
  border-radius: var(--radius-md);
  overflow-x: auto;
  margin: var(--space-3) 0;
  font-family: var(--font-mono);
  font-size: var(--text-sm);
  line-height: var(--leading-normal);
  border-left: 3px solid var(--brand-primary);
}

.message-content code {
  font-family: var(--font-mono);
  font-size: 0.9em;
  background-color: var(--bg-tertiary);
  padding: 0.1em 0.4em;
  border-radius: var(--radius-sm);
  color: var(--brand-primary);
  font-weight: var(--font-medium);
}

.message-content pre code {
  background-color: transparent;
  padding: 0;
  color: var(--text-primary);
}

/* ----------------------------------------------------------------------------
   INPUT COMPONENTS
   ---------------------------------------------------------------------------- */

/* Chat Input */
.chat-input {
  position: relative;
  background-color: var(--bg-secondary);
  border: 1px solid var(--border-default);
  border-radius: var(--radius-xl);
  padding: var(--space-3) var(--space-4);
  min-height: 52px;
  max-height: 200px;
  overflow-y: auto;
  transition: all var(--transition-fast);
}

.chat-input:focus-within {
  border-color: var(--brand-primary);
  box-shadow: 0 0 0 3px var(--brand-light);
}

[data-theme="dark"] .chat-input:focus-within {
  box-shadow: 0 0 0 3px rgba(0, 70, 190, 0.2);
}

.chat-input-textarea {
  width: 100%;
  border: none;
  outline: none;
  background: transparent;
  resize: none;
  font-family: var(--font-sans);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  color: var(--text-primary);
}

.chat-input-textarea::placeholder {
  color: var(--text-placeholder);
}

.chat-input-actions {
  display: flex;
  gap: var(--space-2);
  align-items: center;
  justify-content: space-between;
  margin-top: var(--space-2);
  padding-top: var(--space-2);
  border-top: 1px solid var(--border-subtle);
}

/* ----------------------------------------------------------------------------
   BUTTON COMPONENTS - FERRELLGAS STYLED
   ---------------------------------------------------------------------------- */

/* Base Button */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  padding: var(--space-2) var(--space-4);
  font-family: var(--font-sans);
  font-size: var(--text-sm);
  font-weight: var(--font-medium);
  line-height: 1;
  border: none;
  border-radius: var(--radius-lg);
  cursor: pointer;
  transition: all var(--transition-fast);
  white-space: nowrap;
  user-select: none;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Primary Button - Ferrellgas Blue */
.btn-primary {
  background-color: var(--brand-primary);
  color: white;
  box-shadow: var(--shadow-sm);
}

.btn-primary:hover:not(:disabled) {
  background-color: var(--brand-primary-hover);
  box-shadow: var(--shadow-md);
  transform: translateY(-1px);
}

.btn-primary:active:not(:disabled) {
  background-color: var(--brand-primary-active);
  transform: translateY(0);
}

/* Accent Button - Ferrellgas Red */
.btn-accent {
  background-color: var(--brand-accent);
  color: white;
  box-shadow: var(--shadow-sm);
}

.btn-accent:hover:not(:disabled) {
  background-color: var(--brand-accent-hover);
  box-shadow: var(--shadow-md);
  transform: translateY(-1px);
}

/* Secondary Button */
.btn-secondary {
  background-color: var(--bg-secondary);
  color: var(--text-primary);
  border: 1px solid var(--border-default);
}

.btn-secondary:hover:not(:disabled) {
  background-color: var(--bg-tertiary);
  border-color: var(--brand-primary);
  color: var(--brand-primary);
}

/* Ghost Button */
.btn-ghost {
  background-color: transparent;
  color: var(--text-secondary);
}

.btn-ghost:hover:not(:disabled) {
  background-color: var(--bg-secondary);
  color: var(--brand-primary);
}

/* Icon Button */
.btn-icon {
  padding: var(--space-2);
  width: 36px;
  height: 36px;
}

/* Button Sizes */
.btn-sm {
  padding: var(--space-1) var(--space-3);
  font-size: var(--text-xs);
}

.btn-lg {
  padding: var(--space-3) var(--space-6);
  font-size: var(--text-base);
}

/* ----------------------------------------------------------------------------
   CONVERSATION LIST
   ---------------------------------------------------------------------------- */

.conversation-list {
  display: flex;
  flex-direction: column;
  gap: var(--space-1);
}

.conversation-item {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-3);
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: all var(--transition-fast);
  color: var(--text-secondary);
  border-left: 3px solid transparent;
}

.conversation-item:hover {
  background-color: var(--bg-tertiary);
  color: var(--text-primary);
  border-left-color: var(--brand-primary);
}

.conversation-item.active {
  background-color: var(--brand-light);
  color: var(--brand-primary);
  font-weight: var(--font-medium);
  border-left-color: var(--brand-primary);
}

.conversation-title {
  flex: 1;
  font-size: var(--text-sm);
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.conversation-time {
  font-size: var(--text-xs);
  color: var(--text-tertiary);
}

/* New Conversation Button */
.new-conversation-btn {
  background: linear-gradient(135deg, var(--brand-primary), var(--brand-dark));
  color: white;
  border: none;
  padding: var(--space-3);
  border-radius: var(--radius-md);
  font-weight: var(--font-medium);
  cursor: pointer;
  transition: all var(--transition-fast);
  margin-bottom: var(--space-3);
}

.new-conversation-btn:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

/* ----------------------------------------------------------------------------
   UTILITY CLASSES
   ---------------------------------------------------------------------------- */

/* Scrollbar Styling */
.custom-scrollbar::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

.custom-scrollbar::-webkit-scrollbar-track {
  background: transparent;
}

.custom-scrollbar::-webkit-scrollbar-thumb {
  background-color: var(--gray-400);
  border-radius: var(--radius-full);
}

[data-theme="dark"] .custom-scrollbar::-webkit-scrollbar-thumb {
  background-color: var(--gray-600);
}

.custom-scrollbar::-webkit-scrollbar-thumb:hover {
  background-color: var(--brand-primary);
}

/* Focus Visible */
.focus-visible {
  outline: 2px solid var(--brand-primary);
  outline-offset: 2px;
}

/* Truncate Text */
.truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* Brand Text */
.text-brand {
  color: var(--brand-primary);
}

.text-accent {
  color: var(--brand-accent);
}

/* ----------------------------------------------------------------------------
   ANIMATIONS
   ---------------------------------------------------------------------------- */

@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slide-up {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fade-in var(--transition-base);
}

.animate-slide-up {
  animation: slide-up var(--transition-base);
}

/* ----------------------------------------------------------------------------
   RESPONSIVE BREAKPOINTS
   ---------------------------------------------------------------------------- */

@media (max-width: 768px) {
  .sidebar {
    position: absolute;
    left: -260px;
    height: 100%;
    z-index: var(--z-fixed);
  }
  
  .sidebar.open {
    left: 0;
  }
  
  .chat-messages {
    padding: var(--space-4);
  }
  
  .chat-input-container {
    padding: var(--space-4);
  }
  
  .chat-header {
    padding: var(--space-3) var(--space-4);
  }
}

/* ----------------------------------------------------------------------------
   ACCESSIBILITY
   ---------------------------------------------------------------------------- */

/* High Contrast Mode Support */
@media (prefers-contrast: high) {
  :root {
    --border-default: #000000;
    --text-secondary: #000000;
  }
  
  [data-theme="dark"] {
    --border-default: #FFFFFF;
    --text-secondary: #FFFFFF;
  }
}

/* Reduced Motion */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}

/* Focus-Visible Polyfill */
.js-focus-visible :focus:not(.focus-visible) {
  outline: none;
}

/* Screen Reader Only */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}

/* ----------------------------------------------------------------------------
   FERRELLGAS SPECIFIC ENHANCEMENTS
   ---------------------------------------------------------------------------- */

/* Brand Header */
.brand-header {
  background: linear-gradient(135deg, var(--brand-primary) 0%, var(--brand-dark) 100%);
  color: white;
  padding: var(--space-4);
  text-align: center;
}

.brand-logo {
  font-weight: var(--font-bold);
  font-size: var(--text-xl);
  margin-bottom: var(--space-2);
}

.brand-tagline {
  font-size: var(--text-sm);
  opacity: 0.9;
}

/* Status Indicators */
.status-indicator {
  display: inline-flex;
  align-items: center;
  gap: var(--space-2);
  padding: var(--space-1) var(--space-3);
  border-radius: var(--radius-full);
  font-size: var(--text-xs);
  font-weight: var(--font-medium);
}

.status-online {
  background-color: var(--success-light);
  color: var(--success-dark);
}

.status-offline {
  background-color: var(--error-light);
  color: var(--error-dark);
}

/* Theme Toggle */
.theme-toggle {
  background: var(--bg-secondary);
  border: 1px solid var(--border-default);
  border-radius: var(--radius-full);
  padding: var(--space-1);
  display: flex;
  gap: var(--space-1);
}

.theme-toggle-btn {
  padding: var(--space-1) var(--space-3);
  border-radius: var(--radius-full);
  border: none;
  background: transparent;
  color: var(--text-secondary);
  cursor: pointer;
  transition: all var(--transition-fast);
  font-size: var(--text-xs);
}

.theme-toggle-btn.active {
  background: var(--brand-primary);
  color: white;
}
```

## Key Changes Made:

1. **Color Scheme Replacement**:
   - Primary brand color: `#0046be` (Ferrellgas Blue)
   - Secondary brand color: `#002d72` (Ferrellgas Dark Blue)
   - Accent color: `#da291c` (Ferrellgas Red)
   - Updated all semantic colors to use Ferrellgas palette

2. **Component Styling**:
   - Message bubbles with Ferrellgas blue borders
   - User messages with Ferrellgas red accent
   - Buttons styled with Ferrellgas colors
   - Sidebar header with Ferrellgas gradient

3. **Enhanced Features**:
   - Brand header component
   - Status indicators
   - Theme toggle
   - Improved conversation list styling

4. **Dark Mode**:
   - Adjusted Ferrellgas colors for dark theme
   - Maintained brand identity in dark mode
