# AI Writing Patterns Catalog

24 cataloged patterns for detecting AI-generated prose, organized into 5 categories. Based on Wikipedia's "Signs of AI writing" guide maintained by WikiProject AI Cleanup.

---

## Category 1: Content Patterns

### PQ-1.1: Inflated Significance

- **Detection keywords/regex:** `(serves as a testament|pivotal (role|moment)|indelible mark|setting the stage|key turning point|evolving landscape)`
- **Problem:** Elevates mundane topics to cosmic importance. AI models default to grandiose framing because training data over-represents formal and promotional writing.
- **Before:** "This module serves as a testament to the team's commitment to fostering robust architecture."
- **After:** "This module handles event routing."
- **False-positive guidance:** Historical or genuinely significant events may warrant strong language. If the subject is a literal turning point (e.g., a major version migration), the phrase may be appropriate.

### PQ-1.2: Notability Emphasis

- **Detection keywords/regex:** `(independent coverage|leading expert|active social media presence|widely recognized|notable for)`
- **Problem:** Asserts notability without evidence. AI models insert Wikipedia-style notability markers to make subjects sound important.
- **Before:** "The library has received independent coverage from leading experts in the field."
- **After:** "The library is used by 200+ projects on GitHub."
- **False-positive guidance:** Legitimate when backed by specific citations. Flag only when the claim is vague or unsourced.

### PQ-1.3: Superficial -ing Analyses

- **Detection keywords/regex:** `(highlighting|underscoring|emphasizing|ensuring|reflecting|symbolizing|contributing to|cultivating|fostering|encompassing|showcasing)\s+(the|its|a|an)`
- **Problem:** Present participle phrases tacked on to create an illusion of depth. The -ing verb adds no information -- it just restates the obvious in fancier terms.
- **Before:** "The error handler catches exceptions, ensuring the application remains stable while highlighting the importance of robust error handling."
- **After:** "The error handler catches exceptions and returns a 500 status code."
- **False-positive guidance:** Active voice descriptions of ongoing processes are fine ("the service is currently processing requests"). Flag when the -ing phrase adds no new information.

### PQ-1.4: Promotional Language

- **Detection keywords/regex:** `(boasts a|vibrant|rich\s+(history|tradition|culture|ecosystem)|profound|nestled|in the heart of|groundbreaking|renowned|breathtaking|must-visit|stunning)`
- **Problem:** Loss of neutral, technical tone. AI models default to promotional language especially for cultural, geographic, or heritage topics.
- **Before:** "The framework boasts a vibrant ecosystem of plugins and a rich tradition of community-driven development."
- **After:** "The framework has 340 plugins maintained by 50+ contributors."
- **False-positive guidance:** Marketing copy where promotional tone is intentional. Technical docs should almost never use these words.

### PQ-1.5: Vague Attributions

- **Detection keywords/regex:** `(Industry reports|Observers have cited|Experts argue|Some critics argue|Many believe|It is widely believed|According to sources)`
- **Problem:** Creates false authority by attributing claims to unnamed experts. AI models use vague attribution when they lack specific sources.
- **Before:** "Experts argue that this approach represents a paradigm shift."
- **After:** "Martin Fowler's 2019 article 'Microservices Trade-Offs' argues this approach reduces deployment coupling."
- **False-positive guidance:** Legitimate when the vagueness is intentional (e.g., summarizing a debate without taking sides). Flag when used to bolster a specific claim.

### PQ-1.6: Formulaic Sections

- **Detection keywords/regex:** `(Despite its.*faces several challenges|Despite these challenges|Future Outlook|Looking Ahead|In Conclusion)`
- **Problem:** Template-like section transitions that signal generated structure. Real writers don't mechanically alternate between positive and negative sections.
- **Before:** "Despite its many strengths, the framework faces several challenges. Despite these challenges, the future looks bright."
- **After:** "Two open issues affect production use: memory leaks under high concurrency (#234) and missing ARM64 support (#567)."
- **False-positive guidance:** "In Conclusion" is conventional in academic writing. Flag primarily in technical documentation and README files.

---

## Category 2: Language/Grammar Patterns

### PQ-2.1: AI Vocabulary Words

- **Detection keywords/regex:** `(additionally|align with|crucial|delve|emphasizing|enduring|enhance|fostering|garner|highlight\b|interplay|intricate|intricacies|key\s+(aspect|component|feature|element)|landscape\b|pivotal|showcase|tapestry|testament|underscore\b|valuable|vibrant)`
- **Problem:** These words appear far more frequently in AI output than in human writing. Individually they are fine; clustered together they create an unmistakable AI fingerprint.
- **Before:** "This crucial module delves into the intricate tapestry of event-sourced workflows, highlighting the interplay between producers and consumers."
- **After:** "This module stores events in SQLite and rebuilds state on read."
- **False-positive guidance:** Any single occurrence is fine. Flag when 3+ appear in the same paragraph. In technical writing, "key" as an adjective and "landscape" as a metaphor are the most common false positives.

### PQ-2.2: Copula Avoidance

- **Detection keywords/regex:** `(serves as|stands as|marks a|represents a|constitutes a|functions as|acts as a)\s+(a |an |the )`
- **Problem:** AI models avoid simple "is/are" constructions in favor of fancier verbs. This makes prose feel stilted and over-formal.
- **Before:** "This module serves as a bridge between the event store and the query layer."
- **After:** "This module is the bridge between the event store and the query layer." (Or better: "This module connects the event store to the query layer.")
- **False-positive guidance:** "Acts as" and "functions as" are sometimes genuinely clearer than "is" when describing behavior rather than identity.

### PQ-2.3: Negative Parallelisms

- **Detection keywords/regex:** `(Not only.*but (also)?|It's not just about.*it's (also)?|more than just|goes beyond)`
- **Problem:** Formulaic rhetorical structure that AI uses to build artificial momentum. Rarely adds meaning.
- **Before:** "It's not just about handling errors, it's about building a culture of reliability."
- **After:** "The error handler retries transient failures and circuits open after 3 consecutive timeouts."
- **False-positive guidance:** Legitimate in persuasive writing (blog posts, conference talks). Flag in technical docs and code comments.

### PQ-2.4: Rule of Three Overuse

- **Detection keywords/regex:** Three comma-separated items in a row, especially adjectives or abstract nouns. Manual check -- no single regex.
- **Problem:** AI models force ideas into groups of three for rhetorical effect, even when only two points exist or four would be more accurate.
- **Before:** "The system is fast, reliable, and scalable." (when only speed was measured)
- **After:** "The system handles 10K requests/second with p99 latency under 50ms."
- **False-positive guidance:** Three items is often natural. Flag only when items feel padded to reach three, or when the third item is vague/generic.

### PQ-2.5: Elegant Variation

- **Detection keywords/regex:** Manual check -- look for excessive synonym cycling within a paragraph (e.g., "users"/"individuals"/"people"/"stakeholders" all referring to the same group).
- **Problem:** AI models cycle through synonyms to avoid repetition, creating confusion about whether different terms refer to different things.
- **Before:** "Users configure the system. Individuals can customize their preferences. People set up notifications. Stakeholders manage access."
- **After:** "Users configure the system, customize preferences, set up notifications, and manage access."
- **False-positive guidance:** Technical writing should use consistent terms. Some variation is natural in longer prose. Flag when variation creates ambiguity.

### PQ-2.6: False Ranges

- **Detection keywords/regex:** `(from\s+\w+\s+to\s+\w+|range from|spanning from)`
- **Problem:** AI creates artificial ranges where the endpoints are not on a meaningful scale. "From beginners to experts" or "from development to production" are usually filler.
- **Before:** "The tool serves everyone from beginners to seasoned professionals."
- **After:** "The tool requires no prior experience with event sourcing."
- **False-positive guidance:** Legitimate when describing an actual continuum with meaningful endpoints. Flag when the range is vague or the endpoints are not comparable.

---

## Category 3: Style Patterns

### PQ-3.1: Em Dash Overuse

- **Detection keywords/regex:** Count of `\u2014` (em dash) per file; flag if density > 1 per 100 words.
- **Problem:** ChatGPT overuses em dashes to create punchy, sales-copy-like rhythm. Natural writing uses them sparingly.
- **Before:** "The system handles events -- all of them -- in real time -- no exceptions."
- **After:** "The system handles all events in real time."
- **False-positive guidance:** Quoted dialogue and some editorial styles favor em dashes. Check overall density rather than flagging individual occurrences.

### PQ-3.2: Boldface Overuse

- **Detection keywords/regex:** Count of `\*\*[^*]+\*\*` per section; flag if >3 bold phrases per paragraph.
- **Problem:** AI models mechanically emphasize phrases. Too much boldface reduces its impact and creates visual noise.
- **Before:** "The **event store** provides **reliable** persistence with **guaranteed** ordering and **automatic** compaction."
- **After:** "The event store provides reliable persistence with guaranteed ordering and automatic compaction."
- **False-positive guidance:** Reference docs and API documentation often use bold for parameter names. Flag only in prose paragraphs, not in structured reference material.

### PQ-3.3: Inline-Header Vertical Lists

- **Detection keywords/regex:** `^\s*[-*]\s+\*\*[^*]+\*\*:` (bulleted items with bolded headers followed by colons)
- **Problem:** AI defaults to this format for any list, even when a simple bulleted list or a paragraph would be clearer. The pattern creates unnecessary visual weight.
- **Before:**
  ```
  - **Performance:** The system is fast
  - **Reliability:** The system is reliable
  - **Scalability:** The system scales
  ```
- **After:** "The system is fast, reliable, and scales horizontally."
- **False-positive guidance:** This format is appropriate for glossaries, API parameter docs, and reference material. Flag when used for simple lists that would read better as prose.

### PQ-3.4: Title Case Headings

- **Detection keywords/regex:** Headings where all major words are capitalized (e.g., `^#+\s+([A-Z][a-z]+\s+){2,}[A-Z]`)
- **Problem:** AI defaults to title case headings. Most modern style guides (Google, Microsoft, AP) recommend sentence case for headings in technical writing.
- **Before:** `## Getting Started With Event Sourcing`
- **After:** `## Getting started with event sourcing`
- **False-positive guidance:** Title case is correct for proper nouns, product names, and some style guides (APA). This is a style preference, not a hard rule. Flag only when the project's own style guide prefers sentence case.

### PQ-3.5: Emojis in Prose

- **Detection keywords/regex:** Unicode emoji characters in headings or bullet points (excluding code blocks and quoted strings).
- **Problem:** AI models decorate headings and lists with emojis. Professional technical writing almost never uses emojis in headings or body text.
- **Before:** "## Features", "- Fast performance", "- Easy setup"
- **After:** "## Features", "- Fast performance", "- Easy setup" (identical but without emojis)
- **False-positive guidance:** Some projects intentionally use emojis (changelogs, README badges). Check project conventions before flagging.

### PQ-3.6: Curly Quotation Marks

- **Detection keywords/regex:** `[\u2018\u2019\u201C\u201D]` (Unicode curly single and double quotes)
- **Problem:** ChatGPT uses curly (typographic) quotation marks instead of straight quotes. In code-adjacent documentation, straight quotes are standard.
- **Before:** \u201CThis is a string\u201D
- **After:** "This is a string"
- **False-positive guidance:** Curly quotes are correct in published prose (books, articles). Flag only in technical documentation and code-adjacent content.

---

## Category 4: Communication Patterns

### PQ-4.1: Collaborative Artifacts

- **Detection keywords/regex:** `(I hope this helps|Of course!|Certainly!|You're absolutely right|Would you like|let me know if|here is a|feel free to|don't hesitate to|happy to help)`
- **Problem:** Chatbot correspondence language left in shipped content. These phrases only make sense in a conversation between a user and an AI assistant.
- **Before:** "I hope this helps! Let me know if you'd like me to explain anything else."
- **After:** (Delete entirely -- this is chatbot conversation, not documentation.)
- **False-positive guidance:** Legitimate in user-facing chat interfaces and FAQ sections where conversational tone is intentional. Flag in all other documentation.

### PQ-4.2: Knowledge-Cutoff Disclaimers

- **Detection keywords/regex:** `(as of (my|this|the) (last|latest)|up to my last training|based on available information|while specific details are (limited|scarce)|at the time of (writing|this response))`
- **Problem:** AI model disclaimers about incomplete knowledge left in documentation. These reveal unreviewed AI output.
- **Before:** "As of my last training update, the API supports three endpoints."
- **After:** "The API supports three endpoints (v2.3.0)."
- **False-positive guidance:** "As of the latest release" or "at the time of writing" with a specific date are legitimate. Flag only when the phrasing reveals an AI knowledge cutoff.

### PQ-4.3: Sycophantic Tone

- **Detection keywords/regex:** `(Great question|That's a great|Excellent point|Absolutely[!,]|What a (great|wonderful|fantastic)|You're right to|Good thinking)`
- **Problem:** People-pleasing language that is inappropriate in technical documentation. Signals unreviewed AI output pasted directly from a chat session.
- **Before:** "Great question! The event store uses SQLite for persistence."
- **After:** "The event store uses SQLite for persistence."
- **False-positive guidance:** None in technical docs. Rare in any professional writing. Almost always an AI tell.

---

## Category 5: Filler/Hedging Patterns

### PQ-5.1: Filler Phrases

- **Detection keywords/regex:** `(in order to|due to the fact that|at this point in time|in the event that|has the ability to|it is worth noting that|it is important to note|it should be noted that|needless to say)`
- **Problem:** Wordy phrases that can be simplified without losing meaning. AI models use them to pad output length.
- **Before:** "In order to ensure that the system has the ability to handle errors, it is important to note that..."
- **After:** "To handle errors..."
- **Simplification table:**
  | Filler | Replacement |
  |--------|-------------|
  | In order to | To |
  | Due to the fact that | Because |
  | At this point in time | Now |
  | In the event that | If |
  | Has the ability to | Can |
  | It is worth noting that | (delete or rephrase) |
  | It is important to note | (delete or rephrase) |
- **False-positive guidance:** Legal or formal contexts where precision requires verbosity. "In order to" is sometimes clearer than "to" when the infinitive could be misread.

### PQ-5.2: Excessive Hedging

- **Detection keywords/regex:** `(could potentially|might possibly|may potentially|it could be argued|one might suggest|there is a possibility|to some extent|in some cases|it is possible that)`
- **Problem:** Over-qualifying statements strips them of usefulness. Technical docs should be direct.
- **Before:** "It could potentially be argued that this approach might possibly offer some advantages in certain scenarios."
- **After:** "This approach reduces memory usage by 40%."
- **False-positive guidance:** Some hedging is appropriate when genuinely uncertain (e.g., "performance may vary depending on hardware"). Flag stacked hedges (2+ qualifying phrases in one sentence).

### PQ-5.3: Generic Positive Conclusions

- **Detection keywords/regex:** `(the future looks bright|exciting times|looking forward to|great potential|paving the way|on the right track|poised for|well-positioned)`
- **Problem:** Vague upbeat endings without substance. AI models default to optimistic conclusions that say nothing specific.
- **Before:** "The future looks bright for this framework, and we look forward to exciting times ahead."
- **After:** "The v3.0 roadmap includes WebSocket support (Q2) and horizontal scaling (Q3)."
- **False-positive guidance:** Genuine forward-looking statements with specific plans attached are fine. Flag only when the conclusion is vague and could apply to anything.

---

## Frequency-Based Escalation

Not all occurrences warrant the same severity. Use these escalation rules:

| Pattern Type | 1 occurrence | 3+ in paragraph | 5+ in paragraph | 3+ in file |
|-------------|-------------|-----------------|-----------------|------------|
| AI vocabulary (PQ-2.1) | Ignore | MEDIUM | HIGH | MEDIUM |
| Structural tells (PQ-3.x) | LOW | MEDIUM | MEDIUM | MEDIUM |
| Filler phrases (PQ-5.1) | Ignore | LOW | MEDIUM | LOW |
| Communication (PQ-4.x) | HIGH | HIGH | HIGH | HIGH |
