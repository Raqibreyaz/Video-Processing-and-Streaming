# From RGB to YUV

## What it is

A digital image can represent color in different ways.

The most familiar representation is **RGB**:

```text
Pixel
 ├── R → Red
 ├── G → Green
 └── B → Blue
```

With 8 bits per channel:

```text
R = 8 bits
G = 8 bits
B = 8 bits
----------------
  = 24 bits/pixel
  = 3 bytes/pixel
```

RGB works well for representing images, but video has an important property:

> Human vision is more sensitive to **brightness and detail** than to very fine **color detail**.

YUV-style representations take advantage of this by separating brightness-related information from color information.

The basic idea is:

```text
RGB
↓
R + G + B

YUV
↓
Y + U + V
↓
luma + chroma
```

---

## One-sentence summary

**YUV separates brightness-related information (Y/luma) from color information (U and V/chroma), making it possible to reduce chroma resolution and save video data without reducing all image information equally.**

---

## Intuition

Think of RGB as asking:

```text
How much RED?
How much GREEN?
How much BLUE?
```

YUV instead asks:

```text
How bright is this part?
How does its color differ?
How does the other color component differ?
```

So the mental model is:

```text
RGB
 │
 ├── R
 ├── G
 └── B

       ↓ conversion

YUV
 │
 ├── Y
 │    ↓
 │  brightness/detail
 │
 └── U + V
      ↓
    color information
```

The important idea is **not simply changing the names of R, G, and B**. RGB and YUV are different ways of representing the same image/color information.

---

# 1. Why do we need YUV?

With 8-bit RGB, every pixel needs:

```text
R = 1 byte
G = 1 byte
B = 1 byte

Total = 3 bytes/pixel
```

For video, this can produce a lot of raw data.

But human vision does not treat all visual information equally.

We are generally:

* very sensitive to brightness changes
* very sensitive to image detail carried by brightness
* less sensitive to fine changes in color

Therefore, video systems can preserve brightness information more strongly while storing less color information.

This is the fundamental idea behind **chroma subsampling**.

```text
Human vision
     ↓
More sensitive to luma/detail
     ↓
Less sensitive to fine chroma detail
     ↓
Reduce chroma sampling
     ↓
Less data
```

---

# 2. RGB vs YUV

## RGB

RGB stores three color components:

```text
R → Red
G → Green
B → Blue
```

It can be thought of as:

```text
RGB

"How much red?"
"How much green?"
"How much blue?"
```

## YUV

YUV separates the information conceptually into:

```text
Y → brightness/luma information
U → color-difference information
V → color-difference information
```

So:

```text
YUV
 │
 ├── Y → luma
 │
 └── U + V → chroma
```

### Important: U and V are not simply blue and red

Do **not** think:

```text
Y = brightness
U = blue
V = red
```

That is an oversimplification.

U and V are two separate color-related components that **together describe the color information**.

A useful mental model is to think of them as two coordinates describing color:

```text
          V
          ↑
          │
          │    ●
          │
──────────┼──────────→ U
```

The exact mathematics is not important at this stage.

---

# 3. What is Y?

The **Y** component carries brightness-related information.

In video terminology, you will often encounter the term **luma**.

A simple way to think about it is:

> **"How bright does this part of the image appear?"**

For example:

```text
BLACK
  ↓
DARK GRAY
  ↓
GRAY
  ↓
LIGHT GRAY
  ↓
WHITE
```

Y carries the information needed to represent this brightness structure.

This is important because our eyes are highly sensitive to changes in brightness and detail.

### Y only

If we keep only Y:

```text
Y
↓
brightness information
↓
grayscale image
```

There is no color information because U and V are missing.

### Y + U + V

When all three components are available:

```text
Y + U + V
    ↓
color image
```

Together, they provide enough information to reconstruct the pixel's color.

---

# 4. What are U and V?

U and V carry **color-difference information**.

Together, they are commonly referred to as **chroma**.

```text
Y
↓
Luma / brightness-related information

U + V
↓
Chroma / color information
```

Therefore:

```text
YUV
 │
 ├── Y → luma
 │
 └── U,V → chroma
```

For the fundamentals, the key point is the separation:

```text
Brightness/detail → Y
Color information  → U + V
```

---

# 5. Brightness is not the same as the screen brightness slider

A common misunderstanding is to think that Y is the same thing as the brightness setting on a phone, TV, or monitor.

It is not.

**Y/luma describes brightness-related information inside the video signal.**

It does **not** mean:

> the brightness slider on your display.

These are different concepts.

---

# 6. YUV does not contain opacity

Y, U, and V are not transparency values.

**Opacity** describes how visible something is:

```text
100% → completely visible
50%  → partly transparent
0%   → invisible
```

YUV's:

```text
Y
U
V
```

do not represent opacity.

So:

```text
YUV
↓
brightness + color

Alpha
↓
transparency/opacity
```

Alpha is a separate concept.

---

# 7. RGB → YUV is a mathematical transformation

RGB values are not converted by simply doing:

```text
R → Y
G → U
B → V
```

That is **not** how the conversion works.

Instead, the RGB values are mathematically transformed using equations.

Conceptually:

```text
        R
        │
        ├────────→ Y
        │
        G
        │
        ├────────→ U
        │
        B
        │
        └────────→ V
```

The exact equations depend on the color system or standard being used.

Different standards can use different coefficients.

Therefore, do not memorize one RGB → YUV formula yet.

The important idea is:

> **RGB and YUV are different representations of image color information, and RGB values can be mathematically transformed into YUV components.**

---

# 8. RGB → YUV does not automatically change the visual color

Consider a pure red RGB pixel:

```text
R = 255
G = 0
B = 0
```

This represents pure red.

After RGB → YUV conversion, it becomes:

```text
Y = some value
U = some value
V = some value
```

The exact values depend on the conversion standard.

The important point is:

> The visual color has not suddenly changed. We have changed how its information is represented.

A simple analogy is:

```text
₹100
```

and:

```text
10000 paise
```

The representation is different, but the underlying quantity is the same.

Similarly:

```text
RGB representation
        ↕
YUV representation
```

can describe the same visual color.

---

# 9. Why separate brightness from color?

Imagine an image where most of the important structure comes from brightness:

```text
        Brightness/detail

              ↓

████████████████████
████████████████████
████████████████████
```

Our eyes are quite sensitive to this information.

Now suppose we reduce some fine color information:

```text
Keep more information
        ↓
Y / brightness

Keep less information
        ↓
U + V / color
```

The image can still look visually good because our eyes are less sensitive to fine chroma detail.

This gives us a useful strategy:

```text
Preserve luma strongly
        +
Reduce chroma somewhat
        ↓
Less data
```

This is the foundation of **chroma subsampling**.

---

# 10. What is chroma subsampling?

**Chroma subsampling** means storing chroma information at a lower spatial resolution than luma.

Suppose we start with full-resolution information:

```text
Y Y Y Y
U U U U
V V V V
```

We can reduce chroma sampling:

```text
Y Y Y Y
U   U
V   V
```

The exact arrangement depends on the subsampling format.

Common formats include:

```text
4:4:4
4:2:2
4:2:0
```

The numbers describe how frequently luma and chroma samples are stored relative to each other.

The exact meaning of those numbers is the next concept to study.

---

# 11. Why reduce chroma instead of luma?

The reason comes from human vision.

We are more sensitive to:

```text
Brightness
Detail
Edges
```

than to very fine color changes.

Therefore:

```text
Y → high detail
U/V → reduced detail
```

usually works well.

But doing the opposite:

```text
Y → heavily reduced
U/V → full detail
```

would make the loss of image detail much more noticeable.

So video systems generally preserve luma information more strongly and reduce chroma information.

---

# 12. YUV is not the same thing as compression

This distinction is extremely important.

Converting:

```text
RGB → YUV
```

is primarily a **change in representation/color space**.

It is not automatically compression.

For example:

```text
RGB
 ↓
YUV
```

can still contain essentially the same amount of information.

Compression is a separate operation.

A useful pipeline is:

```text
RGB → YUV
       ↓
different representation

YUV → chroma subsampling
       ↓
reduce chroma samples

YUV → video codec
       ↓
much greater compression
```

So:

### RGB → YUV

**Representation/color-space conversion**

### Chroma subsampling

**Reduce chroma spatial resolution**

### Codec compression

**Compress the video into a much smaller bitstream**

These operations are related, but they are not the same.

---

# 13. YUV vs Y'CbCr

There is an important terminology detail.

People working with video often casually say:

```text
YUV
```

when they actually mean something closer to **Y'CbCr**.

Strictly speaking:

> **YUV and Y'CbCr are not identical systems.**

Modern digital video commonly uses Y'CbCr-style representations.

You will encounter this in systems involving:

```text
H.264
H.265
AV1
MP4
HLS
```

The channels are generally described as:

```text
Y'  → luma
Cb  → blue-difference chroma
Cr  → red-difference chroma
```

However, developers often casually refer to the overall representation as:

```text
YUV
```

For fundamentals, it is useful to remember:

> **"YUV" is commonly used informally when discussing digital video, while modern digital video commonly uses Y'CbCr terminology.**

Later, when working with exact video standards and color conversion, the distinction becomes important.

---

# 14. YUV 4:2:0 and raw video size

Previously, 8-bit RGB required:

```text
3 bytes/pixel
```

For a:

```text
1920 × 1080
```

frame:

```text
1920 × 1080 × 3
≈ 6.22 MB/frame
```

But video often uses YUV-based formats such as:

```text
YUV 4:2:0
```

Here, luma remains full resolution while chroma is sampled at a lower resolution.

For **8-bit 4:2:0 planar video**, a common model is:

```text
1.5 bytes/pixel
```

Why?

For every 2 × 2 block of pixels:

```text
Y → 4 samples
U → 1 sample
V → 1 sample
```

Therefore:

```text
Total = 4 + 1 + 1
      = 6 samples
```

Since each 8-bit sample uses 1 byte:

```text
6 bytes / 4 pixels
= 1.5 bytes/pixel
```

Compare:

```text
8-bit RGB
→ 3 bytes/pixel

8-bit YUV 4:2:0
→ ~1.5 bytes/pixel
```

So the simplified raw representation is roughly **half as many bytes per pixel**.

This is one major reason 4:2:0 is so common in video.

---

# 15. Why YUV 4:2:0 does NOT mean "half the quality"

This is an important gotcha.

It is tempting to think:

```text
3 bytes/pixel
      ↓
1.5 bytes/pixel
      ↓
50% quality
```

That conclusion is wrong.

We are not simply removing half of the image information equally.

Instead, we are mainly reducing **chroma resolution**.

The luma channel remains full resolution:

```text
Y Y Y Y
Y Y Y Y
Y Y Y Y
Y Y Y Y
```

while U and V are sampled less frequently.

Because human vision is less sensitive to fine chroma detail, the visual impact can be relatively small.

So:

```text
Less raw data
≠
Half the visual quality
```

---

# 16. Pixel, channel, and sample

These three terms are easy to confuse.

## Pixel

A **pixel** represents a location in the image.

```text
(x, y)
```

For example:

```text
Pixel location = (100, 50)
```

## Channel

A **channel** is one component of the image representation.

For YUV:

```text
Y
U
V
```

For RGB:

```text
R
G
B
```

## Sample

A **sample** is the numerical value stored for a particular channel location.

For example:

```text
Pixel location
     ↓
  (100, 50)

Y sample
     ↓
brightness value

U sample
     ↓
chroma value

V sample
     ↓
chroma value
```

With 4:2:0, however, **not every pixel has its own U and V sample**.

That is exactly what chroma subsampling means.

```text
Pixel
  ↓
location in image

Channel
  ↓
Y / U / V

Sample
  ↓
numerical value for a channel location
```

---

# 17. YUV and raw video data

The connection to raw video becomes clearer now.

With RGB:

```text
Pixel
 ↓
R + G + B
 ↓
24 bits
 ↓
3 bytes/pixel
```

With YUV:

```text
Image
 ↓
Y / luma
+
U,V / chroma
```

Because chroma does not need to be stored at full spatial resolution for many applications:

```text
Y + U + V
      ↓
Chroma subsampling
      ↓
Fewer chroma samples
      ↓
Less raw data
```

This happens **before** or as part of the preparation for later codec compression.

---

# 18. How this fits into the video pipeline

A useful high-level video pipeline is:

```text
REAL WORLD
    ↓
Camera sensor
    ↓
Image information
    ↓
Color representation
    ↓
RGB / YUV-type representation
    ↓
Chroma subsampling
    ↓
Video codec
    ↓
Compression
    ↓
Encoded bitrate
    ↓
File / streaming
```

Each stage solves a different problem.

## Color representation

```text
RGB → YUV
```

Changes how color information is represented.

## Chroma subsampling

```text
4:4:4 → 4:2:2 → 4:2:0
```

Reduces chroma sampling.

## Codec compression

Examples:

```text
H.264
H.265
AV1
```

Codecs use techniques such as spatial and temporal redundancy to produce a much smaller encoded bitstream.

## Bitrate

Examples:

```text
5 Mbps
10 Mbps
20 Mbps
```

Bitrate describes the encoded data rate.

### Do not mix these concepts

```text
RGB → YUV
```

is not the same as:

```text
YUV → 4:2:0
```

and neither is the same as:

```text
YUV → H.264
```

and bitrate is another separate concept:

```text
H.264 stream → 5 Mbps
```

---

# 19. Practical FFmpeg connection

When inspecting a video using FFmpeg or `ffprobe`, you may see something like:

```text
Video:

  H.264
  yuv420p
  1920x1080
  30 fps
```

Now we can understand what each part means:

```text
H.264
  ↓
video codec

yuv420p
  ↓
pixel format

  ↓
YUV 4:2:0
  ↓
8-bit planar representation

1920x1080
  ↓
resolution

30 fps
  ↓
frames per second
```

### Important

```text
yuv420p
```

is **not the codec**.

It is the **pixel format**.

So:

```text
H.264   → codec
yuv420p → pixel format
```

This distinction becomes very important when working with FFmpeg.

---

# 20. 4:2:0 should not be memorized yet

At this stage, focus on the concepts rather than memorizing the sampling pattern.

Remember:

```text
RGB
 ↓
R + G + B

YUV
 ↓
Y + U + V

Y
 ↓
luma / brightness-related information

U + V
 ↓
chroma / color information
```

Then:

```text
Human vision
      ↓
More sensitive to luma detail
      ↓
Less sensitive to fine chroma detail
      ↓
Chroma can be sampled less frequently
      ↓
Chroma subsampling
```

The exact meaning of:

```text
4:4:4
4:2:2
4:2:0
```

should be studied as the next topic.

---

# 21. A useful mental model: "separating what the eye cares about"

RGB gives us:

```text
RGB
 │
 ├── Red
 ├── Green
 └── Blue
```

It does not explicitly organize the information as:

```text
Brightness
vs
Color detail
```

YUV-style representations do:

```text
YUV
 │
 ├── Y
 │    ↓
 │  brightness/detail
 │
 └── U + V
      ↓
    color information
```

This gives video systems the opportunity to:

```text
Preserve Y strongly
       +
Reduce chroma somewhat
       ↓
Save data
```

That is the key connection between YUV and chroma subsampling.

---

# 22. Important distinction between the layers

Do not confuse these four concepts:

### 1. RGB → YUV

```text
RGB → YUV
```

**Color representation/conversion**

---

### 2. Chroma subsampling

```text
4:4:4 → 4:2:2 → 4:2:0
```

**Reduce chroma sampling**

---

### 3. Video encoding/compression

```text
YUV → H.264/H.265/AV1
```

**Encode and compress video**

---

### 4. Encoded bitrate

```text
H.264 stream → 5 Mbps
```

**Encoded data rate**

---

Keep these layers separate:

```text
RGB → YUV
     │
     └── representation

YUV → 4:2:0
     │
     └── chroma subsampling

YUV → H.264
     │
     └── codec encoding/compression

H.264 → 5 Mbps
     │
     └── encoded bitrate
```

---

# Quick comparison

| Concept                | Main purpose                                    |
| ---------------------- | ----------------------------------------------- |
| **RGB**                | Represents color using red, green, and blue     |
| **YUV / Y'CbCr**       | Separates luma from chroma                      |
| **Y / luma**           | Carries brightness-related image detail         |
| **U/V or Cb/Cr**       | Carries chroma/color-difference information     |
| **Chroma subsampling** | Stores chroma at lower spatial resolution       |
| **Codec**              | Compresses/encodes video information            |
| **Bitrate**            | Describes the amount of encoded data per second |
| **Pixel**              | Represents an image location                    |
| **Channel**            | One component of the representation             |
| **Sample**             | A numerical value stored for a channel location |

---

# Common mistakes / gotchas

## Mistake 1: Thinking U = blue and V = red

Not exactly.

```text
U + V
```

together describe the color information.

In modern digital video, the more precise terminology is often:

```text
Cb → blue-difference chroma
Cr → red-difference chroma
```

---

## Mistake 2: Thinking RGB → YUV is compression

It is primarily a representation/color-space conversion.

```text
RGB → YUV
```

does not automatically mean fewer bits.

Data reduction can happen later through techniques such as:

```text
Chroma subsampling
+
Codec compression
```

---

## Mistake 3: Thinking Y is the monitor brightness setting

Y/luma is brightness-related information in the video signal.

It is not the same as:

```text
phone brightness slider
TV brightness setting
monitor brightness setting
```

---

## Mistake 4: Thinking YUV contains transparency

It does not.

```text
Y → luma
U/V → chroma
```

Opacity/transparency is a separate concept, commonly represented by an alpha channel.

---

## Mistake 5: Thinking 4:2:0 means 50% visual quality

No.

4:2:0 mainly reduces **chroma spatial resolution**.

It does not remove half of the image information equally.

---

## Mistake 6: Thinking `yuv420p` is a codec

It is a **pixel format**.

For example:

```text
H.264
↓
codec

yuv420p
↓
pixel format
```

---

## Mistake 7: Mixing up pixel, channel, and sample

Remember:

```text
Pixel
→ image location

Channel
→ component such as Y, U, or V

Sample
→ numerical value stored for a channel location
```

With chroma subsampling, not every pixel needs its own chroma samples.

---

# Core mental model

Memorize this diagram:

```text
RGB
 │
 ├── R
 ├── G
 └── B
       ↓
  color representation

YUV
 │
 ├── Y
 │    ↓
 │  luma / brightness
 │
 └── U + V
      ↓
    chroma / color
```

And then:

```text
RGB → YUV
       ↓
separate brightness
from color information
       ↓
human vision is less sensitive
to fine chroma detail
       ↓
chroma can be sampled
less frequently
       ↓
less raw data
```

---

# Key takeaways

* **RGB** represents color using Red, Green, and Blue.
* **YUV** is a useful video-oriented representation that separates **luma** from **chroma**.
* **Y** represents luma or brightness-related information.
* **U and V** represent color-difference/chroma information.
* U and V should not simply be thought of as "blue" and "red."
* Y/luma is not the same as the brightness slider on a display.
* YUV does not represent opacity.
* RGB → YUV is a **representation/color-space conversion**, not automatically compression.
* Human vision is more sensitive to luma/detail than fine chroma detail.
* This allows video systems to reduce chroma resolution.
* Reducing chroma resolution is called **chroma subsampling**.
* Common chroma-sampling formats include `4:4:4`, `4:2:2`, and `4:2:0`.
* 8-bit RGB commonly uses **3 bytes/pixel**.
* 8-bit 4:2:0 planar video can be modeled as **1.5 bytes/pixel**.
* Fewer bytes per pixel does **not** mean half the visual quality.
* **Y'CbCr** is the more precise terminology commonly encountered in modern digital video.
* `yuv420p` in FFmpeg is a **pixel format**, not a codec.
* A **pixel** is an image location.
* A **channel** is a component such as Y, U, or V.
* A **sample** is a numerical value stored for a channel location.
* Keep representation, chroma subsampling, codec compression, and bitrate as separate concepts.

---

# Minimal self-test

1. What are the three components of RGB?
2. What are the three components of YUV?
3. What does **Y** represent?
4. What do **U and V** represent?
5. Why is separating luma and chroma useful for video?
6. Is RGB → YUV itself the same thing as video compression?
7. What is chroma subsampling?
8. Why can YUV 4:2:0 use fewer raw bytes than 8-bit RGB?
9. Why does fewer raw data not mean "half the visual quality"?
10. In FFmpeg, is `yuv420p` a codec or a pixel format?
11. What is the difference between a pixel, channel, and sample?
12. Why do modern digital-video discussions often use **Y'CbCr** instead of strictly saying YUV?
13. What is the difference between RGB → YUV, chroma subsampling, codec compression, and bitrate?
14. Why is luma usually preserved at higher resolution than chroma?

---

# What to learn next

The natural next topic is **Chroma Subsampling: 4:4:4 vs 4:2:2 vs 4:2:0**.

Focus on:

```text
4:4:4
  ↓
full chroma resolution

4:2:2
  ↓
horizontal chroma reduction

4:2:0
  ↓
horizontal + vertical chroma reduction
```

Then connect those formats to actual FFmpeg pixel formats such as:

```text
yuv444p
yuv422p
yuv420p
```

Once that is clear, the next useful step is understanding **planar vs packed pixel formats** and how Y, U, and V are actually laid out in memory.
