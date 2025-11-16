import React, { useState } from 'react';
import { Rocket, Zap, Copy, Check, ChevronRight, Globe, Brain, Database, Shield, Cpu } from 'lucide-react';

const QuickStartGuide = () => {
  const [copiedId, setCopiedId] = useState(null);
  const [activeUseCase, setActiveUseCase] = useState(null);

  const copyToClipboard = (text, id) => {
    navigator.clipboard.writeText(text);
    setCopiedId(id);
    setTimeout(() => setCopiedId(null), 2000);
  };

  const quickStartSteps = [
    {
      title: "Install Rust",
      command: "curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh",
      description: "If you don't have Rust installed"
    },
    {
      title: "Create Project",
      command: "mkdir eternal-core && cd eternal-core",
      description: "Create your project directory"
    },
    {
      title: "Initialize Workspace",
      command: `cat > Cargo.toml << 'EOF'
[workspace]
members = ["core", "phoenix"]
resolver = "2"

[workspace.dependencies]
tokio = { version = "1.38", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
async-trait = "0.1"
EOF`,
      description: "Create workspace configuration"
    },
    {
      title: "Create Core Library",
      command: "cargo new --lib core",
      description: "Initialize the core library"
    },
    {
      title: "Build & Test",
      command: "cargo build && cargo test",
      description: "Verify everything works"
    }
  ];

  const useCases = [
    {
      id: 'multi-protocol-api',
      icon: Globe,
      color: 'blue',
      title: "Multi-Protocol API Gateway",
      description: "Route between HTTP/2, HTTP/3, gRPC, and WebSocket protocols dynamically",
      complexity: "Intermediate",
      timeToImplement: "2-4 hours",
      code: `// phoenix/src/main.rs
use eternal_core::{EternalCore, Connector, ConnectorError};
use async_trait::async_trait;
use std::sync::Arc;
use tokio::sync::RwLock;

#[derive(Debug)]
struct HTTP2Connector {
    endpoint: String,
}

#[async_trait]
impl Connector<Vec<u8>> for HTTP2Connector {
    async fn send(&self, data: Vec<u8>) -> Result<(), ConnectorError> {
        println!("Sending {} bytes via HTTP/2", data.len());
        Ok(())
    }
    
    async fn recv(&self) -> Result<Vec<u8>, ConnectorError> {
        Ok(vec![])
    }
    
    fn id(&self) -> &'static str {
        "http2-gateway"
    }
}

#[tokio::main]
async fn main() {
    let core = EternalCore::new("./gateway_data");
    println!("Multi-protocol gateway running!");
}`,
      cursorPrompt: `"Create a multi-protocol API gateway with HTTP/2, HTTP/3, and WebSocket connectors. Include automatic protocol detection and routing based on request headers."`
    },
    {
      id: 'persistent-ai-memory',
      icon: Brain,
      color: 'purple',
      title: "Persistent AI Memory System",
      description: "Long-term memory for AI agents with automatic backup and recovery",
      complexity: "Beginner",
      timeToImplement: "1-2 hours",
      code: `// examples/ai_memory.rs
use eternal_core::Memory;
use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize, Clone)]
struct Conversation {
    user_id: String,
    messages: Vec<Message>,
    context: Vec<String>,
}

#[derive(Debug, Serialize, Deserialize, Clone)]
struct Message {
    role: String,
    content: String,
    timestamp: u64,
}

fn main() {
    let memory = Memory::new("./ai_memory").unwrap();
    
    let conv = Conversation {
        user_id: "user_123".to_string(),
        messages: vec![],
        context: vec!["weather".to_string()],
    };
    
    memory.store("conv:user_123:session_1", &conv).unwrap();
    
    let recalled: Conversation = memory
        .recall("conv:user_123:session_1")
        .unwrap();
    
    println!("Recalled conversation for user: {}", recalled.user_id);
}`,
      cursorPrompt: `"Create a persistent memory system for AI conversations with automatic context tracking, message history, and user preferences. Include methods for searching past conversations."`
    },
    {
      id: 'distributed-data-sync',
      icon: Database,
      color: 'green',
      title: "Distributed Data Sync Engine",
      description: "Sync data across multiple nodes with automatic conflict resolution",
      complexity: "Advanced",
      timeToImplement: "4-8 hours",
      code: `// examples/distributed_sync.rs
use eternal_core::{EternalCore, Memory, Connector, ConnectorError};
use async_trait::async_trait;
use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize, Clone)]
struct DataNode {
    id: String,
    data: Vec<u8>,
    version: u64,
    timestamp: u64,
}

#[derive(Debug)]
struct P2PConnector {
    peer_address: String,
    local_memory: Memory,
}

#[async_trait]
impl Connector<DataNode> for P2PConnector {
    async fn send(&self, node: DataNode) -> Result<(), ConnectorError> {
        println!("Syncing {} to {}", node.id, self.peer_address);
        Ok(())
    }
    
    async fn recv(&self) -> Result<DataNode, ConnectorError> {
        Ok(DataNode {
            id: "received".to_string(),
            data: vec![],
            version: 1,
            timestamp: 0,
        })
    }
    
    fn id(&self) -> &'static str {
        "p2p-sync"
    }
}

#[tokio::main]
async fn main() {
    let core = EternalCore::new("./sync_data");
    println!("Distributed sync operational!");
}`,
      cursorPrompt: `"Build a distributed data synchronization system with P2P connectors, conflict resolution using vector clocks, and automatic node discovery. Include retry logic and health checks."`
    },
    {
      id: 'secure-iot-hub',
      icon: Shield,
      color: 'orange',
      title: "Secure IoT Device Hub",
      description: "Manage thousands of IoT devices with multiple protocol support",
      complexity: "Intermediate",
      timeToImplement: "3-5 hours",
      code: `// examples/iot_hub.rs
use eternal_core::{EternalCore, Connector, ConnectorError};
use async_trait::async_trait;
use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize, Clone)]
struct Device {
    device_id: String,
    protocol: String,
    status: String,
    last_seen: u64,
}

#[derive(Debug, Serialize, Deserialize, Clone)]
struct Telemetry {
    device_id: String,
    temperature: f32,
    humidity: f32,
    timestamp: u64,
}

#[derive(Debug)]
struct MQTTConnector {
    broker: String,
}

#[async_trait]
impl Connector<Telemetry> for MQTTConnector {
    async fn send(&self, data: Telemetry) -> Result<(), ConnectorError> {
        println!("Publishing telemetry from {}", data.device_id);
        Ok(())
    }
    
    async fn recv(&self) -> Result<Telemetry, ConnectorError> {
        Ok(Telemetry {
            device_id: "sensor_001".to_string(),
            temperature: 22.5,
            humidity: 45.0,
            timestamp: 1234567890,
        })
    }
    
    fn id(&self) -> &'static str {
        "mqtt-iot"
    }
}

#[tokio::main]
async fn main() {
    let core = EternalCore::new("./iot_data");
    println!("IoT Hub operational!");
}`,
      cursorPrompt: `"Create an IoT device hub supporting MQTT, CoAP, and LoRaWAN protocols. Include device registration, telemetry collection, and automatic device health monitoring with alerts."`
    },
    {
      id: 'ai-model-router',
      icon: Cpu,
      color: 'pink',
      title: "AI Model Router & Load Balancer",
      description: "Route AI requests across multiple models and providers intelligently",
      complexity: "Advanced",
      timeToImplement: "5-8 hours",
      code: `// examples/ai_router.rs
use eternal_core::{EternalCore, Connector, ConnectorError};
use async_trait::async_trait;
use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize, Clone)]
struct AIRequest {
    prompt: String,
    model: String,
    max_tokens: u32,
}

#[derive(Debug, Serialize, Deserialize, Clone)]
struct AIResponse {
    content: String,
    model: String,
    tokens_used: u32,
    latency_ms: u64,
}

#[derive(Debug)]
struct ClaudeConnector {
    api_key: String,
}

#[async_trait]
impl Connector<(AIRequest, AIResponse)> for ClaudeConnector {
    async fn send(&self, data: (AIRequest, AIResponse)) -> Result<(), ConnectorError> {
        let (req, _) = data;
        println!("Routing to Claude: {}", req.prompt);
        Ok(())
    }
    
    async fn recv(&self) -> Result<(AIRequest, AIResponse), ConnectorError> {
        Ok((
            AIRequest {
                prompt: "".to_string(),
                model: "claude-3-opus".to_string(),
                max_tokens: 1000,
            },
            AIResponse {
                content: "Response from Claude".to_string(),
                model: "claude-3-opus".to_string(),
                tokens_used: 150,
                latency_ms: 500,
            }
        ))
    }
    
    fn id(&self) -> &'static str {
        "claude-api"
    }
}

#[tokio::main]
async fn main() {
    let core = EternalCore::new("./ai_router_data");
    println!("AI Router operational!");
}`,
      cursorPrompt: `"Build an AI model router that intelligently distributes requests across Claude, GPT-4, and local models. Include load balancing, response caching, failover, and cost optimization based on token usage."`
    }
  ];

  return (
    <div className="min-h-screen bg-gradient-to-br from-slate-900 via-indigo-900 to-slate-900 text-white p-8">
      <div className="max-w-7xl mx-auto">
        {/* Header */}
        <div className="text-center mb-12">
          <div className="flex items-center justify-center gap-3 mb-4">
            <Rocket className="w-16 h-16 text-orange-400" />
            <h1 className="text-6xl font-bold bg-gradient-to-r from-orange-400 via-pink-500 to-purple-600 bg-clip-text text-transparent">
              Quick Start Guide
            </h1>
          </div>
          <p className="text-xl text-gray-300 mb-2">Get Eternal Core running in 5 minutes</p>
          <p className="text-gray-400">Then explore 5 production-ready use cases</p>
        </div>

        {/* Quick Start Steps */}
        <div className="bg-gradient-to-r from-blue-500/10 to-purple-500/10 border border-blue-500/30 rounded-xl p-8 mb-12">
          <h2 className="text-3xl font-bold mb-6 flex items-center gap-2">
            <Zap className="w-8 h-8 text-yellow-400" />
            5-Minute Setup
          </h2>
          
          <div className="space-y-4">
            {quickStartSteps.map((step, index) => (
              <div key={index} className="bg-slate-800/50 backdrop-blur border border-slate-700 rounded-lg p-5 hover:border-blue-500/50 transition-colors">
                <div className="flex items-start gap-4">
                  <div className="bg-blue-500 text-white rounded-full w-10 h-10 flex items-center justify-center flex-shrink-0 font-bold text-lg">
                    {index + 1}
                  </div>
                  <div className="flex-1">
                    <h3 className="text-xl font-semibold mb-2">{step.title}</h3>
                    <p className="text-gray-400 text-sm mb-3">{step.description}</p>
                    <div className="bg-slate-900/70 rounded-lg p-4 relative">
                      <pre className="text-sm text-green-400 overflow-x-auto">
                        <code>{step.command}</code>
                      </pre>
                      <button
                        onClick={() => copyToClipboard(step.command, `step-${index}`)}
                        className="absolute top-2 right-2 p-2 bg-slate-700 hover:bg-slate-600 rounded transition-colors"
                      >
                        {copiedId === `step-${index}` ? (
                          <Check className="w-4 h-4 text-green-400" />
                        ) : (
                          <Copy className="w-4 h-4" />
                        )}
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            ))}
          </div>

          <div className="mt-6 bg-green-500/10 border border-green-500/30 rounded-lg p-4">
            <p className="text-green-400 font-semibold">✅ That's it! You now have a working Eternal Core setup.</p>
            <p className="text-gray-300 text-sm mt-1">Now explore the use cases below to see what you can build.</p>
          </div>
        </div>

        {/* Use Cases */}
        <div className="mb-12">
          <h2 className="text-4xl font-bold mb-8 text-center">
            5 Production Use Cases
          </h2>

          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 mb-8">
            {useCases.map((useCase) => {
              const Icon = useCase.icon;
              const isActive = activeUseCase === useCase.id;
              const colorClass = `text-${useCase.color}-400`;
              const bgColorClass = `bg-${useCase.color}-500/20`;
              const borderColorClass = isActive ? `border-${useCase.color}-500` : 'border-slate-700';
              
              return (
                <div
                  key={useCase.id}
                  onClick={() => setActiveUseCase(isActive ? null : useCase.id)}
                  className={`bg-slate-800/50 backdrop-blur border ${borderColorClass} rounded-xl p-6 cursor-pointer transition-all hover:scale-105 hover:border-slate-600`}
                >
                  <div className={`w-12 h-12 rounded-lg ${bgColorClass} flex items-center justify-center mb-4`}>
                    <Icon className={`w-6 h-6 ${colorClass}`} />
                  </div>
                  
                  <h3 className="text-xl font-bold mb-2">{useCase.title}</h3>
                  <p className="text-gray-400 text-sm mb-4">{useCase.description}</p>
                  
                  <div className="flex items-center justify-between text-xs">
                    <span className={`px-2 py-1 rounded ${bgColorClass} ${colorClass}`}>
                      {useCase.complexity}
                    </span>
                    <span className="text-gray-500">⏱️ {useCase.timeToImplement}</span>
                  </div>
                  
                  <div className="mt-4 flex items-center gap-2 text-sm text-blue-400">
                    <span>View code</span>
                    <ChevronRight className={`w-4 h-4 transition-transform ${isActive ? 'rotate-90' : ''}`} />
                  </div>
                </div>
              );
            })}
          </div>

          {/* Active Use Case Detail */}
          {activeUseCase && (
            <div className="bg-slate-800/50 backdrop-blur border border-slate-700 rounded-xl overflow-hidden">
              {useCases.filter(uc => uc.id === activeUseCase).map((useCase) => {
                const Icon = useCase.icon;
                const colorClass = `text-${useCase.color}-400`;
                const bgColorClass = `bg-${useCase.color}-500/20`;
                
                return (
                  <div key={useCase.id}>
                    <div className={`bg-gradient-to-r from-${useCase.color}-500/20 to-transparent p-6 border-b border-slate-700`}>
                      <div className="flex items-start gap-4">
                        <div className={`w-16 h-16 rounded-xl ${bgColorClass} flex items-center justify-center flex-shrink-0`}>
                          <Icon className={`w-8 h-8 ${colorClass}`} />
                        </div>
                        <div className="flex-1">
                          <h3 className="text-2xl font-bold mb-2">{useCase.title}</h3>
                          <p className="text-gray-300 mb-4">{useCase.description}</p>
                          <div className="flex gap-3">
                            <span className={`px-3 py-1 rounded-full ${bgColorClass} ${colorClass} text-sm`}>
                              {useCase.complexity}
                            </span>
                            <span className="px-3 py-1 rounded-full bg-slate-700 text-gray-300 text-sm">
                              ⏱️ {useCase.timeToImplement}
                            </span>
                          </div>
                        </div>
                      </div>
                    </div>

                    <div className="p-6">
                      <h4 className="text-lg font-semibold mb-3">Complete Implementation</h4>
                      <div className="bg-slate-900/70 rounded-lg p-4 relative mb-6">
                        <pre className="text-sm text-gray-300 overflow-x-auto max-h-96">
                          <code>{useCase.code}</code>
                        </pre>
                        <button
                          onClick={() => copyToClipboard(useCase.code, useCase.id)}
                          className="absolute top-2 right-2 px-4 py-2 bg-slate-700 hover:bg-slate-600 rounded-lg flex items-center gap-2 transition-colors"
                        >
                          {copiedId === useCase.id ? (
                            <>
                              <Check className="w-4 h-4 text-green-400" />
                              <span className="text-green-400">Copied!</span>
                            </>
                          ) : (
                            <>
                              <Copy className="w-4 h-4" />
                              <span>Copy Code</span>
                            </>
                          )}
                        </button>
                      </div>

                      <h4 className="text-lg font-semibold mb-3">Cursor AI Prompt</h4>
                      <div className="bg-blue-500/10 border border-blue-500/30 rounded-lg p-4 relative">
                        <p className="text-blue-300 text-sm font-mono pr-12">{useCase.cursorPrompt}</p>
                        <button
                          onClick={() => copyToClipboard(useCase.cursorPrompt, `${useCase.id}-prompt`)}
                          className="absolute top-2 right-2 p-2 bg-blue-700 hover:bg-blue-600 rounded transition-colors"
                        >
                          {copiedId === `${useCase.id}-prompt` ? (
                            <Check className="w-4 h-4 text-green-400" />
                          ) : (
                            <Copy className="w-4 h-4" />
                          )}
                        </button>
                      </div>
                    </div>
                  </div>
                );
              })}
            </div>
          )}
        </div>

        {/* What Makes These Eternal */}
        <div className="bg-gradient-to-r from-orange-500/10 to-pink-500/10 border border-orange-500/30 rounded-xl p-8 mb-12">
          <h2 className="text-3xl font-bold mb-6">🔥 What Makes These Use Cases Eternal?</h2>
          
          <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div className="bg-slate-800/50 rounded-lg p-5">
              <h3 className="font-bold text-xl mb-3 text-orange-400">No Framework Lock-in</h3>
              <p className="text-gray-300 text-sm">
                Each connector uses the same Connector trait. 
                Switch from HTTP to gRPC to quantum channels without rewriting your core logic.
              </p>
            </div>
            
            <div className="bg-slate-800/50 rounded-lg p-5">
              <h3 className="font-bold text-xl mb-3 text-pink-400">Protocol Agnostic</h3>
              <p className="text-gray-300 text-sm">
                Today it's MQTT and WebSocket. Tomorrow it's neural interfaces and quantum networks. 
                The same binary handles it all.
              </p>
            </div>
            
            <div className="bg-slate-800/50 rounded-lg p-5">
              <h3 className="font-bold text-xl mb-3 text-purple-400">Future-Proof Storage</h3>
              <p className="text-gray-300 text-sm">
                The Memory system uses generic serialization. Your data format can evolve 
                without migrations or breaking changes.
              </p>
            </div>
            
            <div className="bg-slate-800/50 rounded-lg p-5">
              <h3 className="font-bold text-xl mb-3 text-blue-400">Composable Forever</h3>
              <p className="text-gray-300 text-sm">
                Mix and match connectors like LEGO blocks. The use cases above can be combined 
                into hybrid architectures without modification.
              </p>
            </div>
          </div>
        </div>

        {/* Quick Command Reference */}
        <div className="bg-slate-800/50 backdrop-blur border border-slate-700 rounded-xl p-8 mb-12">
          <h2 className="text-3xl font-bold mb-6">⚡ Quick Command Reference</h2>
          
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <h3 className="font-semibold text-lg mb-3 text-green-400">Development</h3>
              <div className="space-y-2 text-sm font-mono">
                <div className="bg-slate-900/70 rounded p-3 flex items-center justify-between">
                  <code className="text-gray-300">cargo build</code>
                  <span className="text-gray-500">Build project</span>
                </div>
                <div className="bg-slate-900/70 rounded p-3 flex items-center justify-between">
                  <code className="text-gray-300">cargo test</code>
                  <span className="text-gray-500">Run tests</span>
                </div>
                <div className="bg-slate-900/70 rounded p-3 flex items-center justify-between">
                  <code className="text-gray-300">cargo run --example ai_memory</code>
                  <span className="text-gray-500">Run example</span>
                </div>
                <div className="bg-slate-900/70 rounded p-3 flex items-center justify-between">
                  <code className="text-gray-300">cargo doc --open</code>
                  <span className="text-gray-500">View docs</span>
                </div>
              </div>
            </div>
            
            <div>
              <h3 className="font-semibold text-lg mb-3 text-blue-400">Production</h3>
              <div className="space-y-2 text-sm font-mono">
                <div className="bg-slate-900/70 rounded p-3 flex items-center justify-between">
                  <code className="text-gray-300">cargo build --release</code>
                  <span className="text-gray-500">Optimized build</span>
                </div>
                <div className="bg-slate-900/70 rounded p-3 flex items-center justify-between">
                  <code className="text-gray-300">cargo clippy -- -D warnings</code>
                  <span className="text-gray-500">Lint code</span>
                </div>
                <div className="bg-slate-900/70 rounded p-3 flex items-center justify-between">
                  <code className="text-gray-300">cargo bench</code>
                  <span className="text-gray-500">Benchmark</span>
                </div>
                <div className="bg-slate-900/70 rounded p-3 flex items-center justify-between">
                  <code className="text-gray-300">cargo audit</code>
                  <span className="text-gray-500">Security check</span>
                </div>
              </div>
            </div>
          </div>
        </div>

        {/* Next Steps */}
        <div className="bg-gradient-to-r from-purple-500/10 to-blue-500/10 border border-purple-500/30 rounded-xl p-8">
          <h2 className="text-3xl font-bold mb-6">🚀 Next Steps</h2>
          
          <div className="space-y-4">
            <div className="flex items-start gap-4">
              <div className="bg-purple-500 text-white rounded-full w-8 h-8 flex items-center justify-center flex-shrink-0 font-bold">1</div>
              <div>
                <h3 className="font-bold text-lg mb-1">Choose Your Use Case</h3>
                <p className="text-gray-400 text-sm">Pick one of the 5 examples above that matches your needs</p>
              </div>
            </div>
            
            <div className="flex items-start gap-4">
              <div className="bg-purple-500 text-white rounded-full w-8 h-8 flex items-center justify-center flex-shrink-0 font-bold">2</div>
              <div>
                <h3 className="font-bold text-lg mb-1">Copy the Code</h3>
                <p className="text-gray-400 text-sm">Paste the implementation into your project</p>
              </div>
            </div>
            
            <div className="flex items-start gap-4">
              <div className="bg-purple-500 text-white rounded-full w-8 h-8 flex items-center justify-center flex-shrink-0 font-bold">3</div>
              <div>
                <h3 className="font-bold text-lg mb-1">Use Cursor AI to Extend</h3>
                <p className="text-gray-400 text-sm">Copy the Cursor AI Prompt and ask for enhancements</p>
              </div>
            </div>
            
            <div className="flex items-start gap-4">
              <div className="bg-purple-500 text-white rounded-full w-8 h-8 flex items-center justify-center flex-shrink-0 font-bold">4</div>
              <div>
                <h3 className="font-bold text-lg mb-1">Mix and Match</h3>
                <p className="text-gray-400 text-sm">Combine connectors from different use cases to build hybrid systems</p>
              </div>
            </div>
            
            <div className="flex items-start gap-4">
              <div className="bg-purple-500 text-white rounded-full w-8 h-8 flex items-center justify-center flex-shrink-0 font-bold">5</div>
              <div>
                <h3 className="font-bold text-lg mb-1">Deploy to Production</h3>
                <p className="text-gray-400 text-sm">Build with release flag for optimized binary</p>
              </div>
            </div>
          </div>

          <div className="mt-8 bg-blue-500/10 border border-blue-500/30 rounded-lg p-4">
            <p className="text-blue-300 font-semibold mb-2">💡 Pro Tip</p>
            <p className="text-gray-300 text-sm">
              Start with the Persistent AI Memory System (simplest) to understand the architecture, 
              then move to more complex use cases. Each builds on the same core principles.
            </p>
          </div>
        </div>

        {/* Footer */}
        <div className="mt-12 text-center">
          <div className="inline-block bg-gradient-to-r from-orange-500/20 to-pink-500/20 border border-orange-500/30 rounded-full px-8 py-4">
            <p className="text-2xl font-bold bg-gradient-to-r from-orange-400 to-pink-600 bg-clip-text text-transparent mb-2">
              One Binary. Forever.
            </p>
            <p className="text-gray-400 text-sm">
              Phoenix • Jamey • ORCH • All from one eternal flame 🔥
            </p>
          </div>
        </div>
      </div>
    </div>
  );
};

export default QuickStartGuide;
