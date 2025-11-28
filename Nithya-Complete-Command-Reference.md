# Nithya AI Agent - Complete Command Reference

**Project**: Obsidian AI Agent (Nithya)
**Created**: 2025-11-23 to 2025-11-28
**Status**: PostgreSQL 18 Integration Complete

This document consolidates ALL commands used throughout the Nithya project development, from initial research to PostgreSQL 18 setup and migration.

---

## Table of Contents

1. [Phase 1: Research & Planning](#phase-1-research--planning)
2. [Phase 2: Document Creation](#phase-2-document-creation)
3. [Phase 3: FastAPI Project Setup](#phase-3-fastapi-project-setup)
4. [Phase 4: Database Integration](#phase-4-database-integration)
5. [Phase 5: PostgreSQL 18 Setup & Migration](#phase-5-postgresql-18-setup--migration)
6. [Quick Reference](#quick-reference)

---

## Phase 1: Research & Planning

### Initial Repository Setup

```bash
# Create Obsidian vault for research
cd /home/user
mkdir Obsidian_Vault
cd Obsidian_Vault

# Initialize git repository
git init
git config user.name "Your Name"
git config user.email "your-email@example.com"

# Create initial README
echo "# Obsidian AI Agent Research" > README.md
git add README.md
git commit -m "Initial commit"
```

### Research Phase - Web Research & Analysis

**Note**: Research was conducted through web searches and documentation reading. The following research documents were created:

1. **FastAPI + AI Agent Integration Research**
   - Searched: "FastAPI AI agent integration best practices 2025"
   - Searched: "Pydantic AI FastAPI examples"
   - Searched: "FastAPI streaming AI responses"
   - **Output**: `FastAPI-AI-Agent-Integration-Patterns.md` (comprehensive research document)

2. **MCP Obsidian Server Analysis**
   - Searched: "MCP Obsidian server GitHub"
   - Analyzed: https://github.com/MarkusPfundstein/mcp-obsidian
   - Reviewed: Tool inventory, API patterns, implementation details
   - **Output**: `MCP-Obsidian-Server-Analysis.md` + `MCP-Obsidian-Quick-Reference.md`

3. **Vault Operations Research**
   - Searched: "Obsidian vault operations programmatic access"
   - Searched: "Obsidian Local REST API best practices"
   - Analyzed: User workflows and operation patterns
   - **Output**: `Obsidian-Vault-Operations-AI-Agent-Research.md`

### Git Commands - Research Phase

```bash
# Working in /home/user/Obsidian_Vault

# Add and commit research documents
git add FastAPI-AI-Agent-Integration-Patterns.md
git commit -m "Add FastAPI + AI agent integration research"

git add MCP-Obsidian-Server-Analysis.md MCP-Obsidian-Quick-Reference.md
git commit -m "Add comprehensive MCP Obsidian server research and analysis"

git add Obsidian-Vault-Operations-AI-Agent-Research.md
git commit -m "Add comprehensive vault operations research for AI agent"
```

---

## Phase 2: Document Creation

### MVP Tool Designs

```bash
# Navigate to Nithya project directory
cd /home/user/Obsidian-Agent-Nithya

# Create MVP tool designs following Anthropic best practices
# File created: mvp-tool-designs.md
# Contains: 3 core tools with consolidated operations

git add mvp-tool-designs.md
git commit -m "Add MVP tool designs for Obsidian AI agent"
```

### Product Requirements Document (PRD)

```bash
# Create comprehensive PRD
# File created: PRD.md
# Contains: Executive summary, architecture, user stories, technical specs

git add PRD.md
git commit -m "Add comprehensive Product Requirements Document for Nithya"
```

---

## Phase 3: FastAPI Project Setup

### Project Initialization

```bash
# Create project directory structure
mkdir -p /home/user/Obsidian-Agent-Nithya
cd /home/user/Obsidian-Agent-Nithya

# Initialize git repository
git init
git branch -M main

# Create Python version file
echo "3.12" > .python-version
git add .python-version
git commit -m "Add Python version 3.12 specification"
```

### Python Environment Setup

```bash
# Create virtual environment
python3.12 -m venv .venv

# Activate virtual environment (Linux/Mac)
source .venv/bin/activate

# Activate virtual environment (Windows PowerShell)
.\.venv\Scripts\Activate.ps1

# Install dependencies using uv (Linux/Mac)
uv pip install fastapi uvicorn pydantic-ai-slim[openai,anthropic] \
  pydantic-settings python-dotenv httpx structlog

# Install dependencies using pip (Windows/fallback)
pip install fastapi uvicorn "pydantic-ai-slim[openai,anthropic]" \
  pydantic-settings python-dotenv httpx structlog
```

### Project Structure Creation

```bash
# Create directory structure
mkdir -p app/core
mkdir -p app/shared
mkdir -p app/tests
mkdir -p app/core/tests
mkdir -p app/shared/tests
mkdir -p docs
mkdir -p .claude/commands

# Create __init__.py files
touch app/__init__.py
touch app/core/__init__.py
touch app/shared/__init__.py
touch app/tests/__init__.py
touch app/core/tests/__init__.py
touch app/shared/tests/__init__.py

# Create core application files
touch app/main.py
touch app/core/config.py
touch app/core/logging.py
touch app/core/exceptions.py
touch app/core/middleware.py
touch app/core/health.py
touch app/shared/models.py
touch app/shared/schemas.py
touch app/shared/utils.py
```

### Configuration Files

```bash
# Create .gitignore
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
.venv/
venv/
ENV/
env/

# Environment
.env
.env.local
*.log

# IDE
.vscode/
.idea/
*.swp
*.swo

# Testing
.pytest_cache/
.coverage
htmlcov/
.mypy_cache/
.ruff_cache/

# Docker
*.pid
EOF

git add .gitignore
git commit -m "Add .gitignore for Python and environment files"

# Create .dockerignore
cat > .dockerignore << 'EOF'
.git
.venv
__pycache__
*.pyc
.env
.pytest_cache
.mypy_cache
.ruff_cache
*.log
EOF

git add .dockerignore
git commit -m "Add .dockerignore file to exclude unnecessary files"

# Create .env.example
cat > .env.example << 'EOF'
# Application
APP_NAME=Nithya
VERSION=0.1.0
ENVIRONMENT=development
LOG_LEVEL=INFO

# API Configuration
API_HOST=0.0.0.0
API_PORT=8000

# CORS
ALLOWED_ORIGINS=["app://obsidian.md","http://localhost:3000","http://localhost:8000"]

# Vault Configuration
VAULT_PATH=/path/to/your/obsidian/vault

# LLM Provider
AI_PROVIDER=anthropic
AI_MODEL=claude-sonnet-4-0

# API Keys
ANTHROPIC_API_KEY=your-key-here
OPENAI_API_KEY=your-key-here

# Agent Configuration
DEFAULT_RESPONSE_FORMAT=concise
LLM_TIMEOUT=30

# Development
DEBUG=false
RELOAD=true

# Database Configuration (optional)
ENABLE_DATABASE=false
DATABASE_URL=postgresql+asyncpg://nithya:nithya_dev@localhost:5433/nithya
DB_POOL_SIZE=5
DB_MAX_OVERFLOW=10
DB_ECHO=false
EOF

git add .env.example
git commit -m "Add example environment configuration file"
```

### Docker Setup

```bash
# Create Dockerfile
cat > Dockerfile << 'EOF'
FROM python:3.12-slim

WORKDIR /app

# Install uv for faster dependency management
RUN pip install uv

# Copy dependency files
COPY pyproject.toml uv.lock ./

# Install dependencies
RUN uv sync --frozen

# Copy application code
COPY . .

# Expose API port
EXPOSE 8000

# Run FastAPI with uvicorn
CMD ["uv", "run", "uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
EOF

git add Dockerfile
git commit -m "Add Dockerfile for containerized deployment"

# Create docker-compose.yml (initial version)
cat > docker-compose.yml << 'EOF'
services:
  nithya:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: nithya-agent
    ports:
      - "${API_PORT:-8000}:8000"
    volumes:
      - .:/app
      - /app/.venv
      - ${OBSIDIAN_VAULT_PATH}:/vault:rw
    env_file:
      - path: .env
        required: true
    environment:
      - PYTHONUNBUFFERED=1
      - LOG_LEVEL=${LOG_LEVEL:-INFO}
      - VAULT_PATH=/vault
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    restart: unless-stopped
EOF

git add docker-compose.yml
git commit -m "Add Docker Compose configuration"
```

### pyproject.toml

```bash
# Create pyproject.toml with dependencies
cat > pyproject.toml << 'EOF'
[project]
name = "nithya"
version = "0.1.0"
description = "AI-powered agent for Obsidian vault management"
readme = "README.md"
requires-python = ">=3.12"
dependencies = [
    "fastapi>=0.115.6",
    "uvicorn[standard]>=0.34.0",
    "pydantic>=2.10.6",
    "pydantic-settings>=2.7.1",
    "pydantic-ai-slim[openai,anthropic]>=0.0.18",
    "python-dotenv>=1.0.1",
    "httpx>=0.28.1",
    "structlog>=25.1.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.ruff]
line-length = 100
target-version = "py312"

[tool.mypy]
python_version = "3.12"
strict = true
warn_return_any = true
warn_unused_configs = true

[tool.pytest.ini_options]
testpaths = ["app/tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
EOF

git add pyproject.toml
git commit -m "Add pyproject.toml with dependencies"
```

### Initial Commit

```bash
# Add all initial files
git add .

# Create initial commit
git commit -m "Add files via upload"

# Push to GitHub (if repository exists)
git remote add origin https://github.com/your-username/Obsidian-Agent-Nithya.git
git push -u origin main
```

---

## Phase 4: Database Integration

### Database Dependencies

```bash
# Activate virtual environment
source .venv/bin/activate  # Linux/Mac
.\.venv\Scripts\Activate.ps1  # Windows

# Install database packages
pip install "sqlalchemy[asyncio]>=2.0.36" asyncpg>=0.30.0 alembic>=1.14.0

# Or using uv
uv pip install "sqlalchemy[asyncio]>=2.0.36" asyncpg>=0.30.0 alembic>=1.14.0
```

### Alembic Initialization

```bash
# Initialize Alembic for database migrations
alembic init alembic

# This creates:
# - alembic/ directory with migration scripts
# - alembic.ini configuration file
# - alembic/env.py environment configuration
# - alembic/versions/ directory for migration files
```

### Update Configuration Files

```bash
# Update .env with database settings
cat >> .env << 'EOF'

# Database Configuration
ENABLE_DATABASE=true
DATABASE_URL=postgresql+asyncpg://nithya:nithya_dev@localhost:5433/nithya
DB_POOL_SIZE=5
DB_MAX_OVERFLOW=10
DB_ECHO=false
EOF

# Verify .env file
cat .env
```

### Docker Compose - Add PostgreSQL Service

```bash
# Update docker-compose.yml to include PostgreSQL
# Added postgres service configuration
# Added volume for postgres_data
# Updated nithya service to depend on postgres

git add docker-compose.yml
git commit -m "Add PostgreSQL 18 service to docker-compose"
```

### Create Database Module

```bash
# Create app/core/database.py
# Contains:
# - SQLAlchemy async engine setup
# - Database session management
# - Base model class
# - Connection lifecycle functions

git add app/core/database.py
git commit -m "Add database module with SQLAlchemy async support"
```

### Update Health Check Endpoints

```bash
# Update app/core/health.py
# Added /health/db endpoint for database connectivity check

git add app/core/health.py
git commit -m "Add database health check endpoint"
```

### Commit Database Integration

```bash
git add .
git commit -m "Add hybrid database architecture with optional PostgreSQL support"
```

---

## Phase 5: PostgreSQL 18 Setup & Migration

### Initial Docker Commands (Windows PowerShell)

```powershell
# Navigate to project directory
cd "C:\Users\sathy\Downloads\AI Mastery\Obsidian-Agent-Nithya\Obsidian-Agent-Nithya"

# Check Docker is running
docker --version
docker ps
```

### Troubleshooting Docker Compose (didn't work initially)

```powershell
# These commands didn't work due to uv not being available on Windows

# Attempt 1: Using docker-compose with profile
docker-compose --profile database up -d postgres

# Attempt 2: Using docker compose (space instead of hyphen)
docker compose --profile database up -d postgres

# Attempt 3: Without profile
docker compose up -d postgres
```

### PostgreSQL Container Setup (Final Working Version)

```powershell
# Check existing containers
docker ps
docker ps -a

# Stop and remove old containers (if they exist)
docker stop fastapi-starter-for-ai-coding-db-1
docker rm fastapi-starter-for-ai-coding-db-1

# Stop and remove nithya-postgres if exists
docker stop nithya-postgres
docker rm nithya-postgres

# Remove old volume to fix permission issues
docker volume rm postgres_data

# Create fresh PostgreSQL 18 container (FINAL WORKING VERSION)
docker run -d `
  --name nithya-postgres `
  -p 5433:5432 `
  -e POSTGRES_USER=nithya `
  -e POSTGRES_PASSWORD=nithya_dev `
  -e POSTGRES_DB=nithya `
  -v postgres_data:/var/lib/postgresql/data `
  postgres:18-alpine

# Verify container is running and healthy
docker ps

# Check container logs
docker logs nithya-postgres

# Start existing container
docker start nithya-postgres

# Monitor container status
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

### Python Virtual Environment (Windows)

```powershell
# Navigate to project
cd "C:\Users\sathy\Downloads\AI Mastery\Obsidian-Agent-Nithya\Obsidian-Agent-Nithya"

# Activate virtual environment
.\.venv\Scripts\Activate.ps1

# If execution policy error occurs:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# Verify activation (should see (.venv) in prompt)
```

### Install Dependencies (Windows)

```powershell
# Check if alembic is installed
alembic --version

# Install database dependencies manually
pip install sqlalchemy[asyncio] asyncpg alembic fastapi "pydantic-ai-slim[openai,anthropic]" pydantic-settings python-dotenv httpx structlog

# Verify installation
pip list | Select-String -Pattern "alembic|sqlalchemy|asyncpg"
```

### Environment File Setup

```powershell
# Check if .env exists
Test-Path .env

# Check if .env.example exists
Test-Path .env.example

# Copy .env.example to .env (if needed)
Copy-Item .env.example .env

# Edit .env file
notepad .env

# Update these values in .env:
# 1. VAULT_PATH=C:\Users\sathy\path\to\your\Obsidian_Vault
# 2. ANTHROPIC_API_KEY=sk-ant-api03-your-actual-key
# 3. ENABLE_DATABASE=true
# 4. DATABASE_URL=postgresql+asyncpg://nithya:nithya_dev@localhost:5433/nithya

# Verify .env configuration
Get-Content .env
```

### Database Migration

```powershell
# Ensure virtual environment is activated
# Ensure PostgreSQL container is running (docker ps)

# Run Alembic migration
alembic upgrade head

# Expected output:
# INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
# INFO  [alembic.runtime.migration] Will assume transactional DDL.
# INFO  [alembic.runtime.migration] Running upgrade -> abc123, initial schema

# Verify migration success (no errors, returns to prompt)
```

### Verify Complete Setup

```powershell
# Check Docker container status
docker ps

# Output should show:
# CONTAINER ID   IMAGE                NAMES             STATUS
# xxxxx          postgres:18-alpine   nithya-postgres   Up X minutes (healthy)

# Start FastAPI application
python -m uvicorn app.main:app --reload

# Test health endpoints (in new PowerShell window)
curl http://localhost:8000/health
curl http://localhost:8000/health/db

# Or use Invoke-WebRequest
Invoke-WebRequest -Uri http://localhost:8000/health
Invoke-WebRequest -Uri http://localhost:8000/health/db

# Expected response from /health/db:
# {"status":"healthy","service":"database","provider":"postgresql"}
```

### Final Git Commit

```bash
# On Linux side (where git repository is)
cd /home/user/Obsidian-Agent-Nithya

# Check status
git status

# Stage changes
git add .

# Commit database setup
git commit -m "Complete PostgreSQL 18 integration with Alembic migrations"

# Push to branch
git push -u origin claude/obsidian-ai-agent-research-016W2XgaoYXFuXSqt1LRKb4R
```

---

## Quick Reference

### Essential Docker Commands

```powershell
# Container Management
docker ps                          # List running containers
docker ps -a                       # List all containers (including stopped)
docker start nithya-postgres       # Start container
docker stop nithya-postgres        # Stop container
docker restart nithya-postgres     # Restart container
docker rm nithya-postgres          # Remove container
docker logs nithya-postgres        # View container logs
docker logs -f nithya-postgres     # Follow container logs

# Volume Management
docker volume ls                   # List volumes
docker volume rm postgres_data     # Remove volume

# Image Management
docker images                      # List images
docker pull postgres:18-alpine     # Pull PostgreSQL 18 image
```

### Essential Python/Alembic Commands

```powershell
# Virtual Environment
.\.venv\Scripts\Activate.ps1       # Activate venv (Windows)
source .venv/bin/activate          # Activate venv (Linux/Mac)
deactivate                         # Deactivate venv

# Package Management
pip install <package>              # Install package
pip list                           # List installed packages
pip freeze > requirements.txt      # Export dependencies

# Alembic Migrations
alembic upgrade head               # Run migrations
alembic downgrade -1               # Rollback one migration
alembic revision -m "message"      # Create new migration
alembic current                    # Show current revision
alembic history                    # Show migration history

# FastAPI Development
python -m uvicorn app.main:app --reload    # Start dev server
python -m uvicorn app.main:app --host 0.0.0.0 --port 8000  # Production
```

### Environment Variables Reference

```bash
# Required
VAULT_PATH=/path/to/obsidian/vault
ANTHROPIC_API_KEY=sk-ant-api03-xxxxx

# Database (optional)
ENABLE_DATABASE=true
DATABASE_URL=postgresql+asyncpg://nithya:nithya_dev@localhost:5433/nithya

# Application
APP_NAME=Nithya
API_PORT=8000
LOG_LEVEL=INFO
```

### Common Troubleshooting

```powershell
# PostgreSQL not starting
docker logs nithya-postgres         # Check logs for errors
docker volume rm postgres_data      # Remove volume if permission issues
# Then recreate container

# Migration fails - connection refused
docker ps                           # Verify postgres is running
Test-NetConnection localhost -Port 5433  # Check port is accessible

# .env parsing errors
Get-Content .env                    # Check for syntax errors
# Remove any PowerShell syntax (@", "@ | Out-File)
# File should only contain KEY=VALUE pairs

# Virtual environment issues
.\.venv\Scripts\Activate.ps1        # Reactivate
pip install --upgrade pip           # Update pip
pip install -r requirements.txt     # Reinstall dependencies
```

---

## Summary

### What We Built

✅ **Research Phase** - Comprehensive research on FastAPI, Pydantic AI, MCP servers, and vault operations
✅ **Documentation** - PRD.md and mvp-tool-designs.md following Anthropic best practices
✅ **FastAPI Backend** - Complete project structure with core modules
✅ **Database Integration** - Hybrid architecture with optional PostgreSQL support
✅ **PostgreSQL 18** - Dockerized database with Alembic migrations
✅ **Health Checks** - API health endpoint + database connectivity check

### Architecture

```
┌─────────────────────────────────────────┐
│ Obsidian Desktop (Copilot Plugin)      │
└────────────────┬────────────────────────┘
                 │ HTTP /v1/chat/completions
                 ▼
┌─────────────────────────────────────────┐
│ Docker: nithya-agent (FastAPI)          │
│ ┌─────────────────────────────────────┐ │
│ │ Pydantic AI Agent (3 tools)         │ │
│ └─────────────────────────────────────┘ │
│ ┌─────────────────────────────────────┐ │
│ │ Volume Mount: /vault (read/write)   │ │
│ └─────────────────────────────────────┘ │
└─────────────────┬───────────────────────┘
                  │ SQL (optional)
                  ▼
┌─────────────────────────────────────────┐
│ Docker: nithya-postgres (PostgreSQL 18) │
│ Port: 5433 (to avoid conflicts)         │
└─────────────────────────────────────────┘
```

### Next Steps

1. **Implement 3 MVP Tools** - Create `obsidian_query_vault`, `obsidian_modify_vault`, `obsidian_organize_vault`
2. **Test with Copilot Plugin** - Configure Obsidian to use `http://localhost:8000/v1/chat/completions`
3. **Create Database Models** - Add conversation history and analytics tables
4. **Deploy to Production** - Cloud deployment with proper secrets management

---

**Document Version**: 1.0
**Last Updated**: 2025-11-28
**Status**: ✅ Complete - PostgreSQL 18 Setup Successful
