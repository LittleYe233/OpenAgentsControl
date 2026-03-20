---
name: context7
description: Retrieve up-to-date documentation for software libraries, frameworks, and components via the Context7 API. This skill should be used when looking up documentation for any programming library or framework, finding code examples for specific APIs or features, verifying correct usage of library functions, or obtaining current information about library APIs that may have changed since training.
---

# Context7

## Overview

This skill enables retrieval of current documentation for software libraries and components by calling the Context7 MCP server tools. Use it instead of relying on potentially outdated training data.

The Context7 MCP server must be configured in the `mcp` section of `~/.config/opencode/opencode.json`.

## Workflow

### Step 1: Resolve Library ID

To find the Context7 library ID, use the `Context7_resolve-library-id` tool:

**Tool**: `Context7_resolve-library-id`
**Parameters:**
- `libraryName` (required): The library name to search for (e.g., "react", "nextjs", "fastapi", "axios")
- `query` (required): A description of the topic for relevance ranking

**Response fields:**
- `libraryId`: Library identifier for the context endpoint (e.g., `/websites/react_dev_reference`)
- `name`: Human-readable library name
- `description`: Brief description of the library
- `codeSnippets`: Number of documentation snippets available

### Step 2: Query Documentation

To retrieve documentation, use the library ID from step 1 with the `Context7_query-docs` tool:

**Tool**: `Context7_query-docs`
**Parameters:**
- `libraryId` (required): The library ID from search results
- `query` (required): The specific topic to retrieve documentation for

## Examples

### React hooks documentation

1. **Resolve Library ID**:
   Call `Context7_resolve-library-id(libraryName="react", query="hooks")`
   Returns: `libraryId: "/websites/react_dev_reference"`

2. **Query Documentation**:
   Call `Context7_query-docs(libraryId="/websites/react_dev_reference", query="useState")`

### Next.js routing documentation

1. **Resolve Library ID**:
   Call `Context7_resolve-library-id(libraryName="nextjs", query="routing")`
   Returns: `libraryId: "/vercel/next.js"`

2. **Query Documentation**:
   Call `Context7_query-docs(libraryId="/vercel/next.js", query="app router")`

## Tips

- Be specific with the `query` parameter to improve relevance ranking.
- If the first search result is not correct, try refining the `libraryName` or `query`.
- The MCP server handles all API communication and authentication.
