# Learning Computer Graphics with C++

A personal, growing course for learning computer graphics from the ground up in C++ — starting from
a single pixel and building toward rendering real images. Each lesson is a Markdown file that
generates a dark-mode PDF with diagrams and fully explained code (no prior jargon assumed).

> [!IMPORTANT] > **These lessons are generated with the help of AI.** They're written as a learning
> journal, so some explanations may be simplified, incomplete, or occasionally **inaccurate**. Treat
> them as a friendly starting point, not an authoritative reference — verify anything important
> against a trusted source (a textbook, official docs, or the C++ standard) before relying on it.

## Building the PDFs

Everything is just Markdown and PDF. The only requirements are **Node.js** (any recent version) and
**Google Chrome** — both used only to render the PDF. There is nothing to `npm install`.

```bash
node scripts/build.mjs          # rebuild every lesson PDF
node scripts/build.mjs 01-*.md  # rebuild a single lesson
```

Each PDF is written next to its Markdown file. Styling lives in `scripts/pdf.css`.

## License

Personal learning material, shared as-is. Feel free to read and learn from it.
