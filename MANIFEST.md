
# OpenAI Agent Container - Content Manifest

This document details what has been captured from the OpenAI agent container analysis.

## Repository Structure

### Full Directory Copies

#### Core Project Code
- `openai/` - Complete copy of `/openai` directory (28KB)
  - Chrome policy merge utilities for CUA (Computer Use Agent)
  - Configuration files and Python integration code

#### Runtime Environment
- `opt/` - Selected components from `/opt` directory (excludes large binaries)
  - `apply_patch/` - Patch application utility
  - `entrypoint/` - Container initialization scripts
  - `python-tool/` - FastAPI Python execution server (124KB)
  - `terminal-server/` - FastAPI terminal/PTY server (57MB)
  - `novnc/` - NoVNC client for remote desktop access (14MB)
  - `imagemagick/` - Image processing utilities (39MB)

#### User Workspace
- `home/` - Complete copy of `/home/oai` directory (465KB)
  - `skills/` - Tool usage documentation and workflows
  - `share/` - Exportable artifacts directory with sync scripts
  - Configuration files and user utilities

#### Data Volume
- `mnt/` - Copy of `/mnt/data` directory (4KB)
  - Standard data/output mount point (currently empty)

### Metadata and Analysis

See `opt/opt_info/` for detailed analysis of large components:
- Component size breakdown
- Directory trees (depth 4)
- Large component notes
- Python environment analysis

## Excluded Components

For space and licensing reasons, these large components are NOT included in full:

### Python Environments
- `/opt/pyvenv` (4.7GB) - Main Python virtual environment
  - Metadata and package lists available in `opt_info/python_env/`

- `/opt/pyvenv-python-tool` (378MB) - Isolated Python tool server environment

### Node.js Environment
- `/opt/nvm` (410MB) - Node Version Manager and Node.js installation

### Build Tools
- `/opt/granola-cli` - Node.js-based build/transformation utilities

## Key Observations

### Service Architecture
The container runs a multi-service stack orchestrated by supervisord:

1. **Tool Services**
   - Python Tool Server (FastAPI + Jupyter kernel)
   - Terminal Server (FastAPI + PTY process management)

2. **GUI Stack**
   - Chromium with CDP debugging
   - X11 desktop (XFCE4 + Openbox)
   - VNC/NoVNC remote access

3. **Support Services**
   - mitmproxy for traffic observation
   - rsync daemon for file synchronization
   - Log forwarding and monitoring

### Security Features
- Bearer token authentication for all tool APIs
- Resource limits (memory, CPU, output size)
- Isolated execution environments
- Process lifecycle management

### Workflow Patterns
Skills documentation enforces a "render → inspect → fix" pattern:
- Documents: Create → LibreOffice → PDF → PNG verification
- Spreadsheets: Generate → Style → Visual validation
- Code: Execute → Callback → UI display
