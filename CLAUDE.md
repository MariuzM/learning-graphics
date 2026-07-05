# CLAUDE.md — How we run this graphics course

This file is the living plan for teaching me (Marius) computer graphics with C++.
I edit it whenever I want to change how the lessons work; Claude reads it before writing any lesson.

## Who I am as a learner
- Goal: genuinely understand computer graphics, using C++.
- I am new to the jargon. Every technical term (graphics *and* programming) must be defined the first time it appears, in a short callout — don't assume I know it.
- I learn best from pictures. Lessons should include diagrams (inline SVG so the PDF stays crisp).
- I want to build things from scratch first, understand the machinery, then reach for libraries.

## Project layout — deliberately minimal
- Only Markdown lessons + their generated PDFs live in the **project root** (`NN-topic.md` / `NN-topic.pdf`).
- The single build script lives in `scripts/` (`build.mjs`, `pdf.css`). It has **no dependencies** —
  just Node's built-in modules + macOS Chrome. No `package.json`, no `node_modules`, nothing to install.
- C++ example code lives **inline in the lesson**, not in separate files. To try it, copy it into a
  `.cpp` file yourself and compile (see below). This is a course of documents, not a code project.

## How each lesson must be written
- One Markdown file per lesson in the root, named `NN-topic.md`.
- Start with a short "what you'll build / why it matters" intro.
- Explain jargon inline using callout boxes (see markup below). Never leave a term undefined.
- Include at least one diagram where a picture helps (coordinate systems, pipelines, geometry…).
- Every C++ code block must be followed by a line-by-line explanation.
- End with: **Exercises**, a **What you learned** recap, and a **Next lesson** preview.
- Keep the tone direct. Correct my mistaken predictions rather than smoothing over them.

## Callout box markup (styled in the PDF)
```html
<div class="note jargon"><span class="note-title">Jargon · Term</span> definition…</div>
<div class="note key"><span class="note-title">Key idea</span> the big takeaway…</div>
<div class="note tip"><span class="note-title">Tip</span> …</div>
<div class="note warn"><span class="note-title">Watch out</span> …</div>
```

## Building the PDFs
Everything is just Markdown → dark-mode PDF. To (re)generate:
```bash
node scripts/build.mjs               # rebuild every lesson PDF
node scripts/build.mjs 01-*.md       # rebuild one lesson
```
The PDF is written next to the Markdown file with the same name.
Styling lives in `scripts/pdf.css`. The converter is hand-rolled in `scripts/build.mjs` using only
Node built-ins; Chrome does the PDF rendering. No framework, no dependencies, no install step.

## C++ setup on this machine
- Compiler: `clang++` (Apple clang, already installed).
- To run a lesson's example, copy its code into a `.cpp` file, then:
  ```bash
  clang++ -std=c++17 -O2 gradient.cpp -o gradient
  ./gradient > gradient.ppm
  sips -s format png gradient.ppm --out gradient.png   # view it
  ```

## Roadmap (rough — we adjust as we go)
1. ✅ **What graphics is + your first image** (PPM, pixels, RGB, coordinates) — done.
2. ⬜ Drawing shapes: set-a-pixel, lines (Bresenham), a reusable image buffer class.
3. ⬜ 2D math for graphics: vectors, points, basic linear algebra we actually need.
4. ⬜ Triangles & rasterization: filling shapes, barycentric coordinates.
5. ⬜ From 2D to 3D: the camera, projection, the coordinate transforms (the "pipeline").
6. ⬜ A tiny ray tracer: rays, spheres, and simple lighting.
7. ⬜ Shading & light: normals, diffuse/specular, shadows.
8. ⬜ (Later) Using a real API — likely OpenGL — now that we know what it hides.

## My preferences (set 2026-07-05)
- **Math:** teach it as we go — introduce vectors/linear algebra/trig only when a lesson
  needs them, built from scratch. No separate math-only lessons unless I ask.
- **Lesson size:** thorough, like Lesson 1 — define every term, plenty of diagrams and
  exercises. Prioritize completeness over speed.

## Notes / things to revisit
- (Add requests here — e.g. "spend more time on the math", "go faster", "more exercises".)
