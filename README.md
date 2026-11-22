# academia-dl

[![PyPI version](https://img.shields.io/pypi/v/academia-dl.svg)](https://pypi.org/project/academia-dl/)
[![Python versions](https://img.shields.io/pypi/pyversions/academia-dl.svg)](https://pypi.org/project/academia-dl/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

High-performance, stealth command-line research suite and Python library for searching, citing, and archiving academic papers from Academia.edu.

Created by Antigravity & Q.S@RDOR.

---

## Overview

Academia.edu restricts direct PDF downloads behind forced registration modals and Cloudflare challenge gates. `academia-dl` bypasses these restrictions using a pure Python Fast-TLS Engine (`curl_cffi` with Chrome TLS fingerprint impersonation) that discovers direct CloudFront signed streams in ~1.2 seconds with negligible memory overhead (~15 MB).

Available both as a standalone CLI tool and an importable Python SDK.

---

## Installation

### Via pip (Official PyPI Package):
```bash
pip install academia-dl
```

### Direct Standalone Portability:
No installation required. `academia_dl.py` is a 100% self-contained single-file script that auto-bootstraps its own dependencies at runtime without requiring a virtual environment:
```bash
python academia_dl.py 2945047
```

---

## CLI Usage

### Download Papers

Download by paper ID or full URL:

```bash
# Download by paper ID
academia 2945047

# Download by full URL
academia https://www.academia.edu/83064712/Title

# Custom destination path
academia 2945047 ./papers/output.pdf

# Open in default PDF viewer immediately
academia 2945047 -o

# Print signed stream URL without downloading
academia 2945047 -u
```

### Inspect Metadata

Display structured metadata card (title, authors, year, journal, DOI, abstract):

```bash
academia info 83064712

# Enrich with CrossRef data (DOI, publisher, citation counts)
academia info 83064712 --enrich

# Machine-readable JSON output
academia info 83064712 --json
```

### Generate Citations

Export bibliographic citations in standard formats:

```bash
# BibTeX (default)
academia cite 83064712

# Other formats: apa, mla, ris, all
academia cite 83064712 --format apa
academia cite 83064712 --format mla
academia cite 83064712 --format ris
academia cite 83064712 --format all
```

### Intelligent Search

Multi-factor academic search with BM25 ranking, query operators, and title token deduplication:

```bash
# Standard search
academia search "quantum error correction"

# Search with filters (author, year range, PDF availability)
academia search "neural networks author:Hinton year:2020..2024 has:pdf"

# Thematic clustering and CrossRef enrichment
academia search "reinforcement learning" --cluster --enrich --limit 8

# Batch download search results
academia search "deep learning" --limit 5 --download --outdir ./downloads

# Search researchers and academics
academia search "Andrew Ng" --mode people
```

### Related Papers & Research Graph

Traverse Academia's reading graph to find semantically linked papers:

```bash
# List related papers
academia related 2945047 --limit 10

# Download all related papers
academia related 2945047 --limit 5 --download --outdir ./related_papers
```

### Author Profile Harvester

Extract and download works published by an author:

```bash
# By name or profile URL
academia author "Marina Rabadan"

# Download all author papers
academia author "https://liverpool.academia.edu/MarinaRabad%C3%A1nG%C3%B3mez" --download
```

### Topic Feeds

Browse recent papers by field or department:

```bash
academia topic Teaching_of_Foreign_Languages --limit 20
academia topic Teaching_of_Foreign_Languages --limit 10 --download --outdir ./field_papers
```

### Batch Downloads

Download multiple papers listed in a text file (one URL or ID per line, `#` for comments):

```bash
academia batch list.txt --concurrency 3 --skip-existing --outdir ./archive
```

---

## Python Library Usage (SDK)

`academia-dl` can be imported directly into Python scripts, automation pipelines, and AI agent frameworks:

```python
import academia_dl

# 1. Download paper and receive file metadata
res = academia_dl.download("2945047", out_file="paper.pdf")
print("Saved to:", res["savedTo"])
print("Size:", res["sizeFormatted"])

# 2. Search papers with BM25 ranking
results = academia_dl.search("transformers author:Vaswani", size=5)
for p in results["results"]:
    print(f"- {p['title']} (ID: {p['id']})")

# 3. Generate citations
cites = academia_dl.cite("2945047")
print(cites["citations"]["bibtex"])

# 4. Resolve metadata
meta = academia_dl.resolve_paper("2945047", require_stream=False)
print("Abstract:", meta["abstract"])
```

---

## Agent & Automation Integration

Built for autonomous AI agents, LLM pipelines, and automated scraping workflows:

### 1. Machine-Readable Schema Introspection
```bash
academia spec
```

### 2. Zero-Pollution Pure JSON Mode
When `--json` is supplied or `ACADEMIA_JSON=1` is set in the environment, `stdout` outputs strictly parseable JSON with zero log or progress-bar contamination:
```bash
academia search "quantum error correction" --limit 5 --json
academia info 2945047 --json
academia cite 2945047 --json
```

### 3. Single-Line Path Extraction
To download a paper and pipe its absolute path directly into downstream extractors (`pdftotext`, OCR, vector stores):
```bash
academia download 2945047 --path-only
# Outputs solely:
# /path/to/downloads/Differentiation_or_how_to_deal_with_a_mixed-ability_class.pdf
```

### 4. Structured Error Serialization
On failure in `--json` mode, exits with code `1` and returns a structured error object on `stdout`:
```json
{
  "status": "error",
  "error": "Paper \"...\" has no downloadable PDF stream",
  "code": "ERR_COMMAND_FAILED"
}
```

---

## License

MIT
