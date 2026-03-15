---
name: humanize
description: "Scan for AI writing patterns in markdown, docs, comments, and user-facing strings. Detects 24 cataloged AI-writing tells across content, language, style, communication, and filler categories. Triggers: 'check prose', 'AI writing', 'humanize', or /axiom:humanize. Do NOT use for code quality — use other axiom skills."
user-invokable: true
metadata:
  author: lvlup-sw
  version: 0.1.0
  category: assessment
  dimensions:
    - prose-quality
---

# Humanize — Prose Quality Assessment

## Overview

Detects signs of AI-generated writing in prose content. Based on Wikipedia's "Signs of AI writing" guide maintained by WikiProject AI Cleanup. Covers 24 patterns across 5 categories: content, language/grammar, style, communication, and filler/hedging.

## Triggers

**Use when:**
- Reviewing documentation, README files, or user-facing copy
- Checking comments and docstrings for AI tells
- User says "humanize", "check prose", "AI writing patterns", "de-slop"

**Do NOT use when:**
- Reviewing code logic or architecture (use `axiom:critique`)
- Checking error handling (use `axiom:harden`)
- Running full quality audit (use `axiom:audit`, which includes humanize)

## Process

### Step 1: Scope Resolution

Determine assessment scope:
- **File:** Assess a single file
- **Directory:** Assess all prose-containing files (recursive)
- **Codebase:** Assess the entire project

Default file scope: `*.md`, `*.txt`, `*.mdx`, plus comments and user-facing strings in source files (`*.ts`, `*.js`, `*.py`, etc.).

Exclude: `node_modules/`, `dist/`, `.git/`, `CHANGELOG.md`, lock files, binary files, generated files.

### Step 2: Deterministic Scan

Run `axiom:scan` with `dimensions: prose-quality` for the resolved scope. This executes the PQ-* check catalog against the content.

### Step 3: Qualitative Assessment

Layer qualitative assessment on top of scan results:
- Read flagged sections in context -- some patterns are acceptable in certain contexts
- Check for pattern clustering (multiple AI tells in the same paragraph/section is worse than isolated occurrences)
- Assess overall tone -- does the content read as expert-written or machine-generated?
- Apply false-positive filtering per guidance in `@skills/humanize/references/ai-writing-patterns.md`

### Step 4: Produce Findings

Output findings in standard format: `@skills/backend-quality/references/findings-format.md`

Each finding includes:
- `dimension: "prose-quality"`
- `severity: HIGH | MEDIUM | LOW` per `@skills/humanize/references/severity-guide.md`
- `title`: Pattern name and location
- `evidence`: File path and line numbers
- `explanation`: What pattern was detected and why it matters
- `suggestion`: Concrete rewrite suggestion

## Error Handling

- **No prose files in scope:** Return "No prose content found in scope" with no findings
- **Binary/generated files:** Skip silently
- **Large files (>10K lines):** Sample first 1000 and last 1000 lines

## References

- Pattern catalog: `@skills/humanize/references/ai-writing-patterns.md`
- Severity mapping: `@skills/humanize/references/severity-guide.md`
- Finding format: `@skills/backend-quality/references/findings-format.md`
- Dimension taxonomy: `@skills/backend-quality/references/dimensions.md`
