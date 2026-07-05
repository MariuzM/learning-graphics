# Lesson 2 · How a Computer Stores Things in Memory

<div class="cover-meta">
<span class="badge">Course: Graphics with C++</span>
<span class="badge">Level: Absolute beginner</span>
<span class="badge">Est. 50–70 min</span>
</div>

In Lesson 1 we kept using one slippery word: the **framebuffer** is "a region of memory." This lesson opens that word up. Every image, every 3D model, and every single variable your program touches lives in **memory** — so understanding what memory really is, why we use it, and what kinds exist is the foundation under all of graphics and, honestly, all of programming.

We'll stay concrete: real diagrams, a small C++ program with its output shown, and every term defined the first time it appears.

---

## 1. What "memory" actually is

Picture a impossibly long street of tiny lockers. The lockers are numbered `0, 1, 2, 3, …` all the way up. Each locker holds exactly one **byte** (8 bits — recall from Lesson 1 that's a value from 0 to 255). That street *is* your computer's memory.

<div class="note key">
<span class="note-title">The one idea to hold onto</span>
Memory is one enormous, numbered sequence of byte-sized slots. To store something you write bytes into some slots; to read it back you go to those slot numbers and read. That's the whole model.
</div>

The number of a slot is its **address**.

<div class="note jargon">
<span class="note-title">Jargon · Address</span>
An <strong>address</strong> is the position of a byte in memory — just an integer, like a house number on that street. When the CPU wants a value, it asks memory for "the bytes starting at address N."
</div>

<figure>
<svg viewBox="0 0 620 160" width="560" xmlns="http://www.w3.org/2000/svg">
  <text x="10" y="18" fill="#9aa4b2" font-family="sans-serif" font-size="12">Memory: one long line of numbered, byte-sized slots</text>
  <g font-family="monospace" font-size="12">
    <rect x="20"  y="40" width="66" height="42" fill="#232b34" stroke="#6ea8fe"/>
    <rect x="86"  y="40" width="66" height="42" fill="#232b34" stroke="#6ea8fe"/>
    <rect x="152" y="40" width="66" height="42" fill="#232b34" stroke="#6ea8fe"/>
    <rect x="218" y="40" width="66" height="42" fill="#232b34" stroke="#6ea8fe"/>
    <rect x="284" y="40" width="66" height="42" fill="#171c22" stroke="#333c47"/>
    <rect x="350" y="40" width="66" height="42" fill="#171c22" stroke="#333c47"/>
    <rect x="416" y="40" width="66" height="42" fill="#171c22" stroke="#333c47"/>
    <rect x="482" y="40" width="66" height="42" fill="#171c22" stroke="#333c47"/>
    <text x="42"  y="66" fill="#e8bd8a">2A</text>
    <text x="108" y="66" fill="#e8bd8a">00</text>
    <text x="174" y="66" fill="#e8bd8a">00</text>
    <text x="240" y="66" fill="#e8bd8a">00</text>
    <text x="308" y="66" fill="#8a94a1">..</text>
    <text x="374" y="66" fill="#8a94a1">..</text>
    <text x="440" y="66" fill="#8a94a1">..</text>
    <text x="506" y="66" fill="#8a94a1">..</text>
    <text x="34"  y="100" fill="#8a94a1" font-size="11">1000</text>
    <text x="100" y="100" fill="#8a94a1" font-size="11">1001</text>
    <text x="166" y="100" fill="#8a94a1" font-size="11">1002</text>
    <text x="232" y="100" fill="#8a94a1" font-size="11">1003</text>
    <text x="298" y="100" fill="#8a94a1" font-size="11">1004</text>
    <text x="364" y="100" fill="#8a94a1" font-size="11">1005</text>
    <text x="430" y="100" fill="#8a94a1" font-size="11">1006</text>
    <text x="496" y="100" fill="#8a94a1" font-size="11">1007</text>
  </g>
  <path d="M20 128 L284 128" stroke="#6ea8fe" stroke-width="1.5"/>
  <path d="M20 122 L20 128 M284 122 L284 128" stroke="#6ea8fe" stroke-width="1.5"/>
  <text x="60" y="146" fill="#6ea8fe" font-family="sans-serif" font-size="12">one 4-byte int lives across addresses 1000–1003</text>
</svg>
<figcaption>Each slot is one byte with its own address. A value that needs more than one byte (like a 4-byte int) simply occupies several consecutive slots.</figcaption>
</figure>

## 2. How a single value is stored: binary

The slots don't hold decimal numbers or letters directly — at the hardware level everything is **bits**, each a single 0 or 1 (a tiny switch that's off or on). A byte is 8 of those switches. To store the number 42, the computer picks the pattern of 8 bits that *adds up* to 42.

Each of the 8 positions has a "place value" — like columns in ordinary decimal, but doubling each step instead of ×10:

<figure>
<svg viewBox="0 0 470 150" width="440" xmlns="http://www.w3.org/2000/svg">
  <g font-family="monospace" font-size="15" text-anchor="middle">
    <text x="35"  y="20" fill="#8a94a1" font-size="11">128</text>
    <text x="90"  y="20" fill="#8a94a1" font-size="11">64</text>
    <text x="145" y="20" fill="#8a94a1" font-size="11">32</text>
    <text x="200" y="20" fill="#8a94a1" font-size="11">16</text>
    <text x="255" y="20" fill="#8a94a1" font-size="11">8</text>
    <text x="310" y="20" fill="#8a94a1" font-size="11">4</text>
    <text x="365" y="20" fill="#8a94a1" font-size="11">2</text>
    <text x="420" y="20" fill="#8a94a1" font-size="11">1</text>
    <rect x="12"  y="30" width="46" height="46" fill="#171c22" stroke="#333c47"/>
    <rect x="67"  y="30" width="46" height="46" fill="#171c22" stroke="#333c47"/>
    <rect x="122" y="30" width="46" height="46" fill="#6ea8fe22" stroke="#6ea8fe"/>
    <rect x="177" y="30" width="46" height="46" fill="#171c22" stroke="#333c47"/>
    <rect x="232" y="30" width="46" height="46" fill="#6ea8fe22" stroke="#6ea8fe"/>
    <rect x="287" y="30" width="46" height="46" fill="#171c22" stroke="#333c47"/>
    <rect x="342" y="30" width="46" height="46" fill="#6ea8fe22" stroke="#6ea8fe"/>
    <rect x="397" y="30" width="46" height="46" fill="#171c22" stroke="#333c47"/>
    <text x="35"  y="61" fill="#b6bdc7">0</text>
    <text x="90"  y="61" fill="#b6bdc7">0</text>
    <text x="145" y="61" fill="#7ee787">1</text>
    <text x="200" y="61" fill="#b6bdc7">0</text>
    <text x="255" y="61" fill="#7ee787">1</text>
    <text x="310" y="61" fill="#b6bdc7">0</text>
    <text x="365" y="61" fill="#7ee787">1</text>
    <text x="420" y="61" fill="#b6bdc7">0</text>
  </g>
  <text x="12" y="110" fill="#b6bdc7" font-family="sans-serif" font-size="13">Turned-on bits: 32 + 8 + 2 = <tspan fill="#7ee787" font-weight="bold">42</tspan></text>
  <text x="12" y="132" fill="#8a94a1" font-family="sans-serif" font-size="12">So the byte for 42 is the bit pattern 00101010.</text>
</svg>
<figcaption>One byte = 8 bits with place values 128…1. The number 42 is the pattern 00101010 (32 + 8 + 2).</figcaption>
</figure>

<div class="note jargon">
<span class="note-title">Jargon · Binary</span>
<strong>Binary</strong> is counting with only two digits, 0 and 1 (base 2), because a bit can only be off or on. Decimal columns are 1, 10, 100…; binary columns are 1, 2, 4, 8, 16… Everything in a computer — numbers, text, images, code — is ultimately stored as binary.
</div>

One byte can only reach 255 (all eight bits on = 128+64+…+1). Bigger numbers simply use **more bytes**. That's exactly why different kinds of data reserve different amounts of memory.

## 3. Data types and how many bytes they take

In C++ you tell the compiler what *kind* of value a variable holds — its **type** — and the type decides how many bytes get reserved. More bytes means a bigger range (for integers) or more precision (for decimals).

Here are the common types and their sizes **on your Mac** (an arm64 / Apple Silicon machine — these are typical for any 64-bit computer, but the C++ standard lets them vary, so never assume; measure):

| Type | Meaning | Size on your Mac |
|---|---|---|
| `char` | one character / one byte | 1 byte |
| `bool` | true or false | 1 byte |
| `int` | whole number | 4 bytes |
| `float` | decimal number | 4 bytes |
| `double` | decimal, double precision | 8 bytes |
| `int*` (a pointer) | an address | 8 bytes |

<div class="note jargon">
<span class="note-title">Jargon · float and double</span>
<strong>float</strong> and <strong>double</strong> store numbers with a fractional part (like 3.14) using <em>floating point</em> — a scientific-notation-like format. <strong>double</strong> uses twice the bytes of a <strong>float</strong>, so it holds more digits of precision. Graphics math uses these constantly for positions, colors, and directions.
</div>

A 4-byte `int` has 32 bits, so it can represent 2³² ≈ **4.29 billion** different values. That range is split between negative and positive, giving roughly −2.1 billion to +2.1 billion.

### Example: a program that reveals sizes and addresses

Here's a short program that asks C++ directly for these sizes, and also prints where two variables live in memory:

```cpp
#include <iostream>

int main() {
    // sizeof tells you how many bytes a type occupies on THIS machine.
    std::cout << "char:    " << sizeof(char)   << " byte(s)\n";
    std::cout << "bool:    " << sizeof(bool)   << " byte(s)\n";
    std::cout << "int:     " << sizeof(int)    << " byte(s)\n";
    std::cout << "float:   " << sizeof(float)  << " byte(s)\n";
    std::cout << "double:  " << sizeof(double) << " byte(s)\n";
    std::cout << "pointer: " << sizeof(int*)   << " byte(s)\n";

    int a = 42;
    int b = 7;
    // The & operator asks: "what address does this variable live at?"
    std::cout << "a = " << a << ", stored at address " << &a << '\n';
    std::cout << "b = " << b << ", stored at address " << &b << '\n';
    return 0;
}
```

On this arm64 Mac, that program prints (the addresses are different on every run — that's normal):

```text
char:    1 byte(s)
bool:    1 byte(s)
int:     4 byte(s)
float:   4 byte(s)
double:  8 byte(s)
pointer: 8 byte(s)
a = 42, stored at address 0x16bb3a4a8
b = 7, stored at address 0x16bb3a4a4
```

<div class="note jargon">
<span class="note-title">Jargon · sizeof and the & operator</span>
<code>sizeof(T)</code> is a built-in that reports how many bytes type <code>T</code> takes. The <strong>address-of operator</strong> <code>&x</code> gives the memory address where variable <code>x</code> lives. Notice the two addresses above differ by 4 — the two 4-byte ints sit right next to each other in memory.
</div>

## 4. Addresses and pointers

Because every variable has an address, a program can hand around *the address itself* instead of copying the whole value. A variable that stores an address is called a **pointer**.

<div class="note jargon">
<span class="note-title">Jargon · Pointer</span>
A <strong>pointer</strong> is a variable whose value is a memory address — it "points at" another piece of data. Written <code>int*</code> ("pointer to int"). This is how a program refers to a big block of memory (like an image's pixels) cheaply: pass the one small address, not a copy of all the bytes.
</div>

<div class="note tip">
<span class="note-title">Why this matters for graphics</span>
An image can be millions of bytes. You never want to copy that around by accident. Pointers (and their friends, arrays and references) let your code say "the pixels start <em>here</em>" with a single 8-byte address. Every graphics API you'll ever touch passes image and buffer data by address.
</div>

## 5. Why keep things in memory at all? Memory vs storage

Here's the crucial fact: **the CPU can only compute on data that's in memory.** More precisely, it loads bytes from main memory into tiny on-chip slots called **registers**, does arithmetic there, and writes results back to memory. It cannot add two numbers that are sitting on your SSD — they must be brought into memory first.

So why not just use the disk for everything?

- **Main memory (RAM) is fast but forgetful.** It answers in tens of nanoseconds, but it's **volatile** — cut the power and its contents vanish.
- **Disk/SSD is slow but permanent.** It's thousands of times slower to reach, but **non-volatile** — your files survive a reboot.

<div class="note jargon">
<span class="note-title">Jargon · Volatile vs non-volatile</span>
<strong>Volatile</strong> memory loses everything when powered off (RAM, caches, registers). <strong>Non-volatile</strong> storage keeps its contents without power (SSD, hard disk, flash drives). "Saving a file" means copying from volatile memory to non-volatile storage so it outlives the program.
</div>

<div class="note key">
<span class="note-title">The division of labor</span>
Data you're actively working on lives in <strong>RAM</strong> (fast, temporary). Data you want to keep lives on <strong>disk</strong> (slow, permanent). Your gradient from Lesson 1 was computed in memory, then <em>saved</em> to disk as a <code>.ppm</code> file so it wouldn't disappear.
</div>

## 6. The types of memory: the memory hierarchy

If RAM is "fast" and disk is "slow," reality is actually a whole ladder of memory, each rung faster and smaller than the one below. Faster memory is more expensive per byte, so computers use a little of the fast stuff and a lot of the slow stuff, arranged as a hierarchy.

<figure>
<svg viewBox="0 0 460 250" width="440" xmlns="http://www.w3.org/2000/svg">
  <polygon points="200,15 250,55 150,55" fill="#6ea8fe" stroke="#0d1117"/>
  <polygon points="150,55 250,55 275,95 125,95" fill="#79c0ff" stroke="#0d1117"/>
  <polygon points="125,95 275,95 300,135 100,135" fill="#7ee787" stroke="#0d1117"/>
  <polygon points="100,135 300,135 330,185 70,185" fill="#e8a672" stroke="#0d1117"/>
  <polygon points="70,185 330,185 365,235 35,235" fill="#c8a9ff" stroke="#0d1117"/>
  <g font-family="sans-serif" font-size="12" fill="#0d1117" text-anchor="middle" font-weight="bold">
    <text x="200" y="50">Registers</text>
    <text x="200" y="80">CPU cache (L1/L2/L3)</text>
    <text x="200" y="120">RAM (main memory)</text>
    <text x="200" y="166">SSD</text>
    <text x="200" y="216">Hard disk</text>
  </g>
  <text x="378" y="40" fill="#7ee787" font-family="sans-serif" font-size="11">faster,</text>
  <text x="378" y="55" fill="#7ee787" font-family="sans-serif" font-size="11">smaller,</text>
  <text x="378" y="70" fill="#7ee787" font-family="sans-serif" font-size="11">costlier</text>
  <text x="2" y="205" fill="#e8a672" font-family="sans-serif" font-size="11">slower,</text>
  <text x="2" y="220" fill="#e8a672" font-family="sans-serif" font-size="11">bigger,</text>
  <text x="2" y="235" fill="#e8a672" font-family="sans-serif" font-size="11">cheaper</text>
</svg>
<figcaption>The memory hierarchy. The CPU checks the fast top layers first; only what doesn't fit falls through to the slower, larger layers below.</figcaption>
</figure>

Rough figures (they vary a lot by hardware — treat them as orders of magnitude, not exact):

| Memory | Typical size | Approx. time to reach | Volatile? |
|---|---|---|---|
| Registers | a few hundred bytes | under 1 ns | yes |
| L1 cache | tens of KB | ~1 ns | yes |
| L2 / L3 cache | hundreds of KB – tens of MB | ~4–20 ns | yes |
| RAM | 8 – 64 GB | ~80–100 ns | yes |
| SSD | 256 GB – several TB | ~50–150 µs | no |
| Hard disk | 1 – 20 TB | ~5–10 ms | no |

<div class="note jargon">
<span class="note-title">Jargon · Register, cache, RAM</span>
A <strong>register</strong> is a handful of bytes physically inside the CPU where actual computation happens — the fastest memory there is. A <strong>cache</strong> is a small, fast memory that keeps recently-used data close to the CPU so it doesn't have to make the slow trip to RAM every time. <strong>RAM</strong> stands for <em>Random Access Memory</em>: "random access" means any address can be read in about the same time, in any order — unlike a tape you'd have to wind through.
</div>

<div class="note tip">
<span class="note-title">Graphics footnote: the GPU has its own memory</span>
A <strong>GPU</strong> (graphics processor) traditionally has its own separate memory called <strong>VRAM</strong>, and images/textures must be copied into it before the GPU can draw with them. Your Apple Silicon Mac is special here: it uses <strong>unified memory</strong>, where the CPU and GPU share one pool of RAM — so there's no separate copy step. We'll care about this a lot once we reach the GPU.
</div>

## 7. A quick note on RAM vs ROM

You'll also hear **ROM** — *Read-Only Memory*. Unlike RAM, it's non-volatile and normally not written to during use; it holds things like the firmware that starts your computer. The RAM in your machine is technically **DRAM** (dynamic), which is dense and cheap; caches are **SRAM** (static), which is faster but takes more space per byte. You don't need these acronyms to write graphics code — just know that "memory" isn't one single thing.

## 8. How your program's memory is organized: stack and heap

When your program runs, the operating system hands it a big span of addresses to use. That span is split into regions with different jobs. Two of them matter constantly:

- The **stack** — where local variables (like `a` and `b` above) live. It's automatic: space is reserved when a function starts and freed the instant the function returns. Fast, but limited in size.
- The **heap** — a large pool you request memory from explicitly when you need something big or long-lived (like a whole image). You control when it's allocated and freed.

<figure>
<svg viewBox="0 0 440 250" width="380" xmlns="http://www.w3.org/2000/svg">
  <text x="10" y="16" fill="#8a94a1" font-family="sans-serif" font-size="11">high addresses</text>
  <text x="10" y="244" fill="#8a94a1" font-family="sans-serif" font-size="11">low addresses</text>
  <rect x="120" y="24" width="200" height="46" fill="#6ea8fe22" stroke="#6ea8fe"/>
  <rect x="120" y="70" width="200" height="70" fill="#232b34" stroke="#333c47"/>
  <rect x="120" y="140" width="200" height="46" fill="#7ee78722" stroke="#7ee787"/>
  <rect x="120" y="186" width="200" height="30" fill="#e8a67222" stroke="#e8a672"/>
  <rect x="120" y="216" width="200" height="26" fill="#c8a9ff22" stroke="#c8a9ff"/>
  <g font-family="sans-serif" font-size="12" text-anchor="middle">
    <text x="220" y="45" fill="#6ea8fe" font-weight="bold">Stack</text>
    <text x="220" y="61" fill="#8a94a1" font-size="10">local variables</text>
    <text x="220" y="108" fill="#8a94a1" font-size="10">(free space they grow into)</text>
    <text x="220" y="161" fill="#7ee787" font-weight="bold">Heap</text>
    <text x="220" y="176" fill="#8a94a1" font-size="10">big / long-lived data</text>
    <text x="220" y="205" fill="#e8a672" font-weight="bold">Globals / static data</text>
    <text x="220" y="233" fill="#c8a9ff" font-weight="bold">Code (the program itself)</text>
  </g>
  <path d="M95 30 L95 95" stroke="#6ea8fe" stroke-width="2" marker-end="url(#dn)"/>
  <path d="M345 180 L345 115" stroke="#7ee787" stroke-width="2" marker-end="url(#up)"/>
  <defs>
    <marker id="dn" markerWidth="10" markerHeight="10" refX="4" refY="6" orient="auto"><path d="M0,0 L8,0 L4,7 Z" fill="#6ea8fe"/></marker>
    <marker id="up" markerWidth="10" markerHeight="10" refX="4" refY="1" orient="auto"><path d="M4,0 L8,7 L0,7 Z" fill="#7ee787"/></marker>
  </defs>
  <text x="60" y="66" fill="#6ea8fe" font-family="sans-serif" font-size="10">grows down</text>
  <text x="352" y="150" fill="#7ee787" font-family="sans-serif" font-size="10">grows up</text>
</svg>
<figcaption>A running program's memory. The stack grows downward from high addresses; the heap grows upward toward it. Globals and the program's own code sit at the bottom.</figcaption>
</figure>

<div class="note jargon">
<span class="note-title">Jargon · Stack, heap, allocation</span>
The <strong>stack</strong> holds a function's local variables and is managed automatically (last-in, first-out). The <strong>heap</strong> is memory you request on demand for data whose size or lifetime you decide at run time. <strong>Allocation</strong> is the act of reserving memory; <strong>freeing</strong> (or <em>deallocation</em>) is giving it back. Forgetting to free heap memory is a <strong>memory leak</strong>.
</div>

<div class="note warn">
<span class="note-title">Watch out</span>
The stack is small (often just a few megabytes). A full-screen image is far too big to put there — try, and you get a <em>stack overflow</em> crash. Big buffers like images belong on the heap. In modern C++ you rarely call raw allocation yourself; containers like <code>std::vector</code> manage heap memory for you, which is exactly what we'll use for an image buffer.
</div>

## 9. Back to graphics: how an image sits in memory

Memory is one-dimensional — a single line of bytes — but an image is two-dimensional. So how does a 2D grid of pixels fit into 1D memory? We simply lay the rows out end to end: all of row 0, then all of row 1, then row 2, and so on. This is called **row-major** order.

<figure>
<svg viewBox="0 0 560 220" width="540" xmlns="http://www.w3.org/2000/svg">
  <text x="10" y="16" fill="#9aa4b2" font-family="sans-serif" font-size="12">A 4×3 image (the 2D way we think of it)</text>
  <g font-family="monospace" font-size="11" text-anchor="middle">
    <rect x="20" y="26" width="40" height="34" fill="#232b34" stroke="#333c47"/><text x="40" y="48" fill="#b6bdc7">0</text>
    <rect x="60" y="26" width="40" height="34" fill="#232b34" stroke="#333c47"/><text x="80" y="48" fill="#b6bdc7">1</text>
    <rect x="100" y="26" width="40" height="34" fill="#232b34" stroke="#333c47"/><text x="120" y="48" fill="#b6bdc7">2</text>
    <rect x="140" y="26" width="40" height="34" fill="#232b34" stroke="#333c47"/><text x="160" y="48" fill="#b6bdc7">3</text>
    <rect x="20" y="60" width="40" height="34" fill="#2b3440" stroke="#333c47"/><text x="40" y="82" fill="#b6bdc7">4</text>
    <rect x="60" y="60" width="40" height="34" fill="#2b3440" stroke="#333c47"/><text x="80" y="82" fill="#b6bdc7">5</text>
    <rect x="100" y="60" width="40" height="34" fill="#2b3440" stroke="#6ea8fe" stroke-width="2"/><text x="120" y="82" fill="#7ee787">6</text>
    <rect x="140" y="60" width="40" height="34" fill="#2b3440" stroke="#333c47"/><text x="160" y="82" fill="#b6bdc7">7</text>
    <rect x="20" y="94" width="40" height="34" fill="#232b34" stroke="#333c47"/><text x="40" y="116" fill="#b6bdc7">8</text>
    <rect x="60" y="94" width="40" height="34" fill="#232b34" stroke="#333c47"/><text x="80" y="116" fill="#b6bdc7">9</text>
    <rect x="100" y="94" width="40" height="34" fill="#232b34" stroke="#333c47"/><text x="120" y="116" fill="#b6bdc7">10</text>
    <rect x="140" y="94" width="40" height="34" fill="#232b34" stroke="#333c47"/><text x="160" y="116" fill="#b6bdc7">11</text>
  </g>
  <text x="205" y="80" fill="#6ea8fe" font-family="sans-serif" font-size="20">→</text>
  <text x="250" y="16" fill="#9aa4b2" font-family="sans-serif" font-size="12">…stored as one 1D line in memory (row after row)</text>
  <g font-family="monospace" font-size="10" text-anchor="middle">
    <rect x="250" y="60" width="24" height="34" fill="#232b34" stroke="#333c47"/><text x="262" y="82" fill="#b6bdc7">0</text>
    <rect x="274" y="60" width="24" height="34" fill="#232b34" stroke="#333c47"/><text x="286" y="82" fill="#b6bdc7">1</text>
    <rect x="298" y="60" width="24" height="34" fill="#232b34" stroke="#333c47"/><text x="310" y="82" fill="#b6bdc7">2</text>
    <rect x="322" y="60" width="24" height="34" fill="#232b34" stroke="#333c47"/><text x="334" y="82" fill="#b6bdc7">3</text>
    <rect x="346" y="60" width="24" height="34" fill="#2b3440" stroke="#333c47"/><text x="358" y="82" fill="#b6bdc7">4</text>
    <rect x="370" y="60" width="24" height="34" fill="#2b3440" stroke="#333c47"/><text x="382" y="82" fill="#b6bdc7">5</text>
    <rect x="394" y="60" width="24" height="34" fill="#2b3440" stroke="#6ea8fe" stroke-width="2"/><text x="406" y="82" fill="#7ee787">6</text>
    <rect x="418" y="60" width="24" height="34" fill="#2b3440" stroke="#333c47"/><text x="430" y="82" fill="#b6bdc7">7</text>
    <rect x="442" y="60" width="24" height="34" fill="#232b34" stroke="#333c47"/><text x="454" y="82" fill="#b6bdc7">8</text>
    <rect x="466" y="60" width="24" height="34" fill="#232b34" stroke="#333c47"/><text x="478" y="82" fill="#b6bdc7">9</text>
    <rect x="490" y="60" width="24" height="34" fill="#232b34" stroke="#333c47"/><text x="502" y="82" fill="#b6bdc7">10</text>
    <rect x="514" y="60" width="24" height="34" fill="#232b34" stroke="#333c47"/><text x="526" y="82" fill="#b6bdc7">11</text>
  </g>
  <text x="262" y="112" fill="#8a94a1" font-family="sans-serif" font-size="10">row 0</text>
  <text x="358" y="112" fill="#8a94a1" font-family="sans-serif" font-size="10">row 1</text>
  <text x="454" y="112" fill="#8a94a1" font-family="sans-serif" font-size="10">row 2</text>
  <text x="20" y="165" fill="#7ee787" font-family="monospace" font-size="14">index = y * width + x</text>
  <text x="20" y="190" fill="#b6bdc7" font-family="sans-serif" font-size="12">Pixel (x=2, y=1) in a width-4 image → 1*4 + 2 = index 6 (highlighted).</text>
</svg>
<figcaption>A 2D image flattened into 1D memory, row by row. The formula <code>y * width + x</code> converts a pixel's (x, y) into its position in that single line.</figcaption>
</figure>

That one formula, `index = y * width + x`, is how essentially every framebuffer is addressed. (When each pixel is 3 bytes for R, G, B, you multiply by 3: the red byte of pixel (x, y) sits at `(y * width + x) * 3`.)

<div class="note key">
<span class="note-title">How much memory is an image?</span>
Multiply width × height × bytes-per-pixel. A 1024×1024 image with 3 bytes per pixel is 1024 × 1024 × 3 = <strong>3,145,728 bytes ≈ 3 MB</strong>. A 1920×1080 photo is about <strong>6 MB</strong>. That's why images go on the heap, and why fast memory matters: the GPU may touch millions of these bytes every frame.
</div>

## 10. More worked examples

These tie the ideas together with concrete numbers.

### A · Storing the letter `A`

A `char` is one byte. Text is stored by mapping each character to a number using a code called **ASCII**. The letter `A` is 65, and 65 in binary is `01000001` (64 + 1). So a `char` holding `A` is literally the byte `01000001`. Lowercase `a` is 97 (`01100001`).

### B · When a value doesn't fit in its bytes

One byte can hold at most 255. The number 200 fits fine: `11001000` (128 + 64 + 8). But 300 needs a 9th bit (the 256 place), so it **cannot** live in a single byte — it would *overflow*. This is why a count that might pass 255 is stored in a wider type like `int` (4 bytes) instead of a single byte.

### C · How much memory an image needs

The size is always `width × height × bytes-per-pixel` (3 bytes per pixel for RGB):

| Image | Pixels | Bytes (× 3) | ≈ |
|---|---|---|---|
| 256 × 256 | 65,536 | 196,608 | ~192 KB |
| 1920 × 1080 (Full HD) | 2,073,600 | 6,220,800 | ~5.9 MB |
| 3840 × 2160 (4K) | 8,294,400 | 24,883,200 | ~23.7 MB |

Notice how fast it grows: doubling width *and* height quadruples the memory.

### D · Finding a pixel inside the 1D buffer

Take a 256-pixel-wide RGB image and ask: where is the **red** byte of pixel (x = 10, y = 3)? Using `(y × width + x) × 3`:

```text
(3 × 256 + 10) × 3
= (768 + 10) × 3
= 778 × 3
= 2334
```

So red is at index 2334, green at 2335, blue at 2336.

### E · Stack or heap for a photo?

A 4000 × 3000 photo at 3 bytes per pixel is 4000 × 3000 × 3 = 36,000,000 bytes ≈ **34 MB**. The stack is usually only a few MB, so putting 34 MB there would overflow it and crash. Therefore the image buffer must live on the **heap** (in practice, inside a `std::vector`). A loop counter or an (x, y) pair, being just a few bytes, happily stays on the stack.

## 11. What you learned

- Memory is **one long, numbered sequence of bytes**; each byte's number is its **address**.
- Everything is stored in **binary**; bigger values use more bytes, which is why each **type** reserves a fixed size (`int` = 4 bytes, `double` = 8, a pointer = 8 on your Mac).
- A **pointer** is a variable that holds an address — how programs refer to big data (like images) cheaply.
- The CPU computes only on data in memory; **RAM** is fast but **volatile**, **disk** is slow but **non-volatile** — hence "working data in RAM, saved files on disk."
- Memory is a **hierarchy**: registers → cache → RAM → SSD → disk, trading speed for size.
- A running program splits its memory into **stack** (small, automatic locals) and **heap** (big, on-demand data like images), plus globals and code.
- A 2D image lives in 1D memory in **row-major** order, addressed by `index = y * width + x`.
- New vocabulary: address, binary, bit/byte (recap), type sizes, `sizeof`, `&`, pointer, register, cache, RAM/ROM, volatile/non-volatile, memory hierarchy, stack, heap, allocation, memory leak, row-major.

> If any part of this is fuzzy, tell me which section and I'll expand it and rebuild the PDF.
