# Knowledge Framework → Notion Skill

Converts markdown content to Notion API blocks with full support for Knowledge Framework (MECE, BFO ontology) and Mermaid diagrams.

## Installation

### Local Installation
```bash
# Copy to your skills directory
cp -r knowledge-framework-notion ~/.claude/skills/

# Or symlink from project
ln -s /path/to/knowledge-framework-notion ~/.claude/skills/knowledge-framework-notion
```

### For Windmill + Headless Droid
This skill is used via `droid exec` in Windmill flows:
```bash
droid exec -f prompt.md --output-format json --auto low
```

## Features

- **Markdown → Notion Blocks:** Full conversion of headings, paragraphs, lists, quotes, code
- **Mermaid Support:** Automatic detection and conversion to `language: "mermaid"` (Notion auto-renders!)
- **Content Limits:** Handles 2000 char limit with smart splitting
- **Batching:** Splits into 100-block batches for Notion API compliance
- **JSON Output:** Structured output ready for Notion API calls

## Usage

### Automatic Triggers
- "convert to Notion blocks"
- "format for Notion API"
- markdown content + Notion context

### Manual Invocation
```
/skill knowledge-framework-notion
```

### Via Headless Droid (Windmill)
```python
import subprocess
result = subprocess.run([
    "droid", "exec",
    "-f", "/tmp/format_prompt.md",
    "--output-format", "json",
    "--auto", "low"
], capture_output=True)
```

## Output Format

```json
{
  "success": true,
  "blocks": [...],
  "batches": [[...], [...]],
  "metadata": {
    "total_blocks": 45,
    "mermaid_count": 2,
    "code_blocks_count": 5,
    "requires_batching": false
  }
}
```

## Supported Block Types

| Markdown | Notion Block |
|----------|--------------|
| `# H1` | `heading_1` |
| `## H2` | `heading_2` |
| `### H3` | `heading_3` |
| Paragraph | `paragraph` |
| `- item` | `bulleted_list_item` |
| `1. item` | `numbered_list_item` |
| ` ```mermaid ` | `code` (language: mermaid) |
| ` ```python ` | `code` (language: python) |
| `> quote` | `quote` |
| `---` | `divider` |

## Mermaid Handling

Mermaid diagrams are automatically detected by:
1. Explicit ` ```mermaid ` fence
2. Content indicators (`graph TD`, `sequenceDiagram`, etc.)

Output block:
```json
{
  "type": "code",
  "code": {
    "language": "mermaid",
    "rich_text": [{"type": "text", "text": {"content": "graph TD\n    A-->B"}}]
  }
}
```

**Note:** Notion automatically renders Mermaid diagrams when `language: "mermaid"`!

## API Limits

| Limit | Value | Handling |
|-------|-------|----------|
| rich_text content | 2000 chars | Split into multiple text objects |
| children per request | 100 blocks | Batch into multiple requests |
| Nested depth | 2 levels | Flatten if deeper |

## License

MIT
