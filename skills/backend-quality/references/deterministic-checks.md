# Deterministic Check Catalog

Grep patterns and structural checks organized by dimension. The `scan` skill executes these checks mechanically. Other skills invoke `scan` for their dimensions, then layer qualitative assessment.

Each check has: ID, pattern, what it detects, severity, and false-positive guidance.

## DIM-1: Topology

### T-1.1: Module-global mutable state
- **Pattern:** `^(let|var)\s+\w+\s*[:=]` at file scope (not inside function/class body)
- **Severity:** MEDIUM
- **Detects:** Ambient state that can diverge across module boundaries
- **False positives:** Intentional singletons with documented rationale; module-scoped constants that happen to use `let`

### T-1.2: Lazy fallback constructors
- **Pattern:** `if\s*\(\s*!\w+\s*\)\s*\{?\s*\w+\s*=\s*new\s`
- **Severity:** HIGH
- **Detects:** Degraded-mode instances created silently when wiring is missing
- **False positives:** Intentional lazy initialization with logging/telemetry

### T-1.3: Manual wiring functions
- **Pattern:** `^export\s+(async\s+)?function\s+(configure|register|setup|init)\w+\s*\(`
- **Severity:** MEDIUM
- **Detects:** Manual dependency wiring that requires correct call order
- **False positives:** Framework-required setup functions (e.g., Express middleware registration)

## DIM-2: Observability

### T-2.1: Empty catch blocks
- **Pattern:** `catch\s*(\(\w*\))?\s*\{\s*\}`
- **Severity:** HIGH
- **Detects:** Errors swallowed silently with no handling
- **False positives:** Intentional swallow with comment explaining rationale (rare, should be flagged for review)

### T-2.2: Catch with only console.log
- **Pattern:** `catch\s*\(\w+\)\s*\{\s*console\.(log|warn)\(`
- **Severity:** MEDIUM
- **Detects:** Error "handling" that only logs without context or recovery
- **False positives:** Development-only logging in test helpers

### T-2.3: Swallowed promise rejections
- **Pattern:** `\.catch\(\s*\(\)\s*=>\s*\{?\s*\}?\s*\)`
- **Severity:** HIGH
- **Detects:** Promise rejections silently ignored
- **False positives:** Intentional fire-and-forget with documented rationale

## DIM-3: Contracts

### T-3.1: Unsafe type assertions
- **Pattern:** `\bas\s+(?!const\b)\w+`
- **Severity:** MEDIUM
- **Detects:** Type assertions that bypass TypeScript's type checking
- **False positives:** Assertions after validated guards (e.g., `as Foo` after `instanceof Foo` check)

### T-3.2: Non-null assertions
- **Pattern:** `\w+!\.` or `\w+!\[`
- **Severity:** MEDIUM
- **Detects:** Non-null assertions that assume a value exists without checking
- **False positives:** Assertions after explicit null checks in the same scope

### T-3.3: Schema-type divergence (manual check)
- **Execution:** manual (excluded from deterministic scan)
- **Pattern:** Compare Zod schemas against TypeScript interfaces for missing/extra fields
- **Severity:** HIGH
- **Detects:** Runtime schema doesn't match compile-time types
- **False positives:** Intentional partial schemas (e.g., input validation that accepts a subset)

## DIM-4: Test Fidelity

### T-4.1: Skipped tests
- **Pattern:** `(describe|it|test)\.(skip|todo)\(`
- **Severity:** MEDIUM
- **Detects:** Tests that are disabled without tracked resolution
- **False positives:** Tests with linked issue references (`// TODO(#123)`)

### T-4.2: Mock-heavy tests
- **Pattern:** Count `vi\.mock|jest\.mock|sinon\.stub` per test file; flag if >3
- **Severity:** MEDIUM
- **Detects:** Over-isolation that hides integration behavior
- **False positives:** Tests for modules with many infrastructure boundaries

### T-4.3: Test setup divergence (manual check)
- **Execution:** manual (excluded from deterministic scan)
- **Pattern:** Compare test factory/setup functions against production initialization code
- **Severity:** HIGH
- **Detects:** Test wiring that doesn't match production wiring
- **False positives:** Intentional test-only configuration (e.g., in-memory DB for speed)

## DIM-5: Hygiene

### T-5.1: Commented-out code blocks
- **Pattern:** `^\s*\/\/\s*(function|class|const|let|var|if|for|while|return|export)\s` (3+ consecutive lines)
- **Severity:** MEDIUM
- **Detects:** Code preserved in comments instead of version control
- **False positives:** Documentation examples that happen to look like commented code

### T-5.2: Unused exports
- **Pattern:** Cross-reference `export` declarations against `import` statements project-wide
- **Severity:** LOW
- **Detects:** Exported symbols with no consumers
- **False positives:** Public API surface intentionally exported for external consumers

### T-5.3: Unreachable code
- **Pattern:** Code after unconditional `return`, `throw`, `break`, or `continue`
- **Severity:** MEDIUM
- **Detects:** Dead code that can never execute
- **False positives:** Generated code with defensive returns

## DIM-6: Architecture

### T-6.1a: Deep nesting
- **Pattern:** Count indentation levels per function; flag functions with >3 levels of nesting
- **Severity:** MEDIUM
- **Detects:** Functions where business logic is buried under layers of conditional checks
- **False positives:** Nesting from resource scoping (`try`/`with` blocks) or framework-required patterns

### T-6.1b: Long functions
- **Pattern:** Functions exceeding 50 lines (measured from signature to closing brace)
- **Severity:** MEDIUM
- **Detects:** Functions doing too much — multiple concerns in a single body
- **False positives:** Functions with long but simple data declarations (mapping tables, configuration objects)

### T-6.1c: Long parameter lists
- **Pattern:** Functions with more than 4 parameters in the signature
- **Severity:** MEDIUM
- **Detects:** Functions with too many inputs, suggesting they handle multiple concerns or need an options object
- **False positives:** Constructor functions in DI-heavy codebases where parameters are injected dependencies; callback-heavy APIs where signature is dictated by framework

### T-6.1d: Deep inheritance
- **Pattern:** `class\s+\w+\s+extends\s+` — flag when inheritance chain exceeds 2 levels
- **Severity:** MEDIUM
- **Detects:** Class hierarchies that would be simpler as composition
- **False positives:** Framework-required inheritance (e.g., extending base controller classes); sealed hierarchies with exhaustive matching

### T-6.1: Circular imports
- **Pattern:** Build import graph; detect cycles
- **Severity:** HIGH
- **Detects:** Circular dependencies between modules
- **False positives:** Type-only imports that don't create runtime cycles

### T-6.2: God objects
- **Pattern:** Files with >500 lines or classes/objects with >10 exported members
- **Severity:** MEDIUM
- **Detects:** Modules with too many responsibilities
- **False positives:** Intentional facades or barrel files

### T-6.3: Dependency direction violations
- **Pattern:** Core/domain modules importing from infrastructure/CLI/UI modules
- **Severity:** MEDIUM
- **Detects:** Inverted dependency direction (core depends on periphery)
- **False positives:** Requires project-specific layer definitions to assess accurately

## DIM-7: Resilience

### T-7.1: Unbounded collections
- **Pattern:** `new\s+(Map|Set|Array)\s*\(` without nearby `.delete`, `.clear`, or size check
- **Severity:** HIGH
- **Detects:** Collections that grow without bound
- **False positives:** Collections with short, bounded lifetimes (e.g., within a request handler)

### T-7.2: Missing timeouts
- **Pattern:** `fetch\(` or `http\.(get|post|put)` without `signal`, `timeout`, or `AbortController`
- **Severity:** MEDIUM
- **Detects:** External calls that could hang indefinitely
- **False positives:** Calls to localhost services with guaranteed fast response

### T-7.3: Unbounded retry loops
- **Pattern:** `while\s*\(.*retry` or `for\s*\(.*attempt` without max limit
- **Severity:** HIGH
- **Detects:** Retry logic that could loop forever
- **False positives:** Loops with break conditions that aren't pattern-matched

## DIM-8: Prose Quality

### PQ-8.1: AI vocabulary clustering
- **Pattern:** Count of `(additionally|crucial|delve|foster|garner|intricate|landscape|pivotal|tapestry|testament|underscore|vibrant)` per paragraph; flag if >=3
- **Severity:** MEDIUM
- **Detects:** Clustering of words disproportionately used by language models
- **False positives:** Technical contexts where "landscape" means literal landscape, or "crucial" is genuinely appropriate

### PQ-8.2: Chatbot collaborative artifacts
- **Pattern:** `(I hope this helps|Of course!|Certainly!|You're absolutely right|Would you like|let me know if|here is a)`
- **Severity:** HIGH
- **Detects:** Chatbot correspondence language left in shipped content
- **False positives:** Legitimate user-facing chat interfaces; FAQ sections

### PQ-8.3: Knowledge-cutoff disclaimers
- **Pattern:** `(as of (my|this|the) (last|latest)|up to my last training|based on available information|while specific details are (limited|scarce))`
- **Severity:** HIGH
- **Detects:** AI model disclaimers about incomplete knowledge left in documentation
- **False positives:** Legitimate date-scoped statements ("as of the latest release")

### PQ-8.4: Sycophantic openers
- **Pattern:** `^(Great question|That's a great|Excellent point|Absolutely[!,]|What a (great|wonderful|fantastic))`
- **Severity:** HIGH
- **Detects:** People-pleasing language inappropriate for technical documentation
- **False positives:** None in technical docs; rare in any professional writing

### PQ-8.5: Em dash density
- **Pattern:** Count of `\u2014` (em dash) per file; flag if density > 1 per 100 words
- **Severity:** LOW
- **Detects:** Overuse of em dashes mimicking punchy sales writing
- **False positives:** Quoted dialogue; style guides that favor em dashes

### PQ-8.6: Superficial -ing analyses
- **Pattern:** `(highlighting|underscoring|emphasizing|ensuring|reflecting|symbolizing|contributing to|cultivating|fostering|encompassing|showcasing)\s+(the|its|a|an)`
- **Severity:** MEDIUM
- **Detects:** Present participle phrases tacked on for fake depth
- **False positives:** Active voice descriptions of ongoing processes

### PQ-8.7: Inflated significance
- **Pattern:** `(serves as a testament|stands as a reminder|pivotal (role|moment)|indelible mark|key turning point|evolving landscape|setting the stage|focal point of)`
- **Severity:** MEDIUM
- **Detects:** Inflated language that elevates mundane topics to cosmic importance
- **False positives:** Historical or genuinely significant events

### PQ-8.8: Promotional language
- **Pattern:** `(boasts a|in the heart of|groundbreaking|renowned|breathtaking|must-visit|stunning|nestled)`
- **Severity:** MEDIUM
- **Detects:** Loss of neutral tone, especially on cultural/heritage topics
- **False positives:** Marketing copy where promotional tone is intentional

### PQ-8.9: Filler phrases
- **Pattern:** `(in order to|due to the fact that|at this point in time|in the event that|has the ability to|it is worth noting that|it is important to note)`
- **Severity:** LOW
- **Detects:** Wordy phrases that can be simplified
- **False positives:** Legal or formal contexts where precision requires verbosity

### PQ-8.10: Generic positive conclusions
- **Pattern:** `(the future looks bright|exciting times|looking forward to|great potential|paving the way|on the right track)`
- **Severity:** MEDIUM
- **Detects:** Vague upbeat endings without substance
- **False positives:** Genuine forward-looking statements with specific plans attached

## Extensibility

Projects can add custom checks via `.axiom/checks.md` at the repo root. Format matches this file — one section per dimension, each check with pattern, severity, description, and false-positive guidance. Custom checks are loaded alongside the built-in catalog by the `scan` skill.
