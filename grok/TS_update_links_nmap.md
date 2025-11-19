Here's the focused update prompt with just nmap and IoT capabilities:

🎯 Focused Prompt for Google Studio - nmap & IoT Only

```
I need to add network scanning (nmap-like) and IoT device management capabilities to my existing toolbar system. Please help me implement these two specific features:

## FOCUSED REQUIREMENTS:
1. **Network Scanning Integration** - nmap-like functionality that compiles and runs from the web app
2. **IoT Device Management** - Connect to and manage IoT devices
3. **Minimal Impact** - Integrate with existing toolbar architecture without breaking changes

## NEW SHORTCUTS TO ADD:
- **Network Port Scanner**
- **Host Discovery**
- **Service Detection**
- **IoT Device Monitor**
- **Sensor Data Dashboard**
- **Device Controller**
- **Firmware Manager**
- **IoT Alert Center**

## NETWORK SCANNING SPECIFICS:
Implement multiple scanning approaches:

1. **WebAssembly (WASM) Scanning** - Compile lightweight nmap tools to run in browser
2. **Backend-Powered Scanning** - Use Rust backend for comprehensive scans
3. **Real-time Results** - Live progress and results in the toolbar
4. **Security-First** - Ensure authorized scanning with proper audit trails

**Scan Types Needed:**
- TCP SYN scanning (backend)
- TCP connect scanning (WASM/browser)
- UDP scanning (backend)
- Host discovery (ICMP via backend)
- Service version detection
- Quick ping sweeps

## IoT MANAGEMENT SPECIFICS:
Provide comprehensive IoT device control:

1. **Device Discovery** - Find IoT devices on network
2. **Real-time Monitoring** - Live sensor data streams
3. **Device Control** - Send commands to devices
4. **Protocol Support**:
   - MQTT (primary for sensors/commands)
   - CoAP for constrained devices
   - HTTP/REST for device APIs
   - WebSocket for live data

**IoT Device Types:**
- Environmental sensors (temperature, humidity)
- Smart devices (lights, switches, cameras)
- Industrial IoT (PLCs, sensors)
- Gateways and hubs

## TECHNICAL INTEGRATION:
- **Frontend**: Add to existing TypeScript/React toolbar
- **Backend**: Extend Rust API with scanning and IoT endpoints
- **Real-time**: WebSocket connections for live data
- **Security**: Proper authentication for both features

## IMPLEMENTATION FOCUS:
Please provide:

1. **Network scanning components** that integrate with existing toolbar
2. **IoT device management interface**
3. **Real-time data visualization** for sensor data
4. **Security wrappers** for authorized scanning
5. **Protocol adapters** for different IoT devices
6. **Minimal configuration** updates to existing system

## SAMPLE SHORTCUT CONFIG:
```typescript
// Add these to existing shortcuts
const newShortcuts = [
  {
    id: 'quick-scan',
    name: 'Network Scan',
    icon: '🔍',
    type: 'network_scan',
    config: { scanType: 'quick', target: 'auto-detect' }
  },
  {
    id: 'iot-dashboard',
    name: 'IoT Devices', 
    icon: '🏠',
    type: 'iot_monitor',
    config: { protocol: 'mqtt', refresh: 5000 }
  }
];
```

Focus on clean integration with the existing toolbar system while adding these two powerful capabilities.

```

## 🔧 **Expected Minimal Integration**

### **Network Scanning Additions**
```typescript
// Add to existing connection types
type ConnectionType = 
  | /* existing types */
  | 'network_scan'
  | 'iot_device';

// New config interfaces
interface NetworkScanConfig {
  scanType: 'quick' | 'comprehensive' | 'service_detection';
  target: string; // IP, range, or hostname
  ports?: string; // e.g., "80,443,8000-9000"
  intensity: 'light' | 'normal' | 'intensive';
}

interface IoTDeviceConfig {
  protocol: 'mqtt' | 'http' | 'websocket';
  connection: string; // broker URL or endpoint
  deviceId?: string; // specific device or 'all'
  sensors?: string[]; // which sensors to monitor
}
```

Toolbar Integration Points

· Add 2-3 new network scanning shortcuts
· Add 2-3 new IoT management shortcuts
· Extend existing configuration system
· Reuse existing UI components and styling
· Leverage current authentication system

This focused approach gives you the nmap and IoT capabilities without requiring a complete system redesign!