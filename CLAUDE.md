# CLAUDE.md — How we run this graphics course

This file is the living plan for teaching me (Marius) computer graphics with C++.
I edit it whenever I want to change how the lessons work; Claude reads it before writing any lesson.

## Who I am as a learner
- Goal: genuinely understand computer graphics, using C++.
- I am new to the jargon. Every technical term (graphics *and* programming) must be defined the first time it appears, in a short callout — don't assume I know it.
- I learn best from pictures — I want LOTS of images, more than feels necessary (inline SVG so the PDF
  stays crisp), including a picture of the result for every worked example.
- I'm bad at math. Explain any math visually and geometrically first (arrows, grids, number lines,
  drawn-out shapes); build intuition from the picture before showing any formula, and keep notation
  minimal and fully labelled.
- I want to build things from scratch first, understand the machinery, then reach for libraries.

## Project layout — deliberately minimal
- Only Markdown lessons + their generated PDFs live in the **project root** (`NN-topic.md` / `NN-topic.pdf`).
- The single build script lives in `scripts/` (`build.mjs`, `pdf.css`). It has **no dependencies** —
  just Node's built-in modules + macOS Chrome. No `package.json`, no `node_modules`, nothing to install.
- C++ example code lives **inline in the lesson**, not in separate files. The learner reads it (and
  its shown output) rather than running it. This is a course of documents, not a code project.

## How each lesson must be written
- One Markdown file per lesson in the root, named `NN-topic.md`.
- Start with a short "what you'll build / why it matters" intro.
- Explain jargon inline using callout boxes (see markup below). Never leave a term undefined.
- Include LOTS of images — illustrate concepts AND every worked example (show what the result looks
  like; render the real output and reproduce it accurately). Prefer more pictures over fewer.
- For any math, lead with a diagram/geometric picture and build intuition before any notation.
- Every C++ code block must be followed by a line-by-line explanation.
- **The learner does NOT run the code.** Present code as a worked example and SHOW its output; never
  frame it as "compile and run this" and don't include build/run commands as steps to perform.
- **No "Exercises" section and no "Next lesson" preview.** Instead include plenty of concrete
  **worked examples** (with the answers shown) that apply the concepts with real numbers.
- End with a **What you learned** recap.
- Keep the tone direct. Correct my mistaken predictions rather than smoothing over them.

## Callout box markup (styled in the PDF)
```html
<div class="note jargon"><span class="note-title">Jargon · Term</span> definition…</div>
<div class="note key"><span class="note-title">Key idea</span> the big takeaway…</div>
<div class="note tip"><span class="note-title">Tip</span> …</div>
<div class="note warn"><span class="note-title">Watch out</span> …</div>
```

## Building the PDFs
**ALWAYS regenerate the PDF right after editing any lesson's Markdown** — every change to an `NN-*.md`
must be followed by rebuilding its `.pdf` in the same turn, automatically, without being asked. Then
visually check the rebuilt PDF (accuracy gate). Never leave the `.md` and `.pdf` out of sync.

Everything is just Markdown → dark-mode PDF. To (re)generate:
```bash
node scripts/build.mjs               # rebuild every lesson PDF
node scripts/build.mjs 01-*.md       # rebuild one lesson
```
The PDF is written next to the Markdown file with the same name.
Styling lives in `scripts/pdf.css`. The converter is hand-rolled in `scripts/build.mjs` using only
Node built-ins; Chrome does the PDF rendering. No framework, no dependencies, no install step.

## C++ / tooling on this machine (for Claude to verify, not for the learner to run)
The learner does not run code — this is so Claude can confirm every lesson's C++ actually compiles and
produces the stated output *before* publishing (part of the accuracy gate).
- Compiler: `clang++` (Apple clang, already installed). Machine is arm64 / Apple Silicon.
- Verify a snippet by copying it to a scratch `.cpp` file, then:
  ```bash
  clang++ -std=c++17 -O2 snippet.cpp -o snippet && ./snippet
  ```
- `sips -s format png in.ppm --out out.png` converts PPM output to PNG (tested — works).

## Roadmap (rough — we adjust as we go)
1. ✅ **What graphics is + your first image** (PPM, pixels, RGB, coordinates) — done.
2. ✅ **How memory works** (bytes/addresses, binary, type sizes, pointers, memory hierarchy, stack vs
   heap, row-major image layout) — done.
3. ✅ **Drawing shapes** (reusable Image buffer struct, setPixel, slope, Bresenham line) — done.
4. ⬜ 2D math for graphics: vectors, points, basic linear algebra we actually need.
5. ⬜ Triangles & rasterization: filling shapes, barycentric coordinates.
6. ⬜ From 2D to 3D: the camera, projection, the coordinate transforms (the "pipeline").
7. ⬜ A tiny ray tracer: rays, spheres, and simple lighting.
8. ⬜ Shading & light: normals, diffuse/specular, shadows.
9. ⬜ (Later) Using a real API — likely OpenGL — now that we know what it hides.

## My preferences (set 2026-07-05)
- **Math:** teach it as we go — introduce vectors/linear algebra/trig only when a lesson
  needs them, built from scratch. No separate math-only lessons unless I ask. I'm bad at math, so
  always explain it visually/geometrically first (pictures before formulas), notation minimal.
- **Lesson size:** thorough, like Lesson 1 — define every term, plenty of diagrams and
  worked examples. Prioritize completeness over speed.

## Notes / things to revisit
- (Add requests here — e.g. "spend more time on the math", "go faster", "more exercises".)
