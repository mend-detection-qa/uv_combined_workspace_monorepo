# Phase 4 Combined Integration Test: Workspace Monorepo

## Test ID
T-P4-002

## Category
Combined Integration - Monorepo with Multiple Packages

## Priority
P1

## Description
This fixture simulates a production monorepo using UV workspaces with multiple packages that depend on each other, combining workspace management, internal dependencies, and shared external dependencies.

## Features Combined
1. **UV Workspaces** - Multiple packages in a single repository
2. **Internal Dependencies** - Packages depending on each other
3. **Shared External Dependencies** - Common dependencies across packages
4. **Platform Markers** - Platform-specific dependencies
5. **Git Dependencies** - External packages from Git repositories
6. **Development Dependencies** - Workspace-level and package-level dev deps

## Real-World Inspiration
Based on patterns from:
- modelcontextprotocol/python-sdk (workspace structure)
- Large-scale Python monorepos
- Enterprise multi-package projects

## Workspace Structure
```
workspace-monorepo/
├── pyproject.toml (workspace root)
├── packages/
│   ├── core/
│   │   └── pyproject.toml (shared utilities)
│   ├── api/
│   │   └── pyproject.toml (API server, depends on core)
│   └── cli/
│       └── pyproject.toml (CLI tool, depends on core)
└── uv.lock (single lock file for entire workspace)
```

## Expected Dependency Tree Structure
```
workspace-monorepo
├── Workspace Members
│   ├── core (internal package)
│   │   ├── pydantic>=2.0.0
│   │   ├── python-dateutil>=2.8.0
│   │   └── requests>=2.31.0
│   ├── api (internal package)
│   │   ├── core (internal dependency)
│   │   ├── fastapi>=0.109.0
│   │   ├── uvicorn>=0.27.0
│   │   └── sqlalchemy>=2.0.0
│   └── cli (internal package)
│       ├── core (internal dependency)
│       ├── click>=8.1.0
│       ├── rich>=13.0.0
│       └── typer>=0.9.0
├── Shared Dependencies (used by multiple packages)
│   ├── pydantic (core, api)
│   ├── typing-extensions (multiple packages)
│   └── certifi (transitive, shared)
└── Development Dependencies (workspace-level)
    ├── pytest>=8.0.0
    ├── mypy>=1.8.0
    ├── ruff>=0.3.0
    └── pytest-cov>=4.1.0
```

## Test Objectives
1. Verify workspace member detection and parsing
2. Verify internal dependency resolution (api → core, cli → core)
3. Verify shared external dependencies are identified
4. Verify workspace-level development dependencies
5. Verify single lock file handles all workspace members
6. Verify dependency paths through internal packages

## Success Criteria
- [ ] All 3 workspace members identified
- [ ] Internal dependencies (api→core, cli→core) mapped correctly
- [ ] Shared dependencies identified across workspace
- [ ] Workspace-level dev dependencies separate from package deps
- [ ] Lock file references all workspace members
- [ ] Transitive dependencies through internal packages resolved
- [ ] Total packages: ~25-30 (including workspace members)

## UV Version Compatibility
- Minimum: UV 0.3.0+ (workspace support added)
- Recommended: UV 0.7.0+

## Files in this Fixture
- `pyproject.toml` - Workspace root configuration
- `packages/core/pyproject.toml` - Core package
- `packages/api/pyproject.toml` - API package
- `packages/cli/pyproject.toml` - CLI package
- `uv.lock` - Unified lock file for entire workspace
- `README.md` - This file

## Usage
```bash
# Generate workspace lock file
uv lock

# Sync entire workspace
uv sync

# Run dependency tree builder
dependency-tree-builder scan . > output.json
```

## Mend Integration Notes
This fixture tests Mend's ability to:
- Detect workspace structure
- Handle internal package dependencies
- Track vulnerabilities across workspace members
- Report shared dependency vulnerabilities

## Related Tests
- T-P2-011: Workspace Monorepo (simpler version)
- T-P1-003: Transitive Dependency Mapping
- T-P2-006: Path Dependencies