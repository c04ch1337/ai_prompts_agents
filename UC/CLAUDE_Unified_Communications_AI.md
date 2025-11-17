import React, { useState } from 'react';
import { Network, Laptop, Cloud, Globe, Database, Zap, Shield, ArrowRight, Server, Bot, MessageSquare, Mail, Users, Settings, ChevronDown, ChevronUp } from 'lucide-react';

const UnifiedAIArchitecture = () => {
  const [expandedArch, setExpandedArch] = useState(null);
  const [selectedTab, setSelectedTab] = useState('local');

  const architectures = [
    {
      id: 1,
      name: "Hub-and-Spoke Architecture",
      subtitle: "Master Agent Orchestrator",
      icon: Network,
      description: "Your Digital Twin acts as the central orchestrator hub, with specialized AI agents as spokes",
      pros: [
        "Centralized control and monitoring",
        "Easy to manage and debug",
        "Clear request routing and load balancing",
        "Single point of authentication"
      ],
      cons: [
        "Single point of failure risk",
        "Hub can become bottleneck",
        "Limited agent autonomy"
      ],
      components: [
        { name: "Digital Twin Hub", role: "Master Orchestrator", tech: "LangGraph + FastAPI" },
        { name: "Agent Router", role: "Request Distribution", tech: "Redis Queue" },
        { name: "Spoke Agents", role: "Specialized Workers", tech: "AutoGen/CrewAI" },
        { name: "State Manager", role: "Context Preservation", tech: "PostgreSQL + Vector DB" }
      ]
    },
    {
      id: 2,
      name: "Federated Agent Network",
      subtitle: "Peer-to-Peer Collaboration",
      icon: Globe,
      description: "Independent agents communicate peer-to-peer via Agent2Agent protocol, coordinated by your Digital Twin",
      pros: [
        "High resilience and fault tolerance",
        "Agents can work autonomously",
        "Scales horizontally easily",
        "Flexible agent composition"
      ],
      cons: [
        "Complex coordination logic",
        "More difficult to debug",
        "Requires robust message protocol"
      ],
      components: [
        { name: "Digital Twin Coordinator", role: "Strategic Planner", tech: "Microsoft Agent Framework" },
        { name: "Agent Registry", role: "Discovery Service", tech: "etcd/Consul" },
        { name: "Message Bus", role: "A2A Communication", tech: "RabbitMQ/NATS" },
        { name: "Autonomous Agents", role: "Specialized Services", tech: "LangChain + Custom APIs" }
      ]
    },
    {
      id: 3,
      name: "Layered Middleware Architecture",
      subtitle: "Abstraction & Integration Hub",
      icon: Server,
      description: "Multi-layer middleware provides abstraction between your Digital Twin interface and diverse AI systems",
      pros: [
        "Clean separation of concerns",
        "Easy to add new AI systems",
        "Best for hybrid deployments",
        "Vendor-agnostic integration"
      ],
      cons: [
        "Additional latency from layers",
        "Middleware complexity",
        "Requires careful API design"
      ],
      components: [
        { name: "Presentation Layer", role: "Digital Twin UI", tech: "Electron + React" },
        { name: "API Gateway", role: "Request Routing", tech: "Kong/Nginx" },
        { name: "Integration Layer", role: "Protocol Translation", tech: "Apache Camel" },
        { name: "Service Mesh", role: "Agent Communication", tech: "Istio" }
      ]
    }
  ];

  const deploymentModes = {
    local: {
      title: "LOCAL (Unified Communications)",
      icon: Laptop,
      description: "All AI systems running on your Windows 11 machine with local orchestration",
      architecture: [
        {
          layer: "User Interface",
          items: ["Digital Twin Desktop (Electron App)", "Single Unified GUI Dashboard", "Chat Interface + Admin Panel"]
        },
        {
          layer: "Orchestration Engine",
          items: ["Local FastAPI Server (Port 8000)", "LangGraph Workflow Manager", "Agent Router & Load Balancer"]
        },
        {
          layer: "AI Frameworks",
          items: ["AutoGen (localhost:8001)", "CrewAI (localhost:8002)", "LangChain (localhost:8003)", "Custom Agents (localhost:8004+)"]
        },
        {
          layer: "Integration Services",
          items: ["Outlook 365 Connector (Graph API)", "MS Teams Bot Integration", "Local Vector DB (ChromaDB)", "SQLite for State Management"]
        },
        {
          layer: "Communication",
          items: ["Internal Message Queue (ZeroMQ)", "WebSocket Connections", "REST API Endpoints"]
        }
      ],
      techStack: [
        "Python 3.11+ (AI frameworks)",
        "Node.js/Electron (GUI)",
        "Redis (message queue)",
        "ChromaDB (vector storage)",
        "Docker Desktop (containerization)"
      ],
      benefits: [
        "No internet dependency for core operations",
        "Complete data privacy and control",
        "Low latency communication",
        "No cloud costs",
        "Full debugging capability"
      ]
    },
    hybrid: {
      title: "HYBRID (Best of Both Worlds)",
      icon: Cloud,
      description: "Local orchestration with cloud services for enhanced capabilities and M365 integration",
      architecture: [
        {
          layer: "Frontend",
          items: ["Digital Twin Desktop (Local)", "Web Dashboard (Azure Static Web Apps)", "Progressive Web App (PWA)"]
        },
        {
          layer: "Local Components",
          items: ["Core Orchestrator (Local)", "Lightweight Agents (Local)", "Sensitive Data Processing (Local)"]
        },
        {
          layer: "Cloud Services",
          items: ["Heavy Compute Agents (Azure Container Apps)", "Microsoft 365 Copilot Integration", "Azure OpenAI Service", "Cloud Vector DB (Pinecone)"]
        },
        {
          layer: "Integration Hub",
          items: ["Azure Service Bus (message broker)", "API Management Gateway", "Microsoft Graph API (M365)", "Azure Functions (serverless tasks)"]
        },
        {
          layer: "Data Layer",
          items: ["Local: SQLite + ChromaDB", "Cloud: Azure Cosmos DB", "Synchronized State Management"]
        }
      ],
      techStack: [
        "Azure Container Apps (agent hosting)",
        "Azure Service Bus (messaging)",
        "Microsoft Graph API (M365)",
        "Hybrid authentication (Azure AD)",
        "Docker + Kubernetes"
      ],
      benefits: [
        "Scalable compute for heavy tasks",
        "Native M365/Teams integration",
        "Offline capability with sync",
        "Flexible resource allocation",
        "Enterprise compliance support"
      ]
    },
    remote: {
      title: "REMOTE (Pure Middleware Cloud)",
      icon: Globe,
      description: "Cloud-native architecture with local thin client for maximum scalability",
      architecture: [
        {
          layer: "Client Layer",
          items: ["Digital Twin Thin Client (Electron)", "Web Browser Interface", "Mobile Apps (React Native)"]
        },
        {
          layer: "Edge/CDN",
          items: ["Azure Front Door (CDN)", "Cloudflare Workers", "Edge Authentication"]
        },
        {
          layer: "API & Orchestration",
          items: ["Azure API Management", "Azure Container Instances (orchestrator)", "Microsoft Agent Framework (cloud)", "Auto-scaling Agent Pools"]
        },
        {
          layer: "AI Services",
          items: ["Azure OpenAI GPT-4", "Azure Cognitive Services", "Third-party AI APIs", "Specialized Agent Clusters"]
        },
        {
          layer: "Integration Platform",
          items: ["Microsoft 365 Copilot APIs", "MS Graph (Teams/Outlook)", "Power Automate Flows", "Logic Apps"]
        },
        {
          layer: "Data & Storage",
          items: ["Azure Cosmos DB (multi-region)", "Azure AI Search (vector)", "Azure Blob Storage", "Redis Cache"]
        }
      ],
      techStack: [
        "Azure Kubernetes Service (AKS)",
        "Azure Container Registry",
        "Azure API Management",
        "Azure Monitor + Application Insights",
        "GitHub Actions (CI/CD)"
      ],
      benefits: [
        "Unlimited scalability",
        "Multi-device access",
        "Automatic updates and patches",
        "Enterprise-grade reliability",
        "Global distribution capability"
      ]
    }
  };

  const m365Integration = [
    {
      service: "Outlook 365",
      icon: Mail,
      capabilities: [
        "Read/Send emails via Graph API",
        "Calendar management and scheduling",
        "Contact synchronization",
        "Email analysis and summarization",
        "Automated responses and routing"
      ],
      implementation: "Microsoft Graph SDK + OAuth 2.0"
    },
    {
      service: "MS Teams",
      icon: MessageSquare,
      capabilities: [
        "Bot integration for chat interactions",
        "Meeting transcription processing",
        "Channel message monitoring",
        "Adaptive card responses",
        "File sharing and collaboration"
      ],
      implementation: "Teams AI Library + Bot Framework"
    },
    {
      service: "Microsoft 365 Copilot",
      icon: Zap,
      capabilities: [
        "Extend Copilot with custom agents",
        "Declarative agent registration",
        "Plugin/connector development",
        "Copilot Studio integration",
        "Enterprise data grounding"
      ],
      implementation: "M365 Agents SDK + Copilot APIs"
    }
  ];

  return (
    <div className="min-h-screen bg-gradient-to-br from-slate-900 via-blue-900 to-slate-900 text-white p-8">
      <div className="max-w-7xl mx-auto">
        <div className="text-center mb-12">
          <h1 className="text-5xl font-bold mb-4 bg-gradient-to-r from-blue-400 to-purple-400 bg-clip-text text-transparent">
            Unified AI Infrastructure Design
          </h1>
          <p className="text-xl text-blue-200">
            Enterprise-Grade Agentic Ecosystem for Windows 11
          </p>
        </div>

        <div className="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-12">
          {architectures.map((arch) => {
            const Icon = arch.icon;
            const isExpanded = expandedArch === arch.id;
            return (
              <div key={arch.id} className="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-blue-500/30 hover:border-blue-400/50 transition-all">
                <div className="flex items-start justify-between mb-4">
                  <div className="flex items-center gap-3">
                    <div className="p-3 bg-blue-500/20 rounded-lg">
                      <Icon className="w-8 h-8 text-blue-400" />
                    </div>
                    <div>
                      <h3 className="text-xl font-bold text-blue-300">Architecture {arch.id}</h3>
                      <p className="text-sm text-gray-400">{arch.subtitle}</p>
                    </div>
                  </div>
                </div>
                
                <h4 className="font-bold text-lg mb-2">{arch.name}</h4>
                <p className="text-gray-300 text-sm mb-4">{arch.description}</p>
                
                <button 
                  onClick={() => setExpandedArch(isExpanded ? null : arch.id)}
                  className="flex items-center gap-2 text-blue-400 hover:text-blue-300 text-sm font-medium"
                >
                  {isExpanded ? <ChevronUp className="w-4 h-4" /> : <ChevronDown className="w-4 h-4" />}
                  {isExpanded ? 'Show Less' : 'Show Details'}
                </button>

                {isExpanded && (
                  <div className="mt-4 space-y-4 animate-fadeIn">
                    <div>
                      <p className="text-sm font-semibold text-green-400 mb-2">Advantages:</p>
                      <ul className="text-sm text-gray-300 space-y-1">
                        {arch.pros.map((pro, i) => (
                          <li key={i} className="flex items-start gap-2">
                            <span className="text-green-400">✓</span>
                            <span>{pro}</span>
                          </li>
                        ))}
                      </ul>
                    </div>
                    
                    <div>
                      <p className="text-sm font-semibold text-yellow-400 mb-2">Considerations:</p>
                      <ul className="text-sm text-gray-300 space-y-1">
                        {arch.cons.map((con, i) => (
                          <li key={i} className="flex items-start gap-2">
                            <span className="text-yellow-400">⚠</span>
                            <span>{con}</span>
                          </li>
                        ))}
                      </ul>
                    </div>

                    <div>
                      <p className="text-sm font-semibold text-purple-400 mb-2">Key Components:</p>
                      <div className="space-y-2">
                        {arch.components.map((comp, i) => (
                          <div key={i} className="bg-slate-700/30 rounded-lg p-2">
                            <p className="text-sm font-medium text-purple-300">{comp.name}</p>
                            <p className="text-xs text-gray-400">{comp.role} • {comp.tech}</p>
                          </div>
                        ))}
                      </div>
                    </div>
                  </div>
                )}
              </div>
            );
          })}
        </div>

        <div className="bg-slate-800/50 backdrop-blur-sm rounded-xl p-8 border border-purple-500/30 mb-12">
          <h2 className="text-3xl font-bold mb-6 flex items-center gap-3">
            <Settings className="w-8 h-8 text-purple-400" />
            Deployment Modes
          </h2>
          
          <div className="flex gap-4 mb-6 overflow-x-auto pb-2">
            {Object.entries(deploymentModes).map(([key, mode]) => {
              const Icon = mode.icon;
              return (
                <button
                  key={key}
                  onClick={() => setSelectedTab(key)}
                  className={`flex items-center gap-2 px-6 py-3 rounded-lg font-medium transition-all whitespace-nowrap ${
                    selectedTab === key
                      ? 'bg-blue-500 text-white'
                      : 'bg-slate-700/50 text-gray-300 hover:bg-slate-700'
                  }`}
                >
                  <Icon className="w-5 h-5" />
                  {key.toUpperCase()}
                </button>
              );
            })}
          </div>

          {selectedTab && (
            <div className="space-y-6">
              <div>
                <h3 className="text-2xl font-bold text-blue-300 mb-2">
                  {deploymentModes[selectedTab].title}
                </h3>
                <p className="text-gray-300">{deploymentModes[selectedTab].description}</p>
              </div>

              <div>
                <h4 className="text-lg font-semibold mb-3 text-purple-300">Architecture Layers</h4>
                <div className="space-y-3">
                  {deploymentModes[selectedTab].architecture.map((layer, i) => (
                    <div key={i} className="bg-slate-700/30 rounded-lg p-4">
                      <h5 className="font-semibold text-blue-300 mb-2">{layer.layer}</h5>
                      <div className="flex flex-wrap gap-2">
                        {layer.items.map((item, j) => (
                          <span key={j} className="text-xs bg-blue-500/20 text-blue-200 px-3 py-1 rounded-full">
                            {item}
                          </span>
                        ))}
                      </div>
                    </div>
                  ))}
                </div>
              </div>

              <div className="grid md:grid-cols-2 gap-6">
                <div>
                  <h4 className="text-lg font-semibold mb-3 text-green-300">Technology Stack</h4>
                  <ul className="space-y-2">
                    {deploymentModes[selectedTab].techStack.map((tech, i) => (
                      <li key={i} className="flex items-center gap-2 text-sm text-gray-300">
                        <Database className="w-4 h-4 text-green-400" />
                        {tech}
                      </li>
                    ))}
                  </ul>
                </div>

                <div>
                  <h4 className="text-lg font-semibold mb-3 text-yellow-300">Key Benefits</h4>
                  <ul className="space-y-2">
                    {deploymentModes[selectedTab].benefits.map((benefit, i) => (
                      <li key={i} className="flex items-center gap-2 text-sm text-gray-300">
                        <Zap className="w-4 h-4 text-yellow-400" />
                        {benefit}
                      </li>
                    ))}
                  </ul>
                </div>
              </div>
            </div>
          )}
        </div>

        <div className="bg-slate-800/50 backdrop-blur-sm rounded-xl p-8 border border-green-500/30">
          <h2 className="text-3xl font-bold mb-6 flex items-center gap-3">
            <Users className="w-8 h-8 text-green-400" />
            Microsoft 365 Integration
          </h2>
          
          <div className="grid md:grid-cols-3 gap-6">
            {m365Integration.map((service, i) => {
              const Icon = service.icon;
              return (
                <div key={i} className="bg-slate-700/30 rounded-lg p-6">
                  <div className="flex items-center gap-3 mb-4">
                    <div className="p-2 bg-green-500/20 rounded-lg">
                      <Icon className="w-6 h-6 text-green-400" />
                    </div>
                    <h3 className="font-bold text-lg">{service.service}</h3>
                  </div>
                  
                  <ul className="space-y-2 mb-4">
                    {service.capabilities.map((cap, j) => (
                      <li key={j} className="text-sm text-gray-300 flex items-start gap-2">
                        <ArrowRight className="w-4 h-4 text-green-400 mt-0.5 flex-shrink-0" />
                        <span>{cap}</span>
                      </li>
                    ))}
                  </ul>
                  
                  <div className="pt-4 border-t border-slate-600">
                    <p className="text-xs font-semibold text-purple-300 mb-1">Implementation:</p>
                    <p className="text-xs text-gray-400">{service.implementation}</p>
                  </div>
                </div>
              );
            })}
          </div>
        </div>

        <div className="mt-8 bg-blue-500/10 border border-blue-500/30 rounded-xl p-6">
          <h3 className="text-lg font-bold mb-3 flex items-center gap-2">
            <Shield className="w-6 h-6 text-blue-400" />
            Recommended Implementation Path
          </h3>
          <div className="space-y-2 text-sm text-gray-300">
            <p><strong className="text-blue-300">Phase 1 (Weeks 1-2):</strong> Start with LOCAL deployment using Architecture #1 (Hub-and-Spoke) for simplicity</p>
            <p><strong className="text-blue-300">Phase 2 (Weeks 3-4):</strong> Add Microsoft 365 integration (Outlook/Teams) using Graph API</p>
            <p><strong className="text-blue-300">Phase 3 (Weeks 5-6):</strong> Transition to Architecture #3 (Layered Middleware) for scalability</p>
            <p><strong className="text-blue-300">Phase 4 (Weeks 7-8):</strong> Implement HYBRID deployment for cloud-enhanced capabilities</p>
            <p><strong className="text-blue-300">Phase 5 (Ongoing):</strong> Evolve toward Architecture #2 (Federated) for advanced multi-agent workflows</p>
          </div>
        </div>
      </div>
    </div>
  );
};

export default UnifiedAIArchitecte;

---

Digital Twin Middleware: Complete Implementation Guide
Executive Summary

This guide provides a complete implementation for a unified AI ecosystem on Windows 11, featuring your Digital Twin as the master orchestrator for multiple AI frameworks with Microsoft 365 integration.
Architecture Overview
Recommended: Layered Middleware Architecture (Architecture #3)

Why this approach?

    Best balance of control, scalability, and flexibility
    Easy to add/remove AI frameworks
    Clean separation between UI, orchestration, and services
    Supports all three deployment modes (Local, Hybrid, Remote)
    Native Microsoft 365 integration patterns

Core Technology Stack
Foundation Layer

    Operating System: Windows 11 Pro/Enterprise
    Container Runtime: Docker Desktop with WSL2
    Programming Languages: Python 3.11+, Node.js 20+, TypeScript
    Database: PostgreSQL (state), ChromaDB (vectors)

AI Framework Layer

    Microsoft Agent Framework: Primary orchestration SDK
    LangGraph: Complex workflow orchestration
    AutoGen: Multi-agent conversations
    CrewAI: Role-based agent teams
    LangChain: Tool integration and chains
    Semantic Kernel: .NET/C# integration (optional)

Integration Layer

    Microsoft Graph SDK: M365 API access
    Teams AI Library: Teams bot integration
    Azure OpenAI SDK: LLM access
    FastAPI: REST API server
    Socket.IO: Real-time communication

Middleware Components

    API Gateway: Kong or Nginx
    Message Broker: RabbitMQ or Redis
    Service Mesh: Linkerd (lightweight)
    Monitoring: Prometheus + Grafana

Directory Structure

digital-twin-ecosystem/
├── frontend/                      # Digital Twin Desktop UI
│   ├── electron/                  # Electron main process
│   ├── src/                       # React UI components
│   └── package.json
├── middleware/                    # Core middleware services
│   ├── api-gateway/               # Kong/Nginx configuration
│   ├── orchestrator/              # Master orchestration engine
│   │   ├── router.py              # Agent routing logic
│   │   ├── state_manager.py      # Conversation state
│   │   └── workflow_engine.py    # LangGraph workflows
│   ├── integrations/              # M365 connectors
│   │   ├── outlook_connector.py
│   │   ├── teams_bot.py
│   │   └── graph_client.py
│   └── message_bus/               # RabbitMQ/Redis setup
├── agents/                        # AI Framework containers
│   ├── autogen-service/           # AutoGen agents
│   ├── crewai-service/            # CrewAI crews
│   ├── langchain-service/         # LangChain agents
│   └── custom-agents/             # Your custom agents
├── shared/                        # Shared utilities
│   ├── models/                    # Pydantic models
│   ├── config/                    # Configuration files
│   └── utils/                     # Helper functions
├── docker/                        # Docker configurations
│   ├── docker-compose.local.yml
│   ├── docker-compose.hybrid.yml
│   └── docker-compose.remote.yml
└── docs/                          # Documentation

Implementation Guide
Step 1: Local Environment Setup (Windows 11)
Prerequisites Installation
powershell

# Install Chocolatey (Package Manager)
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Install core dependencies
choco install -y docker-desktop
choco install -y python311
choco install -y nodejs-lts
choco install -y git
choco install -y vscode

# Install Python packages
pip install poetry
poetry config virtualenvs.in-project true

# Install Node.js global packages
npm install -g pnpm
npm install -g electron

Enable WSL2 for Docker
powershell

wsl --install
wsl --set-default-version 2
# Restart computer

Step 2: Core Middleware Implementation
A. Master Orchestrator Service

File: middleware/orchestrator/main.py
python

from fastapi import FastAPI, HTTPException, WebSocket
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import Optional, List, Dict, Any
import asyncio
from datetime import datetime
import uuid

from router import AgentRouter
from state_manager import StateManager
from workflow_engine import WorkflowEngine

app = FastAPI(title="Digital Twin Orchestrator", version="1.0.0")

# CORS configuration
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],  # Electron frontend
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Initialize core components
router = AgentRouter()
state_manager = StateManager()
workflow_engine = WorkflowEngine()

class Message(BaseModel):
    content: str
    user_id: str
    session_id: str
    context: Optional[Dict[str, Any]] = None
    preferred_agent: Optional[str] = None

class AgentResponse(BaseModel):
    message_id: str
    content: str
    agent_used: str
    confidence: float
    timestamp: datetime
    metadata: Dict[str, Any]

@app.post("/api/v1/chat", response_model=AgentResponse)
async def chat_endpoint(message: Message):
    """Main chat endpoint - routes to appropriate agent"""
    try:
        # Generate unique message ID
        message_id = str(uuid.uuid4())
        
        # Get or create session state
        session_state = await state_manager.get_session(message.session_id)
        
        # Determine best agent for this query
        if message.preferred_agent:
            target_agent = message.preferred_agent
        else:
            target_agent = await router.route_message(
                message.content,
                session_state,
                message.context
            )
        
        # Execute agent workflow
        response = await workflow_engine.execute(
            agent_name=target_agent,
            message=message.content,
            state=session_state,
            context=message.context
        )
        
        # Update session state
        await state_manager.update_session(
            message.session_id,
            {
                "last_message": message.content,
                "last_response": response["content"],
                "last_agent": target_agent,
                "updated_at": datetime.utcnow()
            }
        )
        
        return AgentResponse(
            message_id=message_id,
            content=response["content"],
            agent_used=target_agent,
            confidence=response["confidence"],
            timestamp=datetime.utcnow(),
            metadata=response.get("metadata", {})
        )
        
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.websocket("/ws/{session_id}")
async def websocket_endpoint(websocket: WebSocket, session_id: str):
    """WebSocket for real-time communication"""
    await websocket.accept()
    
    try:
        while True:
            data = await websocket.receive_json()
            
            # Process message through orchestrator
            response = await chat_endpoint(
                Message(
                    content=data["message"],
                    user_id=data["user_id"],
                    session_id=session_id,
                    context=data.get("context")
                )
            )
            
            # Send response back
            await websocket.send_json({
                "type": "response",
                "data": response.dict()
            })
            
    except Exception as e:
        await websocket.send_json({
            "type": "error",
            "message": str(e)
        })
    finally:
        await websocket.close()

@app.get("/api/v1/agents")
async def list_agents():
    """Get list of available agents"""
    return await router.get_available_agents()

@app.get("/api/v1/health")
async def health_check():
    """Health check endpoint"""
    agents_status = await router.check_agents_health()
    return {
        "status": "healthy",
        "timestamp": datetime.utcnow(),
        "agents": agents_status
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)

B. Agent Router

File: middleware/orchestrator/router.py
python

import httpx
from typing import Dict, Any, Optional
from enum import Enum

class AgentType(Enum):
    AUTOGEN = "autogen"
    CREWAI = "crewai"
    LANGCHAIN = "langchain"
    CUSTOM = "custom"

class AgentRouter:
    def __init__(self):
        self.agents = {
            AgentType.AUTOGEN: {
                "url": "http://localhost:8001",
                "capabilities": ["conversation", "code", "analysis"],
                "priority": 1
            },
            AgentType.CREWAI: {
                "url": "http://localhost:8002",
                "capabilities": ["research", "writing", "planning"],
                "priority": 2
            },
            AgentType.LANGCHAIN: {
                "url": "http://localhost:8003",
                "capabilities": ["retrieval", "qa", "summarization"],
                "priority": 3
            },
            AgentType.CUSTOM: {
                "url": "http://localhost:8004",
                "capabilities": ["specialized"],
                "priority": 4
            }
        }
        
        self.routing_keywords = {
            "code": [AgentType.AUTOGEN, AgentType.LANGCHAIN],
            "research": [AgentType.CREWAI, AgentType.LANGCHAIN],
            "analyze": [AgentType.AUTOGEN, AgentType.CREWAI],
            "summarize": [AgentType.LANGCHAIN],
            "write": [AgentType.CREWAI],
        }
    
    async def route_message(
        self,
        message: str,
        session_state: Dict[str, Any],
        context: Optional[Dict[str, Any]] = None
    ) -> str:
        """Intelligent routing based on message content and context"""
        
        message_lower = message.lower()
        
        # Check for explicit routing keywords
        for keyword, agents in self.routing_keywords.items():
            if keyword in message_lower:
                # Return highest priority agent that's available
                for agent in sorted(agents, key=lambda x: self.agents[x]["priority"]):
                    if await self.is_agent_healthy(agent):
                        return agent.value
        
        # Context-based routing
        if context and "preferred_domain" in context:
            domain_agent_map = {
                "technical": AgentType.AUTOGEN,
                "creative": AgentType.CREWAI,
                "information": AgentType.LANGCHAIN
            }
            agent = domain_agent_map.get(context["preferred_domain"])
            if agent and await self.is_agent_healthy(agent):
                return agent.value
        
        # Session history-based routing
        if session_state and "last_agent" in session_state:
            last_agent = AgentType(session_state["last_agent"])
            if await self.is_agent_healthy(last_agent):
                return last_agent.value
        
        # Default: Use first available agent
        for agent in AgentType:
            if await self.is_agent_healthy(agent):
                return agent.value
        
        raise Exception("No agents available")
    
    async def is_agent_healthy(self, agent: AgentType) -> bool:
        """Check if agent service is healthy"""
        try:
            async with httpx.AsyncClient() as client:
                response = await client.get(
                    f"{self.agents[agent]['url']}/health",
                    timeout=2.0
                )
                return response.status_code == 200
        except:
            return False
    
    async def get_available_agents(self) -> Dict[str, Any]:
        """Get list of available agents with their status"""
        agents_info = {}
        for agent_type, agent_info in self.agents.items():
            is_healthy = await self.is_agent_healthy(agent_type)
            agents_info[agent_type.value] = {
                "status": "online" if is_healthy else "offline",
                "capabilities": agent_info["capabilities"],
                "url": agent_info["url"]
            }
        return agents_info
    
    async def check_agents_health(self) -> Dict[str, str]:
        """Check health of all agents"""
        health_status = {}
        for agent in AgentType:
            is_healthy = await self.is_agent_healthy(agent)
            health_status[agent.value] = "healthy" if is_healthy else "unhealthy"
        return health_status

Step 3: Microsoft 365 Integration
A. Outlook Connector

File: middleware/integrations/outlook_connector.py
python

from msal import PublicClientApplication
from msgraph.core import GraphClient
from typing import List, Dict, Any
import os

class OutlookConnector:
    def __init__(self):
        self.client_id = os.getenv("AZURE_CLIENT_ID")
        self.tenant_id = os.getenv("AZURE_TENANT_ID")
        self.authority = f"https://login.microsoftonline.com/{self.tenant_id}"
        
        self.app = PublicClientApplication(
            self.client_id,
            authority=self.authority
        )
        
        self.scopes = [
            "Mail.Read",
            "Mail.Send",
            "Mail.ReadWrite",
            "Calendars.ReadWrite",
            "User.Read"
        ]
        
        self.access_token = None
        self.graph_client = None
    
    async def authenticate(self):
        """Authenticate with Microsoft Graph"""
        # Device code flow for desktop app
        flow = self.app.initiate_device_flow(scopes=self.scopes)
        
        if "user_code" not in flow:
            raise ValueError("Failed to create device flow")
        
        print(f"Please visit: {flow['verification_uri']}")
        print(f"And enter code: {flow['user_code']}")
        
        # Wait for user to authenticate
        result = self.app.acquire_token_by_device_flow(flow)
        
        if "access_token" in result:
            self.access_token = result["access_token"]
            self.graph_client = GraphClient(
                credential=lambda: self.access_token
            )
            return True
        else:
            raise Exception(f"Authentication failed: {result.get('error_description')}")
    
    async def get_emails(
        self,
        folder: str = "inbox",
        limit: int = 10
    ) -> List[Dict[str, Any]]:
        """Retrieve emails from specified folder"""
        if not self.graph_client:
            await self.authenticate()
        
        endpoint = f"/me/mailFolders/{folder}/messages?$top={limit}&$select=subject,from,receivedDateTime,bodyPreview"
        
        response = self.graph_client.get(endpoint)
        messages = response.json().get("value", [])
        
        return [{
            "id": msg["id"],
            "subject": msg["subject"],
            "from": msg["from"]["emailAddress"]["address"],
            "preview": msg["bodyPreview"],
            "received": msg["receivedDateTime"]
        } for msg in messages]
    
    async def send_email(
        self,
        to: List[str],
        subject: str,
        body: str,
        body_type: str = "HTML"
    ) -> Dict[str, Any]:
        """Send an email"""
        if not self.graph_client:
            await self.authenticate()
        
        message = {
            "message": {
                "subject": subject,
                "body": {
                    "contentType": body_type,
                    "content": body
                },
                "toRecipients": [
                    {"emailAddress": {"address": addr}} for addr in to
                ]
            }
        }
        
        response = self.graph_client.post("/me/sendMail", json=message)
        return {"status": "sent", "response": response.status_code}
    
    async def get_calendar_events(
        self,
        days_ahead: int = 7
    ) -> List[Dict[str, Any]]:
        """Get upcoming calendar events"""
        if not self.graph_client:
            await self.authenticate()
        
        from datetime import datetime, timedelta
        
        start_date = datetime.utcnow().isoformat()
        end_date = (datetime.utcnow() + timedelta(days=days_ahead)).isoformat()
        
        endpoint = f"/me/calendar/calendarView?startDateTime={start_date}&endDateTime={end_date}"
        
        response = self.graph_client.get(endpoint)
        events = response.json().get("value", [])
        
        return [{
            "id": event["id"],
            "subject": event["subject"],
            "start": event["start"]["dateTime"],
            "end": event["end"]["dateTime"],
            "organizer": event["organizer"]["emailAddress"]["address"]
        } for event in events]

B. Teams Bot Integration

File: middleware/integrations/teams_bot.py
python

from botbuilder.core import ActivityHandler, TurnContext, MessageFactory
from botbuilder.schema import Activity, ActivityTypes
from typing import Dict, Any
import httpx

class DigitalTwinTeamsBot(ActivityHandler):
    def __init__(self, orchestrator_url: str = "http://localhost:8000"):
        self.orchestrator_url = orchestrator_url
        self.sessions = {}  # In-memory session storage
    
    async def on_message_activity(self, turn_context: TurnContext):
        """Handle incoming messages from Teams"""
        user_id = turn_context.activity.from_property.id
        session_id = self.sessions.get(user_id, f"teams_{user_id}")
        
        # Store session mapping
        self.sessions[user_id] = session_id
        
        # Get user message
        user_message = turn_context.activity.text
        
        # Send typing indicator
        await turn_context.send_activity(
            Activity(type=ActivityTypes.typing)
        )
        
        # Forward to orchestrator
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.orchestrator_url}/api/v1/chat",
                json={
                    "content": user_message,
                    "user_id": user_id,
                    "session_id": session_id,
                    "context": {
                        "channel": "teams",
                        "conversation_id": turn_context.activity.conversation.id
                    }
                },
                timeout=30.0
            )
            
            if response.status_code == 200:
                data = response.json()
                reply = MessageFactory.text(data["content"])
                
                # Add agent info as adaptive card (optional)
                if data.get("metadata"):
                    reply.attachments = [
                        self._create_agent_card(data)
                    ]
                
                await turn_context.send_activity(reply)
            else:
                await turn_context.send_activity(
                    "Sorry, I encountered an error processing your request."
                )
    
    async def on_members_added_activity(
        self,
        members_added: list,
        turn_context: TurnContext
    ):
        """Welcome new members"""
        for member in members_added:
            if member.id != turn_context.activity.recipient.id:
                await turn_context.send_activity(
                    f"Hello {member.name}! I'm your Digital Twin assistant. "
                    "I can help you with various tasks across multiple AI systems. "
                    "Just ask me anything!"
                )
    
    def _create_agent_card(self, response_data: Dict[str, Any]) -> Dict[str, Any]:
        """Create an Adaptive Card showing which agent responded"""
        return {
            "contentType": "application/vnd.microsoft.card.adaptive",
            "content": {
                "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
                "type": "AdaptiveCard",
                "version": "1.4",
                "body": [
                    {
                        "type": "TextBlock",
                        "text": f"Powered by: {response_data['agent_used']}",
                        "size": "small",
                        "weight": "lighter",
                        "color": "accent"
                    },
                    {
                        "type": "TextBlock",
                        "text": f"Confidence: {response_data['confidence']:.1%}",
                        "size": "small",
                        "weight": "lighter"
                    }
                ]
            }
        }

Step 4: Docker Compose Configuration

File: docker/docker-compose.local.yml
yaml

version: '3.8'

services:
  # PostgreSQL - State Management
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: digital_twin
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - ai_network

  # Redis - Message Queue & Cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    networks:
      - ai_network

  # ChromaDB - Vector Storage
  chromadb:
    image: ghcr.io/chroma-core/chroma:latest
    ports:
      - "8100:8000"
    volumes:
      - chroma_data:/chroma/chroma
    environment:
      - ALLOW_RESET=TRUE
    networks:
      - ai_network

  # Master Orchestrator
  orchestrator:
    build:
      context: ../middleware/orchestrator
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://admin:${DB_PASSWORD}@postgres:5432/digital_twin
      - REDIS_URL=redis://redis:6379
      - CHROMA_URL=http://chromadb:8000
    depends_on:
      - postgres
      - redis
      - chromadb
    networks:
      - ai_network

  # AutoGen Service
  autogen:
    build:
      context: ../agents/autogen-service
      dockerfile: Dockerfile
    ports:
      - "8001:8000"
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ORCHESTRATOR_URL=http://orchestrator:8000
    networks:
      - ai_network

  # CrewAI Service
  crewai:
    build:
      context: ../agents/crewai-service
      dockerfile: Dockerfile
    ports:
      - "8002:8000"
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ORCHESTRATOR_URL=http://orchestrator:8000
    networks:
      - ai_network

  # LangChain Service
  langchain:
    build:
      context: ../agents/langchain-service
      dockerfile: Dockerfile
    ports:
      - "8003:8000"
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ORCHESTRATOR_URL=http://orchestrator:8000
      - CHROMA_URL=http://chromadb:8000
    networks:
      - ai_network

  # Teams Bot Service
  teams-bot:
    build:
      context: ../middleware/integrations
      dockerfile: Dockerfile.teams
    ports:
      - "3978:3978"
    environment:
      - MICROSOFT_APP_ID=${TEAMS_APP_ID}
      - MICROSOFT_APP_PASSWORD=${TEAMS_APP_PASSWORD}
      - ORCHESTRATOR_URL=http://orchestrator:8000
    networks:
      - ai_network

  # Nginx API Gateway
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ../nginx/nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - orchestrator
    networks:
      - ai_network

volumes:
  postgres_data:
  chroma_data:

networks:
  ai_network:
    driver: bridge

Step 5: Frontend (Digital Twin Desktop)

File: frontend/src/App.tsx
typescript

import React, { useState, useEffect, useRef } from 'react';
import { Send, Bot, User, Settings, Activity } from 'lucide-react';

interface Message {
  id: string;
  content: string;
  sender: 'user' | 'agent';
  agent?: string;
  timestamp: Date;
}

interface AgentStatus {
  name: string;
  status: 'online' | 'offline';
  capabilities: string[];
}

const App: React.FC = () => {
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState('');
  const [loading, setLoading] = useState(false);
  const [agents, setAgents] = useState<AgentStatus[]>([]);
  const [selectedAgent, setSelectedAgent] = useState<string | null>(null);
  const [sessionId] = useState(`session_${Date.now()}`);
  const ws = useRef<WebSocket | null>(null);

  // Initialize WebSocket connection
  useEffect(() => {
    const connectWebSocket = () => {
      ws.current = new WebSocket(`ws://localhost:8000/ws/${sessionId}`);
      
      ws.current.onopen = () => {
        console.log('Connected to orchestrator');
      };
      
      ws.current.onmessage = (event) => {
        const data = JSON.parse(event.data);
        
        if (data.type === 'response') {
          const agentMessage: Message = {
            id: data.data.message_id,
            content: data.data.content,
            sender: 'agent',
            agent: data.data.agent_used,
            timestamp: new Date(data.data.timestamp)
          };
          
          setMessages(prev => [...prev, agentMessage]);
          setLoading(false);
        } else if (data.type === 'error') {
          console.error('Error:', data.message);
          setLoading(false);
        }
      };
      
      ws.current.onerror = (error) => {
        console.error('WebSocket error:', error);
      };
      
      ws.current.onclose = () => {
        console.log('Disconnected from orchestrator');
        // Attempt reconnection after 3 seconds
        setTimeout(connectWebSocket, 3000);
      };
    };

    connectWebSocket();

    // Fetch available agents
    fetch('http://localhost:8000/api/v1/agents')
      .then(res => res.json())
      .then(data => {
        const agentList = Object.entries(data).map(([name, info]: [string, any]) => ({
          name,
          status: info.status,
          capabilities: info.capabilities
        }));
        setAgents(agentList);
      });

    return () => {
      ws.current?.close();
    };
  }, [sessionId]);

  const sendMessage = () => {
    if (!input.trim() || !ws.current) return;

    const userMessage: Message = {
      id: `msg_${Date.now()}`,
      content: input,
      sender: 'user',
      timestamp: new Date()
    };

    setMessages(prev => [...prev, userMessage]);
    setLoading(true);

    // Send via WebSocket
    ws.current.send(JSON.stringify({
      message: input,
      user_id: 'local_user',
      preferred_agent: selectedAgent,
      context: {
        source: 'desktop'
      }
    }));

    setInput('');
  };

  return (
    <div className="flex h-screen bg-gray-900 text-white">
      {/* Sidebar - Agent Status */}
      <div className="w-64 bg-gray-800 p-4 border-r border-gray-700">
        <h2 className="text-xl font-bold mb-4 flex items-center gap-2">
          <Activity className="w-5 h-5" />
          AI Agents
        </h2>
        
        <div className="space-y-2">
          {agents.map(agent => (
            <div
              key={agent.name}
              onClick={() => setSelectedAgent(
                selectedAgent === agent.name ? null : agent.name
              )}
              className={`p-3 rounded cursor-pointer transition-colors ${
                selectedAgent === agent.name 
                  ? 'bg-blue-600' 
                  : 'bg-gray-700 hover:bg-gray-600'
              }`}
            >
              <div className="flex items-center justify-between mb-1">
                <span className="font-medium">{agent.name}</span>
                <span className={`w-2 h-2 rounded-full ${
                  agent.status === 'online' ? 'bg-green-500' : 'bg-red-500'
                }`} />
              </div>
              <div className="text-xs text-gray-400">
                {agent.capabilities.join(', ')}
              </div>
            </div>
          ))}
        </div>
      </div>

      {/* Main Chat Area */}
      <div className="flex-1 flex flex-col">
        <div className="bg-gray-800 p-4 border-b border-gray-700">
          <h1 className="text-2xl font-bold">Digital Twin Assistant</h1>
          <p className="text-sm text-gray-400">
            {selectedAgent ? `Using: ${selectedAgent}` : 'Auto-routing enabled'}
          </p>
        </div>

        <div className="flex-1 overflow-y-auto p-4 space-y-4">
          {messages.map(msg => (
            <div
              key={msg.id}
              className={`flex gap-3 ${
                msg.sender === 'user' ? 'justify-end' : 'justify-start'
              }`}
            >
              {msg.sender === 'agent' && (
                <div className="w-8 h-8 bg-blue-600 rounded-full flex items-center justify-center flex-shrink-0">
                  <Bot className="w-5 h-5" />
                </div>
              )}
              
              <div className={`max-w-2xl ${
                msg.sender === 'user' 
                  ? 'bg-blue-600 rounded-l-lg rounded-tr-lg' 
                  : 'bg-gray-700 rounded-r-lg rounded-tl-lg'
              } p-3`}>
                <p className="whitespace-pre-wrap">{msg.content}</p>
                {msg.agent && (
                  <p className="text-xs text-gray-400 mt-2">
                    via {msg.agent}
                  </p>
                )}
              </div>
              
              {msg.sender === 'user' && (
                <div className="w-8 h-8 bg-gray-600 rounded-full flex items-center justify-center flex-shrink-0">
                  <User className="w-5 h-5" />
                </div>
              )}
            </div>
          ))}
          
          {loading && (
            <div className="flex gap-3">
              <div className="w-8 h-8 bg-blue-600 rounded-full flex items-center justify-center">
                <Bot className="w-5 h-5" />
              </div>
              <div className="bg-gray-700 rounded-r-lg rounded-tl-lg p-3">
                <div className="flex gap-1">
                  <div className="w-2 h-2 bg-gray-400 rounded-full animate-bounce" />
                  <div className="w-2 h-2 bg-gray-400 rounded-full animate-bounce delay-100" />
                  <div className="w-2 h-2 bg-gray-400 rounded-full animate-bounce delay-200" />
                </div>
              </div>
            </div>
          )}
        </div>

        <div className="bg-gray-800 p-4 border-t border-gray-700">
          <div className="flex gap-2">
            <input
              type="text"
              value={input}
              onChange={(e) => setInput(e.target.value)}
              onKeyPress={(e) => e.key === 'Enter' && sendMessage()}
              placeholder="Ask anything..."
              className="flex-1 bg-gray-700 rounded px-4 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
            />
            <button
              onClick={sendMessage}
              disabled={loading || !input.trim()}
              className="bg-blue-600 hover:bg-blue-700 disabled:bg-gray-600 px-6 py-2 rounded flex items-center gap-2 transition-colors"
            >
              <Send className="w-4 h-4" />
              Send
            </button>
          </div>
        </div>
      </div>
    </div>
  );
};

export default App;

Deployment Configurations
1. LOCAL Deployment

Complete local setup running on Windows 11
bash

# Start all services
cd docker
docker-compose -f docker-compose.local.yml up -d

# Verify all services are running
docker-compose ps

# View logs
docker-compose logs -f orchestrator

# Access services:
# - Orchestrator API: http://localhost:8000
# - AutoGen: http://localhost:8001
# - CrewAI: http://localhost:8002
# - LangChain: http://localhost:8003
# - PostgreSQL: localhost:5432
# - Redis: localhost:6379
# - ChromaDB: http://localhost:8100

Benefits:

    Complete offline capability
    Full control and debugging
    No external dependencies
    Maximum privacy
    Zero cloud costs

Ideal for:

    Development and testing
    Sensitive data processing
    Environments without internet
    Learning and experimentation

2. HYBRID Deployment

Local orchestration with cloud enhancements

File: docker/docker-compose.hybrid.yml
yaml

version: '3.8'

services:
  # Local components (same as local)
  postgres:
    # ... (same as local config)
  
  redis:
    # ... (same as local config)
  
  orchestrator:
    build:
      context: ../middleware/orchestrator
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://admin:${DB_PASSWORD}@postgres:5432/digital_twin
      - REDIS_URL=redis://redis:6379
      # Azure connections
      - AZURE_OPENAI_ENDPOINT=${AZURE_OPENAI_ENDPOINT}
      - AZURE_OPENAI_KEY=${AZURE_OPENAI_KEY}
      - AZURE_SERVICE_BUS_CONNECTION=${AZURE_SERVICE_BUS_CONNECTION}
      # M365 integration
      - AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
      - AZURE_TENANT_ID=${AZURE_TENANT_ID}
      - GRAPH_API_ENDPOINT=https://graph.microsoft.com/v1.0
      # Hybrid mode flag
      - DEPLOYMENT_MODE=hybrid
      - CLOUD_AGENT_ENDPOINTS=${CLOUD_AGENT_ENDPOINTS}
    networks:
      - ai_network

  # Lightweight local agents
  autogen-light:
    build:
      context: ../agents/autogen-service
      args:
        - MODEL=gpt-3.5-turbo  # Use lighter model locally
    ports:
      - "8001:8000"
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - OFFLOAD_TO_CLOUD=true
      - CLOUD_ENDPOINT=${AZURE_CONTAINER_APPS_URL}/autogen-heavy
    networks:
      - ai_network

  # M365 Integration Service (local)
  m365-connector:
    build:
      context: ../middleware/integrations
    ports:
      - "8005:8000"
    environment:
      - AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
      - AZURE_TENANT_ID=${AZURE_TENANT_ID}
      - AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
      - GRAPH_API_SCOPES=Mail.Read,Mail.Send,Calendars.ReadWrite,Chat.Read
      - ORCHESTRATOR_URL=http://orchestrator:8000
    networks:
      - ai_network

networks:
  ai_network:
    driver: bridge

Azure Container Apps Deployment (for heavy agents):
bash

# Deploy heavy compute agents to Azure
az containerapp create \
  --name autogen-heavy \
  --resource-group digital-twin-rg \
  --environment digital-twin-env \
  --image youracr.azurecr.io/autogen-service:latest \
  --target-port 8000 \
  --ingress external \
  --min-replicas 0 \
  --max-replicas 10 \
  --env-vars \
    OPENAI_API_KEY=${OPENAI_API_KEY} \
    AZURE_OPENAI_ENDPOINT=${AZURE_OPENAI_ENDPOINT}

# Deploy to Azure Service Bus for messaging
az servicebus namespace create \
  --name digital-twin-bus \
  --resource-group digital-twin-rg \
  --sku Standard

Benefits:

    Best of both worlds
    Scalable compute when needed
    Native M365 integration
    Offline core functionality
    Cost-effective (pay for cloud only when used)

Ideal for:

    Production environments
    Enterprise deployments
    Teams collaboration
    Scalable workloads

3. REMOTE (Cloud-Native)

Pure middleware cloud architecture

Azure Infrastructure Setup:
bash

# Create resource group
az group create \
  --name digital-twin-cloud-rg \
  --location eastus

# Create Azure Kubernetes Service
az aks create \
  --resource-group digital-twin-cloud-rg \
  --name digital-twin-aks \
  --node-count 3 \
  --enable-addons monitoring \
  --generate-ssh-keys

# Get credentials
az aks get-credentials \
  --resource-group digital-twin-cloud-rg \
  --name digital-twin-aks

# Deploy using Kubernetes manifests
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmaps.yaml
kubectl apply -f k8s/secrets.yaml
kubectl apply -f k8s/deployments/
kubectl apply -f k8s/services/
kubectl apply -f k8s/ingress.yaml

Kubernetes Deployment Example:

File: k8s/deployments/orchestrator-deployment.yaml
yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: orchestrator
  namespace: digital-twin
spec:
  replicas: 3
  selector:
    matchLabels:
      app: orchestrator
  template:
    metadata:
      labels:
        app: orchestrator
    spec:
      containers:
      - name: orchestrator
        image: youracr.azurecr.io/orchestrator:latest
        ports:
        - containerPort: 8000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: connection-string
        - name: AZURE_OPENAI_ENDPOINT
          valueFrom:
            configMapKeyRef:
              name: azure-config
              key: openai-endpoint
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
        livenessProbe:
          httpGet:
            path: /api/v1/health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /api/v1/health
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: orchestrator-service
  namespace: digital-twin
spec:
  selector:
    app: orchestrator
  ports:
  - port: 80
    targetPort: 8000
  type: LoadBalancer

Benefits:

    Unlimited scalability
    Global availability
    Auto-scaling and healing
    Enterprise reliability
    Multi-device access

Ideal for:

    Enterprise scale
    Global teams
    SaaS offerings
    Maximum uptime requirements

Quick Start Guide
Phase 1: Local Setup (Week 1)
bash

# 1. Clone repository
git clone https://github.com/yourusername/digital-twin-ecosystem.git
cd digital-twin-ecosystem

# 2. Create environment file
cat > .env << EOF
# Database
DB_PASSWORD=your_secure_password

# OpenAI
OPENAI_API_KEY=your_openai_key

# Azure (for hybrid/remote)
AZURE_CLIENT_ID=your_client_id
AZURE_TENANT_ID=your_tenant_id
AZURE_CLIENT_SECRET=your_client_secret

# Teams Bot
TEAMS_APP_ID=your_teams_app_id
TEAMS_APP_PASSWORD=your_teams_app_password
EOF

# 3. Start local environment
docker-compose -f docker/docker-compose.local.yml up -d

# 4. Verify services
curl http://localhost:8000/api/v1/health

# 5. Start frontend (separate terminal)
cd frontend
npm install
npm run electron:dev

Phase 2: M365 Integration (Week 2)
bash

# 1. Register Azure AD application
az ad app create \
  --display-name "Digital Twin Desktop" \
  --sign-in-audience AzureADMyOrg

# 2. Configure API permissions
az ad app permission add \
  --id <APP_ID> \
  --api 00000003-0000-0000-c000-000000000000 \
  --api-permissions \
    e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope \
    7427e0e9-2fba-42fe-b0c0-848c9e6a8182=Scope

# 3. Test Outlook connection
python middleware/integrations/test_outlook.py

# 4. Deploy Teams bot
az bot create \
  --name digital-twin-bot \
  --resource-group digital-twin-rg \
  --kind registration \
  --appid ${TEAMS_APP_ID}

Phase 3: Hybrid Deployment (Week 3)
bash

# 1. Create Azure resources
./scripts/setup-azure-hybrid.sh

# 2. Deploy cloud agents
./scripts/deploy-cloud-agents.sh

# 3. Update local config for hybrid mode
# Edit .env to add cloud endpoints

# 4. Restart with hybrid compose
docker-compose -f docker/docker-compose.hybrid.yml up -d

Middleware GUI Tool

Simple web-based configuration interface

File: middleware/admin-ui/src/ConfigManager.tsx
typescript

import React, { useState, useEffect } from 'react';
import { Plus, Trash2, Save, RefreshCw } from 'lucide-react';

interface AgentConfig {
  id: string;
  name: string;
  type: 'autogen' | 'crewai' | 'langchain' | 'custom';
  url: string;
  enabled: boolean;
  capabilities: string[];
  priority: number;
}

const ConfigManager: React.FC = () => {
  const [agents, setAgents] = useState<AgentConfig[]>([]);
  const [newAgent, setNewAgent] = useState<Partial<AgentConfig>>({
    type: 'custom',
    enabled: true,
    priority: 5
  });

  useEffect(() => {
    loadConfig();
  }, []);

  const loadConfig = async () => {
    const response = await fetch('http://localhost:8000/api/v1/config/agents');
    const data = await response.json();
    setAgents(data);
  };

  const saveConfig = async () => {
    await fetch('http://localhost:8000/api/v1/config/agents', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(agents)
    });
    alert('Configuration saved!');
  };

  const addAgent = () => {
    if (newAgent.name && newAgent.url) {
      setAgents([...agents, { ...newAgent, id: Date.now().toString() } as AgentConfig]);
      setNewAgent({ type: 'custom', enabled: true, priority: 5 });
    }
  };

  const deleteAgent = (id: string) => {
    setAgents(agents.filter(a => a.id !== id));
  };

  return (
    <div className="min-h-screen bg-gray-900 text-white p-8">
      <div className="max-w-6xl mx-auto">
        <h1 className="text-3xl font-bold mb-8">Agent Configuration Manager</h1>
        
        {/* Existing Agents */}
        <div className="bg-gray-800 rounded-lg p-6 mb-8">
          <h2 className="text-xl font-bold mb-4">Registered Agents</h2>
          <div className="space-y-4">
            {agents.map(agent => (
              <div key={agent.id} className="bg-gray-700 rounded p-4 flex items-center justify-between">
                <div className="flex-1">
                  <h3 className="font-bold">{agent.name}</h3>
                  <p className="text-sm text-gray-400">{agent.url}</p>
                  <p className="text-xs text-gray-500">
                    Type: {agent.type} | Priority: {agent.priority} | 
                    Status: {agent.enabled ? '✓ Enabled' : '✗ Disabled'}
                  </p>
                </div>
                <button
                  onClick={() => deleteAgent(agent.id)}
                  className="p-2 hover:bg-red-600 rounded transition-colors"
                >
                  <Trash2 className="w-5 h-5" />
                </button>
              </div>
            ))}
          </div>
        </div>

        {/* Add New Agent */}
        <div className="bg-gray-800 rounded-lg p-6 mb-8">
          <h2 className="text-xl font-bold mb-4">Add New Agent</h2>
          <div className="grid grid-cols-2 gap-4">
            <input
              type="text"
              placeholder="Agent Name"
              value={newAgent.name || ''}
              onChange={(e) => setNewAgent({...newAgent, name: e.target.value})}
              className="bg-gray-700 rounded px-4 py-2"
            />
            <input
              type="text"
              placeholder="URL (e.g., http://localhost:8005)"
              value={newAgent.url || ''}
              onChange={(e) => setNewAgent({...newAgent, url: e.target.value})}
              className="bg-gray-700 rounded px-4 py-2"
            />
            <select
              value={newAgent.type}
              onChange={(e) => setNewAgent({...newAgent, type: e.target.value as any})}
              className="bg-gray-700 rounded px-4 py-2"
            >
              <option value="autogen">AutoGen</option>
              <option value="crewai">CrewAI</option>
              <option value="langchain">LangChain</option>
              <option value="custom">Custom</option>
            </select>
            <input
              type="number"
              placeholder="Priority (1-10)"
              value={newAgent.priority || 5}
              onChange={(e) => setNewAgent({...newAgent, priority: parseInt(e.target.value)})}
              className="bg-gray-700 rounded px-4 py-2"
            />
          </div>
          <button
            onClick={addAgent}
            className="mt-4 bg-blue-600 hover:bg-blue-700 px-6 py-2 rounded flex items-center gap-2"
          >
            <Plus className="w-4 h-4" />
            Add Agent
          </button>
        </div>

        {/* Actions */}
        <div className="flex gap-4">
          <button
            onClick={saveConfig}
            className="bg-green-600 hover:bg-green-700 px-6 py-2 rounded flex items-center gap-2"
          >
            <Save className="w-4 h-4" />
            Save Configuration
          </button>
          <button
            onClick={loadConfig}
            className="bg-gray-600 hover:bg-gray-700 px-6 py-2 rounded flex items-center gap-2"
          >
            <RefreshCw className="w-4 h-4" />
            Reload
          </button>
        </div>
      </div>
    </div>
  );
};

export default ConfigManager;

Testing & Validation
bash

# Test orchestrator health
curl http://localhost:8000/api/v1/health

# Test agent routing
curl -X POST http://localhost:8000/api/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Write a Python function to calculate fibonacci",
    "user_id": "test_user",
    "session_id": "test_session"
  }'

# Test Outlook integration
python -m pytest tests/test_outlook_connector.py

# Test Teams bot
python -m pytest tests/test_teams_bot.py

# Load testing
locust -f tests/load_test.py --host=http://localhost:8000

Monitoring & Observability

Prometheus Configuration:
yaml

# prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'orchestrator'
    static_configs:
      - targets: ['localhost:8000']
  
  - job_name: 'agents'
    static_configs:
      - targets: 
        - 'localhost:8001'
        - 'localhost:8002'
        - 'localhost:8003'

Grafana Dashboard:

    Agent health status
    Request latency
    Agent utilization
    Error rates
    Token usage
    Cost tracking

Security Best Practices

    Authentication: Use OAuth 2.0 for M365 integration
    API Keys: Store in Azure Key Vault or Windows Credential Manager
    Network: Use TLS/SSL for all communications
    Data: Encrypt sensitive data at rest
    Access Control: Implement RBAC for agent access
    Audit Logging: Track all agent interactions

Cost Optimization

Local: $0 (only electricity costs) Hybrid: ~$100-300/month (depending on cloud usage) Remote: ~$500-2000/month (based on scale)

Tips:

    Use spot instances for non-critical agents
    Implement request caching
    Auto-scale agents based on demand
    Use reserved instances for steady workloads

Next Steps

    Week 1-2: Deploy local infrastructure
    Week 3: Add M365 integration
    Week 4: Test and optimize
    Week 5: Move to hybrid if needed
    Week 6+: Scale and enhance

Support Resources

    Microsoft Graph API: https://docs.microsoft.com/graph
    Teams Bot Framework: https://docs.microsoft.com/microsoftteams
    LangGraph: https://langchain-ai.github.io/langgraph
    AutoGen: https://microsoft.github.io/autogen
    CrewAI: https://docs.crewai.com

Conclusion

This architecture provides a complete, production-ready solution for your unified AI ecosystem with three deployment options to match your needs. Start with LOCAL for development, move to HYBRID for production, and scale to REMOTE when needed.
