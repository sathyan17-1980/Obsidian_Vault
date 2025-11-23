# MCP Obsidian Server - Comprehensive Analysis

**Repository:** https://github.com/MarkusPfundstein/mcp-obsidian
**Version:** 0.2.1
**Author:** Markus Pfundstein
**Stars:** 2.4k | **Forks:** 303 | **Contributors:** 16
**Created:** November 29, 2024

## Executive Summary

The MCP Obsidian server is a Model Context Protocol (MCP) implementation that enables AI assistants (like Claude) to interact with Obsidian vaults through the Local REST API community plugin. It provides 13 specialized tools covering file operations, content manipulation, search, and periodic note management.

---

## 1. Complete Tool Inventory

### 1.1 File Discovery Tools (3 tools)

#### `list_files_in_vault`
**Purpose:** Lists all files and directories in the root directory of the vault
**Category:** Discovery/Navigation
**Input Parameters:** None
**Output Format:** JSON array of file/directory objects
**Use Case:** Initial vault exploration, understanding root structure

```python
# Tool Handler: ListFilesInVaultToolHandler
# Returns: Sequence[TextContent] with JSON-formatted list
```

#### `list_files_in_dir`
**Purpose:** Lists contents of a specific directory
**Category:** Discovery/Navigation
**Input Parameters:**
- `dirpath` (string, required): Path to directory relative to vault root

**Output Format:** JSON array of file/directory objects
**Use Case:** Navigate subdirectories, explore folder structure
**Validation:** Raises RuntimeError if `dirpath` is missing

```python
# Example usage:
# {"dirpath": "Projects/2024"}
```

#### `batch_get_file_contents`
**Purpose:** Retrieves content from multiple files in one request
**Category:** Batch Operations
**Input Parameters:**
- `filepaths` (array of strings, required): List of file paths to retrieve

**Output Format:** Concatenated text with file headers
**Use Case:** Efficiently read multiple related notes, gather context
**Pattern:** Results formatted as:
```
===== Content of file: <filepath> =====
<file content>
===================================
```

---

### 1.2 Content Reading Tools (1 tool)

#### `get_file_contents`
**Purpose:** Retrieves content of a single file
**Category:** CRUD - Read
**Input Parameters:**
- `filepath` (string, required): Path to file relative to vault root

**Output Format:** Raw file content as text
**Use Case:** Read individual notes, inspect file contents
**Validation:** Validates `filepath` presence before execution

```python
# Example:
# {"filepath": "Daily Notes/2024-11-23.md"}
```

---

### 1.3 Content Modification Tools (3 tools)

#### `append_content`
**Purpose:** Adds content to end of existing or new file
**Category:** CRUD - Create/Update
**Input Parameters:**
- `filepath` (string, required): Target file path
- `content` (string, required): Content to append

**Output Format:** Success/failure response
**Use Case:** Add entries to logs, append to meeting notes, create new files
**Validation:** Both parameters required, raises RuntimeError if missing
**HTTP Method:** POST with markdown content-type

```python
# Example:
# {
#   "filepath": "Journal/Today.md",
#   "content": "\n## New Entry\nContent here..."
# }
```

#### `put_content`
**Purpose:** Creates new file or completely replaces existing file content
**Category:** CRUD - Create/Update (Replace)
**Input Parameters:**
- `filepath` (string, required): Target file path
- `content` (string, required): New file content

**Output Format:** Success/failure response
**Use Case:** Create new notes, completely rewrite files
**Warning:** Destructive operation - replaces entire file

#### `patch_content`
**Purpose:** Insert content at specific locations (headings, blocks, frontmatter)
**Category:** CRUD - Update (Targeted)
**Input Parameters:**
- `filepath` (string, required): Target file path
- `content` (string, required): Content to insert
- `operation` (string, required): One of: "append", "prepend", "replace"
- `target_type` (string, required): One of: "heading", "block", "frontmatter"
- `target` (string, required): Hierarchical path to target location

**Output Format:** Success/failure response
**Use Case:** Update specific sections, modify frontmatter, insert at blocks
**Advanced Features:**
- Hierarchical targeting: "Heading 1::Subheading 1:1"
- Custom delimiter support (default: "::")
- Whitespace trimming options

**HTTP Headers Used:**
- `Operation`: append/prepend/replace
- `Target-Type`: heading/block/frontmatter
- `Target`: Path string
- `Target-Delimiter`: Optional (default "::")
- `Trim-Target-Whitespace`: Optional boolean

```python
# Example - Append to specific heading:
# {
#   "filepath": "Projects/Main.md",
#   "content": "New task item",
#   "operation": "append",
#   "target_type": "heading",
#   "target": "Tasks::Today:1"
# }

# Example - Update frontmatter:
# {
#   "filepath": "Notes/File.md",
#   "content": "updated",
#   "operation": "replace",
#   "target_type": "frontmatter",
#   "target": "status"
# }
```

---

### 1.4 Deletion Tools (1 tool)

#### `delete_file`
**Purpose:** Removes files or directories from vault
**Category:** CRUD - Delete
**Input Parameters:**
- `filepath` (string, required): Path to file/directory to delete
- `confirm` (boolean, required): Must be explicitly set to `true`

**Output Format:** Success/failure response
**Use Case:** Clean up old notes, remove temporary files
**Safety:** Requires explicit confirmation flag to prevent accidental deletion
**Validation:** Raises RuntimeError if confirm != true

```python
# Example:
# {
#   "filepath": "Archive/old-file.md",
#   "confirm": true
# }
```

---

### 1.5 Search Tools (2 tools)

#### `search`
**Purpose:** Simple text-based search across all vault files
**Category:** Search - Simple
**Input Parameters:**
- `query` (string, required): Text to search for
- `context_length` (integer, optional): Characters of context around matches (default: 100)

**Output Format:** JSON array of search results with context
**Use Case:** Find notes containing keywords, locate information
**HTTP Endpoint:** POST `/search/simple/`

```python
# Example:
# {
#   "query": "project deadline",
#   "context_length": 150
# }
```

#### `complex_search`
**Purpose:** Advanced search using JsonLogic queries
**Category:** Search - Advanced
**Input Parameters:**
- `query` (object, required): JsonLogic query structure
- Additional parameters depend on query structure

**Output Format:** JSON array of matching files/data
**Use Case:** Complex queries with glob patterns, regexp, logical operators
**Supports:**
- Glob patterns for file matching
- Regular expressions for content matching
- Boolean logic (AND, OR, NOT)
- Dataview DQL queries (if plugin enabled)

**JsonLogic Example:**
```json
{
  "query": {
    "and": [
      {"glob": "Projects/*.md"},
      {"regexp": "status: active"}
    ]
  }
}
```

---

### 1.6 Periodic Notes Tools (2 tools)

#### `get_periodic_note`
**Purpose:** Retrieves current periodic note (daily, weekly, monthly, quarterly, yearly)
**Category:** Specialized - Periodic Notes
**Input Parameters:**
- `period` (string, required): One of: "daily", "weekly", "monthly", "quarterly", "yearly"

**Output Format:** Note content as NoteJson object
**Use Case:** Access today's daily note, current week's planning
**HTTP Endpoint:** GET `/periodic/{period}/`

```python
# Example:
# {"period": "daily"}  # Gets today's daily note
# {"period": "weekly"}  # Gets current week's note
```

#### `get_recent_periodic_notes`
**Purpose:** Retrieves up to 50 recent periodic notes
**Category:** Specialized - Periodic Notes
**Input Parameters:**
- `period` (string, required): Type of periodic note
- `include_content` (boolean, optional): Whether to include note content (default: false)
- `limit` (integer, optional): Max results to return (max: 50)

**Output Format:** Array of NoteJson objects or metadata
**Use Case:** Review past daily notes, analyze weekly progress
**Efficiency:** Can return metadata-only for faster responses

```python
# Example - Get last 10 daily notes with content:
# {
#   "period": "daily",
#   "include_content": true,
#   "limit": 10
# }
```

---

### 1.7 Change Tracking Tools (1 tool)

#### `get_recent_changes`
**Purpose:** Lists files modified within specified timeframe
**Category:** Metadata/Tracking
**Input Parameters:**
- `days` (integer, required): Number of days to look back (max: 100)

**Output Format:** Array of file paths with modification timestamps
**Use Case:** Track recent activity, find recently edited notes
**Limitation:** Maximum 100 days lookback
**Implementation:** Uses Dataview DQL query internally

```python
# Example:
# {"days": 7}  # Files modified in last week
```

---

## 2. Tool Categorization

### By Operation Type

| Category | Tools | Count |
|----------|-------|-------|
| **Discovery/Navigation** | list_files_in_vault, list_files_in_dir | 2 |
| **Read Operations** | get_file_contents, batch_get_file_contents | 2 |
| **Create/Update** | append_content, put_content, patch_content | 3 |
| **Delete** | delete_file | 1 |
| **Search** | search, complex_search | 2 |
| **Periodic Notes** | get_periodic_note, get_recent_periodic_notes | 2 |
| **Metadata/Tracking** | get_recent_changes | 1 |

### By Complexity Level

**Simple (Single-purpose):**
- list_files_in_vault
- get_file_contents
- append_content
- put_content
- delete_file

**Moderate (Multiple parameters):**
- list_files_in_dir
- batch_get_file_contents
- search
- get_periodic_note
- get_recent_changes

**Advanced (Complex logic):**
- patch_content (hierarchical targeting)
- complex_search (JsonLogic queries)
- get_recent_periodic_notes (multi-option)

### By Data Flow

**Read-Only:**
- list_files_in_vault
- list_files_in_dir
- get_file_contents
- batch_get_file_contents
- search
- complex_search
- get_periodic_note
- get_recent_periodic_notes
- get_recent_changes

**Write/Modify:**
- append_content
- put_content
- patch_content
- delete_file

---

## 3. Architecture Analysis

### 3.1 Core Architecture Pattern

The MCP Obsidian server implements a **Handler-Based Architecture** with clear separation of concerns:

```
┌─────────────────────────────────────────────┐
│           MCP Server (server.py)            │
│  - Tool registration                        │
│  - Request routing                          │
│  - Error handling                           │
└─────────────────┬───────────────────────────┘
                  │
         ┌────────┴────────┐
         │                 │
┌────────▼────────┐ ┌─────▼──────────────┐
│  Tool Handlers  │ │  Obsidian Client   │
│   (tools.py)    │ │  (obsidian.py)     │
│  - Validation   │ │  - HTTP requests   │
│  - Logic        │ │  - API wrapping    │
│  - Formatting   │ │  - Error handling  │
└─────────────────┘ └────────────────────┘
                           │
                    ┌──────▼──────────┐
                    │  Obsidian REST  │
                    │      API        │
                    │  (Plugin)       │
                    └─────────────────┘
```

### 3.2 Class Hierarchy

**Abstract Base Class Pattern:**

```python
class ToolHandler(ABC):
    """Base class for all tool handlers"""

    def __init__(self, tool_name: str):
        self.name = tool_name

    @abstractmethod
    def get_tool_description(self) -> Tool:
        """Returns MCP Tool schema"""
        pass

    @abstractmethod
    def run_tool(self, args: dict) -> Sequence[TextContent | ImageContent | EmbeddedResource]:
        """Executes tool logic"""
        pass
```

**Concrete Implementation Example:**

```python
class GetFileContentsToolHandler(ToolHandler):
    def __init__(self):
        super().__init__("get_file_contents")

    def get_tool_description(self) -> Tool:
        return Tool(
            name=self.name,
            description="Retrieves content of a file",
            inputSchema={
                "type": "object",
                "properties": {
                    "filepath": {
                        "type": "string",
                        "description": "Path to file"
                    }
                },
                "required": ["filepath"]
            }
        )

    def run_tool(self, args: dict):
        # Validation
        if "filepath" not in args:
            raise RuntimeError("filepath required")

        # Execution
        obsidian = Obsidian(api_key)
        content = obsidian.get_file_contents(args["filepath"])

        # Return
        return [TextContent(type="text", text=content)]
```

### 3.3 Key Design Patterns

#### 1. **Registry Pattern**
```python
tool_handlers = {}

def add_tool_handler(tool_class: ToolHandler):
    """Register tool handler by name"""
    global tool_handlers
    tool_handlers[tool_class.name] = tool_class

def get_tool_handler(name: str) -> ToolHandler | None:
    """Retrieve tool handler by name"""
    return tool_handlers.get(name)
```

**Benefits:**
- Dynamic tool registration
- Easy to add new tools
- Centralized tool management
- Runtime tool discovery

#### 2. **Template Method Pattern**
The ToolHandler base class defines the template, concrete classes fill in details:
- `get_tool_description()` - Schema definition
- `run_tool()` - Execution logic

#### 3. **Facade Pattern**
The Obsidian client class wraps the REST API complexity:
```python
class Obsidian:
    def _safe_call(self, func, *args, **kwargs):
        """Unified error handling for all API calls"""
        try:
            return func(*args, **kwargs)
        except requests.HTTPError as e:
            # Extract error details from response
            # Raise formatted error
        except Exception as e:
            # Generic error handling
```

**All API methods use this wrapper:**
- Consistent error handling
- Centralized logging
- Automatic retry potential
- Clean interface for tool handlers

#### 4. **Decorator Pattern (MCP Server)**
```python
@app.list_tools()
async def list_tools() -> list[Tool]:
    """MCP protocol: List available tools"""
    return [th.get_tool_description() for th in tool_handlers.values()]

@app.call_tool()
async def call_tool(name: str, arguments: Any):
    """MCP protocol: Execute tool"""
    # Validate, route, execute
```

### 3.4 Error Handling Strategy

**Multi-Layer Error Handling:**

1. **Parameter Validation (Tool Handler Level)**
```python
if "filepath" not in args:
    raise RuntimeError("filepath is required")
```

2. **API Error Handling (Client Level)**
```python
def _safe_call(self, func, *args, **kwargs):
    try:
        return func(*args, **kwargs)
    except requests.HTTPError as e:
        error_data = e.response.json()
        raise RuntimeError(f"API Error {error_data['errorCode']}: {error_data['message']}")
```

3. **Server Error Handling (MCP Level)**
```python
try:
    return tool_handler.run_tool(arguments)
except Exception as e:
    logger.error(str(e))
    raise RuntimeError(f"Caught Exception. Error: {str(e)}")
```

**Error Propagation Flow:**
```
Tool Handler (Validation Error)
    ↓
Obsidian Client (HTTP/API Error)
    ↓
MCP Server (Logged + Wrapped)
    ↓
Client (Formatted Error Message)
```

### 3.5 Async Architecture

**Server Uses Async/Await Pattern:**
```python
async def main():
    from mcp.server.stdio import stdio_server

    async with stdio_server() as (read_stream, write_stream):
        await app.run(
            read_stream,
            write_stream,
            app.create_initialization_options()
        )

# Entry point:
def main():
    asyncio.run(server.main())
```

**Benefits:**
- Non-blocking I/O for multiple requests
- Efficient resource utilization
- Scalable for concurrent operations
- Compatible with MCP protocol requirements

### 3.6 Configuration Management

**Environment-Based Configuration:**

```python
import os
from dotenv import load_dotenv

load_dotenv()

# Required
api_key = os.getenv("OBSIDIAN_API_KEY")
if not api_key:
    raise ValueError("OBSIDIAN_API_KEY required")

# Optional with defaults
protocol = os.getenv('OBSIDIAN_PROTOCOL', 'https').lower()
host = os.getenv('OBSIDIAN_HOST', '127.0.0.1')
port = os.getenv('OBSIDIAN_PORT', '27124')
verify_ssl = os.getenv('OBSIDIAN_VERIFY_SSL', 'true').lower() == 'true'
```

**Configuration Sources:**
1. `.env` file (development)
2. Environment variables (production)
3. Claude Desktop config (integration)

**Claude Desktop Integration:**
```json
{
  "mcpServers": {
    "obsidian": {
      "command": "mcp-obsidian",
      "env": {
        "OBSIDIAN_API_KEY": "your_api_key_here",
        "OBSIDIAN_HOST": "127.0.0.1",
        "OBSIDIAN_PORT": "27124"
      }
    }
  }
}
```

---

## 4. Data Models & Schemas

### 4.1 MCP Tool Schema Pattern

All tools follow this JSON Schema structure:

```json
{
  "name": "tool_name",
  "description": "What the tool does",
  "inputSchema": {
    "type": "object",
    "properties": {
      "param_name": {
        "type": "string|number|boolean|array|object",
        "description": "Parameter description"
      }
    },
    "required": ["required_param1", "required_param2"]
  }
}
```

### 4.2 Obsidian REST API Data Models

**NoteJson Schema:**
```json
{
  "content": "string - file text content",
  "frontmatter": {
    "key": "value - YAML front matter as object"
  },
  "path": "string - file path",
  "stat": {
    "ctime": "number - creation timestamp",
    "mtime": "number - modification timestamp",
    "size": "number - file size in bytes"
  },
  "tags": ["array of tag strings"]
}
```

**Error Schema:**
```json
{
  "errorCode": "number - 5-digit error code",
  "message": "string - error description"
}
```

### 4.3 Response Format Pattern

**All tool responses use MCP content types:**

```python
from mcp.types import TextContent, ImageContent, EmbeddedResource

# Text response (most common)
return [TextContent(
    type="text",
    text=json.dumps(result, indent=2)
)]

# Multiple content blocks
return [
    TextContent(type="text", text="Header"),
    TextContent(type="text", text=data)
]
```

---

## 5. Integration Patterns

### 5.1 HTTP Client Pattern

**Base Request Structure:**

```python
import requests

class Obsidian:
    def __init__(self, api_key: str, host: str = "127.0.0.1", port: str = "27124"):
        self.base_url = f"https://{host}:{port}"
        self.headers = {
            "Authorization": f"Bearer {api_key}",
            "Content-Type": "application/json"
        }

    def _request(self, method: str, endpoint: str, **kwargs):
        url = f"{self.base_url}{endpoint}"
        response = requests.request(
            method,
            url,
            headers=self.headers,
            timeout=30,
            **kwargs
        )
        response.raise_for_status()
        return response
```

### 5.2 REST API Endpoint Mapping

| Tool | HTTP Method | Endpoint | Content-Type |
|------|-------------|----------|--------------|
| list_files_in_vault | GET | `/vault/` | - |
| list_files_in_dir | GET | `/vault/{dirpath}/` | - |
| get_file_contents | GET | `/vault/{filepath}` | - |
| search | POST | `/search/simple/` | application/json |
| complex_search | POST | `/search/` | application/json |
| append_content | POST | `/vault/{filepath}` | text/markdown |
| put_content | PUT | `/vault/{filepath}` | text/markdown |
| patch_content | PATCH | `/vault/{filepath}` | text/markdown |
| delete_file | DELETE | `/vault/{filepath}` | - |
| get_periodic_note | GET | `/periodic/{period}/` | - |
| get_recent_changes | POST | `/search/` | application/json (DQL) |

### 5.3 Authentication Pattern

**Bearer Token Authentication:**

```python
headers = {
    "Authorization": f"Bearer {api_key}"
}
```

**API Key Sources:**
1. Obsidian Plugin Settings → Local REST API → API Key
2. Generated on first plugin activation
3. Stored in environment variable for MCP server

---

## 6. Advanced Features

### 6.1 Hierarchical Targeting (PATCH)

**Target Path Syntax:**
```
Heading::Subheading::SubSubheading:instance
```

**Examples:**

```python
# Target first instance of "Tasks" under "Daily Log"
target = "Daily Log::Tasks:1"

# Target second "Notes" section under "Projects" > "Alpha"
target = "Projects::Alpha::Notes:2"

# Target frontmatter field
target_type = "frontmatter"
target = "status"  # Will modify YAML: status: value
```

**Custom Delimiter:**
```python
# Use "/" instead of "::"
headers = {
    "Target-Delimiter": "/"
}
target = "Heading/Subheading/1"
```

### 6.2 JsonLogic Query Syntax

**Complex Search Examples:**

```json
// Find markdown files in Projects folder containing "urgent"
{
  "and": [
    {"glob": "Projects/*.md"},
    {"regexp": "urgent"}
  ]
}

// OR logic
{
  "or": [
    {"glob": "Archive/*.md"},
    {"glob": "Old Notes/*.md"}
  ]
}

// NOT logic
{
  "and": [
    {"glob": "**/*.md"},
    {"not": {"regexp": "draft"}}
  ]
}
```

### 6.3 Dataview DQL Integration

**Recent Changes Uses DQL:**
```python
# Internally constructs:
query = f'''
TABLE file.mtime as "Modified"
WHERE file.mtime >= date(today) - dur({days} days)
SORT file.mtime DESC
LIMIT 100
'''
```

**Limitation:** Requires Dataview plugin enabled in Obsidian

---

## 7. Best Practices & Patterns for AI Agents

### 7.1 Tool Design Principles

#### **1. Single Responsibility**
Each tool does one thing well:
- ✅ `get_file_contents` - Only reads files
- ✅ `append_content` - Only appends
- ❌ Don't combine read + write in one tool

#### **2. Clear Parameter Names**
```python
# Good
{"filepath": "Notes/file.md", "content": "text"}

# Bad
{"path": "...", "data": "...", "f": "..."}
```

#### **3. Required vs Optional Parameters**
```python
{
  "required": ["filepath"],  # Must have
  "optional": ["context_length"]  # Can omit
}
```

#### **4. Validation at Handler Level**
```python
def run_tool(self, args: dict):
    # Validate BEFORE making API call
    if "filepath" not in args:
        raise RuntimeError("filepath required")

    if args.get("confirm") != True:
        raise RuntimeError("Must confirm deletion")

    # Then execute
    return self.execute(args)
```

#### **5. Consistent Return Format**
All tools return `Sequence[TextContent | ImageContent | EmbeddedResource]`:
```python
# Always wrap in list
return [TextContent(type="text", text=result)]

# Not just a string
# return result  # ❌ Wrong
```

### 7.2 Error Handling Best Practices

#### **1. Descriptive Error Messages**
```python
# Good
raise RuntimeError("filepath required. Provide path to file in vault.")

# Bad
raise RuntimeError("Missing param")
```

#### **2. Error Context**
```python
try:
    content = obsidian.get_file_contents(filepath)
except Exception as e:
    raise RuntimeError(f"Failed to read {filepath}: {str(e)}")
```

#### **3. Safety Checks**
```python
# Confirm destructive operations
if operation == "delete" and not args.get("confirm"):
    raise RuntimeError("Deletion requires confirm=true")
```

### 7.3 API Client Best Practices

#### **1. Unified Error Handler**
```python
def _safe_call(self, func, *args, **kwargs):
    """Single method for all API error handling"""
    try:
        return func(*args, **kwargs)
    except requests.HTTPError as e:
        # Extract structured error
        error_data = e.response.json()
        raise RuntimeError(f"API Error: {error_data['message']}")
    except requests.Timeout:
        raise RuntimeError("Request timeout")
    except Exception as e:
        raise RuntimeError(f"Unexpected error: {str(e)}")
```

#### **2. Timeout Configuration**
```python
response = requests.get(url, timeout=30)  # Always set timeout
```

#### **3. SSL Verification**
```python
# Allow disabling for local dev
verify_ssl = os.getenv('VERIFY_SSL', 'true').lower() == 'true'
response = requests.get(url, verify=verify_ssl)
```

### 7.4 Server Architecture Best Practices

#### **1. Environment Validation on Startup**
```python
api_key = os.getenv("OBSIDIAN_API_KEY")
if not api_key:
    raise ValueError("OBSIDIAN_API_KEY required. Set in environment or .env file")
```

#### **2. Centralized Tool Registration**
```python
# Register all tools in one place
def register_tools():
    add_tool_handler(ListFilesInVaultToolHandler())
    add_tool_handler(GetFileContentsToolHandler())
    # ... etc
```

#### **3. Logging Strategy**
```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("mcp-obsidian")

# Log errors
logger.error(f"Tool {name} failed: {str(e)}")

# Log info
logger.info(f"Executing tool: {name}")
```

#### **4. Async Entry Point**
```python
def main():
    """Sync entry point"""
    asyncio.run(server.main())

async def server_main():
    """Async server logic"""
    async with stdio_server() as (read_stream, write_stream):
        await app.run(read_stream, write_stream, options)
```

---

## 8. Code Examples

### 8.1 Complete Tool Implementation

```python
from abc import ABC, abstractmethod
from typing import Sequence
from mcp.types import Tool, TextContent
import json

class ToolHandler(ABC):
    """Base class for all tool handlers"""

    def __init__(self, tool_name: str):
        self.name = tool_name

    @abstractmethod
    def get_tool_description(self) -> Tool:
        """Return MCP Tool schema"""
        pass

    @abstractmethod
    def run_tool(self, args: dict) -> Sequence[TextContent]:
        """Execute tool logic"""
        pass

class SearchToolHandler(ToolHandler):
    """Simple text search across vault"""

    def __init__(self):
        super().__init__("search")

    def get_tool_description(self) -> Tool:
        return Tool(
            name=self.name,
            description="Search for documents matching a text query across all files in the vault",
            inputSchema={
                "type": "object",
                "properties": {
                    "query": {
                        "type": "string",
                        "description": "Text to search for"
                    },
                    "context_length": {
                        "type": "number",
                        "description": "Number of characters of context around matches (default: 100)"
                    }
                },
                "required": ["query"]
            }
        )

    def run_tool(self, args: dict) -> Sequence[TextContent]:
        # Validation
        if "query" not in args:
            raise RuntimeError("query parameter is required")

        query = args["query"]
        context_length = args.get("context_length", 100)

        # Execute
        obsidian = Obsidian(api_key)
        results = obsidian.search(query, context_length)

        # Format response
        return [TextContent(
            type="text",
            text=json.dumps(results, indent=2)
        )]
```

### 8.2 HTTP Client Implementation

```python
import requests
import os

class Obsidian:
    """Client for Obsidian Local REST API"""

    def __init__(self,
                 api_key: str,
                 protocol: str = "https",
                 host: str = "127.0.0.1",
                 port: str = "27124",
                 verify_ssl: bool = True):
        self.base_url = f"{protocol}://{host}:{port}"
        self.api_key = api_key
        self.verify_ssl = verify_ssl

    def _safe_call(self, func, *args, **kwargs):
        """Unified error handling wrapper"""
        try:
            return func(*args, **kwargs)
        except requests.HTTPError as e:
            try:
                error_data = e.response.json()
                error_code = error_data.get("errorCode", "unknown")
                message = error_data.get("message", str(e))
                raise RuntimeError(f"API Error {error_code}: {message}")
            except (ValueError, KeyError):
                raise RuntimeError(f"HTTP Error: {str(e)}")
        except requests.Timeout:
            raise RuntimeError("Request timeout - server not responding")
        except requests.ConnectionError:
            raise RuntimeError("Connection failed - check if Obsidian is running")
        except Exception as e:
            raise RuntimeError(f"Unexpected error: {str(e)}")

    def _request(self, method: str, endpoint: str, **kwargs):
        """Make HTTP request to Obsidian API"""
        url = f"{self.base_url}{endpoint}"
        headers = kwargs.pop("headers", {})
        headers["Authorization"] = f"Bearer {self.api_key}"

        response = requests.request(
            method,
            url,
            headers=headers,
            verify=self.verify_ssl,
            timeout=30,
            **kwargs
        )
        response.raise_for_status()
        return response

    def get_file_contents(self, filepath: str) -> str:
        """Get content of a file"""
        def _get():
            response = self._request("GET", f"/vault/{filepath}")
            return response.text

        return self._safe_call(_get)

    def search(self, query: str, context_length: int = 100) -> dict:
        """Simple text search"""
        def _search():
            response = self._request(
                "POST",
                "/search/simple/",
                json={
                    "query": query,
                    "contextLength": context_length
                }
            )
            return response.json()

        return self._safe_call(_search)

    def append_content(self, filepath: str, content: str) -> dict:
        """Append content to file"""
        def _append():
            response = self._request(
                "POST",
                f"/vault/{filepath}",
                data=content,
                headers={"Content-Type": "text/markdown"}
            )
            return response.json()

        return self._safe_call(_append)

    def patch_content(self, filepath: str, content: str,
                     operation: str, target_type: str, target: str) -> dict:
        """Patch content at specific location"""
        def _patch():
            response = self._request(
                "PATCH",
                f"/vault/{filepath}",
                data=content,
                headers={
                    "Content-Type": "text/markdown",
                    "Operation": operation,
                    "Target-Type": target_type,
                    "Target": target
                }
            )
            return response.json()

        return self._safe_call(_patch)
```

### 8.3 MCP Server Setup

```python
import asyncio
import logging
import os
from typing import Any, Sequence
from dotenv import load_dotenv
from mcp.server import Server
from mcp.types import Tool, TextContent, ImageContent, EmbeddedResource
from mcp.server.stdio import stdio_server

# Load environment
load_dotenv()

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("mcp-server")

# Validate required config
api_key = os.getenv("API_KEY")
if not api_key:
    raise ValueError("API_KEY environment variable required")

# Create server
app = Server("my-mcp-server")

# Tool registry
tool_handlers = {}

def add_tool_handler(tool_class):
    """Register a tool handler"""
    global tool_handlers
    tool_handlers[tool_class.name] = tool_class

def get_tool_handler(name: str):
    """Get tool handler by name"""
    return tool_handlers.get(name)

# Register tools
add_tool_handler(SearchToolHandler())
add_tool_handler(GetFileContentsToolHandler())
# ... register all tools

# MCP protocol handlers
@app.list_tools()
async def list_tools() -> list[Tool]:
    """List available tools"""
    return [handler.get_tool_description()
            for handler in tool_handlers.values()]

@app.call_tool()
async def call_tool(name: str, arguments: Any) -> Sequence[TextContent | ImageContent | EmbeddedResource]:
    """Execute a tool"""
    # Validate arguments
    if not isinstance(arguments, dict):
        raise RuntimeError("Arguments must be a dictionary")

    # Get handler
    handler = get_tool_handler(name)
    if not handler:
        raise ValueError(f"Unknown tool: {name}")

    # Execute with error handling
    try:
        logger.info(f"Executing tool: {name}")
        return handler.run_tool(arguments)
    except Exception as e:
        logger.error(f"Tool {name} failed: {str(e)}")
        raise RuntimeError(f"Tool execution failed: {str(e)}")

# Server entry point
async def main():
    """Run the MCP server"""
    async with stdio_server() as (read_stream, write_stream):
        await app.run(
            read_stream,
            write_stream,
            app.create_initialization_options()
        )

# Package entry point
def run():
    """Main entry point"""
    asyncio.run(main())
```

### 8.4 Configuration Management

```python
# .env file
OBSIDIAN_API_KEY=your_api_key_here
OBSIDIAN_HOST=127.0.0.1
OBSIDIAN_PORT=27124
OBSIDIAN_PROTOCOL=https
OBSIDIAN_VERIFY_SSL=true

# Python code
import os
from dotenv import load_dotenv

load_dotenv()

class Config:
    """Application configuration"""

    # Required
    API_KEY = os.getenv("OBSIDIAN_API_KEY")

    # Optional with defaults
    PROTOCOL = os.getenv("OBSIDIAN_PROTOCOL", "https").lower()
    HOST = os.getenv("OBSIDIAN_HOST", "127.0.0.1")
    PORT = os.getenv("OBSIDIAN_PORT", "27124")
    VERIFY_SSL = os.getenv("OBSIDIAN_VERIFY_SSL", "true").lower() == "true"

    @classmethod
    def validate(cls):
        """Validate required configuration"""
        if not cls.API_KEY:
            raise ValueError(
                "OBSIDIAN_API_KEY required. "
                "Set in .env file or environment variables."
            )

    @classmethod
    def get_base_url(cls):
        """Construct base URL"""
        return f"{cls.PROTOCOL}://{cls.HOST}:{cls.PORT}"

# Usage
Config.validate()
obsidian = Obsidian(
    api_key=Config.API_KEY,
    protocol=Config.PROTOCOL,
    host=Config.HOST,
    port=Config.PORT,
    verify_ssl=Config.VERIFY_SSL
)
```

---

## 9. Dependencies & Build System

### 9.1 Required Dependencies

```toml
[project]
name = "mcp-obsidian"
version = "0.2.1"
requires-python = ">=3.11"

dependencies = [
    "mcp>=1.1.0",           # Model Context Protocol SDK
    "python-dotenv>=1.0.1",  # Environment variable loading
    "requests>=2.32.3",      # HTTP client
]

[project.optional-dependencies]
dev = [
    "pyright>=1.1.389",  # Type checking
]
```

### 9.2 Build System

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project.scripts]
mcp-obsidian = "mcp_obsidian:main"
```

### 9.3 Package Management

**Using `uv` (recommended):**
```bash
# Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv pip install -e .

# Install dev dependencies
uv pip install -e ".[dev]"

# Run server
uv run mcp-obsidian
```

**Using pip:**
```bash
pip install -e .
pip install -e ".[dev]"
```

---

## 10. Known Limitations & Considerations

### 10.1 Critical Issues (from GitHub Issues)

#### **SSL/TLS Limitations** (Issue #91, #72)
- Cannot use custom SSL certificates
- No support for self-signed certificates
- Non-encrypted HTTP connections may fail

**Workaround:**
```python
verify_ssl = False  # Disable SSL verification (not recommended for production)
```

#### **Connection Issues** (Issue #76)
- "Connection refused [Errno 111]" errors
- Server may not be running or wrong port
- Firewall blocking connections

**Solution:**
1. Verify Obsidian is running
2. Check Local REST API plugin is enabled
3. Confirm port 27124 (HTTPS) or 27123 (HTTP)
4. Check firewall settings

#### **Timeout Issues** (Issue #88)
- Default timeout too short for large vaults
- Search operations may timeout

**Potential Fix:**
```python
# Increase timeout for large operations
response = requests.get(url, timeout=120)  # 2 minutes
```

### 10.2 Feature Gaps

#### **No Multiple Vault Support** (Issue #63)
- Can only connect to one vault at a time
- No vault switching

#### **No Version Command** (Issue #78)
```bash
mcp-obsidian --version  # Not available
```

#### **Plugin Dependencies**
- Dataview plugin required for:
  - `get_recent_changes`
  - Complex search queries using DQL
- Periodic Notes plugin required for periodic note tools
- Tools fail silently if plugins disabled (Issue #70)

### 10.3 Character Encoding Issues (Issue #73)
- May have problems with non-ASCII characters
- Unicode handling inconsistent

### 10.4 Integration Limitations

**Claude Desktop:**
- ✅ Works well
- Configuration in `claude_desktop_config.json`

**ChatGPT:** (Issue #62)
- ❌ Integration difficulties
- May not support MCP protocol

**Docker:** (Issues #57, #61)
- Networking challenges
- SSL certificate issues in containers

### 10.5 Performance Considerations

**Large Vault Search:**
- May timeout with default settings
- Consider pagination or filtering

**Batch Operations:**
- `batch_get_file_contents` more efficient than multiple single calls
- But may hit memory limits with many files

**Recent Changes:**
- Limited to 100 results
- Maximum 100 days lookback

### 10.6 Security Considerations

**API Key Storage:**
- Stored in plain text in environment variables
- No encryption at rest
- Transmitted via Bearer token (HTTPS recommended)

**File Access:**
- No permission system beyond vault-level access
- Any tool can read/write/delete any file in vault
- Deletion requires explicit confirmation flag

**Network Security:**
- Local-only by default (127.0.0.1)
- HTTPS recommended but can use HTTP
- SSL verification can be disabled (risky)

---

## 11. Applying Patterns to Pydantic AI Agent

### 11.1 Tool Architecture

**Adopt Handler-Based Pattern:**

```python
from abc import ABC, abstractmethod
from pydantic import BaseModel, Field
from typing import Any

class ToolSchema(BaseModel):
    """Pydantic model for tool schema"""
    name: str
    description: str
    parameters: dict[str, Any]

class ToolHandler(ABC):
    """Base class for Pydantic AI tools"""

    def __init__(self, name: str):
        self.name = name

    @abstractmethod
    def get_schema(self) -> ToolSchema:
        """Return tool schema"""
        pass

    @abstractmethod
    async def execute(self, **kwargs) -> Any:
        """Execute tool logic"""
        pass

# Tool registry
class ToolRegistry:
    def __init__(self):
        self._tools: dict[str, ToolHandler] = {}

    def register(self, tool: ToolHandler):
        """Register a tool"""
        self._tools[tool.name] = tool

    def get(self, name: str) -> ToolHandler | None:
        """Get tool by name"""
        return self._tools.get(name)

    def list_schemas(self) -> list[ToolSchema]:
        """List all tool schemas"""
        return [tool.get_schema() for tool in self._tools.values()]

# Usage
registry = ToolRegistry()
registry.register(SearchNotesTool())
registry.register(GetNoteContentTool())
```

### 11.2 Error Handling

**Multi-Layer Error Strategy:**

```python
from typing import TypedDict

class ToolError(Exception):
    """Base exception for tool errors"""
    def __init__(self, message: str, code: str = "TOOL_ERROR"):
        self.message = message
        self.code = code
        super().__init__(message)

class ValidationError(ToolError):
    """Parameter validation error"""
    def __init__(self, message: str):
        super().__init__(message, "VALIDATION_ERROR")

class ExecutionError(ToolError):
    """Tool execution error"""
    def __init__(self, message: str):
        super().__init__(message, "EXECUTION_ERROR")

# In tool handler
async def execute(self, **kwargs) -> Any:
    # Layer 1: Validation
    if "note_path" not in kwargs:
        raise ValidationError("note_path is required")

    try:
        # Layer 2: Execution
        result = await self._do_work(kwargs)
        return result
    except Exception as e:
        # Layer 3: Wrap and rethrow
        raise ExecutionError(f"Failed to execute {self.name}: {str(e)}")
```

### 11.3 Configuration Management

**Use Pydantic Settings:**

```python
from pydantic_settings import BaseSettings, SettingsConfigDict

class ObsidianSettings(BaseSettings):
    """Obsidian integration settings"""

    api_key: str = Field(..., description="Obsidian API key")
    host: str = Field("127.0.0.1", description="Obsidian server host")
    port: int = Field(27124, description="Obsidian server port")
    protocol: str = Field("https", description="HTTP or HTTPS")
    verify_ssl: bool = Field(True, description="Verify SSL certificates")
    timeout: int = Field(30, description="Request timeout in seconds")

    model_config = SettingsConfigDict(
        env_prefix="OBSIDIAN_",
        env_file=".env",
        env_file_encoding="utf-8"
    )

    @property
    def base_url(self) -> str:
        """Construct base URL"""
        return f"{self.protocol}://{self.host}:{self.port}"

# Usage
settings = ObsidianSettings()
client = ObsidianClient(
    base_url=settings.base_url,
    api_key=settings.api_key,
    verify_ssl=settings.verify_ssl
)
```

### 11.4 HTTP Client Pattern

**Use httpx with async support:**

```python
import httpx
from typing import Any

class ObsidianClient:
    """Async HTTP client for Obsidian API"""

    def __init__(self, base_url: str, api_key: str, verify_ssl: bool = True):
        self.base_url = base_url
        self.headers = {"Authorization": f"Bearer {api_key}"}
        self.verify_ssl = verify_ssl

    async def _request(self, method: str, endpoint: str, **kwargs) -> httpx.Response:
        """Make HTTP request"""
        url = f"{self.base_url}{endpoint}"

        async with httpx.AsyncClient(verify=self.verify_ssl) as client:
            response = await client.request(
                method,
                url,
                headers=self.headers,
                timeout=30.0,
                **kwargs
            )
            response.raise_for_status()
            return response

    async def get_note(self, filepath: str) -> str:
        """Get note content"""
        try:
            response = await self._request("GET", f"/vault/{filepath}")
            return response.text
        except httpx.HTTPError as e:
            raise ExecutionError(f"Failed to get note {filepath}: {str(e)}")

    async def search_notes(self, query: str, context_length: int = 100) -> dict[str, Any]:
        """Search notes"""
        try:
            response = await self._request(
                "POST",
                "/search/simple/",
                json={"query": query, "contextLength": context_length}
            )
            return response.json()
        except httpx.HTTPError as e:
            raise ExecutionError(f"Search failed: {str(e)}")
```

### 11.5 Pydantic AI Integration

**Create Pydantic AI tools:**

```python
from pydantic_ai import Agent, RunContext
from pydantic_ai.tools import Tool

class ObsidianDeps:
    """Dependencies for Obsidian tools"""
    def __init__(self, settings: ObsidianSettings):
        self.client = ObsidianClient(
            base_url=settings.base_url,
            api_key=settings.api_key,
            verify_ssl=settings.verify_ssl
        )

# Create agent
agent = Agent(
    model="openai:gpt-4",
    deps_type=ObsidianDeps,
    system_prompt="You are an Obsidian vault assistant."
)

# Define tools
@agent.tool
async def search_notes(ctx: RunContext[ObsidianDeps], query: str, context_length: int = 100) -> dict:
    """
    Search for notes matching a query.

    Args:
        query: Text to search for
        context_length: Context characters around matches

    Returns:
        Dictionary with search results
    """
    return await ctx.deps.client.search_notes(query, context_length)

@agent.tool
async def get_note_content(ctx: RunContext[ObsidianDeps], filepath: str) -> str:
    """
    Get content of a specific note.

    Args:
        filepath: Path to note in vault

    Returns:
        Note content as string
    """
    return await ctx.deps.client.get_note(filepath)

@agent.tool
async def append_to_note(ctx: RunContext[ObsidianDeps], filepath: str, content: str) -> str:
    """
    Append content to a note.

    Args:
        filepath: Path to note
        content: Content to append

    Returns:
        Success message
    """
    await ctx.deps.client.append_content(filepath, content)
    return f"Successfully appended to {filepath}"

# Usage
deps = ObsidianDeps(settings=ObsidianSettings())
result = await agent.run(
    "Search for notes about AI agents",
    deps=deps
)
```

### 11.6 Type Safety with Pydantic

**Define response models:**

```python
from pydantic import BaseModel, Field
from datetime import datetime

class FileStat(BaseModel):
    """File statistics"""
    ctime: int = Field(..., description="Creation timestamp")
    mtime: int = Field(..., description="Modification timestamp")
    size: int = Field(..., description="File size in bytes")

class NoteMetadata(BaseModel):
    """Obsidian note metadata"""
    content: str = Field(..., description="Note content")
    frontmatter: dict[str, Any] = Field(default_factory=dict, description="YAML frontmatter")
    path: str = Field(..., description="File path")
    stat: FileStat = Field(..., description="File statistics")
    tags: list[str] = Field(default_factory=list, description="Note tags")

class SearchResult(BaseModel):
    """Search result item"""
    filepath: str
    context: str
    line_number: int | None = None

class SearchResponse(BaseModel):
    """Search response"""
    results: list[SearchResult]
    total_count: int

# Use in client
async def search_notes(self, query: str) -> SearchResponse:
    """Search notes with typed response"""
    response = await self._request("POST", "/search/simple/", json={"query": query})
    data = response.json()
    return SearchResponse.model_validate(data)
```

### 11.7 Logging & Monitoring

**Structured logging:**

```python
import structlog
from typing import Any

logger = structlog.get_logger()

class LoggingToolHandler(ToolHandler):
    """Tool handler with logging"""

    async def execute(self, **kwargs) -> Any:
        logger.info(
            "tool_execution_started",
            tool=self.name,
            parameters=kwargs
        )

        try:
            result = await self._execute_impl(**kwargs)

            logger.info(
                "tool_execution_completed",
                tool=self.name,
                success=True
            )

            return result

        except Exception as e:
            logger.error(
                "tool_execution_failed",
                tool=self.name,
                error=str(e),
                error_type=type(e).__name__
            )
            raise
```

### 11.8 Testing Strategy

**Unit test tool handlers:**

```python
import pytest
from unittest.mock import AsyncMock, patch

@pytest.fixture
def mock_client():
    """Mock Obsidian client"""
    client = AsyncMock()
    client.get_note.return_value = "# Test Note\nContent here"
    client.search_notes.return_value = {
        "results": [{"filepath": "test.md", "context": "..."}]
    }
    return client

@pytest.mark.asyncio
async def test_search_tool(mock_client):
    """Test search tool execution"""
    tool = SearchNotesTool()

    with patch("app.tools.obsidian_client", mock_client):
        result = await tool.execute(query="test", context_length=100)

    assert result is not None
    mock_client.search_notes.assert_called_once_with("test", 100)

@pytest.mark.asyncio
async def test_tool_validation():
    """Test parameter validation"""
    tool = GetNoteContentTool()

    with pytest.raises(ValidationError, match="filepath is required"):
        await tool.execute()  # Missing required parameter
```

---

## 12. Recommendations for Your Project

### 12.1 Architecture Recommendations

1. **Use Handler-Based Pattern**
   - Separate tool definition from execution
   - Easy to add new tools
   - Clear separation of concerns

2. **Implement Tool Registry**
   - Centralized tool management
   - Runtime tool discovery
   - Easy to extend

3. **Use Pydantic for Everything**
   - Settings: `pydantic_settings.BaseSettings`
   - Request/Response: `pydantic.BaseModel`
   - Validation: Automatic with Pydantic

4. **Async by Default**
   - Use `httpx.AsyncClient` instead of `requests`
   - All tool handlers should be async
   - Better performance for I/O operations

### 12.2 Error Handling Recommendations

1. **Create Custom Exception Hierarchy**
   ```python
   ToolError
   ├── ValidationError
   ├── ExecutionError
   └── ConfigurationError
   ```

2. **Validate Early**
   - Check parameters before API calls
   - Use Pydantic for automatic validation
   - Raise descriptive errors

3. **Log Everything**
   - Use structured logging (structlog)
   - Log tool execution start/end
   - Log errors with context

### 12.3 Security Recommendations

1. **Environment Variables**
   - Never hardcode API keys
   - Use `.env` for local development
   - Use secrets manager in production

2. **SSL/TLS**
   - Always use HTTPS in production
   - Provide option to disable for local dev
   - Support custom certificates

3. **Input Validation**
   - Validate all user inputs
   - Sanitize file paths
   - Prevent path traversal attacks

### 12.4 Tool Design Recommendations

1. **Clear Tool Names**
   - Use descriptive names: `search_notes` not `search`
   - Be consistent with naming convention
   - Avoid abbreviations

2. **Good Descriptions**
   - Explain what the tool does
   - Describe when to use it
   - Include examples in docstrings

3. **Typed Parameters**
   - Use Pydantic models for complex parameters
   - Provide defaults where sensible
   - Make required parameters obvious

4. **Consistent Return Types**
   - Always return Pydantic models
   - Use `list[Model]` for collections
   - Use `Model | None` for optional results

### 12.5 Testing Recommendations

1. **Unit Tests**
   - Test each tool handler
   - Mock external dependencies
   - Test error cases

2. **Integration Tests**
   - Test with real Obsidian API (in test vault)
   - Test tool chains
   - Test error handling

3. **Type Checking**
   - Use `pyright` or `mypy`
   - Ensure all functions are typed
   - Run in CI/CD

---

## 13. Complete Example Implementation

Here's a complete example applying all patterns:

```python
# config.py
from pydantic_settings import BaseSettings, SettingsConfigDict
from pydantic import Field

class ObsidianSettings(BaseSettings):
    api_key: str = Field(..., description="Obsidian API key")
    host: str = Field("127.0.0.1")
    port: int = Field(27124)
    protocol: str = Field("https")
    verify_ssl: bool = Field(True)

    model_config = SettingsConfigDict(
        env_prefix="OBSIDIAN_",
        env_file=".env"
    )

    @property
    def base_url(self) -> str:
        return f"{self.protocol}://{self.host}:{self.port}"

# models.py
from pydantic import BaseModel, Field
from typing import Any

class SearchResult(BaseModel):
    filepath: str
    context: str
    score: float | None = None

class SearchResponse(BaseModel):
    results: list[SearchResult]
    total_count: int

class NoteContent(BaseModel):
    content: str
    path: str
    frontmatter: dict[str, Any] = Field(default_factory=dict)

# errors.py
class ToolError(Exception):
    def __init__(self, message: str, code: str = "TOOL_ERROR"):
        self.message = message
        self.code = code
        super().__init__(message)

class ValidationError(ToolError):
    def __init__(self, message: str):
        super().__init__(message, "VALIDATION_ERROR")

class ExecutionError(ToolError):
    def __init__(self, message: str):
        super().__init__(message, "EXECUTION_ERROR")

# client.py
import httpx
import structlog

logger = structlog.get_logger()

class ObsidianClient:
    def __init__(self, settings: ObsidianSettings):
        self.base_url = settings.base_url
        self.headers = {"Authorization": f"Bearer {settings.api_key}"}
        self.verify_ssl = settings.verify_ssl

    async def _request(self, method: str, endpoint: str, **kwargs) -> httpx.Response:
        url = f"{self.base_url}{endpoint}"

        async with httpx.AsyncClient(verify=self.verify_ssl) as client:
            try:
                response = await client.request(
                    method, url, headers=self.headers, timeout=30.0, **kwargs
                )
                response.raise_for_status()
                return response
            except httpx.HTTPError as e:
                logger.error("http_request_failed", method=method, url=url, error=str(e))
                raise ExecutionError(f"HTTP request failed: {str(e)}")

    async def search(self, query: str, context_length: int = 100) -> SearchResponse:
        response = await self._request(
            "POST",
            "/search/simple/",
            json={"query": query, "contextLength": context_length}
        )
        data = response.json()
        return SearchResponse.model_validate(data)

    async def get_note(self, filepath: str) -> NoteContent:
        response = await self._request("GET", f"/vault/{filepath}")
        return NoteContent(content=response.text, path=filepath)

    async def append_note(self, filepath: str, content: str) -> bool:
        await self._request(
            "POST",
            f"/vault/{filepath}",
            content=content,
            headers={"Content-Type": "text/markdown"}
        )
        return True

# agent.py
from pydantic_ai import Agent, RunContext

class ObsidianDeps:
    def __init__(self, settings: ObsidianSettings):
        self.client = ObsidianClient(settings)

agent = Agent(
    model="openai:gpt-4",
    deps_type=ObsidianDeps,
    system_prompt="""You are an Obsidian vault assistant.
    You help users search, read, and modify their notes.
    Always be helpful and precise."""
)

@agent.tool
async def search_notes(ctx: RunContext[ObsidianDeps],
                       query: str,
                       context_length: int = 100) -> dict:
    """
    Search for notes matching a query.

    Args:
        query: Text to search for in notes
        context_length: Characters of context around matches (default: 100)

    Returns:
        Dictionary with search results including filepaths and context
    """
    logger.info("search_notes", query=query)

    if not query:
        raise ValidationError("query parameter is required")

    result = await ctx.deps.client.search(query, context_length)
    return result.model_dump()

@agent.tool
async def get_note(ctx: RunContext[ObsidianDeps], filepath: str) -> str:
    """
    Get the complete content of a note.

    Args:
        filepath: Path to the note in the vault

    Returns:
        The note content as a string
    """
    logger.info("get_note", filepath=filepath)

    if not filepath:
        raise ValidationError("filepath parameter is required")

    note = await ctx.deps.client.get_note(filepath)
    return note.content

@agent.tool
async def append_to_note(ctx: RunContext[ObsidianDeps],
                         filepath: str,
                         content: str) -> str:
    """
    Append content to the end of a note.

    Args:
        filepath: Path to the note
        content: Content to append

    Returns:
        Success message
    """
    logger.info("append_to_note", filepath=filepath)

    if not filepath:
        raise ValidationError("filepath parameter is required")
    if not content:
        raise ValidationError("content parameter is required")

    await ctx.deps.client.append_note(filepath, content)
    return f"Successfully appended content to {filepath}"

# main.py
import asyncio
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

settings = ObsidianSettings()
deps = ObsidianDeps(settings)

class ChatRequest(BaseModel):
    message: str

class ChatResponse(BaseModel):
    response: str
    data: Any | None = None

@app.post("/chat", response_model=ChatResponse)
async def chat(request: ChatRequest):
    try:
        result = await agent.run(request.message, deps=deps)
        return ChatResponse(
            response=result.data,
            data=result.all_messages()
        )
    except ToolError as e:
        raise HTTPException(status_code=400, detail=e.message)
    except Exception as e:
        logger.error("chat_failed", error=str(e))
        raise HTTPException(status_code=500, detail="Internal server error")

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

---

## 14. Summary & Key Takeaways

### What the MCP Obsidian Server Does Well

1. ✅ **Clear Tool Separation** - Each tool has a single, well-defined purpose
2. ✅ **Handler Pattern** - Extensible architecture, easy to add new tools
3. ✅ **Error Handling** - Multi-layer error handling with context
4. ✅ **Configuration** - Environment-based configuration with defaults
5. ✅ **Safety** - Destructive operations require confirmation
6. ✅ **Async Support** - Built for concurrent operations
7. ✅ **Type Hints** - Good use of Python type annotations

### What Could Be Improved

1. ❌ **Type Safety** - Not using Pydantic for validation
2. ❌ **Multiple Vaults** - Limited to single vault
3. ❌ **SSL Support** - Limited SSL configuration options
4. ❌ **Error Messages** - Could be more descriptive
5. ❌ **Testing** - No visible test suite
6. ❌ **Documentation** - Limited API documentation
7. ❌ **Timeout Handling** - Fixed timeouts, not configurable

### Apply to Your Project

1. **Use the Handler Pattern** for tool organization
2. **Implement Tool Registry** for extensibility
3. **Add Pydantic Models** for type safety
4. **Use Async/Await** throughout
5. **Layer Error Handling** (validation → execution → server)
6. **Environment Configuration** with Pydantic Settings
7. **Structured Logging** for debugging
8. **Safety Checks** for destructive operations
9. **Clear Tool Descriptions** for AI understanding
10. **Consistent Return Formats** for predictability

---

## 15. Additional Resources

- **Repository:** https://github.com/MarkusPfundstein/mcp-obsidian
- **MCP Protocol:** https://modelcontextprotocol.io/
- **Obsidian Local REST API:** https://github.com/coddingtonbear/obsidian-local-rest-api
- **Pydantic AI:** https://ai.pydantic.dev/
- **FastAPI:** https://fastapi.tiangolo.com/

---

## Conclusion

The MCP Obsidian server provides an excellent reference implementation for building AI agent tools. Its handler-based architecture, clear tool separation, and robust error handling make it a solid foundation for understanding how to structure tool-based AI systems.

The key patterns to adopt are:
- **Handler-based architecture** for extensibility
- **Registry pattern** for tool management
- **Multi-layer error handling** for robustness
- **Environment configuration** for flexibility
- **Async operations** for performance

By combining these patterns with Pydantic's type safety and validation capabilities, you can build a robust, type-safe AI agent system for interacting with Obsidian (or any other system) through a clean, well-structured API.
