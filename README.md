# ArXiv Research MCP Server

A Model Context Protocol (MCP) server that provides academic paper search and management functionality through the arXiv API. This server enables AI assistants to search for research papers, extract paper details, organize resources, and generate research prompts.

## Features

- **Paper Search**: Search arXiv for academic papers by topic with configurable result limits
- **Paper Information Extraction**: Retrieve detailed information about specific papers including title, authors, summary, and publication details
- **Topic Organization**: Automatically organize papers by research topics in structured directories
- **Resource Management**: Browse available research topics and access paper collections through MCP resources
- **Research Prompts**: Generate structured prompts for comprehensive academic research analysis

## Quick Start

### Deploy on Render

This server is configured for easy deployment on Render.com:

1. Fork this repository
2. Connect your GitHub account to Render
3. Create a new Web Service
4. Connect this repository
5. Render will automatically detect the Python environment and deploy

### Local Development

```bash
# Install dependencies
pip install -r requirements.txt

# Run the server
python research_server.py
```

The server will start on port 8001 with Server-Sent Events (SSE) transport.

## MCP Tools

### `search_papers(topic: str, max_results: int = 5)`
Search for academic papers on arXiv and save results locally.

**Parameters:**
- `topic`: Research topic to search for
- `max_results`: Maximum number of papers to retrieve (default: 5)

**Returns:** List of paper IDs found in the search

### `extract_info(paper_id: str)`
Retrieve detailed information about a specific paper.

**Parameters:**
- `paper_id`: ArXiv paper ID (e.g., "2301.12345")

**Returns:** JSON string with paper details or error message if not found

## MCP Resources

### `papers://folders`
Lists all available research topic folders with saved papers.

### `papers://{topic}`
Provides detailed information about all papers saved for a specific topic.

## MCP Prompts

### `generate_search_prompt(topic: str, num_papers: int = 5)`
Generates a comprehensive research prompt for AI assistants to conduct academic literature reviews.

## Data Storage

Papers are organized in the following structure:
```
papers/
  machine_learning/
    papers_info.json
  quantum_computing/
    papers_info.json
  artificial_intelligence/
    papers_info.json
```

Each `papers_info.json` contains:
```json
{
  "paper_id": {
    "title": "Paper Title",
    "authors": ["Author 1", "Author 2"],
    "summary": "Paper abstract/summary",
    "pdf_url": "https://arxiv.org/pdf/...",
    "published": "2024-01-15"
  }
}
```

## Environment Requirements

- Python 3.11+
- Dependencies listed in `requirements.txt`
- Network access to arXiv API

## Configuration

The server runs with the following default settings:
- **Port**: 8001
- **Transport**: Server-Sent Events (SSE)
- **Papers Directory**: `./papers`

## Usage Examples

### Using MCP Tools

1. **Search for papers on a topic:**
   ```
   search_papers("machine learning", 10)
   ```

2. **Get information about a specific paper:**
   ```
   extract_info("2301.12345")
   ```

3. **Browse available research topics:**
   Access the `papers://folders` resource

4. **View papers on a specific topic:**
   Access the `papers://machine_learning` resource

## Integration Guide

### Using with Claude Desktop

Add this configuration to your Claude Desktop MCP settings:

```json
{
  "mcpServers": {
    "arxiv-research": {
      "command": "npx",
      "args": [
        "@modelcontextprotocol/server-fetch",
        "https://arxiv-mcp-server-00jv.onrender.com/sse"
      ]
    }
  }
}
```

### Using from Custom Applications

#### Python Integration

```python
from mcp import ClientSession
from mcp.client.sse import SSEClientTransport

# Connect to your deployed server
transport = SSEClientTransport("https://arxiv-mcp-server-00jv.onrender.com/sse")
session = ClientSession(transport)

# Search for papers
result = await session.call_tool("search_papers", {
    "topic": "machine learning",
    "max_results": 5
})

# Get paper information
paper_info = await session.call_tool("extract_info", {
    "paper_id": "2301.12345"
})

# Access resources
folders = await session.get_resource("papers://folders")
topic_papers = await session.get_resource("papers://machine_learning")
```

#### JavaScript/TypeScript Integration

```javascript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { SSEClientTransport } from "@modelcontextprotocol/sdk/client/sse.js";

const transport = new SSEClientTransport("https://arxiv-mcp-server-00jv.onrender.com/sse");
const client = new Client({}, { transport });

// Search for papers
const searchResult = await client.request({
  method: "tools/call",
  params: {
    name: "search_papers",
    arguments: { topic: "artificial intelligence", max_results: 3 }
  }
});

// Get paper details
const paperInfo = await client.request({
  method: "tools/call",
  params: {
    name: "extract_info",
    arguments: { paper_id: "2301.12345" }
  }
});

// Access resources
const folders = await client.request({
  method: "resources/read",
  params: { uri: "papers://folders" }
});
```

#### Direct HTTP/REST Integration

For languages without MCP client libraries, you can make direct HTTP requests:

```bash
# Example using curl
curl -X POST https://arxiv-mcp-server-00jv.onrender.com/tools/call \
  -H "Content-Type: application/json" \
  -d '{
    "name": "search_papers",
    "arguments": {
      "topic": "quantum computing",
      "max_results": 5
    }
  }'
```

### Integration Use Cases

- **Research Assistants**: Build AI-powered research tools
- **Academic Dashboards**: Create web interfaces for paper discovery
- **Mobile Apps**: Integrate paper search into mobile applications
- **CLI Tools**: Build command-line research utilities
- **Web Applications**: Add academic search to existing platforms
- **Automated Research**: Schedule periodic research updates
- **Educational Tools**: Create learning platforms with paper integration

### Server Endpoints

- **Primary URL**: `https://arxiv-mcp-server-00jv.onrender.com`
- **SSE Endpoint**: `https://arxiv-mcp-server-00jv.onrender.com/sse` (for MCP connections)
- **Health Check**: Server responds with 200 OK on `/sse` endpoint

### Important Notes

- **Free Tier Limitations**: On Render's free tier, the service may sleep after periods of inactivity
- **Cold Starts**: First request after sleep may take 30-60 seconds to respond
- **Persistent Storage**: Papers are saved to the server's file system
- **Rate Limiting**: Consider implementing rate limiting for production use

## API Integration

This server integrates with:
- **arXiv API**: For academic paper search and retrieval
- **FastMCP**: For Model Context Protocol server functionality

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

This project is open source and available under the MIT License.