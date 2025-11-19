Here's a comprehensive prompt for Google Studio to help you create a dynamic, extensible toolbar system:

🎯 Complete Prompt for Google Studio

```
I need to create a dynamic, extensible toolbar system for my work dashboard with 10+ shortcuts that can connect to various backend services. Please help me design and implement this system with the following requirements:

## CORE REQUIREMENTS:
1. **Dynamic Configuration** - Shortcuts should be configurable without code changes
2. **Multiple Connection Types** - Support various backend protocols
3. **Extensible Architecture** - Easy to add new shortcut types
4. **Type Safety** - Full TypeScript coverage
5. **UI Flexibility** - Responsive, draggable, customizable layout

## DESIRED SHORTCUT EXAMPLES:
- Check JIRA ticket queue
- Search CVE databases  
- Unlock user accounts (AD/LDAP)
- View application logs
- Monitor system metrics
- Check service health
- Run database queries
- Trigger deployments
- View monitoring dashboards
- Handle support tickets

## CONNECTION TYPES TO SUPPORT:
Please identify ALL potential connection protocols I might need and implement a pluggable architecture for:

1. **REST APIs** (JSON/XML)
2. **GraphQL APIs**
3. **WebSocket connections**
4. **Webhook endpoints** 
5. **MQTT brokers**
6. **SSH connections**
7. **Database direct connections**
8. **LDAP/Active Directory**
9. **OAuth2 authenticated services**
10. **gRPC services**
11. **SOAP web services**
12. **CLI command execution**
13. **File system operations**
14. **Email protocols (SMTP/IMAP)**
15. **Message queues (RabbitMQ, Kafka)**

## TECHNICAL SPECIFICS:
- **Frontend**: TypeScript/React with modern hooks
- **Styling**: Tailwind CSS with responsive design
- **State Management**: Zustand or Context API
- **Backend**: Rust API for proxy/connection handling
- **Configuration**: JSON-based dynamic config

## IMPLEMENTATION REQUEST:
Please provide:

1. **TypeScript interfaces** for extensible shortcut definitions
2. **Connection adapter pattern** for multiple protocols
3. **Dynamic toolbar component** with drag-and-drop
4. **Configuration management system**
5. **Error handling** for various connection types
6. **Authentication strategies** for different services
7. **Real-time updates** where applicable
8. **Caching layer** for performance
9. **Plugin architecture** for easy extensions

Focus on creating a foundation where I can easily add new shortcut types and connection protocols without refactoring the core system.
```

🔌 Expected Connection Types Breakdown

Based on your use cases, here are the connection types you'll likely need:

1. REST API Connections

```typescript
interface RESTConnection {
  baseURL: string;
  endpoints: Record<string, string>;
  authentication: 'none' | 'apiKey' | 'bearer' | 'basic';
  headers: Record<string, string>;
}
```

Use Cases: JIRA, CVE databases, internal tools

2. GraphQL Connections

```typescript
interface GraphQLConnection {
  endpoint: string;
  queries: Record<string, string>;
  headers: Record<string, string>;
}
```

Use Cases: Modern APIs, GitHub, internal graph APIs

3. WebSocket Connections

```typescript
interface WebSocketConnection {
  url: string;
  protocols?: string[];
  reconnect: boolean;
  messageHandlers: Record<string, (data: any) => void>;
}
```

Use Cases: Real-time monitoring, live logs, notifications

4. Webhook Configurations

```typescript
interface WebhookConfig {
  targetUrl: string;
  secret?: string;
  events: string[];
  payloadTemplate?: Record<string, any>;
}
```

Use Cases: Trigger actions, receive notifications

5. MQTT Connections

```typescript
interface MQTTConnection {
  broker: string;
  topics: string[];
  qos: 0 | 1 | 2;
  clientId: string;
}
```

Use Cases: IoT monitoring, real-time metrics

6. SSH Connections

```typescript
interface SSHConnection {
  host: string;
  port: number;
  username: string;
  privateKey?: string;
  commands: Record<string, string>;
}
```

Use Cases: Server management, log checking, deployments

7. Database Connections

```typescript
interface DBConnection {
  type: 'postgres' | 'mysql' | 'mongodb' | 'redis';
  connectionString: string;
  queries: Record<string, string>;
}
```

Use Cases: Direct data queries, user management

8. LDAP/Active Directory

```typescript
interface LDAPConnection {
  server: string;
  bindDN: string;
  bindCredentials: string;
  searchBase: string;
}
```

Use Cases: User account management, unlocking accounts

🛠 Sample Implementation Structure

Here's what Google Studio should generate:

Core Types

```typescript
// The main shortcut definition
interface ToolbarShortcut {
  id: string;
  name: string;
  description: string;
  icon: string;
  connectionType: ConnectionType;
  config: ConnectionConfig;
  action: (config: ConnectionConfig) => Promise<ActionResult>;
  refreshInterval?: number; // For auto-updating shortcuts
}

// Union type of all possible connections
type ConnectionType = 
  | 'rest' 
  | 'graphql' 
  | 'websocket' 
  | 'webhook' 
  | 'mqtt'
  | 'ssh'
  | 'database'
  | 'ldap'
  | 'oauth'
  | 'grpc';

// Generic connection configuration
type ConnectionConfig = RESTConfig | GraphQLConfig | SSHConfig | // ... etc
```

Configuration Example

```typescript
// Your dynamic config file
const shortcutConfig: ToolbarShortcut[] = [
  {
    id: 'jira-queue',
    name: 'JIRA Queue',
    description: 'Check assigned tickets',
    icon: '🎫',
    connectionType: 'rest',
    config: {
      type: 'rest',
      baseURL: 'https://your-company.atlassian.net',
      endpoint: '/rest/api/2/search',
      authentication: 'bearer',
      // ... rest config
    },
    refreshInterval: 300000 // 5 minutes
  },
  {
    id: 'user-unlock',
    name: 'Unlock User',
    description: 'Unlock AD accounts',
    icon: '🔓',
    connectionType: 'ldap',
    config: {
      type: 'ldap',
      server: 'ldap://company.com',
      // ... ldap config
    }
  }
  // ... more shortcuts
];
```

🚀 Enhanced Prompt for Specific Needs

If you want even more specificity, use this enhanced version:

```
I'm building an enterprise toolbar dashboard for DevOps/SRE tasks. Create a plugin-based architecture that supports:

## PRIORITY CONNECTIONS:
1. **JIRA REST API** - Ticket queries, assignment checks
2. **CVE Databases** (NVD, vendor APIs) - Security vulnerability checks
3. **Active Directory/LDAP** - User management, unlocks
4. **Log Aggregators** (ELK, Splunk, Loki) - Log searching
5. **Monitoring** (Prometheus, Grafana) - System metrics
6. **SSH Gateways** - Server access, command execution
7. **Database Proxies** - Query execution, user lookups
8. **Internal REST APIs** - Various microservices
9. **Webhook Receivers** - Alert management
10. **Message Queues** - Status checking

## ENTERPRISE REQUIREMENTS:
- **Authentication**: Support for OAuth2, API keys, JWT, LDAP bind
- **Security**: Credential encryption, secure connection handling
- **Caching**: Smart caching with invalidation strategies
- **Error Handling**: Graceful degradation when services are unavailable
- **Audit Logging**: Track all actions performed
- **Permission System**: Role-based access to different shortcuts

## IMPLEMENTATION APPROACH:
Please provide a complete TypeScript/React solution with:
1. Connection factory pattern for different protocols
2. Configuration schema validation
3. Real-time status indicators for each shortcut
4. Drag-and-drop toolbar customization
5. Settings panel for configuring connections
6. Health checking for all connected services
7. Retry mechanisms with exponential backoff
8. Offline capability where possible

Focus on creating a robust foundation that my team can extend with new plugins as our tooling evolves.
```

This prompt should give you a comprehensive, production-ready toolbar system that can grow with your needs!