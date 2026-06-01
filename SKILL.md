---
name: tts-tool
description: Generate a natural-sounding `.opus` audio file from text or markdown. Use when a user wants spoken audio, especially for content with markdown, URLs, formulas, paths, or technical notation that should be rewritten for speech.
---

# TTS Tool

Generate one `.opus` file with:

```bash
chunktts INPUT.txt OUTPUT.opus
```

## Readiness

- Run `chunktts --help`.
- If `chunktts` is missing or fails to start, read
  [references/chunktts-install.md](references/chunktts-install.md), install the
  binary or missing runtime dependency, and retry.
- After installing `chunktts`, tell the user how to configure
  `DEEPINFRA_API_KEY` or `config.json` before generating audio.
- Request unrestricted network or escalated execution directly when running
  `chunktts`. Do not run a sandboxed network probe first.
- Do not inspect environment variables, shell profiles, or filesystem files to
  discover API keys. If authentication fails, ask the user to configure the
  key, then retry.

## Rewrite for Speech

Read the source directly and manually rewrite it for speech. Do not use a
programmatic cleanup pipeline. Preserve the content while optimizing for
listening rather than visual fidelity.

- Remove markdown syntax while keeping useful visible text.
- Turn bullet points and numbered items into normal spoken sentences.
- For markdown links, keep the visible text and drop the URL unless it matters.
- Rewrite raw URLs, email addresses, paths, flags, and technical identifiers
  into natural spoken forms. Avoid punctuation-heavy text.
- Rewrite email addresses into forms like `name at domain dot com`.
- Remove LaTeX delimiters and speak common math notation, such as `\alpha` as
  `alpha`, `=` as `equals`, `/` as `over`, and `^2` as `squared`.
- Drop decorative or purely visual content that adds no spoken value.

## Chunking

- Insert `<bk>` between spoken thought units. Use paragraphs and list items as
  the default units; keep adjacent short sentences together when they form one
  thought.
- Keep headings only when they add spoken meaning.
- Do not summarize or drop meaningful content to fit a chunk. Rewrite dense
  sentences into shorter spoken sentences first.
- If a paragraph or list item is too long, split it first at sentence boundaries.
- Split inside a sentence only as a last resort.
- Target `260-420` characters per chunk. Prefer at most `520`; never exceed
  `620`.
- Remove empty chunks and collapse repeated `<bk>` markers.

## Produce Output

- Write the cleaned text with `<bk>` markers to a temporary input file.
- Run `chunktts` against that file and an agent-managed `.opus` output path.
- Tell the user the exact location of the generated `.opus` file.
