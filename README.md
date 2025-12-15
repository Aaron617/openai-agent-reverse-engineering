# OpenAI Agent Container - Reverse Engineering Analysis

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
  - Document processing (DOCX → PDF → PNG verification)
  - PDF generation with visual validation
  - Spreadsheet creation and formatting

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