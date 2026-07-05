# Lesson 3 · Drawing Shapes: An Image Buffer and Your First Line

<div class="cover-meta">
<span class="badge">Course: Graphics with C++</span>
<span class="badge">Level: Absolute beginner</span>
<span class="badge">Est. 60–75 min</span>
</div>

In Lesson 1 we colored every pixel with a formula. In Lesson 2 we learned that an image is just a 1D block of bytes in memory, addressed by `index = y * width + x`. Now we combine both ideas to actually **draw**: we'll build a small, reusable **image buffer** in C++, give it a `setPixel` function to color one specific dot, and then draw a straight **line** between any two points — which forces us to face the central awkwardness of graphics: a perfectly straight line has to be built out of square pixels.

Everything here is shown as code plus its real output and lots of pictures. Any math is drawn before it's written.

---

## 1. A reusable image buffer

Last lesson's gradient printed pixels straight to the screen as it computed them. That's fine for one image, but to *draw* we need to keep all the pixels around in memory, change them in any order, and save them at the end. So we wrap the pixel bytes in a small **struct** — a bundle of related data with functions attached.

<div class="note jargon">
<span class="note-title">Jargon · struct and std::vector</span>
A <strong>struct</strong> groups related variables (and functions) under one name — here, an image's width, height, and its pixels. A <strong>std::vector</strong> is C++'s resizable array; it stores its elements on the <em>heap</em> (Lesson 2), which is exactly where a big block of pixels belongs. <code>std::vector&lt;unsigned char&gt;</code> is a list of bytes (an <strong>unsigned char</strong> is a one-byte value 0–255 — perfect for a color channel).
</div>

<figure>
<svg viewBox="0 0 560 220" width="540" xmlns="http://www.w3.org/2000/svg">
  <rect x="10" y="30" width="180" height="130" rx="8" fill="#232b34" stroke="#6ea8fe"/>
  <text x="24" y="54" fill="#6ea8fe" font-family="sans-serif" font-size="13" font-weight="bold">struct Image</text>
  <text x="24" y="82" fill="#b6bdc7" font-family="monospace" font-size="12">int width  = 4</text>
  <text x="24" y="104" fill="#b6bdc7" font-family="monospace" font-size="12">int height = 3</text>
  <text x="24" y="126" fill="#b6bdc7" font-family="monospace" font-size="12">vector&lt;byte&gt;</text>
  <text x="24" y="144" fill="#b6bdc7" font-family="monospace" font-size="12">  pixels ────►</text>
  <text x="210" y="24" fill="#9aa4b2" font-family="sans-serif" font-size="11">pixels: one flat 1D array on the heap (row by row)</text>
  <g font-family="monospace" font-size="9" text-anchor="middle">
    <rect x="210" y="40" width="26" height="34" fill="#2b3440" stroke="#333c47"/><text x="223" y="61" fill="#b6bdc7">0</text>
    <rect x="236" y="40" width="26" height="34" fill="#2b3440" stroke="#333c47"/><text x="249" y="61" fill="#b6bdc7">1</text>
    <rect x="262" y="40" width="26" height="34" fill="#2b3440" stroke="#333c47"/><text x="275" y="61" fill="#b6bdc7">2</text>
    <rect x="288" y="40" width="26" height="34" fill="#2b3440" stroke="#333c47"/><text x="301" y="61" fill="#b6bdc7">3</text>
    <rect x="314" y="40" width="26" height="34" fill="#242c35" stroke="#333c47"/><text x="327" y="61" fill="#b6bdc7">4</text>
    <rect x="340" y="40" width="26" height="34" fill="#242c35" stroke="#333c47"/><text x="353" y="61" fill="#b6bdc7">5</text>
    <rect x="366" y="40" width="26" height="34" fill="#242c35" stroke="#333c47"/><text x="379" y="61" fill="#b6bdc7">6</text>
    <rect x="392" y="40" width="26" height="34" fill="#242c35" stroke="#333c47"/><text x="405" y="61" fill="#b6bdc7">7</text>
    <rect x="418" y="40" width="26" height="34" fill="#2b3440" stroke="#333c47"/><text x="431" y="61" fill="#b6bdc7">8</text>
    <rect x="444" y="40" width="26" height="34" fill="#2b3440" stroke="#333c47"/><text x="457" y="61" fill="#b6bdc7">9</text>
    <rect x="470" y="40" width="26" height="34" fill="#2b3440" stroke="#333c47"/><text x="483" y="61" fill="#b6bdc7">10</text>
    <rect x="496" y="40" width="26" height="34" fill="#2b3440" stroke="#333c47"/><text x="509" y="61" fill="#b6bdc7">11</text>
  </g>
  <text x="223" y="92" fill="#8a94a1" font-family="sans-serif" font-size="9" text-anchor="middle">row 0</text>
  <text x="353" y="92" fill="#8a94a1" font-family="sans-serif" font-size="9" text-anchor="middle">row 1</text>
  <text x="483" y="92" fill="#8a94a1" font-family="sans-serif" font-size="9" text-anchor="middle">row 2</text>
  <text x="210" y="130" fill="#9aa4b2" font-family="sans-serif" font-size="11">Each of the 12 pixels is 3 bytes (R, G, B):</text>
  <g font-family="monospace" font-size="9" text-anchor="middle">
    <rect x="210" y="140" width="26" height="26" fill="#ff7b7233" stroke="#ff7b72"/><text x="223" y="157" fill="#ff9b95">R</text>
    <rect x="236" y="140" width="26" height="26" fill="#7ee78733" stroke="#7ee787"/><text x="249" y="157" fill="#9bf0a4">G</text>
    <rect x="262" y="140" width="26" height="26" fill="#6ea8fe33" stroke="#6ea8fe"/><text x="275" y="157" fill="#9cc4ff">B</text>
  </g>
  <text x="300" y="158" fill="#8a94a1" font-family="sans-serif" font-size="10">= pixel 0. So the array is 12 × 3 = 36 bytes.</text>
</svg>
<figcaption>The Image struct holds its size plus one flat vector of bytes — the same row-major layout from Lesson 2, now living inside a reusable object.</figcaption>
</figure>

Here's the struct:

```cpp
#include <vector>
#include <fstream>

struct Image {
    int width, height;
    std::vector<unsigned char> pixels;   // all bytes start at 0 = black

    Image(int w, int h) : width(w), height(h), pixels(w * h * 3, 0) {}

    void setPixel(int x, int y, int r, int g, int b) {
        if (x < 0 || x >= width || y < 0 || y >= height) return;  // skip off-image
        int i = (y * width + x) * 3;          // row-major index, x3 for R,G,B
        pixels[i] = r; pixels[i + 1] = g; pixels[i + 2] = b;
    }

    void save(const char* path) {
        std::ofstream f(path);
        f << "P3\n" << width << ' ' << height << "\n255\n";
        for (size_t i = 0; i < pixels.size(); i += 3)
            f << (int)pixels[i]     << ' '
              << (int)pixels[i + 1] << ' '
              << (int)pixels[i + 2] << '\n';
    }
};
```

### Reading it piece by piece

- `struct Image { ... };` — defines our new type. Anything of type `Image` carries a width, a height, and a `pixels` vector together.
- `Image(int w, int h) : width(w), height(h), pixels(w * h * 3, 0) {}` — this is the **constructor**, the function that runs when you make an `Image`. The part after the colon fills in the fields: it sets width and height, and creates the `pixels` vector with `w * h * 3` bytes, every one initialized to `0` — a fully black image.

<div class="note jargon">
<span class="note-title">Jargon · Constructor and member function</span>
A <strong>constructor</strong> is a special function with the same name as the struct; it sets up a new instance. A <strong>member function</strong> (or <em>method</em>) is a function that belongs to the struct and can touch its fields directly — <code>setPixel</code> and <code>save</code> are member functions of <code>Image</code>.
</div>

- `setPixel` first checks bounds: if `(x, y)` is outside the image it just returns, so we never write past the array (a common crash). Otherwise it computes `i = (y * width + x) * 3` — the exact row-major formula from Lesson 2, times 3 because each pixel owns 3 bytes — and writes red, green, blue into slots `i`, `i+1`, `i+2`.
- `save` writes the buffer out in the same **PPM** text format from Lesson 1: the `P3` header, then every pixel's three numbers. `std::ofstream` is an **output file stream** — like `std::cout`, but the `<<` goes into a file on disk instead of the terminal. `(int)pixels[i]` **casts** the byte to an `int` so it prints as a number (e.g. `200`) rather than as a raw character.

<div class="note key">
<span class="note-title">The payoff</span>
With just this struct you can create an image, poke any pixel with <code>setPixel(x, y, r, g, b)</code> in any order, and <code>save</code> it. Everything else in this lesson — and most 2D drawing — is built on top of that one <code>setPixel</code>.
</div>

## 2. What `setPixel` really does

`setPixel(x, y, …)` is the bridge between the 2D world we think in and the 1D memory the pixels actually live in. It turns a coordinate pair into an array position:

<figure>
<svg viewBox="0 0 560 170" width="540" xmlns="http://www.w3.org/2000/svg">
  <text x="10" y="18" fill="#9aa4b2" font-family="sans-serif" font-size="11">A 4×3 image — colour pixel (x = 2, y = 1)</text>
  <g font-family="monospace" font-size="10" text-anchor="middle">
    <rect x="20" y="28" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="54" y="28" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="88" y="28" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="122" y="28" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="20" y="58" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="54" y="58" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="88" y="58" width="34" height="30" fill="#f0a86855" stroke="#e8a672" stroke-width="2"/>
    <rect x="122" y="58" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="20" y="88" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="54" y="88" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="88" y="88" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <rect x="122" y="88" width="34" height="30" fill="#1d232b" stroke="#333c47"/>
    <text x="105" y="78" fill="#e8a672">2,1</text>
  </g>
  <text x="185" y="66" fill="#6ea8fe" font-family="sans-serif" font-size="22">→</text>
  <text x="220" y="60" fill="#7ee787" font-family="monospace" font-size="13">i = (y·width + x)·3</text>
  <text x="220" y="82" fill="#b6bdc7" font-family="monospace" font-size="13">  = (1·4 + 2)·3 = 18</text>
  <g font-family="monospace" font-size="10" text-anchor="middle">
    <rect x="430" y="40" width="34" height="30" fill="#ff7b7233" stroke="#ff7b72"/><text x="447" y="59" fill="#ff9b95">18</text>
    <rect x="464" y="40" width="34" height="30" fill="#7ee78733" stroke="#7ee787"/><text x="481" y="59" fill="#9bf0a4">19</text>
    <rect x="498" y="40" width="34" height="30" fill="#6ea8fe33" stroke="#6ea8fe"/><text x="515" y="59" fill="#9cc4ff">20</text>
  </g>
  <text x="430" y="88" fill="#8a94a1" font-family="sans-serif" font-size="10">the R, G, B bytes for pixel (2, 1)</text>
</svg>
<figcaption>setPixel converts (x, y) into the byte index <code>(y·width + x)·3</code>. For pixel (2, 1) in a 4-wide image that's byte 18, and its green and blue sit right after at 19 and 20.</figcaption>
</figure>

## 3. The problem with a line

Now the hard part. Ask for a line from one pixel to another — say from the top-left toward a point 10 columns right and 4 rows down. On paper a line is infinitely thin and perfectly straight. But our image is a **grid of whole pixels**: a pixel is either fully colored or not at all. There's no such thing as "35% of pixel (3, 1)." So the smooth line has to be *approximated* by choosing which whole pixels to light up.

<figure>
<svg viewBox="0 0 300 140" width="300" xmlns="http://www.w3.org/2000/svg">
  <g stroke="#333c47" stroke-width="1">
    <line x1="30" y1="20" x2="272" y2="20"/><line x1="30" y1="42" x2="272" y2="42"/>
    <line x1="30" y1="64" x2="272" y2="64"/><line x1="30" y1="86" x2="272" y2="86"/>
    <line x1="30" y1="108" x2="272" y2="108"/>
    <line x1="30" y1="20" x2="30" y2="108"/><line x1="52" y1="20" x2="52" y2="108"/>
    <line x1="74" y1="20" x2="74" y2="108"/><line x1="96" y1="20" x2="96" y2="108"/>
    <line x1="118" y1="20" x2="118" y2="108"/><line x1="140" y1="20" x2="140" y2="108"/>
    <line x1="162" y1="20" x2="162" y2="108"/><line x1="184" y1="20" x2="184" y2="108"/>
    <line x1="206" y1="20" x2="206" y2="108"/><line x1="228" y1="20" x2="228" y2="108"/>
    <line x1="250" y1="20" x2="250" y2="108"/><line x1="272" y1="20" x2="272" y2="108"/>
  </g>
  <line x1="41" y1="31" x2="261" y2="119" stroke="#6ea8fe" stroke-width="2.5"/>
  <circle cx="41" cy="31" r="4" fill="#7ee787"/>
  <circle cx="261" cy="119" r="4" fill="#7ee787"/>
  <text x="10" y="135" fill="#9aa4b2" font-family="sans-serif" font-size="11">The ideal line slices through many cells only partly — but a pixel can't be "half on."</text>
</svg>
<figcaption>The problem: the true straight line (blue) passes through partial pixels, yet each pixel is all-or-nothing. Which whole pixels do we fill?</figcaption>
</figure>

## 4. Slope, seen as a picture

The key idea is **slope**, and it's simpler than the word sounds. Slope just answers: *"as I take one step to the right, how far up or down does the line go?"*

Draw a right triangle under the line. The horizontal side is how far right we travel (the **run**); the vertical side is how far the line moves down (the **rise**). Slope is one divided by the other:

<figure>
<svg viewBox="0 0 320 180" width="320" xmlns="http://www.w3.org/2000/svg">
  <line x1="40" y1="30" x2="290" y2="130" stroke="#6ea8fe" stroke-width="2.5"/>
  <line x1="40" y1="30" x2="290" y2="30" stroke="#7ee787" stroke-width="2" stroke-dasharray="4 3"/>
  <line x1="290" y1="30" x2="290" y2="130" stroke="#e8a672" stroke-width="2" stroke-dasharray="4 3"/>
  <circle cx="40" cy="30" r="4" fill="#ffffff"/>
  <circle cx="290" cy="130" r="4" fill="#ffffff"/>
  <text x="120" y="24" fill="#7ee787" font-family="sans-serif" font-size="12">run = 10 columns right</text>
  <text x="298" y="85" fill="#e8a672" font-family="sans-serif" font-size="12">rise = 4</text>
  <text x="298" y="100" fill="#e8a672" font-family="sans-serif" font-size="12">rows down</text>
  <text x="40" y="160" fill="#b6bdc7" font-family="monospace" font-size="13">slope = rise ÷ run = 4 ÷ 10 = 0.4</text>
</svg>
<figcaption>Slope = rise ÷ run. For a line going 10 right and 4 down, slope = 0.4 — meaning each single step right drops the line by 0.4 of a row.</figcaption>
</figure>

<div class="note key">
<span class="note-title">That's the whole secret</span>
A slope of 0.4 means: every time <code>x</code> moves right by 1, the true line moves down by 0.4 of a pixel. After 1 step it's 0.4 down, after 2 steps 0.8 down, after 3 steps 1.2 down, and so on. Drawing the line is just deciding which whole row is closest to that running height at each column.
</div>

## 5. Rounding to the nearest row → the staircase

So walk column by column. At column `x`, the true line is `0.4 × x` rows down. Since a pixel must sit on a whole row, we light up the pixel on the **nearest whole row** to that height:

| Column x | True height (0.4 · x) | Nearest whole row → pixel y |
|---|---|---|
| 0 | 0.0 | 0 |
| 1 | 0.4 | 0 |
| 2 | 0.8 | 1 |
| 3 | 1.2 | 1 |
| 4 | 1.6 | 2 |
| 5 | 2.0 | 2 |
| 6 | 2.4 | 2 |
| 7 | 2.8 | 3 |
| 8 | 3.2 | 3 |
| 9 | 3.6 | 4 |
| 10 | 4.0 | 4 |

Fill those exact pixels and you get a **staircase** that hugs the ideal line as closely as whole pixels allow:

<figure>
<svg viewBox="0 0 300 140" width="300" xmlns="http://www.w3.org/2000/svg">
  <g>
    <rect x="30" y="20" width="22" height="22" fill="#6ea8fe"/>
    <rect x="52" y="20" width="22" height="22" fill="#6ea8fe"/>
    <rect x="74" y="42" width="22" height="22" fill="#6ea8fe"/>
    <rect x="96" y="42" width="22" height="22" fill="#6ea8fe"/>
    <rect x="118" y="64" width="22" height="22" fill="#6ea8fe"/>
    <rect x="140" y="64" width="22" height="22" fill="#6ea8fe"/>
    <rect x="162" y="64" width="22" height="22" fill="#6ea8fe"/>
    <rect x="184" y="86" width="22" height="22" fill="#6ea8fe"/>
    <rect x="206" y="86" width="22" height="22" fill="#6ea8fe"/>
    <rect x="228" y="108" width="22" height="22" fill="#6ea8fe"/>
    <rect x="250" y="108" width="22" height="22" fill="#6ea8fe"/>
  </g>
  <g stroke="#333c47" stroke-width="1">
    <line x1="30" y1="20" x2="272" y2="20"/><line x1="30" y1="42" x2="272" y2="42"/>
    <line x1="30" y1="64" x2="272" y2="64"/><line x1="30" y1="86" x2="272" y2="86"/>
    <line x1="30" y1="108" x2="272" y2="108"/><line x1="30" y1="130" x2="272" y2="130"/>
    <line x1="30" y1="20" x2="30" y2="130"/><line x1="52" y1="20" x2="52" y2="130"/>
    <line x1="74" y1="20" x2="74" y2="130"/><line x1="96" y1="20" x2="96" y2="130"/>
    <line x1="118" y1="20" x2="118" y2="130"/><line x1="140" y1="20" x2="140" y2="130"/>
    <line x1="162" y1="20" x2="162" y2="130"/><line x1="184" y1="20" x2="184" y2="130"/>
    <line x1="206" y1="20" x2="206" y2="130"/><line x1="228" y1="20" x2="228" y2="130"/>
    <line x1="250" y1="20" x2="250" y2="130"/><line x1="272" y1="20" x2="272" y2="130"/>
  </g>
  <line x1="41" y1="31" x2="261" y2="119" stroke="#ffffff" stroke-width="1.5" stroke-dasharray="5 3" opacity="0.85"/>
</svg>
<figcaption>The 11 pixels chosen by "nearest whole row" (blue), with the ideal line dashed on top. This staircase is exactly what the code below draws.</figcaption>
</figure>

## 6. Bresenham's line algorithm

Rounding `0.4 × x` works, but it uses decimals, and decimals are slower and can drift with rounding errors. **Bresenham's algorithm** does the identical rounding using **only whole numbers** — no decimals at all. The trick: instead of tracking the height as `0.4`, `0.8`, `1.2`…, it keeps a running **error** — how far the line has drifted from the current pixel's row — and only ever adds and subtracts integers. When the drift passes the halfway mark, it steps to the next row and pays the error back down.

<div class="note tip">
<span class="note-title">You don't need to memorize the algebra</span>
The mental model is enough: <em>march along the longer axis one pixel at a time; keep a tally of drift; when the tally tips past halfway, step over by one row.</em> The version below also handles lines going in <em>any</em> direction (up, down, left, right, steep or shallow) by treating the two axes symmetrically.
</div>

```cpp
#include <cstdlib>   // for std::abs

void drawLine(Image& img, int x0, int y0, int x1, int y1, int r, int g, int b) {
    int dx =  std::abs(x1 - x0), sx = x0 < x1 ? 1 : -1;   // x distance + dir
    int dy = -std::abs(y1 - y0), sy = y0 < y1 ? 1 : -1;   // y distance + dir
    int err = dx + dy;                        // the running drift ("error")

    while (true) {
        img.setPixel(x0, y0, r, g, b);        // light the current pixel
        if (x0 == x1 && y0 == y1) break;      // reached the end -> stop
        int e2 = 2 * err;
        if (e2 >= dy) { err += dy; x0 += sx; }   // step horizontally
        if (e2 <= dx) { err += dx; y0 += sy; }   // step vertically
    }
}
```

### Reading it piece by piece

- The parameters are the image (passed by **reference**, `Image&`, so we draw into the real buffer, not a copy), the two endpoints `(x0, y0)`–`(x1, y1)`, and the color.
- `dx` is how far apart the points are horizontally; `sx` is the **step direction**, `+1` if we're going right, `-1` if left. `dy` and `sy` are the same vertically (`dy` is stored negative, a neat trick that lets the two axes share one formula). The `? :` is the **ternary operator** — `condition ? a : b` means "use `a` if the condition is true, else `b`."
- `err` is the integer stand-in for that fractional drift from §5.
- Inside the loop we colour the current pixel, stop if we've arrived, then the two `if`s decide whether this step moves us across, down, or both. Each move adjusts `err` to keep the running tally honest. Because everything is `int`, there's not a single decimal in sight — that's Bresenham's whole point.

<div class="note jargon">
<span class="note-title">Jargon · Reference parameter and while(true)</span>
An <strong>&amp;</strong> on a parameter (<code>Image&amp; img</code>) passes the actual object by <strong>reference</strong> — changes inside the function affect the caller's image (and nothing gets copied, which matters for big buffers). <code>while (true)</code> is a loop with no built-in stop; it runs until a <code>break</code> statement jumps out — here, when we reach the end pixel.
</div>

## 7. Drawing something with it

Now the fun part. Here's a `main` that makes a 256×256 image and draws 24 colored lines radiating from the center — a starburst — then saves it:

```cpp
#include <cmath>   // for std::cos, std::sin

int main() {
    Image img(256, 256);
    int cx = 128, cy = 128;                       // center of the image
    int cols[6][3] = {{110,168,254},{126,231,135},{232,166,114},
                      {255,123,114},{200,150,255},{121,192,255}};
    for (int k = 0; k < 24; ++k) {
        double a = k * (2 * 3.14159265 / 24);   // one of 24 angles on a circle
        int x1 = cx + (int)(118 * std::cos(a));  // point on radius-118 circle
        int y1 = cy + (int)(118 * std::sin(a));
        int* c = cols[k % 6];                    // cycle the 6 colors
        drawLine(img, cx, cy, x1, y1, c[0], c[1], c[2]);
    }
    img.save("lines.ppm");
    return 0;
}
```

Each pass around the loop picks an **angle** `a` spread evenly around a full turn, finds the point that far around a circle (using `cos` and `sin` — we'll properly meet those when we do rotation later; here just trust that they place a point on a circle), and draws a line from the center out to it. Compiled and run, `drawLine` fires in every direction, so we're exercising all those step-direction cases at once. The saved image looks like this:

<figure>
<img alt="A starburst of 24 colored straight lines radiating from the center of a black square" style="width:256px;image-rendering:pixelated" src="data:image/png;base64,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"/>
<figcaption>The real output of the program above (converted from lines.ppm to PNG). 24 straight lines, every one built pixel-by-pixel by the same tiny <code>drawLine</code>.</figcaption>
</figure>

<div class="note key">
<span class="note-title">What you can now build</span>
A line is the first real building block. Three lines make a triangle outline; many short lines make curves; a grid of lines makes graphs and wireframes. And <code>setPixel</code> + <code>drawLine</code> are enough to draw the outline of almost any 2D shape. Filling those shapes with solid color is the next big idea (rasterization), which we'll get to with triangles.
</div>

## 8. What you learned

- An **image buffer** is a struct holding `width`, `height`, and a flat `std::vector` of bytes (heap memory) — a reusable object you can draw into and save.
- **`setPixel(x, y, r, g, b)`** is the bridge from 2D coordinates to 1D memory: it writes 3 bytes at `(y·width + x)·3`, with a bounds check to stay safe.
- A straight line can't be drawn exactly on a pixel grid — pixels are **whole, all-or-nothing** — so we approximate it with a **staircase** of the nearest pixels.
- **Slope = rise ÷ run** tells you how far the line moves per step; picking the **nearest whole row** to the running height gives the staircase.
- **Bresenham's algorithm** produces that same staircase using **only integers** (a running "error" tally instead of decimals), and handles lines in every direction.
- New vocabulary: struct, member function, constructor, `std::vector`, `unsigned char` / byte, `std::ofstream`, cast `(int)`, reference parameter `&`, ternary `? :`, `while(true)` / `break`, slope, rise, run, Bresenham, staircase (aliasing).

> Tell me which section to expand or slow down and I'll revise this lesson and rebuild the PDF.
