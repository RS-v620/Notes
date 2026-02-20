# Copilot instructions for the OAuth2 Notes workspace

This repository contains structured technical notes about OAuth 2.0 and PKCE (Proof Key for Code Exchange). The content is primarily Markdown documentation and diagrams (Excalidraw). The goal for AI coding agents: edit, extend, and re-organize documentation while preserving technical accuracy and Obsidian linking conventions.

Key files and locations
- `Navigation.md` — top-level map of the content and where to find major sections (OAuth2, PKCE, Best Practices).
- `OAuth2/` — canonical sequence of spec-like documents (e.g. `Introduction.md`, `Protocol Flow in OAuth.md`, `Authorization Code Grant.md`, `Token Endpoint.md`). Use these for protocol descriptions and example request/response shapes.
- `PCKE-Protocol.md` and `PKCE-Introduction.md` — definitive text for PKCE behavior and required fields (code_verifier, code_challenge, methods `S256`/`plain`).
- `Excalidraw/` — diagram notes; keep filenames and `[[...]]` internal links intact when moving or renaming.

What matters to contributors and agents
- Preserve Obsidian-style internal links: `[[Page Name]]`. If you create or rename files, update `Navigation.md` and any `[[...]]` references.
- Do not convert Markdown to other formats without asking. The owner uses Markdown+Obsidian features; changes should remain readable in Obsidian.
- Keep examples literal and exact for protocol steps (e.g. code_challenge = BASE64URL-ENCODE(SHA256(ASCII(code_verifier)))). Avoid paraphrasing when editing authoritative sections.

Project-specific conventions
- Files are mostly spec-like: use headings, numbered lists, inline code fences for parameters and ABNF fragments (see `PCKE-Protocol.md`). Maintain fenced code blocks and ABNF formatting.
- Date and tag front-matter is occasionally used (see `BP-Introduction.md`). Preserve YAML front-matter if present.
- Use US-ASCII and keep RFC links intact (they're explicit references used as citations).

Editing/authoring guidance for AI
- When adding new content, add a single-line reference link in `Navigation.md` under the appropriate section (e.g., `### PKCE`), keeping human-curated order.
- For protocol edits, prefer minimal diffs: update the precise code examples and add a short 'note' block explaining why (if needed). Example: when updating PKCE transformation text, change the code formula only and add a `> [!note]` with one short sentence.
- When expanding acronyms or terms, add definitions to `OAuth2/Terminology.md` and cross-link with `[[Terminology]]` in files that use them.

Workflows and how-to hints
- There is no build system. Validate edits by opening the repository in Obsidian or any Markdown viewer. For large refactors, run a markdown link checker locally (not required for small edits).
- Diagrams in `Excalidraw/` are stored as `.excalidraw.md` files; edits should preserve the Excalidraw YAML/JSON blob in the file. If updating a diagram, keep the original drawing metadata intact.

Integration and external dependencies
- The notes reference standards (RFC 6749, RFC 7636). Do not change normative language unless correcting factual errors backed by RFC text.
- There are no code modules or runtime services in this workspace — focus is documentation. No tests or CI to run.

Edge cases and pitfalls
- Avoid removing or renaming files referenced by `Navigation.md` without updating the navigation links; broken `[[...]]` links are common regressions.
- Do not reformat ABNF or code blocks into prose; those are intentionally machine/copy-friendly.

If unsure
- Ask a human for clarification before making sweeping structural changes (reorganizing many files or renaming directories).
- When editing protocol text, reference the RFC linked in the file header if the change affects normative behavior.

If this update should be merged differently or you want stricter rules (linting, link-checking), tell me how strict you'd like changes to be and I will iterate.
