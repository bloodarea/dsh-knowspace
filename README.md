# dsh-knowspace

**Agent-native, local-first knowledge infrastructure for DeepSeekHarness.**

[English](README.md) | [简体中文](README_zh.md)

> **Status: Work in progress · Public placeholder · No public alpha release**  
> Last README update: October 12, 2026. Development continues in a private repository. This public repository currently serves as the project's official landing page; it does not provide an installable plugin or publicly auditable source code.

`dsh-knowspace` aims to make personal knowledge available to both people and authorized AI agents without making a cloud service, a vector database, or an always-on AI process the owner of that knowledge. It is designed as a [DeepSeekHarness](https://github.com/deepseek-ai/deepseek-harness) plugin, not a separate knowledge server.

The long-term goal is a shared foundation for notes, retrieval, rendering, and interaction that other agents, Skills, scripts, and domain-specific plugins can reuse.

## Why KnowSpace?

Most AI workflows treat knowledge as one-off chat context or an external retrieval service. KnowSpace takes a different approach:

- **Your files are the source of truth.** Markdown notes and images remain ordinary, portable local assets.
- **Agents work within permissions.** Access is scoped by workspace and user policy; knowledge is not silently exposed to every agent.
- **Offline comes first.** Reading, editing, and baseline search should work without network access.
- **Sync is optional.** A WebDAV endpoint can exchange supported knowledge assets between devices without hosting a separate KnowSpace service.
- **Interactive content is reusable infrastructure.** Math rendering, diagrams, temporary pages, and input collection belong in common capabilities rather than being reinvented by every Skill.

**Design principle:** Your knowledge, your control. AI participation should be explicit, scoped, and reviewable.

## Project status

The first five internal development rounds have been recorded as closed in the private project's progress history. **This is not equivalent to completing public release acceptance.** Some implementation results have been reported internally, while important security, synchronization, and end-to-end checks remain part of the acceptance plan.

| Area | Current description | Public availability |
| --- | --- | --- |
| DeepSeekHarness Host/Client integration and sidebar | Implemented in private development records; compatibility is version-sensitive | Not released |
| Local workspaces, Markdown, JPG/PNG assets, basic search | Implemented/reported internally; subject to regression checks | Not released |
| Agent knowledge tools, edits, and change previews | Implemented/reported internally; end-to-end gateway checks are tracked separately | Not released |
| Markdown and mathematical notation | Existing private implementation and test history; maintained rather than rewritten | Not released |
| WebDAV bidirectional synchronization | Designed and worked on internally; cross-device/conflict/data-safety evidence must satisfy release gates | Not released |
| Interactive HTML, drawing input, submission bridge | Partially implemented or planned; security sandbox and real UI flows are not yet fully accepted | Not released |
| Editable knowledge diagrams and advanced local retrieval | Architecture decisions made; integration and acceptance are pending | Not released |

**No installation instructions are published yet.** A pinned, reproducible installation process and compatibility matrix will accompany the first usable alpha release. Please do not treat a development design or an example tool name as a supported public API.

## Product capabilities and direction

### 1. Knowledge: workspaces, files, and optional sync

Two intended workspace modes keep their responsibilities separate:

| Workspace | Behavior |
| --- | --- |
| **Local** | Independent knowledge workspace stored on the current device; never synced automatically. |
| **Synced** | Complete local working copy with optional, bidirectional WebDAV synchronization when connectivity is available. |

Markdown is the primary portable knowledge format. The default **sync allowlist** is `.md`, `.jpg`, and `.png`; PDF, video, audio, 3D files, executable content, and native diagram sources are not automatically uploaded as originals. Rich media may remain local. A Markdown document explicitly derived from rich media is an independent knowledge asset, not a continuously attached copy of its source.

The design prioritizes change history, understandable diffs, collision protection, non-destructive conflict handling, and recovery over silent overwrites.

### 2. Retrieval: local-first search and evidence

KnowSpace starts with workspace-aware textual retrieval. Its planned search improvements borrow *ideas* from Greb-style candidate retrieval and ranking, but **do not use a cloud Greb API or make any third-party MCP service mandatory**.

The intended retrieval path is:

```text
Authorized workspaces
        |
Local text / file candidates
        |
Filtering + ranking / optional fusion
        |
Traceable source passages
        |
Agent answer or user-facing search result
```

Optional local accelerators such as ripgrep or SQLite FTS5 may be used when compatible. In-process scanning remains the fallback. Embeddings and reranking are optional later extensions, not prerequisites for a useful knowledge library.

### 3. Rendering: Markdown, math, and safe previews

The shared rendering layer is intended to support:

- Markdown notes, tables, code blocks, and image references.
- LaTeX-style mathematical expressions and math-friendly study material.
- Safe, bounded previews of knowledge content and tool results.
- Temporary interactive artifacts where the Host can enforce isolation, permissions, and resource limits.

Untrusted HTML/JavaScript must not gain access to the Host, filesystem, or agent permissions merely because it is displayed in a note or preview. Interactive execution is gated separately from Markdown rendering.

### 4. Interaction: agent-to-user workflows

Beyond static reading, KnowSpace is being designed to support explicit agent-created interaction flows: a temporary page or form is shown to the user, the user submits structured input or an image, and the authorized agent receives that result through a controlled bridge.

Examples include handwritten mathematical answers, study exercises, research review forms, and Skill-generated visual tools. These are examples of reusable infrastructure, **not a promise that a full exam platform or research product is included in the core plugin**.

### 5. Knowledge diagrams: editable diagrams rather than image generation

A planned diagram capability will represent flowcharts, mind maps, and system architecture diagrams as **editable vector/structured assets**, rather than requiring photorealistic image-generation models.

The selected approach is to selectively adapt suitable `.drawio` parsing, model, and layout concepts/code from [fourzkw/dsh-drawai](https://github.com/fourzkw/dsh-drawai), subject to pinned-source and license review. Host authorization, workspace handling, and agent tools remain owned by KnowSpace. The native editable `.drawio` source is local-only by default; an explicitly exported `.png` can be stored as a separate knowledge asset under the normal sync policy.

**This diagram integration has been designed, not shipped.**

## Intended architecture

```text
DeepSeekHarness
  |-- Sidebar / knowledge UI
  |-- Agent tools (scoped operations)
  |-- Skills and other plugins
  |
  `-- dsh-knowspace plugin
       |-- Knowledge Core: workspaces / assets / changes / access
       |-- Retrieval Core: local search / source evidence
       |-- Rendering Core: Markdown / math / safe previews
       |-- Interaction Core: temporary artifacts / user submissions
       |-- Diagram capability: editable structured assets (planned)
       |
       |-- Local filesystem and indexes
       `-- Optional WebDAV synchronization (allowlisted files)

No dedicated KnowSpace server or mandatory cloud vector database.
```

This diagram describes the **target architecture**, not a statement that every component is complete or published.

## Privacy and safety boundaries

- No silent model calls, note summarization, or behavioral profiling while browsing, indexing, or syncing.
- Agent-readable workspace scopes are distinct from private or explicitly agent-denied workspaces.
- Agent writes and interactive submissions must be tied to valid authorization; unknown actor identity must not turn into permissive access.
- File changes, conflicts, and failure states should be inspectable and recoverable.
- Synchronization must not silently upload local-only source media, `.drawio`, or arbitrary executable files.
- New retrieval and diagram integrations must not replace the established storage/sync protocol without explicit review and migration tests.

These are **design and release requirements**. They should not be interpreted as claims that every security scenario has already passed independent audit.

## Roadmap

This checklist is a **public-facing roadmap**, not an item-by-item copy of internal acceptance results.

- [ ] Publish an auditable alpha release, installation steps, and a DeepSeekHarness compatibility matrix.
- [ ] Complete and publish Host/API security and real-agent integration acceptance.
- [ ] Complete reproducible multi-device WebDAV, conflict, deletion, and recovery acceptance.
- [ ] Finalize safe temporary artifact, handwriting, and interaction-submission flows.
- [ ] Integrate and validate editable knowledge diagrams with local-only source handling.
- [ ] Validate offline retrieval improvements with source citations and permission-safe fallbacks.
- [ ] Release documentation for extension points, third-party acknowledgments, and data migration/rollback.

Internal development work is organized into rounds. Rounds 1–5 are recorded as closed; Round 6 begins with technology-debt review, upstream/license checks, and isolated proofs of concept before new integrations are treated as complete.

## Relationship to other projects

KnowSpace complements, but does not depend on, [dsh-agent-team-presets](https://github.com/bloodarea/dsh-agent-team-presets), which manages reusable Agent Team configurations. A team decides *who does the work*; KnowSpace is intended to provide *what knowledge those authorized agents can read, update, retrieve, and present*.

The project draws on public research and software from the DeepSeekHarness ecosystem and beyond. Its current implementation selection distinguishes **using a dependency**, **selectively adapting source code**, and **independently implementing an idea**. Mentioning a project here does not imply that its source has already been incorporated.

Relevant references include [DeepSeekHarness](https://github.com/deepseek-ai/deepseek-harness), [dsh-drawai](https://github.com/fourzkw/dsh-drawai), [ripgrep](https://github.com/BurntSushi/ripgrep), [SQLite FTS5](https://www.sqlite.org/fts5.html), [markdown-it](https://github.com/markdown-it/markdown-it), and [KaTeX](https://github.com/KaTeX/KaTeX). Actual redistributed third-party source, when any, will require specific license and attribution notices.

## Feedback and contributions

This repository is currently a project placeholder. Issues about the design, reproducible safety tests, compatibility with DeepSeekHarness, and practical knowledge-management workflows are welcome. Source-level pull requests and installation support will be more meaningful once the alpha code is public.

Please do not post credentials, private notes, WebDAV endpoints containing secrets, or confidential agent logs in public issues.

## License

**To be determined before the source release.** The existence of this public README does not grant an open-source license for the private implementation. Third-party components retain their own licenses; any source reuse will be reviewed and attributed before distribution.

---

**Project:** [bloodarea/dsh-knowspace](https://github.com/bloodarea/dsh-knowspace)  
**Maintainer:** [bloodarea](https://github.com/bloodarea)  
**Status:** Active private development · Public placeholder · No public alpha yet
