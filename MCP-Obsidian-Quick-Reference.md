# MCP Obsidian Server - Quick Reference

## All 13 Tools at a Glance

| Tool | Category | Parameters | Use Case |
|------|----------|-----------|----------|
| `list_files_in_vault` | Discovery | None | List root directory |
| `list_files_in_dir` | Discovery | dirpath | List specific folder |
| `get_file_contents` | Read | filepath | Read single note |
| `batch_get_file_contents` | Read | filepaths[] | Read multiple notes |
| `search` | Search | query, context_length? | Simple text search |
| `complex_search` | Search | JsonLogic query | Advanced search with regex/glob |
| `append_content` | Write | filepath, content | Append to note |
| `put_content` | Write | filepath, content | Create/replace note |
| `patch_content` | Write | filepath, content, operation, target_type, target | Insert at specific location |
| `delete_file` | Delete | filepath, confirm | Delete file/folder |
| `get_periodic_note` | Specialized | period | Get current daily/weekly/monthly note |
| `get_recent_periodic_notes` | Specialized | period, include_content?, limit? | Get recent periodic notes |
| `get_recent_changes` | Metadata | days | Files modified in last N days |

## Tool Categories

### Discovery & Navigation (2)
- `list_files_in_vault` - Root directory listing
- `list_files_in_dir` - Subdirectory listing

### Read Operations (2)
- `get_file_contents` - Single file read
- `batch_get_file_contents` - Multiple file read

### Write Operations (3)
- `append_content` - Add to end
- `put_content` - Create/replace entire file
- `patch_content` - Targeted insertion (headings/blocks/frontmatter)

### Search (2)
- `search` - Simple text search
- `complex_search` - JsonLogic queries with glob/regexp

### Periodic Notes (2)
- `get_periodic_note` - Current period note
- `get_recent_periodic_notes` - Historical period notes

### Delete (1)
- `delete_file` - Remove files/folders (requires confirmation)

### Metadata (1)
- `get_recent_changes` - Recently modified files

## Key Architecture Patterns

### 1. Handler-Based Pattern
```python
class ToolHandler(ABC):
    def __init__(self, tool_name: str)
    def get_tool_description() -> Tool  # Schema
    def run_tool(args: dict) -> Sequence[TextContent]  # Execute
```

### 2. Registry Pattern
```python
tool_handlers = {}
add_tool_handler(tool_instance)
get_tool_handler(name)
```

### 3. Facade Pattern (HTTP Client)
```python
class Obsidian:
    def _safe_call(func, *args, **kwargs)  # Unified error handling
    def get_file_contents(filepath)
    def search(query, context_length)
    # ... all API methods
```

### 4. Decorator Pattern (MCP Server)
```python
@app.list_tools()
async def list_tools() -> list[Tool]

@app.call_tool()
async def call_tool(name: str, arguments: Any)
```

## Error Handling Layers

```
1. Parameter Validation (Tool Handler)
   ↓
2. API Error Handling (Client)
   ↓
3. Server Error Handling (MCP)
   ↓
4. Client Response
```

## Configuration

```python
# Required
OBSIDIAN_API_KEY=your_api_key

# Optional (with defaults)
OBSIDIAN_HOST=127.0.0.1
OBSIDIAN_PORT=27124
OBSIDIAN_PROTOCOL=https
OBSIDIAN_VERIFY_SSL=true
```

## Advanced Features

### Hierarchical Targeting (patch_content)
```python
{
  "target": "Heading 1::Subheading::Item:1",
  "target_type": "heading",
  "operation": "append"  # or prepend, replace
}
```

### JsonLogic Search
```json
{
  "and": [
    {"glob": "Projects/*.md"},
    {"regexp": "status: active"}
  ]
}
```

## REST API Endpoints

| Operation | Method | Endpoint |
|-----------|--------|----------|
| List vault | GET | `/vault/` |
| List dir | GET | `/vault/{dir}/` |
| Get file | GET | `/vault/{filepath}` |
| Append | POST | `/vault/{filepath}` |
| Replace | PUT | `/vault/{filepath}` |
| Patch | PATCH | `/vault/{filepath}` |
| Delete | DELETE | `/vault/{filepath}` |
| Search | POST | `/search/simple/` |
| Complex search | POST | `/search/` |
| Periodic note | GET | `/periodic/{period}/` |

## Common Patterns for Your Project

### 1. Tool Handler Base Class
```python
from abc import ABC, abstractmethod

class ToolHandler(ABC):
    def __init__(self, name: str):
        self.name = name

    @abstractmethod
    def get_schema(self) -> ToolSchema:
        pass

    @abstractmethod
    async def execute(self, **kwargs) -> Any:
        pass
```

### 2. Tool Registry
```python
class ToolRegistry:
    def __init__(self):
        self._tools = {}

    def register(self, tool: ToolHandler):
        self._tools[tool.name] = tool

    def get(self, name: str) -> ToolHandler | None:
        return self._tools.get(name)
```

### 3. Pydantic Settings
```python
from pydantic_settings import BaseSettings

class ObsidianSettings(BaseSettings):
    api_key: str
    host: str = "127.0.0.1"
    port: int = 27124

    model_config = SettingsConfigDict(
        env_prefix="OBSIDIAN_",
        env_file=".env"
    )
```

### 4. Error Hierarchy
```python
class ToolError(Exception):
    pass

class ValidationError(ToolError):
    pass

class ExecutionError(ToolError):
    pass
```

### 5. Async HTTP Client
```python
import httpx

class ObsidianClient:
    async def _request(self, method, endpoint, **kwargs):
        async with httpx.AsyncClient() as client:
            response = await client.request(
                method, url, headers=self.headers, **kwargs
            )
            response.raise_for_status()
            return response
```

### 6. Pydantic AI Integration
```python
from pydantic_ai import Agent, RunContext

agent = Agent(model="openai:gpt-4", deps_type=ObsidianDeps)

@agent.tool
async def search_notes(ctx: RunContext[ObsidianDeps],
                       query: str) -> dict:
    """Search for notes matching a query"""
    return await ctx.deps.client.search(query)
```

## Known Limitations

- SSL/TLS configuration limited
- No multiple vault support
- Requires Dataview plugin for some operations
- Requires Periodic Notes plugin for periodic note tools
- Fixed timeout (30s) - may fail on large vaults
- Maximum 100 results for recent changes
- Maximum 100 days lookback for recent changes

## Key Dependencies

```toml
dependencies = [
    "mcp>=1.1.0",           # MCP protocol
    "python-dotenv>=1.0.1",  # Environment variables
    "requests>=2.32.3",      # HTTP client
]
```

## Best Practices to Adopt

1. **Single Responsibility** - Each tool does one thing
2. **Clear Naming** - Descriptive tool and parameter names
3. **Validation First** - Check parameters before execution
4. **Consistent Returns** - All tools return same format
5. **Safety Checks** - Confirm destructive operations
6. **Error Context** - Include details in error messages
7. **Environment Config** - No hardcoded credentials
8. **Async Operations** - Use async/await throughout
9. **Structured Logging** - Log execution and errors
10. **Type Hints** - Type everything for clarity

## Security Checklist

- [ ] API keys in environment variables only
- [ ] HTTPS in production
- [ ] SSL verification enabled
- [ ] Input validation on all parameters
- [ ] Path sanitization to prevent traversal
- [ ] Confirmation required for delete operations
- [ ] Timeout on all HTTP requests
- [ ] Error messages don't leak sensitive data

## Testing Strategy

1. **Unit Tests** - Test each tool handler in isolation
2. **Integration Tests** - Test with real Obsidian API
3. **Error Tests** - Test validation and error handling
4. **Type Checking** - Use pyright or mypy
5. **Mock External** - Mock HTTP client in unit tests

## Quick Start Integration

```python
# 1. Install dependencies
pip install mcp python-dotenv requests httpx pydantic pydantic-ai

# 2. Create .env file
OBSIDIAN_API_KEY=your_key_here

# 3. Create settings
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    obsidian_api_key: str
    model_config = SettingsConfigDict(env_file=".env")

# 4. Create client
class ObsidianClient:
    def __init__(self, api_key: str):
        self.api_key = api_key

    async def search(self, query: str):
        # Implementation

# 5. Create agent
from pydantic_ai import Agent

agent = Agent(model="openai:gpt-4")

@agent.tool
async def search_notes(query: str) -> dict:
    client = ObsidianClient(settings.obsidian_api_key)
    return await client.search(query)

# 6. Run
result = await agent.run("Search for AI notes")
```

## Useful Links

- Repository: https://github.com/MarkusPfundstein/mcp-obsidian
- MCP Protocol: https://modelcontextprotocol.io/
- Obsidian REST API: https://github.com/coddingtonbear/obsidian-local-rest-api
- Pydantic AI: https://ai.pydantic.dev/
