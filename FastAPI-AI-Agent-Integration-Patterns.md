# FastAPI Integration Patterns for AI Agents - Comprehensive Research

## Executive Summary

This document provides a comprehensive overview of best practices, patterns, and implementation strategies for integrating AI agents with FastAPI. Based on research from 2025 sources, it covers API design patterns, streaming approaches, async operations, error handling, authentication, and performance optimization.

---

## 1. Best Practices for FastAPI + AI Agent Integration

### 1.1 Core Integration Approaches

#### FastAPI-MCP (Model Context Protocol)
- **FastAPI-MCP** is an open-source library for connecting FastAPI applications with AI agents through the Model Context Protocol
- Zero-configuration setup that automatically exposes API endpoints as MCP-compatible tools
- Provides machine-readable manifest describing each endpoint's name, purpose, inputs, and outputs
- Ideal for making your FastAPI services discoverable and usable by AI agents

#### FastAPI + LangGraph
- Combines LangGraph's stateful agent graphs with FastAPI's high-performance backend
- Enables building AI workflows with planning, integration, and deployment capabilities
- Recommended stack: Docker for containerization, Uvicorn + Gunicorn for scaling
- Deploy to AWS, Railway, Render with observability via LangSmith and Prometheus

#### FastAPI + Pydantic AI
- Natural integration due to shared Pydantic foundation
- Both frameworks leverage async/await for performance
- Emphasize type safety and validation
- Official examples and production-ready templates available

### 1.2 Architecture Best Practices

**Documentation & API Design**
- Good `operation_id` and summary fields boost agent reasoning capabilities
- Use clear, descriptive endpoint names that indicate their purpose
- Leverage OpenAPI/Swagger documentation for agent discoverability

**Security Implementation**
- Apply FastAPI dependencies for API keys and OAuth2
- Implement least privilege access for agents
- Use rate limiting with libraries like `slowapi`
- Implement audit logging and monitoring via Prometheus, OpenTelemetry, or Sentry

**Code Organization**
- Use async routes for I/O-bound services
- Schema everything with Pydantic v2
- Separate prompt logic from route logic
- Implement dependency injection for services and agents
- Use structured logging for debugging agent behavior

**Performance Optimization**
- Cache embeddings and search queries with Redis
- Implement connection pooling for database and external API calls
- Use async context managers for resource management
- Stream responses early using SSE/WebSockets for partial results
- Automate evaluations with synthetic test cases

### 1.3 Production-Ready Features

Key features to implement:
- Streaming and non-streaming endpoints
- Token-based and message-based streaming
- Multiple agent support with URL path routing
- Asynchronous design using async/await throughout
- Content moderation integration (e.g., LlamaGuard)
- Background task processing for long-running operations
- Proper error handling with retry mechanisms

---

## 2. API Endpoint Design Patterns

### 2.1 REST API Pattern

**When to Use:**
- Stateless operations
- Standard CRUD operations
- When you don't need real-time bidirectional communication
- Client-to-server requests in combination with SSE for responses

**Advantages:**
- Simple to implement and debug
- Wide client support
- Stateless and cacheable
- Well-understood HTTP semantics

**Disadvantages:**
- Less efficient for real-time updates
- Higher latency for rapid back-and-forth interactions
- Not ideal for streaming responses (though possible)

**Example Use Cases:**
- Single-turn AI queries
- Batch processing requests
- File uploads for AI analysis
- Configuration management

### 2.2 Server-Sent Events (SSE) Pattern

**When to Use:**
- Streaming AI responses (token-by-token)
- One-way server-to-client communication
- GPT-style token streaming to UI
- Progress updates for long-running tasks

**Advantages:**
- Native browser support via EventSource API
- Simple to implement
- Automatic reconnection built-in
- Works over standard HTTP/HTTPS
- Lower overhead than WebSockets for one-way communication

**Disadvantages:**
- Only server-to-client communication
- Text-based protocol (though can use JSON)
- Some proxies may buffer events

**Recommendation:**
SSE is the best choice for frontend token streaming from AI agents. It's simple, has native browser support, and is perfect for displaying AI responses as they're generated.

**Implementation Pattern:**
```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
import asyncio

app = FastAPI()

async def event_generator():
    """Generate SSE events"""
    for i in range(10):
        # Simulate AI generating tokens
        await asyncio.sleep(0.1)
        yield f"data: {{'token': 'word_{i}', 'index': {i}}}\\n\\n"

@app.get("/stream")
async def stream_response():
    return StreamingResponse(
        event_generator(),
        media_type="text/event-stream",
        headers={
            "Cache-Control": "no-cache",
            "Connection": "keep-alive",
        }
    )
```

### 2.3 WebSocket Pattern

**When to Use:**
- Full-duplex bidirectional communication needed
- Interactive real-time applications
- Multi-turn agents with actions requiring context
- Live chat systems
- When client needs to send frequent updates

**Advantages:**
- Full bidirectional communication
- Lower latency due to persistent connection
- Less overhead for frequent messages
- Supports binary data

**Disadvantages:**
- More complex to implement
- Requires careful connection lifecycle management
- Not all proxies/load balancers support WebSockets well
- More difficult to debug than HTTP

**Recommendation:**
WebSockets are ideal for multi-turn agents where the client needs to send context, actions, or interruptions to the AI agent during processing.

**Implementation Pattern:**
```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from typing import List

app = FastAPI()

class ConnectionManager:
    def __init__(self):
        self.active_connections: List[WebSocket] = []

    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.append(websocket)

    def disconnect(self, websocket: WebSocket):
        self.active_connections.remove(websocket)

    async def send_personal_message(self, message: str, websocket: WebSocket):
        await websocket.send_text(message)

    async def broadcast(self, message: str):
        for connection in self.active_connections:
            await connection.send_text(message)

manager = ConnectionManager()

@app.websocket("/ws/{client_id}")
async def websocket_endpoint(websocket: WebSocket, client_id: int):
    await manager.connect(websocket)
    try:
        while True:
            # Receive message from client
            data = await websocket.receive_text()

            # Process with AI agent
            response = await process_with_agent(data)

            # Send response back
            await manager.send_personal_message(response, websocket)

    except WebSocketDisconnect:
        manager.disconnect(websocket)
        await manager.broadcast(f"Client #{client_id} left the chat")
```

### 2.4 Pattern Selection Guide

**Use SSE when:**
- You don't need to hear from the client very often
- Streaming AI-generated text to the UI
- Displaying progress updates
- Following KISS and YAGNI principles

**Use WebSockets when:**
- Bidirectional communication is essential
- Client needs to send frequent updates
- Interactive agents with tool calls requiring user input
- Real-time collaborative features

**Use REST when:**
- Simple request/response pattern sufficient
- Stateless operations
- Can combine with SSE for the best of both worlds (REST for client-to-server, SSE for streaming responses)

**Hybrid Approach (Recommended for many use cases):**
- REST endpoints for client-to-server requests
- SSE for streaming AI responses
- This combines simplicity with streaming capabilities

---

## 3. Request/Response Schemas for Natural Language Interactions

### 3.1 Pydantic Models for Conversational AI

Pydantic AI and FastAPI both use Pydantic for validation, making schema definition natural and type-safe.

#### Basic Message Schema
```python
from pydantic import BaseModel, Field
from typing import List, Optional, Literal
from datetime import datetime

class Message(BaseModel):
    """Represents a single message in a conversation"""
    role: Literal["user", "assistant", "system"]
    content: str
    timestamp: datetime = Field(default_factory=datetime.utcnow)
    metadata: Optional[dict] = None

class ConversationRequest(BaseModel):
    """Request schema for conversational endpoints"""
    message: str = Field(..., min_length=1, max_length=10000)
    conversation_id: Optional[str] = None
    context: Optional[dict] = None
    stream: bool = False

    class Config:
        json_schema_extra = {
            "example": {
                "message": "What are the best practices for API design?",
                "conversation_id": "conv_123456",
                "stream": True
            }
        }

class ConversationResponse(BaseModel):
    """Response schema for non-streaming responses"""
    message: str
    conversation_id: str
    metadata: dict = {}
    usage: Optional[dict] = None
```

#### Advanced Schema with Tool Calls
```python
from typing import Union, Any
from enum import Enum

class ToolCall(BaseModel):
    """Represents an AI agent tool call"""
    id: str
    name: str
    arguments: dict[str, Any]

class MessageContent(BaseModel):
    """Message content that can include text and tool calls"""
    text: Optional[str] = None
    tool_calls: Optional[List[ToolCall]] = None

class AgentMessage(BaseModel):
    """Enhanced message supporting tool calls"""
    role: Literal["user", "assistant", "system", "tool"]
    content: Union[str, MessageContent]
    tool_call_id: Optional[str] = None

class AgentRequest(BaseModel):
    """Request for AI agent with tool support"""
    messages: List[AgentMessage]
    max_tokens: int = Field(default=1000, ge=1, le=4000)
    temperature: float = Field(default=0.7, ge=0, le=2)
    tools: Optional[List[dict]] = None
    stream: bool = False
```

#### Streaming Event Schemas
```python
class StreamEventType(str, Enum):
    """Types of streaming events"""
    MESSAGE_START = "message_start"
    CONTENT_DELTA = "content_delta"
    TOOL_CALL = "tool_call"
    MESSAGE_END = "message_end"
    ERROR = "error"

class StreamEvent(BaseModel):
    """Base streaming event"""
    event: StreamEventType
    data: dict

class ContentDelta(BaseModel):
    """Incremental content update"""
    delta: str
    index: int

class ToolCallEvent(BaseModel):
    """Tool call event"""
    tool_name: str
    arguments: dict
    status: Literal["started", "completed", "failed"]
```

### 3.2 Conversation History Management

```python
from typing import List
from pydantic import BaseModel

class ConversationThread(BaseModel):
    """Represents a conversation thread with history"""
    id: str
    messages: List[Message]
    created_at: datetime
    updated_at: datetime
    metadata: dict = {}

class ConversationStore:
    """Simple in-memory conversation store (use Redis/DB in production)"""
    def __init__(self):
        self.conversations: dict[str, ConversationThread] = {}

    def get_thread(self, conversation_id: str) -> Optional[ConversationThread]:
        return self.conversations.get(conversation_id)

    def add_message(self, conversation_id: str, message: Message):
        if conversation_id not in self.conversations:
            self.conversations[conversation_id] = ConversationThread(
                id=conversation_id,
                messages=[],
                created_at=datetime.utcnow(),
                updated_at=datetime.utcnow()
            )

        thread = self.conversations[conversation_id]
        thread.messages.append(message)
        thread.updated_at = datetime.utcnow()
```

### 3.3 Pydantic AI Integration Schemas

```python
from pydantic_ai import Agent, ModelMessage

# Define structured output
class SupportOutput(BaseModel):
    """Structured output from support agent"""
    response: str
    category: Literal["technical", "billing", "general"]
    confidence: float = Field(ge=0, le=1)
    requires_human: bool = False

# Create agent with typed output
support_agent = Agent(
    'openai:gpt-4',
    result_type=SupportOutput,
    system_prompt="You are a helpful support agent."
)

class AgentRunRequest(BaseModel):
    """Request to run agent"""
    prompt: str
    message_history: List[ModelMessage] = []

class AgentRunResponse(BaseModel):
    """Response from agent run"""
    output: SupportOutput
    usage: dict
    timestamp: datetime = Field(default_factory=datetime.utcnow)
```

---

## 4. Async Handling Patterns

### 4.1 Core Async Principles

FastAPI is built on async/await, making it ideal for I/O-bound operations like AI API calls.

#### When to Use Async
```python
# ✅ Use async for I/O-bound operations
async def call_openai_api(prompt: str) -> str:
    async with httpx.AsyncClient() as client:
        response = await client.post(...)
        return response.json()

# ✅ Use async for database operations
async def save_conversation(conversation: Conversation):
    async with database.session() as session:
        await session.add(conversation)
        await session.commit()

# ❌ Don't use async for CPU-bound operations
async def heavy_computation():  # Wrong!
    result = sum(range(1000000))  # Blocks event loop
    return result

# ✅ Use thread pool for CPU-bound operations
from concurrent.futures import ThreadPoolExecutor
import asyncio

executor = ThreadPoolExecutor()

async def heavy_computation():
    loop = asyncio.get_event_loop()
    result = await loop.run_in_executor(executor, sum, range(1000000))
    return result
```

### 4.2 Async Context Managers

Properly manage resources with async context managers:

```python
from contextlib import asynccontextmanager
from typing import AsyncIterator

@asynccontextmanager
async def get_agent() -> AsyncIterator[Agent]:
    """Manage agent lifecycle"""
    agent = Agent('openai:gpt-4')
    try:
        # Initialize resources
        await agent.initialize()
        yield agent
    finally:
        # Cleanup
        await agent.cleanup()

# Usage in endpoint
@app.post("/chat")
async def chat_endpoint(request: ChatRequest):
    async with get_agent() as agent:
        result = await agent.run(request.message)
        return result
```

### 4.3 Streaming with Async Generators

```python
from typing import AsyncGenerator

async def stream_ai_response(prompt: str) -> AsyncGenerator[str, None]:
    """Stream AI response token by token"""
    async with get_agent() as agent:
        async with agent.run_stream(prompt) as stream:
            async for chunk in stream.stream_text():
                yield f"data: {json.dumps({'token': chunk})}\\n\\n"

@app.post("/stream-chat")
async def stream_chat(request: ChatRequest):
    return StreamingResponse(
        stream_ai_response(request.message),
        media_type="text/event-stream"
    )
```

### 4.4 Concurrent Operations

Use `asyncio.gather()` for parallel operations:

```python
import asyncio

async def process_with_multiple_agents(prompt: str):
    """Run multiple agents concurrently"""
    results = await asyncio.gather(
        agent1.run(prompt),
        agent2.run(prompt),
        agent3.run(prompt),
        return_exceptions=True  # Don't fail all if one fails
    )

    # Filter out errors and return successful results
    successful = [r for r in results if not isinstance(r, Exception)]
    return successful

@app.post("/multi-agent")
async def multi_agent_endpoint(request: ChatRequest):
    results = await process_with_multiple_agents(request.message)
    return {"results": results}
```

### 4.5 Background Tasks

For operations that can complete after the response:

```python
from fastapi import BackgroundTasks

async def log_interaction(conversation_id: str, message: str, response: str):
    """Log interaction to analytics system"""
    async with httpx.AsyncClient() as client:
        await client.post(
            "https://analytics.example.com/log",
            json={
                "conversation_id": conversation_id,
                "message": message,
                "response": response
            }
        )

@app.post("/chat")
async def chat(
    request: ChatRequest,
    background_tasks: BackgroundTasks
):
    response = await agent.run(request.message)

    # Log in background after response is sent
    background_tasks.add_task(
        log_interaction,
        request.conversation_id,
        request.message,
        response
    )

    return {"response": response}
```

### 4.6 Timeout and Error Handling

```python
import asyncio
from contextlib import asynccontextmanager

@asynccontextmanager
async def timeout_context(seconds: float):
    """Context manager for timeouts"""
    try:
        async with asyncio.timeout(seconds):
            yield
    except asyncio.TimeoutError:
        raise HTTPException(
            status_code=408,
            detail=f"Request timed out after {seconds} seconds"
        )

@app.post("/chat")
async def chat_with_timeout(request: ChatRequest):
    async with timeout_context(30.0):  # 30 second timeout
        result = await agent.run(request.message)
        return result
```

### 4.7 Connection Pooling

```python
import asyncpg
from typing import Optional

class Database:
    def __init__(self):
        self.pool: Optional[asyncpg.Pool] = None

    async def connect(self):
        """Create connection pool"""
        self.pool = await asyncpg.create_pool(
            host='localhost',
            database='ai_chat',
            min_size=5,
            max_size=25,
            max_queries=50000,
            max_inactive_connection_lifetime=3600
        )

    async def disconnect(self):
        """Close pool"""
        if self.pool:
            await self.pool.close()

    async def save_conversation(self, conversation: Conversation):
        """Save using pooled connection"""
        async with self.pool.acquire() as conn:
            await conn.execute(
                "INSERT INTO conversations VALUES ($1, $2)",
                conversation.id,
                conversation.messages
            )

# Initialize with lifespan
@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    db = Database()
    await db.connect()
    app.state.db = db
    yield
    # Shutdown
    await db.disconnect()

app = FastAPI(lifespan=lifespan)
```

---

## 5. Code Examples Demonstrating Integration

### 5.1 Complete Pydantic AI + FastAPI Example

```python
from fastapi import FastAPI, HTTPException
from fastapi.responses import StreamingResponse
from pydantic import BaseModel, Field
from pydantic_ai import Agent, ModelMessage
from typing import List, Optional
import json

app = FastAPI(title="AI Agent API")

# Initialize agent
agent = Agent(
    'openai:gpt-4',
    system_prompt="You are a helpful AI assistant."
)

# Request/Response Models
class ChatMessage(BaseModel):
    role: str
    content: str

class ChatRequest(BaseModel):
    message: str
    conversation_history: List[ChatMessage] = []
    stream: bool = False

class ChatResponse(BaseModel):
    response: str
    conversation_id: str

# Non-streaming endpoint
@app.post("/chat", response_model=ChatResponse)
async def chat(request: ChatRequest):
    """Non-streaming chat endpoint"""
    try:
        # Convert to ModelMessage format
        history = [
            ModelMessage(role=msg.role, content=msg.content)
            for msg in request.conversation_history
        ]

        # Run agent
        result = await agent.run(
            request.message,
            message_history=history
        )

        return ChatResponse(
            response=result.data,
            conversation_id="conv_123"  # Generate proper ID in production
        )
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

# Streaming endpoint
@app.post("/chat/stream")
async def chat_stream(request: ChatRequest):
    """Streaming chat endpoint using SSE"""

    async def event_stream():
        try:
            history = [
                ModelMessage(role=msg.role, content=msg.content)
                for msg in request.conversation_history
            ]

            # Stream response
            async with agent.run_stream(
                request.message,
                message_history=history
            ) as stream:
                async for chunk in stream.stream_text():
                    yield f"data: {json.dumps({'token': chunk})}\\n\\n"

                # Send completion event
                yield f"data: {json.dumps({'done': True})}\\n\\n"

        except Exception as e:
            error_event = json.dumps({
                'error': str(e),
                'type': type(e).__name__
            })
            yield f"data: {error_event}\\n\\n"

    return StreamingResponse(
        event_stream(),
        media_type="text/event-stream",
        headers={
            "Cache-Control": "no-cache",
            "Connection": "keep-alive",
        }
    )
```

### 5.2 WebSocket Chat with Agent

```python
from fastapi import WebSocket, WebSocketDisconnect
import json
from typing import Dict

class ChatManager:
    def __init__(self):
        self.active_connections: Dict[str, WebSocket] = {}
        self.conversation_history: Dict[str, List[ModelMessage]] = {}

    async def connect(self, client_id: str, websocket: WebSocket):
        await websocket.accept()
        self.active_connections[client_id] = websocket
        self.conversation_history[client_id] = []

    def disconnect(self, client_id: str):
        if client_id in self.active_connections:
            del self.active_connections[client_id]
        if client_id in self.conversation_history:
            del self.conversation_history[client_id]

    async def process_message(self, client_id: str, message: str):
        """Process message with AI agent"""
        websocket = self.active_connections[client_id]
        history = self.conversation_history[client_id]

        # Add user message to history
        history.append(ModelMessage(role="user", content=message))

        # Stream agent response
        response_text = ""
        async with agent.run_stream(message, message_history=history) as stream:
            async for chunk in stream.stream_text():
                response_text += chunk
                await websocket.send_json({
                    "type": "token",
                    "content": chunk
                })

        # Add assistant response to history
        history.append(ModelMessage(role="assistant", content=response_text))

        # Send completion
        await websocket.send_json({
            "type": "complete",
            "full_response": response_text
        })

manager = ChatManager()

@app.websocket("/ws/{client_id}")
async def websocket_chat(websocket: WebSocket, client_id: str):
    await manager.connect(client_id, websocket)
    try:
        while True:
            # Receive message
            data = await websocket.receive_text()
            message_data = json.loads(data)

            # Process with agent
            await manager.process_message(
                client_id,
                message_data["message"]
            )

    except WebSocketDisconnect:
        manager.disconnect(client_id)
```

### 5.3 Multi-Agent System with FastAPI

```python
from typing import Dict, Any
from enum import Enum

class AgentType(str, Enum):
    SUPPORT = "support"
    TECHNICAL = "technical"
    SALES = "sales"

# Define specialized agents
support_agent = Agent(
    'openai:gpt-4',
    system_prompt="You are a customer support agent."
)

technical_agent = Agent(
    'openai:gpt-4',
    system_prompt="You are a technical support specialist."
)

sales_agent = Agent(
    'openai:gpt-4',
    system_prompt="You are a sales representative."
)

AGENTS = {
    AgentType.SUPPORT: support_agent,
    AgentType.TECHNICAL: technical_agent,
    AgentType.SALES: sales_agent,
}

class MultiAgentRequest(BaseModel):
    message: str
    agent_type: AgentType
    context: Optional[Dict[str, Any]] = None

@app.post("/multi-agent/chat")
async def multi_agent_chat(request: MultiAgentRequest):
    """Route to appropriate agent based on type"""
    agent = AGENTS[request.agent_type]

    # Add context to prompt if provided
    prompt = request.message
    if request.context:
        prompt = f"Context: {json.dumps(request.context)}\\n\\n{prompt}"

    result = await agent.run(prompt)

    return {
        "response": result.data,
        "agent_type": request.agent_type,
        "metadata": result.usage
    }

# Agent routing based on content
async def route_to_agent(message: str) -> Agent:
    """Automatically route to appropriate agent"""
    # Simple keyword-based routing (use ML classifier in production)
    message_lower = message.lower()

    if any(word in message_lower for word in ["bug", "error", "not working"]):
        return technical_agent
    elif any(word in message_lower for word in ["price", "buy", "purchase"]):
        return sales_agent
    else:
        return support_agent

@app.post("/auto-route/chat")
async def auto_route_chat(request: ChatRequest):
    """Automatically route to best agent"""
    agent = await route_to_agent(request.message)
    result = await agent.run(request.message)

    return {
        "response": result.data,
        "routed_to": agent.system_prompt
    }
```

### 5.4 Error Handling and Retry Logic

```python
from tenacity import (
    retry,
    stop_after_attempt,
    wait_exponential,
    retry_if_exception_type
)
import httpx

class AIServiceError(Exception):
    """Custom exception for AI service errors"""
    pass

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=4, max=10),
    retry=retry_if_exception_type((httpx.HTTPError, AIServiceError))
)
async def call_agent_with_retry(message: str) -> str:
    """Call agent with automatic retry logic"""
    try:
        result = await agent.run(message)
        return result.data
    except httpx.HTTPError as e:
        # Log and retry
        print(f"HTTP error: {e}")
        raise
    except Exception as e:
        # Convert to custom exception for retry
        print(f"AI service error: {e}")
        raise AIServiceError(str(e))

@app.post("/chat/reliable")
async def chat_with_retry(request: ChatRequest):
    """Chat endpoint with retry logic"""
    try:
        response = await call_agent_with_retry(request.message)
        return {"response": response}
    except Exception as e:
        raise HTTPException(
            status_code=503,
            detail=f"Service temporarily unavailable: {str(e)}"
        )

# Global exception handler
@app.exception_handler(Exception)
async def global_exception_handler(request, exc):
    """Handle all unhandled exceptions"""
    return JSONResponse(
        status_code=500,
        content={
            "error": "Internal server error",
            "type": type(exc).__name__,
            "detail": str(exc) if app.debug else "An error occurred"
        }
    )
```

---

## 6. Performance Considerations

### 6.1 Connection Pooling

**Database Connection Pooling**
```python
# Optimized asyncpg pool settings
pool = await asyncpg.create_pool(
    host='localhost',
    database='ai_chat',
    min_size=5,          # Minimum connections
    max_size=25,         # Maximum connections
    max_queries=50000,   # Queries per connection
    max_inactive_connection_lifetime=3600  # 1 hour
)

# MySQL with aiomysql (async)
import aiomysql

pool = await aiomysql.create_pool(
    host='localhost',
    port=3306,
    user='user',
    password='password',
    db='ai_chat',
    minsize=5,
    maxsize=25
)
```

**HTTP Client Pooling**
```python
import httpx
from contextlib import asynccontextmanager

class HTTPClientManager:
    def __init__(self):
        self.client: Optional[httpx.AsyncClient] = None

    async def start(self):
        """Initialize HTTP client with connection pooling"""
        self.client = httpx.AsyncClient(
            limits=httpx.Limits(
                max_keepalive_connections=20,
                max_connections=100,
                keepalive_expiry=30.0
            ),
            timeout=httpx.Timeout(30.0)
        )

    async def stop(self):
        """Cleanup"""
        if self.client:
            await self.client.aclose()

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    http_manager = HTTPClientManager()
    await http_manager.start()
    app.state.http_client = http_manager.client
    yield
    # Shutdown
    await http_manager.stop()

app = FastAPI(lifespan=lifespan)

# Use in endpoints
@app.post("/chat")
async def chat(request: ChatRequest):
    # Reuse pooled client
    response = await app.state.http_client.post(
        "https://api.openai.com/v1/chat/completions",
        json={"messages": [{"role": "user", "content": request.message}]}
    )
    return response.json()
```

### 6.2 Caching Strategies

**Multi-Level Caching**
```python
from functools import lru_cache
import redis.asyncio as aioredis
import json
from typing import Optional

class CacheManager:
    def __init__(self):
        self.redis: Optional[aioredis.Redis] = None
        self.local_cache = {}  # In-memory cache

    async def connect(self):
        """Connect to Redis"""
        self.redis = await aioredis.from_url(
            "redis://localhost",
            encoding="utf-8",
            decode_responses=True
        )

    async def get(self, key: str) -> Optional[str]:
        """Get from cache with fallback"""
        # Try local cache first
        if key in self.local_cache:
            return self.local_cache[key]

        # Try Redis
        if self.redis:
            value = await self.redis.get(key)
            if value:
                # Populate local cache
                self.local_cache[key] = value
                return value

        return None

    async def set(self, key: str, value: str, ttl: int = 3600):
        """Set in both caches"""
        self.local_cache[key] = value
        if self.redis:
            await self.redis.setex(key, ttl, value)

cache = CacheManager()

@app.post("/chat/cached")
async def cached_chat(request: ChatRequest):
    """Chat with response caching"""
    # Create cache key
    cache_key = f"chat:{hash(request.message)}"

    # Check cache
    cached = await cache.get(cache_key)
    if cached:
        return json.loads(cached)

    # Generate response
    result = await agent.run(request.message)
    response = {"response": result.data}

    # Cache result
    await cache.set(cache_key, json.dumps(response), ttl=1800)  # 30 min

    return response
```

**Embedding Cache**
```python
class EmbeddingCache:
    """Cache for expensive embedding operations"""

    def __init__(self):
        self.cache: Dict[str, List[float]] = {}

    async def get_embedding(self, text: str) -> List[float]:
        """Get embedding with caching"""
        if text in self.cache:
            return self.cache[text]

        # Compute embedding (expensive)
        embedding = await compute_embedding(text)

        # Cache it
        self.cache[text] = embedding

        return embedding

embedding_cache = EmbeddingCache()
```

### 6.3 Horizontal Scaling

**Gunicorn + Uvicorn Workers**
```bash
# Production deployment
gunicorn main:app \
    --workers 4 \
    --worker-class uvicorn.workers.UvicornWorker \
    --bind 0.0.0.0:8000 \
    --timeout 120 \
    --access-logfile - \
    --error-logfile -
```

**Worker Configuration**
```python
# Calculate workers based on CPU
import multiprocessing

workers = multiprocessing.cpu_count() * 2 + 1
worker_class = "uvicorn.workers.UvicornWorker"
worker_connections = 1000
keepalive = 5
```

### 6.4 Rate Limiting

```python
from slowapi import Limiter, _rate_limit_exceeded_handler
from slowapi.util import get_remote_address
from slowapi.errors import RateLimitExceeded

limiter = Limiter(key_func=get_remote_address)
app.state.limiter = limiter
app.add_exception_handler(RateLimitExceeded, _rate_limit_exceeded_handler)

@app.post("/chat")
@limiter.limit("10/minute")  # 10 requests per minute
async def rate_limited_chat(request: Request, chat_request: ChatRequest):
    """Chat endpoint with rate limiting"""
    result = await agent.run(chat_request.message)
    return {"response": result.data}

# Per-user rate limiting
@app.post("/chat/user")
@limiter.limit("100/hour", key_func=lambda: request.state.user_id)
async def user_rate_limited_chat(
    request: Request,
    chat_request: ChatRequest
):
    """Rate limit per authenticated user"""
    result = await agent.run(chat_request.message)
    return {"response": result.data}
```

### 6.5 Monitoring and Observability

```python
from prometheus_client import Counter, Histogram, generate_latest
import time

# Metrics
request_count = Counter(
    'ai_requests_total',
    'Total AI requests',
    ['endpoint', 'status']
)

request_duration = Histogram(
    'ai_request_duration_seconds',
    'AI request duration',
    ['endpoint']
)

@app.middleware("http")
async def metrics_middleware(request: Request, call_next):
    """Track metrics for all requests"""
    start_time = time.time()

    response = await call_next(request)

    duration = time.time() - start_time

    # Record metrics
    request_count.labels(
        endpoint=request.url.path,
        status=response.status_code
    ).inc()

    request_duration.labels(
        endpoint=request.url.path
    ).observe(duration)

    return response

@app.get("/metrics")
async def metrics():
    """Expose Prometheus metrics"""
    return Response(
        generate_latest(),
        media_type="text/plain"
    )
```

### 6.6 Resource Management

**Circuit Breaker Pattern**
```python
from datetime import datetime, timedelta

class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.failures = 0
        self.last_failure = None
        self.state = "closed"  # closed, open, half-open

    async def call(self, func, *args, **kwargs):
        """Execute function with circuit breaker"""
        if self.state == "open":
            if datetime.now() - self.last_failure > timedelta(seconds=self.timeout):
                self.state = "half-open"
            else:
                raise Exception("Circuit breaker is OPEN")

        try:
            result = await func(*args, **kwargs)
            self.on_success()
            return result
        except Exception as e:
            self.on_failure()
            raise e

    def on_success(self):
        """Reset on success"""
        self.failures = 0
        self.state = "closed"

    def on_failure(self):
        """Track failures"""
        self.failures += 1
        self.last_failure = datetime.now()

        if self.failures >= self.failure_threshold:
            self.state = "open"

# Usage
ai_circuit_breaker = CircuitBreaker(failure_threshold=5, timeout=60)

@app.post("/chat/resilient")
async def resilient_chat(request: ChatRequest):
    """Chat with circuit breaker protection"""
    try:
        result = await ai_circuit_breaker.call(
            agent.run,
            request.message
        )
        return {"response": result.data}
    except Exception as e:
        raise HTTPException(
            status_code=503,
            detail="AI service temporarily unavailable"
        )
```

---

## 7. Authentication and Authorization Patterns

### 7.1 JWT Authentication

**Basic JWT Setup**
```python
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from jose import JWTError, jwt
from datetime import datetime, timedelta
from passlib.context import CryptContext

# Configuration
SECRET_KEY = "your-secret-key-keep-it-secret"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
security = HTTPBearer()

class User(BaseModel):
    username: str
    email: str
    disabled: bool = False

class UserInDB(User):
    hashed_password: str

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password: str) -> str:
    return pwd_context.hash(password)

def create_access_token(data: dict, expires_delta: timedelta = None):
    """Create JWT access token"""
    to_encode = data.copy()

    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=15)

    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

    return encoded_jwt

async def get_current_user(
    credentials: HTTPAuthorizationCredentials = Depends(security)
) -> User:
    """Validate JWT and return current user"""
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )

    try:
        token = credentials.credentials
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")

        if username is None:
            raise credentials_exception

    except JWTError:
        raise credentials_exception

    # Get user from database
    user = await get_user(username)  # Implement this

    if user is None:
        raise credentials_exception

    return user

# Login endpoint
class LoginRequest(BaseModel):
    username: str
    password: str

@app.post("/token")
async def login(request: LoginRequest):
    """Authenticate and return JWT"""
    user = await authenticate_user(request.username, request.password)

    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
        )

    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user.username},
        expires_delta=access_token_expires
    )

    return {"access_token": access_token, "token_type": "bearer"}

# Protected endpoint
@app.post("/chat/protected")
async def protected_chat(
    request: ChatRequest,
    current_user: User = Depends(get_current_user)
):
    """Protected chat endpoint requiring authentication"""
    result = await agent.run(request.message)

    return {
        "response": result.data,
        "user": current_user.username
    }
```

### 7.2 OAuth2 with Password Flow

```python
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.post("/token")
async def login_oauth2(form_data: OAuth2PasswordRequestForm = Depends()):
    """OAuth2 password flow"""
    user = await authenticate_user(form_data.username, form_data.password)

    if not user:
        raise HTTPException(
            status_code=400,
            detail="Incorrect username or password"
        )

    access_token = create_access_token(data={"sub": user.username})

    return {
        "access_token": access_token,
        "token_type": "bearer"
    }

async def get_current_user_oauth2(token: str = Depends(oauth2_scheme)):
    """Get current user from OAuth2 token"""
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise HTTPException(status_code=401, detail="Invalid token")
    except JWTError:
        raise HTTPException(status_code=401, detail="Invalid token")

    user = await get_user(username)
    if user is None:
        raise HTTPException(status_code=401, detail="User not found")

    return user
```

### 7.3 Role-Based Access Control (RBAC)

```python
from enum import Enum
from typing import List

class Role(str, Enum):
    USER = "user"
    PREMIUM = "premium"
    ADMIN = "admin"

class UserWithRoles(User):
    roles: List[Role]

def require_roles(required_roles: List[Role]):
    """Dependency to check user roles"""
    async def role_checker(
        current_user: UserWithRoles = Depends(get_current_user)
    ):
        for role in required_roles:
            if role in current_user.roles:
                return current_user

        raise HTTPException(
            status_code=403,
            detail="Insufficient permissions"
        )

    return role_checker

# Endpoints with role requirements
@app.post("/chat/premium")
async def premium_chat(
    request: ChatRequest,
    current_user: UserWithRoles = Depends(require_roles([Role.PREMIUM, Role.ADMIN]))
):
    """Premium chat endpoint (requires premium or admin role)"""
    # Use more advanced agent for premium users
    result = await premium_agent.run(request.message)
    return {"response": result.data}

@app.post("/admin/agents")
async def manage_agents(
    current_user: UserWithRoles = Depends(require_roles([Role.ADMIN]))
):
    """Admin-only endpoint for managing agents"""
    return {"agents": list(AGENTS.keys())}
```

### 7.4 API Key Authentication

```python
from fastapi import Header, HTTPException

API_KEYS = {
    "sk_test_123": {"name": "Test App", "tier": "free"},
    "sk_prod_456": {"name": "Production App", "tier": "premium"},
}

async def verify_api_key(x_api_key: str = Header(...)):
    """Verify API key from header"""
    if x_api_key not in API_KEYS:
        raise HTTPException(
            status_code=401,
            detail="Invalid API key"
        )

    return API_KEYS[x_api_key]

@app.post("/chat/api-key")
async def api_key_chat(
    request: ChatRequest,
    api_key_info: dict = Depends(verify_api_key)
):
    """Chat endpoint using API key authentication"""
    result = await agent.run(request.message)

    return {
        "response": result.data,
        "api_key_name": api_key_info["name"],
        "tier": api_key_info["tier"]
    }
```

### 7.5 Security Best Practices

```python
from fastapi.middleware.cors import CORSMiddleware
from fastapi.middleware.trustedhost import TrustedHostMiddleware
from fastapi.middleware.httpsredirect import HTTPSRedirectMiddleware

# CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://yourdomain.com"],  # Don't use ["*"] in production
    allow_credentials=True,
    allow_methods=["GET", "POST"],
    allow_headers=["*"],
)

# HTTPS redirect (production)
app.add_middleware(HTTPSRedirectMiddleware)

# Trusted hosts
app.add_middleware(
    TrustedHostMiddleware,
    allowed_hosts=["yourdomain.com", "*.yourdomain.com"]
)

# Security headers
@app.middleware("http")
async def add_security_headers(request: Request, call_next):
    response = await call_next(request)
    response.headers["X-Content-Type-Options"] = "nosniff"
    response.headers["X-Frame-Options"] = "DENY"
    response.headers["X-XSS-Protection"] = "1; mode=block"
    response.headers["Strict-Transport-Security"] = "max-age=31536000; includeSubDomains"
    return response
```

---

## 8. Summary and Recommendations

### 8.1 Quick Decision Matrix

| Requirement | Recommended Pattern |
|-------------|-------------------|
| Simple AI query/response | REST API |
| Streaming AI responses to UI | SSE (Server-Sent Events) |
| Interactive bidirectional chat | WebSocket |
| Multi-turn conversation | SSE + REST hybrid |
| Authentication | JWT with OAuth2 |
| High throughput | Connection pooling + caching |
| Reliability | Retry logic + circuit breakers |
| Type safety | Pydantic models throughout |

### 8.2 Technology Stack Recommendations

**For Production AI Agent APIs:**
- **Framework**: FastAPI with Uvicorn/Gunicorn
- **AI Integration**: Pydantic AI or LangGraph
- **Streaming**: SSE for one-way, WebSocket for bidirectional
- **Caching**: Redis for distributed cache
- **Database**: PostgreSQL with asyncpg
- **Authentication**: JWT with OAuth2PasswordBearer
- **Monitoring**: Prometheus + Grafana
- **Rate Limiting**: slowapi or custom middleware
- **Error Handling**: Tenacity for retries

### 8.3 Implementation Checklist

- [ ] Define clear Pydantic schemas for all requests/responses
- [ ] Implement async/await throughout for I/O operations
- [ ] Add connection pooling for databases and HTTP clients
- [ ] Implement caching strategy (multi-level if needed)
- [ ] Add authentication and authorization
- [ ] Implement rate limiting
- [ ] Add retry logic with exponential backoff
- [ ] Implement circuit breakers for external services
- [ ] Add comprehensive error handling
- [ ] Set up monitoring and metrics
- [ ] Implement proper logging
- [ ] Add request/response validation
- [ ] Configure CORS appropriately
- [ ] Use HTTPS in production
- [ ] Implement graceful shutdown
- [ ] Add health check endpoints
- [ ] Document API with OpenAPI/Swagger
- [ ] Add integration and load tests
- [ ] Set up CI/CD pipeline
- [ ] Configure horizontal scaling

### 8.4 Common Pitfalls to Avoid

1. **Don't mix sync and async** - Use async throughout for I/O operations
2. **Don't block the event loop** - Use thread pools for CPU-bound tasks
3. **Don't skip connection pooling** - It's essential for performance
4. **Don't ignore timeouts** - Always set timeouts for external calls
5. **Don't skip error handling** - Handle and log all exceptions properly
6. **Don't hardcode secrets** - Use environment variables
7. **Don't use `["*"]` for CORS in production** - Specify allowed origins
8. **Don't forget rate limiting** - Protect against abuse
9. **Don't skip monitoring** - You can't fix what you can't measure
10. **Don't forget to validate inputs** - Use Pydantic models

---

## Sources and References

### FastAPI + AI Agent Integration
- [Build AI Workflows with FastAPI & LangGraph | 2025 Guide](https://www.zestminds.com/blog/build-ai-workflows-fastapi-langgraph/)
- [FastAPI-MCP: Simplifying the Integration of FastAPI with AI Agents - InfoQ](https://www.infoq.com/news/2025/04/fastapi-mcp/)
- [GitHub - FastAPI LangGraph Agent Production-Ready Template](https://github.com/wassim249/fastapi-langgraph-agent-production-ready-template)
- [FastAPI Agents](https://fastapi-agents.blairhudson.com/)
- [Building AI Agentic Systems with AG2 and FastAPI](https://pub.towardsai.net/building-ai-agentic-systems-with-ag2-and-fastapi-f1bf8f478f42)

### Pydantic AI Integration
- [Chat App with FastAPI - Pydantic AI](https://ai.pydantic.dev/examples/chat-app/)
- [Building an API for Your Pydantic-AI Agent with FastAPI (Part: 2)](https://tech.appunite.com/posts/building-an-api-for-your-pydantic-ai-agent-with-fast-api-part-2)
- [Streaming with Pydantic AI](https://datastud.dev/posts/pydantic-ai-streaming/)
- [Overview - Pydantic AI](https://ai.pydantic.dev/ui/overview/)
- [How to Stream Structured JSON from LLMs with FastAPI & PydanticAI](https://python.plainenglish.io/how-to-stream-structured-json-output-from-llms-using-fastapi-and-pydanticai-c1dacae66ca6)

### WebSocket vs SSE vs REST
- [WebSockets - FastAPI](https://fastapi.tiangolo.com/advanced/websockets/)
- [Streaming AI Responses with WebSockets, SSE, and gRPC: Which One Wins?](https://medium.com/@pranavprakash4777/streaming-ai-responses-with-websockets-sse-and-grpc-which-one-wins-a481cab403d3)
- [Comparing Real-Time Communication options for conversational LLM workflows](https://tech-depth-and-breadth.medium.com/comparing-real-time-communication-options-http-streaming-sse-or-websockets-for-conversational-74c12f0bd7bc)
- [Create a streaming AI assistant with ChatGPT, FastAPI, WebSockets and React](https://dev.to/dpills/create-a-streaming-ai-assistant-with-chatgpt-fastapi-websockets-and-react-3ehf)

### Async Patterns and Error Handling
- [FastAPI Error Handling Patterns | Better Stack Community](https://betterstack.com/community/guides/scaling-python/error-handling-fastapi/)
- [Python Retry Logic with Tenacity and Instructor](https://python.useinstructor.com/concepts/retrying/)
- [How I Handled 100K Daily Jobs in FastAPI Using Task Queues and Async Retries](https://medium.com/@connect.hashblock/how-i-handled-100k-daily-jobs-in-fastapi-using-task-queues-and-async-retries-62bbcdd8240d)
- [FastAPI Async/Await Patterns - Complete Guide](https://craftyourstartup.com/cys-docs/fastapi-async-patterns/)

### Authentication and Authorization
- [FastAPI Security Essentials: Using OAuth2 and JWT for Authentication](https://medium.com/@suganthi2496/fastapi-security-essentials-using-oauth2-and-jwt-for-authentication-7e007d9d473c)
- [Securing FastAPI with JWT Token-based Authentication](https://testdriven.io/blog/fastapi-jwt-auth/)
- [OAuth2 with Password (and hashing), Bearer with JWT tokens - FastAPI](https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt/)
- [Authentication and Authorization with FastAPI: A Complete Guide](https://betterstack.com/community/guides/scaling-python/authentication-fastapi/)
- [Bulletproof JWT Authentication in FastAPI: A Complete Guide](https://medium.com/@ancilartech/bulletproof-jwt-authentication-in-fastapi-a-complete-guide-2c5602a38b4f)

### Performance Optimization
- [Advanced Strategies for Profiling, Caching, and Optimizing FastAPI Performance](https://blog.stackademic.com/advanced-strategies-for-profiling-caching-and-optimizing-fastapi-performance-f23bb7f6dfc5)
- [FastAPI Performance Tuning: Tricks to Enhance Speed and Scalability](https://loadforge.com/guides/fastapi-performance-tuning-tricks-to-enhance-speed-and-scalability)
- [FastAPI Performance Optimization: Ultimate Guide to High-Performance APIs](https://craftyourstartup.com/cys-docs/fastapi-performance-optimization/)
- [FastAPI Performance Benchmarks: Tips to Handle 1M+ Requests](https://medium.com/@prabha.ochetty/fastapi-performance-benchmarks-tips-to-handle-1m-requests-5df42d5dbd21)

### Streaming Implementations
- [Building a Real-time Streaming API with FastAPI and OpenAI](https://medium.com/@shudongai/building-a-real-time-streaming-api-with-fastapi-and-openai-a-comprehensive-guide-cb65b3e686a5)
- [Building an OpenAI-Compatible Streaming Interface Using Server-Sent Events with FastAPI](https://medium.com/@moustafa.abdelbaky/building-an-openai-compatible-streaming-interface-using-server-sent-events-with-fastapi-and-8f014420bca7)
- [Streaming AI Agents Responses with Server-Sent Events (SSE): A Technical Case Study](https://akanuragkumar.medium.com/streaming-ai-agents-responses-with-server-sent-events-sse-a-technical-case-study-f3ac855d0755)
- [How to Build a Streaming Agent with Burr, FastAPI, and React](https://towardsdatascience.com/how-to-build-a-streaming-agent-with-burr-fastapi-and-react-e2459ef527a8/)

### WebSocket Chat Implementations
- [Building a Real-Time Chat Application with FastAPI and WebSocket](https://medium.com/@chodvadiyasaurabh/building-a-real-time-chat-application-with-fastapi-and-websocket-9965778e97be)
- [FastAPI and WebSockets — Building real-time features and notifications](https://unfoldai.com/fastapi-and-websockets/)
- [GitHub - notarious2/fastapi-chat: Real-time chat with FastAPI, Websockets and SQLAlchemy2](https://github.com/notarious2/fastapi-chat)
- [Building an AI-Powered Chat Interface Using FastAPI and Gemini](https://dev.to/muhammadnizamani/building-an-ai-powered-chat-interface-using-fastapi-and-gemini-2j14)

---

**Document Generated**: 2025-11-23
**Research Focus**: FastAPI Integration Patterns for AI Agents
**Status**: Comprehensive Research Complete
