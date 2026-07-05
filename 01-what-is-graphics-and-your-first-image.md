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
<svg viewBox="0 0 420 160" width="420" xmlns="http://www.w3.org/2000/svg">
  <circle cx="150" cy="70" r="55" fill="#ff0000" fill-opacity="0.75"/>
  <circle cx="210" cy="70" r="55" fill="#00ff00" fill-opacity="0.75"/>
  <circle cx="180" cy="110" r="55" fill="#0000ff" fill-opacity="0.75"/>
  <text x="95"  y="70" fill="#ffffff" font-family="sans-serif" font-size="13">R</text>
  <text x="258" y="70" fill="#ffffff" font-family="sans-serif" font-size="13">G</text>
  <text x="176" y="150" fill="#ffffff" font-family="sans-serif" font-size="13">B</text>
  <text x="300" y="60" fill="#9aa4b2" font-family="sans-serif" font-size="12">Overlap of all</text>
  <text x="300" y="78" fill="#9aa4b2" font-family="sans-serif" font-size="12">three = white</text>
</svg>
<figcaption>Additive color: red + green + blue light combine toward white. Zero of all three is black.</figcaption>
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

## 8. Compile and run it

Save the code as `gradient.cpp`, then in a terminal:

```bash
# 1. Compile the source file into an executable called "gradient"
clang++ -std=c++17 -O2 gradient.cpp -o gradient

# 2. Run it, sending its printed output into an image file
./gradient > gradient.ppm
```

<div class="note jargon">
<span class="note-title">Jargon · What those flags mean</span>
<code>-std=c++17</code> picks the 2017 version of the C++ language. <code>-O2</code> asks for optimized, faster machine code. <code>-o gradient</code> names the output executable. The <code>&gt;</code> is a shell <strong>redirect</strong>: instead of printing to the screen, send the program's output into <code>gradient.ppm</code>.
</div>

Open `gradient.ppm` in an image viewer. On macOS you can also convert it to PNG:

```bash
# sips is built into macOS; no install needed
sips -s format png gradient.ppm --out gradient.png
```

You should see a square that fades from dark in the top-left toward bright red on the right and bright green toward the bottom:

<figure>
<svg viewBox="0 0 200 200" width="200" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="gx" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0" stop-color="#000080"/><stop offset="1" stop-color="#ff0080"/>
    </linearGradient>
    <linearGradient id="gy" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="#000000" stop-opacity="0"/><stop offset="1" stop-color="#00ff00" stop-opacity="0.85"/>
    </linearGradient>
  </defs>
  <rect x="0" y="0" width="200" height="200" fill="url(#gx)"/>
  <rect x="0" y="0" width="200" height="200" fill="url(#gy)"/>
</svg>
<figcaption>Roughly what your rendered gradient.png will look like (blue held at 128).</figcaption>
</figure>

<div class="note tip">
<span class="note-title">You just did real graphics</span>
No engine, no library — you decided the color of 65,536 pixels with a formula and wrote them to disk. That loop-over-every-pixel-and-compute-a-color is the exact shape of a <strong>ray tracer</strong>, which we'll build in a few lessons. The only thing that changes is the formula inside the loop.
</div>

## 9. Exercises

Try these by editing only the formula inside the inner loop, then recompiling:

1. **Flip a channel.** Set `r = 255 - x;`. Which corner gets bright now? Predict before you run.
2. **Grayscale.** Set all three channels equal to `x`. What do you see, and why is "R = G = B" always a shade of gray?
3. **Checkerboard.** Make the pixel white when `(x / 32 + y / 32)` is even and black when odd. (Hint: the `%` operator gives a remainder; `n % 2 == 0` means "even.")
4. **Bigger.** Change `width` and `height` to 512. What happens to the file size, and why?

<div class="note tip">
<span class="note-title">How to think about the exercises</span>
Every one is the same move: change the color formula, leave the loops alone. If your prediction and the result disagree, that gap is exactly the thing worth understanding — sit with it before moving on.
</div>

## 10. What you learned

- An image is a **grid of pixels**; each pixel is an **RGB** triple of bytes (0–255).
- The in-memory grid you write into is the **framebuffer**.
- Image coordinates put **(0,0) at the top-left**, with **y pointing down**.
- You can produce a real image with nothing but `std::cout` and the **PPM** format.
- The core loop of graphics is *"for every pixel, compute a color."*
- C++ words now in your vocabulary: compiler, source file, executable, header, `#include`, function, `main`, variable, type, `int`, `const`, namespace, `std::cout`, `for` loop, shell redirect.

## 11. Next lesson (preview)

**Lesson 2 — Drawing shapes: lines and the math of putting a pixel at (x, y).** We'll stop coloring by formula and start *placing* things: a function to set one specific pixel, then drawing a straight line between two points (Bresenham's line algorithm), which forces us to confront why screens are made of discrete squares. We'll also switch from dumping text to a small, reusable image buffer in C++.

> Keep this file open as you work. When something is unclear or you want a concept expanded, tell me and I'll revise this lesson and regenerate the PDF — that's the whole point of keeping it as living Markdown.
