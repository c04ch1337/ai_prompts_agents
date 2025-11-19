Here are 25 TypeScript/React UI tips and tricks to create amazing user experiences:

1. Framer Motion for Advanced Animations

```typescript
import { motion, AnimatePresence } from 'framer-motion';

const FadeInCard: React.FC = () => (
  <motion.div
    initial={{ opacity: 0, y: 20 }}
    animate={{ opacity: 1, y: 0 }}
    transition={{ duration: 0.5 }}
    whileHover={{ scale: 1.05 }}
    className="card"
  >
    Content
  </motion.div>
);
```

Use Case: Page transitions, hover effects, loading states

2. Tailwind CSS with Custom Animations

```typescript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in-out',
        'slide-in': 'slideIn 0.3s ease-out',
      }
    }
  }
}

// Component
<div className="animate-fade-in hover:animate-pulse">
  Animated Content
</div>
```

3. Glass Morphism Effect

```typescript
const GlassCard: React.FC = () => (
  <div className="backdrop-blur-lg bg-white/10 border border-white/20 rounded-xl shadow-2xl">
    Glass Content
  </div>
);
```

Use Case: Modern dashboards, premium UIs

4. Custom Hook for Drag & Drop

```typescript
import { useState, useRef } from 'react';

interface UseDragResult {
  isDragging: boolean;
  position: { x: number; y: number };
  dragProps: {
    onMouseDown: (e: React.MouseEvent) => void;
  };
}

export const useDrag = (): UseDragResult => {
  const [isDragging, setIsDragging] = useState(false);
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const ref = useRef<{ offsetX: number; offsetY: number }>();

  const onMouseDown = (e: React.MouseEvent) => {
    setIsDragging(true);
    ref.current = {
      offsetX: e.clientX - position.x,
      offsetY: e.clientY - position.y,
    };

    const onMouseMove = (e: MouseEvent) => {
      if (!ref.current) return;
      setPosition({
        x: e.clientX - ref.current.offsetX,
        y: e.clientY - ref.current.offsetY,
      });
    };

    const onMouseUp = () => {
      setIsDragging(false);
      document.removeEventListener('mousemove', onMouseMove);
    };

    document.addEventListener('mousemove', onMouseMove);
    document.addEventListener('mouseup', onMouseUp, { once: true });
  };

  return { isDragging, position, dragProps: { onMouseDown } };
};
```

5. Smooth Scroll with TypeScript

```typescript
const useSmoothScroll = () => {
  const scrollTo = (elementId: string, behavior: ScrollBehavior = 'smooth') => {
    const element = document.getElementById(elementId);
    element?.scrollIntoView({ behavior });
  };

  const scrollToTop = () => {
    window.scrollTo({ top: 0, behavior: 'smooth' });
  };

  return { scrollTo, scrollToTop };
};
```

6. Advanced Loading Skeletons

```typescript
const ShimmerLoader: React.FC = () => (
  <div className="animate-pulse">
    <div className="h-4 bg-gray-200 rounded mb-2"></div>
    <div className="h-4 bg-gray-200 rounded w-3/4"></div>
    <div className="h-20 bg-gray-200 rounded mt-4"></div>
  </div>
);
```

7. Type-Safe Notification System

```typescript
interface Notification {
  id: string;
  type: 'success' | 'error' | 'warning' | 'info';
  title: string;
  message: string;
  duration?: number;
}

const useNotifications = () => {
  const [notifications, setNotifications] = useState<Notification[]>([]);

  const addNotification = (notification: Omit<Notification, 'id'>) => {
    const id = Math.random().toString(36);
    setNotifications(prev => [...prev, { ...notification, id }]);
    
    if (notification.duration !== 0) {
      setTimeout(() => removeNotification(id), notification.duration || 5000);
    }
  };

  const removeNotification = (id: string) => {
    setNotifications(prev => prev.filter(n => n.id !== id));
  };

  return { notifications, addNotification, removeNotification };
};
```

8. Custom Cursor with Hover Effects

```typescript
const CustomCursor: React.FC = () => {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [isPointer, setIsPointer] = useState(false);

  useEffect(() => {
    const mouseMove = (e: MouseEvent) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    const mouseOver = (e: MouseEvent) => {
      const target = e.target as HTMLElement;
      setIsPointer(window.getComputedStyle(target).cursor === 'pointer');
    };

    document.addEventListener('mousemove', mouseMove);
    document.addEventListener('mouseover', mouseOver);
    
    return () => {
      document.removeEventListener('mousemove', mouseMove);
      document.removeEventListener('mouseover', mouseOver);
    };
  }, []);

  return (
    <div className={`fixed top-0 left-0 w-6 h-6 rounded-full mix-blend-difference 
      pointer-events-none z-50 transition-transform duration-150
      ${isPointer ? 'bg-white scale-150' : 'bg-blue-500'}`}
      style={{ transform: `translate(${position.x - 12}px, ${position.y - 12}px)` }}
    />
  );
};
```

9. Particle Background Effect

```typescript
const ParticleBackground: React.FC = () => {
  const canvasRef = useRef<HTMLCanvasElement>(null);

  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;

    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    const particles: Array<{ x: number; y: number; size: number; speedX: number; speedY: number }> = [];

    for (let i = 0; i < 100; i++) {
      particles.push({
        x: Math.random() * canvas.width,
        y: Math.random() * canvas.height,
        size: Math.random() * 2 + 1,
        speedX: Math.random() * 1 - 0.5,
        speedY: Math.random() * 1 - 0.5,
      });
    }

    const animate = () => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      
      particles.forEach(particle => {
        particle.x += particle.speedX;
        particle.y += particle.speedY;

        if (particle.x > canvas.width) particle.x = 0;
        if (particle.x < 0) particle.x = canvas.width;
        if (particle.y > canvas.height) particle.y = 0;
        if (particle.y < 0) particle.y = canvas.height;

        ctx.beginPath();
        ctx.arc(particle.x, particle.y, particle.size, 0, Math.PI * 2);
        ctx.fillStyle = 'rgba(255, 255, 255, 0.5)';
        ctx.fill();
      });

      requestAnimationFrame(animate);
    };

    animate();
  }, []);

  return <canvas ref={canvasRef} className="fixed inset-0 -z-10" />;
};
```

10. Type-Safe Theme System

```typescript
type Theme = 'light' | 'dark' | 'system';

interface ThemeContextType {
  theme: Theme;
  setTheme: (theme: Theme) => void;
  resolvedTheme: 'light' | 'dark';
}

export const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used within ThemeProvider');
  return context;
};
```

11. Advanced Form with Real-time Validation

```typescript
const useForm = <T extends Record<string, any>>(initialValues: T) => {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
  const [touched, setTouched] = useState<Partial<Record<keyof T, boolean>>>({});

  const setValue = <K extends keyof T>(key: K, value: T[K]) => {
    setValues(prev => ({ ...prev, [key]: value }));
  };

  const setError = <K extends keyof T>(key: K, error: string) => {
    setErrors(prev => ({ ...prev, [key]: error }));
  };

  const setFieldTouched = <K extends keyof T>(key: K) => {
    setTouched(prev => ({ ...prev, [key]: true }));
  };

  return {
    values,
    errors,
    touched,
    setValue,
    setError,
    setFieldTouched,
    isValid: Object.keys(errors).length === 0,
  };
};
```

12. Virtual Scrolling for Large Lists

```typescript
interface VirtualScrollProps<T> {
  items: T[];
  height: number;
  itemHeight: number;
  renderItem: (item: T, index: number) => React.ReactNode;
}

const VirtualScroll = <T,>({ items, height, itemHeight, renderItem }: VirtualScrollProps<T>) => {
  const [scrollTop, setScrollTop] = useState(0);
  const visibleStart = Math.floor(scrollTop / itemHeight);
  const visibleEnd = Math.min(visibleStart + Math.ceil(height / itemHeight) + 1, items.length);

  const visibleItems = items.slice(visibleStart, visibleEnd);

  return (
    <div
      style={{ height, overflow: 'auto' }}
      onScroll={(e) => setScrollTop(e.currentTarget.scrollTop)}
    >
      <div style={{ height: items.length * itemHeight, position: 'relative' }}>
        {visibleItems.map((item, index) => (
          <div
            key={visibleStart + index}
            style={{
              position: 'absolute',
              top: (visibleStart + index) * itemHeight,
              width: '100%',
            }}
          >
            {renderItem(item, visibleStart + index)}
          </div>
        ))}
      </div>
    </div>
  );
};
```

13. Type-Safe Keyboard Shortcuts

```typescript
type ShortcutHandler = (e: KeyboardEvent) => void;

interface ShortcutConfig {
  key: string;
  ctrl?: boolean;
  shift?: boolean;
  alt?: boolean;
  handler: ShortcutHandler;
}

export const useKeyboardShortcut = (shortcuts: ShortcutConfig[]) => {
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      shortcuts.forEach(({ key, ctrl, shift, alt, handler }) => {
        if (
          e.key === key &&
          !!e.ctrlKey === !!ctrl &&
          !!e.shiftKey === !!shift &&
          !!e.altKey === !!alt
        ) {
          e.preventDefault();
          handler(e);
        }
      });
    };

    document.addEventListener('keydown', handleKeyDown);
    return () => document.removeEventListener('keydown', handleKeyDown);
  }, [shortcuts]);
};
```

14. Advanced Tooltip System

```typescript
interface TooltipState {
  visible: boolean;
  content: string;
  position: { x: number; y: number };
}

export const useTooltip = () => {
  const [tooltip, setTooltip] = useState<TooltipState>({
    visible: false,
    content: '',
    position: { x: 0, y: 0 },
  });

  const showTooltip = (content: string, x: number, y: number) => {
    setTooltip({ visible: true, content, position: { x, y } });
  };

  const hideTooltip = () => {
    setTooltip(prev => ({ ...prev, visible: false }));
  };

  const TooltipComponent: React.FC = () => (
    <AnimatePresence>
      {tooltip.visible && (
        <motion.div
          initial={{ opacity: 0, scale: 0.8 }}
          animate={{ opacity: 1, scale: 1 }}
          exit={{ opacity: 0, scale: 0.8 }}
          className="absolute z-50 px-2 py-1 text-sm bg-gray-900 text-white rounded shadow-lg"
          style={{
            left: tooltip.position.x,
            top: tooltip.position.y - 10,
          }}
        >
          {tooltip.content}
        </motion.div>
      )}
    </AnimatePresence>
  );

  return { showTooltip, hideTooltip, TooltipComponent };
};
```

15. Smooth Gradient Transitions

```typescript
const AnimatedGradient: React.FC = () => {
  const [gradient, setGradient] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setGradient(prev => (prev + 1) % 360);
    }, 50);

    return () => clearInterval(interval);
  }, []);

  return (
    <div
      className="w-full h-screen transition-all duration-1000"
      style={{
        background: `linear-gradient(${gradient}deg, #667eea 0%, #764ba2 100%)`,
      }}
    />
  );
};
```

16. Type-Safe Local Storage with Expiry

```typescript
interface StorageItem<T> {
  value: T;
  expiry: number;
}

export const useLocalStorage = <T>(key: string, initialValue: T) => {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = localStorage.getItem(key);
      if (!item) return initialValue;

      const parsed: StorageItem<T> = JSON.parse(item);
      if (parsed.expiry && Date.now() > parsed.expiry) {
        localStorage.removeItem(key);
        return initialValue;
      }

      return parsed.value;
    } catch {
      return initialValue;
    }
  });

  const setValue = (value: T, ttl?: number) => {
    const item: StorageItem<T> = {
      value,
      expiry: ttl ? Date.now() + ttl : 0,
    };
    localStorage.setItem(key, JSON.stringify(item));
    setStoredValue(value);
  };

  return [storedValue, setValue] as const;
};
```

17. Advanced File Drop Zone

```typescript
interface UseFileDropResult {
  isOver: boolean;
  dropProps: {
    onDragOver: (e: React.DragEvent) => void;
    onDragLeave: (e: React.DragEvent) => void;
    onDrop: (e: React.DragEvent) => void;
  };
}

export const useFileDrop = (onFiles: (files: File[]) => void): UseFileDropResult => {
  const [isOver, setIsOver] = useState(false);

  const handleDragOver = (e: React.DragEvent) => {
    e.preventDefault();
    setIsOver(true);
  };

  const handleDragLeave = (e: React.DragEvent) => {
    e.preventDefault();
    setIsOver(false);
  };

  const handleDrop = (e: React.DragEvent) => {
    e.preventDefault();
    setIsOver(false);
    const files = Array.from(e.dataTransfer.files);
    onFiles(files);
  };

  return {
    isOver,
    dropProps: {
      onDragOver: handleDragOver,
      onDragLeave: handleDragLeave,
      onDrop: handleDrop,
    },
  };
};
```

18. Real-time Data Streaming Hook

```typescript
export const useWebSocket = <T>(url: string) => {
  const [data, setData] = useState<T | null>(null);
  const [isConnected, setIsConnected] = useState(false);

  useEffect(() => {
    const ws = new WebSocket(url);

    ws.onopen = () => setIsConnected(true);
    ws.onclose = () => setIsConnected(false);
    ws.onmessage = (event) => {
      try {
        const parsedData: T = JSON.parse(event.data);
        setData(parsedData);
      } catch (error) {
        console.error('WebSocket parse error:', error);
      }
    };

    return () => ws.close();
  }, [url]);

  return { data, isConnected };
};
```

19. Advanced Search with Debounce

```typescript
export const useDebouncedSearch = (delay: number = 300) => {
  const [searchTerm, setSearchTerm] = useState('');
  const [debouncedTerm, setDebouncedTerm] = useState('');

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedTerm(searchTerm);
    }, delay);

    return () => clearTimeout(timer);
  }, [searchTerm, delay]);

  return { searchTerm, debouncedTerm, setSearchTerm };
};
```

20. Type-Safe Internationalization (i18n)

```typescript
type Locale = 'en' | 'es' | 'fr';
type Translations = Record<string, Record<Locale, string>>;

const translations = {
  welcome: {
    en: 'Welcome',
    es: 'Bienvenido',
    fr: 'Bienvenue',
  },
} as const;

export const useTranslation = () => {
  const [locale, setLocale] = useState<Locale>('en');

  const t = (key: keyof typeof translations) => {
    return translations[key][locale];
  };

  return { t, locale, setLocale };
};
```

21. Advanced Chart Animations

```typescript
const AnimatedBarChart: React.FC<{ data: number[] }> = ({ data }) => {
  return (
    <div className="flex items-end gap-2 h-40">
      {data.map((value, index) => (
        <motion.div
          key={index}
          initial={{ height: 0 }}
          animate={{ height: `${value}%` }}
          transition={{ duration: 0.5, delay: index * 0.1 }}
          className="bg-blue-500 rounded-t w-8"
        />
      ))}
    </div>
  );
};
```

22. Type-Safe Route System

```typescript
type Route = '/' | '/about' | '/contact' | '/users/:id';

interface RouterContext {
  currentRoute: Route;
  navigate: (route: Route) => void;
  params: Record<string, string>;
}

export const useRouter = (): RouterContext => {
  // Implementation with type-safe routes
};
```

23. Advanced Image Lazy Loading

```typescript
const LazyImage: React.FC<{ src: string; alt: string }> = ({ src, alt }) => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [inView, setInView] = useState(false);

  const imgRef = useRef<HTMLImageElement>(null);

  useEffect(() => {
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) {
        setInView(true);
        observer.unobserve(entry.target);
      }
    });

    if (imgRef.current) observer.observe(imgRef.current);

    return () => observer.disconnect();
  }, []);

  return (
    <div ref={imgRef} className="relative">
      {!isLoaded && <div className="absolute inset-0 bg-gray-200 animate-pulse" />}
      {inView && (
        <img
          src={src}
          alt={alt}
          onLoad={() => setIsLoaded(true)}
          className={`transition-opacity duration-500 ${
            isLoaded ? 'opacity-100' : 'opacity-0'
          }`}
        />
      )}
    </div>
  );
};
```

24. Type-Safe Audio/Video Player

```typescript
interface MediaPlayerState {
  isPlaying: boolean;
  currentTime: number;
  duration: number;
  volume: number;
}

export const useMediaPlayer = (src: string) => {
  const [state, setState] = useState<MediaPlayerState>({
    isPlaying: false,
    currentTime: 0,
    duration: 0,
    volume: 1,
  });

  const audioRef = useRef<HTMLAudioElement>(new Audio(src));

  // Media control methods with type safety
  const play = () => audioRef.current.play();
  const pause = () => audioRef.current.pause();
  const seek = (time: number) => { audioRef.current.currentTime = time; };

  return { state, play, pause, seek };
};
```

25. Advanced Context Menu

```typescript
interface ContextMenuItem {
  label: string;
  action: () => void;
  disabled?: boolean;
}

export const useContextMenu = (items: ContextMenuItem[]) => {
  const [position, setPosition] = useState<{ x: number; y: number } | null>(null);

  const handleContextMenu = (e: React.MouseEvent) => {
    e.preventDefault();
    setPosition({ x: e.clientX, y: e.clientY });
  };

  const ContextMenuComponent: React.FC = () => (
    <AnimatePresence>
      {position && (
        <>
        <motion.div
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
          className="fixed inset-0 z-40"
          onClick={() => setPosition(null)}
        />
        <motion.div
          initial={{ opacity: 0, scale: 0.8 }}
          animate={{ opacity: 1, scale: 1 }}
          exit={{ opacity: 0, scale: 0.8 }}
          className="fixed z-50 bg-white border rounded-lg shadow-lg py-1"
          style={{ left: position.x, top: position.y }}
        >
          {items.map((item, index) => (
            <button
              key={index}
              onClick={() => {
                item.action();
                setPosition(null);
              }}
              disabled={item.disabled}
              className="block w-full px-4 py-2 text-left hover:bg-gray-100 disabled:opacity-50"
            >
              {item.label}
            </button>
          ))}
        </motion.div>
        </>
      )}
    </AnimatePresence>
  );

  return { handleContextMenu, ContextMenuComponent };
};
```

These advanced TypeScript/React techniques will make your UI look professional and feel amazing to use! Each example includes proper typing and real-world use cases.