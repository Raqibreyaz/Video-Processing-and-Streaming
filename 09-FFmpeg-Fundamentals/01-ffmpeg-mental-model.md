# FFmpeg Mental Model

## What it is

**FFmpeg** is a toolset for working with multimedia such as:

* Video
* Audio
* Images
* Containers/files
* Codecs
* Filters

For video processing, a useful high-level mental model is:

```text
Input file
    ↓
  Demux
    ↓
  Decode
    ↓
Raw video/audio
    ↓
  Filter
    ↓
  Encode
    ↓
   Mux
    ↓
Output file
```

You do not need to memorize every term immediately. The most important idea is:

> **FFmpeg generally decodes compressed media into raw frames before applying operations such as resizing, then encodes the result again.**

---

## One-sentence summary

**For operations such as video resizing, FFmpeg typically follows the flow: compressed input → demux → decode → raw frames → filter → encode → mux → compressed output.**

---

## Intuition

Think of a compressed video as a **packed box**.

You cannot directly perform most image-processing operations on the packed representation.

Instead:

```text
Compressed video
      ↓
Unpack/decode
      ↓
Raw frames
      ↓
Modify frames
      ↓
Encode again
      ↓
Compressed video
```

For example, if you want to resize a video, FFmpeg conceptually does:

```text
H.264 video
    ↓
decode
    ↓
raw frames
    ↓
resize
    ↓
new raw frames
    ↓
encode
    ↓
H.264 video
```

So the `scale` operation works on **decoded/raw frames**, not directly on the original H.264 compressed byte stream.

---

# 1. The basic FFmpeg pipeline

The complete mental model is:

```text
Input file
    ↓
  Demux
    ↓
  Decode
    ↓
Raw video/audio
    ↓
  Filter
    ↓
  Encode
    ↓
   Mux
    ↓
Output file
```

Each stage has a different job.

```text
Demux
  ↓
separate streams from the container

Decode
  ↓
compressed data → raw media

Filter
  ↓
modify raw media

Encode
  ↓
raw media → compressed media

Mux
  ↓
put streams into an output container
```

The important thing is that **container handling, decoding, filtering, encoding, and output packaging are different stages**.

---

# 2. Example: resizing an MP4 video

Suppose we have:

```text
input.mp4
```

Inside the MP4, we might have:

```text
H.264 compressed video
AAC compressed audio
```

Now suppose we want to resize the video.

The conceptual process is:

```text
input.mp4
    ↓
H.264 compressed video
    ↓
  decode
    ↓
raw video frames
    ↓
scale filter
    ↓
new raw frames
    ↓
  encode
    ↓
H.264 compressed video
    ↓
output.mp4
```

The important part is:

```text
compressed video
       ↓
     decode
       ↓
  raw video frames
       ↓
   scale filter
       ↓
 encoded video
```

The resize operation happens on the **raw/decoded frames**.

---

# 3. Why can't the scale filter simply resize the compressed bytes?

A compressed video such as H.264 does not store every frame as a simple grid of independent RGB/YUV pixels.

Instead, codecs use compression techniques to represent video efficiently.

Therefore, the data inside the compressed stream is not directly equivalent to:

```text
Pixel  Pixel  Pixel  Pixel
Pixel  Pixel  Pixel  Pixel
Pixel  Pixel  Pixel  Pixel
```

A filter such as scaling needs access to actual image samples/pixels.

So FFmpeg conceptually needs:

```text
H.264 compressed representation
          ↓
        Decode
          ↓
Raw frame
          ↓
Scale
          ↓
New raw frame
```

This is why decoding is an important step in the filtering pipeline.

---

# 4. What does "raw frame" mean?

A **raw frame** is the decoded image data before it is encoded into a compressed video format.

For example, a frame might be represented using a pixel format such as:

```text
yuv420p
```

or:

```text
rgb24
```

At this stage, FFmpeg has actual image samples that filters can operate on.

For example:

```text
Compressed H.264
        ↓
     decoder
        ↓
Raw YUV frame
        ↓
     scale
        ↓
Resized YUV frame
        ↓
     encoder
        ↓
Compressed H.264
```

This connects directly to the earlier distinction between **codec** and **pixel format**:

```text
H.264
  ↓
codec

yuv420p
  ↓
pixel format
```

---

# 5. Filter stage

The **filter** stage modifies decoded media.

Examples include:

```text
Scale
Crop
Rotate
Flip
Overlay
Color adjustment
```

For resizing:

```text
Raw frame
   ↓
scale filter
   ↓
Resized raw frame
```

The filter does not normally work directly on the compressed H.264 bitstream.

It works on the decoded representation.

---

# 6. Encoding after filtering

After the filter has produced a new raw frame, FFmpeg needs to turn that raw frame back into compressed video.

Conceptually:

```text
Raw frame
   ↓
Filter
   ↓
Modified raw frame
   ↓
Encoder
   ↓
Compressed video
```

For example:

```text
Original raw frame
1920 × 1080
      ↓
scale
      ↓
New raw frame
1280 × 720
      ↓
H.264 encoder
      ↓
Compressed 1280 × 720 video
```

So resizing a video generally involves **decoding and re-encoding the video**.

---

# 7. The complete mental model

Keep this diagram in mind:

```text
                FFmpeg

Input container
     │
     ↓
   Demux
     │
     ↓
Compressed streams
     │
     ├───────────────┐
     ↓               ↓
 Video decoder    Audio decoder
     ↓               ↓
Raw video         Raw audio
     │               │
     ↓               ↓
 Video filters    Audio filters
     │               │
     ↓               ↓
 Video encoder    Audio encoder
     │               │
     └───────┬───────┘
             ↓
            Mux
             ↓
      Output container
```

This is a useful high-level picture of what FFmpeg is doing.

---

# Common mistakes / gotchas

## 1. Thinking FFmpeg directly resizes compressed H.264 bytes

For a normal filtering operation such as `scale`, the conceptual process is:

```text
H.264
 ↓
decode
 ↓
raw frame
 ↓
scale
```

The scale filter operates on decoded frames.

---

## 2. Thinking filtering and encoding are the same thing

They are different stages:

```text
Filter
↓
modify raw media

Encode
↓
compress raw media
```

For example:

```text
raw 1920×1080 frame
        ↓
scale filter
        ↓
raw 1280×720 frame
        ↓
H.264 encoder
        ↓
compressed video
```

---

## 3. Forgetting the decode step

When learning FFmpeg, it is tempting to imagine:

```text
Input
 ↓
Filter
 ↓
Output
```

A better mental model for normal video filtering is:

```text
Input
 ↓
Demux
 ↓
Decode
 ↓
Filter
 ↓
Encode
 ↓
Mux
 ↓
Output
```

---

# Key takeaways

* **FFmpeg** is a multimedia toolset.
* A useful mental model is:

```text
Input
 ↓
Demux
 ↓
Decode
 ↓
Raw media
 ↓
Filter
 ↓
Encode
 ↓
Mux
 ↓
Output
```

* Video filters such as `scale` generally work on **decoded/raw frames**.
* A compressed H.264 stream must conceptually be decoded before a scale filter can operate on its image data.
* After filtering, the modified raw frames are normally encoded again.
* `Demux` deals with separating streams from a container.
* `Decode` converts compressed media into raw media.
* `Filter` modifies raw media.
* `Encode` converts raw media back into compressed media.
* `Mux` packages the resulting streams into an output container.
* For a resize operation, think:

```text
H.264 compressed video
        ↓
      decode
        ↓
    raw frames
        ↓
   scale filter
        ↓
  resized frames
        ↓
      encode
        ↓
H.264 compressed video
```

## Minimal self-test

1. What is FFmpeg?
2. What is the basic FFmpeg processing pipeline?
3. What does **demuxing** conceptually do?
4. What does decoding produce?
5. Does the `scale` filter normally operate directly on H.264 compressed bytes?
6. What does the filter stage do?
7. Why is encoding needed after filtering?
8. What does muxing do?
9. What is the difference between a compressed video stream and a raw video frame?
10. For resizing an H.264 video, where does the resize operation happen?

## What to learn next

The next logical step is to understand **Demuxing vs Decoding vs Encoding vs Muxing** in more detail, and then connect those concepts to actual FFmpeg commands such as:

```text
ffmpeg -i input.mp4 ...
```

and understand what FFmpeg is doing internally at each stage.
