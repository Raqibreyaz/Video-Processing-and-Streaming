# Chroma Subsampling

## What it is

**Chroma subsampling** is a technique used in video/image representation to reduce the spatial detail stored for **color information** while keeping more detail for **brightness information**.

The basic idea is:

```text
Y → brightness
U + V → color
```

Our eyes are generally **more sensitive to fine brightness/detail differences than to fine color differences**, especially around edges and fine structures.

So video can keep detailed brightness information while storing less detailed color information.

> **Chroma subsampling = keep detailed luma information, but sample chroma at a lower spatial resolution to save data.**

---

## One-sentence summary

> **Chroma subsampling reduces the spatial resolution of U/V color information while keeping Y brightness information more detailed, reducing the amount of data needed to represent video.**

---

## Intuition

Imagine four nearby pixels:

```text
┌────┬────┐
│ P1 │ P2 │
├────┼────┤
│ P3 │ P4 │
└────┴────┘
```

Each pixel needs brightness information:

```text
Y1  Y2
Y3  Y4
```

But our eyes are less sensitive to very fine differences in color than to brightness/detail.

So instead of storing completely separate high-resolution chroma information for every location, we can use fewer chroma samples:

```text
    U
    V
```

Conceptually:

```text
P1 ─┐
P2 ─┤
P3 ─┼──→ shared/reconstructed color information
P4 ─┘
```

The pixels **still have color**.

We are simply storing the color information at a lower spatial resolution.

---

# Why do we need chroma subsampling?

Without chroma subsampling, we could store:

```text
Y → full detail
U → full detail
V → full detail
```

But this means storing a lot of color information.

Since fine color differences are generally less noticeable than fine brightness differences, we can reduce the spatial resolution of U and V.

```text
Detailed brightness
        +
Less-detailed color
        ↓
Good visual result
        +
Less data
```

This is one reason video can represent color without storing full-resolution color information everywhere.

---

# What does "reducing color detail" actually mean?

It does **not** mean:

```text
make the color thinner      ❌
reduce brightness           ❌
change opacity              ❌
remove color completely     ❌
```

Instead, it means:

> **Use fewer color measurements across nearby pixels and let nearby pixels share/reconstruct some chroma information.**

For example, instead of:

```text
P1 → its own color information
P2 → its own color information
P3 → its own color information
P4 → its own color information
```

we can have fewer chroma samples:

```text
P1 ─┐
P2 ─┤
P3 ─┼──→ shared/reconstructed color information
P4 ─┘
```

The result is that **fine differences between neighboring colors may be lost**.

---

# 4:4:4, 4:2:2, and 4:2:0

These are different **chroma-sampling patterns**.

A very important point:

> The numbers do **not** mean `4 = Y`, `2 = U`, `0 = V`.

That interpretation is incorrect.

Instead, the notation describes the **relative spatial sampling of luma and chroma**.

The easiest way to understand them is by looking at the resolution of the Y, U, and V planes.

---

# 1. 4:4:4 — Full chroma resolution

In `4:4:4`, chroma has the same spatial resolution as luma.

```text
Y → full width × full height
U → full width × full height
V → full width × full height
```

For a:

```text
1280 × 720
```

frame:

```text
Y → 1280 × 720
U → 1280 × 720
V → 1280 × 720
```

Conceptually:

```text
Y:
● ● ● ●
● ● ● ●

U/V:
● ● ● ●
● ● ● ●
```

So color is sampled at the same spatial resolution as brightness.

### Mental model

```text
4:4:4

Y → ████████████
U → ████████████
V → ████████████

Full spatial resolution for all components
```

---

# 2. 4:2:2 — Half horizontal chroma resolution

In `4:2:2`, chroma resolution is reduced **horizontally**.

For a:

```text
1280 × 720
```

frame:

```text
Y → 1280 × 720
U →  640 × 720
V →  640 × 720
```

Conceptually:

```text
Y:
● ● ● ●
● ● ● ●

Chroma:
●   ●
●   ●
```

Notice:

* Y has full width.
* U and V have half the width.
* U and V keep the full height.

So:

```text
4:2:2
   ↓
half horizontal chroma resolution
   +
full vertical chroma resolution
```

---

# 3. 4:2:0 — Half horizontal and vertical chroma resolution

In `4:2:0`, chroma resolution is reduced in **both directions**.

For:

```text
1280 × 720
```

we have:

```text
Y → 1280 × 720
U →  640 × 360
V →  640 × 360
```

Conceptually:

```text
Y:
● ● ● ●
● ● ● ●

Chroma:
●   ●
```

So:

```text
4:2:0
   ↓
half horizontal chroma resolution
   +
half vertical chroma resolution
```

This means each chroma plane has:

```text
640 × 360
```

samples instead of:

```text
1280 × 720
```

---

# Comparison

For a `1280 × 720` image:

| Format    |        Y |        U |        V | Chroma resolution        |
| --------- | -------: | -------: | -------: | ------------------------ |
| **4:4:4** | 1280×720 | 1280×720 | 1280×720 | Full width + full height |
| **4:2:2** | 1280×720 |  640×720 |  640×720 | Half width, full height  |
| **4:2:0** | 1280×720 |  640×360 |  640×360 | Half width + half height |

The important progression is:

```text
4:4:4
   ↓
Full chroma resolution


4:2:2
   ↓
Half horizontal chroma resolution


4:2:0
   ↓
Half horizontal + half vertical chroma resolution
```

---

# Understanding 4:2:0 with four pixels

Consider a 2×2 group:

```text
┌────┬────┐
│ P1 │ P2 │
├────┼────┤
│ P3 │ P4 │
└────┴────┘
```

The luma information can be represented as:

```text
Y1  Y2
Y3  Y4
```

So every pixel has its own luma sample.

But the chroma is sampled at a lower resolution:

```text
    U
    V
```

Conceptually, that chroma information can be used/shared across the nearby pixels.

So think:

```text
P1 → Y1 + chroma information
P2 → Y2 + chroma information
P3 → Y3 + chroma information
P4 → Y4 + chroma information
```

The important point is that **the four pixels do not need four separate full-resolution chroma samples**.

---

# What does the `0` in 4:2:0 mean?

This is a common source of confusion.

It does **not** mean:

```text
0 chroma
```

It also does **not** mean:

```text
4 pixels
→ 2 chroma samples
→ 0 chroma samples
```

Instead, `4:2:0` is a notation describing the **sampling pattern**.

The useful mental model is simply:

```text
4:2:0
→ reduced horizontal chroma sampling
→ reduced vertical chroma sampling
→ chroma has lower spatial resolution
```

So:

> **0 does not mean "zero color."**

---

# What is actually being saved?

Suppose we compare `4:4:4` and `4:2:0` for a `1280 × 720` frame.

### 4:4:4

```text
Y = 1280 × 720
U = 1280 × 720
V = 1280 × 720
```

Total samples:

```text
921,600
+ 921,600
+ 921,600

= 2,764,800 samples
```

### 4:2:0

```text
Y = 1280 × 720
U = 640 × 360
V = 640 × 360
```

Total samples:

```text
921,600
+ 230,400
+ 230,400

= 1,382,400 samples
```

So, under this simplified equal-sized-sample assumption:

```text
4:2:0
≈ half the total Y/U/V sample count of 4:4:4
```

This is why chroma subsampling can significantly reduce the amount of raw component data.

---

# Connection to raw video size

This connects directly to the earlier **"why is raw video huge?"** discussion.

We previously used:

```text
RGB
8 bits/channel
3 channels
```

Therefore:

```text
24 bits/pixel
= 3 bytes/pixel
```

For:

```text
1280 × 720
```

that gives:

```text
921,600 pixels
× 3 bytes

= 2,764,800 bytes
```

But video commonly uses YUV formats such as `YUV 4:2:0`, where chroma is stored at lower spatial resolution.

For **8-bit YUV 4:2:0**, using the simplified planar model:

```text
Y:
921,600 samples × 1 byte
= 921,600 bytes

U:
230,400 samples × 1 byte
= 230,400 bytes

V:
230,400 samples × 1 byte
= 230,400 bytes
```

Total:

```text
921,600
+ 230,400
+ 230,400

= 1,382,400 bytes
```

So approximately:

```text
1.38 MB/frame
```

compared with:

```text
2.76 MB/frame
```

for the simplified 8-bit RGB case.

At 30 FPS:

```text
1,382,400 × 30
= 41,472,000 bytes/sec
```

≈ **41.5 MB/sec**

This is still huge.

And remember:

> This is **raw/uncompressed** data.

Video codecs later compress this much further.

---

# Chroma subsampling vs compression

Don't confuse these two concepts.

### Chroma subsampling

Changes the **sampling resolution** of color information.

```text
Full chroma
    ↓
fewer chroma samples
    ↓
less raw data
```

### Video compression

Uses more advanced techniques to represent video efficiently.

For example:

```text
prediction
transforms
quantization
entropy coding
```

Conceptually:

```text
Raw video
   ↓
YUV representation
   ↓
Chroma subsampling
   ↓
Codec compression
   ↓
Encoded video
   ↓
much smaller data
```

Chroma subsampling can therefore be thought of as **one part of the overall data-reduction process**, while codec compression goes much further.

---

# Chroma subsampling and visual quality

Reducing chroma resolution doesn't necessarily make a video look obviously bad.

For many normal scenes:

```text
High luma detail
+
Lower chroma detail
```

still looks very good because our visual system is generally more sensitive to brightness/detail.

However, fine color boundaries can be affected.

For example, situations with:

* sharp colored edges
* small colored text
* graphics
* screen recordings
* highly saturated details

can make chroma-resolution limitations more noticeable.

The key idea is:

> **4:2:0 preserves less fine color detail than 4:4:4, even though the overall image can still look very good.**

---

# Connection to FFmpeg

When working with FFmpeg, you may encounter pixel formats such as:

```text
yuv420p
yuv422p
yuv444p
```

The important part to recognize for now is:

```text
yuv420p → YUV 4:2:0 planar
yuv422p → YUV 4:2:2 planar
yuv444p → YUV 4:4:4 planar
```

The `p` indicates a **planar** representation, where the components are stored in separate planes.

Conceptually:

```text
yuv420p

Y plane
┌──────────────┐
│ Y Y Y Y Y Y  │
│ Y Y Y Y Y Y  │
└──────────────┘

U plane
┌───────┐
│ U U U │
│ U U U │
└───────┘

V plane
┌───────┐
│ V V V │
│ V V V │
└───────┘
```

The exact memory layout can involve additional details such as **strides/padding**, so this diagram is a conceptual representation, not necessarily the exact byte layout in memory.

---

# Important distinction: YUV terminology

You've been using:

```text
Y → brightness
U/V → color
```

This is a useful mental model.

But don't assume that YUV is always exactly the same thing as a specific mathematical color space.

In practical video discussions, people often use "YUV" loosely when referring to **YCbCr-style video component formats**.

For the current fundamentals, the important idea is:

```text
Y
↓
luma-related component

U/V
↓
chroma-related components
```

And:

```text
4:4:4 / 4:2:2 / 4:2:0
```

describe how those components are sampled spatially.

---

# Common mistakes / gotchas

## Mistake 1: "4:2:0 means no chroma"

Wrong.

```text
4:2:0
```

still contains chroma.

It simply stores chroma at a lower spatial resolution.

---

## Mistake 2: "4:2:0 means 4 Y, 2 U, 0 V"

Wrong.

The numbers describe a **sampling pattern**, not direct channel counts.

---

## Mistake 3: "Chroma subsampling reduces brightness"

Not the goal.

The idea is:

```text
Y → keep detailed
U/V → reduce spatial sampling
```

---

## Mistake 4: "Every pixel has its own U and V sample"

Not necessarily.

With subsampling such as `4:2:0`, multiple nearby pixels can share/reconstruct chroma information from lower-resolution chroma samples.

---

## Mistake 5: "Chroma subsampling is the same as codec compression"

Not exactly.

```text
Chroma subsampling
→ reduces chroma spatial resolution

Codec compression
→ uses prediction, transforms, quantization, entropy coding, etc.
```

They are related but different ideas.

---

## Mistake 6: "More pixels automatically means more raw bytes"

Only after you also specify the **representation**.

For example:

```text
RGB 8-bit
```

and:

```text
YUV 4:2:0 8-bit
```

use different numbers of samples per image location.

So raw size depends on:

```text
resolution
+
pixel format
+
bit depth
+
storage/layout details
```

---

# The complete mental model so far

You have now connected several layers:

```text
DIGITAL IMAGE
      ↓
Grid of pixels
      ↓
Resolution
      ↓
Width × Height
      ↓
Pixels per frame
```

Then:

```text
PIXEL DATA
      ↓
Color representation
      ↓
RGB / YUV-style components
      ↓
Bit depth
      ↓
Precision of each component value
```

Then:

```text
YUV
      ↓
Y = luma-related information
U/V = chroma-related information
      ↓
Chroma subsampling
      ↓
4:4:4 / 4:2:2 / 4:2:0
      ↓
Fewer chroma samples
      ↓
Less raw data
```

And finally:

```text
Frames
   ↓
FPS
   ↓
raw data per second

        ↓

Codec compression
        ↓
Encoded bitrate
        ↓
File size / streaming bandwidth
```

This is the chain you should keep in your head:

> **Pixels → resolution → frame → FPS → pixel format → bit depth → chroma subsampling → compression → bitrate → file size/bandwidth.**

---

# ⭐ Key takeaways

* **Chroma subsampling** reduces the spatial resolution of color information.
* `Y` represents luma-related information; `U/V` represent chroma-related information in the simplified model.
* Our eyes are generally more sensitive to fine brightness/detail than fine color detail.
* `4:4:4` → full horizontal and vertical chroma resolution.
* `4:2:2` → half horizontal chroma resolution, full vertical resolution.
* `4:2:0` → half horizontal and half vertical chroma resolution.
* The `0` in `4:2:0` does **not** mean zero chroma.
* Chroma subsampling is different from codec compression.
* Lower chroma resolution reduces **raw component data**, but the resulting video can still look very good.
* `yuv420p`, `yuv422p`, and `yuv444p` are common FFmpeg planar pixel formats corresponding to these sampling schemes.
* Raw video size depends on **resolution, pixel format, bit depth, and memory/storage layout**.
* Codec compression comes later and reduces the data much more aggressively.

---

# Minimal self-test

Try answering these without looking back:

1. What problem does chroma subsampling solve?
2. What is the difference between **luma** and **chroma**?
3. What does `4:4:4` mean?
4. What does `4:2:2` mean?
5. What does `4:2:0` mean?
6. Why doesn't the `0` in `4:2:0` mean "zero color"?
7. For `1280×720` 8-bit `YUV 4:2:0`, what are the dimensions of the Y, U, and V planes?
8. Why can `4:2:0` use less raw data than `4:4:4`?
9. How is chroma subsampling different from codec compression?
10. What does the `p` in `yuv420p` conceptually indicate?

---

# What to learn next

The natural next step is:

**Chapter 7 — Video Codecs & Compression**

You now understand:

```text
pixels
  ↓
resolution
  ↓
frames
  ↓
FPS
  ↓
RGB/YUV
  ↓
bit depth
  ↓
chroma subsampling
```

The next question is:

> **How does a codec take all this raw image information and turn it into a much smaller stream of bits?**

That leads directly to:

```text
I-frames
P-frames
B-frames
prediction
motion estimation
motion compensation
DCT/transform
quantization
entropy coding
GOP
H.264 / H.265 / AV1
```

That is where **compression → bitrate → actual video files** finally comes together.
