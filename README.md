# OpenAI Agent Container - Reverse Engineering Analysis

<!-- [![Twitter](https://img.shields.io/twitter/url/https/twitter.com/yourhandle.svg?style=social&label=Follow)](https://twitter.com/yourhandle)
[![Xiaohongshu](https://img.shields.io/badge/小红书-Explore%20Agent%20Architecture-red)](https://www.xiaohongshu.com/user/profile/yourid) -->

This repository contains a reverse-engineered analysis of OpenAI's agent container architecture, focusing on the observable runtime structure of a tool-using AI agent sandbox.

## Quick Overview

The OpenAI agent container is organized into four main directories with distinct roles:

- **`/opt`** (5.7GB) - Core runtime environment containing Python tools, terminal services, GUI stack, and browser automation
- **`/home/oai`** (465KB) - User workspace with skills documentation and toolchain scripts
- **`/openai`** (28KB) - Chrome/computer-use agent integration components
- **`/mnt/data`** (4KB) - Data/output volume mount point

## Key Components

### Tool Services Layer
- **Python Tool Server** (`/opt/python-tool/`) - FastAPI service providing secure code execution via Jupyter kernel
- **Terminal Server** (`/opt/terminal-server/`) - FastAPI service for controlled shell command execution via PTY

### GUI & Visualization Stack
- Chromium browser with CDP remote debugging
- NoVNC for remote desktop visualization
- X11 desktop environment (XFCE4 + Openbox)
- mitmproxy for network traffic observation

### Documentation & Workflows
- Skills documentation in `/home/oai/skills/` providing structured workflows for:
  - **Document Processing** (`docs/skill.md`) - Complete DOCX manipulation workflow:
    - Reading/writing DOCX files using `python-docx`
    - **Critical validation step**: Convert to PDF with LibreOffice, then to PNG for visual inspection
    - This "render→inspect→fix" pattern prevents layout issues in generated documents
  - **PDF Generation** (`pdfs/skill.md`) - Professional PDF creation workflow:
    - Using `reportlab` for structured PDF generation
    - **Mandatory rasterization**: Convert PDF pages to PNG images for verification
    - Special handling for tables, graphics, and complex layouts to avoid rendering errors
  - **Spreadsheet Operations** (`spreadsheets/skill.md`) - Excel file manipulation:
    - Integration with `artifact_tool` and `openpyxl` for cell operations
    - Style guidelines and formatting standards
    - Data validation and consistency checks

### Build & Transformation Tools
- **Granola CLI** (`/opt/granola-cli`) - Node.js-based build and transformation utility (410MB):
  - Part of the Node.js toolchain managed via NVM
  - Used for script transformation and build processes
  - Particularly important for presentation and slide generation workflows
  - Works in conjunction with the `pptxgenjs` helpers in `/home/oai/share/slides/`

#### Skills System Architecture (Parallel to Claude Skills)
The OpenAI agent's skills system is architecturally identical to Claude's skill framework:

- **Skill Definition**: Each tool has a dedicated `.md` file defining:
  - Required dependencies and imports
  - Step-by-step execution patterns
  - Error handling and edge cases
  - Quality validation checkpoints

- **Mandatory Workflows**: Unlike unconstrained code generation, skills enforce:
  - Specific library usage patterns (e.g., LibreOffice for document conversion)
  - Validation through visual inspection (PNG rendering)
  - Iterative refinement loops (generate → validate → fix)

- **Tool Isolation**: Each skill operates within its own context with:
  - Dedicated import paths and tool wrappers
  - Standardized error reporting
  - Output format requirements

This design ensures consistent, reliable outputs from generative tools - a critical requirement when agents must produce production-quality documents and data artifacts.

### Orchestration
- supervisord manages all services as a cohesive stack
- Container entrypoint via `/opt/entrypoint/`

## Notable Engineering Decisions

1. **Strong isolation** - Tools exposed as authenticated microservices, not direct shell access
2. **Output validation** - Skills emphasize render→inspect→fix workflow to handle generative uncertainty
3. **Resource limits** - Message size caps (10MB), memory limits, ulimits for stability
4. **Observability** - Comprehensive logging and monitoring infrastructure

## Directory Structure in This Repository

- `openai/` - Full copy of `/openai` directory
- `opt/` - Selected components from `/opt` (excludes large binaries like pyvenv)
- `home/` - Copy of `/home/oai` directory
- `mnt/` - Copy of `/mnt/data` directory
- `opt/opt_info/` - Metadata and analysis of large components

## Analysis Documents

See `opt_info/` for:
- Component size breakdown
- Directory trees
- Python environment package listings
- Large component analysis notes

<!-- ## Reproduction Project

Based on this reverse engineering analysis, we're building an open-source reproduction of the agent architecture. This will help the community understand how tool-using agents are implemented in practice.

**Repository**: [https://github.com/yourorg/agent-container-reproduction](https://github.com/yourorg/agent-container-reproduction)

The reproduction will include:
- Multi-service architecture with FastAPI tool servers
- Skills system with validation workflows
- GUI stack for computer-use scenarios
- Complete containerization with supervisord orchestration -->