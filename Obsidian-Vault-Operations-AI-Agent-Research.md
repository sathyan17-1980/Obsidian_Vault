# Obsidian Vault Operations for AI Agents: Comprehensive Research & Analysis

**Document Generated**: 2025-11-23
**Research Focus**: Best practices and patterns for programmatic Obsidian vault operations that an AI agent should support
**Status**: Complete Research & Analysis

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Core Vault Operations (Categorized)](#core-vault-operations-categorized)
3. [MVP vs Phase 2/3 Classification](#mvp-vs-phase-23-classification)
4. [Complexity Assessment](#complexity-assessment)
5. [Dependencies Between Operations](#dependencies-between-operations)
6. [Natural Language Query Examples](#natural-language-query-examples)
7. [Technical Implementation Considerations](#technical-implementation-considerations)
8. [Analysis of Existing Tools](#analysis-of-existing-tools)
9. [User Expectations & Workflows](#user-expectations--workflows)
10. [Recommendations & Next Steps](#recommendations--next-steps)

---

## Executive Summary

This research analyzes best practices for programmatic Obsidian vault operations that AI agents should support. Based on analysis of existing tools (Obsidian Local REST API, MCP servers, automation plugins) and real-world user workflows, this document categorizes 40+ vault operations into MVP and advanced tiers, assesses implementation complexity, and provides natural language query examples.

### Key Findings

1. **Essential MVP Operations** (8 core operations): Basic CRUD, simple search, and list operations form the foundation
2. **Performance Critical**: Metadata/tag searches should leverage cache (5-10x faster than full-text)
3. **User Priority**: Task management, daily notes, and search capabilities are most requested
4. **Architecture Pattern**: REST API + filesystem access hybrid provides best performance
5. **Natural Language Gap**: Users expect conversational queries like "find notes about X from last month" but most tools require exact syntax

### Implementation Priorities

- **Phase 1 (MVP)**: Core CRUD + basic search + metadata operations (8 operations)
- **Phase 2**: Advanced search + graph operations + templates (12 operations)
- **Phase 3**: Analytics + bulk operations + custom workflows (15+ operations)

---

## Core Vault Operations (Categorized)

### Category 1: Core CRUD Operations

#### 1.1 Create Operations
- **Create Note**: Create a new markdown note with optional frontmatter
- **Create Note from Template**: Create note using predefined template
- **Create Folder**: Create directory structure
- **Create Daily Note**: Create or open daily note for specific date
- **Create Periodic Note**: Create weekly/monthly/yearly notes

#### 1.2 Read Operations
- **Read Note Content**: Get full content of a note
- **Read Note Metadata**: Get frontmatter/metadata only
- **Read Active File**: Get currently open file content
- **List Files in Vault**: List all files/folders (with filters)
- **List Files in Folder**: List files in specific directory
- **Get File Statistics**: Get word count, character count, creation date, etc.

#### 1.3 Update Operations
- **Update Note (Full)**: Replace entire note content
- **Update Frontmatter**: Modify YAML frontmatter
- **Append Content**: Add content to end of note
- **Prepend Content**: Add content to beginning of note
- **Patch Content**: Insert content at specific location (heading/block reference)
- **Search and Replace**: Find and replace text in note(s)

#### 1.4 Delete Operations
- **Delete Note**: Remove a note
- **Delete Folder**: Remove directory and contents
- **Move Note**: Move note to different folder
- **Rename Note**: Change note name

---

### Category 2: Search Operations

#### 2.1 Basic Search
- **Full-Text Search**: Search all note content (regex supported)
- **File Name Search**: Search by note title/filename
- **Folder Search**: Search within specific folder(s)

#### 2.2 Advanced Search
- **Tag Search**: Find notes by tag (including nested tags)
- **Metadata Search**: Search by frontmatter fields
- **Date Range Search**: Find notes created/modified in date range
- **Multi-Criteria Search**: Combine text + tags + metadata + dates
- **Regex Search**: Advanced pattern matching

#### 2.3 Specialized Search
- **Dataview Queries**: SQL-like queries across vault
- **Semantic Search**: AI-powered similarity search
- **Link Search**: Find notes linking to/from specific note

---

### Category 3: Metadata Operations

#### 3.1 Frontmatter Management
- **Add Frontmatter Field**: Add new metadata field
- **Update Frontmatter Field**: Modify existing field
- **Delete Frontmatter Field**: Remove metadata field
- **Bulk Update Frontmatter**: Update multiple notes' metadata

#### 3.2 Tag Management
- **Add Tags**: Add tags to note (inline or frontmatter)
- **Remove Tags**: Remove specific tags
- **List All Tags**: Get all tags in vault
- **List Tags in Note**: Get tags for specific note
- **Rename Tags**: Bulk rename tags across vault

#### 3.3 Property Management
- **Get Properties**: List all property keys in vault
- **Update Properties**: Modify property values
- **Property Schema**: Define property types/validation

---

### Category 4: Graph & Relationship Operations

#### 4.1 Link Operations
- **Get Backlinks**: Find notes linking to current note
- **Get Outbound Links**: Find links from current note
- **Get Unlinked References**: Find mentions without links
- **Create Link**: Add wikilink or markdown link
- **Resolve Link**: Convert relative to absolute path

#### 4.2 Graph Analysis
- **Get Note Connections**: Get all connected notes (depth N)
- **Find Orphaned Notes**: Notes with no links in/out
- **Find Hub Notes**: Notes with many connections
- **Graph Query**: Query graph structure (e.g., "find notes 2 hops from X")

#### 4.3 Map of Content (MOC)
- **Generate MOC**: Create map of content from backlinks
- **Update MOC**: Auto-refresh MOC with new backlinks
- **List Related Notes**: Find notes related by topic/tags

---

### Category 5: Specialized Operations

#### 5.1 Task Management
- **List Tasks**: Get all tasks from vault
- **List Tasks by Status**: Filter by done/incomplete
- **List Tasks by Tag**: Get tasks with specific tags
- **List Tasks by Date**: Due dates, scheduled dates
- **Update Task Status**: Mark task complete/incomplete
- **Create Task**: Add task to note

#### 5.2 Daily Notes & Periodic Notes
- **Get Today's Daily Note**: Open/create today's note
- **Get Daily Note by Date**: Access specific date's note
- **List Daily Notes**: Get all daily notes in range
- **Create Weekly Note**: Create/open weekly note
- **Navigate Periodic Notes**: Previous/next day/week/month

#### 5.3 Template Operations
- **List Templates**: Get all available templates
- **Apply Template**: Insert template into note
- **Create Template**: Save note as template
- **Template with Variables**: Process Templater syntax

#### 5.4 Command Execution
- **List Commands**: Get all available Obsidian commands
- **Execute Command**: Run specific command
- **Get Command Status**: Check if command is available

---

### Category 6: Bulk & Advanced Operations

#### 6.1 Bulk Operations
- **Bulk Create**: Create multiple notes at once
- **Bulk Update**: Update multiple notes
- **Bulk Tag**: Add/remove tags from multiple notes
- **Bulk Move**: Move multiple notes to folder
- **Bulk Delete**: Delete multiple notes

#### 6.2 Analytics & Insights
- **Vault Statistics**: Total notes, total words, etc.
- **Tag Statistics**: Tag usage counts
- **Link Statistics**: Most linked notes, orphans count
- **Activity Timeline**: Creation/modification patterns
- **Note Health**: Find broken links, missing backlinks

#### 6.3 Import/Export
- **Export Note**: Export to PDF, HTML, etc.
- **Import from URL**: Fetch and create note from web
- **Import from File**: Import external markdown
- **Sync Operations**: Backup, restore, sync to remote

---

## MVP vs Phase 2/3 Classification

### MVP (Phase 1) - Essential Operations
**Goal**: Basic note management and search
**Timeline**: Weeks 1-4
**Operations**: 8 core operations

| Operation | Category | Why MVP |
|-----------|----------|---------|
| **Create Note** | CRUD | Fundamental capability |
| **Read Note Content** | CRUD | Most common operation |
| **Update Note (Full)** | CRUD | Essential for editing |
| **Delete Note** | CRUD | Complete CRUD set |
| **List Files in Vault** | Read | Discovery & navigation |
| **Full-Text Search** | Search | Core user need |
| **Tag Search** | Search | Common workflow |
| **Read Note Metadata** | Metadata | Frontmatter is critical |

**Success Criteria**:
- Can create, read, update, delete notes
- Can find notes by content or tags
- Can list and navigate vault
- Performance: <100ms for single note operations, <1s for searches in 1000-note vault

---

### Phase 2 - Enhanced Capabilities
**Goal**: Power user features and automation
**Timeline**: Weeks 5-8
**Operations**: 12 additional operations

| Operation | Category | Why Phase 2 |
|-----------|----------|-------------|
| **Append/Prepend Content** | CRUD | Non-destructive editing |
| **Patch Content** | CRUD | Precise insertions |
| **Update Frontmatter** | Metadata | Granular control |
| **Add/Remove Tags** | Metadata | Tag management |
| **Get Backlinks** | Graph | Note relationships |
| **Create Daily Note** | Specialized | Popular workflow |
| **Apply Template** | Specialized | Automation need |
| **Search and Replace** | Update | Bulk editing |
| **Multi-Criteria Search** | Search | Advanced queries |
| **List Tasks** | Specialized | Task management |
| **Move Note** | CRUD | Organization |
| **Folder Operations** | CRUD | Structure management |

**Success Criteria**:
- Can perform surgical edits without replacing entire notes
- Can manage metadata independently
- Can work with daily notes and templates
- Can find relationships between notes
- Performance: <500ms for graph operations on 1000-note vault

---

### Phase 3 - Advanced Features
**Goal**: Analytics, bulk operations, and advanced workflows
**Timeline**: Weeks 9-12+
**Operations**: 15+ advanced operations

| Operation Category | Operations | Why Phase 3 |
|-------------------|------------|-------------|
| **Bulk Operations** | Bulk create, update, delete, tag, move | Efficiency for power users |
| **Advanced Graph** | Graph queries, hub detection, orphan finding | Complex analysis |
| **Analytics** | Statistics, health checks, activity timelines | Insights & maintenance |
| **Advanced Search** | Semantic search, Dataview queries, regex | Power user needs |
| **Automation** | Command execution, custom workflows | Advanced integration |
| **Import/Export** | Multiple formats, web import, sync | Data portability |
| **MOC Operations** | Generate/update MOCs, auto-organization | Advanced PKM |
| **Property Management** | Schema, validation, bulk updates | Data governance |

**Success Criteria**:
- Can perform operations on hundreds of notes efficiently
- Can analyze vault structure and health
- Can automate complex workflows
- Can integrate with external tools
- Performance: Bulk operations handle 100+ notes in <5s

---

## Complexity Assessment

### Low Complexity (1-2 weeks per operation)

**Single File CRUD Operations**
- **Create Note**: Simple file write with frontmatter
  - Dependencies: Filesystem access, YAML parsing
  - Edge cases: Duplicate names, invalid paths
  - Estimated effort: 1 week

- **Read Note Content**: Read file, parse frontmatter
  - Dependencies: Filesystem access, YAML parsing
  - Edge cases: Binary files, large files
  - Estimated effort: 1 week

- **Update Note (Full)**: Overwrite file content
  - Dependencies: Read operation
  - Edge cases: Concurrent edits, backup handling
  - Estimated effort: 1 week

- **Delete Note**: Remove file
  - Dependencies: Filesystem access
  - Edge cases: Confirmation, undo, broken links
  - Estimated effort: 1 week

**Basic List Operations**
- **List Files in Vault**: Directory traversal
  - Dependencies: Filesystem access, filtering
  - Edge cases: Hidden files, symlinks, permissions
  - Estimated effort: 1 week

---

### Medium Complexity (2-4 weeks per operation)

**Search Operations**
- **Full-Text Search**: Scan all files, match content
  - Dependencies: Filesystem, regex engine, caching
  - Edge cases: Large vaults, special characters, performance
  - Estimated effort: 3 weeks
  - Optimization: Index-based search can reduce to <1s

- **Tag Search**: Parse frontmatter and inline tags
  - Dependencies: Metadata cache, tag parser
  - Edge cases: Nested tags, tag variations (#tag vs tag:)
  - Estimated effort: 2 weeks

- **Multi-Criteria Search**: Combine multiple filters
  - Dependencies: All search operations, query parser
  - Edge cases: Complex boolean logic, performance
  - Estimated effort: 4 weeks

**Metadata Operations**
- **Update Frontmatter**: Parse, modify, serialize YAML
  - Dependencies: YAML parser, schema validation
  - Edge cases: Invalid YAML, multiline values, special characters
  - Estimated effort: 2 weeks

- **Tag Management**: Add/remove tags (frontmatter + inline)
  - Dependencies: Note parser, tag detection
  - Edge cases: Tag placement, duplicate tags, nested tags
  - Estimated effort: 3 weeks

**Partial Update Operations**
- **Append/Prepend Content**: Add to start/end
  - Dependencies: Read, write operations
  - Edge cases: Frontmatter preservation, newlines
  - Estimated effort: 2 weeks

- **Patch Content**: Insert at specific location
  - Dependencies: Heading/block reference parser
  - Edge cases: Missing headings, ambiguous references
  - Estimated effort: 3 weeks

---

### High Complexity (4-8 weeks per operation)

**Graph Operations**
- **Get Backlinks**: Find all notes linking to target
  - Dependencies: Link parser, cache, graph index
  - Edge cases: Alias links, broken links, circular references
  - Estimated effort: 4 weeks
  - Note: Requires building and maintaining graph index

- **Graph Query**: Query graph structure with depth/filters
  - Dependencies: Graph index, query language, traversal algorithms
  - Edge cases: Large graphs, circular paths, performance
  - Estimated effort: 6 weeks

- **Semantic Search**: AI-powered similarity search
  - Dependencies: Embedding model, vector database, chunking strategy
  - Edge cases: Long notes, update frequency, cost/performance
  - Estimated effort: 8 weeks

**Advanced Search**
- **Dataview Queries**: SQL-like query language
  - Dependencies: Query parser, AST, execution engine
  - Edge cases: Complex queries, aggregations, performance
  - Estimated effort: 8+ weeks
  - Note: May be better to integrate existing Dataview plugin

**Bulk Operations**
- **Bulk Update**: Update multiple notes with single command
  - Dependencies: All update operations, transaction handling
  - Edge cases: Partial failures, rollback, performance
  - Estimated effort: 4 weeks

**Template Operations**
- **Apply Template with Variables**: Process Templater syntax
  - Dependencies: Template parser, variable substitution, date handling
  - Edge cases: Complex templates, dynamic content, JavaScript execution
  - Estimated effort: 6 weeks
  - Note: May integrate with Templater plugin

---

### Very High Complexity (8+ weeks per operation)

**Analytics & Insights**
- **Vault Health Check**: Find broken links, orphans, inconsistencies
  - Dependencies: Full graph index, link resolver, all metadata
  - Edge cases: Large vaults, external links, attachment links
  - Estimated effort: 8 weeks

- **Activity Timeline**: Analyze creation/modification patterns
  - Dependencies: File metadata, historical data, visualization
  - Edge cases: Git history, external edits, timezone handling
  - Estimated effort: 6 weeks

**Import/Export**
- **Web Import**: Fetch URL, convert to markdown, extract metadata
  - Dependencies: HTTP client, HTML parser, metadata extraction
  - Edge cases: Paywalls, JavaScript content, images, formatting
  - Estimated effort: 8 weeks

**Command Execution**
- **Execute Obsidian Commands**: Programmatically trigger commands
  - Dependencies: Obsidian API or REST API plugin
  - Edge cases: Command availability, parameters, async execution
  - Estimated effort: 4 weeks (if REST API exists), 12+ weeks (if building from scratch)

---

## Dependencies Between Operations

### Dependency Graph

```
Core Infrastructure (Weeks 1-2)
├─ Filesystem Access
├─ YAML Parser
├─ Markdown Parser
├─ Configuration Management
└─ Error Handling

↓

Basic CRUD (Weeks 2-4)
├─ Create Note ────┐
├─ Read Note ──────┼─→ Update Note ──→ Delete Note
└─ List Files ─────┘

↓

Search Foundation (Weeks 3-5)
├─ Metadata Cache ─────┐
├─ File Scanner ───────┼─→ Full-Text Search
└─ Tag Parser ─────────┘        ├─→ Tag Search
                                 └─→ File Name Search

↓

Advanced Read/Write (Weeks 5-7)
├─ Append/Prepend ──────→ (depends on Read + Write)
├─ Patch Content ───────→ (depends on Heading Parser)
└─ Search & Replace ────→ (depends on Search + Update)

↓

Metadata Management (Weeks 6-8)
├─ Update Frontmatter ──→ (depends on YAML Parser)
├─ Add/Remove Tags ─────→ (depends on Tag Parser)
└─ Bulk Tag Operations ─→ (depends on Tag Management + Search)

↓

Graph & Relationships (Weeks 8-12)
├─ Link Index ──────────┐
├─ Link Parser ─────────┼─→ Get Backlinks
└─ Graph Builder ───────┘     ├─→ Graph Queries
                               └─→ Hub Detection

↓

Specialized Features (Weeks 10-14)
├─ Daily Notes ─────────→ (depends on Create + Template)
├─ Templates ───────────→ (depends on Create + Variables)
├─ Task Management ─────→ (depends on Search + Parser)
└─ Command Execution ───→ (depends on REST API or Plugin API)

↓

Advanced Features (Weeks 12-20+)
├─ Bulk Operations ─────→ (depends on all CRUD + transaction handling)
├─ Analytics ───────────→ (depends on Graph + Statistics)
├─ Semantic Search ─────→ (depends on Embeddings + Vector DB)
└─ Import/Export ───────→ (depends on parsers + converters)
```

### Critical Path Dependencies

1. **Foundation Layer** (Must build first)
   - Filesystem access
   - YAML parser
   - Markdown parser
   - Configuration/settings

2. **CRUD Layer** (Enables most other operations)
   - Read → enables all operations
   - Write → enables create/update
   - List → enables search/discovery

3. **Cache Layer** (Performance multiplier)
   - Metadata cache → enables fast tag/frontmatter search
   - Link index → enables graph operations
   - File index → enables instant file search

4. **Search Layer** (High-value, builds on cache)
   - Tag search → most requested
   - Full-text search → essential
   - Multi-criteria → combines all

5. **Graph Layer** (Requires substantial infrastructure)
   - Link parser → foundation
   - Graph index → performance
   - Query engine → usability

---

## Natural Language Query Examples

### MVP Operations (Phase 1)

#### Create Operations
- "Create a new note called 'Meeting Notes'"
- "Make a note about today's standup"
- "Create a note with the title 'Project Ideas' and add the tag #ideas"

#### Read Operations
- "Show me the note called 'Weekly Review'"
- "What's in my daily note?"
- "Read the content of 'Project Roadmap'"
- "List all notes in my vault"
- "What notes do I have in the Projects folder?"

#### Update Operations
- "Update my 'Goals 2025' note with new goals"
- "Change the content of 'Reading List'"
- "Replace the text in 'Draft Ideas'"

#### Delete Operations
- "Delete the note 'Old Ideas'"
- "Remove 'Archived Project' from my vault"

#### Search Operations
- "Find notes with the tag #important"
- "Search for notes mentioning 'machine learning'"
- "Find all notes tagged with #project"
- "Show me notes that contain 'API design'"

---

### Phase 2 Operations

#### Advanced Editing
- "Add this text to the end of my daily note"
- "Prepend a new task to my Todo list"
- "Insert this content under the 'Ideas' heading in my brainstorming note"
- "Find and replace 'old term' with 'new term' in all notes"

#### Metadata Management
- "Add the tag #review to this note"
- "Remove the tag #draft from my finished notes"
- "Update the status field to 'complete' in the frontmatter"
- "Add a due date to the frontmatter of this note"

#### Graph & Relationships
- "What notes link to this one?"
- "Show me all notes that reference 'Project Alpha'"
- "Find notes connected to my 'PKM System' note"
- "What notes link to my MOC?"

#### Daily Notes
- "Create today's daily note"
- "Open yesterday's daily note"
- "Show me the daily note from last Monday"
- "Create this week's weekly note"

#### Templates
- "Create a meeting note using the meeting template"
- "Apply the book notes template to this note"
- "Make a new project note from the project template"

#### Task Management
- "List all incomplete tasks"
- "Show me tasks tagged with #urgent"
- "Find tasks due this week"
- "Mark this task as complete"

---

### Phase 3 Operations

#### Bulk Operations
- "Add the tag #archive to all notes in the Old Projects folder"
- "Move all notes tagged #2024 to the Archive folder"
- "Update the status to 'reviewed' for all notes in the Inbox"
- "Delete all notes with the tag #draft and no links"

#### Advanced Search
- "Find notes similar to this one" (semantic search)
- "Show me all notes created last month with the tag #research"
- "Find notes that mention 'productivity' and were modified in the last week"
- "Run a Dataview query to show all books I rated 5 stars"

#### Graph Analytics
- "Show me orphaned notes with no links"
- "Find my most connected notes"
- "Which notes are hub notes in my knowledge graph?"
- "Find notes that are 2 connections away from 'Machine Learning MOC'"

#### Vault Analytics
- "How many notes do I have?"
- "What are my most used tags?"
- "Show me notes with broken links"
- "What's my writing activity for the last month?"
- "Find notes that haven't been updated in 6 months"

#### Import/Export
- "Import this article from the web into a note"
- "Create a note from this URL"
- "Export this note to PDF"
- "Backup my entire vault"

#### MOC Operations
- "Generate a Map of Content for all notes tagged #programming"
- "Update my PKM MOC with new backlinks"
- "Create an automatic index of all book notes"
- "Show me related notes for this topic"

#### Advanced Workflows
- "Find all draft notes without tags and add #needs-review"
- "Create a weekly review note with links to all notes from this week"
- "Generate a reading list from all notes tagged #article with rating > 4"
- "Find meeting notes from last quarter and create a summary"

---

## Technical Implementation Considerations

### Architecture Patterns

Based on research, there are three main approaches for implementing Obsidian vault operations:

#### 1. REST API via Obsidian Local REST API Plugin
**How it works**: Install plugin in Obsidian, communicate over HTTPS with API keys

**Pros**:
- Official plugin with community support
- Secure HTTPS + API key authentication
- Access to Obsidian-specific features (commands, plugins)
- Can trigger Obsidian UI updates in real-time
- Interactive API documentation available

**Cons**:
- Requires Obsidian to be running
- Additional setup/configuration for users
- Depends on plugin maintenance
- Network overhead (localhost, but still HTTP)

**Best for**:
- Users who actively use Obsidian desktop app
- Real-time collaboration with Obsidian UI
- Command execution and plugin integration

**API Endpoints** (from Local REST API):
- `/vault/` - List notes, search
- `/vault/{path}` - Read, create, update, delete notes
- `/active/` - Operations on currently active file
- `/periodic/{period}` - Daily/weekly/monthly notes
- `/commands/` - List and execute commands
- `/search/` - Advanced search

**Performance**: Good for individual operations, slower for bulk (HTTP overhead)

---

#### 2. Direct Filesystem Access
**How it works**: Read/write vault as markdown files directly

**Pros**:
- No dependencies on Obsidian running
- Fastest performance (5-10x faster than REST API)
- Full control over caching and indexing
- Works with any text editor, not just Obsidian
- Can build persistent SQLite index for fast searches

**Cons**:
- Must implement all parsing (frontmatter, links, tags)
- No access to Obsidian commands or plugins
- Must handle concurrent access carefully
- No real-time UI updates in Obsidian
- Need to watch for external file changes

**Best for**:
- Headless/server environments
- Batch operations and analytics
- High-performance requirements
- Building custom tools

**Implementation details**:
- Use filesystem watcher for change detection
- Build SQLite index for metadata and links
- Implement incremental indexing (only reindex changed files)
- Use async I/O for concurrent operations
- Cache parsed frontmatter and metadata

**Performance**: Excellent for bulk operations, enables instant search with index

---

#### 3. Hybrid Approach (Recommended)
**How it works**: Filesystem for read/write + cache, REST API for commands

**Pros**:
- Best performance for most operations
- Can execute Obsidian commands when needed
- Persistent indexing for instant search
- Works even when Obsidian is closed (except commands)

**Cons**:
- More complex implementation
- Need to handle two code paths
- Cache synchronization required

**Architecture**:
```
AI Agent
    ├─→ Filesystem Layer (for CRUD, search, metadata)
    │   ├─ SQLite Index (metadata, tags, links)
    │   ├─ File Watcher (detect changes)
    │   └─ Async I/O (concurrent operations)
    │
    └─→ REST API Layer (for commands, active file)
        ├─ Command Execution
        ├─ Active File Operations
        └─ Real-time UI Updates
```

**Best for**: Production AI agents that need both performance and features

---

### Performance Optimization Strategies

#### 1. Caching & Indexing

**Metadata Cache** (Critical for performance)
```python
# SQLite schema for metadata cache
CREATE TABLE notes (
    path TEXT PRIMARY KEY,
    title TEXT,
    created_at TIMESTAMP,
    modified_at TIMESTAMP,
    frontmatter JSON,  -- Store as JSON blob
    content_hash TEXT  -- Detect changes
);

CREATE TABLE tags (
    tag TEXT,
    note_path TEXT,
    location TEXT,  -- 'frontmatter' or 'inline'
    FOREIGN KEY (note_path) REFERENCES notes(path)
);

CREATE INDEX idx_tags_tag ON tags(tag);
CREATE INDEX idx_notes_modified ON notes(modified_at);
```

**Benefits**:
- Tag search: 10-50ms instead of 2-5s (50-250x faster)
- Metadata queries: Instant vs. full vault scan
- Only reindex changed files (incremental updates)

**Implementation**:
- Hash file content to detect changes
- Update index on file save/change
- Invalidate cache on external changes
- Background indexing for large vaults

---

#### 2. Search Optimization

**Full-Text Search Performance**:
- **Without Index**: Linear scan, 2-10s for 10,000 notes
- **With SQLite FTS5**: 50-200ms for same vault
- **Recommendation**: Build FTS5 index for content search

```python
# SQLite FTS5 for full-text search
CREATE VIRTUAL TABLE notes_fts USING fts5(
    path,
    title,
    content,
    content=notes  -- Link to notes table
);

# Search query (instant results)
SELECT path, title, snippet(notes_fts, 2, '<mark>', '</mark>', '...', 32)
FROM notes_fts
WHERE notes_fts MATCH 'machine AND learning'
ORDER BY rank;
```

**Tag Search Optimization**:
- Use metadata cache instead of full-text search
- Pre-parse all tags on indexing
- Handle both frontmatter tags and inline #tags
- Support nested tags (#parent/child)

**Multi-Criteria Search**:
- Combine indexed queries (tags + metadata + dates)
- Only do full-text search as final filter
- Use query planner to optimize order

---

#### 3. Graph Operations Performance

**Link Index**:
```python
CREATE TABLE links (
    source_path TEXT,
    target_path TEXT,
    link_type TEXT,  -- 'wikilink', 'markdown', 'embed'
    link_text TEXT,
    FOREIGN KEY (source_path) REFERENCES notes(path)
);

CREATE INDEX idx_links_source ON links(source_path);
CREATE INDEX idx_links_target ON links(target_path);
CREATE INDEX idx_links_both ON links(source_path, target_path);
```

**Benefits**:
- Backlinks: Instant (index lookup)
- Graph traversal: Fast (no file parsing)
- Orphan detection: Single query

**Graph Queries**:
- Use graph algorithms on indexed data
- Cache graph metrics (degree, betweenness)
- Build adjacency list for traversal

---

#### 4. Bulk Operations

**Transaction-based Updates**:
```python
# Atomic bulk operations
async def bulk_update_tags(notes: list[str], add_tags: list[str]):
    async with db.transaction():
        for note in notes:
            content = await read_note(note)
            updated = add_frontmatter_tags(content, add_tags)
            await write_note(note, updated)
            await update_cache(note, updated)
        # All-or-nothing commit
```

**Parallelization**:
- Use async I/O for concurrent reads/writes
- Process independent files in parallel
- Batch database updates
- Show progress for long operations

---

### Error Handling & Edge Cases

#### Common Edge Cases

1. **Duplicate Note Names**
   - Check for existing file before create
   - Offer auto-rename (note-1.md, note-2.md)
   - Or return error for user to resolve

2. **Concurrent Modifications**
   - Detect via file hash/modified time
   - Offer merge or overwrite options
   - Lock files during multi-step operations

3. **Invalid Frontmatter**
   - Validate YAML syntax before write
   - Preserve invalid frontmatter (don't delete)
   - Show warnings but allow operation

4. **Broken Links**
   - Track broken links in health check
   - Offer auto-fix for renamed notes
   - Update backlinks on rename/move

5. **Large Files**
   - Stream content for files >1MB
   - Warn on very large files (>10MB)
   - Chunk for embeddings/processing

6. **Special Characters**
   - Handle Unicode properly
   - Escape special characters in filenames
   - Support international characters

7. **Nested Tags**
   - Parse #parent/child correctly
   - Support tag hierarchy queries
   - Consistent handling in frontmatter vs inline

---

### Security Considerations

#### API Authentication
- **REST API**: API key in HTTP header
- **Filesystem**: File permissions, user isolation
- **Rate Limiting**: Prevent abuse
- **Input Validation**: Sanitize all user input

#### Sensitive Data
- **Never log**: File content, API keys, passwords
- **Encrypt**: Sensitive frontmatter fields (optional)
- **Access Control**: Per-vault, per-folder permissions
- **Audit Log**: Track all operations

#### Safe Operations
- **Confirmations**: Delete, bulk operations
- **Undo/Backup**: Version history or git
- **Dry Run**: Preview changes before applying
- **Rollback**: Transaction-based updates

---

### Scalability Considerations

#### Vault Size Performance

**Small Vaults (1-1,000 notes)**:
- All operations fast without optimization
- In-memory caching sufficient
- Full vault scan acceptable for search

**Medium Vaults (1,000-10,000 notes)**:
- Need metadata cache for tags/frontmatter
- FTS index recommended for search
- Graph index recommended for backlinks

**Large Vaults (10,000-100,000 notes)**:
- Metadata cache mandatory
- FTS index mandatory
- Incremental indexing required
- Background indexing on startup
- Consider partitioning by folder

**Very Large Vaults (100,000+ notes)**:
- All optimizations mandatory
- Partition data by date/topic
- Lazy loading for graph operations
- Consider external search engine (e.g., Meilisearch)
- Distributed indexing for multi-GB vaults

#### Benchmarks from Research

From Obsidian forums and user reports:

| Vault Size | Without Index | With Index | Operation |
|------------|---------------|------------|-----------|
| 1,000 notes | 200ms | 50ms | Tag search |
| 10,000 notes | 2-5s | 100ms | Tag search |
| 30,000 notes | 10-30s | 200ms | Tag search |
| 10,000 notes | 5-10s | 150ms | Full-text search |
| Any size | Instant | Instant | Read single note |
| Any size | Instant | Instant | Metadata from cache |

**Key takeaway**: Indexing provides 10-100x performance improvement for large vaults

---

## Analysis of Existing Tools

### 1. Obsidian Local REST API Plugin

**Source**: [GitHub - coddingtonbear/obsidian-local-rest-api](https://github.com/coddingtonbear/obsidian-local-rest-api)
**Documentation**: [Interactive API Docs](https://coddingtonbear.github.io/obsidian-local-rest-api/)

**Overview**:
The most mature and widely-used REST API for Obsidian, providing HTTPS interface with API key authentication.

**Core Features**:
- ✅ Full CRUD operations on notes
- ✅ List vault contents
- ✅ Search (simple and advanced)
- ✅ Periodic notes (daily/weekly/monthly)
- ✅ Command execution
- ✅ Active file operations
- ✅ PATCH for inserting content at headings/blocks
- ✅ OpenAPI spec available at `/openapi.yaml`

**Strengths**:
- Well-documented with interactive docs
- Secure (HTTPS + API keys)
- Active maintenance
- Extension system for other plugins
- Works with Obsidian's internal state

**Limitations**:
- Requires Obsidian running
- HTTP overhead for bulk operations
- Limited graph/relationship operations
- No built-in semantic search
- No bulk operations endpoint

**Best Use Cases**:
- Real-time integration with Obsidian UI
- Command execution
- Single-note operations
- Daily notes workflow

**API Example**:
```bash
# Read note
GET https://localhost:27124/vault/Meeting%20Notes.md
Authorization: Bearer <api-key>

# Create note
PUT https://localhost:27124/vault/New%20Note.md
Content-Type: application/json
{
  "content": "# New Note\n\nContent here"
}

# Search
POST https://localhost:27124/search/
{
  "query": "machine learning",
  "contextLength": 100
}

# Execute command
POST https://localhost:27124/commands/daily-notes
```

---

### 2. Obsidian MCP Servers

**Sources**:
- [cyanheads/obsidian-mcp-server](https://github.com/cyanheads/obsidian-mcp-server) - Most comprehensive
- [MarkusPfundstein/mcp-obsidian](https://github.com/MarkusPfundstein/mcp-obsidian)
- [StevenStavrakis/obsidian-mcp](https://github.com/StevenStavrakis/obsidian-mcp)
- [tokidoo-mcp-obsidian-advanced](https://lobehub.com/mcp/tokidoo-mcp-obsidian-advanced)

**Overview**:
MCP (Model Context Protocol) servers enable AI agents to interact with Obsidian vaults through a standardized interface.

**cyanheads/obsidian-mcp-server Features**:
- ✅ Read, update, search, manage notes
- ✅ Tag and frontmatter management
- ✅ Search and replace operations
- ✅ List notes with filtering
- ✅ Modular architecture
- ✅ Robust error handling and logging
- ✅ Security features

**tokidoo-mcp-obsidian-advanced Features**:
- ✅ All standard operations
- ✅ **Vault tree structure discovery** (unique)
- ✅ **NetworkX graph of all notes and connections** (unique)
- ✅ Advanced link analysis
- ✅ Structural context understanding

**Strengths**:
- Designed specifically for AI agents
- Natural language friendly
- Standardized MCP interface
- Some include graph operations
- Bridge to Local REST API

**Limitations**:
- Still depends on Local REST API plugin
- Varying feature sets across implementations
- Less mature than REST API
- Documentation varies by implementation

**Best Use Cases**:
- AI agent integration (Claude, ChatGPT)
- Natural language vault management
- Graph analysis (tokidoo-advanced)
- Conversational note-taking

**Real-World Usage**:
- User report: "Wrote 2,000 words in 90 minutes with Obsidian + MCP + Claude"
- Common workflow: "Connect Claude Desktop to Obsidian via MCP for real-time note assistance"

---

### 3. Obsidian Plugin API (Internal)

**Source**: [GitHub - obsidianmd/obsidian-api](https://github.com/obsidianmd/obsidian-api)
**Documentation**: [Developer Docs](https://docs.obsidian.md/)

**Overview**:
TypeScript API for building Obsidian plugins, provides deepest integration but requires running as plugin.

**Core Interfaces**:
- **App**: Global entry point to Obsidian
- **Vault**: File and folder operations
- **Workspace**: UI and pane management
- **MetadataCache**: Cached metadata (headings, links, tags, blocks)
- **FileManager**: High-level file operations

**Key Features**:
- ✅ Full access to Obsidian internals
- ✅ MetadataCache for instant metadata access
- ✅ Graph data structures
- ✅ Event system for file changes
- ✅ UI integration
- ✅ Command registration

**Vault API Operations**:
```typescript
// Read
const content = await vault.read(file);
const metadata = metadataCache.getFileCache(file);

// Write
await vault.create(path, content);
await vault.modify(file, newContent);
await vault.delete(file);

// Metadata
const tags = metadataCache.getTags();
const backlinks = metadataCache.getBacklinksForFile(file);
```

**Strengths**:
- Fastest access to metadata (already cached)
- Real-time updates
- Full feature access
- Type-safe TypeScript

**Limitations**:
- Must run as Obsidian plugin
- Requires plugin development knowledge
- Desktop app only (not mobile API)
- Can't run headless

**Best Use Cases**:
- Building Obsidian plugins
- Deep integration with UI
- Leveraging existing cache
- Real-time features

---

### 4. ObsidianTools (Python)

**Source**: [GitHub - mfarragher/obsidiantools](https://github.com/mfarragher/obsidiantools)

**Overview**:
Python package for analyzing Obsidian vaults as data, focused on analytics rather than CRUD.

**Features**:
- ✅ Vault statistics (note count, file count)
- ✅ Graph analysis (connect notes, backlinks/wikilinks)
- ✅ Metadata export to Pandas DataFrames
- ✅ NetworkX graph integration
- ✅ Canvas file support

**API Example**:
```python
import obsidiantools.api as otools

# Load vault
vault = otools.Vault('/path/to/vault').connect()

# Get statistics
vault.get_note_metadata()  # Pandas DataFrame with backlinks, wikilinks
vault.get_media_file_metadata()

# Graph analysis
graph = vault.graph  # NetworkX graph object
```

**Strengths**:
- Pure Python, no dependencies on Obsidian running
- Great for analytics and data science
- Pandas + NetworkX integration
- Read-only focus (safe)

**Limitations**:
- Read-only (no CRUD operations)
- No search functionality
- Analytics focus, not automation
- Graph only (no full-text)

**Best Use Cases**:
- Vault analytics
- Data science projects
- Graph visualization
- Knowledge graph analysis

---

### 5. Dataview Plugin

**Source**: [GitHub - blacksmithgu/obsidian-dataview](https://github.com/blacksmithgu/obsidian-dataview)

**Overview**:
Most popular Obsidian plugin for querying vault as database using SQL-like syntax.

**Features**:
- ✅ SQL-like query language
- ✅ Filter by tags, frontmatter, dates
- ✅ Aggregations (count, group by)
- ✅ Four output types: LIST, TABLE, TASK, CALENDAR
- ✅ JavaScript API for complex queries

**Query Examples**:
```javascript
// List notes with tag, sorted by date
LIST
FROM #project
SORT file.mtime DESC

// Table of books with rating
TABLE rating, author, date-read
FROM #book
WHERE rating >= 4
SORT rating DESC

// Tasks due this week
TASK
WHERE due >= date(today) AND due <= date(today) + dur(7 days)

// JavaScript API
dv.pages("#book")
  .where(p => p.rating >= 4)
  .sort(p => p.rating, 'desc')
```

**Strengths**:
- Most powerful query capabilities
- User-friendly syntax
- Widely used (1M+ downloads)
- Supports complex aggregations

**Limitations**:
- Obsidian plugin only (not programmatic API)
- Can be slow on large vaults without persistent cache
- JavaScript queries have learning curve

**Best Use Cases**:
- Complex queries within Obsidian
- Dynamic MOCs
- Task dashboards
- Note aggregations

**Relevance for AI Agent**:
- Could expose Dataview query execution via API
- Natural language → Dataview query translation
- Leverage existing query engine

---

### 6. Templater Plugin

**Source**: [Templater Documentation](https://silentvoid13.github.io/Templater/)

**Overview**:
Advanced template plugin with variables, functions, and JavaScript execution.

**Features**:
- ✅ Template variables (date, time, file name)
- ✅ JavaScript execution in templates
- ✅ System commands
- ✅ Prompt user for input
- ✅ Template folder organization

**Template Example**:
```markdown
---
created: <% tp.file.creation_date() %>
modified: <% tp.file.last_modified_date() %>
tags: [<% tp.system.prompt("Enter tags") %>]
---

# <% tp.file.title %>

## Meeting Details
- Date: <% tp.date.now("YYYY-MM-DD") %>
- Attendees:

## Notes


## Action Items
- [ ]
```

**Strengths**:
- Most powerful templating system
- JavaScript execution
- User prompts for dynamic content
- Wide adoption

**Limitations**:
- Complex syntax for advanced features
- Obsidian plugin only
- Security considerations with JS execution

**Best Use Cases**:
- Dynamic templates
- Automated note creation
- Daily notes with computed content

**Relevance for AI Agent**:
- Could integrate for template processing
- Or build simpler variable substitution
- Natural language → template selection

---

### Comparison Matrix

| Tool | CRUD | Search | Metadata | Graph | Templates | Bulk Ops | Analytics | AI-Ready |
|------|------|--------|----------|-------|-----------|----------|-----------|----------|
| **Local REST API** | ✅ Full | ✅ Good | ✅ Good | ⚠️ Limited | ⚠️ Basic | ❌ | ❌ | ✅ |
| **MCP Servers** | ✅ Full | ✅ Good | ✅ Good | ⚠️ Some | ❌ | ⚠️ Limited | ❌ | ✅✅ |
| **Plugin API** | ✅ Full | ✅ Excellent | ✅✅ Cached | ✅ Good | ⚠️ Via plugins | ⚠️ Manual | ⚠️ Manual | ❌ |
| **ObsidianTools** | ❌ | ❌ | ✅ Read | ✅ Good | ❌ | ❌ | ✅✅ | ⚠️ |
| **Dataview** | ❌ | ✅✅ SQL | ✅✅ | ⚠️ Links | ❌ | ❌ | ✅ Queries | ⚠️ |
| **Templater** | ⚠️ Create | ❌ | ⚠️ Read | ❌ | ✅✅ | ❌ | ❌ | ❌ |

**Legend**:
- ✅✅ = Excellent
- ✅ = Good
- ⚠️ = Limited/Partial
- ❌ = Not supported

---

### Recommendation: Hybrid Architecture

**For Production AI Agent**, combine strengths:

1. **Core Operations**: Direct filesystem access + SQLite cache
   - Fastest performance
   - Works headless
   - Full control

2. **Advanced Features**: Local REST API for commands
   - Execute Obsidian commands
   - Active file operations
   - Real-time UI updates (when needed)

3. **Query Capabilities**: Optionally integrate Dataview
   - Leverage existing query engine
   - Natural language → Dataview translation
   - Complex aggregations

4. **Analytics**: Integrate ObsidianTools concepts
   - NetworkX for graph analysis
   - Pandas for statistics
   - Health checks

**Architecture**:
```
AI Agent
├─ Filesystem Layer (Primary)
│  ├─ SQLite Index (metadata, FTS, links)
│  ├─ CRUD Operations
│  └─ Search & Analytics
│
├─ REST API Layer (Optional)
│  ├─ Command Execution
│  └─ Active File Ops
│
└─ Query Layer (Optional)
   ├─ Dataview Integration
   └─ Custom Query Engine
```

This provides best performance while maintaining flexibility for advanced features.

---

## User Expectations & Workflows

### What Users Mean by "Natural Language Vault Management"

Based on real-world examples and forum discussions:

#### 1. Conversational Query & Retrieval

**What users want**:
- "Show me notes about X"
- "Find notes from last month tagged with Y"
- "What did I write about machine learning?"

**NOT**:
- Complex search syntax: `tag:#ml AND (created:>2025-01-01 OR modified:<2025-01-01)`
- Technical commands: `grep -r "machine learning" vault/`

**Implementation needs**:
- Natural language → structured query translation
- Fuzzy matching on terms
- Context-aware search (understand "last month", "recent", "old")
- Semantic similarity for "about X"

---

#### 2. Task Automation

**What users want**:
- "Add this to my todo list"
- "Mark task X as complete"
- "Show me what's due this week"
- "Create a meeting note for tomorrow's standup"

**NOT**:
- Manual file editing
- Remember exact note paths
- Know specific task syntax

**Implementation needs**:
- Understand task/todo intent
- Parse natural date/time ("tomorrow", "this week", "next Monday")
- Smart note selection (find the right todo list)
- Task format detection (Obsidian tasks, basic markdown, Dataview)

---

#### 3. Daily Note Integration

**What users want**:
- "Add this to today's note"
- "What did I do yesterday?"
- "Create my daily note with the usual template"
- "Log this meeting to my daily note"

**NOT**:
- Navigate to specific date's file
- Remember daily note format
- Manual template application

**Implementation needs**:
- Automatic daily note detection/creation
- Append to appropriate section (if structured)
- Template application
- Date math ("yesterday", "last Friday")

---

#### 4. Knowledge Discovery

**What users want**:
- "Find notes similar to this"
- "What notes connect to X?"
- "Show me related ideas to Y"
- "Resurface old notes about Z"

**NOT**:
- Manual backlink exploration
- Remember all connections
- Search multiple times with different terms

**Implementation needs**:
- Semantic search/embeddings
- Graph traversal (backlinks, connections)
- Similarity scoring
- Smart ranking (recency, relevance, connections)

---

#### 5. Content Creation & Editing

**What users want**:
- "Create a note about X with tags Y and Z"
- "Add this content under the 'Ideas' section"
- "Update my reading list with this book"
- "Combine these notes into a summary"

**NOT**:
- Manual file creation
- Navigate to specific heading
- Copy-paste between files

**Implementation needs**:
- Smart note creation with structure
- Section detection and insertion
- List/table manipulation
- Content summarization (AI)

---

#### 6. Organizational Help

**What users want**:
- "Organize my inbox notes"
- "Move old drafts to archive"
- "Tag untagged notes about programming"
- "Find notes that need review"

**NOT**:
- Manual filing
- Remember organizational rules
- Bulk manual operations

**Implementation needs**:
- Content classification (AI)
- Auto-tagging suggestions
- Rule-based organization
- Batch operations with confirmation

---

### Real-World User Workflows

#### Workflow 1: Daily Review & Planning (Morning Routine)

**User Intent**: "Help me start my day"

**What happens**:
1. "Create today's daily note"
2. "Show me incomplete tasks from yesterday"
3. "What meetings do I have today?" (from calendar notes)
4. "Add these priorities to today's note"
5. "Show me notes I worked on yesterday for context"

**Operations needed**:
- Create daily note from template
- Task filtering by date/status
- Calendar/meeting note integration
- Content append to specific section
- Modified date search

---

#### Workflow 2: Research & Note-Taking (Writing Session)

**User Intent**: "Help me research and write about topic X"

**What happens**:
1. "Find all notes about X"
2. "Show me related concepts"
3. "Create a new note for my article about X"
4. "Add relevant sources to the article"
5. "Resurface old notes I might have forgotten"
6. "Generate a Map of Content for topic X"

**Operations needed**:
- Multi-criteria search (content + tags + metadata)
- Graph/relationship queries
- Note creation with structure
- Link insertion
- Semantic similarity search
- MOC generation from backlinks

---

#### Workflow 3: Meeting Notes (During/After Meeting)

**User Intent**: "Capture meeting information quickly"

**What happens**:
1. "Create a meeting note for standup"
2. "Log these action items"
3. "Link to project X note"
4. "Add attendees from my contacts"
5. "Tag with #meeting and #project-alpha"
6. "Add to today's daily note summary"

**Operations needed**:
- Template-based note creation
- Task creation with syntax
- Smart linking (search + link insertion)
- Metadata/frontmatter updates
- Multi-tag addition
- Cross-note references

---

#### Workflow 4: Weekly Review (Reflection)

**User Intent**: "Review my week and plan ahead"

**What happens**:
1. "Show me all notes from this week"
2. "What tasks did I complete?"
3. "List unfinished tasks"
4. "Find notes that need review or follow-up"
5. "Create weekly review note with summary"
6. "Move completed project notes to archive"

**Operations needed**:
- Date range queries
- Task filtering by status + date
- Tag/metadata-based filtering
- Note creation with dynamic content
- Bulk move operations
- Automated summaries

---

#### Workflow 5: Knowledge Gardening (Maintenance)

**User Intent**: "Keep my vault healthy and organized"

**What happens**:
1. "Find orphaned notes with no links"
2. "Show me broken links"
3. "Find old notes that haven't been updated in 6 months"
4. "Suggest tags for untagged notes"
5. "Identify duplicate or very similar notes"
6. "Update MOCs with new backlinks"

**Operations needed**:
- Graph analysis (orphans, hubs)
- Link validation
- Date-based queries
- AI classification for tags
- Semantic similarity for duplicates
- Automated MOC updates

---

### Common Pain Points (What Users Struggle With)

Based on forum discussions and user feedback:

1. **Search Limitations**
   - Pain: "I know I wrote about this, but can't find it"
   - Need: Better semantic search, not just keyword matching

2. **Manual Organization**
   - Pain: "I have 100 notes in my inbox that need filing"
   - Need: Auto-tagging, auto-filing, bulk operations

3. **Lost Connections**
   - Pain: "I don't see relationships between my notes"
   - Need: Better graph visualization, auto-linking suggestions

4. **Inconsistent Structure**
   - Pain: "My notes have different formats and missing metadata"
   - Need: Template enforcement, metadata validation

5. **Information Resurfacing**
   - Pain: "I forget about old notes and ideas"
   - Need: Smart reminders, random note surfacing, spaced repetition

6. **Task Fragmentation**
   - Pain: "Tasks are scattered across many notes"
   - Need: Unified task view, smart task aggregation

---

### User Personas & Their Needs

#### Persona 1: The PKM Enthusiast
**Background**: Uses Obsidian for serious personal knowledge management
**Vault Size**: 1,000-10,000 notes
**Priority Operations**:
- Graph operations (backlinks, MOCs, connections)
- Advanced search (Dataview-style queries)
- Metadata management
- Template automation

**Natural Language Examples**:
- "Generate a MOC for all my programming notes"
- "Find hub notes with more than 20 connections"
- "Show me notes tagged #permanent that I haven't reviewed in 3 months"

---

#### Persona 2: The Daily Note User
**Background**: Uses Obsidian primarily for daily journaling and task management
**Vault Size**: 500-2,000 notes (mostly daily notes)
**Priority Operations**:
- Daily note creation/navigation
- Task management
- Quick capture (append to daily note)
- Date-based queries

**Natural Language Examples**:
- "Add this idea to today's note"
- "Show me what I did last week"
- "Create my weekly review note"
- "What tasks are due this week?"

---

#### Persona 3: The Researcher/Writer
**Background**: Uses Obsidian for research, writing articles/books
**Vault Size**: 2,000-20,000 notes (literature notes, source material)
**Priority Operations**:
- Content search and discovery
- Source management (citations, references)
- Outline and draft creation
- Note linking and synthesis

**Natural Language Examples**:
- "Find all sources about cognitive bias"
- "Create a new article note about decision-making"
- "Show me notes related to this concept"
- "Extract quotes about X from my literature notes"

---

#### Persona 4: The Meeting Note-Taker
**Background**: Uses Obsidian for work meetings, project notes
**Vault Size**: 500-3,000 notes
**Priority Operations**:
- Meeting note creation from templates
- Action item tracking
- Project organization
- People/contact linking

**Natural Language Examples**:
- "Create a meeting note for today's standup"
- "Show me action items assigned to me"
- "Find all notes about Project Alpha"
- "Link this to the project roadmap"

---

#### Persona 5: The Casual User
**Background**: Uses Obsidian as a simple note-taking app
**Vault Size**: 50-500 notes
**Priority Operations**:
- Basic CRUD (create, read, update)
- Simple search
- Folder organization
- Minimal friction

**Natural Language Examples**:
- "Create a note about my vacation ideas"
- "Find my shopping list"
- "Add this to my reading list"
- "Delete old draft notes"

---

## Recommendations & Next Steps

### Phase 1 (MVP) - Weeks 1-4

**Goal**: Launch functional AI agent with core operations

**Must-Have Operations** (8):
1. Create Note
2. Read Note Content
3. Update Note (Full)
4. Delete Note
5. List Files in Vault
6. Full-Text Search
7. Tag Search
8. Read Note Metadata

**Technical Stack**:
- **Architecture**: Hybrid (Filesystem + SQLite cache)
- **Index**: Metadata cache (tags, frontmatter, basic stats)
- **Search**: Basic full-text (can optimize later with FTS5)
- **API**: FastAPI with async I/O
- **Parser**: Python-Markdown + PyYAML

**Deliverables**:
- [ ] Filesystem access layer
- [ ] YAML frontmatter parser
- [ ] Tag parser (frontmatter + inline)
- [ ] SQLite metadata cache
- [ ] Basic CRUD endpoints
- [ ] Search endpoints (text + tag)
- [ ] Natural language query mapper (basic)
- [ ] Error handling & validation
- [ ] Unit tests
- [ ] API documentation

**Success Metrics**:
- All 8 operations working
- <100ms single note ops
- <1s search on 1000-note vault
- 90%+ test coverage

---

### Phase 2 (Enhanced) - Weeks 5-8

**Goal**: Add power-user features and automation

**Operations to Add** (12):
1. Append/Prepend Content
2. Patch Content (insert at heading)
3. Update Frontmatter (granular)
4. Add/Remove Tags
5. Get Backlinks
6. Create Daily Note
7. Apply Template
8. Search and Replace
9. Multi-Criteria Search
10. List Tasks
11. Move Note
12. Folder Operations

**Technical Enhancements**:
- **Graph Index**: Link index in SQLite
- **FTS5**: Full-text search index
- **Template Engine**: Variable substitution
- **Heading Parser**: For patch operations
- **Task Parser**: Detect Obsidian tasks

**Deliverables**:
- [ ] Graph index (links, backlinks)
- [ ] FTS5 full-text search
- [ ] Heading/section parser
- [ ] Template system (basic)
- [ ] Task parser and queries
- [ ] Multi-criteria search engine
- [ ] Advanced NL query mapper
- [ ] Integration tests
- [ ] Performance benchmarks

**Success Metrics**:
- All Phase 2 operations working
- <500ms graph queries on 1000-note vault
- <200ms FTS5 search on 10,000 notes
- Template system supports 80% of use cases

---

### Phase 3 (Advanced) - Weeks 9-12+

**Goal**: Analytics, bulk operations, advanced workflows

**Operations to Add** (15+):
1. Bulk operations (create, update, delete, tag, move)
2. Graph queries (depth N, paths, hubs, orphans)
3. Semantic search (embeddings)
4. Vault statistics & analytics
5. Health checks (broken links, orphans)
6. Advanced templates (Templater-style)
7. Dataview integration
8. MOC generation/update
9. Import from web
10. Export operations
11. Command execution (via REST API)
12. Property management
13. Activity timeline
14. Auto-organization suggestions
15. Spaced repetition surfacing

**Technical Enhancements**:
- **Vector Database**: For semantic search (ChromaDB or FAISS)
- **Graph Algorithms**: NetworkX integration
- **Batch Processor**: Transaction-based bulk ops
- **Analytics Engine**: Pandas for statistics
- **Web Scraper**: For import operations
- **AI Classification**: For auto-tagging/filing

**Deliverables**:
- [ ] Vector database + embeddings
- [ ] Graph algorithm library
- [ ] Bulk operation engine
- [ ] Analytics dashboard
- [ ] Web import pipeline
- [ ] Auto-organization ML model
- [ ] Advanced template processor
- [ ] Integration with Local REST API
- [ ] Comprehensive docs
- [ ] User guide with examples

**Success Metrics**:
- Semantic search works well
- Bulk ops handle 100+ notes in <5s
- Analytics provide actionable insights
- Auto-organization 70%+ accuracy
- Graph queries handle complex patterns

---

### Natural Language Processing Strategy

**Phase 1: Rule-Based Mapping**
- Pattern matching for common queries
- Regex for date/time parsing
- Simple keyword extraction

```python
# Example rules
if "create" in query and "note" in query:
    operation = "create_note"
    title = extract_title(query)
    tags = extract_tags(query)

if "find" in query or "search" in query:
    operation = "search"
    search_terms = extract_terms(query)
    filters = extract_filters(query)  # tags, dates, etc.
```

**Phase 2: Intent Classification**
- Train classifier on common query patterns
- Map to operation + parameters
- Handle multi-step queries

```python
# Intent classification
intents = [
    "create_note",
    "search_notes",
    "update_note",
    "delete_note",
    "list_tasks",
    "create_daily_note",
    etc.
]

# Extract entities
entities = {
    "note_title": "Meeting Notes",
    "tags": ["#meeting", "#work"],
    "date": "2025-11-23",
    "search_terms": ["machine learning"],
}
```

**Phase 3: LLM-Powered Understanding**
- Use LLM to parse complex queries
- Generate structured operations
- Handle multi-step workflows

```python
# LLM prompt
system_prompt = """
You are a query parser for an Obsidian vault. Convert natural language
queries into structured operations. Available operations: {operations_list}

Output JSON with: operation, parameters, confidence
"""

# Example
query = "Find notes about AI from last month and create a summary MOC"
# → [
#     {op: "search", params: {terms: ["AI"], date_range: "last_month"}},
#     {op: "create_moc", params: {source: "search_results", title: "AI Summary"}}
#   ]
```

---

### API Design Pattern (FastAPI)

**Endpoint Structure**:

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI(title="Obsidian AI Agent API")

# Natural Language Endpoint (Primary Interface)
class NLQuery(BaseModel):
    query: str
    conversation_id: Optional[str] = None
    context: Optional[dict] = None

@app.post("/query")
async def natural_language_query(request: NLQuery):
    """
    Main endpoint for natural language queries.
    Examples:
    - "Create a note about project ideas"
    - "Find notes tagged #important from last week"
    - "Show me my tasks due today"
    """
    # Parse query → operation(s)
    operations = parse_natural_language(request.query)

    # Execute operation(s)
    results = await execute_operations(operations)

    # Return human-readable response + structured data
    return {
        "response": format_response(results),
        "operations": operations,
        "data": results
    }

# Structured Operations Endpoints (Advanced Users/Developers)
@app.post("/notes")
async def create_note(title: str, content: str, tags: List[str] = []):
    """Create a new note"""
    pass

@app.get("/notes/{path}")
async def read_note(path: str):
    """Read a note"""
    pass

@app.put("/notes/{path}")
async def update_note(path: str, content: str):
    """Update a note"""
    pass

@app.delete("/notes/{path}")
async def delete_note(path: str):
    """Delete a note"""
    pass

@app.get("/search")
async def search(
    q: Optional[str] = None,
    tags: Optional[List[str]] = None,
    date_from: Optional[date] = None,
    date_to: Optional[date] = None,
):
    """Advanced search with multiple criteria"""
    pass

@app.get("/graph/backlinks/{path}")
async def get_backlinks(path: str):
    """Get notes linking to this note"""
    pass

@app.post("/daily-notes")
async def create_daily_note(date: Optional[date] = None):
    """Create or get daily note"""
    pass

@app.get("/tasks")
async def list_tasks(
    status: Optional[str] = None,
    tags: Optional[List[str]] = None,
    due_before: Optional[date] = None,
):
    """List tasks with filters"""
    pass
```

**Response Format**:

```json
{
  "success": true,
  "response": "Found 5 notes tagged #important from last week",
  "data": {
    "notes": [
      {
        "path": "Projects/Project Alpha.md",
        "title": "Project Alpha",
        "excerpt": "...",
        "created": "2025-11-15",
        "tags": ["#important", "#project"]
      },
      // ... more notes
    ],
    "count": 5
  },
  "operations": [
    {
      "type": "search",
      "parameters": {
        "tags": ["#important"],
        "date_from": "2025-11-16",
        "date_to": "2025-11-23"
      }
    }
  ]
}
```

---

### Performance Targets

| Vault Size | Operation | Target | With Index |
|------------|-----------|--------|------------|
| Any | Read single note | <50ms | N/A |
| Any | Create note | <100ms | N/A |
| Any | Update note | <100ms | N/A |
| 1,000 | Tag search | <200ms | <50ms |
| 10,000 | Tag search | <1s | <100ms |
| 1,000 | Full-text search | <500ms | <150ms |
| 10,000 | Full-text search | <2s | <300ms |
| 1,000 | Get backlinks | <300ms | <100ms |
| 10,000 | Get backlinks | <1s | <200ms |
| Any | Bulk 100 notes | <5s | <3s |

**Optimization Priority**:
1. **Phase 1**: Basic metadata cache (tags, frontmatter)
2. **Phase 2**: FTS5 index + link index
3. **Phase 3**: Vector embeddings for semantic search

---

### Open Questions & Future Research

1. **Semantic Search**:
   - Which embedding model? (OpenAI, local, hybrid?)
   - Chunking strategy for long notes?
   - Update frequency vs. cost?

2. **Dataview Integration**:
   - Build custom engine or integrate Dataview plugin?
   - Support full Dataview syntax or subset?
   - Performance implications?

3. **Templater Integration**:
   - Support JavaScript execution? (security risk)
   - Subset of features vs. full compatibility?
   - Custom template language?

4. **Multi-Vault Support**:
   - How to handle multiple vaults?
   - Vault switching in conversation?
   - Cross-vault operations?

5. **Collaboration**:
   - Multi-user access to same vault?
   - Conflict resolution?
   - Permission system?

6. **Mobile Support**:
   - Mobile app integration?
   - Sync considerations?
   - Offline operations?

---

## Conclusion

This research provides a comprehensive foundation for building an AI agent that can manage Obsidian vaults through natural language. The key insights:

1. **Start Simple**: MVP with 8 core operations covers 80% of use cases
2. **Optimize Early**: Metadata cache is essential for good UX even at 1,000 notes
3. **Hybrid Architecture**: Combine filesystem access (performance) with REST API (features)
4. **Natural Language**: Users want conversation, not syntax
5. **Incremental Complexity**: Phase 1 → 2 → 3 allows validation and learning

The phased approach ensures rapid delivery of value while building toward advanced capabilities like semantic search, analytics, and automated organization.

---

## Sources

### Obsidian REST API & MCP Servers
- [Obsidian Local REST API - GitHub](https://github.com/coddingtonbear/obsidian-local-rest-api)
- [Interactive API Documentation](https://coddingtonbear.github.io/obsidian-local-rest-api/)
- [Obsidian MCP Server - cyanheads](https://github.com/cyanheads/obsidian-mcp-server)
- [Advanced MCP Server - tokidoo](https://lobehub.com/mcp/tokidoo-mcp-obsidian-advanced)
- [Obsidian Vault MCP server for AI agents](https://playbooks.com/mcp/cyanheads-obsidian)
- [Obsidian MCP servers: experiences and recommendations - Forum](https://forum.obsidian.md/t/obsidian-mcp-servers-experiences-and-recommendations/99936)

### Obsidian API & Development
- [Vault - Developer Documentation](https://docs.obsidian.md/Plugins/Vault)
- [Obsidian API - GitHub](https://github.com/obsidianmd/obsidian-api)
- [Build a plugin - Developer Documentation](https://docs.obsidian.md/Plugins/Getting+started/Build+a+plugin)
- [MetadataCache - Developer Documentation](https://docs.obsidian.md/Reference/TypeScript+API/MetadataCache)

### Automation & Workflows
- [9 Obsidian plugins for vault management (2025)](https://danielprindii.com/blog/9-obsidian-plugins-for-your-vault-management)
- [Obsidian MCP Server by Takuya: AI Engineer's Workflow](https://skywork.ai/skypage/en/obsidian-mcp-server-ai-engineer-workflow/1977574213834706944)
- [Writing 2,000 words in 90 minutes with Obsidian + MCP + Claude](https://www.haihai.ai/obsidian-mcp/)
- [I Built a Personal AI Assistant for My Day in Obsidian](https://artemxtech.github.io/I-Built-a-Personal-AI-Assistant-for-My-Day-in-Obsidian)

### Daily Notes & Templates
- [The Ultimate Guide to Obsidian Daily Notes Template](https://blog.obsibrain.com/other-articles/the-ultimate-guide-to-obsidian-daily-notes-template-proven-strategies-that-drive-better-results)
- [How to Structure Your Daily Notes in Obsidian](https://www.dsebastien.net/my-daily-note-template-in-obsidian/)
- [Mastering Obsidian Daily Notes: A Complete Guide](https://blog.obsibrain.com/templates/mastering-obsidian-daily-notes-a-complete-guide)
- [Fully dynamic daily notes in Obsidian](https://dev.to/michalbryxi/fully-dynamic-daily-notes-in-obsidian-2p42)

### Graph & Relationships
- [Maps of Content: Effortless organization for notes](https://obsidian.rocks/maps-of-content-effortless-organization-for-notes/)
- [Automated maps of content in Obsidian with Templater and Dataview](https://readwithai.substack.com/p/automated-maps-of-content-in-obsidian)
- [Obsidian Backlinks - Medium](https://medium.com/obsidian-observer/obsidian-backlinks-8c64ae0ec02e)
- [In what ways can we form useful relationships between notes](https://forum.obsidian.md/t/in-what-ways-can-we-form-useful-relationships-between-notes-long-read/702)

### Search & Performance
- [Search - Obsidian Help](https://help.obsidian.md/Plugins/Search)
- [Obsidian Search: Five Hidden Features](https://obsidian.rocks/obsidian-search-five-hidden-features/)
- [Use Metadata cache for tag search - Forum](https://forum.obsidian.md/t/use-metadata-cache-for-tag-search/49981)
- [Obsidian with very large vaults / Performance results](https://forum.obsidian.md/t/obsidian-with-very-large-vaults-performance-results/30635)

### Dataview & Queries
- [Query Types - Dataview](https://blacksmithgu.github.io/obsidian-dataview/queries/query-types/)
- [Dataview in Obsidian: A Beginner's Guide](https://obsidian.rocks/dataview-in-obsidian-a-beginners-guide/)
- [Practical dataview examples for Obsidian](https://willcodefor.beer/posts/dataview)
- [Resurface your Obsidian notes with these Dataview queries](https://efemkay.medium.com/resurface-your-obsidian-notes-with-these-dataview-queries-97f254c6c9c5)

### AI Integration
- [Get Plugged In: How to Use Generative AI Tools in Obsidian](https://blogs.nvidia.com/blog/ai-decoded-obsidian/)
- [Awesome Obsidian AI Tools - GitHub](https://github.com/danielrosehill/Awesome-Obsidian-AI-Tools)
- [How to Create an A.I. Agent for Obsidian Using n8n RAG](https://rumjahn.com/how-to-create-an-a-i-agent-for-obsidian-using-n8n-rag-a-step-by-step-guide-without-coding/)
- [AI Assistant | QuickAdd](https://quickadd.obsidian.guide/docs/AIAssistant)

### Python Tools
- [ObsidianTools - GitHub](https://github.com/mfarragher/obsidiantools)
- [obsidian-mcp - PyPI](https://pypi.org/project/obsidian-mcp/)

---

**End of Research Document**
