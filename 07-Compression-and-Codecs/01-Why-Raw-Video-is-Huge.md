# Why Is Raw Video So Huge?

## 1. What is raw video?

**Raw video** means video data before normal video compression.

Remember:

```text
Video
  ↓
many frames
  ↓
each frame = many pixels
  ↓
each pixel = image/color information
```

So raw video contains a **very large amount of image data**.

The key reason raw video is huge is:

> **There are enormous numbers of pixel values being stored for every frame, and many frames are stored every second.**

---

## 2. Start with one pixel

For a simple example, assume an **uncompressed RGB** video.

Each pixel contains:

```text
Red   → 8 bits
Green → 8 bits
Blue  → 8 bits
```

Therefore:

```text
8 + 8 + 8
= 24 bits
= 3 bytes
```

So, under this simplified RGB assumption:

> **1 pixel = 3 bytes**

This is where the earlier **3 bytes/pixel** calculation comes from.

---

## 3. Now look at one frame

Suppose the video is:

```text
1920 × 1080
```

Number of pixels:

```text
1920 × 1080
= 2,073,600 pixels
```

If every pixel requires 3 bytes:

```text
2,073,600 × 3
= 6,220,800 bytes
```

So one uncompressed RGB frame requires approximately:

```text
6.22 MB
```

That's **just one frame**.

---

## 4. Now add FPS

Suppose the video is:

```text
1920 × 1080
30 FPS
```

That means:

```text
30 frames every second
```

Each frame:

```text
≈ 6.22 MB
```

Therefore:

```text
6.22 MB × 30
≈ 186.6 MB/sec
```

So an uncompressed 1080p RGB video at 30 FPS can require roughly:

> **187 MB of data every second**

And for one minute:

```text
186.6 MB × 60
≈ 11,196 MB
```

or roughly:

> **11.2 GB per minute**

That's why raw video becomes enormous very quickly.

---

# 5. The formula

For the simplified **8-bit RGB** case:

```text
Raw frame size
≈ width × height × 3 bytes
```

And:

```text
Raw video data rate
≈ width × height × 3 × FPS
```

So:

```text
1920 × 1080 × 3 × 30
```

gives:

```text
186,624,000 bytes/sec
```

or approximately:

```text
186.6 MB/sec
```

---

# 6. The important connection: resolution + FPS

You already learned:

```text
Resolution
    ↓
pixels per frame
```

and:

```text
FPS
    ↓
frames per second
```

Now combine them:

```text
Resolution
    ↓
More pixels/frame
    ↓
More data/frame

FPS
    ↓
More frames/sec
    ↓
More data/sec
```

Therefore:

> **Higher resolution and higher FPS both increase the amount of raw video data.**

For example:

### 720p @ 30 FPS

```text
1280 × 720
= 921,600 pixels/frame

× 3 bytes
≈ 2.76 MB/frame

× 30 FPS
≈ 82.9 MB/sec
```

### 1080p @ 30 FPS

```text
1920 × 1080
= 2,073,600 pixels/frame

× 3 bytes
≈ 6.22 MB/frame

× 30 FPS
≈ 186.6 MB/sec
```

So 1080p has:

```text
2.25× more pixels/frame
```

and therefore, under the same RGB assumptions:

```text
2.25× more raw data
```

at the same FPS.

---

# 7. Why doesn't a normal video file have this size?

Because normal video is **compressed**.

Without compression:

```text
Pixels
   ↓
Every pixel value stored
   ↓
Every frame stored
   ↓
Huge amount of data
```

With video compression:

```text
Pixels
   ↓
Codec
   ↓
Find redundancy
   ↓
Represent information more efficiently
   ↓
Much smaller encoded stream
```

For example:

```text
RAW

~187 MB/sec
      ↓
    Codec
      ↓
Compressed video

~5–10 Mbps
```

These numbers are only an example; actual bitrate depends heavily on the codec, quality settings, content, pixel format, and other factors.

---

# 8. Why is compression so effective?

Because raw video contains **a lot of redundancy**.

Consider a simple frame:

```text
████████████████████
████████████████████
████████████████████
████████████████████
```

There are many pixels, but they are very similar.

It would be wasteful to treat every pixel as completely unrelated information.

Compression can exploit patterns like:

```text
Large areas are similar
        ↓
Spatial redundancy
```

Video codecs can also exploit similarity between frames:

```text
Frame 1
   ↓
Frame 2
   ↓
Frame 3
```

If most of the scene stays the same, the codec doesn't need to represent every frame as if it were completely unrelated to the previous frame.

This is **temporal redundancy**.

---

# 9. Raw video vs compressed video

Think of it this way:

```text
RAW VIDEO

Frame 1 → all pixel values
Frame 2 → all pixel values
Frame 3 → all pixel values
Frame 4 → all pixel values
...
```

Everything is explicitly represented.

Whereas compressed video tries to represent the same visual result more efficiently:

```text
Compressed video

Reference information
       +
changes/predictions
       +
compressed data
       ↓
Reconstruct frames during playback
```

So the decoder reconstructs the images from the compressed representation.

---

# 10. Raw data rate vs bitrate

This distinction is **very important**.

You previously learned:

> **Bitrate = encoded bits per second.**

Raw video also has a data rate, but it is calculated from the raw representation.

For example:

```text
1920 × 1080
30 FPS
8-bit RGB
```

gives approximately:

```text
186.6 MB/sec
```

Convert that to bits:

```text
186.6 MB/sec × 8
≈ 1,492.99 Mbps
```

So roughly:

```text
RAW
≈ 1.49 Gbps
```

But a compressed version might be something like:

```text
5 Mbps
```

Conceptually:

```text
RAW
~1,493 Mbps
      ↓
   compression
      ↓
Compressed
~5 Mbps
```

That's an enormous reduction.

---

# 11. "But aren't pixels already just numbers?"

Yes.

That's actually the point.

A raw video frame can essentially be thought of as a huge collection of numerical values.

For simplified RGB:

```text
Pixel 1 → R,G,B
Pixel 2 → R,G,B
Pixel 3 → R,G,B
...
```

For a 1920×1080 frame:

```text
2,073,600 pixels
```

Each with multiple values.

Then multiply by:

```text
30 frames/sec
```

and the amount of numerical data becomes enormous.

---

# 12. The deeper reason: raw video has no efficient representation

Imagine this image:

```text
AAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAA
```

You could store it literally:

```text
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA...
```

or you could describe it more efficiently as:

```text
"Repeat A for N positions"
```

Raw video is closer to the first idea: **the pixel representation is directly stored according to the chosen pixel format**, rather than being heavily compressed.

A codec tries to find a more efficient representation.

This is why:

> **Raw video is huge not because each individual pixel is huge, but because there are huge numbers of pixels × frames, and the raw representation stores their values with little or no compression.**

---

# 13. Resolution, FPS, bit depth, and pixel format all matter

The earlier `3 bytes/pixel` calculation is a **simplified RGB example**.

Real raw video can use different **pixel formats**.

For example:

```text
RGB
YUV 4:4:4
YUV 4:2:2
YUV 4:2:0
8-bit
10-bit
12-bit
...
```

These formats can require different amounts of raw data.

So don't memorize:

> "Every raw video pixel is always 3 bytes."

Instead remember:

> **Raw video size depends on resolution, FPS, bit depth, and pixel format.**

Our 3-byte example is useful for understanding the basic idea.

---

# 14. Bit depth also increases raw size

Suppose we compare:

```text
8-bit RGB
```

with:

```text
10-bit RGB
```

For simplified RGB:

### 8-bit

```text
8 bits R
8 bits G
8 bits B

= 24 bits/pixel
= 3 bytes/pixel
```

### 10-bit

```text
10 bits R
10 bits G
10 bits B

= 30 bits/pixel
```

So higher bit depth means more information has to be represented in the raw format.

Remember:

```text
Resolution
→ number of pixel positions

Bit depth
→ precision of each channel value
```

They increase raw data for different reasons.

---

# 15. A useful master formula

For a general raw video:

```text
Raw data/sec
≈
pixels/frame
×
frames/sec
×
bytes/pixel
```

Or:

```text
Raw data/sec
≈
width
×
height
×
FPS
×
bytes/pixel
```

For our simplified 8-bit RGB example:

```text
bytes/pixel = 3
```

Therefore:

```text
Raw data/sec
≈
width × height × FPS × 3
```

---

# 16. Example: 4K raw video

Consider:

```text
3840 × 2160
60 FPS
8-bit RGB
```

Pixels/frame:

```text
3840 × 2160
= 8,294,400 pixels
```

Raw frame size:

```text
8,294,400 × 3
= 24,883,200 bytes
```

Approximately:

```text
24.9 MB/frame
```

At 60 FPS:

```text
24.9 × 60
≈ 1,493 MB/sec
```

That's approximately:

> **1.49 GB/sec**

And that's only the simplified 8-bit RGB case.

So you can see why high-resolution, high-FPS raw video requires enormous storage and data throughput.

---

# 17. Why this matters for FFmpeg

When working with FFmpeg, you often start with something like:

```text
Raw frames
    ↓
FFmpeg
    ↓
Codec
    ↓
Compressed video
    ↓
MP4 / MKV / HLS segments
```

The codec is doing the important job of turning a massive stream of raw image information into a much smaller encoded representation.

For example:

```text
Raw frame
1920 × 1080
      ↓
Encoder
      ↓
H.264 / H.265 / AV1
      ↓
Compressed bitstream
```

The compressed bitstream is what gives you practical:

* file sizes
* streaming bitrates
* network bandwidth requirements
* storage requirements
* CDN transfer costs

---

# 18. The complete mental model

This connects everything you've learned so far:

```text
DIGITAL IMAGE
      ↓
    Pixels
      ↓
Resolution
(width × height)
      ↓
Pixels per frame
      ↓
FPS
      ↓
Frames per second
      ↓
Bit depth + pixel format
      ↓
Data required per pixel/frame
      ↓
RAW VIDEO DATA
      ↓
Huge
```

Then:

```text
RAW VIDEO
    ↓
Codec / Compression
    ↓
Remove/exploit redundancy
    ↓
Encoded representation
    ↓
BITRATE
    ↓
Much smaller practical file/stream
```

---

# ⭐ The most important idea

> **Raw video is huge because a video contains a large number of pixel values for every frame, and many frames are produced every second. Resolution determines how many pixels each frame has, FPS determines how many frames occur each second, and bit depth/pixel format determine how much raw data is needed to represent those pixels. Compression then exploits redundancy to reduce this enormous raw data into a much smaller encoded stream.**

---

## Quick reference

| Concept           | Controls                               |
| ----------------- | -------------------------------------- |
| **Resolution**    | Pixels per frame                       |
| **FPS**           | Frames per second                      |
| **Bit depth**     | Precision of channel values            |
| **Pixel format**  | How pixel/channel data is organized    |
| **Raw data rate** | Amount of uncompressed data per second |
| **Codec**         | How efficiently video is compressed    |
| **Bitrate**       | Encoded bits per second                |
| **Duration**      | Total amount of video data             |

### Simplified formula

```text
Raw data/sec
≈ width × height × FPS × bytes/pixel
```

For 8-bit RGB:

```text
Raw data/sec
≈ width × height × FPS × 3
```

And for compressed video:

```text
File size
≈ encoded bitrate × duration
```

So the big picture is:

```text
PIXELS
   ↓
FRAMES
   ↓
RAW VIDEO
   ↓
COMPRESSION
   ↓
BITRATE
   ↓
FILE SIZE / STREAMING
```

---

## Minimal self-test

1. Why does increasing **resolution** increase raw video size?
2. Why does increasing **FPS** increase raw video size?
3. Why does increasing **bit depth** increase raw video size?
4. For simplified 8-bit RGB, where does **3 bytes/pixel** come from?
5. Why can a compressed 1080p video be only a few Mbps while the corresponding raw video can require **hundreds or thousands of Mbps**?
6. Does `3 bytes/pixel` apply to every real-world raw video format?
7. What is the difference between **raw data rate** and **encoded bitrate**?

### What to learn next

The natural next step is **pixel formats and YUV**, especially **YUV 4:4:4, 4:2:2, and 4:2:0**. That will explain why real video often doesn't use the simple `3 bytes/pixel RGB` model and why video can represent color more efficiently.
