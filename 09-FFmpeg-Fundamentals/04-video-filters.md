# Video Filters

## What it is

A **video filter** modifies decoded video frames.

The key idea is:

```text
Compressed video
      ↓
    Decode
      ↓
Raw video frames
      ↓
   Filter
      ↓
Modified raw frames
      ↓
    Encode
      ↓
Output video
```

In FFmpeg, the main option for applying video filters is:

```bash
-vf
```

`-vf` means **video filter**.

---

## One-sentence summary

**`-vf` is FFmpeg's video-filter mechanism, and filters such as `scale` and `crop` modify decoded video frames before they are encoded again.**

---

# 1. The `-vf` option

The option:

```bash
-vf
```

means:

```text
video filter
```

It tells FFmpeg that we want to apply a filter to the video.

For example:

```bash
ffmpeg -i input.mp4 -vf scale=640:360 output.mp4
```

Here:

```text
-i input.mp4
    ↓
input

-vf scale=640:360
    ↓
video filter

output.mp4
    ↓
output
```

So `-vf` is the **filter mechanism**, while `scale` is one particular filter.

---

# 2. `scale` filter

The `scale` filter changes the dimensions of each video frame.

Example:

```bash
ffmpeg -i input.mp4 -vf scale=640:360 output.mp4
```

The important part is:

```text
scale=640:360
```

This means:

```text
width  = 640
height = 360
```

The conceptual pipeline is:

```text
input
  ↓
decode
  ↓
raw frames
  ↓
scale each frame to 640×360
  ↓
encode
  ↓
output
```

So if the input contains:

```text
1920×1080
```

frames, the scale filter can produce:

```text
640×360
```

frames.

### Important connection

The `scale` filter operates on **decoded/raw frames**.

```text
H.264 compressed video
        ↓
      decode
        ↓
raw video frames
        ↓
scale
        ↓
resized raw frames
        ↓
      encode
        ↓
compressed output
```

---

# 3. `crop` filter

The `crop` filter selects a region from each frame.

For example:

```bash
-vf crop=1280:720
```

means to keep a:

```text
1280×720
```

region from each frame.

Suppose the input is:

```text
1920×1080
```

Then conceptually:

```text
1920×1080 frame
       ↓
select 1280×720 region
       ↓
1280×720 frame
```

### Important: crop does not mean "remove 1280 pixels"

This is a common misunderstanding.

```text
crop=1280:720
```

does **not** mean:

```text
Remove 1280 pixels
```

It means:

> Select/keep a region whose dimensions are 1280×720.

Think of it like cutting out a rectangular portion of a larger image.

```text
Original frame
┌──────────────────────────────┐
│                              │
│      ┌──────────────┐        │
│      │              │        │
│      │   KEEP THIS  │        │
│      │    REGION    │        │
│      │              │        │
│      └──────────────┘        │
│                              │
└──────────────────────────────┘
             ↓
        cropped frame
```

---

# 4. `scale` vs `crop`

These filters can both change the output dimensions, but they do different things.

| Filter  | What it does                  |
| ------- | ----------------------------- |
| `scale` | Resizes the entire frame      |
| `crop`  | Selects a region of the frame |

For example:

```text
Original
1920×1080
```

### Scale

```text
1920×1080
     ↓
 scale
     ↓
640×360
```

The whole image is resized.

### Crop

```text
1920×1080
     ↓
 crop
     ↓
1280×720 region
```

A portion of the original frame is selected.

So:

```text
scale → resize
crop  → cut/select a region
```

---

# 5. `-vf` is not the same thing as `scale`

This distinction is important.

`-vf` is the **mechanism for specifying video filters**.

`scale` is one specific filter.

Think:

```text
-vf
 ↓
"Apply these video filters"

scale
 ↓
"Resize the frame"
```

So:

```bash
-vf scale=640:360
```

can be read as:

> Apply the `scale` video filter with a width of 640 and height of 360.

Other filters can also be used with `-vf`.

---

# 6. Other video filters

Video filtering is not limited to resizing.

Examples include:

```text
scale
crop
fps
rotate
overlay
blur
```

Each solves a different problem.

### `scale`

Changes frame dimensions.

```text
1920×1080
    ↓
640×360
```

### `crop`

Selects part of a frame.

```text
large frame
    ↓
selected region
```

### `fps`

Works with the frame rate.

Conceptually:

```text
60 fps
  ↓
fps filter
  ↓
30 fps
```

### `rotate`

Rotates the video frames.

```text
Frame
  ↓
rotate
  ↓
rotated frame
```

### `overlay`

Places one video/image layer on top of another.

```text
Video
  +
Image/logo
  ↓
overlay
  ↓
Combined frame
```

### `blur`

Applies a blur effect to the frame.

```text
Sharp frame
    ↓
  blur
    ↓
Blurred frame
```

These are all examples of the same general mechanism:

```text
-vf
 ↓
video filtering
```

---

# 7. Hardware scaling

FFmpeg also provides different scaling implementations.

A useful command for inspecting available filters is:

```bash
ffmpeg -filters | grep scale
```

The source showed filters such as:

```text
scale
scale_cuda
scale_qsv
scale_vaapi
scale_vulkan
```

These represent different implementations or processing paths for scaling.

The fundamental operation remains:

```text
input frame
    ↓
scaling
    ↓
output frame
```

But the underlying implementation can differ.

For example:

```text
scale
```

is the general scaling filter, while:

```text
scale_cuda
```

can use CUDA-related processing.

Similarly:

```text
scale_qsv
scale_vaapi
scale_vulkan
```

represent other hardware/API-specific paths.

The important idea for now is:

> **The operation is still scaling, but different filter implementations can use different processing paths or hardware.**

---

# 8. Software vs hardware scaling — mental model

A simplified mental model is:

```text
Software path

Raw frame
   ↓
CPU-based scale
   ↓
Scaled frame
```

versus a hardware-oriented path:

```text
Raw frame
   ↓
Hardware/API-specific scale
   ↓
Scaled frame
```

The exact memory transfers and hardware pipeline can become much more complicated, so do not assume that simply choosing a hardware filter automatically makes the entire FFmpeg pipeline hardware-accelerated.

For now, the important distinction is the **different implementation path**.

---

# 9. Filters operate on frames

A useful mental model is:

```text
Video stream
     ↓
Decode
     ↓
Frame 1 ──→ filter ──→ modified Frame 1
Frame 2 ──→ filter ──→ modified Frame 2
Frame 3 ──→ filter ──→ modified Frame 3
   ...
```

For a scale filter:

```text
Frame 1: 1920×1080
       ↓
     scale
       ↓
Frame 1: 640×360

Frame 2: 1920×1080
       ↓
     scale
       ↓
Frame 2: 640×360
```

The filter is therefore operating on the individual decoded frames.

---

# 10. Video filters are part of the larger FFmpeg pipeline

Connect this with the previous FFmpeg mental model:

```text
Input file
    ↓
  Demux
    ↓
  Decode
    ↓
Raw video frames
    ↓
  Video filter
    ↓
Modified raw frames
    ↓
  Encode
    ↓
   Mux
    ↓
Output file
```

For resizing:

```text
input.mp4
    ↓
H.264 video
    ↓
decode
    ↓
raw frames
    ↓
scale=640:360
    ↓
640×360 raw frames
    ↓
encode
    ↓
output.mp4
```

This is why filtering is not simply a modification of the original compressed H.264 bytes.

---

# Common mistakes / gotchas

## 1. Thinking `-vf` means scaling

It does not.

```text
-vf
 ↓
video filtering mechanism

scale
 ↓
one particular filter
```

For example:

```bash
-vf scale=640:360
```

uses the `scale` filter through the `-vf` mechanism.

---

## 2. Thinking `crop=1280:720` removes 1280 pixels

It does not.

It means:

```text
Keep a 1280×720 region
```

For example:

```text
1920×1080
     ↓
crop=1280:720
     ↓
1280×720
```

---

## 3. Thinking filters operate directly on compressed video

For normal video filtering, the mental model is:

```text
Compressed video
      ↓
    decode
      ↓
Raw frames
      ↓
   filter
      ↓
Modified frames
```

The filter operates on decoded frames.

---

## 4. Thinking scaling is the only video filter

`scale` is just one filter.

Other examples include:

```text
scale
crop
fps
rotate
overlay
blur
```

---

## 5. Thinking all scaling filters use the same processing path

FFmpeg can provide different scaling implementations:

```text
scale
scale_cuda
scale_qsv
scale_vaapi
scale_vulkan
```

The basic operation is still scaling, but the implementation can use different processing paths or hardware.

---

# What remains to learn

This topic is intentionally **not complete yet**.

Several important areas still need deeper study.

## 1. Chaining filters

For example, performing multiple operations:

```text
scale
  ↓
crop
  ↓
blur
```

or:

```text
crop
  ↓
scale
  ↓
overlay
```

The order matters.

---

## 2. Filter syntax in depth

We still need to understand how FFmpeg represents:

```text
filter names
filter parameters
multiple filters
filter chains
```

For example:

```bash
-vf "scale=640:360,crop=600:300"
```

The exact syntax and behavior should be studied separately.

---

## 3. `fps`

The `fps` filter deserves its own explanation.

Conceptually:

```text
60 fps
   ↓
fps filter
   ↓
30 fps
```

This is different from changing the resolution.

```text
scale
 ↓
spatial dimensions

fps
 ↓
temporal frame rate
```

---

## 4. `overlay`

`overlay` becomes particularly useful when combining:

```text
video + logo
video + image
video + another video
```

For example:

```text
Main video
    +
Logo
    ↓
 overlay
    ↓
Video with logo
```

---

## 5. Practical filter combinations

Eventually, we want to be comfortable with combinations such as:

```text
Input
  ↓
crop
  ↓
scale
  ↓
overlay
  ↓
Output
```

and understand why the order of filters matters.

---

# Key takeaways

* A **video filter modifies decoded video frames**.
* `-vf` means **video filter**.
* `scale` is one particular video filter.
* Example:

```bash
ffmpeg -i input.mp4 -vf scale=640:360 output.mp4
```

* `scale=640:360` resizes each frame to:

```text
640×360
```

* `crop=1280:720` selects a `1280×720` region from each frame.
* `crop=1280:720` does **not** mean removing 1280 pixels.
* `-vf` is the filtering mechanism; `scale`, `crop`, `fps`, `rotate`, `overlay`, and `blur` are individual filters.
* Scaling can have different implementations:

```text
scale
scale_cuda
scale_qsv
scale_vaapi
scale_vulkan
```

* Different scaling implementations can use different processing paths or hardware.
* The general filtering pipeline is:

```text
Compressed video
      ↓
    Decode
      ↓
Raw frames
      ↓
   Filter
      ↓
Modified frames
      ↓
    Encode
      ↓
Output video
```

* The topic is not finished yet. The next important areas are:

  * filter chaining
  * filter syntax
  * `fps`
  * `overlay`
  * practical filter combinations

---

# Minimal self-test

1. What does `-vf` mean?
2. Does `-vf` itself mean `scale`?
3. What does `scale=640:360` do?
4. Does the `scale` filter operate on compressed H.264 bytes or decoded frames?
5. What does `crop=1280:720` mean?
6. Does `crop=1280:720` remove 1280 pixels?
7. Name five FFmpeg video filters.
8. What is the difference between `scale` and `crop`?
9. What is the basic difference between `scale` and `fps`?
10. What is the difference between `scale` and `scale_cuda`?
11. Why can the order of multiple filters matter?
12. What filter-related topics still need to be studied?
