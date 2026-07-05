# Lesson 1 · What Computer Graphics Is, and Your First Image

<div class="cover-meta">
<span class="badge">Course: Graphics with C++</span>
<span class="badge">Level: Absolute beginner</span>
<span class="badge">Est. 45–60 min</span>
</div>

Welcome. This is the first lesson of a course we'll grow together, one Markdown file (and one PDF) at a time. By the end of *this* lesson you will have written a real C++ program that produces a real image file — a color gradient — completely from scratch, using **no graphics libraries at all**. That's the honest way to learn graphics: understand the pixels before you let a library hide them from you.

We'll also stop and explain every piece of jargon as it appears. Nothing is assumed.

---

## 1. What "computer graphics" actually means

Computer graphics is the craft of **turning numbers into pictures**. Somewhere in your computer there is a grid of tiny colored squares (your screen). Graphics is the set of techniques for deciding what color each of those squares should be.

<div class="note key">
<span class="note-title">The one idea to hold onto</span>
An image is just a grid of numbers. A program that "does graphics" is a program that fills in those numbers. Everything else — 3D, lighting, games, movies — is an elaborate way of computing those numbers.
</div>

The word for "compute the final colors of an image" is **rendering**.

<div class="note jargon">
<span class="note-title">Jargon · Rendering</span>
<strong>Rendering</strong> is the process of producing an image from a description of a scene. The description might be "a red triangle at these coordinates" or "a spaceship lit by two lights." The renderer's job is to turn that description into concrete pixel colors.
</div>

## 2. Pixels and the framebuffer

Zoom far enough into any screen and you find **pixels** — the smallest addressable dots of color.

<div class="note jargon">
<span class="note-title">Jargon · Pixel</span>
<strong>Pixel</strong> is short for "picture element." It is one dot in the image grid, and it stores a single color.
</div>

<figure>
<svg viewBox="0 0 420 220" width="420" xmlns="http://www.w3.org/2000/svg">
  <text x="10" y="18" fill="#9aa4b2" font-family="sans-serif" font-size="12">A 6 × 4 image = 24 pixels, each holding one color</text>
  <g stroke="#2b3138" stroke-width="1">
    <!-- draw a 6x4 grid of colored cells -->
    <rect x="20"  y="40" width="60" height="40" fill="#6ea8fe"/>
    <rect x="80"  y="40" width="60" height="40" fill="#79c0ff"/>
    <rect x="140" y="40" width="60" height="40" fill="#7ee787"/>
    <rect x="200" y="40" width="60" height="40" fill="#f0a868"/>
    <rect x="260" y="40" width="60" height="40" fill="#ff7b72"/>
    <rect x="320" y="40" width="60" height="40" fill="#c8a9ff"/>
    <rect x="20"  y="80" width="60" height="40" fill="#12161d"/>
    <rect x="80"  y="80" width="60" height="40" fill="#161b22"/>
    <rect x="140" y="80" width="60" height="40" fill="#1d2530"/>
    <rect x="200" y="80" width="60" height="40" fill="#28323f"/>
    <rect x="260" y="80" width="60" height="40" fill="#33404f"/>
    <rect x="320" y="80" width="60" height="40" fill="#3e4c5e"/>
    <rect x="20"  y="120" width="60" height="40" fill="#f0a868"/>
    <rect x="80"  y="120" width="60" height="40" fill="#ff7b72"/>
    <rect x="140" y="120" width="60" height="40" fill="#c8a9ff"/>
    <rect x="200" y="120" width="60" height="40" fill="#6ea8fe"/>
    <rect x="260" y="120" width="60" height="40" fill="#79c0ff"/>
    <rect x="320" y="120" width="60" height="40" fill="#7ee787"/>
    <rect x="20"  y="160" width="60" height="40" fill="#7ee787"/>
    <rect x="80"  y="160" width="60" height="40" fill="#f0a868"/>
    <rect x="140" y="160" width="60" height="40" fill="#6ea8fe"/>
    <rect x="200" y="160" width="60" height="40" fill="#ff7b72"/>
    <rect x="260" y="160" width="60" height="40" fill="#c8a9ff"/>
    <rect x="320" y="160" width="60" height="40" fill="#79c0ff"/>
  </g>
</svg>
<figcaption>Every image is a rectangular grid of pixels. Width × height = total pixel count.</figcaption>
</figure>

While your program is computing an image, it keeps those pixel colors somewhere in memory. That block of memory is called the **framebuffer**.

<div class="note jargon">
<span class="note-title">Jargon · Framebuffer</span>
A <strong>framebuffer</strong> is the region of memory that holds the color of every pixel for one image ("frame"). Rendering = writing values into the framebuffer. Displaying = copying the framebuffer to the screen.
</div>

## 3. How a color is stored: RGB

Each pixel's color is almost always described with three numbers: how much **R**ed, **G**reen, and **B**lue light it emits. Mix those three and you can make (nearly) any color the eye perceives. This is the **RGB color model**.

<figure>
<svg viewBox="0 0 420 220" width="360" xmlns="http://www.w3.org/2000/svg">
  <g style="isolation:isolate">
    <rect x="105" y="12" width="210" height="196" rx="8" fill="#000000"/>
    <circle cx="175" cy="82"  r="62" fill="#ff0000" style="mix-blend-mode:screen"/>
    <circle cx="245" cy="82"  r="62" fill="#00ff00" style="mix-blend-mode:screen"/>
    <circle cx="210" cy="142" r="62" fill="#0000ff" style="mix-blend-mode:screen"/>
  </g>
  <text x="118" y="74"  fill="#ffffff" font-family="sans-serif" font-size="15" font-weight="bold">R</text>
  <text x="292" y="74"  fill="#000000" font-family="sans-serif" font-size="15" font-weight="bold">G</text>
  <text x="204" y="196" fill="#ffffff" font-family="sans-serif" font-size="15" font-weight="bold">B</text>
  <text x="188" y="70"  fill="#000000" font-family="sans-serif" font-size="10">yellow</text>
  <text x="135" y="128" fill="#ffffff" font-family="sans-serif" font-size="10">magenta</text>
  <text x="248" y="128" fill="#000000" font-family="sans-serif" font-size="10">cyan</text>
  <text x="196" y="112" fill="#000000" font-family="sans-serif" font-size="10">white</text>
</svg>
<figcaption>Additive light: R + G = yellow, R + B = magenta, G + B = cyan, and all three together = white. Zero of all three is black. (Rendered with real additive blending, so the overlaps are accurate.)</figcaption>
</figure>

Each of the three channels is usually a whole number from **0 to 255**. Why 255? Because one channel is stored in a single **byte** (8 bits), and 8 bits can represent 256 distinct values, 0 through 255.

<div class="note jargon">
<span class="note-title">Jargon · Bit and byte</span>
A <strong>bit</strong> is a single 0-or-1. A <strong>byte</strong> is 8 bits grouped together, giving 2<sup>8</sup> = 256 possible values (0–255). One byte per color channel is the classic "24-bit color" you've seen in image settings (8 + 8 + 8).
</div>

Examples: `(255, 0, 0)` is pure red, `(0, 0, 0)` is black, `(255, 255, 255)` is white, `(255, 165, 0)` is orange.

## 4. Where is pixel (0,0)? The image coordinate system

To fill in pixels we need to *address* them with coordinates. In image code the convention is: **x grows to the right, y grows downward, and (0,0) is the top-left corner.**

<div class="note warn">
<span class="note-title">Watch out</span>
In school math, y goes <em>up</em>. In image and screen coordinates, y goes <em>down</em>. This trips up everyone at first. We'll be explicit about it every time it matters.
</div>

<figure>
<svg viewBox="0 0 320 220" width="320" xmlns="http://www.w3.org/2000/svg">
  <line x1="30" y1="30" x2="290" y2="30" stroke="#6ea8fe" stroke-width="2" marker-end="url(#a)"/>
  <line x1="30" y1="30" x2="30" y2="190" stroke="#7ee787" stroke-width="2" marker-end="url(#b)"/>
  <defs>
    <marker id="a" markerWidth="10" markerHeight="10" refX="6" refY="3" orient="auto"><path d="M0,0 L6,3 L0,6 Z" fill="#6ea8fe"/></marker>
    <marker id="b" markerWidth="10" markerHeight="10" refX="6" refY="3" orient="auto"><path d="M0,0 L6,3 L0,6 Z" fill="#7ee787"/></marker>
  </defs>
  <circle cx="30" cy="30" r="4" fill="#ffffff"/>
  <text x="36" y="24" fill="#ffffff" font-family="sans-serif" font-size="12">(0, 0) origin</text>
  <text x="250" y="24" fill="#6ea8fe" font-family="sans-serif" font-size="13">+x →</text>
  <text x="36" y="185" fill="#7ee787" font-family="sans-serif" font-size="13">+y ↓</text>
  <circle cx="190" cy="120" r="4" fill="#f0a868"/>
  <text x="198" y="124" fill="#f0a868" font-family="sans-serif" font-size="12">pixel (x=160, y=90)</text>
</svg>
<figcaption>Image coordinates: origin at top-left, y increases downward.</figcaption>
</figure>

## 5. Setting up C++ (and the jargon that comes with it)

You already have a **compiler** installed (Apple's `clang++`). Let's define the words you'll hear constantly.

<div class="note jargon">
<span class="note-title">Jargon · The C++ toolchain</span>
<strong>Source file</strong> — a text file of C++ code you write, ending in <code>.cpp</code>.<br/>
<strong>Compiler</strong> — a program that translates your source file into machine instructions the CPU can run.<br/>
<strong>Compiling</strong> — the act of running the compiler on your source file.<br/>
<strong>Executable / binary</strong> — the runnable program the compiler produces.<br/>
<strong>Header</strong> — a file (often <code>&lt;iostream&gt;</code>) that declares functionality you want to <em>use</em>. You "include" it to gain access.
</div>

## 6. The plan: write an image with zero libraries

We'll output the **PPM** image format. It's the simplest real image format in existence — it's basically plain text listing the pixels. No library needed; we just print numbers. Later we'll open it with any image viewer (or convert it to PNG).

<div class="note jargon">
<span class="note-title">Jargon · PPM format</span>
<strong>PPM</strong> (Portable Pixmap) is a trivially simple image format. A small text header says "I'm an image, this wide, this tall, colors go up to 255," followed by the R G B values of every pixel, row by row, top to bottom.
</div>

A PPM file looks like this for a tiny 2×2 image:

```text
P3
2 2
255
255 0 0    0 255 0
0 0 255    255 255 255
```

That header means: `P3` = "ASCII RGB PPM"; `2 2` = width and height; `255` = the maximum value a channel can hold. Then four pixels: red, green, blue, white.

## 7. Your first program: a color gradient

Here is the complete program. Read it once top to bottom, then we'll dissect it.

```cpp
#include <iostream>   // gives us std::cout for printing

int main() {
    const int width  = 256;
    const int height = 256;

    // PPM header: format, dimensions, max color value.
    std::cout << "P3\n" << width << ' ' << height << "\n255\n";

    // Fill the image top-to-bottom (y), left-to-right (x).
    for (int y = 0; y < height; ++y) {
        for (int x = 0; x < width; ++x) {
            int r = x;                       // red grows to the right
            int g = y;                       // green grows downward
            int b = 128;                     // constant blue

            std::cout << r << ' ' << g << ' ' << b << '\n';
        }
    }

    return 0;
}
```

### Dissecting it, line by line

- `#include <iostream>` — pull in the **header** that lets us print text. `iostream` = "input/output stream."
- `int main() { ... }` — every C++ program starts running at a **function** named `main`. The `int` says `main` hands back a whole number when it finishes.

<div class="note jargon">
<span class="note-title">Jargon · Function</span>
A <strong>function</strong> is a named, reusable block of code. <code>main</code> is special: it's the entry point the operating system calls to start your program. The <code>{ }</code> curly braces enclose the function's body.
</div>

- `const int width = 256;` — declare a **variable** named `width` holding the whole number 256. `int` is its **type** (integer). `const` means "this never changes."

<div class="note jargon">
<span class="note-title">Jargon · Variable and type</span>
A <strong>variable</strong> is a named box in memory holding a value. Its <strong>type</strong> (like <code>int</code> for integers) tells the compiler what kind of value it holds and how big the box is.
</div>

- `std::cout << ...` — `std::cout` is the **standard output stream** (your terminal). The `<<` operator sends things into it. We redirect that output into a file when we run the program, so everything printed *becomes the image file*.

<div class="note jargon">
<span class="note-title">Jargon · std, namespace, and ::</span>
<code>std</code> is the <strong>standard library</strong>'s <strong>namespace</strong> — a labeled container of names so the library's <code>cout</code> doesn't collide with any <code>cout</code> you might define. The <code>::</code> means "reach inside this namespace," so <code>std::cout</code> = "the <code>cout</code> that lives in <code>std</code>."
</div>

- The two `for` loops are the heart of it. The outer loop walks every **row** `y` from top (0) to bottom. The inner loop walks every **column** `x` left to right. Together they visit all 256 × 256 = 65,536 pixels exactly once.

<div class="note jargon">
<span class="note-title">Jargon · for loop</span>
A <strong>for loop</strong> repeats a block of code. <code>for (int x = 0; x &lt; width; ++x)</code> reads as: start with <code>x = 0</code>; keep going while <code>x &lt; width</code>; after each pass do <code>++x</code> (add one to <code>x</code>). Nesting one loop inside another is how we sweep a 2D grid.
</div>

- Inside, we choose this pixel's color: red equals the column, green equals the row, blue is a flat 128. So the left edge is dark red, the right edge is bright red, the top is dark green, the bottom is bright green — a smooth two-way gradient.
- `return 0;` — tell the operating system "the program finished successfully." By convention, `0` means success.

## 8. How the code becomes an image

When this program is compiled into a runnable form and executed, everything it prints with `std::cout` can be captured into a file instead of shown on screen. Because what it prints is exactly the PPM format from §6, that file *is* an image — conventionally named `gradient.ppm`.

<div class="note jargon">
<span class="note-title">Jargon · Compiling, running, and redirecting</span>
<strong>Compiling</strong> turns the <code>.cpp</code> source into an executable program (the language version and optimization level are chosen with compiler options like <code>-std=c++17</code> and <code>-O2</code>). <strong>Running</strong> it makes it emit the pixel numbers. A shell <strong>redirect</strong> — the <code>&gt;</code> symbol — sends that printed output into a file instead of the terminal, which is what saves the image. A <code>.ppm</code> file can then be turned into the more common <code>.png</code> with macOS's built-in <code>sips</code> tool.
</div>

Compiled and run, the program produces a square that fades from dark in the top-left toward bright red on the right and bright green toward the bottom:

<figure>
<svg viewBox="0 0 200 200" width="200" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="rchan" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0" stop-color="#000080"/><stop offset="1" stop-color="#ff0080"/>
    </linearGradient>
    <linearGradient id="gchan" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="#000000"/><stop offset="1" stop-color="#00ff00"/>
    </linearGradient>
  </defs>
  <g style="isolation:isolate">
    <rect x="0" y="0" width="200" height="200" fill="url(#rchan)"/>
    <rect x="0" y="0" width="200" height="200" fill="url(#gchan)" style="mix-blend-mode:screen"/>
  </g>
</svg>
<figcaption>Your rendered gradient.png (blue held at 128): dark blue (0,0,128) top-left, magenta (255,0,128) top-right, spring green (0,255,128) bottom-left, pale yellow (255,255,128) bottom-right.</figcaption>
</figure>

<div class="note tip">
<span class="note-title">You just did real graphics</span>
No engine, no library — you decided the color of 65,536 pixels with a formula and wrote them to disk. That loop-over-every-pixel-and-compute-a-color is the exact shape of a <strong>ray tracer</strong>, which we'll build in a few lessons. The only thing that changes is the formula inside the loop.
</div>

## 9. More worked examples

The whole image is decided by the three lines that set `r`, `g`, `b`. Change only those and you get a completely different picture — the loops never change. Here's what a few formulas produce:

### A · Flip a channel

Replace `int r = x;` with `int r = 255 - x;`. Now red is brightest where `x = 0`, so the **bright red edge moves to the left** instead of the right. Subtracting a coordinate from 255 mirrors that channel.

<figure>
<svg viewBox="0 0 200 200" width="180" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="flipr" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0" stop-color="#ff0080"/><stop offset="1" stop-color="#000080"/>
    </linearGradient>
    <linearGradient id="flipg" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="#000000"/><stop offset="1" stop-color="#00ff00"/>
    </linearGradient>
  </defs>
  <g style="isolation:isolate">
    <rect x="0" y="0" width="200" height="200" fill="url(#flipr)"/>
    <rect x="0" y="0" width="200" height="200" fill="url(#flipg)" style="mix-blend-mode:screen"/>
  </g>
</svg>
<figcaption><code>r = 255 − x</code>: the bright-red edge is now on the left — the whole picture is the original gradient mirrored left-to-right.</figcaption>
</figure>

### B · Grayscale

Set all three channels to the same value: `int r = x, g = x, b = x;`. Whenever R, G, and B are equal the pixel is a shade of **gray** — no channel dominates. Since the value here depends only on `x`, the result is vertical bands fading from black on the left (`x = 0`) to white on the right (`x = 255`).

<figure>
<svg viewBox="0 0 200 200" width="180" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="grayx" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0" stop-color="#000000"/><stop offset="1" stop-color="#ffffff"/>
    </linearGradient>
  </defs>
  <rect x="0" y="0" width="200" height="200" fill="url(#grayx)"/>
</svg>
<figcaption><code>r = g = b = x</code>: equal channels look gray, and depending only on <code>x</code> gives a smooth black-to-white ramp across the columns.</figcaption>
</figure>

### C · Checkerboard

Make the color depend on which 32-pixel block a pixel lands in: white when `(x / 32 + y / 32)` is even, black when odd. Because `x / 32` is integer division, each 32×32 block shares one value, and adding the two block indices flips between even and odd across the grid — a **checkerboard**. (`n % 2 == 0` tests "even", using the remainder operator `%`.)

<figure>
<svg viewBox="0 0 200 200" width="180" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <pattern id="checker" width="50" height="50" patternUnits="userSpaceOnUse">
      <rect width="50" height="50" fill="#000000"/>
      <rect width="25" height="25" fill="#ffffff"/>
      <rect x="25" y="25" width="25" height="25" fill="#ffffff"/>
    </pattern>
  </defs>
  <rect x="0" y="0" width="200" height="200" fill="url(#checker)" stroke="#333c47"/>
</svg>
<figcaption>White when <code>(x / 32 + y / 32)</code> is even: a checkerboard. The top-left block is white because 0 + 0 is even. A 256-wide image has 8 blocks of 32 pixels across, so an 8×8 board.</figcaption>
</figure>

### D · A bigger image

Change `width` and `height` from 256 to 512. That doubles each side, so there are **4× as many pixels** (512 × 512 = 262,144, versus 65,536) — and the output file grows about 4× too, since every pixel writes its own line of numbers.

<figure>
<svg viewBox="0 0 330 180" width="330" xmlns="http://www.w3.org/2000/svg">
  <rect x="160" y="12" width="156" height="156" fill="#7ee78722" stroke="#7ee787"/>
  <rect x="12" y="90" width="78" height="78" fill="#6ea8fe33" stroke="#6ea8fe"/>
  <text x="51" y="132" fill="#e8edf3" font-family="sans-serif" font-size="11" text-anchor="middle">256×256</text>
  <text x="51" y="152" fill="#8a94a1" font-family="sans-serif" font-size="9" text-anchor="middle">65,536 px</text>
  <text x="238" y="86" fill="#e8edf3" font-family="sans-serif" font-size="12" text-anchor="middle">512×512</text>
  <text x="238" y="104" fill="#8a94a1" font-family="sans-serif" font-size="9" text-anchor="middle">262,144 px = 4×</text>
</svg>
<figcaption>Doubling each side quadruples the area: the 512×512 image holds four times the pixels (and takes ~4× the memory and file size) of the 256×256 one.</figcaption>
</figure>

## 10. What you learned

- An image is a **grid of pixels**; each pixel is an **RGB** triple of bytes (0–255).
- The in-memory grid you write into is the **framebuffer**.
- Image coordinates put **(0,0) at the top-left**, with **y pointing down**.
- You can produce a real image with nothing but `std::cout` and the **PPM** format.
- The core loop of graphics is *"for every pixel, compute a color."*
- C++ words now in your vocabulary: compiler, source file, executable, header, `#include`, function, `main`, variable, type, `int`, `const`, namespace, `std::cout`, `for` loop, shell redirect.

> When something is unclear or you want a concept expanded, tell me the section and I'll revise this lesson and regenerate the PDF — that's the whole point of keeping it as living Markdown.
