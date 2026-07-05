# Learning Computer Graphics with C++

A personal, growing course for learning computer graphics from the ground up in C++ — starting
from a single pixel and building toward rendering real images. Each lesson is a Markdown file that
generates a dark-mode PDF with diagrams and fully explained code (no prior jargon assumed).

> [!IMPORTANT]
> **These lessons are generated with the help of AI.** They're written as a learning journal, so
> some explanations may be simplified, incomplete, or occasionally **inaccurate**. Treat them as a
> friendly starting point, not an authoritative reference — verify anything important against a
> trusted source (a textbook, official docs, or the C++ standard) before relying on it.

## What's inside

- `NN-topic.md` — the lessons (read and edit these).
- `NN-topic.pdf` — the same lesson rendered as a dark-mode PDF, generated from the Markdown.
- `CLAUDE.md` — the living plan for how the course is taught (preferences, roadmap).
- `scripts/` — a tiny, **dependency-free** Markdown → PDF build script + its dark theme.

## Lessons

1. [What Computer Graphics Is, and Your First Image](01-what-is-graphics-and-your-first-image.md)
   — pixels, the framebuffer, RGB color, image coordinates, and writing a real gradient image in
   C++ with zero libraries (PPM format).

_More lessons are added over time._

## Building the PDFs

Everything is just Markdown and PDF. The only requirements are **Node.js** (any recent version) and
**Google Chrome** — both used only to render the PDF. There is nothing to `npm install`.

```bash
node scripts/build.mjs          # rebuild every lesson PDF
node scripts/build.mjs 01-*.md  # rebuild a single lesson
```

Each PDF is written next to its Markdown file. Styling lives in `scripts/pdf.css`.

## Running the C++ examples

Each lesson includes its C++ code inline. To try it, copy the code into a `.cpp` file and compile
with any C++ compiler, e.g. Apple/LLVM clang:

```bash
clang++ -std=c++17 -O2 gradient.cpp -o gradient
./gradient > gradient.ppm
sips -s format png gradient.ppm --out gradient.png   # macOS: view as PNG
```

## License

Personal learning material, shared as-is. Feel free to read and learn from it.
