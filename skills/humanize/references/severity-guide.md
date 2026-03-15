# Prose Quality Severity Guide

Severity mappings for each AI-writing pattern, with rationale and frequency-based escalation rules.

---

## HIGH Severity

Dead giveaways of unreviewed AI output. These should never appear in shipped content.

| Pattern ID | Pattern Name | Rationale |
|-----------|-------------|-----------|
| PQ-4.1 | Collaborative artifacts | Chatbot language ("I hope this helps") in documentation is an obvious copy-paste from an AI session |
| PQ-4.2 | Knowledge-cutoff disclaimers | "As of my last training update" in docs reveals AI-generated content that was never reviewed |
| PQ-4.3 | Sycophantic tone | "Great question!" in technical docs is inappropriate in any context |
| PQ-2.1 | AI vocabulary (5+ cluster) | Five or more AI-typical words in one paragraph creates an unmistakable AI fingerprint |

---

## MEDIUM Severity

Patterns that cluster to create an AI-generated feel. Individually tolerable, but multiple occurrences signal low editorial effort.

| Pattern ID | Pattern Name | Rationale |
|-----------|-------------|-----------|
| PQ-1.1 | Inflated significance | Grandiose framing makes mundane topics sound absurd |
| PQ-1.3 | Superficial -ing analyses | Participial phrases that restate the obvious in fancier terms |
| PQ-1.4 | Promotional language | Loss of neutral, technical tone |
| PQ-2.1 | AI vocabulary (3-4 cluster) | Noticeable AI fingerprint but not overwhelming |
| PQ-2.2 | Copula avoidance | Over-formal "serves as" / "stands as" instead of simple "is" |
| PQ-2.3 | Negative parallelisms | "Not only...but also" rhetorical formula |
| PQ-5.3 | Generic positive conclusions | Vague upbeat endings with no substance |
| PQ-1.5 | Vague attributions | False authority via unnamed experts |
| PQ-1.6 | Formulaic sections | Template-like section transitions |
| PQ-2.4 | Rule of three overuse | Forced triadic groupings |
| PQ-2.6 | False ranges | Artificial ranges with non-comparable endpoints |
| PQ-1.2 | Notability emphasis | Unsubstantiated notability claims |

---

## LOW Severity

Minor style issues. Worth fixing in a polish pass but not urgent.

| Pattern ID | Pattern Name | Rationale |
|-----------|-------------|-----------|
| PQ-3.1 | Em dash overuse | Style preference; only a problem at high density |
| PQ-3.5 | Emojis in prose | May be intentional per project conventions |
| PQ-5.1 | Filler phrases (isolated) | Single occurrences of wordy phrases are minor |
| PQ-5.2 | Excessive hedging (isolated) | Some hedging is appropriate for uncertainty |
| PQ-3.2 | Boldface overuse | Visual noise but does not affect meaning |
| PQ-3.3 | Inline-header vertical lists | Format preference; appropriate in some contexts |
| PQ-3.4 | Title case headings | Style guide dependent |
| PQ-3.6 | Curly quotation marks | Typographic preference |
| PQ-2.5 | Elegant variation | Confusing synonym cycling |

---

## Frequency-Based Escalation Rules

Base severity can be escalated based on pattern density:

### AI Vocabulary (PQ-2.1)
- **1 occurrence in paragraph:** Ignore (no finding)
- **3-4 occurrences in paragraph:** MEDIUM
- **5+ occurrences in paragraph:** HIGH
- **3+ occurrences across file (non-clustered):** MEDIUM

### Structural Tells (PQ-3.x)
- **1-2 occurrences in file:** LOW
- **3+ occurrences in file:** MEDIUM

### Filler Phrases (PQ-5.1)
- **1 occurrence in paragraph:** Ignore
- **3+ in paragraph:** LOW
- **5+ in paragraph:** MEDIUM
- **3+ across file:** LOW

### Communication Patterns (PQ-4.x)
- **Any occurrence:** HIGH (no escalation needed; always critical)

### Content Patterns (PQ-1.x)
- **Isolated occurrence:** Base severity
- **3+ in same section:** Escalate one level (LOW -> MEDIUM, MEDIUM -> HIGH)

---

## Context Modifiers

Severity may be adjusted based on where the pattern appears:

| Context | Modifier |
|---------|----------|
| User-facing documentation (README, guides) | No change (base severity) |
| Internal comments and docstrings | Reduce one level |
| Marketing copy or landing pages | Promotional language (PQ-1.4) acceptable; reduce to LOW |
| Changelogs and release notes | Formulaic sections (PQ-1.6) acceptable; reduce to LOW |
| Chat interface copy | Collaborative artifacts (PQ-4.1) acceptable; reduce to LOW |
