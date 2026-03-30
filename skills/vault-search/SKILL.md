# Vault Search — Obsidian Semantic Search

Search your Obsidian vault using keyword, semantic, and hybrid queries via [qmd](https://www.npmjs.com/package/@tobilu/qmd).

## Prerequisites

- `qmd` installed: `npm install -g @tobilu/qmd`
- Vault indexed: `qmd index -c obsidian -d /path/to/your/vault`
- Recommended: schedule a daily re-index via cron

## Usage

### Keyword Search
```bash
qmd search -c obsidian "meeting notes"
```

### Semantic Search (meaning-based)
```bash
qmd vsearch -c obsidian "how to handle difficult conversations with stakeholders"
```

### Hybrid Search (keyword + semantic + reranking)
```bash
qmd query -c obsidian "design system migration plan"
```

## When to Use

- User asks about something that might be in their notes
- Need to find context before generating a response
- Cross-referencing notes for pattern recognition
- Building summaries from multiple related notes

## Tips

- Always scope with `-c <collection>` to search the right vault
- Hybrid (`qmd query`) gives the best results for most queries
- Semantic (`qmd vsearch`) is better for conceptual/meaning-based lookups
- Keyword (`qmd search`) is fastest and best for exact terms
